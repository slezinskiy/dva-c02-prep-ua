# ElastiCache / RDS / Aurora

**Домен 4 — Усунення несправностей та оптимізація** · питань: 9  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q05"></a>
### T01-Q05
*домен: офіційна мітка*

A company has recently launched a new gaming application that the users are adopting rapidly. The company uses RDS MySQL as the database. The development team wants an urgent solution to this issue where the rapidly increasing workload might exceed the available database storage.

As a developer associate, which of the following solutions would you recommend so that it requires minimum development effort to address this requirement?

- ▫️ Migrate RDS MySQL database to DynamoDB which automatically allocates storage space when required
- ▫️ Migrate RDS MySQL database to Aurora which offers storage auto-scaling
- ▫️ Create read replica for RDS MySQL
- ✅ Enable storage auto-scaling for RDS MySQL

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable storage auto-scaling for RDS MySQL»**

Якщо навантаження непередбачуване, для інстансу Amazon RDS можна ввімкнути storage autoscaling. Тоді RDS сам збільшує сховище, коли вільне місце закінчується. Модифікація починається, якщо одночасно:
- вільного місця менше 10% від виділеного;
- стан низького запасу триває щонайменше 5 хвилин;
- з останньої зміни сховища минуло щонайменше 6 годин.

Maximum storage threshold — межа, до якої дозволено автомасштабування; вона не може перевищувати максимально можливий обсяг сховища.

**❌ Неправильні варіанти:**
- *Migrate RDS MySQL to Aurora* — Aurora теж масштабує сховище автоматично, але міграція потребує значних адміністративних зусиль. Значно простіше ввімкнути storage auto-scaling.
- *Migrate to DynamoDB* — DynamoDB є NoSQL-базою, тож довелося б суттєво переписати логіку застосунку.
- *Create read replica* — read replicas масштабують навантаження на читання, але не збільшують сховище основної БД. Це дистрактор.

</details>

---

<a id="t01-q28"></a>
### T01-Q28
*домен: офіційна мітка*

> ⚠️ **Кеш: write-through.** → [детальніше](../../06-contradictions-and-traps.md#c03)

You are running a web application where users can author blogs and share them with their followers. Most of the workflow is read based, but when a blog is updated, you would like to ensure that the latest data is served to the users (no stale data). The Developer has already suggested using ElastiCache to cope with the read load but has asked you to implement a caching strategy that complies with the requirements of the site.

Which strategy would you recommend?

- ▫️ Use DAX
- ✅ Use a Write Through strategy
- ▫️ Use a Lazy Loading strategy with TTL
- ▫️ Use a Lazy Loading strategy without TTL

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a Write Through strategy»**

Write-through додає або оновлює дані в кеші щоразу, коли вони записуються в базу. Кожен новий блог або оновлення пишеться і в БД, і в кеш, тож користувачі завжди отримують із кешу найсвіжіші дані.

**❌ Неправильні варіанти:**
- *Lazy Loading without TTL* — дані потрапляють у кеш лише за потреби: застосунок спершу питає ElastiCache і йде в БД, тільки якщо даних у кеші немає. Оновлений блог у кеші не оновиться.
- *Lazy Loading with TTL* — оновлені дані з'являться в кеші лише після спливання TTL (кількість секунд до видалення ключа). До того користувачі бачитимуть старі дані, а за умовою застарілих даних бути не повинно.
- *Use DAX* — це кеш для DynamoDB, а в питанні йдеться про ElastiCache.

</details>

---

<a id="t01-q40"></a>
### T01-Q40
*домен: офіційна мітка*

> ⚠️ **Кеш: lazy loading + TTL (дозволено застарілі дані).** → [детальніше](../../06-contradictions-and-traps.md#c03)

You are creating a web application in which users can follow each other. Some users will be more popular than others and thus their data will be requested very often. Currently, the user data sits in RDS and it has been recommended by your Developer to use ElastiCache as a caching layer to improve the read performance. The whole dataset of users cannot sit in ElastiCache without incurring tremendous costs and therefore you would like to cache only the most often requested users profiles there. As your website is high traffic, it is accepted to have stale data for users for a while, as long as the stale data is less than a minute old.

What caching strategy do you recommend implementing?

- ▫️ Use a Write Through strategy without TTL
- ▫️ Use a Write Through strategy with TTL
- ✅ Use a Lazy Loading strategy with TTL
- ▫️ Use a Lazy Loading strategy without TTL

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a Lazy Loading strategy with TTL»**

Lazy loading завантажує дані в кеш лише за потреби: застосунок спершу питає ElastiCache і, якщо даних немає або вони протухли, бере їх зі сховища. Так у кеші лишаються тільки дані, які реально запитують, а TTL робить їх застарілими не довше ніж хвилину.

**❌ Неправильні варіанти:**
- *Lazy Loading without TTL* — вимоги до читання задовольняє, але старі дані не протухатимуть.
- *Write Through with/without TTL* — кеш заповниться непотрібними даними, а за умовою місця на весь набір даних не вистачає.

</details>

---

<a id="t02-q62"></a>
### T02-Q62
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Пояснення хибне: Aurora Replicas — асинхронні (лаг < 100 мс).** → [детальніше](../../07-outdated-and-wrong-answers.md#o07)

A review of Amazon CloudWatch metrics shows that there are a high number of reads taking place on a primary database built on Amazon Aurora with MySQL. What can a developer do to improve the read scaling of the database? (Select TWO.)

- ✅ Create Aurora Replicas in same cluster as the primary database instance.
- ✅ Create a separate Aurora MySQL cluster and configure binlog replication.
- ▫️ Create a duplicate Aurora database cluster to process read requests.
- ▫️ Create a duplicate Aurora primary database to process read requests.
- ▫️ Create Aurora Replicas in a global S3 bucket as the primary read source.

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Create Aurora Replicas in same cluster as the primary database instance» та «Create a separate Aurora MySQL cluster and configure binlog replication»**

Aurora Replicas масштабують читання: вони отримують оновлення з primary з лагом до ~100 мс. Банк каже «синхронно», але насправді реплікація асинхронна. Aurora Replicas створюються в тому ж кластері в межах регіону. Для Aurora MySQL також можна налаштувати binlog-реплікацію в інший кластер Aurora — у тому ж або іншому регіоні.

**❌ Неправильні варіанти:**
- *Duplicate Aurora database cluster* та *Duplicate Aurora primary database* — окрема база з читанням і записом, яка не масштабує читання основної.
- *Aurora read replicas in a global S3 bucket* — S3 — об'єктне сховище, бази в ньому не хостять.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Managing.Performance.html#Aurora.Managing.Performance.ReadScaling>

</details>

---

<a id="t03-q52"></a>
### T03-Q52
*домен: офіційна мітка*

A company wants to add geospatial capabilities to the cache layer, along with query capabilities and an ability to horizontally scale. The company uses Amazon RDS as the database tier.

Which solution is optimal for this use-case?

- ▫️ Use CloudFront caching to cater to demands of increasing workloads
- ▫️ Leverage the capabilities offered by ElastiCache for Redis with cluster mode disabled
- ▫️ Migrate to Amazon DynamoDB to utilize the automatically integrated DynamoDB Accelerator (DAX) along with query capability features
- ✅ Leverage the capabilities offered by ElastiCache for Redis with cluster mode enabled

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Leverage the capabilities offered by ElastiCache for Redis with cluster mode enabled»**

ElastiCache кешує дані в пам'яті з субмілісекундним доступом і разом з RDS чи DynamoDB знімає з них навантаження. Новіші версії Redis мають потужні **geospatial**-можливості. Cluster mode дає горизонтальне масштабування: кількість шардів змінюється майже без впливу на продуктивність, до 90 шардів (сотні TB), тоді як без cluster mode можливе лише вертикальне масштабування (тип вузла). Для читання шард масштабується репліками (до 5), а cluster mode додає ще й кілька endpoint'ів для запису.

**❌ Неправильні варіанти:**
- *Redis with cluster mode disabled* — лише вертикальне масштабування, а потрібне горизонтальне.
- *CloudFront caching* — зменшує запити до origin, але потрібен in-memory кеш для застосунку.
- *Migrate to DynamoDB + DAX* — міграція бази значно складніша, ніж налаштувати кеш.

Джерела: <https://aws.amazon.com/blogs/database/work-with-cluster-mode-on-amazon-elasticache-for-redis/> · <https://aws.amazon.com/blogs/database/amazon-elasticache-utilizing-redis-geospatial-capabilities/>

</details>

---

<a id="t04-q38"></a>
### T04-Q38
*домен: визначено автоматично (у джерелі: AWS Database)*

A retail organization stores stock information in an Amazon RDS database. An application reads and writes data to the database. A Developer has been asked to provide read access to the database from a reporting application in another region.

Which configuration would provide BEST performance for the reporting application without impacting the performance of the main database?

- ▫️ Implement a cross-region multi-AZ deployment in the region where the reporting application will run
- ▫️ Create a snapshot of the database and create a new database from the snapshot in the region where the reporting application will run
- ✅ Implement a cross-region read replica in the region where the reporting application will run
- ▫️ Implement a read replica in another AZ and configure the reporting application to connect to the read replica using a VPN connection

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement a cross-region read replica in the region where the reporting application will run»**

RDS дає змогу створити read replica MariaDB, MySQL, Oracle або PostgreSQL в іншому регіоні (для SQL Server — ні). Навіщо:
- покращити disaster recovery;
- масштабувати читання в регіоні, ближчому до користувачів;
- спростити міграцію між регіонами.

Репліка в регіоні звітного застосунку дає найменшу затримку, а асинхронна реплікація тримає дані актуальними, не навантажуючи основну БД.

**❌ Неправильні варіанти:**
- *Cross-region multi-AZ deployment* — Multi-AZ працює між AZ, а не регіонами.
- *Snapshot → new database in another region* — дані швидко застаріють, бо оновлення не надходять.
- *Read replica in another AZ + VPN* — затримка між регіонами значно більша, ніж до локальної репліки.

Джерела: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html>

</details>

---

<a id="t05-q32"></a>
### T05-Q32
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Кеш: write-through.** → [детальніше](../../06-contradictions-and-traps.md#c03)

A Developer is creating a database solution using an Amazon ElastiCache caching layer. The solution must provide strong consistency to ensure that updates to product data are consistent between the backend database and the ElastiCache cache. Low latency performance is required for all items in the database.

Which cache writing policy will satisfy these requirements?

- ▫️ Add a short duration TTL value to each write.
- ✅ Use a write-through caching strategy.
- ▫️ Invalidate the cache for each database write.
- ▫️ Use a lazy-loading caching strategy.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a write-through caching strategy.»**

Write-through додає чи оновлює дані в кеші при кожному записі в БД. Переваги й ціна:
- Дані в кеші ніколи не застарівають, бо оновлюються з кожним записом.
- Кожен запис — це два звернення: в кеш і в БД. Затримка зростає, але користувачі легше миряться з повільнішим оновленням, ніж із повільним читанням.

**❌ Неправильні варіанти:**
- *Lazy loading* — завантажує дані в кеш лише при потребі; сильної узгодженості кешу і БД не гарантує.
- *Short TTL on each write* — TTL лише задає, через скільки секунд ключ протухне; узгодженості не гарантує.
- *Invalidate cache on each write* — кеш оновиться при наступному читанні, але найкращої продуктивності для всіх елементів не буде.

Джерела: <https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html>

</details>

---

<a id="t07-q19"></a>
### T07-Q19
*домен: офіційна мітка*

As a Senior Developer, you manage 10 Amazon EC2 instances that make read-heavy database requests to the Amazon RDS for PostgreSQL. You need to make this architecture resilient for disaster recovery.

Which of the following features will help you prepare for database disaster recovery? (Select two)

- ▫️ Use RDS Provisioned IOPS (SSD) Storage in place of General Purpose (SSD) Storage
- ▫️ Use database cloning feature of the RDS DB cluster
- ▫️ Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups across multiple Regions
- ✅ Use cross-Region Read Replicas
- ✅ Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups in a single AWS Region

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use cross-Region Read Replicas» + «Enable the automated backup feature of Amazon RDS in a multi-AZ deployment that creates backups in a single AWS Region»**

**Cross-Region Read Replicas.** Read Replica не лише розвантажує основну БД, а й підходить для DR. Якщо основний інстанс впаде, репліку підвищують (promote) до самостійного. Репліка в іншому регіоні рятує навіть при недоступності цілого регіону.

**Automated backups у Multi-AZ.** Multi-AZ дає високу доступність і failover. Автоматичні бекапи дозволяють point-in-time recovery: RDS зберігає БД і transaction logs протягом заданого терміну. У Multi-AZ бекапи знімаються зі standby, щоб менше навантажувати primary. Автоматичні бекапи обмежені одним регіоном, а ручні snapshots і read replicas можуть бути в кількох регіонах.

⚠️ Зараз RDS підтримує **cross-Region automated backup replication**, тож твердження «автоматичні бекапи лише в одному регіоні» застаріло. В межах цього тесту ключ такий, як вище.

**❌ Неправильні варіанти:**
- *Automated backups across multiple Regions* — за логікою тесту автоматичні бекапи обмежені одним регіоном.
- *Provisioned IOPS* — підвищує продуктивність, але не є DR-рішенням.
- *Database cloning* — клонування є лише в Aurora, не в RDS.

Джерела: <https://aws.amazon.com/rds/features/> · <https://aws.amazon.com/blogs/database/implementing-a-disaster-recovery-strategy-with-amazon-rds/>

</details>

---

<a id="t08-q50"></a>
### T08-Q50
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Memcached для сесій — прийнятно, бо немає вимог HA/персистентності.** → [детальніше](../../06-contradictions-and-traps.md#c04)

An online retail application developer is planning to migrate to AWS to accommodate a future surge in traffic. Currently, a web server, which hosts the web application and manages session state in memory, and a separate server hosting a MySQL database for order details, are used.

During peak traffic, memory usage on the web server reaches its limit, leading to considerable slowdowns. As part of the migration plan, the developer intends to use Amazon EC2 instances with an Auto Scaling group and an Application Load Balancer for the web server.

What other changes can the developer implement to enhance application performance?

- ▫️ Store both the session data and the application data in a MySQL database hosted on an EC2 instance.
- ▫️ Use Amazon ElastiCache for Memcached to store and manage both the session data and the application data.
- ✅ Use Amazon ElastiCache for Memcached to store and manage session data, while utilizing Amazon RDS for MySQL DB instance for application data storage.
- ▫️ Leverage the EC2 instance store for managing the session data and Amazon RDS for MySQL DB instance for application data storage.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon ElastiCache for Memcached to store and manage session data, while utilizing Amazon RDS for MySQL DB instance for application data storage.»**

Продуктивність падає, бо зберігання сесій збільшує використання пам'яті веб-серверів. ElastiCache for Memcached знімає з них цю роботу і дає швидке in-memory сховище. Дані застосунку зберігаються в RDS for MySQL — керованій реляційній БД з гнучкою ємністю.

⚠️ Redis кращий для сесій, бо має реплікацію, HA і персистентність. Але його немає серед варіантів, а інші ще гірші. Див. 06 і 07.

**❌ Неправильні варіанти:**
- *MySQL on EC2 for both* — може перевантажити сервер БД.
- *Memcached for both* — Memcached — кеш, а не основне сховище даних застосунку.
- *EC2 instance store for sessions* — дані зникають, якщо інстанс зупиниться або вийде з ладу.

Джерела: <https://aws.amazon.com/elasticache/memcached/> · <https://aws.amazon.com/rds/mysql/>

</details>

---
