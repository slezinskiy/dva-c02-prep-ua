# Amazon API Gateway / AppSync

**Домен 3 — Розгортання** · питань: 6  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t05-q18"></a>
### T05-Q18
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Development team are creating a new REST API that uses Amazon API Gateway and AWS Lambda. To support testing there need to be different versions of the service. What is the BEST way to provide multiple versions of the REST API?

- ✅ Deploy the API versions as unique stages with unique endpoints and use stage variables to provide further context
- ▫️ Deploy an HTTP Proxy integration and configure the proxy with API versions
- ▫️ Create an AWS Lambda authorizer to route API clients to the correct API version
- ▫️ Create an API Gateway resource policy to isolate versions and provide context to the Lambda functions

<details><summary>Пояснення (оригінал, EN)</summary>

A stage is a named reference to a deployment, which is a snapshot of the API. You use a Stage to manage and optimize a particular deployment. For example, you can set up stage settings to enable caching, customize request throttling, configure logging, define stage variables or attach a canary release for testing. APIs are deployed to stages:

Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates.

With deployment stages in API Gateway, you can manage multiple release stages for each API, such as alpha, beta, and production. Using stage variables you can configure an API deployment stage to interact with different backend endpoints. For example, your API can pass a GET request as an HTTP proxy to the backend web host (for example, http://example.com).

In this case, the backend web host is configured in a stage variable so that when developers call your production endpoint, API Gateway calls example.com. When you call your beta endpoint, API Gateway uses the value configured in the stage variable for the beta stage, and calls a different web host (for example, beta.example.com). Similarly, stage variables can be used to specify a different AWS Lambda function name for each stage in your API.

Therefore, for this scenario the Developers can deploy the API versions as unique stages with unique endpoints and use stage variables to provide further context such as connections to different backend services.

CORRECT: "Deploy the API versions as unique stages with unique endpoints and use stage variables to provide further context" is the correct answer.

INCORRECT: "Create an API Gateway resource policy to isolate versions and provide context to the Lambda functions" is incorrect. API Gateway resource policies are JSON policy documents that you attach to an API to control whether a specified principal (typically, an IAM user or role) can invoke the API.

INCORRECT: "Create an AWS Lambda authorizer to route API clients to the correct API version" is incorrect. A Lambda authorizer (formerly known as a custom authorizer) is an API Gateway feature that uses a Lambda function to control access to your API. This is not used for routing API clients to different versions.

INCORRECT: "Deploy an HTTP Proxy integration and configure the proxy with API versions" is incorrect. The HTTP proxy integration allows a client to access the backend HTTP endpoints with a streamlined integration setup on a single API method. This is not used for providing multiple versions of the API, use stages and stage variables instead.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t05-q51"></a>
### T05-Q51
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

> ⚠️ **API Gateway canary.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A company is releasing an updated version of its APIs for its new mobile application, which uses Amazon API Gateway. The developers aim to gradually and seamlessly roll out the new version of APIs.

What is the MOST straightforward method for them to introduce the new API version to a subset of users through API Gateway?

- ▫️ Use an Amazon Route 53 failover routing policy to divert a certain percentage of traffic to the updated API version.
- ✅ Utilize the canary release deployment feature in API Gateway. Configure the canarySettings to redirect a portion of the API traffic.
- ▫️ Develop a custom Lambda function to control the API traffic distribution between the two API versions.
- ▫️ Deploy the new API in a separate VPC and use Amazon CloudFront to distribute the API traffic between the old and new versions.

<details><summary>Пояснення (оригінал, EN)</summary>

The canary release deployment in API Gateway enables developers to roll out API changes gradually. By configuring the canarySettings, a percentage of the API traffic can be redirected to the new version, allowing for cautious and controlled rollout.

CORRECT: "Utilize the canary release deployment feature in API Gateway. Configure the canarySettings to redirect a portion of the API traffic" is the correct answer (as explained above.)

INCORRECT: "Use an Amazon Route 53 failover routing policy to divert a certain percentage of traffic to the updated API version" is incorrect.

Amazon Route 53 failover routing policy is used for routing internet traffic to a resource when the primary resource becomes unavailable. It is not a suitable solution for this scenario.

INCORRECT: "Deploy the new API in a separate VPC and use Amazon CloudFront to distribute the API traffic between the old and new versions" is incorrect.

Deploying the new API in a separate VPC and using Amazon CloudFront for traffic distribution could work, but this approach is more complex and does not provide an out-of-the-box traffic control mechanism like canary releases.

INCORRECT: "Develop a custom Lambda function to control the API traffic distribution between the two API versions" is incorrect.

Developing a custom Lambda function to control API traffic distribution can be overly complicated and might not provide the desired level of control, especially when compared to built-in solutions like canary releases.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html>

</details>

---

<a id="t05-q61"></a>
### T05-Q61
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An Amazon API Gateway API developer aims to integrate request validation in a production setting but wants to test it before deployment. Which of the following methods offers the least operational overhead for testing via a tool by sending test requests?

- ▫️ Create a new API from scratch with the necessary resources, methods, and request validation, run the tests, then modify and deploy the original API.
- ✅ Modify the existing API to include request validation, deploy this to a new API Gateway stage, test it, then deploy it to the production stage.
- ▫️ First export the current API to an OpenAPI file, create and modify a new API by importing the OpenAPI file and adding request validation, test it, then modify and deploy the original API.
- ▫️ Clone the existing API, add the request validation, run the tests, then modify the original API to include request validation before deploying to production.

<details><summary>Пояснення (оригінал, EN)</summary>

The primary concern here is reducing operational overhead. The correct answer is the most straightforward and efficient because it leverages the functionality of Amazon API Gateway stages.

By modifying the existing API to add request validation and deploying the updated API to a new API Gateway stage, it allows for testing in a controlled environment before deploying to production. It provides the least operational overhead because it does not involve creating a new API or exporting/importing an OpenAPI file.

CORRECT: "Modify the existing API to include request validation, deploy this to a new API Gateway stage, test it, then deploy it to the production stage" is the correct answer (as explained above.)

INCORRECT: "Clone the existing API, add the request validation, run the tests, then modify the original API to include request validation before deploying to production" is incorrect.

Cloning the API or exporting it to an OpenAPI file and importing into a new API both involve unnecessary steps that increase operational overhead.

INCORRECT: "First export the current API to an OpenAPI file, create and modify a new API by importing the OpenAPI file and adding request validation, test it, then modify and deploy the original API" is incorrect.

Cloning the API or exporting it to an OpenAPI file and importing into a new API both involve unnecessary steps that increase operational overhead.

INCORRECT: "Create a new API from scratch with the necessary resources, methods and request validation, run the tests, then modify and deploy the original API" is incorrect.

Creating a new API from scratch can be time-consuming and requires extra effort which again adds to operational overhead.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stages.html>

</details>

---

<a id="t07-q31"></a>
### T07-Q31
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Сумнівно: зазвичай деплоять перевірену deployment у prod-stage або промоутять canary.** → [детальніше](../../07-outdated-and-wrong-answers.md#d07)

A development team has deployed a REST API in Amazon API Gateway to two different stages - a test stage and a prod stage. The test stage is used as a test build and the prod stage as a stable build. After the updates have passed the test, the team wishes to promote the test stage to the prod stage.

Which of the following represents the optimal solution for this use-case?

- ▫️ Delete the existing prod stage. Create a new stage with the same name (prod) and deploy the tested version on this stage
- ✅ Update stage variable value from the stage name of test to that of prod
- ▫️ API performance is optimized in a different way for prod environments. Hence, promoting test to prod is not correct. The promotion should be done by redeploying the API to the prod stage
- ▫️ Deploy the API without choosing a stage. This way, the working deployment will be updated in all stages

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Update stage variable value from the stage name of test to that of prod

After creating your API, you must deploy it to make it callable by your users. To deploy an API, you create an API deployment and associate it with a stage. A stage is a logical reference to a lifecycle state of your API (for example, dev, prod, beta, v2). API stages are identified by the API ID and stage name. They're included in the URL that you use to invoke the API. Each stage is a named reference to a deployment of the API and is made available for client applications to call.

Stages enable robust version control of your API. In our current use-case, after the updates pass the test, you can promote the test stage to the prod stage. The promotion can be done by redeploying the API to the prod stage or updating a stage variable value from the stage name of test to that of prod.

Incorrect options:

Deploy the API without choosing a stage. This way, the working deployment will be updated in all stages - An API can only be deployed to a stage. Hence, it is not possible to deploy an API without choosing a stage.

Delete the existing prod stage. Create a new stage with the same name (prod) and deploy the tested version on this stage\* - This is possible, but not an optimal way of deploying a change. Also, as prod refers to real production system, this option will result in downtime.

API performance is optimized in a different way for prod environments. Hence, promoting test to prod is not correct. The promotion should be done by redeploying the API to the prod stage - For each stage, you can optimize API performance by adjusting the default account-level request throttling limits and enabling API caching. And these settings can be changed/updated at any time.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-deploy-api.html

</details>

---

<a id="t08-q41"></a>
### T08-Q41
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company is creating a REST service using an Amazon API Gateway with AWS Lambda integration. The service must run different versions for testing purposes.

What would be the BEST way to accomplish this?

- ▫️ Create an API Gateway Lambda authorizer to route API clients to the correct API version
- ▫️ Create an API Gateway resource policy to isolate versions and provide context to the Lambda function(s)
- ✅ Deploy the API version as unique stages with unique endpoints and use stage variables to provide further context
- ▫️ Use an X-Version header to denote which version is being called and pass that header to the Lambda function(s)

<details><summary>Пояснення (оригінал, EN)</summary>

A stage is a named reference to a deployment, which is a snapshot of the API. You use a Stage to manage and optimize a particular deployment. For example, you can configure stage settings to enable caching, customize request throttling, configure logging, define stage variables, or attach a canary release for testing.

Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates.

With stages and stage variables, you can configure different settings for different versions of the application and point to different versions of your Lambda function.

CORRECT: "Deploy the API version as unique stages with unique endpoints and use stage variables to provide further context" is the correct answer.

INCORRECT: "Use an X-Version header to denote which version is being called and pass that header to the Lambda function(s)" is incorrect as you cannot pass a value in a header to a Lambda function and have that determine which version is executed. Versions have unique ARNs and must be connected to separately.

INCORRECT: "Create an API Gateway Lambda authorizer to route API clients to the correct API version" is incorrect as a Lambda authorizer is used for authentication, and different versions of an API are created using stages.

INCORRECT: "Create an API Gateway resource policy to isolate versions and provide context to the Lambda function(s)" is incorrect as resource policies are not used to isolate versions or provide context. In this scenario, stages and stage variables should be used.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t12-q22"></a>
### T12-Q22
*⏭ пропущено · домен: офіційна мітка*

A developer is creating a RESTful API service using an Amazon API Gateway with AWS Lambda integration. The service must support different API versions for testing purposes.

As a Developer Associate, which of the following would you suggest as the best way to accomplish this?

- ▫️ Use an X-Version header to identify which version is being called and pass that header to the Lambda function
- ▫️ Set up an API Gateway resource policy to identify the API versions and provide context to the Lambda function
- ▫️ Use an API Gateway Lambda authorizer to route API clients to the correct API version
- ✅ Deploy the API versions as unique stages with unique endpoints and use stage variables to provide the context to identify the API versions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Deploy the API versions as unique stages with unique endpoints and use stage variables to provide the context to identify the API versions - A stage is a named reference to a deployment, which is a snapshot of the API. You use a stage to manage and optimize a particular deployment. For example, you can configure stage settings to enable caching, customize request throttling, configure logging, define stage variables, or attach a canary release for testing.

Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates.

With deployment stages in API Gateway, you can manage multiple release stages for each API, such as alpha, beta, and production. Using stage variables you can configure an API deployment stage to interact with different backend endpoints.

For example, your API can pass a GET request as an HTTP proxy to the backend web host (for example, http://example.com). In this case, the backend web host is configured in a stage variable so that when developers call your production endpoint, API Gateway calls example.com. When you call your beta endpoint, API Gateway uses the value configured in the stage variable for the beta stage, and calls a different web host (for example, beta.example.com). Similarly, stage variables can be used to specify a different AWS Lambda function name for each stage in your API.

Incorrect options:

Use an X-Version header to identify which version is being called and pass that header to the Lambda function - This is an incorrect option and has been added as a distractor.

Use an API Gateway Lambda authorizer to route API clients to the correct API version - A Lambda authorizer is an API Gateway feature that uses a Lambda function to control access to your API. A Lambda authorizer is useful if you want to implement a custom authorization scheme that uses a bearer token authentication strategy such as OAuth or SAML, or that uses request parameters to determine the caller's identity.

Set up an API Gateway resource policy to identify the API versions and provide context to the Lambda function - Amazon API Gateway resource policies are JSON policy documents that you attach to an API to control whether a specified principal (typically an IAM user or role) can invoke the API. You can use API Gateway resource policies to allow your API to be securely invoked requestors. They are not meant for choosing the version of APIs.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-resource-policies.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html>

</details>

---
