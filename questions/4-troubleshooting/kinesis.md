# Kinesis / Data Firehose

**Домен 4 — Усунення несправностей та оптимізація** · питань: 4  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#kinesis) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q25"></a>
### T09-Q25
*домен: офіційна мітка*

A data analytics company is processing real-time Internet-of-Things (IoT) data via Kinesis Producer Library (KPL) and sending the data to a Kinesis Data Streams driven application. The application has halted data processing because of a ProvisionedThroughputExceeded exception.

Which of the following actions would help in addressing this issue? (Select two)

- ▫️ Use Kinesis enhanced fan-out for Kinesis Data Streams
- ✅ Configure the data producer to retry with an exponential backoff
- ✅ Increase the number of shards within your data streams to provide enough capacity
- ▫️ Use Amazon Kinesis Agent instead of Kinesis Producer Library (KPL) for sending data to Kinesis Data Streams
- ▫️ Use Amazon SQS instead of Kinesis Data Streams

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure the data producer to retry with an exponential backoff» + «Increase the number of shards within your data streams to provide enough capacity»**

Kinesis Data Streams приймає потоки даних із сотень тисяч джерел: кліки, логи, соцмережі. Як працює: https://aws.amazon.com/kinesis/data-streams/

Ємність потоку визначається кількістю шардів. Її можна перевищити пропускною здатністю або кількістю PUT-записів, і тоді запити відхиляються з `ProvisionedThroughputExceededException`.
- **Тимчасовий сплеск** — producer повторює запит з exponential backoff, і зрештою він проходить.
- **Стабільне зростання** — збільшити кількість шардів.

**❌ Неправильні варіанти:**
- *Kinesis Agent instead of KPL* — обмеження в ємності потоку, а не в способі надсилання.
- *SQS instead of Kinesis* — не вирішує проблему з ємністю потоку.
- *Enhanced fan-out* — допомагає кільком споживачам читати паралельно, а не запису. Детальніше: https://aws.amazon.com/kinesis/data-streams/faqs/

Джерела: <https://aws.amazon.com/kinesis/data-streams/> · <https://aws.amazon.com/kinesis/data-streams/faqs/>

</details>

---

<a id="t10-q01"></a>
### T10-Q01
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

An Amazon Kinesis Data Stream has recently been configured to receive data from sensors in a manufacturing facility. A consumer EC2 instance is configured to process the data every 48 hours and save processing results to an Amazon RedShift data warehouse. Testing has identified a large amount of data is missing. A review of monitoring logs has identified that the sensors are sending data correctly and the EC2 instance is healthy.

What is the MOST likely explanation for this issue?

- ▫️ Amazon Kinesis has too many shards provisioned
- ▫️ The EC2 instance is failing intermittently
- ▫️ Amazon RedShift is not suitable for storing streaming data
- ✅ Records are retained for 24 hours in the Kinesis Data Stream by default

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Records are retained for 24 hours in the Kinesis Data Stream by default»**

Потік Kinesis — упорядкована послідовність записів, які пишуть і читають у реальному часі, тож у шардах вони зберігаються тимчасово. Retention period — час від додавання запису до моменту, коли він стає недоступним. За замовчуванням 24 години, максимум 8 760 годин (365 днів). Змінюють його так:
- `IncreaseStreamRetentionPeriod` — збільшити, до 8 760 год;
- `DecreaseStreamRetentionPeriod` — зменшити, мінімум 24 год;
- `DescribeStream` — переглянути поточне значення.

Найімовірніше пояснення: записи старші за 24 години вже видалено.

**❌ Неправильні варіанти:**
- *Redshift not suitable* — Kinesis приймає потік, а EC2 може записувати оброблені дані куди завгодно, зокрема в Redshift.
- *EC2 failing intermittently* — за умовою моніторинг показує, що інстанс здоровий.
- *Too many shards* — більше шардів — це більша ємність, а не втрата даних.

Джерела: <https://docs.aws.amazon.com/streams/latest/dev/kinesis-extended-retention.html>

</details>

---

<a id="t11-q25"></a>
### T11-Q25
*домен: офіційна мітка*

```json
A data analytics company processes Internet-of-Things (IoT) data using Amazon Kinesis. The development team has noticed that the IoT data feed into Kinesis experiences periodic spikes. The PutRecords API call occasionally fails and the logs show that the failed call returns the response shown below:
HTTP/1.1 200 OK
x-amzn-RequestId: <RequestId>
Content-Type: application/x-amz-json-1.1
Content-Length: <PayloadSizeBytes>
Date: <Date>
{
    "FailedRecordCount": 2,
    "Records": [
        {
            "SequenceNumber": "49543463076548007577105092703039560359975228518395012686",
            "ShardId": "shardId-000000000000"
        },
        {
            "ErrorCode": "ProvisionedThroughputExceededException",
            "ErrorMessage": "Rate exceeded for shard shardId-000000000001 in stream exampleStreamName under account 111111111111."
        },
        {
            "ErrorCode": "InternalFailure",
            "ErrorMessage": "Internal service failure."
        }
    ]
}
As an AWS Certified Developer Associate, which of the following options would you recommend to address this use case? (Select two)
```

- ✅ Decrease the frequency or size of your requests
- ▫️ Merge the shards to decrease the number of shards in the stream
- ✅ Use an error retry and exponential backoff mechanism
- ▫️ Decrease the number of KCL consumers
- ▫️ Increase the frequency or size of your requests

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Decrease the frequency or size of your requests» + «Use an error retry and exponential backoff mechanism»**

`PutRecords` записує кілька записів за один виклик. Ліміти:
- до 500 записів у запиті;
- запис — до 1 MiB, запит — до 5 MiB з partition keys;
- шард приймає до 1 000 записів і 1 MiB на секунду.

Відповідь містить і успішні, і невдалі записи: збій одного не зупиняє обробку решти, тож порядок не гарантовано. Невдалий запис має `ErrorCode` — `ProvisionedThroughputExceededException` або `InternalFailure`. Перший означає, що запитів забагато або дані завеликі для наявної ємності.

Що робити:
- решардинг — збільшити кількість шардів;
- зменшити частоту чи розмір запитів;
- рівномірно розподіляти записи по шардах;
- повторювати з exponential backoff.

**❌ Неправильні варіанти:**
- *Merge shards* — ємність зменшиться.
- *Increase frequency or size* — ще більше навантаження.
- *Decrease KCL consumers* — помилку дає запис producers, а не читання.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/kinesis-readprovisionedthroughputexceeded/> · <https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecords.html>

</details>

---

<a id="t12-q34"></a>
### T12-Q34
*домен: офіційна мітка*

A development team is configuring Kinesis Data Streams for ingesting real-time data from various appliances. The team has declared a shard capacity of one to test the configuration.

What happens if the capacity limits of an Amazon Kinesis data stream are exceeded while the data producer adds data to the data stream?

- ▫️ Contact AWS support to request an increase in the number of shards
- ▫️ Data is lost unless the partition key of the data records is changed in order to write data to a different shard in the stream
- ▫️ The put data calls will be rejected with a AccessDeniedException exception once the limit is reached
- ✅ The put data calls will be rejected with a ProvisionedThroughputExceeded exception

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The put data calls will be rejected with a ProvisionedThroughputExceeded exception»**

Ємність потоку Kinesis визначається кількістю шардів. Її можна перевищити пропускною здатністю або кількістю PUT-записів, і тоді запити відхиляються з `ProvisionedThroughputExceededException`.
- **Тимчасовий сплеск** — повтори з боку producer зрештою пройдуть.
- **Стабільне зростання** — треба збільшити кількість шардів.

**❌ Неправильні варіанти:**
- *AccessDeniedException* — це помилка дозволів, а дані до перевищення приймалися.
- *Data lost unless partition key changes* — зміна partition key не додасть шардів; шард тут один.
- *Contact Support for more shards* — шарди додають самі, наприклад через `UpdateShardCount`.

</details>

---
