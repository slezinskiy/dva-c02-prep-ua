# ElastiCache / RDS / Aurora

**Домен 4 — Усунення несправностей та оптимізація** · питань: 9  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q05"></a>
### T01-Q05
*⏭ пропущено · домен: офіційна мітка*

A company has recently launched a new gaming application that the users are adopting rapidly. The company uses RDS MySQL as the database. The development team wants an urgent solution to this issue where the rapidly increasing workload might exceed the available database storage.

As a developer associate, which of the following solutions would you recommend so that it requires minimum development effort to address this requirement?

- ▫️ Migrate RDS MySQL database to DynamoDB which automatically allocates storage space when required
- ▫️ Migrate RDS MySQL database to Aurora which offers storage auto-scaling
- ▫️ Create read replica for RDS MySQL
- ✅ Enable storage auto-scaling for RDS MySQL

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable storage auto-scaling for RDS MySQL

If your workload is unpredictable, you can enable storage autoscaling for an Amazon RDS DB instance. With storage autoscaling enabled, when Amazon RDS detects that you are running out of free database space it automatically scales up your storage. Amazon RDS starts a storage modification for an autoscaling-enabled DB instance when these factors apply:

Free available space is less than 10 percent of the allocated storage.

The low-storage condition lasts at least five minutes.

At least six hours have passed since the last storage modification.

The maximum storage threshold is the limit that you set for autoscaling the DB instance. You can't set the maximum storage threshold for autoscaling-enabled instances to a value greater than the maximum allocated storage.

Incorrect options:

Migrate RDS MySQL to Aurora which offers storage auto-scaling - Although Aurora offers automatic storage scaling, this option is ruled out since it involves significant systems administration effort to migrate from RDS MySQL to Aurora. It is much easier to just enable storage auto-scaling for RDS MySQL.

Migrate RDS MySQL database to DynamoDB which automatically allocates storage space when required - This option is ruled out since DynamoDB is a NoSQL database which implies significant development effort to change the application logic to connect and query data from the underlying database. It is much easier to just enable storage auto-scaling for RDS MySQL.

Create read replica for RDS MySQL - Read replicas make it easy to take advantage of supported engines' built-in replication functionality to elastically scale out beyond the capacity constraints of a single DB instance for read-heavy database workloads. You can create multiple read replicas for a given source DB Instance and distribute your application’s read traffic amongst them. This option acts as a distractor as read replicas cannot help to automatically scale storage for the primary database.

Reference:

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIOPS.StorageTypes.html

</details>

---

<a id="t01-q28"></a>
### T01-Q28
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Кеш: write-through.** → [детальніше](../../06-contradictions-and-traps.md#c03)

You are running a web application where users can author blogs and share them with their followers. Most of the workflow is read based, but when a blog is updated, you would like to ensure that the latest data is served to the users (no stale data). The Developer has already suggested using ElastiCache to cope with the read load but has asked you to implement a caching strategy that complies with the requirements of the site.

Which strategy would you recommend?

- ▫️ Use DAX
- ✅ Use a Write Through strategy
- ▫️ Use a Lazy Loading strategy with TTL
- ▫️ Use a Lazy Loading strategy without TTL

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use a Write Through strategy

The write-through strategy adds data or updates data in the cache whenever data is written to the database.

In a Write Through strategy, any new blog or update to the blog will be written to both the database layer and the caching layer, thus ensuring that the latest data is always served from the cache.

 via - https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html

Incorrect options:

Use a Lazy Loading strategy without TTL

Lazy Loading is a caching strategy that loads data into the cache only when necessary. Whenever your application requests data, it first requests the ElastiCache cache. If the data exists in the cache and is current, ElastiCache returns the data to your application. If the data doesn't exist in the cache or has expired, your application requests the data from your data store.

Use a Lazy Loading strategy with TTL

In the case of Lazy Loading, the data is loaded onto the cache whenever the data is missing from the cache. In case the blog gets updated, it won't be updated from the cache unless that cache expires (in case you used a TTL). Time to live (TTL) is an integer value that specifies the number of seconds until the key expires. When an application attempts to read an expired key, it is treated as though the key is not found. The database is queried for the key and the cache is updated. Therefore, for a while, old data will be served to users which is a problem from a requirements perspective as we don't want any stale data.

Use DAX - This is a cache for DynamoDB based implementations, but in this question, we are considering ElastiCache. Therefore this option is not relevant.

Reference:

https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html

</details>

---

<a id="t01-q40"></a>
### T01-Q40
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Кеш: lazy loading + TTL (дозволено застарілі дані).** → [детальніше](../../06-contradictions-and-traps.md#c03)

You are creating a web application in which users can follow each other. Some users will be more popular than others and thus their data will be requested very often. Currently, the user data sits in RDS and it has been recommended by your Developer to use ElastiCache as a caching layer to improve the read performance. The whole dataset of users cannot sit in ElastiCache without incurring tremendous costs and therefore you would like to cache only the most often requested users profiles there. As your website is high traffic, it is accepted to have stale data for users for a while, as long as the stale data is less than a minute old.

What caching strategy do you recommend implementing?

- ▫️ Use a Write Through strategy without TTL
- ▫️ Use a Write Through strategy with TTL
- ✅ Use a Lazy Loading strategy with TTL
- ▫️ Use a Lazy Loading strategy without TTL

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option

Use a Lazy Loading strategy with TTL

Lazy loading is a caching strategy that loads data into the cache only when necessary. Whenever your application requests data, it first requests the ElastiCache cache. If the data exists in the cache and is current, ElastiCache returns the data to your application. If the data doesn't exist in the cache or has expired, your application requests the data from your data store. Your datastore then returns the data to your application.

In this case, data that is actively requested by users will be cached in ElastiCache, and thanks to the TTL, we can expire that data after a minute to limit the data staleness.

 via - https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html#Strategies.LazyLoading

Incorrect option:

Use a Lazy Loading strategy without TTL - This fits the read requirements, but won't help expiring stale data, so we need TTL.

Use a Write Through strategy with TTL

Use a Write Through strategy without TTL

The problem with these two options for the write-through strategy is that we would fill up the cache with unnecessary data and as mentioned in the question we don't have enough space in the cache to fit all the dataset. Therefore we can't use a write-through strategy.

Reference:

https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html#Strategies.LazyLoading

</details>

---

<a id="t02-q62"></a>
### T02-Q62
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Пояснення хибне: Aurora Replicas — асинхронні (лаг < 100 мс).** → [детальніше](../../07-outdated-and-wrong-answers.md#o07)

A review of Amazon CloudWatch metrics shows that there are a high number of reads taking place on a primary database built on Amazon Aurora with MySQL. What can a developer do to improve the read scaling of the database? (Select TWO.)

- ✅ Create Aurora Replicas in same cluster as the primary database instance.
- ✅ Create a separate Aurora MySQL cluster and configure binlog replication.
- ❌ Create a duplicate Aurora database cluster to process read requests.
- ❌ Create a duplicate Aurora primary database to process read requests.
- ▫️ Create Aurora Replicas in a global S3 bucket as the primary read source.

<details><summary>Пояснення (оригінал, EN)</summary>

Aurora Replicas can help improve read scaling because it synchronously updates data with the primary database (within 100 ms). Aurora Replicas are created in the same DB cluster within a Region. With Aurora MySQL you can also enable binlog replication to another Aurora DB cluster which can be in the same or a different Region.

CORRECT: "Create Aurora Replicas in same cluster as the primary database instance" is the correct answer (as explained above.)

CORRECT: "Create a separate Aurora MySQL cluster and configure binlog replication" is also a correct answer (as explained above.)

INCORRECT: "Create a duplicate Aurora database cluster to process read requests" is incorrect. A duplicate Aurora database cluster would be a separate database with read and write capability and would not help with read scaling.

INCORRECT: "Create a duplicate Aurora primary database to process read requests" is incorrect. A duplicate Aurora primary database would be for read and write requests and would not help with read scaling.

INCORRECT: "Creating read replicas of Aurora in a S3 global bucket as the primary read source" is incorrect. S3 is an object storage service. It cannot be used to host databases.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Managing.Performance.html#Aurora.Managing.Performance.ReadScaling>

</details>

---

<a id="t03-q52"></a>
### T03-Q52
*⏭ пропущено · домен: офіційна мітка*

A company wants to add geospatial capabilities to the cache layer, along with query capabilities and an ability to horizontally scale. The company uses Amazon RDS as the database tier.

Which solution is optimal for this use-case?

- ▫️ Use CloudFront caching to cater to demands of increasing workloads
- ▫️ Leverage the capabilities offered by ElastiCache for Redis with cluster mode disabled
- ▫️ Migrate to Amazon DynamoDB to utilize the automatically integrated DynamoDB Accelerator (DAX) along with query capability features
- ✅ Leverage the capabilities offered by ElastiCache for Redis with cluster mode enabled

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Leverage the capabilities offered by ElastiCache for Redis with cluster mode enabled

You can use Amazon ElastiCache to accelerate your high volume application workloads by caching your data in-memory providing sub-millisecond data retrieval performance. When used in conjunction with any database including Amazon RDS or Amazon DynamoDB, ElastiCache can alleviate the pressure associated with heavy request loads, increase overall application performance and reduce costs associated with scaling for throughput on other databases.

Amazon ElastiCache makes it easy to deploy and manage a highly available and scalable in-memory data store in the cloud. Among the open source in-memory engines available for use with ElastiCache is Redis, which added powerful geospatial capabilities in its newer versions.

You can leverage ElastiCache for Redis with cluster mode enabled to enhance reliability and availability with little change to your existing workload. Cluster Mode comes with the primary benefit of horizontal scaling up and down of your Redis cluster, with almost zero impact on the performance of the cluster.

Enabling Cluster Mode provides a number of additional benefits in scaling your cluster. In short, it allows you to scale in or out the number of shards (horizontal scaling) versus scaling up or down the node type (vertical scaling). This means that Cluster Mode can scale to very large amounts of storage (potentially 100s of terabytes) across up to 90 shards, whereas a single node can only store as much data in memory as the instance type has capacity for.

Cluster Mode also allows for more flexibility when designing new workloads with unknown storage requirements or heavy write activity. In a read-heavy workload, one can scale a single shard by adding read replicas, up to five, but a write-heavy workload can benefit from additional write endpoints when cluster mode is enabled.

Geospatial on Amazon ElastiCache for Redis:  via - https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/

Incorrect options:

Leverage the capabilities offered by ElastiCache for Redis with cluster mode disabled - For a production workload, you should consider using a configuration that includes replication to enhance the protection of your data. Also, only vertical scaling is possible when cluster mode is disabled. The use case mentions horizontal scaling as a requirement, hence disabling cluster mode is not an option.

Use CloudFront caching to cater to demands of increasing workloads - One of the purposes of using CloudFront is to reduce the number of requests that your origin server must respond to directly. With CloudFront caching, more objects are served from CloudFront edge locations, which are closer to your users. This reduces the load on your origin server and reduces latency. However, the use case mentions that in-memory caching is needed for enhancing the performance of the application. So, this option is incorrect.

Migrate to Amazon DynamoDB to utilize the automatically integrated DynamoDB Accelerator (DAX) along with query capability features - Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement – from milliseconds to microseconds – even at millions of requests per second. Database migration is a more elaborate effort compared to implementing and optimizing the caching layer.

Джерела: <https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/> · <https://aws.amazon.com/blogs/database/amazon-elasticache-utilizing-redis-geospatial-capabilities/>

</details>

---

<a id="t04-q38"></a>
### T04-Q38
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A retail organization stores stock information in an Amazon RDS database. An application reads and writes data to the database. A Developer has been asked to provide read access to the database from a reporting application in another region.

Which configuration would provide BEST performance for the reporting application without impacting the performance of the main database?

- ▫️ Implement a cross-region multi-AZ deployment in the region where the reporting application will run
- ▫️ Create a snapshot of the database and create a new database from the snapshot in the region where the reporting application will run
- ✅ Implement a cross-region read replica in the region where the reporting application will run
- ▫️ Implement a read replica in another AZ and configure the reporting application to connect to the read replica using a VPN connection

<details><summary>Пояснення (оригінал, EN)</summary>

With Amazon RDS, you can create a MariaDB, MySQL, Oracle, or PostgreSQL read replica in a different AWS Region than the source DB instance. Creating a cross-Region read replica isn't supported for SQL Server on Amazon RDS.

You create a read replica in a different AWS Region to do the following:

• Improve your disaster recovery capabilities.

• Scale read operations into an AWS Region closer to your users.

• Make it easier to migrate from a data center in one AWS Region to a data center in another AWS Region.

Creating a read replica in a different AWS Region from the source instance is similar to creating a replica in the same AWS Region. You can use the AWS Management Console, run the create-db-instance-read-replica command, or call the CreateDBInstanceReadReplica API operation.

Creating read replica in the region where the reporting application is going to run will provide the best performance as latency will be much lower than connecting across regions. As the database is a replica it will also be continuously updated using asynchronous replication so the reporting application will have the latest data available.

CORRECT: "Implement a cross-region read replica in the region where the reporting application will run" is the correct answer.

INCORRECT: "Implement a cross-region multi-AZ deployment in the region where the reporting application will run" is incorrect as multi-AZ is used across availability zones, not regions.

INCORRECT: "Create a snapshot of the database and create a new database from the snapshot in the region where the reporting application will run" is incorrect as this would be OK from a performance perspective but the database would not receive ongoing updates from the main database so the data would quickly become out of date.

INCORRECT: "Implement a read replica in another AZ and configure the reporting application to connect to the read replica using a VPN connection" is incorrect as this would result in much higher latency than having the database in the local region close to the reporting application and would impact performance.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html>

</details>

---

<a id="t05-q32"></a>
### T05-Q32
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Кеш: write-through.** → [детальніше](../../06-contradictions-and-traps.md#c03)

A Developer is creating a database solution using an Amazon ElastiCache caching layer. The solution must provide strong consistency to ensure that updates to product data are consistent between the backend database and the ElastiCache cache. Low latency performance is required for all items in the database.

Which cache writing policy will satisfy these requirements?

- ▫️ Add a short duration TTL value to each write.
- ✅ Use a write-through caching strategy.
- ▫️ Invalidate the cache for each database write.
- ▫️ Use a lazy-loading caching strategy.

<details><summary>Пояснення (оригінал, EN)</summary>

The write-through strategy adds data or updates data in the cache whenever data is written to the database. The advantages of write-through are as follows:

- Data in the cache is never stale. Because the data in the cache is updated every time it's written to the database, the data in the cache is always current.

- Write penalty vs. read penalty.

Every write involves two trips:

  1. A write to the cache

  2. A write to the database

Which adds latency to the process. That said, end users are generally more tolerant of latency when updating data than when retrieving data. There is an inherent sense that updates are more work and thus take longer.

CORRECT: "Use a write-through caching strategy" is the correct answer.

INCORRECT: "Use a lazy-loading caching strategy" is incorrect. Lazy loading is a caching strategy that loads data into the cache only when necessary. This will not ensure strong consistency between the database and the cache.

INCORRECT: "Add a short duration TTL value to each write" is incorrect. A TTL specifies the number of seconds until the key expires. This will not ensure strong consistency between the database and the cache.

INCORRECT: "Invalidate the cache for each database write" is incorrect. This will allow the cache to be updated when an item is next read but will not ensure the best performance for all items in the database.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html>

</details>

---

<a id="t07-q19"></a>
### T07-Q19
*⏭ пропущено · домен: офіційна мітка*

As a Senior Developer, you manage 10 Amazon EC2 instances that make read-heavy database requests to the Amazon RDS for PostgreSQL. You need to make this architecture resilient for disaster recovery.

Which of the following features will help you prepare for database disaster recovery? (Select two)

- ▫️ Use RDS Provisioned IOPS (SSD) Storage in place of General Purpose (SSD) Storage
- ▫️ Use database cloning feature of the RDS DB cluster
- ▫️ Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups across multiple Regions
- ✅ Use cross-Region Read Replicas
- ✅ Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups in a single AWS Region

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use cross-Region Read Replicas

In addition to using Read Replicas to reduce the load on your source DB instance, you can also use Read Replicas to implement a DR solution for your production DB environment. If the source DB instance fails, you can promote your Read Replica to a standalone source server. Read Replicas can also be created in a different Region than the source database. Using a cross-Region Read Replica can help ensure that you get back up and running if you experience a regional availability issue.

Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups in a single AWS Region

Amazon RDS provides high availability and failover support for DB instances using Multi-AZ deployments. Amazon RDS uses several different technologies to provide failover support. Multi-AZ deployments for MariaDB, MySQL, Oracle, and PostgreSQL DB instances use Amazon's failover technology.

The automated backup feature of Amazon RDS enables point-in-time recovery for your database instance. Amazon RDS will backup your database and transaction logs and store both for a user-specified retention period. If it’s a Multi-AZ configuration, backups occur on the standby to reduce I/O impact on the primary. Automated backups are limited to a single AWS Region while manual snapshots and Read Replicas are supported across multiple Regions.

Incorrect options:

Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups across multiple Regions - This is an incorrect statement. Automated backups are limited to a single AWS Region while manual snapshots and Read Replicas are supported across multiple Regions.

Use RDS Provisioned IOPS (SSD) Storage in place of General Purpose (SSD) Storage - Amazon RDS Provisioned IOPS Storage is an SSD-backed storage option designed to deliver fast, predictable, and consistent I/O performance. This storage type enhances the performance of the RDS database, but this isn't a disaster recovery option.

Use database cloning feature of the RDS DB cluster - This option has been added as a distractor. Database cloning is only available for Aurora and not for RDS.

Джерела: <https://aws.amazon.com/rds/features/> · <https://aws.amazon.com/blogs/database/implementing-a-disaster-recovery-strategy-with-amazon-rds/>

</details>

---

<a id="t08-q50"></a>
### T08-Q50
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Memcached для сесій — прийнятно, бо немає вимог HA/персистентності.** → [детальніше](../../06-contradictions-and-traps.md#c04)

An online retail application developer is planning to migrate to AWS to accommodate a future surge in traffic. Currently, a web server, which hosts the web application and manages session state in memory, and a separate server hosting a MySQL database for order details, are used.

During peak traffic, memory usage on the web server reaches its limit, leading to considerable slowdowns. As part of the migration plan, the developer intends to use Amazon EC2 instances with an Auto Scaling group and an Application Load Balancer for the web server.

What other changes can the developer implement to enhance application performance?

- ▫️ Store both the session data and the application data in a MySQL database hosted on an EC2 instance.
- ▫️ Use Amazon ElastiCache for Memcached to store and manage both the session data and the application data.
- ✅ Use Amazon ElastiCache for Memcached to store and manage session data, while utilizing Amazon RDS for MySQL DB instance for application data storage.
- ▫️ Leverage the EC2 instance store for managing the session data and Amazon RDS for MySQL DB instance for application data storage.

<details><summary>Пояснення (оригінал, EN)</summary>

The main performance issue with the current application setup is due to managing additional user sessions, which significantly increases memory usage. To address this, Amazon ElastiCache for Memcached is an ideal service as it is designed to offload the burden of managing session state from web servers, providing fast, in-memory data storage.

For application data, Amazon RDS for MySQL DB instance is a fully managed relational database service, which takes care of database management tasks and provides cost-efficient and resizable capacity.

CORRECT: "Use Amazon ElastiCache for Memcached to store and manage session data, while utilizing Amazon RDS for MySQL DB instance for application data storage" is the correct answer (as explained above.)

INCORRECT: "Store both the session data and the application data in a MySQL database hosted on an EC2 instance" is incorrect.

Storing both session and application data in a MySQL database hosted on an EC2 instance might overload the database server leading to performance issues.

INCORRECT: "Use Amazon ElastiCache for Memcached to store and manage both the session data and the application data" is incorrect.

Amazon ElastiCache for Memcached is not typically used for application data storage but for caching frequently accessed data and offloading the database.

INCORRECT: "Leverage the EC2 instance store for managing the session data and Amazon RDS for MySQL DB instance for application data storage" is incorrect.

EC2 instance store provides temporary block-level storage for instances. This storage is ideal for temporary data, but not for session data, as data is lost if the instance is stopped or fails.

Джерела: <https://aws.amazon.com/elasticache/memcached/> · <https://aws.amazon.com/rds/mysql/>

</details>

---
