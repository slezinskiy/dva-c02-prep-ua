# Kinesis / Data Firehose

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#kinesis) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t12-q47"></a>
### T12-Q47
*⏭ пропущено · домен: офіційна мітка*

An analytics company is using Kinesis Data Streams (KDS) to process automobile health-status data from the taxis managed by a taxi ride-hailing service. Multiple consumer applications are using the incoming data streams and the engineers have noticed a performance lag for the data delivery speed between producers and consumers of the data streams.

As a Developer Associate, which of the following options would you suggest for improving the performance for the given use-case?

- ▫️ Swap out Kinesis Data Streams with SQS Standard queues
- ▫️ Swap out Kinesis Data Streams with Kinesis Data Firehose
- ▫️ Swap out Kinesis Data Streams with SQS FIFO queues
- ✅ Use Enhanced Fanout feature of Kinesis Data Streams

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Enhanced Fanout feature of Kinesis Data Streams

Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events.

By default, the 2MB/second/shard output is shared between all of the applications consuming data from the stream. You should use enhanced fan-out if you have multiple consumers retrieving data from a stream in parallel. With enhanced fan-out developers can register stream consumers to use enhanced fan-out and receive their own 2MB/second pipe of read throughput per shard, and this throughput automatically scales with the number of shards in a stream.

Kinesis Data Streams Fanout  via - https://aws.amazon.com/blogs/aws/kds-enhanced-fanout/

Incorrect options:

Swap out Kinesis Data Streams with Kinesis Data Firehose - Amazon Kinesis Data Firehose is the easiest way to reliably load streaming data into data lakes, data stores, and analytics tools. It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. It can also batch, compress, transform, and encrypt the data before loading it, minimizing the amount of storage used at the destination and increasing security. Kinesis Data Firehose can only write to S3, Redshift, Elasticsearch or Splunk. You can't have applications consuming data streams from Kinesis Data Firehose, that's the job of Kinesis Data Streams. Therefore this option is not correct.

Swap out Kinesis Data Streams with SQS Standard queues

Swap out Kinesis Data Streams with SQS FIFO queues

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues. Standard queues offer maximum throughput, best-effort ordering, and at-least-once delivery. SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent. As multiple applications are consuming the same stream concurrently, both SQS Standard and SQS FIFO are not the right fit for the given use-case.

Exam Alert:

Please understand the differences between the capabilities of Kinesis Data Streams vs SQS, as you may be asked scenario-based questions on this topic in the exam.

 via - https://aws.amazon.com/kinesis/data-streams/faqs/

Джерела: <https://aws.amazon.com/blogs/aws/kds-enhanced-fanout/> · <https://aws.amazon.com/kinesis/data-streams/faqs/>

</details>

---
