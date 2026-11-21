# Amazon DynamoDB

**Домен 4 — Усунення несправностей та оптимізація** · питань: 12  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#dynamodb) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q64"></a>
### T02-Q64
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

The Lambda function needs to write this data to an Amazon DynamoDB table. After deploying the function, the developer notices that the write operations to the DynamoDB table occasionally fail due to throttling.

What should the developer do to reduce the likelihood of these throttling issues without significantly over-provisioning the DynamoDB table's write capacity?

- ▫️ Increase the Lambda function's timeout setting to allow for retries in case of throttling.
- ❌ Use Amazon SQS to queue data before writing it to the DynamoDB table, ensuring a steady and controlled write rate.
- ▫️ Convert the DynamoDB table to an on-demand capacity mode to automatically adjust its write capacity to match the workload.
- ✅ Implement exponential backoff in the Lambda function's error handling code to retry failed write operations.

<details><summary>Пояснення (оригінал, EN)</summary>

Implementing exponential backoff is a best practice for handling retries in distributed systems, especially when dealing with AWS services like DynamoDB. Exponential backoff involves gradually increasing the delay between retry attempts to reduce the load on the service and the likelihood of subsequent throttling.

This approach allows the Lambda function to manage intermittent spikes more effectively in demand without requiring significant over-provisioning of the DynamoDB table's write capacity.

CORRECT: "Implement exponential backoff in the Lambda function's error handling code to retry failed write operations" is the correct answer (as explained above.)

INCORRECT: "Increase the Lambda function's timeout setting to allow for retries in case of throttling" is incorrect.

This issue is not directly related to handling throttling issues; increasing the timeout setting allows the function to run longer but does not address the root cause of the throttling.

INCORRECT: "Convert the DynamoDB table to an on-demand capacity mode to automatically adjust its write capacity to match the workload" is incorrect.

This solution could potentially solve the issue by automatically scaling the table's capacity, but it might lead to higher costs due to uncontrolled scaling and does not represent a coding or architectural strategy to manage write throughput efficiently.

INCORRECT: "Use Amazon SQS to queue data before writing it to the DynamoDB table, ensuring a steady and controlled write rate" is incorrect.

This solution introduces additional complexity and potential latency without directly addressing the issue of handling write retries when throttling occurs. While SQS can help decouple components and manage load, the key to solving the immediate problem of write throttling is to implement a retry strategy that respects DynamoDB's adaptive capacity and avoids overwhelming the table with rapid, repeated write requests.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html>

</details>

---

<a id="t03-q14"></a>
### T03-Q14
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Scan: "швидше" → parallel scan.** → [детальніше](../../06-contradictions-and-traps.md#c02)

A development team uses the AWS SDK for Java to maintain an application that stores data in AWS DynamoDB. The application makes use of Scan operations to return several items from a 25 GB table. There is no possibility of creating indexes to retrieve these items predictably. Developers are trying to get these specific rows from DynamoDB as fast as possible.

Which of the following options can be used to improve the performance of the Scan operation?

- ▫️ Use a Query
- ✅ Use parallel scans
- ▫️ Use a ProjectionExpression
- ▫️ Use a FilterExpression

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use parallel scans

By default, the Scan operation processes data sequentially. Amazon DynamoDB returns data to the application in 1 MB increments, and an application performs additional Scan operations to retrieve the next 1 MB of data. The larger the table or index being scanned, the more time the Scan takes to complete. To address these issues, the Scan operation can logically divide a table or secondary index into multiple segments, with multiple application workers scanning the segments in parallel.

To make use of a parallel Scan feature, you will need to run multiple worker threads or processes in parallel. Each worker will be able to scan a separate partition of a table concurrently with the other workers.

How DynamoDB parallel Scan works:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.ParallelScan

Incorrect options:

Use a ProjectionExpression - A projection expression is a string that identifies the attributes you want. To retrieve a single attribute, specify its name. For multiple attributes, the names must be comma-separated

Use a FilterExpression - If you need to further refine the Scan results, you can optionally provide a filter expression. A filter expression determines which items within the Scan results should be returned to you. All of the other results are discarded.

A filter expression is applied after a Scan finishes, but before the results are returned. Therefore, a Scan consumes the same amount of read capacity, regardless of whether a filter expression is present.

Use a Query - This could work if we were able to create an index, but the question says: "There is no possibility of creating indexes to retrieve these items predictably". As such, we cannot use a Query.

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.ParallelScan

</details>

---

<a id="t03-q24"></a>
### T03-Q24
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Видалити ВСЮ таблицю щоночі → drop & recreate.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A firm uses AWS DynamoDB to store information about people’s favorite sports teams and allow the information to be searchable from their home page. There is a daily requirement that all 10 million records in the table should be deleted then re-loaded at 2:00 AM each night.

Which option is an efficient way to delete with minimal costs?

- ▫️ Scan and call DeleteItem
- ▫️ Scan and call BatchDeleteItem
- ▫️ Call PurgeTable
- ✅ Delete then re-create the table

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Delete then re-create the table

The DeleteTable operation deletes a table and all of its items. After a DeleteTable request, the specified table is in the DELETING state until DynamoDB completes the deletion.

Scan and call DeleteItem - Scan is a very slow operation for 10 million items and this is not the best-fit option for the given use-case.

Scan and call BatchDeleteItem - Scan is a very slow operation for 10 million items and this is not the best-fit option for the given use-case.

Call PurgeTable - This is a made-up option and has been added as a distractor.

Reference:

https://docs.aws.amazon.com/cli/latest/reference/dynamodb/delete-table.html

</details>

---

<a id="t04-q09"></a>
### T04-Q09
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Development team are creating a financial trading application. The application requires sub-millisecond latency for processing trading requests. Amazon DynamoDB is used to store the trading data. During load testing the Development team found that in periods of high utilization the latency is too high and read capacity must be significantly over-provisioned to avoid throttling.

How can the Developers meet the latency requirements of the application?

- ▫️ Store the trading data in Amazon S3 and use Transfer Acceleration
- ▫️ Use exponential backoff in the application code for DynamoDB queries
- ✅ Use Amazon DynamoDB Accelerator (DAX) to cache the data
- ▫️ Create a Global Secondary Index (GSI) for the trading data

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB is designed for scale and performance. In most cases, the DynamoDB response times can be measured in single-digit milliseconds. However, there are certain use cases that require response times in microseconds. For these use cases, DynamoDB Accelerator (DAX) delivers fast response times for accessing eventually consistent data.

DAX is a DynamoDB-compatible caching service that enables you to benefit from fast in-memory performance for demanding applications. DAX addresses three core scenarios:

1. As an in-memory cache, DAX reduces the response times of eventually consistent read workloads by an order of magnitude from single-digit milliseconds to microseconds.

2. DAX reduces operational and application complexity by providing a managed service that is API-compatible with DynamoDB. Therefore, it requires only minimal functional changes to use with an existing application.

3. For read-heavy or bursty workloads, DAX provides increased throughput and potential operational cost savings by reducing the need to overprovision read capacity units. This is especially beneficial for applications that require repeated reads for individual keys.

In this scenario the question is calling for sub-millisecond (e.g. microsecond) latency and this is required for read traffic as evidenced by the need to over-provision reads. Therefore, DynamoDB DAX would be the best solution for reducing the latency and meeting the requirements.

CORRECT: "Use Amazon DynamoDB Accelerator (DAX) to cache the data" is the correct answer.

INCORRECT: "Create a Global Secondary Index (GSI) for the trading data" is incorrect as a GSI is used to speed up queries on non-key attributes. There is no requirement here for a Global Secondary Index.

INCORRECT: "Use exponential backoff in the application code for DynamoDB queries" is incorrect as this may reduce the requirement for over-provisioning reads but it will not solve the problem of reducing latency. With this solution the application performance will be worse, it’s a case of reducing cost along with performance.

INCORRECT: "Store the trading data in Amazon S3 and use Transfer Acceleration" is incorrect as this will not reduce the latency of the application. Transfer Acceleration is used for improving performance of uploads of data to Amazon S3.

Джерела: <https://aws.amazon.com/dynamodb/dax/>

</details>

---

<a id="t05-q44"></a>
### T05-Q44
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An application writes items to an Amazon DynamoDB table. As the application scales to thousands of instances, calls to the DynamoDB API generate occasional ThrottlingException errors. The application is coded in a language incompatible with the AWS SDK.

How should the error be handled?

- ▫️ Pass API calls through Amazon API Gateway
- ▫️ Send the items to DynamoDB through Amazon Kinesis Data Firehose
- ✅ Add exponential backoff to the application logic
- ▫️ Use Amazon SQS as an API message bus

<details><summary>Пояснення (оригінал, EN)</summary>

Exponential backoff can improve an application's reliability by using progressively longer waits between retries. When using the AWS SDK, this logic is built‑in. However, in this case the application is incompatible with the AWS SDK so it is necessary to manually implement exponential backoff.

CORRECT: "Add exponential backoff to the application logic" is the correct answer.

INCORRECT: "Use Amazon SQS as an API message bus" is incorrect as SQS requires instances or functions to pick up and process the messages and put them in the DynamoDB table. This is unnecessary cost and complexity and will not improve performance.

INCORRECT: "Pass API calls through Amazon API Gateway" is incorrect as this is not a suitable method of throttling the application. Exponential backoff logic in the application is a better solution.

INCORRECT: "Send the items to DynamoDB through Amazon Kinesis Data Firehose" is incorrect as DynamoDB is not a destination for Kinesis Data Firehose.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-table-throttled/>

</details>

---

<a id="t05-q45"></a>
### T05-Q45
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer has added a Global Secondary Index (GSI) to an existing Amazon DynamoDB table. The GSI is used mainly for read operations whereas the primary table is extremely write-intensive. Recently, the Developer has noticed throttling occurring under heavy write activity on the primary table. However, the write capacity units on the primary table are not fully utilized.

What is the best explanation for why the writes are being throttled on the primary table?

- ▫️ The Developer should have added an LSI instead of a GSI
- ▫️ There are insufficient write capacity units on the primary table
- ▫️ There are insufficient read capacity units on the primary table
- ✅ The write capacity units on the GSI are under provisioned

<details><summary>Пояснення (оригінал, EN)</summary>

Some applications might need to perform many kinds of queries, using a variety of different attributes as query criteria. To support these requirements, you can create one or more global secondary indexes and issue Query requests against these indexes in Amazon DynamoDB.

When items from a primary table are written to the GSI they consume write capacity units. It is essential to ensure the GSI has sufficient WCUs (typically, at least as many as the primary table). If writes are throttled on the GSI, the main table will be throttled (even if there’s enough WCUs on the main table). LSIs do not cause any special throttling considerations.

In this scenario, it is likely that the Developer assumed that the GSI would need fewer WCUs as it is more read-intensive and neglected to factor in the WCUs required for writing data into the GSI. Therefore, the most likely explanation is that the write capacity units on the GSI are under provisioned

CORRECT: "The write capacity units on the GSI are under provisioned" is the correct answer.

INCORRECT: "There are insufficient read capacity units on the primary table" is incorrect as the table is being throttled due to writes, not reads.

INCORRECT: "The Developer should have added an LSI instead of a GSI" is incorrect as a GSI has specific advantages and there was likely good reason for adding a GSI. Also, you cannot add an LSI to an existing table.

INCORRECT: "There are insufficient write capacity units on the primary table" is incorrect as the question states that the WCUs are underutilized.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t07-q29"></a>
### T07-Q29
*⏭ пропущено · домен: офіційна мітка*

A developer is working on an AWS Lambda function that reads data from Amazon S3 objects and writes the data to an Amazon DynamoDB table. Although the function triggers successfully from an S3 event notification upon object creation, it encounters a failure while attempting to write data to the DynamoDB table.

What is the probable reason for the failure?

- ▫️ The Lambda function's provisioned concurrency limit has been exceeded
- ▫️ The Lambda function's reserved concurrency limit has been exceeded
- ✅ The Lambda function does not have IAM permissions to write to DynamoDB
- ▫️ DynamoDB table does not have a Gateway VPC Endpoint, which is required by the Lambda function for a successful write

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The Lambda function does not have IAM permissions to write to DynamoDB

You need to use an identity-based policy that allows read and write access to a specific Amazon DynamoDB table. To use this policy, attach the policy to a Lambda service role. A service role is a role that you create in your account to allow a service to perform actions on your behalf. That service role must include AWS Lambda as the principal in the trust policy. The role is then used to grant a Lambda function access to a DynamoDB table. By using an IAM policy and role to control access, you don’t need to embed credentials in code and can tightly control which services the Lambda function can access.

Incorrect options:

The Lambda function's provisioned concurrency limit has been exceeded

The Lambda function's reserved concurrency limit has been exceeded

Reserved concurrency – Reserved concurrency guarantees the maximum number of concurrent instances for the function. When a function has reserved concurrency, no other function can use that concurrency. There is no charge for configuring reserved concurrency for a function.

Provisioned concurrency – Provisioned concurrency initializes a requested number of execution environments so that they are prepared to respond immediately to your function's invocations. Note that configuring provisioned concurrency incurs charges to your AWS account.

Neither reserved concurrency nor provisioned concurrency has any relevance to the given use case. Both options have been added as distractors.

DynamoDB table does not have a Gateway VPC Endpoint, which is required by the Lambda function for a successful write - Gateway endpoints provide reliable connectivity to Amazon S3 and DynamoDB without requiring an internet gateway or a NAT device for your VPC. Gateway endpoints do not enable AWS PrivateLink. This option acts as a distractor since the Lambda function is not provisioned within a VPC by default, so there is no need of a Gateway VPC Endpoint to access DynamoDB.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_lambda-access-dynamodb.html> · <https://aws.amazon.com/blogs/security/how-to-create-an-aws-iam-policy-to-grant-aws-lambda-access-to-an-amazon-dynamodb-table/>

</details>

---

<a id="t08-q02"></a>
### T08-Q02
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is troubleshooting an issue with a DynamoDB table. The table is used to store order information for a busy online store and uses the order date as the partition key. During busy periods writes to the table are being throttled despite the consumed throughput being well below the provisioned throughput.

According to AWS best practices, how can the Developer resolve the issue at the LOWEST cost?

- ▫️ Increase the read and write capacity units for the table
- ▫️ Use an Amazon SQS queue to buffer the incoming writes
- ✅ Add a random number suffix to the partition key values
- ▫️ Add a global secondary index to the table

<details><summary>Пояснення (оригінал, EN)</summary>

DynamoDB stores data as groups of attributes, known as items. Items are similar to rows or records in other database systems. DynamoDB stores and retrieves each item based on the primary key value, which must be unique.

Items are distributed across 10-GB storage units, called partitions (physical storage internal to DynamoDB). Each table has one or more partitions, as shown in the following illustration.

DynamoDB uses the partition key’s value as an input to an internal hash function. The output from the hash function determines the partition in which the item is stored. Each item’s location is determined by the hash value of its partition key.

All items with the same partition key are stored together, and for composite partition keys, are ordered by the sort key value. DynamoDB splits partitions by sort key if the collection size grows bigger than 10 GB.

DynamoDB evenly distributes provisioned throughput—read capacity units (RCUs) and write capacity units (WCUs)—among partitions and automatically supports your access patterns using the throughput you have provisioned. However, if your access pattern exceeds 3000 RCU or 1000 WCU for a single partition key value, your requests might be throttled with a ProvisionedThroughputExceededException error.

To avoid request throttling, design your DynamoDB table with the right partition key to meet your access requirements and provide even distribution of data. Recommendations for doing this include the following:

• Use high cardinality attributes (e.g. email_id, employee_no, customer_id etc.)

• Use composite attributes

• Cache popular items

• Add random numbers or digits from a pre-determined range for write-heavy use cases

In this case there is a hot partition due to the order date being used as the partition key and this is causing writes to be throttled. Therefore, the best solution to ensure the writes are more evenly distributed in this scenario is to add a random number suffix to the partition key values.

CORRECT: "Add a random number suffix to the partition key values" is the correct answer.

INCORRECT: "Increase the read and write capacity units for the table" is incorrect as this will not solve the hot partition issue and we know that the consumed throughput is lower than provisioned throughput.

INCORRECT: "Add a global secondary index to the table" is incorrect as a GSI is used for querying data more efficiently, it will not solve the problem of write performance due to a hot partition.

INCORRECT: "Use an Amazon SQS queue to buffer the incoming writes" is incorrect as this is not the lowest cost option. You would need to have producers and consumers of the queue as well as paying for the queue itself.

Джерела: <https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/>

</details>

---

<a id="t08-q40"></a>
### T08-Q40
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer recently created an Amazon DynamoDB table. The table has the following configuration:

The Developer attempted to add two items for userid “user0001” with unique timestamps and received an error for the second item stating: “The conditional request failed”.

What MUST the Developer do to resolve the issue?

- ▫️ Use the SDK to add the items
- ▫️ Update the table with a primary sort key for the timestamp attribute
- ▫️ Add a local secondary index (LSI) for the timestamp attribute
- ✅ Recreate the table with a composite key consisting of userid and timestamp

<details><summary>Пояснення (оригінал, EN)</summary>

DynamoDB stores and retrieves data based on a Primary key. There are two types of Primary key:

•Partition key – unique attribute (e.g. user ID).

• Value of the Partition key is input to an internal hash function which determines the partition or physical location on which the data is stored.

• If you are using the Partition key as your Primary key, then no two items can have the same partition key.

Composite key – Partition key + Sort key in combination.

• Example is user posting to a forum. Partition key would be the user ID, Sort key would be the timestamp of the post.

• 2 items may have the same Partition key, but they must have a different Sort key.

• All items with the same Partition key are stored together, then sorted according to the Sort key value.

• Allows you to store multiple items with the same partition key.

As stated above, if using a partition key alone as per the configuration provided with the question, then you cannot have two items with the same partition key. The only resolution is to recreate the table with a composite key consisting of the userid and timestamp attributes. In that case the Developer will be able to add multiple items with the same userid as long as the timestamp is unique.

CORRECT: "Recreate the table with a composite key consisting of userid and timestamp" is the correct answer.

INCORRECT: "Update the table with a primary sort key for the timestamp attribute" is incorrect as you cannot update the table in this case, it must be recreated.

INCORRECT: "Add a local secondary index (LSI) for the timestamp attribute" is incorrect as the Developer will still not be able to add multiple entries to the main table for the same userid.

INCORRECT: "Use the SDK to add the items" is incorrect as it doesn’t matter whether you use the console, CLI or SDK, the conditional update will still fail with this configuration.

Джерела: <https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/>

</details>

---

<a id="t09-q36"></a>
### T09-Q36
*⏭ пропущено · домен: офіційна мітка*

A CRM application is hosted on Amazon EC2 instances with the database tier using DynamoDB. The customers have raised privacy and security concerns regarding sending and receiving data across the public internet.

As a developer associate, which of the following would you suggest as an optimal solution for providing communication between EC2 instances and DynamoDB without using the public internet?

- ▫️ Create a NAT Gateway to provide the necessary communication channel between EC2 instances and DynamoDB
- ▫️ The firm can use a virtual private network (VPN) to route all DynamoDB network traffic through their own corporate network infrastructure
- ▫️ Create an Internet Gateway to provide the necessary communication channel between EC2 instances and DynamoDB
- ✅ Configure VPC endpoints for DynamoDB that will provide required internal access without using public internet

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure VPC endpoints for DynamoDB that will provide required internal access without using public internet

When you create a VPC endpoint for DynamoDB, any requests to a DynamoDB endpoint within the Region (for example, dynamodb.us-west-2.amazonaws.com) are routed to a private DynamoDB endpoint within the Amazon network. You don't need to modify your applications running on EC2 instances in your VPC. The endpoint name remains the same, but the route to DynamoDB stays entirely within the Amazon network, and does not access the public internet. You use endpoint policies to control access to DynamoDB. Traffic between your VPC and the AWS service does not leave the Amazon network.

Using Amazon VPC Endpoints to Access DynamoDB:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/vpc-endpoints-dynamodb.html

Incorrect options:

The firm can use a virtual private network (VPN) to route all DynamoDB network traffic through their own corporate network infrastructure - You can address the requested security concerns by using a virtual private network (VPN) to route all DynamoDB network traffic through your own corporate network infrastructure. However, this approach can introduce bandwidth and availability challenges and hence is not an optimal solution here.

Create a NAT Gateway to provide the necessary communication channel between EC2 instances and DynamoDB - You can use a network address translation (NAT) gateway to enable instances in a private subnet to connect to the internet or other AWS services, but prevent the internet from initiating a connection with those instances. NAT Gateway is not useful here since the instance and DynamoDB are present in AWS network and do not need NAT Gateway for communicating with each other.

Create an Internet Gateway to provide the necessary communication channel between EC2 instances and DynamoDB - An internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet. An internet gateway serves two purposes: to provide a target in your VPC route tables for internet-routable traffic, and to perform network address translation (NAT) for instances that have been assigned public IPv4 addresses. Using an Internet Gateway would imply that the EC2 instances are connecting to DynamoDB using the public internet. Therefore, this option is incorrect.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/vpc-endpoints-dynamodb.html> · <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html> · <https://docs.aws.amazon.com/vpc/latest/userguide/Carrier_Gateway.html>

</details>

---

<a id="t10-q02"></a>
### T10-Q02
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An application writes items to an Amazon DynamoDB table. As the application scales to thousands of instances, calls to the DynamoDB API generate occasional ThrottlingException errors. The application is coded in a language that is incompatible with the AWS SDK.

What can be done to prevent the errors from occurring?

- ▫️ Pass API calls through Amazon API Gateway
- ✅ Add exponential backoff to the application logic
- ▫️ Send the items to DynamoDB through Amazon Kinesis Data Firehose
- ▫️ Use Amazon SQS as an API message bus

<details><summary>Пояснення (оригінал, EN)</summary>

Implementing error retries and exponential backoff is a good way to resolve this issue. Exponential backoff can improve an application's reliability by using progressively longer waits between retries. If you're using an AWS SDK, this logic is built‑in. If you're not using an AWS SDK, consider manually implementing exponential backoff.

Additional options for preventing throttling from occurring include:

• Distribute read and write operations as evenly as possible across your table. A hot partition can degrade the overall performance of your table. For more information, see Designing Partition Keys to Distribute Your Workload Evenly.

• Implement a caching solution. If your workload is mostly read access to static data, then query results can be delivered much faster if the data is in a well‑designed cache rather than in a database. DynamoDB Accelerator (DAX) is a caching service that offers fast in‑memory performance for your application. You can also use Amazon ElastiCache.

CORRECT: "Add exponential backoff to the application logic" is the correct answer.

INCORRECT: "Use Amazon SQS as an API message bus" is incorrect. SQS is used for decoupling (messages, nut not APIs), however for this scenario it would add extra cost and complexity.

INCORRECT: "Pass API calls through Amazon API Gateway" is incorrect. For this scenario we don’t want to add an additional layer in when we can simply configure the application to back off and retry.

INCORRECT: "Send the items to DynamoDB through Amazon Kinesis Data Firehose" is incorrect as DynamoDB is not a supported destination for Kinesis Data Firehose.

Джерела: <https://docs.aws.amazon.com/general/latest/gr/api-retries.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-table-throttled/>

</details>

---

<a id="t12-q58"></a>
### T12-Q58
*⏭ пропущено · домен: офіційна мітка*

An organization with high data volume workloads have successfully moved to DynamoDB after having many issues with traditional database systems. However, a few months into production, DynamoDB tables are consistently recording high latency.

As a Developer Associate, which of the following would you suggest to reduce the latency? (Select two)

- ✅ Use eventually consistent reads in place of strongly consistent reads whenever possible
- ✅ Consider using Global tables if your application is accessed by globally distributed users
- ▫️ Increase the request timeout settings, so the client gets enough time to complete the requests, thereby reducing retries on the system
- ▫️ Use DynamoDB Accelerator (DAX) for businesses with heavy write-only workloads
- ▫️ Reduce connection pooling, which keeps the connections alive even when user requests are not present, thereby, blocking the services

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon DynamoDB is a key-value and document database that delivers single-digit millisecond performance at any scale. It's a fully managed, multi-Region, multi-master, durable database with built-in security, backup, and restore and in-memory caching for internet-scale applications.

Consider using Global tables if your application is accessed by globally distributed users - If you have globally dispersed users, consider using global tables. With global tables, you can specify the AWS Regions where you want the table to be available. This can significantly reduce latency for your users. So, reducing the distance between the client and the DynamoDB endpoint is an important performance fix to be considered.

Use eventually consistent reads in place of strongly consistent reads whenever possible - If your application doesn't require strongly consistent reads, consider using eventually consistent reads. Eventually consistent reads are cheaper and are less likely to experience high latency.

Incorrect options:

Increase the request timeout settings, so the client gets enough time to complete the requests, thereby reducing retries on the system - This statement is incorrect. The right way is to reduce the request timeout settings. This causes the client to abandon high latency requests after the specified time period and then send a second request that usually completes much faster than the first.

Reduce connection pooling, which keeps the connections alive even when user requests are not present, thereby, blocking the services - This is not correct. When you're not making requests, consider having the client send dummy traffic to a DynamoDB table. Alternatively, you can reuse client connections or use connection pooling. All of these techniques keep internal caches warm, which helps keep latency low.

Use DynamoDB Accelerator (DAX) for businesses with heavy write-only workloads - This is not correct. If your traffic is read-heavy, consider using a caching service such as DynamoDB Accelerator (DAX). DAX is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement—from milliseconds to microseconds—even at millions of requests per second.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-high-latency/> · <https://aws.amazon.com/dynamodb/>

</details>

---
