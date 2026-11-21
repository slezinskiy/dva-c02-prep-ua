# SQS / SNS / EventBridge / SES

**Домен 4 — Усунення несправностей та оптимізація** · питань: 14  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#messaging) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q09"></a>
### T01-Q09
*⏭ пропущено · домен: офіційна мітка*

Applications running on EC2 instances process messages from an SQS queue but sometimes they experience errors due to messages not being processed.

To isolate the messages, which option will help with further debugging?

- ▫️ Use DeleteMessage
- ▫️ Reduce the VisibilityTimeout
- ▫️ Increase the VisibilityTimeout
- ✅ Implement a Dead Letter Queue

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Implement a Dead Letter Queue

Dead-letter queues can be used by other queues (source queues) as a target for messages that can't be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed.

Sometimes, messages can’t be processed because of a variety of possible issues, such as when a user comments on a story but it remains unprocessed because the original story itself is deleted by the author while the comments were being posted. In such a case, the dead-letter queue can be used to handle message processing failures.

How do dead-letter queues work?  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

Use-cases for dead-letter queues:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

Amazon SQS supports dead-letter queues, which other queues (source queues) can target for messages that cannot be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed.

Incorrect:

Use DeleteMessage - This API call deletes the message in the queue but does not help you find the issue.

Reduce the VisibilityTimeout - Amazon SQS uses a visibility timeout to prevent other consumers from receiving and processing the same message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours. If you reduce the VisibilityTimeout, more consumers will get the failed message

Increase the VisibilityTimeout - It won't help because you don't need more time but rather an isolated place to debug.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

</details>

---

<a id="t01-q15"></a>
### T01-Q15
*⏭ пропущено · домен: офіційна мітка*

A data analytics company ingests a large number of messages and stores them in an RDS database using Lambda. Because of the increased payload size, it is taking more than 15 minutes to process each message.

As a Developer Associate, which of the following options would you recommend to process each message in the MOST scalable way?

- ▫️ Use DynamoDB instead of RDS as database
- ▫️ Contact AWS Support to increase the Lambda timeout to 60 minutes
- ✅ Provision EC2 instances in an Auto Scaling group to poll the messages from an SQS queue
- ▫️ Provision an EC2 instance to poll the messages from an SQS queue

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Provision EC2 instances in an Auto Scaling group to poll the messages from an SQS queue

As each message takes more than 15 minutes to process, therefore the development team cannot use Lambda for message processing. To build a scalable solution, the EC2 instances must be provisioning via an Auto Scaling group to handle variations in the message processing workload.

Amazon EC2 Auto Scaling Overview:  via - https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html

Incorrect options:

Provision an EC2 instance to poll the messages from an SQS queue - Just using a single EC2 instance may not be sufficient to handle a sudden spike in the number of incoming messages.

Contact AWS Support to increase the Lambda timeout to 60 minutes - AWS Support cannot increase the Lambda timeout upper limit.

Use DynamoDB instead of RDS as database - This option has been added as a distractor, as changing the database would have no impact on the Lambda timeout while processing the message.

Reference:

https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html

</details>

---

<a id="t01-q35"></a>
### T01-Q35
*⏭ пропущено · домен: офіційна мітка*

You are using AWS SQS FIFO queues to get the ordering of messages on a per user_id basis. On top of this, you would like to make sure that duplicate messages should not be sent to SQS as this would cause application failure.

As a developer, which message parameter should you set for deduplicating messages?

- ▫️ MessageGroupId
- ▫️ ContentBasedDeduplication
- ✅ MessageDeduplicationId
- ▫️ ReceiveRequestAttemptId

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS FIFO queues are designed to enhance messaging between applications when the order of operations and events has to be enforced.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html

MessageDeduplicationId

The message deduplication ID is the token used for the deduplication of sent messages. If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5-minute deduplication interval.

Incorrect options:

MessageGroupId - The message group ID is the tag that specifies that a message belongs to a specific message group. Messages that belong to the same message group are always processed one by one, in a strict order relative to the message group (however, messages that belong to different message groups might be processed out of order).

ReceiveRequestAttemptId - This parameter applies only to FIFO (first-in-first-out) queues. The token is used for deduplication of ReceiveMessage calls. If a networking issue occurs after a ReceiveMessage action, and instead of a response you receive a generic error, you can retry the same action with an identical ReceiveRequestAttemptId to retrieve the same set of messages, even if their visibility timeout has not yet expired.

ContentBasedDeduplication - This is not a message parameter, but a queue setting. Enable content-based deduplication to instruct Amazon SQS to use an SHA-256 hash to generate the message deduplication ID using the body of the message - but not the attributes of the message.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/using-messagededuplicationid-property.html

</details>

---

<a id="t02-q47"></a>
### T02-Q47
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Сумнівний ключ: для async Lambda правильніше on-failure destination / Lambda DLQ.** → [детальніше](../../07-outdated-and-wrong-answers.md#d02)

An application asynchronously invokes an AWS Lambda function. The application has recently been experiencing occasional errors that result in failed invocations. A developer wants to store the messages that resulted in failed invocations such that the application can automatically retry processing them.

What should the developer do to accomplish this goal with the LEAST operational overhead?

- ✅ Configure a redrive policy on an Amazon SQS queue. Set the dead-letter queue as an event source to the Lambda function.
- ▫️ Configure an Amazon S3 bucket as a destination for failed invocations. Configure event notifications to trigger the Lambda function to process the events.
- ▫️ Configure Amazon EventBridge to send the messages to Amazon SNS to initiate the Lambda function again.
- ❌ Configure logging to an Amazon CloudWatch Logs group. Configure Lambda to read failed invocation events from the log group.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon SQS supports dead-letter queues (DLQ), which other queues (source queues) can target for messages that can't be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate unconsumed messages to determine why their processing doesn't succeed.

The redrive policy specifies the source queue, the dead-letter queue, and the conditions under which Amazon SQS moves messages from the former to the latter if the consumer of the source queue fails to process a message a specified number of times.

You can set your DLQ as an event source to the Lambda function to drain your DLQ. This will ensure that all failed invocations are automatically retried.

CORRECT: "Configure a redrive policy on an Amazon SQS queue. Set the dead-letter queue as an event source to the Lambda function" is the correct answer (as explained above.)

INCORRECT: "Configure logging to an Amazon CloudWatch Logs group. Configure Lambda to read failed invocation events from the log group" is incorrect.

The information in the logs may not be sufficient for processing the event. This is not an automated or ideal solution.

INCORRECT: "Configure Amazon EventBridge to send the messages to Amazon SNS to initiate the Lambda function again" is incorrect.

Amazon EventBridge can be configured as a failure destination and can send to SNS. SNS can also be configured with Lambda as a target. However, this solution requires more operational overhead compared to using a DLQ.

INCORRECT: "Configure an Amazon S3 bucket as a destination for failed invocations. Configure event notifications to trigger the Lambda function to process the events" is incorrect.

S3 is not a supported failure destination. Supported destinations are Amazon SNS, Amazon SQS, and Amazon EventBridge.

Джерела: <https://aws.amazon.com/blogs/compute/introducing-aws-lambda-destinations/> · <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html>

</details>

---

<a id="t03-q40"></a>
### T03-Q40
*⏭ пропущено · домен: офіційна мітка*

After reviewing your monthly AWS bill you notice that the cost of using Amazon SQS has gone up substantially after creating new queues; however, you know that your queue clients do not have a lot of traffic and are receiving empty responses.

Which of the following actions should you take?

- ✅ Use LongPolling
- ▫️ Decrease DelaySeconds
- ▫️ Use a FIFO queue
- ▫️ Increase the VisibilityTimeout

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use LongPolling

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

Amazon SQS provides short polling and long polling to receive messages from a queue. By default, queues use short polling. With short polling, Amazon SQS sends the response right away, even if the query found no messages. With long polling, Amazon SQS sends a response after it collects at least one available message, up to the maximum number of messages specified in the request. Amazon SQS sends an empty response only if the polling wait time expires.

Long polling makes it inexpensive to retrieve messages from your Amazon SQS queue as soon as the messages are available. Long polling helps reduce the cost of using Amazon SQS by eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request) and false empty responses (when messages are available but aren't included in a response). When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. The maximum long polling wait time is 20 seconds.

Exam Alert:

Please review the differences between Short Polling vs Long Polling:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

Incorrect options:

Increase the VisibilityTimeout - Because there is no guarantee that a consumer received a message, the consumer must delete it. To prevent other consumers from processing the message again, Amazon SQS sets a visibility timeout. Visibility timeout will not help with cost reduction.

Use a FIFO queue - FIFO queues are designed to enhance messaging between applications when the order of operations and events has to be enforced. FIFO queues will not help with cost reduction. In fact, they are costlier than standard queues.

Decrease DelaySeconds - This is similar to VisibilityTimeout. The difference is that a message is hidden when it is first added to a queue for DelaySeconds, whereas for visibility timeouts a message is hidden only after it is consumed from the queue. DelaySeconds will not help with cost reduction.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html

</details>

---

<a id="t03-q65"></a>
### T03-Q65
*⏭ пропущено · домен: офіційна мітка*

An order management system uses a cron job to poll for any new orders. Every time a new order is created, the cron job sends this order data as a message to the message queues to facilitate downstream order processing in a reliable way. To reduce costs and improve performance, the company wants to move this functionality to AWS cloud.

Which of the following is the most optimal solution to meet this requirement?

- ▫️ Use Amazon Simple Notification Service (SNS) to push notifications and use AWS Lambda functions to process the information received from SNS
- ✅ Use Amazon Simple Notification Service (SNS) to push notifications when an order is created. Configure different Amazon Simple Queue Service (SQS) queues to receive these messages for downstream processing
- ▫️ Use Amazon Simple Notification Service (SNS) to push notifications to Kinesis Data Firehose delivery streams for processing the data for downstream applications
- ▫️ Configure different Amazon Simple Queue Service (SQS) queues to poll for new orders

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Amazon Simple Notification Service (SNS) to push notifications when an order is created. Configure different Amazon Simple Queue Service (SQS) queues to receive these messages for downstream processing

Amazon SNS works closely with Amazon Simple Queue Service (Amazon SQS). These services provide different benefits for developers. Amazon SNS allows applications to send time-critical messages to multiple subscribers through a “push” mechanism, eliminating the need to periodically check or “poll” for updates. Amazon SQS is a message queue service used by distributed applications to exchange messages through a polling model, and can be used to decouple sending and receiving components—without requiring each component to be concurrently available.

Using Amazon SNS and Amazon SQS together, messages can be delivered to applications that require immediate notification of an event, and also stored in an Amazon SQS queue for other applications to process at a later time.

When you subscribe an Amazon SQS queue to an Amazon SNS topic, you can publish a message to the topic and Amazon SNS sends an Amazon SQS message to the subscribed queue. The Amazon SQS message contains the subject and message that were published to the topic along with metadata about the message in a JSON document.

SNS-SQS fanout is the right solution for this use case.

Sample SNS-SQS Fanout message:  via - https://docs.aws.amazon.com/sns/latest/dg/sns-sqs-as-subscriber.html

Incorrect options:

Configure different Amazon Simple Queue Service (SQS) queues to poll for new orders - Amazon SQS cannot be used as a polling service, as messages need to be pushed to the queue, which are then handled by the queue consumers.

Use Amazon Simple Notification Service (SNS) to push notifications and use AWS Lambda functions to process the information received from SNS - Amazon SNS and AWS Lambda are integrated so you can invoke Lambda functions with Amazon SNS notifications. When a message is published to an SNS topic that has a Lambda function subscribed to it, the Lambda function is invoked with the payload of the published message. For the given scenario, we need a service that can store the message data pushed by SNS, for further processing. AWS Lambda does not have capacity to store the message data. In case a Lambda function is unable to process a specific message, it will be left unprocessed. Hence this option is not correct.

Use Amazon Simple Notification Service (SNS) to push notifications to Kinesis Data Firehose delivery streams for processing the data for downstream applications - You can subscribe Amazon Kinesis Data Firehose delivery streams to SNS topics, which allows you to send notifications to additional storage and analytics endpoints. However, Kinesis is built for real-time processing of big data. Whereas, SQS is meant for decoupling dependent systems with easy methods to transmit data/messages. SQS also is a cheaper option when compared to Firehose. Therefore this option is not the right fit for the given use case.

Джерела: <https://docs.aws.amazon.com/sns/latest/dg/sns-sqs-as-subscriber.html> · <https://docs.aws.amazon.com/sns/latest/dg/sns-lambda-as-subscriber.html> · <https://docs.aws.amazon.com/sns/latest/dg/sns-firehose-as-subscriber.html>

</details>

---

<a id="t06-q28"></a>
### T06-Q28
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A company is deploying a serverless application on AWS, using an AWS Lambda function for handling customer orders. This function utilizes an external HTTP API for processing payments, which occasionally times out. The requirement is to notify the support team via an existing Amazon SNS topic when the error rate of this API surpasses 10% per hour.

How can this be achieved?

- ❌ Use AWS X-Ray to monitor the error rate and configure it to send alerts through the SNS topic.
- ▫️ Enable AWS CloudTrail to monitor the Lambda function and send alerts to the SNS topic when the error rate exceeds 10%.
- ▫️ Configure the AWS Lambda function to directly send alerts to the SNS topic when there is a timeout.
- ✅ Implement Amazon CloudWatch Metrics with a custom threshold and link it to the existing SNS topic.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon CloudWatch Metrics can monitor and track API error rates and can be configured to trigger an alert when a particular threshold is surpassed. By linking this to the existing Amazon SNS topic, the support team can be notified in near real-time when the payment processing external API error rate exceeds 10% of the total number of transactions in an hour.

CORRECT: "Implement Amazon CloudWatch Metrics with a custom threshold and link it to the existing SNS topic" is the correct answer (as explained above.)

INCORRECT: "Use AWS X-Ray to monitor the error rate and configure it to send alerts through the SNS topic" is incorrect.

AWS X-Ray is more suited for tracing applications to debug and analyze individual user paths, rather than for real-time alerting on API error rates.

INCORRECT: "Configure the AWS Lambda function to directly send alerts to the SNS topic when there is a timeout" is incorrect.

Modifying the Lambda function to directly send alerts may increase the complexity of the function and may not be as reliable or timely as using a dedicated monitoring service like CloudWatch.

INCORRECT: "Enable AWS CloudTrail to monitor the Lambda function and send alerts to the SNS topic when the error rate exceeds 10%" is incorrect.

AWS CloudTrail is a service that provides event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command-line tools, and other AWS services. It is not designed to monitor error rates of APIs or Lambda functions and trigger alerts based on those.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html>

</details>

---

<a id="t07-q27"></a>
### T07-Q27
*⏭ пропущено · домен: офіційна мітка*

You company runs business logic on smaller software components that perform various functions. Some functions process information in a few seconds while others seem to take a long time to complete. Your manager asked you to decouple components that take a long time to ensure software applications stay responsive under load. You decide to configure Amazon Simple Queue Service (SQS) to work with your Elastic Beanstalk configuration.

Which of the following Elastic Beanstalk environment should you choose to meet this requirement?

- ▫️ Load-balancing, Autoscaling environment
- ✅ Dedicated worker environment
- ▫️ Single Instance with Elastic IP
- ▫️ Single Instance Worker node

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.

Elastic BeanStalk Key Concepts:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html

Dedicated worker environment - If your AWS Elastic Beanstalk application performs operations or workflows that take a long time to complete, you can offload those tasks to a dedicated worker environment. Decoupling your web application front end from a process that performs blocking operations is a common way to ensure that your application stays responsive under load.

A long-running task is anything that substantially increases the time it takes to complete a request, such as processing images or videos, sending emails, or generating a ZIP archive. These operations can take only a second or two to complete, but a delay of a few seconds is a lot for a web request that would otherwise complete in less than 500 ms.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-tiers.html

Incorrect options:

Single Instance Worker node - Worker machines in Kubernetes are called nodes. Amazon EKS worker nodes are standard Amazon EC2 instances. EKS worker nodes are not to be confused with the Elastic Beanstalk worker environment. Since we are talking about the Elastic Beanstalk environment, this is not the correct choice.

Load-balancing, Autoscaling environment - A load-balancing and autoscaling environment uses the Elastic Load Balancing and Amazon EC2 Auto Scaling services to provision the Amazon EC2 instances that are required for your deployed application. Amazon EC2 Auto Scaling automatically starts additional instances to accommodate increasing load on your application. If your application requires scalability with the option of running in multiple Availability Zones, use a load-balancing, autoscaling environment. This is not the right environment for the given use-case since it will add costs to the overall solution.

Single Instance with Elastic IP - A single-instance environment contains one Amazon EC2 instance with an Elastic IP address. A single-instance environment doesn't have a load balancer, which can help you reduce costs compared to a load-balancing, autoscaling environment. This is not a highly available architecture, because if that one instance goes down then your application is down. This is not recommended for production environments.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-types.html

</details>

---

<a id="t08-q09"></a>
### T08-Q09
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company is running an order processing system on AWS. Amazon SQS is used to queue orders and an AWS Lambda function processes them. The company recently started noticing a lot of orders are failing to process.

How can a Developer MOST effectively manage these failures to debug the failed orders later and reprocess them, as necessary?

- ✅ Implement dead-letter queues for failed orders from the order queue
- ▫️ Publish failed orders from the order queue to an Amazon SNS topic
- ▫️ Log the failed orders from the order queue using Amazon CloudWatch Logs
- ▫️ Send failed orders from the order queue to AWS CloudTrail logs

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon SQS supports dead-letter queues, which other queues (source queues) can target for messages that can't be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed.

The Developer should therefore implement dead-letter queues for failed orders from the order queue. This will allow full debugging as the entire message is available for analysis.

CORRECT: "Implement dead-letter queues for failed orders from the order queue" is the correct answer.

INCORRECT: "Publish failed orders from the order queue to an Amazon SNS topic" is incorrect as there is no way to isolate messages that have failed to process when subscribing an SQS queue to an SNS topic.

INCORRECT: "Log the failed orders from the order queue using Amazon CloudWatch Logs" is incorrect as SQS does not publish message success/failure to CloudWatch Logs.

INCORRECT: "Send failed orders from the order queue to AWS CloudTrail logs" is incorrect as CloudTrail records API activity not performance metrics or logs.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html>

</details>

---

<a id="t09-q03"></a>
### T09-Q03
*⏭ пропущено · домен: офіційна мітка*

A company has a cloud system in AWS with components that send and receive messages using SQS queues. While reviewing the system you see that it processes a lot of information and would like to be aware of any limits of the system.

Which of the following represents the maximum number of messages that can be stored in an SQS queue?

- ▫️ 10000
- ✅ no limit
- ▫️ 100000
- ▫️ 10000000

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"no limit": There are no message limits for storing in SQS, but 'in-flight messages' do have limits. Make sure to delete messages after you have processed them. There can be a maximum of approximately 120,000 inflight messages (received from a queue by a consumer, but not yet deleted from the queue).

Incorrect options:

"10000"

"100000"

"10000000"

These three options contradict the details provided in the explanation above, so these are incorrect.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-limits.html

</details>

---

<a id="t09-q48"></a>
### T09-Q48
*⏭ пропущено · домен: офіційна мітка*

A company uses Amazon Simple Email Service (SES) to cost-effectively send susbscription emails to the customers. Intermittently, the SES service throws the error: Throttling – Maximum sending rate exceeded.

As a developer associate, which of the following would you recommend to fix this issue?

- ✅ Use Exponential Backoff technique to introduce delay in time before attempting to execute the operation again
- ▫️ Raise a service request with Amazon to increase the throttling limit for the SES API
- ▫️ Configure Timeout mechanism for each request made to the SES service
- ▫️ Implement retry mechanism for all 4xx errors to avoid throttling error

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Exponential Backoff technique to introduce delay in time before attempting to execute the operation again - A “Throttling – Maximum sending rate exceeded” error is retriable. This error is different than other errors returned by Amazon SES. A request rejected with a “Throttling” error can be retried at a later time and is likely to succeed.

Retries are “selfish.” In other words, when a client retries, it spends more of the server's time to get a higher chance of success. Where failures are rare or transient, that's not a problem. This is because the overall number of retried requests is small, and the tradeoff of increasing apparent availability works well. When failures are caused by overload, retries that increase load can make matters significantly worse. They can even delay recovery by keeping the load high long after the original issue is resolved.

The preferred solution is to use a backoff. Instead of retrying immediately and aggressively, the client waits some amount of time between tries. The most common pattern is an exponential backoff, where the wait time is increased exponentially after every attempt.

A variety of factors can affect your send rate, e.g. message size, network performance or Amazon SES availability. The advantage of the exponential backoff approach is that your application will self-tune and it will call Amazon SES at close to the maximum allowed rate.

Incorrect options:

Configure Timeout mechanism for each request made to the SES service - Requests are configured to timeout if they do not complete successfully in a given time. This helps free up the database, application and any other resource that could potentially keep on waiting to eventually succeed. But, if errors are caused by load, retries can be ineffective if all clients retry at the same time. Throttling error signifies that load is high on SES and it does not make sense to keep retrying.

Raise a service request with Amazon to increase the throttling limit for the SES API - If throttling error is persistent, then it indicates a high load on the system consistently and increasing the throttling limit will be the right solution for the problem. But, the error is only intermittent here, signifying that decreasing the rate of requests will handle the error.

Implement retry mechanism for all 4xx errors to avoid throttling error - 4xx status codes indicate that there was a problem with the client request. Common client request errors include providing invalid credentials and omitting required parameters. When you get a 4xx error, you need to correct the problem and resubmit a properly formed client request. Throttling is a server error and not a client error, hence retry on 4xx errors does not make sense here.

Джерела: <https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/> · <https://aws.amazon.com/blogs/messaging-and-targeting/how-to-handle-a-throttling-maximum-sending-rate-exceeded-error/>

</details>

---

<a id="t09-q64"></a>
### T09-Q64
*⏭ пропущено · домен: офіційна мітка*

An application running on EC2 instances processes messages from an SQS queue. However, sometimes the messages are not processed and they end up in errors. These messages need to be isolated for further processing and troubleshooting.

Which of the following options will help achieve this?

- ▫️ Increase the VisibilityTimeout
- ▫️ Reduce the VisibilityTimeout
- ▫️ Use DeleteMessage
- ✅ Implement a Dead-Letter Queue

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Implement a Dead-Letter Queue - Amazon SQS supports dead-letter queues, which other queues (source queues) can target for messages that can't be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed. Amazon SQS does not create the dead-letter queue automatically. You must first create the queue before using it as a dead-letter queue.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

Incorrect options:

Increase the VisibilityTimeout - When a consumer receives and processes a message from a queue, the message remains in the queue. Amazon SQS doesn't automatically delete the message. Immediately after a message is received, it remains in the queue. To prevent other consumers from processing the message again, Amazon SQS sets a visibility timeout, a period of time during which Amazon SQS prevents other consumers from receiving and processing the message. Increasing visibility timeout will not help in troubleshooting the messages running into error or isolating them from the rest. Hence this is an incorrect option for the current use case.

Use DeleteMessage - Deletes the specified message from the specified queue. This will not help understand the reason for error or isolate messages ending with the error.

Reduce the VisibilityTimeout - As explained above, VisibilityTimeout makes sure that the message is not read by any other consumer while it is being processed by one consumer. By reducing the VisibilityTimeout, more consumers will receive the same failed message. Hence, this is an incorrect option for this use case.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html>

</details>

---

<a id="t12-q36"></a>
### T12-Q36
*⏭ пропущено · домен: офіційна мітка*

An e-commerce company uses Amazon SQS queues to decouple their application architecture. The development team has observed message processing failures for an edge case scenario when a user places an order for a particular product ID, but the product ID is deleted, thereby causing the application code to fail.

As a Developer Associate, which of the following solutions would you recommend to address such message failures?

- ▫️ Use a temporary queue to handle message processing failures
- ▫️ Use long polling to handle message processing failures
- ▫️ Use short polling to handle message processing failures
- ✅ Use a dead-letter queue to handle message processing failures

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use a dead-letter queue to handle message processing failures

Dead-letter queues can be used by other queues (source queues) as a target for messages that can't be processed (consumed) successfully. Dead-letter queues are useful for debugging your application or messaging system because they let you isolate problematic messages to determine why their processing doesn't succeed.

Sometimes, messages can’t be processed because of a variety of possible issues, such as when a user comments on a story but it remains unprocessed because the original story itself is deleted by the author while the comments were being posted. In such a case, the dead-letter queue can be used to handle message processing failures.

How do dead-letter queues work?  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

Use-cases for dead-letter queues:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

Incorrect options:

Use a temporary queue to handle message processing failures - The most common use case for temporary queues is the request-response messaging pattern (for example, processing a login request), where a requester creates a temporary queue for receiving each response message. To avoid creating an Amazon SQS queue for each response message, the Temporary Queue Client lets you create and delete multiple temporary queues without making any Amazon SQS API calls. Temporary queues cannot be used to handle message processing failures.

Use short polling to handle message processing failures

Use long polling to handle message processing failures

Amazon SQS provides short polling and long polling to receive messages from a queue. By default, queues use short polling. With short polling, Amazon SQS sends the response right away, even if the query found no messages. With long polling, Amazon SQS sends a response after it collects at least one available message, up to the maximum number of messages specified in the request. Amazon SQS sends an empty response only if the polling wait time expires. Neither short polling nor long polling can be used to handle message processing failures.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html

</details>

---

<a id="t12-q39"></a>
### T12-Q39
*⏭ пропущено · домен: офіційна мітка*

A video encoding application running on an EC2 instance takes about 20 seconds on average to process each raw footage file. The application picks the new job messages from an SQS queue. The development team needs to account for the use-case when the video encoding process takes longer than usual so that the same raw footage is not processed by multiple consumers.

As a Developer Associate, which of the following solutions would you recommend to address this use-case?

- ▫️ Use WaitTimeSeconds action to short poll and extend a message's visibility timeout
- ▫️ Use WaitTimeSeconds action to long poll and extend a message's visibility timeout
- ✅ Use ChangeMessageVisibility action to extend a message's visibility timeout
- ▫️ Use DelaySeconds action to delay a message's visibility timeout

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use ChangeMessageVisibility action to extend a message's visibility timeout

Amazon SQS uses a visibility timeout to prevent other consumers from receiving and processing the same message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html

For example, you have a message with a visibility timeout of 5 minutes. After 3 minutes, you call ChangeMessageVisibility with a timeout of 10 minutes. You can continue to call ChangeMessageVisibility to extend the visibility timeout to the maximum allowed time. If you try to extend the visibility timeout beyond the maximum, your request is rejected. So, for the given use-case, the application can set the initial visibility timeout to 1 minute and then continue to update the ChangeMessageVisibility value if required.

 via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html

Incorrect options:

Use DelaySeconds action to delay a message's visibility timeout - Delay queues let you postpone the delivery of new messages to a queue for a number of seconds. To set delay seconds on individual messages, rather than on an entire queue, use message timers to allow Amazon SQS to use the message timer's DelaySeconds value instead of the delay queue's DelaySeconds value. You cannot use DelaySeconds to alter the visibility of a message which has been picked for processing.

Use WaitTimeSeconds action to short poll and extend a message's visibility timeout

Use WaitTimeSeconds action to long poll and extend a message's visibility timeout

Amazon SQS provides short polling and long polling to receive messages from a queue. Both these options have been added as distractors as WaitTimeSeconds (via short polling or long polling) cannot be used to influence the message's visibility.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html> · <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibility.html>

</details>

---
