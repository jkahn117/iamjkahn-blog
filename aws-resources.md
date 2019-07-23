---
layout: page
title: AWS Resources
permalink: /aws-resources/
---

A selection of my favorite AWS resources on various topics...

{:.resources}
| **AWS AppSync** |
| [AWS AppSync Community](https://github.com/aws/aws-appsync-community) | Collection of resources and links related to AWS AppSync. Great place to start. |
| [AWS AppSync Velocity Template Guide](https://medium.com/@gerard.sans/aws-appsync-velocity-templates-guide-55b9d2bff053) | Overview of Velocity Templates and how they are used to build AppSync resolvers. |
| **AWS Amplify** |
| [How to Build Production-ready Vue Authentication](https://dev.to/dabit3/how-to-build-production-ready-vue-authentication-23mk) | Detailed review of how to integrate Amplify in a Vue.js application with authentication. |
| [Tracking and Reminders in AWS Amplify](https://geromekevin.com/tracking-and-email-reminders-in-aws-amplify/) | Nice overview of adding Amazon Pinpoint to your AWS Amplify project. |
| **AWS API Gateway** |
| [A Detailed Overview of AWS API Gateway](https://www.alexdebrie.com/posts/api-gateway-elements/) | Incredible deep dive on AWS API Gateway. |
| [How to sign AWS Api Gateway Requests with Signature Version 4 using AWS Amplify?](https://jun711.github.io/aws/how-to-sign-api-gateway-requests-with-signature-version-4-using-aws-amplify/) | Detailed overview of the SigV4 signing process and how to. |
| **CloudFormation** |
| [CloudFormation Nested Stacks Primer](https://www.trek10.com/blog/cloudformation-nested-stacks-primer/) | While I did not formerly believe, I've grown to feel Nested Stacks are a good thing. |
| **DynamoDB** |
| [From relational DB to single DynamoDB table: a step-by-step exploration](https://www.trek10.com/blog/dynamodb-single-table-relational-modeling/) | Excellent article detailing DynamoDB design best practices in reference to an existing relational database model. |
| [DynamoDB Pricing: OnDemand, Autoscaled, Provisioned, or Reserved?](https://www.trek10.com/blog/findev-dynamodb-pricing-analysis/) | An overview of DynamoDB pricing models. |
| [Understanding the scaling behaviour of DynamoDB OnDemand tables](https://theburningmonk.com/2019/03/understanding-the-scaling-behaviour-of-dynamodb-ondemand-tables/) | Analysis of scaling behavior when using DynamoDB On-Demand. Useful when migrating from provisioned capacity or when considering tables with very spikey usage. |
| **Lambda** |
| [Understanding the Different Ways to Invoke Lambda Functions](https://aws.amazon.com/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/) | Part of a series from George Mao, a terrific overview of the three different ways a Lambda function can be invoked: synchronous, asynchronous, and poll-based. |
| [How Should You Organize Your Functions in Production?](https://epsagon.com/blog/how-should-you-organize-your-functions-in-roduction/) | Thoughts on the age old monolithic versus single-purpose function along with pros and cons of each. |
| [AWS Lambda — should you have few monolithic functions or many single-purposed functions?](https://hackernoon.com/aws-lambda-should-you-have-few-monolithic-functions-or-many-single-purposed-functions-8c3872d4338f) | Another discussion on Lambda organization. |
| [To VPC or not to VPC? Pros and Cons in AWS Lambda](https://lumigo.io/blog/to-vpc-or-not-to-vpc-in-aws-lambda/) | Discussion on the use of Lambda in an AWS VPC. Be sure to check the flow chart (also in AWS documentation). Bottom line: *only* use a VPC if necessary (e.g. to connect to RDS, ElastiCache). VPC does not add any further security for Lambda. |
| [Lambda Warmer: Optimize AWS Lambda Function Cold Starts](https://www.jeremydaly.com/lambda-warmer-optimize-aws-lambda-function-cold-starts/) | Nice approach for keeping Lambda functions warm via a smartly written plugin. |
| [Benchmarking Lambda’s Idle Timeout Before A Cold Start](https://kevinslin.com/aws/lambda_cold_start_idle/) | Interesting study of the how long the Lambda service keeps an idle container before removing it. |
| [You are wrong about serverless vendor lock-in](https://lumigo.io/blog/you-are-wrong-about-serverless-vendor-lock-in/) | Realistic point of view on serverless and perceived notion of lock-in (you're not). Serverless enhances focus and allows teams to deliver on business value as opposed to building supporting systems. Business logic is portable. |
| **AWS Step Functions** |
| [Supported AWS Service Integrations for Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-supported-services.html) | Useful table of integrations between Step Functions and other AWS services. |
| **Security** |
| [AWS Security Reference](https://docs.aws.amazon.com/security/) | Quick links to service-specific security documentation. |
| **Workshops** |
| [AWS Hands-On Workshops](https://github.com/angelarw/aws-hands-on-workshops) | Excellent collection of AWS workshops across a variety of topics, including serverless, development, IoT, and more. |
