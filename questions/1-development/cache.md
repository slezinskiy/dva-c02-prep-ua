# ElastiCache / RDS / Aurora

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 21  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q53"></a>
### T01-Q53
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Кеш: запис у БД, потім інвалідація кешу.** → [детальніше](../../06-contradictions-and-traps.md#c03)

The development team at an e-commerce company is preparing for the upcoming Thanksgiving sale. The product manager wants the development team to implement appropriate caching strategy on Amazon ElastiCache to withstand traffic spikes on the website during the sale. A key requirement is to facilitate consistent updates to the product prices and product description, so that the cache never goes out of sync with the backend.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Use a caching strategy to update the cache and the backend at the same time
- ✅ Use a caching strategy to write to the backend first and then invalidate the cache
- ▫️ Use a caching strategy to write to the cache directly and sync the backend at a later time
- ▫️ Use a caching strategy to write to the backend first and wait for the cache to expire via TTL

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon ElastiCache allows you to seamlessly set up, run, and scale popular open-Source compatible in-memory data stores in the cloud. Build data-intensive apps or boost the performance of your existing databases by retrieving data from high throughput and low latency in-memory data stores. Amazon ElastiCache is a popular choice for real-time use cases like Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics, and Queuing.

Broadly, you can set up two types of caching strategies:

Lazy Loading

Write-Through

 via - https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html

 via - https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html

Use a caching strategy to write to the backend first and then invalidate the cache

This option is similar to the write-through strategy wherein the application writes to the backend first and then invalidate the cache. As the cache gets invalidated, the caching engine would then fetch the latest value from the backend, thereby making sure that the product prices and product description stay consistent with the backend.

Incorrect options:

Use a caching strategy to update the cache and the backend at the same time - The cache and the backend cannot be updated at the same time via a single atomic operation as these are two separate systems. Therefore this option is incorrect.

Use a caching strategy to write to the backend first and wait for the cache to expire via TTL - This strategy could work if the TTL is really short. However, for the duration of this TTL, the cache would be out of sync with the backend, hence this option is not correct for the given use-case.

Use a caching strategy to write to the cache directly and sync the backend at a later time - This option is given as a distractor as this strategy is not viable to address the given use-case. The product prices and description on the cache must always stay consistent with the backend. You cannot sync the backend at a later time.

Reference:

https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html

</details>

---

<a id="t02-q05"></a>
### T02-Q05
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Redis cluster mode: шифрування + HA.** → [детальніше](../../06-contradictions-and-traps.md#c04)

> ⚠️ **Пояснення застаріле: Memcached вже підтримує шифрування in-transit.** → [детальніше](../../07-outdated-and-wrong-answers.md#o08)

An application uses an Amazon RDS database. The company requires that the performance of database reads is improved, and they want to add a caching layer in front of the database. The cached data must be encrypted, and the solution must be highly available.

Which solution will meet these requirements?

- ▫️ Amazon DynamoDB Accelerator (DAX).
- ✅ Amazon ElastiCache for Redis in cluster mode.
- ▫️ Amazon CloudFront with multiple origins.
- ▫️ Amazon ElastiCache for Memcached.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon ElastiCache is an in-memory database cache that can be used in front of Amazon RDS. The key to answering this question is to know the differences between ElastiCache Memcached and ElastiCache Redis. To support both encryption and high availability we must use ElastiCache Redis with cluster mode enabled.

You can see the differences between the different engines and configuration options for ElastiCache in the table below:

CORRECT: "Amazon ElastiCache for Redis in cluster mode" is the correct answer (as explained above.)

INCORRECT: "Amazon ElastiCache for Memcached" is incorrect.

The Memcached engine does not support encryption or high availability.

INCORRECT: "Amazon CloudFront with multiple origins" is incorrect.

You cannot configure an Amazon RDS as an origin for Amazon RDS. Also, what would the second origin be anyway? There’s only one database!

INCORRECT: "Amazon DynamoDB Accelerator (DAX)" is incorrect.

DynamoDB DAX can be used to increase the performance of DynamoDB tables and offload read requests. It cannot be used in front of an Amazon RDS database.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Redis-RedisCluster.html>

</details>

---

<a id="t02-q12"></a>
### T02-Q12
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії: тут ElastiCache (найменша затримка). Інші питання дають DynamoDB — див. правило вибору.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A Developer is building a three-tier web application that must be able to handle a minimum of 10,000 requests per minute. The requirements state that the web tier should be completely stateless while the application maintains session state data for users.

How can the session state data be maintained externally, whilst keeping latency at the LOWEST possible value?

- ✅ Create an Amazon ElastiCache Redis cluster, then implement session handling at the application level to leverage the cluster for session data storage
- ▫️ Create an Amazon RedShift instance, then implement session handling at the application level to leverage a database inside the RedShift database instance for session data storage
- ▫️ Create an Amazon DynamoDB table, then implement session handling at the application level to leverage the table for session data storage
- ▫️ Implement a shared Amazon EFS file system solution across the underlying Amazon EC2 instances, then implement session handling at the application level to leverage the EFS file system for session data storage

<details><summary>Пояснення (оригінал, EN)</summary>

It is common to use key/value stores for storing session state data. The two options presented in the answers are Amazon DynamoDB and Amazon ElastiCache Redis. Of these two, ElastiCache will provide the lowest latency as it is an in-memory database.

Therefore, the best answer is to create an Amazon ElastiCache Redis cluster, then implement session handling at the application level to leverage the cluster for session data storage

CORRECT: "Create an Amazon ElastiCache Redis cluster, then implement session handling at the application level to leverage the cluster for session data storage" is the correct answer.

INCORRECT: "Create an Amazon DynamoDB table, then implement session handling at the application level to leverage the table for session data storage" is incorrect as though this is a good solution for storing session state data, the latency will not be as low as with ElastiCache.

INCORRECT: "Create an Amazon RedShift instance, then implement session handling at the application level to leverage a database inside the RedShift database instance for session data storage" is incorrect. RedShift is a data warehouse that is used for OLAP use cases, not for storing session state data.

INCORRECT: "Implement a shared Amazon EFS file system solution across the underlying Amazon EC2 instances, then implement session handling at the application level to leverage the EFS file system for session data storage" is incorrect. For session state data a key/value store such as DynamoDB or ElastiCache will provide better performance.

Джерела: <https://aws.amazon.com/caching/session-management/>

</details>

---

<a id="t02-q18"></a>
### T02-Q18
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache. Порівняй з DynamoDB-варіантами.** → [детальніше](../../06-contradictions-and-traps.md#c01)

An e-commerce web application that shares session state on-premises is being migrated to AWS. The application must be fault tolerant, natively highly scalable, and any service interruption should not affect the user experience.

What is the best option to store the session state?

- ▫️ Store the session state in Amazon S3
- ▫️ Store the session state in Amazon CloudFront
- ✅ Store the session state in Amazon ElastiCache
- ❌ Enable session stickiness using elastic load balancers

<details><summary>Пояснення (оригінал, EN)</summary>

There are various ways to manage user sessions including storing those sessions locally to the node responding to the HTTP request or designating a layer in your architecture which can store those sessions in a scalable and robust manner. Common approaches used include utilizing Sticky sessions or using a Distributed Cache for your session management.

In this scenario, a distributed cache is suitable for storing session state data. ElastiCache can perform this role and with the Redis engine replication is also supported. Therefore, the solution is fault-tolerant and natively highly scalable.

CORRECT: "Store the session state in Amazon ElastiCache" is the correct answer.

INCORRECT: "Store the session state in Amazon CloudFront" is incorrect as CloudFront is not suitable for storing session state data, it is used for caching content for better global performance.

INCORRECT: "Store the session state in Amazon S3" is incorrect as though you can store session data in Amazon S3 and replicate the data to another bucket, this would result in a service interruption if the S3 bucket was not accessible.

INCORRECT: "Enable session stickiness using elastic load balancers" is incorrect as this feature directs sessions from a specific client to a specific EC2 instances. Therefore, if the instance fails the user must be redirected to another EC2 instance and the session state data would be lost.

Джерела: <https://aws.amazon.com/caching/session-management/>

</details>

---

<a id="t02-q33"></a>
### T02-Q33
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

An organization handles data that requires high availability in its relational database. The main headquarters for the organization is in Virginia with smaller offices located in California. The main headquarters uses the data more frequently than the smaller offices. How should the developer configure their databases to meet high availability standards?

- ✅ Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in Virginia.
- ❌ Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in California.
- ▫️ Create an Athena database with the primary database in Virginia and specify the failover to the Athena replica in another AZ in Virginia.
- ▫️ Create a DynamoDB database with the primary database in Virginia and specify the failover to the DynamoDB replica in another AZ in Virginia.

<details><summary>Пояснення (оригінал, EN)</summary>

Aurora is a relational database that provides high availability by allowing customers to create up to 15 database replications in different Availability Zones. It also allows you to specify which Aurora replica can be promoted to the primary database should the primary database become unavailable. Selecting the AZ that is closest to the main headquarters should not negatively impact the smaller offices but changing the primary database to California could negatively impact the main headquarters.

CORRECT: "Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in Virginia" is the correct answer (as explained above.)

INCORRECT: "Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in California" is incorrect. It could create some latency issues for the main headquarters in Virginia.

INCORRECT: "Create a DynamoDB database with the primary database in Virginia and specify the failover to the DynamoDB replica in another AZ in Virginia" is incorrect. DynamoDB is not a relational database.

INCORRECT: "Create an Athena database with the primary database in Virginia and specify the failover to the Athena replica in another AZ in Virginia" is incorrect. Athena analyzes data but is not a database service.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.html>

</details>

---

<a id="t02-q41"></a>
### T02-Q41
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

A Development team is involved with migrating an on-premises MySQL database to Amazon RDS. The database usage is very read-heavy. The Development team wants re-factor the application code to achieve optimum read performance for queries.

How can this objective be met?

- ▫️ Add a connection string to use a read replica on an Amazon EC2 instance
- ✅ Add a connection string to use an Amazon RDS read replica for read queries
- ▫️ Add database retries to the code and vertically scale the Amazon RDS database
- ▫️ Use Amazon RDS with a multi-AZ deployment

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon RDS uses the MariaDB, MySQL, Oracle, and PostgreSQL DB engines' built-in replication functionality to create a special type of DB instance called a Read Replica from a source DB instance. Updates made to the source DB instance are asynchronously copied to the Read Replica.

You can reduce the load on your source DB instance by routing read queries from your applications to the Read Replica. Using Read Replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read-heavy database workloads.

In the image below a primary Amazon RDS database server allows reads and writes while a Read Replica can be used for running read-only workloads such as BI/reporting. This reduces the load on the primary database.

It is necessary to add logic to your code to direct read traffic to the Read Replica and write traffic to the primary database. Therefore, in this scenario the Development team will need to “Add a connection string to use an Amazon RDS read replica for read queries”.

CORRECT: "Add a connection string to use an Amazon RDS read replica for read queries" is the correct answer.

INCORRECT: "Add database retries to the code and vertically scale the Amazon RDS database" is incorrect as this is not a good way to scale reads as you will likely hit a ceiling at some point in terms of cost or instance type. Scaling reads can be better implemented with horizontal scaling using a Read Replica.

INCORRECT: "Use Amazon RDS with a multi-AZ deployment" is incorrect as this creates a standby copy of the database in another AZ that can be failed over to in a failure scenario. This is used for DR not (at least not primarily) used for scaling performance. It is possible for certain RDS engines to use a multi-AZ standby as a read replica however the requirements in this solution do not warrant this configuration.

INCORRECT: "Add a connection string to use a read replica on an Amazon EC2 instance" is incorrect as Read Replicas are something you create on Amazon RDS, not on an EC2 instance.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html>

</details>

---

<a id="t02-q53"></a>
### T02-Q53
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

A developer is updating an Amazon Aurora MySQL database to allow more clients to connect. What database parameter needs to be updated to support a higher number of client connections?

- ▫️ max_user_connections
- ▫️ max_allowed_packet
- ✅ max_connections
- ▫️ max_join_size

<details><summary>Пояснення (оригінал, EN)</summary>

The maximum number of connections allowed to an Aurora MySQL DB instance is determined by the max_connections parameter in the instance-level parameter group for the DB instance.

You can increase the maximum number of connections to your Aurora MySQL DB instance by scaling the instance up to a DB instance class with more memory, or by setting a larger value for the max_connections parameter in the DB parameter group for your instance, up to 16,000.

CORRECT: "max_connections" is the correct answer (as explained above.)

INCORRECT: "max_allowed_packet" is incorrect. This parameter sets the maximum size of one packet or any generated or intermediate string.

INCORRECT: "max_join_size" is incorrect. This option is used to set a limit on the maximum number of row accesses.

INCORRECT: "max_user_connections" is incorrect. This option limits the number of simultaneous connections that the user can make.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/rds-mysql-max-connections/>

</details>

---

<a id="t02-q65"></a>
### T02-Q65
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сумнівний ключ: read replicas не масштабують ЗАПИСИ замовлень.** → [детальніше](../../07-outdated-and-wrong-answers.md#d03)

An ecommerce company manages a storefront that uses an Amazon API Gateway API which exposes an AWS Lambda function. The Lambda functions processes orders and stores the orders in an Amazon RDS for MySQL database. The number of transactions increases sporadically during marketing campaigns, and then goes close to zero during quite times.

How can a developer increase the elasticity of the system MOST cost-effectively?

- ▫️ Create an Amazon SQS queue. Publish transactions to the queue and set the queue to invoke the Lambda function. Set the reserved concurrency of the Lambda function to be equal to the max number of database connections.
- ▫️ Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average CPU utilization.
- ✅ Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average connections of Aurora Replicas.
- ▫️ Create an Amazon SNS topic. Publish transactions to the topic configure an SQS queue as a destination. Configure Lambda to process transactions from the queue.

<details><summary>Пояснення (оригінал, EN)</summary>

The most efficient solution would be to use Aurora Auto Scaling and configure the scaling events to happen based on target metric. The metric to use is Average connections of Aurora Replicas which will create a policy based on the average number of connections to Aurora Replicas.

This will ensure that the Aurora replicas scale based on actual numbers of connections to the replicas which will vary based on how busy the storefront is and how many transactions are being processed.

CORRECT: "Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average connections of Aurora Replicas" is the correct answer (as explained above.)

INCORRECT: "Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average CPU utilization" is incorrect.

The better metric to use for this situation would be the number of connections to Aurora Replicas as that is the metric that has the closest correlation to the number of transactions being executed.

INCORRECT: "Create an Amazon SNS topic. Publish transactions to the topic configure an SQS queue as a destination. Configure Lambda to process transactions from the queue" is incorrect.

This is highly inefficient. There is no need for an SNS topic in this situation.

INCORRECT: "Create an Amazon SQS queue. Publish transactions to the queue and set the queue to invoke the Lambda function. Set the reserved concurrency of the Lambda function to be equal to the max number of database connections" is incorrect.

This would be less cost effective as you would be paying for the reserved concurrency at all times.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html>

</details>

---

<a id="t04-q18"></a>
### T04-Q18
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An Amazon RDS database is experiencing a high volume of read requests that are slowing down the database. Which fully managed, in-memory AWS database service can assist with offloading reads from the RDS database?

- ✅ Amazon ElastiCache Redis
- ▫️ Memcached on Amazon EC2
- ▫️ Amazon RDS Read Replica
- ▫️ Amazon Aurora Serverless

<details><summary>Пояснення (оригінал, EN)</summary>

ElastiCache is a web service that makes it easy to deploy and run Memcached or Redis protocol-compliant server nodes in the cloud. The in-memory caching provided by ElastiCache can be used to significantly improve latency and throughput for many read-heavy application workloads or compute-intensive workloads.

This is a fully managed AWS service and is ideal for offloading reads from the main database to reduce the performance impact.

CORRECT: "Amazon ElastiCache Redis" is the correct answer.

INCORRECT: "Amazon RDS Read Replica" is incorrect as it is not an in-memory database. RDS Read Replicas can be used for offloading reads from the main database, however.

INCORRECT: "Amazon Aurora Serverless" is incorrect. Aurora Serverless is not an in-memory solution, nor is it suitable for functioning as a method of offloading reads from RDS databases.

INCORRECT: "Memcached on Amazon EC2" is incorrect as this is an implementation of Memcached running on EC2 and therefore is not a fully managed AWS service.

Джерела: <https://aws.amazon.com/elasticache/redis/>

</details>

---

<a id="t04-q30"></a>
### T04-Q30
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache for Redis.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A company is migrating an on-premises web application to AWS. The web application runs on a single server and stores session data in memory. On AWS the company plan to implement multiple Amazon EC2 instances behind an Elastic Load Balancer (ELB). The company want to refactor the application so that data is resilient if an instance fails and user downtime is minimized.

Where should the company move session data to MOST effectively reduce downtime and make users’ session data more fault tolerant?

- ✅ An Amazon ElastiCache for Redis cluster
- ▫️ An Amazon EC2 instance dedicated to session data
- ▫️ The web server’s primary disk
- ▫️ A second Amazon EBS volume

<details><summary>Пояснення (оригінал, EN)</summary>

ElastiCache is a fully managed, low latency, in-memory data store that supports either Memcached or Redis. The Redis engine supports multi-AZ and high availability.

With ElastiCache the company can move the session data to a high-performance, in-memory data store that is well suited to this use case. This will provide high availability for the session data in the case of EC2 instance failure and will reduce downtime for users.

CORRECT: "An Amazon ElastiCache for Redis cluster" is the correct answer.

INCORRECT: "A second Amazon EBS volume" is incorrect as the session data needs to be highly available so should not be stored on an EC2 instance.

INCORRECT: "The web server’s primary disk" is incorrect as the session data needs to be highly available so should not be stored on an EC2 instance.

INCORRECT: "An Amazon EC2 instance dedicated to session data" is incorrect as the session data needs to be highly available so should not be stored on an EC2 instance.

Джерела: <https://aws.amazon.com/elasticache/features/>

</details>

---

<a id="t04-q45"></a>
### T04-Q45
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Кеш: lazy loading (кешувати лише запитане).** → [детальніше](../../06-contradictions-and-traps.md#c03)

An Amazon ElastiCache cluster has been placed in front of a large Amazon RDS database. To reduce cost the ElastiCache cluster should only cache items that are actually requested. How should ElastiCache be optimized?

- ✅ Use a lazy loading caching strategy
- ▫️ Only cache database writes
- ▫️ Enable a TTL on cached data
- ▫️ Use a write-through caching strategy

<details><summary>Пояснення (оригінал, EN)</summary>

There are two caching strategies available: Lazy Loading and Write-Through:

Lazy Loading

Loads the data into the cache only when necessary (if a cache miss occurs).

Lazy loading avoids filling up the cache with data that won’t be requested.

If requested data is in the cache, ElastiCache returns the data to the application.

If the data is not in the cache or has expired, ElastiCache returns a null.

The application then fetches the data from the database and writes the data received into the cache so that it is available for next time.

Data in the cache can become stale if Lazy Loading is implemented without other strategies (such as TTL).

Write Through

When using a write through strategy, the cache is updated whenever a new write or update is made to the underlying database.

Allows cache data to remain up-to-date.

Can add wait time to write operations in your application.

Without a TTL you can end up with a lot of cached data that is never read.

CORRECT: "Use a lazy loading caching strategy" is the correct answer.

INCORRECT: "Use a write-through caching strategy" is incorrect as this will load all database items into the cache increasing cost.

INCORRECT: "Only cache database writes" is incorrect as you cannot cache writes, only reads.

INCORRECT: "Enable a TTL on cached data" is incorrect. This would help expire stale items but it is not a cache optimization strategy that will cache only items that are requested.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html>

</details>

---

<a id="t05-q48"></a>
### T05-Q48
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An eCommerce application uses an Amazon RDS database with Amazon ElastiCache in front. Stock volume data is updated dynamically in listings as sales are made. Customers have complained that occasionally the stock volume data is incorrect, and they end up purchasing items that are out of stock. A Developer has checked the front end and indeed some items display the incorrect stock count.

What could be causing this issue?

- ✅ The cache is not being invalidated when the stock volume data is changed.
- ▫️ The stock volume data is being retrieved using a write-through ElastiCache cluster.
- ▫️ The Amazon RDS database is deployed as Multi-AZ and the standby is inconsistent.
- ▫️ The Amazon RDS database has insufficient IOPS provisioned for its EBS volumes.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon ElastiCache is being used to cache data from the Amazon RDS database to improve performance when performing queries. In this case the cache has stale stock volume data stored and is returning this information when customers are purchasing items.

The resolution is to ensure that the cache is invalidated whenever the stock volume data is changed. This can be done in the application layer.

CORRECT: "The cache is not being invalidated when the stock volume data is changed" is the correct answer.

INCORRECT: "The stock volume data is being retrieved using a write-through ElastiCache cluster" is incorrect. If this was the case the data would not be stale.

INCORRECT: "The Amazon RDS database is deployed as Multi-AZ and the standby is inconsistent" is incorrect. Multi-AZ standbys are not used for reading data and the replication is synchronous so it would not be inconsistent.

INCORRECT: "The Amazon RDS database has insufficient IOPS provisioned for its EBS volumes" is incorrect. This is not the issue here; the stale data is being retrieved from the ElastiCache database.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Strategies.html>

</details>

---

<a id="t05-q49"></a>
### T05-Q49
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → DynamoDB (ElastiCache відсутній у варіантах).** → [детальніше](../../06-contradictions-and-traps.md#c01)

A Developer is designing a fault-tolerant environment where client sessions will be saved. How can the Developer ensure that no sessions are lost if an Amazon EC2 instance fails?

- ▫️ Use Elastic Load Balancer connection draining to stop sending requests to failing instances
- ▫️ Use Amazon SQS to save session data
- ▫️ Use sticky sessions with an Elastic Load Balancer target group
- ✅ Use Amazon DynamoDB to perform scalable session handling

<details><summary>Пояснення (оригінал, EN)</summary>

The DynamoDB Session Handler is a custom session handler for PHP that allows developers to use Amazon DynamoDB as a session store. Using DynamoDB for session storage alleviates issues that occur with session handling in a distributed web application by moving sessions off of the local file system and into a shared location. DynamoDB is fast, scalable, easy to setup, and handles replication of your data automatically.

CORRECT: "Use Amazon DynamoDB to perform scalable session handling" is the correct answer.

INCORRECT: "Use sticky sessions with an Elastic Load Balancer target group" is incorrect as this involves maintaining session state data on the EC2 instances which means that data is lost if an instance fails.

INCORRECT: "Use Amazon SQS to save session data" is incorrect as SQS is not used for session data, it is used for application component decoupling.

INCORRECT: "Use Elastic Load Balancer connection draining to stop sending requests to failing instances" is incorrect as this does not solve the problem of ensuring the session data is available, the data will be on the failing instance and will be lost.

Джерела: <https://docs.aws.amazon.com/aws-sdk-php/v2/guide/feature-dynamodb-session-handler.html>

</details>

---

<a id="t06-q25"></a>
### T06-Q25
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Кеш: write-through (real-time).** → [детальніше](../../06-contradictions-and-traps.md#c03)

A developer is implementing Amazon ElastiCache in an application that needs to reflect real-time data on dashboards. The data is sourced from a database and needs to be stored in the cache.

What would be the ideal caching mechanism for this requirement?

- ✅ A write-through cache
- ▫️ A read-through cache
- ▫️ A write-behind cache
- ❌ A lazy-loading cache

<details><summary>Пояснення (оригінал, EN)</summary>

The write-through caching strategy ensures that data is written into the cache every time it's updated in the database. This makes it an ideal strategy for scenarios where real-time data is required on dashboards as it reduces the chances of a cache miss, and any read request will have the latest data.

CORRECT: "A write-through cache" is the correct answer (as explained above.)

INCORRECT: "A lazy-loading cache" is incorrect.

Lazy-loading strategy only caches the data when a request for that data is made. This might cause a delay in populating real-time dashboards during the first request for data.

INCORRECT: "A write-behind cache" is incorrect.

Write-behind caching is not supported by Amazon ElastiCache. This strategy can lead to data inconsistency between the cache and the database.

INCORRECT: "A read-through cache" is incorrect.

Read-through caches are not supported by Amazon ElastiCache.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Strategies.html>

</details>

---

<a id="t06-q43"></a>
### T06-Q43
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Мінус write-through: кеш роздувається.** → [детальніше](../../06-contradictions-and-traps.md#c03)

An application stores data in Amazon RDS and uses Amazon ElastiCache to improve read performance. The developer has configured ElastiCache to update the cache immediately after any writes to the primary database.

What will be the result of this approach to caching?

- ❌ Load on the RDS database instance will increase because the cache is updated for every database update.
- ✅ The cache will become large and expensive because the infrequently requested data is also written to the cache.
- ▫️ Caching will slow performance of the read queries because the cache is updated when the cache cannot find the requested data.
- ▫️ There is a cache miss penalty because the cache is updated only after a cache miss, resulting in response latency.

<details><summary>Пояснення (оригінал, EN)</summary>

The ElastiCache deployment is using a write-through caching strategy. The write-through strategy adds data or updates data in the cache whenever data is written to the database. This means data in the cache is never stale. There is a write penalty, but not a read penalty (in terms of latency added).

However, with a write-through strategy, most data are never read so the cache can become large and expensive. Adding a TTL to records can assist with this.

CORRECT: "The cache will become large and expensive because the infrequently requested data is also written to the cache" is the correct answer (as explained above.)

INCORRECT: "Caching will slow performance of the read queries because the cache is updated when the cache cannot find the requested data" is incorrect.

This is true of a lazy loading strategy. With a write-through strategy there is a write penalty, but not a read penalty.

INCORRECT: "Load on the RDS database instance will increase because the cache is updated for every database update" is incorrect.

There is still only one write to the RDS database instance.

INCORRECT: "There is a cache miss penalty because the cache is updated only after a cache miss, resulting in response latency" is incorrect.

This is a description of a lazy loading strategy, not a write-through strategy.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html>

</details>

---

<a id="t07-q28"></a>
### T07-Q28
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Redis: HA/реплікація, бо відновлення кешу дороге.** → [детальніше](../../06-contradictions-and-traps.md#c04)

A company uses Amazon RDS as its database. For improved user experience, it has been decided that a highly reliable fully-managed caching layer has to be configured in front of RDS.

Which of the following is the right choice, keeping in mind that cache content regeneration is a costly activity?

- ▫️ Implement Amazon ElastiCache Memcached
- ✅ Implement Amazon ElastiCache Redis in Cluster Mode
- ▫️ Migrate the database to Amazon Redshift
- ▫️ Install Redis on an Amazon EC2 instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Implement Amazon ElastiCache Redis in Cluster-Mode - One can leverage ElastiCache for Redis with cluster mode enabled to enhance reliability and availability with little change to your existing workload. Cluster mode comes with the primary benefit of horizontal scaling of your Redis cluster, with almost zero impact on the performance of the cluster.

When building production workloads, you should consider using a configuration with replication, unless you can easily recreate your data. Enabling Cluster-Mode provides a number of additional benefits in scaling your cluster. In short, it allows you to scale in or out the number of shards (horizontal scaling) versus scaling up or down the node type (vertical scaling). This means that Cluster-Mode can scale to very large amounts of storage (potentially 100s of terabytes) across up to 90 shards, whereas a single node can only store as much data in memory as the instance type has capacity for.

Redis Cluster config:  via - https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/

Incorrect options:

Install Redis on an Amazon EC2 instance - It is possible to install Redis directly onto Amazon EC2 instance. But, unlike ElastiCache for Redis, which is a managed service, you will need to maintain and manage your Redis installation.

Implement Amazon ElastiCache Memcached - Redis and Memcached are popular, open-source, in-memory data stores. Although they are both easy to use and offer high performance, there are important differences to consider when choosing an engine. Memcached is designed for simplicity while Redis offers a rich set of features that make it effective for a wide range of use cases. Redis offers snapshots facility, replication, and supports transactions, which Memcached cannot and hence ElastiCache Redis is the right choice for our use case.

Migrate the database to Amazon Redshift - Amazon Redshift belongs to "Big Data as a Service" cloud facility, while Redis can be primarily classified under "In-Memory Databases". "Data Warehousing" is the primary reason why developers consider Amazon Redshift over the competitors, whereas "Performance" is the key factor in picking Redis.

Джерела: <https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/> · <https://aws.amazon.com/elasticache/redis-vs-memcached/>

</details>

---

<a id="t07-q48"></a>
### T07-Q48
*⏭ пропущено · домен: офіційна мітка*

The development team at a social media company is considering using Amazon ElastiCache to boost the performance of their existing databases.

As a Developer Associate, which of the following use-cases would you recommend as the BEST fit for ElastiCache? (Select two)

- ✅ Use ElastiCache to improve latency and throughput for read-heavy application workloads
- ✅ Use ElastiCache to improve performance of compute-intensive workloads
- ▫️ Use ElastiCache to improve latency and throughput for write-heavy application workloads
- ▫️ Use ElastiCache to improve performance of Extract-Transform-Load (ETL) workloads
- ▫️ Use ElastiCache to run highly complex JOIN queries

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use ElastiCache to improve latency and throughput for read-heavy application workloads

Use ElastiCache to improve performance of compute-intensive workloads

Amazon ElastiCache allows you to run in-memory data stores in the AWS cloud. Amazon ElastiCache is a popular choice for real-time use cases like Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics, and Queuing.

 via - https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/elasticache-use-cases.html

Amazon ElastiCache can be used to significantly improve latency and throughput for many read-heavy application workloads (such as social networking, gaming, media sharing, and Q&A portals) or compute-intensive workloads (such as a recommendation engine) by allowing you to store the objects that are often read in the cache.

Overview of Amazon ElastiCache features:  via - https://aws.amazon.com/elasticache/features/

Incorrect options:

Use ElastiCache to improve latency and throughput for write-heavy application workloads - As mentioned earlier in the explanation, Amazon ElastiCache can be used to significantly improve latency and throughput for many read-heavy application workloads. Caching is not a good fit for write-heavy applications as the cache goes stale at a very fast rate.

Use ElastiCache to improve performance of Extract-Transform-Load (ETL) workloads - ETL workloads involve reading and transforming high volume data which is not a good fit for caching. You should use AWS Glue or Amazon EMR to facilitate ETL workloads.

Use ElastiCache to run highly complex JOIN queries - Complex JSON queries can be run on relational databases such as RDS or Aurora. ElastiCache is not a good fit for this use-case.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/elasticache-use-cases.html> · <https://aws.amazon.com/elasticache/features/>

</details>

---

<a id="t08-q38"></a>
### T08-Q38
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Redis: максимальний uptime.** → [детальніше](../../06-contradictions-and-traps.md#c04)

A developer needs to implement a caching layer in front of an Amazon RDS database. If the caching layer fails, it is time consuming to repopulate cached data so the solution should be designed for maximum uptime. Which solution is best for this scenario?

- ▫️ Migrate the database to Amazon RedShift
- ▫️ Implement Amazon DynamoDB DAX
- ▫️ Implement Amazon ElastiCache Memcached
- ✅ Implement Amazon ElastiCache Redis

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon ElastiCache provides fully managed implementations of two popular in-memory data stores – Redis and Memcached. ElastiCache is a web service that makes it easy to deploy and run Memcached or Redis protocol-compliant server nodes in the cloud.

The in-memory caching provided by ElastiCache can be used to significantly improve latency and throughput for many read-heavy application workloads or compute-intensive workloads. It is common to use ElastiCache as a cache in front of databases such as Amazon RDS.

The two implementations, Memcached, and Redis, each offer different capabilities and limitations. As you can see from the table below, only Redis supports read replicas and auto-failover:

The Redis implementation must be used if high availability is required, as is necessary for this scenario. Therefore the correct answer is to use Amazon ElastiCache Redis.

CORRECT: "Implement Amazon ElastiCache Redis" is the correct answer.

INCORRECT: "Implement Amazon ElastiCache Memcached" is incorrect as Memcached does not offer read replicas or auto-failover and therefore cannot provide high availability.

INCORRECT: "Migrate the database to Amazon RedShift" is incorrect as RedShift is a data warehouse for use in online analytics processing (OLAP) use cases. It is not suitable to be used as a caching layer.

INCORRECT: "Implement Amazon DynamoDB DAX" is incorrect as DAX is used in front of DynamoDB, not Amazon RDS.

Джерела: <https://aws.amazon.com/elasticache/redis-vs-memcached/>

</details>

---

<a id="t10-q53"></a>
### T10-Q53
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A multimedia streaming service wants to migrate its user authentication system to AWS. The system keeps user session data during an active session, which is crucial for seamless user experience. The new system needs to be fault-tolerant, highly scalable natively, and any service disruption must not affect user experience.

What is the best option to store the user session data?

- ▫️ Store the user session data in Amazon S3.
- ▫️ Store the user session data in Amazon CloudFront.
- ▫️ Enable session stickiness using elastic load balancers.
- ✅ Store the user session data in Amazon ElastiCache.

<details><summary>Пояснення (оригінал, EN)</summary>

ElastiCache is a fully managed in-memory caching service that provides a high-performance, scalable, and cost-effective caching solution, while removing the complexity associated with deploying and managing a distributed cache environment. It is well-suited for storing user session data due to its high-speed and low-latency capabilities.

CORRECT: "Store the user session data in Amazon ElastiCache" is the correct answer (as explained above.)

INCORRECT: "Store the user session data in Amazon CloudFront" is incorrect.

CloudFront is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency and high transfer speeds. However, it is not designed for session state storage and thus, would not be the best choice for storing user session data.

INCORRECT: "Store the user session data in Amazon S3" is incorrect.

Amazon S3 provides durable, scalable object storage but is not optimized for the type of high-speed, read/write operations typically associated with session data. While you could technically use it to store session data, it would likely not provide the same level of performance as an in-memory store like ElastiCache.

INCORRECT: "Enable session stickiness using elastic load balancers" is incorrect.

Enabling session stickiness in load balancers can help ensure that a client is consistently directed to the same instance if that instance is healthy. However, this is more of a traffic routing mechanism rather than a storage solution for session data.

Джерела: <https://aws.amazon.com/elasticache/>

</details>

---

<a id="t11-q40"></a>
### T11-Q40
*⏭ пропущено · домен: офіційна мітка*

A retail company is migrating its on-premises database to Amazon RDS for PostgreSQL. The company has read-heavy workloads. The development team at the company is looking at refactoring the code to achieve optimum read performance for SQL queries.

Which solution will address this requirement with the least current as well as future development effort?

- ▫️ Set up Amazon RDS in the multi-AZ configuration with a single standby instance. Refactor the application code so that the queries use the standby instance endpoint
- ▫️ Configure Elasticache for Memcached to act as a caching layer for Amazon RDS. Refactor the application code so that the queries use the Elasticache for Memcached endpoint
- ✅ Set up Amazon RDS with one or more read replicas. Refactor the application code so that the queries use the endpoint for the read replicas
- ▫️ Configure Elasticache for Redis to act as a caching layer for Amazon RDS. Refactor the application code so that the queries use the Elasticache for Redis endpoint

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up Amazon RDS with one or more read replicas. Refactor the application code so that the queries use the endpoint for the read replicas

Amazon RDS uses the PostgreSQL DB engine's built-in replication functionality to create a special type of DB instance called a read replica from a source DB instance. The source DB instance becomes the primary DB instance. Updates made to the primary DB instance are asynchronously copied to the read replica. You can reduce the load on your primary DB instance by routing read queries from your applications to the read replica. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read-heavy database workloads. For the given use case, you can achieve optimum read performance for SQL queries by using the read-replica endpoint for the read-heavy workload.

 via - https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html

Incorrect options:

Configure Elasticache for Redis to act as a caching layer for Amazon RDS. Refactor the application code so that the queries use the Elasticache for Redis endpoint

Configure Elasticache for Memcached to act as a caching layer for Amazon RDS. Refactor the application code so that the queries use the Elasticache for Memcached endpoint

Both Redis and Memcached are popular, open-source, in-memory data stores (also known as in-memory caches). These are not relational databases and cannot be used to run SQL queries. So, both these options are incorrect.

Set up Amazon RDS in the multi-AZ configuration with a single standby instance. Refactor the application code so that the queries use the standby instance endpoint - In an Amazon RDS Multi-AZ deployment with a single standby instance, Amazon RDS automatically creates a primary database (DB) instance and synchronously replicates the data to an instance in a different AZ. When it detects a failure, Amazon RDS automatically fails over to a standby instance without manual intervention. You cannot route the read queries from an application to the standby instance of a multi-AZ RDS database as it's not accessible for the read traffic in the single standby instance configuration.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/requests-rds-read-replicas/> · <https://aws.amazon.com/rds/features/multi-az/> · <https://aws.amazon.com/blogs/database/readable-standby-instances-in-amazon-rds-multi-az-deployments-a-new-high-availability-option/>

</details>

---

<a id="t12-q04"></a>
### T12-Q04
*⏭ пропущено · домен: офіційна мітка*

The development team at a health-care company is planning to migrate to AWS Cloud from the on-premises data center. The team is evaluating Amazon RDS as the database tier for its flagship application.

Which of the following would you identify as correct for RDS Multi-AZ? (Select two)

- ▫️ For automated backups, I/O activity is suspended on your primary DB since backups are not taken from standby DB
- ▫️ To enhance read scalability, a Multi-AZ standby instance can be used to serve read requests
- ✅ Amazon RDS automatically initiates a failover to the standby, in case primary database fails for any reason
- ✅ RDS applies OS updates by performing maintenance on the standby, then promoting the standby to primary and finally performing maintenance on the old primary, which becomes the new standby
- ▫️ Updates to your DB Instance are asynchronously replicated across the Availability Zone to the standby in order to keep both in sync

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

RDS applies OS updates by performing maintenance on the standby, then promoting the standby to primary, and finally performing maintenance on the old primary, which becomes the new standby

Running a DB instance as a Multi-AZ deployment can further reduce the impact of a maintenance event because Amazon RDS applies operating system updates by following these steps:

Perform maintenance on the standby.

Promote the standby to primary.

Perform maintenance on the old primary, which becomes the new standby.

When you modify the database engine for your DB instance in a Multi-AZ deployment, then Amazon RDS upgrades both the primary and secondary DB instances at the same time. In this case, the database engine for the entire Multi-AZ deployment is shut down during the upgrade.

Amazon RDS automatically initiates a failover to the standby, in case the primary database fails for any reason - You also benefit from enhanced database availability when running your DB instance as a Multi-AZ deployment. If an Availability Zone failure or DB instance failure occurs, your availability impact is limited to the time automatic failover takes to complete.

Another implied benefit of running your DB instance as a Multi-AZ deployment is that DB instance failover is automatic and requires no administration. In an Amazon RDS context, this means you are not required to monitor DB instance events and initiate manual DB instance recovery in the event of an Availability Zone failure or DB instance failure.

Incorrect options:

For automated backups, I/O activity is suspended on your primary DB since backups are not taken from standby DB - The availability benefits of Multi-AZ also extend to planned maintenance. For example, with automated backups, I/O activity is no longer suspended on your primary during your preferred backup window, since backups are taken from the standby.

To enhance read scalability, a Multi-AZ standby instance can be used to serve read requests - A Multi-AZ standby cannot serve read requests. Multi-AZ deployments are designed to provide enhanced database availability and durability, rather than read scaling benefits. As such, the feature uses synchronous replication between primary and standby. AWS implementation makes sure the primary and the standby are constantly in sync, but precludes using the standby for read or write operations.

Updates to your DB Instance are asynchronously replicated across the Availability Zone to the standby in order to keep both in sync - When you create your DB instance to run as a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous “standby” replica in a different Availability Zone. Updates to your DB Instance are synchronously replicated across the Availability Zone to the standby in order to keep both in sync and protect your latest database updates against DB instance failure.

Reference:

https://aws.amazon.com/rds/faqs/

</details>

---
