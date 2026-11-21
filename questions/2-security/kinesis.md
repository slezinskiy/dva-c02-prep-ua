# Kinesis / Data Firehose

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#kinesis) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t12-q47"></a>
### T12-Q47
*домен: офіційна мітка*

An analytics company is using Kinesis Data Streams (KDS) to process automobile health-status data from the taxis managed by a taxi ride-hailing service. Multiple consumer applications are using the incoming data streams and the engineers have noticed a performance lag for the data delivery speed between producers and consumers of the data streams.

As a Developer Associate, which of the following options would you suggest for improving the performance for the given use-case?

- ▫️ Swap out Kinesis Data Streams with SQS Standard queues
- ▫️ Swap out Kinesis Data Streams with Kinesis Data Firehose
- ▫️ Swap out Kinesis Data Streams with SQS FIFO queues
- ✅ Use Enhanced Fanout feature of Kinesis Data Streams

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Enhanced Fanout feature of Kinesis Data Streams»**

За замовчуванням 2 MB/с на шард **спільні** для всіх застосунків, що читають потік. **Enhanced fan-out** дає кожному зареєстрованому споживачу **власні** 2 MB/с на шард, і ця пропускна здатність масштабується разом із кількістю шардів. Використовують, коли кілька споживачів читають потік паралельно.

Детальніше: https://aws.amazon.com/blogs/aws/kds-enhanced-fanout/

**❌ Неправильні варіанти:**
- *Kinesis Data Firehose* — доставляє дані в S3, Redshift, OpenSearch чи Splunk; застосунки не можуть читати з нього, як з потоку.
- *SQS Standard* — кожне повідомлення обробляє один споживач, тож кілька застосунків не прочитають той самий потік.
- *SQS FIFO* — так само.

Джерела: <https://aws.amazon.com/blogs/aws/kds-enhanced-fanout/> · <https://aws.amazon.com/kinesis/data-streams/faqs/>

</details>

---
