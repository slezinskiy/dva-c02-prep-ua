# Amazon DynamoDB

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 46  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#dynamodb) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q20"></a>
### T01-Q20
*⏭ пропущено · домен: офіційна мітка*

Which of the following CLI options will allow you to retrieve a subset of the attributes coming from a DynamoDB scan?

- ▫️ --max-items
- ✅ --projection-expression
- ▫️ --page-size
- ▫️ --filter-expression

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

--projection-expression

A projection expression is a string that identifies the attributes you want. To retrieve a single attribute, specify its name. For multiple attributes, the names must be comma-separated.

 via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ProjectionExpressions.html

To read data from a table, you use operations such as GetItem, Query, or Scan. DynamoDB returns all of the item attributes by default. To get just some, rather than all of the attributes, use a projection expression.

Incorrect options:

--filter-expression - If you need to further refine the Query results, you can optionally provide a filter expression. A filter expression determines which items within the Query results should be returned to you. All of the other results are discarded. A filter expression is applied after Query finishes, but before the results are returned. Therefore, a Query will consume the same amount of read capacity, regardless of whether a filter expression is present.

--page-size - You can use the --page-size option to specify that the AWS CLI requests a smaller number of items from each call to the AWS service. The CLI still retrieves the full list but performs a larger number of service API calls in the background and retrieves a smaller number of items with each call.

--max-items - To include fewer items at a time in the AWS CLI output, use the --max-items option. The AWS CLI still handles pagination with the service as described above, but prints out only the number of items at a time that you specify.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ProjectionExpressions.html

</details>

---

<a id="t01-q22"></a>
### T01-Q22
*⏭ пропущено · домен: офіційна мітка*

You have created a DynamoDB table to support your application and provisioned RCU and WCU to it so that your application has been running for over a year now without any throttling issues. Your application now requires a second type of query over your table and as such, you have decided to create an LSI and a GSI on a new table to support that use case. One month after having implemented such indexes, it seems your table is experiencing throttling.

Upon looking at the table's metrics, it seems the RCU and WCU provisioned are still sufficient. What's happening?

- ▫️ Metrics are lagging in your CloudWatch dashboard and you should see the RCU and WCU peaking for the main table in a few minutes
- ✅ The GSI is throttling so you need to provision more RCU and WCU to the GSI
- ▫️ The LSI is throttling so you need to provision more RCU and WCU to the LSI
- ▫️ Adding both an LSI and a GSI to a table is not recommended by AWS best practices as this is a known cause for creating throttles

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The GSI is throttling so you need to provision more RCU and WCU to the GSI

DynamoDB supports two types of secondary indexes:

Global secondary index — An index with a partition key and a sort key that can be different from those on the base table. A global secondary index is considered "global" because queries on the index can span all of the data in the base table, across all partitions. A global secondary index is stored in its own partition space away from the base table and scales separately from the base table.

Local secondary index — An index that has the same partition key as the base table, but a different sort key. A local secondary index is "local" in the sense that every partition of a local secondary index is scoped to a base table partition that has the same partition key value.

Differences between GSI and LSI:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html

If you perform heavy write activity on the table, but a global secondary index on that table has insufficient write capacity, then the write activity on the table will be throttled. To avoid potential throttling, the provisioned write capacity for a global secondary index should be equal or greater than the write capacity of the base table since new updates will write to both the base table and global secondary index.

Incorrect options

The LSI is throttling so you need to provision more RCU and WCU to the LSI - LSI use the RCU and WCU of the main table, so you can't provision more RCU and WCU to the LSI.

Adding both an LSI and a GSI to a table is not recommended by AWS best practices as this is a known cause for creating throttles - This option has been added as a distractor. It is fine to have LSI and GSI together.

Metrics are lagging in your CloudWatch dashboard and you should see the RCU and WCU peaking for the main table in a few minutes - This could be a reason, but in this case, the GSI is at fault as the application has been running fine for months.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html#GSI.ThroughputConsiderations>

</details>

---

<a id="t01-q50"></a>
### T01-Q50
*⏭ пропущено · домен: офіційна мітка*

A popular mobile app retrieves data from an AWS DynamoDB table that was provisioned with read-capacity units (RCU’s) that are evenly shared across four partitions. One of those partitions is receiving more traffic than the other partitions, causing hot partition issues.

What technology will allow you to reduce the read traffic on your AWS DynamoDB table with minimal effort?

- ▫️ ElastiCache
- ▫️ DynamoDB Streams
- ▫️ More partitions
- ✅ DynamoDB DAX

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

DynamoDB DAX

Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement: from milliseconds to microseconds: even at millions of requests per second.

Incorrect options:

DynamoDB Streams - A stream record contains information about a data modification to a single item in a DynamoDB table. This is not the correct option for the given use-case.

ElastiCache - ElastiCache can cache the results from anything but you will need to modify your code to check the cache before querying the main query store. As the given use-case mandates minimal effort, so this option is not correct.

More partitions - This option has been added as a distractor as DynamoDB handles that for you automatically.

Reference:

https://aws.amazon.com/dynamodb/dax/

</details>

---

<a id="t01-q56"></a>
### T01-Q56
*⏭ пропущено · домен: офіційна мітка*

A financial services company has developed a REST API which is deployed in an Auto Scaling Group behind an Application Load Balancer. The API stores the data payload in DynamoDB and the static content is served through S3. Upon analyzing the usage pattern, it's found that 80% of the read requests are shared across all users.

As a Developer Associate, how can you improve the application performance while optimizing the cost with the least development effort?

- ▫️ Enable ElastiCache Redis for DynamoDB and ElastiCache Memcached for S3
- ▫️ Enable DAX for DynamoDB and ElastiCache Memcached for S3
- ✅ Enable DynamoDB Accelerator (DAX) for DynamoDB and CloudFront for S3
- ▫️ Enable ElastiCache Redis for DynamoDB and CloudFront for S3

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable DynamoDB Accelerator (DAX) for DynamoDB and CloudFront for S3

DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for Amazon DynamoDB that delivers up to a 10 times performance improvement—from milliseconds to microseconds—even at millions of requests per second.

DAX is tightly integrated with DynamoDB—you simply provision a DAX cluster, use the DAX client SDK to point your existing DynamoDB API calls at the DAX cluster, and let DAX handle the rest. Because DAX is API-compatible with DynamoDB, you don't have to make any functional application code changes. DAX is used to natively cache DynamoDB reads.

CloudFront is a content delivery network (CDN) service that delivers static and dynamic web content, video streams, and APIs around the world, securely and at scale. By design, delivering data out of CloudFront can be more cost-effective than delivering it from S3 directly to your users.

When a user requests content that you serve with CloudFront, their request is routed to a nearby Edge Location. If CloudFront has a cached copy of the requested file, CloudFront delivers it to the user, providing a fast (low-latency) response. If the file they’ve requested isn’t yet cached, CloudFront retrieves it from your origin – for example, the S3 bucket where you’ve stored your content.

So, you can use CloudFront to improve application performance to serve static content from S3.

Incorrect options:

Enable ElastiCache Redis for DynamoDB and CloudFront for S3

Amazon ElastiCache for Redis is a blazing fast in-memory data store that provides sub-millisecond latency to power internet-scale real-time applications. Amazon ElastiCache for Redis is a great choice for real-time transactional and analytical processing use cases such as caching, chat/messaging, gaming leaderboards, geospatial, machine learning, media streaming, queues, real-time analytics, and session store.

ElastiCache for Redis Overview:  via - https://aws.amazon.com/elasticache/redis/

Although, you can integrate Redis with DynamoDB, it's much more involved from a development perspective. For the given use-case, you should use DAX which is a much better fit.

Enable DAX for DynamoDB and ElastiCache Memcached for S3

Enable ElastiCache Redis for DynamoDB and ElastiCache Memcached for S3

Amazon ElastiCache for Memcached is a Memcached-compatible in-memory key-value store service that can be used as a cache or a data store. Amazon ElastiCache for Memcached is a great choice for implementing an in-memory cache to decrease access latency, increase throughput, and ease the load off your relational or NoSQL database.

ElastiCache cannot be used as a cache to serve static content from S3, so both these options are incorrect.

Джерела: <https://aws.amazon.com/dynamodb/dax/> · <https://aws.amazon.com/blogs/networking-and-content-delivery/amazon-s3-amazon-cloudfront-a-match-made-in-the-cloud/> · <https://aws.amazon.com/elasticache/redis/>

</details>

---

<a id="t01-q57"></a>
### T01-Q57
*⏭ пропущено · домен: офіційна мітка*

You are implementing a banking application in which you need to update the Exchanges DynamoDB table and the AccountBalance DynamoDB table at the same time or not at all.

Which DynamoDB feature should you use?

- ▫️ DynamoDB Streams
- ✅ DynamoDB Transactions
- ▫️ DynamoDB TTL
- ▫️ DynamoDB Indexes

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

DynamoDB Transactions

You can use DynamoDB transactions to make coordinated all-or-nothing changes to multiple items both within and across tables. Transactions provide atomicity, consistency, isolation, and durability (ACID) in DynamoDB, helping you to maintain data correctness in your applications.

DynamoDB Transactions Overview:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html

Incorrect options:

DynamoDB TTL - DynamoDB TTL allows you to expire data based on a timestamp, so this option is not correct.

DynamoDB Streams - DynamoDB Streams gives a changelog of changes that happened to your tables and then may even relay these to a Lambda function for further processing.

DynamoDB Indexes - GSI and LSI are used to allow you to query your tables using different partition/sort keys.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html

</details>

---

<a id="t02-q09"></a>
### T02-Q09
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A developer is setting up the primary key for an Amazon DynamoDB table that logs a company's purchases from various suppliers. Each transaction is recorded with these attributes: supplierId, transactionTime, item, and unitCost.

Which primary key configuration will be valid in this case?

- ▫️ A composite primary key with supplierId as the partition key and unitCost as the sort key.
- ▫️ A simple primary key with supplierId serving as the partition key.
- ✅ A composite primary key with supplierId as the partition key and transactionTime as the sort key.
- ▫️ A composite primary key with supplierId as the partition key and item as the sort key.

<details><summary>Пояснення (оригінал, EN)</summary>

A composite primary key with supplierId as the partition key and transactionTime as the sort key ensures each entry is unique. It is extremely unlikely for the same supplier to make two separate deliveries at the exact same millisecond.

CORRECT: "A composite primary key with supplierId as the partition key and transactionTime as the sort key" is the correct answer (as explained above.)

INCORRECT: "A simple primary key with supplierId serving as the partition key" is incorrect.

This simple primary key (only partition key) will not ensure uniqueness because multiple purchases can be made from the same supplier.

INCORRECT: "A composite primary key with supplierId as the partition key and item as the sort key" is incorrect.

Using supplierId and item as a composite key would not guarantee uniqueness, as a supplier can deliver the same item multiple times.

INCORRECT: "A composite primary key with supplierId as the partition key and unitCost as the sort key" is incorrect.

A composite key using supplierId and unitCost would not ensure uniqueness either, as the same supplier can supply multiple items with the same unit cost.

Джерела: <https://repost.aws/knowledge-center/dynamodb-create-composite-key>

</details>

---

<a id="t02-q14"></a>
### T02-Q14
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: "найшвидше, не шкодячи" → Parallel Scan + rate limit.** → [детальніше](../../06-contradictions-and-traps.md#c02)

An application uses an Amazon DynamoDB table that is 50 GB in size and provisioned with 10,000 read capacity units (RCUs) per second. The table must be scanned during non-peak hours when normal traffic consumes around 5,000 RCUs. The Developer must scan the whole table in the shortest possible time whilst ensuring the normal workload is not affected.

How would the Developer optimize this scan cost-effectively?

- ▫️ Use sequential scans and apply a FilterExpression.
- ✅ Use the Parallel Scan API operation and limit the rate.
- ❌ Increase read capacity units during the scan operation.
- ▫️ Use sequential scans and set the ConsistentRead parameter to false.

<details><summary>Пояснення (оригінал, EN)</summary>

To make the most of the table’s provisioned throughput, the Developer can use the Parallel Scan API operation so that the scan is distributed across the table’s partitions. This will help to optimize the scan to complete in the fastest possible time. However, the Developer will also need to apply rate limiting to ensure that the scan does not affect normal workloads.

CORRECT: "Use the Parallel Scan API operation and limit the rate" is the correct answer.

INCORRECT: "Use sequential scans and apply a FilterExpression" is incorrect. A FilterExpression is a string that contains conditions that DynamoDB applies after the Scan operation, but before the data is returned to you. This will not assist with speeding up the scan or preventing it from affecting normal workloads.

INCORRECT: "Increase read capacity units during the scan operation" is incorrect. There are already more RCUs provisioned than are needed during the non-peak hours. The key here is to use what is available for cost-effectiveness whilst ensuing normal workloads are not affected.

INCORRECT: "Use sequential scans and set the ConsistentRead parameter to false" is incorrect. This setting would turn off consistent reads making the scan eventually consistent. This will not satisfy the requirements of the question.

Джерела: <https://aws.amazon.com/blogs/Developer/rate-limited-scans-in-amazon-dynamodb/>

</details>

---

<a id="t02-q27"></a>
### T02-Q27
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A developer is responsible for a business critical application that uses Amazon DynamoDB as its main data repository. This DynamoDB table holds millions of records and handles high volumes of requests. The developer must implement near-real time processing on the records as soon as they are inserted or modified in the DynamoDB table.

What's the most efficient way to introduce this capability with MINIMUM modification to the existing application code?

- ▫️ Use Amazon SQS to queue incoming data and process it using Amazon EC2 instances.
- ▫️ Modify the application code to add processing logic after each DynamoDB write operation.
- ✅ Use AWS Lambda triggered by DynamoDB Streams to process the documents.
- ▫️ Set up an Amazon Kinesis Data Stream to process updates from the DynamoDB table.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Lambda can be triggered by DynamoDB Streams to automatically process changes to the DynamoDB table, ensuring near-real-time processing without substantial changes to the application code.

CORRECT: "Use AWS Lambda triggered by DynamoDB Streams to process the documents" is the correct answer (as explained above.)

INCORRECT: "Use Amazon SQS to queue incoming data and process it using Amazon EC2 instances" is incorrect.

Amazon SQS would require significant changes to the application code to integrate the queuing and processing mechanism. Moreover, it would not guarantee near-real-time processing.

INCORRECT: "Modify the application code to add processing logic after each DynamoDB write operation" is incorrect.

Modifying the application code to include processing logic after each write operation would entail significant changes to the code and may also slow down write operations.

INCORRECT: "Set up an Amazon Kinesis Data Stream to process updates from the DynamoDB table" is incorrect.

Amazon Kinesis Data Streams can be used to capture the changes, but consumers are required to perform the processing, and this is not mentioned in the solution. The simplest solution is to use DynamoDB streams with Lambda.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html>

</details>

---

<a id="t02-q35"></a>
### T02-Q35
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: "мінімізувати вплив на throughput" → менший page size (Limit).** → [детальніше](../../06-contradictions-and-traps.md#c02)

A company has a large Amazon DynamoDB table which they scan periodically so they can analyze several attributes. The scans are consuming a lot of provisioned throughput. What technique can a Developer use to minimize the impact of the scan on the table's provisioned throughput?

- ▫️ Prewarm the table by updating all items
- ✅ Set a smaller page size for the scan
- ❌ Use parallel scans
- ▫️ Define a range key on the table

<details><summary>Пояснення (оригінал, EN)</summary>

In general, Scan operations are less efficient than other operations in DynamoDB. A Scan operation always scans the entire table or secondary index. It then filters out values to provide the result you want, essentially adding the extra step of removing data from the result set.

If possible, you should avoid using a Scan operation on a large table or index with a filter that removes many results. Also, as a table or index grows, the Scan operation slows. The Scan operation examines every item for the requested values and can use up the provisioned throughput for a large table or index in a single operation.

The following diagram illustrates the impact of a sudden spike of capacity unit usage by Query and Scan operations, and its impact on your other requests against the same table.

Instead of using a large Scan operation, you can use the following techniques to minimize the impact of a scan on a table's provisioned throughput.

Reduce page size

Because a Scan operation reads an entire page (by default, 1 MB), you can reduce the impact of the scan operation by setting a smaller page size. The Scan operation provides a Limit parameter that you can use to set the page size for your request. Each Query or Scan request that has a smaller page size uses fewer read operations and creates a "pause" between each request.

Isolate scan operations

DynamoDB is designed for easy scalability. As a result, an application can create tables for distinct purposes, possibly even duplicating content across several tables. You want to perform scans on a table that is not taking "mission-critical" traffic. Some applications handle this load by rotating traffic hourly between two tables—one for critical traffic, and one for bookkeeping. Other applications can do this by performing every write on two tables: a "mission-critical" table, and a "shadow" table.

Therefore, the best option to reduce the impact of the scan on the table's provisioned throughput is to set a smaller page size for the scan.

CORRECT: "Set a smaller page size for the scan" is the correct answer.

INCORRECT: "Use parallel scans" is incorrect as this will return results faster but place more burden on the table’s provisioned throughput.

INCORRECT: "Define a range key on the table" is incorrect. A range key is a composite key that includes the hash key and another attribute. This is of limited use in this scenario as the table is being scanned to analyze multiple attributes.

INCORRECT: "Prewarm the table by updating all items" is incorrect as updating all items would incur significant costs in terms of provisioned throughput and would not be advantageous.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-query-scan.html>

</details>

---

<a id="t02-q59"></a>
### T02-Q59
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Пояснення застаріле: TransactWriteItems — до 100 дій, не 25.** → [детальніше](../../07-outdated-and-wrong-answers.md#o09)

A Developer is creating a serverless application that uses an Amazon DynamoDB table. The application must make idempotent, all-or-nothing operations for multiple groups of write actions.

Which solution will meet these requirements?

- ✅ Update the items in the table using the TransactWriteltems operation to group the changes.
- ▫️ Update the items in the table using the BatchWriteltem operation and configure idempotency at the table level.
- ▫️ Enable DynamoDB streams and capture new images. Update the items in the table using the BatchWriteltem.
- ▫️ Create an Amazon SQS FIFO queue and use the SendMessageBatch operation to group the changes.

<details><summary>Пояснення (оригінал, EN)</summary>

TransactWriteItems is a synchronous and idempotent write operation that groups up to 25 write actions in a single all-or-nothing operation. These actions can target up to 25 distinct items in one or more DynamoDB tables within the same AWS account and in the same Region. The aggregate size of the items in the transaction cannot exceed 4 MB. The actions are completed atomically so that either all of them succeed or none of them succeeds.

A TransactWriteItems operation differs from a BatchWriteItem operation in that all the actions it contains must be completed successfully, or no changes are made at all. With a BatchWriteItem operation, it is possible that only some of the actions in the batch succeed while the others do not.

CORRECT: "Update the items in the table using the TransactWriteltems operation to group the changes" is the correct answer.

INCORRECT: "Update the items in the table using the BatchWriteltem operation and configure idempotency at the table level" is incorrect. As explained above, the TransactWriteItems operation must be used.

INCORRECT: "Enable DynamoDB streams and capture new images. Update the items in the table using the BatchWriteltem" is incorrect. DynamoDB streams will not assist with making idempotent write operations.

INCORRECT: "Create an Amazon SQS FIFO queue and use the SendMessageBatch operation to group the changes" is incorrect. Amazon SQS should not be used as it does not assist and this solution is supposed to use a DynamoDB table

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html>

</details>

---

<a id="t02-q60"></a>
### T02-Q60
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: Parallel + Limit.** → [детальніше](../../06-contradictions-and-traps.md#c02)

A Developer needs to scan a full DynamoDB 50GB table within non-peak hours. About half of the strongly consistent RCUs are typically used during non-peak hours and the scan duration must be minimized.

How can the Developer optimize the scan execution time without impacting production workloads?

- ▫️ Use sequential scans
- ✅ Use parallel scans while limiting the rate
- ▫️ Increase the RCUs during the scan operation
- ▫️ Change to eventually consistent RCUs during the scan operation

<details><summary>Пояснення (оригінал, EN)</summary>

Performing a scan on a table consumes a lot of RCUs. A Scan operation always scans the entire table or secondary index. It then filters out values to provide the result you want, essentially adding the extra step of removing data from the result set. To reduce the amount of RCUs used by the scan so it doesn’t affect production workloads whilst minimizing the execution time, there are a couple of recommendations the Developer can follow.

Firstly, the Limit parameter can be used to reduce the page size. The Scan operation provides a Limit parameter that you can use to set the page size for your request. Each Query or Scan request that has a smaller page size uses fewer read operations and creates a "pause" between each request.

Secondly, the Developer can configure parallel scans. With parallel scans the Developer can maximize usage of the available throughput and have the scans distributed across the table’s partitions.

A parallel scan can be the right choice if the following conditions are met:

The table size is 20 GB or larger.

The table's provisioned read throughput is not being fully used.

Sequential Scan operations are too slow.

Therefore, to optimize the scan operation the Developer should use parallel scans while limiting the rate as this will ensure that the scan operation does not affect the performance of production workloads and still have it complete in the minimum time.

CORRECT: "Use parallel scans while limiting the rate" is the correct answer.

INCORRECT: "Use sequential scans" is incorrect as this is slower than parallel scans and the Developer needs to minimize scan execution time.

INCORRECT: "Increase the RCUs during the scan operation" is incorrect as the table is only using half of the RCUs during non-peak hours so there are RCUs available. You could increase RCUs and perform the scan faster, but this would be more expensive. The better solution is to use parallel scans with the limit parameter.

INCORRECT: "Change to eventually consistent RCUs during the scan operation" is incorrect as this does not provide a solution for preventing impact to the production workloads. The limit parameter should be used to ensure the tables RCUs are not fully used.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-query-scan.html#QueryAndScanGuidelines.ParallelScan>

</details>

---

<a id="t03-q07"></a>
### T03-Q07
*⏭ пропущено · домен: офіційна мітка*

You are a system administrator whose company recently moved its production application to AWS and migrated data from MySQL to AWS DynamoDB. You are adding new tables to AWS DynamoDB and need to allow your application to query your data by the primary key and an alternate key. This option must be added when first creating tables otherwise changes cannot be made afterward.

Which of the following actions should you take?

- ✅ Create a LSI
- ▫️ Call Scan
- ▫️ Create a GSI
- ▫️ Migrate away from DynamoDB

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create an LSI

LSI stands for Local Secondary Index. Some applications only need to query data using the base table's primary key; however, there may be situations where an alternate sort key would be helpful. To give your application a choice of sort keys, you can create one or more local secondary indexes on a table and issue Query or Scan requests against these indexes.

Differences between GSI and LSI:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html

Incorrect options:

Call Scan - Scan is an operation on the data. Once you create your local secondary indexes on a table you can then issue Scan requests again.

Create a GSI - GSI (Global Secondary Index) is an index with a partition key and a sort key that can be different from those on the base table.

Migrate away from DynamoDB - Migrating to another database that is not NoSQL may cause you to make changes that require substantial code changes.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html

</details>

---

<a id="t03-q23"></a>
### T03-Q23
*⏭ пропущено · домен: офіційна мітка*

Your mobile application needs to perform API calls to DynamoDB. You do not want to store AWS secret and access keys onto the mobile devices and need all the calls to DynamoDB made with a different identity per mobile device.

Which of the following services allows you to achieve this?

- ▫️ IAM
- ▫️ Cognito User Pools
- ✅ Cognito Identity Pools
- ▫️ Cognito Sync

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"Cognito Identity Pools"

Amazon Cognito identity pools provide temporary AWS credentials for users who are guests (unauthenticated) and for users who have been authenticated and received a token. Identity pools provide AWS credentials to grant your users access to other AWS services.

Cognito Overview:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Incorrect options:

"Cognito User Pools" - AWS Cognito User Pools is there to authenticate users for your applications which looks similar to Cognito Identity Pools. The difference is that Identity Pools allows a way to authorize your users to use the various AWS services and User Pools is not about authorizing to AWS services but to provide add sign-up and sign-in functionality to web and mobile applications.

"Cognito Sync" - You can use it to synchronize user profile data across mobile devices and the web without requiring your own backend. The client libraries cache data locally so your app can read and write data regardless of device connectivity status.

"IAM" - This is not a good solution because it would require you to have an IAM user for each mobile device which is not a good practice or manageable way of handling deployment.

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Reference:

https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

</details>

---

<a id="t03-q50"></a>
### T03-Q50
*⏭ пропущено · домен: офіційна мітка*

As a Full-stack Web Developer, you are involved with every aspect of a company’s platform from development with PHP and JavaScript to the configuration of NoSQL databases with Amazon DynamoDB. You are not concerned about your response receiving stale data from your database and need to perform 16 eventually consistent reads per second of 12 KB in size each.

How many read capacity units (RCUs) do you need?

- ▫️ 12
- ▫️ 192
- ✅ 24
- ▫️ 48

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Before proceeding with the calculations, please review the following:

via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

24

One read capacity unit represents two eventually consistent reads per second, for an item up to 4 KB in size. So that means that for an item of 12KB in size, we need 3 RCU (12 KB / 4 KB) for two eventually consistent reads per second. As we need 16 eventually consistent reads per second, we need 3 \* (16 / 2) = 24 RCU.

Incorrect options:

12

192

48

These three options contradict the details provided in the explanation above, so these are incorrect.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ProvisionedThroughput.html

</details>

---

<a id="t04-q01"></a>
### T04-Q01
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A company is designing a new application that will store thousands of terabytes of data. They need a fully managed NoSQL data store that provides low-latency and can store key-value pairs. Which type of database should they use?

- ▫️ Amazon RDS
- ▫️ Amazon S3
- ▫️ Amazon ElastiCache
- ✅ Amazon DynamoDB

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB is a fully managed NoSQL database. With DynamoDB, you can create database tables that can store and retrieve any amount of data and serve any level of request traffic. You can scale up or scale down your tables' throughput capacity without downtime or performance degradation.

DynamoDB is a key-value database. A key-value database is a type of nonrelational database that uses a simple key-value method to store data. A key-value database stores data as a collection of key-value pairs in which a key serves as a unique identifier. Both keys and values can be anything, ranging from simple objects to complex compound objects.

CORRECT: "Amazon DynamoDB" is the correct answer.

INCORRECT: "Amazon RDS" is incorrect as RDS is a SQL (not a NoSQL) type of database.

INCORRECT: "Amazon ElastiCache" is incorrect as ElastiCache is a SQL (not a NoSQL) type of database. ElastiCache is an in-memory database typically used for caching data.

INCORRECT: "Amazon S3" is incorrect as S3 is not a NoSQL database. S3 is an object storage system.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html> · <https://aws.amazon.com/nosql/key-value/>

</details>

---

<a id="t04-q08"></a>
### T04-Q08
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Очищення всієї таблиці → delete table.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A nightly batch job loads 1 million new records in to a DynamoDB table. The records are only needed for one hour, and the table needs to be empty by the next night’s batch job.

Which is the MOST efficient and cost-effective method to provide an empty table?

- ▫️ Use BatchWriteItem to empty all of the rows
- ▫️ Write a recursive function that scans and calls out DeleteItem
- ▫️ Use DeleteItem using a ConditionExpression
- ✅ Create and then delete the table after the task has completed

<details><summary>Пояснення (оригінал, EN)</summary>

The key requirements here are to be efficient and cost-effective. Therefore, it’s important to choose the option that requires the fewest API calls. As the table is only required for a short period of time, the most efficient and cost-effective option is to simply delete and recreate the table.

The following API actions can be used to perform this operation programmatically:

· CreateTable - The CreateTable operation adds a new table to your account.

· DeleteTable - The DeleteTable operation deletes a table and all of its items.

This solution means fewer API calls and also the table is not consuming RCUs/WCUs whilst not being used. Therefore, the best option is to create and then delete the table after the task has completed.

CORRECT: "Create and then delete the table after the task has completed" is the correct answer.

INCORRECT: "Use DeleteItem using a ConditionExpression" is incorrect as this will use more RCUs and WCUs and is not cost-effective.

INCORRECT: "Use BatchWriteItem to empty all of the rows" is incorrect. The BatchWriteItem operation puts or deletes multiple items (not rows) in one or more tables. This would use more RCUs and WCUs and is not cost-effective.

INCORRECT: "Write a recursive function that scans and calls out DeleteItem" is incorrect as scans are the least efficient and cost-effective option as all items must be retrieved from the table.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations.html>

</details>

---

<a id="t04-q12"></a>
### T04-Q12
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A company is building an application to track athlete performance using an Amazon DynamoDB table. Each item in the table is identified by a partition key (user_id) and a sort key (sport_name). The table design is shown below:

• Partition key: user_id

• Sort Key: sport_name

• Attributes: score, score_datetime

A Developer is asked to write a leaderboard application to display the top performers (user_id) based on the score for each sport_name.

What process will allow the Developer to extract results MOST efficiently from the DynamoDB table?

- ▫️ Create a local secondary index with a primary key of sport_name and a sort key of score and get the results based on the score attribute
- ✅ Create a global secondary index with a partition key of sport_name and a sort key of score, and get the results
- ▫️ Use a DynamoDB query operation with the key attributes of user_id and sport_name and order the results based on the score attribute
- ▫️ Use a DynamoDB scan operation to retrieve scores and user_id based on sport_name, and order the results based on the score attribute

<details><summary>Пояснення (оригінал, EN)</summary>

The Developer needs to be able to sort the scores for each sport and then extract the highest performing athletes. In this case BOTH the partition key and sort key must be different which means a Global Secondary index is required (as a Local Secondary index only has a different sort key). The GSI would be configured as follows:

· Partition key: sport_name

· Sort Key: score

The results will then be listed in order of the highest score for each sport which is exactly what is required.

CORRECT: "Create a global secondary index with a partition key of sport_name and a sort key of score, and get the results" is the correct answer.

INCORRECT: "Create a local secondary index with a primary key of sport_name and a sort key of score and get the results based on the score attribute" is incorrect as an LSI cannot be created after table creation and also only has a different sort key, not a different partition key.

INCORRECT: "Use a DynamoDB query operation with the key attributes of user_id and sport_name and order the results based on the score attribute" is incorrect as this is less efficient compared to using a GSI.

INCORRECT: "Use a DynamoDB scan operation to retrieve scores and user_id based on sport_name, and order the results based on the score attribute" is incorrect as this is the least efficient option as a scan returns every item in the table (more RCUs).

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t04-q37"></a>
### T04-Q37
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is creating a social networking app for games that uses a single Amazon DynamoDB table. All users’ saved game data is stored in the single table, but users should not be able to view each other’s data.

How can the Developer restrict user access so they can only view their own data?

- ▫️ Read records from DynamoDB and discard irrelevant data client-side
- ✅ Restrict access to specific items based on certain primary key values
- ▫️ Use an identity-based policy that restricts read access to the table to specific principals
- ▫️ Use separate access keys for each user to call the API and restrict access to specific items based on access key ID

<details><summary>Пояснення (оригінал, EN)</summary>

In DynamoDB, you have the option to specify conditions when granting permissions using an IAM policy. For example, you can:

• Grant permissions to allow users read-only access to certain items and attributes in a table or a secondary index.

• Grant permissions to allow users write-only access to certain attributes in a table, based upon the identity of that user.

To implement this kind of fine-grained access control, you write an IAM permissions policy that specifies conditions for accessing security credentials and the associated permissions. You then apply the policy to IAM users, groups, or roles that you create using the IAM console. Your IAM policy can restrict access to individual items in a table, access to the attributes in those items, or both at the same time.

You use the IAM Condition element to implement a fine-grained access control policy. By adding a Condition element to a permissions policy, you can allow or deny access to items and attributes in DynamoDB tables and indexes, based upon your particular business requirements. You can also grant permissions on a table, but restrict access to specific items in that table based on certain primary key values.

CORRECT: "Restrict access to specific items based on certain primary key values" is the correct answer.

INCORRECT: "Use separate access keys for each user to call the API and restrict access to specific items based on access key ID" is incorrect. You cannot restrict access based on access key ID.

INCORRECT: "Use an identity-based policy that restricts read access to the table to specific principals" is incorrect as this would only restrict read access to the entire table, not to specific items in the table.

INCORRECT: "Read records from DynamoDB and discard irrelevant data client-side" is incorrect as this is inefficient and insecure as it will use more RCUs and has more potential to leak the information.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t04-q42"></a>
### T04-Q42
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is creating an application that will utilize an Amazon DynamoDB table for storing session data. The data being stored is expected to be around 4.5KB in size and the application will make 20 eventually consistent reads/sec, and 12 standard writes/sec.

How many RCUs/WCUs are required?

- ▫️ 40 RCU and 60 WCU
- ▫️ 40 RCU and 12 WCU
- ▫️ 10 RCU and 24 WCU
- ✅ 20 RCU and 60 WCU

<details><summary>Пояснення (оригінал, EN)</summary>

With provisioned capacity mode, you specify the number of data reads and writes per second that you require for your application.

Read capacity unit (RCU):

• Each API call to read data from your table is a read request.

• Read requests can be strongly consistent, eventually consistent, or transactional.

• For items up to 4 KB in size, one RCU can perform one strongly consistent read request per second.

• Items larger than 4 KB require additional RCUs.

• For items up to 4 KB in size, one RCU can perform two eventually consistent read requests per second.

• Transactional read requests require two RCUs to perform one read per second for items up to 4 KB.

• For example, a strongly consistent read of an 8 KB item would require two RCUs, an eventually consistent read of an 8 KB item would require one RCU, and a transactional read of an 8 KB item would require four RCUs.

Write capacity unit (WCU):

• Each API call to write data to your table is a write request.

• For items up to 1 KB in size, one WCU can perform one standard write request per second.

• Items larger than 1 KB require additional WCUs.

• Transactional write requests require two WCUs to perform one write per second for items up to 1 KB.

• For example, a standard write request of a 1 KB item would require one WCU, a standard write request of a 3 KB item would require three WCUs, and a transactional write request of a 3 KB item would require six WCUs.

To determine the number of RCUs required to handle 20 eventually consistent reads per/second with an average item size of 4.5KB, perform the following steps:

1. Determine the average item size by rounding up the next multiple of 4KB (4.5KB rounds up to 8KB).

2. Determine the RCU per item by dividing the item size by 8KB (8KB/8KB = 1).

3. Multiply the value from step 2 with the number of reads required per second (1x20 = 20).

To determine the number of WCUs required to handle 12 standard writes per/second with an average item size of 8KB, simply multiply the average item size by the number of writes required (5x12=60).

CORRECT: "20 RCU and 60 WCU" is the correct answer.

INCORRECT: "40 RCU and 60 WCU" is incorrect. This would be the correct answer for strongly consistent reads and standard writes.

INCORRECT: "40 RCU and 120 WCU" is incorrect. This would be the correct answer for strongly consistent reads and transactional writes.

INCORRECT: "6 RCU and 18 WCU" is incorrect.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t05-q08"></a>
### T05-Q08
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An engineer is constructing a web-based application that uses Amazon DynamoDB for storing data. The data is distributed across two tables: 'authors' and 'books'. The 'authors' table uses 'authorName' as its partition key, while the 'books' table has 'bookTitle' as the partition key and 'authorName' as the sort key.

The application requires the ability to fetch multiple books and authors simultaneously in a single database operation for effective performance. The engineer is seeking a solution that maximizes application efficiency and reduces network traffic.

What strategy should the engineer employ to achieve these requirements?

- ▫️ First query the 'books' table using 'bookTitle' as a key condition, then separately query the 'authors' table using 'authorName'.
- ▫️ Use a DynamoDB Scan operation to fetch the required items from both tables.
- ▫️ Execute individual GetItem operations for each book and author to be retrieved.
- ✅ Utilize the DynamoDB BatchGetItem operation to fetch multiple items from both tables in a single network round trip.

<details><summary>Пояснення (оригінал, EN)</summary>

The BatchGetItem operation in DynamoDB permits the retrieval of multiple items from one or more tables in a single operation, thereby minimizing network traffic and improving overall application performance.

CORRECT: "Utilize the DynamoDB BatchGetItem operation to fetch multiple items from both tables in a single network round trip" is the correct answer (as explained above.)

INCORRECT: "Execute individual GetItem operations for each book and author to be retrieved" is incorrect.

Executing individual GetItem operations for each book and author would increase network traffic and slow down application performance.

INCORRECT: "Use a DynamoDB Scan operation to fetch the required items from both tables" is incorrect.

A DynamoDB Scan operation retrieves every item in the table and then filters to provide the required result, making it inefficient in this context.

INCORRECT: "First query the 'books' table using 'bookTitle' as a key condition, then separately query the 'authors' table using 'authorName'" is incorrect.

Querying the 'books' and 'authors' tables separately would increase the number of network operations and negatively affect the application's performance.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html>

</details>

---

<a id="t05-q22"></a>
### T05-Q22
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A customer requires a schema-less, key/value database that can be used for storing customer orders. Which type of AWS database is BEST suited to this requirement?

- ▫️ Amazon RDS
- ✅ Amazon DynamoDB
- ▫️ Amazon S3
- ▫️ Amazon ElastiCache

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability. It is a non-relational (schema-less), key-value type of database. This is the most suitable solution for this requirement.

CORRECT: "Amazon DynamoDB" is the correct answer.

INCORRECT: "Amazon RDS" is incorrect as this a relational database that has a schema.

INCORRECT: "Amazon ElastiCache" is incorrect as this is a key/value database but it is used to cache the contents of other databases (including DynamoDB and RDS) for better performance for reads.

INCORRECT: "Amazon S3" is incorrect as this is an object-based storage system not a database. It is a key/value store but DynamoDB is a better fit for a customer order database.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html>

</details>

---

<a id="t05-q39"></a>
### T05-Q39
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An application is using Amazon DynamoDB as its data store and needs to be able to read 100 items per second as strongly consistent reads. Each item is 5 KB in size.
What value should be set for the table's provisioned throughput for reads?

- ▫️ 500 Read Capacity Units
- ▫️ 50 Read Capacity Units
- ✅ 200 Read Capacity Units
- ▫️ 250 Read Capacity Units

<details><summary>Пояснення (оригінал, EN)</summary>

With provisioned capacity mode, you specify the number of data reads and writes per second that you require for your application.

Read capacity unit (RCU):

• Each API call to read data from your table is a read request.

• Read requests can be strongly consistent, eventually consistent, or transactional.

• For items up to 4 KB in size, one RCU can perform one strongly consistent read request per second.

• Items larger than 4 KB require additional RCUs.

• For items up to 4 KB in size, one RCU can perform two eventually consistent read requests per second.

• Transactional read requests require two RCUs to perform one read per second for items up to 4 KB.

• For example, a strongly consistent read of an 8 KB item would require two RCUs, an eventually consistent read of an 8 KB item would require one RCU, and a transactional read of an 8 KB item would require four RCUs.

Write capacity unit (WCU):

• Each API call to write data to your table is a write request.

• For items up to 1 KB in size, one WCU can perform one standard write request per second.

• Items larger than 1 KB require additional WCUs.

• Transactional write requests require two WCUs to perform one write per second for items up to 1 KB.

• For example, a standard write request of a 1 KB item would require one WCU, a standard write request of a 3 KB item would require three WCUs, and a transactional write request of a 3 KB item would require six WCUs.

To determine the number of RCUs required to handle 100 strongly consistent reads per/second with an average item size of 5KB, perform the following steps:

1. Determine the average item size by rounding up the next multiple of 4KB (5KB rounds up to 8KB).

2. Determine the RCU per item by dividing the item size by 4KB (8KB/4KB = 2).

3. Multiply the value from step 2 with the number of reads required per second (2x100 = 200).

CORRECT: "200 Read Capacity Units" is the correct answer.

INCORRECT: "50 Read Capacity Units" is incorrect.

INCORRECT: "250 Read Capacity Units" is incorrect.

INCORRECT: "500 Read Capacity Units" is incorrect.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t05-q40"></a>
### T05-Q40
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is creating a DynamoDB table for storing application logs. The table has 5 write capacity units (WCUs). The Developer needs to configure the read capacity units (RCUs) for the table. Which of the following configurations represents the most efficient use of throughput?

- ▫️ Strongly consistent reads of 5 RCUs reading items that are 4 KB in size
- ✅ Eventually consistent reads of 15 RCUs reading items that are 1 KB in size
- ▫️ Eventually consistent reads of 5 RCUs reading items that are 4 KB in size
- ▫️ Strongly consistent reads of 15 RCUs reading items that are 1KB in size

<details><summary>Пояснення (оригінал, EN)</summary>

Among these options, the most cost-efficient in terms of maximizing the number of read operations per RCU is:

· Eventually consistent reads of 15 RCUs reading items that are 1 KB in size.

This choice utilizes the RCU allocation most effectively because:

· It leverages the lower RCU consumption of eventually consistent reads.

· It takes advantage of the smaller item size (1 KB), which requires fewer RCUs for the same number of operations compared to items up to 4 KB.

CORRECT: "Eventually consistent reads of 15 RCUs reading items that are 1 KB in size" is incorrect as described above.

INCORRECT: "Strongly consistent reads of 15 RCUs reading items that are 1KB in size" is correct as described above.

INCORRECT: "Strongly consistent reads of 5 RCUs reading items that are 4 KB in size" is incorrect as described above.

INCORRECT: "Eventually consistent reads of 5 RCUs reading items that are 4 KB in size" is incorrect as described above.

Джерела: <https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/ProvisionedThroughput.html>

</details>

---

<a id="t05-q41"></a>
### T05-Q41
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A gaming company is building an application to track the scores for their games using an Amazon DynamoDB table. Each item in the table is identified by a partition key
(user_id) and a sort key (game_name). The table also includes the attribute “TopScore”. The table design is shown below:

A Developer has been asked to write a leaderboard application to display the highest achieved scores for each game (game_name), based on the score identified in the “TopScore” attribute.

What process will allow the Developer to extract results MOST efficiently from the DynamoDB table?

- ▫️ Create a global secondary index with a partition key of “user_id” and a sort key of “game_name” and get the results based on the “TopScore” attribute
- ▫️ Create a local secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute
- ▫️ Use a DynamoDB scan operation to retrieve the scores for “game_name” using the “TopScore” attribute, and order the results based on the score attribute
- ✅ Create a global secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute

<details><summary>Пояснення (оригінал, EN)</summary>

In an Amazon DynamoDB table, the primary key that uniquely identifies each item in the table can be composed not only of a partition key, but also of a sort key.

Well-designed sort keys have two key benefits:

- They gather related information together in one place where it can be queried efficiently. Careful design of the sort key lets you retrieve commonly needed groups of related items using range queries with operators such as begins_with, between, &gt;, &lt;, and so on.

- Composite sort keys let you define hierarchical (one-to-many) relationships in your data that you can query at any level of the hierarchy.

To speed up queries on non-key attributes, you can create a global secondary index. A global secondary index contains a selection of attributes from the base table, but they are organized by a primary key that is different from that of the table. The index key does not need to have any of the key attributes from the table. It doesn't even need to have the same key schema as a table.

For this scenario we need to identify the top achieved score for each game. The most efficient way to do this is to create a global secondary index using “game_name” as the partition key and “TopScore” as the sort key. We can then efficiently query the global secondary index to find the top achieved score for each game.

CORRECT: "Create a global secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute" is the correct answer.

INCORRECT: "Create a local secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute" is incorrect. With a local secondary index you can have a different sort key but the partition key is the same.

INCORRECT: "Use a DynamoDB scan operation to retrieve the scores for “game_name” using the “TopScore” attribute, and order the results based on the score attribute" is incorrect. This would be inefficient as it scans the whole table. First, we should create a global secondary index, and then use a query to efficiently retrieve the data.

INCORRECT: "Create a global secondary index with a partition key of “user_id” and a sort key of “game_name” and get the results based on the score attribute" is incorrect as with a global secondary index you have a different partition key and sort key. Also, we don’t need “user_id”, we need “game_name” and “TopScore”.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t06-q18"></a>
### T06-Q18
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

An Amazon DynamoDB table has been created using provisioned capacity. A manager needs to understand whether the DynamoDB table is cost-effective. How can the manager query how much provisioned capacity is actually being used?

- ▫️ Use Amazon CloudTrail and monitor the DescribeLimits API action
- ▫️ Monitor the ReadThrottleEvents and WriteThrottleEvents metrics for the table
- ▫️ 1. Use AWS X-Ray to instrument the DynamoDB table and monitor sub segments
- ✅ Monitor the ConsumedReadCapacityUnits and ConsumedWriteCapacityUnits over a specified time period

<details><summary>Пояснення (оригінал, EN)</summary>

You can monitor Amazon DynamoDB using CloudWatch, which collects and processes raw data from DynamoDB into readable, near real-time metrics. These statistics are retained for a period of time, so that you can access historical information for a better perspective on how your web application or service is performing. By default, DynamoDB metric data is sent to CloudWatch automatically.

To determine how much of the provisioned capacity is being used you can monitor ConsumedReadCapacityUnits or ConsumedWriteCapacityUnits over the specified time period.

CORRECT: "Monitor the ConsumedReadCapacityUnits and ConsumedWriteCapacityUnits over a specified time period" is the correct answer.

INCORRECT: "Monitor the ReadThrottleEvents and WriteThrottleEvents metrics for the table" is incorrect as these metrics are used to determine which requests exceed the provisioned throughput limits of a table.

INCORRECT: "Use Amazon CloudTrail and monitor the DescribeLimits API action" is incorrect as CloudTrail records API actions, not performance metrics.

INCORRECT: "Use AWS X-Ray to instrument the DynamoDB table and monitor subsegments" is incorrect. DynamoDB does not directly integrate with X-Ray but you can record information in subsegments for downstream requests. This is not, however, a method for monitoring provisioned capacity utilization.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/monitoring-cloudwatch.html>

</details>

---

<a id="t07-q10"></a>
### T07-Q10
*⏭ пропущено · домен: офіційна мітка*

Your web application reads and writes data to your DynamoDB table. The table is provisioned with 400 Write Capacity Units (WCU’s) shared across 4 partitions. One of the partitions receives 250 WCU/second while others receive much less. You receive the error 'ProvisionedThroughputExceededException'.

What is the likely cause of this error?

- ✅ You have a hot partition
- ▫️ Configured IAM policy is wrong
- ▫️ CloudWatch monitoring is lagging
- ▫️ Write Capacity Units (WCU’s) are applied across to all your DynamoDB tables and this needs reconfiguration

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

You have a hot partition

It's not always possible to distribute read and write activity evenly. When data access is imbalanced, a "hot" partition can receive a higher volume of read and write traffic compared to other partitions. To better accommodate uneven access patterns, DynamoDB adaptive capacity enables your application to continue reading and writing to hot partitions without being throttled, provided that traffic does not exceed your table’s total provisioned capacity or the partition maximum capacity.

ProvisionedThroughputExceededException explained:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

Hot partition explained:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html

Incorrect options:

CloudWatch monitoring is lagging - The error is specific to DynamoDB itself and not to any connected service. CloudWatch is a fully managed service from AWS and does not result in throttling.

Configured IAM policy is wrong - The error is not associated with authorization but to exceeding something pre-configured value. So, it's clearly not a permissions issue.

Write-capacity units (WCU’s) are applied across to all your DynamoDB tables and this needs reconfiguration - This statement is incorrect. Read Capacity Units and Write Capacity Units are specific to one table.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

</details>

---

<a id="t08-q08"></a>
### T08-Q08
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A company stores session information for a serverless application in an Amazon DynamoDB table. The company requires an automated process to eliminate outdated items from the table.

What is the most straightforward and lowest cost method to accomplish this?

- ✅ Use Amazon DynamoDB Time to Live (TTL) to automatically delete old items.
- ▫️ Use Amazon CloudWatch to monitor and delete old items.
- ▫️ Use AWS DMS (Database Migration Service) to purge old items.
- ▫️ Implement a periodic AWS Lambda function to scan and delete old items.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB Time to Live (TTL) enables you to define a per-item timestamp to determine when an item is no longer needed. Shortly after the date and time of the specified timestamp, DynamoDB deletes the item from your table, making this the simplest automated method to remove old items.

CORRECT: "Use Amazon DynamoDB Time to Live (TTL) to automatically delete old items" is the correct answer (as explained above.)

INCORRECT: "Implement a periodic AWS Lambda function to scan and delete old items" is incorrect.

Although a periodic AWS Lambda function could potentially scan and delete old items, this would require additional development and maintenance efforts compared to using DynamoDB TTL.

INCORRECT: "Use AWS DMS (Database Migration Service) to purge old items" is incorrect.

AWS DMS (Database Migration Service) is used for migrating databases to AWS and is not designed to automatically purge old items from a database.

INCORRECT: "Use Amazon CloudWatch to monitor and delete old items" is incorrect.

Amazon CloudWatch is a monitoring service, not a tool designed to delete items from a database based on their age.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html>

</details>

---

<a id="t08-q59"></a>
### T08-Q59
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An application is using Amazon DynamoDB as its data store and needs to be able to read 200 items per second as eventually consistent reads. Each item is 12 KB in size.
What value should be set for the table's provisioned throughput for reads?

- ▫️ 600 Read Capacity Units
- ▫️ 1200 Read Capacity Units
- ▫️ 150 Read Capacity Units
- ✅ 300 Read Capacity Units

<details><summary>Пояснення (оригінал, EN)</summary>

With provisioned capacity mode, you specify the number of data reads and writes per second that you require for your application.

Read capacity unit (RCU):

• Each API call to read data from your table is a read request.

• Read requests can be strongly consistent, eventually consistent, or transactional.

• For items up to 4 KB in size, one RCU can perform one strongly consistent read request per second.

• Items larger than 4 KB require additional RCUs.

• For items up to 4 KB in size, one RCU can perform two eventually consistent read requests per second.

• Transactional read requests require two RCUs to perform one read per second for items up to 4 KB.

• For example, a strongly consistent read of an 8 KB item would require two RCUs, an eventually consistent read of an 8 KB item would require one RCU, and a transactional read of an 8 KB item would require four RCUs.

Write capacity unit (WCU):

• Each API call to write data to your table is a write request.

• For items up to 1 KB in size, one WCU can perform one standard write request per second.

• Items larger than 1 KB require additional WCUs.

• Transactional write requests require two WCUs to perform one write per second for items up to 1 KB.

• For example, a standard write request of a 1 KB item would require one WCU, a standard write request of a 3 KB item would require three WCUs, and a transactional write request of a 3 KB item would require six WCUs.

To determine the number of RCUs required to handle 200 eventually consistent reads per/second with an average item size of 12KB, perform the following steps:

    1. Determine the average item size by rounding up the next multiple of 4KB (12KB rounds up to 12KB).

    2. Determine the RCU per item by dividing the item size by 8KB (12KB/8KB = 1.5).

    3. Multiply the value from step 2 with the number of reads required per second (1.5x200 = 300).

CORRECT: "300 Read Capacity Units" is the correct answer.

INCORRECT: "600 Read Capacity Units" is incorrect. This would be the value for strongly consistent reads.

INCORRECT: "1200 Read Capacity Units" is incorrect. This would be the value for transactional reads.

INCORRECT: "150 Read Capacity Units" is incorrect.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t09-q07"></a>
### T09-Q07
*⏭ пропущено · домен: офіційна мітка*

A social gaming application supports the transfer of gift vouchers between users. When a user hits a certain milestone on the leaderboard, they earn a gift voucher that can be redeemed or transferred to another user. The development team wants to ensure that this transfer is captured in the database such that the records for both users are either written successfully with the new gift vouchers or the status quo is maintained.

Which of the following solutions represent the best-fit options to meet the requirements for the given use-case? (Select two)

- ✅ Use the DynamoDB transactional read and write APIs on the table items as a single, all-or-nothing operation
- ▫️ Perform DynamoDB read and write operations with ConsistentRead parameter set to true
- ▫️ Use the Amazon Athena transactional read and write APIs on the table items as a single, all-or-nothing operation
- ✅ Complete both operations on RDS MySQL in a single transaction block
- ▫️ Complete both operations on Amazon RedShift in a single transaction block

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the DynamoDB transactional read and write APIs on the table items as a single, all-or-nothing operation

You can use DynamoDB transactions to make coordinated all-or-nothing changes to multiple items both within and across tables. Transactions provide atomicity, consistency, isolation, and durability (ACID) in DynamoDB, helping you to maintain data correctness in your applications.

DynamoDB Transactions Overview:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html

Complete both operations on RDS MySQL in a single transaction block

Amazon Relational Database Service (Amazon RDS) makes it easy to set up, operate, and scale a relational database with support for transactions in the cloud. A relational database is a collection of data items with pre-defined relationships between them. RDS supports the most demanding database applications. You can choose between two SSD-backed storage options: one optimized for high-performance Online Transaction Processing (OLTP) applications, and the other for cost-effective general-purpose use.

 via - https://aws.amazon.com/relational-database/

Incorrect options:

Perform DynamoDB read and write operations with ConsistentRead parameter set to true - DynamoDB uses eventually consistent reads unless you specify otherwise. Read operations (such as GetItem, Query, and Scan) provide a ConsistentRead parameter. If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation. Read consistency does not facilitate DynamoDB transactions and this option has been added as a distractor.

Complete both operations on Amazon RedShift in a single transaction block - Amazon Redshift is a fully-managed petabyte-scale cloud-based data warehouse product designed for large scale data set storage and analysis. It cannot be used to manage database transactions.

Use the Amazon Athena transactional read and write APIs on the table items as a single, all-or-nothing operation - Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL. Athena is serverless, so there is no infrastructure to manage, and you pay only for the queries that you run. It cannot be used to manage database transactions.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html> · <https://aws.amazon.com/relational-database/>

</details>

---

<a id="t09-q28"></a>
### T09-Q28
*⏭ пропущено · домен: офіційна мітка*

The development team at a multi-national retail company wants to support trusted third-party authenticated users from the supplier organizations to create and update records in specific DynamoDB tables in the company's AWS account.

As a Developer Associate, which of the following solutions would you suggest for the given use-case?

- ✅ Use Cognito Identity pools to enable trusted third-party authenticated users to access DynamoDB
- ▫️ Create a new IAM group in the company's AWS account for each of the third-party authenticated users from the supplier organizations. The users can then use the IAM group credentials to access DynamoDB
- ▫️ Use Cognito User pools to enable trusted third-party authenticated users to access DynamoDB
- ▫️ Create a new IAM user in the company's AWS account for each of the third-party authenticated users from the supplier organizations. The users can then use the IAM user credentials to access DynamoDB

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Cognito Identity pools to enable trusted third-party authenticated users to access DynamoDB

Amazon Cognito identity pools (federated identities) enable you to create unique identities for your users and federate them with identity providers. With an identity pool, you can obtain temporary, limited-privilege AWS credentials to access other AWS services. Amazon Cognito identity pools support the following identity providers:

Public providers: Login with Amazon (Identity Pools), Facebook (Identity Pools), Google (Identity Pools), Sign in with Apple (Identity Pools).

Amazon Cognito User Pools

Open ID Connect Providers (Identity Pools)

SAML Identity Providers (Identity Pools)

Developer Authenticated Identities (Identity Pools)

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Incorrect options:

Use Cognito User pools to enable trusted third-party authenticated users to access DynamoDB - A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito, or federate through a third-party identity provider (IdP). Cognito User Pools cannot be used to obtain temporary AWS credentials to access AWS services, such as Amazon S3 and DynamoDB.

Create a new IAM user in the company's AWS account for each of the third-party authenticated users from the supplier organizations. The users can then use the IAM user credentials to access DynamoDB

Create a new IAM group in the company's AWS account for each of the third-party authenticated users from the supplier organizations. The users can then use the IAM group credentials to access DynamoDB

Both these options involve setting up IAM resources such as IAM users or IAM groups just to provide access to DynamoDB tables. As the users are already trusted third-party authenticated users, Cognito Identity Pool can address this use-case in an elegant way.

Reference:

https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

</details>

---

<a id="t09-q31"></a>
### T09-Q31
*⏭ пропущено · домен: офіційна мітка*

The technology team at an investment bank uses DynamoDB to facilitate high-frequency trading where multiple trades can try and update an item at the same time.

Which of the following actions would make sure that only the last updated value of any item is used in the application?

- ✅ Use ConsistentRead = true while doing GetItem operation for any item
- ▫️ Use ConsistentRead = false while doing PutItem operation for any item
- ▫️ Use ConsistentRead = true while doing PutItem operation for any item
- ▫️ Use ConsistentRead = true while doing UpdateItem operation for any item

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use ConsistentRead = true while doing GetItem operation for any item

DynamoDB supports eventually consistent and strongly consistent reads.

Eventually Consistent Reads

When you read data from a DynamoDB table, the response might not reflect the results of a recently completed write operation. The response might include some stale data. If you repeat your read request after a short time, the response should return the latest data.

Strongly Consistent Reads

When you request a strongly consistent read, DynamoDB returns a response with the most up-to-date data, reflecting the updates from all prior write operations that were successful.

DynamoDB uses eventually consistent reads by default. Read operations (such as GetItem, Query, and Scan) provide a ConsistentRead parameter. If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation. As per the given use-case, to make sure that only the last updated value of any item is used in the application, you should use strongly consistent reads by setting ConsistentRead = true for GetItem operation.

 via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html

Incorrect options:

Use ConsistentRead = true while doing UpdateItem operation for any item

Use ConsistentRead = true while doing PutItem operation for any item

Use ConsistentRead = false while doing PutItem operation for any item

As mentioned in the explanation above, strongly consistent reads apply only while using the read operations (such as GetItem, Query, and Scan). So these three options are incorrect.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html

</details>

---

<a id="t09-q32"></a>
### T09-Q32
*⏭ пропущено · домен: офіційна мітка*

A development team is building a game where players can buy items with virtual coins. For every virtual coin bought by a user, both the players table as well as the items table in DynamodDB need to be updated simultaneously using an all-or-nothing operation.

As a developer associate, how will you implement this functionality?

- ✅ Use TransactWriteItems API of DynamoDB Transactions
- ▫️ Capture the transactions in the players table using DynamoDB streams and then sync with the items table
- ▫️ Use BatchWriteItem API to update multiple tables simultaneously
- ▫️ Capture the transactions in the items table using DynamoDB streams and then sync with the players table

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option: Use TransactWriteItems API of DynamoDB Transactions

With Amazon DynamoDB transactions, you can group multiple actions together and submit them as a single all-or-nothing TransactWriteItems or TransactGetItems operation.

TransactWriteItems is a synchronous and idempotent write operation that groups up to 25 write actions in a single all-or-nothing operation. These actions can target up to 25 distinct items in one or more DynamoDB tables within the same AWS account and in the same Region. The aggregate size of the items in the transaction cannot exceed 4 MB. The actions are completed atomically so that either all of them succeed or none of them succeeds.

You can optionally include a client token when you make a TransactWriteItems call to ensure that the request is idempotent. Making your transactions idempotent helps prevent application errors if the same operation is submitted multiple times due to a connection time-out or other connectivity issue.

Incorrect options:

Use BatchWriteItem API to update multiple tables simultaneously - A TransactWriteItems operation differs from a BatchWriteItem operation in that all the actions it contains must be completed successfully, or no changes are made at all. With a BatchWriteItem operation, it is possible that only some of the actions in the batch succeed while the others do not.

Capture the transactions in the players table using DynamoDB streams and then sync with the items table

Capture the transactions in the items table using DynamoDB streams and then sync with the players table

Many applications benefit from capturing changes to items stored in a DynamoDB table, at the point in time when such changes occur. DynamoDB supports streaming of item-level change data capture records in near-real-time. You can build applications that consume these streams and take action based on the contents. DynamoDB streams cannot be used to capture transactions in DynamoDB, therefore both these options are incorrect.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-apis-txwriteitems

</details>

---

<a id="t09-q34"></a>
### T09-Q34
*⏭ пропущено · домен: офіційна мітка*

A diagnostic lab stores its data on DynamoDB. The lab wants to backup a particular DynamoDB table data on Amazon S3, so it can download the S3 backup locally for some operational use.

Which of the following options is NOT feasible?

- ▫️ Use AWS Glue to copy your table to Amazon S3 and download locally
- ▫️ Use Hive with Amazon EMR to export your data to an S3 bucket and download locally
- ▫️ Use AWS Data Pipeline to export your table to an S3 bucket in the account of your choice and download locally
- ✅ Use the DynamoDB on-demand backup capability to write to Amazon S3 and download locally

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the DynamoDB on-demand backup capability to write to Amazon S3 and download locally - This option is not feasible for the given use-case. DynamoDB has two built-in backup methods (On-demand, Point-in-time recovery) that write to Amazon S3, but you will not have access to the S3 buckets that are used for these backups.

 via - https://aws.amazon.com/premiumsupport/knowledge-center/back-up-dynamodb-s3/

Incorrect options:

Use AWS Data Pipeline to export your table to an S3 bucket in the account of your choice and download locally - This is the easiest method. This method is used when you want to make a one-time backup using the lowest amount of AWS resources possible. Data Pipeline uses Amazon EMR to create the backup, and the scripting is done for you. You don't have to learn Apache Hive or Apache Spark to accomplish this task.

Use Hive with Amazon EMR to export your data to an S3 bucket and download locally - Use Hive to export data to an S3 bucket. Or, use the open-source emr-dynamodb-connector to manage your own custom backup method in Spark or Hive. These methods are the best practice to use if you're an active Amazon EMR user and are comfortable with Hive or Spark. These methods offer more control than the Data Pipeline method.

Use AWS Glue to copy your table to Amazon S3 and download locally - Use AWS Glue to copy your table to Amazon S3. This is the best practice to use if you want automated, continuous backups that you can also use in another service, such as Amazon Athena.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html> · <https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb-part1.html> · <https://docs.aws.amazon.com/emr/latest/ReleaseGuide/EMR_Hive_Commands.html#EMR_Hive_Commands_exporting> · <https://aws.amazon.com/blogs/big-data/how-to-export-an-amazon-dynamodb-table-to-amazon-s3-using-aws-step-functions-and-aws-glue/>

</details>

---

<a id="t09-q55"></a>
### T09-Q55
*⏭ пропущено · домен: офіційна мітка*

As a senior architect, you are responsible for the development, support, maintenance, and implementation of all database applications written using NoSQL technology. A new project demands a throughput requirement of 10 strongly consistent reads per second of 6KB in size each.

How many read capacity units will you need when configuring your DynamoDB table?

- ▫️ 60
- ✅ 20
- ▫️ 10
- ▫️ 30

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Before proceeding with the calculations, please review the following:

via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

20

One read capacity unit represents one strongly consistent read per second for an item up to 4 KB in size. If you need to read an item that is larger than 4 KB, DynamoDB will need to consume additional read capacity units.

1) Item Size / 4KB, rounding to the nearest whole number.

So, in the above case, 6KB / 4 KB = 1.5 or 2 read capacity units.

2) 1 read capacity unit per item (since strongly consistent read) × No of reads per second

So, in the above case, 2 x 10 = 20 read capacity units.

Incorrect options:

60

30

10

These three options contradict the details provided in the explanation above, so these are incorrect.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

</details>

---

<a id="t09-q61"></a>
### T09-Q61
*⏭ пропущено · домен: офіційна мітка*

A startup has been experimenting with DynamoDB in its new test environment. The development team has discovered that some of the write operations have been overwriting existing items that have the specified primary key. This has messed up their data, leading to data discrepancies.

Which DynamoDB write option should be selected to prevent this kind of overwriting?

- ▫️ Use Scan operation
- ▫️ Atomic Counters
- ▫️ Batch writes
- ✅ Conditional writes

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Conditional writes - DynamoDB optionally supports conditional writes for write operations (PutItem, UpdateItem, DeleteItem). A conditional write succeeds only if the item attributes meet one or more expected conditions. Otherwise, it returns an error.

For example, you might want a PutItem operation to succeed only if there is not already an item with the same primary key. Or you could prevent an UpdateItem operation from modifying an item if one of its attributes has a certain value. Conditional writes are helpful in cases where multiple users attempt to modify the same item. This is the right choice for the current scenario.

Incorrect options:

Batch writes - Bath operations (read and write) help reduce the number of network round trips from your application to DynamoDB. In addition, DynamoDB performs the individual read or write operations in parallel. Applications benefit from this parallelism without having to manage concurrency or threading. But, this is of no use in the current scenario of overwriting changes.

Atomic Counters - Atomic Counters is a numeric attribute that is incremented, unconditionally, without interfering with other write requests. You might use an atomic counter to track the number of visitors to a website. This functionality is not useful for the current scenario.

Use Scan operation - A Scan operation in Amazon DynamoDB reads every item in a table or a secondary index. By default, a Scan operation returns all of the data attributes for every item in the table or index. This is given as a distractor and not related to DynamoDB item updates.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems.ConditionalUpdate

</details>

---

<a id="t10-q05"></a>
### T10-Q05
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An application needs to read up to 100 items at a time from an Amazon DynamoDB. Each item is up to 100 KB in size and all attributes must be retrieved.

What is the BEST way to minimize latency?

- ▫️ Use a Query operation with a FilterExpression
- ▫️ Use a Scan operation with pagination
- ✅ Use BatchGetItem
- ▫️ Use GetItem and use a projection expression

<details><summary>Пояснення (оригінал, EN)</summary>

The BatchGetItem operation returns the attributes of one or more items from one or more tables. You identify requested items by primary key.

A single operation can retrieve up to 16 MB of data, which can contain as many as 100 items. In order to minimize response latency, BatchGetItem retrieves items in parallel.

By default, BatchGetItem performs eventually consistent reads on every table in the request. If you want strongly consistent reads instead, you can set ConsistentRead to true for any or all tables.

CORRECT: "Use BatchGetItem" is the correct answer.

INCORRECT: "Use GetItem and use a projection expression" is incorrect as this will limit the attributes returned and will retrieve the items sequentially which results in more latency.

INCORRECT: "Use a Scan operation with pagination" is incorrect as a Scan operation is the least efficient way to retrieve the data as all items in the table are returned and then filtered. Pagination just breaks the results into pages.

INCORRECT: "Use a Query operation with a FilterExpression" is incorrect as this would limit the results that are returned.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html>

</details>

---

<a id="t10-q21"></a>
### T10-Q21
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: обидва — Limit + Parallel Scan.** → [детальніше](../../06-contradictions-and-traps.md#c02)

An application scans an Amazon DynamoDB table once per day to produce a report. The scan is performed in non-peak hours when production usage uses around 50% of the provisioned throughput.

How can you MINIMIZE the time it takes to produce the report without affecting production workloads? (Select TWO.)

- ▫️ Use a Sequential Scan API operation
- ✅ Use the Limit parameter
- ✅ Use a Parallel Scan API operation
- ▫️ Use pagination to divide results into 1 MB pages
- ▫️ Increase read capacity units during the scan operation

<details><summary>Пояснення (оригінал, EN)</summary>

By default, the Scan operation processes data sequentially. Amazon DynamoDB returns data to the application in 1 MB increments, and an application performs additional Scan operations to retrieve the next 1 MB of data.

The larger the table or index being scanned, the more time the Scan takes to complete. In addition, a sequential Scan might not always be able to fully use the provisioned read throughput capacity: Even though DynamoDB distributes a large table's data across multiple physical partitions, a Scan operation can only read one partition at a time. For this reason, the throughput of a Scan is constrained by the maximum throughput of a single partition.

To address these issues, the Scan operation can logically divide a table or secondary index into multiple segments, with multiple application workers scanning the segments in parallel. Each worker can be a thread (in programming languages that support multithreading) or an operating system process. To perform a parallel scan, each worker issues its own Scan request with the following parameters:

• Segment — A segment to be scanned by a particular worker. Each worker should use a different value for Segment.

• TotalSegments — The total number of segments for the parallel scan. This value must be the same as the number of workers that your application will use.

The following diagram shows how a multithreaded application performs a parallel Scan with three degrees of parallelism.

To make the most of your table’s provisioned throughput, you’ll want to use the Parallel Scan API operation so that your scan is distributed across your table’s partitions. However, you also need to ensure the scan doesn’t consume your table’s provisioned throughput and cause the critical parts of your application to be throttled.

To control the amount of data returned per request, use the Limit parameter. This can help prevent situations where one worker consumes all of the provisioned throughput, at the expense of all other workers.

Therefore, the best solution to this problem is to use a parallel scan API operation with the Limit parameter.

CORRECT: "Use a Parallel Scan API operation " is the correct answer.

CORRECT: "Use the Limit parameter" is also a correct answer.

INCORRECT: "Use a Sequential Scan API operation" is incorrect as this would take more time and the question requests that we minimize the time it takes to complete the scan.

INCORRECT: "Increase read capacity units during the scan operation" is incorrect as this would increase cost and we still need a solution to ensure we maximize usage of available throughput without affecting production workloads.

INCORRECT: "Use pagination to divide results into 1 MB pages" is incorrect as this does only divides the results into pages, it does not segment and limit the amount of throughput used.

Джерела: <https://aws.amazon.com/blogs/developer/rate-limited-scans-in-amazon-dynamodb/> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.ParallelScan>

</details>

---

<a id="t10-q28"></a>
### T10-Q28
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer needs to return a list of items in a global secondary index from an Amazon DynamoDB table.

Which DynamoDB API call can the Developer use in order to consume the LEAST number of read capacity units?

- ▫️ Scan operation using strongly-consistent reads
- ▫️ Scan operation using eventually-consistent reads
- ▫️ Query operation using strongly-consistent reads
- ✅ Query operation using eventually-consistent reads

<details><summary>Пояснення (оригінал, EN)</summary>

The Query operation finds items based on primary key values. You can query any table or secondary index that has a composite primary key (a partition key and a sort key).

For items up to 4 KB in size, one RCU equals one strongly consistent read request per second or two eventually consistent read requests per second. Therefore, using eventually consistent reads uses fewer RCUs.

CORRECT: "Query operation using eventually-consistent reads" is the correct answer.

INCORRECT: "Query operation using strongly-consistent reads" is incorrect as strongly-consistent reads use more RCUs than eventually consistent reads.

INCORRECT: "Scan operation using eventually-consistent reads" is incorrect. The Scan operation returns one or more items and item attributes by accessing every item in a table or a secondary index and therefore uses more RCUs than a query operation.

INCORRECT: "Scan operation using strongly-consistent reads" is incorrect. The Scan operation returns one or more items and item attributes by accessing every item in a table or a secondary index and therefore uses more RCUs than a query operation.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html>

</details>

---

<a id="t10-q30"></a>
### T10-Q30
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A developer is creating a serverless application that will use a DynamoDB table. The average item size is 9KB. The application will make 4 strongly consistent reads/sec, and 2 standard write/sec. How many RCUs/WCUs are required?

- ✅ 12 RCU and 18 WCU
- ▫️ 12 RCU and 36 WCU
- ▫️ 24 RCU and 18 WCU
- ▫️ 6 RCU and 18 WCU

<details><summary>Пояснення (оригінал, EN)</summary>

With provisioned capacity mode, you specify the number of data reads and writes per second that you require for your application.

Read capacity unit (RCU):

• Each API call to read data from your table is a read request.

• Read requests can be strongly consistent, eventually consistent, or transactional.

• For items up to 4 KB in size, one RCU can perform one strongly consistent read request per  second.

• Items larger than 4 KB require additional RCUs.

For items up to 4 KB in size, one RCU can perform two eventually consistent read requests per second.

Transactional read requests require two RCUs to perform one read per second for items up to 4 KB.

For example, a strongly consistent read of an 8 KB item would require two RCUs, an eventually consistent read of an 8 KB item would require one RCU, and a transactional read of an 8 KB item would require four RCUs.

Write capacity unit (WCU):

• Each API call to write data to your table is a write request.

For items up to 1 KB in size, one WCU can perform one standard write request per second.

Items larger than 1 KB require additional WCUs.

• Transactional write requests require two WCUs to perform one write per second for items up to 1 KB.

• For example, a standard write request of a 1 KB item would require one WCU, a standard write request of a 3 KB item would require three WCUs, and a transactional write request of a 3 KB item would require six WCUs.

To determine the number of RCUs required to handle 4 strongly consistent reads per/second with an average item size of 9KB, perform the following steps:

1. Determine the average item size by rounding up the next multiple of 4KB (9KB rounds up to 12KB).

2. Determine the RCU per item by dividing the item size by 4KB (12KB/4KB = 3).

3. Multiply the value from step 2 with the number of reads required per second (3x4 = 12).

To determine the number of WCUs required to handle 2 standard writes per/second with an average item size of 9KB, simply multiply the average item size by the number of writes required (9x2=18).

CORRECT: "12 RCU and 18 WCU" is the correct answer.

INCORRECT: "24 RCU and 18 WCU" is incorrect. This would be the correct answer for transactional reads and standard writes.

INCORRECT: "12 RCU and 36 WCU" is incorrect. This would be the correct answer for strongly consistent reads and transactional writes.

INCORRECT: "6 RCU and 18 WCU" is incorrect. This would be the correct answer for eventually consistent reads and standard writes

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t10-q37"></a>
### T10-Q37
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer wants to find a list of items in a global secondary index from an Amazon DynamoDB table.

Which DynamoDB API call can the Developer use in order to consume the LEAST number of read capacity units?

- ▫️ Query operation using strongly-consistent reads
- ▫️ Scan operation using eventually-consistent reads
- ✅ Query operation using eventually-consistent reads
- ▫️ Scan operation using strongly-consistent reads

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB provides fast access to items in a table by specifying primary key values. However, many applications might benefit from having one or more secondary (or alternate) keys available, to allow efficient access to data with attributes other than the primary key. To address this, you can create one or more secondary indexes on a table and issue Query or Scan requests against these indexes.

A secondary index is a data structure that contains a subset of attributes from a table, along with an alternate key to support Query operations. You can retrieve data from the index using a Query, in much the same way as you use Query with a table. A table can have multiple secondary indexes, which give your applications access to many different query patterns.

You can also issue scan operations on a global secondary index however it is less efficient as it will return all items in the index.

CORRECT: "Query operation using eventually-consistent reads" is the correct answer.

INCORRECT: "Query operation using strongly-consistent reads" is incorrect. Strongly consistent reads require more RCUs and also are not supported on a global secondary index (they are supported on local secondary indexes).

INCORRECT: "Scan operation using eventually-consistent reads" is incorrect as a scan is less efficient than a query and will therefore use more RCUs.

INCORRECT: "Scan operation using strongly-consistent reads" is incorrect as a scan is less efficient than a query and will therefore use more RCUs.

Джерела: <https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/SecondaryIndexes.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t10-q38"></a>
### T10-Q38
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A company manages an application that stores data in an Amazon DynamoDB table. The company need to keep a record of all new changes made to the DynamoDB table in another table within the same AWS region. What is the MOST suitable way to deliver this requirement?

- ▫️ Use Amazon CloudTrail
- ✅ Use Amazon DynamoDB streams
- ▫️ Use Amazon DynamoDB snapshots
- ▫️ Use CloudWatch events

<details><summary>Пояснення (оригінал, EN)</summary>

A DynamoDB stream is an ordered flow of information about changes to items in a DynamoDB table. When you enable a stream on a table, DynamoDB captures information about every modification to data items in the table.

Whenever an application creates, updates, or deletes items in the table, DynamoDB Streams writes a stream record with the primary key attributes of the items that were modified. A stream record contains information about a data modification to a single item in a DynamoDB table. You can configure the stream so that the stream records capture additional information, such as the "before" and "after" images of modified items.

This is the best way to capture a record of new changes made to the DynamoDB table. Another table can then be populated with this data so the data is stored persistently.

CORRECT: "Use Amazon DynamoDB streams" is the correct answer.

INCORRECT: "Use CloudWatch events" is incorrect. CloudWatch Events delivers a near real-time stream of system events that describe changes in Amazon Web Services (AWS) resources. However, it does not capture the information that changes in a DynamoDB table so is unsuitable for this purpose.

INCORRECT: "Use Amazon CloudTrail" is incorrect as CloudTrail records a history of API calls on your account. It is used for creating an audit trail of events.

INCORRECT: "Use Amazon DynamoDB snapshots" is incorrect as snapshots only capture a point in time, they are not used for recording item-level changes.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html>

</details>

---

<a id="t11-q17"></a>
### T11-Q17
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

You are storing bids information on your betting application and you would like to automatically expire DynamoDB table data after one week.

What should you use?

- ▫️ Use DAX
- ✅ Use TTL
- ▫️ Use a Lambda function
- ▫️ Use DynamoDB Streams

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use TTL

Time To Live (TTL) for DynamoDB allows you to define when items in a table expire so that they can be automatically deleted from the database. TTL is provided at no extra cost as a way to reduce storage usage and reduce the cost of storing irrelevant data without using provisioned throughput. With TTL enabled on a table, you can set a timestamp for deletion on a per-item basis, allowing you to limit storage usage to only those records that are relevant.

Incorrect options:

Use DynamoDB Streams - These help you get a changelog of your DynamoDB table but won't help you delete expired data. Note that data expired using a TTL will appear as an event in your DynamoDB streams.

Use DAX - Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement: from milliseconds to microseconds: even at millions of requests per second. This is a caching technology for your DynamoDB tables.

Use a Lambda function - This could work but would require setting up indexes, queries, or scans to work, as well as trigger them often enough using EventBridge. This band-aid solution would never be as good as using the TTL feature in DynamoDB.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html

</details>

---

<a id="t11-q62"></a>
### T11-Q62
*⏭ пропущено · домен: офіційна мітка*

A gaming company wants to store information about all the games that the company has released. Each game has a name, version number, and category (such as sports, puzzles, strategy, etc). The game information also can include additional properties about the supported platforms and technical specifications. This additional information is inconsistent across games.

You have been hired as an AWS Certified Developer Associate to build a solution that addresses the following use cases:

For a given name and version number, get all details about the game that has that name and version number.

For a given name, get all details about all games that have that name.

For a given category, get all details about all games in that category.

What will you recommend as the most efficient solution?

- ▫️ Set up an Amazon RDS MySQL instance having a games table that contains columns for name, version number, and category. Configure the name column as the primary key
- ✅ Set up an Amazon DynamoDB table with a primary key that consists of the name as the partition key and the version number as the sort key. Create a global secondary index that has the category as the partition key and the name as the sort key
- ▫️ Permanently store the name, version number, and category information about the games in an Amazon Elasticache for Memcached instance
- ▫️ Set up an Amazon DynamoDB table with a primary key that consists of the category as the partition key and the version number as the sort key. Create a global secondary index that has the name as the partition key

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up an Amazon DynamoDB table with a primary key that consists of the name as the partition key and the version number as the sort key. Create a global secondary index that has the category as the partition key and the name as the sort key

When you create a DynamoDB table, in addition to the table name, you must specify the primary key of the table. The primary key uniquely identifies each item in the table, so that no two items can have the same key. You can create one or more secondary indexes on a table. A secondary index lets you query the data in the table using an alternate key, in addition to queries against the primary key. DynamoDB doesn't require that you use indexes, but they give your applications more flexibility when querying your data.

 via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html

You should note that a global secondary index (GSI) contains a selection of attributes from the base table, but they are organized by a primary key that is different from that of the table. The Global secondary indexes allow you to perform queries on attributes that are not part of the table's primary key.

 via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html

Incorrect options:

Set up an Amazon DynamoDB table with a primary key that consists of the category as the partition key and the version number as the sort key. Create a global secondary index that has the name as the partition key - The DynamoDB table for this option has the primary key and GSI that do not solve for the condition - "For a given name and version number, get all details about the game that has that name and version number". This option does not allow for efficient querying of a specific game by its name and version number as you need multiple queries which would be less efficient than the single query allowed by the correct option.

Set up an Amazon RDS MySQL instance having a games table that contains columns for name, version number, and category. Configure the name column as the primary key - This option is not the right fit as it does not allow you to efficiently query on the version number and category columns.

Permanently store the name, version number, and category information about the games in an Amazon Elasticache for Memcached instance - You cannot use Elasticache for Memcached to permanently store values meant to be persisted in a database (relational or NoSQL). Elasticache is a caching layer. So this option is incorrect.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/primary-key-dynamodb-table/>

</details>

---

<a id="t12-q06"></a>
### T12-Q06
*⏭ пропущено · домен: офіційна мітка*

You're a developer for 'Movie Gallery', a company that just migrated to the cloud. A database must be created using NoSQL technology to hold movies that are listed for public viewing. You are taking an important step in designing the database with DynamoDB and need to choose the appropriate partition key.

Which of the following unique attributes satisfies this requirement?

- ▫️ producer_name
- ▫️ movie_language
- ✅ movie_id
- ▫️ lead_actor_name

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

DynamoDB stores data as groups of attributes, known as items. Items are similar to rows or records in other database systems. DynamoDB stores and retrieves each item based on the primary key value, which must be unique. Items are distributed across 10-GB storage units, called partitions (physical storage internal to DynamoDB).

DynamoDB uses the partition key’s value as an input to an internal hash function. The output from the hash function determines the partition in which the item is stored. Each item’s location is determined by the hash value of its partition key.

Please see these details for the DynamoDB Partition Keys:  via - https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/

 via - https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/

movie_id

The movie_id attribute has high-cardinality across the entire collection of the movie database, hence it is the most suitable candidate for the partition key in this use case.

Incorrect options:

producer_name - Does not qualify because the attribute will have duplicate values (and therefore low cardinality)

lead_actor_name - Does not qualify because the attribute will have duplicate values (and therefore low cardinality)

movie_language - Does not qualify because the attribute will have duplicate values (and therefore low cardinality)

Reference:

https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/

</details>

---

<a id="t12-q08"></a>
### T12-Q08
*⏭ пропущено · домен: офіційна мітка*

You are a DynamoDB developer for an aerospace company that requires you to write 6 objects per second of 4.5KB in size each.

What write capacity unit is needed for your project?

- ▫️ 24
- ✅ 30
- ▫️ 46
- ▫️ 15

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Before proceeding with the calculations, please review the following:

via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

30

A write capacity unit represents one write per second, for an item up to 1 KB in size.

Item sizes for writes are rounded up to the next 1 KB multiple. For example, writing a 500-byte item consumes the same throughput as writing a 1 KB item. So, for the given use-case, each object is of size 4.5 KB, which will be rounded up to 5KB.

Therefore, for 6 objects, you need 6x5 = 30 WCUs.

Incorrect options:

24

15

46

These three options contradict the details provided in the explanation above, so these are incorrect.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

</details>

---

<a id="t12-q27"></a>
### T12-Q27
*⏭ пропущено · домен: офіційна мітка*

You are a software engineer working for an IT company and are asked to contribute to a growing internal application that includes dashboards for data visualization. You are provisioning your AWS DynamoDB table and need to perform 10 strongly consistent reads per second of 4 KB in size each.

How many Read Capacity Units (RCUs) are needed?

- ▫️ 20
- ✅ 10
- ▫️ 40
- ▫️ 5

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Before proceeding with the calculations, please review the following:

via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

10

One Read Capacity Unit represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size. If you need to read an item that is larger than 4 KB, DynamoDB will need to consume additional read capacity units. The total number of Read Capacity Units required depends on the item size, and whether you want an eventually consistent or strongly consistent read.

1) Item Size / 4KB, rounding to the nearest whole number.

So, in the above case, 4KB / 4 KB = 1 read capacity unit.

2) 1 read capacity unit per item (since strongly consistent read) × No of reads per second

So, in the above case, 1 x 10 = 10 read capacity units.

Incorrect options:

40

20

5

These three options contradict the details provided in the explanation above, so these are incorrect.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html

</details>

---
