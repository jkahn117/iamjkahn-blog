---
layout: page
title: AWS Resources
permalink: /aws-resources/
---

A selection of my favorite AWS resources on various topics...

{:.resources}
| **DynamoDB** |
| [From relational DB to single DynamoDB table: a step-by-step exploration](https://www.trek10.com/blog/dynamodb-single-table-relational-modeling/) | Excellent article detailing DynamoDB design best practices in reference to an existing relational database model. |
| [DynamoDB Pricing: OnDemand, Autoscaled, Provisioned, or Reserved?](https://www.trek10.com/blog/findev-dynamodb-pricing-analysis/) | An overview of DynamoDB pricing models. |
| [Understanding the scaling behaviour of DynamoDB OnDemand tables](https://theburningmonk.com/2019/03/understanding-the-scaling-behaviour-of-dynamodb-ondemand-tables/) | Analysis of scaling behavior when using DynamoDB On-Demand. Useful when migrating from provisioned capacity or when considering tables with very spikey usage. |
| **Lambda** |
| [Lambda Warmer: Optimize AWS Lambda Function Cold Starts](https://www.jeremydaly.com/lambda-warmer-optimize-aws-lambda-function-cold-starts/) | Nice approach for keeping Lambda functions warm via a smartly written plugin. |
| [Benchmarking Lambda’s Idle Timeout Before A Cold Start](https://kevinslin.com/aws/lambda_cold_start_idle/) | Interesting study of the how long the Lambda service keeps an idle container before removing it. |
| **Workshops** |
| [AWS Hands-On Workshops](https://github.com/angelarw/aws-hands-on-workshops) | Excellent collection of AWS workshops across a variety of topics, including serverless, development, IoT, and more. |