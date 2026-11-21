# Secrets Manager / Parameter Store

**Домен 2 — Безпека** · питань: 10  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#secrets) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q10"></a>
### T03-Q10
*домен: офіційна мітка*

You are planning to build a fleet of EBS-optimized EC2 instances to handle the load of your new application. Due to security compliance, your organization wants any secret strings used in the application to be encrypted to prevent exposing values as clear text.

The solution requires that decryption events be audited and API calls to be simple. How can this be achieved? (select two)

- ▫️ Store the secret as PlainText in SSM Parameter Store
- ✅ Store the secret as SecureString in SSM Parameter Store
- ▫️ Encrypt first with KMS then store in SSM Parameter store
- ▫️ Audit using SSM Audit Trail
- ✅ Audit using CloudTrail

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Store the secret as SecureString in SSM Parameter Store» та «Audit using CloudTrail»**

У Parameter Store параметри типу **SecureString** мають відкрите ім'я і зашифроване значення; шифрує й розшифровує їх AWS KMS. З customer managed ключем доступ до шифрування й розшифрування регулюють IAM- і key-політики. Розшифроване значення отримують одним викликом API (`GetParameter --with-decryption`).

AWS CloudTrail логує й зберігає всі дії в акаунті (консоль, SDK, CLI, сервіси), тобто покаже всі виклики до SSM і KMS, включно з розшифруваннями.

**❌ Неправильні варіанти:**
- *Encrypt first with KMS then store in SSM* — спрацює, але для отримання значення потрібно два виклики замість одного.
- *Store the secret as PlainText* — незахищено.
- *SSM Audit Trail* — вигадано.

</details>

---

<a id="t05-q12"></a>
### T05-Q12
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application runs on a fleet of Amazon EC2 instances and stores data in a Microsoft SQL Server database hosted on Amazon RDS. The developer wants to avoid storing database connection credentials the application code. The developer would also like a solution that automatically rotates the credentials.

What is the MOST secure way to store and access the database credentials?

- ▫️ Create an IAM role that has permissions to access the database. Attach the role to the EC2 instance.
- ✅ Use AWS Secrets Manager to store the credentials. Retrieve the credentials from Secrets Manager as needed.
- ▫️ Use AWS Systems Manager Parameter store to store the credentials. Enable automatic rotation of the credentials.
- ▫️ Store the credentials in an encrypted source code repository. Retrieve the credentials from AWS CodeCommit as needed.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Secrets Manager to store the credentials. Retrieve the credentials from Secrets Manager as needed.»**

AWS Secrets Manager призначений для безпечного зберігання секретів, зокрема облікових даних до БД. Він підтримує автоматичну ротацію для кількох типів RDS, включно з Microsoft SQL Server. Це найбезпечніший спосіб зберігати й отримувати облікові дані.

**❌ Неправильні варіанти:**
- *SSM Parameter Store with automatic rotation* — Parameter Store не має вбудованої автоматичної ротації; її можна зробити лише власною Lambda-функцією.
- *IAM role attached to EC2* — RDS for SQL Server підтримує Windows-автентифікацію через Managed Microsoft AD, але в цьому сценарії її не описано.
- *Encrypted source code repository / CodeCommit* — репозиторій коду не підходить для зберігання облікових даних до БД і не підтримує автоматичну ротацію.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html>

</details>

---

<a id="t05-q55"></a>
### T05-Q55
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application will use AWS Lambda and an Amazon RDS database. The Developer needs to secure the database connection string and enable automatic rotation every 30 days. What is the SIMPLEST way to achieve this requirement?

- ▫️ Store the connection string in an encrypted Amazon S3 bucket and use a scheduled CloudWatch Event to update the connection string every 30 days
- ▫️ Store a SecureString in Systems Manager Parameter Store and enable automatic rotation every 30 days
- ▫️ Store the connection string as an encrypted environment variable in Lambda and create a separate function that rotates the connection string every 30 days
- ✅ Store a secret in AWS Secrets Manager and enable automatic rotation every 30 days

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Store a secret in AWS Secrets Manager and enable automatic rotation every 30 days»**

Secrets Manager шифрує секрети в стані спокою ключами KMS. Під час отримання розшифровує їх і передає через TLS. Ротацію можна робити за розкладом або на вимогу через консоль, SDK чи CLI. Для пароля БД вказують тип БД, частоту ротації та master-облікові дані. Нативно підтримуються RDS, DocumentDB і Redshift.

**❌ Неправильні варіанти:**
- *SecureString in Parameter Store with automatic rotation* — Parameter Store не має автоматичної ротації.
- *Encrypted Lambda env variable + rotation function* — не найпростіше: Secrets Manager має ротацію з коробки.
- *S3 + scheduled CloudWatch Event* — CloudWatch сам не оновить рядок підключення; знадобиться ще Lambda, якої у варіанті немає.

Джерела: <https://aws.amazon.com/secrets-manager/features/>

</details>

---

<a id="t06-q39"></a>
### T06-Q39
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

> ⚠️ **Secrets Manager (Parameter Store відсутній у варіантах).** → [детальніше](../../06-contradictions-and-traps.md#c06)

A programmer is rolling out a new solution to Amazon ECS. This solution necessitates secure handling and access to diverse variables such as credentials, remote API authentication details, and the API URL. The remote API authentication information and API URL should be accessible across all versions of the solution, spanning development, testing, and production environments.

What is the most efficient method for the programmer to access these variables with minimal modifications to the solution?

- ▫️ Include these variables directly in the application code and use version control system to manage different versions.
- ▫️ Create environment variables for these details and access them within the application code.
- ▫️ Use AWS S3 to store these variables and access them via AWS SDK within the application.
- ✅ Utilize AWS Secrets Manager to store these variables and retrieve them directly within the application using AWS SDK.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Utilize AWS Secrets Manager to store these variables and retrieve them directly within the application using AWS SDK.»**

Secrets Manager створений для чутливих даних: облікових даних до БД, API-ключів та інших секретів. Він зберігає, ротує й видає їх протягом усього життєвого циклу — для ресурсів в AWS, сторонніх сервісів і on-premises.

ℹ️ В ECS секрет можна також передати в контейнер через `secrets` → `valueFrom` у task definition. Тоді він з'являється як змінна середовища, але в task definition відкритим текстом не зберігається.

**❌ Неправильні варіанти:**
- *In application code + VCS* — небезпечно, порушує найкращі практики безпеки.
- *Plain environment variables* — видно відкритим текстом у task definition і консолі ECS; для секретів не годиться.
- *S3* — це сховище об'єктів; зберігати в ньому секрети не відповідає найкращим практикам.

Джерела: <https://aws.amazon.com/secrets-manager/>

</details>

---

<a id="t06-q44"></a>
### T06-Q44
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A developer wants to use a cron job to schedule key rotation for an Amazon RDS database. What steps should the developer take to complete this task?

- ▫️ Use AWS Identity and Access Management (IAM) to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.
- ✅ Use AWS Secrets Manager to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.
- ▫️ Use AWS Systems Manager (SSM) Parameter Store to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.
- ▫️ Use AWS Relational Database Service to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Secrets Manager to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.»**

Ротація — це періодичне оновлення секрету: облікові дані змінюються і в секреті, і в самій БД чи сервісі. Secrets Manager підтримує автоматичну ротацію за розкладом, зокрема заданим cron-виразом.

Деякі сервіси пропонують **managed rotation**: ротацію налаштовує й веде сам сервіс, без вашої Lambda-функції. Так працює, зокрема, RDS.

**❌ Неправильні варіанти:**
- *IAM managed rotation* — IAM керує дозволами користувачів, груп і ролей, а не секретами.
- *RDS managed rotation* — RDS спрощує розгортання БД, але не є сервісом керування секретами. ℹ️ RDS може керувати master-паролем, але робить це через Secrets Manager.
- *SSM Parameter Store* — не має вбудованої ротації за cron-розкладом.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotate-secrets_schedule.html>

</details>

---

<a id="t09-q17"></a>
### T09-Q17
*домен: офіційна мітка*

> ⚠️ **Parameter Store (Secrets Manager відсутній у варіантах).** → [детальніше](../../06-contradictions-and-traps.md#c06)

A developer wants to securely store and retrieve various types of variables, such as remote API authentication information, API URL, and related credentials across different environments of an application deployed on Amazon Elastic Container Service (Amazon ECS).

What would be the best approach that needs minimal modifications in the application code?

- ▫️ Configure the application to fetch the variables from AWS KMS by storing the API URL and credentials as unique keys in KMS for each environment
- ▫️ Configure the application to fetch the variables from each of the deployed environments by defining the authentication information and API URL in the ECS task definition as unique names during the deployment process
- ▫️ Configure the application to fetch the variables from an encrypted file that is stored with the application by storing the API URL and credentials in unique files for each environment
- ✅ Configure the application to fetch the variables and credentials from AWS Systems Manager Parameter Store by leveraging hierarchical unique paths in Parameter Store for each variable in each environment

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure the application to fetch the variables and credentials from AWS Systems Manager Parameter Store by leveraging hierarchical unique paths in Parameter Store for each variable in each environment»**

Parameter Store — можливість AWS Systems Manager для безпечного ієрархічного зберігання конфігурацій і секретів. У ньому зберігають паролі, рядки підключення, ID AMI, ліцензійні коди — відкритим текстом або зашифровано. На параметри посилаються за іменем зі скриптів, команд, SSM-документів і workflows.

Плаский список із десятків чи сотень параметрів важко підтримувати: легко взяти не той параметр або продублювати дані. Ієрархії розв'язують це: ім'я параметра містить шлях через `/`, наприклад `/prod/payment/api_url` і `/dev/payment/api_url`.

**❌ Неправильні варіанти:**
- *AWS KMS as key-value store* — KMS керує криптографічними ключами і не зберігає пари ключ–значення.
- *Encrypted file with the application* — зберігати облікові дані у файлі разом із застосунком — погана практика.
- *Environment variables in ECS task definition* — task definition — це JSON-опис застосунку: контейнери, launch type, порти, томи. Змінні середовища в ній видно кожному, хто має дозвіл `DescribeTaskDefinition`. AWS радить тримати чутливі дані в Secrets Manager або Parameter Store.

Джерела: <https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-hierarchies.html> · <https://aws.amazon.com/kms/> · <https://ecsworkshop.com/introduction/ecs_basics/task_definition/> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/taskdef-envfiles.html>

</details>

---

<a id="t09-q22"></a>
### T09-Q22
*домен: офіційна мітка*

A company wants to share information with a third party via an HTTP API endpoint managed by the third party. The company has the necessary API key to access the endpoint and the integration of the API key with the company's application code must not impact the application's performance.

What is the most secure approach?

- ▫️ Keep the API credentials in an encrypted file in S3 and use the credentials to make the API call by fetching the API credentials from S3 at runtime by using the AWS SDK
- ▫️ Keep the API credentials in an encrypted table in MySQL RDS and use the credentials to make the API call by fetching the API credentials from RDS at runtime by using the AWS SDK
- ▫️ Keep the API credentials in a local code variable and use the local code variable at runtime to make the API call
- ✅ Keep the API credentials in AWS Secrets Manager and use the credentials to make the API call by fetching the API credentials at runtime by using the AWS SDK

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Keep the API credentials in AWS Secrets Manager and use the credentials to make the API call by fetching the API credentials at runtime by using the AWS SDK»**

Secrets Manager замінює захардкоджені облікові дані викликом API, який отримує секрет під час виконання. Секрету немає в коді, тож його не знайде той, хто переглядає код. Автоматична ротація за розкладом замінює довгострокові секрети короткостроковими.

Раніше облікові дані вбудовували в застосунок, і для ротації його доводилося оновлювати й перерозгортати. Якщо одну зі спільних копій пропускали, застосунок падав, тож ротацію часто просто не робили. Кешування секретів додатково підвищує доступність і зменшує затримку.

**❌ Неправильні варіанти:**
- *Encrypted table in RDS* — тримати облікові дані в базі даних — погана практика.
- *Encrypted file in S3* — тримати облікові дані у файлі в об'єктному сховищі — погана практика.
- *Local code variable* — тримати облікові дані в коді — погана практика.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html> · <https://aws.amazon.com/blogs/security/improve-availability-and-latency-of-applications-by-using-aws-secret-managers-python-client-side-caching-library/>

</details>

---

<a id="t10-q14"></a>
### T10-Q14
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A healthcare service wants to exchange patient data securely with a partner organization through an HTTP API endpoint provided by the partner. The healthcare service has the requisite API key for accessing the HTTP API. The service needs a solution to manage the API key through code.

Which method will fulfill these requirements with maximum security?

- ▫️ Store the API key in an environment variable on the application server.
- ▫️ Store the API key in the application code.
- ✅ Use AWS Secrets Manager to store and retrieve the API key.
- ▫️ Use Amazon DynamoDB to store the API key and retrieve it when needed.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Secrets Manager to store and retrieve the API key.»**

Secrets Manager захищає доступ до застосунків, сервісів і ресурсів. Він ротує, керує й видає API-ключі, облікові дані до БД та інші секрети протягом усього їхнього життєвого циклу. Найбезпечніший варіант.

**❌ Неправильні варіанти:**
- *In application code* — ризик витоку ключа.
- *DynamoDB* — немає такого керування безпекою і ротації, як у Secrets Manager.
- *Environment variable on the server* — може витекти через логи чи інші канали.

Джерела: <https://aws.amazon.com/secrets-manager/>

</details>

---

<a id="t10-q61"></a>
### T10-Q61
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization is developing a data processing application that is hosted on AWS Lambda and utilizes a PostgreSQL database on Amazon RDS. The security team mandates a policy that requires rotating database credentials every week.

What strategy should the developer adopt to manage the database credentials for the application?

- ▫️ Enable IAM database authentication and manage weekly rotation manually.
- ✅ Deploy AWS Secrets Manager to store database credentials and set up automatic weekly rotation.
- ▫️ Store database credentials in Amazon S3 buckets with versioning enabled, rotating, and uploading new credentials weekly.
- ▫️ Store the credentials in environment variables of the Lambda function and manually update them every week.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy AWS Secrets Manager to store database credentials and set up automatic weekly rotation.»**

Secrets Manager створює, видає й ротує секрети, зокрема облікові дані до БД. Автоматична щотижнева ротація задовольняє вимогу безпеки з мінімумом ручної роботи.

**❌ Неправильні варіанти:**
- *S3 with versioning* — небезпечно, і вбудованої ротації немає.
- *IAM DB auth + manual rotation* — IAM DB auth використовує токени, і автоматичної ротації в такій постановці немає.
- *Lambda env vars + manual update* — ризик витоку і багато ручної роботи.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotate-secrets_managed.html>

</details>

---

<a id="t11-q31"></a>
### T11-Q31
*домен: офіційна мітка*

You are running workloads on AWS and have embedded RDS database connection strings within each web server hosting your applications. After failing a security audit, you are looking at a different approach to store your secrets securely and automatically rotate the database credentials.

Which AWS service can you use to address this use-case?

- ✅ Secrets Manager
- ▫️ Systems Manager
- ▫️ KMS
- ▫️ SSM Parameter Store

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Secrets Manager»**

Secrets Manager ротує, керує й видає облікові дані до БД, API-ключі та інші секрети протягом їх життєвого циклу. Застосунки отримують секрети через API, тож нічого не зберігається в коді. Вбудована ротація для RDS, Redshift і DocumentDB.

Переваги: https://aws.amazon.com/secrets-manager/

**❌ Неправильні варіанти:**
- *SSM Parameter Store* — безпечне ієрархічне сховище конфігурацій і секретів, але автоматичної ротації облікових даних до БД немає.
- *Systems Manager* — дає видимість і контроль над інфраструктурою; ротації секретів немає.
- *KMS* — керує ключами шифрування, а не секретами.

Джерела: <https://aws.amazon.com/secrets-manager/> · <https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-paramstore.html>

</details>

---
