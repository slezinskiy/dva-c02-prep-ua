# Amazon API Gateway / AppSync

**Домен 2 — Безпека** · питань: 4  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q02"></a>
### T01-Q02
*домен: офіційна мітка*

> ⚠️ **STS не є механізмом авторизації API Gateway.** → [детальніше](../../06-contradictions-and-traps.md#c20)

You've just deployed an AWS Lambda function. The lambda function will be invoked via the API Gateway. The API Gateway will need to control access to it.

Which of the following mechanisms is not supported for API Gateway?

- ✅ STS
- ▫️ IAM permissions with sigv4
- ▫️ Cognito User Pools
- ▫️ Lambda Authorizer

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «STS»** (питання про те, що НЕ підтримується)

Amazon API Gateway — сервіс для створення, публікації, підтримки, моніторингу та захисту REST, HTTP і WebSocket API будь-якого масштабу.

AWS Security Token Service (STS) видає тимчасові облікові дані з обмеженими правами для IAM-користувачів або федеративних користувачів. Але як механізм контролю доступу до API Gateway STS не підтримується.

**❌ Неправильні варіанти (ці механізми підтримуються):**
- *IAM permissions with SigV4* — можна застосувати до всього API або до окремих методів.
- *Lambda Authorizer* — контроль доступу до методів REST API за bearer-токеном або за параметрами запиту (заголовки, шляхи, query string, stage variables, context variables).
- *Cognito User Pools* — гнучкі рішення автентифікації та авторизації для REST API.

</details>

---

<a id="t05-q53"></a>
### T05-Q53
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company wants to implement authentication for its new REST service using Amazon API Gateway. To authenticate the calls, each request must include HTTP headers with a client ID and user ID. These credentials must be compared to authentication data in an Amazon DynamoDB table.

What MUST the company do to implement this authentication in API Gateway?

- ▫️ Implement an Amazon Cognito authorizer that references the DynamoDB authentication table
- ✅ Implement an AWS Lambda authorizer that references the DynamoDB authentication table
- ▫️ Modify the integration requests to require the credentials, then grant API Gateway access to the authentication table
- ▫️ Create a model that requires the credentials, then grant API Gateway access to the authentication table

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement an AWS Lambda authorizer that references the DynamoDB authentication table»**

Lambda authorizer (раніше custom authorizer) — функція API Gateway, яка через Lambda контролює доступ до API. Її використовують для власної схеми авторизації: bearer token (OAuth, SAML) або визначення особи за параметрами запиту. На кожен запит API Gateway викликає authorizer, той отримує дані про особу і повертає IAM-політику.

Типи authorizer:
- **TOKEN** — особа передається в bearer token (JWT, OAuth).
- **REQUEST** — особа визначається з комбінації заголовків, query string, stageVariables і змінних `$context`.
- Для WebSocket API підтримується лише REQUEST.

Тут облікові дані передаються в заголовках, тож потрібен REQUEST authorizer, який звіряє їх із таблицею DynamoDB.

**❌ Неправильні варіанти:**
- *Model* — model описує структуру payload через JSON Schema і не авторизує.
- *Integration request + access to table* — API Gateway сам не авторизує за даними таблиці; потрібен authorizer.
- *Cognito authorizer referencing DynamoDB* — Cognito authorizer перевіряє токени user pool і не читає власну таблицю. Тут потрібен Lambda authorizer.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html>

</details>

---

<a id="t07-q06"></a>
### T07-Q06
*домен: офіційна мітка*

> ⚠️ **Сумнівно: CORS — не захист від клієнтів поза браузером.** → [детальніше](../../07-outdated-and-wrong-answers.md#d05)

You team maintains a public API Gateway that is accessed by clients from another domain. Usage has been consistent for the last few months but recently it has more than doubled. As a result, your costs have gone up and would like to prevent other unauthorized domains from accessing your API.

Which of the following actions should you take?

- ▫️ Use Account-level throttling
- ▫️ Assign a Security Group to your API Gateway
- ▫️ Use Mapping Templates
- ✅ Restrict access by using CORS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Restrict access by using CORS»**

CORS визначає, як веб-застосунок з одного домену звертається до ресурсів на іншому домені. Щоб API не обслуговував запити з чужих доменів, CORS для відповідних методів вимикають або не налаштовують.

⚠️ CORS обмежує лише **браузери**; запити з `curl` чи бекенду він не блокує. Для реального контролю доступу використовують resource policy, authorizers або WAF. На іспиті для формулювання «заборонити іншим доменам» ключ — CORS. Див. 07 (d05).

**❌ Неправильні варіанти:**
- *Account-level throttling* — обмежує кількість запитів (за замовчуванням 10 000 rps і burst 5 000 на акаунт), а не доступ із доменів.
- *Mapping templates* — це VTL-скрипти, що перетворюють payload; до доступу вони не мають стосунку.
- *Security group on API Gateway* — API Gateway не має security groups. Є resource policies, якими можна обмежити IP, але IP користувач може змінити.

Джерела: <https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-protect.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/rest-api-data-transformations.html>

</details>

---

<a id="t09-q62"></a>
### T09-Q62
*домен: офіційна мітка*

A university has created a student portal that is accessible through a smartphone app and web application. The smartphone app is available in both Android and IOS and the web application works on most major browsers. Students will be able to do group study online and create forum questions. All changes made via smartphone devices should be available even when offline and should synchronize with other devices.

Which of the following AWS services will meet these requirements?

- ✅ AWS AppSync
- ▫️ Cognito Identity Pools
- ▫️ Cognito User Pools
- ▫️ BeanStalk

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS AppSync»**

AppSync — керований GraphQL-сервіс для застосунків, що працюють у реальному часі й над якими користувачі працюють спільно. Він дає офлайн-доступ, вирішення конфліктів і синхронізацію між пристроями. Мобільний застосунок працює офлайн, складає зміни локально і синхронізує їх, коли з'являється зв'язок. Інтегрується з Cognito для автентифікації та з DynamoDB чи Aurora для зберігання.

**❌ Неправильні варіанти:**
- *Cognito Identity Pools* — видають тимчасові облікові дані AWS (включно з гостями), але даних не синхронізують.
- *Cognito User Pools* — каталог користувачів і вхід, синхронізації немає. Порівняння: https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html
- *Beanstalk* — деплой і масштабування застосунків, а не синхронізація даних.

</details>

---
