# SQS / SNS / EventBridge / SES

**Домен 3 — Розгортання** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q23"></a>
### T09-Q23
*домен: офіційна мітка*

An e-commerce company manages a microservices application that receives orders from various partners through a customized API for each partner exposed via Amazon API Gateway. The orders are processed by a shared Lambda function.

How can the company notify each partner regarding the status of their respective orders in the most efficient manner, without affecting other partners' orders? Also, the solution should be scalable to accommodate new partners with minimal code changes required.

- ✅ Set up an SNS topic and subscribe each partner to the SNS topic. Modify the Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions
- ▫️ Set up a separate Lambda function for each partner. Set up an SNS topic and subscribe each partner to the SNS topic. Modify each partner's Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions
- ▫️ Set up a separate SNS topic for each partner. Modify the Lambda function to publish messages for each partner to the partner's SNS topic
- ▫️ Set up a separate SNS topic for each partner and subscribe each partner to the respective SNS topic. Modify the Lambda function to publish messages with specific attributes to the partner's SNS topic and apply the appropriate filter policy to the topic subscriptions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up an SNS topic and subscribe each partner to the SNS topic. Modify the Lambda function to publish messages with specific attributes to the SNS topic and apply the appropriate filter policy to the topic subscriptions»**

SNS topic — точка доступу, що об'єднує кілька endpoints: Lambda, SQS, HTTP/S, email. За замовчуванням підписник отримує **всі** повідомлення топіка. Щоб отримувати лише частину, на підписку ставлять **filter policy** — JSON, що визначає потрібні повідомлення. Залежно від scope фільтрують за атрибутами або за тілом повідомлення; тіло має бути валідним JSON.

Lambda публікує повідомлення з атрибутами в один топік, а кожен партнер має свою filter policy. Новий партнер — лише нова підписка з фільтром.

**❌ Неправильні варіанти:**
- *Separate topic per partner* — неефективно: одного топіка з фільтрами достатньо.
- *Separate topic per partner + filter policies* — те саме: окремі топіки зайві.
- *Separate Lambda per partner* — неефективно: однієї спільної функції достатньо.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-create-topic.html> · <https://docs.aws.amazon.com/sns/latest/dg/sns-message-filtering.html>

</details>

---
