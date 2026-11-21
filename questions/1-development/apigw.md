# Amazon API Gateway / AppSync

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 19  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q16"></a>
### T02-Q16
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company runs a legacy application that uses an XML-based SOAP interface. The company needs to expose the functionality of the service to external customers and plans to use Amazon API Gateway.

How can a Developer configure the integration?

- ▫️ Create a RESTful API using Amazon API Gateway. Pass the incoming JSON to the SOAP interface through an Application Load Balancer.
- ✅ Create a RESTful API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates.
- ▫️ Create a SOAP API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using AWS Lambda.
- ▫️ Create a SOAP API using Amazon API Gateway. Pass the incoming JSON to the SOAP interface through a Network Load Balancer.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a RESTful API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates»**

В API Gateway method request може мати інший формат payload, ніж integration request, якого чекає backend, і навпаки для відповіді. **Mapping templates** перетворюють payload method request → integration request та integration response → method response, наприклад JSON у XML для SOAP.

**❌ Неправильні варіанти:**
- *Pass the incoming JSON to the SOAP interface through an ALB* — без перетворення SOAP-сервіс не зрозуміє JSON.
- *Create a SOAP API using API Gateway* (обидва варіанти) — API Gateway не підтримує тип SOAP API.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/Developerguide/request-response-data-mappings.html>

</details>

---

<a id="t02-q24"></a>
### T02-Q24
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company has created a set of APIs using Amazon API Gateway and exposed them to partner companies. The APIs have caching enabled for all stages. The partners require a method of invalidating the cache that they can build into their applications.

What can the partners use to invalidate the API cache?

- ▫️ They must wait for the TTL to expire
- ✅ They can pass the HTTP header Cache-Control: max-age=0
- ▫️ They can use the query string parameter INVALIDATE_CACHE
- ▫️ They can invoke an AWS API endpoint which invalidates the cache

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «They can pass the HTTP header Cache-Control: max-age=0»**

API caching в API Gateway кешує відповіді endpoint'а: менше викликів бекенду, менша затримка. Кеш вмикається на рівні stage з TTL у секундах (за замовчуванням 300, максимум 3600, 0 — кеш вимкнено).

Клієнт може інвалідувати запис кешу для окремого запиту, надіславши заголовок `Cache-Control: max-age=0`. Тоді відповідь береться напряму з інтеграції й замінює запис у кеші, якщо клієнт має на це право. Право надають IAM-політикою з дією `execute-api:InvalidateCache` на потрібні ресурси.

**❌ Неправильні варіанти:**
- *Query string parameter INVALIDATE_CACHE* — такого механізму немає.
- *They must wait for the TTL to expire* — не обов'язково, заголовок можна передати будь-коли.
- *Invoke an AWS API endpoint which invalidates the cache* — такого способу немає.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t02-q37"></a>
### T02-Q37
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A customer requires a serverless application with an API which mobile clients will use. The API will have both and AWS Lambda function and an Amazon DynamoDB table as data sources. Responses that are sent to the mobile clients must contain data that is aggregated from both of these data sources.

The developer must minimize the number of API endpoints and must minimize the number of API calls that are required to retrieve the necessary data.

Which solution should the developer use to meet these requirements?

- ▫️ REST API on Amazon API Gateway
- ▫️ GraphQL API on an Amazon EC2 instance
- ▫️ REST API on AWS Elastic Beanstalk
- ✅ GraphQL API on AWS AppSync

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «GraphQL API on AWS AppSync»**

GraphQL API на AppSync дає фронтенду змогу з одного endpoint'а запитувати кілька баз даних, мікросервісів і API (тут Lambda + DynamoDB) — менше endpoint'ів і менше викликів. У REST API на API Gateway кожен endpoint має одну ціль.

**❌ Неправильні варіанти:**
- *REST API on API Gateway* — один target на endpoint, тож endpoint'ів знадобиться більше.
- *GraphQL API on EC2* — не serverless, а рішення має бути serverless.
- *REST API on Elastic Beanstalk* — з тих самих причин.

Джерела: <https://aws.amazon.com/blogs/mobile/appsync-microservices/>

</details>

---

<a id="t02-q46"></a>
### T02-Q46
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A startup is developing a prototype for a news aggregator application. This application will display the latest news for a specific industry and provide a RESTful API endpoint that clients can invoke. Where feasible, the application should leverage AWS's caching features to reduce the load on the backend service. The backend of the application is expected to handle a modest amount of traffic, primarily during testing periods.

Which method would be the most cost-effective for the developer to implement this REST endpoint?

- ▫️ Launch an Amazon EC2 instance, host the application on it, and employ Amazon ElastiCache for caching purposes.
- ▫️ Construct an Amazon ECS service for the application, using Amazon ElastiCache for caching.
- ▫️ Deploy an AWS Elastic Beanstalk application and use Amazon DynamoDB for caching services.
- ✅ Utilize AWS API Gateway with an AWS Lambda function as the backend and enable caching in API Gateway.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Utilize AWS API Gateway with an AWS Lambda function as the backend and enable caching in API Gateway»**

API Gateway + Lambda — serverless-рішення, де вартість прямо залежить від використання. А кешування в API Gateway знімає навантаження з бекенду.

**❌ Неправильні варіанти:**
- *EC2 + ElastiCache* — EC2 коштує постійно, навіть у простої, а ElastiCache для прототипу зайво збільшує витрати.
- *ECS + ElastiCache* — надмірно для простого прототипу, більше ресурсів і керування.
- *Elastic Beanstalk + DynamoDB for caching* — EB сам кешування не дає, а DynamoDB — база даних, а не кеш.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t03-q18"></a>
### T03-Q18
*домен: офіційна мітка*

A financial services company with over 10,000 employees has hired you as the new Senior Developer. Initially caching was enabled to reduce the number of calls made to all API endpoints and improve the latency of requests to the company’s API Gateway.

For testing purposes, you would like to invalidate caching for the API clients to get the most recent responses. Which of the following should you do?

- ▫️ Using the Header Bypass-Cache=1
- ▫️ Using the request parameter ?cache-control-max-age=0
- ▫️ Use the Request parameter: ?bypass_cache=1
- ✅ Using the Header Cache-Control: max-age=0

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Using the Header Cache-Control: max-age=0»**

Клієнт API може інвалідувати запис кешу й перезавантажити його з інтеграції для окремого запиту, надіславши заголовок `Cache-Control: max-age=0` (якщо має на це дозвіл). Нова відповідь замінює запис у кеші.

**❌ Неправильні варіанти:**
- *?bypass_cache=1* — такого параметра немає.
- *Header Bypass-Cache=1* — вигадано.
- *?cache-control-max-age=0* — потрібен заголовок, а не параметр запиту.

</details>

---

<a id="t05-q37"></a>
### T05-Q37
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A legacy service has an XML-based SOAP interface. The Developer wants to expose the functionality of the service to external clients with the Amazon API Gateway. Which technique will accomplish this?

- ▫️ Create a RESTful API with the API Gateway; pass the incoming XML to the SOAP interface through an Application Load Balancer
- ✅ Create a RESTful API with the API Gateway; transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates
- ▫️ Create a RESTful API with the API Gateway; pass the incoming JSON to the SOAP interface through an Application Load Balancer
- ▫️ Create a RESTful API with the API Gateway; transform the incoming XML into a valid message for the SOAP interface using mapping templates

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a RESTful API with the API Gateway; transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates»**

API Gateway створює, публікує й захищає REST- і WebSocket-API будь-якого масштабу. Формат payload у method request може відрізнятися від того, що потрібен бекенду в integration request, і так само у відповідях. Mapping templates перетворюють payload в обидва боки. Наприклад, XML від legacy-сервісу можна віддати клієнту як JSON без змін у самому сервісі.

Тут сучасний інтерфейс приймає JSON, а mapping template перетворює його на коректне XML-повідомлення для SOAP.

**❌ Неправильні варіанти:**
- *Pass JSON through an ALB* — ALB не потрібен; mapping template в API Gateway дешевший.
- *Pass XML through an ALB* — вхідні дані будуть JSON, а не XML, і замість ALB потрібен mapping template.
- *Transform incoming XML* — вхідні дані будуть JSON, бо публікується сучасний інтерфейс.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/models-mappings.html>

</details>

---

<a id="t06-q13"></a>
### T06-Q13
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer is building a WebSocket API using Amazon API Gateway. The payload sent to this API is JSON that includes an action key which can have multiple values. The Developer must integrate with different routes based on the value of the action key of the incoming JSON payload.

How can the Developer accomplish this task with the LEAST amount of configuration?

- ▫️ Create a separate stage for each possible value of the action key.
- ▫️ Create a mapping template to map the action key to an integration request.
- ▫️ Set the value of the route selection expression to $default.
- ✅ Set the value of the route selection expression to $request.body.action.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set the value of the route selection expression to $request.body.action.»**

У WebSocket API вхідні JSON-повідомлення спрямовуються до бекенд-інтеграцій за налаштованими маршрутами. Повідомлення не у форматі JSON ідуть на маршрут `$default`.

Як працює маршрутизація:
- `routeSelectionExpression` задається на рівні API і вказує JSON-властивість, яка має бути в payload.
- Route key — значення, з яким порівнюється результат цього виразу.

Якщо в повідомленні є властивість `action`, вираз матиме вигляд `$request.body.action`, а таблиця маршрутів зіставить значення `action` з вашими route keys.

**❌ Неправильні варіанти:**
- *Separate stage per action* — окремі stages не потрібні; маршрутизує ключ action.
- *Mapping template* — mapping templates трансформують дані, а не маршрутизують.
- *Route selection expression = $default* — `$default` — це маршрут для повідомлень не у форматі JSON, а не вираз вибору.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/Developerguide/websocket-api-develop-routes.html>

</details>

---

<a id="t06-q42"></a>
### T06-Q42
*домен: визначено автоматично (у джерелі: AWS Storage)*

A Developer implemented a static website hosted in Amazon S3 that makes web service requests hosted in Amazon API Gateway and AWS Lambda. The site is showing an error that reads:

“No ‘Access-Control-Allow-Origin’ header is present on the requested resource. Origin ‘null’ is therefore not allowed access.”

What should the Developer do to resolve this issue?

- ▫️ Add the Access-Control-Request-Method header to the request
- ▫️ Add the Access-Control-Request-Headers header to the request
- ▫️ Enable cross-origin resource sharing (CORS) on the S3 bucket
- ✅ Enable cross-origin resource sharing (CORS) for the method in API Gateway

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable cross-origin resource sharing (CORS) for the method in API Gateway»**

CORS дозволяє веб-застосунку, завантаженому з одного домену, звертатися до ресурсів на іншому домені. Тут сторінка з S3 звертається до API Gateway і Lambda. CORS вмикають на ресурсі, до якого звертаються, тобто на методі API Gateway.

ℹ️ Для Lambda proxy-інтеграції заголовок `Access-Control-Allow-Origin` має повертати сама функція.

**❌ Неправильні варіанти:**
- *CORS on S3 bucket* — CORS вмикають на ресурсі, до якого звертаються, а це API Gateway, не S3.
- *Access-Control-Request-Method header* — цей заголовок браузер надсилає в preflight, щоб запитати дозвіл на HTTP-метод; проблему він не вирішує.
- *Access-Control-Request-Headers header* — цим заголовком браузер повідомляє сервер, які заголовки буде надіслано.

Джерела: <https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html>

</details>

---

<a id="t06-q53"></a>
### T06-Q53
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A new application will be hosted on the domain name dctlabs.com using an Amazon API Gateway REST API front end. The Developer needs to configure the API with a path to dctlabs.com/products that will be accessed using the HTTP GET verb. How MUST the Developer configure the API? (Select TWO

- ▫️ Create a GET resource
- ✅ Create a GET method
- ▫️ Create a /GET method
- ✅ Create a /products resource
- ▫️ Create a /products method

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a GET method» + «Create a /products resource»**

REST API в API Gateway — це набір HTTP-ресурсів і методів, інтегрованих із HTTP-endpoints, Lambda або іншими сервісами AWS. Набір деплоять на один чи кілька stages. Ресурси утворюють дерево за логікою застосунку, і кожен ресурс має один чи кілька методів з HTTP-дієсловами. Тож спочатку створюють ресурс `/products`, а в ньому — метод `GET`.

**❌ Неправильні варіанти:**
- */products method* — `/products` — це ресурс, а не метод.
- *GET resource* — GET — це метод, а не ресурс.
- */GET method* — перед назвою методу немає слеша.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-basic-concept.html>

</details>

---

<a id="t06-q64"></a>
### T06-Q64
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company maintains a REST API service using Amazon API Gateway with native API key validation. The company recently launched a new registration page, which allows users to sign up for the service. The registration page creates a new API key using CreateApiKey and sends the new key to the user. When the user attempts to call the API using this key, the user receives a 403 Forbidden error. Existing users are unaffected and can still call the API.

What code updates will grant these new users’ access to the API?

- ▫️ The importApiKeys method must be called to import all newly created API keys into the current stage of the API
- ✅ The createUsagePlanKey method must be called to associate the newly created API key with the correct usage plan
- ▫️ The createDeployment method must be called so the API can be redeployed to include the newly created API key
- ▫️ The updateAuthorizer method must be called to update the API’s authorizer to include the newly created API key

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The createUsagePlanKey method must be called to associate the newly created API key with the correct usage plan»**

Usage plan визначає, хто має доступ до задеплоєних stages і методів API, скільки запитів і з якою швидкістю. Клієнтів план розпізнає за API keys і окремо для кожного ключа рахує запити, обмежує швидкість (throttling) і квоти.

API key — буквено-цифровий рядок, який видають розробникам-клієнтам для доступу до API. Його використовують разом з usage plans або Lambda authorizers. Ключ може згенерувати API Gateway, або його імпортують, наприклад з CSV. Щоб прив'язати новий ключ до usage plan, викликають `CreateUsagePlanKey`.

**❌ Неправильні варіанти:**
- *createDeployment* — щоб прив'язати API key, повторно деплоїти API не потрібно.
- *updateAuthorizer* — оновлює authorizer, а не API key.
- *importApiKeys* — імпортує ключі із зовнішнього джерела, наприклад CSV; тут не потрібно.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html> · <http://docs.amazonaws.cn/en_us/sdkfornet/v3/apidocs/items/APIGateway/MAPIGatewayCreateUsagePlanKeyCreateUsagePlanKeyRequest.html>

</details>

---

<a id="t07-q43"></a>
### T07-Q43
*домен: офіційна мітка*

You are creating a mobile application that needs access to the AWS API Gateway. Users will need to register first before they can access your API and you would like the user management to be fully managed.

Which authentication option should you use for your API Gateway layer?

- ✅ Use Cognito User Pools
- ▫️ Use API Gateway User Pools
- ▫️ Use Lambda Authorizer
- ▫️ Use IAM permissions with sigv4

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Cognito User Pools»**

Замість IAM чи Lambda authorizer доступ до API можна контролювати через Cognito user pool:
1. Створити authorizer типу `COGNITO_USER_POOLS`.
2. Налаштувати на нього методи API і задеплоїти.
3. Клієнт входить у user pool і отримує ID- або access-токен.
4. Клієнт викликає метод із токеном, зазвичай у заголовку `Authorization`.

Виклик успішний, лише якщо токен є і він валідний. User pool також підтримує вхід через соцмережі.

**❌ Неправильні варіанти:**
- *Lambda Authorizer* — дає власну схему авторизації (bearer token, OAuth, SAML, параметри запиту), але не є керованим каталогом користувачів.
- *IAM + SigV4* — запити підписують access keys IAM, а створювати IAM-користувача для кожного відвідувача сайту неможливо.
- *API Gateway User Pools* — такої функції не існує.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> · <https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html>

</details>

---

<a id="t07-q46"></a>
### T07-Q46
*домен: офіційна мітка*

You have a three-tier web application consisting of a web layer using AngularJS, an application layer using an AWS API Gateway and a data layer in an Amazon Relational Database Service (RDS) database. Your web application allows visitors to look up popular movies from the past. The company is looking at reducing the number of calls made to endpoint and improve latency to the API.

What can you do to improve performance?

- ▫️ Use Mapping Templates
- ▫️ Use Stage Variables
- ✅ Enable API Gateway Caching
- ▫️ Use Amazon Kinesis Data Streams to stream incoming data and reduce the burden on Gateway APIs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable API Gateway Caching»**

API caching кешує відповіді endpoint, тож викликів бекенду менше, а затримка нижча. Кеш вмикають на stage, і відповіді зберігаються на час TTL; поки він не сплив, API Gateway відповідає з кешу без звернення до бекенду. TTL за замовчуванням — 300 с, максимум — 3 600 с; TTL = 0 вимикає кеш.

**❌ Неправильні варіанти:**
- *Mapping templates* — VTL-скрипти для перетворення payload; затримку не зменшують.
- *Stage variables* — працюють як змінні середовища для stage, наприклад вибір бекенду; затримку не зменшують.
- *Kinesis Data Streams* — сервіс для потокових даних; не зменшує навантаження на API і не знижує затримку.

</details>

---

<a id="t08-q34"></a>
### T08-Q34
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company is providing APIs as a web-based service to allow anonymous access to daily updated statistical data, using Amazon API Gateway and AWS Lambda for API development. The service's popularity has grown, and the company aims to improve the API responsiveness.

What measure should the company undertake to fulfill this objective?

- ▫️ Set up API Gateway with an interface VPC endpoint.
- ▫️ Cache API results in an Amazon ElastiCache cluster.
- ✅ Activate caching in API Gateway.
- ▫️ Set up API keys and usage plans in API Gateway.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Activate caching in API Gateway.»**

Кешування в API Gateway зберігає відповіді endpoint. Звернень до бекенду стає менше, а затримка — нижча.

**❌ Неправильні варіанти:**
- *Interface VPC endpoint* — підвищує безпеку й приватність, а не швидкість відповіді.
- *ElastiCache for API results* — ElastiCache кешує дані з БД, а для кешу API Gateway не призначений.
- *API keys and usage plans* — контролюють використання API, а не швидкість.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t08-q48"></a>
### T08-Q48
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A set of APIs are exposed to customers using Amazon API Gateway. These APIs have caching enabled on the API Gateway. Customers have asked for an option to invalidate this cache for each of the APIs.

What action can be taken to allow API customers to invalidate the API Cache?

- ▫️ Ask customers to invoke an AWS API endpoint which invalidates the cache
- ▫️ Ask customers to add a query string parameter called INVALIDATE_CACHE” when making an API call
- ▫️ Ask customers to use AWS credentials to call the InvalidateCache API
- ✅ Ask customers to pass an HTTP header called Cache-Control:max-age=0

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Ask customers to pass an HTTP header called Cache-Control:max-age=0»**

Клієнт може інвалідувати запис у кеші API Gateway для окремого запиту, надіславши заголовок `Cache-Control: max-age=0`. Тоді відповідь приходить напряму з бекенду і замінює запис у кеші. Клієнт має мати на це дозвіл, тобто `execute-api:InvalidateCache` у політиці.

**❌ Неправильні варіанти:**
- *Call InvalidateCache API with AWS credentials* — так клієнти кеш не інвалідують: потрібен заголовок.
- *Invoke an endpoint that invalidates the cache* — кеш не інвалідують окремим endpoint, а заголовком.
- *Query string INVALIDATE_CACHE* — такого механізму немає.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t09-q14"></a>
### T09-Q14
*домен: офіційна мітка*

As a Senior Developer, you are tasked with creating several API Gateway powered APIs along with your team of developers. The developers are working on the API in the development environment, but they find the changes made to the APIs are not reflected when the API is called.

As a Developer Associate, which of the following solutions would you recommend for this use-case?

- ▫️ Enable Lambda authorizer to access API
- ▫️ Use Stage Variables for development state of API
- ▫️ Developers need IAM permissions on API execution component of API Gateway
- ✅ Redeploy the API to an existing stage or to a new stage

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Redeploy the API to an existing stage or to a new stage»**

Щоб API можна було викликати, його деплоять: створюють deployment і прив'язують до stage. Stage — це стан API в життєвому циклі (dev, prod, beta, v2), який входить в URL. Після **будь-якої** зміни API — маршрутів, методів, інтеграцій, authorizers, усього, крім налаштувань stage — його треба повторно задеплоїти на наявний або новий stage. Інакше зміни не видно.

**❌ Неправильні варіанти:**
- *IAM permissions on execution component* — `execute-api` потрібен тим, хто викликає API. Розробникам для створення й деплою потрібні права на management-компонент (`apigateway:*`).
- *Lambda authorizer* — контролює доступ користувачів, а проблема в розробників.
- *Stage variables* — до сценарію не стосуються.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-deploy-api.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/permissions.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t10-q12"></a>
### T10-Q12
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An online multiplayer game employs Amazon API Gateway WebSocket APIs with an HTTP backend. The game developer needs to add a feature that identifies players with unstable connections who repeatedly join and leave the game. The developer also wants the ability to disconnect such players from the game.

What two modifications should the developer implement in the game to fulfill these requirements? (Select TWO.)

- ▫️ Switch to AWS App Runner for the backend service.
- ▫️ Implement AWS Cognito for player authentication in the backend service.
- ▫️ Switch to REST APIs in the backend service.
- ✅ Add logic to track the player's connection status using Amazon DynamoDB in the backend service.
- ✅ Implement $connect and $disconnect routes in the backend service.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add logic to track the player's connection status using Amazon DynamoDB in the backend service.» + «Implement $connect and $disconnect routes in the backend service.»**

- **`$connect` і `$disconnect`** у WebSocket API фіксують підключення і відключення гравців.
- **DynamoDB** зберігає й відстежує стан підключення кожного гравця, тож видно, хто часто відключається.

**❌ Неправильні варіанти:**
- *Switch to REST APIs* — не дасть керувати нестабільними з'єднаннями; для двостороннього зв'язку в реальному часі краще WebSocket.
- *Cognito* — автентифікація, а не відстеження з'єднань.
- *App Runner* — запуск контейнерних застосунків, задачу не вирішує.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api-route-keys-connect-disconnect.html>

</details>

---

<a id="t10-q42"></a>
### T10-Q42
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company provides a large number of services on AWS to customers. The customers connect to one or more services directly and the architecture is becoming complex. How can the architecture be refactored to provide a single interface for the services?

- ▫️ AWS Single Sign On (SSO)
- ▫️ AWS X-Ray
- ✅ Amazon API Gateway
- ▫️ AWS Cognito

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon API Gateway»**

API Gateway — керований сервіс для створення, публікації й захисту API: «вхідні двері» до даних і логіки бекенду. Підтримує RESTful і WebSocket API, контейнерні й serverless-навантаження. Тут це єдиний інтерфейс для всіх споживачів сервісів компанії, і архітектура стає простішою.

**❌ Неправильні варіанти:**
- *X-Ray* — аналіз і налагодження застосунків.
- *Cognito* — реєстрація, вхід і контроль доступу.
- *AWS SSO* — єдиний вхід в акаунти AWS і бізнес-застосунки. Тепер це IAM Identity Center.

Джерела: <https://aws.amazon.com/api-gateway/features/>

</details>

---

<a id="t11-q01"></a>
### T11-Q01
*домен: офіційна мітка*

> ⚠️ **Окремий dev stage.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A company wants to provide beta access to some developers on its development team for a new version of the company's Amazon API Gateway REST API, without causing any disturbance to the existing customers who are using the API via a frontend UI and Amazon Cognito authentication. The new version has new endpoints and backward-incompatible interface changes, and the company's development team is responsible for its maintenance.

Which of the following will satisfy these requirements in the MOST operationally efficient manner?

- ▫️ Create a new API Gateway API that points to the new API application code and then have the developers point the endpoints to the new API
- ▫️ Configure a canary release deployment on the API Gateway API and then have the developers point to the relevant deployment by referencing the stage variable in the endpoint
- ✅ Create a development stage on the API Gateway API and then have the developers point the endpoints to the development stage
- ▫️ Create new API keys on the API Gateway API and then have the developers point the endpoints by passing the new API keys

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a development stage on the API Gateway API and then have the developers point the endpoints to the development stage»**

Stage — стан API в життєвому циклі (dev, prod, beta, v2), який визначається ID API та іменем stage. Для stage налаштовують кешування, throttling, логування, stage variables і canary. Нові stages можна додавати й прив'язувати до наявних deployments.

Тут створюють stage розробки, інтегрований із новою версією бекенду, де є нові endpoints і несумісні зміни інтерфейсу. Клієнти й далі працюють зі старим API.

**❌ Неправильні варіанти:**
- *Canary release* — частина трафіку на тому самому stage випадково йде на нову версію, тобто її побачать і клієнти.
- *New API keys* — API key лише ідентифікує розробника-клієнта; вибірково дати доступ до нової версії ключем не можна.
- *New API Gateway API* — надмірно для раннього доступу кількох розробників.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-deploy-api.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-basic-concept.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html>

</details>

---

<a id="t11-q14"></a>
### T11-Q14
*домен: офіційна мітка*

A SaaS company runs a HealthCare web application that is used worldwide by users. There have been requests by mobile developers to expose public APIs for the application-specific functionality. You decide to make the APIs available to mobile developers as product offerings.

Which of the following options will allow you to do that?

- ▫️ Use CloudFront Usage Plans
- ▫️ Use AWS Billing Usage Plans
- ▫️ Use AWS Lambda Custom Authorizers
- ✅ Use API Gateway Usage Plans

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use API Gateway Usage Plans»**

API Gateway створює, публікує, супроводжує, моніторить і захищає REST, HTTP та WebSocket API будь-якого масштабу. Як працює: https://aws.amazon.com/api-gateway/

Usage plan визначає, хто має доступ до задеплоєних stages і методів, скільки запитів і з якою швидкістю. Клієнтів він розпізнає за **API keys** і рахує доступ окремо для кожного ключа. Так клієнти отримують доступ до вибраних API з погодженими лімітами швидкості (throttling) і квотами під свої потреби й бюджет.

Детальніше: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html

**❌ Неправильні варіанти:**
- *AWS Billing Usage Plans* — такої функції немає; Billing — це оплата рахунку, моніторинг використання і контроль витрат.
- *CloudFront Usage Plans* — такої функції немає.
- *Lambda Custom Authorizers* — authorizer контролює доступ до API, а ліміти використання не задає.

</details>

---
