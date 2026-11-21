# Kinesis / Data Firehose

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 10  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#kinesis) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q45"></a>
### T01-Q45
*домен: офіційна мітка*

One of your Kinesis Stream is experiencing increased traffic due to a sale day. Therefore your Kinesis Administrator has split shards and thus you went from having 6 shards to having 10 shards in your Kinesis Stream. Your consuming application is running a KCL-based application on EC2 instances.

What is the maximum number of EC2 instances that can be deployed to process the shards?

- ▫️ 20
- ▫️ 1
- ✅ 10
- ▫️ 6

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «10»**

Kinesis data stream складається з шардів. Шард — унікально ідентифікована послідовність записів, і кожен дає фіксовану одиницю потужності. Кожен екземпляр KCL-застосунку обробляє шарди через workers, і в кожен момент шард прив'язаний (через lease) лише до одного worker'а. Тут worker — EC2-інстанс, тож на шард може припадати щонайбільше один інстанс. 10 шардів = максимум 10 інстансів.

**❌ Неправильні варіанти:** *1*, *6*, *20* суперечать цьому правилу.

</details>

---

<a id="t03-q16"></a>
### T03-Q16
*домен: офіційна мітка*

You have an Amazon Kinesis Data Stream with 10 shards, and from the metrics, you are well below the throughput utilization of 10 MB per second to send data. You send 3 MB per second of data and yet you are receiving ProvisionedThroughputExceededException errors frequently.

What is the likely cause of this?

- ✅ The partition key that you have selected isn't distributed enough
- ▫️ Metrics are slow to update
- ▫️ You have too many shards
- ▫️ The data retention period is too long

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The partition key that you have selected isn't distributed enough»**

Kinesis data stream складається з шардів; кожен дає фіксовану одиницю потужності. За partition key кожного запису Kinesis визначає, у який шард його покласти. Якщо partition key розподілений погано, дані скупчуються в кількох «гарячих» шардах, а решта простоює — звідси `ProvisionedThroughputExceededException`, хоча сумарно потужності вистачає. Метрики допомагають знайти гарячі й холодні шарди: гарячі можна розщепити (split), холодні — злити (merge).

**❌ Неправильні варіанти:**
- *Metrics are slow to update* — дистрактор.
- *You have too many shards* — тоді була б інша помилка (LimitExceededException).
- *The data retention period is too long* — термін зберігання (до 365 днів) на цю помилку не впливає.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html> · <https://docs.aws.amazon.com/streams/latest/dev/kinesis-using-sdk-java-resharding-strategies.html>

</details>

---

<a id="t04-q05"></a>
### T04-Q05
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A monitoring application that keeps track of a large eCommerce website uses Amazon Kinesis for data ingestion. During periods of peak data rates, the producers are not making best use of the available shards.
What step will allow the producers to better utilize the available shards and increase write throughput to the Kinesis data stream?

- ▫️ Create an SQS queue and decouple the producers from the Kinesis data stream
- ✅ Install the Kinesis Producer Library (KPL) for ingesting data into the stream
- ▫️ Ingest multiple records into the stream in a single call using BatchWriteItem
- ▫️ Increase the shard count of the stream using UpdateShardCount

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install the Kinesis Producer Library (KPL) for ingesting data into the stream»**

Producer — застосунок, що записує дані в Kinesis data stream. KPL спрощує розробку producers і дає високу пропускну здатність запису. Він стоїть між кодом застосунку і API Kinesis і:
- пише в один чи кілька стрімів з автоматичними повторами;
- збирає записи й надсилає їх `PutRecords` у кілька шардів за один запит;
- агрегує дрібні записи в більші payload'и;
- інтегрується з KCL, який розагреговує записи на боці споживача;
- публікує метрики в CloudWatch.

За умовою шардів достатньо, але producers погано їх використовують — KPL це вирішує.

**❌ Неправильні варіанти:**
- *SQS queue to decouple producers* — проблема в тому, як producers пишуть у шарди.
- *Increase the shard count* — шардів уже достатньо.
- *BatchWriteItem* — це API DynamoDB, а не Kinesis.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-kpl.html>

</details>

---

<a id="t04-q44"></a>
### T04-Q44
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A solution requires a serverless service for receiving streaming data and loading it directly into an Amazon Elasticsearch datastore. Which AWS service would be suitable for this requirement?

- ▫️ Amazon Kinesis Data Streams
- ▫️ Amazon Simple Queue Service (SQS)
- ▫️ Amazon Kinesis Data Analytics
- ✅ Amazon Kinesis Data Firehose

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Kinesis Data Firehose»** (зараз — Amazon Data Firehose)

Firehose — найпростіший спосіб завантажити потокові дані в сховища та аналітичні інструменти: він приймає, трансформує і доставляє дані в S3, Redshift, Elasticsearch (тепер OpenSearch) і Splunk майже в реальному часі. Сервіс повністю керований, масштабується автоматично, може батчити, стискати й шифрувати дані перед доставкою.

**❌ Неправильні варіанти:**
- *Kinesis Data Streams* — потрібні власні consumers (EC2 або Lambda); напряму в сховище не пише.
- *Kinesis Data Analytics* — SQL-аналітика потокових даних.
- *Amazon SQS* — черга повідомлень; знадобилися б сервери, що записують дані в Elasticsearch.

Джерела: <https://aws.amazon.com/kinesis/data-firehose/faqs/>

</details>

---

<a id="t05-q64"></a>
### T05-Q64
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company needs to ingest several terabytes of data every hour from a large number of distributed sources. The messages are delivered continually 24 hrs a day. Messages must be delivered in real time for security analysis and live operational dashboards.

Which approach will meet these requirements?

- ✅ Use Amazon Kinesis Data Streams with Kinesis Client Library to ingest and deliver messages
- ▫️ Send the messages to an Amazon SQS queue, then process the messages by using a fleet of Amazon EC2 instances
- ▫️ Use the Amazon S3 API to write messages to an S3 bucket, then process the messages by using Amazon RedShift
- ▫️ Use AWS Data Pipeline to automate the movement and transformation of data

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon Kinesis Data Streams with Kinesis Client Library to ingest and deliver messages»**

Kinesis Data Streams збирає й обробляє великі потоки записів у реальному часі. Застосунки-обробники читають записи з потоку і можуть використовувати Kinesis Client Library (KCL) та працювати на EC2. Оброблені дані йдуть на дашборди, в алерти, у динамічне ціноутворення чи інші сервіси AWS. Великий потік даних у реальному часі — ідеальний сценарій для KDS.

**❌ Неправильні варіанти:**
- *SQS + EC2 fleet* — SQS роз'єднує компоненти й не призначена для потокових даних. Буде дорожче і з більшою затримкою, а розмір повідомлень може стати проблемою.
- *S3 + Redshift* — Redshift — сховище даних для аналітики; він не обробляє повідомлення з S3.
- *AWS Data Pipeline* — у питанні мова не про трансформацію, а про прийом і обробку потоку в реальному часі. ℹ️ Data Pipeline у maintenance mode.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/introduction.html>

</details>

---

<a id="t07-q36"></a>
### T07-Q36
*домен: офіційна мітка*

A leading financial services company offers data aggregation services for Wall Street trading firms. The company bills its clients based on per unit of clickstream data provided to the clients. As the company operates in a regulated industry, it needs to have the same ordered clickstream data available for auditing within a window of 7 days.

As a Developer Associate, which of the following AWS services do you think provides the ability to run the billing process and auditing process on the given clickstream data in the same order?

- ▫️ Amazon SQS
- ▫️ AWS Kinesis Data Firehose
- ✅ AWS Kinesis Data Streams
- ▫️ AWS Kinesis Data Analytics

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Kinesis Data Streams»**

KDS — масштабований і надійний сервіс потокових даних у реальному часі. Він зберігає порядок записів і дозволяє кільком застосункам читати й повторно читати їх у тому самому порядку. KCL передає всі записи одного partition key одному обробнику.

KDS рекомендують, коли записи треба прочитати в тому самому порядку через кілька годин. Наприклад, застосунок аудиту відстає від білінгу на кілька годин. Записи зберігаються 24 години за замовчуванням, і цей термін можна збільшити до 365 днів. Якщо налаштувати 7 днів, аудит може відставати до 7 днів.

Детальніше: https://aws.amazon.com/kinesis/data-streams/faqs/

**❌ Неправильні варіанти:**
- *Kinesis Data Firehose* — завантажує потоки в сховища (S3, Redshift, OpenSearch, Splunk); повторного читання немає.
- *Kinesis Data Analytics* — SQL- або Java-аналітика потоків, а не їх зберігання для кількох споживачів. Зараз це Managed Service for Apache Flink.
- *SQS* — повідомлення не можуть прочитати кілька споживачів у тому самому порядку через кілька годин. Standard черга — best-effort порядок і at-least-once; FIFO — exactly-once і строгий порядок.

Джерела: <https://aws.amazon.com/kinesis/data-streams/faqs/> · <https://aws.amazon.com/kinesis/data-firehose/faqs/> · <https://aws.amazon.com/kinesis/data-analytics/faqs/>

</details>

---

<a id="t09-q44"></a>
### T09-Q44
*домен: офіційна мітка*

A developer working with EC2 Windows instance has installed Kinesis Agent for Windows to stream JSON-formatted log files to Amazon Simple Storage Service (S3) via Amazon Kinesis Data Firehose. The developer wants to understand the sink type capabilities of Kinesis Firehose.

Which of the following sink types is NOT supported by Kinesis Firehose.

- ✅ Amazon ElastiCache with Amazon S3 as backup
- ▫️ Amazon Redshift with Amazon S3
- ▫️ Amazon Simple Storage Service (Amazon S3) as a direct Firehose destination
- ▫️ Amazon Elasticsearch Service (Amazon ES) with optionally backing up data to Amazon S3

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon ElastiCache with Amazon S3 as backup»**

Питання на пошук **непідтримуваного** призначення. Kinesis Data Firehose доставляє потокові дані в S3, Redshift, Elasticsearch (тепер OpenSearch), Splunk та інші сервіси без власного коду. ElastiCache не є призначенням Firehose.

**❌ Інші варіанти підтримуються:**
- *Amazon ES/OpenSearch + optional S3 backup* — дані йдуть у кластер і паралельно, за бажанням, у S3.
- *S3 direct* — дані йдуть у бакет; якщо ввімкнена трансформація, сирі дані можна зберегти в інший бакет.
- *Redshift via S3* — спочатку дані потрапляють у S3, потім Firehose виконує `COPY` у Redshift.

Детальніше: https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html

</details>

---

<a id="t10-q20"></a>
### T10-Q20
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A monitoring application that keeps track of a large eCommerce website uses Amazon Kinesis for data ingestion. During periods of peak data rates, the Kinesis stream cannot keep up with the incoming data.
What step will allow Kinesis data streams to accommodate the traffic during peak hours?

- ▫️ 1. Ingest multiple records into the stream in a single call using PutRecords
- ✅ Increase the shard count of the stream using UpdateShardCount
- ▫️ Create an SQS queue and decouple the producers from the Kinesis data stream
- ▫️ Install the Kinesis Producer Library (KPL) for ingesting data into the stream

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the shard count of the stream using UpdateShardCount»**

`UpdateShardCount` змінює кількість шардів потоку асинхронно:
- статус потоку стає `UPDATING`, а після завершення — знову `ACTIVE`;
- це може тривати кілька хвилин, і в цей час можна читати й писати;
- Kinesis ділить або об'єднує окремі шарди, тож тимчасові шарди теж рахуються в ліміт акаунта;
- найшвидше, якщо цільова кількість кратна 25% поточної.

Ліміти за замовчуванням:
- не більше 10 масштабувань за 24 години на потік;
- не більше ніж удвічі вгору і не менше ніж наполовину вниз;
- обмеження на кількість шардів у потоці та в акаунті.

Потік не встигає приймати дані, бо не вистачає шардів, тож їх треба додати.

ℹ️ Альтернатива — режим **on-demand**, де шарди масштабуються автоматично.

**❌ Неправильні варіанти:**
- *Install KPL* — пришвидшує producers, але потоку однаково бракує ємності.
- *SQS to decouple producers* — Kinesis так не роз'єднують; йому просто потрібні шарди.
- *PutRecords* — потік уже перевантажений; потрібно більше шардів, а не більше даних за виклик.

Джерела: <https://docs.aws.amazon.com/kinesis/latest/APIReference/API_UpdateShardCount.html>

</details>

---

<a id="t10-q56"></a>
### T10-Q56
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

An application uses Amazon Kinesis Data Streams to ingest and process large streams of data records in real time. Amazon EC2 instances consume and process the data using the Amazon Kinesis Client Library (KCL). The application handles the failure scenarios and does not require standby workers. The application reports that a specific shard is receiving more data than expected. To adapt to the changes in the rate of data flow, the “hot” shard is resharded.

Assuming that the initial number of shards in the Kinesis data stream is 6, and after resharding the number of shards increased to 8, what is the maximum number of EC2 instances that can be deployed to process data from all the shards?

- ✅ 8
- ▫️ 12
- ▫️ 6
- ▫️ 1

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «8»**

У KCL кількість інстансів не повинна перевищувати кількість шардів, хіба що як резерв на випадок збою. Кожен шард обробляє рівно один worker з одним record processor, тож кілька інстансів на один шард не потрібні. Один worker може обробляти будь-яку кількість шардів. Шардів 8, тож максимум — 8 інстансів.

**❌ Неправильні варіанти:**
- *6* — це не максимум.
- *12* — інстансів більше, ніж шардів, і зайві простоюватимуть.
- *1* — це не максимум.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-scaling.html>

</details>

---

<a id="t10-q60"></a>
### T10-Q60
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer has setup an Amazon Kinesis Data Stream with 6 shards to ingest a maximum of 2000 records per second. An AWS Lambda function has been configured to process these records. In which order will these records be processed?

- ▫️ Lambda will receive each record in the exact order it was placed into the stream
- ▫️ The Developer can select exact order or reverse order using the GetRecords API
- ✅ Lambda will receive each record in the exact order it was placed into the shard. There is no guarantee of order across shards
- ▫️ Lambda will receive each record in the reverse order it was placed into the stream

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Lambda will receive each record in the exact order it was placed into the shard. There is no guarantee of order across shards»**

KDS приймає дані від producers і зберігає їх у шардах, а споживачі, тут Lambda, їх обробляють. Шард — append-only лог і одиниця пропускної здатності: записи в ньому впорядковані за часом надходження. Порядок гарантовано **в межах шарда**, але не між шардами.

**❌ Неправильні варіанти:**
- *Exact order across the stream* — шардів кілька, тож загального порядку немає.
- *Reverse order* — у межах шарда порядок прямий.
- *Choose order via GetRecords* — порядок через `GetRecords` не вибирають.

Джерела: <https://aws.amazon.com/kinesis/data-streams/getting-started/> · <https://aws.amazon.com/kinesis/data-streams/faqs/>

</details>

---
