---
layout: post
title: Invoking even more AWS services directly from AWS AppSync
date: 2019-12-09 15:58 -0600
background: '/assets/images/patrick-perkins-ETRPjvb0KM0-unsplash.jpg'
---

A few months ago, I wrote a [post on the AWS Mobile Blog](https://aws.amazon.com/blogs/mobile/invoke-aws-services-directly-from-aws-appsync/) that demonstrated how to invoke AWS service directly from AWS AppSync. In that case, I specifically focused on AWS Step Functions, but it is possible to integrate AppSync with many other AWS services. The goal of this post is to document the integration of AppSync with services beyond Step Functions.

AWS AppSync uses GraphQL resolvers to define the interaction between AppSync and each data source. These are Apache Velocity Templates that will be unique per GraphQL operation. By moving integration of AWS services to resolvers, we can minimize the maintenance of integration code. Velocity Templates generally require little upkeep over time. This approach can be even more maintainable than relying on a thin layer of Lambda code for integration, which would still require dependency management and updates.

## Adding an AWS data source

The first step in integrating AppSync with an AWS service is to create an HTTP data source for the service. For an AWS service, the data source endpoint is set to the service API endpoint for the given AWS region and we configure [SigV4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) signing.

Currently, to configure signing of HTTP data sources, we can use either the AWS CLI or CloudFormation. My preference is to utilize CloudFormation for these needs, though my earlier blog post demonstrates the CLI approach as well. For example, we can add a new data source to invoke Amazon SQS using CloudFormation as follows

``` yaml
SQSHttpDataSource:
  Type: AWS::AppSync::DataSource
  Properties:
    ApiId: !GetAtt MyApi.ApiId
    Name: SQSDataSource
    Description: SQS Data Source
    Type: HTTP
    # IAM role defined elsewhere in AWS CloudFormation template
    # Note: this role needs a policy with 'sqs:SendMessage' permission
    ServiceRoleArn: !GetAtt AppSyncServiceRole.Arn
    HttpConfig:
      Endpoint: !Sub https://sqs.${AWS::Region}.amazonaws.com/
      AuthorizationConfig:
        AuthorizationType: AWS_IAM
        AwsIamConfig:
          SigningRegion: !Ref AWS::Region
          SigningServiceName: sqs
```

AppSync will require an AWS IAM role that allows the service to perform the desired action. For example, SQS would require `sqs:SendMessage` permission and Step Functions `states:StartExecution`.

After creating the data source, we can implement the resolvers that allow interaction with the AWS service. Let's step through a few example integrations.

## Amazon SQS

For each service reviewed in this post, we will start with a sample GraphQL schema that defines the interactions and data associated with the resolver.

We will begin with Amazon Simple Queue Service (SQS), one of the earliest AWS services and one of my favorites for building reliable, decoupled workloads. Here, we send a messsage via a queue, though other operations are available as well. For example, we could list all messages on a particular queue.

``` graphql
input SendMessageInput {
  email: String!
  message: String!
}

type Message {
  id: ID!
  email: String!
  message: String!
}

type Mutation {
  sendMessage(input: SendMessageInput!): Message
}
```

AppSync can be used to deliver a message to an SQS Queue using the following resolver. While SQS also [supports a `POST` API](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-making-api-requests.html) as well, I have implemented here using a `GET`. Note that the endpoint resource path includes the AWS Account ID and name of the queue. Infrastructure as code approaches, such as CloudFormation, would make wiring this information easy.

**`sendMessage` - Request Mapping**

``` json
{
  "version": "2018-05-29",
  "method": "GET",
  "resourcePath": "/<ACCOUNT_ID>/<QUEUE_NAME>",
  "params": {
    "query": {
      "Action": "SendMessage",
      "Version": "2012-11-05",
      "MessageBody": "$util.urlEncode($util.toJson($ctx.args.input))"
    }
  }
}
```

The response from SQS is encoded in XML and can be easily transformed to a JSON payload using the `$util` functions provided by AppSync.

**`sendMessage` - Response Mapping**

``` json
#set( $result = $utils.xml.toMap($ctx.result.body) )
{
  "id": "$result.SendMessageResponse.SendMessageResult.MessageId",
  "email": "$ctx.args.input.email",
  "message": "$ctx.args.input.message",
}
```

## AWS Secrets Manager

AWS Secrets Manager allows customers to protect sensitive information such as API keys. In a future blog post, I will share how I incorporated Secrets Manager in an [AWS AppSync Pipeline Resolver](https://docs.aws.amazon.com/appsync/latest/devguide/pipeline-resolvers.html) to first retrieve a secret API key before querying a third-party web service for data, all via AppSync resolvers. For now, we can simply retrieve and return a secret from Secrets Manager.

``` graphql
type Query {
  getSecret(SecretName: String!): String
}
```

**`getSecret` - Request Mapping**

``` json
{
  "version": "2018-05-29",
  "method": "POST",
  "resourcePath": "/",
  "params": {
    "headers": {
      "content-type": "application/x-amz-json-1.1",
      "x-amz-target": "secretsmanager.GetSecretValue"
    },
    "body": {
      "SecretId": "$ctx.args.SecretName"
    }
  }
}
```

**`getSecret` - Response Mapping**

``` json
#set( $result = $util.parseJson($ctx.result.body) )
$util.toJson($result.SecretString)
```

## AWS Step Functions

Although I discussed Step Functions in the earlier mentioned blog, I will include starting a Step Functions state machine here for completeness and to document handling more complex input.

``` graphql
type Mutation {
  submitOrder(input: OrderInput!): Order
}
```

**`submitOrder` - Request Mapping**

```json
$util.qr($ctx.stash.put("orderId", $util.autoId()))
{
  "version": "2018-05-29",
  "method": "POST",
  "resourcePath": "/",
  "params": {
    "headers": {
      "content-type": "application/x-amz-json-1.0",
      "x-amz-target":"AWSStepFunctions.StartExecution"
    },
    "body": {
      "stateMachineArn": "<STATE_MACHINE_ARN>",
      "input": "$util.escapeJavaScript($util.toJson($input))"
    }
  }
}
```

**`sumitOrder` - Response Mapping**

```json
{
  "id": "${ctx.stash.orderId}"
}
```


## Event Bridge
see ed limas post
https://github.com/awsed/AppSync2EventBridge/blob/master/index.js

X-Amz-Target: 'AWSEvents.PutEvents'
Content-Type: 'application/x-amz-json-1.1'



#set($inputRoot = $input.path('$'))
{
  "Entries": [
    {
      "DetailType": "webhookdetail",
      "Source": "external-webhook",
      "EventBusName": "webhooks",
      "Detail": "$util.escapeJavaScript($input.json('$'))"
    }
  ]
}


Please let me know if there are other integrations you would be interested in seeing.


_Photo by Patrick Perkins on Unsplash_