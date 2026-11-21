# Amazon DynamoDB

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 46  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#dynamodb) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q20"></a>
### T01-Q20
*домен: офіційна мітка*

Which of the following CLI options will allow you to retrieve a subset of the attributes coming from a DynamoDB scan?

- ▫️ --max-items
- ✅ --projection-expression
- ▫️ --page-size
- ▫️ --filter-expression

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «--projection-expression»**

Projection expression — рядок з переліком потрібних атрибутів (кілька імен через кому). GetItem, Query та Scan за замовчуванням повертають усі атрибути item'а, а projection expression обмежує їх лише потрібними.

**❌ Неправильні варіанти:**
- *--filter-expression* — додатково фільтрує items у результатах. Фільтр застосовується після читання, тому споживання read capacity не зменшується. Та й обмежує він items, а не атрибути.
- *--page-size* — CLI запитує менше items за один виклик, але все одно отримує весь список через більшу кількість викликів.
- *--max-items* — обмежує кількість items, які CLI виводить за раз, а пагінацію з сервісом обробляє так само.

</details>

---

<a id="t01-q22"></a>
### T01-Q22
*домен: офіційна мітка*

You have created a DynamoDB table to support your application and provisioned RCU and WCU to it so that your application has been running for over a year now without any throttling issues. Your application now requires a second type of query over your table and as such, you have decided to create an LSI and a GSI on a new table to support that use case. One month after having implemented such indexes, it seems your table is experiencing throttling.

Upon looking at the table's metrics, it seems the RCU and WCU provisioned are still sufficient. What's happening?

- ▫️ Metrics are lagging in your CloudWatch dashboard and you should see the RCU and WCU peaking for the main table in a few minutes
- ✅ The GSI is throttling so you need to provision more RCU and WCU to the GSI
- ▫️ The LSI is throttling so you need to provision more RCU and WCU to the LSI
- ▫️ Adding both an LSI and a GSI to a table is not recommended by AWS best practices as this is a known cause for creating throttles

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The GSI is throttling so you need to provision more RCU and WCU to the GSI»**

DynamoDB має два типи вторинних індексів:
- **Global secondary index (GSI)** — partition key і sort key можуть відрізнятися від ключів таблиці. Запити охоплюють усі дані таблиці в усіх партиціях. GSI зберігається у власному просторі партицій і масштабується окремо від таблиці.
- **Local secondary index (LSI)** — той самий partition key, що в таблиці, але інший sort key. Кожна партиція LSI прив'язана до партиції таблиці з тим самим значенням partition key.

Якщо на таблицю йде інтенсивний запис, а GSI має недостатню write capacity, запис у таблицю теж throttle'иться. Тому write capacity GSI має бути не меншою за write capacity таблиці: кожен запис оновлює і таблицю, і GSI.

**❌ Неправильні варіанти:**
- *The LSI is throttling…* — LSI використовує RCU/WCU основної таблиці, окремо провізіонувати їх для LSI неможливо.
- *Adding both an LSI and a GSI is not recommended* — дистрактор: мати LSI і GSI разом цілком нормально.
- *Metrics are lagging…* — теоретично можливо, але застосунок місяцями працював нормально, тож винен саме новий GSI.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html#GSI.ThroughputConsiderations>

</details>

---

<a id="t01-q50"></a>
### T01-Q50
*домен: офіційна мітка*

A popular mobile app retrieves data from an AWS DynamoDB table that was provisioned with read-capacity units (RCU’s) that are evenly shared across four partitions. One of those partitions is receiving more traffic than the other partitions, causing hot partition issues.

What technology will allow you to reduce the read traffic on your AWS DynamoDB table with minimal effort?

- ▫️ ElastiCache
- ▫️ DynamoDB Streams
- ▫️ More partitions
- ✅ DynamoDB DAX

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «DynamoDB DAX»**

DynamoDB Accelerator (DAX) — повністю керований, високодоступний in-memory кеш для DynamoDB, що прискорює читання до 10 разів (з мілісекунд до мікросекунд) навіть при мільйонах запитів на секунду. Підключається з мінімальними змінами коду (сумісний з API DynamoDB).

**❌ Неправильні варіанти:**
- *DynamoDB Streams* — записи про зміни items у таблиці; читання не зменшує.
- *ElastiCache* — кешувати можна що завгодно, але доведеться змінювати код, щоб він спершу перевіряв кеш. За умовою зусилля мають бути мінімальними.
- *More partitions* — DynamoDB керує партиціями сама; дистрактор.

</details>

---

<a id="t01-q56"></a>
### T01-Q56
*домен: офіційна мітка*

A financial services company has developed a REST API which is deployed in an Auto Scaling Group behind an Application Load Balancer. The API stores the data payload in DynamoDB and the static content is served through S3. Upon analyzing the usage pattern, it's found that 80% of the read requests are shared across all users.

As a Developer Associate, how can you improve the application performance while optimizing the cost with the least development effort?

- ▫️ Enable ElastiCache Redis for DynamoDB and ElastiCache Memcached for S3
- ▫️ Enable DAX for DynamoDB and ElastiCache Memcached for S3
- ✅ Enable DynamoDB Accelerator (DAX) for DynamoDB and CloudFront for S3
- ▫️ Enable ElastiCache Redis for DynamoDB and CloudFront for S3

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable DynamoDB Accelerator (DAX) for DynamoDB and CloudFront for S3»**

DAX — керований високодоступний in-memory кеш для DynamoDB, що прискорює читання до 10 разів (з мілісекунд до мікросекунд). Він сумісний з API DynamoDB: досить створити кластер DAX і направити на нього наявні виклики через DAX-клієнт SDK, функціональних змін у коді не треба.

CloudFront — CDN для статичного та динамічного вмісту, відео й API. Запит потрапляє на найближчу edge-локацію; якщо файл у кеші, CloudFront віддає його з мінімальною затримкою, якщо ні — бере з origin (наприклад, S3). Віддача через CloudFront часто ще й дешевша, ніж напряму з S3.

**❌ Неправильні варіанти:**
- *ElastiCache Redis for DynamoDB and CloudFront for S3* — Redis з DynamoDB поєднати можна, але це значно більше розробки, ніж DAX.
- *DAX for DynamoDB and ElastiCache Memcached for S3* і *ElastiCache Redis for DynamoDB and ElastiCache Memcached for S3* — ElastiCache не використовують для роздачі статичного вмісту з S3.

Джерела: <https://aws.amazon.com/dynamodb/dax/> · <https://aws.amazon.com/blogs/networking-and-content-delivery/amazon-s3-amazon-cloudfront-a-match-made-in-the-cloud/> · <https://aws.amazon.com/elasticache/redis/>

</details>

---

<a id="t01-q57"></a>
### T01-Q57
*домен: офіційна мітка*

You are implementing a banking application in which you need to update the Exchanges DynamoDB table and the AccountBalance DynamoDB table at the same time or not at all.

Which DynamoDB feature should you use?

- ▫️ DynamoDB Streams
- ✅ DynamoDB Transactions
- ▫️ DynamoDB TTL
- ▫️ DynamoDB Indexes

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «DynamoDB Transactions»**

Транзакції DynamoDB дають скоординовані all-or-nothing зміни кількох items у межах однієї або кількох таблиць. Вони забезпечують ACID (атомарність, узгодженість, ізоляцію, довговічність).

**❌ Неправильні варіанти:**
- *DynamoDB TTL* — автоматичне видалення даних за часовою міткою.
- *DynamoDB Streams* — журнал змін таблиці, який можна передати Lambda для обробки.
- *DynamoDB Indexes* — GSI/LSI для запитів за іншими ключами.

</details>

---

<a id="t02-q09"></a>
### T02-Q09
*домен: визначено автоматично (у джерелі: AWS Database)*

A developer is setting up the primary key for an Amazon DynamoDB table that logs a company's purchases from various suppliers. Each transaction is recorded with these attributes: supplierId, transactionTime, item, and unitCost.

Which primary key configuration will be valid in this case?

- ▫️ A composite primary key with supplierId as the partition key and unitCost as the sort key.
- ▫️ A simple primary key with supplierId serving as the partition key.
- ✅ A composite primary key with supplierId as the partition key and transactionTime as the sort key.
- ▫️ A composite primary key with supplierId as the partition key and item as the sort key.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A composite primary key with supplierId as the partition key and transactionTime as the sort key»**

Комбінація supplierId + transactionTime забезпечує унікальність запису: малоймовірно, що той самий постачальник зробить дві поставки в ту саму мілісекунду.

**❌ Неправильні варіанти:**
- *Simple primary key with supplierId* — в одного постачальника багато закупівель, тож унікальності не буде.
- *supplierId + item* — постачальник може кілька разів поставити той самий товар.
- *supplierId + unitCost* — у постачальника можуть бути різні товари з однаковою ціною.

Джерела: <https://repost.aws/knowledge-center/dynamodb-create-composite-key>

</details>

---

<a id="t02-q14"></a>
### T02-Q14
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: "найшвидше, не шкодячи" → Parallel Scan + rate limit.** → [детальніше](../../06-contradictions-and-traps.md#c02)

An application uses an Amazon DynamoDB table that is 50 GB in size and provisioned with 10,000 read capacity units (RCUs) per second. The table must be scanned during non-peak hours when normal traffic consumes around 5,000 RCUs. The Developer must scan the whole table in the shortest possible time whilst ensuring the normal workload is not affected.

How would the Developer optimize this scan cost-effectively?

- ▫️ Use sequential scans and apply a FilterExpression.
- ✅ Use the Parallel Scan API operation and limit the rate.
- ▫️ Increase read capacity units during the scan operation.
- ▫️ Use sequential scans and set the ConsistentRead parameter to false.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the Parallel Scan API operation and limit the rate»**

Щоб найкраще використати провізіоновану пропускну здатність, scan розподіляють по партиціях таблиці через Parallel Scan — так він завершиться найшвидше. А rate limiting не дасть scan'у зачепити звичайне навантаження.

**❌ Неправильні варіанти:**
- *Sequential scans with a FilterExpression* — фільтр застосовується після читання, тож scan не пришвидшує і не зменшує навантаження.
- *Increase RCUs during the scan* — у непікові години RCU вже з запасом; економніше використати наявні.
- *Sequential scans with ConsistentRead=false* — eventually consistent читання не вирішує ні швидкість, ні вплив на навантаження.

Джерела: <https://aws.amazon.com/blogs/Developer/rate-limited-scans-in-amazon-dynamodb/>

</details>

---

<a id="t02-q27"></a>
### T02-Q27
*домен: визначено автоматично (у джерелі: AWS Database)*

A developer is responsible for a business critical application that uses Amazon DynamoDB as its main data repository. This DynamoDB table holds millions of records and handles high volumes of requests. The developer must implement near-real time processing on the records as soon as they are inserted or modified in the DynamoDB table.

What's the most efficient way to introduce this capability with MINIMUM modification to the existing application code?

- ▫️ Use Amazon SQS to queue incoming data and process it using Amazon EC2 instances.
- ▫️ Modify the application code to add processing logic after each DynamoDB write operation.
- ✅ Use AWS Lambda triggered by DynamoDB Streams to process the documents.
- ▫️ Set up an Amazon Kinesis Data Stream to process updates from the DynamoDB table.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Lambda triggered by DynamoDB Streams to process the documents»**

DynamoDB Streams можуть тригерити Lambda й автоматично обробляти зміни таблиці майже в реальному часі без суттєвих змін коду застосунку.

**❌ Неправильні варіанти:**
- *SQS + EC2* — довелося б суттєво змінити код для інтеграції черги, і near-real-time не гарантовано.
- *Modify the application code to add processing logic after each write* — великі зміни коду, до того ж запис може сповільнитися.
- *Kinesis Data Stream* — може захоплювати зміни, але потрібні consumers для обробки. Простіше — DynamoDB Streams + Lambda.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html>

</details>

---

<a id="t02-q35"></a>
### T02-Q35
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: "мінімізувати вплив на throughput" → менший page size (Limit).** → [детальніше](../../06-contradictions-and-traps.md#c02)

A company has a large Amazon DynamoDB table which they scan periodically so they can analyze several attributes. The scans are consuming a lot of provisioned throughput. What technique can a Developer use to minimize the impact of the scan on the table's provisioned throughput?

- ▫️ Prewarm the table by updating all items
- ✅ Set a smaller page size for the scan
- ▫️ Use parallel scans
- ▫️ Define a range key on the table

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set a smaller page size for the scan»**

Scan менш ефективний за інші операції: він завжди читає всю таблицю чи індекс і лише потім відфільтровує. На великій таблиці один Scan може спожити всю провізіоновану пропускну здатність, створивши раптовий пік і заважаючи іншим запитам. Щоб зменшити вплив:
- **Зменшити page size.** Scan читає сторінку цілком (за замовчуванням 1 MB). Параметр `Limit` задає менший розмір сторінки: кожен запит читає менше і між запитами з'являється «пауза».
- **Ізолювати scan'и.** Сканувати таблицю, що не обслуговує критичний трафік, наприклад «тіньову» копію.

**❌ Неправильні варіанти:**
- *Use parallel scans* — швидше, але ще більше навантажує throughput.
- *Define a range key* — мало допомагає, бо scan аналізує кілька атрибутів.
- *Prewarm the table by updating all items* — дорого по throughput і нічого не дає.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-query-scan.html>

</details>

---

<a id="t02-q59"></a>
### T02-Q59
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Пояснення застаріле: TransactWriteItems — до 100 дій, не 25.** → [детальніше](../../07-outdated-and-wrong-answers.md#o09)

A Developer is creating a serverless application that uses an Amazon DynamoDB table. The application must make idempotent, all-or-nothing operations for multiple groups of write actions.

Which solution will meet these requirements?

- ✅ Update the items in the table using the TransactWriteltems operation to group the changes.
- ▫️ Update the items in the table using the BatchWriteltem operation and configure idempotency at the table level.
- ▫️ Enable DynamoDB streams and capture new images. Update the items in the table using the BatchWriteltem.
- ▫️ Create an Amazon SQS FIFO queue and use the SendMessageBatch operation to group the changes.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Update the items in the table using the TransactWriteItems operation to group the changes»**

`TransactWriteItems` — синхронна й ідемпотентна операція запису, що групує кілька дій в одну all-or-nothing. У банку вказано до 25 дій; **зараз ліміт — 100 дій**. Дії можуть стосуватися різних таблиць того ж акаунта й регіону, загальний розмір — до 4 MB, і або виконуються всі, або жодна. На відміну від `BatchWriteItem`, де частина дій може пройти, а частина — ні.

**❌ Неправильні варіанти:**
- *BatchWriteItem … idempotency at the table level* — BatchWriteItem не атомарний, а «ідемпотентності на рівні таблиці» немає.
- *DynamoDB streams + BatchWriteItem* — Streams не роблять запис ідемпотентним.
- *SQS FIFO + SendMessageBatch* — SQS тут не допомагає; дані мають записуватися в DynamoDB.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_TransactWriteItems.html>

</details>

---

<a id="t02-q60"></a>
### T02-Q60
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: Parallel + Limit.** → [детальніше](../../06-contradictions-and-traps.md#c02)

A Developer needs to scan a full DynamoDB 50GB table within non-peak hours. About half of the strongly consistent RCUs are typically used during non-peak hours and the scan duration must be minimized.

How can the Developer optimize the scan execution time without impacting production workloads?

- ▫️ Use sequential scans
- ✅ Use parallel scans while limiting the rate
- ▫️ Increase the RCUs during the scan operation
- ▫️ Change to eventually consistent RCUs during the scan operation

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use parallel scans while limiting the rate»**

Scan споживає багато RCU: він читає всю таблицю або індекс і лише потім фільтрує. Щоб не зачепити продакшн і водночас мінімізувати час:
1. **Параметр `Limit`** зменшує розмір сторінки: кожен запит читає менше, і між запитами з'являється «пауза».
2. **Parallel scan** розподіляє scan по партиціях і максимально використовує наявну пропускну здатність.

Parallel scan доречний, якщо таблиця від 20 GB, провізіонована пропускна здатність читання не використана повністю, а послідовний Scan надто повільний. Тож потрібен parallel scan з обмеженням швидкості.

**❌ Неправильні варіанти:**
- *Use sequential scans* — повільніше за паралельні.
- *Increase the RCUs* — у непікові години половина RCU вільна; збільшувати дорожче, ніж використати наявні.
- *Change to eventually consistent reads* — не захищає продакшн від впливу scan'а.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-query-scan.html#QueryAndScanGuidelines.ParallelScan>

</details>

---

<a id="t03-q07"></a>
### T03-Q07
*домен: офіційна мітка*

You are a system administrator whose company recently moved its production application to AWS and migrated data from MySQL to AWS DynamoDB. You are adding new tables to AWS DynamoDB and need to allow your application to query your data by the primary key and an alternate key. This option must be added when first creating tables otherwise changes cannot be made afterward.

Which of the following actions should you take?

- ✅ Create a LSI
- ▫️ Call Scan
- ▫️ Create a GSI
- ▫️ Migrate away from DynamoDB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an LSI»**

LSI (Local Secondary Index) дає альтернативний sort key при тому ж partition key, що й у таблиці. Його використовують, коли запитувати дані лише за первинним ключем таблиці недостатньо. LSI створюється **лише під час створення таблиці**, змінити потім не можна — саме це сказано в умові.

**❌ Неправильні варіанти:**
- *Call Scan* — це операція читання, а не індекс.
- *Create a GSI* — GSI має довільні partition і sort key і його можна додати будь-коли; умова «тільки при створенні + той самий partition key» вказує на LSI.
- *Migrate away from DynamoDB* — міграція на не-NoSQL базу вимагатиме суттєвих змін коду.

</details>

---

<a id="t03-q23"></a>
### T03-Q23
*домен: офіційна мітка*

Your mobile application needs to perform API calls to DynamoDB. You do not want to store AWS secret and access keys onto the mobile devices and need all the calls to DynamoDB made with a different identity per mobile device.

Which of the following services allows you to achieve this?

- ▫️ IAM
- ▫️ Cognito User Pools
- ✅ Cognito Identity Pools
- ▫️ Cognito Sync

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Cognito Identity Pools»**

Identity pools видають тимчасові облікові дані AWS гостям (неавтентифікованим) і автентифікованим користувачам, які отримали токен. Так мобільні пристрої звертаються до DynamoDB без збережених ключів, і кожен пристрій має окрему ідентичність.

**❌ Неправильні варіанти:**
- *Cognito User Pools* — додають у застосунок sign-up і sign-in, але не авторизують доступ до сервісів AWS.
- *Cognito Sync* — синхронізує дані профілю між пристроями.
- *IAM* — довелося б заводити IAM-користувача на кожен пристрій — некеровано і погана практика.

</details>

---

<a id="t03-q50"></a>
### T03-Q50
*домен: офіційна мітка*

As a Full-stack Web Developer, you are involved with every aspect of a company’s platform from development with PHP and JavaScript to the configuration of NoSQL databases with Amazon DynamoDB. You are not concerned about your response receiving stale data from your database and need to perform 16 eventually consistent reads per second of 12 KB in size each.

How many read capacity units (RCUs) do you need?

- ▫️ 12
- ▫️ 192
- ✅ 24
- ▫️ 48

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «24»**

1 RCU = 2 eventually consistent читання на секунду для item'а до 4 KB. Item 12 KB = 3 × 4 KB, тобто 3 RCU на два eventually consistent читання. 16 читань: 3 × (16 / 2) = **24 RCU**.

**❌ Неправильні варіанти:** *12*, *192*, *48* — не відповідають розрахунку.

</details>

---

<a id="t04-q01"></a>
### T04-Q01
*домен: визначено автоматично (у джерелі: AWS Database)*

A company is designing a new application that will store thousands of terabytes of data. They need a fully managed NoSQL data store that provides low-latency and can store key-value pairs. Which type of database should they use?

- ▫️ Amazon RDS
- ▫️ Amazon S3
- ▫️ Amazon ElastiCache
- ✅ Amazon DynamoDB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon DynamoDB»**

DynamoDB — повністю керована NoSQL-база: таблиці зберігають будь-який обсяг даних і обслуговують будь-який трафік, а пропускну здатність можна змінювати без простою. Це key-value база: дані — пари ключ-значення, де ключ — унікальний ідентифікатор, а значення може бути будь-яким, від простого до складного об'єкта.

**❌ Неправильні варіанти:**
- *Amazon RDS* — реляційна (SQL) база.
- *Amazon ElastiCache* — in-memory кеш, а не довговічна NoSQL-база для тисяч терабайт.
- *Amazon S3* — об'єктне сховище, а не база даних.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html> · <https://aws.amazon.com/nosql/key-value/>

</details>

---

<a id="t04-q08"></a>
### T04-Q08
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Очищення всієї таблиці → delete table.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A nightly batch job loads 1 million new records in to a DynamoDB table. The records are only needed for one hour, and the table needs to be empty by the next night’s batch job.

Which is the MOST efficient and cost-effective method to provide an empty table?

- ▫️ Use BatchWriteItem to empty all of the rows
- ▫️ Write a recursive function that scans and calls out DeleteItem
- ▫️ Use DeleteItem using a ConditionExpression
- ✅ Create and then delete the table after the task has completed

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create and then delete the table after the task has completed»**

Потрібно ефективно і дешево, тобто з мінімумом API-викликів. Таблиця потрібна ненадовго, тож найпростіше створювати її (`CreateTable`) і видаляти разом з усіма items (`DeleteTable`). Мало викликів, і таблиця не споживає RCU/WCU, поки не використовується.

**❌ Неправильні варіанти:**
- *DeleteItem with a ConditionExpression* — багато RCU/WCU, дорого.
- *BatchWriteItem to empty all rows* — теж споживає багато WCU.
- *Recursive function that scans and calls DeleteItem* — scan найменш ефективний: читає всі items.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations.html>

</details>

---

<a id="t04-q12"></a>
### T04-Q12
*домен: визначено автоматично (у джерелі: AWS Database)*

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

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a global secondary index with a partition key of sport_name and a sort key of score, and get the results»**

Треба сортувати результати за кожним видом спорту і брати найкращих. Для цього потрібні і інший partition key, і інший sort key, тож лише **GSI** (LSI змінює тільки sort key): partition key — `sport_name`, sort key — `score`. Запит до GSI одразу поверне результати, впорядковані за рахунком у межах виду спорту.

**❌ Неправильні варіанти:**
- *LSI with sport_name and score* — LSI не можна створити після таблиці, і він не змінює partition key.
- *Query with user_id and sport_name, order by score* — менш ефективно, ніж GSI.
- *Scan* — найменш ефективно: читає всі items, споживає найбільше RCU.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t04-q37"></a>
### T04-Q37
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is creating a social networking app for games that uses a single Amazon DynamoDB table. All users’ saved game data is stored in the single table, but users should not be able to view each other’s data.

How can the Developer restrict user access so they can only view their own data?

- ▫️ Read records from DynamoDB and discard irrelevant data client-side
- ✅ Restrict access to specific items based on certain primary key values
- ▫️ Use an identity-based policy that restricts read access to the table to specific principals
- ▫️ Use separate access keys for each user to call the API and restrict access to specific items based on access key ID

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Restrict access to specific items based on certain primary key values»**

В IAM-політиках для DynamoDB можна задавати умови (`Condition`), наприклад:
- read-only доступ лише до певних items і атрибутів таблиці чи індексу;
- write-only доступ до певних атрибутів залежно від ідентичності користувача.

Це fine-grained access control: можна дати права на таблицю, але обмежити доступ items з певними значеннями первинного ключа (`dynamodb:LeadingKeys`, наприклад ID користувача).

**❌ Неправильні варіанти:**
- *Restrict access based on access key ID* — так обмежувати не можна.
- *Identity-based policy that restricts read access to the table to specific principals* — обмежить доступ до всієї таблиці, а не до окремих items.
- *Read records and discard irrelevant data client-side* — неефективно (більше RCU) і небезпечно (витік даних).

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t04-q42"></a>
### T04-Q42
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is creating an application that will utilize an Amazon DynamoDB table for storing session data. The data being stored is expected to be around 4.5KB in size and the application will make 20 eventually consistent reads/sec, and 12 standard writes/sec.

How many RCUs/WCUs are required?

- ▫️ 40 RCU and 60 WCU
- ▫️ 40 RCU and 12 WCU
- ▫️ 10 RCU and 24 WCU
- ✅ 20 RCU and 60 WCU

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «20 RCU and 60 WCU»**

У режимі provisioned ти задаєш кількість читань і записів на секунду.

**RCU:** кожен виклик читання — read request (strongly consistent, eventually consistent або transactional). Для item'а до 4 KB 1 RCU = 1 strongly consistent або 2 eventually consistent читання на секунду; transactional — 2 RCU на читання. Більші items потребують більше RCU. Наприклад, для 8 KB: strong — 2 RCU, eventual — 1 RCU, transactional — 4 RCU.

**WCU:** для item'а до 1 KB 1 WCU = 1 стандартний запис на секунду; transactional — 2 WCU. Наприклад, 3 KB: стандартний — 3 WCU, transactional — 6 WCU.

Розрахунок:
- **Читання:** 4,5 KB округлюємо до 8 KB → 2 RCU для strong → 1 RCU для eventual. 1 × 20 = **20 RCU**.
- **Запис:** 4,5 KB округлюємо до 5 KB → 5 WCU. 5 × 12 = **60 WCU**.

**❌ Неправильні варіанти:**
- *40 RCU and 60 WCU* — правильно для strongly consistent читань.
- *40 RCU and 120 WCU* — для strongly consistent читань і транзакційних записів.
- *6 RCU and 18 WCU* — неправильний розрахунок.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t05-q08"></a>
### T05-Q08
*домен: визначено автоматично (у джерелі: AWS Database)*

An engineer is constructing a web-based application that uses Amazon DynamoDB for storing data. The data is distributed across two tables: 'authors' and 'books'. The 'authors' table uses 'authorName' as its partition key, while the 'books' table has 'bookTitle' as the partition key and 'authorName' as the sort key.

The application requires the ability to fetch multiple books and authors simultaneously in a single database operation for effective performance. The engineer is seeking a solution that maximizes application efficiency and reduces network traffic.

What strategy should the engineer employ to achieve these requirements?

- ▫️ First query the 'books' table using 'bookTitle' as a key condition, then separately query the 'authors' table using 'authorName'.
- ▫️ Use a DynamoDB Scan operation to fetch the required items from both tables.
- ▫️ Execute individual GetItem operations for each book and author to be retrieved.
- ✅ Utilize the DynamoDB BatchGetItem operation to fetch multiple items from both tables in a single network round trip.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Utilize the DynamoDB BatchGetItem operation to fetch multiple items from both tables in a single network round trip»**

`BatchGetItem` отримує кілька items з однієї або кількох таблиць за одну операцію: менше мережевого трафіку, краща продуктивність.

**❌ Неправильні варіанти:**
- *Individual GetItem operations* — більше мережевих запитів, повільніше.
- *Scan* — читає всю таблицю, неефективно.
- *Query 'books' then separately 'authors'* — більше мережевих операцій.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html>

</details>

---

<a id="t05-q22"></a>
### T05-Q22
*домен: визначено автоматично (у джерелі: AWS Database)*

A customer requires a schema-less, key/value database that can be used for storing customer orders. Which type of AWS database is BEST suited to this requirement?

- ▫️ Amazon RDS
- ✅ Amazon DynamoDB
- ▫️ Amazon S3
- ▫️ Amazon ElastiCache

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon DynamoDB»**

DynamoDB — повністю керована NoSQL-БД зі швидкою, передбачуваною продуктивністю і безшовним масштабуванням. Вона нереляційна (без схеми) і типу key-value. Найкраще підходить під вимогу.

**❌ Неправильні варіанти:**
- *Amazon RDS* — реляційна БД зі схемою.
- *Amazon ElastiCache* — key-value сховище, але воно кешує дані інших БД для швидшого читання.
- *Amazon S3* — об'єктне сховище, а не БД. Для бази замовлень DynamoDB підходить краще.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html>

</details>

---

<a id="t05-q39"></a>
### T05-Q39
*домен: визначено автоматично (у джерелі: AWS Database)*

An application is using Amazon DynamoDB as its data store and needs to be able to read 100 items per second as strongly consistent reads. Each item is 5 KB in size.
What value should be set for the table's provisioned throughput for reads?

- ▫️ 500 Read Capacity Units
- ▫️ 50 Read Capacity Units
- ✅ 200 Read Capacity Units
- ▫️ 250 Read Capacity Units

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «200 Read Capacity Units»**

**RCU** (блок 4 KB):
- 1 RCU = 1 strongly consistent читання на секунду;
- 1 RCU = 2 eventually consistent читання на секунду;
- transactional читання коштує 2 RCU;
- приклад для елемента 8 KB: strongly consistent — 2 RCU, eventually consistent — 1 RCU, transactional — 4 RCU.

**WCU** (блок 1 KB):
- 1 WCU = 1 стандартний запис на секунду;
- transactional запис коштує 2 WCU;
- приклад для елемента 3 KB: стандартний запис — 3 WCU, transactional — 6 WCU.

Розрахунок для 100 strongly consistent читань на секунду при елементі 5 KB:
1. Округлити розмір до кратного 4 KB: 5 KB → 8 KB.
2. RCU на одне читання: 8 / 4 = 2.
3. Помножити на кількість читань: 2 × 100 = **200 RCU**.

**❌ Неправильні варіанти:**
- *50 RCU* — неправильний розрахунок.
- *250 RCU* — неправильний розрахунок.
- *500 RCU* — неправильний розрахунок.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t05-q40"></a>
### T05-Q40
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is creating a DynamoDB table for storing application logs. The table has 5 write capacity units (WCUs). The Developer needs to configure the read capacity units (RCUs) for the table. Which of the following configurations represents the most efficient use of throughput?

- ▫️ Strongly consistent reads of 5 RCUs reading items that are 4 KB in size
- ✅ Eventually consistent reads of 15 RCUs reading items that are 1 KB in size
- ▫️ Eventually consistent reads of 5 RCUs reading items that are 4 KB in size
- ▫️ Strongly consistent reads of 15 RCUs reading items that are 1KB in size

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Eventually consistent reads of 15 RCUs reading items that are 1 KB in size»**

Порахуйте, скільки читань на секунду дає кожен варіант. Будь-який елемент до 4 KB округлюється до 4 KB.
- EC, 15 RCU, 1 KB → 15 × 2 = **30 читань/с** — найбільше.
- SC, 15 RCU, 1 KB → 15 читань/с.
- EC, 5 RCU, 4 KB → 10 читань/с.
- SC, 5 RCU, 4 KB → 5 читань/с.

Eventually consistent читання коштує вдвічі менше RCU, ніж strongly consistent.

⚠️ В оригінальному поясненні мітки CORRECT/INCORRECT переплутані, а також стверджується, що 1 KB «економить» RCU порівняно з 4 KB. Це неточно: для читання все до 4 KB коштує однаково. Виграш дає лише eventually consistent.

**❌ Неправильні варіанти:**
- *Strongly consistent, 15 RCU, 1 KB* — лише 15 читань/с.
- *Strongly consistent, 5 RCU, 4 KB* — лише 5 читань/с.
- *Eventually consistent, 5 RCU, 4 KB* — лише 10 читань/с.

Джерела: <https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/ProvisionedThroughput.html>

</details>

---

<a id="t05-q41"></a>
### T05-Q41
*домен: визначено автоматично (у джерелі: AWS Database)*

A gaming company is building an application to track the scores for their games using an Amazon DynamoDB table. Each item in the table is identified by a partition key
(user_id) and a sort key (game_name). The table also includes the attribute “TopScore”. The table design is shown below:

A Developer has been asked to write a leaderboard application to display the highest achieved scores for each game (game_name), based on the score identified in the “TopScore” attribute.

What process will allow the Developer to extract results MOST efficiently from the DynamoDB table?

- ▫️ Create a global secondary index with a partition key of “user_id” and a sort key of “game_name” and get the results based on the “TopScore” attribute
- ▫️ Create a local secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute
- ▫️ Use a DynamoDB scan operation to retrieve the scores for “game_name” using the “TopScore” attribute, and order the results based on the score attribute
- ✅ Create a global secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a global secondary index with a partition key of “game_name” and a sort key of “TopScore” and get the results based on the score attribute»**

Первинний ключ DynamoDB складається з partition key і, за потреби, sort key. Добре спроєктований sort key дає дві переваги:
- групує пов'язані дані в одному місці, і їх можна ефективно читати range-запитами (`begins_with`, `between`, `>`, `<`);
- складений sort key моделює ієрархії (one-to-many), які можна запитувати на будь-якому рівні.

Щоб швидко шукати за неключовими атрибутами, створюють GSI. Він організовує вибрані атрибути таблиці за іншим первинним ключем, і схема його ключа може повністю відрізнятися від схеми таблиці.

Щоб знайти найкращий результат для кожної гри, створюють GSI з partition key `game_name` і sort key `TopScore`, а потім ефективно виконують Query до нього.

**❌ Неправильні варіанти:**
- *LSI з partition key game_name* — у LSI можна змінити лише sort key, partition key лишається тим самим, що в таблиці. До того ж LSI створюється тільки разом із таблицею.
- *Scan* — неефективно, бо читає всю таблицю. Краще GSI + Query.
- *GSI з user_id / game_name* — потрібні `game_name` і `TopScore`, `user_id` тут не потрібен.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-sort-keys.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t06-q18"></a>
### T06-Q18
*домен: визначено автоматично (у джерелі: AWS Database)*

An Amazon DynamoDB table has been created using provisioned capacity. A manager needs to understand whether the DynamoDB table is cost-effective. How can the manager query how much provisioned capacity is actually being used?

- ▫️ Use Amazon CloudTrail and monitor the DescribeLimits API action
- ▫️ Monitor the ReadThrottleEvents and WriteThrottleEvents metrics for the table
- ▫️ 1. Use AWS X-Ray to instrument the DynamoDB table and monitor sub segments
- ✅ Monitor the ConsumedReadCapacityUnits and ConsumedWriteCapacityUnits over a specified time period

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Monitor the ConsumedReadCapacityUnits and ConsumedWriteCapacityUnits over a specified time period»**

DynamoDB автоматично надсилає метрики в CloudWatch, і там вони зберігаються певний час для аналізу історії. Скільки виділеної ємності реально використовується, показують `ConsumedReadCapacityUnits` і `ConsumedWriteCapacityUnits` за заданий період.

**❌ Неправильні варіанти:**
- *ReadThrottleEvents/WriteThrottleEvents* — показують запити, що перевищили provisioned throughput, а не використання.
- *CloudTrail + DescribeLimits* — CloudTrail записує API-дії, а не метрики.
- *X-Ray subsegments* — X-Ray може записувати downstream-виклики до DynamoDB у субсегменти, але використання ємності так не моніторять.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/monitoring-cloudwatch.html>

</details>

---

<a id="t07-q10"></a>
### T07-Q10
*домен: офіційна мітка*

Your web application reads and writes data to your DynamoDB table. The table is provisioned with 400 Write Capacity Units (WCU’s) shared across 4 partitions. One of the partitions receives 250 WCU/second while others receive much less. You receive the error 'ProvisionedThroughputExceededException'.

What is the likely cause of this error?

- ✅ You have a hot partition
- ▫️ Configured IAM policy is wrong
- ▫️ CloudWatch monitoring is lagging
- ▫️ Write Capacity Units (WCU’s) are applied across to all your DynamoDB tables and this needs reconfiguration

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «You have a hot partition»**

Навантаження не завжди розподіляється рівномірно. Hot partition отримує непропорційно багато читань і записів. Adaptive capacity дає змогу працювати з такою партицією без throttling, доки не перевищено загальну ємність таблиці або максимум партиції (3 000 RCU / 1 000 WCU). Якщо перевищено, з'являється `ProvisionedThroughputExceededException`.

Детальніше:
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ProvisionedThroughput.html
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html

**❌ Неправильні варіанти:**
- *CloudWatch lagging* — помилку повертає сама DynamoDB; CloudWatch throttling не спричиняє.
- *Wrong IAM policy* — помилка про перевищення ємності, а не про авторизацію.
- *WCU applied across all tables* — RCU і WCU задаються для кожної таблиці окремо.

</details>

---

<a id="t08-q08"></a>
### T08-Q08
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A company stores session information for a serverless application in an Amazon DynamoDB table. The company requires an automated process to eliminate outdated items from the table.

What is the most straightforward and lowest cost method to accomplish this?

- ✅ Use Amazon DynamoDB Time to Live (TTL) to automatically delete old items.
- ▫️ Use Amazon CloudWatch to monitor and delete old items.
- ▫️ Use AWS DMS (Database Migration Service) to purge old items.
- ▫️ Implement a periodic AWS Lambda function to scan and delete old items.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon DynamoDB Time to Live (TTL) to automatically delete old items.»**

TTL дає змогу задати для кожного елемента мітку часу, після якої він більше не потрібен. Невдовзі після цього часу DynamoDB сама видаляє елемент. Це найпростіший автоматичний спосіб прибирати старі дані.

ℹ️ Видалення через TTL не миттєве: зазвичай відбувається протягом кількох днів, а не точно в заданий час. Див. 06.

**❌ Неправильні варіанти:**
- *Periodic Lambda scan & delete* — можливо, але це додаткова розробка й підтримка.
- *AWS DMS* — сервіс міграції БД, він не видаляє старі дані.
- *CloudWatch* — сервіс моніторингу, він не видаляє елементи з БД.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html>

</details>

---

<a id="t08-q59"></a>
### T08-Q59
*домен: визначено автоматично (у джерелі: AWS Database)*

An application is using Amazon DynamoDB as its data store and needs to be able to read 200 items per second as eventually consistent reads. Each item is 12 KB in size.
What value should be set for the table's provisioned throughput for reads?

- ▫️ 600 Read Capacity Units
- ▫️ 1200 Read Capacity Units
- ▫️ 150 Read Capacity Units
- ✅ 300 Read Capacity Units

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «300 Read Capacity Units»**

**RCU** (блок 4 KB):
- 1 RCU = 1 strongly consistent читання на секунду;
- 1 RCU = 2 eventually consistent читання на секунду;
- transactional читання коштує 2 RCU.

**WCU** (блок 1 KB):
- 1 WCU = 1 стандартний запис на секунду;
- transactional запис коштує 2 WCU.

Розрахунок для 200 eventually consistent читань на секунду при елементі 12 KB:
1. Округлити до кратного 4 KB: 12 KB лишається 12 KB, тобто 3 блоки.
2. Strongly consistent: 3 RCU на читання; eventually consistent — удвічі менше, 1,5 RCU.
3. 1,5 × 200 = **300 RCU**.

**❌ Неправильні варіанти:**
- *600 RCU* — для strongly consistent читань.
- *1200 RCU* — для transactional читань.
- *150 RCU* — неправильний розрахунок.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t09-q07"></a>
### T09-Q07
*домен: офіційна мітка*

A social gaming application supports the transfer of gift vouchers between users. When a user hits a certain milestone on the leaderboard, they earn a gift voucher that can be redeemed or transferred to another user. The development team wants to ensure that this transfer is captured in the database such that the records for both users are either written successfully with the new gift vouchers or the status quo is maintained.

Which of the following solutions represent the best-fit options to meet the requirements for the given use-case? (Select two)

- ✅ Use the DynamoDB transactional read and write APIs on the table items as a single, all-or-nothing operation
- ▫️ Perform DynamoDB read and write operations with ConsistentRead parameter set to true
- ▫️ Use the Amazon Athena transactional read and write APIs on the table items as a single, all-or-nothing operation
- ✅ Complete both operations on RDS MySQL in a single transaction block
- ▫️ Complete both operations on Amazon RedShift in a single transaction block

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the DynamoDB transactional read and write APIs on the table items as a single, all-or-nothing operation» + «Complete both operations on RDS MySQL in a single transaction block»**

- **DynamoDB transactions** вносять узгоджені зміни «все або нічого» в кілька елементів однієї чи кількох таблиць і дають ACID-гарантії. Детальніше: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html
- **RDS MySQL** — реляційна БД з підтримкою транзакцій, тож обидві операції можна виконати в одній транзакції.

**❌ Неправильні варіанти:**
- *ConsistentRead=true* — вмикає strongly consistent читання; транзакцій не дає.
- *Redshift* — сховище даних для аналітики, а не для транзакційної обробки (OLTP).
- *Athena transactional APIs* — Athena — сервіс SQL-запитів до S3, транзакцій у такому сенсі не має.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transactions.html> · <https://aws.amazon.com/relational-database/>

</details>

---

<a id="t09-q28"></a>
### T09-Q28
*домен: офіційна мітка*

The development team at a multi-national retail company wants to support trusted third-party authenticated users from the supplier organizations to create and update records in specific DynamoDB tables in the company's AWS account.

As a Developer Associate, which of the following solutions would you suggest for the given use-case?

- ✅ Use Cognito Identity pools to enable trusted third-party authenticated users to access DynamoDB
- ▫️ Create a new IAM group in the company's AWS account for each of the third-party authenticated users from the supplier organizations. The users can then use the IAM group credentials to access DynamoDB
- ▫️ Use Cognito User pools to enable trusted third-party authenticated users to access DynamoDB
- ▫️ Create a new IAM user in the company's AWS account for each of the third-party authenticated users from the supplier organizations. The users can then use the IAM user credentials to access DynamoDB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Cognito Identity pools to enable trusted third-party authenticated users to access DynamoDB»**

Identity pools (federated identities) створюють унікальні ідентичності, федерують їх з IdP і видають тимчасові облікові дані AWS з обмеженими правами. Підтримувані провайдери:
- Login with Amazon, Facebook, Google, Sign in with Apple;
- Cognito user pools;
- OpenID Connect;
- SAML;
- developer authenticated identities.

Користувачі постачальників уже автентифіковані у своїх IdP, тож identity pool дасть їм тимчасовий доступ до DynamoDB.

Порівняння user pools та identity pools: https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

**❌ Неправильні варіанти:**
- *User pools* — каталог для входу, але облікових даних AWS не видає.
- *IAM user per third-party user* — громіздко, коли користувачі вже автентифіковані деінде.
- *IAM group per user* — групи не мають облікових даних.

</details>

---

<a id="t09-q31"></a>
### T09-Q31
*домен: офіційна мітка*

The technology team at an investment bank uses DynamoDB to facilitate high-frequency trading where multiple trades can try and update an item at the same time.

Which of the following actions would make sure that only the last updated value of any item is used in the application?

- ✅ Use ConsistentRead = true while doing GetItem operation for any item
- ▫️ Use ConsistentRead = false while doing PutItem operation for any item
- ▫️ Use ConsistentRead = true while doing PutItem operation for any item
- ▫️ Use ConsistentRead = true while doing UpdateItem operation for any item

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use ConsistentRead = true while doing GetItem operation for any item»**

Режими читання DynamoDB:
- **Eventually consistent** (за замовчуванням) — відповідь може не враховувати щойно завершений запис; через короткий час повторне читання поверне свіжі дані.
- **Strongly consistent** — завжди повертає найсвіжіші дані з урахуванням усіх успішних записів.

Параметр `ConsistentRead` є лише в операціях читання (`GetItem`, `Query`, `Scan`). Щоб застосунок завжди бачив останнє значення, у `GetItem` ставлять `ConsistentRead = true`.

**❌ Неправильні варіанти:**
- *ConsistentRead in UpdateItem* — це операція запису, параметра `ConsistentRead` у неї немає.
- *ConsistentRead=true in PutItem* — це теж запис.
- *ConsistentRead=false in PutItem* — це теж запис.

</details>

---

<a id="t09-q32"></a>
### T09-Q32
*домен: офіційна мітка*

A development team is building a game where players can buy items with virtual coins. For every virtual coin bought by a user, both the players table as well as the items table in DynamodDB need to be updated simultaneously using an all-or-nothing operation.

As a developer associate, how will you implement this functionality?

- ✅ Use TransactWriteItems API of DynamoDB Transactions
- ▫️ Capture the transactions in the players table using DynamoDB streams and then sync with the items table
- ▫️ Use BatchWriteItem API to update multiple tables simultaneously
- ▫️ Capture the transactions in the items table using DynamoDB streams and then sync with the players table

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use TransactWriteItems API of DynamoDB Transactions»**

Транзакції DynamoDB групують кілька дій в одну операцію «все або нічого»: `TransactWriteItems` або `TransactGetItems`. `TransactWriteItems` — синхронна й ідемпотентна операція запису. Вона може зачіпати різні елементи в одній чи кількох таблицях того самого акаунта і регіону; загальний розмір — до 4 MB. Або всі дії успішні, або жодна. Client token (`ClientRequestToken`) робить запит ідемпотентним: повторне надсилання через timeout не спричинить помилок.

⚠️ В оригіналі вказано ліміт 25 дій. З 2022 року їх може бути до **100**.

**❌ Неправильні варіанти:**
- *BatchWriteItem* — частина операцій може бути успішною, а частина ні; атомарності немає.
- *Streams on players table* — Streams фіксують зміни елементів майже в реальному часі, але транзакцій не забезпечують.
- *Streams on items table* — те саме.

</details>

---

<a id="t09-q34"></a>
### T09-Q34
*домен: офіційна мітка*

A diagnostic lab stores its data on DynamoDB. The lab wants to backup a particular DynamoDB table data on Amazon S3, so it can download the S3 backup locally for some operational use.

Which of the following options is NOT feasible?

- ▫️ Use AWS Glue to copy your table to Amazon S3 and download locally
- ▫️ Use Hive with Amazon EMR to export your data to an S3 bucket and download locally
- ▫️ Use AWS Data Pipeline to export your table to an S3 bucket in the account of your choice and download locally
- ✅ Use the DynamoDB on-demand backup capability to write to Amazon S3 and download locally

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the DynamoDB on-demand backup capability to write to Amazon S3 and download locally»**

Питання на пошук **нездійсненного** варіанта. Вбудовані бекапи DynamoDB (on-demand і PITR) зберігаються в S3, але доступу до цих бакетів у вас немає, тож завантажити бекап локально неможливо.

ℹ️ Зараз є нативний **Export to S3**: на основі PITR, у ваш бакет, у форматі DynamoDB JSON або Ion.

**❌ Інші варіанти здійсненні:**
- *AWS Data Pipeline → S3* — найпростіший спосіб для разового бекапу: використовує EMR, скрипти готові. ⚠️ Data Pipeline у maintenance mode.
- *Hive з EMR → S3* — або open-source `emr-dynamodb-connector` для власних бекапів на Spark чи Hive. Дає більше контролю.
- *AWS Glue → S3* — для автоматичних регулярних бекапів, які можна використати в Athena.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/BackupRestore.html> · <https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-importexport-ddb-part1.html> · <https://docs.aws.amazon.com/emr/latest/ReleaseGuide/EMR_Hive_Commands.html#EMR_Hive_Commands_exporting> · <https://aws.amazon.com/blogs/big-data/how-to-export-an-amazon-dynamodb-table-to-amazon-s3-using-aws-step-functions-and-aws-glue/>

</details>

---

<a id="t09-q55"></a>
### T09-Q55
*домен: офіційна мітка*

As a senior architect, you are responsible for the development, support, maintenance, and implementation of all database applications written using NoSQL technology. A new project demands a throughput requirement of 10 strongly consistent reads per second of 6KB in size each.

How many read capacity units will you need when configuring your DynamoDB table?

- ▫️ 60
- ✅ 20
- ▫️ 10
- ▫️ 30

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «20»**

1 RCU — це одне strongly consistent читання на секунду для елемента до 4 KB. Розрахунок:
1. Елемент 6 KB ÷ 4 KB = 1,5, округлюємо вгору → 2 RCU на одне читання.
2. 2 × 10 читань на секунду = **20 RCU**.

**❌ Неправильні варіанти:**
- *60* — неправильний розрахунок.
- *30* — неправильний розрахунок.
- *10* — неправильний розрахунок.

</details>

---

<a id="t09-q61"></a>
### T09-Q61
*домен: офіційна мітка*

A startup has been experimenting with DynamoDB in its new test environment. The development team has discovered that some of the write operations have been overwriting existing items that have the specified primary key. This has messed up their data, leading to data discrepancies.

Which DynamoDB write option should be selected to prevent this kind of overwriting?

- ▫️ Use Scan operation
- ▫️ Atomic Counters
- ▫️ Batch writes
- ✅ Conditional writes

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Conditional writes»**

DynamoDB підтримує умовний запис для `PutItem`, `UpdateItem` і `DeleteItem`. Запис виконується, лише якщо атрибути елемента відповідають умові; інакше повертається помилка. Приклади:
- `PutItem` спрацює, лише якщо елемента з таким ключем ще немає (`attribute_not_exists`);
- `UpdateItem` не змінить елемент, якщо якийсь атрибут має певне значення.

Так кілька користувачів не перезаписують зміни одне одного (optimistic locking).

**❌ Неправильні варіанти:**
- *Batch writes* — зменшують кількість мережевих звернень і виконуються паралельно, але від перезапису не захищають.
- *Atomic Counters* — безумовний інкремент числового атрибута, наприклад лічильник відвідувачів; не для цього сценарію.
- *Scan* — читає всю таблицю і до оновлень не стосується.

</details>

---

<a id="t10-q05"></a>
### T10-Q05
*домен: визначено автоматично (у джерелі: AWS Database)*

An application needs to read up to 100 items at a time from an Amazon DynamoDB. Each item is up to 100 KB in size and all attributes must be retrieved.

What is the BEST way to minimize latency?

- ▫️ Use a Query operation with a FilterExpression
- ▫️ Use a Scan operation with pagination
- ✅ Use BatchGetItem
- ▫️ Use GetItem and use a projection expression

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use BatchGetItem»**

`BatchGetItem` повертає атрибути одного чи багатьох елементів з однієї чи кількох таблиць за первинними ключами:
- за одну операцію — до 16 MB даних і до 100 елементів;
- елементи читаються паралельно, тож затримка мінімальна;
- за замовчуванням читання eventually consistent; для strongly consistent ставлять `ConsistentRead=true` для потрібних таблиць.

**❌ Неправильні варіанти:**
- *GetItem with projection* — обмежує атрибути, але елементи читаються послідовно, тож затримка більша.
- *Scan with pagination* — найменш ефективно: читає всю таблицю; пагінація лише ділить результат.
- *Query with FilterExpression* — лише відфільтровує результат.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html>

</details>

---

<a id="t10-q21"></a>
### T10-Q21
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Scan: обидва — Limit + Parallel Scan.** → [детальніше](../../06-contradictions-and-traps.md#c02)

An application scans an Amazon DynamoDB table once per day to produce a report. The scan is performed in non-peak hours when production usage uses around 50% of the provisioned throughput.

How can you MINIMIZE the time it takes to produce the report without affecting production workloads? (Select TWO.)

- ▫️ Use a Sequential Scan API operation
- ✅ Use the Limit parameter
- ✅ Use a Parallel Scan API operation
- ▫️ Use pagination to divide results into 1 MB pages
- ▫️ Increase read capacity units during the scan operation

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the Limit parameter» + «Use a Parallel Scan API operation»**

За замовчуванням Scan послідовний: повертає дані порціями по 1 MB і читає одну партицію за раз, тож швидкість обмежена однією партицією.

**Parallel Scan** ділить таблицю на сегменти, і кілька workers (потоків чи процесів) сканують їх паралельно. Кожен worker передає:
- `Segment` — свій номер сегмента;
- `TotalSegments` — загальна кількість сегментів, яка дорівнює кількості workers.

**`Limit`** обмежує обсяг даних в одному запиті. Так жоден worker не забере всю ємність таблиці, і критичні частини застосунку не тротлитимуться.

**❌ Неправильні варіанти:**
- *Sequential Scan* — довше.
- *Increase RCU* — дорожче і не вирішує проблему впливу на продакшн.
- *Pagination into 1 MB pages* — лише ділить результат на сторінки, не паралелізує і не обмежує використання ємності.

Джерела: <https://aws.amazon.com/blogs/developer/rate-limited-scans-in-amazon-dynamodb/> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html#Scan.ParallelScan>

</details>

---

<a id="t10-q28"></a>
### T10-Q28
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer needs to return a list of items in a global secondary index from an Amazon DynamoDB table.

Which DynamoDB API call can the Developer use in order to consume the LEAST number of read capacity units?

- ▫️ Scan operation using strongly-consistent reads
- ▫️ Scan operation using eventually-consistent reads
- ▫️ Query operation using strongly-consistent reads
- ✅ Query operation using eventually-consistent reads

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Query operation using eventually-consistent reads»**

Query шукає елементи за первинним ключем і працює з таблицею чи індексом, що мають composite key. Для елементів до 4 KB 1 RCU — це 1 strongly consistent або 2 eventually consistent читання на секунду. Тож eventually consistent споживає вдвічі менше RCU.

**❌ Неправильні варіанти:**
- *Query, strongly consistent* — удвічі більше RCU.
- *Scan, eventually consistent* — Scan читає всі елементи таблиці, тож RCU більше.
- *Scan, strongly consistent* — найдорожче.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html>

</details>

---

<a id="t10-q30"></a>
### T10-Q30
*домен: визначено автоматично (у джерелі: AWS Database)*

A developer is creating a serverless application that will use a DynamoDB table. The average item size is 9KB. The application will make 4 strongly consistent reads/sec, and 2 standard write/sec. How many RCUs/WCUs are required?

- ✅ 12 RCU and 18 WCU
- ▫️ 12 RCU and 36 WCU
- ▫️ 24 RCU and 18 WCU
- ▫️ 6 RCU and 18 WCU

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «12 RCU and 18 WCU»**

**RCU** (блок 4 KB):
- 1 RCU = 1 strongly consistent або 2 eventually consistent читання на секунду;
- transactional читання коштує 2 RCU.

**WCU** (блок 1 KB):
- 1 WCU = 1 стандартний запис на секунду;
- transactional запис коштує 2 WCU.

Розрахунок для елемента 9 KB:
- **4 strongly consistent читання на секунду:** 9 KB округлюємо до 12 KB → 3 RCU на читання → 3 × 4 = **12 RCU**.
- **2 стандартні записи на секунду:** 9 KB → 9 WCU на запис → 9 × 2 = **18 WCU**.

**❌ Неправильні варіанти:**
- *24 RCU / 18 WCU* — так було б для transactional читань.
- *12 RCU / 36 WCU* — так було б для transactional записів.
- *6 RCU / 18 WCU* — так було б для eventually consistent читань.

Джерела: <https://aws.amazon.com/dynamodb/pricing/provisioned/>

</details>

---

<a id="t10-q37"></a>
### T10-Q37
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer wants to find a list of items in a global secondary index from an Amazon DynamoDB table.

Which DynamoDB API call can the Developer use in order to consume the LEAST number of read capacity units?

- ▫️ Query operation using strongly-consistent reads
- ▫️ Scan operation using eventually-consistent reads
- ✅ Query operation using eventually-consistent reads
- ▫️ Scan operation using strongly-consistent reads

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Query operation using eventually-consistent reads»**

Вторинні індекси дають ефективний доступ за атрибутами, що не входять у первинний ключ. Індекс містить частину атрибутів таблиці й альтернативний ключ, і до нього роблять Query так само, як до таблиці. Таблиця може мати кілька індексів під різні патерни запитів. Scan по GSI теж можливий, але менш ефективний: він повертає всі елементи індексу.

**❌ Неправильні варіанти:**
- *Query, strongly consistent* — більше RCU, і GSI не підтримує strongly consistent (LSI — підтримує).
- *Scan, eventually consistent* — Scan менш ефективний за Query.
- *Scan, strongly consistent* — і менш ефективний, і для GSI неможливий.

Джерела: <https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/SecondaryIndexes.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t10-q38"></a>
### T10-Q38
*домен: визначено автоматично (у джерелі: AWS Database)*

A company manages an application that stores data in an Amazon DynamoDB table. The company need to keep a record of all new changes made to the DynamoDB table in another table within the same AWS region. What is the MOST suitable way to deliver this requirement?

- ▫️ Use Amazon CloudTrail
- ✅ Use Amazon DynamoDB streams
- ▫️ Use Amazon DynamoDB snapshots
- ▫️ Use CloudWatch events

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon DynamoDB streams»**

DynamoDB Stream — упорядкований потік інформації про зміни елементів таблиці. Щоразу, коли елемент створюють, змінюють чи видаляють, у stream з'являється запис із первинним ключем зміненого елемента. Можна налаштувати додатково знімки до і після зміни (old/new image). Ці записи можна зберегти в іншу таблицю для постійного зберігання.

**❌ Неправильні варіанти:**
- *CloudWatch Events* — події про зміни ресурсів, але не про зміни даних у таблиці.
- *CloudTrail* — історія API-викликів для аудиту, а не зміни даних.
- *DynamoDB snapshots* — стан на момент часу, а не окремі зміни елементів.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html>

</details>

---

<a id="t11-q17"></a>
### T11-Q17
*домен: офіційна мітка*

> ⚠️ **TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

You are storing bids information on your betting application and you would like to automatically expire DynamoDB table data after one week.

What should you use?

- ▫️ Use DAX
- ✅ Use TTL
- ▫️ Use a Lambda function
- ▫️ Use DynamoDB Streams

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use TTL»**

TTL у DynamoDB задає, коли елементи «протухають» і автоматично видаляються. Видалення безкоштовні й не витрачають provisioned throughput. Мітку часу видалення задають для кожного елемента окремо, тож у таблиці зберігаються лише актуальні дані.

**❌ Неправильні варіанти:**
- *DynamoDB Streams* — журнал змін, а не видалення. Видалення через TTL теж потрапляють у stream як події.
- *DAX* — in-memory кеш для DynamoDB; до видалення не стосується.
- *Lambda function* — можливо, але потрібні індекси, запити чи Scan і регулярний запуск через EventBridge. Гірше, ніж вбудований TTL.

</details>

---

<a id="t11-q62"></a>
### T11-Q62
*домен: офіційна мітка*

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

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up an Amazon DynamoDB table with a primary key that consists of the name as the partition key and the version number as the sort key. Create a global secondary index that has the category as the partition key and the name as the sort key»**

Під час створення таблиці DynamoDB вказують первинний ключ, який унікально ідентифікує кожен елемент. Вторинні індекси дають змогу робити запити за альтернативним ключем. GSI містить вибрані атрибути таблиці, організовані за іншим первинним ключем, тож можна запитувати атрибути поза ключем таблиці.

Як ця схема покриває запити:
- `name` + `version` як ключ таблиці — «деталі гри за назвою і версією» одним запитом;
- GSI з `category` + `name` — ігри за категорією.

**❌ Неправильні варіанти:**
- *Category + version as key, GSI by name* — щоб знайти гру за назвою і версією, знадобиться кілька запитів.
- *RDS MySQL with name as PK* — неефективні запити за версією та категорією.
- *ElastiCache Memcached for permanent storage* — це кеш, а не постійне сховище.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/primary-key-dynamodb-table/>

</details>

---

<a id="t12-q06"></a>
### T12-Q06
*домен: офіційна мітка*

You're a developer for 'Movie Gallery', a company that just migrated to the cloud. A database must be created using NoSQL technology to hold movies that are listed for public viewing. You are taking an important step in designing the database with DynamoDB and need to choose the appropriate partition key.

Which of the following unique attributes satisfies this requirement?

- ▫️ producer_name
- ▫️ movie_language
- ✅ movie_id
- ▫️ lead_actor_name

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «movie_id»**

DynamoDB зберігає елементи за унікальним первинним ключем у партиціях по 10 GB. Хеш від partition key визначає, в якій партиції лежить елемент. Щоб навантаження розподілялося рівномірно, partition key має бути з високою кардинальністю. `movie_id` унікальний для кожного фільму, тож підходить найкраще.

Детальніше: https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/

**❌ Неправильні варіанти:**
- *producer_name* — значення повторюються, кардинальність низька.
- *lead_actor_name* — значення повторюються, кардинальність низька.
- *movie_language* — значення повторюються, кардинальність дуже низька.

</details>

---

<a id="t12-q08"></a>
### T12-Q08
*домен: офіційна мітка*

You are a DynamoDB developer for an aerospace company that requires you to write 6 objects per second of 4.5KB in size each.

What write capacity unit is needed for your project?

- ▫️ 24
- ✅ 30
- ▫️ 46
- ▫️ 15

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «30»**

1 WCU — це один запис на секунду для елемента до 1 KB. Розмір округлюється вгору до цілого KB: навіть 500 байт рахуються як 1 KB. Розрахунок:
1. Об'єкт 4,5 KB округлюємо до 5 KB → 5 WCU на запис.
2. 6 об'єктів × 5 = **30 WCU**.

**❌ Неправильні варіанти:**
- *24* — неправильний розрахунок.
- *15* — неправильний розрахунок.
- *46* — неправильний розрахунок.

</details>

---

<a id="t12-q27"></a>
### T12-Q27
*домен: офіційна мітка*

You are a software engineer working for an IT company and are asked to contribute to a growing internal application that includes dashboards for data visualization. You are provisioning your AWS DynamoDB table and need to perform 10 strongly consistent reads per second of 4 KB in size each.

How many Read Capacity Units (RCUs) are needed?

- ▫️ 20
- ✅ 10
- ▫️ 40
- ▫️ 5

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «10»**

1 RCU — це 1 strongly consistent або 2 eventually consistent читання на секунду для елемента до 4 KB. Більші елементи потребують додаткових RCU. Розрахунок:
1. 4 KB ÷ 4 KB = 1 RCU на читання.
2. Strongly consistent: 1 × 10 = **10 RCU**.

**❌ Неправильні варіанти:**
- *40* — неправильний розрахунок.
- *20* — неправильний розрахунок.
- *5* — так було б для eventually consistent.

</details>

---
