# SQS / SNS / EventBridge / SES

**Домен 2 — Безпека** · питань: 2  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t05-q06"></a>
### T05-Q06
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

```json
The following permissions policy is applied to an IAM user account:
{
"Version": "2012-10-17",
"Statement": [{
"Effect": "Allow",
"Action": "sqs:*",
"Resource": "arn:aws:sqs:*:513246782345:staging-queue*"
}]
}
Due to this policy, what Amazon SQS actions will the user be able to perform?
```

- ▫️ The user will be granted cross-account access from account number “513246782345” to queue “staging-queue”
- ✅ The user will be able to use all Amazon SQS actions, but only for queues with names begin with the string “staging-queue“
- ▫️ The user will be able to create a queue named “staging-queue“
- ▫️ The user will be able to apply a resource-based policy to the Amazon SQS queue named “staging-queue”

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The user will be able to use all Amazon SQS actions, but only for queues with names begin with the string "staging-queue"»**

Політика дозволяє `sqs:*`, але лише для черг з іменами, що починаються з `staging-queue` (wildcard у кінці ARN). Так зручно давати творцю черг права на всі дії з ними: хто може створити чергу, має мати й права нею користуватися.

**❌ Неправильні варіанти:**
- *Create a queue named "staging-queue"* — неточно: політика дає всі дії над чергами з цим префіксом, а не лише створення.
- *Apply a resource-based policy to the queue* — це лише одна операція, а дозволено всі.
- *Cross-account access from account 513246782345* — це не політика крос-акаунтного доступу: акаунт і черга з одного акаунта.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-overview-of-managing-access.html>

</details>

---

<a id="t07-q59"></a>
### T07-Q59
*домен: офіційна мітка*

A high-frequency stock trading firm is migrating their messaging queues from self-managed message-oriented middleware systems to Amazon SQS. The development team at the company wants to minimize the costs of using SQS.

As a Developer Associate, which of the following options would you recommend to address the given use-case?

- ▫️ Use SQS visibility timeout to retrieve messages from your Amazon SQS queues
- ▫️ Use SQS short polling to retrieve messages from your Amazon SQS queues
- ▫️ Use SQS message timer to retrieve messages from your Amazon SQS queues
- ✅ Use SQS long polling to retrieve messages from your Amazon SQS queues

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use SQS long polling to retrieve messages from your Amazon SQS queues»**

SQS отримує повідомлення двома способами:
- **Short polling** (за замовчуванням) — відповідає одразу, навіть якщо повідомлень немає.
- **Long polling** — чекає, доки з'явиться хоча б одне повідомлення (до вказаного максимуму); порожня відповідь приходить лише коли сплив час очікування.

Long polling дешевший, бо прибирає порожні відповіді і хибно порожні, коли повідомлення є, але не потрапили у відповідь. Вмикається, коли `WaitTimeSeconds` > 0; максимум — 20 секунд.

Порівняння: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

**❌ Неправильні варіанти:**
- *Short polling* — багато порожніх відповідей, тож дорожче.
- *Visibility timeout* — час, протягом якого інші споживачі не бачать отримане повідомлення: за замовчуванням 30 с, від 0 до 12 годин. Для отримання повідомлень не використовується.
- *Message timer* — початкова затримка видимості повідомлення: від 0 (за замовчуванням) до 15 хвилин. Для отримання повідомлень не використовується.

</details>

---
