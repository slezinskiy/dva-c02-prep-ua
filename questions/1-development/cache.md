# ElastiCache / RDS / Aurora

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 21  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q53"></a>
### T01-Q53
*домен: офіційна мітка*

> ⚠️ **Кеш: запис у БД, потім інвалідація кешу.** → [детальніше](../../06-contradictions-and-traps.md#c03)

The development team at an e-commerce company is preparing for the upcoming Thanksgiving sale. The product manager wants the development team to implement appropriate caching strategy on Amazon ElastiCache to withstand traffic spikes on the website during the sale. A key requirement is to facilitate consistent updates to the product prices and product description, so that the cache never goes out of sync with the backend.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Use a caching strategy to update the cache and the backend at the same time
- ✅ Use a caching strategy to write to the backend first and then invalidate the cache
- ▫️ Use a caching strategy to write to the cache directly and sync the backend at a later time
- ▫️ Use a caching strategy to write to the backend first and wait for the cache to expire via TTL

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a caching strategy to write to the backend first and then invalidate the cache»**

Amazon ElastiCache — керовані in-memory сховища (Redis/Memcached-сумісні) для кешування, сесій, ігор, геосервісів, real-time аналітики та черг. Базових стратегій кешування дві: lazy loading і write-through.

Цей варіант схожий на write-through: застосунок спершу пише в backend, а потім інвалідує кеш. Після інвалідації кеш при наступному читанні бере найсвіжіше значення з backend, тож ціни й описи товарів завжди узгоджені з ним.

**❌ Неправильні варіанти:**
- *Update the cache and the backend at the same time* — це дві окремі системи, атомарно оновити їх однією операцією неможливо.
- *Write to the backend first and wait for the cache to expire via TTL* — працює при дуже короткому TTL, але поки TTL не сплив, кеш розходиться з backend.
- *Write to the cache directly and sync the backend later* — дистрактор: дані в кеші мають завжди збігатися з backend, відкладена синхронізація неприпустима.

</details>

---

<a id="t02-q05"></a>
### T02-Q05
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Redis cluster mode: шифрування + HA.** → [детальніше](../../06-contradictions-and-traps.md#c04)

> ⚠️ **Пояснення застаріле: Memcached вже підтримує шифрування in-transit.** → [детальніше](../../07-outdated-and-wrong-answers.md#o08)

An application uses an Amazon RDS database. The company requires that the performance of database reads is improved, and they want to add a caching layer in front of the database. The cached data must be encrypted, and the solution must be highly available.

Which solution will meet these requirements?

- ▫️ Amazon DynamoDB Accelerator (DAX).
- ✅ Amazon ElastiCache for Redis in cluster mode.
- ▫️ Amazon CloudFront with multiple origins.
- ▫️ Amazon ElastiCache for Memcached.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon ElastiCache for Redis in cluster mode»**

ElastiCache — in-memory кеш, який ставлять перед Amazon RDS. Щоб мати і шифрування, і високу доступність, потрібен ElastiCache Redis (з реплікацією; cluster mode enabled).

**❌ Неправильні варіанти:**
- *ElastiCache for Memcached* — у банку: «не підтримує шифрування і HA». Сьогодні Memcached уже підтримує шифрування in-transit, але реплікації та HA в нього досі немає.
- *CloudFront with multiple origins* — RDS не може бути origin CloudFront, та й другого origin тут немає: база одна.
- *DAX* — прискорює лише DynamoDB, перед RDS його не поставиш.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Redis-RedisCluster.html>

</details>

---

<a id="t02-q12"></a>
### T02-Q12
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії: тут ElastiCache (найменша затримка). Інші питання дають DynamoDB — див. правило вибору.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A Developer is building a three-tier web application that must be able to handle a minimum of 10,000 requests per minute. The requirements state that the web tier should be completely stateless while the application maintains session state data for users.

How can the session state data be maintained externally, whilst keeping latency at the LOWEST possible value?

- ✅ Create an Amazon ElastiCache Redis cluster, then implement session handling at the application level to leverage the cluster for session data storage
- ▫️ Create an Amazon RedShift instance, then implement session handling at the application level to leverage a database inside the RedShift database instance for session data storage
- ▫️ Create an Amazon DynamoDB table, then implement session handling at the application level to leverage the table for session data storage
- ▫️ Implement a shared Amazon EFS file system solution across the underlying Amazon EC2 instances, then implement session handling at the application level to leverage the EFS file system for session data storage

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Amazon ElastiCache Redis cluster, then implement session handling at the application level to leverage the cluster for session data storage»**

Для сесій зазвичай використовують key-value сховища. З двох запропонованих (DynamoDB і ElastiCache Redis) найменшу затримку дає ElastiCache, бо це in-memory база.

**❌ Неправильні варіанти:**
- *DynamoDB table* — хороше рішення для сесій, але затримка вища, ніж в ElastiCache.
- *Amazon Redshift* — сховище даних для OLAP, не для сесій.
- *Shared Amazon EFS* — для сесій key-value сховища працюють продуктивніше.

Джерела: <https://aws.amazon.com/caching/session-management/>

</details>

---

<a id="t02-q18"></a>
### T02-Q18
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache. Порівняй з DynamoDB-варіантами.** → [детальніше](../../06-contradictions-and-traps.md#c01)

An e-commerce web application that shares session state on-premises is being migrated to AWS. The application must be fault tolerant, natively highly scalable, and any service interruption should not affect the user experience.

What is the best option to store the session state?

- ▫️ Store the session state in Amazon S3
- ▫️ Store the session state in Amazon CloudFront
- ✅ Store the session state in Amazon ElastiCache
- ▫️ Enable session stickiness using elastic load balancers

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Store the session state in Amazon ElastiCache»**

Сесії можна тримати локально на вузлі, що обробляє запит, або винести в окремий масштабований і надійний рівень. Поширені підходи — sticky sessions або розподілений кеш. Тут підходить розподілений кеш: ElastiCache з рушієм Redis підтримує реплікацію, тож рішення відмовостійке й масштабується «з коробки».

**❌ Неправильні варіанти:**
- *Amazon CloudFront* — кешує вміст для глобальної продуктивності, сесії в ньому не зберігають.
- *Amazon S3* — зберігати й реплікувати сесії можна, але при недоступності бакета сервіс перерветься.
- *Session stickiness on ELB* — прив'язує клієнта до конкретного інстансу. Якщо той впаде, користувача перекинуть на інший, а сесія загубиться.

Джерела: <https://aws.amazon.com/caching/session-management/>

</details>

---

<a id="t02-q33"></a>
### T02-Q33
*домен: визначено автоматично (у джерелі: AWS Database)*

An organization handles data that requires high availability in its relational database. The main headquarters for the organization is in Virginia with smaller offices located in California. The main headquarters uses the data more frequently than the smaller offices. How should the developer configure their databases to meet high availability standards?

- ✅ Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in Virginia.
- ▫️ Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in California.
- ▫️ Create an Athena database with the primary database in Virginia and specify the failover to the Athena replica in another AZ in Virginia.
- ▫️ Create a DynamoDB database with the primary database in Virginia and specify the failover to the DynamoDB replica in another AZ in Virginia.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica in another AZ in Virginia»**

Aurora — реляційна БД з високою доступністю: до 15 реплік у різних AZ, і можна задати, яку репліку підвищувати до primary при збої. Репліка в іншій AZ у Вірджинії (найближче до головного офісу) не шкодить малим офісам, а перенесення primary в Каліфорнію погіршило б роботу головного офісу.

**❌ Неправильні варіанти:**
- *Failover to the Aurora replica in another AZ in California* — додасть затримку головному офісу у Вірджинії.
- *DynamoDB…* — це не реляційна БД.
- *Athena…* — сервіс аналізу даних, а не база даних.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.html>

</details>

---

<a id="t02-q41"></a>
### T02-Q41
*домен: визначено автоматично (у джерелі: AWS Database)*

A Development team is involved with migrating an on-premises MySQL database to Amazon RDS. The database usage is very read-heavy. The Development team wants re-factor the application code to achieve optimum read performance for queries.

How can this objective be met?

- ▫️ Add a connection string to use a read replica on an Amazon EC2 instance
- ✅ Add a connection string to use an Amazon RDS read replica for read queries
- ▫️ Add database retries to the code and vertically scale the Amazon RDS database
- ▫️ Use Amazon RDS with a multi-AZ deployment

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add a connection string to use an Amazon RDS read replica for read queries»**

RDS використовує вбудовану реплікацію MariaDB, MySQL, Oracle і PostgreSQL, щоб створити Read Replica — окремий інстанс, куди зміни з основного копіюються асинхронно. Якщо спрямувати туди читання, навантаження на основний інстанс падає, і читання масштабуються за межі одного інстансу. Основна БД обслуговує читання й запис, репліка — лише читання (наприклад, BI/звіти). У коді треба додати логіку: читання — на endpoint репліки, запис — на основний.

**❌ Неправильні варіанти:**
- *Retries + vertical scaling* — рано чи пізно впрешся в межу вартості або розміру інстансу; читання краще масштабувати горизонтально.
- *Multi-AZ deployment* — standby-копія для відмовостійкості, а не для масштабування продуктивності.
- *Read replica on EC2* — read replicas створюють в RDS, а не на EC2.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html>

</details>

---

<a id="t02-q53"></a>
### T02-Q53
*домен: визначено автоматично (у джерелі: AWS Database)*

A developer is updating an Amazon Aurora MySQL database to allow more clients to connect. What database parameter needs to be updated to support a higher number of client connections?

- ▫️ max_user_connections
- ▫️ max_allowed_packet
- ✅ max_connections
- ▫️ max_join_size

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «max_connections»**

Максимальну кількість з'єднань з інстансом Aurora MySQL визначає параметр `max_connections` у параметр-групі інстансу. Збільшити її можна, перейшовши на клас інстансу з більшою пам'яттю або задавши більше значення `max_connections` (до 16 000).

**❌ Неправильні варіанти:**
- *max_allowed_packet* — максимальний розмір одного пакета чи проміжного рядка.
- *max_join_size* — ліміт на кількість доступів до рядків.
- *max_user_connections* — ліміт одночасних з'єднань для одного користувача.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/rds-mysql-max-connections/>

</details>

---

<a id="t02-q65"></a>
### T02-Q65
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сумнівний ключ: read replicas не масштабують ЗАПИСИ замовлень.** → [детальніше](../../07-outdated-and-wrong-answers.md#d03)

An ecommerce company manages a storefront that uses an Amazon API Gateway API which exposes an AWS Lambda function. The Lambda functions processes orders and stores the orders in an Amazon RDS for MySQL database. The number of transactions increases sporadically during marketing campaigns, and then goes close to zero during quite times.

How can a developer increase the elasticity of the system MOST cost-effectively?

- ▫️ Create an Amazon SQS queue. Publish transactions to the queue and set the queue to invoke the Lambda function. Set the reserved concurrency of the Lambda function to be equal to the max number of database connections.
- ▫️ Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average CPU utilization.
- ✅ Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average connections of Aurora Replicas.
- ▫️ Create an Amazon SNS topic. Publish transactions to the topic configure an SQS queue as a destination. Configure Lambda to process transactions from the queue.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь (за ключем банку): «Migrate from Amazon RDS to Amazon Aurora MySQL. Use an Aurora Auto Scaling policy to scale read replicas based on average connections of Aurora Replicas»**

За банком найефективніше використати Aurora Auto Scaling з target-метрикою «Average connections of Aurora Replicas»: кількість реплік змінюватиметься разом із реальною кількістю з'єднань, яка залежить від завантаженості магазину.

⚠️ **Сумнівно:** замовлення — це записи, а read replicas записи не масштабують. Інженерно краще SQS + Lambda з reserved concurrency. Див. файл 07, розділ d03.

**❌ Неправильні варіанти (за банком):**
- *…based on average CPU utilization* — з кількістю транзакцій краще корелює кількість з'єднань.
- *SNS topic → SQS → Lambda* — SNS тут зайвий, неефективно.
- *SQS → Lambda with reserved concurrency = max DB connections* — банк вважає це дорожчим через «постійну оплату reserved concurrency». Насправді reserved concurrency безкоштовна — це аргумент проти ключа.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html>

</details>

---

<a id="t04-q18"></a>
### T04-Q18
*домен: визначено автоматично (у джерелі: AWS Database)*

An Amazon RDS database is experiencing a high volume of read requests that are slowing down the database. Which fully managed, in-memory AWS database service can assist with offloading reads from the RDS database?

- ✅ Amazon ElastiCache Redis
- ▫️ Memcached on Amazon EC2
- ▫️ Amazon RDS Read Replica
- ▫️ Amazon Aurora Serverless

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon ElastiCache Redis»**

ElastiCache розгортає в хмарі вузли, сумісні з Memcached або Redis. In-memory кеш суттєво зменшує затримку і підвищує пропускну здатність для read-heavy і обчислювально важких навантажень. Це повністю керований сервіс, ідеальний, щоб зняти читання з основної БД.

**❌ Неправильні варіанти:**
- *RDS Read Replica* — розвантажує читання, але це не in-memory сервіс.
- *Aurora Serverless* — не in-memory і не механізм розвантаження читань RDS.
- *Memcached on EC2* — не керований сервіс AWS.

Джерела: <https://aws.amazon.com/elasticache/redis/>

</details>

---

<a id="t04-q30"></a>
### T04-Q30
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache for Redis.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A company is migrating an on-premises web application to AWS. The web application runs on a single server and stores session data in memory. On AWS the company plan to implement multiple Amazon EC2 instances behind an Elastic Load Balancer (ELB). The company want to refactor the application so that data is resilient if an instance fails and user downtime is minimized.

Where should the company move session data to MOST effectively reduce downtime and make users’ session data more fault tolerant?

- ✅ An Amazon ElastiCache for Redis cluster
- ▫️ An Amazon EC2 instance dedicated to session data
- ▫️ The web server’s primary disk
- ▫️ A second Amazon EBS volume

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «An Amazon ElastiCache for Redis cluster»**

ElastiCache — кероване in-memory сховище з низькою затримкою (Memcached або Redis); Redis підтримує Multi-AZ і високу доступність. Перенесення сесій у ElastiCache Redis робить їх доступними навіть при падінні EC2 і скорочує простій для користувачів.

**❌ Неправильні варіанти:**
- *A second EBS volume*, *the web server's primary disk*, *an EC2 instance dedicated to session data* — усе це на EC2, тобто не високодоступно.

Джерела: <https://aws.amazon.com/elasticache/features/>

</details>

---

<a id="t04-q45"></a>
### T04-Q45
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Кеш: lazy loading (кешувати лише запитане).** → [детальніше](../../06-contradictions-and-traps.md#c03)

An Amazon ElastiCache cluster has been placed in front of a large Amazon RDS database. To reduce cost the ElastiCache cluster should only cache items that are actually requested. How should ElastiCache be optimized?

- ✅ Use a lazy loading caching strategy
- ▫️ Only cache database writes
- ▫️ Enable a TTL on cached data
- ▫️ Use a write-through caching strategy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a lazy loading caching strategy»**

Дві стратегії кешування:

**Lazy loading:**
- завантажує дані в кеш лише за потреби (при cache miss);
- не заповнює кеш даними, які ніхто не запитує;
- якщо дані в кеші є — ElastiCache їх повертає; якщо немає або протухли — повертає null, і застосунок бере дані з БД та записує в кеш;
- без додаткових механізмів (TTL) дані можуть застарівати.

**Write-through:**
- кеш оновлюється при кожному записі в БД;
- дані в кеші завжди актуальні;
- запис трохи повільніший;
- без TTL кеш наповнюється даними, які ніхто не читає.

Щоб кешувати лише запитане і зменшити вартість — lazy loading.

**❌ Неправильні варіанти:**
- *Write-through* — завантажить у кеш усі записані дані, дорожче.
- *Only cache database writes* — кешують читання, а не записи.
- *Enable a TTL* — допомагає прибирати застарілі дані, але не робить так, щоб кешувалося лише запитане.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html>

</details>

---

<a id="t05-q48"></a>
### T05-Q48
*домен: визначено автоматично (у джерелі: AWS Database)*

An eCommerce application uses an Amazon RDS database with Amazon ElastiCache in front. Stock volume data is updated dynamically in listings as sales are made. Customers have complained that occasionally the stock volume data is incorrect, and they end up purchasing items that are out of stock. A Developer has checked the front end and indeed some items display the incorrect stock count.

What could be causing this issue?

- ✅ The cache is not being invalidated when the stock volume data is changed.
- ▫️ The stock volume data is being retrieved using a write-through ElastiCache cluster.
- ▫️ The Amazon RDS database is deployed as Multi-AZ and the standby is inconsistent.
- ▫️ The Amazon RDS database has insufficient IOPS provisioned for its EBS volumes.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The cache is not being invalidated when the stock volume data is changed.»**

ElastiCache кешує дані з RDS. У кеші лишилися застарілі дані про залишки, і клієнти бачать їх під час покупки. Рішення — інвалідувати кеш щоразу, коли змінюються залишки. Це робиться на рівні застосунку.

**❌ Неправильні варіанти:**
- *Write-through cluster* — із write-through дані не були б застарілими.
- *Multi-AZ standby inconsistent* — standby в Multi-AZ не обслуговує читання, а реплікація синхронна.
- *Insufficient IOPS* — проблема не в IOPS: застарілі дані надходять з ElastiCache.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Strategies.html>

</details>

---

<a id="t05-q49"></a>
### T05-Q49
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → DynamoDB (ElastiCache відсутній у варіантах).** → [детальніше](../../06-contradictions-and-traps.md#c01)

A Developer is designing a fault-tolerant environment where client sessions will be saved. How can the Developer ensure that no sessions are lost if an Amazon EC2 instance fails?

- ▫️ Use Elastic Load Balancer connection draining to stop sending requests to failing instances
- ▫️ Use Amazon SQS to save session data
- ▫️ Use sticky sessions with an Elastic Load Balancer target group
- ✅ Use Amazon DynamoDB to perform scalable session handling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon DynamoDB to perform scalable session handling»**

DynamoDB Session Handler — обробник сесій для PHP, який зберігає сесії в DynamoDB. Сесії переїжджають з локальної файлової системи у спільне сховище, і зникають проблеми розподіленого застосунку. DynamoDB швидка, масштабована, проста в налаштуванні й сама реплікує дані.

**❌ Неправильні варіанти:**
- *Sticky sessions* — стан сесії лишається на EC2 і пропадає, якщо інстанс відмовить.
- *SQS for session data* — SQS роз'єднує компоненти застосунку, а не зберігає сесії.
- *Connection draining* — не зберігає дані сесій: вони лишаються на несправному інстансі й губляться.

Джерела: <https://docs.aws.amazon.com/aws-sdk-php/v2/guide/feature-dynamodb-session-handler.html>

</details>

---

<a id="t06-q25"></a>
### T06-Q25
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Кеш: write-through (real-time).** → [детальніше](../../06-contradictions-and-traps.md#c03)

A developer is implementing Amazon ElastiCache in an application that needs to reflect real-time data on dashboards. The data is sourced from a database and needs to be stored in the cache.

What would be the ideal caching mechanism for this requirement?

- ✅ A write-through cache
- ▫️ A read-through cache
- ▫️ A write-behind cache
- ▫️ A lazy-loading cache

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A write-through cache»**

Write-through записує дані в кеш при кожному оновленні БД. Для дашбордів у реальному часі це ідеально: cache miss рідкісні, і кожне читання отримує найсвіжіші дані.

**❌ Неправильні варіанти:**
- *Lazy loading* — кешує дані лише після першого запиту, тож перше заповнення дашборда буде із затримкою.
- *Write-behind* — ElastiCache його не підтримує, і він може спричинити розбіжність кешу та БД.
- *Read-through* — ElastiCache його не підтримує.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Strategies.html>

</details>

---

<a id="t06-q43"></a>
### T06-Q43
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Мінус write-through: кеш роздувається.** → [детальніше](../../06-contradictions-and-traps.md#c03)

An application stores data in Amazon RDS and uses Amazon ElastiCache to improve read performance. The developer has configured ElastiCache to update the cache immediately after any writes to the primary database.

What will be the result of this approach to caching?

- ▫️ Load on the RDS database instance will increase because the cache is updated for every database update.
- ✅ The cache will become large and expensive because the infrequently requested data is also written to the cache.
- ▫️ Caching will slow performance of the read queries because the cache is updated when the cache cannot find the requested data.
- ▫️ There is a cache miss penalty because the cache is updated only after a cache miss, resulting in response latency.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The cache will become large and expensive because the infrequently requested data is also written to the cache.»**

Тут ElastiCache працює за стратегією write-through: дані додаються чи оновлюються в кеші при кожному записі в БД. Наслідки:
- дані в кеші ніколи не застарівають;
- є затримка на запис, але немає затримки на читання;
- більшість записаних у кеш даних ніхто не читає, тож кеш стає великим і дорогим.

Розмір кешу допомагає контролювати TTL на записах.

**❌ Неправильні варіанти:**
- *Slow reads because cache is updated on miss* — так працює lazy loading. У write-through є затримка на запис, а не на читання.
- *More RDS load* — у RDS, як і раніше, лише один запис на кожне оновлення.
- *Cache miss penalty* — це теж опис lazy loading.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html>

</details>

---

<a id="t07-q28"></a>
### T07-Q28
*домен: офіційна мітка*

> ⚠️ **Redis: HA/реплікація, бо відновлення кешу дороге.** → [детальніше](../../06-contradictions-and-traps.md#c04)

A company uses Amazon RDS as its database. For improved user experience, it has been decided that a highly reliable fully-managed caching layer has to be configured in front of RDS.

Which of the following is the right choice, keeping in mind that cache content regeneration is a costly activity?

- ▫️ Implement Amazon ElastiCache Memcached
- ✅ Implement Amazon ElastiCache Redis in Cluster Mode
- ▫️ Migrate the database to Amazon Redshift
- ▫️ Install Redis on an Amazon EC2 instance

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement Amazon ElastiCache Redis in Cluster Mode»**

ElastiCache for Redis з увімкненим cluster mode підвищує надійність і доступність з мінімальними змінами у застосунку. Головна перевага — горизонтальне масштабування майже без впливу на продуктивність.

Для продакшну варто мати реплікацію, якщо дані не можна легко відтворити. Cluster mode дозволяє змінювати кількість шардів (горизонтальне масштабування), а не лише тип вузла (вертикальне). Сховище може сягати сотень терабайтів на десятках шардів (до 500 вузлів), тоді як один вузол обмежений пам'яттю свого типу.

Детальніше: https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/

**❌ Неправильні варіанти:**
- *Redis on EC2* — можливо, але це не керований сервіс: обслуговувати його доведеться самим.
- *ElastiCache Memcached* — простий, але не має snapshots, реплікації і транзакцій, які є в Redis.
- *Migrate to Redshift* — Redshift — сховище даних для аналітики, а не in-memory кеш.

Джерела: <https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/> · <https://aws.amazon.com/elasticache/redis-vs-memcached/>

</details>

---

<a id="t07-q48"></a>
### T07-Q48
*домен: офіційна мітка*

The development team at a social media company is considering using Amazon ElastiCache to boost the performance of their existing databases.

As a Developer Associate, which of the following use-cases would you recommend as the BEST fit for ElastiCache? (Select two)

- ✅ Use ElastiCache to improve latency and throughput for read-heavy application workloads
- ✅ Use ElastiCache to improve performance of compute-intensive workloads
- ▫️ Use ElastiCache to improve latency and throughput for write-heavy application workloads
- ▫️ Use ElastiCache to improve performance of Extract-Transform-Load (ETL) workloads
- ▫️ Use ElastiCache to run highly complex JOIN queries

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use ElastiCache to improve latency and throughput for read-heavy application workloads» + «Use ElastiCache to improve performance of compute-intensive workloads»**

ElastiCache — керовані in-memory сховища в AWS. Типові сценарії: кешування, сесії, ігри, геосервіси, аналітика в реальному часі, черги. Часто читані об'єкти зберігаються в кеші, і це суттєво зменшує затримку та збільшує пропускну здатність для:
- навантажень з переважанням читань — соцмережі, ігри, медіа, Q&A-портали;
- обчислювально важких задач, наприклад рекомендаційних систем.

Огляд можливостей: https://aws.amazon.com/elasticache/features/

**❌ Неправильні варіанти:**
- *Write-heavy* — при частих записах кеш дуже швидко застаріває.
- *ETL* — ETL читає й трансформує великі обсяги даних, тут кеш не допоможе; для цього є Glue або EMR.
- *Complex JOIN queries* — складні JOIN виконують у реляційних БД (RDS, Aurora).

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/elasticache-use-cases.html> · <https://aws.amazon.com/elasticache/features/>

</details>

---

<a id="t08-q38"></a>
### T08-Q38
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Redis: максимальний uptime.** → [детальніше](../../06-contradictions-and-traps.md#c04)

A developer needs to implement a caching layer in front of an Amazon RDS database. If the caching layer fails, it is time consuming to repopulate cached data so the solution should be designed for maximum uptime. Which solution is best for this scenario?

- ▫️ Migrate the database to Amazon RedShift
- ▫️ Implement Amazon DynamoDB DAX
- ▫️ Implement Amazon ElastiCache Memcached
- ✅ Implement Amazon ElastiCache Redis

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement Amazon ElastiCache Redis»**

ElastiCache — керовані Redis і Memcached. In-memory кеш суттєво зменшує затримку і збільшує пропускну здатність для навантажень з переважанням читань та обчислювально важких задач. Його часто ставлять перед RDS. Read replicas і автоматичний failover має лише Redis, тож для високої доступності потрібен Redis.

**❌ Неправильні варіанти:**
- *Memcached* — немає реплік і автоматичного failover, тож немає HA.
- *Redshift* — сховище даних для OLAP, а не кеш.
- *DynamoDB DAX* — DAX працює лише перед DynamoDB, а не перед RDS.

Джерела: <https://aws.amazon.com/elasticache/redis-vs-memcached/>

</details>

---

<a id="t10-q53"></a>
### T10-Q53
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A multimedia streaming service wants to migrate its user authentication system to AWS. The system keeps user session data during an active session, which is crucial for seamless user experience. The new system needs to be fault-tolerant, highly scalable natively, and any service disruption must not affect user experience.

What is the best option to store the user session data?

- ▫️ Store the user session data in Amazon S3.
- ▫️ Store the user session data in Amazon CloudFront.
- ▫️ Enable session stickiness using elastic load balancers.
- ✅ Store the user session data in Amazon ElastiCache.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Store the user session data in Amazon ElastiCache.»**

ElastiCache — повністю керований in-memory кеш: швидкий, масштабований і недорогий, без складнощів розподіленого кешу. Через високу швидкість і низьку затримку добре підходить для сесій.

**❌ Неправильні варіанти:**
- *CloudFront* — CDN для доставки контенту, а не сховище стану сесій.
- *S3* — надійне сховище, але не для частих швидких читань і записів сесій.
- *Session stickiness* — механізм маршрутизації, а не сховище; якщо інстанс упаде, сесія зникне.

Джерела: <https://aws.amazon.com/elasticache/>

</details>

---

<a id="t11-q40"></a>
### T11-Q40
*домен: офіційна мітка*

A retail company is migrating its on-premises database to Amazon RDS for PostgreSQL. The company has read-heavy workloads. The development team at the company is looking at refactoring the code to achieve optimum read performance for SQL queries.

Which solution will address this requirement with the least current as well as future development effort?

- ▫️ Set up Amazon RDS in the multi-AZ configuration with a single standby instance. Refactor the application code so that the queries use the standby instance endpoint
- ▫️ Configure Elasticache for Memcached to act as a caching layer for Amazon RDS. Refactor the application code so that the queries use the Elasticache for Memcached endpoint
- ✅ Set up Amazon RDS with one or more read replicas. Refactor the application code so that the queries use the endpoint for the read replicas
- ▫️ Configure Elasticache for Redis to act as a caching layer for Amazon RDS. Refactor the application code so that the queries use the Elasticache for Redis endpoint

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up Amazon RDS with one or more read replicas. Refactor the application code so that the queries use the endpoint for the read replicas»**

RDS використовує вбудовану реплікацію PostgreSQL, щоб створити **read replica** — копію основного інстансу. Зміни копіюються асинхронно. Якщо спрямувати SQL-запити на читання на endpoint репліки, основна БД розвантажується, а для навантажень із переважанням читань можна масштабуватися за межі одного інстансу.

**❌ Неправильні варіанти:**
- *ElastiCache for Redis* — in-memory сховище, а не реляційна БД; SQL-запити в ньому не виконати.
- *ElastiCache for Memcached* — те саме.
- *Multi-AZ standby endpoint* — standby з одним резервним інстансом лише для failover, читати з нього не можна.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/requests-rds-read-replicas/> · <https://aws.amazon.com/rds/features/multi-az/> · <https://aws.amazon.com/blogs/database/readable-standby-instances-in-amazon-rds-multi-az-deployments-a-new-high-availability-option/>

</details>

---

<a id="t12-q04"></a>
### T12-Q04
*домен: офіційна мітка*

The development team at a health-care company is planning to migrate to AWS Cloud from the on-premises data center. The team is evaluating Amazon RDS as the database tier for its flagship application.

Which of the following would you identify as correct for RDS Multi-AZ? (Select two)

- ▫️ For automated backups, I/O activity is suspended on your primary DB since backups are not taken from standby DB
- ▫️ To enhance read scalability, a Multi-AZ standby instance can be used to serve read requests
- ✅ Amazon RDS automatically initiates a failover to the standby, in case primary database fails for any reason
- ✅ RDS applies OS updates by performing maintenance on the standby, then promoting the standby to primary and finally performing maintenance on the old primary, which becomes the new standby
- ▫️ Updates to your DB Instance are asynchronously replicated across the Availability Zone to the standby in order to keep both in sync

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon RDS automatically initiates a failover to the standby, in case primary database fails for any reason» + «RDS applies OS updates by performing maintenance on the standby, then promoting the standby to primary and finally performing maintenance on the old primary, which becomes the new standby»**

**Оновлення ОС у Multi-AZ** зменшують вплив обслуговування:
1. Обслуговують standby.
2. Підвищують standby до primary.
3. Обслуговують старий primary, який стає новим standby.

Але при зміні версії рушія БД оновлюються primary і standby одночасно, і БД на цей час зупиняється.

**Автоматичний failover.** Якщо падає AZ чи інстанс, RDS сам переключається на standby, і простій триває лише стільки, скільки failover. Моніторити й відновлювати вручну не потрібно.

**❌ Неправильні варіанти:**
- *I/O suspended for backups* — у Multi-AZ бекапи знімаються зі standby, тож I/O на primary не зупиняється.
- *Standby serves reads* — standby класичного Multi-AZ не обслуговує читання. ℹ️ Читати зі standby можна в **Multi-AZ DB cluster**, де два readable standby.
- *Asynchronous replication* — реплікація на standby **синхронна**.

</details>

---
