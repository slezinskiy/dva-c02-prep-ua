# Kinesis / Data Firehose

**Домен 4 — Усунення несправностей та оптимізація** · питань: 4  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#kinesis) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q25"></a>
### T09-Q25
*⏭ пропущено · домен: офіційна мітка*

A data analytics company is processing real-time Internet-of-Things (IoT) data via Kinesis Producer Library (KPL) and sending the data to a Kinesis Data Streams driven application. The application has halted data processing because of a ProvisionedThroughputExceeded exception.

Which of the following actions would help in addressing this issue? (Select two)

- ▫️ Use Kinesis enhanced fan-out for Kinesis Data Streams
- ✅ Configure the data producer to retry with an exponential backoff
- ✅ Increase the number of shards within your data streams to provide enough capacity
- ▫️ Use Amazon Kinesis Agent instead of Kinesis Producer Library (KPL) for sending data to Kinesis Data Streams
- ▫️ Use Amazon SQS instead of Kinesis Data Streams

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure the data producer to retry with an exponential backoff

Increase the number of shards within your data streams to provide enough capacity

Amazon Kinesis Data Streams enables you to build custom applications that process or analyze streaming data for specialized needs. You can continuously add various types of data such as clickstreams, application logs, and social media to an Amazon Kinesis data stream from hundreds of thousands of sources.

How Kinesis Data Streams Work  via - https://aws.amazon.com/kinesis/data-streams/

The capacity limits of an Amazon Kinesis data stream are defined by the number of shards within the data stream. The limits can be exceeded by either data throughput or the number of PUT records. While the capacity limits are exceeded, the put data call will be rejected with a ProvisionedThroughputExceeded exception.

If this is due to a temporary rise of the data stream’s input data rate, retry (with exponential backoff) by the data producer will eventually lead to the completion of the requests.

If this is due to a sustained rise of the data stream’s input data rate, you should increase the number of shards within your data stream to provide enough capacity for the put data calls to consistently succeed.

Incorrect options:

Use Amazon Kinesis Agent instead of Kinesis Producer Library (KPL) for sending data to Kinesis Data Streams - Kinesis Agent works with data producers. Using Kinesis Agent instead of KPL will not help as the constraint is the capacity limit of the Kinesis Data Stream.

Use Amazon SQS instead of Kinesis Data Streams - This is a distractor as using SQS will not help address the ProvisionedThroughputExceeded exception for the Kinesis Data Stream. This option does not address the issues in the use-case.

Use Kinesis enhanced fan-out for Kinesis Data Streams - You should use enhanced fan-out if you have, or expect to have, multiple consumers retrieving data from a stream in parallel. Therefore, using enhanced fan-out will not help address the ProvisionedThroughputExceeded exception as the constraint is the capacity limit of the Kinesis Data Stream.

Please review this note for more details on enhanced fan-out for Kinesis Data Streams:  via - https://aws.amazon.com/kinesis/data-streams/faqs/

Джерела: <https://aws.amazon.com/kinesis/data-streams/> · <https://aws.amazon.com/kinesis/data-streams/faqs/>

</details>

---

<a id="t10-q01"></a>
### T10-Q01
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

An Amazon Kinesis Data Stream has recently been configured to receive data from sensors in a manufacturing facility. A consumer EC2 instance is configured to process the data every 48 hours and save processing results to an Amazon RedShift data warehouse. Testing has identified a large amount of data is missing. A review of monitoring logs has identified that the sensors are sending data correctly and the EC2 instance is healthy.

What is the MOST likely explanation for this issue?

- ▫️ Amazon Kinesis has too many shards provisioned
- ▫️ The EC2 instance is failing intermittently
- ▫️ Amazon RedShift is not suitable for storing streaming data
- ✅ Records are retained for 24 hours in the Kinesis Data Stream by default

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Kinesis Data Streams supports changes to the data record retention period of your stream. A Kinesis data stream is an ordered sequence of data records meant to be written to and read from in real time. Data records are therefore stored in shards in your stream temporarily. The time period from when a record is added to when it is no longer accessible is called the retention period. A Kinesis data stream stores records from 24 hours by default, up to 8760 hours.

You can increase the retention period up to 8760 hours using the IncreaseStreamRetentionPeriod operation. You can decrease the retention period down to a minimum of 24 hours using the DecreaseStreamRetentionPeriod operation. The request syntax for both operations includes the stream name and the retention period in hours. Finally, you can check the current retention period of a stream by calling the DescribeStream operation.

Both operations are easy to use. The following is an example of changing the retention period using the AWS CLI:

Therefore, the most likely explanation is that the message retention period is set at the 24-hour default.

CORRECT: "Records are retained for 24 hours in the Kinesis Data Stream by default" is the correct answer.

INCORRECT: "Amazon RedShift is not suitable for storing streaming data" is incorrect. In this architecture Amazon Kinesis is responsible for receiving streaming data and storing it in a stream. The EC2 instances can then process and store the data in a number of different destinations including Amazon RedShift.

INCORRECT: "The EC2 instance is failing intermittently" is incorrect as the question states that a review of monitoring logs indicates that the EC2 instance is healthy. If it was failing intermittently this should be recorded in the logs.

INCORRECT: "Amazon Kinesis has too many shards provisioned" is incorrect as this would just mean that the Kinesis Stream has more capacity, not less.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/kinesis-extended-retention.html>

</details>

---

<a id="t11-q25"></a>
### T11-Q25
*⏭ пропущено · домен: офіційна мітка*

```json
A data analytics company processes Internet-of-Things (IoT) data using Amazon Kinesis. The development team has noticed that the IoT data feed into Kinesis experiences periodic spikes. The PutRecords API call occasionally fails and the logs show that the failed call returns the response shown below:
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId>
Content-Type: application/x-amz-json-1.1
Content-Length: <PayloadSizeBytes>
Date: <Date>
{
    "FailedRecordCount": 2,
    "Records": [
        {
            "SequenceNumber": "49543463076548007577105092703039560359975228518395012686",
            "ShardId": "shardId-000000000000"
        },
        {
            "ErrorCode": "ProvisionedThroughputExceededException",
            "ErrorMessage": "Rate exceeded for shard shardId-000000000001 in stream exampleStreamName under account 111111111111."
        },
        {
            "ErrorCode": "InternalFailure",
            "ErrorMessage": "Internal service failure."
        }
    ]
}
As an AWS Certified Developer Associate, which of the following options would you recommend to address this use case? (Select two)
```

- ✅ Decrease the frequency or size of your requests
- ▫️ Merge the shards to decrease the number of shards in the stream
- ✅ Use an error retry and exponential backoff mechanism
- ▫️ Decrease the number of KCL consumers
- ▫️ Increase the frequency or size of your requests

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Use an error retry and exponential backoff mechanism

Decrease the frequency or size of your requests

You can use PutRecords API call to write multiple data records into a Kinesis data stream in a single call. Each PutRecords request can support up to 500 records. Each record in the request can be as large as 1 MiB, up to a limit of 5 MiB for the entire request, including partition keys. Each shard can support writes up to 1,000 records per second, up to a maximum data write of 1 MiB per second.

The response Records array includes both successfully and unsuccessfully processed records. Kinesis Data Streams attempts to process all records in each PutRecords request. A single record failure does not stop the processing of subsequent records. As a result, PutRecords doesn't guarantee the ordering of records. An unsuccessfully processed record includes ErrorCode and ErrorMessage values. ErrorCode reflects the type of error and can be one of the following values: ProvisionedThroughputExceededException or InternalFailure. ProvisionedThroughputExceededException indicates that the request rate for the stream is too high, or the requested data is too large for the available throughput. Reduce the frequency or size of your requests.

To address the given use case, you can apply these best practices:

Reshard your stream to increase the number of shards in the stream.

Reduce the frequency or size of your requests.

Distribute read and write operations as evenly as possible across all of the shards in Data Streams.

Use an error retry and exponential backoff mechanism.

Incorrect options:

Merge the shards to decrease the number of shards in the stream

Increase the frequency or size of your requests

These two options contradict the explanation provided above, so these options are incorrect.

Decrease the number of KCL consumers - This option has been added as a distractor. The number of KCL consumers is irrelevant for the given use case since the ProvisionedThroughputExceededException is due to the PutRecords API call being used by the producers.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/kinesis-readprovisionedthroughputexceeded/> · <https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecords.html>

</details>

---

<a id="t12-q34"></a>
### T12-Q34
*⏭ пропущено · домен: офіційна мітка*

A development team is configuring Kinesis Data Streams for ingesting real-time data from various appliances. The team has declared a shard capacity of one to test the configuration.

What happens if the capacity limits of an Amazon Kinesis data stream are exceeded while the data producer adds data to the data stream?

- ▫️ Contact AWS support to request an increase in the number of shards
- ▫️ Data is lost unless the partition key of the data records is changed in order to write data to a different shard in the stream
- ▫️ The put data calls will be rejected with a AccessDeniedException exception once the limit is reached
- ✅ The put data calls will be rejected with a ProvisionedThroughputExceeded exception

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The put data calls will be rejected with a ProvisionedThroughputExceeded exception

The capacity limits of an Amazon Kinesis data stream are defined by the number of shards within the data stream. The limits can be exceeded by either data throughput or the number of PUT records. While the capacity limits are exceeded, the put data call will be rejected with a ProvisionedThroughputExceeded exception. If this is due to a temporary rise of the data stream’s input data rate, retry by the data producer will eventually lead to completion of the requests. If this is due to a sustained rise of the data stream’s input data rate, you should increase the number of shards within your data stream to provide enough capacity for the put data calls to consistently succeed.

Incorrect options:

The put data calls will be rejected with a AccessDeniedException exception once the limit is reached - Access Denied error is thrown when the accessing system does not have enough permissions. Since data was getting ingested into Data Streams before reaching the capacity, this error is not possible.

Data is lost unless the partition key of the data records is changed in order to write data to a different shard in the stream - Partition key is used to segregate and route records to different shards of a data stream. A partition key is specified by your data producer while adding data to an Amazon Kinesis data stream. The use case talks about provisioning only one shard. It is not possible to set up more shards by simply changing the partition key. Hence, this choice is incorrect.

Contact AWS support to request an increase in the number of shards - This is a made-up option that acts as a distractor.

Reference:

https://aws.amazon.com/kinesis/data-streams/faqs/

</details>

---
