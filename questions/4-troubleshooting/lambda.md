# AWS Lambda

**Домен 4 — Усунення несправностей та оптимізація** · питань: 15  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q48"></a>
### T01-Q48
*домен: офіційна мітка*

> ⚠️ **EventBridge rule.** → [детальніше](../../06-contradictions-and-traps.md#c14)

An IT company leverages CodePipeline to automate its release pipelines. The development team wants to write a Lambda function that will send notifications for state changes within the pipeline.

As a Developer Associate, which steps would you suggest to associate the Lambda function with the event source?

- ▫️ Use the CodePipeline console to set up a trigger for the Lambda function
- ▫️ Use the Lambda console to configure a trigger that invokes the Lambda function with CodePipeline as the event source
- ✅ Set up an Amazon EventBridge rule that uses CodePipeline as an event source with the target as the Lambda function
- ▫️ Set up an Amazon CloudWatch alarm that monitors status changes in Code Pipeline and triggers the Lambda function

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up an Amazon EventBridge rule that uses CodePipeline as an event source with the target as the Lambda function»**

Amazon EventBridge доставляє майже в реальному часі потік системних подій про зміни ресурсів AWS. Простими правилами події зіставляють і маршрутизують до цілей. EventBridge фіксує зміни стану pipeline, stage або action і за правилом викликає ціль, тут — Lambda-функцію.

**❌ Неправильні варіанти:**
- *Amazon CloudWatch alarm that monitors status changes in CodePipeline* — потрібна подія EventBridge, а не alarm (alarm працює з метриками).
- *Use the Lambda console to configure a trigger with CodePipeline as the event source* — CodePipeline не можна вибрати як джерело тригера в консолі Lambda.
- *Use the CodePipeline console to set up a trigger for the Lambda function* — консоль CodePipeline такого не вміє.

Джерела: <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html> · <https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html>

</details>

---

<a id="t02-q06"></a>
### T02-Q06
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Developer has recently created an application that uses an AWS Lambda function, an Amazon DynamoDB table, and also sends notifications using Amazon SNS. The application is not working as expected and the Developer needs to analyze what is happening across all components of the application.

What is the BEST way to analyze the issue?

- ▫️ Create an Amazon CloudWatch Events rule
- ▫️ Monitor the application with AWS Trusted Advisor
- ✅ Enable X-Ray tracing for the Lambda function
- ▫️ Assess the application with Amazon Inspector

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable X-Ray tracing for the Lambda function»**

AWS X-Ray дає наскрізне трасування розподілених застосунків: знаходить вузькі місця продуктивності, помилки в граничних випадках та інші складні проблеми. Дані з окремих сервісів збираються в один **trace**, по якому видно шлях запиту крізь кожен сервіс чи рівень і місце збою. **Service map** візуально показує зв'язки компонентів і їхню продуктивність.

**❌ Неправильні варіанти:**
- *CloudWatch Events rule* — запускає дії при зміні стану сервісів AWS.
- *Amazon Inspector* — автоматична оцінка безпеки та відповідності вимогам.
- *AWS Trusted Advisor* — рекомендації щодо best practices для ресурсів.

Джерела: <https://aws.amazon.com/xray/features/>

</details>

---

<a id="t02-q08"></a>
### T02-Q08
*домен: визначено автоматично (у джерелі: AWS Database)*

A critical application is hosted on AWS exposed by an HTTP API through Amazon API Gateway. The API is integrated with an AWS Lambda function and the application data is housed in an Amazon RDS for PostgreSQL DB instance, featuring 2 vCPUs and 16 GB of RAM.

The company has been receiving customer complaints about occasional HTTP 500 Internal Server Error responses from some API calls during unpredictable peak usage times. Amazon CloudWatch Logs has recorded "connection limit exceeded" errors. The company wants to ensure resilience in the application, with no unscheduled downtime for the database.

Which solution would best fit these requirements?

- ▫️ Double the RAM and vCPUs of the RDS instance.
- ▫️ Implement auto-scaling for the RDS instance based on connection count.
- ▫️ Use AWS Lambda to create a connection pool for the RDS instance.
- ✅ Use Amazon RDS Proxy and update the Lambda function to connect to the proxy.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon RDS Proxy and update the Lambda function to connect to the proxy»**

RDS Proxy об'єднує з'єднання з БД у пул і розділяє їх між клієнтами. Це зменшує навантаження на CPU і пам'ять бази, плавно обробляє сплески з'єднань і підвищує масштабованість і стійкість.

**❌ Неправильні варіанти:**
- *Double the RAM and vCPUs* — не вирішує проблему надмірної кількості з'єднань.
- *Auto-scaling based on connection count* — RDS такої функції не має.
- *Use AWS Lambda to create a connection pool* — для пулу з'єднань створено RDS Proxy; Lambda тут дорожча й обмежена в часі виконання.

Джерела: <https://aws.amazon.com/blogs/compute/using-amazon-rds-proxy-with-aws-lambda/>

</details>

---

<a id="t02-q48"></a>
### T02-Q48
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company has an application that logs all information to Amazon S3. Whenever there is a new log file, an AWS Lambda function is invoked to process the log files. The code works, gathering all of the necessary information. However, when checking the Lambda function logs, duplicate entries with the same request ID are found.

What is the BEST explanation for the duplicate entries?

- ▫️ There was an S3 outage, which caused duplicate entries of the same log file
- ▫️ The application stopped intermittently and then resumed
- ✅ The Lambda function failed, and the Lambda service retried the invocation with a delay
- ▫️ The S3 bucket name was specified incorrectly

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The Lambda function failed, and the Lambda service retried the invocation with a delay»**

З документації AWS: при помилці функцію можуть викликати кілька разів. Поведінка повторів залежить від типу помилки, клієнта, джерела подій і типу виклику. Наприклад, якщо асинхронно викликана функція повертає помилку, Lambda виконує її ще до двох разів. При асинхронному виклику події спершу потрапляють у чергу; якщо функція не встигає, події можуть губитися. Інколи функція отримує ту саму подію кілька разів навіть без помилок. Щоб не втрачати необроблені події, налаштовують dead-letter queue.

Отже, найімовірніше функція впала, і Lambda повторила виклик з тим самим request ID.

**❌ Неправильні варіанти:**
- *The S3 bucket name was specified incorrectly* — тоді невдалими були б усі спроби.
- *There was an S3 outage, which caused duplicate entries* — дублікатів файлів в S3 не буває: ключ об'єкта унікальний, повторне завантаження перезаписує файл. До того ж окремий виклик Lambda мав би інший request ID.
- *The application stopped intermittently and then resumed* — проблема в дублікатах з тим самим request ID.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html>

</details>

---

<a id="t03-q01"></a>
### T03-Q01
*домен: офіційна мітка*

```json
A developer has pushed a Lambda function that pushes data into an RDS MySQL database with the following Python code:
def handler(event, context):
    mysql = mysqlclient.connect()
    data = event['data']
    mysql.execute(f"INSERT INTO foo (bar) VALUES (${data});")
    mysql.close()
    return
On the first execution, the Lambda function takes 2 seconds to execute. On the second execution and all the subsequent ones, the Lambda function takes 1.9 seconds to execute.
What can be done to improve the execution time of the Lambda function?
```

- ✅ Move the database connection out of the handler
- ▫️ Change the runtime to Node.js
- ▫️ Upgrade the MySQL instance type
- ▫️ Increase the Lambda function RAM

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Move the database connection out of the handler»**

Тут з'єднання з БД створюється і закривається при кожному виклику функції, а це дорого за часом. Його треба винести за межі handler: тоді воно зберігається в execution context і повторно використовується між викликами:

```python
mysql = mysqlclient.connect()

def handler(event, context):
    data = event['data']
    mysql.execute(...)
```

**❌ Неправильні варіанти:**
- *Upgrade the MySQL instance type* — вузьке місце в об'єкті з'єднання, а не в інстансі MySQL.
- *Change the runtime to Node.js* — інша мова не покращить продуктивність.
- *Increase the Lambda function RAM* — більше пам'яті дає більше CPU, але це має сенс, лише якщо проблема в RAM/CPU. Тут винне з'єднання.

</details>

---

<a id="t03-q11"></a>
### T03-Q11
*домен: офіційна мітка*

A voting system hosted on-premise was recently migrated to AWS to lower cost, gain scalability, and to better serve thousands of concurrent users. When one of the AWS resource state changes, it generates an event and will need to trigger AWS Lambda. The AWS resource whose state changes and AWS Lambda does not have direct integration.

Which of the following methods can be used to trigger AWS Lambda?

- ▫️ AWS Lambda Custom Sources
- ▫️ Cron jobs to trigger AWS Lambda to check the state of your service
- ✅ Amazon EventBridge rules with AWS Lambda
- ▫️ Open a support ticket with AWS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon EventBridge rules with AWS Lambda»**

EventBridge реагує на події (зміни стану ресурсів) і за правилами викликає цілі, наприклад Lambda. Також запускає Lambda за розкладом: з фіксованою частотою або за cron-виразом. Це підходить, коли в ресурсу немає прямої інтеграції з Lambda.

**❌ Неправильні варіанти:**
- *AWS Lambda Custom Sources* — вигадано.
- *Open a support ticket with AWS* — підтримка лише підкаже, як налаштувати правило EventBridge.
- *Cron jobs to trigger Lambda* — знадобиться окремий сервер для cron; краще cron-вираз у EventBridge.

Джерела: <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html> · <https://docs.aws.amazon.com/eventbridge/latest/userguide/using-eventbridge-scheduler.html>

</details>

---

<a id="t04-q34"></a>
### T04-Q34
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating an AWS Lambda function to process a stream of data from an Amazon Kinesis Data Stream. When the Lambda function parses the data and encounters a missing field, it exits the function with an error. The function is generating duplicate records from the Kinesis stream. When the Developer looks at the stream output without the Lambda function, there are no duplicate records.

What is the reason for the duplicates?

- ▫️ The Lambda function did not advance the Kinesis stream point to the next record after the error
- ▫️ The Lambda function is not keeping up with the amount of data coming from the stream
- ▫️ The Lambda event source used asynchronous invocation, resulting in duplicate records
- ✅ The Lambda function did not handle the error, and the Lambda service attempted to reprocess the data

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The Lambda function did not handle the error, and the Lambda service attempted to reprocess the data»**

Помилки бувають двох типів: помилки виклику (запит відхилено до того, як функція його отримала) і помилки функції (код або runtime повернули помилку). Поведінка повторів залежить від типу помилки, типу виклику і джерела. Для event source mapping зі стріму (Kinesis) Lambda повторює **весь батч**. Тож функція не обробила помилку, Lambda повторила батч, і з'явилися дублікати.

**❌ Неправильні варіанти:**
- *Did not advance the Kinesis stream point* — Lambda обробляє записи батчами, а не «просуває вказівник» вручну.
- *Asynchronous invocation* — записи з Kinesis Lambda обробляє синхронно.
- *Not keeping up with the stream* — відставання дає затримку, а не дублікати; Lambda масштабується під потік.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-retries.html>

</details>

---

<a id="t06-q04"></a>
### T06-Q04
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer is creating a serverless application. The application looks up information about a customer using a separate Lambda function for each item such as address and phone number. The Developer has created branches in AWS Step Functions for each lookup function.

How can the Developer optimize the performance, so the lookups complete faster?

- ▫️ Use a Map state to iterate over all the items.
- ▫️ Use a Choice state to lookup the specific information required.
- ✅ Use a Parallel state to iterate over all the branches parallel.
- ▫️ Use a Wait state to reduce the wait time for function execution.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a Parallel state to iterate over all the branches parallel.»**

Стан Parallel (`"Type": "Parallel"`) створює паралельні гілки виконання в state machine Step Functions. Усі пошуки інформації виконуються одночасно, і застосунок працює швидше.

**❌ Неправильні варіанти:**
- *Choice state* — додає логіку розгалуження, але тут не потрібен і швидкості не додасть.
- *Wait state* — затримує виконання на заданий час.
- *Map state* — виконує однакові кроки для кожного елемента масиву у вхідних даних; тут потрібні різні гілки.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-parallel-state.html>

</details>

---

<a id="t06-q54"></a>
### T06-Q54
*домен: визначено автоматично (у джерелі: AWS Storage)*

An AWS Lambda function is being developed in a VPC. When a file is added to an Amazon S3 bucket, this Lambda function is triggered, processes the file, and logs the results into a file. These result and log files need to be accessible by other AWS services and on-premises resources.

What should the developer use to meet these requirements?

- ✅ Keep the result and log files in Amazon Elastic File System (EFS) accessible by Lambda functions.
- ▫️ Store the result and log files in Amazon S3 and append the new log entries to existing objects.
- ▫️ Use AWS Glue to consolidate and catalog all result and log files and append log entries.
- ▫️ Use Amazon DynamoDB to store the files and enable DynamoDB Streams to send notifications of changes.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Keep the result and log files in Amazon Elastic File System (EFS) accessible by Lambda functions.»**

Amazon EFS — масштабована, повністю керована NFS-файлова система для AWS і on-premises. Вона росте до петабайтів без перерв у роботі застосунків. Lambda може монтувати EFS, тож функції легко читають і пишуть великі обсяги даних і ділять файли між собою.

**❌ Неправильні варіанти:**
- *S3 + append* — S3 не вміє дописувати дані в наявний об'єкт.
- *DynamoDB + Streams* — DynamoDB зберігає дані, а Streams сповіщають про зміни, але це не файлове сховище для спільних файлів.
- *AWS Glue* — це ETL-сервіс для підготовки даних до аналітики, а не сховище файлів.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/services-efs.html>

</details>

---

<a id="t07-q45"></a>
### T07-Q45
*домен: офіційна мітка*

An IT company has migrated to a serverless application stack on the AWS Cloud with the compute layer being implemented via Lambda functions. The engineering managers would like to actively troubleshoot any failures in the Lambda functions.

As a Developer Associate, which of the following solutions would you suggest for this use-case?

- ▫️ Use Amazon EventBridge to identify and notify any failures in the Lambda code
- ▫️ Use CodeDeploy to identify and notify any failures in the Lambda code
- ✅ The developers should insert logging statements in the Lambda function code which are then available via CloudWatch logs
- ▫️ Use CodeCommit to identify and notify any failures in the Lambda code

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The developers should insert logging statements in the Lambda function code which are then available via CloudWatch logs»**

Під час виклику Lambda бувають два типи помилок:
- **invocation errors** — запит відхилено ще до того, як функція його отримала;
- **function errors** — помилку повернув код або runtime.

Повторні спроби й обробка помилок залежать від типу помилки, типу виклику і того, хто викликає функцію. Типові причини збоїв: дозволи, код, мережа, throttling, помилки 500/502 від Invoke API.

Щоб перевірити, що код працює правильно, додають логування. Lambda автоматично надсилає всі логи в log group `/aws/lambda/<function name>`.

Детальніше: https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html

**❌ Неправильні варіанти:**
- *EventBridge* — зазвичай запускає Lambda, але не виявляє помилок у її коді.
- *CodeCommit* — Git-репозиторій; помилок у коді Lambda не виявляє і не сповіщає.
- *CodeDeploy* — сервіс деплою; помилок у коді Lambda не виявляє і не сповіщає.

</details>

---

<a id="t08-q56"></a>
### T08-Q56
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company is running an application built on AWS Lambda functions. One Lambda function has performance issues when it has to download a 50 MB file from the internet every execution. This function is called multiple times a second.

What solution would give the BEST performance increase?

- ▫️ Cache the file in Amazon S3
- ✅ Cache the file in the /tmp directory
- ▫️ Put an Elastic Load Balancer in front of the Lambda function
- ▫️ Increase the Lambda maximum execution time

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Cache the file in the /tmp directory»**

Функція може використовувати директорію `/tmp`. Файл, закешований там, доступний наступним викликам у тому самому середовищі виконання, тож затримка менша.

ℹ️ В оригіналі вказано 512 MB для `/tmp`. Це лише значення за замовчуванням: зараз `/tmp` (ephemeral storage) налаштовується до **10 GB**.

**❌ Неправильні варіанти:**
- *Increase max execution time* — функція не впирається в timeout.
- *ELB in front of Lambda* — не зменшить затримку.
- *Cache in S3* — файл однаково доведеться завантажувати з S3 на кожен виклик.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html>

</details>

---

<a id="t09-q18"></a>
### T09-Q18
*домен: офіційна мітка*

The development team at a retail company is gearing up for the upcoming Thanksgiving sale and wants to make sure that the application's serverless backend running via Lambda functions does not hit latency bottlenecks as a result of the traffic spike.

As a Developer Associate, which of the following solutions would you recommend to address this use-case?

- ▫️ Configure Application Auto Scaling to manage Lambda reserved concurrency on a schedule
- ✅ Configure Application Auto Scaling to manage Lambda provisioned concurrency on a schedule
- ▫️ No need to make any special provisions as Lambda is automatically scalable because of its serverless nature
- ▫️ Add an Application Load Balancer in front of the Lambda functions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure Application Auto Scaling to manage Lambda provisioned concurrency on a schedule»**

Concurrency — кількість запитів, які функція обслуговує одночасно. Якщо функцію викликають, поки попередній запит ще обробляється, виділяється новий екземпляр. При стрибку трафіку частина запитів потрапляє на щойно створені екземпляри, тому має вищу затримку (cold start).

Provisioned concurrency заздалегідь ініціалізує екземпляри, тож усі запити обслуговуються з низькою затримкою. Application Auto Scaling керує нею двома способами:
- **за розкладом** — збільшити перед очікуваним піком;
- **за використанням** — зареєструвати target і створити scaling policy.

Детальніше: https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html

**❌ Неправильні варіанти:**
- *Reserved concurrency on a schedule* — reserved concurrency гарантує функції частину ліміту, але водночас обмежує її максимум. Application Auto Scaling нею не керує, і cold starts вона не прибирає.
- *ALB in front of Lambda* — сам ALB не допоможе функціям масштабуватися.
- *No special provisions* — Lambda масштабується сама, але cold starts і ліміти concurrency нікуди не зникають.

</details>

---

<a id="t09-q49"></a>
### T09-Q49
*домен: офіційна мітка*

As an AWS Certified Developer Associate, you have been hired to work with the development team at a company to create a REST API using the serverless architecture.

Which of the following solutions will you choose to move the company to the serverless architecture paradigm?

- ▫️ Route 53 with EC2 as backend
- ✅ API Gateway exposing Lambda Functionality
- ▫️ Fargate with Lambda at the front
- ▫️ Public-facing Application Load Balancer with ECS on Amazon EC2

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «API Gateway exposing Lambda Functionality»**

API Gateway — повністю керований сервіс для створення, публікації й захисту API будь-якого масштабу. API — це «вхідні двері» застосунку до даних і логіки бекенду. Lambda виконує код без серверів, і ви платите лише за час виконання. API Gateway відкриває функціональність Lambda через RESTful API, і обидва сервіси serverless.

Детальніше:
- https://aws.amazon.com/api-gateway/
- https://aws.amazon.com/lambda/

**❌ Неправильні варіанти:**
- *Fargate with Lambda at the front* — Lambda сама по собі не обробляє RESTful-запити; для цього потрібен API Gateway.
- *ALB + ECS on EC2* — ECS на EC2 не serverless.
- *Route 53 + EC2* — EC2 не serverless.

Джерела: <https://aws.amazon.com/serverless/> · <https://aws.amazon.com/api-gateway/>

</details>

---

<a id="t10-q25"></a>
### T10-Q25
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Kinesis ESM → on-failure destination (SNS).** → [детальніше](../../06-contradictions-and-traps.md#c21)

A Developer is writing an AWS Lambda function that processes records from an Amazon Kinesis Data Stream. The Developer must write the function so that it sends a notice to Administrators if it fails to process a batch of records.

How should the Developer write the function?

- ▫️ Separate the Lambda handler from the core logic
- ▫️ Push the failed records to an Amazon SQS queue
- ▫️ Use Amazon CloudWatch Events to send the processed data
- ✅ Configure an Amazon SNS topic as an on-failure destination

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure an Amazon SNS topic as an on-failure destination»**

Destinations надсилають результати асинхронного виклику як execution record без додаткового коду. Запис у JSON містить версію, час, контекст запиту, payload запиту, контекст відповіді і payload відповіді.

Для кожного статусу (Success чи Failure) можна вибрати один з чотирьох destinations: Lambda, SNS, SQS або EventBridge. Тут SNS як on-failure destination сповістить адміністраторів.

**❌ Неправильні варіанти:**
- *Separate handler from core logic* — гарна практика, але сповіщень не дає.
- *CloudWatch Events* — відстежує зміни стану ресурсів, а не результати виконання функції.
- *Push failed records to SQS* — SQS не сповіщає адміністраторів; для цього потрібен SNS.

Джерела: <https://aws.amazon.com/blogs/compute/introducing-aws-lambda-destinations/>

</details>

---

<a id="t11-q48"></a>
### T11-Q48
*домен: офіційна мітка*

A development team wants to deploy an AWS Lambda function that requires significant CPU utilization.

As a Developer Associate, which of the following would you suggest for reducing the average runtime of the function?

- ▫️ Deploy the function into multiple AWS Regions
- ▫️ Deploy the function with its CPU allocation set to the maximum amount
- ✅ Deploy the function with its memory allocation set to the maximum amount
- ▫️ Deploy the function using Lambda layers

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy the function with its memory allocation set to the maximum amount»**

Lambda виділяє CPU пропорційно до пам'яті. Пам'ять задають від 128 до 10 240 MB з кроком 1 MB. На 1 769 MB функція має еквівалент одного vCPU. Тож щоб отримати максимум обчислювальної потужності, ставлять максимум пам'яті.

Детальніше: https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html

**❌ Неправильні варіанти:**
- *Multiple Regions* — не додає потужності одному виклику.
- *Lambda layers* — ZIP з бібліотеками, runtime чи даними для повторного використання коду; потужності не додає.
- *CPU allocation* — такого параметра немає: CPU залежить від пам'яті.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---
