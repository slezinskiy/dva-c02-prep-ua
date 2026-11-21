# SQS / SNS / EventBridge / SES

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 27  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q31"></a>
### T01-Q31
*⏭ пропущено · домен: офіційна мітка*

You are using AWS SQS FIFO queues to get the ordering of messages on a per user_id basis.

As a developer, which message parameter should you set the value of user_id to guarantee the ordering?

- ▫️ MessageOrderId
- ✅ MessageGroupId
- ▫️ MessageHash
- ▫️ MessageDeduplicationId

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS FIFO queues are designed to enhance messaging between applications when the order of operations and events has to be enforced.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html

MessageGroupId

The message group ID is the tag that specifies that a message belongs to a specific message group. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group (however, messages that belong to different message groups might be processed out of order).

Incorrect options:

MessageDeduplicationId - The message deduplication ID is the token used for the deduplication of sent messages. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5-minute deduplication interval.

MessageOrderId - This is a made-up option and has been added as a distractor.

MessageHash - This is a made-up option and has been added as a distractor.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/using-messagegroupid-property.html>

</details>

---

<a id="t02-q07"></a>
### T02-Q07
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Application Integration)*

An application needs to generate SMS text messages and emails for a large number of subscribers. Which AWS service can be used to send these messages to customers?

- ▫️ Amazon SWF
- ✅ Amazon SNS
- ▫️ Amazon SQS
- ▫️ Amazon SES

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Simple Notification Service (Amazon SNS) is a web service that coordinates and manages the delivery or sending of messages to subscribing endpoints or clients. In Amazon SNS, there are two types of clients—publishers and subscribers—also referred to as producers and consumers.

Publishers communicate asynchronously with subscribers by producing and sending a message to a topic, which is a logical access point and communication channel.

Subscribers (that is, web servers, email addresses, Amazon SQS queues, AWS Lambda functions) consume or receive the message or notification over one of the supported protocols (that is, Amazon SQS, HTTP/S, email, SMS, Lambda) when they are subscribed to the topic.

CORRECT: "Amazon SNS" is the correct answer.

INCORRECT: "Amazon SES" is incorrect as this service only sends email, not SMS text messages.

INCORRECT: "Amazon SQS" is incorrect as this is a hosted message queue for decoupling application components.

INCORRECT: "Amazon SWF" is incorrect as the Simple Workflow Service is used for orchestrating multi-step workflows.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/welcome.html>

</details>

---

<a id="t02-q25"></a>
### T02-Q25
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

To reduce the cost of API actions performed on an Amazon SQS queue, a Developer has decided to implement long polling. Which of the following modifications should the Developer make to the API actions?

- ✅ Set the ReceiveMessage API with a WaitTimeSeconds of 20
- ▫️ Set the ReceiveMessage API with a VisibilityTimeout of 30
- ▫️ Set the SetQueueAttributes with a MessageRetentionPeriod of 60
- ▫️ Set the SetQueueAttributes API with a DelaySeconds of 20

<details><summary>Пояснення (оригінал, EN)</summary>

The process of consuming messages from a queue depends on whether you use short or long polling. By default, Amazon SQS uses short polling, querying only a subset of its servers (based on a weighted random distribution) to determine whether any messages are available for a response. You can use long polling to reduce your costs while allowing your consumers to receive messages as soon as they arrive in the queue.

When you consume messages from a queue using short polling, Amazon SQS samples a subset of its servers (based on a weighted random distribution) and returns messages from only those servers. Thus, a particular ReceiveMessage request might not return all of your messages. However, if you have fewer than 1,000 messages in your queue, a subsequent request will return your messages. If you keep consuming from your queues, Amazon SQS samples all of its servers, and you receive all of your messages.

The following diagram shows the short-polling behavior of messages returned from a standard queue after one of your system components makes a receive request. Amazon SQS samples several of its servers (in gray) and returns messages A, C, D, and B from these servers. Message E isn't returned for this request but is returned for a subsequent request.

When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response).

Long polling occurs when the WaitTimeSeconds parameter of a ReceiveMessage request is set to a value greater than 0 in one of two ways:

The ReceiveMessage call sets WaitTimeSeconds to a value greater than 0.

The ReceiveMessage call doesn’t set WaitTimeSeconds, but the queue attribute ReceiveMessageWaitTimeSeconds is set to a value greater than 0.

Therefore, the Developer should set the ReceiveMessage API with a WaitTimeSeconds of 20.

CORRECT: "Set the ReceiveMessage API with a WaitTimeSeconds of 20" is the correct answer.

INCORRECT: "Set the SetQueueAttributes API with a DelaySeconds of 20" is incorrect as this would be used to configure a delay queue where the delivery of messages in the queue is delayed.

INCORRECT: "Set the ReceiveMessage API with a VisibilityTimeout of 30" is incorrect as this would configure the visibility timeout which is the length of time a message that has been received is invisible.

INCORRECT: "Set the SetQueueAttributes with a MessageRetentionPeriod of 60" is incorrect as this would configure how long messages are retained in the queue.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html>

</details>

---

<a id="t03-q27"></a>
### T03-Q27
*⏭ пропущено · домен: офіційна мітка*

DevOps engineers are developing an order processing system where notifications are sent to a department whenever an order is placed for a product. The system also pushes identical notifications of the new order to a processing module that would allow EC2 instances to handle the fulfillment of the order. In the case of processing errors, the messages should be allowed to be re-processed at a later stage. The order processing system should be able to scale transparently without the need for any manual or programmatic provisioning of resources.

Which of the following solutions can be used to address this use-case in the most cost-efficient way?

- ✅ SNS + SQS
- ▫️ SQS + SES
- ▫️ SNS + Kinesis
- ▫️ SNS + Lambda

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

SNS + SQS

Amazon SNS enables message filtering and fanout to a large number of subscribers, including serverless functions, queues, and distributed systems. Additionally, Amazon SNS fans out notifications to end users via mobile push messages, SMS, and email.

How SNS Works:  via - https://aws.amazon.com/sns/

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues. Standard queues offer maximum throughput, best-effort ordering, and at-least-once delivery. SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent.

Because each buffered request can be processed independently, Amazon SQS can scale transparently to handle the load without any provisioning instructions from you.

SNS and SQS can be used to create a fanout messaging scenario in which messages are "pushed" to multiple subscribers, which eliminates the need to periodically check or poll for updates and enables parallel asynchronous processing of the message by the subscribers. SQS can allow for later re-processing and dead letter queues. This is called the fan-out pattern.

Incorrect options:

SNS + Kinesis - You can use Amazon Kinesis Data Streams to collect and process large streams of data records in real-time. Kinesis Data Streams stores records from 24 hours (by default) to 8760 hours (365 days). However, you need to manually provision shards in case the load increases or you need to use CloudWatch alarms to set up auto scaling for the shards. Since Kinesis only supports transparent scaling in the on-demand mode, however, it is not cost efficient for the given use case, so this option is not the right fit for the given use case.

SNS + Lambda - Amazon SNS and AWS Lambda are integrated so you can invoke Lambda functions with Amazon SNS notifications. The Lambda function receives the message payload as an input parameter and can manipulate the information in the message, publish the message to other SNS topics, or send the message to other AWS services. However, your EC2 instances cannot "poll" from Lambda functions and as such, this would not work.

SQS + SES - This will not work as the messages need to be processed twice (once for sending the notification and later for order fulfillment) and SQS only allows for one consuming application.

Джерела: <https://aws.amazon.com/sns/> · <https://aws.amazon.com/getting-started/tutorials/send-fanout-event-notifications/>

</details>

---

<a id="t03-q33"></a>
### T03-Q33
*⏭ пропущено · домен: офіційна мітка*

A senior cloud engineer designs and deploys online fraud detection solutions for credit card companies processing millions of transactions daily. The Elastic Beanstalk application sends files to Amazon S3 and then sends a message to an Amazon SQS queue containing the path of the uploaded file in S3. The engineer wants to postpone the delivery of any new messages to the queue for at least 10 seconds.

Which SQS feature should the engineer leverage?

- ▫️ Use visibility timeout parameter
- ✅ Use DelaySeconds parameter
- ▫️ Implement application-side delay
- ▫️ Enable LongPolling

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use DelaySeconds parameter

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues. Standard queues offer maximum throughput, best-effort ordering, and at-least-once delivery. SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent.

Delay queues let you postpone the delivery of new messages to a queue for several seconds, for example, when your consumer application needs additional time to process messages. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html

Incorrect options:

Implement application-side delay - You can customize your application to delay sending messages but it is not a robust solution. You can run into a scenario where your application crashes before sending a message, then that message would be lost.

Use visibility timeout parameter - Visibility timeout is a period during which Amazon SQS prevents other consumers from receiving and processing a given message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours. You cannot use visibility timeout to postpone the delivery of new messages to the queue for a few seconds.

Enable LongPolling - Long polling makes it inexpensive to retrieve messages from your Amazon SQS queue as soon as the messages are available. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response). When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds. You cannot use LongPolling to postpone the delivery of new messages to the queue for a few seconds.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html

</details>

---

<a id="t03-q53"></a>
### T03-Q53
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **SQS max = 1 MiB (оновлено у 2025).** → [детальніше](../../06-contradictions-and-traps.md#c10)

Your company has been hired to build a resilient mobile voting app for an upcoming music award show that expects to have 5 to 20 million viewers. The mobile voting app will be marketed heavily months in advance so you are expected to handle millions of messages in the system. You are configuring Amazon Simple Queue Service (SQS) queues for your architecture that should receive messages from 20 KiB to 200 KiB.

Is it possible to send these messages to SQS?

- ▫️ No, the max message size is 64 KiB
- ▫️ Yes, the max message size is 512 KiB
- ▫️ No, the max message size is 128 KiB
- ✅ Yes, the max message size is 1024 KiB

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Yes, the max message size is 1024 KiB

The minimum message size is 1 byte (1 character). The maximum is 1,048,576 bytes (1024 KiB).

 via - https://aws.amazon.com/sqs/faqs/

Incorrect options:

Yes, the max message size is 512KB - The max size is 1024 KiB.

No, the max message size is 128KB - The max size is 1024 KiB.

No, the max message size is 64KB - The max size is 1024 KiB.

Reference:

https://aws.amazon.com/sqs/faqs/

</details>

---

<a id="t03-q60"></a>
### T03-Q60
*⏭ пропущено · домен: офіційна мітка*

You work as a developer doing contract work for the government on AWS gov cloud. Your applications use Amazon Simple Queue Service (SQS) for its message queue service. Due to recent hacking attempts, security measures have become stricter and require you to store data in encrypted queues.

Which of the following steps can you take to meet your requirements without making changes to the existing code?

- ▫️ Use Client side encryption
- ▫️ Use Secrets Manager
- ✅ Enable SQS KMS encryption
- ▫️ Use the SSL endpoint

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable SQS KMS encryption

Server-side encryption (SSE) lets you transmit sensitive data in encrypted queues. SSE protects the contents of messages in queues using keys managed in AWS Key Management Service (AWS KMS).

AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud. When you use Amazon SQS with AWS KMS, the data keys that encrypt your message data are also encrypted and stored with the data they protect.

You can choose to have SQS encrypt messages stored in both Standard and FIFO queues using an encryption key provided by AWS Key Management Service (KMS).

Incorrect options:

Use the SSL endpoint - The given use-case needs encryption at rest. When using SSL, the data is encrypted during transit, but the data needs to be encrypted at rest as well, so this option is incorrect.

Use Client-side encryption - For additional security, you can build your application to encrypt messages before they are placed in a message queue but will require a code change, so this option is incorrect.

\*Use Secrets Manager \* - AWS Secrets Manager enables you to easily rotate, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle. Users and applications retrieve secrets with a call to Secrets Manager APIs, eliminating the need to hardcode sensitive information in plain text. Secrets Manager offers secret rotation with built-in integration for Amazon RDS, Amazon Redshift, and Amazon DocumentDB. Secrets Manager cannot be used for encrypting data at rest.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-server-side-encryption.html

</details>

---

<a id="t04-q32"></a>
### T04-Q32
*⏭ пропущено · домен: визначено автоматично (у джерелі: —)*

> ⚠️ **1 GB → S3 + Extended Client (актуально).** → [детальніше](../../06-contradictions-and-traps.md#c10)

A developer is creating a multi-tier web application. The front-end will place messages in an Amazon SQS queue for the back-end to process. Each job includes a file that is 1GB in size. What MUST the developer do to ensure this works as expected?

- ▫️ Create a FIFO queue that supports large files
- ▫️ Store the large files in DynamoDB and use the SQS Extended Client Library for Java to manage SQS messages
- ▫️ Increase the maximum message size of the queue from 256KB to 1GB
- ✅ Store the large files in Amazon S3 and use the SQS Extended Client Library for Java to manage SQS messages

<details><summary>Пояснення (оригінал, EN)</summary>

You can use Amazon S3 and the Amazon SQS Extended Client Library for Java to manage Amazon SQS messages. This is especially useful for storing and consuming messages up to 2 GB in size. Unless your application requires repeatedly creating queues and leaving them inactive or storing large amounts of data in your queue, consider using Amazon S3 for storing your data.

You can use the Amazon SQS Extended Client Library for Java library to do the following:

• Specify whether messages are always stored in Amazon S3 or only when the size of a message exceeds 256 KB.

• Send a message that references a single message object stored in an Amazon S3 bucket.

• Get the corresponding message object from an Amazon S3 bucket.

• Delete the corresponding message object from an Amazon S3 bucket.

Note: Amazon SQS only supports messages up to 256KB in size. Therefore, the extended client library for Java must be used.

CORRECT: "Store the large files in Amazon S3 and use the SQS Extended Client Library for Java to manage SQS messages" is the correct answer.

INCORRECT: "Increase the maximum message size of the queue from 256KB to 1GB" is incorrect as you cannot increase the maximum message size above 256KB.

INCORRECT: "Store the large files in DynamoDB and use the SQS Extended Client Library for Java to manage SQS messages" is incorrect as you should store the files in Amazon S3.

INCORRECT: "Create a FIFO queue that supports large files " is incorrect as FIFO queues also have a maximum message size of 256KB.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-s3-messages.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/working-java-example-using-s3-for-large-sqs-messages.html>

</details>

---

<a id="t04-q35"></a>
### T04-Q35
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A mobile application runs as a serverless application on AWS. A Developer needs to create a push notification feature that sends periodic message to subscribers. How can the Developer send the notification from the application?

- ▫️ Publish a notification to Amazon CloudWatch Events
- ▫️ Publish a message to an Amazon SWF Workflow
- ▫️ Publish a message to an Amazon SQS Queue
- ✅ Publish a notification to an Amazon SNS Topic

<details><summary>Пояснення (оригінал, EN)</summary>

With Amazon SNS, you have the ability to send push notification messages directly to apps on mobile devices. Push notification messages sent to a mobile endpoint can appear in the mobile app as message alerts, badge updates, or even sound alerts.

You send push notification messages to both mobile devices and desktops using one of the following supported push notification services:

• Amazon Device Messaging (ADM)

• Apple Push Notification Service (APNs) for both iOS and Mac OS X

• Baidu Cloud Push (Baidu)

• Firebase Cloud Messaging (FCM)

• Microsoft Push Notification Service for Windows Phone (MPNS)

• Windows Push Notification Services (WNS)

To send a notification to an Amazon SNS subscriber, the application needs to send the notification to an Amazon SNS Topic. Amazon SNS will then send the notification to the relevant subscribers.

CORRECT: "Publish a notification to an Amazon SNS Topic" is the correct answer.

INCORRECT: "Publish a message to an Amazon SQS Queue" is incorrect as SQS is a message queue service, not a notification service.

INCORRECT: "Publish a notification to Amazon CloudWatch Events" is incorrect as CloudWatch Events will not be able to send notifications to mobile app users.

INCORRECT: "Publish a message to an Amazon SWF Workflow" is incorrect as SWF is a workflow orchestration service and it is not used for publishing messages to mobile app users.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-how-user-notifications-work.html>

</details>

---

<a id="t04-q56"></a>
### T04-Q56
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

An application collects data from sensors in a manufacturing facility. The data is stored in an Amazon SQS Standard queue by an AWS Lambda function and an Amazon EC2 instance processes the data and stores it in an Amazon RedShift data warehouse. A fault in the sensors’ software is causing occasional duplicate messages to be sent. Timestamps on the duplicate messages show they are generated within a few seconds of the primary message.

How can a Developer prevent duplicate data being stored in the data warehouse?

- ▫️ Send a ChangeMessageVisibility call with VisibilityTimeout set to 30 seconds after the receipt of every message from the queue
- ✅ Use a FIFO queue and configure the Lambda function to add a message deduplication token to the message body
- ▫️ Use a FIFO queue and configure the Lambda function to add a message group ID to the messages generated by each individual sensor
- ▫️ Configure a redrive policy, specify a destination Dead-Letter queue, and set the maxReceiveCount to 1

<details><summary>Пояснення (оригінал, EN)</summary>

FIFO (First-In-First-Out) queues are designed to enhance messaging between applications when the order of operations and events is critical, or where duplicates can't be tolerated.

In FIFO queues, messages are ordered based on message group ID. If multiple hosts (or different threads on the same host) send messages with the same message group ID to a FIFO queue, Amazon SQS stores the messages in the order in which they arrive for processing. To ensure that Amazon SQS preserves the order in which messages are sent and received, ensure that each producer uses a unique message group ID to send all its messages.

FIFO queue logic applies only per message group ID. Each message group ID represents a distinct ordered message group within an Amazon SQS queue. For each message group ID, all messages are sent and received in strict order. However, messages with different message group ID values might be sent and received out of order. You must associate a message group ID with a message. If you don't provide a message group ID, the action fails. If you require a single group of ordered messages, provide the same message group ID for messages sent to the FIFO queue.

Therefore, the Developer can use a FIFO queue and configure the Lambda function to add a message deduplication token to the message body. This will ensure that the messages are deduplicated before being picked up for processing by the Amazon EC2 instance.

CORRECT: "Use a FIFO queue and configure the Lambda function to add a message deduplication token to the message body" is the correct answer.

INCORRECT: "Use a FIFO queue and configure the Lambda function to add a message group ID to the messages generated by each individual sensor" is incorrect. The message group ID is the tag that specifies that a message belongs to a specific message group. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group.

INCORRECT: "Send a ChangeMessageVisibility call with VisibilityTimeout set to 30 seconds after the receipt of every message from the queue" is incorrect as this will just change the visibility timeout for the message which will prevent others from seeing it until it has been processed and deleted from the queue. This doesn’t stop a message with duplicate data being processed.

INCORRECT: "Configure a redrive policy, specify a destination Dead-Letter queue, and set the maxReceiveCount to 1" is incorrect as without a FIFO queue and a message deduplication ID duplicate messages will still enter the queue. The redrive policy only applies to individual messages for which processing has failed a number of times as specified in the maxReceiveCount.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/using-messagededuplicationid-property.html>

</details>

---

<a id="t05-q29"></a>
### T05-Q29
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Для Lambda+SQS реальний параметр — BatchSize event source mapping.** → [детальніше](../../07-outdated-and-wrong-answers.md#d09)

A company runs a decoupled application that uses an Amazon SQS queue. The messages are processed by an AWS Lambda function. The function is not keeping up with the number of messages in the queue. A developer noticed that though the application can process multiple messages per invocation, it is only processing one at a time.

How can the developer configure the application to process messages more efficiently?

- ✅ Call the ReceiveMessage API to set MaxNumberOfMessages to a value greater than the default of 1.
- ▫️ Call the ReceiveMessage API to set MaximumMessageSize to a value greater than the default of 1.
- ▫️ Call the ChangeMessageVisibility API for the queue and set MessageRetentionPeriod to a value greater than the default of 1.
- ▫️ Call the SetQueueAttributes API for the queue and set MaxNumberOfMessages to a value greater than the default of 1.

<details><summary>Пояснення (оригінал, EN)</summary>

The ReceiveMessage API retrieves one or more messages (up to 10), from the specified queue. The MaxNumberOfMessages specifies the maximum number of messages to return. Amazon SQS never returns more messages than this value (however, fewer messages might be returned). Valid values: 1 to 10. Default: 1.

Changing the MaxNumberOfMessages using the ReceiveMessage API to a value greater than 1 will therefore enable the application to process more messages in a single invocation, leading to greater efficiency.

CORRECT: "Call the ReceiveMessage API to set MaxNumberOfMessages to a value greater than the default of 1" is the correct answer (as explained above.)

INCORRECT: "Call the ReceiveMessage API to set MaximumMessageSize to a value greater than the default of 1" is incorrect.

MaximumMessageSize specifies the maximum bytes a message can contain before SQS rejects it.

INCORRECT: "Call the ChangeMessageVisibility API for the queue and set MessageRetentionPeriod to a value greater than the default of 1" is incorrect.

ChangeMessageVisibility changes the visibility timeout of a specified message in a queue to a new value.

INCORRECT: "Call the SetQueueAttributes API for the queue and set MaxNumberOfMessages to a value greater than the default of 1" is incorrect.

MaxNumberOfMessages is configured using the ReceiveMessage API, not the SetQueueAttributes API.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html>

</details>

---

<a id="t05-q62"></a>
### T05-Q62
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

Messages produced by an application must be pushed to multiple Amazon SQS queues. What is the BEST solution for this requirement?

- ▫️ Create an Amazon SWF workflow that receives the messages and pushes them to multiple SQS queues
- ▫️ Create and AWS Step Functions state machine that uses multiple Lambda functions to process and push the messages into multiple SQS queues
- ▫️ Publish the messages to an Amazon SQS queue and configure an AWS Lambda function to duplicate the message into multiple queues
- ✅ Publish the messages to an Amazon SNS topic and subscribe each SQS queue to the topic

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon SNS works closely with Amazon Simple Queue Service (Amazon SQS). Both services provide different benefits for developers. Amazon SNS allows applications to send time-critical messages to multiple subscribers through a “push” mechanism, eliminating the need to periodically check or “poll” for updates.

When you subscribe an Amazon SQS queue to an Amazon SNS topic, you can publish a message to the topic and Amazon SNS sends an Amazon SQS message to the subscribed queue. The Amazon SQS message contains the subject and message that were published to the topic along with metadata about the message in a JSON document.

CORRECT: "Publish the messages to an Amazon SNS topic and subscribe each SQS queue to the topic" is the correct answer.

INCORRECT: "Publish the messages to an Amazon SQS queue and configure an AWS Lambda function to duplicate the message into multiple queues" is incorrect as this seems like an inefficient solution. By using SNS we can eliminate the initial queue and Lambda function.

INCORRECT: "Create an Amazon SWF workflow that receives the messages and pushes them to multiple SQS queues" is incorrect as this is not a workable solution. Amazon SWF is not suitable for pushing messages to SQS queues.

INCORRECT: Create and AWS Step Functions state machine that uses multiple Lambda functions to process and push the messages into multiple SQS queues"" is incorrect as this is an inefficient solution and there is not mention on how the functions will be invoked with the message data

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-sqs-as-subscriber.html>

</details>

---

<a id="t06-q30"></a>
### T06-Q30
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A developer needs to create a serverless application that uses an event-driven architecture.

How can the developer configure the application to automatically receive and process events?

- ▫️ Create an Amazon SQS queue and publish events to the queue. Configure an Amazon EC2 instance to poll the queue and consume the messages.
- ▫️ Create an Amazon SNS topic and an AWS Lambda function. Configure an HTTP endpoint on the Lambda function and subscribe the HTTP endpoint to the SNS topic. Submit events to the SNS topic.
- ✅ Create an Amazon SNS topic and an AWS Lambda function. Subscribe the Lambda function to the SNS topic and submit events to the SNS topic.
- ▫️ Create an Amazon SQS topic and an Amazon EC2 instance. Subscribe the instance to the SNS topic and submit events to the topic.

<details><summary>Пояснення (оригінал, EN)</summary>

You can use a Lambda function to process Amazon Simple Notification Service (Amazon SNS) notifications. Amazon SNS supports Lambda functions as a target for messages sent to a topic. You can subscribe your function to topics in the same account or in other AWS accounts.

When an event is submitted to the SNS topic it will be sent to AWS Lambda which can then process the event. This is an example of a simple event-driven architecture.

CORRECT: "Create an Amazon SNS topic and an AWS Lambda function. Subscribe the Lambda function to the SNS topic and submit events to the SNS topic" is the correct answer (as explained above.)

INCORRECT: "Create an Amazon SQS queue and publish events to the queue. Configure an Amazon EC2 instance to poll the queue and consume the messages" is incorrect.

Amazon EC2 is not a serverless service so cannot if the application must be serverless.

INCORRECT: "Create an Amazon SQS topic and an Amazon EC2 instance. Subscribe the instance to the SNS topic and submit events to the topic" is incorrect.

EC2 is not serverless and cannot be subscribed to an SNS topic.

INCORRECT: "Create an Amazon SNS topic and an AWS Lambda function. Configure an HTTP endpoint on the Lambda function and subscribe the HTTP endpoint to the SNS topic. Submit events to the SNS topic" is incorrect.

An HTTP endpoint is not required for the serverless application. Lambda can be subscribed directly to an SNS topic.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/with-sns.html>

</details>

---

<a id="t07-q40"></a>
### T07-Q40
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Застаріле: ліміт 256 KB → тепер 1 MiB. Extended Client — для > 1 MiB.** → [детальніше](../../07-outdated-and-wrong-answers.md#o01)

A media company uses Amazon Simple Queue Service (SQS) queue to manage their transactions. With changing business needs, the payload size of the messages is increasing. The Team Lead of the project is worried about the 256 KB message size limit that SQS has.

What can be done to make the queue accept messages of a larger size?

- ▫️ Get a service limit increase from AWS
- ✅ Use the SQS Extended Client
- ▫️ Use the MultiPart API
- ▫️ Use gzip compression

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the SQS Extended Client - To manage large Amazon Simple Queue Service (Amazon SQS) messages, you can use Amazon Simple Storage Service (Amazon S3) and the Amazon SQS Extended Client Library for Java. This is especially useful for storing and consuming messages up to 2 GB. Unless your application requires repeatedly creating queues and leaving them inactive or storing large amounts of data in your queues, consider using Amazon S3 for storing your data.

Incorrect options:

Use the MultiPart API - This is an incorrect statement. There is no multi-part API for Amazon Simple Queue Service.

Get a service limit increase from AWS - While it is possible to get service limits extended for certain AWS services, AWS already offers Extended Client to deal with queues that have larger messages.

Use gzip compression - You can compress the messages before sending them to the queue. The messages also need to be encoded after this to cater to SQS message standards. This adds bulk to the messages and will not be an optimal solution for the current scenario.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-s3-messages.html

</details>

---

<a id="t07-q62"></a>
### T07-Q62
*⏭ пропущено · домен: офіційна мітка*

A company’s e-commerce website is expecting hundreds of thousands of visitors on Black Friday. The marketing department is concerned that high volumes of orders might stress SQS leading to message failures. The company has approached you for the steps to be taken as a precautionary measure against the high volumes.

What step will you suggest as a Developer Associate?

- ▫️ Convert the queue into FIFO ordered queue, since messages to the down system will be processed faster once they are ordered
- ▫️ Enable auto-scaling in the SQS queue
- ✅ Amazon SQS is highly scalable and does not need any intervention to handle the expected high volumes
- ▫️ Pre-configure the SQS queue to increase the capacity when messages hit a certain threshold

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon SQS is highly scalable and does not need any intervention to handle the expected high volumes

Amazon SQS leverages the AWS cloud to dynamically scale, based on demand. SQS scales elastically with your application so you don't have to worry about capacity planning and pre-provisioning. For most standard queues (depending on queue traffic and message backlog), there can be a maximum of approximately 120,000 inflight messages (received from a queue by a consumer, but not yet deleted from the queue).

Info on Queue Quotas:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-quotas.html

Incorrect options:

Pre-configure the SQS queue to increase the capacity when messages hit a certain threshold - This is an incorrect statement. Amazon SQS scales dynamically, automatically provisioning the needed capacity.

Enable auto-scaling in the SQS queue - SQS queues are, by definition, auto-scalable and do not need any configuration changes for auto-scaling.

Convert the queue into FIFO ordered queue, since messages to the down system will be processed faster once they are ordered - This is a wrong statement. You cannot convert an existing standard queue to FIFO queue. To make the move, you must either create a new FIFO queue for your application or delete your existing standard queue and recreate it as a FIFO queue.

Standard to FIFO queue conversion:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-quotas.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html>

</details>

---

<a id="t08-q15"></a>
### T08-Q15
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company is in the process of migrating an application from a monolithic architecture to a microservices-based architecture. The developers need to refactor the application so that the many microservices can asynchronously communicate with each other in a decoupled manner.

Which AWS services can be used for asynchronous message passing? (Select TWO.)

- ▫️ AWS Lambda
- ✅ Amazon SNS
- ▫️ Amazon Kinesis
- ✅ Amazon SQS
- ▫️ Amazon ECS

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

Amazon Simple Notification Service (SNS) is a highly available, durable, secure, fully managed pub/sub messaging service that enables you to decouple microservices, distributed systems, and serverless applications.

These services both enable asynchronous message passing in the form of a message bus (SQS) and notifications (SNS).

CORRECT: "Amazon SQS" is the correct answer.

CORRECT: "Amazon SNS" is also a correct answer.

INCORRECT: "Amazon Kinesis" is incorrect. Kinesis is used for streaming data, it is used for real-time analytics, mobile data capture and IoT and similar use cases.

INCORRECT: "Amazon ECS" is incorrect. ECS is a service providing Docker containers on Amazon EC2.

INCORRECT: "AWS Lambda" is incorrect. AWS Lambda is a compute service that runs functions in response to triggers.

Джерела: <https://aws.amazon.com/sqs/> · <https://aws.amazon.com/sns/>

</details>

---

<a id="t08-q18"></a>
### T08-Q18
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company uses an Amazon Simple Queue Service (SQS) Standard queue for an application. An issue has been identified where applications are picking up messages from the queue that are still being processed causing duplication. What can a Developer do to resolve this issue?

- ▫️ Increase the ReceiveMessageWaitTimeSeconds API action on the queue
- ✅ Increase the VisibilityTimeout API action on the queue
- ▫️ Increase the DelaySeconds API action on the queue
- ▫️ Create a RedrivePolicy for the queue

<details><summary>Пояснення (оригінал, EN)</summary>

When a consumer receives and processes a message from a queue, the message remains in the queue. Amazon SQS doesn't automatically delete the message. Because Amazon SQS is a distributed system, there's no guarantee that the consumer actually receives the message (for example, due to a connectivity issue, or due to an issue in the consumer application). Thus, the consumer must delete the message from the queue after receiving and processing it.

Immediately after a message is received, it remains in the queue. To prevent other consumers from processing the message again, Amazon SQS sets a visibility timeout, a period of time during which Amazon SQS prevents other consumers from receiving and processing the message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours.

Therefore, the best thing the Developer can do in this situation is to increase the VisibilityTimeout API action on the queue

CORRECT: "Increase the VisibilityTimeout API action on the queue" is the correct answer.

INCORRECT: "Increase the DelaySeconds API action on the queue" is incorrect as this controls the length of time, in seconds, for which the delivery of all messages in the queue is delayed.

INCORRECT: "Increase the ReceiveMessageWaitTimeSeconds API action on the queue" is incorrect as this is the length of time, in seconds, for which a ReceiveMessage action waits for a message to arrive. This is used to configure long polling.

INCORRECT: "Create a RedrivePolicy for the queue" is incorrect as this is a string that includes the parameters for the dead-letter queue functionality of the source queue as a JSON object.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html>

</details>

---

<a id="t08-q62"></a>
### T08-Q62
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company uses Amazon SQS to decouple an online application that generates memes. The SQS consumers poll the queue regularly to keep throughput high and this is proving to be costly and resource intensive. A Developer has been asked to review the system and propose changes that can reduce costs and the number of empty responses.

What would be the BEST approach to MINIMIZING cost?

- ✅ Set the Imaging queue ReceiveMessageWaitTimeSeconds attribute to 20 seconds
- ▫️ Set the DelaySeconds parameter of a message to 20 seconds
- ▫️ Set the imaging queue MessageRetentionPeriod attribute to 20 seconds
- ▫️ Set the imaging queue visibility Timeout attribute to 20 seconds

<details><summary>Пояснення (оригінал, EN)</summary>

The process of consuming messages from a queue depends on whether you use short or long polling. By default, Amazon SQS uses short polling, querying only a subset of its servers (based on a weighted random distribution) to determine whether any messages are available for a response. You can use long polling to reduce your costs while allowing your consumers to receive messages as soon as they arrive in the queue.

When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response).

Therefore, the best way to optimize resource usage and reduce the number of empty responses (and cost) is to configure long polling by setting the Imaging queue ReceiveMessageWaitTimeSeconds attribute to 20 seconds.

CORRECT: "Set the Imaging queue ReceiveMessageWaitTimeSeconds attribute to 20 seconds" is the correct answer.

INCORRECT: "Set the imaging queue visibility Timeout attribute to 20 seconds" is incorrect. This attribute configures message visibility which will not reduce empty responses.

INCORRECT: "Set the imaging queue MessageRetentionPeriod attribute to 20 seconds" is incorrect. This attribute sets the length of time, in seconds, for which Amazon SQS retains a message.

INCORRECT: "Set the DelaySeconds parameter of a message to 20 seconds" is incorrect. This attribute sets the length of time, in seconds, for which the delivery of all messages in the queue is delayed.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html>

</details>

---

<a id="t09-q46"></a>
### T09-Q46
*⏭ пропущено · домен: офіційна мітка*

The development team at an analytics company is using SQS queues for decoupling the various components of application architecture. As the consumers need additional time to process SQS messages, the development team wants to postpone the delivery of new messages to the queue for a few seconds.

As a Developer Associate, which of the following solutions would you recommend to the development team?

- ✅ Use delay queues to postpone the delivery of new messages to the queue for a few seconds
- ▫️ Use visibility timeout to postpone the delivery of new messages to the queue for a few seconds
- ▫️ Use dead-letter queues to postpone the delivery of new messages to the queue for a few seconds
- ▫️ Use FIFO queues to postpone the delivery of new messages to the queue for a few seconds

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use delay queues to postpone the delivery of new messages to the queue for a few seconds

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues. Standard queues offer maximum throughput, best-effort ordering, and at-least-once delivery. SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent.

Delay queues let you postpone the delivery of new messages to a queue for several seconds, for example, when your consumer application needs additional time to process messages. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html

Incorrect options:

Use FIFO queues to postpone the delivery of new messages to the queue for a few seconds - SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent. You cannot use FIFO queues to postpone the delivery of new messages to the queue for a few seconds.

Use dead-letter queues to postpone the delivery of new messages to the queue for a few seconds - Dead-letter queues can be used by other queues (source queues) as a target for messages that can't be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed. You cannot use dead-letter queues to postpone the delivery of new messages to the queue for a few seconds.

Use visibility timeout to postpone the delivery of new messages to the queue for a few seconds - Visibility timeout is a period during which Amazon SQS prevents other consumers from receiving and processing a given message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours. You cannot use visibility timeout to postpone the delivery of new messages to the queue for a few seconds.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html

</details>

---

<a id="t10-q04"></a>
### T10-Q04
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

What does an Amazon SQS delay queue accomplish?

- ▫️ The consumer can poll the queue for a configurable amount of time before retrieving a message
- ▫️ Messages are hidden for a configurable amount of time after they are consumed from the queue
- ✅ Messages are hidden for a configurable amount of time when they are first added to the queue
- ▫️ Message cannot be deleted for a configurable amount of time after they are consumed from the queue

<details><summary>Пояснення (оригінал, EN)</summary>

Delay queues let you postpone the delivery of new messages to a queue for a number of seconds, for example, when your consumer application needs additional time to process messages.

If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes.

Therefore, the correct explanation is that with an Amazon SQS Delay Queue messages are hidden for a configurable amount of time when they are first added to the queue

CORRECT: "Messages are hidden for a configurable amount of time when they are first added to the queue" is the correct answer.

INCORRECT: "Messages are hidden for a configurable amount of time after they are consumed from the queue" is incorrect. They are hidden when they are added to the queue.

INCORRECT: "The consumer can poll the queue for a configurable amount of time before retrieving a message" is incorrect. A delay queue simply delays visibility of the message, it does not affect polling behavior.

INCORRECT: "Message cannot be deleted for a configurable amount of time after they are consumed from the queue" is incorrect. That is what a visibility timeout achieves.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html>

</details>

---

<a id="t10-q26"></a>
### T10-Q26
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer is managing an application that includes an Amazon SQS queue. The consumers that process the data from the queue are connecting in short cycles and the queue often does not return messages. The cost for API calls is increasing. How can the Developer optimize the retrieval of messages and reduce cost?

- ▫️ Call the SetQueueAttributes API with the maxReceiveCount set to 20
- ✅ Call the ReceiveMessage API with the WaitTimeSeconds parameter set to 20
- ▫️ Call the SetQueueAttributes API with the DelaySeconds parameter set to 900
- ▫️ Call the ReceiveMessage API with the VisibilityTimeout parameter set to 30

<details><summary>Пояснення (оригінал, EN)</summary>

The process of consuming messages from a queue depends on whether you use short or long polling. By default, Amazon SQS uses short polling, querying only a subset of its servers (based on a weighted random distribution) to determine whether any messages are available for a response. You can use long polling to reduce your costs while allowing your consumers to receive messages as soon as they arrive in the queue.

When you consume messages from a queue using short polling, Amazon SQS samples a subset of its servers (based on a weighted random distribution) and returns messages from only those servers. Thus, a particular ReceiveMessage request might not return all of your messages. However, if you have fewer than 1,000 messages in your queue, a subsequent request will return your messages. If you keep consuming from your queues, Amazon SQS samples all of its servers, and you receive all of your messages.

When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response)

Therefore, the Developer should call the ReceiveMessage API with the WaitTimeSeconds parameter set to 20 to enable long polling.

CORRECT: "Call the ReceiveMessage API with the WaitTimeSeconds parameter set to 20 " is the correct answer.

INCORRECT: "Call the ReceiveMessage API with the VisibilityTimeout parameter set to 30" is incorrect

INCORRECT: "Call the SetQueueAttributes API with the DelaySeconds parameter set to 900" is incorrect

INCORRECT: "Call the SetQueueAttributes API with the maxReceiveCount set to 20" is incorrect

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SetQueueAttributes.html>

</details>

---

<a id="t10-q29"></a>
### T10-Q29
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A media organization utilizes an Amazon API Gateway REST API endpoint to disseminate updates from an internal Content Management System (CMS) to Amazon EventBridge. An EventBridge rule is set up to monitor these updates and control content syndication in a primary AWS account. The organization now wants to extend the reach of these updates across several affiliate AWS accounts.

How can the developer accomplish this without altering the configuration of the CMS?

- ▫️ Set up multiple instances of the CMS, each linked to a different AWS account.
- ▫️ Use AWS Lambda in the main account to clone updates and manually invoke it in affiliate accounts.
- ▫️ Establish multiple API Gateway REST API endpoints in affiliate accounts to directly receive updates from the CMS.
- ✅ Implement an EventBridge event bus in the affiliate AWS accounts to create a rule that matches events and forwards them from the main account.

<details><summary>Пояснення (оригінал, EN)</summary>

EventBridge allows events to be sent to other AWS accounts, providing a straightforward solution to distribute the updates across multiple accounts. This wouldn't require any changes to the CMS configuration.

CORRECT: "Implement an EventBridge event bus in the affiliate AWS accounts to create a rule that matches events and forwards them from the main account" is the correct answer (as explained above.)

INCORRECT: "Establish multiple API Gateway REST API endpoints in affiliate accounts to directly receive updates from the CMS" is incorrect.

Setting up multiple API Gateway REST API endpoints in affiliate accounts to directly receive updates from the CMS would not only require changes to the CMS configuration but would also mean maintaining multiple endpoints.

INCORRECT: "Use AWS Lambda in the main account to clone updates and manually invoke it in affiliate accounts" is incorrect.

Using AWS Lambda in the main account to clone updates and manually invoke it in affiliate accounts would be an inefficient solution, leading to potential latency and management overhead.

INCORRECT: "Set up multiple instances of the CMS, each linked to a different AWS account" is incorrect.

Setting up multiple instances of the CMS, each linked to a different AWS account, would be a significantly complex and resource-intensive solution. It's neither practical nor necessary for the given requirement.

Джерела: <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-cross-account.html>

</details>

---

<a id="t10-q58"></a>
### T10-Q58
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Формулювання дивне; суть: long polling повертає повідомлення одразу.** → [детальніше](../../07-outdated-and-wrong-answers.md#d08)

A gaming application displays the results of games in a leaderboard. The leaderboard is updated by 4 KB messages that are retrieved from an Amazon SQS queue. The updates are received infrequently but the Developer needs to minimize the time between the messages arriving in the queue and the leaderboard being updated.

Which technique provides the shortest delay in updating the leaderboard?

- ▫️ Retrieve the messages from the queue using short polling every 10 seconds
- ▫️ Reduce the size of the messages with compression before sending them
- ✅ Retrieve the messages from the queue using long polling every 15 seconds
- ▫️ Store the message payload in Amazon S3 and use the SQS Extended Client Library for Java

<details><summary>Пояснення (оригінал, EN)</summary>

The process of consuming messages from a queue depends on whether you use short or long polling. By default, Amazon SQS uses short polling, querying only a subset of its servers (based on a weighted random distribution) to determine whether any messages are available for a response.

You can use long polling to reduce your costs while allowing your consumers to receive messages as soon as they arrive in the queue. When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds.

Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response). It also returns messages as soon as they become available.

CORRECT: "Retrieve the messages from the queue using long polling every 15 seconds" is the correct answer.

INCORRECT: "Retrieve the messages from the queue using short polling every 10 seconds" is incorrect as short polling is configured when the WaitTimeSeconds parameter of a ReceiveMessage request is set to 0. Any number above zero indicates long polling is in effect.

INCORRECT: "Reduce the size of the messages with compression before sending them" is incorrect as this will not mean messages are picked up earlier and there is no reason to compress messages that are 4 KB in size.

INCORRECT: "Store the message payload in Amazon S3 and use the SQS Extended Client Library for Java" is incorrect as this is unnecessary for messages of this size and will also not result in the shortest delay when updating the leaderboard.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html>

</details>

---

<a id="t11-q08"></a>
### T11-Q08
*⏭ пропущено · домен: офіційна мітка*

A developer is testing Amazon Simple Queue Service (SQS) queues in a development environment. The queue along with all its contents has to be deleted after testing.

Which SQS API should be used for this requirement?

- ▫️ RemovePermission
- ▫️ RemoveQueue
- ▫️ PurgeQueue
- ✅ DeleteQueue

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

DeleteQueue - Deletes the queue specified by the QueueUrl, regardless of the queue's contents. When you delete a queue, any messages in the queue are no longer available.

When you delete a queue, the deletion process takes up to 60 seconds. Requests you send involving that queue during the 60 seconds might succeed. For example, a SendMessage request might succeed, but after 60 seconds the queue and the message you sent no longer exist.

When you delete a queue, you must wait at least 60 seconds before creating a queue with the same name.

Incorrect options:

PurgeQueue - Deletes the messages in a queue specified by the QueueURL parameter. When you use the PurgeQueue action, you can't retrieve any messages deleted from a queue. The queue however remains.

RemoveQueue - This is an invalid option, given only as a distractor.

RemovePermission - Revokes any permissions in the queue policy that matches the specified Label parameter.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_RemovePermission.html

</details>

---

<a id="t11-q12"></a>
### T11-Q12
*⏭ пропущено · домен: офіційна мітка*

Amazon Simple Queue Service (SQS) has a set of APIs for various actions supported by the service.

As a developer associate, which of the following would you identify as correct regarding the CreateQueue API? (Select two)

- ✅ The visibility timeout value for the queue is in seconds, which defaults to 30 seconds
- ▫️ Queue tags are case insensitive. A new tag with a key identical to that of an existing tag overwrites the existing tag
- ▫️ The dead-letter queue of a FIFO queue must also be a FIFO queue. Whereas, the dead-letter queue of a standard queue can be a standard queue or a FIFO queue
- ✅ You can't change the queue type after you create it
- ▫️ The length of time, in seconds, for which the delivery of all messages in the queue is delayed is configured using MessageRetentionPeriod attribute

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

You can't change the queue type after you create it - You can't change the queue type after you create it and you can't convert an existing standard queue into a FIFO queue. You must either create a new FIFO queue for your application or delete your existing standard queue and recreate it as a FIFO queue.

The visibility timeout value for the queue is in seconds, which defaults to 30 seconds - The visibility timeout for the queue is in seconds. Valid values are: An integer from 0 to 43,200 (12 hours), the Default value is 30.

Incorrect options:

The dead-letter queue of a FIFO queue must also be a FIFO queue. Whereas, the dead-letter queue of a standard queue can be a standard queue or a FIFO queue - The dead-letter queue of a FIFO queue must also be a FIFO queue. Similarly, the dead-letter queue of a standard queue must also be a standard queue.

The length of time, in seconds, for which the delivery of all messages in the queue is delayed is configured using MessageRetentionPeriod attribute - The length of time, in seconds, for which the delivery of all messages in the queue is delayed is configured using DelaySeconds attribute. MessageRetentionPeriod attribute controls the length of time, in seconds, for which Amazon SQS retains a message.

Queue tags are case insensitive. A new tag with a key identical to that of an existing tag overwrites the existing tag - Queue tags are case-sensitive. A new tag with a key identical to that of an existing tag overwrites the existing tag. To be able to tag a queue on creation, you must have the sqs:CreateQueue and sqs:TagQueue permissions.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_CreateQueue.html

</details>

---

<a id="t12-q57"></a>
### T12-Q57
*⏭ пропущено · домен: офіційна мітка*

Your application sends messages to an Amazon Simple Queue Service (SQS) queue frequently, which are then polled by another application that specifies which message to retrieve.

Which of the following options describe the maximum number of messages that can be retrieved at one time?

- ▫️ 100
- ✅ 10
- ▫️ 5
- ▫️ 20

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

10

After you send messages to a queue, you can receive and delete them. When you request messages from a queue, you can't specify which messages to retrieve. Instead, you specify the maximum number of messages (up to 10) that you want to retrieve.

Incorrect options:

5

20

100

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-using-receive-delete-message.html

</details>

---

<a id="t12-q63"></a>
### T12-Q63
*⏭ пропущено · домен: офіційна мітка*

As a Developer, you are working on a mobile application that utilizes Amazon Simple Queue Service (SQS) for sending messages to downstream systems for further processing. One of the requirements is that the messages should be stored in the queue for a period of 12 days.

How will you configure this requirement?

- ▫️ Enable Long Polling for the SQS queue
- ▫️ The maximum retention period of SQS messages is 7 days, therefore retention period of 12 days is not possible
- ▫️ Use a FIFO SQS queue
- ✅ Change the queue message retention setting

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Change the queue message retention setting - Amazon SQS automatically deletes messages that have been in a queue for more than the maximum message retention period. The default message retention period is 4 days. However, you can set the message retention period to a value from 60 seconds to 1,209,600 seconds (14 days) using the SetQueueAttributes action.

More info here:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-architecture.html

Incorrect options:

Enable Long Polling for the SQS queue - Amazon SQS provides short polling and long polling to receive messages from a queue. By default, queues use short polling. When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response). This feature is not useful for the current use case.

The maximum retention period of SQS messages is 7 days, therefore retention period of 12 days is not possible - This is an incorrect statement. Retention period of up to 14 days is possible.

Use a FIFO SQS queue - FIFO (First-In-First-Out) queues are designed to enhance messaging between applications when the order of operations and events is critical, or where duplicates can't be tolerated. This is not useful for the current scenario.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-architecture.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html>

</details>

---
