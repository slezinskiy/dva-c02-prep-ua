# AWS Lambda

**Домен 2 — Безпека** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t08-q23"></a>
### T08-Q23
*домен: визначено автоматично (у джерелі: AWS Compute)*

An AWS Lambda function requires several environment variables with secret values. The secret values should be obscured in the Lambda console and API output even for users who have permission to use the key.

What is the best way to achieve this outcome and MINIMIZE complexity and latency?

- ▫️ Store the encrypted values in an encrypted Amazon S3 bucket and reference them from within the code
- ✅ Encrypt the secret values client-side using encryption helpers
- ▫️ Encrypt the secret values with a customer-managed CMK
- ▫️ Use an external encryption infrastructure to encrypt the values and add them as environment variables

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Encrypt the secret values client-side using encryption helpers»**

Lambda завжди шифрує змінні середовища at rest. Це шифрування можна налаштувати:
- **Ключ.** Для кожної функції можна вказати власний customer managed ключ KMS. Інакше використовується AWS managed `aws/lambda`.
- **Encryption helpers.** Консоль шифрує значення на клієнті ще до надсилання в Lambda. Тоді секрети не видно відкритим текстом ні в консолі, ні в конфігурації, яку повертає API. Консоль також дає приклад коду для розшифрування в handler.

Це найпростіший спосіб: шифрування й далі працює через KMS, і функція розшифровує значення під час виконання.

**❌ Неправильні варіанти:**
- *Customer-managed CMK only* — значення однаково видно в консолі; потрібне шифрування на клієнті.
- *Encrypted S3 bucket* — додає складність і затримку.
- *External encryption infrastructure* — додає складність і затримку.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/security-dataprotection.html>

</details>

---

<a id="t08-q63"></a>
### T08-Q63
*домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Надлишково: PutItem не потрібен — UpdateItem створює item.** → [детальніше](../../06-contradictions-and-traps.md#c17)

A Developer is working on an AWS Lambda function that accesses Amazon DynamoDB. The Lambda function must retrieve an item and update some of its attributes or create the item if it does not exist. The Lambda function has access to the primary key.

Which IAM permission should the Developer request for the Lambda function to achieve this functionality?

- ✅ “dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”
- ▫️ “dynamodb:GetRecords”, “dynamodb:PutItem”, and “dynamodb:UpdateTable”
- ▫️ “dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:DescribeTable”
- ▫️ “dynamodb:DeleteItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «“dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”»**

Розробнику потрібно читати, змінювати і створювати елементи:
- **GetItem** — повертає атрибути елемента за первинним ключем.
- **UpdateItem** — змінює атрибути наявного елемента або створює новий, якщо його немає.
- **PutItem** — створює новий елемент або повністю замінює наявний з тим самим ключем.

**❌ Неправильні варіанти:**
- *DeleteItem…* — видаляти елементи не потрібно.
- *…DescribeTable* — інформація про таблицю (статус, схема ключів, індекси) не потрібна.
- *GetRecords, PutItem, UpdateTable* — `GetRecords` — це API DynamoDB Streams, а не таблиці; `UpdateTable` змінює налаштування таблиці.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations.html>

</details>

---

<a id="t09-q51"></a>
### T09-Q51
*домен: офіційна мітка*

> ⚠️ **STS не є механізмом авторизації API Gateway.** → [детальніше](../../06-contradictions-and-traps.md#c20)

A developer is looking at establishing access control for an API that connects to a Lambda function downstream.

Which of the following represents a mechanism that CANNOT be used for authenticating with the API Gateway?

- ▫️ Cognito User Pools
- ✅ AWS Security Token Service (STS)
- ▫️ Lambda Authorizer
- ▫️ Standard AWS IAM roles and policies

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Security Token Service (STS)»**

Питання на пошук механізму, який API Gateway **не підтримує** для автентифікації й авторизації. STS видає тимчасові облікові дані для IAM-користувачів і федеративних користувачів, але як механізм контролю доступу до API Gateway не використовується.

Підтримувані механізми: https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html

**❌ Інші варіанти підтримуються:**
- *IAM roles and policies* — гнучкий контроль доступу до всього API чи окремих методів: хто може створювати, керувати й викликати.
- *Lambda Authorizer* — керує доступом через bearer token або параметри запиту.
- *Cognito User Pools* — визначають, хто може викликати методи REST API.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html> · <https://docs.aws.amazon.com/STS/latest/APIReference/welcome.html>

</details>

---

<a id="t11-q34"></a>
### T11-Q34
*домен: офіційна мітка*

The development team at a company creates serverless solutions using AWS Lambda. Functions are invoked by clients via AWS API Gateway which anyone can access. The team lead would like to control access using a 3rd party authorization mechanism.

As a Developer Associate, which of the following options would you recommend for the given use-case?

- ✅ Lambda Authorizer
- ▫️ Cognito User Pools
- ▫️ API Gateway User Pools
- ▫️ IAM permissions with sigv4

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Lambda Authorizer»**

Lambda authorizer (раніше custom authorizer) — ваша Lambda-функція, що контролює доступ до API. Вона працює з bearer token, наприклад OAuth чи SAML, тож підходить для автентифікації стороннім провайдером. Спочатку створюють функцію з логікою авторизації і, за потреби, автентифікації.

**❌ Неправильні варіанти:**
- *IAM + SigV4* — дозволи все одно потрібні, але вимога — автентифікація третьою стороною, а це Lambda authorizer.
- *Cognito User Pools* — каталог, керований AWS; не відповідає вимозі власної автентифікації третьою стороною.
- *API Gateway User Pools* — такої функції не існує.

</details>

---

<a id="t11-q63"></a>
### T11-Q63
*домен: офіційна мітка*

> ⚠️ **Lambda alias 10%.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A development team at a social media company uses AWS Lambda functions for its serverless stack on AWS Cloud. For a new deployment, the Team Lead wants to send only a certain portion of the traffic to a new version of a Lambda function. In case the deployment goes wrong, the solution should also support the ability to roll back to a previous version of the Lambda function, with MIMINUM downtime for the application.

As a Developer Associate, which of the following options would you recommend to address this use-case?

- ✅ Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure the alias to send 10% of the users to this new version. If the deployment goes wrong, reset the alias to point all traffic to the current version
- ▫️ Set up the application to have multiple alias of the Lambda function. Deploy the new version of the code. Configure a new alias that points to the current alias of the Lambda function for handling 10% of the traffic. If the deployment goes wrong, reset the new alias to point all traffic to the most recent working alias of the Lambda function
- ▫️ Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure alias to send all users to this new version. If the deployment goes wrong, reset the alias to point to the current version
- ▫️ Set up the application to directly deploy the new Lambda version. If the deployment goes wrong, reset the application back to the current version using the version number in the ARN

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure the alias to send 10% of the users to this new version. If the deployment goes wrong, reset the alias to point all traffic to the current version»**

Версії Lambda допомагають керувати деплоями: нову версію можна опублікувати для бета-тестування, не зачіпаючи стабільну. Змінювати код і налаштування можна лише в неопублікованій версії (`$LATEST`); після публікації вони фіксуються.

Alias — вказівник на версію. Routing configuration alias відправляє частину трафіку на іншу версію, наприклад 90% на стару і 10% на нову. Якщо щось піде не так, alias повертають на поточну версію.

**❌ Неправильні варіанти:**
- *Alias → all users to new version* — уся аудиторія відразу на новій версії, і при проблемі буде простій.
- *Deploy directly, roll back via version ARN* — те саме, і доведеться змінювати застосунок.
- *Alias pointing to another alias* — alias може вказувати лише на версію, а не на інший alias.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html>

</details>

---
