# Інше

**Домен 3 — Розгортання** · питань: 1  
Теорія: [README.md](../../README.md) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q49"></a>
### T07-Q49
*⏭ пропущено · домен: офіційна мітка*

A company follows collaborative development practices. The engineering manager wants to isolate the development effort by setting up simulations of API components owned by various development teams.

Which API integration type is best suited for this requirement?

- ▫️ HTTP_PROXY
- ✅ MOCK
- ▫️ AWS_PROXY
- ▫️ HTTP

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

MOCK

This type of integration lets API Gateway return a response without sending the request further to the backend. This is useful for API testing because it can be used to test the integration setup without incurring charges for using the backend and to enable collaborative development of an API.

In collaborative development, a team can isolate their development effort by setting up simulations of API components owned by other teams by using the MOCK integrations. It is also used to return CORS-related headers to ensure that the API method permits CORS access. In fact, the API Gateway console integrates the OPTIONS method to support CORS with a mock integration.

Incorrect options:

AWS_PROXY - This type of integration lets an API method be integrated with the Lambda function invocation action with a flexible, versatile, and streamlined integration setup. This integration relies on direct interactions between the client and the integrated Lambda function.

HTTP_PROXY - The HTTP proxy integration allows a client to access the backend HTTP endpoints with a streamlined integration setup on single API method. You do not set the integration request or the integration response. API Gateway passes the incoming request from the client to the HTTP endpoint and passes the outgoing response from the HTTP endpoint to the client.

HTTP - This type of integration lets an API expose HTTP endpoints in the backend. With the HTTP integration, you must configure both the integration request and integration response. You must set up necessary data mappings from the method request to the integration request, and from the integration response to the method response.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-integration-types.html

</details>

---
