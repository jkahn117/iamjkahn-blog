---
layout: post
title: On Lambda Invocations
date: 2020-08-14 11:17 -0500
summary: Learning in Public - my notes on the types of Lambda invocations and related error handling.
background: /assets/images/arisa-chattasa-AZcNLJgO4XE-unsplash.jpg
---

> This post is my first attempt at ["learning in public"](https://www.swyx.io/writing/learn-in-public/), capturing knowledge in a place that will be useful for myself and others.

[AWS Lambda](https://aws.amazon.com/lambda/) Functions can be invoked by numerous AWS services. In every case, each Lambda invocation handles a single event (though that one event may contain multiple records, a "batch"). This event-driven nature is one way in which Lambda can achieve tremendous scale. Each invocation handling a single event also helps (me at least) not only design my function code, but also understand function scaling and concurrency.

> Concurrency is the number of Lambda invocations that can be executing at the same time.

There are three different ways a Lambda function can be invoked. Understanding these modes and the error handling behavior of each is vital in building resilient serverless applications.

> Other sources covering this topic can be found in Resources section below. My goal is to capture the primary details I rely on here.

## Synchronous Invocation

*Function executes immediately and waits for completion. The caller awaits a response.*

Services that invoke Lambda synchronously include:

* [Amazon API Gateway](https://docs.aws.amazon.com/lambda/latest/dg/with-on-demand-https.html)
* [Amazon Alexa](https://docs.aws.amazon.com/lambda/latest/dg/services-alexa.html)
* [Amazon CloudFront (Lambda@Edge)](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html)
* [Amazon Cognito](https://docs.aws.amazon.com/lambda/latest/dg/services-cognito.html)
* [Elastic Load Balancing (Application Load Balancer)](https://docs.aws.amazon.com/lambda/latest/dg/services-alb.html)

### Retry Behavior & Error Handling

Neither invocation nor function errors are retried. The caller is responsible for checking the response for an error and retrying if needed.

> When working with Lambda, we see two categories of errors: (1) invocation and (2) function. Invocation errors are the result of the caller not having permission to invoke the function. Function errors may be related to bugs or others issues in the function code.

A canonical example of synchronous invocation is a web service. One approach to building a RESTful web service would be API Gateway and Lambda. A request to the web service triggers API Gateway to invoke the appropriate Lambda function with an event containing information on the request (e.g. parameters, timestamp). API Gateway will wait up to 30 seconds for the Lambda function to respond or will respond with a server error. If there is an error, the caller (or client) would need to retry. This requires special handling at the client or use of SDKs generated by API Gatway that support retry (and backoff) out-of-the-box.

---

## Asynchronous Invocation

*Function executes when available (driven by an internal/service queue). The caller does not wait for a response.*

Services that invoke Lambda asynchronously include:

* [Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)
* [Amazon SNS](https://docs.aws.amazon.com/lambda/latest/dg/with-sns.html)
* [Amazon EventBridge](https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html)
* [AWS Config](https://docs.aws.amazon.com/lambda/latest/dg/services-config.html)

Asynchronous events are placed on an internal queue, managed by the Lambda service. The *service*, not the function polls this queue and invokes the function as appropriate. AWS X-Ray provides a "dwell time" segment that can be used to understand how long the request waits before being handled by the function.

### Retry Behavior & Error Handling

On function errors, the Lambda service will automatically retry the invocation a maximum of two times, for a total of three invocations. The maximum number of retries can be lowered to 0 or 1 as well by changing the `Retry Attempts` configuration.

For an invocation error, the Lambda service will attempt to retry for up to six (6) hours. You can also configure the `Maximum Event Age` to modify the retry duration.

To handle errors, use [Lambda Destinations](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-features.html#gettingstarted-features-destinations). Configure the `OnFailure` destination to capture failed events and metadata or re-process with another function.

> When a function retries, you are charged for the additional invocations and function duration.

---

## Poll-Based Invocation

*Lambda service polls data source for new data. Service invokes function, passing batches of records.*

In this method, the Lambda service (again, not the function) polls the service on your behalf. Records (or messages or changes) are retrieved and the function is synchronously invoked with the event containing batches of records.

Services that utilize poll-based invocation:

* [Amazon SQS](https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html)
* [Amazon Kinesis](https://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html)
* [Amazon DynamoDB Stream](https://docs.aws.amazon.com/lambda/latest/dg/with-ddb.html)
* [Amazon Managed Streaming Kafka (MSK)](https://docs.aws.amazon.com/lambda/latest/dg/with-msk.html)

### Retry Behavior & Error Handling

Function errors will be retried based on the data expiration at the source. Data expiration varies by data source, so it is important to understand how long data will remain available if not processed by Lambda. You can also configure `Maximum Record Age` to set a shorter period than the data source.  For example, data remains on a Kinesis Data Stream for 24 hours by default, but visibility to Lambda can be lowered. Lambda will attempt to retry for as long as the data is on the stream. If you want to stop processing after some number of attempts, configure `Maximum Retry Attempts` with a desired value. After reaching that number of attempts, the record will be pushed to the `OnFailure` Lambda desintation.

Lambda also provides an option called `BisectBatchOnFunctionError` that helps to isolate problematic records by continually halving the set of records in an event each time the batch fails. Over successive invocations, the bisect approach will eventually yield problematic records, which can be dealth with separately.

---

## Resources

### Blog Posts
* [Understanding the Different Ways to Invoke Lambda Functions](https://aws.amazon.com/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/)
* [New AWS Lambda controls for stream processing and asynchronous invocations](https://aws.amazon.com/blogs/compute/new-aws-lambda-controls-for-stream-processing-and-asynchronous-invocations/)

### Documentation
* [Invoking Lambda Functions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-invocation.html)
* [Managing concurrency for a Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html)


*Photo by [Arisa Chattasa](https://unsplash.com/@golfarisa) on [Unsplash](https://unsplash.com/)*