# SQS / SNS / EventBridge / SES

**Домен 4 — Усунення несправностей та оптимізація** · питань: 14  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q09"></a>
### T01-Q09
*домен: офіційна мітка*

Applications running on EC2 instances process messages from an SQS queue but sometimes they experience errors due to messages not being processed.

To isolate the messages, which option will help with further debugging?

- ▫️ Use DeleteMessage
- ▫️ Reduce the VisibilityTimeout
- ▫️ Increase the VisibilityTimeout
- ✅ Implement a Dead Letter Queue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement a Dead Letter Queue»**

Dead-letter queue (DLQ) — це черга, куди інші черги (source queues) перенаправляють повідомлення, які не вдалося успішно обробити. DLQ корисні для дебагу: вони ізолюють проблемні повідомлення, щоб можна було з'ясувати, чому обробка не вдається. Буває, що повідомлення неможливо обробити: наприклад, коментар до статті, яку автор видалив, поки коментар ще йшов.

**❌ Неправильні варіанти:**
- *Use DeleteMessage* — видаляє повідомлення з черги, але не допомагає знайти причину.
- *Reduce the VisibilityTimeout* — visibility timeout не дає іншим consumers отримати те саме повідомлення (за замовчуванням 30 с, від 0 с до 12 год). Якщо його зменшити, невдале повідомлення отримає ще більше consumers.
- *Increase the VisibilityTimeout* — не допоможе: потрібен не довший час, а ізольоване місце для дебагу.

</details>

---

<a id="t01-q15"></a>
### T01-Q15
*домен: офіційна мітка*

A data analytics company ingests a large number of messages and stores them in an RDS database using Lambda. Because of the increased payload size, it is taking more than 15 minutes to process each message.

As a Developer Associate, which of the following options would you recommend to process each message in the MOST scalable way?

- ▫️ Use DynamoDB instead of RDS as database
- ▫️ Contact AWS Support to increase the Lambda timeout to 60 minutes
- ✅ Provision EC2 instances in an Auto Scaling group to poll the messages from an SQS queue
- ▫️ Provision an EC2 instance to poll the messages from an SQS queue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Provision EC2 instances in an Auto Scaling group to poll the messages from an SQS queue»**

Обробка кожного повідомлення триває понад 15 хвилин, тож Lambda тут не підходить (максимальний timeout — 15 хв). Для масштабованого рішення EC2-інстанси мають працювати в Auto Scaling group, яка підлаштовується під зміни навантаження.

**❌ Неправильні варіанти:**
- *Provision an EC2 instance to poll the messages* — одного інстансу може не вистачити при різкому сплеску повідомлень.
- *Contact AWS Support to increase the Lambda timeout to 60 minutes* — AWS Support не може підняти верхню межу timeout Lambda.
- *Use DynamoDB instead of RDS* — зміна БД ніяк не вплине на timeout Lambda. Це дистрактор.

</details>

---

<a id="t01-q35"></a>
### T01-Q35
*домен: офіційна мітка*

You are using AWS SQS FIFO queues to get the ordering of messages on a per user_id basis. On top of this, you would like to make sure that duplicate messages should not be sent to SQS as this would cause application failure.

As a developer, which message parameter should you set for deduplicating messages?

- ▫️ MessageGroupId
- ▫️ ContentBasedDeduplication
- ✅ MessageDeduplicationId
- ▫️ ReceiveRequestAttemptId

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «MessageDeduplicationId»**

Message deduplication ID — токен для дедуплікації відправлених повідомлень у FIFO-черзі. Якщо повідомлення з певним ID успішно надіслано, будь-які наступні з тим самим ID протягом 5-хвилинного інтервалу приймаються, але не доставляються.

**❌ Неправильні варіанти:**
- *MessageGroupId* — визначає групу для впорядкованої обробки, а не дедуплікацію.
- *ReceiveRequestAttemptId* — дедуплікує виклики `ReceiveMessage` (повтор після мережевої помилки поверне той самий набір повідомлень), а не відправлені повідомлення.
- *ContentBasedDeduplication* — це налаштування черги, а не параметр повідомлення: SQS генерує ID дедуплікації як SHA-256 від тіла повідомлення.

</details>

---

<a id="t02-q47"></a>
### T02-Q47
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Сумнівний ключ: для async Lambda правильніше on-failure destination / Lambda DLQ.** → [детальніше](../../07-outdated-and-wrong-answers.md#d02)

An application asynchronously invokes an AWS Lambda function. The application has recently been experiencing occasional errors that result in failed invocations. A developer wants to store the messages that resulted in failed invocations such that the application can automatically retry processing them.

What should the developer do to accomplish this goal with the LEAST operational overhead?

- ✅ Configure a redrive policy on an Amazon SQS queue. Set the dead-letter queue as an event source to the Lambda function.
- ▫️ Configure an Amazon S3 bucket as a destination for failed invocations. Configure event notifications to trigger the Lambda function to process the events.
- ▫️ Configure Amazon EventBridge to send the messages to Amazon SNS to initiate the Lambda function again.
- ▫️ Configure logging to an Amazon CloudWatch Logs group. Configure Lambda to read failed invocation events from the log group.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь (за ключем банку): «Configure a redrive policy on an Amazon SQS queue. Set the dead-letter queue as an event source to the Lambda function»**

SQS підтримує DLQ — черги, куди потрапляють повідомлення, що не обробилися. Redrive policy задає вихідну чергу, DLQ і умову переміщення (скільки разів consumer може невдало обробити повідомлення). DLQ можна зробити event source для Lambda, щоб «вичерпувати» її й автоматично повторювати обробку невдалих викликів.

⚠️ **Сумнівно:** для асинхронних викликів Lambda простіше налаштувати on-failure destination (SQS) або Lambda DLQ. Див. файл 07, розділ d02.

**❌ Неправильні варіанти:**
- *CloudWatch Logs group … Lambda reads failed events from logs* — інформації в логах може бракувати для повторної обробки; це не автоматизовано.
- *EventBridge → SNS → Lambda* — EventBridge може бути failure destination, а SNS викликати Lambda, але операційних накладних витрат більше, ніж з DLQ.
- *S3 as a destination for failed invocations* — у банку: S3 не підтримується як failure destination (на той час були SNS, SQS, EventBridge і Lambda; S3 додали пізніше).

Джерела: <https://aws.amazon.com/blogs/compute/introducing-aws-lambda-destinations/> · <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html>

</details>

---

<a id="t03-q40"></a>
### T03-Q40
*домен: офіційна мітка*

After reviewing your monthly AWS bill you notice that the cost of using Amazon SQS has gone up substantially after creating new queues; however, you know that your queue clients do not have a lot of traffic and are receiving empty responses.

Which of the following actions should you take?

- ✅ Use LongPolling
- ▫️ Decrease DelaySeconds
- ▫️ Use a FIFO queue
- ▫️ Increase the VisibilityTimeout

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use LongPolling»**

SQS отримує повідомлення через short polling (за замовчуванням) або long polling. Short polling відповідає одразу, навіть якщо повідомлень немає. Long polling чекає, поки з'явиться хоча б одне повідомлення (до максимальної кількості в запиті), і повертає порожню відповідь лише після спливання часу очікування (до 20 с). Так стає менше порожніх і хибно-порожніх відповідей, а отже й витрат.

**❌ Неправильні варіанти:**
- *Increase the VisibilityTimeout* — запобігає повторній обробці, але витрат не знижує.
- *Use a FIFO queue* — для строгого порядку; навпаки, дорожче за standard.
- *Decrease DelaySeconds* — затримка доставки нових повідомлень, на витрати не впливає.

</details>

---

<a id="t03-q65"></a>
### T03-Q65
*домен: офіційна мітка*

An order management system uses a cron job to poll for any new orders. Every time a new order is created, the cron job sends this order data as a message to the message queues to facilitate downstream order processing in a reliable way. To reduce costs and improve performance, the company wants to move this functionality to AWS cloud.

Which of the following is the most optimal solution to meet this requirement?

- ▫️ Use Amazon Simple Notification Service (SNS) to push notifications and use AWS Lambda functions to process the information received from SNS
- ✅ Use Amazon Simple Notification Service (SNS) to push notifications when an order is created. Configure different Amazon Simple Queue Service (SQS) queues to receive these messages for downstream processing
- ▫️ Use Amazon Simple Notification Service (SNS) to push notifications to Kinesis Data Firehose delivery streams for processing the data for downstream applications
- ▫️ Configure different Amazon Simple Queue Service (SQS) queues to poll for new orders

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon SNS to push notifications when an order is created. Configure different Amazon SQS queues to receive these messages for downstream processing»**

SNS і SQS доповнюють одне одного. SNS «проштовхує» термінові повідомлення багатьом підписникам, тож не треба періодично опитувати. SQS — черга, з якої розподілені застосунки забирають повідомлення (polling); вона розв'язує відправника й отримувача, і їм не потрібно бути доступними одночасно. Разом: SNS публікує подію, а кожна підписана черга SQS отримує копію (з темою, текстом і метаданими в JSON) для подальшої обробки. Це патерн **fan-out**.

**❌ Неправильні варіанти:**
- *Different SQS queues to poll for new orders* — SQS не опитує джерела; повідомлення треба надсилати в чергу.
- *SNS + Lambda* — Lambda не зберігає повідомлення; якщо обробка не вдасться, повідомлення лишиться необробленим.
- *SNS + Kinesis Data Firehose* — Firehose для потокової доставки великих даних в аналітичні сховища; для розв'язування систем SQS простіша й дешевша.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-sqs-as-subscriber.html> · <https://docs.aws.amazon.com/sns/latest/dg/sns-lambda-as-subscriber.html> · <https://docs.aws.amazon.com/sns/latest/dg/sns-firehose-as-subscriber.html>

</details>

---

<a id="t06-q28"></a>
### T06-Q28
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A company is deploying a serverless application on AWS, using an AWS Lambda function for handling customer orders. This function utilizes an external HTTP API for processing payments, which occasionally times out. The requirement is to notify the support team via an existing Amazon SNS topic when the error rate of this API surpasses 10% per hour.

How can this be achieved?

- ▫️ Use AWS X-Ray to monitor the error rate and configure it to send alerts through the SNS topic.
- ▫️ Enable AWS CloudTrail to monitor the Lambda function and send alerts to the SNS topic when the error rate exceeds 10%.
- ▫️ Configure the AWS Lambda function to directly send alerts to the SNS topic when there is a timeout.
- ✅ Implement Amazon CloudWatch Metrics with a custom threshold and link it to the existing SNS topic.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement Amazon CloudWatch Metrics with a custom threshold and link it to the existing SNS topic.»**

CloudWatch відстежує частку помилок API, а alarm спрацьовує при перевищенні порога. Якщо прив'язати alarm до наявного SNS-топіка, підтримка майже в реальному часі дізнається, що помилки зовнішнього платіжного API перевищили 10% транзакцій за годину.

**❌ Неправильні варіанти:**
- *X-Ray alerts via SNS* — X-Ray для трасування й аналізу окремих запитів, а не для алертів на частку помилок.
- *Lambda sends alerts directly on timeout* — ускладнює функцію і менш надійно, ніж спеціальний сервіс моніторингу.
- *CloudTrail error rate* — CloudTrail зберігає історію дій в акаунті; він не моніторить частку помилок і не надсилає алерти.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html>

</details>

---

<a id="t07-q27"></a>
### T07-Q27
*домен: офіційна мітка*

You company runs business logic on smaller software components that perform various functions. Some functions process information in a few seconds while others seem to take a long time to complete. Your manager asked you to decouple components that take a long time to ensure software applications stay responsive under load. You decide to configure Amazon Simple Queue Service (SQS) to work with your Elastic Beanstalk configuration.

Which of the following Elastic Beanstalk environment should you choose to meet this requirement?

- ▫️ Load-balancing, Autoscaling environment
- ✅ Dedicated worker environment
- ▫️ Single Instance with Elastic IP
- ▫️ Single Instance Worker node

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Dedicated worker environment»**

Elastic Beanstalk бере на себе виділення потужностей, балансування, масштабування й моніторинг здоров'я застосунку. Ключові поняття: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html

Довгі операції краще винести в окреме **worker environment**: воно бере задачі з черги SQS і виконує їх у фоні. Так веб-частина лишається чутливою під навантаженням. Довга задача — будь-що, що помітно затягує відповідь: обробка зображень чи відео, надсилання листів, створення ZIP. Навіть 1–2 секунди — це багато для запиту, що мав би займати до 500 мс.

**❌ Неправильні варіанти:**
- *Single Instance Worker node* — «worker nodes» — це термін Kubernetes/EKS, і з worker environment Beanstalk їх не слід плутати.
- *Load-balancing, Autoscaling environment* — масштабує веб-частину в кількох AZ, але довгі задачі не виносить і додає вартості.
- *Single Instance with Elastic IP* — один інстанс без балансувальника, тож немає високої доступності; не для продакшну.

</details>

---

<a id="t08-q09"></a>
### T08-Q09
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company is running an order processing system on AWS. Amazon SQS is used to queue orders and an AWS Lambda function processes them. The company recently started noticing a lot of orders are failing to process.

How can a Developer MOST effectively manage these failures to debug the failed orders later and reprocess them, as necessary?

- ✅ Implement dead-letter queues for failed orders from the order queue
- ▫️ Publish failed orders from the order queue to an Amazon SNS topic
- ▫️ Log the failed orders from the order queue using Amazon CloudWatch Logs
- ▫️ Send failed orders from the order queue to AWS CloudTrail logs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement dead-letter queues for failed orders from the order queue»**

Dead-letter queue (DLQ) отримує повідомлення, які не вдалося обробити з основної черги. Так проблемні повідомлення ізолюються, і можна розібратися, чому обробка не вдається. Повне повідомлення доступне для аналізу.

**❌ Неправильні варіанти:**
- *SNS topic* — неможливо відокремити саме невдало оброблені повідомлення.
- *CloudWatch Logs* — SQS не логує успіх чи збій обробки повідомлень.
- *CloudTrail* — записує API-виклики, а не вміст повідомлень.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html>

</details>

---

<a id="t09-q03"></a>
### T09-Q03
*домен: офіційна мітка*

A company has a cloud system in AWS with components that send and receive messages using SQS queues. While reviewing the system you see that it processes a lot of information and would like to be aware of any limits of the system.

Which of the following represents the maximum number of messages that can be stored in an SQS queue?

- ▫️ 10000
- ✅ no limit
- ▫️ 100000
- ▫️ 10000000

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «no limit»**

Кількість повідомлень, що зберігаються в черзі SQS, не обмежена. Обмежена лише кількість **in-flight** повідомлень — отриманих споживачем, але ще не видалених: приблизно 120 000 для standard черги. Тому видаляйте повідомлення після обробки.

**❌ Неправильні варіанти:**
- *10000* — кількість повідомлень у черзі не обмежена.
- *100000* — кількість повідомлень у черзі не обмежена.
- *10000000* — кількість повідомлень у черзі не обмежена.

</details>

---

<a id="t09-q48"></a>
### T09-Q48
*домен: офіційна мітка*

A company uses Amazon Simple Email Service (SES) to cost-effectively send susbscription emails to the customers. Intermittently, the SES service throws the error: Throttling – Maximum sending rate exceeded.

As a developer associate, which of the following would you recommend to fix this issue?

- ✅ Use Exponential Backoff technique to introduce delay in time before attempting to execute the operation again
- ▫️ Raise a service request with Amazon to increase the throttling limit for the SES API
- ▫️ Configure Timeout mechanism for each request made to the SES service
- ▫️ Implement retry mechanism for all 4xx errors to avoid throttling error

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Exponential Backoff technique to introduce delay in time before attempting to execute the operation again»**

Помилку SES «Throttling – Maximum sending rate exceeded» можна повторювати: запит, повторений пізніше, найімовірніше пройде.

Повтори «егоїстичні»: кожен забирає ще трохи часу сервера. Якщо збої рідкісні, це не страшно. Але якщо збій спричинений перевантаженням, агресивні повтори погіршують ситуацію і затягують відновлення.

Тому використовують backoff — паузу між спробами, яка росте експоненційно. Швидкість надсилання залежить від розміру повідомлень, мережі й доступності SES, а з backoff застосунок сам підлаштовується під максимально дозволену швидкість.

**❌ Неправильні варіанти:**
- *Timeouts* — звільняють ресурси, але якщо всі клієнти повторюють одночасно, перевантаження лише зросте.
- *Raise throttling limit* — доречно, лише якщо помилка постійна; тут вона періодична.
- *Retry all 4xx* — 4xx означає помилку в самому запиті (облікові дані, параметри), такий запит треба виправити, а не повторювати.

Джерела: <https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/> · <https://aws.amazon.com/blogs/messaging-and-targeting/how-to-handle-a-throttling-maximum-sending-rate-exceeded-error/>

</details>

---

<a id="t09-q64"></a>
### T09-Q64
*домен: офіційна мітка*

An application running on EC2 instances processes messages from an SQS queue. However, sometimes the messages are not processed and they end up in errors. These messages need to be isolated for further processing and troubleshooting.

Which of the following options will help achieve this?

- ▫️ Increase the VisibilityTimeout
- ▫️ Reduce the VisibilityTimeout
- ▫️ Use DeleteMessage
- ✅ Implement a Dead-Letter Queue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement a Dead-Letter Queue»**

DLQ отримує повідомлення, які не вдалося обробити з основної черги. Так проблемні повідомлення ізолюються, і можна з'ясувати причину збою. SQS не створює DLQ автоматично: чергу треба створити заздалегідь і вказати в redrive policy, разом із `maxReceiveCount`.

**❌ Неправильні варіанти:**
- *Increase VisibilityTimeout* — довше ховає повідомлення від інших споживачів, але проблемні повідомлення не ізолює.
- *DeleteMessage* — повідомлення просто зникне, і причину збою не дізнатися.
- *Reduce VisibilityTimeout* — те саме проблемне повідомлення частіше отримуватимуть інші споживачі.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html>

</details>

---

<a id="t12-q36"></a>
### T12-Q36
*домен: офіційна мітка*

An e-commerce company uses Amazon SQS queues to decouple their application architecture. The development team has observed message processing failures for an edge case scenario when a user places an order for a particular product ID, but the product ID is deleted, thereby causing the application code to fail.

As a Developer Associate, which of the following solutions would you recommend to address such message failures?

- ▫️ Use a temporary queue to handle message processing failures
- ▫️ Use long polling to handle message processing failures
- ▫️ Use short polling to handle message processing failures
- ✅ Use a dead-letter queue to handle message processing failures

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a dead-letter queue to handle message processing failures»**

DLQ отримує повідомлення, які не вдалося обробити з основної черги. Так проблемні повідомлення ізолюються, і можна з'ясувати причину. Приклад: коментар до історії не обробляється, бо автор видалив саму історію, і такий коментар потрапляє в DLQ.

Детальніше: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

**❌ Неправильні варіанти:**
- *Temporary queue* — для патерну request-response: тимчасові черги для відповідей без зайвих викликів API. Збоїв обробки не вирішує.
- *Short polling* — спосіб отримання повідомлень, а не обробка збоїв.
- *Long polling* — так само лише спосіб отримання повідомлень.

</details>

---

<a id="t12-q39"></a>
### T12-Q39
*домен: офіційна мітка*

A video encoding application running on an EC2 instance takes about 20 seconds on average to process each raw footage file. The application picks the new job messages from an SQS queue. The development team needs to account for the use-case when the video encoding process takes longer than usual so that the same raw footage is not processed by multiple consumers.

As a Developer Associate, which of the following solutions would you recommend to address this use-case?

- ▫️ Use WaitTimeSeconds action to short poll and extend a message's visibility timeout
- ▫️ Use WaitTimeSeconds action to long poll and extend a message's visibility timeout
- ✅ Use ChangeMessageVisibility action to extend a message's visibility timeout
- ▫️ Use DelaySeconds action to delay a message's visibility timeout

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use ChangeMessageVisibility action to extend a message's visibility timeout»**

Visibility timeout ховає отримане повідомлення від інших споживачів: за замовчуванням 30 с, від 0 до 12 годин. `ChangeMessageVisibility` змінює його для конкретного повідомлення. Наприклад, timeout 5 хвилин, а через 3 хвилини його продовжують до 10. Продовжувати можна неодноразово, але не довше максимуму. Тож тут ставлять початковий timeout 1 хвилину і продовжують за потреби.

**❌ Неправильні варіанти:**
- *DelaySeconds* — затримує доставку нових повідомлень (для черги або через message timer для окремого повідомлення), а не видимість уже отриманого.
- *WaitTimeSeconds, short polling* — визначає, як чекати повідомлення, а не видимість.
- *WaitTimeSeconds, long polling* — так само не впливає на видимість.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibility.html>

</details>

---
