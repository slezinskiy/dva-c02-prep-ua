# CloudWatch / X-Ray / CloudTrail

**Домен 4 — Усунення несправностей та оптимізація** · питань: 41  
Теорія: [04-domain4-troubleshooting.md](../../04-domain4-troubleshooting.md#observability) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q03"></a>
### T01-Q03
*домен: офіційна мітка*

> ⚠️ **X-Ray SDK/daemon у maintenance з 25.02.2026 → ADOT/CloudWatch agent.** → [детальніше](../../07-outdated-and-wrong-answers.md#o05)

Which environment variable can be used by AWS X-Ray SDK to ensure that the daemon is correctly discovered on ECS?

- ✅ AWS_XRAY_DAEMON_ADDRESS
- ▫️ AWS_XRAY_TRACING_NAME
- ▫️ AWS_XRAY_DEBUG_MODE
- ▫️ AWS_XRAY_CONTEXT_MISSING

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS_XRAY_DAEMON_ADDRESS»**

Задає хост і порт, на яких слухає X-Ray daemon. За замовчуванням SDK використовує 127.0.0.1:2000 і для даних трейсів (UDP), і для sampling (TCP). Змінну встановлюють, якщо daemon слухає інший порт або працює на іншому хості — як це буває в ECS.

**❌ Неправильні варіанти:**
- *AWS_XRAY_TRACING_NAME* — задає ім'я сервісу, яке SDK пише в сегменти.
- *AWS_XRAY_CONTEXT_MISSING* — встановлюють у LOG_ERROR, щоб SDK не кидав виняток, коли код пробує записати дані, а відкритого сегмента немає.
- *AWS_XRAY_DEBUG_MODE* — встановлюють у TRUE, щоб SDK виводив логи в консоль замість налаштованого логера.

</details>

---

<a id="t01-q18"></a>
### T01-Q18
*домен: офіційна мітка*

> ⚠️ **On-prem → CloudWatch agent + IAM user.** → [детальніше](../../06-contradictions-and-traps.md#c15)

A development team has a mix of applications hosted on-premises as well as on EC2 instances. The on-premises application controls all applications deployed on the EC2 instances. In case of any errors, the team wants to leverage Amazon CloudWatch to monitor and troubleshoot the on-premises application.

As a Developer Associate, which of the following solutions would you suggest to address this use-case?

- ▫️ Upload log files from the on-premises server to S3 and let CloudWatch process the files from S3
- ✅ Configure the CloudWatch agent on the on-premises server by using IAM user credentials with permissions for CloudWatch
- ▫️ Configure CloudWatch Logs to directly read the logs from the on-premises server
- ▫️ Upload log files from the on-premises server to an EC2 instance which further forwards the logs to CloudWatch

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure the CloudWatch agent on the on-premises server by using IAM user credentials with permissions for CloudWatch»**

CloudWatch agent дає змогу:
- збирати системні метрики з on-premises серверів (гібридне середовище, сервери поза AWS);
- збирати логи з EC2 та on-premises серверів (Linux і Windows Server).

Щоб агент надсилав дані з on-premises сервера, треба вказати access key і secret key IAM-користувача з правами на CloudWatch.

**❌ Неправильні варіанти:**
- *Configure CloudWatch Logs to directly read the logs from the on-premises server* — вигадано: CloudWatch Logs не читає сервер напряму, лише через агента.
- *Upload log files … to an EC2 instance* та *Upload log files … to S3* — потребують значних кастомізацій і інтегруються з CloudWatch гірше, ніж готовий агент.

</details>

---

<a id="t01-q38"></a>
### T01-Q38
*домен: офіційна мітка*

A business-critical application is hosted on an Amazon EC2 instance and the latest update is in the testing phase. The business has requested a solution to track the average response time of the application and send a notification to the manager if it exceeds a particular threshold. The update will eventually be implemented in the production environment where the solution will be deployed on multiple EC2 instances.

Which of the following options would you combine to address the requirements of the business? (Select two)

- ▫️ Configure the application to write the response time to a log file on the instance. Install and configure the Amazon Inspector agent on the EC2 instances to read the logs and send the response time to Amazon EventBridge
- ▫️ Install and configure AWS Systems Manager Agent (SSM Agent) on the EC2 instances to monitor the response time and send the data to Amazon CloudWatch as a custom metric
- ▫️ Configure an EventBridge custom rule to send an Amazon Simple Notification Service (Amazon SNS) notification when the average of the response time metric exceeds the threshold
- ✅ Configure the application to write the response time to a log file on the EC2 instance. Install and configure the Amazon CloudWatch agent on the EC2 instance to stream the application logs to CloudWatch Logs. Create a metric filter for the response time from the log file
- ✅ Create a CloudWatch alarm to send an Amazon Simple Notification Service (Amazon SNS) notification when the average of the response time metric exceeds the threshold

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Write the response time to a log file… install the CloudWatch agent to stream logs to CloudWatch Logs… create a metric filter» та «Create a CloudWatch alarm to send an SNS notification when the average response time exceeds the threshold»**

Unified CloudWatch agent дає змогу:
- збирати внутрішні системні метрики з EC2 у будь-якій ОС;
- збирати системні метрики з on-premises серверів;
- отримувати custom metrics із застосунків через протоколи StatsD і collectd;
- збирати логи з EC2 та on-premises серверів (Linux, Windows Server).

Зібрані метрики зберігаються в CloudWatch як звичайні (namespace за замовчуванням — `CWAgent`). Metric filter перетворює дані логів на метрику. Metric alarm стежить за метрикою і при перетині порогу виконує дії: SNS-сповіщення, дії EC2/Auto Scaling, створення OpsItem у Systems Manager. Тут alarm у стані ALARM надсилає SNS-сповіщення.

**❌ Неправильні варіанти:**
- *Amazon Inspector agent…* — Inspector сканує вразливості EC2, Lambda і контейнерів, а логи не збирає.
- *EventBridge custom rule…* — EventBridge може реагувати на спрацювання CloudWatch alarm, але alarm і так потрібен, тож простіше одразу надіслати SNS з alarm.
- *SSM Agent…* — SSM Agent дає Systems Manager змогу керувати інстансами, але логи в CloudWatch не надсилає (хоча Systems Manager може автоматизувати встановлення CloudWatch agent).

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html>

</details>

---

<a id="t01-q43"></a>
### T01-Q43
*домен: офіційна мітка*

You have been collecting AWS X-Ray traces across multiple applications and you would now like to index your XRay traces to search and filter through them efficiently.

What should you use in your instrumentation?

- ▫️ Sampling
- ▫️ Metadata
- ▫️ Segments
- ✅ Annotations

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Annotations»**

AWS X-Ray допомагає аналізувати розподілені застосунки, у тому числі збирати дані між акаунтами (агент може припускати роль в іншому акаунті).

Annotations — прості пари key-value, які **індексуються** для filter expressions. Їх використовують, щоб групувати трейси в консолі або фільтрувати через API `GetTraceSummaries`. X-Ray індексує до 50 annotations на трейс.

**❌ Неправильні варіанти:**
- *Metadata* — пари key-value з будь-якими значеннями (об'єкти, списки), але **не індексуються**. Для даних, що треба зберегти, але не шукати.
- *Segments* — дані про роботу, які надсилають обчислювальні ресурси (ім'я ресурсу, деталі запиту, виконана робота).
- *Sampling* — алгоритм вибору запитів для трасування (за замовчуванням перший запит щосекунди + 5% решти).

</details>

---

<a id="t02-q02"></a>
### T02-Q02
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A critical application runs on an Amazon EC2 instance. A Developer has configured a custom Amazon CloudWatch metric that monitors application availability with a data granularity of 1 second. The Developer must be notified within 30 seconds if the application experiences any issues.

What should the Developer do to meet this requirement?

- ▫️ Specify an Amazon SNS topic for alarms when issuing the put-metric-data AWS CLI command.
- ✅ Configure a high-resolution CloudWatch alarm and use Amazon SNS to send the alert.
- ▫️ Use a default CloudWatch metric, configure an alarm, and use Amazon SNS to send the alert.
- ▫️ Use Amazon CloudWatch Logs Insights and trigger an Amazon Eventbridge rule to send a notification.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure a high-resolution CloudWatch alarm and use Amazon SNS to send the alert»**

Для high-resolution метрики можна створити high-resolution alarm з періодом 10 або 30 секунд (або звичайний з періодом, кратним 60 с). High-resolution alarms коштують дорожче. Сповіщення за alarm надсилає Amazon SNS. Так виконується вимога «повідомити протягом 30 с».

**❌ Неправильні варіанти:**
- *Specify an SNS topic when issuing put-metric-data* — ця команда не приймає SNS-топік.
- *CloudWatch Logs Insights + EventBridge* — Logs Insights не створює alarms на custom metrics.
- *Use a default CloudWatch metric* — стандартної метрики доступності застосунку немає, і її роздільна здатність нижча.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#high-resolution-alarms>

</details>

---

<a id="t02-q10"></a>
### T02-Q10
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

An application serves customers in several different geographical regions. Information about the location users connect from is written to logs stored in Amazon CloudWatch Logs. The company needs to publish an Amazon CloudWatch custom metric that tracks connections for each location.

Which approach will meet these requirements?

- ▫️ Create a CloudWatch Logs Insights query to extract the location information from the logs and to create a custom metric with location as a dimension.
- ✅ Create a CloudWatch metric filter to extract metrics from the log files with location as a dimension.
- ▫️ Configure a CloudWatch Events rule that creates a custom metric from the CloudWatch Logs group.
- ▫️ Stream data to an Amazon Elasticsearch cluster in near-real time and export a custom metric.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a CloudWatch metric filter to extract metrics from the log files with location as a dimension»**

Metric filters задають терміни й шаблони, які CloudWatch Logs шукає в даних логів, і перетворюють знайдене на числові метрики (для графіків і alarms). Метриці з фільтра можна призначити dimensions і одиницю виміру, тут — dimension з локацією.

**❌ Неправильні варіанти:**
- *CloudWatch Logs Insights query … to create a custom metric* — Logs Insights не створює custom metrics.
- *CloudWatch Events rule that creates a custom metric* — правила подій цього не вміють.
- *Stream data to Elasticsearch and export a custom metric* — це не спосіб створити custom metric у CloudWatch.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t02-q34"></a>
### T02-Q34
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Скрипт/cron → put-metric-data.** → [детальніше](../../06-contradictions-and-traps.md#c15)

A Developer is writing code to run in a cron job on an Amazon EC2 instance that sends status information about the application to Amazon CloudWatch.

Which method should the Developer use?

- ▫️ Use the CloudWatch console with detailed monitoring.
- ✅ Use the AWS CLI put-metric-data command.
- ▫️ Use the unified CloudWatch agent to publish custom metrics.
- ▫️ Use the AWS CLI put-metric-alarm command.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the AWS CLI put-metric-data command»**

`put-metric-data` публікує точки даних у CloudWatch і прив'язує їх до вказаної метрики; якщо метрики немає, CloudWatch її створює. Для cron-скрипта на EC2 цього досить.

**❌ Неправильні варіанти:**
- *put-metric-alarm* — створює або оновлює alarm.
- *Unified CloudWatch agent* — тут не обов'язковий: досить AWS CLI в cron.
- *CloudWatch console with detailed monitoring* — detailed monitoring лише частіше (раз на хвилину) надсилає стандартні метрики, custom-даних не збирає.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-data.html>

</details>

---

<a id="t03-q06"></a>
### T03-Q06
*домен: офіційна мітка*

> ⚠️ **RAM скриптом → custom metric.** → [детальніше](../../06-contradictions-and-traps.md#c15)

Your company manages hundreds of EC2 instances running on Linux OS. The instances are configured in several Availability Zones in the eu-west-3 region. Your manager has requested to collect system memory metrics on all EC2 instances using a script.

Which of the following solutions will help you collect this data?

- ▫️ Extract RAM statistics using the instance metadata
- ✅ Use a cron job on the instances that pushes the EC2 RAM statistics as a Custom metric into CloudWatch
- ▫️ Extract RAM statistics from the standard CloudWatch metrics for EC2 instances
- ▫️ Extract RAM statistics using X-Ray

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a cron job on the instances that pushes the EC2 RAM statistics as a Custom metric into CloudWatch»**

Стандартні метрики EC2 не містять пам'яті. Скрипти моніторингу CloudWatch для Linux (Perl) показують, як публікувати custom metrics: пам'ять, swap, місце на диску. За cron-розкладом вони надсилають дані в CloudWatch кожні x хвилин. (Сьогодні те саме робить CloudWatch agent.)

**❌ Неправильні варіанти:**
- *Instance metadata* — метадані містять hostname, events, security groups тощо; щодо RAM там лише ID RAM disk, заданого при запуску.
- *Standard CloudWatch metrics* — EC2 надсилає метрики кожні 5 хв (з detailed monitoring — кожну хвилину), але використання пам'яті серед них немає.
- *X-Ray* — трасування запитів у розподілених застосунках, а не метрики RAM.

</details>

---

<a id="t03-q34"></a>
### T03-Q34
*домен: офіційна мітка*

A company has several Linux-based EC2 instances that generate various log files which need to be analyzed for security and compliance purposes. The company wants to use Kinesis Data Streams (KDS) to analyze this log data.

Which of the following is the most optimal way of sending log data from the EC2 instances to KDS?

- ▫️ Run cron job on each of the instances to collect log data and send it to Kinesis Data Streams
- ✅ Install and configure Kinesis Agent on each of the instances
- ▫️ Install AWS SDK on each of the instances and configure it to send the necessary files to Kinesis Data Streams
- ▫️ Use Kinesis Producer Library (KPL) to collect and ingest data from each EC2 instance

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install and configure Kinesis Agent on each of the instances»**

Kinesis Agent — окремий Java-застосунок, який збирає дані й надсилає їх у Kinesis Data Streams. Він постійно стежить за набором файлів і надсилає нові дані в стрім, сам обробляє ротацію файлів, checkpoint'и і повтори після збоїв, а також публікує метрики в CloudWatch. Ставиться на Linux-сервери (веб, логи, БД); у конфігурації вказують файли й стрім. Може попередньо обробляти записи (`dataProcessingOptions`).

**❌ Неправильні варіанти:**
- *Cron job to send logs* — можливо, але треба писати власний код (відстеження змін, повтори тощо).
- *AWS SDK on each instance* — знову власний код для відстеження нових даних у логах.
- *Kinesis Producer Library (KPL)* — бібліотека для запису у стрім з коду застосунку; для збору лог-файлів Agent простіший.

</details>

---

<a id="t03-q62"></a>
### T03-Q62
*домен: офіційна мітка*

An organization uses Alexa as its intelligent assistant to improve productivity throughout the organization. A group of developers manages custom Alexa Skills written in Node.Js to control conference-room equipment settings and start meetings using voice activation. The manager has requested developers that all functions code should be monitored for error rates with the possibility of creating alarms on top of them.

Which of the following options should be chosen? (select two)

- ▫️ X-Ray
- ▫️ CloudTrail
- ✅ CloudWatch Metrics
- ▫️ SSM
- ✅ CloudWatch Alarms

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «CloudWatch Metrics» та «CloudWatch Alarms»**

CloudWatch збирає логи, метрики й події, показує їх на дашбордах і дає змогу корелювати метрики й логи. Alarms спрацьовують на порогові значення або на аномалії (ML).

- **Metrics** — змінні, які вимірюються для ресурсів і застосунків; зберігаються 15 місяців. Точки з періодом < 60 с (high-resolution) — 3 години; 60 с — 15 днів; 5 хв — 63 дні; 1 год — 455 днів.
- **Alarms** стежать за метрикою протягом заданого часу і при перетині порогу виконують дії: SNS-сповіщення або політику Auto Scaling. Стани alarm: **OK**, **ALARM**, **INSUFFICIENT_DATA**.

**❌ Неправильні варіанти:**
- *X-Ray* — трасування запитів у розподілених застосунках, а не метрики й alarms.
- *CloudTrail* — аудит API-викликів (хто і що викликав), а не моніторинг.
- *Systems Manager* — групування ресурсів і операційні дії, метрики й alarms не налаштовує.

Джерела: <https://aws.amazon.com/cloudwatch/> · <https://aws.amazon.com/cloudtrail/> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html>

</details>

---

<a id="t04-q02"></a>
### T04-Q02
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team wants to instrument their code to provide more detailed information to AWS X-Ray than simple outgoing and incoming requests. This will generate large amounts of data, so the Development team wants to implement indexing so they can filter the data.

What should the Development team do to achieve this?

- ▫️ Install required plugins for the appropriate AWS SDK
- ▫️ Configure the necessary X-Ray environment variables
- ✅ Add annotations to the segment document
- ▫️ Add metadata to the segment document

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add annotations to the segment document»**

X-Ray SDK записує дані про вхідні й вихідні запити, використані ресурси AWS і сам застосунок. До segment document можна додати власну інформацію як annotations і metadata — на будь-який сегмент чи підсегмент.

- **Annotations** — прості пари key-value, **індексуються** для filter expressions (групування трейсів у консолі, `GetTraceSummaries`). До 50 на трейс.
- **Metadata** — key-value з будь-якими значеннями (об'єкти, списки), **не індексуються**.

Щоб фільтрувати, потрібні annotations.

**❌ Неправильні варіанти:**
- *Add metadata* — не індексуються.
- *Configure X-Ray environment variables* — індексування не дадуть.
- *Install required plugins for the AWS SDK* — плагіни не потрібні, annotations — вбудована можливість X-Ray.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-annotations>

</details>

---

<a id="t04-q31"></a>
### T04-Q31
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer is publishing custom metrics for Amazon EC2 using the Amazon CloudWatch CLI. The Developer needs to add further context to the metrics being published by organizing them by EC2 instance and Auto Scaling Group.

What should the Developer add to the CLI command when publishing the metrics using put-metric-data

- ▫️ The --statistic-values parameter
- ✅ 1. The --dimensions parameter
- ▫️ The --metric-name parameter
- ▫️ The --namespace parameter

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The --dimensions parameter»**

Власні метрики публікують у CloudWatch через CLI або API; дані зберігаються як точки з часовими мітками (можна публікувати й агреговані statistic sets). Параметр `--dimensions` уточнює, що це за метрика і які дані вона несе: до 10 dimensions на метрику, кожна — пара ім'я–значення. Так метрики групуються, наприклад, за InstanceId і AutoScalingGroupName.

**❌ Неправильні варіанти:**
- *--namespace* — контейнер для метрик, а для деталізації потрібні dimensions.
- *--statistic-values* — для публікації statistic sets.
- *--metric-name* — лише ім'я метрики.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html>

</details>

---

<a id="t04-q64"></a>
### T04-Q64
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Гібрид, системні метрики → CloudWatch agent.** → [детальніше](../../06-contradictions-and-traps.md#c15)

A Development team manage a hybrid cloud environment. They would like to collect system-level metrics from on-premises servers and Amazon EC2 instances. How can the Development team collect this information MOST efficiently?

- ▫️ Install the CloudWatch agent on the EC2 instances and use a cron job on the on-premises servers
- ✅ Install the CloudWatch agent on the on-premises servers and EC2 instances
- ▫️ Use CloudWatch detailed monitoring for both EC2 instances and on-premises servers
- ▫️ Use CloudWatch for monitoring EC2 instances and custom AWS CLI scripts using the put-metric-data API

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install the CloudWatch agent on the on-premises servers and EC2 instances»**

Unified CloudWatch agent ставиться і на on-premises сервери, і на EC2 з різними ОС. Він:
- збирає розширені системні метрики з EC2 (зокрема з гостьової ОС);
- збирає системні метрики з on-premises серверів (гібрид, сервери поза AWS);
- отримує custom metrics через StatsD і collectd;
- збирає логи з EC2 та on-premises (Linux і Windows Server).

**❌ Неправильні варіанти:**
- *CloudWatch for EC2 + custom CLI scripts with put-metric-data* — доведеться писати й підтримувати скрипти; агент усе це вже вміє.
- *Agent on EC2 + cron job on-prem* — незрозуміло, що робитиме cron.
- *Detailed monitoring for EC2 and on-premises* — для on-premises серверів це нічого не дає.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html>

</details>

---

<a id="t05-q15"></a>
### T05-Q15
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A company is deploying a new serverless application with an AWS Lambda function. A developer ran some test invocations using the AWS CLI. The function is invoking correctly and returning a success message, but not log data is being generated in Amazon CloudWatch Logs. The developer waited for 15 minutes but the log data is not showing up.

What is the most likely explanation for this issue?

- ▫️ The function configuration does not have CloudWatch Logs configured as a success destination.
- ▫️ A log group and log stream has not been configured for the function in CloudWatch Logs.
- ✅ The function execution role does not have permission to write log data to CloudWatch Logs.
- ▫️ The Lambda function does not have any explicit log statements for the log data to send it to CloudWatch Logs.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The function execution role does not have permission to write log data to CloudWatch Logs.»**

Lambda автоматично передає метрики в CloudWatch. Коли дозволи налаштовані, усі логи коду потрапляють у log group `/aws/lambda/<function name>`. Логи можуть з'явитися через 5–10 хвилин після виклику. Якщо код виконується, а логів немає й через кілька хвилин, найімовірніше execution role не має дозволів на запис у CloudWatch Logs: `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`. Ці дозволи дає managed-політика `AWSLambdaBasicExecutionRole`.

**❌ Неправильні варіанти:**
- *No explicit log statements* — щоб отримати змістовні логи, оператори логування потрібні. Але найімовірніша причина повної відсутності логів — дозволи.
- *CloudWatch Logs not configured as success destination* — CloudWatch Logs не налаштовується як destination Lambda.
- *Log group/stream not configured* — log group і stream створюються автоматично, якщо є дозволи.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html>

</details>

---

<a id="t05-q21"></a>
### T05-Q21
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **On-prem → CloudWatch agent + IAM credentials.** → [детальніше](../../06-contradictions-and-traps.md#c15)

An application uses both Amazon EC2 instances and on-premises servers. The on-premises servers are a critical component of the application, and a developer wants to collect metrics and logs from these servers. The developer would like to use Amazon CloudWatch.

How can the developer accomplish this?

- ▫️ Write a batch script that uses system utilities to collect performance metrics and application logs. Upload the metrics and logs to CloudWatch.
- ✅ Install the CloudWatch agent on the on-premises servers and specify IAM credentials with permissions to CloudWatch.
- ▫️ Install an AWS SDK on the on-premises servers that automatically sends logs to CloudWatch.
- ▫️ Install the CloudWatch agent on the on-premises servers and specify an IAM role with permissions to CloudWatch.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install the CloudWatch agent on the on-premises servers and specify IAM credentials with permissions to CloudWatch.»**

Пакет CloudWatch agent можна завантажити через SSM Run Command або за посиланням на S3. Під час налаштування вказують облікові дані: access key і secret key IAM-користувача з дозволами на CloudWatch. Після цього on-premises сервери автоматично надсилають метрики та логи в CloudWatch, і їх можна моніторити разом із ресурсами AWS.

⚠️ Відповідь спрощена й частково застаріла. On-premises серверам можна дати роль через SSM hybrid activation або IAM Roles Anywhere, тобто твердження «роль вказати неможливо» сьогодні неточне. Але в межах цього питання правильний варіант — IAM credentials. Див. 07.

**❌ Неправильні варіанти:**
- *Specify an IAM role* — за логікою питання on-premises серверу роль не призначити, тому потрібні access keys.
- *Batch script with system utilities* — агент кращий, і в будь-якому разі потрібні дозволи на CloudWatch.
- *AWS SDK that automatically sends logs* — агент кращий, і в будь-якому разі потрібні дозволи на CloudWatch.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html>

</details>

---

<a id="t05-q25"></a>
### T05-Q25
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer wants to debug an application by searching and filtering log data. The application logs are stored in Amazon CloudWatch Logs. The Developer creates a new metric filter to count exceptions in the application logs. However, no results are returned from the logs.

What is the reason that no filtered results are being returned?

- ▫️ A setup of the Amazon CloudWatch interface VPC endpoint is required for filtering the CloudWatch Logs in the VPC
- ✅ CloudWatch Logs only publishes metric data for events that happen after the filter is created
- ▫️ Metric data points to logs groups can be filtered only after they are exported to an Amazon S3 bucket
- ▫️ The log group for CloudWatch Logs should be first streamed to Amazon Elasticsearch Service before filtering returns the results

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CloudWatch Logs only publishes metric data for events that happen after the filter is created»**

Metric filters задають терміни й шаблони для пошуку в логах і перетворюють записи на числові метрики CloudWatch. На ці метрики можна будувати графіки й ставити alarms, з будь-якою статистикою, включно з перцентилями.

Фільтри не працюють ретроспективно: вони публікують точки метрики лише для подій після свого створення. Тестовий перегляд показує перші 50 рядків і не відображає записи з часовою міткою, ранішою за створення метрики.

**❌ Неправильні варіанти:**
- *VPC interface endpoint required* — VPC endpoint для цього не потрібен.
- *Stream to Elasticsearch first* — стрімити в Elasticsearch (тепер OpenSearch) не потрібно.
- *Export to S3 first* — експортувати логи в S3 не потрібно.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t05-q35"></a>
### T05-Q35
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application is instrumented to generate traces using AWS X-Ray and generates a large amount of trace data. A Developer would like to use filter expressions to filter the results to specific key-value pairs added to custom subsegments.

How should the Developer add the key-value pairs to the custom subsegments?

- ▫️ Add metadata to the custom subsegments
- ✅ Add annotations to the custom subsegments
- ▫️ Add the key-value pairs to the Trace ID
- ▫️ Setup sampling for the custom subsegments

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add annotations to the custom subsegments»**

До сегментів, які створює X-Ray SDK, і до власних субсегментів можна додавати annotations і metadata:
- **Annotations** — пари key-value зі значеннями string, number або Boolean. Вони **індексуються**, тому працюють у filter expressions, групуванні трасувань у консолі та `GetTraceSummaries`.
- **Metadata** — значення будь-якого типу (об'єкти, списки), але **не індексуються** і для пошуку не підходять.

Тож annotations на custom subsegments дозволять фільтрувати трасування в X-Ray.

ℹ️ Підтримка X-Ray SDK і daemon завершується 25.02.2027; рекомендований шлях — ADOT/OpenTelemetry. Для DVA-C02 концепція annotations і metadata залишається ключовою.

**❌ Неправильні варіанти:**
- *Metadata* — не індексується, тож фільтрувати за нею не можна.
- *Key-value pairs in the Trace ID* — так зробити неможливо.
- *Sampling* — це механізм, яким X-Ray надсилає в API лише статистично значущу вибірку.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-java-segment.html>

</details>

---

<a id="t05-q43"></a>
### T05-Q43
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Development team has deployed several applications running on an Auto Scaling fleet of Amazon EC2 instances. The Operations team have asked for a display that shows a key performance metric for each application on a single screen for monitoring purposes.

What steps should a Developer take to deliver this capability using Amazon CloudWatch?

- ▫️ Create a custom alarm with a unique metric name for each application
- ▫️ Create a custom event with a unique metric name for each application
- ✅ Create a custom namespace with a unique metric name for each application
- ▫️ Create a custom dimension with a unique metric name for each application

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a custom namespace with a unique metric name for each application»**

Namespace — це контейнер для метрик CloudWatch. Метрики з різних namespaces ізольовані, тож дані різних застосунків не потраплять випадково в одну статистику. Власний namespace з унікальними іменами метрик для кожного застосунку дозволить бачити метрики кожного з них окремо в одному поданні.

**❌ Неправильні варіанти:**
- *Custom dimension* — dimension уточнює, що саме описує метрика, а не відокремлює застосунки.
- *Custom event* — події не організовують метрики для відображення.
- *Custom alarm* — alarm запускає дії при досягненні порога; до організації метрик він не має стосунку.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html>

</details>

---

<a id="t05-q46"></a>
### T05-Q46
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application has been instrumented to use the AWS X-Ray SDK to collect data about the requests the application serves. The Developer has set the user field on segments to a string that identifies the user who sent the request.

How can the Developer search for segments associated with specific users?

- ▫️ By using the GetTraceGraph API with a filter expression
- ▫️ Use a filter expression to search for the user field in the segment annotations
- ✅ By using the GetTraceSummaries API with a filter expression
- ▫️ Use a filter expression to search for the user field in the segment metadata

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «By using the GetTraceSummaries API with a filter expression»**

Segment document передає в X-Ray інформацію про сегмент. Він може мати до 64 KB і містити повний сегмент із субсегментами, фрагмент незавершеного запиту або окремий субсегмент. Надсилається напряму через `PutTraceSegments`.

Мінімальний сегмент:
```
{ "name": "example.com", "id": "70de5b6f19ff9a0a", "start_time": 1.478293361271E9,
  "trace_id": "1-581cf771-a006649127e371903a2de979", "end_time": 1.478293361449E9 }
```

Частину полів сегмента X-Ray індексує для filter expressions. Якщо записати в поле `user` унікальний ідентифікатор, сегменти конкретного користувача можна знайти в консолі X-Ray або через `GetTraceSummaries`.

**❌ Неправильні варіанти:**
- *GetTraceGraph with filter* — повертає service graph для вказаних trace ID, а не шукає їх.
- *User field in metadata* — поле `user` не є частиною metadata, а metadata не індексуються.
- *User field in annotations* — `user` — це окреме поле сегмента, а не annotation.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html>

</details>

---

<a id="t06-q06"></a>
### T06-Q06
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer is attempting to call the Amazon CloudWatch API and is receiving HTTP 400: ThrottlingException errors intermittently. When a call fails, no data is retrieved.

What best practice should the Developer first attempt to resolve this issue?

- ▫️ Analyze the applications and remove the API call
- ▫️ Contact AWS Support for a limit increase
- ▫️ Use the AWS CLI to get the metrics
- ✅ Retry the call with exponential backoff

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Retry the call with exponential backoff»**

`PutMetricData` іноді повертає `400 ThrottlingException`. Запити до CloudWatch обмежуються окремо для кожного акаунта в кожному регіоні. У ліміт входять усі виклики: від ваших застосунків, CloudWatch Agent, CLI і консолі.

AWS радить такі способи зменшити кількість викликів:
- **Розподіляти виклики в часі.** Для метрики з роздільністю 1 хв маєте цілу хвилину на відправку; додайте jitter.
- **Об'єднувати метрики в один виклик.** Один `PutMetricData` вміщує багато метрик і точок; можна також надсилати попередньо агреговані дані (StatisticSet).
- **Повторювати виклик з exponential backoff і jitter.**

Збільшення ліміту AWS радить лише після цих кроків, і воно може суттєво збільшити рахунок. Тож із наведених варіантів перший крок — повторити виклик з exponential backoff.

**❌ Неправильні варіанти:**
- *Contact AWS Support for a limit increase* — спершу варто спробувати інші рішення.
- *Use the AWS CLI* — CLI робить ті самі API-виклики.
- *Remove the API call* — пропадуть важливі дані моніторингу.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-400-error-throttling/>

</details>

---

<a id="t06-q17"></a>
### T06-Q17
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

There are multiple AWS accounts across multiple regions managed by a company. The operations team require a single operational dashboard that displays some key performance metrics from these accounts and regions. What is the SIMPLEST solution?

- ▫️ Create an AWS Lambda function that collects metrics from each account and region and pushes the metrics to the account where the dashboard has been created
- ▫️ Create an Amazon CloudTrail trail that applies to all regions and deliver the logs to a single Amazon S3 bucket. Create a dashboard using the data in the bucket
- ✅ Create an Amazon CloudWatch cross-account cross-region dashboard
- ▫️ Create an Amazon CloudWatch dashboard in one account and region and import the data from the other accounts and regions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Amazon CloudWatch cross-account cross-region dashboard»**

Cross-account cross-Region dashboard зводить дані CloudWatch із кількох акаунтів і регіонів на один дашборд. З нього видно весь застосунок, і можна переходити до детальніших дашбордів без перелогінювання й перемикання регіонів. Створюється в консолі або програмно.

**❌ Неправильні варіанти:**
- *Dashboard in one account, import data* — складніше й не потрібно.
- *Lambda collecting metrics* — непросте рішення.
- *CloudTrail trail → S3 → dashboard* — CloudTrail логує API-активність, а не метрики продуктивності.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_xaxr_dashboard.html>

</details>

---

<a id="t06-q55"></a>
### T06-Q55
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

Customers who use a REST API have reported performance issues. A Developer needs to measure the time between when API Gateway receives a request from a client and when it returns a response to the client.

Which metric should the Developer monitor?

- ▫️ 5XXError
- ▫️ CacheHitCount
- ✅ Latency
- ▫️ IntegrationLatency

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Latency»**

Метрика `Latency` — час від отримання запиту клієнта до повернення йому відповіді. Вона включає `IntegrationLatency` і власні накладні витрати API Gateway.

**❌ Неправильні варіанти:**
- *IntegrationLatency* — лише час від передачі запиту в бекенд до отримання відповіді від нього.
- *CacheHitCount* — кількість запитів, обслужених з кешу API.
- *5XXError* — кількість серверних помилок.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-metrics-and-dimensions.html>

</details>

---

<a id="t07-q11"></a>
### T07-Q11
*домен: офіційна мітка*

> ⚠️ **RAM → custom metric PutMetricData.** → [детальніше](../../06-contradictions-and-traps.md#c15)

As a developer, you are looking at creating a custom configuration for Amazon EC2 instances running in an Auto Scaling group. The solution should allow the group to auto-scale based on the metric of 'average RAM usage' for your Amazon EC2 instances.

Which option provides the best solution?

- ✅ Create a custom metric in CloudWatch and make your instances send data to it using PutMetricData. Then, create an alarm based on this metric
- ▫️ Migrate your application to AWS Lambda
- ▫️ Enable detailed monitoring for EC2 and ASG to get the RAM usage data and create a CloudWatch Alarm on top of it
- ▫️ Create a custom alarm for your ASG and make your instances trigger the alarm using PutAlarmData API

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a custom metric in CloudWatch and make your instances send data to it using PutMetricData. Then, create an alarm based on this metric»**

EC2 не надсилає використання RAM у CloudWatch за замовчуванням. Тому створюють custom metric, наприклад скриптом з AWS CLI, надсилають дані через `PutMetricData` і будують на цій метриці alarm.

Метрики сервісів AWS мають стандартну роздільність. Custom metric можна зробити standard або high resolution. High resolution зберігається з точністю 1 с і читається з періодом 1, 5, 10, 30 с або кратним 60 с. Але кожен виклик `PutMetricData` платний, тож часті виклики коштують дорожче.

ℹ️ На практиці RAM збирає CloudWatch agent, який сам публікує метрики.

**❌ Неправильні варіанти:**
- *PutAlarmData API* — такого API немає, і кожен інстанс мав би знати RAM інших інстансів.
- *Detailed monitoring* — змінює лише частоту метрик з 5 хв на 1 хв, а RAM не додає.
- *Migrate to Lambda* — не підходить для цього сценарію.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-instance-monitoring.html#CloudWatchAlarm>

</details>

---

<a id="t07-q17"></a>
### T07-Q17
*домен: офіційна мітка*

> ⚠️ **X-Ray daemon у maintenance з 25.02.2026.** → [детальніше](../../07-outdated-and-wrong-answers.md#o05)

A developer wants to enable X-Ray tracing on an on-premises Linux server running a custom application that is accessed through Amazon API Gateway.

What is the most efficient solution that requires minimal configuration?

- ▫️ Install and run the CloudWatch Unified Agent on the on-premises servers to capture and relay the X-Ray data to the X-Ray service using the PutTraceSegments API call
- ▫️ Configure a Lambda function to analyze the incoming traffic data on the on-premises servers and then relay the X-Ray data to the X-Ray service using the PutTelemetryRecords API call
- ▫️ Install and run the X-Ray SDK on the on-premises servers to capture and relay the data to the X-Ray service
- ✅ Install and run the X-Ray daemon on the on-premises servers to capture and relay the data to the X-Ray service

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install and run the X-Ray daemon on the on-premises servers to capture and relay the data to the X-Ray service»**

X-Ray daemon слухає UDP-порт 2000, збирає сирі дані сегментів і передає їх в API X-Ray. Він працює разом з X-Ray SDK і має бути запущений, щоб дані SDK потрапили в X-Ray. На on-premises сервері daemon завантажують, запускають і дають йому дозвіл надсилати сегменти в X-Ray.

ℹ️ Підтримка X-Ray SDK і daemon завершується 25.02.2027; рекомендована заміна — ADOT або CloudWatch agent. Див. 07.

**❌ Неправильні варіанти:**
- *X-Ray SDK only* — SDK лише генерує сегменти; передає їх у X-Ray daemon.
- *CloudWatch Unified Agent + PutTraceSegments* — у формулюванні питання агент не передає дані X-Ray. ℹ️ Сучасні версії CloudWatch agent уміють збирати трасування.
- *Lambda + PutTelemetryRecords* — Lambda не може обробляти трафік on-premises серверів.

</details>

---

<a id="t07-q63"></a>
### T07-Q63
*домен: офіційна мітка*

An IT company has its serverless stack integrated with AWS X-Ray. The developer at the company has noticed a high volume of data going into X-Ray and the AWS monthly usage charges have skyrocketed as a result. The developer has requested changes to mitigate the issue.

As a Developer Associate, which of the following solutions would you recommend to obtain tracing trends while reducing costs with minimal disruption?

- ▫️ Custom configuration for the X-Ray agents
- ✅ Enable X-Ray sampling
- ▫️ Implement a network sampling rule
- ▫️ Use Filter Expressions in the X-Ray console

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable X-Ray sampling»**

X-Ray допомагає аналізувати й налагоджувати розподілені застосунки, зокрема мікросервіси. Він показує шлях запитів крізь застосунок і карту компонентів, тож легше знайти причину проблем із продуктивністю та помилок. Як працює: https://aws.amazon.com/xray/

Щоб трасування було ефективним і репрезентативним, X-Ray SDK застосовує алгоритм вибірки. За замовчуванням записується перший запит щосекунди і 5% решти. Правила вибірки налаштовуються в консолі, тож код змінювати не треба.

Власні правила вибірки: https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html

**❌ Неправильні варіанти:**
- *Filter Expressions* — лише звужують показані в консолі трасування, а обсяг надісланих даних не зменшують.
- *Custom configuration for X-Ray agents* — так не налаштовують; є правила вибірки.
- *Network sampling rule* — такої функції не існує.

Джерела: <https://aws.amazon.com/xray/> · <https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html>

</details>

---

<a id="t08-q06"></a>
### T08-Q06
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer manages a monitoring service for a fleet of IoT sensors in a major city. The monitoring application uses an Amazon Kinesis Data Stream with a group of EC2 instances processing the data. Amazon CloudWatch custom metrics show that the instances a reaching maximum processing capacity and there are insufficient shards in the Data Stream to handle the rate of data flow.

What course of action should the Developer take to resolve the performance issues?

- ▫️ Increase the number of EC2 instances to match the number of shards
- ✅ Increase the EC2 instance size and add shards to the stream
- ▫️ Increase the EC2 instance size
- ▫️ Increase the number of open shards

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the EC2 instance size and add shards to the stream»**

Ємність потоку Kinesis дорівнює сумі ємностей його шардів. Потрібні обидві зміни:
- **більші інстанси** запускатимуть більше record processors паралельно;
- **більше шардів** приймуть поточний потік даних.

**❌ Неправильні варіанти:**
- *Instances = number of shards* — один інстанс може обслуговувати кілька шардів.
- *Only increase instance size* — ємність потоку теж треба збільшити.
- *Only add shards* — інстанси вже працюють на межі, тож їх теж треба посилити.

</details>

---

<a id="t08-q17"></a>
### T08-Q17
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An AWS developer is building an application that processes sensitive personally identifiable information (PII). The application operates on AWS Lambda and writes diagnostic data to Amazon CloudWatch Logs. However, the developer wants to ensure that PII is not accidentally stored in CloudWatch Logs.
What strategy should the developer adopt to ensure this?

- ▫️ Manually insert logging commands into the application code while ensuring PII is not included.
- ▫️ Incorporate AWS X-Ray and configure it to filter out sensitive PII before logging.
- ✅ Configure Amazon CloudWatch Logs data protection to detect and mask PII in log events (using managed data identifiers) before the data is stored.
- ▫️ Implement AWS Secrets Manager for secure logging of sensitive information.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure Amazon CloudWatch Logs data protection to detect and mask PII in log events (using managed data identifiers) before the data is stored.»**

CloudWatch Logs data protection автоматично знаходить і маскує чутливі дані, зокрема типові PII, у лог-подіях. Для цього використовуються managed data identifiers і data protection policies. PII не потрапить у сховище, навіть якщо застосунок помилково її залогує.

**❌ Неправильні варіанти:**
- *Manually ensure PII is not logged* — корисна практика, але покладається на те, що розробники ніколи не помиляються, і не дає керованого захисту.
- *Secrets Manager* — зберігає й ротує секрети, але не заважає PII потрапити в логи.
- *X-Ray filtering* — X-Ray для трасування й продуктивності; рядки логів він не фільтрує.

Джерела: <https://digitalcloud.training/amazon-cloudwatch/>

</details>

---

<a id="t08-q24"></a>
### T08-Q24
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A development team have deployed a new application and users have reported some performance issues. The developers need to enable monitoring for specific metrics with a data granularity of one second. How can this be achieved?

- ▫️ Do nothing, CloudWatch uses standard resolution metrics by default
- ▫️ Create custom metrics and configure them as standard resolution
- ✅ Create custom metrics and configure them as high resolution
- ▫️ Create custom metrics and enable detailed monitoring

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create custom metrics and configure them as high resolution»**

Власні метрики публікують у CloudWatch через CLI або API і переглядають у консолі. Дані зберігаються як точки з часовими мітками; можна надсилати й агреговані набори (statistic set). Роздільність метрики:
- **standard** — 1 хвилина;
- **high resolution** — 1 секунда.

Метрики сервісів AWS — standard, а для custom metric роздільність вибирають. High-resolution метрику читають з періодом 1, 5, 10, 30 с або кратним 60 с. Вона дає змогу бачити активність з точністю до секунди, але кожен виклик `PutMetricData` платний, тож часті виклики дорожчі.

**❌ Неправильні варіанти:**
- *Do nothing* — стандартна роздільність лише 1 хвилина.
- *Custom metrics, standard resolution* — роздільність 1 хвилина.
- *Detailed monitoring* — роздільність 1 хвилина.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html>

</details>

---

<a id="t08-q55"></a>
### T08-Q55
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A company is migrating several applications to the AWS cloud. The security team has strict security requirements and mandate that a log of all API calls to AWS resources must be maintained.

Which AWS service should be used to record this information for the security team?

- ▫️ Amazon CloudWatch
- ✅ AWS CloudTrail
- ▫️ Amazon CloudWatch Logs
- ▫️ AWS X-Ray

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CloudTrail»**

CloudTrail записує активність в акаунті, тобто історію API-викликів. Trail доставляє логи в бакет S3. Сервіс потрібен для governance, compliance, операційного аудиту й аудиту ризиків.

**❌ Неправильні варіанти:**
- *CloudWatch* — метрики продуктивності.
- *CloudWatch Logs* — логи сервісів і застосунків, а не історія API.
- *X-Ray* — трасування застосунків.

Джерела: <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-concepts.html>

</details>

---

<a id="t08-q64"></a>
### T08-Q64
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A developer is debugging an application by sifting through log data stored in Amazon CloudWatch Logs. A fresh metric filter has been established to identify exceptions in these logs. Yet, the logs are not returning any results filtered through the new metric.

What could be the reason behind the absence of filtered results?

- ▫️ The CloudWatch Logs agent hasn't been installed on the EC2 instances.
- ▫️ The time range for the filter hasn't been properly configured.
- ▫️ The application logs have been archived to Amazon S3, making them non-filterable.
- ✅ CloudWatch Logs only publish metric data for events that occur after the filter has been established.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CloudWatch Logs only publish metric data for events that occur after the filter has been established.»**

Metric filter публікує точки метрики лише для лог-подій, що надійшли після його створення. Ретроспективно до старих логів він не застосовується.

**❌ Неправильні варіанти:**
- *Logs agent not installed* — агент надсилає логи, але для роботи фільтра його встановлення не потрібне.
- *Archived to S3* — експорт у S3 не впливає на фільтри CloudWatch Logs.
- *Time range* — metric filter не має діапазону часу: він працює з новими подіями.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t09-q04"></a>
### T09-Q04
*домен: офіційна мітка*

A serverless application built on AWS processes customer orders 24/7 using an AWS Lambda function and communicates with an external vendor's HTTP API for payment processing. The development team wants to notify the support team in near real-time using an existing Amazon Simple Notification Service (Amazon SNS) topic, but only when the external API error rate exceeds 5% of the total transactions processed in an hour.

As an AWS Certified Developer Associate, which option will you suggest as the most efficient solution?

- ▫️ Log the results of payment processing API calls to Amazon CloudWatch. Leverage Amazon CloudWatch Logs Insights to query the CloudWatch logs. Set up the Lambda function to check the output from CloudWatch Logs Insights on a schedule and send notification via the existing SNS topic when the error rate exceeds the specified rate
- ▫️ Configure CloudWatch metrics with detailed monitoring for the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate
- ▫️ Log the results of payment processing API calls to Amazon CloudWatch. Leverage Amazon CloudWatch Metric Filter to look at the CloudWatch logs. Set up the Lambda function to check the output from CloudWatch Metric Filter on a schedule and send notification via the existing SNS topic when the error rate exceeds the specified rate
- ✅ Configure and push high-resolution custom metrics to CloudWatch that record the failures of the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure and push high-resolution custom metrics to CloudWatch that record the failures of the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate»**

Власні метрики (custom metrics) публікують у CloudWatch через CLI або API. Роздільність метрики:
- **standard** — 1 хвилина;
- **high resolution** — 1 секунда; читати можна з періодом 1, 5, 10, 30 с або кратним 60 с.

Метрики сервісів AWS — standard, а для custom metric роздільність вибирають. Кожен виклик `PutMetricData` платний, тож часті виклики дорожчі.

Метрика рахує помилки зовнішнього API, а metric alarm спрацьовує, коли їх частка перевищує 5%, і надсилає сповіщення в наявний SNS-топік.

**❌ Неправильні варіанти:**
- *Detailed monitoring* — лише частіші стандартні метрики (наприклад, EC2 — 1 хв замість 5) і лише для деяких сервісів. Помилок зовнішнього API в них немає.
- *Logs Insights + scheduled Lambda* — Lambda за розкладом не моніторить у реальному часі, і для підрахунку частки помилок потрібно багато власного коду.
- *Metric filter + scheduled Lambda* — те саме: не в реальному часі й багато коду. Alarm можна поставити прямо на метрику від metric filter, без Lambda.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-push-custom-metrics/> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t09-q57"></a>
### T09-Q57
*домен: офіційна мітка*

Recently in your organization, the AWS X-Ray SDK was bundled into each Lambda function to record outgoing calls for tracing purposes. When your team leader goes to the X-Ray service in the AWS Management Console to get an overview of the information collected, they discover that no data is available.

What is the most likely reason for this issue?

- ✅ Fix the IAM Role
- ▫️ Change the security group rules
- ▫️ Enable X-Ray sampling
- ▫️ X-Ray only works with AWS Lambda aliases

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Fix the IAM Role»**

X-Ray допомагає аналізувати й налагоджувати розподілені застосунки: показує шлях запитів і карту компонентів. Як працює: https://aws.amazon.com/xray/

Якщо трасування не з'являються, перше, що варто перевірити, — дозволи. Ресурсам, де працює застосунок, потрібна IAM-роль із правами на запис у X-Ray.

Дозволи на читання (приклад):
```json
{ "Effect": "Allow", "Action": [ "xray:GetSamplingRules", "xray:GetSamplingTargets",
  "xray:GetSamplingStatisticSummaries", "xray:BatchGetTraces", "xray:GetServiceGraph",
  "xray:GetTraceGraph", "xray:GetTraceSummaries", "xray:GetGroups", "xray:GetGroup" ], "Resource": ["*"] }
```

Дозволи на запис (саме вони потрібні застосунку):
```json
{ "Effect": "Allow", "Action": [ "xray:PutTraceSegments", "xray:PutTelemetryRecords",
  "xray:GetSamplingRules", "xray:GetSamplingTargets", "xray:GetSamplingStatisticSummaries" ], "Resource": ["*"] }
```

Готова managed-політика з правами на запис — `AWSXRayDaemonWriteAccess`.

**❌ Неправильні варіанти:**
- *Enable sampling* — без дозволів вибірка не допоможе.
- *X-Ray only works with aliases* — неправда. Alias — це вказівник на версію Lambda; X-Ray SDK просто пакують разом із кодом функції.
- *Security group rules* — доступ Lambda до сервісів AWS дає IAM-роль, а не security group.

</details>

---

<a id="t10-q19"></a>
### T10-Q19
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **CloudWatch Events schedule = EventBridge.** → [детальніше](../../06-contradictions-and-traps.md#c14)

A Developer has created a serverless function that processes log files. The function should be invoked once every 15 minutes. How can the Developer automatically invoke the function using serverless services?

- ▫️ Configure the Lambda scheduler to run based on recurring time value
- ✅ Create an Amazon CloudWatch Events rule that is scheduled to run and invoke the function
- ▫️ Launch an EC2 Linux instance and add a command to periodically invoke the function to its /etc/crontab file
- ▫️ Create an Amazon SNS rule to send a notification to Lambda to instruct it to run

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Amazon CloudWatch Events rule that is scheduled to run and invoke the function»**

CloudWatch Events — майже реального часу потік системних подій про зміни ресурсів AWS. Правила зіставляють події і спрямовують їх у цілі. Правило за розкладом може викликати Lambda кожні 15 хвилин — повністю автоматично і serverless.

ℹ️ Зараз це Amazon EventBridge (rules або EventBridge Scheduler).

**❌ Неправильні варіанти:**
- *EC2 + crontab* — автоматично, але не serverless.
- *Lambda scheduler* — такого вбудованого планувальника в Lambda немає.
- *SNS rule* — SNS не запускає дії за розкладом. Lambda можна підписати на топік, але хтось має публікувати повідомлення, а розкладу тут немає.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/RunLambdaSchedule.html>

</details>

---

<a id="t10-q36"></a>
### T10-Q36
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application is being instrumented to send trace data using AWS X-Ray. A Developer needs to upload segment documents using JSON-formatted strings to X-Ray using the API. Which API action should the developer use?

- ✅ The PutTraceSegments API action
- ▫️ The GetTraceSummaries API action
- ▫️ The UpdateGroup API action
- ▫️ The PutTelemetryRecords API action

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The PutTraceSegments API action»**

Дані трасування надсилають у X-Ray як segment documents — JSON з інформацією про роботу, виконану для запиту. Роботу самого застосунку записують у сегменти, а виклики downstream-сервісів — у субсегменти. Мінімальний сегмент містить назву, час і два ідентифікатори:
- **trace ID** — відстежує запит між сервісами;
- **segment ID** — роботу одного сервісу.

Документи завантажують через `PutTraceSegments` з єдиним параметром `TraceSegmentDocuments` — списком JSON-документів.

**❌ Неправильні варіанти:**
- *PutTelemetryRecords* — daemon надсилає телеметрію про свою роботу.
- *UpdateGroup* — оновлює групу X-Ray.
- *GetTraceSummaries* — повертає ID та annotations трасувань за період, з фільтром.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-api-sendingdata.html>

</details>

---

<a id="t10-q41"></a>
### T10-Q41
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Logs agent застарів → unified CloudWatch agent.** → [детальніше](../../07-outdated-and-wrong-answers.md#o15)

An application runs on Amazon EC2 and generates log files. A Developer needs to centralize the log files so they can be queried and retained. What is the EASIEST way for the Developer to centralize the log files?

- ✅ Install the Amazon CloudWatch Logs agent and collect the logs from the instances
- ▫️ Create a script that uses the AWS SDK to collect and send the log files to Amazon CloudWatch Logs
- ▫️ Create a script that copies the log files to Amazon S3 and use a cron job to run the script on a recurring schedule
- ▫️ Setup a CloudWatch Events rule to trigger an SNS topic when an application log file is generated

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install the Amazon CloudWatch Logs agent and collect the logs from the instances»**

CloudWatch Logs централізує логи систем, застосунків і сервісів AWS. Їх можна переглядати, шукати коди помилок, фільтрувати за полями та архівувати.

Щоб збирати логи з EC2 і on-premises серверів, потрібен агент:
- **unified CloudWatch agent** — логи і розширені метрики, зокрема пам'ять;
- **старий CloudWatch Logs agent** — лише логи, лише Linux.

⚠️ Старий Logs agent застарів; використовуйте **CloudWatch agent**. Див. 07.

**❌ Неправильні варіанти:**
- *Script + cron to S3* — для запитів і довгого зберігання краще CloudWatch Logs, а агент простіший за скрипт.
- *SDK script* — складніше, ніж поставити агента.
- *CloudWatch Events → SNS* — Events не збирає логи.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html>

</details>

---

<a id="t10-q45"></a>
### T10-Q45
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer created an AWS Lambda function for a serverless application. The Lambda function has been executing for several minutes and the Developer cannot find any log data in CloudWatch Logs.

What is the MOST likely explanation for this issue?

- ▫️ The Lambda function does not have any explicit log statements for the log data to send it to CloudWatch Logs
- ▫️ The Lambda function is missing CloudWatch Logs as a source trigger to send log data
- ▫️ The Lambda function is missing a target CloudWatch Logs group
- ✅ The execution role for the Lambda function is missing permissions to write log data to the CloudWatch Logs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The execution role for the Lambda function is missing permissions to write log data to the CloudWatch Logs»**

Lambda автоматично надсилає метрики в CloudWatch, а логи — в log group `/aws/lambda/<function name>`. Execution role дає функції доступ до сервісів AWS: її вказують при створенні функції, і Lambda приймає її під час виклику. Для логів роль має дозволяти:
- `logs:CreateLogGroup`;
- `logs:CreateLogStream`;
- `logs:PutLogEvents`.

Найімовірніша причина відсутності логів — цих дозволів немає.

**❌ Неправильні варіанти:**
- *No explicit log statements* — Lambda однаково логує START, END і REPORT, тож без дозволів логів не буде взагалі.
- *Missing target log group* — log group створюється автоматично, якщо є дозволи.
- *CloudWatch Logs as source trigger* — CloudWatch Logs — це місце призначення, і налаштовувати його не треба.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html>

</details>

---

<a id="t11-q39"></a>
### T11-Q39
*домен: офіційна мітка*

An e-commerce company has developed an API that is hosted on Amazon ECS. Variable traffic spikes on the application are causing order processing to take too long. The application processes orders using Amazon SQS queues. The ApproximateNumberOfMessagesVisible metric spikes at very high values throughout the day which triggers the CloudWatch alarm. Other ECS metrics for the API containers are well within limits.

As a Developer Associate, which of the following will you recommend for improving performance while keeping costs low?

- ▫️ Use ECS step scaling policy
- ▫️ Use Docker swarm
- ✅ Use backlog per instance metric with target tracking scaling policy
- ▫️ Use ECS service scheduler

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use backlog per instance metric with target tracking scaling policy»**

Target tracking за метрикою SQS краще підлаштовується під навантаження. Але `ApproximateNumberOfMessagesVisible` не змінюється пропорційно розміру ASG. Потрібна кількість інстансів залежить ще й від часу обробки повідомлення та допустимої затримки. Тож використовують метрику **backlog per instance**:
- **Backlog per instance** = `ApproximateNumberOfMessages` ÷ кількість інстансів у стані InService.
- **Цільове значення** = допустима затримка ÷ середній час обробки одного повідомлення.

Приклад: 1 500 повідомлень і 10 інстансів дають backlog 150. Обробка — 0,1 с, допустима затримка — 10 с, тож ціль 10 / 0,1 = 100. Оскільки 150 > 100, група додає 5 інстансів.

**❌ Неправильні варіанти:**
- *Docker swarm* — оркестратор контейнерів на кількох хостах; до масштабування за чергою не стосується.
- *ECS service scheduler* — розміщує довготривалі задачі в кластері, а не масштабує за чергою.
- *ECS step scaling* — підтримується, але AWS рекомендує target tracking. Step scaling вимагає самостійно керувати alarms і змінює потужність кроками за порогами.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-using-sqs-queue.html>

</details>

---

<a id="t11-q42"></a>
### T11-Q42
*домен: офіційна мітка*

You have deployed a Java application to an EC2 instance where it uses the X-Ray SDK. When testing from your personal computer, the application sends data to X-Ray but when the application runs from within EC2, the application fails to send data to X-Ray.

Which of the following does NOT help with debugging the issue?

- ▫️ EC2 Instance Role
- ▫️ EC2 X-Ray Daemon
- ▫️ CloudTrail
- ✅ X-Ray sampling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «X-Ray sampling»**

Питання на пошук того, що **не допоможе** знайти причину. Правила вибірки визначають, скільки запитів записувати, і змінюються без редеплою коду. Але дані взагалі не доходять до X-Ray, тож вибірка тут не допоможе.

Огляд X-Ray: https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html

**❌ Інші варіанти допоможуть:**
- *EC2 X-Ray Daemon* — daemon слухає UDP 2000 і передає сегменти в API X-Ray; його логи покажуть проблему.
- *EC2 Instance Role* — на EC2 daemon автоматично бере облікові дані instance profile. Якщо в ролі немає прав на запис у X-Ray, дані не надсилаються. Найімовірніша причина: на ПК працювали ваші облікові дані.
- *CloudTrail* — покаже, чи відхиляються виклики API X-Ray.

</details>

---

<a id="t11-q54"></a>
### T11-Q54
*домен: офіційна мітка*

A multi-national company has multiple business units with each unit having its own AWS account. The development team at the company would like to debug and trace data across accounts and visualize it in a centralized account.

As a Developer Associate, which of the following solutions would you suggest for the given use-case?

- ▫️ EventBridge
- ✅ X-Ray
- ▫️ CloudTrail
- ▫️ VPC Flow Logs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «X-Ray»**

X-Ray допомагає аналізувати й налагоджувати розподілені застосунки, зокрема мікросервіси. Він показує шлях запитів end-to-end і карту компонентів. X-Ray збирає дані з кількох акаунтів: агент приймає роль і публікує дані в центральний акаунт.

Як працює: https://aws.amazon.com/xray/

**❌ Неправильні варіанти:**
- *VPC Flow Logs* — мережевий трафік; трасувати дані між акаунтами не можна.
- *EventBridge* — події про зміни ресурсів; не для налагодження.
- *CloudTrail* — історія API-викликів для аудиту; не для трасування.

</details>

---

<a id="t12-q32"></a>
### T12-Q32
*домен: офіційна мітка*

A developer is configuring Amazon ECS container instances to send log information to CloudWatch Logs. For the container instances to be able to send log data to CloudWatch Logs, an IAM policy needs to be created that will allow the container instances to use the CloudWatch Logs APIs.

Which policy is the right fit for the given requirement?

- ▫️

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents,
                  "ecs:DescribeServices"
              ],
              "Resource": [
                  "arn:aws:logs:<ARN of the Log Group>"
              ]
          }
      ]
  }
  ```
- ✅

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents",
                  "logs:DescribeLogStreams"
              ],
              "Resource": [
                  "arn:aws:logs:*:*:*"
              ]
          }
      ]
  }
  ```
- ▫️

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents"
              ],
              "Resource": [
                  "arn:aws:logs:*:*:*"
              ]
          }
      ]
  }
  ```
- ▫️

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents",
                  "logs:DescribeLogGroups"
              ],
              "Resource": [
                  "arn:aws:logs:*:*:*"
              ]
          }
      ]
  }
  ```

<details><summary>Пояснення</summary>

**✅ Правильна відповідь:**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
```

Щоб container instances могли надсилати логи в CloudWatch Logs, створюють IAM-політику з доступом до API CloudWatch Logs і прикріплюють її до `ecsInstanceRole`. Політика дозволяє створювати log groups і streams, надсилати лог-події і переглядати деталі streams. `*` у Resource означає будь-яку log group; щоб обмежити, вказують ARN конкретної групи.

**❌ Неправильні варіанти:**
- *Без `logs:DescribeLogStreams`* — бракує дозволу переглядати streams.
- *З `ecs:DescribeServices`* — цей дозвіл не потрібен, а `logs:DescribeLogStreams` бракує.
- *З `logs:DescribeLogGroups`* — потрібен `DescribeLogStreams`, а не `DescribeLogGroups`.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_cloudwatch_logs.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-identity-based-access-control-cwl.html>

</details>

---

<a id="t12-q64"></a>
### T12-Q64
*домен: офіційна мітка*

A multi-national company runs its technology operations on AWS Cloud. As part of their storage solution, they use a large number of EBS volumes, with AWS Config and CloudTrail activated. A manager has tried to find the user name that created an EBS volume by searching CloudTrail events logs but wasn't successful.

As a Developer Associate, which of the following would you recommend as the correct solution?

- ✅ AWS CloudTrail event logs for 'CreateVolume' aren't available for EBS volumes created during an Amazon EC2 launch
- ▫️ Amazon EBS CloudWatch metrics are disabled
- ▫️ AWS CloudTrail event logs for 'ManageVolume' aren't available for EBS volumes created during an Amazon EC2 launch
- ▫️ EBS volume status checks are disabled

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CloudTrail event logs for 'CreateVolume' aren't available for EBS volumes created during an Amazon EC2 launch»**

Для томів EBS, створених під час запуску інстансу EC2 (у block device mapping), подій `CreateVolume` у CloudTrail немає. Такі томи фіксуються лише в події `RunInstances`.

**❌ Неправильні варіанти:**
- *'ManageVolume' events* — такої події не існує.
- *EBS CloudWatch metrics disabled* — метрики надходять лише для підключених томів і відображають стан, а не метадані створення.
- *Volume status checks disabled* — status checks показують, чи пошкоджений том, а не метадані.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/find-ebs-user-config-cloudtrail/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using_cloudwatch_ebs.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-volume-status.html>

</details>

---
