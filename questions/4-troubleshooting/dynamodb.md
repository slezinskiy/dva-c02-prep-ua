# Amazon DynamoDB

**Домен 4 — Усунення несправностей та оптимізація** · питань: 12  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#dynamodb) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q64"></a>
### T02-Q64
*домен: визначено автоматично (у джерелі: AWS Database)*

The Lambda function needs to write this data to an Amazon DynamoDB table. After deploying the function, the developer notices that the write operations to the DynamoDB table occasionally fail due to throttling.

What should the developer do to reduce the likelihood of these throttling issues without significantly over-provisioning the DynamoDB table's write capacity?

- ▫️ Increase the Lambda function's timeout setting to allow for retries in case of throttling.
- ▫️ Use Amazon SQS to queue data before writing it to the DynamoDB table, ensuring a steady and controlled write rate.
- ▫️ Convert the DynamoDB table to an on-demand capacity mode to automatically adjust its write capacity to match the workload.
- ✅ Implement exponential backoff in the Lambda function's error handling code to retry failed write operations.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement exponential backoff in the Lambda function's error handling code to retry failed write operations»**

Exponential backoff — найкраща практика повторів у розподілених системах, особливо з сервісами AWS на кшталт DynamoDB: затримка між спробами поступово зростає, що зменшує навантаження на сервіс і ймовірність повторного throttling. Так Lambda переживає короткочасні піки без суттєвого надлишкового провізіонінгу WCU.

**❌ Неправильні варіанти:**
- *Increase the Lambda timeout* — функція працюватиме довше, але причину throttling це не усуне.
- *Convert the table to on-demand* — може допомогти через автомасштабування, але коштуватиме дорожче через неконтрольоване масштабування і не є стратегією обробки в коді.
- *SQS before writing to DynamoDB* — додає складність і затримку, не вирішуючи безпосередньо повтори при throttling.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html>

</details>

---

<a id="t03-q14"></a>
### T03-Q14
*домен: офіційна мітка*

> ⚠️ **Scan: "швидше" → parallel scan.** → [детальніше](../../06-contradictions-and-traps.md#c02)

A development team uses the AWS SDK for Java to maintain an application that stores data in AWS DynamoDB. The application makes use of Scan operations to return several items from a 25 GB table. There is no possibility of creating indexes to retrieve these items predictably. Developers are trying to get these specific rows from DynamoDB as fast as possible.

Which of the following options can be used to improve the performance of the Scan operation?

- ▫️ Use a Query
- ✅ Use parallel scans
- ▫️ Use a ProjectionExpression
- ▫️ Use a FilterExpression

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use parallel scans»**

За замовчуванням Scan обробляє дані послідовно: DynamoDB повертає їх порціями по 1 MB, і застосунок робить нові запити за наступною порцією. Що більша таблиця, то довше триває scan. Parallel Scan логічно ділить таблицю чи індекс на сегменти, і кілька workers (потоків чи процесів) сканують їх паралельно.

**❌ Неправильні варіанти:**
- *Use a ProjectionExpression* — лише обмежує атрибути у відповіді.
- *Use a FilterExpression* — фільтрує після scan; споживання і час не зменшуються.
- *Use a Query* — потрібен індекс, а за умовою створювати індекси не можна.

</details>

---

<a id="t03-q24"></a>
### T03-Q24
*домен: офіційна мітка*

> ⚠️ **Видалити ВСЮ таблицю щоночі → drop & recreate.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A firm uses AWS DynamoDB to store information about people’s favorite sports teams and allow the information to be searchable from their home page. There is a daily requirement that all 10 million records in the table should be deleted then re-loaded at 2:00 AM each night.

Which option is an efficient way to delete with minimal costs?

- ▫️ Scan and call DeleteItem
- ▫️ Scan and call BatchDeleteItem
- ▫️ Call PurgeTable
- ✅ Delete then re-create the table

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Delete then re-create the table»**

`DeleteTable` видаляє таблицю з усіма items (поки видалення не завершиться, таблиця в стані DELETING). Це найшвидший і найдешевший спосіб щоночі очистити 10 млн записів.

**❌ Неправильні варіанти:**
- *Scan and call DeleteItem* та *Scan and call BatchDeleteItem* — Scan на 10 млн items дуже повільний і дорогий.
- *Call PurgeTable* — вигадано.

</details>

---

<a id="t04-q09"></a>
### T04-Q09
*домен: визначено автоматично (у джерелі: AWS Database)*

A Development team are creating a financial trading application. The application requires sub-millisecond latency for processing trading requests. Amazon DynamoDB is used to store the trading data. During load testing the Development team found that in periods of high utilization the latency is too high and read capacity must be significantly over-provisioned to avoid throttling.

How can the Developers meet the latency requirements of the application?

- ▫️ Store the trading data in Amazon S3 and use Transfer Acceleration
- ▫️ Use exponential backoff in the application code for DynamoDB queries
- ✅ Use Amazon DynamoDB Accelerator (DAX) to cache the data
- ▫️ Create a Global Secondary Index (GSI) for the trading data

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon DynamoDB Accelerator (DAX) to cache the data»**

DynamoDB зазвичай відповідає за одиниці мілісекунд, але деяким задачам потрібні мікросекунди. DAX — сумісний з DynamoDB кеш, що:
1. скорочує затримку eventually consistent читань на порядок — з мілісекунд до мікросекунд;
2. знижує складність: керований сервіс, сумісний з API DynamoDB, мінімум змін коду;
3. для read-heavy або «сплескових» навантажень підвищує пропускну здатність і знижує потребу переплачувати за RCU (особливо при частих читаннях тих самих ключів).

Тут потрібна субмілісекундна затримка читань, а RCU доводиться завищувати, тож DAX ідеальний.

**❌ Неправильні варіанти:**
- *Create a GSI* — для запитів за неключовими атрибутами, а потреби в індексі немає.
- *Exponential backoff* — може зменшити потребу в завищенні RCU, але затримку не скоротить, а продуктивність погіршить.
- *S3 + Transfer Acceleration* — прискорює завантаження в S3, не затримку застосунку.

Джерела: <https://aws.amazon.com/dynamodb/dax/>

</details>

---

<a id="t05-q44"></a>
### T05-Q44
*домен: визначено автоматично (у джерелі: AWS Database)*

An application writes items to an Amazon DynamoDB table. As the application scales to thousands of instances, calls to the DynamoDB API generate occasional ThrottlingException errors. The application is coded in a language incompatible with the AWS SDK.

How should the error be handled?

- ▫️ Pass API calls through Amazon API Gateway
- ▫️ Send the items to DynamoDB through Amazon Kinesis Data Firehose
- ✅ Add exponential backoff to the application logic
- ▫️ Use Amazon SQS as an API message bus

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add exponential backoff to the application logic»**

Exponential backoff — щоразу довші паузи між повторними спробами — підвищує надійність застосунку. AWS SDK робить це автоматично, але цей застосунок із SDK несумісний, тож backoff треба реалізувати вручну.

**❌ Неправильні варіанти:**
- *SQS as API message bus* — знадобляться інстанси чи функції, які читатимуть чергу й писатимуть у DynamoDB. Це зайві витрати й складність без виграшу в продуктивності.
- *API Gateway* — не підходить як спосіб throttling для цього застосунку; backoff у коді кращий.
- *Kinesis Data Firehose → DynamoDB* — DynamoDB не є destination для Firehose.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-table-throttled/>

</details>

---

<a id="t05-q45"></a>
### T05-Q45
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer has added a Global Secondary Index (GSI) to an existing Amazon DynamoDB table. The GSI is used mainly for read operations whereas the primary table is extremely write-intensive. Recently, the Developer has noticed throttling occurring under heavy write activity on the primary table. However, the write capacity units on the primary table are not fully utilized.

What is the best explanation for why the writes are being throttled on the primary table?

- ▫️ The Developer should have added an LSI instead of a GSI
- ▫️ There are insufficient write capacity units on the primary table
- ▫️ There are insufficient read capacity units on the primary table
- ✅ The write capacity units on the GSI are under provisioned

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The write capacity units on the GSI are under provisioned»**

Кожен запис у таблицю, що потрапляє в GSI, споживає WCU самого індексу. Тому GSI потрібно стільки WCU, скільки таблиці, або більше. Якщо запис у GSI тротлиться, тротлиться й основна таблиця, навіть коли її власних WCU достатньо. LSI особливого throttling не спричиняють.

Найімовірніше, розробник вирішив, що індексу, призначеному для читання, вистачить менше WCU, і забув про записи в нього.

**❌ Неправильні варіанти:**
- *Insufficient RCU on primary table* — throttling спричиняють записи, а не читання.
- *Should have added an LSI* — GSI мав свої причини. До того ж LSI не додати до наявної таблиці.
- *Insufficient WCU on primary table* — за умовою WCU таблиці використовуються не повністю.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html>

</details>

---

<a id="t07-q29"></a>
### T07-Q29
*домен: офіційна мітка*

A developer is working on an AWS Lambda function that reads data from Amazon S3 objects and writes the data to an Amazon DynamoDB table. Although the function triggers successfully from an S3 event notification upon object creation, it encounters a failure while attempting to write data to the DynamoDB table.

What is the probable reason for the failure?

- ▫️ The Lambda function's provisioned concurrency limit has been exceeded
- ▫️ The Lambda function's reserved concurrency limit has been exceeded
- ✅ The Lambda function does not have IAM permissions to write to DynamoDB
- ▫️ DynamoDB table does not have a Gateway VPC Endpoint, which is required by the Lambda function for a successful write

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The Lambda function does not have IAM permissions to write to DynamoDB»**

Lambda потрібна identity-based політика з правами читання й запису до конкретної таблиці DynamoDB. Її прикріплюють до service role (execution role), у trust policy якої principal — `lambda.amazonaws.com`. Через цю роль функція отримує доступ до таблиці без облікових даних у коді, і можна точно визначити, до яких сервісів вона має доступ.

**❌ Неправильні варіанти:**
- *Provisioned concurrency exceeded* — до проблеми не стосується. Provisioned concurrency заздалегідь ініціалізує середовища виконання, щоб функція відповідала одразу; це платно.
- *Reserved concurrency exceeded* — до проблеми не стосується. Reserved concurrency гарантує і водночас обмежує максимум одночасних екземплярів функції; це безкоштовно.
- *Gateway VPC Endpoint required* — Gateway endpoint дає доступ до S3 і DynamoDB з VPC без IGW чи NAT. Але Lambda за замовчуванням працює не у VPC, тож endpoint їй не потрібен.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_lambda-access-dynamodb.html> · <https://aws.amazon.com/blogs/security/how-to-create-an-aws-iam-policy-to-grant-aws-lambda-access-to-an-amazon-dynamodb-table/>

</details>

---

<a id="t08-q02"></a>
### T08-Q02
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer is troubleshooting an issue with a DynamoDB table. The table is used to store order information for a busy online store and uses the order date as the partition key. During busy periods writes to the table are being throttled despite the consumed throughput being well below the provisioned throughput.

According to AWS best practices, how can the Developer resolve the issue at the LOWEST cost?

- ▫️ Increase the read and write capacity units for the table
- ▫️ Use an Amazon SQS queue to buffer the incoming writes
- ✅ Add a random number suffix to the partition key values
- ▫️ Add a global secondary index to the table

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add a random number suffix to the partition key values»**

DynamoDB зберігає елементи (аналог рядків) і знаходить кожен за унікальним первинним ключем. Як розподіляються дані:
- Елементи лежать у партиціях — внутрішніх одиницях зберігання по 10 GB.
- Хеш від значення partition key визначає, в якій партиції елемент.
- Елементи з однаковим partition key зберігаються разом, упорядковані за sort key. Якщо колекція перевищує 10 GB, партиція ділиться за sort key.

Provisioned throughput рівномірно ділиться між партиціями. Якщо на одне значення partition key припадає понад 3 000 RCU або 1 000 WCU, з'являється `ProvisionedThroughputExceededException`.

Як рівномірно розподілити навантаження:
- атрибути з високою кардинальністю (email, customer_id);
- складені атрибути;
- кешування популярних елементів;
- для інтенсивних записів — випадковий суфікс із заданого діапазону (write sharding).

Тут partition key — дата замовлення, тому виникла гаряча партиція і записи тротляться. Випадковий суфікс рівномірно розподілить записи.

**❌ Неправильні варіанти:**
- *Increase RCU/WCU* — гарячу партицію це не вирішить, до того ж спожито менше, ніж виділено.
- *Add a GSI* — GSI пришвидшує запити, а не записи в гарячу партицію.
- *SQS buffer* — не найдешевше: потрібні producers, consumers і оплата черги.

Джерела: <https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/>

</details>

---

<a id="t08-q40"></a>
### T08-Q40
*домен: визначено автоматично (у джерелі: AWS Database)*

A Developer recently created an Amazon DynamoDB table. The table has the following configuration:

The Developer attempted to add two items for userid “user0001” with unique timestamps and received an error for the second item stating: “The conditional request failed”.

What MUST the Developer do to resolve the issue?

- ▫️ Use the SDK to add the items
- ▫️ Update the table with a primary sort key for the timestamp attribute
- ▫️ Add a local secondary index (LSI) for the timestamp attribute
- ✅ Recreate the table with a composite key consisting of userid and timestamp

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Recreate the table with a composite key consisting of userid and timestamp»**

Первинний ключ DynamoDB буває двох типів:
- **Partition key** — унікальний атрибут, наприклад user ID. Хеш від нього визначає партицію. Два елементи не можуть мати однаковий partition key.
- **Composite key** = partition key + sort key. Наприклад, допис на форумі: partition — user ID, sort — час допису. Partition key може повторюватися, якщо sort key різний. Елементи з однаковим partition key зберігаються разом і впорядковані за sort key.

Таблиця має лише partition key, тож двох записів з однаковим `userid` бути не може. Ключову схему не змінити, тож таблицю треба створити наново з composite key `userid` + `timestamp`.

**❌ Неправильні варіанти:**
- *Update the table with sort key* — ключову схему наявної таблиці змінити неможливо.
- *Add an LSI* — основна таблиця однаково не прийме кілька записів з одним `userid`. До того ж LSI не додати до наявної таблиці.
- *Use the SDK* — не важливо, консоль, CLI чи SDK: обмеження в ключовій схемі.

Джерела: <https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/>

</details>

---

<a id="t09-q36"></a>
### T09-Q36
*домен: офіційна мітка*

A CRM application is hosted on Amazon EC2 instances with the database tier using DynamoDB. The customers have raised privacy and security concerns regarding sending and receiving data across the public internet.

As a developer associate, which of the following would you suggest as an optimal solution for providing communication between EC2 instances and DynamoDB without using the public internet?

- ▫️ Create a NAT Gateway to provide the necessary communication channel between EC2 instances and DynamoDB
- ▫️ The firm can use a virtual private network (VPN) to route all DynamoDB network traffic through their own corporate network infrastructure
- ▫️ Create an Internet Gateway to provide the necessary communication channel between EC2 instances and DynamoDB
- ✅ Configure VPC endpoints for DynamoDB that will provide required internal access without using public internet

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure VPC endpoints for DynamoDB that will provide required internal access without using public internet»**

З VPC endpoint для DynamoDB (gateway endpoint) запити до регіонального endpoint, наприклад `dynamodb.us-west-2.amazonaws.com`, йдуть приватним маршрутом усередині мережі Amazon і не виходять в інтернет. Ім'я endpoint те саме, тож застосунки на EC2 змінювати не треба. Доступ контролюють endpoint policies.

Детальніше: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/vpc-endpoints-dynamodb.html

**❌ Неправильні варіанти:**
- *VPN через корпоративну мережу* — можливо, але створює проблеми з пропускною здатністю й доступністю.
- *NAT Gateway* — дає інстансам у приватній підмережі вихід в інтернет; тут не потрібен, і трафік однаково йшов би через публічні endpoints.
- *Internet Gateway* — означає з'єднання через інтернет, а цього треба уникнути.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/vpc-endpoints-dynamodb.html> · <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html> · <https://docs.aws.amazon.com/vpc/latest/userguide/Carrier_Gateway.html>

</details>

---

<a id="t10-q02"></a>
### T10-Q02
*домен: визначено автоматично (у джерелі: AWS Database)*

An application writes items to an Amazon DynamoDB table. As the application scales to thousands of instances, calls to the DynamoDB API generate occasional ThrottlingException errors. The application is coded in a language that is incompatible with the AWS SDK.

What can be done to prevent the errors from occurring?

- ▫️ Pass API calls through Amazon API Gateway
- ✅ Add exponential backoff to the application logic
- ▫️ Send the items to DynamoDB through Amazon Kinesis Data Firehose
- ▫️ Use Amazon SQS as an API message bus

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add exponential backoff to the application logic»**

Повтори з exponential backoff — паузами, що щоразу довшають, — підвищують надійність застосунку. AWS SDK робить це автоматично; без SDK backoff реалізують вручну.

Інші способи уникнути throttling:
- рівномірно розподіляти читання і записи по таблиці, бо гаряча партиція погіршує продуктивність;
- кешувати: для статичних даних, які переважно читають, DAX або ElastiCache.

**❌ Неправильні варіанти:**
- *SQS as API message bus* — зайві витрати й складність.
- *API Gateway* — зайвий шар, коли достатньо backoff.
- *Kinesis Data Firehose → DynamoDB* — DynamoDB не є destination для Firehose.

Джерела: <https://docs.aws.amazon.com/general/latest/gr/api-retries.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-table-throttled/>

</details>

---

<a id="t12-q58"></a>
### T12-Q58
*домен: офіційна мітка*

An organization with high data volume workloads have successfully moved to DynamoDB after having many issues with traditional database systems. However, a few months into production, DynamoDB tables are consistently recording high latency.

As a Developer Associate, which of the following would you suggest to reduce the latency? (Select two)

- ✅ Use eventually consistent reads in place of strongly consistent reads whenever possible
- ✅ Consider using Global tables if your application is accessed by globally distributed users
- ▫️ Increase the request timeout settings, so the client gets enough time to complete the requests, thereby reducing retries on the system
- ▫️ Use DynamoDB Accelerator (DAX) for businesses with heavy write-only workloads
- ▫️ Reduce connection pooling, which keeps the connections alive even when user requests are not present, thereby, blocking the services

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use eventually consistent reads in place of strongly consistent reads whenever possible» + «Consider using Global tables if your application is accessed by globally distributed users»**

DynamoDB — key-value і документна БД із затримкою в межах кількох мілісекунд за будь-якого масштабу. Вона повністю керована, мультирегіональна, multi-master, з безпекою, бекапами й кешуванням.
- **Global tables.** Якщо користувачі по всьому світу, таблицю реплікують у потрібні регіони. Endpoint ближчий до клієнта, тож затримка суттєво менша.
- **Eventually consistent читання.** Якщо strong consistency не потрібна, eventually consistent дешевші й рідше мають високу затримку.

**❌ Неправильні варіанти:**
- *Increase request timeout* — навпаки, timeout зменшують: клієнт кидає повільний запит і надсилає новий, який зазвичай виконується швидше.
- *Reduce connection pooling* — навпаки: connection pooling, повторне використання з'єднань або фоновий трафік тримають внутрішні кеші «теплими» і затримку низькою.
- *DAX for write-only workloads* — DAX — кеш для навантажень з переважанням **читань**.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-high-latency/> · <https://aws.amazon.com/dynamodb/>

</details>

---
