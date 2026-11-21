# Amazon API Gateway / AppSync

**Домен 3 — Розгортання** · питань: 6  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t05-q18"></a>
### T05-Q18
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Development team are creating a new REST API that uses Amazon API Gateway and AWS Lambda. To support testing there need to be different versions of the service. What is the BEST way to provide multiple versions of the REST API?

- ✅ Deploy the API versions as unique stages with unique endpoints and use stage variables to provide further context
- ▫️ Deploy an HTTP Proxy integration and configure the proxy with API versions
- ▫️ Create an AWS Lambda authorizer to route API clients to the correct API version
- ▫️ Create an API Gateway resource policy to isolate versions and provide context to the Lambda functions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy the API versions as unique stages with unique endpoints and use stage variables to provide further context»**

Stage — це іменоване посилання на deployment, тобто знімок API. На рівні stage налаштовують кешування, throttling, логування, stage variables і canary-релізи.

Stage variables — пари «ім'я–значення», прив'язані до stage REST API. Вони працюють як змінні середовища в налаштуваннях інтеграції та mapping templates. Через них кожен stage (alpha, beta, prod) може звертатися до свого бекенду. Наприклад, prod викликає example.com, а beta — beta.example.com. Так само можна вказати різні імена Lambda-функцій для різних stages.

Отже, версії API деплоять як окремі stages з окремими endpoints, а stage variables задають контекст, наприклад підключення до різних бекендів.

**❌ Неправильні варіанти:**
- *Resource policy* — resource policy визначає, хто може викликати API; версій вона не ізолює.
- *Lambda authorizer to route clients* — authorizer контролює доступ і не маршрутизує запити.
- *HTTP Proxy integration with versions* — HTTP proxy спрощує доступ до HTTP-бекенду в одному методі, але не дає кількох версій API; для цього потрібні stages і stage variables.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t05-q51"></a>
### T05-Q51
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

> ⚠️ **API Gateway canary.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A company is releasing an updated version of its APIs for its new mobile application, which uses Amazon API Gateway. The developers aim to gradually and seamlessly roll out the new version of APIs.

What is the MOST straightforward method for them to introduce the new API version to a subset of users through API Gateway?

- ▫️ Use an Amazon Route 53 failover routing policy to divert a certain percentage of traffic to the updated API version.
- ✅ Utilize the canary release deployment feature in API Gateway. Configure the canarySettings to redirect a portion of the API traffic.
- ▫️ Develop a custom Lambda function to control the API traffic distribution between the two API versions.
- ▫️ Deploy the new API in a separate VPC and use Amazon CloudFront to distribute the API traffic between the old and new versions.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Utilize the canary release deployment feature in API Gateway. Configure the canarySettings to redirect a portion of the API traffic.»**

Canary release в API Gateway дає поступово викочувати зміни API. У `canarySettings` задають відсоток трафіку, який іде на нову версію, тож розгортання контрольоване й обережне.

**❌ Неправильні варіанти:**
- *Route 53 failover routing* — перемикає трафік на резерв, коли основний ресурс недоступний; відсоток трафіку не ділить.
- *Separate VPC + CloudFront* — можливо, але складно, і немає готового механізму керування трафіком.
- *Custom Lambda for traffic distribution* — надто складно й гірше керовано, ніж вбудований canary.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html>

</details>

---

<a id="t05-q61"></a>
### T05-Q61
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An Amazon API Gateway API developer aims to integrate request validation in a production setting but wants to test it before deployment. Which of the following methods offers the least operational overhead for testing via a tool by sending test requests?

- ▫️ Create a new API from scratch with the necessary resources, methods, and request validation, run the tests, then modify and deploy the original API.
- ✅ Modify the existing API to include request validation, deploy this to a new API Gateway stage, test it, then deploy it to the production stage.
- ▫️ First export the current API to an OpenAPI file, create and modify a new API by importing the OpenAPI file and adding request validation, test it, then modify and deploy the original API.
- ▫️ Clone the existing API, add the request validation, run the tests, then modify the original API to include request validation before deploying to production.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Modify the existing API to include request validation, deploy this to a new API Gateway stage, test it, then deploy it to the production stage.»**

Головна вимога — мінімальні операційні витрати. Додати валідацію запитів до наявного API, задеплоїти на новий stage, протестувати, а потім задеплоїти на продакшн-stage — найпростіший і найефективніший шлях. Stages дають ізольоване середовище для тестування, і не потрібно створювати новий API чи експортувати/імпортувати OpenAPI.

**❌ Неправильні варіанти:**
- *Clone the API* — зайві кроки й більші операційні витрати.
- *Export to OpenAPI, import as new API* — зайві кроки й більші операційні витрати.
- *New API from scratch* — довго й трудомістко.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stages.html>

</details>

---

<a id="t07-q31"></a>
### T07-Q31
*домен: офіційна мітка*

> ⚠️ **Сумнівно: зазвичай деплоять перевірену deployment у prod-stage або промоутять canary.** → [детальніше](../../07-outdated-and-wrong-answers.md#d07)

A development team has deployed a REST API in Amazon API Gateway to two different stages - a test stage and a prod stage. The test stage is used as a test build and the prod stage as a stable build. After the updates have passed the test, the team wishes to promote the test stage to the prod stage.

Which of the following represents the optimal solution for this use-case?

- ▫️ Delete the existing prod stage. Create a new stage with the same name (prod) and deploy the tested version on this stage
- ✅ Update stage variable value from the stage name of test to that of prod
- ▫️ API performance is optimized in a different way for prod environments. Hence, promoting test to prod is not correct. The promotion should be done by redeploying the API to the prod stage
- ▫️ Deploy the API without choosing a stage. This way, the working deployment will be updated in all stages

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Update stage variable value from the stage name of test to that of prod»**

Щоб API можна було викликати, його деплоять: створюють deployment і прив'язують до stage. Stage — це стан API в життєвому циклі (dev, prod, beta, v2). Він визначається ID API та ім'ям stage, входить в URL виклику і вказує на конкретний deployment.

Stages дають контроль версій. Коли оновлення пройдуть тести, test просувають у prod: повторно деплоять API на prod або змінюють значення stage variable з імені test на prod.

⚠️ Формулювання ключа дивне. На практиці «промоут» — це повторний деплой того самого deployment на stage prod. Див. 07 (d07).

**❌ Неправильні варіанти:**
- *Deploy without choosing a stage* — API деплоять лише на stage.
- *Delete prod stage and recreate* — можливо, але не оптимально і призведе до простою продакшну.
- *Prod is optimized differently* — throttling і кешування налаштовуються для кожного stage і змінюються будь-коли; промоуту це не заважає.

</details>

---

<a id="t08-q41"></a>
### T08-Q41
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company is creating a REST service using an Amazon API Gateway with AWS Lambda integration. The service must run different versions for testing purposes.

What would be the BEST way to accomplish this?

- ▫️ Create an API Gateway Lambda authorizer to route API clients to the correct API version
- ▫️ Create an API Gateway resource policy to isolate versions and provide context to the Lambda function(s)
- ✅ Deploy the API version as unique stages with unique endpoints and use stage variables to provide further context
- ▫️ Use an X-Version header to denote which version is being called and pass that header to the Lambda function(s)

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy the API version as unique stages with unique endpoints and use stage variables to provide further context»**

Stage — іменоване посилання на deployment, тобто знімок API. На рівні stage налаштовують кешування, throttling, логування, stage variables і canary. Stage variables — пари «ім'я–значення», що працюють як змінні середовища в налаштуваннях і mapping templates. Через них кожна версія API отримує свої налаштування і викликає свою версію чи alias Lambda.

**❌ Неправильні варіанти:**
- *X-Version header* — заголовок не визначає, яка версія Lambda виконується: кожна версія має свій ARN, і на неї вказують окремо.
- *Lambda authorizer for routing* — authorizer для автентифікації, а версії API роблять через stages.
- *Resource policy* — не ізолює версії й не дає контексту.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t12-q22"></a>
### T12-Q22
*домен: офіційна мітка*

A developer is creating a RESTful API service using an Amazon API Gateway with AWS Lambda integration. The service must support different API versions for testing purposes.

As a Developer Associate, which of the following would you suggest as the best way to accomplish this?

- ▫️ Use an X-Version header to identify which version is being called and pass that header to the Lambda function
- ▫️ Set up an API Gateway resource policy to identify the API versions and provide context to the Lambda function
- ▫️ Use an API Gateway Lambda authorizer to route API clients to the correct API version
- ✅ Deploy the API versions as unique stages with unique endpoints and use stage variables to provide the context to identify the API versions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy the API versions as unique stages with unique endpoints and use stage variables to provide the context to identify the API versions»**

Stage — іменоване посилання на deployment (знімок API). На ньому налаштовують кешування, throttling, логування, stage variables і canary. Stage variables — пари «ім'я–значення», що працюють як змінні середовища в налаштуваннях і mapping templates.

Через stages API має кілька версій (alpha, beta, production), і кожна викликає свій бекенд. Наприклад, у prod stage variable вказує на `example.com`, а в beta — на `beta.example.com`. Так само можна задати різні Lambda-функції.

**❌ Неправильні варіанти:**
- *X-Version header* — вигаданий механізм.
- *Lambda authorizer for routing* — authorizer контролює доступ (bearer token, OAuth, SAML, параметри запиту), а не маршрутизує.
- *Resource policy* — визначає, хто може викликати API; версій не вибирає.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-resource-policies.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html>

</details>

---
