# Amazon API Gateway / AppSync

**Домен 4 — Усунення несправностей та оптимізація** · питань: 8  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q19"></a>
### T01-Q19
*домен: офіційна мітка*

A developer has defined a Lambda integration in Amazon API Gateway using a stage variable. However, when the developer invokes the API method, it consistently returns an "Internal server error" and a 500 status code.

What steps should the developer take to fix the issue?

- ▫️ API calls can't exceed the maximum allowed API request rate per account and per Region. Implement error retries and exponential backoffs to fix the error
- ▫️ If you create a stage variable to call a function through your API, you must add the required permissions. Create an IAM role that your Lambda function can assume when invoking the respective AWS resources
- ✅ If you create a stage variable to call a Lambda function through your API, you must add the required permissions. Update your Lambda function's resource-based AWS Identity and Access Management (IAM) policy so that it grants invoke permission to the API Gateway
- ▫️ When setting the Lambda function as the value of a stage variable, use the function's ARN and not the function alias for setting up the value

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «If you create a stage variable to call a Lambda function through your API, you must add the required permissions. Update your Lambda function's resource-based IAM policy so that it grants invoke permission to the API Gateway»**

Якщо resource-based policy Lambda-функції не дозволяє API її викликати, API Gateway повертає Internal server error (500). Коли функцію викликають через stage variable, дозвіл треба додати одним зі способів:
- оновити resource-based policy функції, дозволивши invoke для API Gateway;
- або створити IAM-роль, яку API Gateway припускатиме, щоб викликати функцію.

Якщо інтеграцію створено в консолі API Gateway звичайним способом (без stage variable), консоль додає дозвіл автоматично.

**❌ Неправильні варіанти:**
- *API calls can't exceed the maximum allowed request rate… implement retries and backoff* — при перевищенні ліміту запитів помилка інша («Rate Exceeded»), а не «Internal server error».
- *Create an IAM role that your Lambda function can assume…* — хибно: потрібна роль, яку припускає API Gateway, щоб викликати функцію, а не роль для самої Lambda.
- *Use the function's ARN and not the alias* — навпаки: у stage variable вказують локальне ім'я функції (можна з alias або версією), а не ARN. Консоль сама розгортає його в ARN.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/services-apigateway.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-set-stage-variables-aws-console.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/api-gateway-lambda-stage-variable-500/>

</details>

---

<a id="t06-q08"></a>
### T06-Q08
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company has deployed a REST API using Amazon API Gateway with a Lambda authorizer. The company needs to log who has accessed the API and how the caller accessed the API. They also require logs that include errors and execution traces for the Lambda authorizer.

Which combination of actions should the Developer take to meet these requirements? (Select TWO.)

- ✅ Enable API Gateway access logs.
- ▫️ Create an API Gateway usage plan.
- ▫️ Enable detailed logging in Amazon CloudWatch.
- ▫️ Enable server access logging.
- ✅ Enable API Gateway execution logging.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable API Gateway access logs.» + «Enable API Gateway execution logging.»**

API Gateway має два типи логування в CloudWatch:
- **Execution logging.** Логами керує API Gateway: створює log groups і streams та записує запити й відповіді. У логах є помилки, трасування виконання (параметри і payload запитів та відповідей), дані Lambda authorizers, вимоги до API keys, usage plans тощо.
- **Access logging.** Ви логуєте, хто звертався до API і як. Log group можна створити власну або вибрати наявну.

**❌ Неправильні варіанти:**
- *Detailed logging in CloudWatch* — не дає потрібної інформації.
- *Usage plan* — не вмикає логування.
- *Server access logging* — це логування для бакетів S3.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/Developerguide/set-up-logging.html>

</details>

---

<a id="t07-q23"></a>
### T07-Q23
*домен: офіційна мітка*

A banking application needs to send real-time alerts and notifications based on any updates from the backend services. The company wants to avoid implementing complex polling mechanisms for these notifications.

Which of the following types of APIs supported by the Amazon API Gateway is the right fit?

- ▫️ HTTP APIs
- ✅ WebSocket APIs
- ▫️ REST or HTTP APIs
- ▫️ REST APIs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «WebSocket APIs»**

У WebSocket API клієнт і сервер можуть надсилати повідомлення одне одному будь-коли. Бекенд сам надсилає (push) дані підключеним користувачам, і складне опитування не потрібне.

Приклади застосувань:
- чати, наприклад на WebSocket API + Lambda;
- дашборди в реальному часі, як-от біржові котирування;
- сповіщення в реальному часі.

API Gateway для WebSocket дає моніторинг і throttling з'єднань і повідомлень, трасування через X-Ray та інтеграцію з HTTP/HTTPS-endpoints. Повідомлення можна спрямовувати в Lambda, Kinesis чи HTTP залежно від вмісту.

**❌ Неправильні варіанти:**
- *REST APIs* — складаються з ресурсів і методів (наприклад, `POST /incomes`, `GET /expenses`), але сервер не може сам надсилати дані клієнту.
- *HTTP APIs* — RESTful API з меншою затримкою і вартістю; push від сервера теж не підтримують.

</details>

---

<a id="t08-q21"></a>
### T08-Q21
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A serverless application uses Amazon API Gateway an AWS Lambda function and a Lambda authorizer function. There is a failure with the application and a developer needs to trace and analyze user requests that pass through API Gateway through to the back end services.

Which AWS service is MOST suitable for this purpose?

- ▫️ VPC Flow Logs
- ▫️ Amazon Inspector
- ▫️ Amazon CloudWatch
- ✅ AWS X-Ray

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS X-Ray»**

X-Ray трасує запити користувачів крізь API Gateway до сервісів за ним. Підтримуються всі типи endpoints (Regional, edge-optimized, private) у всіх регіонах, де є X-Ray.

X-Ray показує весь запит end-to-end:
- service map — затримки всього запиту і downstream-сервісів;
- правила вибірки — які запити записувати й з якою частотою.

Наприклад, у трасуванні видно Lambda-бекенд, Lambda authorizer і код відповіді 200.

**❌ Неправильні варіанти:**
- *CloudWatch* — метрики й логи допомагають, але для трасування розподіленого застосунку X-Ray ефективніший.
- *Inspector* — автоматична оцінка безпеки, а не трасування.
- *VPC Flow Logs* — мережевий трафік інтерфейсів у VPC.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html>

</details>

---

<a id="t11-q37"></a>
### T11-Q37
*домен: офіційна мітка*

You are a developer working on AWS Lambda functions that are invoked via REST API's using Amazon API Gateway. Currently, when a GET request is invoked by the consumer, the entire data-set returned by the Lambda function is visible. Your team lead asked you to format the data response.

Which feature of the API Gateway can be used to solve this issue?

- ✅ Use API Gateway Mapping Templates
- ▫️ Use a Lambda custom interceptor
- ▫️ Use an API Gateway stage variable
- ▫️ Deploy an interceptor shell script

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use API Gateway Mapping Templates»**

Mapping templates в API Gateway перетворюють payload з method request в integration request і з integration response у method response.

Приклад: бекенд повертає інвентар відділу овочів і фруктів (поля `department`, `categories`, `bins` з `category`, `type`, `price`, `unit`, `quantity`). Менеджеру зручніше бачити масив `choices` з полями `kind`, `suggestedPrice` і `available`. Таке перетворення робить VTL-шаблон:

```
#set($inputRoot = $input.path('$'))
{
  "choices": [
#foreach($elem in $inputRoot.bins)
    {
      "kind": "$elem.type",
      "suggestedPrice": "$elem.price per $elem.unit",
      "available": $elem.quantity
    }#if($foreach.hasNext),#end
#end
  ]
}
```

**❌ Неправильні варіанти:**
- *Interceptor shell script* — такої функції немає.
- *Stage variable* — працює як змінна середовища stage; payload не перетворює.
- *Lambda custom interceptor* — такої функції не існує.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/rest-api-data-transformations.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t11-q43"></a>
### T11-Q43
*домен: офіційна мітка*

A company wants to improve the performance of its popular API service that offers unauthenticated read access to daily updated statistical information via Amazon API Gateway and AWS Lambda.

What measures can the company take?

- ▫️ Configure API Gateway to use Elasticache for Memcached
- ▫️ Configure API Gateway to use Gateway VPC Endpoint
- ✅ Enable API caching in API Gateway
- ▫️ Set up usage plans and API keys in API Gateway

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable API caching in API Gateway»**

API Gateway має кілька способів пришвидшити відповіді, зокрема кешування і стиснення payload. Кеш зберігає відповіді endpoint, тож звернень до бекенду менше, а затримка нижча.

**❌ Неправильні варіанти:**
- *Usage plans and API keys* — роблять API продуктом для клієнтів з throttling і квотами на рівні API чи методу; швидкість відповіді не покращують.
- *ElastiCache for Memcached* — API Gateway не інтегрується з ним як із кешем; це downstream-сервіс.
- *Gateway VPC Endpoint* — дає доступ до S3 і DynamoDB з VPC без IGW чи NAT; до швидкості API не стосується.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html>

</details>

---

<a id="t12-q01"></a>
### T12-Q01
*домен: офіційна мітка*

A company uses microservices-based infrastructure to process the API calls from clients, perform request filtering and cache requests using the AWS API Gateway. Users report receiving 501 error code and you have been contacted to find out what is failing.

Which service will you choose to help you troubleshoot?

- ▫️ Use CloudTrail service
- ▫️ Use CloudWatch service
- ✅ Use X-Ray service
- ▫️ Use API Gateway service

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use X-Ray service»**

X-Ray допомагає аналізувати й налагоджувати розподілені застосунки, зокрема мікросервіси, і в розробці, і в продакшні. Він показує шлях запиту end-to-end і карту компонентів.

- **Service map** — зв'язки між сервісами й агреговані дані для кожного: середня затримка, частка помилок. Видно дерева залежностей і виклики між AZ чи регіонами.
- **Traces** — деталі окремих запитів.

Детальніше:
- https://aws.amazon.com/xray/
- https://aws.amazon.com/xray/features/

**❌ Неправильні варіанти:**
- *CloudTrail* — історія API-викликів; корисна для загального моніторингу, але не для аналізу взаємодії мікросервісів.
- *API Gateway* — створює й публікує API, але не показує потік між мікросервісами.
- *CloudWatch* — метрики й події, але для налагодження мікросервісів X-Ray кращий.

Джерела: <https://aws.amazon.com/cloudtrail/> · <https://aws.amazon.com/api-gateway/> · <https://aws.amazon.com/cloudwatch/features/>

</details>

---

<a id="t12-q30"></a>
### T12-Q30
*домен: офіційна мітка*

A company wants to implement authentication for its new RESTful API service that uses Amazon API Gateway. To authenticate the calls, each request must include HTTP headers with a client ID and user ID. These credentials must be compared to the authentication data in a DynamoDB table.

As an AWS Certified Developer Associate, which of the following would you recommend for implementing this authentication in API Gateway?

- ✅ Develop an AWS Lambda authorizer that references the authentication data in the DynamoDB table
- ▫️ Set up an API Gateway Model that requires the credentials, then grant API Gateway access to the authentication table in DynamoDB
- ▫️ Update the API Gateway integration requests to require the credentials, then grant API Gateway access to the authentication table in DynamoDB
- ▫️ Authorize using Amazon Cognito that will reference the authentication table of DynamoDB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Develop an AWS Lambda authorizer that references the authentication data in the DynamoDB table»**

Lambda authorizer — функція API Gateway, що через Lambda контролює доступ до API. Її використовують для власної схеми авторизації: bearer token (OAuth, SAML) або визначення особи за параметрами запиту. API Gateway викликає authorizer, той отримує дані про особу і повертає IAM-політику. Типи:
- **TOKEN** — особа в bearer token (JWT, OAuth);
- **REQUEST** — особа в заголовках, query string, stage variables і `$context`.

Тут authorizer перевіряє облікові дані за таблицею DynamoDB.

Детальніше: https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html

**❌ Неправильні варіанти:**
- *Model* — описує структуру payload через JSON Schema draft 4, не авторизує; моделі необов'язкові.
- *Integration request* — це запит, який API Gateway надсилає в бекенд (Lambda, HTTP, сервіс AWS), а не механізм авторизації.
- *Cognito referencing DynamoDB* — Cognito authorizer (`COGNITO_USER_POOLS`) перевіряє токени user pool і не читає власну таблицю.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-integration-settings.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/models-mappings.html>

</details>

---
