# SQS / SNS / EventBridge / SES

**Домен 3 — Розгортання** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q23"></a>
### T09-Q23
*⏭ пропущено · домен: офіційна мітка*

An e-commerce company manages a microservices application that receives orders from various partners through a customized API for each partner exposed via Amazon API Gateway. The orders are processed by a shared Lambda function.

How can the company notify each partner regarding the status of their respective orders in the most efficient manner, without affecting other partners' orders? Also, the solution should be scalable to accommodate new partners with minimal code changes required.

- ✅ Set up an SNS topic and subscribe each partner to the SNS topic. Modify the Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions
- ▫️ Set up a separate Lambda function for each partner. Set up an SNS topic and subscribe each partner to the SNS topic. Modify each partner's Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions
- ▫️ Set up a separate SNS topic for each partner. Modify the Lambda function to publish messages for each partner to the partner's SNS topic
- ▫️ Set up a separate SNS topic for each partner and subscribe each partner to the respective SNS topic. Modify the Lambda function to publish messages with specific attributes to the partner's SNS topic and apply the appropriate filter policy to the topic subscriptions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up an SNS topic and subscribe each partner to the SNS topic. Modify the Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions

An Amazon SNS topic is a logical access point that acts as a communication channel. A topic lets you group multiple endpoints (such as AWS Lambda, Amazon SQS, HTTP/S, or an email address). For example, to broadcast the messages of a message-producer system (such as, an e-commerce website) working with multiple other services that require its messages (for example, checkout and fulfillment systems), you can create a topic for your producer system.

By default, an Amazon SNS topic subscriber receives every message that's published to the topic. To receive only a subset of the messages, a subscriber must assign a filter policy to the topic subscription. A filter policy is a JSON object containing properties that define which messages the subscriber receives. Amazon SNS supports policies that act on the message attributes or the message body, according to the filter policy scope that you set for the subscription. Filter policies for the message body assume that the message payload is a well-formed JSON object.

For the given use case, you can change the Lambda function to publish messages with specific attributes to the single SNS topic and apply the appropriate filter policy to the topic subscriptions for each of the partners. This is also easily scalable for new partners since only the filter policy needs to be set up for the new partner.

Incorrect options:

Set up a separate SNS topic for each partner. Modify the Lambda function to publish messages for each partner to the partner's SNS topic

Set up a separate SNS topic for each partner and subscribe each partner to the respective SNS topic. Modify the Lambda function to publish messages with specific attributes to the partner's SNS topic and apply the appropriate filter policy to the topic subscriptions

Both of these options represent an inefficient solution as there is no need to segregate each partner's updates into a separate SNS topic. A single SNS topic with distinct filter policies is sufficient.

Set up a separate Lambda function for each partner. Set up an SNS topic and subscribe each partner to the SNS topic. Modify each partner's Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions - This is again an inefficient solution as there is no need to create a separate Lambda function for each partner as just a shared Lambda function is sufficient to process the orders and send an update to the single SNS topic with distinct filter policies.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-create-topic.html> · <https://docs.aws.amazon.com/sns/latest/dg/sns-message-filtering.html>

</details>

---
