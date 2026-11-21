# Kinesis / Data Firehose

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 10  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#kinesis) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q45"></a>
### T01-Q45
*⏭ пропущено · домен: офіційна мітка*

One of your Kinesis Stream is experiencing increased traffic due to a sale day. Therefore your Kinesis Administrator has split shards and thus you went from having 6 shards to having 10 shards in your Kinesis Stream. Your consuming application is running a KCL-based application on EC2 instances.

What is the maximum number of EC2 instances that can be deployed to process the shards?

- ▫️ 20
- ▫️ 1
- ✅ 10
- ▫️ 6

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

10

Amazon Kinesis Data Streams enables you to build custom applications that process or analyze streaming data for specialized needs.

A Kinesis data stream is a set of shards. A shard is a uniquely identified sequence of data records in a stream. A stream is composed of one or more shards, each of which provides a fixed unit of capacity.

Kinesis Data Streams Overview:  via - https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html

Each KCL consumer application instance uses "workers" to process data in Kinesis shards. At any given time, each shard of data records is bound to a particular worker via a lease. For the given use-case, an EC2 instance acts as the worker for the KCL application. You can have at most one EC2 instance per shard in Kinesis for the given application. As we have 10 shards, the max number of EC2 instances is 10.

 via - https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html

Incorrect options:

1

6

20

These three options contradict the explanation provided earlier. So these are incorrect.

Reference:

https://docs.aws.amazon.com/streams/latest/dev/developing-consumers-with-kcl.html

</details>

---

<a id="t03-q16"></a>
### T03-Q16
*⏭ пропущено · домен: офіційна мітка*

You have an Amazon Kinesis Data Stream with 10 shards, and from the metrics, you are well below the throughput utilization of 10 MB per second to send data. You send 3 MB per second of data and yet you are receiving ProvisionedThroughputExceededException errors frequently.

What is the likely cause of this?

- ✅ The partition key that you have selected isn't distributed enough
- ▫️ Metrics are slow to update
- ▫️ You have too many shards
- ▫️ The data retention period is too long

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The partition key that you have selected isn't distributed enough

Amazon Kinesis Data Streams enables you to build custom applications that process or analyze streaming data for specialized needs.

A Kinesis data stream is a set of shards. A shard is a uniquely identified sequence of data records in a stream. A stream is composed of one or more shards, each of which provides a fixed unit of capacity.

The partition key is used by Kinesis Data Streams to distribute data across shards. Kinesis Data Streams segregates the data records that belong to a stream into multiple shards, using the partition key associated with each data record to determine the shard to which a given data record belongs.

Kinesis Data Streams Overview:  via - https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html

For the given use-case, as the partition key is not distributed enough, all the data is getting skewed at a few specific shards and not leveraging the entire cluster of shards.

You can also use metrics to determine which are your "hot" or "cold" shards, that is, shards that are receiving much more data, or much less data, than expected. You could then selectively split the hot shards to increase capacity for the hash keys that target those shards. Similarly, you could merge cold shards to make better use of their unused capacity.

Incorrect options:

Metrics are slow to update - Metrics are a CloudWatch concept. This option has been added as a distractor.

You have too many shards - Too many shards is not the issue as you would see a LimitExceededException in that case.

The data retention period is too long - Your streaming data is retained for up to 365 days. The data retention period is not an issue causing this error.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html> · <https://docs.aws.amazon.com/streams/latest/dev/kinesis-using-sdk-java-resharding-strategies.html>

</details>

---

<a id="t04-q05"></a>
### T04-Q05
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A monitoring application that keeps track of a large eCommerce website uses Amazon Kinesis for data ingestion. During periods of peak data rates, the producers are not making best use of the available shards.
What step will allow the producers to better utilize the available shards and increase write throughput to the Kinesis data stream?

- ▫️ Create an SQS queue and decouple the producers from the Kinesis data stream
- ✅ Install the Kinesis Producer Library (KPL) for ingesting data into the stream
- ▫️ Ingest multiple records into the stream in a single call using BatchWriteItem
- ▫️ Increase the shard count of the stream using UpdateShardCount

<details><summary>Пояснення (оригінал, EN)</summary>

An Amazon Kinesis Data Streams producer is an application that puts user data records into a Kinesis data stream (also called data ingestion). The Kinesis Producer Library (KPL) simplifies producer application development, allowing developers to achieve high write throughput to a Kinesis data stream.

The KPL is an easy-to-use, highly configurable library that helps you write to a Kinesis data stream. It acts as an intermediary between your producer application code and the Kinesis Data Streams API actions. The KPL performs the following primary tasks:

• Writes to one or more Kinesis data streams with an automatic and configurable retry mechanism

• Collects records and uses PutRecords to write multiple records to multiple shards per request

• Aggregates user records to increase payload size and improve throughput

• Integrates seamlessly with the Kinesis Client Library (KCL) to de-aggregate batched records on the consumer

• Submits Amazon CloudWatch metrics on your behalf to provide visibility into producer performance

The question states that the producers are not making best use of the available shards. Therefore, we understand that there are adequate shards available but the producers are either not discovering them or are not writing records at sufficient speed to best utilize the shards.

We therefore need to install the Kinesis Producer Library (KPL) for ingesting data into the stream.

CORRECT: "Install the Kinesis Producer Library (KPL) for ingesting data into the stream" is the correct answer.

INCORRECT: "Create an SQS queue and decouple the producers from the Kinesis data stream " is incorrect. In this case we need to ensure our producers are discovering shards and writing records to best utilize those shards.

INCORRECT: "Increase the shard count of the stream using UpdateShardCount" is incorrect. The problem statement is that the producers are not making best use of the available shards. We don’t need to add more shards, we need to make sure the producers are discovering and then fully utilizing the shards that are available.

INCORRECT: "Ingest multiple records into the stream in a single call using BatchWriteItem" is incorrect. This API is used with DynamoDB, not Kinesis.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-kpl.html>

</details>

---

<a id="t04-q44"></a>
### T04-Q44
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A solution requires a serverless service for receiving streaming data and loading it directly into an Amazon Elasticsearch datastore. Which AWS service would be suitable for this requirement?

- ▫️ Amazon Kinesis Data Streams
- ▫️ Amazon Simple Queue Service (SQS)
- ▫️ Amazon Kinesis Data Analytics
- ✅ Amazon Kinesis Data Firehose

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Kinesis Data Firehose is the easiest way to load streaming data into data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today.

Firehose is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. It can also batch, compress, and encrypt the data before loading it, minimizing the amount of storage used at the destination and increasing security.

CORRECT: "Amazon Kinesis Data Firehose" is the correct answer.

INCORRECT: "Amazon Kinesis Data Streams" is incorrect as with Kinesis Data Streams you need consumers running on EC2 instances or AWS Lambda for processing the data from the stream. It therefore will not load data directly to a datastore.

INCORRECT: "Amazon Kinesis Data Analytics" is incorrect as this service is used for performing analytics on streaming data using Structured Query Language (SQL queries.

INCORRECT: "Amazon Simple Queue Service (SQS)" is incorrect as this is a message queueing service. You would need servers to place messages on the queue and then other servers to process messages from the queue and store them in Elasticsearch.

Джерела: <https://aws.amazon.com/kinesis/data-firehose/faqs/>

</details>

---

<a id="t05-q64"></a>
### T05-Q64
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company needs to ingest several terabytes of data every hour from a large number of distributed sources. The messages are delivered continually 24 hrs a day. Messages must be delivered in real time for security analysis and live operational dashboards.

Which approach will meet these requirements?

- ✅ Use Amazon Kinesis Data Streams with Kinesis Client Library to ingest and deliver messages
- ▫️ Send the messages to an Amazon SQS queue, then process the messages by using a fleet of Amazon EC2 instances
- ▫️ Use the Amazon S3 API to write messages to an S3 bucket, then process the messages by using Amazon RedShift
- ▫️ Use AWS Data Pipeline to automate the movement and transformation of data

<details><summary>Пояснення (оригінал, EN)</summary>

You can use Amazon Kinesis Data Streams to collect and process large streams of data records in real time. You can create data-processing applications, known as Kinesis Data Streams applications. A typical Kinesis Data Streams application reads data from a data stream as data records.

These applications can use the Kinesis Client Library, and they can run on Amazon EC2 instances. You can send the processed records to dashboards, use them to generate alerts, dynamically change pricing and advertising strategies, or send data to a variety of other AWS services.

This scenario is an ideal use case for Kinesis Data Streams as large volumes of real time streaming data are being ingested. Therefore, the best approach is to use Amazon Kinesis Data Streams with Kinesis Client Library to ingest and deliver messages

CORRECT: "Use Amazon Kinesis Data Streams with Kinesis Client Library to ingest and deliver messages" is the correct answer.

INCORRECT: "Send the messages to an Amazon SQS queue, then process the messages by using a fleet of Amazon EC2 instances" is incorrect as this is not an ideal use case for SQS because SQS is used for decoupling application components, not for ingesting streaming data. It would require more cost (lots of instances to process data) and introduce latency. Also, the message size limitations could be an issue.

INCORRECT: "Use the Amazon S3 API to write messages to an S3 bucket, then process the messages by using Amazon RedShift" is incorrect as RedShift does not process messages from S3. RedShift is a data warehouse which is used for analytics.

INCORRECT: "Use AWS Data Pipeline to automate the movement and transformation of data" is incorrect as the question is not asking for transformation of data. The scenario calls for a solution for ingesting and processing the real time streaming data for analytics and feeding some data into a system that generates an operational dashboard.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/introduction.html>

</details>

---

<a id="t07-q36"></a>
### T07-Q36
*⏭ пропущено · домен: офіційна мітка*

A leading financial services company offers data aggregation services for Wall Street trading firms. The company bills its clients based on per unit of clickstream data provided to the clients. As the company operates in a regulated industry, it needs to have the same ordered clickstream data available for auditing within a window of 7 days.

As a Developer Associate, which of the following AWS services do you think provides the ability to run the billing process and auditing process on the given clickstream data in the same order?

- ▫️ Amazon SQS
- ▫️ AWS Kinesis Data Firehose
- ✅ AWS Kinesis Data Streams
- ▫️ AWS Kinesis Data Analytics

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Kinesis Data Streams

Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events. The data collected is available in milliseconds to enable real-time analytics use cases such as real-time dashboards, real-time anomaly detection, dynamic pricing, and more.

Amazon Kinesis Data Streams enables real-time processing of streaming big data. It provides ordering of records, as well as the ability to read and/or replay records in the same order to multiple Amazon Kinesis Applications. The Amazon Kinesis Client Library (KCL) delivers all records for a given partition key to the same record processor, making it easier to build multiple applications reading from the same Amazon Kinesis data stream (for example, to perform counting, aggregation, and filtering). Amazon Kinesis Data Streams is recommended when you need the ability to consume records in the same order a few hours later.

For example, you have a billing application and an audit application that runs a few hours behind the billing application. By default, records of a stream are accessible for up to 24 hours from the time they are added to the stream. You can raise this limit to a maximum of 365 days. For the given use-case, Amazon Kinesis Data Streams can be configured to store data for up to 7 days and you can run the audit application up to 7 days behind the billing application.

KDS provides the ability to consume records in the same order a few hours later  via - https://aws.amazon.com/kinesis/data-streams/faqs/

Incorrect options:

AWS Kinesis Data Firehose - Amazon Kinesis Data Firehose is the easiest way to load streaming data into data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today. It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. It can also batch, compress, and encrypt the data before loading it, minimizing the amount of storage used at the destination and increasing security. As Kinesis Data Firehose is used to load streaming data into data stores, therefore this option is incorrect.

AWS Kinesis Data Analytics - Amazon Kinesis Data Analytics is the easiest way to analyze streaming data in real-time. You can quickly build SQL queries and sophisticated Java applications using built-in templates and operators for common processing functions to organize, transform, aggregate, and analyze data at any scale. Kinesis Data Analytics enables you to easily and quickly build queries and sophisticated streaming applications in three simple steps: setup your streaming data sources, write your queries or streaming applications and set up your destination for processed data. As Kinesis Data Analytics is used to build SQL queries and sophisticated Java applications, therefore this option is incorrect.

Amazon SQS - Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues. Standard queues offer maximum throughput, best-effort ordering, and at-least-once delivery. SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent. For SQS, you cannot have the same message being consumed by multiple consumers in the same order a few hours later, therefore this option is incorrect.

Джерела: <https://aws.amazon.com/kinesis/data-streams/faqs/> · <https://aws.amazon.com/kinesis/data-firehose/faqs/> · <https://aws.amazon.com/kinesis/data-analytics/faqs/>

</details>

---

<a id="t09-q44"></a>
### T09-Q44
*⏭ пропущено · домен: офіційна мітка*

A developer working with EC2 Windows instance has installed Kinesis Agent for Windows to stream JSON-formatted log files to Amazon Simple Storage Service (S3) via Amazon Kinesis Data Firehose. The developer wants to understand the sink type capabilities of Kinesis Firehose.

Which of the following sink types is NOT supported by Kinesis Firehose.

- ✅ Amazon ElastiCache with Amazon S3 as backup
- ▫️ Amazon Redshift with Amazon S3
- ▫️ Amazon Simple Storage Service (Amazon S3) as a direct Firehose destination
- ▫️ Amazon Elasticsearch Service (Amazon ES) with optionally backing up data to Amazon S3

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon Kinesis Data Firehose is a fully managed service for delivering real-time streaming data to destinations such as Amazon Simple Storage Service (Amazon S3), Amazon Redshift, Amazon Elasticsearch Service (Amazon ES), and Splunk. With Kinesis Data Firehose, you don't need to write applications or manage resources. You configure your data producers to send data to Kinesis Data Firehose, and it automatically delivers the data to the destination that you specified.

Amazon ElastiCache with Amazon S3 as backup - Amazon ElastiCache is a fully managed in-memory data store, compatible with Redis or Memcached. ElastiCache is NOT a supported destination for Amazon Kinesis Data Firehose.

Incorrect options:

Amazon Elasticsearch Service (Amazon ES) with optionally backing up data to Amazon S3 - Amazon ES is a supported destination type for Kinesis Firehose. Streaming data is delivered to your Amazon ES cluster, and can optionally be backed up to your S3 bucket concurrently.

Data Flow for ES:  via - https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html

Amazon Simple Storage Service (Amazon S3) as a direct Firehose destination - For Amazon S3 destinations, streaming data is delivered to your S3 bucket. If data transformation is enabled, you can optionally back up source data to another Amazon S3 bucket.

Data Flow for S3:  via - https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html

Amazon Redshift with Amazon S3 - For Amazon Redshift destinations, streaming data is delivered to your S3 bucket first. Kinesis Data Firehose then issues an Amazon Redshift COPY command to load data from your S3 bucket to your Amazon Redshift cluster. If data transformation is enabled, you can optionally back up source data to another Amazon S3 bucket.

Data Flow for Redshift:  via - https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html

Reference:

https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html

</details>

---

<a id="t10-q20"></a>
### T10-Q20
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A monitoring application that keeps track of a large eCommerce website uses Amazon Kinesis for data ingestion. During periods of peak data rates, the Kinesis stream cannot keep up with the incoming data.
What step will allow Kinesis data streams to accommodate the traffic during peak hours?

- ▫️ 1. Ingest multiple records into the stream in a single call using PutRecords
- ✅ Increase the shard count of the stream using UpdateShardCount
- ▫️ Create an SQS queue and decouple the producers from the Kinesis data stream
- ▫️ Install the Kinesis Producer Library (KPL) for ingesting data into the stream

<details><summary>Пояснення (оригінал, EN)</summary>

The UpdateShardCount API action updates the shard count of the specified stream to the specified number of shards.

Updating the shard count is an asynchronous operation. Upon receiving the request, Kinesis Data Streams returns immediately and sets the status of the stream to UPDATING. After the update is complete, Kinesis Data Streams sets the status of the stream back to ACTIVE.

Depending on the size of the stream, the scaling action could take a few minutes to complete. You can continue to read and write data to your stream while its status is UPDATING.

To update the shard count, Kinesis Data Streams performs splits or merges on individual shards. This can cause short-lived shards to be created, in addition to the final shards. These short-lived shards count towards your total shard limit for your account in the Region.

When using this operation, we recommend that you specify a target shard count that is a multiple of 25% (25%, 50%, 75%, 100%). You can specify any target value within your shard limit. However, if you specify a target that isn't a multiple of 25%, the scaling action might take longer to complete.

This operation has the following default limits. By default, you cannot do the following:

• Scale more than ten times per rolling 24-hour period per stream

• Scale up to more than double your current shard count for a stream

• Scale down below half your current shard count for a stream

• Scale up to more than 500 shards in a stream

• Scale a stream with more than 500 shards down unless the result is less than 500 shards

Scale up to more than the shard limit for your account

Note that the question specifically states that the Kinesis data stream cannot keep up with incoming data. This indicates that the producers are attempting to add records to the stream but there are not enough shards to keep up with demand. Therefore, we need to add additional shards and can do this using the UpdateShardCount API action.

CORRECT: "Increase the shard count of the stream using UpdateShardCount" is the correct answer.

INCORRECT: "Install the Kinesis Producer Library (KPL) for ingesting data into the stream" is incorrect as that will help the producers to be more efficient and increase write throughput to a Kinesis data stream. However, this will not help as the Kinesis data stream already cannot keep up with the incoming demand.

INCORRECT: "Create an SQS queue and decouple the producers from the Kinesis data stream " is incorrect. You cannot decouple a Kinesis producer from a Kinesis data stream using SQS. Kinesis is more than capable of keeping up with demand, it just needs more shards in this case.

INCORRECT: "Ingest multiple records into the stream in a single call using PutRecords" is incorrect as the stream is already overloaded, we need more shards, not more data to be written.

Джерела: <https://docs.aws.amazon.com/kinesis/latest/APIReference/API_UpdateShardCount.html>

</details>

---

<a id="t10-q56"></a>
### T10-Q56
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

An application uses Amazon Kinesis Data Streams to ingest and process large streams of data records in real time. Amazon EC2 instances consume and process the data using the Amazon Kinesis Client Library (KCL). The application handles the failure scenarios and does not require standby workers. The application reports that a specific shard is receiving more data than expected. To adapt to the changes in the rate of data flow, the “hot” shard is resharded.

Assuming that the initial number of shards in the Kinesis data stream is 6, and after resharding the number of shards increased to 8, what is the maximum number of EC2 instances that can be deployed to process data from all the shards?

- ✅ 8
- ▫️ 12
- ▫️ 6
- ▫️ 1

<details><summary>Пояснення (оригінал, EN)</summary>

Typically, when you use the KCL, you should ensure that the number of instances does not exceed the number of shards (except for failure standby purposes). Each shard is processed by exactly one KCL worker and has exactly one corresponding record processor, so you never need multiple instances to process one shard. However, one worker can process any number of shards, so it's fine if the number of shards exceeds the number of instances.

In this scenario, the number of shards has been increased to 8. Therefore, the maximum number of instances that can be deployed is 8 as the number of instances cannot exceed the number of shards.

CORRECT: "8" is the correct answer.

INCORRECT: "6" is incorrect as this is not the maximum number of instances that can be deployed to process 8 shards. The maximum number of instances should be the same as the number of shards.

INCORRECT: "12" is incorrect as the number of instances exceeds the number of shards. You should ensure that the number of instances does not exceed the number of shards

INCORRECT: "1" is incorrect as this is not the maximum number of instances that can be deployed to process 8 shards. The maximum number of instances should be the same as the number of shards.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-scaling.html>

</details>

---

<a id="t10-q60"></a>
### T10-Q60
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer has setup an Amazon Kinesis Data Stream with 6 shards to ingest a maximum of 2000 records per second. An AWS Lambda function has been configured to process these records. In which order will these records be processed?

- ▫️ Lambda will receive each record in the exact order it was placed into the stream
- ▫️ The Developer can select exact order or reverse order using the GetRecords API
- ✅ Lambda will receive each record in the exact order it was placed into the shard. There is no guarantee of order across shards
- ▫️ Lambda will receive each record in the reverse order it was placed into the stream

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events.

KDS receives data from producers, and the data is stored in shards. Consumers then take the data and process it. In this case the AWS Lambda function is consuming the records from the shards.

In this scenario an application will be producing records and placing them in the stream as in step 1 of the image below. The AWS Lambda function will then consume the records (step 2) and will then execute the function by assuming the execution role specified (step 3).

A shard is an append-only log and a unit of streaming capability. A shard contains an ordered sequence of records ordered by arrival time. The order is guaranteed within a shard but not across shards.

Therefore, the best answer to this question is that AWS Lambda will receive each record in the exact order it was placed into the shard but there is no guarantee of order across shards

CORRECT: "Lambda will receive each record in the exact order it was placed into the shard. There is no guarantee of order across shards" is the correct answer.

INCORRECT: "Lambda will receive each record in the exact order it was placed into the stream " is incorrect as there are multiple shards in the stream and the order of records is not guaranteed across shards.

INCORRECT: "Lambda will receive each record in the reverse order it was placed into the stream" is incorrect as the order is guaranteed within a shard.

INCORRECT: "The Developer can select exact order or reverse order using the GetRecords API" is incorrect as you cannot choose the order you receive records with the GetRecords API.

Джерела: <https://aws.amazon.com/kinesis/data-streams/getting-started/> · <https://aws.amazon.com/kinesis/data-streams/faqs/>

</details>

---
