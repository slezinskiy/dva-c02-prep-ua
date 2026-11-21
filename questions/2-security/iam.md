# IAM / STS / Organizations

**Домен 2 — Безпека** · питань: 39  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q10"></a>
### T01-Q10
*домен: офіційна мітка*

When your company first created an AWS account, you began with a single sign-in principal called a root user account that had complete access to all AWS services and resources.

What should you do to adhere to best practices for using the root user account?

- ▫️ It should be accessible by 3 to 6 members of the IT team
- ✅ It should be accessible by one admin only after enabling Multi-factor authentication
- ▫️ It should be accessible using the access key id and secret access key
- ▫️ It should be accessible by no one, throw away the passwords after creating the account

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «It should be accessible by one admin only after enabling Multi-factor authentication»**

Якщо root-обліковий запис усе ж використовується, AWS рекомендує ввімкнути для нього MFA. Root може виконувати найчутливіші операції в акаунті, тому додатковий фактор автентифікації суттєво підвищує захист.

**❌ Неправильні варіанти:**
- *Accessible by 3 to 6 members of the IT team* — облікові дані root має лише власник акаунта. Для ІТ-команди створюють IAM-групу з адмінськими правами.
- *Accessible using the access key id and secret access key* — AWS рекомендує не створювати й не використовувати access keys для root.
- *Accessible by no one, throw away the passwords* — пароль root усе одно треба десь безпечно зберігати.

</details>

---

<a id="t01-q29"></a>
### T01-Q29
*домен: офіційна мітка*

Your company likes to operate multiple AWS accounts so that teams have their environments. Services deployed across these accounts interact with one another, and now there's a requirement to implement X-Ray traces across all your applications deployed on EC2 instances and AWS accounts.

As such, you would like to have a unified account to view all the traces. What should you in your X-Ray daemon set up to make this work? (Select two)

- ▫️ Create a user in the target unified account and generate access and secret keys
- ✅ Configure the X-Ray daemon to use an IAM instance role
- ▫️ Enable Cross Account collection in the X-Ray console
- ▫️ Configure the X-Ray daemon to use access and secret keys
- ✅ Create a role in the target unified account and allow roles in each sub-account to assume the role.

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Create a role in the target unified account and allow roles in each sub-account to assume the role» та «Configure the X-Ray daemon to use an IAM instance role»**

AWS X-Ray допомагає аналізувати й дебажити розподілені застосунки (наприклад, мікросервіси): показує повний шлях запитів і карту компонентів, щоб знайти першопричину проблем із продуктивністю та помилок.

X-Ray агент може припускати роль, щоб публікувати дані в інший акаунт, ніж той, де він працює. Так дані з різних компонентів збираються в одному центральному акаунті. X-Ray також відстежує запити між кількома регіонами.

**❌ Неправильні варіанти:**
- *Create a user in the target unified account and generate access and secret keys* + *Configure the X-Ray daemon to use access and secret keys* — разом спрацюють, але це погана практика безпеки (статичні ключі замість ролей).
- *Enable Cross Account collection in the X-Ray console* — вигадана опція, дистрактор.

Джерела: <https://aws.amazon.com/xray/faqs/> · <https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-configuration.html>

</details>

---

<a id="t02-q21"></a>
### T02-Q21
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **"Least privilege" + конкретна таблиця → customer managed policy з ARN.** → [детальніше](../../06-contradictions-and-traps.md#c05)

A team of Developers require read-only access to an Amazon DynamoDB table. The Developers have been added to a group. What should an administrator do to provide the team with access whilst following the principal of least privilege?

- ✅ Create a customer managed policy with read only access to DynamoDB and specify the ARN of the table for the “Resource” element. Attach the policy to the group
- ▫️ Assign the AmazonDynamoDBReadOnlyAccess AWS managed policy to the group
- ▫️ Create a customer managed policy with read/write access to DynamoDB for all resources. Attach the policy to the group
- ▫️ Assign the AWSLambdaDynamoDBExecutionRole AWS managed policy to the group

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a customer managed policy with read only access to DynamoDB and specify the ARN of the table for the "Resource" element. Attach the policy to the group»**

Потрібен лише read-only доступ до **конкретної** таблиці. AWS managed policy дає доступ до всіх таблиць акаунта, що порушує least privilege. Тому створюють customer managed policy з read-only діями та ARN таблиці в Resource, наприклад `arn:aws:dynamodb:us-west-1:515148227241:table/exampletable`.

**❌ Неправильні варіанти:**
- *AmazonDynamoDBReadOnlyAccess* — read-only до всіх таблиць акаунта.
- *AWSLambdaDynamoDBExecutionRole* — політика для Lambda (читання DynamoDB Streams), не для людей.
- *Customer managed policy with read/write access for all resources* — потрібне лише читання.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t02-q31"></a>
### T02-Q31
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: повна потужність + існуючі інстанси → Rolling with additional batch.** → [детальніше](../../06-contradictions-and-traps.md#c07)

An application deployed on AWS Elastic Beanstalk experienced increased error rates during deployments of new application versions, resulting in service degradation for users. The Development team believes that this is because of the reduction in capacity during the deployment steps. The team would like to change the deployment policy configuration of the environment to an option that maintains full capacity during deployment while using the existing instances.

Which deployment policy will meet these requirements while using the existing instances?

- ✅ Rolling with additional batch
- ▫️ All at once
- ▫️ Rolling
- ▫️ Immutable

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Rolling with additional batch»**

Політики деплою Elastic Beanstalk:
- **All at once** — нова версія одразу на всіх інстансах;
- **Rolling** — по кілька інстансів за раз, наступний батч після того, як попередній став healthy; один батч щоразу недоступний;
- **Rolling with additional batch** — як Rolling, але спершу запускає додатковий батч, тож доступність повна;
- **Immutable** — нові інстанси в новій ASG; трафік перемикається, коли вони healthy; zero downtime;
- **Blue/Green** — окреме нове середовище, zero downtime.

Rolling with additional batch зберігає потужність і оновлює **наявні** інстанси, тож саме він відповідає вимогам.

**❌ Неправильні варіанти:**
- *Rolling* — без додаткового батча потужність під час оновлення падає.
- *All at once* — повний простій.
- *Immutable* — оновлення ставиться на нові інстанси, а не на наявні.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t03-q45"></a>
### T03-Q45
*домен: офіційна мітка*

You are a manager for a tech company that has just hired a team of developers to work on the company's AWS infrastructure. All the developers are reporting to you that when using the AWS CLI to execute commands it fails with the following exception: You are not authorized to perform this operation. Encoded authorization failure message: 6h34GtpmGjJJUm946eDVBfzWQJk6z5GePbbGDs9Z2T8xZj9EZtEduSnTbmrR7pMqpJrVYJCew2m8YBZQf4HRWEtrpncANrZMsnzk.

Which of the following actions will help developers decode the message?

- ▫️ AWS Cognito Decoder
- ▫️ Use KMS decode-authorization-message
- ▫️ AWS IAM decode-authorization-message
- ✅ AWS STS decode-authorization-message

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS STS decode-authorization-message»**

`decode-authorization-message` розшифровує додаткові відомості про статус авторизації із закодованого повідомлення у відповіді AWS. Якщо дія не дозволена, запит повертає `Client.UnauthorizedOperation` (HTTP 403). Повідомлення закодоване, бо деталі можуть бути конфіденційними. Щоб його розшифрувати, потрібен дозвіл `sts:DecodeAuthorizationMessage`.

**❌ Неправильні варіанти:** *IAM decode-authorization-message*, *KMS decode-authorization-message*, *AWS Cognito Decoder* — таких команд немає.

</details>

---

<a id="t03-q47"></a>
### T03-Q47
*домен: офіційна мітка*

You have a web application hosted on EC2 that makes GET and PUT requests for objects stored in Amazon Simple Storage Service (S3) using the SDK for PHP. As the security team completed the final review of your application for vulnerabilities, they noticed that your application uses hardcoded IAM access key and secret access key to gain access to AWS services. They recommend you leverage a more secure setup, which should use temporary credentials if possible.

Which of the following options can be used to address the given use-case?

- ✅ Use an IAM Instance Role
- ▫️ Use environment variables
- ▫️ Hardcode the credentials in the application code
- ▫️ Use the SSM parameter store

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an IAM Instance Role»**

Instance profile — контейнер для IAM-ролі, через який роль передається EC2-інстансу при запуску. AWS SDK отримує тимчасові облікові дані ролі через сервіс метаданих EC2. Це найбезпечніший і найпоширеніший спосіб для застосунків на EC2.

**❌ Неправильні варіанти:**
- *Use environment variables* (`AWS_ACCESS_KEY_ID` тощо) — довгострокові ключі на кожному інстансі, які доведеться змінювати вручну; роль дає тимчасові.
- *Hardcode the credentials* — працює, але небезпечно.
- *SSM Parameter Store* — щоб звернутися до нього через SDK, все одно потрібні облікові дані.

</details>

---

<a id="t03-q57"></a>
### T03-Q57
*домен: офіційна мітка*

An Amazon Simple Queue Service (SQS) has to be configured between two AWS accounts for shared access to the queue. AWS account A has the SQS queue in its account and AWS account B has to be given access to this queue.

Which of the following options need to be combined to allow this cross-account access? (Select three)

- ✅ The account A administrator attaches a trust policy to the role that identifies account B as the principal who can assume the role
- ✅ The account A administrator creates an IAM role and attaches a permissions policy
- ✅ The account B administrator delegates the permission to assume the role to any users in account B
- ▫️ The account B administrator creates an IAM role and attaches a trust policy to the role with account B as the principal
- ▫️ The account A administrator delegates the permission to assume the role to any users in account A
- ▫️ The account A administrator attaches a trust policy to the role that identifies account B as the AWS service principal who can assume the role

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «The account A administrator creates an IAM role and attaches a permissions policy», «The account A administrator attaches a trust policy to the role that identifies account B as the principal who can assume the role», «The account B administrator delegates the permission to assume the role to any users in account B»**

Крос-акаунтний доступ надають через IAM-роль:
1. Адміністратор акаунта A створює роль із permissions policy на ресурси акаунта A.
2. Адміністратор акаунта A додає до ролі trust policy, де principal — акаунт B.
3. Адміністратор акаунта B дозволяє своїм користувачам припускати цю роль. Тоді користувачі B можуть працювати з чергами в акаунті A.

**❌ Неправильні варіанти:**
- *The account B administrator creates an IAM role…* — роль створюють в акаунті A, де ресурс.
- *The account A administrator delegates … to users in account A* — доступ потрібен користувачам акаунта B.
- *…account B as the AWS service principal* — service principal вказують для сервісів AWS, а тут інший акаунт.

</details>

---

<a id="t03-q58"></a>
### T03-Q58
*домен: офіційна мітка*

> ⚠️ **Тут AWS managed ReadOnly — бо питання про роль vs ключі, не про конкретну таблицю.** → [детальніше](../../06-contradictions-and-traps.md#c05)

An IT company has a web application running on Amazon EC2 instances that needs read-only access to an Amazon DynamoDB table.

As a Developer Associate, what is the best-practice solution you would recommend to accomplish this task?

- ▫️ Run application code with AWS account root user credentials to ensure full access to all AWS services
- ▫️ Create a new IAM user with access keys. Attach an inline policy to the IAM user with read-only access to DynamoDB. Place the keys in the code. For security, redeploy the code whenever the keys rotate
- ✅ Create an IAM role with an AmazonDynamoDBReadOnlyAccess policy and apply it to the EC2 instance profile
- ▫️ Create an IAM user with Administrator access and configure AWS credentials for this user on the given EC2 instance

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an IAM role with an AmazonDynamoDBReadOnlyAccess policy and apply it to the EC2 instance profile»**

Найкраща практика безпеки — не створювати IAM-користувача для застосунку і не вбудовувати його облікові дані. Натомість IAM-роль прикріплюють до інстансу, і застосунок отримує тимчасові облікові дані з правами з політик ролі. Тут — роль з `AmazonDynamoDBReadOnlyAccess` через instance profile.

**❌ Неправильні варіанти:**
- *IAM user with access keys in the code* — небезпечно.
- *IAM user with Administrator access on the instance* — ще небезпечніше, і порушує least privilege.
- *Root user credentials* — найгірший варіант.

</details>

---

<a id="t04-q16"></a>
### T04-Q16
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has lost their access key ID and secret access key for programmatic access. What should the Developer do?

- ✅ Disable and delete the user's access key and generate a new set
- ▫️ Contact AWS support and request a password reset
- ▫️ Generate a new key pair from the EC2 management console
- ▫️ Reset the AWS account access keys

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Disable and delete the user's access key and generate a new set»**

Access key має дві частини:
- **access key ID** — не секрет, його видно в консолі IAM;
- **secret access key** — видається лише при створенні і, як пароль, потім не відновлюється.

Загубив secret key — створюй нову пару. Якщо вже досягнуто максимуму ключів (2), спершу видали стару.

**❌ Неправильні варіанти:**
- *Contact AWS support for a password reset* — пароль для консолі, а не для програмного доступу.
- *Generate a new key pair from the EC2 console* — key pair EC2 — для SSH до інстансів.
- *Reset the AWS account access keys* — це ключі root-акаунта, а не IAM-користувача.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_general.html#troubleshoot_general_access-keys>

</details>

---

<a id="t04-q21"></a>
### T04-Q21
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has hired a team of remote Developers. The Developers need to work programmatically with AWS resources from their laptop computers.

Which security components MUST the Developers use to authenticate? (Select TWO.)

- ✅ Access key ID
- ✅ Secret access key
- ▫️ MFA device
- ▫️ Console password
- ▫️ IAM user ID

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Access key ID» та «Secret access key»**

Access keys складаються з access key ID (наприклад, `AKIAIOSFODNN7EXAMPLE`) і secret access key. Ними підписують програмні запити до AWS через CLI, SDK чи API. Розробникам на ноутбуках для програмного доступу потрібні саме вони.

**❌ Неправильні варіанти:**
- *Console password* та *IAM user ID* — для входу в консоль.
- *MFA device* — для програмних запитів не обов'язковий (але може додати безпеки).

Джерела: <https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html>

</details>

---

<a id="t04-q25"></a>
### T04-Q25
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is reviewing their security practices. According to AWS best practice, how should access keys be managed to improve security? (Select TWO.)

- ✅ Delete all access keys for the root account IAM user
- ✅ Use different access keys for different applications
- ▫️ Rotate access keys daily
- ▫️ Use the same access key in all applications for consistency
- ▫️ Embed access keys directly into code

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Delete all access keys for the root account IAM user» та «Use different access keys for different applications»**

Access key (ID + secret) підтверджує твою ідентичність і ідентичність застосунків при програмному доступі. Хто має твій ключ, той має і твої права. Як захищати ключі:
- видалити (або не створювати) ключі root-акаунта;
- використовувати тимчасові облікові дані (IAM-ролі) замість довгострокових ключів;
- не вбудовувати ключі в код;
- різні ключі для різних застосунків;
- періодично ротувати ключі;
- видаляти невикористані;
- вмикати MFA для найчутливіших операцій.

**❌ Неправильні варіанти:**
- *Embed access keys directly into code* — саме цього слід уникати.
- *Rotate access keys daily* — важко керувати; AWS рекомендує ротувати «періодично», а не щодня.
- *Use the same access key in all applications* — навпаки, для кожного застосунку свій ключ.

Джерела: <https://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html#iam-user-access-keys>

</details>

---

<a id="t04-q28"></a>
### T04-Q28
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

> ⚠️ **Тут AWS managed ReadOnly на ролі — ключ "IAM role", а не "least privilege".** → [детальніше](../../06-contradictions-and-traps.md#c05)

A Developer has code running on Amazon EC2 instances that needs read-only access to an Amazon DynamoDB table.

What is the MOST secure approach the Developer should take to accomplish this task?

- ▫️ Use an IAM role with Administrator access applied to the EC2 instance
- ✅ Use an IAM role with an AmazonDynamoDBReadOnlyAccess policy applied to the EC2 instances
- ▫️ Create a user access key for each EC2 instance with read-only access to DynamoDB. Place the keys in the code. Redeploy the code as keys rotate
- ▫️ Run all code with only AWS account root user access keys to ensure maximum access to services

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an IAM role with an AmazonDynamoDBReadOnlyAccess policy applied to the EC2 instances»**

За принципом least privilege застосунок отримує лише потрібні права — тут лише читання DynamoDB. Роль з `AmazonDynamoDBReadOnlyAccess` прикріплюють до інстансу через консоль або програмно (instance profile), і інстанс отримує тимчасові облікові дані з read-only доступом.

**❌ Неправильні варіанти:**
- *User access key for each instance in the code* — ключі в коді менш безпечні за роль.
- *Root user access keys* — вкрай небезпечно: повні права на весь акаунт, ще й у коді.
- *IAM role with Administrator access* — порушує least privilege.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t04-q49"></a>
### T04-Q49
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Development team are developing a micro-services application that will use Docker containers on Amazon ECS. There will be 6 distinct services included in the architecture. Each service requires specific permissions to various AWS services.

What is the MOST secure way to grant the services the necessary permissions?

- ▫️ Create a new Identity and Access Management (IAM) instance profile containing the required permissions for the various ECS services, then associate that instance role with the underlying EC2 instances
- ▫️ Create a single IAM policy and use principal statements referencing the ECS tasks and assigning the required permissions, then apply the policy to the ECS service
- ✅ Create six separate IAM roles, each containing the required permissions for the associated ECS service, then configure each ECS task definition to reference the associated IAM role
- ▫️ Create six separate IAM roles, each containing the required permissions for the associated ECS service, then create an IAM group and configure the ECS cluster to reference that group

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create six separate IAM roles, each containing the required permissions for the associated ECS service, then configure each ECS task definition to reference the associated IAM role»**

IAM roles for tasks дають змогу задати роль для контейнерів задачі, як instance profile для EC2. Замість розповсюдження ключів чи використання ролі EC2-інстансу роль прив'язують до task definition (або `RunTask`), і застосунки в контейнерах викликають сервіси AWS через SDK/CLI з цими правами. Найбезпечніше — окрема роль з мінімальними правами для кожного з шести сервісів, прив'язана до відповідного task definition.

**❌ Неправильні варіанти:**
- *Six roles + an IAM group referenced by the ECS cluster* — роль прив'язують до task definition, а не до кластера; групи — для користувачів.
- *One instance profile for the EC2 instances* — усі сервіси на інстансі отримали б однакові широкі права.
- *Single IAM policy applied to the ECS service* — політики на ECS service контролюють керування ресурсами ECS, а не доступ задач до інших сервісів.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html>

</details>

---

<a id="t04-q65"></a>
### T04-Q65
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

> ⚠️ **Сумнівно з т. з. least privilege: досить sqs:SendMessage на цю чергу.** → [детальніше](../../07-outdated-and-wrong-answers.md#d04)

A Developer created an AWS Lambda function and then attempted to add an on failure destination but received the following error:

The function's execution role does not have permissions to call SendMessage on arn:aws:sqs:us-east-1:515148212435:FailureDestination

How can the Developer resolve this issue MOST securely?

- ▫️ Add the Lambda function to a group with administrative privileges
- ▫️ Add the AWSLambdaSQSQueueExecutionRole AWS managed policy to the function’s execution role
- ▫️ Add a permissions policy to the SQS queue allowing the SendMessage action and specify the AWS account number
- ✅ Create a customer managed policy with all read/write permissions to SQS and attach the policy to the function’s execution role

<details><summary>Пояснення</summary>

**✅ Правильна відповідь (за ключем банку): «Create a customer managed policy with all read/write permissions to SQS and attach the policy to the function's execution role»**

Функції потрібен дозвіл `sqs:SendMessage` на чергу; права надають через execution role. AWS managed `AWSLambdaSQSQueueExecutionRole` не містить SendMessage (вона для читання з черги), тож треба створити customer managed policy.

⚠️ **Сумнівно з погляду least privilege:** досить `sqs:SendMessage` на ARN конкретної черги, а не всі права read/write. Див. файл 07, розділ d04.

**❌ Неправильні варіанти:**
- *AWSLambdaSQSQueueExecutionRole* — не дає SendMessage.
- *Permissions policy on the SQS queue allowing SendMessage for the account number* — дозволить писати в чергу будь-якому ресурсу акаунта, менш безпечно.
- *Add the Lambda function to an admin group* — Lambda не можна додати в IAM-групу.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html>

</details>

---

<a id="t05-q10"></a>
### T05-Q10
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer plan to deploy an application on Amazon ECS that uses the AWS SDK to make API calls to Amazon DynamoDB. In the development environment the application was configured with access keys. The application is now ready for deployment to a production cluster.

How should the developer configure the application to securely authenticate to AWS services?

- ▫️ Add environment variables pointing to new access key credentials.
- ▫️ Configure the credentials file with a new access key/secret access key.
- ▫️ Add the necessary AWS service permissions to an ECS instance profile.
- ✅ Configure an ECS task IAM role for the application to use.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure an ECS task IAM role for the application to use»**

Задачі ECS можуть мати IAM-роль, права якої отримують контейнери задачі. Переваги:
- **ізоляція облікових даних** — контейнер отримує облікові дані лише ролі зі свого task definition;
- **авторизація** — чужі контейнери не мають доступу до облікових даних ролі;
- **аудит** — CloudTrail логує доступ, і в сесії є taskArn, тож видно, яка задача використовує яку роль.

**❌ Неправильні варіанти:**
- *Permissions in an ECS instance profile* — права instance profile доступні всім задачам на інстансі; AWS радить їх мінімізувати.
- *Credentials file with a new access key* — статичні ключі небезпечні; краще ролі з тимчасовими обліковими даними через STS.
- *Environment variables pointing to access keys* — те саме.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html>

</details>

---

<a id="t05-q38"></a>
### T05-Q38
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to create an instance profile for an Amazon EC2 instance using the AWS CLI. How can this be achieved? (Select THREE.)

- ✅ Run the aws ec2 associate-instance-profile command
- ▫️ Run the AddRoleToInstanceProfile API
- ✅ Run the aws iam create-instance-profile command
- ▫️ Run the CreateInstanceProfile API
- ✅ Run the aws iam add-role-to-instance-profile command
- ▫️ Run the AssignInstanceProfile API

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Run the aws ec2 associate-instance-profile command» + «Run the aws iam create-instance-profile command» + «Run the aws iam add-role-to-instance-profile command»**

Щоб призначити роль EC2 через CLI:
1. Створити instance profile.
2. Додати до нього роль.
3. Прив'язати instance profile до інстансу.

```
aws iam create-instance-profile --instance-profile-name EXAMPLEPROFILENAME
aws iam add-role-to-instance-profile --instance-profile-name EXAMPLEPROFILENAME --role-name EXAMPLEROLENAME
aws ec2 associate-iam-instance-profile --iam-instance-profile Name=EXAMPLEPROFILENAME --instance-id i-012345678910abcde
```

⚠️ Реальна команда — `associate-iam-instance-profile`; у варіанті відповіді назва скорочена.

**❌ Неправильні варіанти:**
- *CreateInstanceProfile API* — це API-дія, а не команда CLI.
- *AddRoleToInstanceProfile API* — це API-дія, а не команда CLI.
- *AssignInstanceProfile API* — це API-дія, а не команда CLI.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/attach-replace-ec2-instance-profile/>

</details>

---

<a id="t06-q12"></a>
### T06-Q12
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has an application that provides access to objects in Amazon S3 based on the type of user. The user types are registered user and guest user. The company has 30,000 users. Information is read from an S3 bucket depending on the user type.

Which approaches are recommended to provide access to both user types MOST efficiently? (Select TWO.)

- ✅ Use the AWS IAM service and let the application assume different roles depending on the type of user.
- ✅ Use Amazon Cognito to provide access using authenticated and unauthenticated roles.
- ▫️ Store separate access keys in the application code for registered users and guest users to provide access to the objects.
- ▫️ Create a new IAM user for each user and grant access to the S3 objects.
- ▫️ Use S3 bucket policies to restrict read access to specific IAM users.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the AWS IAM service and let the application assume different roles depending on the type of user.» + «Use Amazon Cognito to provide access using authenticated and unauthenticated roles.»**

Cognito identity pool підтримує два типи ідентичностей:
- **authenticated** — користувачі, які увійшли через будь-який підтримуваний identity provider;
- **unauthenticated** — зазвичай гості.

З IAM найбезпечніше розділити доступ окремими ролями. Застосунок приймає роль залежно від типу користувача, і кожна роль має свої дозволи для зареєстрованих і гостей.

**❌ Неправильні варіанти:**
- *Separate access keys in code* — дуже небезпечно; ключі в коді не зберігають, використовують ролі.
- *IAM user per user* — величезна кількість користувачів, неефективно.
- *Bucket policies per IAM user* — дуже складно й вимагає постійного оновлення політики.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/identity-pools.html> · <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html>

</details>

---

<a id="t06-q33"></a>
### T06-Q33
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A serverless application uses an IAM role to authenticate and authorize access to an Amazon DynamoDB table. A Developer is troubleshooting access issues affecting the application. The Developer has access to the IAM role that the application is using.

Which of the following commands will help the Developer to test the role permissions using the AWS CLI?

- ▫️ aws dynamodb describe-endpoints
- ▫️ aws sts get-session-token
- ▫️ aws iam get-role-policy
- ✅ aws sts assume-role

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «aws sts assume-role»**

Команда `aws sts assume-role` дозволяє прийняти роль застосунку й отримати тимчасові облікові дані. З ними розробник відтворює доступ застосунку і з'ясовує проблеми з дозволами.

**❌ Неправильні варіанти:**
- *get-session-token* — тимчасові облікові дані для IAM-користувача чи акаунта, наприклад для MFA; ролі він не приймає.
- *iam get-role-policy* — повертає inline-політику ролі, а не облікові дані.
- *dynamodb describe-endpoints* — повертає інформацію про регіональні endpoints.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/sts/assume-role.html>

</details>

---

<a id="t06-q48"></a>
### T06-Q48
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization has an account for each environment: Production, Testing, Development. A Developer with an IAM user in the Development account needs to launch resources in the Production and Testing accounts. What is the MOST efficient way to provide access

- ▫️ Create an IAM permissions policy in the Production and Testing accounts and reference the IAM user in the Development account
- ▫️ Create a separate IAM user in each account and have the Developer login separately to each account
- ✅ Create a role with the required permissions in the Production and Testing accounts and have the Developer assume that role
- ▫️ Create an IAM group in the Production and Testing accounts and add the Developer’s user from the Development account to the groups

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a role with the required permissions in the Production and Testing accounts and have the Developer assume that role»**

IAM-користувачам можна дозволити перемикатися на ролі у своєму акаунті або в інших ваших акаунтах — це cross-account access. Наприклад, користувач з акаунта Development приймає роль в акаунті Production і отримує доступ до бакета S3 там. Перемкнутися на роль можна через консоль або API/CLI. Це ефективніше, ніж заводити користувачу окремі облікові записи.

**❌ Неправильні варіанти:**
- *Separate IAM user in each account* — неефективно; cross-account доступ кращий.
- *IAM group with a user from another account* — до групи не можна додати IAM-користувача з іншого акаунта.
- *Permissions policy referencing the user* — identity-based політика в одному акаунті не може надати права користувачу з іншого акаунта.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html>

</details>

---

<a id="t06-q56"></a>
### T06-Q56
*домен: визначено автоматично (у джерелі: AWS Database)*

A development team is working with Amazon MemoryDB for Redis. The team lead wants to limit the default access of the service-linked role and implement a custom-managed policy instead.

What needs to be done prior to making this change?

- ▫️ The team lead would have to login as the root user to change the permission setting.
- ✅ The team lead would need to have permission to call `iam:createServiceLinkedRole`.
- ▫️ The team lead would edit the default policy named `AmazonMemoryDBFullAccess`.
- ▫️ The team lead would need permission from an AWS Technical Account Manager.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The team lead would need to have permission to call `iam:createServiceLinkedRole`.»**

Service-linked role прив'язана до сервісу і має наперед визначені дозволи. Стандартна політика для Amazon MemoryDB — `AmazonMemoryDBFullAccess`; вона вже містить дозвіл створити service-linked role, через яку сервіс створює ресурси й звертається до інших сервісів. Якщо замість неї використати власну політику, вона має дозволяти `iam:CreateServiceLinkedRole`, або роль треба створити заздалегідь.

**❌ Неправильні варіанти:**
- *Login as root* — тімлід може не мати root-доступу, і root використовують лише для задач, які потребують саме його. Потрібні права може видати адміністратор.
- *Permission from a TAM* — зміни IAM-політик і service-linked ролей не потребують Technical Account Manager.
- *Edit AmazonMemoryDBFullAccess* — AWS managed політики не редагуються; треба створити власну політику.

Джерела: <https://docs.aws.amazon.com/memorydb/latest/devguide/set-up.html>

</details>

---

<a id="t07-q03"></a>
### T07-Q03
*домен: офіційна мітка*

Two policies are attached to an IAM user. The first policy states that the user has explicitly been denied all access to EC2 instances. The second policy states that the user has been allowed permission for EC2:Describe action.

When the user tries to use 'Describe' action on an EC2 instance using the CLI, what will be the output?

- ▫️ The order of the policy matters. If policy 1 is before 2, then the user is denied access. If policy 2 is before 1, then the user is allowed access
- ▫️ The IAM user stands in an invalid state, because of conflicting policies
- ▫️ The user will get access because it has an explicit allow
- ✅ The user will be denied access because one of the policies has an explicit deny on it

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The user will be denied access because one of the policies has an explicit deny on it»**

Явний Deny завжди переважає будь-який Allow. Логіку оцінки політик описано тут: https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html

**❌ Неправильні варіанти:**
- *Invalid state* — політики з Allow і Deny цілком допустимі й оцінюються за правилами; користувач від них «невалідним» не стає.
- *Access due to explicit allow* — явний Deny переважає.
- *Order of policies matters* — порядок не важливий: якщо є і Allow, і Deny, перемагає Deny.

</details>

---

<a id="t07-q61"></a>
### T07-Q61
*домен: офіційна мітка*

A developer wants to securely store an access token that allows a transaction-processing application running on Amazon EC2 instances to authenticate and send a chat message (via the chat API) to the company's support team when an invalid transaction is detected. While minimizing management overhead, the chat API access token must be encrypted both at rest and in transit, and also be accessible from other AWS accounts.

What is the most efficient solution to address this scenario?

- ✅ Leverage AWS Secrets Manager with an AWS KMS customer-managed key to store the access token as a secret and configure a resource-based policy for the secret to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Secrets Manager. Fetch the token from Secrets Manager and then use the decrypted access token to send the message to the chat
- ▫️ Leverage SSE-KMS to store the access token as an encrypted object on S3 and configure a resource-based policy for the S3 bucket to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access the S3 object. Fetch the token from S3 and then use the decrypted access token to send the message to the chat
- ▫️ Leverage AWS Systems Manager Parameter Store with an AWS KMS customer-managed key to store the access token as a SecureString parameter and configure a resource-based policy for the parameter to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Parameter Store. Fetch the token from Parameter Store using the with decryption flag and then use the decrypted access token to send the message to the chat
- ▫️ Store AWS KMS encrypted access token in a DynamoDB table and configure a resource-based policy for the DynamoDB table to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access the DynamoDB table. Fetch the token from the Dynamodb table and then use the decrypted access token to send the message to the chat

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Leverage AWS Secrets Manager with an AWS KMS customer-managed key to store the access token as a secret and configure a resource-based policy for the secret to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Secrets Manager. Fetch the token from Secrets Manager and then use the decrypted access token to send the message to the chat»**

Secrets Manager шифрує й зберігає секрети, а застосунку повертає їх у відкритому вигляді. Він створений для облікових даних, які змінюються і які не можна тримати в коді чи відкритим текстом. Інші можливості:
- періодична ротація секретів популярних БД; нові версії завжди шифруються;
- кожна версія секрету шифрується унікальним data key під захистом KMS-ключа, який не покидає KMS відкритим;
- власні дозволи на KMS-ключ і аудит операцій.

Дозволи на секрет надають політиками на сам секрет або на identity. Тут:
1. Resource-based policy на секреті дозволяє доступ з інших акаунтів.
2. IAM-роль EC2 отримує дозволи на Secrets Manager.
3. Застосунок отримує токен і надсилає повідомлення в чат.

Для крос-акаунтного доступу потрібен саме customer managed ключ, бо його key policy теж має дозволяти інший акаунт.

**❌ Неправильні варіанти:**
- *Parameter Store + resource-based policy* — у класичній логіці тесту Parameter Store не підтримує resource-based policies. Є лише parameter policies в advanced tier: expiration, TTL. ℹ️ Зараз advanced-параметрами можна ділитися між акаунтами через AWS RAM, але на іспиті для крос-акаунтного секрету правильний Secrets Manager.
- *DynamoDB + resource-based policy* — в оригіналі сказано, що DynamoDB не підтримує resource-based policies. Це застаріло: підтримка є з 2024 року. Але зберігати облікові дані в БД все одно погана практика.
- *SSE-KMS object in S3* — зберігати облікові дані в об'єктному сховищі — погана практика.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_examples.html> · <https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-policies.html>

</details>

---

<a id="t08-q26"></a>
### T08-Q26
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A development team are creating a mobile application that customers will use to receive notifications and special offers. Users will not be required to log in.

What is the MOST efficient method to grant users access to AWS resources?

- ✅ Use Amazon Cognito to associate unauthenticated users with an IAM role that has limited access to resources
- ▫️ Use Amazon Cognito Federated Identities and setup authentication using a Cognito User Pool
- ▫️ Embed access keys in the application that have limited access to resources
- ▫️ Use an IAM SAML 2.0 identity provider to establish trust

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon Cognito to associate unauthenticated users with an IAM role that has limited access to resources»**

Identity pools підтримують unauthenticated identities: користувач без входу отримує унікальний ідентифікатор і облікові дані AWS. Налаштувати це просто, а IAM-роль для неавтентифікованих користувачів дає лише дозволений їм доступ. Найефективніший і найбезпечніший спосіб.

**❌ Неправильні варіанти:**
- *IAM SAML 2.0 provider* — призначений для користувачів, автентифікованих деінде, наприклад в Active Directory; а тут потрібен доступ без автентифікації.
- *Federated Identities + User Pool authentication* — це автентифікований доступ, а потрібен неавтентифікований.
- *Embedded access keys* — ключі в коді — погана практика.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/identity-pools.html>

</details>

---

<a id="t08-q36"></a>
### T08-Q36
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization has a new AWS account and is setting up IAM users and policies. According to AWS best practices, which of the following strategies should be followed? (Select TWO.)

- ▫️ Always use customer managed policies instead of AWS managed policies
- ✅ Create standalone policies instead of using inline policies
- ▫️ Use user accounts to delegate permissions
- ✅ Use groups to assign permissions to users
- ▫️ Create user accounts that can be shared for efficiency

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create standalone policies instead of using inline policies» + «Use groups to assign permissions to users»**

Тут застосовні дві найкращі практики IAM:
- **Групи для дозволів** — політики прикріплюють до груп, а користувачів додають у групи.
- **Окремі політики замість inline** (customer managed policies) — їх можна повторно використовувати для кількох груп і користувачів, тоді як inline-політика прив'язана до однієї сутності.

**❌ Неправильні варіанти:**
- *User accounts to delegate* — для делегування використовують ролі.
- *Shared user accounts* — облікові записи не діляться; кожен має свій.
- *Always customer managed instead of AWS managed* — AWS радить починати саме з AWS managed політик.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html>

</details>

---

<a id="t08-q45"></a>
### T08-Q45
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer received the following error when attempting to launch an Amazon EC2 instance using the AWS CLI.

An error occurred (UnauthorizedOperation) when calling the RunInstances operation: You are not authorized to perform this operation. Encoded authorization failure message: VNVaHFdCohROkbyT_rIXoRyNTp7vXFJCqnGiwPuyKnsSVf-WSSGK_06H3vKnrkUa3qx5D40hqj9HEG8kznr04Acmi6lvc8m51tfqtsomFSDylK15x96ZrxMW7MjDJLrMkM0BasPvy8ixo1wi6X2b0C-J1ThyWU9IcrGd7WbaRDOiGbBhJtKs1z01WSn2rVa5_7sr5PwEK-ARrC9y5Pl54pmeF6wh7QhSv2pFO0y39WVBajL2GmByFmQ4p8s-6Lcgxy23b4NJdJwWOF4QGxK9HcKof1VTVZ2oIpsI-dH6_0t2DI0BTwaIgmaT7ldontI1p7OGz-3wPgXm67x2NVNgaK63zPxjYNbpl32QuXLKUKNlB9DdkSdoLvsuFIvf-lQOXLPHnZKCWMqrkI87eqKHYpYKyV5c11TIZTAJ3MntTGO_TJ4U9ySYvTzU2LgswYOtKF_O76-13fryGG5dhgOW5NxwCWBj6WT2NSJvqOeLykAFjR_ET4lM6Dl1XYfQITWCqIzlvlQdLmHJ1jqjp4gW56VcQCdqozLv2UAg8IdrZIXd0OJ047RQcvvN1IyZN0ElL7dR6RzAAQrftoKMRhZQng6THZs8PZM6wep6-yInzwfg8J5_FW6G_PwYqO-4VunVtJSTzM_F_8kojGlRmzqy7eCk5or__bIisUoslw

What action should the Developer perform to make this error more human-readable?

- ▫️ Use an open source decoding library to decode the message
- ▫️ Make a call to AWS KMS to decode the message
- ▫️ Use the AWS IAM decode-authorization-message API to decode this message
- ✅ Use the AWS STS decode-authorization-message API to decode the message

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the AWS STS decode-authorization-message API to decode the message»**

`aws sts decode-authorization-message` розшифровує закодоване повідомлення про авторизацію з відповіді AWS у читабельний JSON. Там видно:
- `allowed`, `explicitDeny`;
- principal, тобто хто викликав;
- action, наприклад `ec2:RunInstances`;
- resource;
- усі ключі умов: тип інстансу, регіон, AZ тощо.

Для цього виклику потрібен дозвіл `sts:DecodeAuthorizationMessage`.

**❌ Неправильні варіанти:**
- *KMS* — повідомлення закодоване, а не зашифроване KMS.
- *Open source decoding library* — для цього є власний API STS.
- *IAM decode-authorization-message* — цей API належить STS, а не IAM.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html>

</details>

---

<a id="t08-q47"></a>
### T08-Q47
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A software firm is introducing a multimedia application that allows guest users to sample content before deciding to fully register. The company requires a mechanism that can identify users who have already created an account and to monitor the quantity of guest users who ultimately register.

What two actions would best satisfy these needs? (Select TWO.)

- ▫️ Use AWS Lambda to track the transitions from guest to full accounts.
- ▫️ Use AWS Glue to oversee user registration and account conversion.
- ✅ Use Amazon Cognito User Pools for managing user registration and authentication.
- ✅ Implement AWS IAM roles to provide distinct permissions for guest users and registered users.
- ▫️ Deploy Amazon S3 for managing user data and monitoring account conversions.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon Cognito User Pools for managing user registration and authentication.» + «Implement AWS IAM roles to provide distinct permissions for guest users and registered users.»**

- **Cognito User Pools** — каталог користувачів з реєстрацією, автентифікацією і відновленням облікового запису. З ним видно, хто створив обліковий запис.
- **IAM roles** розділяють дозволи гостей і зареєстрованих користувачів, наприклад unauthenticated і authenticated ролі в identity pool. Самі переходи з гостя в зареєстрованого вони не відстежують, але розмежовують стани.

**❌ Неправильні варіанти:**
- *AWS Glue* — ETL для аналітики, а не реєстрація користувачів.
- *S3* — сховище, не система реєстрації.
- *Lambda to track transitions* — це обчислювальний сервіс, не система керування користувачами.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html>

</details>

---

<a id="t08-q52"></a>
### T08-Q52
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is trying to make API calls using AWS SDK. The IAM user credentials used by the application require multi-factor authentication for all API calls.

Which method should the Developer use to access the multi-factor authentication protected API?

- ▫️ GetFederationToken
- ✅ GetSessionToken
- ▫️ DecodeAuthorizationMessage
- ▫️ GetCallerIdentity

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «GetSessionToken»**

`GetSessionToken` повертає тимчасові облікові дані для акаунта чи IAM-користувача: access key ID, secret access key і session token. Зазвичай його використовують, щоб захистити програмні виклики певних API за допомогою MFA. Розробник викликає `GetSessionToken` з MFA-пристроєм і робить захищені виклики через SDK.

**❌ Неправильні варіанти:**
- *GetFederationToken* — тимчасові облікові дані для федеративних користувачів.
- *GetCallerIdentity* — показує, від чийого імені зроблено виклик.
- *DecodeAuthorizationMessage* — розшифровує закодоване повідомлення про помилку авторизації.

Джерела: <https://docs.aws.amazon.com/STS/latest/APIReference/API_GetSessionToken.html>

</details>

---

<a id="t08-q54"></a>
### T08-Q54
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A team of Developers require access to an AWS account that is a member account in AWS Organizations. The administrator of the master account needs to restrict the AWS services, resources, and API actions that can be accessed by the users in the account.

What should the administrator create?

- ▫️ A Tag Policy
- ✅ A Service Control Policy (SCP)
- ▫️ A Consolidated Billing account
- ▫️ An Organizational Unit

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A Service Control Policy (SCP)»**

SCP задають максимальні дозволи для акаунтів-учасників в AWS Organizations. Вони обмежують, до яких сервісів, ресурсів і API мають доступ користувачі та ролі, з умовами. Наприклад, SCP може дозволити лише певні типи інстансів EC2. Обмеження SCP діють навіть на адміністраторів акаунтів-учасників: якщо SCP блокує дію, явний дозвіл в IAM не допоможе.

**❌ Неправильні варіанти:**
- *Tag Policy* — забезпечує однакові теги, включно з регістром ключів і значень.
- *Organizational Unit* — групує акаунти для адміністрування.
- *Consolidated Billing* — це про оплату, а не про контроль доступу.

Джерела: <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html> · <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_about-scps.html>

</details>

---

<a id="t08-q57"></a>
### T08-Q57
*домен: визначено автоматично (у джерелі: AWS Database)*

An Amazon DynamoDB table will store authentication credentials for a mobile app. The table must be secured so only a small group of Developers are able to access it.

How can table access be secured according to this requirement and following AWS best practice?

- ▫️ Create a shared user account and attach a permissions policy granting access to the table. Instruct the Developer’s to login with the user account
- ▫️ Create an AWS KMS resource-based policy to a CMK and grant the developer’s user accounts the permissions to decrypt data in the table using the CMK
- ✅ Attach a permissions policy to an IAM group containing the Developer’s IAM user accounts that grants access to the table
- ▫️ Attach a resource-based policy to the table and add an IAM group containing the Developer’s IAM user accounts as a Principal in the policy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Attach a permissions policy to an IAM group containing the Developer’s IAM user accounts that grants access to the table»**

Найкраща практика: створити політику доступу до таблиці і прикріпити її до IAM-групи з обліковими записами розробників. Усі учасники групи отримають доступ.

⚠️ В оригіналі сказано, що DynamoDB підтримує лише identity-based політики. Це застаріло: з 2024 року DynamoDB підтримує resource-based policies. Проте для доступу користувачів свого акаунта найкраща відповідь однаково — політика на групу.

**❌ Неправильні варіанти:**
- *Resource-based policy with IAM group as Principal* — IAM-групу не можна вказати як Principal у жодній політиці.
- *KMS resource-based policy* — потрібен доступ до таблиці, а не до розшифрування.
- *Shared user account* — облікові записи не діляться.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t08-q58"></a>
### T08-Q58
*домен: визначено автоматично (у джерелі: AWS Compute)*

An application running on a fleet of EC2 instances use the AWS SDK for Java to copy files into several AWS buckets using access keys stored in environment variables. A Developer has modified the instances to use an assumed IAM role with a more restrictive policy that allows access to only one bucket.

However, after applying the change the Developer logs into one of the instances and is still able to write to all buckets. What is the MOST likely explanation for this situation?

- ✅ The AWS credential provider looks for instance profile credentials last
- ▫️ The AWS CLI is corrupt and needs to be reinstalled
- ▫️ An IAM inline policy is being used on the IAM role
- ▫️ An IAM managed policy is being used on the IAM role

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The AWS credential provider looks for instance profile credentials last»**

Якщо клієнта сервісу створити без облікових даних, AWS SDK for Java шукає їх через default credential provider chain у такому порядку:
1. **Змінні середовища** `AWS_ACCESS_KEY_ID` і `AWS_SECRET_ACCESS_KEY`.
2. **Java system properties** `aws.accessKeyId` і `aws.secretKey`.
3. **Файл профілів** `~/.aws/credentials`.
4. **Облікові дані контейнера ECS** — якщо задано `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI`.
5. **Instance profile** — через EC2 metadata service.

Змінні середовища перевіряються раніше за роль інстансу. Тому SDK бере access keys зі змінних середовища, і з'являється доступ до додаткових бакетів. В інших SDK і CLI ланцюжок дуже схожий.

**❌ Неправильні варіанти:**
- *Inline policy on the role* — роль узагалі не використовується, бо спершу знайдено ключі в змінних середовища.
- *Managed policy on the role* — те саме: ключі зі змінних середовища мають пріоритет над роллю.
- *CLI is corrupt* — є логічне пояснення, баг тут ні до чого.

Джерела: <https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html> · <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html>

</details>

---

<a id="t10-q07"></a>
### T10-Q07
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A developer has a user account in the Development AWS account. He has been asked to modify resources in a Production AWS account. What is the MOST secure way to provide temporary access to the developer?

- ▫️ Add the user to a group in the second account that has a role attached granting the necessary permissions
- ▫️ Use AWS KMS to generate cross-account customer master keys and use those get short-lived credentials
- ✅ Create a cross-account access role, and use sts:AssumeRole API to get short-lived credentials
- ▫️ Generate an access key on the second account using the root account and share the access keys with the developer for API access

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a cross-account access role, and use sts:AssumeRole API to get short-lived credentials»**

У Production створюють роль із потрібним доступом, яку може прийняти група в Development. Розробника додають у цю групу. Він приймає роль через `sts:AssumeRole` і отримує короткострокові облікові дані. Це найбезпечніший спосіб.

**❌ Неправильні варіанти:**
- *Root access keys shared* — дуже небезпечно: ключами не діляться, особливо root-ключами.
- *Add user to a group in the second account* — не можна додати користувача до групи в іншому акаунті.
- *KMS cross-account CMKs* — KMS-ключі шифрують дані, а не видають облікові дані.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html>

</details>

---

<a id="t10-q22"></a>
### T10-Q22
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer needs to restrict all users and roles from using a list of API actions within a member account in AWS Organizations. The Developer needs to deny access to a few specific API actions.

What is the MOST efficient way to do this?

- ▫️ Create an allow list and specify the API actions to deny
- ▫️ Create an IAM policy that allows only the unrestricted API actions
- ✅ Create a deny list and specify the API actions to deny
- ▫️ Create an IAM policy that denies the API actions for all users and roles

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a deny list and specify the API actions to deny»**

SCP централізовано задають максимальні дозволи для всіх акаунтів організації. Два підходи:
- **Deny list** — усе дозволено за замовчуванням, а ви забороняєте конкретні сервіси й дії;
- **Allow list** — усе заборонено за замовчуванням, а ви дозволяєте конкретні.

Обмежити треба лише кілька дій, тож найефективніше — deny list.

**❌ Неправильні варіанти:**
- *Allow list specifying actions to deny* — в allow list перелічують дозволені дії, а не заборонені.
- *IAM policy that denies* — ⚠️ в оригіналі сказано, що в IAM не можна створити deny-політику. Це неправда: явний `"Effect": "Deny"` в IAM є. Але IAM-політику довелося б чіпляти до кожного користувача й ролі, і вона не обмежує root акаунта-учасника. Тому потрібна SCP.
- *IAM policy allowing only unrestricted actions* — не обмежить root і вимагає призначення кожній сутності.

Джерела: <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html> · <https://docs.aws.amazon.com/organizations/latest/userguide/SCP_strategies.html>

</details>

---

<a id="t10-q27"></a>
### T10-Q27
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has noticed some suspicious activity in her AWS account and is concerned that the access keys associated with her IAM user account may have been compromised. What is the first thing the Developer do in should do in this situation?

- ▫️ Report the incident to AWS Support
- ▫️ Delete her IAM user account
- ▫️ Change her IAM User account password
- ✅ Delete the compromised access keys

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Delete the compromised access keys»**

Перший крок — зробити скомпрометовані access keys недійсними, тобто видалити їх або деактивувати. Далі:
- перевірити, чи не видавалися за ними тимчасові облікові дані, і відкликати їх;
- проаналізувати CloudTrail.

Сам обліковий запис і пароль не скомпрометовані, тож міняти пароль не перший крок, хоча загалом це рекомендовано.

**❌ Неправильні варіанти:**
- *Delete IAM user* — скомпрометовано лише ключі; досить видалити їх.
- *Report to AWS Support* — корисно, але не першим кроком.
- *Change password* — доказів компрометації пароля немає; це не перший крок.

Джерела: <https://aws.amazon.com/blogs/security/what-to-do-if-you-inadvertently-expose-an-aws-access-key/>

</details>

---

<a id="t10-q31"></a>
### T10-Q31
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An online retail platform uses the AWS SDK for Python (Boto3) on the frontend to handle user authentication through AWS Security Token Service (AWS STS). The platform stores its digital assets in an Amazon S3 bucket and delivers them using an Amazon CloudFront distribution, which uses the S3 bucket as its origin.

Currently, the application holds its role credentials in plaintext within a Python file in the application code. The platform developers are looking to improve security by creating a mechanism that enables the application to retrieve user credentials without embedding any credentials in the application code.

What solution would meet these requirements?

- ✅ Integrate a Lambda@Edge function with the CloudFront distribution. Trigger the function upon each viewer request. Give the execution role of the function the required permissions to interact with AWS STS. Shift all SDK calls from the frontend to this function.
- ▫️ Attach a Lambda@Edge function to the CloudFront distribution. Invoke this function with each viewer request. Transfer the credentials from the Python file into this function and move all SDK calls from the frontend into this function.
- ▫️ Configure a CloudFront function within the distribution. Activate the function on viewer request. Grant the function's execution role the necessary permissions to access AWS STS. Relocate all SDK calls from the frontend to this function.
- ▫️ Set up a CloudFront function on the distribution. Initiate this function for each viewer request. Shift the credentials from the Python file into this function and relocate all SDK calls from the frontend into the function.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Integrate a Lambda@Edge function with the CloudFront distribution. Trigger the function upon each viewer request. Give the execution role of the function the required permissions to interact with AWS STS. Shift all SDK calls from the frontend to this function.»**

Потрібно, щоб застосунок отримував облікові дані без зберігання їх у коді. Lambda@Edge з execution role отримує тимчасові облікові дані через STS і виконує SDK-виклики замість фронтенду. Ключів у коді немає, а код виконується ближче до користувача.

⚠️ Рішення нетипове, але серед варіантів єдине без захардкоджених ключів. Див. 07.

**❌ Неправильні варіанти:**
- *CloudFront Function + STS* — CloudFront Functions виконують лише легкі HTTP-маніпуляції (заголовки, редиректи, нормалізація ключа кешу) і не мають доступу до сервісів AWS.
- *Lambda@Edge with credentials moved in* — ключі просто переносяться в іншу функцію, тобто лишаються захардкодженими.
- *CloudFront Function with credentials* — захардкоджені ключі, і CloudFront Functions однаково не звертаються до сервісів AWS.

Джерела: <https://aws.amazon.com/lambda/edge/>

</details>

---

<a id="t10-q39"></a>
### T10-Q39
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer manages a website running behind an Elastic Load Balancer in the us-east-1 region. The Developer has recently deployed an identical copy of the website in us-west-1 and needs to send 20% of the traffic to the new site.

How can the Developer achieve this requirement?

- ▫️ Use a blue/green deployment with Amazon Elastic Beanstalk
- ▫️ Use a blue/green deployment with Amazon CodeDeploy
- ✅ Use an Amazon Route 53 Weighted Routing Policy
- ▫️ Use an Amazon Route 53 Geolocation Routing Policy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an Amazon Route 53 Weighted Routing Policy»**

Weighted routing прив'язує кілька ресурсів до одного домену чи субдомену і задає, яка частка трафіку йде на кожен. Корисно для балансування і тестування нових версій. Тут 20% трафіку йде на новий сайт.

**❌ Неправильні варіанти:**
- *Geolocation routing* — маршрутизує за місцем користувача, а не за відсотком.
- *Blue/green with Elastic Beanstalk* — Beanstalk не згадано, і новий сайт уже розгорнуто.
- *Blue/green with CodeDeploy* — CodeDeploy не згадано, і сайт уже розгорнуто.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-weighted>

</details>

---

<a id="t11-q27"></a>
### T11-Q27
*домен: офіційна мітка*

> ⚠️ **Зараз рекомендовані trusted key groups (без root).** → [детальніше](../../07-outdated-and-wrong-answers.md#o21)

Which of the following security credentials can only be created by the AWS Account root user?

- ▫️ IAM User passwords
- ▫️ IAM User Access Keys
- ▫️ EC2 Instance Key Pairs
- ✅ CloudFront Key Pairs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CloudFront Key Pairs»**

Питання на пошук облікових даних, які створює **лише root**. CloudFront key pairs використовують для signed URLs до приватного контенту. Signer — це або trusted key group у CloudFront, або акаунт AWS із CloudFront key pair. Key pair може створити лише root, IAM-користувачі — ні.

ℹ️ AWS рекомендує **trusted key groups**: ними керують через IAM і API, root не потрібен. Див. 07.

**❌ Неправильні варіанти:**
- *EC2 Instance Key Pairs* — створює IAM-користувач з потрібними правами.
- *IAM User Access Keys* — IAM-користувачі створюють їх самі.
- *IAM User passwords* — користувачі змінюють паролі самі.

Джерела: <https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-trusted-signers.html>

</details>

---

<a id="t11-q32"></a>
### T11-Q32
*домен: офіційна мітка*

The development team has just configured and attached the IAM policy needed to access AWS Billing and Cost Management for all users under the Finance department. But, the users are unable to see AWS Billing and Cost Management service in the AWS console.

What could be the reason for this issue?

- ▫️ Only root user has access to AWS Billing and Cost Management console
- ▫️ IAM user should be created under AWS Billing and Cost Management and not under AWS account to have access to Billing console
- ▫️ The users might have another policy that restricts them from accessing the Billing information
- ✅ You need to activate IAM user access to the Billing and Cost Management console for all the users who need access

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «You need to activate IAM user access to the Billing and Cost Management console for all the users who need access»**

За замовчуванням IAM-користувачі не мають доступу до консолі Billing and Cost Management. Root чи адміністратор має:
1. **активувати IAM user access** до білінгу в налаштуваннях акаунта — це робиться один раз;
2. прикріпити користувачам відповідні IAM-політики.

Без активації політики не діють.

**❌ Неправильні варіанти:**
- *Another restrictive policy* — з умови не випливає.
- *Only root has access* — доступ можна дати будь-якому користувачу після активації.
- *Create users under Billing* — IAM-користувачі створюються лише в IAM, незалежно від сервісу.

</details>

---

<a id="t11-q51"></a>
### T11-Q51
*домен: офіційна мітка*

A development team lead is responsible for managing access for her IAM principals. At the start of the cycle, she has granted excess privileges to users to keep them motivated for trying new things. She now wants to ensure that the team has only the minimum permissions required to finish their work.

Which of the following will help her identify unused IAM roles and remove them without disrupting any service?

- ▫️ AWS Security Hub
- ▫️ AWS Trusted Advisor
- ▫️ Amazon Inspector
- ✅ IAM Access Analyzer

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «IAM Access Analyzer»**

IAM Access Analyzer допомагає дійти до найменших привілеїв. **Unused access analyzer** постійно аналізує акаунти організації і збирає findings на центральному дашборді:
- невикористані ролі;
- невикористані access keys і паролі IAM-користувачів;
- для активних ролей і користувачів — невикористані сервіси й дії.

**❌ Неправильні варіанти:**
- *Trusted Advisor* — рекомендації щодо витрат, безпеки, відмовостійкості, лімітів і продуктивності.
- *Inspector* — автоматична оцінка вразливостей.
- *Security Hub* — загальна картина стану безпеки і відповідність стандартам.

</details>

---

<a id="t11-q60"></a>
### T11-Q60
*домен: офіційна мітка*

The manager at an IT company wants to set up member access to user-specific folders in an Amazon S3 bucket - bucket-a. So, user x can only access files in his folder - bucket-a/user/user-x/ and user y can only access files in her folder - bucket-a/user/user-y/ and so on.

As a Developer Associate, which of the following IAM constructs would you recommend so that the policy snippet can be made generic for all team members and the manager does not need to create separate IAM policy for each team member?

- ✅ IAM policy variables
- ▫️ IAM policy condition
- ▫️ IAM policy principal
- ▫️ IAM policy resource

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «IAM policy variables»**

Policy variables замінюють окремі політики для кожного користувача однією політикою на групу. Змінна — це плейсхолдер, який під час оцінки політики замінюється значенням із запиту, наприклад `${aws:username}`. Приклад: кожен користувач групи отримує доступ лише до своєї «домашньої директорії» в S3: `arn:aws:s3:::bucket/home/${aws:username}/*`.

**❌ Неправильні варіанти:**
- *Principal* — визначає, кому дозволено чи заборонено доступ. В identity-based політиках не використовується, лише в trust і resource-based.
- *Condition* — задає умови дії політики, наприклад `"StringEquals": {"aws:username": "johndoe"}`. Сам по собі вимогу не виконує.
- *Resource* — вказує об'єкти через ARN. Сам по собі вимогу не виконує.

</details>

---
