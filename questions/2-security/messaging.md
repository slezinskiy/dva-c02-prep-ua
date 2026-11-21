# SQS / SNS / EventBridge / SES

**Домен 2 — Безпека** · питань: 2  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t05-q06"></a>
### T05-Q06
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

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

<details><summary>Пояснення (оригінал, EN)</summary>

The policy allows the user to use all Amazon SQS actions, but only with queues whose names are prefixed with the literal string “staging-queue”. This policy is useful to provide a queue creator the ability to use Amazon SQS actions. Any user who has permissions to create a queue must also have permissions to use other Amazon SQS actions in order to do anything with the created queues.

CORRECT: "The user will be able to use all Amazon SQS actions, but only for queues with names begin with the string “staging-queue“" is the correct answer.

INCORRECT: "The user will be able to create a queue named “staging-queue“" is incorrect as this policy provides the permissions to perform SQS actions on an existing queue.

INCORRECT: "The user will be able to apply a resource-based policy to the Amazon SQS queue named “staging-queue”" is incorrect as this is a single operation and the permissions policy allows all SQS actions.

INCORRECT: "The user will be granted cross-account access from account number “513246782345” to queue “staging-queue”" is incorrect as this is not a policy for granting cross-account access. The account number and queue relate to the same account.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-overview-of-managing-access.html>

</details>

---

<a id="t07-q59"></a>
### T07-Q59
*⏭ пропущено · домен: офіційна мітка*

A high-frequency stock trading firm is migrating their messaging queues from self-managed message-oriented middleware systems to Amazon SQS. The development team at the company wants to minimize the costs of using SQS.

As a Developer Associate, which of the following options would you recommend to address the given use-case?

- ▫️ Use SQS visibility timeout to retrieve messages from your Amazon SQS queues
- ▫️ Use SQS short polling to retrieve messages from your Amazon SQS queues
- ▫️ Use SQS message timer to retrieve messages from your Amazon SQS queues
- ✅ Use SQS long polling to retrieve messages from your Amazon SQS queues

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use SQS long polling to retrieve messages from your Amazon SQS queues

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

Amazon SQS provides short polling and long polling to receive messages from a queue. By default, queues use short polling. With short polling, Amazon SQS sends the response right away, even if the query found no messages. With long polling, Amazon SQS sends a response after it collects at least one available message, up to the maximum number of messages specified in the request. Amazon SQS sends an empty response only if the polling wait time expires.

Long polling makes it inexpensive to retrieve messages from your Amazon SQS queue as soon as the messages are available. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response). When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds.

Exam Alert:

Please review the differences between Short Polling vs Long Polling:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

Incorrect options:

Use SQS short polling to retrieve messages from your Amazon SQS queues - With short polling, Amazon SQS sends the response right away, even if the query found no messages. You end up paying more because of the increased number of empty receives.

Use SQS visibility timeout to retrieve messages from your Amazon SQS queues - Visibility timeout is a period during which Amazon SQS prevents other consumers from receiving and processing a given message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours. You cannot use visibility timeout to retrieve messages from your Amazon SQS queues. This option has been added as a distractor.

Use SQS message timer to retrieve messages from your Amazon SQS queues - You can use message timers to set an initial invisibility period for a message added to a queue. So, if you send a message with a 60-second timer, the message isn't visible to consumers for its first 60 seconds in the queue. The default (minimum) delay for a message is 0 seconds. The maximum is 15 minutes. You cannot use message timer to retrieve messages from your Amazon SQS queues. This option has been added as a distractor.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

</details>

---
