# CloudWatch / X-Ray / CloudTrail

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 5  
Теорія: [04-domain4-troubleshooting.md](../../04-domain4-troubleshooting.md#observability) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q20"></a>
### T07-Q20
*⏭ пропущено · домен: офіційна мітка*

A company has more than 100 million members worldwide enjoying 125 million hours of TV shows and movies each day. The company uses AWS for nearly all its computing and storage needs, which use more than 10,000 server instances on AWS. This results in an extremely complex and dynamic networking environment where applications are constantly communicating inside AWS and across the Internet. Monitoring and optimizing its network is critical for the company.

The company needs a solution for ingesting and analyzing the multiple terabytes of real-time data its network generates daily in the form of flow logs. Which technology/service should the company use to ingest this data economically and has the flexibility to direct this data to other downstream systems?

- ▫️ Amazon Simple Queue Service (SQS)
- ▫️ Amazon Kinesis Firehose
- ✅ Amazon Kinesis Data Streams
- ▫️ AWS Glue

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon Kinesis Data Streams

Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events. The data collected is available in milliseconds to enable real-time analytics use cases such as real-time dashboards, real-time anomaly detection, dynamic pricing, and more.

Kinesis Data Streams enables real-time processing of streaming big data. It provides ordering of records, as well as the ability to read and/or replay records in the same order to multiple Amazon Kinesis Applications. The Amazon Kinesis Client Library (KCL) delivers all records for a given partition key to the same record processor, making it easier to build multiple applications reading from the same Amazon Kinesis data stream (for example, to perform counting, aggregation, and filtering).

Incorrect options:

Amazon Simple Queue Service (SQS) - Amazon Simple Queue Service (Amazon SQS) offers a reliable, highly scalable hosted queue for storing messages as they travel between computers. Amazon SQS lets you easily move data between distributed application components and helps you build applications in which messages are processed independently (with message-level ack/fail semantics), such as automated workflows. AWS recommends using Amazon SQS for cases where individual message fail/success are important, message delays are needed and there is only one consumer for the messages received (if more than one consumers need to consume the message, then AWS suggests configuring more queues).

Amazon Kinesis Firehose - Amazon Kinesis Data Firehose is the easiest way to load streaming data into data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today. It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration.

Kinesis data streams is highly customizable and best suited for developers building custom applications or streaming data for specialized needs. Data Streams also provide greater flexibility in integrating downstream applications than Firehose. Data Streams is also a cost-effective option compared to Firehose. Therefore, KDS is the right solution.

AWS Glue - AWS Glue is a serverless data integration service that makes it easy to discover, prepare, and combine data for analytics, machine learning, and application development. AWS Glue provides all of the capabilities needed for data integration so that you can start analyzing your data and putting it to use in minutes instead of months. Glue is not best suited to handle real-time data.

Джерела: <https://aws.amazon.com/kinesis/data-streams/> · <https://aws.amazon.com/sqs/> · <https://aws.amazon.com/kinesis/data-firehose/>

</details>

---

<a id="t07-q38"></a>
### T07-Q38
*⏭ пропущено · домен: офіційна мітка*

You have been asked by your Team Lead to enable detailed monitoring of the Amazon EC2 instances your team uses. As a Developer working on AWS CLI, which of the below command will you run?

- ✅ aws ec2 monitor-instances --instance-ids i-1234567890abcdef0
- ▫️ aws ec2 run-instances --image-id ami-09092360 --monitoring Enabled=true
- ▫️ aws ec2 run-instances --image-id ami-09092360 --monitoring State=enabled
- ▫️ aws ec2 monitor-instances --instance-id i-1234567890abcdef0

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

aws ec2 monitor-instances --instance-ids i-1234567890abcdef0 - This enables detailed monitoring for a running instance.

EC2 detailed monitoring:  via - https://docs.aws.amazon.com/cli/latest/reference/ec2/monitor-instances.html

Incorrect options:

aws ec2 run-instances --image-id ami-09092360 --monitoring Enabled=true - This syntax is used to enable detailed monitoring when launching an instance from AWS CLI.

aws ec2 run-instances --image-id ami-09092360 --monitoring State=enabled - This is an invalid syntax

aws ec2 monitor-instances --instance-id i-1234567890abcdef0 - This is an invalid syntax

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-cloudwatch-new.html> · <https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html>

</details>

---

<a id="t09-q63"></a>
### T09-Q63
*⏭ пропущено · домен: офіційна мітка*

As a Team Lead, you are expected to generate a report of the code builds for every week to report internally and to the client. This report consists of the number of code builds performed for a week, the percentage success and failure, and overall time spent on these builds by the team members. You also need to retrieve the CodeBuild logs for failed builds and analyze them in Athena.

Which of the following options will help achieve this?

- ✅ Enable S3 and CloudWatch Logs integration
- ▫️ Use Amazon EventBridge
- ▫️ Use AWS Lambda integration
- ▫️ Use AWS CloudTrail and deliver logs to S3

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable S3 and CloudWatch Logs integration - AWS CodeBuild monitors functions on your behalf and reports metrics through Amazon CloudWatch. These metrics include the number of total builds, failed builds, successful builds, and the duration of builds. You can monitor your builds at two levels: Project level, AWS account level. You can export log data from your log groups to an Amazon S3 bucket and use this data in custom processing and analysis, or to load onto other systems.

Incorrect options:

Use Amazon EventBridge - You can integrate EventBridge with CodeBuild. However, we are looking at storing and running queries on logs, so Cloudwatch logs with S3 integration makes sense for this context.

Use AWS Lambda integration - Lambda is a good choice to use boto3 library to read logs programmatically. But, CloudWatch and S3 integration is already built-in and is an optimized way of managing the given use-case.

Use AWS CloudTrail and deliver logs to S3 - AWS CodeBuild is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in CodeBuild. CloudTrail captures all API calls for CodeBuild as events, including calls from the CodeBuild console and from code calls to the CodeBuild APIs. If you create a trail, you can enable continuous delivery of CloudTrail events to an S3 bucket, including events for CodeBuild. This is an important feature for monitoring a service but isn't a good fit for the current scenario.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/monitoring-metrics.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3Export.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started-input-bucket-console.html>

</details>

---

<a id="t12-q40"></a>
### T12-Q40
*⏭ пропущено · домен: офіційна мітка*

A developer has just integrated an AWS Lambda function to an Amazon API Gateway API. The integration has led to errors that the developer is unable to troubleshoot. The developer has decided to enable CloudWatch logging at the method level for the API Gateway API.

What are the key points of consideration while configuring configuring method-level logging for the API Gateway? (Select two)

- ✅ To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your user. The IAM role must also contain the following trust relationship statement
- ▫️ You are charged for accessing method-level and stage-level CloudWatch metrics, but not for API-level metrics
- ▫️ API Gateway API log groups or streams can only be deleted and recreated by redeploying the API
- ▫️ In access logging, only $context and $input variables are supported
- ✅ AWS Security Token Service(STS) is used by API Gateway for logging data to CloudWatch logs. Hence, AWS STS has to be enabled for the Region that you're using

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

AWS Security Token Service(STS) is used by API Gateway for logging data to CloudWatch logs. Hence, AWS STS has to be enabled for the Region that you're using

API Gateway calls AWS Security Token Service to assume the IAM role, so make sure that AWS STS is enabled for the Region. If you receive an error when setting the IAM role ARN, check your AWS Security Token Service account settings to make sure that AWS STS is enabled in the Region that you're using.

To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your user. The IAM role must also contain the following trust relationship statement

To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your user. To do so, choose Settings from the APIs main navigation pane. Then enter the ARN of an IAM role in the CloudWatch log role ARN text field. The IAM role must also contain the trust relationship statement.

Policy of AmazonAPIGatewayPushToCloudWatchLogs for IAM role:  via - https://docs.aws.amazon.com/apigateway/latest/developerguide/stages.html#how-to-stage-settings-console

Incorrect options:

In access logging, only $context and $input variables are supported - This statement is incorrect. In access logging, you, as an API developer, want to log who has accessed your API and how the caller accessed the API. You can create your own log group or choose an existing log group that could be managed by API Gateway. To specify the access details, you select $context variables and choose a log group as the destination. Only $context variables are supported (not $input, and so on).

You are charged for accessing method-level and stage-level CloudWatch metrics, but not for API-level metrics - This statement is incorrect. Your account is charged for accessing method-level CloudWatch metrics, but not the API-level or stage-level metrics.

API Gateway API log groups or streams can only be deleted and recreated by redeploying the API - API Gateway API log groups or streams can be deleted from the CloudWatch console. But, it is not recommended.

Do not manually delete API Gateway API log groups or streams; let API Gateway manage these resources. Manually deleting log groups or streams may cause API requests and responses not to be logged. If that happens, you can delete the entire log group for the API and redeploy the API. This is because API Gateway creates log groups or log streams for an API stage at the time when it is deployed.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stages.html#how-to-stage-settings-console> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-logging.html#apigateway-cloudwatch-log-formats> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/view-cloudwatch-log-events-in-cloudwatch-console.html>

</details>

---

<a id="t12-q43"></a>
### T12-Q43
*⏭ пропущено · домен: офіційна мітка*

An e-commerce application writes log files into Amazon S3. The application also reads these log files in parallel on a near real-time basis. The development team wants to address any data discrepancies that might arise when the application overwrites an existing log file and then tries to read that specific log file.

Which of the following options BEST describes the capabilities of Amazon S3 relevant to this scenario?

- ▫️ A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 might return the new data
- ▫️ A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 does not return any data
- ✅ A process replaces an existing object and immediately tries to read it. Amazon S3 always returns the latest version of the object
- ▫️ A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 might return the previous data

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

A process replaces an existing object and immediately tries to read it. Amazon S3 always returns the latest version of the object

Amazon S3 delivers strong read-after-write consistency automatically, without changes to performance or availability, without sacrificing regional isolation for applications, and at no additional cost.

After a successful write of a new object or an overwrite of an existing object, any subsequent read request immediately receives the latest version of the object. S3 also provides strong consistency for list operations, so after a write, you can immediately perform a listing of the objects in a bucket with any changes reflected.

Strong read-after-write consistency helps when you need to immediately read an object after a write. For example, strong read-after-write consistency when you often read and list immediately after writing objects.

To summarize, all S3 GET, PUT, and LIST operations, as well as operations that change object tags, ACLs, or metadata, are strongly consistent. What you write is what you will read, and the results of a LIST will be an accurate reflection of what’s in the bucket.

Incorrect options:

A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 might return the previous data

A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 does not return any data

A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 might return the new data

These three options contradict the earlier details provided in the explanation.

Джерела: <https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel> · <https://aws.amazon.com/s3/faqs/>

</details>

---
