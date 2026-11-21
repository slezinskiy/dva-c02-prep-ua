# AWS Lambda

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 45  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q04"></a>
### T01-Q04
*домен: офіційна мітка*

> ⚠️ **EventBridge schedule.** → [детальніше](../../06-contradictions-and-traps.md#c14)

You are looking to invoke an AWS Lambda function every hour (similar to a cron job) in a serverless way.

Which event source should you use for your AWS Lambda function?

- ▫️ Amazon S3
- ✅ EventBridge
- ▫️ SQS
- ▫️ Kinesis

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «EventBridge»**

Можна створити Lambda-функцію і доручити EventBridge запускати її за розкладом: з фіксованою частотою (наприклад, щогодини чи кожні 15 хвилин) або за cron-виразом.

**❌ Неправильні варіанти:** *Amazon S3*, *SQS*, *Kinesis* — у жодного з цих сервісів немає можливостей cron.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html> · <https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html>

</details>

---

<a id="t01-q24"></a>
### T01-Q24
*домен: офіційна мітка*

Your Lambda function must use the Node.js drivers to connect to your RDS PostgreSQL database in your VPC.

How do you bundle your Lambda function to add the dependencies?

- ▫️ Zip the function as-is with a package.json file so that AWS Lambda can resolve the dependencies for you
- ▫️ Upload the code through the AWS console and upload the dependencies as a zip
- ▫️ Zip the function and the dependencies separately and upload them in AWS Lambda as two parts
- ✅ Put the function and the dependencies in one folder and zip them together

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Put the function and the dependencies in one folder and zip them together»**

AWS Lambda виконує код без провізіонінгу серверів; платиш лише за використаний час обчислень. Deployment package — це ZIP-архів з кодом функції та залежностями. Він потрібен, якщо ти керуєш функціями через Lambda API або тобі потрібні бібліотеки, крім AWS SDK. Пакет завантажують у Lambda напряму або через S3; якщо він більший за 50 MB, — обов'язково через S3. Це стандартний спосіб пакування.

**❌ Неправильні варіанти:**
- *Zip the function as-is with a package.json so that AWS Lambda can resolve the dependencies* — Lambda не встановлює залежності сама.
- *Upload the code through the console and the dependencies as a zip* та *Zip the function and the dependencies separately* — функцію деплоять одним zip-файлом з усіма залежностями.

</details>

---

<a id="t01-q34"></a>
### T01-Q34
*домен: офіційна мітка*

Your Lambda function processes files for your customers and as part of that process, it creates a lot of intermediary files it needs to store on its disk and then discard.

What is the best way to store temporary files for your Lambda functions that will be discarded when the function stops running?

- ▫️ Use an S3 bucket
- ▫️ Create a tmp/ directory in the source zip file and use it
- ▫️ Use the local directory /opt
- ✅ Use the local directory /tmp

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the local directory /tmp»**

`/tmp` — тимчасовий простір для Lambda (за замовчуванням 512 MB, можна збільшити до 10 GB), що зникає разом із середовищем виконання.

**❌ Неправильні варіанти:**
- *Create a tmp/ directory in the source zip file* — до каталогу всередині zip-файлу доступу немає; дистрактор.
- *Use the local directory /opt* — не для запису (туди монтуються layers).
- *Use an S3 bucket* — дані в S3 не зникнуть після завершення функції, тобто не будуть тимчасовими.

</details>

---

<a id="t01-q49"></a>
### T01-Q49
*домен: офіційна мітка*

The customer feedback functionality for a company's flagship web application is handled via an Amazon API Gateway based REST API that invokes an AWS Lambda function for further processing. Although the performance of the function is satisfactory, the development team has been tasked to optimize the startup time of the Lambda function to further improve the customer experience.

How will you optimize the Lambda function for faster initialization?

- ✅ Configure provisioned concurrency for the Lambda function to respond immediately to the function's invocations
- ▫️ Configure an interface VPC endpoint powered by AWS PrivateLink to access the Amazon API Gateway REST API with milliseconds latency
- ▫️ Configure reserved concurrency to guarantee the maximum number of concurrent instances of the Lambda function
- ▫️ Enable API caching in Amazon API Gateway to cache AWS Lambda function response

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure provisioned concurrency for the Lambda function to respond immediately to the function's invocations»**

Коли Lambda створює екземпляр функції, runtime завантажує код і виконує ініціалізацію поза handler. Якщо код і залежності великі або при ініціалізації створюються SDK-клієнти, це забирає час. Нові середовища виконання з'являються, коли функцію довго не використовували, коли треба масштабуватися або після оновлення. Запити, які потрапляють на нові середовища, мають вищу затримку — це cold start.

Provisioned concurrency заздалегідь готує середовища, тож усі запити обслуговуються вже ініціалізованими екземплярами зі стабільною затримкою. Це ідеально для мобільних і веб-бекендів, чутливих до затримки мікросервісів і синхронних API. Особливості:
- код ініціалізації не обов'язково оптимізувати: він виконується задовго до виклику (корисно для Java);
- ініціалізація виконується частіше за кількість викликів: на кожну одиницю provisioned concurrency готується щонайменше два середовища в різних AZ (це не тарифікується);
- не працює з `$LATEST`, лише з опублікованими версіями та aliases.

**❌ Неправильні варіанти:**
- *Configure reserved concurrency* — резервує (і обмежує) максимум одночасних екземплярів, безкоштовно, але не прогріває середовища.
- *Interface VPC endpoint (PrivateLink)…* — дає приватне з'єднання без публічного інтернету, до cold start відношення не має.
- *Enable API caching in API Gateway* — зменшує кількість викликів і затримку, але не скорочує ініціалізацію Lambda.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html> · <https://aws.amazon.com/blogs/compute/operating-lambda-performance-optimization-part-1/> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t01-q64"></a>
### T01-Q64
*домен: офіційна мітка*

Your company wants to move away from manually managing Lambda in the AWS console and wants to upload and update them using AWS CloudFormation.

How do you declare an AWS Lambda function in CloudFormation? (Select two)

- ▫️ Upload all the code to CodeCommit and refer to the CodeCommit Repository in AWS::Lambda::Function block
- ▫️ Write the AWS Lambda code inline in CloudFormation in the AWS::Lambda::Function block and reference the dependencies as a zip file stored in S3
- ✅ Upload all the code as a zip to S3 and refer the object in AWS::Lambda::Function block
- ✅ Write the AWS Lambda code inline in CloudFormation in the AWS::Lambda::Function block as long as there are no third-party dependencies
- ▫️ Upload all the code as a folder to S3 and refer the folder in AWS::Lambda::Function block

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Upload all the code as a zip to S3 and refer the object in AWS::Lambda::Function block» та «Write the AWS Lambda code inline … as long as there are no third-party dependencies»**

Ресурс `AWS::Lambda::Function` створює функцію; для нього потрібні deployment package (код) і execution role. Код можна:
- запакувати в zip, завантажити в S3 і послатися на об'єкт у властивості `Code` (`S3Bucket`/`S3Key`);
- написати inline (`ZipFile`) для Node.js чи Python, якщо крім уже наявних у runtime бібліотек (aws-sdk, boto3, cfn-response тощо) інших залежностей немає.

Основні властивості ресурсу: `Code`, `Handler`, `Runtime`, `Role`, `MemorySize`, `Timeout`, `Environment`, `Layers`, `VpcConfig`, `TracingConfig`, `ReservedConcurrentExecutions`, `DeadLetterConfig`, `KmsKeyArn`.

**❌ Неправильні варіанти:** посилання на репозиторій CodeCommit, на «папку» в S3 (потрібен zip) або inline-код із залежностями у zip в S3 — так `AWS::Lambda::Function` не працює.

</details>

---

<a id="t02-q04"></a>
### T02-Q04
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer has deployed an application on AWS Lambda. The application uses Python and must generate and then upload a file to an Amazon S3 bucket. The developer must implement the upload functionality with the least possible change to the application code.

Which solution BEST meets these requirements?

- ▫️ Include the AWS SDK for Python in the Lambda function code.
- ✅ Use the AWS SDK for Python that is installed in the Lambda execution environment.
- ▫️ Use the AWS CLI that is installed in the Lambda execution environment.
- ▫️ Make an HTTP request directly to the S3 API to upload the file.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the AWS SDK for Python that is installed in the Lambda execution environment»**

Best practice — бандлити всі залежності, зокрема AWS SDK, разом із функцією. Але за умовою зміни коду мають бути мінімальними, тож можна використати boto3, уже наявний у runtime Python, щоб завантажити файл у S3.

**❌ Неправильні варіанти:**
- *Include the AWS SDK for Python in the Lambda function code* — це best practice, але вимагає змін (додавання залежності, можливо, оновлення коду).
- *Make an HTTP request directly to the S3 API* — можливо, але це більше коду (підпис SigV4 тощо).
- *Use the AWS CLI installed in the Lambda execution environment* — AWS CLI в середовищі Lambda немає.

Джерела: <https://aws.amazon.com/blogs/compute/upcoming-changes-to-the-python-sdk-in-aws-lambda/>

</details>

---

<a id="t02-q36"></a>
### T02-Q36
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **20×20=400.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A serverless application uses an AWS Lambda function to process Amazon S3 events. The Lambda function executes 20 times per second and takes 20 seconds to complete each execution.

How many concurrent executions will the Lambda function require?

- ▫️ 40
- ▫️ 20
- ▫️ 5
- ✅ 400

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «400»**

Concurrency — кількість запитів, які функція обслуговує одночасно. На кожен виклик Lambda виділяє екземпляр; якщо новий виклик приходить, поки попередній ще виконується, виділяється ще один. Формула: викликів на секунду × тривалість виконання = 20 × 20 = **400**.

**❌ Неправильні варіанти:** *5*, *40*, *20* — не відповідають формулі.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html>

</details>

---

<a id="t02-q52"></a>
### T02-Q52
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is writing an imaging microservice on AWS Lambda. The service is dependent on several libraries that are not available in the Lambda runtime environment.

Which strategy should the Developer follow to create the Lambda deployment package?

- ▫️ Create a ZIP file with the source code and an appspec.yml file. Add the libraries to the appspec.yml file and upload to Amazon S3. Deploy using CloudFormation
- ✅ Create a ZIP file with the source code and all dependent libraries
- ▫️ Create a ZIP file with the source code and a script that installs the dependent libraries at runtime
- ▫️ Create a ZIP file with the source code and a buildspec.yml file that installs the dependent libraries on AWS Lambda

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a ZIP file with the source code and all dependent libraries»**

Deployment package — ZIP-архів з кодом функції та залежностями. Він потрібен, якщо функцією керують через Lambda API або потрібні бібліотеки, крім AWS SDK. Пакет завантажують напряму або через S3; понад 50 MB — лише через S3.

**❌ Неправильні варіанти:**
- *Script that installs the dependent libraries at runtime* — встановлення під час виконання додає затримку; залежності мають бути в пакеті.
- *appspec.yml with the libraries … deploy using CloudFormation* — `appspec.yml` — файл для CodeDeploy; бібліотеки туди не додають, і CloudFormation його не розгортає.
- *buildspec.yml that installs the libraries on Lambda* — `buildspec.yml` — для CodeBuild (компіляція, тести), він не встановлює бібліотеки в Lambda.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/python-package.html>

</details>

---

<a id="t03-q36"></a>
### T03-Q36
*домен: офіційна мітка*

You are assigned as the new project lead for a web application that processes orders for customers. You want to integrate event-driven processing anytime data is modified or deleted and use a serverless approach using AWS Lambda for processing stream events.

Which of the following databases should you choose from?

- ▫️ ElastiCache
- ▫️ RDS
- ✅ DynamoDB
- ▫️ Kinesis

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «DynamoDB»**

DynamoDB stream — упорядкований за часом потік змін items у таблиці. Після ввімкнення DynamoDB Streams фіксує кожну зміну на рівні item'а і зберігає ці записи до 24 годин. Застосунки бачать дані до і після зміни майже в реальному часі. Щоразу при створенні, оновленні чи видаленні item'а у стрім пишеться запис з ключовими атрибутами. Стрім можна обробляти через Lambda.

**❌ Неправильні варіанти:**
- *RDS* — сама по собі не стрімить зміни, як DynamoDB (для цього довелося б додати, наприклад, Kinesis).
- *ElastiCache* — in-memory кеш і сховище, стрімити зміни не вміє.
- *Kinesis* — сервіс потоків даних, а не база даних.

</details>

---

<a id="t03-q61"></a>
### T03-Q61
*домен: офіційна мітка*

You have uploaded a zip file to AWS Lambda that contains code files written in Node.Js. When your function is executed you receive the following output, 'Error: Memory Size: 10,240 MB Max Memory Used'.

Which of the following explains the problem?

- ▫️ The uncompressed zip file exceeds AWS Lambda limits
- ✅ Your Lambda function ran out of RAM
- ▫️ Your zip file is corrupt
- ▫️ You have uploaded a zip file larger than 50 MB to AWS Lambda

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Your Lambda function ran out of RAM»**

Максимум пам'яті для Lambda — 10 240 MB. Функцію задеплоєно саме з 10 240 MB, але код запитав або використав більше, тож вона впала через нестачу RAM.

**❌ Неправильні варіанти:**
- *Your zip file is corrupt* — помилка про пам'ять означає, що Lambda розпакувала й запустила код, тож файл цілий.
- *The uncompressed zip file exceeds AWS Lambda limits* — функція запустилася, отже ліміт не перевищено.
- *You have uploaded a zip file larger than 50 MB* — функція запустилася, отже це не так.

</details>

---

<a id="t04-q17"></a>
### T04-Q17
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating an AWS Lambda function that will process medical images. The function is dependent on several libraries that are not available in the Lambda runtime environment. Which strategy should be used to create the Lambda deployment package?

- ▫️ Create a ZIP file with the source code and a buildspec.yaml file that installs the dependent libraries on AWS Lambda
- ✅ Create a ZIP file with the source code and all dependent libraries
- ▫️ Create a ZIP file with the source code and a script that installs the dependent libraries at runtime
- ▫️ Create a ZIP file with the source code. Stage the dependent libraries on an Amazon S3 bucket indicated by the Lambda environment variable LIBRARY_PATH

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a ZIP file with the source code and all dependent libraries»**

Deployment package — ZIP з кодом і залежностями; потрібен, якщо функцією керують через API або потрібні бібліотеки, крім AWS SDK. Пакет завантажують напряму або через S3 (понад 50 MB — лише через S3). Бібліотеки, яких немає в runtime, встановлюють у локальний каталог і додають у пакет.

**❌ Неправильні варіанти:**
- *Script that installs libraries at runtime* — виконуватиметься при кожному запуску і додасть затримку.
- *Libraries on S3 via LIBRARY_PATH env var* — зовнішній шлях змінною середовища не підключиш.
- *buildspec.yaml that installs libraries on Lambda* — buildspec — для CodeBuild; бібліотеки мають бути в zip.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/python-package.html>

</details>

---

<a id="t04-q19"></a>
### T04-Q19
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **10×3=30.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A serverless application uses an AWS Lambda function, Amazon API Gateway API and an Amazon DynamoDB table. The Lambda function executes 10 times per second and takes 3 seconds to complete each execution.

How many concurrent executions will the Lambda function require?

- ✅ 30
- ▫️ 12
- ▫️ 10
- ▫️ 3

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «30»**

Concurrency — кількість запитів, які функція обслуговує одночасно. Формула: викликів на секунду × тривалість = 10 × 3 = **30**.

**❌ Неправильні варіанти:** *10*, *12*, *3* — не відповідають формулі.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html>

</details>

---

<a id="t04-q39"></a>
### T04-Q39
*домен: визначено автоматично (у джерелі: AWS Storage)*

An application resizes images that are uploaded to an Amazon S3 bucket. Amazon S3 event notifications are used to trigger an AWS Lambda function that resizes the images. The processing time for each image is less than one second. A large amount of images are expected to be received in a short burst of traffic. How will AWS Lambda accommodate the workload?

- ▫️ Lambda will scale the memory allocated to the function to increase the amount of CPU available to process many images
- ▫️ Lambda will process the images sequentially in the order they are received
- ▫️ Lambda will collect and then batch process the images in a single execution
- ✅ Lambda will scale out and execute the requests concurrently

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Lambda will scale out and execute the requests concurrently»**

При першому виклику Lambda створює екземпляр функції. Якщо новий виклик приходить, поки перший ще обробляється, Lambda ініціалізує ще один екземпляр, і події обробляються паралельно. Concurrency — кількість екземплярів, що одночасно обслуговують запити. За банком, початковий сплеск — від 500 до 3000 залежно від регіону, далі +500 щохвилини. (З 2023 року модель інша: кожна функція може додавати до 1000 одночасних виконань кожні 10 секунд.) Ліміт акаунта за замовчуванням — 1000 на регіон, його можна підвищити.

**❌ Неправильні варіанти:**
- *Process the images sequentially* — Lambda обробляє події паралельно.
- *Collect and batch process in a single execution* — Lambda не накопичує запити для пізнішої обробки (для S3-подій).
- *Scale the memory allocated to the function* — Lambda не змінює пам'ять автоматично.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html>

</details>

---

<a id="t04-q46"></a>
### T04-Q46
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating multiple AWS Lambda functions that will be using an external library that is not included in the standard Lambda libraries. What is the BEST way to make these libraries available to the functions?

- ✅ Create a layer in Lambda that includes the external library
- ▫️ Include the external library with the function code
- ▫️ Create a deployment package that includes the external library
- ▫️ Store the files in Amazon S3 and reference them from your function code

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a layer in Lambda that includes the external library»**

Layer — ZIP-архів з бібліотеками, власним runtime чи іншими залежностями, який підключають до функції без включення в її пакет. Пакет лишається маленьким, а з пакуванням залежностей менше помилок. Під час виконання layers розпаковуються в `/opt`, і runtime шукає там мовні каталоги. Мінімальний пакет швидше завантажується й розпаковується, а один layer можуть використовувати всі функції.

**❌ Неправильні варіанти:**
- *Include the external library with the function code* — роздутий код, можливо повільніший старт.
- *Deployment package that includes the library* — бібліотеку доведеться додавати в пакет кожної функції; layer — один на всіх.
- *Store the files in S3 and reference them* — збільшить затримку виконання.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---

<a id="t04-q47"></a>
### T04-Q47
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A manufacturing company is creating a new RESTful API that their customers can use to query the status of orders. The endpoint for customer queries will be https://www.manufacturerdomain.com/status/customerID

Which of the following application designs will meet the requirements? (Select TWO.)

- ✅ Amazon API Gateway; AWS Lambda
- ▫️ Amazon ElastiCache; Amazon Elacticsearch Service
- ▫️ Amazon S3; Amazon CloudFront
- ▫️ Amazon SQS; Amazon SNS
- ✅ Elastic Load Balancing; Amazon EC2

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Amazon API Gateway; AWS Lambda» та «Elastic Load Balancing; Amazon EC2»**

Потрібен RESTful API, що динамічно повертає статус замовлень. Підходять:
- **API Gateway + Lambda** — керований REST API і бекенд-код у Lambda;
- **ELB + EC2** — балансер розподіляє запити між EC2 з API і логікою (працює, але дорожче операційно й фінансово).

**❌ Неправильні варіанти:**
- *Amazon SQS; Amazon SNS* — черги й сповіщення, не хостинг REST API.
- *ElastiCache; Elasticsearch* — кеш і пошук.
- *Amazon S3; CloudFront* — лише статичні сайти, API не захостиш.

Джерела: <https://aws.amazon.com/ec2/features/> · <https://aws.amazon.com/elasticloadbalancing/> · <https://aws.amazon.com/api-gateway/features/> · <https://aws.amazon.com/lambda/features/>

</details>

---

<a id="t04-q55"></a>
### T04-Q55
*домен: визначено автоматично (у джерелі: —)*

> ⚠️ **Окремі застарілі items → TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A serverless application uses Amazon API Gateway, AWS Lambda and DynamoDB. The application writes statistical data that is constantly received from sensors. The data is analyzed soon after it is written to the database and is then not required.

What is the EASIEST method to remove stale data and optimize database size?

- ▫️ Use atomic counters to decrement the data when it becomes stale
- ▫️ Scan the table for stale data and delete it once every hour
- ✅ Enable the TTL attribute and add expiry timestamps to items
- ▫️ Delete the table and recreate it every hour

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable the TTL attribute and add expiry timestamps to items»**

TTL у DynamoDB задає час, після якого item автоматично видаляється (мітка часу для кожного item'а), тож зберігаються лише актуальні записи. Корисно для даних, що втрачають цінність з часом (сесії, логи подій, тимчасові дані), і для чутливих даних, які за регуляторними вимогами можна тримати лише певний час.

**❌ Неправильні варіанти:**
- *Atomic counters* — для інкременту чи декременту атрибута (наприклад, лічильник відвідувачів).
- *Scan and delete every hour* — дорого по RCU/WCU і можна видалити ще не проаналізовані дані.
- *Delete and recreate the table every hour* — запис іде постійно, тож дані будуть губитися.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html>

</details>

---

<a id="t05-q09"></a>
### T05-Q09
*домен: визначено автоматично (у джерелі: AWS Compute)*

An engineer is constructing an AWS Lambda function and intends to log specific key events that transpire during the function's execution. To correlate the events with a particular function invocation, the engineer is looking to incorporate a unique identifier.

The following code segment has been added to the Lambda function:

- ▫️ function handler (event, context) {
- ▫️ }
- ✅ Use context.awsRequestId within the function to fetch the unique identifier associated with each invocation.
- ▫️ Use context.invocationId to extract the unique identifier tied to each function run.
- ▫️ Use event.requestId to obtain the unique identifier for each function execution.
- ▫️ Use context.lambdaId to get the unique identifier corresponding to each function invocation.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use context.awsRequestId within the function to fetch the unique identifier associated with each invocation»**

Об'єкт `context` у Lambda містить метадані функції й поточного виклику, зокрема унікальний ID запиту `awsRequestId`. За ним корелюють логи конкретного виклику.

**❌ Неправильні варіанти:**
- *event.requestId* — у об'єкті event такої властивості немає.
- *context.invocationId* — такої властивості немає.
- *context.lambdaId* — такої властивості немає.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/nodejs-context.html>

</details>

---

<a id="t05-q11"></a>
### T05-Q11
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **50×100=5000 > 1000 → ліміт.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A Developer is creating an AWS Lambda function that will process data from an Amazon Kinesis data stream. The function is expected to be invoked 50 times per second and take 100 seconds to complete each request.

What MUST the Developer do to ensure the functions runs without errors?

- ▫️ Increase the concurrency limit for the function
- ✅ Contact AWS and request to increase the limit for concurrent executions
- ▫️ No action is required as AWS Lambda can easily accommodate this requirement
- ▫️ Implement exponential backoff in the function code

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Contact AWS and request to increase the limit for concurrent executions»**

Concurrency — кількість запитів, які функція обслуговує одночасно; ліміт спільний для всіх функцій регіону. За банком початковий сплеск — 500–3000 залежно від регіону, далі +500 щохвилини (з 2023 — до 1000 на функцію кожні 10 с). Якщо запити надходять швидше, ніж функція масштабується, або досягнуто максимуму, зайві запити отримують помилку throttling (429). Невикористані екземпляри «заморожуються» без оплати. Регіональний ліміт за замовчуванням — **1000**, підвищують його через Support Center (Service Quotas).

Розрахунок: 50 викликів/с × 100 с = **5000** > 1000 → потрібне підвищення ліміту.

**❌ Неправильні варіанти:**
- *No action is required* — ліміт акаунта за замовчуванням недостатній.
- *Increase the concurrency limit for the function* — при ліміті акаунта 1000 функції можна зарезервувати максимум 900 (100 лишаються нерезервованими).
- *Exponential backoff* — сповільнить застосунок; правильно — підняти ліміт.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html>

</details>

---

<a id="t05-q36"></a>
### T05-Q36
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company is creating a serverless application that uses AWS Lambda functions. The developer has written the code to initialize the AWS SDK outside of the Lambda handler function.

What is PRIMARY benefit of this action?

- ▫️ It minimizes the deployment package size.
- ▫️ Improves readability and reduces complexity.
- ✅ Takes advantage of execution environment reuse.
- ▫️ Creates a new SDK instance for each invocation.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Takes advantage of execution environment reuse.»**

SDK-клієнти і з'єднання з БД ініціалізують поза handler, а статичні ресурси кешують у `/tmp`. Наступні виклики в тому самому середовищі виконання використовують ці ресурси повторно. Це скорочує час роботи функції, а отже й вартість. Головна вигода — повторне використання execution environment.

**❌ Неправильні варіанти:**
- *Creates a new SDK instance for each invocation* — це протилежне тому, чого досягає техніка.
- *Minimizes the deployment package size* — на розмір пакета ця техніка не впливає.
- *Improves readability* — можливо, але це спірно і не є головною причиною.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html>

</details>

---

<a id="t05-q54"></a>
### T05-Q54
*домен: визначено автоматично (у джерелі: AWS Database)*

An application uses Amazon API Gateway, an AWS Lambda function and a DynamoDB table. The developer requires that another Lambda function is triggered when an item lifecycle activity occurs in the DynamoDB table.

How can this be achieved?

- ▫️ Configure an Amazon CloudWatch alarm that sends an Amazon SNS notification. Trigger the Lambda function asynchronously from the SNS notification
- ▫️ Enable a DynamoDB stream and trigger the Lambda function asynchronously from the stream
- ✅ Enable a DynamoDB stream and trigger the Lambda function synchronously from the stream
- ▫️ Configure an Amazon CloudTrail API alarm that sends a message to an Amazon SQS queue. Configure the Lambda function to poll the queue and invoke the function synchronously

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable a DynamoDB stream and trigger the Lambda function synchronously from the stream»**

DynamoDB інтегрована з Lambda через тригери — код, що автоматично реагує на події в DynamoDB Streams. Stream ARN таблиці пов'язують із функцією через event source mapping. Щойно елемент змінюється, у stream з'являється запис. Lambda опитує stream і **синхронно** викликає функцію, коли бачить нові записи.

**❌ Неправильні варіанти:**
- *Asynchronously from the stream* — виклик зі stream синхронний.
- *CloudWatch alarm → SNS* — alarm не реагує на зміни окремих елементів. Краще DynamoDB Streams + Lambda.
- *CloudTrail API alarm → SQS* — такого alarm для змін елементів у CloudTrail немає.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.Lambda.html>

</details>

---

<a id="t05-q59"></a>
### T05-Q59
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is designing a cloud native application. The application will use several AWS Lambda functions that will process items that the functions read from an event source. Which AWS services are supported for Lambda event source mappings? (Select THREE.)

- ▫️ Another Lambda function
- ✅ Amazon Kinesis
- ✅ Amazon DynamoDB
- ✅ Amazon Simple Queue Service (SQS)
- ▫️ Amazon Simple Storage Service (S3)
- ▫️ Amazon Simple Notification Service (SNS)

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Kinesis» + «Amazon DynamoDB» + «Amazon Simple Queue Service (SQS)»**

Event source mapping — ресурс Lambda, який сам читає з джерела й викликає функцію. Він потрібен для потоків і черг сервісів, які не викликають Lambda напряму. Lambda читає події з:
- Kinesis;
- DynamoDB Streams;
- SQS;
- також MSK, self-managed Kafka, Amazon MQ і DocumentDB.

Event source mapping використовує дозволи execution role функції. Дозволи, структура подій, налаштування й поведінка опитування залежать від джерела.

**❌ Неправильні варіанти:**
- *SNS* — SNS сам викликає Lambda асинхронно (push), mapping не потрібен.
- *S3* — Lambda не читає з S3; event notification налаштовують на боці S3.
- *Another Lambda function* — іншу функцію викликають напряму, а не через mapping.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html>

</details>

---

<a id="t06-q11"></a>
### T06-Q11
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer is creating a REST service using Amazon API Gateway with AWS Lambda integration. The service adds data to a spreadsheet and the data is sent as query string parameters in the method request.

How should the Developer convert the query string parameters to arguments for the Lambda function?

- ▫️ Change the integration type
- ✅ Create a mapping template
- ▫️ Include the Amazon Resource Name (ARN) of the Lambda function
- ▫️ Enable request validation

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a mapping template»**

Стандартні мапінги API Gateway відображають параметри один до одного, а групу кодів відповіді інтеграції (за regex) — в один код. Mapping template overrides дають більше:
- many-to-one мапінг параметрів;
- перевизначення параметрів після стандартних мапінгів;
- умовний мапінг залежно від body чи інших параметрів;
- створення нових параметрів на льоту;
- заміна кодів статусу від інтеграції.

Перевизначити можна будь-який параметр запиту, заголовок відповіді чи код статусу. Наприклад:
- зібрати новий заголовок із двох параметрів;
- повернути успіх або помилку залежно від вмісту body;
- умовно перемапити параметр;
- розкласти JSON body на заголовки або query string.

Тож query string параметри перетворюються mapping template.

**❌ Неправильні варіанти:**
- *Request validation* — лише базова перевірка запиту перед інтеграцією.
- *Include the Lambda ARN* — ARN не допоможе перетворити параметри.
- *Change the integration type* — для перетворення не потрібно міняти тип інтеграції, наприклад на Lambda proxy.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-override-request-response-parameters.html>

</details>

---

<a id="t06-q26"></a>
### T06-Q26
*домен: визначено автоматично (у джерелі: AWS Compute)*

An application must be refactored for the cloud. The application data is stored in an Amazon DynamoDB table and is processed by a Lambda function which prepares the data for analytics. The data processing currently takes place once a day, but the data analysts require it to be performed in near-real time.

Which architecture pattern could be used to enable the data to be processed as it is received?

- ✅ Use an event-driven architecture.
- ▫️ Use a microservices architecture.
- ▫️ Use a fan-out architecture.
- ▫️ Use a scheduled architecture.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an event-driven architecture.»**

Подієва архітектура обробляє записи щойно вони надходять. Наприклад, вмикають DynamoDB Stream для таблиці, і Lambda читає з нього: подія (новий запис у stream) запускає обробку.

**❌ Неправильні варіанти:**
- *Microservices* — багато дрібних слабко пов'язаних компонентів. Елементи такої архітектури тут можуть бути, але не вони забезпечують обробку майже в реальному часі.
- *Fan-out* — наприклад, одне повідомлення SNS розсилається в багато черг SQS; це не про вимогу.
- *Scheduled* — обробка за розкладом, а не в міру надходження подій.

Джерела: <https://aws.amazon.com/event-driven-architecture/>

</details>

---

<a id="t07-q30"></a>
### T07-Q30
*домен: офіційна мітка*

A new member of your team is working on creating Dead Letter Queue (DLQ) for AWS Lambda functions.

As a Developer Associate, can you help him identify the use cases, wherein AWS Lambda will add a message into a DLQ after being processed? (Select two)

- ▫️ The Lambda function invocation is synchronous
- ▫️ The Lambda function invocation failed only once but succeeded thereafter
- ▫️ The event has been processed successfully
- ✅ The event fails all processing attempts
- ✅ The Lambda function invocation is asynchronous

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The event fails all processing attempts» + «The Lambda function invocation is asynchronous»**

- **Асинхронний виклик.** Якщо подія перевищила максимальний вік або вичерпала всі спроби, Lambda відкидає її або надсилає в DLQ, якщо він налаштований.
- **Усі спроби невдалі.** DLQ працює так само, як on-failure destination: туди потрапляють події, що не вдалося обробити за всі спроби або що прострочилися.

**❌ Неправильні варіанти:**
- *Synchronous invocation* — при синхронному виклику Lambda повертає помилку тому, хто викликав, і DLQ не використовується.
- *Processed successfully* — успішно оброблені події в DLQ не потрапляють.
- *Failed once, then succeeded* — подія зрештою оброблена успішно, тож у DLQ не потрапляє.

</details>

---

<a id="t07-q32"></a>
### T07-Q32
*домен: офіційна мітка*

A cybersecurity company is running a serverless backend with several compute-heavy workflows running on Lambda functions. The development team has noticed a performance lag after analyzing the performance metrics for the Lambda functions.

As a Developer Associate, which of the following options would you suggest as the BEST solution to address the compute-heavy workloads?

- ▫️ Use reserved concurrency to account for the compute-heavy workflows
- ▫️ Invoke the Lambda functions asynchronously to process the compute-heavy workflows
- ▫️ Use provisioned concurrency to account for the compute-heavy workflows
- ✅ Increase the amount of memory available to the Lambda functions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the amount of memory available to the Lambda functions»**

У Lambda ви вибираєте обсяг пам'яті, і пропорційно до нього виділяються CPU та інші ресурси. Тож щоб пришвидшити обчислювально важкі задачі, збільшують пам'ять. Налаштовується через консоль, CLI або SDK.

⚠️ В оригіналі вказано діапазон 128–3 008 MB з кроком 64 MB. Це застаріло: зараз пам'ять — від 128 MB до **10 240 MB** з кроком 1 MB, а на 1 769 MB функція отримує 1 повний vCPU.

**❌ Неправильні варіанти:**
- *Invoke asynchronously* — спосіб виклику не впливає на обчислювальну потужність.
- *Reserved concurrency* — concurrency — це кількість одночасних запитів, а не потужність одного виконання.
- *Provisioned concurrency* — так само не додає потужності одному виконанню.

</details>

---

<a id="t07-q34"></a>
### T07-Q34
*домен: офіційна мітка*

You are a developer working with the AWS CLI to create Lambda functions that contain environment variables. Your functions will require over 50 environment variables consisting of sensitive information of database table names.

What is the total set size/number of environment variables you can create for AWS Lambda?

- ▫️ The total size of all environment variables shouldn't exceed 8 KB. There is no limit on the number of variables
- ▫️ The total size of all environment variables shouldn't exceed 8 KB. The maximum number of variables that can be created is 50
- ▫️ The total size of all environment variables shouldn't exceed 4 KB. The maximum number of variables that can be created is 35
- ✅ The total size of all environment variables shouldn't exceed 4 KB. There is no limit on the number of variables

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The total size of all environment variables shouldn't exceed 4 KB. There is no limit on the number of variables»**

Змінна середовища — пара рядків у конфігурації конкретної версії функції. Runtime робить їх доступними коду і додає власні змінні з інформацією про функцію та виклик. Загальний розмір усіх змінних — до 4 KB; обмеження на кількість змінних немає.

**❌ Неправильні варіанти:**
- *8 KB, max 50* — ліміт 4 KB, а кількість не обмежена.
- *8 KB, no limit* — ліміт 4 KB.
- *4 KB, max 35* — кількість не обмежена.

</details>

---

<a id="t07-q42"></a>
### T07-Q42
*домен: офіційна мітка*

> ⚠️ **EventBridge Scheduler.** → [детальніше](../../06-contradictions-and-traps.md#c14)

Your company has a three-year contract with a healthcare provider. The contract states that monthly database backups must be retained for the duration of the contract for compliance purposes. Currently, the limit on backup retention for automated backups, on Amazon Relational Database Service (RDS), does not meet your requirements.

Which of the following solutions can help you meet your requirements?

- ▫️ Enable RDS Read replicas
- ▫️ Enable RDS Multi-AZ
- ✅ Create a cron expression in EventBridge Scheduler, which triggers an AWS Lambda function that triggers the database snapshot
- ▫️ Enable RDS automatic backups

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a cron expression in EventBridge Scheduler, which triggers an AWS Lambda function that triggers the database snapshot»**

Найпростіший спосіб запускати періодичні задачі — EventBridge разом із Lambda:
1. В EventBridge Scheduler створити розклад типу Recurring schedule.
2. Задати cron-вираз.
3. Вибрати target «Lambda Function».
4. У функції написати код, що створює snapshot.

Ручні snapshots зберігаються, доки їх не видалять, тож обмеження на термін зберігання бекапів немає.

ℹ️ Альтернатива без коду — AWS Backup з власним планом і терміном зберігання.

**❌ Неправильні варіанти:**
- *RDS automatic backups* — зберігаються максимум 35 днів.
- *Read replicas* — асинхронні копії для розвантаження читань, а не бекапи.
- *Multi-AZ* — висока доступність, а не бекапи чи термін їх зберігання.

</details>

---

<a id="t07-q51"></a>
### T07-Q51
*домен: офіційна мітка*

> ⚠️ **Lambda alias weighted.** → [детальніше](../../06-contradictions-and-traps.md#c13)

Recently, you started an online learning platform using AWS Lambda and AWS Gateway API. Your first version was successful, and you began developing new features for the second version. You would like to gradually introduce the second version by routing only 10% of the incoming traffic to the new Lambda version.

Which solution should you opt for?

- ✅ Use AWS Lambda aliases
- ▫️ Deploy your Lambda in a VPC
- ▫️ Use environment variables
- ▫️ Use Tags to distinguish the different versions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Lambda aliases»**

Alias Lambda — це вказівник на конкретну версію функції, і викликати функцію можна за ARN alias. Alias вказує лише на версію, а не на інший alias, і його можна перенаправити на нову версію.

Джерела подій, наприклад S3, зберігають зв'язок із функцією, яку викликати. Якщо в цьому зв'язку вказати alias, при зміні версії функції нічого оновлювати не потрібно.

**❌ Неправильні варіанти:**
- *Tags* — організовують функції за власником, проєктом чи відділом і допомагають у звітах про витрати; версіями не керують.
- *Environment variables* — змінюють поведінку функції без зміни коду, наприклад вказують, яку БД використовувати; версіями не керують.
- *Lambda in a VPC* — додає мережевий рівень безпеки і до задачі не стосується.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-tags.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html>

</details>

---

<a id="t08-q01"></a>
### T08-Q01
*домен: визначено автоматично (у джерелі: AWS Storage)*

A developer is in the process of revising multiple AWS Lambda functions and notes that these functions utilize the same bespoke libraries. The developer intends to centralize these libraries, implement updates with minimal effort, and keep the libraries version controlled.

Which solution aligns with these needs while requiring the least development effort?

- ▫️ Create an Amazon S3 bucket to store all the custom libraries.
- ▫️ Create a custom Amazon Machine Image (AMI) that includes the custom libraries.
- ▫️ Create an AWS CodeCommit repository for storing the custom libraries.
- ✅ Create a Lambda layer including all the custom libraries.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a Lambda layer including all the custom libraries.»**

Lambda layers дають спільно використовувати код, бібліотеки та інші ресурси в багатьох функціях. Версіоновані бібліотеки легко оновлювати й поширювати, і це потребує найменше розробки.

**❌ Неправильні варіанти:**
- *CodeCommit repository* — сервіс контролю версій, а не центральна бібліотека для Lambda.
- *Custom AMI* — до Lambda не стосується: вона працює без власних серверів.
- *S3 bucket* — це сховище, а не центральна бібліотека для функцій.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---

<a id="t08-q19"></a>
### T08-Q19
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to write some code to invoke an AWS Lambda function using the AWS Command Line Interface (CLI). Which option must be specified to cause the function to be invoked asynchronously?

- ✅ Set the –invocation-type option to Event
- ▫️ Set the –qualifier option to Asynchronous
- ▫️ Set the –payload option to Asynchronous
- ▫️ Set the –invocation-type option to Invoke

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set the –invocation-type option to Event»**

S3, SNS та інші сервіси викликають Lambda асинхронно: подію передають у Lambda і не чекають відповіді коду. Можна налаштувати обробку помилок і надсилання записів про виклики в downstream-ресурси. Для асинхронного виклику з CLI вказують `--invocation-type Event`:

`aws lambda invoke --function-name my-function --invocation-type Event --payload '{...}' response.json`

**❌ Неправильні варіанти:**
- *--invocation-type Invoke* — недопустиме значення; допустимі `RequestResponse`, `Event` і `DryRun`.
- *--payload Asynchronous* — `--payload` — це вхідний JSON для функції.
- *--qualifier Asynchronous* — `--qualifier` вказує версію або alias.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html> · <https://docs.aws.amazon.com/cli/latest/reference/lambda/invoke.html>

</details>

---

<a id="t08-q20"></a>
### T08-Q20
*домен: визначено автоматично (у джерелі: AWS Compute)*

```json
Based on the following AWS CLI command the resulting output, what has happened here?
$ aws lambda invoke --function-name MyFunction --invocation-type Event --payload ewogICJrZXkxIjogInZhbHVlMSIsCiAgImtleTIiOiAidmFsdWUyIiwKICAia2V5MyI6ICJ2YWx1ZTMiCn0= response.json
{
"StatusCode": 202
}
```

- ▫️ An AWS Lambda function has been invoked asynchronously and has not completed successfully
- ✅ An AWS Lambda function has been invoked asynchronously and has completed successfully
- ▫️ An AWS Lambda function has been invoked synchronously and has not completed successfully
- ▫️ An AWS Lambda function has been invoked synchronously and has completed successfully

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «An AWS Lambda function has been invoked asynchronously and has completed successfully»**

При асинхронному виклику Lambda ставить подію в чергу і одразу повертає успіх без додаткових даних, а окремий процес передає подію у функцію. Асинхронний виклик задається параметром `Event`. Тут використано `Event`, тож виклик асинхронний, а статус **202** означає, що подію успішно прийнято.

ℹ️ Для синхронного виклику успіх — **200**.

**❌ Неправильні варіанти:**
- *Synchronous, success* — параметр `Event` означає асинхронний виклик.
- *Synchronous, failure* — виклик асинхронний; для синхронного успіх був би 200.
- *Asynchronous, failure* — 202 означає успіх.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html>

</details>

---

<a id="t08-q25"></a>
### T08-Q25
*домен: визначено автоматично (у джерелі: AWS Compute)*

An application uses AWS Lambda to process many files. The Lambda function takes approximately 3 minutes to process each file and does not return any important data. A Developer has written a script that will invoke the function using the AWS CLI.

What is the FASTEST way to process all the files?

- ▫️ Invoke the Lambda function synchronously with the invocation type Event and process the files in parallel
- ▫️ Invoke the Lambda function synchronously with the invocation type RequestResponse and process the files sequentially
- ▫️ Invoke the Lambda function asynchronously with the invocation type RequestResponse and process the files sequentially
- ✅ Invoke the Lambda function asynchronously with the invocation type Event and process the files in parallel

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Invoke the Lambda function asynchronously with the invocation type Event and process the files in parallel»**

Lambda викликають з консолі, API, SDK, CLI і toolkits, з інших сервісів або через читання з потоку чи черги. Типи виклику:
- **Синхронний** (`RequestResponse`) — чекаєте, поки функція обробить подію і поверне відповідь.
- **Асинхронний** (`Event`) — відповіді не чекаєте; Lambda сама робить повторні спроби й може надсилати записи в destination.

Найшвидше обробити всі файли — асинхронно, паралельно, з типом `Event`.

**❌ Неправильні варіанти:**
- *Synchronous with Event* — для синхронного виклику тип `RequestResponse`.
- *Synchronous, RequestResponse, sequentially* — кожен файл чекає попередній, тож це не найшвидше.
- *Asynchronous with RequestResponse* — `RequestResponse` — це синхронний виклик.

Джерела: <https://aws.amazon.com/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/>

</details>

---

<a id="t08-q46"></a>
### T08-Q46
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer has deployed an AWS Lambda function and an Amazon DynamoDB table. The function code returns data from the DynamoDB table when it receives a request. The Developer needs to implement a front end that can receive HTTP GET requests and proxy the request information to the Lambda function.

What is the SIMPLEST and most COST-EFFECTIVE solution?

- ▫️ Implement an API Gateway API with a POST method
- ▫️ Implement an Elastic Load Balancer with a Lambda function target
- ✅ Implement an API Gateway API with Lambda proxy integration
- ▫️ Implement an Amazon Cognito User Pool with a Lambda proxy integration

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement an API Gateway API with Lambda proxy integration»**

Lambda proxy integration — простий спосіб побудувати API на одному методі. API Gateway передає функції сирий запит повністю: заголовки, query string, path-змінні, payload і конфігурацію API; не зберігається лише порядок параметрів. Функція може звертатися до інших сервісів і Lambda. Це найпростіший і найдешевший спосіб прийняти HTTP GET і передати його в Lambda.

**❌ Неправильні варіанти:**
- *POST method* — потрібен GET, бо POST для надсилання даних.
- *ELB with Lambda target* — можливо, але не найпростіше і не найдешевше.
- *Cognito User Pool with Lambda proxy* — Cognito не створює Lambda proxy-інтеграцій.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html>

</details>

---

<a id="t08-q53"></a>
### T08-Q53
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer has written some code that will connect and pull information from several hundred websites. The code needs to run on a daily schedule and execution time will be less than 60 seconds.

Which AWS service will be most suitable and cost-effective?

- ✅ AWS Lambda
- ▫️ Amazon EC2
- ▫️ Amazon ECS Fargate
- ▫️ Amazon API Gateway

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Lambda»**

Lambda — serverless-сервіс з максимальним часом виконання 900 секунд (15 хвилин). Для короткої задачі це найдешевше і найзручніше рішення. Запуск за розкладом налаштовують через EventBridge (раніше CloudWatch Events).

**❌ Неправильні варіанти:**
- *ECS Fargate* — для контейнерів і мікросервісів, а не для короткого запуску коду.
- *EC2* — постійно запущені інстанси дорожчі.
- *API Gateway* — створює API, а не виконує код.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html>

</details>

---

<a id="t09-q12"></a>
### T09-Q12
*домен: офіційна мітка*

> ⚠️ **Мінімум: UpdateItem + GetItem.** → [детальніше](../../06-contradictions-and-traps.md#c17)

A development team is working on an AWS Lambda function that accesses DynamoDB. The Lambda function must do an upsert, that is, it must retrieve an item and update some of its attributes or create the item if it does not exist.

Which of the following represents the solution with MINIMUM IAM permissions that can be used for the Lambda function to achieve this functionality?

- ▫️ dynamodb:UpdateItem, dynamodb:GetItem, dynamodb:PutItem
- ▫️ dynamodb:AddItem, dynamodb:GetItem
- ✅ dynamodb:UpdateItem, dynamodb:GetItem
- ▫️ dynamodb:GetRecords, dynamodb:PutItem, dynamodb:UpdateTable

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «dynamodb:UpdateItem, dynamodb:GetItem»**

Транзакції DynamoDB групують кілька дій в одну операцію «все або нічого»: `TransactWriteItems` або `TransactGetItems`. Права на дії Put, Update, Delete і Get у транзакції визначаються дозволами відповідних операцій: `PutItem`, `UpdateItem`, `DeleteItem` і `GetItem`. Для ConditionCheck є окремий дозвіл `dynamodb:ConditionCheckItem`.

`UpdateItem` змінює атрибути наявного елемента або створює новий, якщо його немає, і підтримує умовні оновлення. Тож `PutItem` тут не потрібен: достатньо `GetItem` і `UpdateItem`.

Список дій DynamoDB: https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html

**❌ Неправильні варіанти:**
- *AddItem, GetItem* — дії `AddItem` не існує.
- *GetRecords, PutItem, UpdateTable* — `GetRecords` належить до Streams, а `UpdateTable` змінює налаштування таблиці.
- *UpdateItem, GetItem, PutItem* — `PutItem` зайвий, тож порушується принцип найменших привілеїв.

Джерела: <https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html> · <https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/transaction-apis-iam.html> · <https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html>

</details>

---

<a id="t09-q20"></a>
### T09-Q20
*домен: офіційна мітка*

What steps can a developer take to optimize the performance of a CPU-bound AWS Lambda function and ensure fast response time?

- ▫️ Increase the function's CPU
- ▫️ Increase the function's timeout
- ✅ Increase the function's memory
- ▫️ Increase the function's provisioned concurrency

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the function's memory»**

Пам'ять — головний важіль продуктивності Lambda. Її можна задати від 128 MB до 10 240 MB; за замовчуванням консоль ставить найменше значення. Разом із пам'яттю пропорційно зростає кількість vCPU. Тож для функцій, обмежених CPU, мережею чи пам'яттю, збільшення пам'яті може різко покращити продуктивність.

**❌ Неправильні варіанти:**
- *Provisioned concurrency* — заздалегідь ініціалізує середовища (платно), але CPU не додає.
- *Reserved concurrency* — гарантує максимум одночасних екземплярів (безкоштовно), але CPU не додає.
- *Increase CPU* — CPU окремо не налаштовується, лише через пам'ять.

Джерела: <https://docs.aws.amazon.com/lambda/latest/operatorguide/computing-power.html> · <https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html>

</details>

---

<a id="t10-q33"></a>
### T10-Q33
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

Every time an Amazon EC2 instance is launched, certain metadata about the instance should be recorded in an Amazon DynamoDB table. The data is gathered and written to the table by an AWS Lambda function.

What is the MOST efficient method of invoking the Lambda function?

- ✅ Create a CloudWatch Event with an event pattern looking for EC2 state changes and a target set to use the Lambda function
- ▫️ Configure detailed monitoring on Amazon EC2 and create an alarm that triggers the Lambda function in initialization
- ▫️ Create a CloudWatch alarm that triggers the Lambda function based on log streams indicating an EC2 state change in CloudWatch logs
- ▫️ Create a CloudTrail trail alarm that triggers the Lambda function based on the RunInstances API action

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a CloudWatch Event with an event pattern looking for EC2 state changes and a target set to use the Lambda function»**

CloudWatch Events, тепер EventBridge, — майже реального часу потік подій про зміни ресурсів AWS. Правило з event pattern (EC2 Instance State-change Notification) спрямовує подію в ціль, тут у Lambda. Сервіс реагує на операційні зміни: надсилає повідомлення, запускає функції, вносить зміни, фіксує стан.

**❌ Неправильні варіанти:**
- *Alarm on CloudWatch Logs streams* — EC2 за замовчуванням не створює log groups.
- *CloudTrail trail alarm* — CloudTrail не має власних alarms; потрібен CloudWatch.
- *Detailed monitoring alarm on initialization* — detailed monitoring дає лише частіші метрики; запуск інстансу так не відловити.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html>

</details>

---

<a id="t10-q50"></a>
### T10-Q50
*домен: визначено автоматично (у джерелі: AWS Compute)*

```json
Based on the following AWS CLI command the resulting output, what has happened here?
$ aws lambda invoke --function-name MyFunction --payload ewogICJrZXkxIjogInZhbHVlMSIsCiAgImtleTIiOiAidmFsdWUyIiwKICAia2V5MyI6ICJ2YWx1ZTMiCn0= response.json
{
"StatusCode": 200
}
```

- ▫️ An AWS Lambda function has been invoked synchronously and has not completed successfully
- ▫️ An AWS Lambda function has been invoked asynchronously and has not completed successfully
- ✅ An AWS Lambda function has been invoked synchronously and has completed successfully
- ▫️ An AWS Lambda function has been invoked asynchronously and has completed successfully

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «An AWS Lambda function has been invoked synchronously and has completed successfully»**

При синхронному виклику Lambda виконує функцію, чекає на результат і повертає відповідь коду разом із додатковими даними, наприклад версією функції. З CLI це команда `invoke`. Тут параметра `--invocation-type Event` немає, тож виклик синхронний, а статус **200** означає успіх.

ℹ️ Строго кажучи, 200 повертається і тоді, коли сам код функції впав; у такому разі у відповіді буде поле `FunctionError`. Для іспиту: 200 — синхронний успіх, 202 — асинхронний.

**❌ Неправильні варіанти:**
- *Synchronous, not successful* — 200 означає успіх.
- *Asynchronous, successful* — без `Event` виклик синхронний.
- *Asynchronous, not successful* — без `Event` виклик синхронний.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-sync.html>

</details>

---

<a id="t10-q51"></a>
### T10-Q51
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to run some code using Lambda in response to an event and forward the execution result to another application using a pub/sub notification.

How can the Developer accomplish this?

- ✅ Configure a Lambda “on success” destination and route the execution results to Amazon SNS
- ▫️ Configure a CloudWatch Events alarm the triggers based on Lambda execution success and route the execution results to Amazon SQS
- ▫️ Configure a Lambda “on success” destination and route the execution results to Amazon SQS
- ▫️ Configure a CloudWatch Events alarm the triggers based on Lambda execution success and route the execution results to Amazon SNS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure a Lambda “on success” destination and route the execution results to Amazon SNS»**

Destinations надсилають результати асинхронного виклику в інший ресурс без додаткового коду. Результат містить версію, час, контекст і payload запиту, контекст і payload відповіді. Для кожного статусу (Success чи Failure) можна вибрати одне з чотирьох призначень: Lambda, SNS, SQS (standard) або EventBridge.

Тут результат успішного виконання йде в SNS-топік, і застосунок-підписник отримує сповіщення.

**❌ Неправильні варіанти:**
- *CloudWatch Events alarm → SNS* — CloudWatch Events відстежує зміни стану ресурсів; результати виконання передають через destinations.
- *On success → SQS* — SQS — черга, а не pub/sub-сервіс сповіщень.
- *CloudWatch Events alarm → SQS* — неправильний механізм і неправильне призначення.

Джерела: <https://aws.amazon.com/about-aws/whats-new/2019/11/aws-lambda-supports-destinations-for-asynchronous-invocations/>

</details>

---

<a id="t10-q54"></a>
### T10-Q54
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **40×100=4000 > 1000 → ліміт.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A Developer has created an AWS Lambda function in a new AWS account. The function is expected to be invoked 40 times per second and the execution duration will be around 100 seconds. What MUST the Developer do to ensure there are no errors?

- ▫️ Implement error handling within the function code
- ▫️ Implement a Dead Letter Queue to capture invocation errors
- ▫️ Implement tracing with X-Ray
- ✅ Contact AWS Support to increase the concurrent execution limits

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Contact AWS Support to increase the concurrent execution limits»**

Concurrency — кількість запитів, які функція обслуговує одночасно. Тут 40 викликів на секунду × 100 секунд виконання = до **4 000** одночасних виконань. Це вище за ліміт за замовчуванням — 1 000 на регіон, тож ліміт треба збільшити через Service Quotas або підтримку.

**❌ Неправильні варіанти:**
- *Error handling in code* — корисно, але для цього сценарію не обов'язково.
- *DLQ* — для обробки невдалих повідомлень, а не для ліміту concurrency.
- *X-Ray tracing* — конкретних проблем, які треба трасувати, ще немає.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html>

</details>

---

<a id="t11-q20"></a>
### T11-Q20
*домен: офіційна мітка*

A company is creating a gaming application that will be deployed on mobile devices. The application will send data to a Lambda function-based RESTful API. The application will assign each API request a unique identifier. The volume of API requests from the application can randomly vary at any given time of day. During request throttling, the application might need to retry requests. The API must be able to address duplicate requests without inconsistencies or data loss.

Which of the following would you recommend to handle these requirements?

- ▫️ Persist the unique identifier for each request in an RDS MySQL table. Change the Lambda function to check the table for the identifier before processing the request
- ▫️ Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to send a client error response when the function receives a duplicate request
- ✅ Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to check the table for the identifier before processing the request
- ▫️ Persist the unique identifier for each request in an ElastiCache for Memcached cache. Change the Lambda function to check the cache for the identifier before processing the request

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to check the table for the identifier before processing the request»**

DynamoDB — повністю керована serverless NoSQL-БД (key-value), що масштабується до будь-якого навантаження. Має вбудовану безпеку, безперервні бекапи, PITR (відновлення до будь-якої секунди за останні 35 днів), мультирегіональну реплікацію. Тож дані не втрачаються. Функція перевіряє ідентифікатор у таблиці й обробляє запит, лише якщо він новий, тобто забезпечує ідемпотентність.

Огляд: https://aws.amazon.com/dynamodb/

**❌ Неправильні варіанти:**
- *ElastiCache for Memcached* — без snapshots і реплікації, тож дані можна втратити.
- *RDS MySQL* — масштабується вертикально і гірше справляється з різкими сплесками записів, ніж DynamoDB.
- *DynamoDB + client error on duplicate* — відповідь на дублікат буде непослідовною: клієнт отримає помилку замість звичайного результату.

Джерела: <https://aws.amazon.com/dynamodb/> · <https://aws.amazon.com/elasticache/redis-vs-memcached/>

</details>

---

<a id="t12-q17"></a>
### T12-Q17
*домен: офіційна мітка*

A company has AWS Lambda functions where each is invoked by other AWS services such as Amazon Kinesis Data Firehose, Amazon API Gateway, Amazon Simple Storage Service, or Amazon EventBridge. What these Lambda functions have in common is that they process heavy workloads such as big data analysis, large file processing, and statistical computations.

What should you do to improve the performance of your AWS Lambda functions without changing your code?

- ▫️ Change the instance type for your Lambda function
- ▫️ Change your Lambda function runtime to use Golang
- ▫️ Increase the Lambda function timeout
- ✅ Increase the RAM assigned to your Lambda function

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the RAM assigned to your Lambda function»**

Lambda виділяє CPU та інші ресурси пропорційно до пам'яті. Пам'ять задають від 128 до 10 240 MB з кроком 1 MB; на 1 769 MB функція має еквівалент одного vCPU. Налаштовується в консолі, CLI чи SDK. Тож для обчислювально важких задач збільшують пам'ять.

**❌ Неправильні варіанти:**
- *Change the instance type* — типи інстансів є в EC2, а не в Lambda.
- *Switch to Golang* — потрібно переписати код, і продуктивність від цього може не зрости.
- *Increase timeout* — функція працюватиме довше, але не швидше.

</details>

---

<a id="t12-q48"></a>
### T12-Q48
*домен: офіційна мітка*

> ⚠️ **Stage variables + aliases.** → [детальніше](../../06-contradictions-and-traps.md#c13)

You are a developer working at a cloud company that embraces serverless. You have performed your initial deployment and would like to work towards adding API Gateway stages and associate them with existing deployments. Your stages will include prod, test, and dev and will need to match a Lambda function variant that can be updated over time.

Which of the following features must you add to achieve this? (select two)

- ▫️ Mapping Templates
- ▫️ Lambda Versions
- ▫️ Lambda X-Ray integration
- ✅ Stage Variables
- ✅ Lambda Aliases

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Stage Variables» + «Lambda Aliases»**

- **Stage variables** — пари «ім'я–значення», що працюють як змінні середовища stage у налаштуваннях і mapping templates. Кожен stage (alpha, beta, prod) може викликати свій бекенд, наприклад prod — `example.com`, beta — `beta.example.com`.
- **Lambda aliases** — вказівники на версії функції, тобто «змінні» посилання на незмінні версії. Aliases `dev`, `test`, `prod` лишаються стабільними, а версії за ними змінюються.

Разом: stage variable підставляє ім'я alias у ARN інтеграції.

**❌ Неправильні варіанти:**
- *Lambda Versions* — версії незмінні; потрібен вказівник, тобто alias.
- *X-Ray integration* — трасування і налагодження; до задачі не стосується.
- *Mapping Templates* — перетворюють параметри й payload; до задачі не стосуються.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-override-request-response-parameters.html>

</details>

---

<a id="t12-q53"></a>
### T12-Q53
*домен: офіційна мітка*

The development team at an IT company has configured an Application Load Balancer (ALB) with a Lambda function A as the target but the Lambda function A is not able to process any request from the ALB. Upon investigation, the team finds that there is another Lambda function B in the AWS account that is exceeding the concurrency limits.

How can the development team address this issue?

- ✅ Set up reserved concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit
- ▫️ Use an API Gateway instead of an Application Load Balancer (ALB) for Lambda function A
- ▫️ Set up provisioned concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit
- ▫️ Use a Cloudfront Distribution instead of an Application Load Balancer (ALB) for Lambda function A

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up reserved concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit»**

Concurrency — кількість запитів, які функція обслуговує одночасно. **Reserved concurrency**:
- гарантує функції певну частину ліміту concurrency, і іншим функціям вона недоступна;
- водночас **обмежує максимум** для цієї функції, включно з усіма її версіями й aliases.

Reserved concurrency для B обмежить її, і B не забиратиме весь ліміт акаунта. Тоді функція A працюватиме без throttling.

**❌ Неправильні варіанти:**
- *Provisioned concurrency for B* — заздалегідь ініціалізує середовища, щоб уникнути cold start, але максимум не обмежує.
- *API Gateway instead of ALB* — не впливає на concurrency функції B.
- *CloudFront instead of ALB* — так само не впливає.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html#configuration-concurrency-reserved> · <https://aws.amazon.com/blogs/networking-and-content-delivery/lambda-functions-as-targets-for-application-load-balancers/>

</details>

---

<a id="t12-q55"></a>
### T12-Q55
*домен: офіційна мітка*

A company that specializes in cloud communications platform as a service allows software developers to programmatically use their services to send and receive text messages. The initial platform did not have a scalable architecture as all components were hosted on one server and should be redesigned for high availability and scalability.

Which of the following options can be used to implement the new architecture? (select two)

- ▫️ EBS + RDS
- ▫️ SES + S3
- ▫️ CloudWatch + CloudFront
- ✅ API Gateway + Lambda
- ✅ ALB + ECS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «API Gateway + Lambda» + «ALB + ECS»**

- **ALB + ECS.** ECS запускає Docker-контейнери в керованому кластері. ALB розподіляє трафік між цілями (EC2, контейнери, IP, Lambda) в одній чи кількох AZ. Разом вони дають масштабований і високодоступний REST API.
- **API Gateway + Lambda.** API Gateway — «вхідні двері» до бекенду (EC2, Lambda) з публікацією, моніторингом і захистом. Lambda виконує код без серверів. Разом — serverless REST API з автентифікацією, повністю масштабований і високодоступний.

Детальніше:
- https://aws.amazon.com/ecs/
- https://aws.amazon.com/api-gateway/
- https://aws.amazon.com/lambda/

**❌ Неправильні варіанти:**
- *SES + S3* — лише пошта й об'єктне сховище.
- *CloudWatch + CloudFront* — лише моніторинг і CDN.
- *EBS + RDS* — лише блокове сховище й БД.

Джерела: <https://aws.amazon.com/getting-started/projects/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/module-4/> · <https://aws.amazon.com/blogs/compute/microservice-delivery-with-amazon-ecs-and-application-load-balancers/>

</details>

---
