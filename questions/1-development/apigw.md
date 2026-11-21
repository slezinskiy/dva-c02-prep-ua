# Amazon API Gateway / AppSync

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 19  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q16"></a>
### T02-Q16
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company runs a legacy application that uses an XML-based SOAP interface. The company needs to expose the functionality of the service to external customers and plans to use Amazon API Gateway.

How can a Developer configure the integration?

- ▫️ Create a RESTful API using Amazon API Gateway. Pass the incoming JSON to the SOAP interface through an Application Load Balancer.
- ✅ Create a RESTful API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates.
- ▫️ Create a SOAP API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using AWS Lambda.
- ▫️ Create a SOAP API using Amazon API Gateway. Pass the incoming JSON to the SOAP interface through a Network Load Balancer.

<details><summary>Пояснення (оригінал, EN)</summary>

In API Gateway, an API's method request can take a payload in a different format from the corresponding integration request payload, as required in the backend. Similarly, the backend may return an integration response payload different from the method response payload, as expected by the frontend.

API Gateway lets you use mapping templates to map the payload from a method request to the corresponding integration request and from an integration response to the corresponding method response.

CORRECT: "Create a RESTful API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates" is the correct answer.

INCORRECT: "Create a RESTful API using Amazon API Gateway. Pass the incoming JSON to the SOAP interface through an Application Load Balancer" is incorrect. The API Gateway cannot process the XML SOAP data and cannot pass it through an ALB.

INCORRECT: "Create a SOAP API using Amazon API Gateway. Transform the incoming JSON into a valid XML message for the SOAP interface using AWS Lambda" is incorrect. API Gateway does not support SOAP APIs.

INCORRECT: "Create a SOAP API using Amazon API Gateway. Pass the incoming JSON to the SOAP interface through a Network Load Balancer" is incorrect. API Gateway does not support SOAP APIs.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/Developerguide/request-response-data-mappings.html>

</details>

---

<a id="t02-q24"></a>
### T02-Q24
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company has created a set of APIs using Amazon API Gateway and exposed them to partner companies. The APIs have caching enabled for all stages. The partners require a method of invalidating the cache that they can build into their applications.

What can the partners use to invalidate the API cache?

- ▫️ They must wait for the TTL to expire
- ✅ They can pass the HTTP header Cache-Control: max-age=0
- ▫️ They can use the query string parameter INVALIDATE_CACHE
- ▫️ They can invoke an AWS API endpoint which invalidates the cache

<details><summary>Пояснення (оригінал, EN)</summary>

You can enable API caching in Amazon API Gateway to cache your endpoint's responses. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of requests to your API.

When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified time-to-live (TTL) period, in seconds. API Gateway then responds to the request by looking up the endpoint response from the cache instead of making a request to your endpoint. The default TTL value for API caching is 300 seconds. The maximum TTL value is 3600 seconds. TTL=0 means caching is disabled.

A client of your API can invalidate an existing cache entry and reload it from the integration endpoint for individual requests. The client must send a request that contains the Cache-Control: max-age=0 header.

The client receives the response directly from the integration endpoint instead of the cache, provided that the client is authorized to do so. This replaces the existing cache entry with the new response, which is fetched from the integration endpoint.

To grant permission for a client, attach a policy of the following format to an IAM execution role for the user.

This policy allows the API Gateway execution service to invalidate the cache for requests on the specified resource (or resources).

Therefore, as described above the solution is to get the partners to pass the HTTP header Cache-Control: max-age=0.

CORRECT: "They can pass the HTTP header Cache-Control: max-age=0" is the correct answer.

INCORRECT: "They can use the query string parameter INVALIDATE_CACHE" is incorrect. This is not a valid method of invalidating the cache with API Gateway.

INCORRECT: "They must wait for the TTL to expire" is incorrect as this is not true, you do not need to wait as you can pass the HTTP header Cache-Control: max-age=0 whenever you need to in order to invalidate the cache.

INCORRECT: "They can invoke an AWS API endpoint which invalidates the cache" is incorrect. This is not a valid method of invalidating the cache with API Gateway.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t02-q37"></a>
### T02-Q37
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A customer requires a serverless application with an API which mobile clients will use. The API will have both and AWS Lambda function and an Amazon DynamoDB table as data sources. Responses that are sent to the mobile clients must contain data that is aggregated from both of these data sources.

The developer must minimize the number of API endpoints and must minimize the number of API calls that are required to retrieve the necessary data.

Which solution should the developer use to meet these requirements?

- ❌ REST API on Amazon API Gateway
- ▫️ GraphQL API on an Amazon EC2 instance
- ▫️ REST API on AWS Elastic Beanstalk
- ✅ GraphQL API on AWS AppSync

<details><summary>Пояснення (оригінал, EN)</summary>

GraphQL APIs built with AWS AppSync give front-end developers the ability to query multiple databases, microservices, and APIs from a single GraphQL endpoint. This would not be possible with a REST API running on API Gateway which would have a single target for each API endpoint.

The example diagram below depicts a solution that includes AWS AppSync in front of mobile clients. The services then connect to Lambda and DynamoDB via API Gateway and AppSync.

CORRECT: "GraphQL API on AWS AppSync" is the correct answer (as explained above.)

INCORRECT: "REST API on Amazon API Gateway" is incorrect.

A REST API is not suitable as the question asks to reduce the number of API endpoints. With a REST API there is a single target such as a Lambda per API endpoint so more endpoints would be required.

INCORRECT: "GraphQL API on an Amazon EC2 instance" is incorrect.

This would not be a serverless solution and the question states that the solution must be serverless.

INCORRECT: "REST API on AWS Elastic Beanstalk" is incorrect.

As explained above.

Джерела: <https://aws.amazon.com/blogs/mobile/appsync-microservices/>

</details>

---

<a id="t02-q46"></a>
### T02-Q46
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A startup is developing a prototype for a news aggregator application. This application will display the latest news for a specific industry and provide a RESTful API endpoint that clients can invoke. Where feasible, the application should leverage AWS's caching features to reduce the load on the backend service. The backend of the application is expected to handle a modest amount of traffic, primarily during testing periods.

Which method would be the most cost-effective for the developer to implement this REST endpoint?

- ❌ Launch an Amazon EC2 instance, host the application on it, and employ Amazon ElastiCache for caching purposes.
- ▫️ Construct an Amazon ECS service for the application, using Amazon ElastiCache for caching.
- ▫️ Deploy an AWS Elastic Beanstalk application and use Amazon DynamoDB for caching services.
- ✅ Utilize AWS API Gateway with an AWS Lambda function as the backend and enable caching in API Gateway.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS API Gateway, combined with AWS Lambda, offers a serverless solution where costs are directly related to usage. API Gateway also provides a caching feature which can help reduce the load on the backend.

CORRECT: "Utilize AWS API Gateway with an AWS Lambda function as the backend and enable caching in API Gateway" is the correct answer (as explained above.)

INCORRECT: "Launch an Amazon EC2 instance, host the application on it, and employ Amazon ElastiCache for caching purposes" is incorrect.

Using an Amazon EC2 instance would incur constant costs, regardless of whether the instance is in use or idle. Additionally, employing AWS ElastiCache could increase the costs unnecessarily, especially for a prototype application.

INCORRECT: "Construct an Amazon ECS service for the application, using Amazon ElastiCache for caching" is incorrect.

Amazon ECS is overkill for a simple prototype application like this. It requires more resources and management than a serverless solution.

INCORRECT: "Deploy an AWS Elastic Beanstalk application and use Amazon DynamoDB for caching services" is incorrect.

While AWS Elastic Beanstalk simplifies application deployment, it does not inherently provide caching capabilities. Amazon DynamoDB is a database service, not a caching service, making it unsuitable for this requirement.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t03-q18"></a>
### T03-Q18
*⏭ пропущено · домен: офіційна мітка*

A financial services company with over 10,000 employees has hired you as the new Senior Developer. Initially caching was enabled to reduce the number of calls made to all API endpoints and improve the latency of requests to the company’s API Gateway.

For testing purposes, you would like to invalidate caching for the API clients to get the most recent responses. Which of the following should you do?

- ▫️ Using the Header Bypass-Cache=1
- ▫️ Using the request parameter ?cache-control-max-age=0
- ▫️ Use the Request parameter: ?bypass_cache=1
- ✅ Using the Header Cache-Control: max-age=0

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Using the Header Cache-Control: max-age=0

A client of your API can invalidate an existing cache entry and reload it from the integration endpoint for individual requests. The client must send a request that contains the Cache-Control: max-age=0 header. The client receives the response directly from the integration endpoint instead of the cache, provided that the client is authorized to do so. This replaces the existing cache entry with the new response, which is fetched from the integration endpoint.

 via - https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html#invalidate-method-caching

Incorrect options:

Use the Request parameter: ?bypass_cache=1 - Method parameters take query string but this is not one of them.

Using the Header Bypass-Cache=1 - This is a made-up option.

Using the request parameter ?cache-control-max-age=0 - To invalidate cache it requires a header and not a request parameter.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html#invalidate-method-caching

</details>

---

<a id="t05-q37"></a>
### T05-Q37
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A legacy service has an XML-based SOAP interface. The Developer wants to expose the functionality of the service to external clients with the Amazon API Gateway. Which technique will accomplish this?

- ▫️ Create a RESTful API with the API Gateway; pass the incoming XML to the SOAP interface through an Application Load Balancer
- ✅ Create a RESTful API with the API Gateway; transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates
- ▫️ Create a RESTful API with the API Gateway; pass the incoming JSON to the SOAP interface through an Application Load Balancer
- ▫️ Create a RESTful API with the API Gateway; transform the incoming XML into a valid message for the SOAP interface using mapping templates

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon API Gateway is an AWS service for creating, publishing, maintaining, monitoring, and securing REST and WebSocket APIs at any scale. API developers can create APIs that access AWS or other web services as well as data stored in the AWS Cloud.

In API Gateway, an API's method request can take a payload in a different format from the corresponding integration request payload, as required in the backend. Similarly, the backend may return an integration response payload different from the method response payload, as expected by the frontend.

API Gateway lets you use mapping templates to map the payload from a method request to the corresponding integration request and from an integration response to the corresponding method response.

If an existing legacy service returns XML-style data, you can use the API Gateway to transform the output to JSON as part of your modernization effort. The API Gateway can be configured to transform the output of legacy services from XML to JSON, allowing them to make a move that is seamless and non-disruptive. The transformation is specified using JSON-Schema.

Therefore, the technique the Developer should use is to create a RESTful API with the API Gateway and transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates.

CORRECT: "Create a RESTful API with the API Gateway; transform the incoming JSON into a valid XML message for the SOAP interface using mapping templates" is the correct answer.

INCORRECT: "Create a RESTful API with the API Gateway; pass the incoming JSON to the SOAP interface through an Application Load Balancer" is incorrect as we don’t need an ALB to do this, we can use a mapping template within the API Gateway which will be more cost-efficient.

INCORRECT: "Create a RESTful API with the API Gateway; pass the incoming XML to the SOAP interface through an Application Load Balancer" is incorrect as the incoming data will be JSON, not XML as the Developer needs to publish a modern application interface. A mapping template should also be used in place of the ALB.

INCORRECT: "Create a RESTful API with the API Gateway; transform the incoming XML into a valid message for the SOAP interface using mapping templates" is incorrect as the incoming data will be JSON, not XML as the Developer needs to publish a modern application interface.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/models-mappings.html>

</details>

---

<a id="t06-q13"></a>
### T06-Q13
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer is building a WebSocket API using Amazon API Gateway. The payload sent to this API is JSON that includes an action key which can have multiple values. The Developer must integrate with different routes based on the value of the action key of the incoming JSON payload.

How can the Developer accomplish this task with the LEAST amount of configuration?

- ▫️ Create a separate stage for each possible value of the action key.
- ▫️ Create a mapping template to map the action key to an integration request.
- ▫️ Set the value of the route selection expression to $default.
- ✅ Set the value of the route selection expression to $request.body.action.

<details><summary>Пояснення (оригінал, EN)</summary>

In your WebSocket API, incoming JSON messages are directed to backend integrations based on routes that you configure. (Non-JSON messages are directed to a $default route that you configure.)

A route includes a route key, which is the value that is expected once a route selection expression is evaluated. The routeSelectionExpression is an attribute defined at the API level. It specifies a JSON property that is expected to be present in the message payload.

For example, if your JSON messages contain an action property and you want to perform different actions based on this property, your route selection expression might be ${request.body.action}. Your routing table would specify which action to perform by matching the value of the action property against the custom route key values that you have defined in the table.

CORRECT: "Set the value of the route selection expression to $request.body.action" is the correct answer.

INCORRECT: "Create a separate stage for each possible value of the action key" is incorrect. There is no need to create separate stages, the action key can be used for routing as described above.

INCORRECT: "Create a mapping template to map the action key to an integration request" is incorrect. Mapping templates are not used for routing to different integrations, they are used for transforming data.

INCORRECT: "Set the value of the route selection expression to $default" is incorrect. The $default route is used for routing non-JSON messages.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/Developerguide/websocket-api-develop-routes.html>

</details>

---

<a id="t06-q42"></a>
### T06-Q42
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Storage)*

A Developer implemented a static website hosted in Amazon S3 that makes web service requests hosted in Amazon API Gateway and AWS Lambda. The site is showing an error that reads:

“No ‘Access-Control-Allow-Origin’ header is present on the requested resource. Origin ‘null’ is therefore not allowed access.”

What should the Developer do to resolve this issue?

- ▫️ Add the Access-Control-Request-Method header to the request
- ▫️ Add the Access-Control-Request-Headers header to the request
- ❌ Enable cross-origin resource sharing (CORS) on the S3 bucket
- ✅ Enable cross-origin resource sharing (CORS) for the method in API Gateway

<details><summary>Пояснення (оригінал, EN)</summary>

Cross-origin resource sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. In this scenario the S3 bucket is the requestor and is requesting access to resources served by Amazon API Gateway and AWS Lambda. Therefore, the CORS configuration must be enabled on the requested endpoint which is the method in API Gateway.

CORRECT: "Enable cross-origin resource sharing (CORS) for the method in API Gateway" is the correct answer.

INCORRECT: "Enable cross-origin resource sharing (CORS) on the S3 bucket" is incorrect as CORS must be enabled on the requested endpoint which is API Gateway, not S3.

INCORRECT: "Add the Access-Control-Request-Method header to the request" is incorrect as this is a request header value that asks permission to use a specific HTTP method.

INCORRECT: "Add the Access-Control-Request-Headers header to the request" is incorrect as this notifies a server what headers will be sent in a request.

Джерела: <https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html>

</details>

---

<a id="t06-q53"></a>
### T06-Q53
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A new application will be hosted on the domain name dctlabs.com using an Amazon API Gateway REST API front end. The Developer needs to configure the API with a path to dctlabs.com/products that will be accessed using the HTTP GET verb. How MUST the Developer configure the API? (Select TWO

- ▫️ Create a GET resource
- ✅ Create a GET method
- ▫️ Create a /GET method
- ✅ Create a /products resource
- ▫️ Create a /products method

<details><summary>Пояснення (оригінал, EN)</summary>

An API Gateway REST API is a collection of HTTP resources and methods that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services. You can deploy this collection in one or more stages. Typically, API resources are organized in a resource tree according to the application logic. Each API resource can expose one or more API methods that have unique HTTP verbs supported by API Gateway.

As you can see from the image above, the Developer would need to create a resource which in this case would be /products. The Developer would then create a GET method within the resource.

CORRECT: "Create a /products resource" is a correct answer.

CORRECT: "Create a GET method" is a correct answer.

INCORRECT: "Create a /products method" is incorrect as a resource should be created.

INCORRECT: "Create a GET resource" is incorrect as a method should be created.

INCORRECT: "Create a /GET method" is incorrect as a method is not preceded by a slash.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-basic-concept.html>

</details>

---

<a id="t06-q64"></a>
### T06-Q64
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company maintains a REST API service using Amazon API Gateway with native API key validation. The company recently launched a new registration page, which allows users to sign up for the service. The registration page creates a new API key using CreateApiKey and sends the new key to the user. When the user attempts to call the API using this key, the user receives a 403 Forbidden error. Existing users are unaffected and can still call the API.

What code updates will grant these new users’ access to the API?

- ▫️ The importApiKeys method must be called to import all newly created API keys into the current stage of the API
- ✅ The createUsagePlanKey method must be called to associate the newly created API key with the correct usage plan
- ▫️ The createDeployment method must be called so the API can be redeployed to include the newly created API key
- ❌ The updateAuthorizer method must be called to update the API’s authorizer to include the newly created API key

<details><summary>Пояснення (оригінал, EN)</summary>

A usage plan specifies who can access one or more deployed API stages and methods—and also how much and how fast they can access them. The plan uses API keys to identify API clients and meters access to the associated API stages for each key. It also lets you configure throttling limits and quota limits that are enforced on individual client API keys.

API keys are alphanumeric string values that you distribute to application developer customers to grant access to your API. You can use API keys together with usage plans or Lambda authorizers to control access to your APIs. API Gateway can generate API keys on your behalf, or you can import them from a CSV file. You can generate an API key in API Gateway, or import it into API Gateway from an external source.

To associate the newly created key with a usage plan the CreatUsagePlanKey API can be called. This creates a usage plan key for adding an existing API key to a usage plan.

CORRECT: "The createUsagePlanKey method must be called to associate the newly created API key with the correct usage plan" is the correct answer.

INCORRECT: "The createDeployment method must be called so the API can be redeployed to include the newly created API key" is incorrect as you do not need to redeploy an API to a stage in order to associate an API key.

INCORRECT: "The updateAuthorizer method must be called to update the API’s authorizer to include the newly created API key" is incorrect as this updates and authorizer resource, not an API key.

INCORRECT: "The importApiKeys method must be called to import all newly created API keys into the current stage of the API" is incorrect as this imports API keys to API Gateway from an external source such as a CSV file which is not relevant to this scenario.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html> · <http://docs.amazonaws.cn/en_us/sdkfornet/v3/apidocs/items/APIGateway/MAPIGatewayCreateUsagePlanKeyCreateUsagePlanKeyRequest.html>

</details>

---

<a id="t07-q43"></a>
### T07-Q43
*⏭ пропущено · домен: офіційна мітка*

You are creating a mobile application that needs access to the AWS API Gateway. Users will need to register first before they can access your API and you would like the user management to be fully managed.

Which authentication option should you use for your API Gateway layer?

- ✅ Use Cognito User Pools
- ▫️ Use API Gateway User Pools
- ▫️ Use Lambda Authorizer
- ▫️ Use IAM permissions with sigv4

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Cognito User Pools - As an alternative to using IAM roles and policies or Lambda authorizers, you can use an Amazon Cognito user pool to control who can access your API in Amazon API Gateway. To use an Amazon Cognito user pool with your API, you must first create an authorizer of the COGNITO_USER_POOLS type and then configure an API method to use that authorizer. After the API is deployed, the client must first sign the user into the user pool, obtain an identity or access token for the user, and then call the API method with one of the tokens, which are typically set to the request's Authorization header. The API call succeeds only if the required token is supplied and the supplied token is valid, otherwise, the client isn't authorized to make the call because the client did not have credentials that could be authorized.

Incorrect options:

Use Lambda Authorizer- A Lambda authorizer (formerly known as a custom authorizer) is an API Gateway feature that uses a Lambda function to control access to your API. A Lambda authorizer is useful if you want to implement a custom authorization scheme that uses a bearer token authentication strategy such as OAuth or SAML, or that uses request parameters to determine the caller's identity. This won't be a fully managed user management solution but it would allow you to check for access at the AWS API Gateway level.

Use IAM permissions with sigv4 - Signature Version 4 is the process to add authentication information to AWS requests sent by HTTP. For security, most requests to AWS must be signed with an access key, which consists of an access key ID and secret access key. These two keys are commonly referred to as your security credentials. But, we cannot possibly create an IAM user for every visitor of the site, so this is where social identity providers come in to help.

Use API Gateway User Pools - This is a made-up option.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> · <https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html>

</details>

---

<a id="t07-q46"></a>
### T07-Q46
*⏭ пропущено · домен: офіційна мітка*

You have a three-tier web application consisting of a web layer using AngularJS, an application layer using an AWS API Gateway and a data layer in an Amazon Relational Database Service (RDS) database. Your web application allows visitors to look up popular movies from the past. The company is looking at reducing the number of calls made to endpoint and improve latency to the API.

What can you do to improve performance?

- ▫️ Use Mapping Templates
- ▫️ Use Stage Variables
- ✅ Enable API Gateway Caching
- ▫️ Use Amazon Kinesis Data Streams to stream incoming data and reduce the burden on Gateway APIs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable API Gateway Caching - You can enable API caching in Amazon API Gateway to cache your endpoint's responses. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of requests to your API. When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified time-to-live (TTL) period, in seconds. API Gateway then responds to the request by looking up the endpoint response from the cache instead of making a request to your endpoint. The default TTL value for API caching is 300 seconds. The maximum TTL value is 3600 seconds. TTL=0 means caching is disabled.

Incorrect options:

Use Mapping Templates - A mapping template is a script expressed in Velocity Template Language (VTL) and applied to the payload using JSONPath expressions. Mapping templates help format/structure the data in a way that it is easily readable, unlike a server response that might always be easy to ready. Mapping Templates do not help in latency issues of the APIs.

Use Stage Variables - Stage variables act like environment variables and can be used to change the behavior of your API Gateway methods for each deployment stage; for example, making it possible to reach a different back end depending on which stage the API is running on. Stage variables do not help in latency issues.

Use Amazon Kinesis Data Streams to stream incoming data and reduce the burden on Gateway APIs - Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html

</details>

---

<a id="t08-q34"></a>
### T08-Q34
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company is providing APIs as a web-based service to allow anonymous access to daily updated statistical data, using Amazon API Gateway and AWS Lambda for API development. The service's popularity has grown, and the company aims to improve the API responsiveness.

What measure should the company undertake to fulfill this objective?

- ▫️ Set up API Gateway with an interface VPC endpoint.
- ▫️ Cache API results in an Amazon ElastiCache cluster.
- ✅ Activate caching in API Gateway.
- ▫️ Set up API keys and usage plans in API Gateway.

<details><summary>Пояснення (оригінал, EN)</summary>

Enabling caching in API Gateway allows the service to cache the endpoint's responses, which improves the performance of the APIs by reducing the number of calls made to the endpoint and by improving the latency of the requests.

CORRECT: "Activate caching in API Gateway" is the correct answer (as explained above.)

INCORRECT: "Set up API Gateway with an interface VPC endpoint" is incorrect.

Configuring API Gateway to use an interface VPC endpoint helps in enhancing the security and privacy of the data being exchanged with the service. It does not directly affect the API responsiveness.

INCORRECT: "Cache API results in an Amazon ElastiCache cluster" is incorrect.

Amazon ElastiCache can be used for caching data from databases but is not suitable for caching API results for API gateway.

INCORRECT: "Set up API keys and usage plans in API Gateway" is incorrect.

Configuring usage plans and API keys in API Gateway can help control and manage usage of the APIs. However, they don't directly improve API responsiveness.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t08-q48"></a>
### T08-Q48
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A set of APIs are exposed to customers using Amazon API Gateway. These APIs have caching enabled on the API Gateway. Customers have asked for an option to invalidate this cache for each of the APIs.

What action can be taken to allow API customers to invalidate the API Cache?

- ▫️ Ask customers to invoke an AWS API endpoint which invalidates the cache
- ▫️ Ask customers to add a query string parameter called INVALIDATE_CACHE” when making an API call
- ▫️ Ask customers to use AWS credentials to call the InvalidateCache API
- ✅ Ask customers to pass an HTTP header called Cache-Control:max-age=0

<details><summary>Пояснення (оригінал, EN)</summary>

A client of your API can invalidate an existing cache entry and reload it from the integration endpoint for individual requests. The client must send a request that contains the Cache-Control: max-age=0 header.

The client receives the response directly from the integration endpoint instead of the cache, provided that the client is authorized to do so. This replaces the existing cache entry with the new response, which is fetched from the integration endpoint.

Therefore, the company should ask customers to pass an HTTP header called Cache-Control:max-age=0.

CORRECT: "Ask customers to pass an HTTP header called Cache-Control:max-age=0" is the correct answer.

INCORRECT: "Ask customers to use AWS credentials to call the InvalidateCache API" is incorrect as this API action is used to invalidate the cache but is not the method the clients use to invalidate the cache.

INCORRECT: "Ask customers to invoke an AWS API endpoint which invalidates the cache" is incorrect as you don’t invalidate the cache by invoking an endpoint, the HTTP header mentioned in the explanation is required.

INCORRECT: "Ask customers to add a query string parameter called INVALIDATE_CACHE” when making an API call" is incorrect as this is not a valid method of invalidating an API Gateway cache.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t09-q14"></a>
### T09-Q14
*⏭ пропущено · домен: офіційна мітка*

As a Senior Developer, you are tasked with creating several API Gateway powered APIs along with your team of developers. The developers are working on the API in the development environment, but they find the changes made to the APIs are not reflected when the API is called.

As a Developer Associate, which of the following solutions would you recommend for this use-case?

- ▫️ Enable Lambda authorizer to access API
- ▫️ Use Stage Variables for development state of API
- ▫️ Developers need IAM permissions on API execution component of API Gateway
- ✅ Redeploy the API to an existing stage or to a new stage

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Redeploy the API to an existing stage or to a new stage

After creating your API, you must deploy it to make it callable by your users. To deploy an API, you create an API deployment and associate it with a stage. A stage is a logical reference to a lifecycle state of your API (for example, dev, prod, beta, v2). API stages are identified by the API ID and stage name. Every time you update an API, you must redeploy the API to an existing stage or to a new stage. Updating an API includes modifying routes, methods, integrations, authorizers, and anything else other than stage settings.

Incorrect options:

Developers need IAM permissions on API execution component of API Gateway - Access control access to Amazon API Gateway APIs is done with IAM permissions. To call a deployed API or to refresh the API caching, you must grant the API caller permissions to perform required IAM actions supported by the API execution component of API Gateway. In the current scenario, developers do not need permissions on "execution components" but on "management components" of API Gateway that help them to create, deploy, and manage an API. Hence, this statement is an incorrect option.

Enable Lambda authorizer to access API - A Lambda authorizer (formerly known as a custom authorizer) is an API Gateway feature that uses a Lambda function to control access to your API. So, this feature too helps in access control, but in the current scenario its the developers and not the users who are facing the issue. So, this statement is an incorrect option.

Use Stage Variables for development state of API - Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates. Stage variables are not connected to the scenario described in the current use case.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-deploy-api.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/permissions.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t10-q12"></a>
### T10-Q12
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An online multiplayer game employs Amazon API Gateway WebSocket APIs with an HTTP backend. The game developer needs to add a feature that identifies players with unstable connections who repeatedly join and leave the game. The developer also wants the ability to disconnect such players from the game.

What two modifications should the developer implement in the game to fulfill these requirements? (Select TWO.)

- ▫️ Switch to AWS App Runner for the backend service.
- ▫️ Implement AWS Cognito for player authentication in the backend service.
- ▫️ Switch to REST APIs in the backend service.
- ✅ Add logic to track the player's connection status using Amazon DynamoDB in the backend service.
- ✅ Implement $connect and $disconnect routes in the backend service.

<details><summary>Пояснення (оригінал, EN)</summary>

By implementing $connect and $disconnect routes in the backend service, you can capture events when a player connects and disconnects from the WebSocket API. This enables tracking and managing of players' connection statuses.

Using Amazon DynamoDB (or another database service) allows you to persist and track the connection status of each player in real-time. This enables you to identify players who connect and disconnect frequently.

CORRECT: "Implement $connect and $disconnect routes in the backend service" is a correct answer (as explained above.)

CORRECT: "Add logic to track the player's connection status using Amazon DynamoDB in the backend service" is also a correct answer (as explained above.)

INCORRECT: "Switch to REST APIs in the backend service" is incorrect.

Switching to REST APIs won't inherently provide the ability to manage or track unstable connections. WebSocket APIs are more suited for applications requiring real-time, two-way communication.

INCORRECT: "Implement AWS Cognito for player authentication in the backend service" is incorrect.

While AWS Cognito provides authentication and user management, it doesn't offer features for tracking and managing unstable connections.

INCORRECT: "Switch to AWS App Runner for the backend service" is incorrect.

AWS App Runner is a service that makes it easy to build, deploy, and scale containerized applications quickly, but it doesn't directly address the specific requirement of tracking and managing unstable connections.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api-route-keys-connect-disconnect.html>

</details>

---

<a id="t10-q42"></a>
### T10-Q42
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company provides a large number of services on AWS to customers. The customers connect to one or more services directly and the architecture is becoming complex. How can the architecture be refactored to provide a single interface for the services?

- ▫️ AWS Single Sign On (SSO)
- ▫️ AWS X-Ray
- ✅ Amazon API Gateway
- ▫️ AWS Cognito

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale. APIs act as the "front door" for applications to access data, business logic, or functionality from your backend services.

Using API Gateway, you can create RESTful APIs and WebSocket APIs that enable real-time two-way communication applications. API Gateway supports containerized and serverless workloads, as well as web applications.

API Gateway can be used as the single interface for consumers of the services provided by the organization in this scenario. This solution will simplify the architecture.

CORRECT: "Amazon API Gateway" is the correct answer.

INCORRECT: "AWS X-Ray" is incorrect. AWS X-Ray is used for analyzing and debugging applications.

INCORRECT: "AWS Cognito" is incorrect. AWS Cognito is used for adding sign-up, sign-in and access control to web and mobile apps.

INCORRECT: "AWS Single Sign On (SSO)" is incorrect. AWS SSO is used to provide central management of multiple AWS accounts and business applications and to provide single sign-on to accounts.

Джерела: <https://aws.amazon.com/api-gateway/features/>

</details>

---

<a id="t11-q01"></a>
### T11-Q01
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Окремий dev stage.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A company wants to provide beta access to some developers on its development team for a new version of the company's Amazon API Gateway REST API, without causing any disturbance to the existing customers who are using the API via a frontend UI and Amazon Cognito authentication. The new version has new endpoints and backward-incompatible interface changes, and the company's development team is responsible for its maintenance.

Which of the following will satisfy these requirements in the MOST operationally efficient manner?

- ▫️ Create a new API Gateway API that points to the new API application code and then have the developers point the endpoints to the new API
- ▫️ Configure a canary release deployment on the API Gateway API and then have the developers point to the relevant deployment by referencing the stage variable in the endpoint
- ✅ Create a development stage on the API Gateway API and then have the developers point the endpoints to the development stage
- ▫️ Create new API keys on the API Gateway API and then have the developers point the endpoints by passing the new API keys

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a development stage on the API Gateway API and then have the developers point the endpoints to the development stage

 via - https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-basic-concept.html

A stage is a logical reference to a lifecycle state of your API (for example, 'dev', 'prod', 'beta', 'v2'). API stages are identified by API ID and stage name. You use a stage to manage and optimize a particular deployment. For example, you can configure stage settings to enable caching, customize request throttling, configure logging, define stage variables, or attach a canary release for testing. After the initial deployment, you can add more stages and associate them with existing deployments. You can use the API Gateway console to create a new stage, or you can choose an existing stage while deploying an API. In general, you can add a new stage to an API deployment before redeploying the API.

For the given use case, you can configure a development stage for your API Gateway API and then integrate it with the new version of the backend functionality that has new endpoints and backward-incompatible interface changes. The customers can continue to use the existing API.

Incorrect options:

Configure a canary release deployment on the API Gateway API and then have the developers point to the relevant deployment by referencing the stage variable in the endpoint - An API deployment is a point-in-time snapshot of your API Gateway API. To be available for clients to use, the deployment must be associated with one or more API stages. Canary release is a software development strategy in which a new version of an API (as well as other software) is deployed for testing purposes, and the base version remains deployed as a production release for normal operations on the same stage. In a canary release deployment, total API traffic is separated at random into a production release and a canary release with a pre-configured ratio. Typically, the canary release receives a small percentage of API traffic and the production release takes up the rest. The updated API features are only visible to API traffic through the canary. You can adjust the canary traffic percentage to optimize test coverage or performance. By keeping canary traffic small and the selection random, most users are not adversely affected at any time by potential bugs in the new version, and no single user is adversely affected all the time.

This option is incorrect for the given use case as some of the customers would also access the new version of the API.

Create new API keys on the API Gateway API and then have the developers point the endpoints by passing the new API keys - AN API key is an alphanumeric string that API Gateway uses to identify an app developer who uses your REST or WebSocket API. This option is a distractor as you cannot selectively provide access to the new version just based on API keys.

Create a new API Gateway API that points to the new API application code and then have the developers point the endpoints to the new API - This is an overkill for the given requirement as there is no need to create a completely new API just to provide some developers early access to the beta version.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-deploy-api.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-basic-concept.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html>

</details>

---

<a id="t11-q14"></a>
### T11-Q14
*⏭ пропущено · домен: офіційна мітка*

A SaaS company runs a HealthCare web application that is used worldwide by users. There have been requests by mobile developers to expose public APIs for the application-specific functionality. You decide to make the APIs available to mobile developers as product offerings.

Which of the following options will allow you to do that?

- ▫️ Use CloudFront Usage Plans
- ▫️ Use AWS Billing Usage Plans
- ▫️ Use AWS Lambda Custom Authorizers
- ✅ Use API Gateway Usage Plans

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use API Gateway Usage Plans

Amazon API Gateway is an AWS service for creating, publishing, maintaining, monitoring, and securing REST, HTTP, and WebSocket APIs at any scale. API developers can create APIs that access AWS or other web services, as well as data stored in the AWS Cloud.

How API Gateway Works:  via - https://aws.amazon.com/api-gateway/

A usage plan specifies who can access one or more deployed API stages and methods—and also how much and how fast they can access them. The plan uses API keys to identify API clients and meters access to the associated API stages for each key.

You can configure usage plans and API keys to allow customers to access selected APIs at agreed-upon request rates and quotas that meet their business requirements and budget constraints.

Overview of API Gateway Usage Plans and API keys:  via - https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html

Incorrect options:

Use AWS Billing Usage Plans - AWS Billing and Cost Management is the service that you use to pay your AWS bill, monitor your usage, and analyze and control your costs. There is no such thing as AWS Billing Usage Plans. You cannot use AWS Billing to set up public APIs for the application.

Use CloudFront Usage Plans - Amazon CloudFront is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency, high transfer speeds, all within a developer-friendly environment. There is no such thing as CloudFront Usage Plans. You cannot use CloudFront to set up public APIs for the application.

Use AWS Lambda Custom Authorizers - Lambda is a separate service than Gateway API, therefore, it cannot be used to determine the API usage limits.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html

</details>

---
