# CloudWatch / X-Ray / CloudTrail

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 5  
Теорія: [04-domain4-troubleshooting.md](../../04-domain4-troubleshooting.md#observability) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q20"></a>
### T07-Q20
*домен: офіційна мітка*

A company has more than 100 million members worldwide enjoying 125 million hours of TV shows and movies each day. The company uses AWS for nearly all its computing and storage needs, which use more than 10,000 server instances on AWS. This results in an extremely complex and dynamic networking environment where applications are constantly communicating inside AWS and across the Internet. Monitoring and optimizing its network is critical for the company.

The company needs a solution for ingesting and analyzing the multiple terabytes of real-time data its network generates daily in the form of flow logs. Which technology/service should the company use to ingest this data economically and has the flexibility to direct this data to other downstream systems?

- ▫️ Amazon Simple Queue Service (SQS)
- ▫️ Amazon Kinesis Firehose
- ✅ Amazon Kinesis Data Streams
- ▫️ AWS Glue

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Kinesis Data Streams»**

Kinesis Data Streams — масштабований і надійний сервіс потокових даних у реальному часі. Він приймає гігабайти даних на секунду з сотень тисяч джерел: кліки, події БД, транзакції, соцмережі, логи, геолокацію. Дані доступні за мілісекунди для дашбордів, виявлення аномалій, динамічного ціноутворення.

KDS зберігає порядок записів і дозволяє кільком застосункам читати й повторно читати їх у тому самому порядку. KCL передає всі записи одного partition key одному обробнику. Тому кілька застосунків легко читають один потік для підрахунку, агрегації чи фільтрації.

**❌ Неправильні варіанти:**
- *SQS* — черга для обміну повідомленнями між компонентами з підтвердженням кожного повідомлення. Підходить, коли важливий успіх чи збій окремого повідомлення, потрібні затримки і є один споживач. Для кількох споживачів AWS радить кілька черг.
- *Kinesis Firehose* — найпростіший спосіб завантажувати потоки в S3, Redshift, OpenSearch чи Splunk майже в реальному часі. Але KDS гнучкіший для власних застосунків, краще інтегрується з downstream і дешевший.
- *AWS Glue* — serverless-інтеграція даних (ETL) для аналітики; для реального часу не найкращий вибір.

Джерела: <https://aws.amazon.com/kinesis/data-streams/> · <https://aws.amazon.com/sqs/> · <https://aws.amazon.com/kinesis/data-firehose/>

</details>

---

<a id="t07-q38"></a>
### T07-Q38
*домен: офіційна мітка*

You have been asked by your Team Lead to enable detailed monitoring of the Amazon EC2 instances your team uses. As a Developer working on AWS CLI, which of the below command will you run?

- ✅ aws ec2 monitor-instances --instance-ids i-1234567890abcdef0
- ▫️ aws ec2 run-instances --image-id ami-09092360 --monitoring Enabled=true
- ▫️ aws ec2 run-instances --image-id ami-09092360 --monitoring State=enabled
- ▫️ aws ec2 monitor-instances --instance-id i-1234567890abcdef0

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «aws ec2 monitor-instances --instance-ids i-1234567890abcdef0»**

`aws ec2 monitor-instances --instance-ids <id>` вмикає detailed monitoring для вже запущеного інстансу.

Довідка: https://docs.aws.amazon.com/cli/latest/reference/ec2/monitor-instances.html

**❌ Неправильні варіанти:**
- *run-instances --monitoring Enabled=true* — це для запуску нового інстансу, а не для вже запущеного.
- *run-instances --monitoring State=enabled* — неправильний синтаксис.
- *monitor-instances --instance-id* — неправильний параметр: має бути `--instance-ids`.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-cloudwatch-new.html> · <https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html>

</details>

---

<a id="t09-q63"></a>
### T09-Q63
*домен: офіційна мітка*

As a Team Lead, you are expected to generate a report of the code builds for every week to report internally and to the client. This report consists of the number of code builds performed for a week, the percentage success and failure, and overall time spent on these builds by the team members. You also need to retrieve the CodeBuild logs for failed builds and analyze them in Athena.

Which of the following options will help achieve this?

- ✅ Enable S3 and CloudWatch Logs integration
- ▫️ Use Amazon EventBridge
- ▫️ Use AWS Lambda integration
- ▫️ Use AWS CloudTrail and deliver logs to S3

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable S3 and CloudWatch Logs integration»**

CodeBuild надсилає в CloudWatch метрики збірок: загальна кількість, невдалі, успішні, тривалість — на рівні проєкту і на рівні акаунта. Логи збірок пишуться в CloudWatch Logs і/або S3. З log groups дані можна експортувати в S3 для власного аналізу або завантаження в інші системи.

**❌ Неправильні варіанти:**
- *EventBridge* — інтегрується з CodeBuild, але задача — зберігати логи й робити запити до них.
- *Lambda + boto3* — можна читати логи програмно, але вбудована інтеграція CloudWatch і S3 простіша.
- *CloudTrail → S3* — записує API-виклики CodeBuild, а не логи збірок.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/monitoring-metrics.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3Export.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started-input-bucket-console.html>

</details>

---

<a id="t12-q40"></a>
### T12-Q40
*домен: офіційна мітка*

A developer has just integrated an AWS Lambda function to an Amazon API Gateway API. The integration has led to errors that the developer is unable to troubleshoot. The developer has decided to enable CloudWatch logging at the method level for the API Gateway API.

What are the key points of consideration while configuring configuring method-level logging for the API Gateway? (Select two)

- ✅ To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your user. The IAM role must also contain the following trust relationship statement
- ▫️ You are charged for accessing method-level and stage-level CloudWatch metrics, but not for API-level metrics
- ▫️ API Gateway API log groups or streams can only be deleted and recreated by redeploying the API
- ▫️ In access logging, only $context and $input variables are supported
- ✅ AWS Security Token Service(STS) is used by API Gateway for logging data to CloudWatch logs. Hence, AWS STS has to be enabled for the Region that you're using

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «To enable CloudWatch Logs for all or only some of the methods, you must also specify the ARN of an IAM role that enables API Gateway to write information to CloudWatch Logs on behalf of your user. The IAM role must also contain the following trust relationship statement» + «AWS Security Token Service(STS) is used by API Gateway for logging data to CloudWatch logs. Hence, AWS STS has to be enabled for the Region that you're using»**

- **IAM-роль для логів.** Щоб увімкнути CloudWatch Logs для всіх або частини методів, у налаштуваннях API (Settings → CloudWatch log role ARN) вказують ARN IAM-ролі. Ця роль дає API Gateway право писати в CloudWatch Logs, тобто має managed-політику `AmazonAPIGatewayPushToCloudWatchLogs`. У її trust policy principal — `apigateway.amazonaws.com`.
- **STS у регіоні.** API Gateway приймає цю роль через STS, тож STS має бути ввімкнений у регіоні. Якщо при введенні ARN ролі виникає помилка, перевірте налаштування STS в акаунті.

Детальніше: https://docs.aws.amazon.com/apigateway/latest/developerguide/stages.html#how-to-stage-settings-console

**❌ Неправильні варіанти:**
- *Access logging supports $context and $input* — підтримуються лише змінні `$context`. Access logging фіксує, хто і як звертався до API; log group можна створити власну або взяти наявну.
- *Charged for method- and stage-level metrics* — платні лише метрики на рівні методу; на рівні API і stage — безкоштовні.
- *Log groups only via redeploy* — log groups можна видалити в консолі CloudWatch, хоча не рекомендується: логи можуть перестати писатися. Тоді видаляють усю log group API і роблять redeploy, бо групи створюються під час деплою stage.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stages.html#how-to-stage-settings-console> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-logging.html#apigateway-cloudwatch-log-formats> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/view-cloudwatch-log-events-in-cloudwatch-console.html>

</details>

---

<a id="t12-q43"></a>
### T12-Q43
*домен: офіційна мітка*

An e-commerce application writes log files into Amazon S3. The application also reads these log files in parallel on a near real-time basis. The development team wants to address any data discrepancies that might arise when the application overwrites an existing log file and then tries to read that specific log file.

Which of the following options BEST describes the capabilities of Amazon S3 relevant to this scenario?

- ▫️ A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 might return the new data
- ▫️ A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 does not return any data
- ✅ A process replaces an existing object and immediately tries to read it. Amazon S3 always returns the latest version of the object
- ▫️ A process replaces an existing object and immediately tries to read it. Until the change is fully propagated, Amazon S3 might return the previous data

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A process replaces an existing object and immediately tries to read it. Amazon S3 always returns the latest version of the object»**

S3 дає **strong read-after-write consistency** автоматично, без втрати продуктивності чи доступності і без доплат:
- після запису нового об'єкта або перезапису наявного наступне читання одразу повертає останню версію;
- операції LIST теж strongly consistent і одразу відображають зміни.

Усі GET, PUT і LIST, а також зміни тегів, ACL і метаданих — strongly consistent.

**❌ Неправильні варіанти:**
- *Might return previous data* — так було до грудня 2020 року; тепер ні.
- *Does not return any data* — повертає нову версію.
- *Might return the new data* — повертає **завжди**, а не «можливо».

Джерела: <https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel> · <https://aws.amazon.com/s3/faqs/>

</details>

---
