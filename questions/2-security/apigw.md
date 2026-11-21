# Amazon API Gateway / AppSync

**Домен 2 — Безпека** · питань: 4  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q02"></a>
### T01-Q02
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **STS не є механізмом авторизації API Gateway.** → [детальніше](../../06-contradictions-and-traps.md#c20)

You've just deployed an AWS Lambda function. The lambda function will be invoked via the API Gateway. The API Gateway will need to control access to it.

Which of the following mechanisms is not supported for API Gateway?

- ✅ STS
- ▫️ IAM permissions with sigv4
- ▫️ Cognito User Pools
- ▫️ Lambda Authorizer

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon API Gateway is an AWS service for creating, publishing, maintaining, monitoring, and securing REST, HTTP, and WebSocket APIs at any scale. API developers can create APIs that access AWS or other web services, as well as data stored in the AWS Cloud.

How API Gateway Works:  via - https://aws.amazon.com/api-gateway/

STS

The AWS Security Token Service (STS) is a web service that enables you to request temporary, limited-privilege credentials for AWS Identity and Access Management (IAM) users or for users that you authenticate (federated users). However, is not supported at the time with API Gateway.

Incorrect options:

IAM permissions with sigv4 - They can be applied to an entire API or individual methods.

Lambda Authorizer - Control access to REST API methods using bearer token authentication as well as information described by headers, paths, query strings, stage variables, or context variables request parameter.

Cognito User Pools - Use Cognito User Pools to create customizable authentication and authorization solutions for your REST APIs.

Reference:

https://aws.amazon.com/api-gateway/

</details>

---

<a id="t05-q53"></a>
### T05-Q53
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company wants to implement authentication for its new REST service using Amazon API Gateway. To authenticate the calls, each request must include HTTP headers with a client ID and user ID. These credentials must be compared to authentication data in an Amazon DynamoDB table.

What MUST the company do to implement this authentication in API Gateway?

- ▫️ Implement an Amazon Cognito authorizer that references the DynamoDB authentication table
- ✅ Implement an AWS Lambda authorizer that references the DynamoDB authentication table
- ▫️ Modify the integration requests to require the credentials, then grant API Gateway access to the authentication table
- ▫️ Create a model that requires the credentials, then grant API Gateway access to the authentication table

<details><summary>Пояснення (оригінал, EN)</summary>

A Lambda authorizer (formerly known as a custom authorizer) is an API Gateway feature that uses a Lambda function to control access to your API.

A Lambda authorizer is useful if you want to implement a custom authorization scheme that uses a bearer token authentication strategy such as OAuth or SAML, or that uses request parameters to determine the caller's identity.

When a client makes a request to one of your API's methods, API Gateway calls your Lambda authorizer, which takes the caller's identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

• A token-based Lambda authorizer (also called a TOKEN authorizer) receives the caller's identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.

• A request parameter-based Lambda authorizer (also called a REQUEST authorizer) receives the caller's identity in a combination of headers, query string parameters, stageVariables, and $context variables.

• For WebSocket APIs, only request parameter-based authorizers are supported.

In this scenario, the authentication is using headers in the request and therefore the request parameter-based Lambda authorizer should be used.

CORRECT: "Implement an AWS Lambda authorizer that references the DynamoDB authentication table" is the correct answer.

INCORRECT: "Create a model that requires the credentials, then grant API Gateway access to the authentication table" is incorrect as a model defines the structure of the incoming payload using the JSON Schema.

INCORRECT: "Modify the integration requests to require the credentials, then grant API Gateway access to the authentication table" is incorrect as API Gateway will not authorize directly using the table information, an authorizer should be used.

INCORRECT: "Implement an Amazon Cognito authorizer that references the DynamoDB authentication table" is incorrect as a Lambda authorizer should be used in this example as the authentication data is being passed in request headers.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html>

</details>

---

<a id="t07-q06"></a>
### T07-Q06
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Сумнівно: CORS — не захист від клієнтів поза браузером.** → [детальніше](../../07-outdated-and-wrong-answers.md#d05)

You team maintains a public API Gateway that is accessed by clients from another domain. Usage has been consistent for the last few months but recently it has more than doubled. As a result, your costs have gone up and would like to prevent other unauthorized domains from accessing your API.

Which of the following actions should you take?

- ▫️ Use Account-level throttling
- ▫️ Assign a Security Group to your API Gateway
- ▫️ Use Mapping Templates
- ✅ Restrict access by using CORS

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Restrict access by using CORS - Cross-origin resource sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. When your API's resources receive requests from a domain other than the API's own domain and you want to restrict servicing these requests, you must disable cross-origin resource sharing (CORS) for selected methods on the resource.

Incorrect options:

Use Account-level throttling - To prevent your API from being overwhelmed by too many requests, Amazon API Gateway throttles requests to your API. By default, API Gateway limits the steady-state request rate to 10,000 requests per second (rps). It limits the burst (that is, the maximum bucket size) to 5,000 requests across all APIs within an AWS account. This is Account-level throttling. As you see, this is about limit on the number of requests and is not a suitable answer for the current scenario.

Use Mapping Templates - A mapping template is a script expressed in Velocity Template Language (VTL) and applied to the payload using JSONPath expressions. Mapping templates help format/structure the data in a way that it is easily readable, unlike a server response that might always be easy to ready. Mapping Templates have nothing to do with access and are not useful for the current scenario.

Assign a Security Group to your API Gateway - API Gateway does not use security groups but uses resource policies, which are JSON policy documents that you attach to an API to control whether a specified principal (typically an IAM user or role) can invoke the API. You can restrict IP address using this, the downside being, an IP address can be changed by the accessing user. So, this is not an optimal solution for the current use case.

Джерела: <https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-protect.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/rest-api-data-transformations.html>

</details>

---

<a id="t09-q62"></a>
### T09-Q62
*⏭ пропущено · домен: офіційна мітка*

A university has created a student portal that is accessible through a smartphone app and web application. The smartphone app is available in both Android and IOS and the web application works on most major browsers. Students will be able to do group study online and create forum questions. All changes made via smartphone devices should be available even when offline and should synchronize with other devices.

Which of the following AWS services will meet these requirements?

- ✅ AWS AppSync
- ▫️ Cognito Identity Pools
- ▫️ Cognito User Pools
- ▫️ BeanStalk

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS AppSync

AWS AppSync is a managed GraphQL service designed for building real-time, collaborative applications with features like offline data access, conflict resolution, and device synchronization. With AppSync, mobile apps can continue to work offline, queue changes locally, and then sync seamlessly across users and devices when connectivity returns. It also integrates with Cognito for authentication and DynamoDB or Aurora for storage, making it ideal for the portal’s real-time group study and Q&A features.

Incorrect options:

Cognito Identity Pools - You can use Identity pools to grant your users access to other AWS services. With an identity pool, your users can obtain temporary AWS credentials to access AWS services, such as Amazon S3 and DynamoDB. Identity pools support anonymous guest users, as well as the specific identity providers that you can use to authenticate users for identity pools.

Cognito User Pools - A Cognito user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito, or federate through a third-party identity provider (IdP). Whether your users sign-in directly or through a third party, all members of the user pool have a directory profile that you can access through an SDK.

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Beanstalk - With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.

How Elastic BeanStalk Works:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html

Reference:

https://aws.amazon.com/appsync/

</details>

---
