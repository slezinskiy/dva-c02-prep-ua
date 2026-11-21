# ElastiCache / RDS / Aurora

**Домен 3 — Розгортання** · питань: 2  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q39"></a>
### T03-Q39
*⏭ пропущено · домен: офіційна мітка*

A development team is considering Amazon ElastiCache for Redis as its in-memory caching solution for its relational database.

Which of the following options are correct while configuring ElastiCache? (Select two)

- ✅ All the nodes in a Redis cluster must reside in the same region
- ▫️ If you have no replicas and a node fails, you experience no loss of data when using Redis with cluster mode enabled
- ▫️ You can scale write capacity for Redis by adding replica nodes
- ▫️ While using Redis with cluster mode enabled, asynchronous replication mechanisms are used to keep the read replicas synchronized with the primary. If cluster mode is disabled, the replication mechanism is done synchronously
- ✅ While using Redis with cluster mode enabled, you cannot manually promote any of the replica nodes to primary

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

All the nodes in a Redis cluster must reside in the same region

All the nodes in a Redis cluster (cluster mode enabled or cluster mode disabled) must reside in the same region.

While using Redis with cluster mode enabled, you cannot manually promote any of the replica nodes to primary

While using Redis with cluster mode enabled, there are some limitations:

You cannot manually promote any of the replica nodes to primary.

Multi-AZ is required.

You can only change the structure of a cluster, the node type, and the number of nodes by restoring from a backup.

Incorrect options:

While using Redis with cluster mode enabled, asynchronous replication mechanisms are used to keep the read replicas synchronized with the primary. If cluster mode is disabled, the replication mechanism is done synchronously - When you add a read replica to a cluster, all of the data from the primary is copied to the new node. From that point on, whenever data is written to the primary, the changes are asynchronously propagated to all the read replicas, for both the Redis offerings (cluster mode enabled or cluster mode disabled).

If you have no replicas and a node fails, you experience no loss of data when using Redis with cluster mode enabled - If you have no replicas and a node fails, you experience loss of all data in that node's shard, when using Redis with cluster mode enabled. If you have no replicas and the node fails, you experience total data loss in Redis with cluster mode disabled.

You can scale write capacity for Redis by adding replica nodes - This increases only the read capacity of the Redis cluster, write capacity is not enhanced by read replicas.

Reference:

https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Redis.Groups.html

</details>

---

<a id="t11-q64"></a>
### T11-Q64
*⏭ пропущено · домен: офіційна мітка*

You have created a Java application that uses RDS for its main data storage and ElastiCache for user session storage. The application needs to be deployed using Elastic Beanstalk and every new deployment should allow the application servers to reuse the RDS database. On the other hand, user session data stored in ElastiCache can be re-created for every deployment.

Which of the following configurations will allow you to achieve this? (Select two)

- ✅ RDS database defined externally and referenced through environment variables
- ✅ ElastiCache defined in .ebextensions/
- ▫️ RDS database defined in .ebextensions/
- ▫️ ElastiCache database defined externally and referenced through environment variables
- ▫️ ElastiCache bundled with the application source code

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

ElastiCache defined in .ebextensions/ - Any resources created as part of your .ebextensions is part of your Elastic Beanstalk template and will get deleted if the environment is terminated.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html

RDS database defined externally and referenced through environment variables - To decouple your database instance from your environment, you can run a database instance in Amazon RDS and configure your application to connect to it on launch. This enables you to connect multiple environments to a database, terminate an environment without affecting the database, and perform seamless updates with blue-green deployments. To allow the Amazon EC2 instances in your environment to connect to an outside database, you can configure the environment's Auto Scaling group with an additional security group.

Using Elastic Beanstalk with Amazon RDS:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.RDS.html

Incorrect options:

ElastiCache bundled with the application source code - ElastiCache is an AWS service and cannot be bundled with the source code.

RDS database defined in .ebextensions/ - The lifetime of the RDS instance gets tied to the lifetime of the Elastic Beanstalk environment, so this option is incorrect.

ElastiCache database defined externally and referenced through environment variables - For the given use-case, the client is fine with losing user session data and hence defining it in .ebextensions/ is more appropriate.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.RDS.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-environment-resources-elasticache.html>

</details>

---
