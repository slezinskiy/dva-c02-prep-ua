# SQS / SNS / EventBridge / SES

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 27  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q31"></a>
### T01-Q31
*домен: офіційна мітка*

You are using AWS SQS FIFO queues to get the ordering of messages on a per user_id basis.

As a developer, which message parameter should you set the value of user_id to guarantee the ordering?

- ▫️ MessageOrderId
- ✅ MessageGroupId
- ▫️ MessageHash
- ▫️ MessageDeduplicationId

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «MessageGroupId»**

FIFO-черги SQS призначені для випадків, коли важливий порядок операцій і подій. Message group ID — тег, що відносить повідомлення до певної групи. Повідомлення однієї групи завжди обробляються по одному, у строгому порядку всередині групи (а повідомлення різних груп можуть оброблятися не по порядку). Тому для порядку в межах user_id задають `MessageGroupId = user_id`.

**❌ Неправильні варіанти:**
- *MessageDeduplicationId* — токен для дедуплікації: повідомлення з тим самим ID протягом 5-хвилинного інтервалу приймаються, але не доставляються повторно. На порядок не впливає.
- *MessageOrderId*, *MessageHash* — вигадані параметри.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/using-messagegroupid-property.html>

</details>

---

<a id="t02-q07"></a>
### T02-Q07
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

An application needs to generate SMS text messages and emails for a large number of subscribers. Which AWS service can be used to send these messages to customers?

- ▫️ Amazon SWF
- ✅ Amazon SNS
- ▫️ Amazon SQS
- ▫️ Amazon SES

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon SNS»**

Amazon SNS керує доставкою повідомлень підписникам. Є два типи клієнтів: publishers (producers) і subscribers (consumers). Publisher асинхронно надсилає повідомлення в topic — логічну точку доступу. Підписники (веб-сервери, email, черги SQS, Lambda) отримують його за підтримуваними протоколами: SQS, HTTP/S, email, **SMS**, Lambda.

**❌ Неправильні варіанти:**
- *Amazon SES* — лише email, без SMS.
- *Amazon SQS* — черга для розв'язування компонентів.
- *Amazon SWF* — оркестрація багатокрокових workflow.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/welcome.html>

</details>

---

<a id="t02-q25"></a>
### T02-Q25
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

To reduce the cost of API actions performed on an Amazon SQS queue, a Developer has decided to implement long polling. Which of the following modifications should the Developer make to the API actions?

- ✅ Set the ReceiveMessage API with a WaitTimeSeconds of 20
- ▫️ Set the ReceiveMessage API with a VisibilityTimeout of 30
- ▫️ Set the SetQueueAttributes with a MessageRetentionPeriod of 60
- ▫️ Set the SetQueueAttributes API with a DelaySeconds of 20

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set the ReceiveMessage API with a WaitTimeSeconds of 20»**

За замовчуванням SQS використовує short polling: перевіряє лише частину серверів (за зваженим випадковим розподілом) і повертає повідомлення лише з них. Тому окремий запит може повернути не всі повідомлення, а то й порожню відповідь.

Long polling діє, коли час очікування `ReceiveMessage` більший за 0 (максимум 20 с). Він зменшує кількість порожніх і хибно-порожніх відповідей, а отже й витрати, і водночас повідомлення приходять одразу, щойно з'являються. Long polling вмикається, якщо:
- виклик `ReceiveMessage` задає `WaitTimeSeconds` > 0;
- або виклик не задає `WaitTimeSeconds`, але атрибут черги `ReceiveMessageWaitTimeSeconds` > 0.

**❌ Неправильні варіанти:**
- *DelaySeconds of 20* — налаштовує delay queue (затримку доставки).
- *VisibilityTimeout of 30* — час, на який отримане повідомлення стає невидимим.
- *MessageRetentionPeriod of 60* — як довго повідомлення зберігаються в черзі.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html>

</details>

---

<a id="t03-q27"></a>
### T03-Q27
*домен: офіційна мітка*

DevOps engineers are developing an order processing system where notifications are sent to a department whenever an order is placed for a product. The system also pushes identical notifications of the new order to a processing module that would allow EC2 instances to handle the fulfillment of the order. In the case of processing errors, the messages should be allowed to be re-processed at a later stage. The order processing system should be able to scale transparently without the need for any manual or programmatic provisioning of resources.

Which of the following solutions can be used to address this use-case in the most cost-efficient way?

- ✅ SNS + SQS
- ▫️ SQS + SES
- ▫️ SNS + Kinesis
- ▫️ SNS + Lambda

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «SNS + SQS»**

SNS забезпечує фільтрацію повідомлень і fan-out до великої кількості підписників (функцій, черг, систем), а також push, SMS і email кінцевим користувачам. SQS — керована черга: standard (максимальна пропускна здатність, at-least-once) і FIFO (exactly-once, строгий порядок). SQS масштабується прозоро, без провізіонінгу.

Разом SNS і SQS дають fan-out: повідомлення «проштовхується» кільком підписникам, polling не потрібен, обробка паралельна й асинхронна, а SQS додає можливість повторної обробки і DLQ.

**❌ Неправильні варіанти:**
- *SNS + Kinesis* — шарди треба провізіонувати (або платити за on-demand); для цього випадку неекономно.
- *SNS + Lambda* — EC2-інстанси не можуть «опитувати» Lambda.
- *SQS + SES* — повідомлення треба обробити двічі (сповіщення і виконання замовлення), а з черги SQS його читає лише один consumer.

Джерела: <https://aws.amazon.com/sns/> · <https://aws.amazon.com/getting-started/tutorials/send-fanout-event-notifications/>

</details>

---

<a id="t03-q33"></a>
### T03-Q33
*домен: офіційна мітка*

A senior cloud engineer designs and deploys online fraud detection solutions for credit card companies processing millions of transactions daily. The Elastic Beanstalk application sends files to Amazon S3 and then sends a message to an Amazon SQS queue containing the path of the uploaded file in S3. The engineer wants to postpone the delivery of any new messages to the queue for at least 10 seconds.

Which SQS feature should the engineer leverage?

- ▫️ Use visibility timeout parameter
- ✅ Use DelaySeconds parameter
- ▫️ Implement application-side delay
- ▫️ Enable LongPolling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use DelaySeconds parameter»**

Delay queues відкладають доставку нових повідомлень на кілька секунд (наприклад, коли consumer'у потрібен додатковий час). Повідомлення лишається невидимим для consumers протягом затримки: від 0 (за замовчуванням) до 15 хвилин.

**❌ Неправильні варіанти:**
- *Application-side delay* — ненадійно: застосунок може впасти до відправлення, і повідомлення загубиться.
- *Visibility timeout* — період, протягом якого отримане повідомлення не бачать інші consumers (30 с за замовчуванням, від 0 до 12 год). Доставку нових повідомлень не відкладає.
- *Long polling* — зменшує кількість порожніх відповідей, але доставку не відкладає.

</details>

---

<a id="t03-q53"></a>
### T03-Q53
*домен: офіційна мітка*

> ⚠️ **SQS max = 1 MiB (оновлено у 2025).** → [детальніше](../../06-contradictions-and-traps.md#c10)

Your company has been hired to build a resilient mobile voting app for an upcoming music award show that expects to have 5 to 20 million viewers. The mobile voting app will be marketed heavily months in advance so you are expected to handle millions of messages in the system. You are configuring Amazon Simple Queue Service (SQS) queues for your architecture that should receive messages from 20 KiB to 200 KiB.

Is it possible to send these messages to SQS?

- ▫️ No, the max message size is 64 KiB
- ▫️ Yes, the max message size is 512 KiB
- ▫️ No, the max message size is 128 KiB
- ✅ Yes, the max message size is 1024 KiB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Yes, the max message size is 1024 KiB»**

Мінімальний розмір повідомлення SQS — 1 байт, максимальний — 1 048 576 байт (1024 KiB = 1 MiB; ліміт діє з 08.2025, раніше був 256 KiB). Повідомлення 20–200 KiB проходять.

**❌ Неправильні варіанти:** *512 KB*, *128 KB*, *64 KB* — неправильні ліміти.

</details>

---

<a id="t03-q60"></a>
### T03-Q60
*домен: офіційна мітка*

You work as a developer doing contract work for the government on AWS gov cloud. Your applications use Amazon Simple Queue Service (SQS) for its message queue service. Due to recent hacking attempts, security measures have become stricter and require you to store data in encrypted queues.

Which of the following steps can you take to meet your requirements without making changes to the existing code?

- ▫️ Use Client side encryption
- ▫️ Use Secrets Manager
- ✅ Enable SQS KMS encryption
- ▫️ Use the SSL endpoint

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable SQS KMS encryption»**

Server-side encryption (SSE) захищає вміст повідомлень у черзі ключами з AWS KMS. Data keys, що шифрують повідомлення, теж зашифровані й зберігаються разом із даними. Шифрування доступне для standard і FIFO черг і не потребує змін коду.

**❌ Неправильні варіанти:**
- *Use the SSL endpoint* — шифрує лише при передачі, а потрібне at rest.
- *Use client-side encryption* — вимагає змін у коді.
- *Use Secrets Manager* — для зберігання й ротації секретів, а не для шифрування черг.

</details>

---

<a id="t04-q32"></a>
### T04-Q32
*домен: визначено автоматично (у джерелі: —)*

> ⚠️ **1 GB → S3 + Extended Client (актуально).** → [детальніше](../../06-contradictions-and-traps.md#c10)

A developer is creating a multi-tier web application. The front-end will place messages in an Amazon SQS queue for the back-end to process. Each job includes a file that is 1GB in size. What MUST the developer do to ensure this works as expected?

- ▫️ Create a FIFO queue that supports large files
- ▫️ Store the large files in DynamoDB and use the SQS Extended Client Library for Java to manage SQS messages
- ▫️ Increase the maximum message size of the queue from 256KB to 1GB
- ✅ Store the large files in Amazon S3 and use the SQS Extended Client Library for Java to manage SQS messages

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Store the large files in Amazon S3 and use the SQS Extended Client Library for Java to manage SQS messages»**

S3 разом з Amazon SQS Extended Client Library for Java дає змогу працювати з повідомленнями до 2 GB. Бібліотека вміє:
- зберігати повідомлення в S3 завжди або лише коли вони більші за ліміт;
- надсилати повідомлення з посиланням на об'єкт в S3;
- отримувати й видаляти відповідний об'єкт з S3.

У банку сказано, що SQS приймає до 256 KB. **З 08.2025 ліміт — 1 MiB**, але файли по 1 GB однаково йдуть через S3 + Extended Client.

**❌ Неправильні варіанти:**
- *Increase the maximum message size to 1 GB* — неможливо (максимум 1 MiB).
- *Store the files in DynamoDB + Extended Client* — бібліотека працює з S3.
- *FIFO queue that supports large files* — у FIFO той самий ліміт розміру.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-s3-messages.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/working-java-example-using-s3-for-large-sqs-messages.html>

</details>

---

<a id="t04-q35"></a>
### T04-Q35
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A mobile application runs as a serverless application on AWS. A Developer needs to create a push notification feature that sends periodic message to subscribers. How can the Developer send the notification from the application?

- ▫️ Publish a notification to Amazon CloudWatch Events
- ▫️ Publish a message to an Amazon SWF Workflow
- ▫️ Publish a message to an Amazon SQS Queue
- ✅ Publish a notification to an Amazon SNS Topic

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Publish a notification to an Amazon SNS Topic»**

SNS надсилає push-сповіщення в мобільні застосунки (спливаючі повідомлення, бейджі, звуки) і на десктопи через ADM, APNs (iOS, macOS), Baidu, FCM, MPNS, WNS. Застосунок публікує сповіщення в SNS-топік, а SNS доставляє його підписникам.

**❌ Неправильні варіанти:**
- *Amazon SQS Queue* — черга повідомлень, а не сервіс сповіщень.
- *Amazon CloudWatch Events* — не надсилає сповіщення користувачам мобільних застосунків.
- *Amazon SWF Workflow* — оркестрація workflow.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-how-user-notifications-work.html>

</details>

---

<a id="t04-q56"></a>
### T04-Q56
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

An application collects data from sensors in a manufacturing facility. The data is stored in an Amazon SQS Standard queue by an AWS Lambda function and an Amazon EC2 instance processes the data and stores it in an Amazon RedShift data warehouse. A fault in the sensors’ software is causing occasional duplicate messages to be sent. Timestamps on the duplicate messages show they are generated within a few seconds of the primary message.

How can a Developer prevent duplicate data being stored in the data warehouse?

- ▫️ Send a ChangeMessageVisibility call with VisibilityTimeout set to 30 seconds after the receipt of every message from the queue
- ✅ Use a FIFO queue and configure the Lambda function to add a message deduplication token to the message body
- ▫️ Use a FIFO queue and configure the Lambda function to add a message group ID to the messages generated by each individual sensor
- ▫️ Configure a redrive policy, specify a destination Dead-Letter queue, and set the maxReceiveCount to 1

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a FIFO queue and configure the Lambda function to add a message deduplication token to the message body»**

FIFO-черги призначені для випадків, коли критичний порядок або дублікати неприпустимі. Порядок забезпечується в межах message group ID: повідомлення однієї групи надсилаються й отримуються строго по порядку, різних груп — ні. Group ID обов'язковий. Для дедуплікації потрібен **message deduplication ID** (токен): повідомлення з тим самим токеном протягом 5 хвилин відкидаються, тож EC2 не обробить дублікати.

**❌ Неправильні варіанти:**
- *FIFO + message group ID per sensor* — group ID задає порядок, а не дедуплікацію.
- *ChangeMessageVisibility 30 s* — лише ховає повідомлення на час обробки; дублікати з іншими ID все одно пройдуть.
- *Redrive policy + DLQ with maxReceiveCount 1* — без FIFO і deduplication ID дублікати однаково потраплять у чергу; redrive — для повідомлень, що не обробилися.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/using-messagededuplicationid-property.html>

</details>

---

<a id="t05-q29"></a>
### T05-Q29
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Для Lambda+SQS реальний параметр — BatchSize event source mapping.** → [детальніше](../../07-outdated-and-wrong-answers.md#d09)

A company runs a decoupled application that uses an Amazon SQS queue. The messages are processed by an AWS Lambda function. The function is not keeping up with the number of messages in the queue. A developer noticed that though the application can process multiple messages per invocation, it is only processing one at a time.

How can the developer configure the application to process messages more efficiently?

- ✅ Call the ReceiveMessage API to set MaxNumberOfMessages to a value greater than the default of 1.
- ▫️ Call the ReceiveMessage API to set MaximumMessageSize to a value greater than the default of 1.
- ▫️ Call the ChangeMessageVisibility API for the queue and set MessageRetentionPeriod to a value greater than the default of 1.
- ▫️ Call the SetQueueAttributes API for the queue and set MaxNumberOfMessages to a value greater than the default of 1.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Call the ReceiveMessage API to set MaxNumberOfMessages to a value greater than the default of 1.»**

`ReceiveMessage` повертає до 10 повідомлень із черги. `MaxNumberOfMessages` задає максимум повідомлень у відповіді (може прийти менше): від 1 до 10, за замовчуванням 1. Якщо збільшити значення, застосунок за один виклик обробить більше повідомлень, і це ефективніше.

**❌ Неправильні варіанти:**
- *MaximumMessageSize* — максимальний розмір повідомлення в байтах, понад який SQS його відхиляє.
- *ChangeMessageVisibility + MessageRetentionPeriod* — ChangeMessageVisibility лише змінює visibility timeout конкретного повідомлення.
- *SetQueueAttributes + MaxNumberOfMessages* — це параметр `ReceiveMessage`, а не атрибут черги.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html>

</details>

---

<a id="t05-q62"></a>
### T05-Q62
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

Messages produced by an application must be pushed to multiple Amazon SQS queues. What is the BEST solution for this requirement?

- ▫️ Create an Amazon SWF workflow that receives the messages and pushes them to multiple SQS queues
- ▫️ Create and AWS Step Functions state machine that uses multiple Lambda functions to process and push the messages into multiple SQS queues
- ▫️ Publish the messages to an Amazon SQS queue and configure an AWS Lambda function to duplicate the message into multiple queues
- ✅ Publish the messages to an Amazon SNS topic and subscribe each SQS queue to the topic

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Publish the messages to an Amazon SNS topic and subscribe each SQS queue to the topic»**

Це патерн fan-out. SNS надсилає повідомлення багатьом підписникам за моделлю push, без опитування. Якщо підписати SQS-черги на топік, кожне опубліковане повідомлення потрапить у кожну чергу. Воно надходить як JSON-документ із subject, текстом і метаданими.

**❌ Неправильні варіанти:**
- *SQS + Lambda duplicating to multiple queues* — неефективно; SNS прибирає і проміжну чергу, і Lambda.
- *SWF workflow* — нереалістичне рішення: SWF не призначений для розсилки в SQS.
- *Step Functions + multiple Lambdas* — неефективно, і незрозуміло, як функції отримають повідомлення.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-sqs-as-subscriber.html>

</details>

---

<a id="t06-q30"></a>
### T06-Q30
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A developer needs to create a serverless application that uses an event-driven architecture.

How can the developer configure the application to automatically receive and process events?

- ▫️ Create an Amazon SQS queue and publish events to the queue. Configure an Amazon EC2 instance to poll the queue and consume the messages.
- ▫️ Create an Amazon SNS topic and an AWS Lambda function. Configure an HTTP endpoint on the Lambda function and subscribe the HTTP endpoint to the SNS topic. Submit events to the SNS topic.
- ✅ Create an Amazon SNS topic and an AWS Lambda function. Subscribe the Lambda function to the SNS topic and submit events to the SNS topic.
- ▫️ Create an Amazon SQS topic and an Amazon EC2 instance. Subscribe the instance to the SNS topic and submit events to the topic.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Amazon SNS topic and an AWS Lambda function. Subscribe the Lambda function to the SNS topic and submit events to the SNS topic.»**

Lambda може обробляти повідомлення SNS: функцію підписують на топік у тому самому або в іншому акаунті. Подія, надіслана в топік, потрапляє в Lambda й обробляється. Це проста serverless подієва архітектура.

**❌ Неправильні варіанти:**
- *SQS + EC2 polling* — EC2 не serverless.
- *SQS topic + EC2 subscribed* — EC2 не serverless і не підписується на SNS напряму. Крім того, «SQS topic» не існує.
- *HTTP endpoint on Lambda* — HTTP endpoint не потрібен: Lambda підписується на SNS напряму.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/with-sns.html>

</details>

---

<a id="t07-q40"></a>
### T07-Q40
*домен: офіційна мітка*

> ⚠️ **Застаріле: ліміт 256 KB → тепер 1 MiB. Extended Client — для > 1 MiB.** → [детальніше](../../07-outdated-and-wrong-answers.md#o01)

A media company uses Amazon Simple Queue Service (SQS) queue to manage their transactions. With changing business needs, the payload size of the messages is increasing. The Team Lead of the project is worried about the 256 KB message size limit that SQS has.

What can be done to make the queue accept messages of a larger size?

- ▫️ Get a service limit increase from AWS
- ✅ Use the SQS Extended Client
- ▫️ Use the MultiPart API
- ▫️ Use gzip compression

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the SQS Extended Client»**

Великі повідомлення SQS обробляють через Amazon S3 і SQS Extended Client Library: тіло повідомлення зберігається в S3, а в черзі лишається лише посилання. Так можна працювати з повідомленнями до 2 GB.

⚠️ Ключ «Extended Client через ліміт 256 KB» застарів наполовину. З серпня 2025 року SQS приймає повідомлення до **1 MiB**, тож Extended Client потрібен лише для більших повідомлень. Див. 07.

**❌ Неправильні варіанти:**
- *MultiPart API* — у SQS немає multipart API.
- *Service limit increase* — розмір повідомлення — фіксований ліміт, а для великих повідомлень є Extended Client.
- *gzip* — після стиснення повідомлення треба кодувати (наприклад, у base64), що збільшує розмір; не оптимально.

</details>

---

<a id="t07-q62"></a>
### T07-Q62
*домен: офіційна мітка*

A company’s e-commerce website is expecting hundreds of thousands of visitors on Black Friday. The marketing department is concerned that high volumes of orders might stress SQS leading to message failures. The company has approached you for the steps to be taken as a precautionary measure against the high volumes.

What step will you suggest as a Developer Associate?

- ▫️ Convert the queue into FIFO ordered queue, since messages to the down system will be processed faster once they are ordered
- ▫️ Enable auto-scaling in the SQS queue
- ✅ Amazon SQS is highly scalable and does not need any intervention to handle the expected high volumes
- ▫️ Pre-configure the SQS queue to increase the capacity when messages hit a certain threshold

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon SQS is highly scalable and does not need any intervention to handle the expected high volumes»**

SQS масштабується динамічно разом із застосунком, тож планувати ємність заздалегідь не потрібно. У standard черзі може бути приблизно до 120 000 повідомлень in-flight — отриманих споживачем, але ще не видалених.

Квоти: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-quotas.html

**❌ Неправильні варіанти:**
- *Pre-configure capacity increase* — SQS сам виділяє потрібну ємність.
- *Enable auto-scaling* — черги масштабуються автоматично, налаштовувати нічого не потрібно.
- *Convert to FIFO* — наявну standard чергу не можна перетворити на FIFO; треба створити нову FIFO-чергу. До того ж порядок не пришвидшує обробку. Детальніше: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-quotas.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html>

</details>

---

<a id="t08-q15"></a>
### T08-Q15
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company is in the process of migrating an application from a monolithic architecture to a microservices-based architecture. The developers need to refactor the application so that the many microservices can asynchronously communicate with each other in a decoupled manner.

Which AWS services can be used for asynchronous message passing? (Select TWO.)

- ▫️ AWS Lambda
- ✅ Amazon SNS
- ▫️ Amazon Kinesis
- ✅ Amazon SQS
- ▫️ Amazon ECS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon SNS» + «Amazon SQS»**

- **SQS** — керована черга повідомлень для роз'єднання і масштабування мікросервісів, розподілених і serverless-систем.
- **SNS** — керований pub/sub-сервіс сповіщень з високою доступністю й надійністю.

Обидва дають асинхронний обмін повідомленнями: SQS як шина повідомлень, SNS як сповіщення.

**❌ Неправильні варіанти:**
- *Kinesis* — потокові дані: аналітика в реальному часі, IoT.
- *ECS* — запуск Docker-контейнерів.
- *Lambda* — обчислення: функції у відповідь на тригери.

Джерела: <https://aws.amazon.com/sqs/> · <https://aws.amazon.com/sns/>

</details>

---

<a id="t08-q18"></a>
### T08-Q18
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company uses an Amazon Simple Queue Service (SQS) Standard queue for an application. An issue has been identified where applications are picking up messages from the queue that are still being processed causing duplication. What can a Developer do to resolve this issue?

- ▫️ Increase the ReceiveMessageWaitTimeSeconds API action on the queue
- ✅ Increase the VisibilityTimeout API action on the queue
- ▫️ Increase the DelaySeconds API action on the queue
- ▫️ Create a RedrivePolicy for the queue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the VisibilityTimeout API action on the queue»**

Після отримання повідомлення лишається в черзі: SQS автоматично його не видаляє. Систему розподілено, тож немає гарантії, що споживач справді отримав повідомлення. Тому споживач сам видаляє повідомлення після обробки.

Щоб інші споживачі тим часом не обробили його повторно, діє visibility timeout — повідомлення для них невидиме: за замовчуванням 30 с, від 0 с до 12 годин. Якщо обробка триває довше, повідомлення знову стає видимим і обробляється вдруге. Тож visibility timeout треба збільшити.

**❌ Неправильні варіанти:**
- *DelaySeconds* — затримка доставки всіх повідомлень у черзі.
- *ReceiveMessageWaitTimeSeconds* — час очікування в `ReceiveMessage`, тобто long polling.
- *RedrivePolicy* — JSON-рядок з налаштуваннями DLQ.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html>

</details>

---

<a id="t08-q62"></a>
### T08-Q62
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company uses Amazon SQS to decouple an online application that generates memes. The SQS consumers poll the queue regularly to keep throughput high and this is proving to be costly and resource intensive. A Developer has been asked to review the system and propose changes that can reduce costs and the number of empty responses.

What would be the BEST approach to MINIMIZING cost?

- ✅ Set the Imaging queue ReceiveMessageWaitTimeSeconds attribute to 20 seconds
- ▫️ Set the DelaySeconds parameter of a message to 20 seconds
- ▫️ Set the imaging queue MessageRetentionPeriod attribute to 20 seconds
- ▫️ Set the imaging queue visibility Timeout attribute to 20 seconds

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set the Imaging queue ReceiveMessageWaitTimeSeconds attribute to 20 seconds»**

За замовчуванням SQS використовує short polling і опитує лише частину своїх серверів, тож відповіді бувають порожні. Long polling зменшує вартість і водночас віддає повідомлення щойно вони з'являються.

Long polling діє, коли час очікування більший за 0; максимум — 20 секунд. Він прибирає порожні відповіді і хибно порожні, коли повідомлення є, але не потрапили у відповідь. Тож треба встановити `ReceiveMessageWaitTimeSeconds` = 20.

**❌ Неправильні варіанти:**
- *Visibility timeout 20 s* — керує видимістю отриманих повідомлень і порожніх відповідей не зменшує.
- *MessageRetentionPeriod 20 s* — визначає, скільки зберігається повідомлення.
- *DelaySeconds 20 s* — затримує доставку повідомлень.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html>

</details>

---

<a id="t09-q46"></a>
### T09-Q46
*домен: офіційна мітка*

The development team at an analytics company is using SQS queues for decoupling the various components of application architecture. As the consumers need additional time to process SQS messages, the development team wants to postpone the delivery of new messages to the queue for a few seconds.

As a Developer Associate, which of the following solutions would you recommend to the development team?

- ✅ Use delay queues to postpone the delivery of new messages to the queue for a few seconds
- ▫️ Use visibility timeout to postpone the delivery of new messages to the queue for a few seconds
- ▫️ Use dead-letter queues to postpone the delivery of new messages to the queue for a few seconds
- ▫️ Use FIFO queues to postpone the delivery of new messages to the queue for a few seconds

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use delay queues to postpone the delivery of new messages to the queue for a few seconds»**

SQS має два типи черг:
- **Standard** — максимальна пропускна здатність, best-effort порядок, at-least-once.
- **FIFO** — exactly-once обробка і строгий порядок.

Delay queue відкладає доставку нових повідомлень, наприклад коли споживачу потрібен додатковий час. Усі повідомлення в такій черзі невидимі протягом затримки: від 0 (за замовчуванням) до 15 хвилин.

**❌ Неправильні варіанти:**
- *FIFO queues* — гарантують порядок і exactly-once, але затримки не дають.
- *Dead-letter queues* — для повідомлень, які не вдалося обробити, а не для затримки.
- *Visibility timeout* — ховає вже отримане повідомлення від інших споживачів (за замовчуванням 30 с, до 12 год), а не затримує нові.

</details>

---

<a id="t10-q04"></a>
### T10-Q04
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

What does an Amazon SQS delay queue accomplish?

- ▫️ The consumer can poll the queue for a configurable amount of time before retrieving a message
- ▫️ Messages are hidden for a configurable amount of time after they are consumed from the queue
- ✅ Messages are hidden for a configurable amount of time when they are first added to the queue
- ▫️ Message cannot be deleted for a configurable amount of time after they are consumed from the queue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Messages are hidden for a configurable amount of time when they are first added to the queue»**

Delay queue відкладає доставку **нових** повідомлень, наприклад коли споживачу потрібен додатковий час. Повідомлення невидимі протягом затримки: від 0 (за замовчуванням) до 15 хвилин.

**❌ Неправильні варіанти:**
- *Hidden after consumed* — так працює visibility timeout, а затримка діє, коли повідомлення додають у чергу.
- *Consumer can poll for a configurable time* — це long polling; delay queue на опитування не впливає.
- *Cannot be deleted after consumed* — це не опис жодного механізму SQS.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html>

</details>

---

<a id="t10-q26"></a>
### T10-Q26
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer is managing an application that includes an Amazon SQS queue. The consumers that process the data from the queue are connecting in short cycles and the queue often does not return messages. The cost for API calls is increasing. How can the Developer optimize the retrieval of messages and reduce cost?

- ▫️ Call the SetQueueAttributes API with the maxReceiveCount set to 20
- ✅ Call the ReceiveMessage API with the WaitTimeSeconds parameter set to 20
- ▫️ Call the SetQueueAttributes API with the DelaySeconds parameter set to 900
- ▫️ Call the ReceiveMessage API with the VisibilityTimeout parameter set to 30

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Call the ReceiveMessage API with the WaitTimeSeconds parameter set to 20»**

За замовчуванням SQS використовує short polling: опитує лише частину серверів, тож окремий `ReceiveMessage` може не повернути всі повідомлення. Якщо в черзі менше 1 000 повідомлень, наступний запит їх поверне; при постійному читанні рано чи пізно опитуються всі сервери.

Long polling діє, коли `WaitTimeSeconds` > 0; максимум — 20 секунд. Він прибирає порожні й хибно порожні відповіді, тож здешевлює роботу і віддає повідомлення щойно вони з'являються.

**❌ Неправильні варіанти:**
- *VisibilityTimeout=30* — ховає вже отримане повідомлення від інших споживачів, а не вмикає long polling.
- *DelaySeconds=900* — затримує доставку нових повідомлень на 15 хвилин.
- *maxReceiveCount=20* — параметр redrive policy для DLQ.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html>

</details>

---

<a id="t10-q29"></a>
### T10-Q29
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A media organization utilizes an Amazon API Gateway REST API endpoint to disseminate updates from an internal Content Management System (CMS) to Amazon EventBridge. An EventBridge rule is set up to monitor these updates and control content syndication in a primary AWS account. The organization now wants to extend the reach of these updates across several affiliate AWS accounts.

How can the developer accomplish this without altering the configuration of the CMS?

- ▫️ Set up multiple instances of the CMS, each linked to a different AWS account.
- ▫️ Use AWS Lambda in the main account to clone updates and manually invoke it in affiliate accounts.
- ▫️ Establish multiple API Gateway REST API endpoints in affiliate accounts to directly receive updates from the CMS.
- ✅ Implement an EventBridge event bus in the affiliate AWS accounts to create a rule that matches events and forwards them from the main account.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement an EventBridge event bus in the affiliate AWS accounts to create a rule that matches events and forwards them from the main account.»**

EventBridge може надсилати події в інші акаунти AWS: правило в основному акаунті передає події на event bus в акаунтах партнерів. Це простий спосіб розповсюдити оновлення, і конфігурацію CMS змінювати не потрібно.

**❌ Неправильні варіанти:**
- *Multiple API Gateway endpoints* — довелося б змінювати CMS і підтримувати багато endpoints.
- *Lambda cloning updates manually* — неефективно, із затримками і зайвими витратами на супровід.
- *Multiple CMS instances* — дуже складно й ресурсоємно.

Джерела: <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-cross-account.html>

</details>

---

<a id="t10-q58"></a>
### T10-Q58
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Формулювання дивне; суть: long polling повертає повідомлення одразу.** → [детальніше](../../07-outdated-and-wrong-answers.md#d08)

A gaming application displays the results of games in a leaderboard. The leaderboard is updated by 4 KB messages that are retrieved from an Amazon SQS queue. The updates are received infrequently but the Developer needs to minimize the time between the messages arriving in the queue and the leaderboard being updated.

Which technique provides the shortest delay in updating the leaderboard?

- ▫️ Retrieve the messages from the queue using short polling every 10 seconds
- ▫️ Reduce the size of the messages with compression before sending them
- ✅ Retrieve the messages from the queue using long polling every 15 seconds
- ▫️ Store the message payload in Amazon S3 and use the SQS Extended Client Library for Java

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Retrieve the messages from the queue using long polling every 15 seconds»**

За замовчуванням SQS використовує short polling і опитує лише частину серверів. Long polling (`WaitTimeSeconds` > 0, максимум 20 с) здешевлює роботу: прибирає порожні й хибно порожні відповіді. А ще він повертає повідомлення **щойно вони з'являються**.

⚠️ Ключ дивний. Long polling справді повертає повідомлення одразу, але формулювання «кожні 15 секунд» неточне. Див. 07 (d08).

**❌ Неправильні варіанти:**
- *Short polling every 10 s* — short polling — це `WaitTimeSeconds = 0`, з порожніми відповідями.
- *Compress messages* — повідомлення не прийдуть швидше, а стискати 4 KB немає сенсу.
- *S3 + Extended Client* — зайве для такого розміру і не пришвидшить оновлення.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html>

</details>

---

<a id="t11-q08"></a>
### T11-Q08
*домен: офіційна мітка*

A developer is testing Amazon Simple Queue Service (SQS) queues in a development environment. The queue along with all its contents has to be deleted after testing.

Which SQS API should be used for this requirement?

- ▫️ RemovePermission
- ▫️ RemoveQueue
- ▫️ PurgeQueue
- ✅ DeleteQueue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «DeleteQueue»**

`DeleteQueue` видаляє чергу разом з усім вмістом, і повідомлення стають недоступні. Видалення триває до 60 секунд: запити в цей час можуть бути успішними, але потім черга і повідомлення зникнуть. Створити чергу з тим самим іменем можна не раніше ніж через 60 секунд.

**❌ Неправильні варіанти:**
- *PurgeQueue* — видаляє лише повідомлення, а черга лишається.
- *RemoveQueue* — такої дії немає.
- *RemovePermission* — прибирає дозволи з політики черги.

</details>

---

<a id="t11-q12"></a>
### T11-Q12
*домен: офіційна мітка*

Amazon Simple Queue Service (SQS) has a set of APIs for various actions supported by the service.

As a developer associate, which of the following would you identify as correct regarding the CreateQueue API? (Select two)

- ✅ The visibility timeout value for the queue is in seconds, which defaults to 30 seconds
- ▫️ Queue tags are case insensitive. A new tag with a key identical to that of an existing tag overwrites the existing tag
- ▫️ The dead-letter queue of a FIFO queue must also be a FIFO queue. Whereas, the dead-letter queue of a standard queue can be a standard queue or a FIFO queue
- ✅ You can't change the queue type after you create it
- ▫️ The length of time, in seconds, for which the delivery of all messages in the queue is delayed is configured using MessageRetentionPeriod attribute

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The visibility timeout value for the queue is in seconds, which defaults to 30 seconds» + «You can't change the queue type after you create it»**

- **Тип черги не змінюється.** Standard не перетворити на FIFO; треба створити нову FIFO-чергу або видалити стару і створити заново.
- **Visibility timeout** — у секундах: від 0 до 43 200 (12 годин), за замовчуванням 30.

**❌ Неправильні варіанти:**
- *Standard DLQ can be FIFO* — DLQ має бути того самого типу: для FIFO — FIFO, для standard — standard.
- *Delay via MessageRetentionPeriod* — затримку задає `DelaySeconds`; `MessageRetentionPeriod` — скільки зберігається повідомлення.
- *Tags case insensitive* — теги чутливі до регістру. Новий тег з тим самим ключем перезаписує старий. Для тегування при створенні потрібні `sqs:CreateQueue` і `sqs:TagQueue`.

</details>

---

<a id="t12-q57"></a>
### T12-Q57
*домен: офіційна мітка*

Your application sends messages to an Amazon Simple Queue Service (SQS) queue frequently, which are then polled by another application that specifies which message to retrieve.

Which of the following options describe the maximum number of messages that can be retrieved at one time?

- ▫️ 100
- ✅ 10
- ▫️ 5
- ▫️ 20

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «10»**

Повідомлення з черги отримують і потім видаляють. Вибрати конкретні повідомлення не можна; можна лише вказати максимальну кількість за один запит (`MaxNumberOfMessages`) — **до 10**.

**❌ Неправильні варіанти:**
- *5* — максимум 10.
- *20* — максимум 10.
- *100* — максимум 10.

</details>

---

<a id="t12-q63"></a>
### T12-Q63
*домен: офіційна мітка*

As a Developer, you are working on a mobile application that utilizes Amazon Simple Queue Service (SQS) for sending messages to downstream systems for further processing. One of the requirements is that the messages should be stored in the queue for a period of 12 days.

How will you configure this requirement?

- ▫️ Enable Long Polling for the SQS queue
- ▫️ The maximum retention period of SQS messages is 7 days, therefore retention period of 12 days is not possible
- ▫️ Use a FIFO SQS queue
- ✅ Change the queue message retention setting

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Change the queue message retention setting»**

SQS автоматично видаляє повідомлення, що пробули в черзі довше за retention period. За замовчуванням — **4 дні**, налаштовується від 60 секунд до **14 днів** (1 209 600 с) через `SetQueueAttributes`. Тож 12 днів — допустимо.

Детальніше: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-architecture.html

**❌ Неправильні варіанти:**
- *Long polling* — `WaitTimeSeconds` > 0 (максимум 20 с) зменшує порожні відповіді, але не впливає на те, скільки зберігаються повідомлення.
- *Max retention is 7 days* — максимум 14 днів.
- *FIFO queue* — для порядку і відсутності дублікатів; на термін зберігання не впливає.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-architecture.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html>

</details>

---
