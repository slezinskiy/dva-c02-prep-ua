# Amazon API Gateway / AppSync

**Домен 4 — Усунення несправностей та оптимізація** · питань: 8  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#apigw) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q19"></a>
### T01-Q19
*⏭ пропущено · домен: офіційна мітка*

A developer has defined a Lambda integration in Amazon API Gateway using a stage variable. However, when the developer invokes the API method, it consistently returns an "Internal server error" and a 500 status code.

What steps should the developer take to fix the issue?

- ▫️ API calls can't exceed the maximum allowed API request rate per account and per Region. Implement error retries and exponential backoffs to fix the error
- ▫️ If you create a stage variable to call a function through your API, you must add the required permissions. Create an IAM role that your Lambda function can assume when invoking the respective AWS resources
- ✅ If you create a stage variable to call a Lambda function through your API, you must add the required permissions. Update your Lambda function's resource-based AWS Identity and Access Management (IAM) policy so that it grants invoke permission to the API Gateway
- ▫️ When setting the Lambda function as the value of a stage variable, use the function's ARN and not the function alias for setting up the value

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

If you create a stage variable to call a Lambda function through your API, you must add the required permissions. Update your Lambda function's resource-based AWS Identity and Access Management (IAM) policy so that it grants invoke permission to the API Gateway

If your Lambda function's resource-based policy doesn't include permissions for your API to invoke the function, API Gateway returns an Internal server error message.

If you create a stage variable to call a function through your API, you must add the required permissions by doing one of the following:

Update your Lambda function's resource-based AWS Identity and Access Management (IAM) policy so that it grants invoke permission to API Gateway.

OR

Create an IAM role that API Gateway can assume to invoke your Lambda function.

If you build an API Gateway API with standard Lambda integration using the API Gateway console, the console adds the required permissions automatically.

Example resource-based policy that grants invoke permission to API Gateway:  via - https://aws.amazon.com/premiumsupport/knowledge-center/api-gateway-lambda-stage-variable-500/

Incorrect options:

API calls can't exceed the maximum allowed API request rate per account and per Region. Implement error retries and exponential backoffs to fix the error - When API calls exceed the maximum allowed API request rate per account and per Region, the error received is a "Rate Exceeded" error and not an "Internal server error". Hence, this option is irrelevant to the given use case.

If you create a stage variable to call a function through your API, you must add the required permissions. Create an IAM role that your Lambda function can assume when invoking the respective AWS resources - This option is incorrect. However, creating an IAM role that API Gateway can assume to invoke the Lambda function is another solution to fix the given problem.

When setting the Lambda function as the value of a stage variable, use the function's ARN and not the function alias for setting up the value - This statement is incorrect. When setting a Lambda function as the value of a stage variable, use the function's local name, possibly including its alias or version specification. Do not use the function's ARN. The API Gateway console assumes the stage variable value for a Lambda function as the unqualified function name and expands the given stage variable into an ARN.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/services-apigateway.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-set-stage-variables-aws-console.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/api-gateway-lambda-stage-variable-500/>

</details>

---

<a id="t06-q08"></a>
### T06-Q08
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company has deployed a REST API using Amazon API Gateway with a Lambda authorizer. The company needs to log who has accessed the API and how the caller accessed the API. They also require logs that include errors and execution traces for the Lambda authorizer.

Which combination of actions should the Developer take to meet these requirements? (Select TWO.)

- ✅ Enable API Gateway access logs.
- ▫️ Create an API Gateway usage plan.
- ❌ Enable detailed logging in Amazon CloudWatch.
- ▫️ Enable server access logging.
- ✅ Enable API Gateway execution logging.

<details><summary>Пояснення (оригінал, EN)</summary>

There are two types of API logging in CloudWatch: execution logging and access logging. In execution logging, API Gateway manages the CloudWatch Logs. The process includes creating log groups and log streams, and reporting to the log streams any caller's requests and responses.

The logged data includes errors or execution traces (such as request or response parameter values or payloads), data used by Lambda authorizers, whether API keys are required, whether usage plans are enabled, and so on.

In access logging, you, as an API Developer, want to log who has accessed your API and how the caller accessed the API. You can create your own log group or choose an existing log group that could be managed by API Gateway.

CORRECT: "Enable API Gateway execution logging" is a correct answer.

CORRECT: "Enable API Gateway access logs" is also a correct answer.

INCORRECT: "Enable detailed logging in Amazon CloudWatch" is incorrect. Detailed logging does not provide the requested information.

INCORRECT: "Create an API Gateway usage plan" is incorrect. This will not enable logging.

INCORRECT: "Enable server access logging" is incorrect. This is a type of logging that applies to Amazon S3 buckets.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/Developerguide/set-up-logging.html>

</details>

---

<a id="t07-q23"></a>
### T07-Q23
*⏭ пропущено · домен: офіційна мітка*

A banking application needs to send real-time alerts and notifications based on any updates from the backend services. The company wants to avoid implementing complex polling mechanisms for these notifications.

Which of the following types of APIs supported by the Amazon API Gateway is the right fit?

- ▫️ HTTP APIs
- ✅ WebSocket APIs
- ▫️ REST or HTTP APIs
- ▫️ REST APIs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

WebSocket APIs

In a WebSocket API, the client and the server can both send messages to each other at any time. Backend servers can easily push data to connected users and devices, avoiding the need to implement complex polling mechanisms.

For example, you could build a serverless application using an API Gateway WebSocket API and AWS Lambda to send and receive messages to and from individual users or groups of users in a chat room. Or you could invoke backend services such as AWS Lambda, Amazon Kinesis, or an HTTP endpoint based on message content.

You can use API Gateway WebSocket APIs to build secure, real-time communication applications without having to provision or manage any servers to manage connections or large-scale data exchanges. Targeted use cases include real-time applications such as the following:

Chat applications
Real-time dashboards such as stock tickers
Real-time alerts and notifications
API Gateway provides WebSocket API management functionality such as the following:

Monitoring and throttling of connections and messages
Using AWS X-Ray to trace messages as they travel through the APIs to backend services
Easy integration with HTTP/HTTPS endpoints
Incorrect options:

REST or HTTP APIs

REST APIs - An API Gateway REST API is made up of resources and methods. A resource is a logical entity that an app can access through a resource path. A method corresponds to a REST API request that is submitted by the user of your API and the response returned to the user.

For example, /incomes could be the path of a resource representing the income of the app user. A resource can have one or more operations that are defined by appropriate HTTP verbs such as GET, POST, PUT, PATCH, and DELETE. A combination of a resource path and an operation identifies a method of the API. For example, a POST /incomes method could add an income earned by the caller, and a GET /expenses method could query the reported expenses incurred by the caller.

HTTP APIs - HTTP APIs enable you to create RESTful APIs with lower latency and lower cost than REST APIs. You can use HTTP APIs to send requests to AWS Lambda functions or to any publicly routable HTTP endpoint.

For example, you can create an HTTP API that integrates with a Lambda function on the backend. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client.

Server push mechanism is not possible in REST and HTTP APIs.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-overview-developer-experience.html

</details>

---

<a id="t08-q21"></a>
### T08-Q21
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A serverless application uses Amazon API Gateway an AWS Lambda function and a Lambda authorizer function. There is a failure with the application and a developer needs to trace and analyze user requests that pass through API Gateway through to the back end services.

Which AWS service is MOST suitable for this purpose?

- ▫️ VPC Flow Logs
- ▫️ Amazon Inspector
- ▫️ Amazon CloudWatch
- ✅ AWS X-Ray

<details><summary>Пояснення (оригінал, EN)</summary>

You can use AWS X-Ray to trace and analyze user requests as they travel through your Amazon API Gateway APIs to the underlying services. API Gateway supports X-Ray tracing for all API Gateway endpoint types: Regional, edge-optimized, and private. You can use X-Ray with Amazon API Gateway in all AWS Regions where X-Ray is available.

Because X-Ray gives you an end-to-end view of an entire request, you can analyze latencies in your APIs and their backend services. You can use an X-Ray service map to view the latency of an entire request and that of the downstream services that are integrated with X-Ray. You can also configure sampling rules to tell X-Ray which requests to record and at what sampling rates, according to criteria that you specify.

The following diagram shows a trace view generated for the example API described above, with a Lambda backend function and a Lambda authorizer function. A successful API method request is shown with a response code of 200.

CORRECT: "AWS X-Ray" is the correct answer.

INCORRECT: "Amazon CloudWatch" is incorrect as it is used to collect metrics and logs. You can use these for troubleshooting however it will be more effective to use AWS X-Ray for analyzing and tracing a distributed application such as this one.

INCORRECT: "Amazon Inspector" is incorrect as this is an automated security assessment service. It is not used for analyzing and tracing serverless applications.

INCORRECT: "VPC Flow Logs" is incorrect as this is a feature that captures information about TCP/IP traffic related to network interfaces in a VPC.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html>

</details>

---

<a id="t11-q37"></a>
### T11-Q37
*⏭ пропущено · домен: офіційна мітка*

You are a developer working on AWS Lambda functions that are invoked via REST API's using Amazon API Gateway. Currently, when a GET request is invoked by the consumer, the entire data-set returned by the Lambda function is visible. Your team lead asked you to format the data response.

Which feature of the API Gateway can be used to solve this issue?

- ✅ Use API Gateway Mapping Templates
- ▫️ Use a Lambda custom interceptor
- ▫️ Use an API Gateway stage variable
- ▫️ Deploy an interceptor shell script

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use API Gateway Mapping Templates - In API Gateway, an API's method request can take a payload in a different format from the corresponding integration request payload, as required in the backend. Similarly, vice versa is also possible. API Gateway lets you use mapping templates to map the payload from a method request to the corresponding integration request and from an integration response to the corresponding method response.

Suppose we have an API for managing fruit and vegetable inventory in the produce department of a supermarket. When a manager queries the backend for the current inventory, the server sends back the following response payload:

{
  "department": "produce",
  "categories": [
    "fruit",
    "vegetables"
  ],
  "bins": [
    {
      "category": "fruit",
      "type": "apples",
      "price": 1.99,
      "unit": "pound",
      "quantity": 232
    },
    {
      "category": "fruit",
      "type": "bananas",
      "price": 0.19,
      "unit": "each",
      "quantity": 112
    },
    {
      "category": "vegetables",
      "type": "carrots",
      "price": 1.29,
      "unit": "bag",
      "quantity": 57
    }
  ]
}
When the backend returns the query results shown above, the manager of the produce department might be interested in reading them, as follows:

{
  "choices": [
    {
      "kind": "apples",
      "suggestedPrice": "1.99 per pound",
      "available": 232
    },
    {
      "kind": "bananas",
      "suggestedPrice": "0.19 per each",
      "available": 112
    },
    {
      "kind": "carrots",
      "suggestedPrice": "1.29 per bag",
      "available": 57
    }
  ]
}
To enable this, we need to provide API Gateway with a mapping template to translate the data from the backend format like so:

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
Incorrect options:

Deploy an interceptor shell script - This option has been added as a distractor.

Use an API Gateway stage variable - Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates. This feature is not useful for the current use case.

Use a Lambda custom interceptor - This is a made-up option. Lambda cannot intercept the response for the given use-case.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/rest-api-data-transformations.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html>

</details>

---

<a id="t11-q43"></a>
### T11-Q43
*⏭ пропущено · домен: офіційна мітка*

A company wants to improve the performance of its popular API service that offers unauthenticated read access to daily updated statistical information via Amazon API Gateway and AWS Lambda.

What measures can the company take?

- ▫️ Configure API Gateway to use Elasticache for Memcached
- ▫️ Configure API Gateway to use Gateway VPC Endpoint
- ✅ Enable API caching in API Gateway
- ▫️ Set up usage plans and API keys in API Gateway

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable API caching in API Gateway

API Gateway provides a few strategies for optimizing your API to improve responsiveness, like response caching and payload compression. You can enable API caching in Amazon API Gateway to cache your endpoint's responses. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of requests to your API.

Incorrect options:

Set up usage plans and API keys in API Gateway - After you create, test, and deploy your APIs, you can use API Gateway usage plans to make them available as product offerings for your customers. You can configure usage plans and API keys to allow customers to access selected APIs, and begin throttling requests to those APIs based on defined limits and quotas. These can be set at the API, or API method level. This option is incorrect as usage plans and API keys cannot be used to improve the responsiveness of the API.

Configure API Gateway to use Elasticache for Memcached - This option has been added as a distractor. Elasticache for Memcached cannot be used with API Gateway to improve the responsiveness of the API for the given use case. You should note that Elasticache for Memcached is a downstream service in the request flow.

Configure API Gateway to use Gateway VPC Endpoint - This option has been added as a distractor. Gateway endpoints provide reliable connectivity to Amazon S3 and DynamoDB without requiring an internet gateway or a NAT device for your VPC. Gateway endpoints do not enable AWS PrivateLink.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html>

</details>

---

<a id="t12-q01"></a>
### T12-Q01
*⏭ пропущено · домен: офіційна мітка*

A company uses microservices-based infrastructure to process the API calls from clients, perform request filtering and cache requests using the AWS API Gateway. Users report receiving 501 error code and you have been contacted to find out what is failing.

Which service will you choose to help you troubleshoot?

- ▫️ Use CloudTrail service
- ▫️ Use CloudWatch service
- ✅ Use X-Ray service
- ▫️ Use API Gateway service

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use X-Ray service - AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components. You can use X-Ray to analyze both applications in development and in production, from simple three-tier applications to complex microservices applications consisting of thousands of services.

X-Ray Overview:  via - https://aws.amazon.com/xray/

AWS X-Ray creates a map of services used by your application with trace data that you can use to drill into specific services or issues. This provides a view of connections between services in your application and aggregated data for each service, including average latency and failure rates. You can create dependency trees, perform cross-availability zone or region call detections, and more.

X-Ray Service maps:  via - https://aws.amazon.com/xray/features/

X-Ray Traces:  via - https://aws.amazon.com/xray/features/

Incorrect options:

Use CloudTrail service - With CloudTrail, you can get a history of AWS API calls for your account - including API calls made via the AWS Management Console, AWS SDKs, command-line tools, and higher-level AWS services (such as AWS CloudFormation). This is a very useful service for general monitoring and tracking. But, it will not give a detailed analysis of the outcome of microservices or drill into specific issues. For the current use case, X-Ray offers a better solution.

Use API Gateway service - Amazon API Gateway is an AWS service for creating, publishing, maintaining, monitoring, and securing REST, HTTP, and WebSocket APIs at any scale. API Gateway will not be able to drill into the flow between different microservices or their issues.

Use CloudWatch service - Amazon CloudWatch is a monitoring and management service that provides data and actionable insights for AWS, hybrid, and on-premises applications and infrastructure resources. CloudWatch can collect numbers and respond to AWS service-related events, but it can't help you debug microservices specific issues on AWS.

Джерела: <https://aws.amazon.com/cloudtrail/> · <https://aws.amazon.com/api-gateway/> · <https://aws.amazon.com/cloudwatch/features/>

</details>

---

<a id="t12-q30"></a>
### T12-Q30
*⏭ пропущено · домен: офіційна мітка*

A company wants to implement authentication for its new RESTful API service that uses Amazon API Gateway. To authenticate the calls, each request must include HTTP headers with a client ID and user ID. These credentials must be compared to the authentication data in a DynamoDB table.

As an AWS Certified Developer Associate, which of the following would you recommend for implementing this authentication in API Gateway?

- ✅ Develop an AWS Lambda authorizer that references the authentication data in the DynamoDB table
- ▫️ Set up an API Gateway Model that requires the credentials, then grant API Gateway access to the authentication table in DynamoDB
- ▫️ Update the API Gateway integration requests to require the credentials, then grant API Gateway access to the authentication table in DynamoDB
- ▫️ Authorize using Amazon Cognito that will reference the authentication table of DynamoDB

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Develop an AWS Lambda authorizer that references the DynamoDB authentication table - A Lambda authorizer is an API Gateway feature that uses a Lambda function to control access to your API.

A Lambda authorizer is useful if you want to implement a custom authorization scheme that uses a bearer token authentication strategy such as OAuth or SAML, or that uses request parameters to determine the caller's identity.

When a client makes a request to one of your API's methods, API Gateway calls your Lambda authorizer, which takes the caller's identity as input and returns an IAM policy as output.

There are two types of Lambda authorizers:

A token-based Lambda authorizer (also called a TOKEN authorizer) receives the caller's identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.

A request parameter-based Lambda authorizer (also called a REQUEST authorizer) receives the caller's identity in a combination of headers, query string parameters, state variables, and $context variables.

API Gateway Lambda authorization workflow:  via - https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html

Incorrect options:

Set up an API Gateway Model that requires the credentials, then grant API Gateway access to the authentication table in DynamoDB - In API Gateway, a model defines the data structure of a payload. In API Gateway models are defined using the JSON schema draft 4. Models are not mandatory.

Update the API Gateway integration requests to require the credentials, then grant API Gateway access to the authentication table in DynamoDB - After setting up an API method, you must integrate it with an endpoint in the backend. A backend endpoint is also referred to as an integration endpoint and can be a Lambda function, an HTTP webpage, or an AWS service action.

An integration request is an HTTP request that API Gateway submits to the backend, passing along the client-submitted request data, and transforming the data, if necessary. The HTTP method (or verb) and URI of the integration request are dictated by the backend (that is, the integration endpoint). They can be the same as or different from the method request's HTTP method and URI, respectively.

Authorize using Amazon Cognito that will reference the authentication table of DynamoDB - As an alternative to using IAM roles and policies or Lambda authorizers (formerly known as custom authorizers), you can use an Amazon Cognito user pool to control who can access your API in Amazon API Gateway.

To use an Amazon Cognito user pool with your API, you must first create an authorizer of the COGNITO_USER_POOLS type and then configure an API method to use that authorizer. After the API is deployed, the client must first sign the user in to the user pool, obtain an identity or access token for the user, and then call the API method with one of the tokens, which are typically set to the request's Authorization header. The API call succeeds only if the required token is supplied and the supplied token is valid, otherwise, the client isn't authorized to make the call because the client did not have credentials that could be authorized.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-integration-settings.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/models-mappings.html>

</details>

---
