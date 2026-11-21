# AWS Lambda

**Домен 4 — Усунення несправностей та оптимізація** · питань: 15  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q48"></a>
### T01-Q48
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EventBridge rule.** → [детальніше](../../06-contradictions-and-traps.md#c14)

An IT company leverages CodePipeline to automate its release pipelines. The development team wants to write a Lambda function that will send notifications for state changes within the pipeline.

As a Developer Associate, which steps would you suggest to associate the Lambda function with the event source?

- ▫️ Use the CodePipeline console to set up a trigger for the Lambda function
- ▫️ Use the Lambda console to configure a trigger that invokes the Lambda function with CodePipeline as the event source
- ✅ Set up an Amazon EventBridge rule that uses CodePipeline as an event source with the target as the Lambda function
- ▫️ Set up an Amazon CloudWatch alarm that monitors status changes in Code Pipeline and triggers the Lambda function

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon EventBridge delivers a near real-time stream of system events that describe changes in Amazon Web Services (AWS) resources. Using simple rules that you can quickly set up, you can match events and route them to one or more target functions or streams.

Amazon EventBridge Key Concepts:  via - https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html

Set up an Amazon EventBridge rule that uses CodePipeline as an event source with the target as the Lambda function

You can use Amazon EventBridge to detect and react to changes in the state of a pipeline, stage, or action. Then, based on rules you create, EventBridge invokes one or more target actions when a pipeline, stage, or action enters the state you specify in a rule. For the given use-case, you can set up a rule that detects pipeline changes and invokes an AWS Lambda function.

Amazon EventBridge With CodePipeline:  https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html

Incorrect options:

Set up an Amazon CloudWatch alarm that monitors status changes in Code Pipeline and triggers the Lambda function - As mentioned in the explanation above, you need to use an EventBridge event and not CloudWatch alarm for this use-case.

Use the Lambda console to configure a trigger that invokes the Lambda function with CodePipeline as the event source - You cannot create a trigger with CodePipeline as the event source via the Lambda Console.

Use the CodePipeline console to set up a trigger for the Lambda function - CodePipeline console cannot be used to configure a trigger for a Lambda function.

Джерела: <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html> · <https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html>

</details>

---

<a id="t02-q06"></a>
### T02-Q06
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Developer has recently created an application that uses an AWS Lambda function, an Amazon DynamoDB table, and also sends notifications using Amazon SNS. The application is not working as expected and the Developer needs to analyze what is happening across all components of the application.

What is the BEST way to analyze the issue?

- ▫️ Create an Amazon CloudWatch Events rule
- ▫️ Monitor the application with AWS Trusted Advisor
- ✅ Enable X-Ray tracing for the Lambda function
- ▫️ Assess the application with Amazon Inspector

<details><summary>Пояснення (оригінал, EN)</summary>

AWS X-Ray makes it easy for developers to analyze the behavior of their production, distributed applications with end-to-end tracing capabilities. You can use X-Ray to identify performance bottlenecks, edge case errors, and other hard to detect issues.

AWS X-Ray provides an end-to-end, cross-service view of requests made to your application. It gives you an application-centric view of requests flowing through your application by aggregating the data gathered from individual services in your application into a single unit called a trace. You can use this trace to follow the path of an individual request as it passes through each service or tier in your application so that you can pinpoint where issues are occurring.

AWS X-Ray will assist the developer with visually analyzing the end-to-end view of connectivity between the application components and how they are performing using a Service Map. X-Ray also provides aggregated data about the application.

CORRECT: "Enable X-Ray tracing for the Lambda function" is the correct answer.

INCORRECT: "Create an Amazon CloudWatch Events rule" is incorrect as this feature of CloudWatch is used to trigger actions based on changes in the state of AWS services.

INCORRECT: "Assess the application with Amazon Inspector" is incorrect. Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS.

INCORRECT: "Monitor the application with AWS Trusted Advisor" is incorrect. AWS Trusted Advisor is an online tool that provides you real time guidance to help you provision your resources following AWS best practices.

Джерела: <https://aws.amazon.com/xray/features/>

</details>

---

<a id="t02-q08"></a>
### T02-Q08
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

A critical application is hosted on AWS exposed by an HTTP API through Amazon API Gateway. The API is integrated with an AWS Lambda function and the application data is housed in an Amazon RDS for PostgreSQL DB instance, featuring 2 vCPUs and 16 GB of RAM.

The company has been receiving customer complaints about occasional HTTP 500 Internal Server Error responses from some API calls during unpredictable peak usage times. Amazon CloudWatch Logs has recorded "connection limit exceeded" errors. The company wants to ensure resilience in the application, with no unscheduled downtime for the database.

Which solution would best fit these requirements?

- ▫️ Double the RAM and vCPUs of the RDS instance.
- ❌ Implement auto-scaling for the RDS instance based on connection count.
- ▫️ Use AWS Lambda to create a connection pool for the RDS instance.
- ✅ Use Amazon RDS Proxy and update the Lambda function to connect to the proxy.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon RDS Proxy is designed to improve application scalability and resilience by pooling and sharing database connections, reducing the CPU and memory overhead on the database. It handles the burst in connections seamlessly and improves the application's ability to scale.

CORRECT: "Use Amazon RDS Proxy and update the Lambda function to connect to the proxy" is the correct answer (as explained above.)

INCORRECT: "Double the RAM and vCPUs of the RDS instance" is incorrect.

Merely augmenting the number of vCPUs and RAM for the RDS instance might not resolve the issue as it doesn't directly tackle the problem of too many connections.

INCORRECT: "Implement auto-scaling for the RDS instance based on connection count" is incorrect.

Auto-scaling in response to connection count is not a feature provided by RDS.

INCORRECT: "Use AWS Lambda to create a connection pool for the RDS instance" is incorrect.

The best solution is to use RDS proxy which is designed for creating a pool of connections. Lambda may not be the best solution for this problem as it could be costly and has a limitation in execution time.

Джерела: <https://aws.amazon.com/blogs/compute/using-amazon-rds-proxy-with-aws-lambda/>

</details>

---

<a id="t02-q48"></a>
### T02-Q48
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A company has an application that logs all information to Amazon S3. Whenever there is a new log file, an AWS Lambda function is invoked to process the log files. The code works, gathering all of the necessary information. However, when checking the Lambda function logs, duplicate entries with the same request ID are found.

What is the BEST explanation for the duplicate entries?

- ▫️ There was an S3 outage, which caused duplicate entries of the same log file
- ▫️ The application stopped intermittently and then resumed
- ✅ The Lambda function failed, and the Lambda service retried the invocation with a delay
- ▫️ The S3 bucket name was specified incorrectly

<details><summary>Пояснення (оригінал, EN)</summary>

From the AWS documentation:

“When an error occurs, your function may be invoked multiple times. Retry behavior varies by error type, client, event source, and invocation type. For example, if you invoke a function asynchronously and it returns an error, Lambda executes the function up to two more times. For more information, see Retry Behavior.

For asynchronous invocation, Lambda adds events to a queue before sending them to your function. If your function does not have enough capacity to keep up with the queue, events may be lost. Occasionally, your function may receive the same event multiple times, even if no error occurs. To retain events that were not processed, configure your function with a dead-letter queue.”

Therefore, the most likely explanation is that the function failed, and Lambda retried the invocation.

CORRECT: "The Lambda function failed, and the Lambda service retried the invocation with a delay" is the correct answer.

INCORRECT: "The S3 bucket name was specified incorrectly" is incorrect. If this was the case all attempts would fail but this is not the case.

INCORRECT: "There was an S3 outage, which caused duplicate entries of the same log file" is incorrect. There cannot be duplicate log files in Amazon S3 as every object must be unique within a bucket. Therefore, if the same log file was uploaded twice it would just overwrite the previous version of the file. Also, if a separate request was made to Lambda it would have a different request ID.

INCORRECT: "The application stopped intermittently and then resumed" is incorrect. The issue is duplicate entries of the same request ID.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html>

</details>

---

<a id="t03-q01"></a>
### T03-Q01
*⏭ пропущено · домен: офіційна мітка*

```json
A developer has pushed a Lambda function that pushes data into an RDS MySQL database with the following Python code:
def handler(event, context):
    mysql = mysqlclient.connect()
    data = event['data']
    mysql.execute(f"INSERT INTO foo (bar) VALUES (${data});")
    mysql.close()
    return
On the first execution, the Lambda function takes 2 seconds to execute. On the second execution and all the subsequent ones, the Lambda function takes 1.9 seconds to execute.
What can be done to improve the execution time of the Lambda function?
```

- ✅ Move the database connection out of the handler
- ▫️ Change the runtime to Node.js
- ▫️ Upgrade the MySQL instance type
- ▫️ Increase the Lambda function RAM

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Move the database connection out of the handler

Here at every Lambda function execution, the database connection handler will be created, and then closed. These connections steps are expensive in terms of time, and thus should be moved out of the handler function so that they are kept in the function execution context, and re-used across function calls. This is what the function should look like in the end:

mysql = mysqlclient.connect()

def handler(event, context):
    data = event['data']
    mysql.execute(f"INSERT INTO foo (bar) VALUES (${data});")
    return
Incorrect options:

Upgrade the MySQL instance type - The bottleneck here is the MySQL connection object, not the MySQL instance itself.

Change the runtime to Node.js - Re-writing the function in another runtime won't improve the performance.

Increase the Lambda function RAM - While this may help speed-up the Lambda function, as increasing the RAM also increases the CPU allocated to your function, it only makes sense if RAM or CPU is a critical factor in the Lambda function performance. Here, the connection object is at fault.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/running-lambda-code.html

</details>

---

<a id="t03-q11"></a>
### T03-Q11
*⏭ пропущено · домен: офіційна мітка*

A voting system hosted on-premise was recently migrated to AWS to lower cost, gain scalability, and to better serve thousands of concurrent users. When one of the AWS resource state changes, it generates an event and will need to trigger AWS Lambda. The AWS resource whose state changes and AWS Lambda does not have direct integration.

Which of the following methods can be used to trigger AWS Lambda?

- ▫️ AWS Lambda Custom Sources
- ▫️ Cron jobs to trigger AWS Lambda to check the state of your service
- ✅ Amazon EventBridge rules with AWS Lambda
- ▫️ Open a support ticket with AWS

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon EventBridge rules with AWS Lambda

You can create a Lambda function and direct EventBridge to execute it on a regular schedule. You can specify a fixed rate (for example, execute a Lambda function every hour or 15 minutes), or you can specify a Cron expression.

EventBridge Key Concepts:  via - https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html

Schedule Expressions for EventBridge Rules:  via - https://docs.aws.amazon.com/eventbridge/latest/userguide/using-eventbridge-scheduler.html

Incorrect options:

AWS Lambda Custom Sources - This is a made-up option and has been added as a distractor.

Open a support ticket with AWS - You can, although the AWS support team will not add a custom configuration for you, they will step you through creating event rule with Lambda.

Cron jobs to trigger AWS Lambda to check the state of your service - You would need an additional server for your cron job instead you should consider using a cron expression with CloudWatch.

Джерела: <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html> · <https://docs.aws.amazon.com/eventbridge/latest/userguide/using-eventbridge-scheduler.html>

</details>

---

<a id="t04-q34"></a>
### T04-Q34
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating an AWS Lambda function to process a stream of data from an Amazon Kinesis Data Stream. When the Lambda function parses the data and encounters a missing field, it exits the function with an error. The function is generating duplicate records from the Kinesis stream. When the Developer looks at the stream output without the Lambda function, there are no duplicate records.

What is the reason for the duplicates?

- ▫️ The Lambda function did not advance the Kinesis stream point to the next record after the error
- ▫️ The Lambda function is not keeping up with the amount of data coming from the stream
- ▫️ The Lambda event source used asynchronous invocation, resulting in duplicate records
- ✅ The Lambda function did not handle the error, and the Lambda service attempted to reprocess the data

<details><summary>Пояснення (оригінал, EN)</summary>

When you invoke a function, two types of error can occur. Invocation errors occur when the invocation request is rejected before your function receives it. Function errors occur when your function's code or runtime returns an error.

Depending on the type of error, the type of invocation, and the client or service that invokes the function, the retry behavior and the strategy for managing errors varies. Function errors occur when your function code or the runtime that it uses return an error.

In this case, with an event source mapping from a stream (Kinesis Data Stream), Lambda retries the entire batch of items. Therefore, the best explanation is that the Lambda function did not handle the error, and the Lambda service attempted to reprocess the data.

CORRECT: "The Lambda function did not handle the error, and the Lambda service attempted to reprocess the data" is the correct answer.

INCORRECT: "The Lambda function did not advance the Kinesis stream point to the next record after the error" is incorrect. Lambda does not advance a stream “point” to the next record. It processed records in batches.

INCORRECT: "The Lambda event source used asynchronous invocation, resulting in duplicate records" is incorrect as Lambda processes records from Kinesis Data Streams synchronously.

INCORRECT: "The Lambda function is not keeping up with the amount of data coming from the stream" is incorrect as Lambda can scale seamlessly to handle the load coming from the stream.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-retries.html>

</details>

---

<a id="t06-q04"></a>
### T06-Q04
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer is creating a serverless application. The application looks up information about a customer using a separate Lambda function for each item such as address and phone number. The Developer has created branches in AWS Step Functions for each lookup function.

How can the Developer optimize the performance, so the lookups complete faster?

- ▫️ Use a Map state to iterate over all the items.
- ▫️ Use a Choice state to lookup the specific information required.
- ✅ Use a Parallel state to iterate over all the branches parallel.
- ▫️ Use a Wait state to reduce the wait time for function execution.

<details><summary>Пояснення (оригінал, EN)</summary>

The Parallel state ("Type": "Parallel") can be used to create parallel branches of execution in your AWS Step Functions state machine. This will improve the performance of the application by ensuring that all information lookups occur in parallel.

CORRECT: "Use a Parallel state to iterate over all the branches parallel" is the correct answer.

INCORRECT: "Use a Choice state to lookup the specific information required" is incorrect. This is used to add additional logic but is not required and is unlikely to improve performance.

INCORRECT: "Use a Wait state to reduce the wait time for function execution" is incorrect. The Wait state delays the state machine from continuing for a specified time.

INCORRECT: "Use a Map state to iterate over all the items" is incorrect. The Map state executes the same steps for multiple entries of an array in the state input.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-parallel-state.html>

</details>

---

<a id="t06-q54"></a>
### T06-Q54
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Storage)*

An AWS Lambda function is being developed in a VPC. When a file is added to an Amazon S3 bucket, this Lambda function is triggered, processes the file, and logs the results into a file. These result and log files need to be accessible by other AWS services and on-premises resources.

What should the developer use to meet these requirements?

- ✅ Keep the result and log files in Amazon Elastic File System (EFS) accessible by Lambda functions.
- ❌ Store the result and log files in Amazon S3 and append the new log entries to existing objects.
- ▫️ Use AWS Glue to consolidate and catalog all result and log files and append log entries.
- ▫️ Use Amazon DynamoDB to store the files and enable DynamoDB Streams to send notifications of changes.

<details><summary>Пояснення (оригінал, EN)</summary>

The correct solution is Amazon EFS as it provides a scalable, fully managed elastic NFS file system for AWS Cloud and on-premises resources. EFS can scale to petabytes without disrupting applications, making it suitable for Lambda functions to read/write large amounts of data.

Furthermore, AWS Lambda can now mount an Amazon EFS file system, making it easy for Lambda functions to read and write large amounts of data or to share data across a fleet of functions.

CORRECT: "Keep the result and log files in Amazon Elastic File System (EFS) accessible by Lambda functions" is the correct answer (as explained above.)

INCORRECT: "Store the result and log files in Amazon S3 and append the new log entries to existing objects" is incorrect.

Amazon S3 does not support appending data to existing S3 objects.

INCORRECT: "Use Amazon DynamoDB to store the files and enable DynamoDB Streams to send notifications of changes" is incorrect.

While Amazon DynamoDB can store data and Streams can notify services of changes, it is not designed for the type of file storage and sharing described in this scenario.

INCORRECT: "Use AWS Glue to consolidate and catalog all result and log files and append log entries" is incorrect.

AWS Glue is a fully managed extract, transform, and load (ETL) service that makes it easy for users to prepare and load their data for analytics, but it is not used for direct file storage and sharing.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/services-efs.html>

</details>

---

<a id="t07-q45"></a>
### T07-Q45
*⏭ пропущено · домен: офіційна мітка*

An IT company has migrated to a serverless application stack on the AWS Cloud with the compute layer being implemented via Lambda functions. The engineering managers would like to actively troubleshoot any failures in the Lambda functions.

As a Developer Associate, which of the following solutions would you suggest for this use-case?

- ▫️ Use Amazon EventBridge to identify and notify any failures in the Lambda code
- ▫️ Use CodeDeploy to identify and notify any failures in the Lambda code
- ✅ The developers should insert logging statements in the Lambda function code which are then available via CloudWatch logs
- ▫️ Use CodeCommit to identify and notify any failures in the Lambda code

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"The developers should insert logging statements in the Lambda function code which are then available via CloudWatch logs"

When you invoke a Lambda function, two types of error can occur. Invocation errors occur when the invocation request is rejected before your function receives it. Function errors occur when your function's code or runtime returns an error. Depending on the type of error, the type of invocation, and the client or service that invokes the function, the retry behavior, and the strategy for managing errors varies.

Lambda function failures are commonly caused by:

Permissions issues Code issues Network issues Throttling Invoke API 500 and 502 errors

You can insert logging statements into your code to help you validate that your code is working as expected. Lambda automatically integrates with CloudWatch Logs and pushes all logs from your code to a CloudWatch Logs group associated with a Lambda function, which is named /aws/lambda/&lt;function name&gt;.

Please see this note for more details:  via - https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html

Incorrect options:

"Use Amazon EventBridge to identify and notify any failures in the Lambda code" - Typically Lambda functions are triggered as a response to an EventBridge event. Amazon EventBridge cannot identify and notify failures in the Lambda code.

"Use CodeCommit to identify and notify any failures in the Lambda code"

"Use CodeDeploy to identify and notify any failures in the Lambda code"

AWS CodeCommit is a fully-managed source control service that hosts secure Git-based repositories. It makes it easy for teams to collaborate on code in a secure and highly scalable ecosystem.

AWS CodeDeploy is a fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers.

Neither CodeCommit nor CodeDeploy can identify and notify failures in the Lambda code.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html

</details>

---

<a id="t08-q56"></a>
### T08-Q56
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A company is running an application built on AWS Lambda functions. One Lambda function has performance issues when it has to download a 50 MB file from the internet every execution. This function is called multiple times a second.

What solution would give the BEST performance increase?

- ▫️ Cache the file in Amazon S3
- ✅ Cache the file in the /tmp directory
- ▫️ Put an Elastic Load Balancer in front of the Lambda function
- ▫️ Increase the Lambda maximum execution time

<details><summary>Пояснення (оригінал, EN)</summary>

The /tmp directory provides 512 MB of storage space that can be used by a function. When a file is cached by a function in the /tmp directory it is available to be used by subsequent executions of the function which will reduce latency.

CORRECT: "Cache the file in the /tmp directory" is the correct answer.

INCORRECT: "Increase the Lambda maximum execution time" is incorrect as the function is not timing out.

INCORRECT: "Put an Elastic Load Balancer in front of the Lambda function" is incorrect as this would not reduce latency or improve performance.

INCORRECT: "Cache the file in Amazon S3" is incorrect as this would not provide better performance as it would still need to be retrieved from S3 for each execution if it is not cached in the /tmp directory.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html>

</details>

---

<a id="t09-q18"></a>
### T09-Q18
*⏭ пропущено · домен: офіційна мітка*

The development team at a retail company is gearing up for the upcoming Thanksgiving sale and wants to make sure that the application's serverless backend running via Lambda functions does not hit latency bottlenecks as a result of the traffic spike.

As a Developer Associate, which of the following solutions would you recommend to address this use-case?

- ▫️ Configure Application Auto Scaling to manage Lambda reserved concurrency on a schedule
- ✅ Configure Application Auto Scaling to manage Lambda provisioned concurrency on a schedule
- ▫️ No need to make any special provisions as Lambda is automatically scalable because of its serverless nature
- ▫️ Add an Application Load Balancer in front of the Lambda functions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure Application Auto Scaling to manage Lambda provisioned concurrency on a schedule

Concurrency is the number of requests that a Lambda function is serving at any given time. If a Lambda function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency.

Due to a spike in traffic, when Lambda functions scale, this causes the portion of requests that are served by new instances to have higher latency than the rest. To enable your function to scale without fluctuations in latency, use provisioned concurrency. By allocating provisioned concurrency before an increase in invocations, you can ensure that all requests are served by initialized instances with very low latency.

You can configure Application Auto Scaling to manage provisioned concurrency on a schedule or based on utilization. Use scheduled scaling to increase provisioned concurrency in anticipation of peak traffic. To increase provisioned concurrency automatically as needed, use the Application Auto Scaling API to register a target and create a scaling policy.

Please see this note for more details on provisioned concurrency:  via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html

Incorrect options:

Configure Application Auto Scaling to manage Lambda reserved concurrency on a schedule - To ensure that a function can always reach a certain level of concurrency, you can configure the function with reserved concurrency. When a function has reserved concurrency, no other function can use that concurrency. More importantly, reserved concurrency also limits the maximum concurrency for the function, and applies to the function as a whole, including versions and aliases.

You cannot configure Application Auto Scaling to manage Lambda reserved concurrency on a schedule.

Add an Application Load Balancer in front of the Lambda functions - This is a distractor as just adding the Application Load Balancer will not help in scaling the Lambda functions to address the surge in traffic.

No need to make any special provisions as Lambda is automatically scalable because of its serverless nature - It's true that Lambda is serverless, however, due to the surge in traffic the Lambda functions can still hit the concurrency limits. So this option is incorrect.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html

</details>

---

<a id="t09-q49"></a>
### T09-Q49
*⏭ пропущено · домен: офіційна мітка*

As an AWS Certified Developer Associate, you have been hired to work with the development team at a company to create a REST API using the serverless architecture.

Which of the following solutions will you choose to move the company to the serverless architecture paradigm?

- ▫️ Route 53 with EC2 as backend
- ✅ API Gateway exposing Lambda Functionality
- ▫️ Fargate with Lambda at the front
- ▫️ Public-facing Application Load Balancer with ECS on Amazon EC2

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

API Gateway exposing Lambda Functionality

Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale. APIs act as the "front door" for applications to access data, business logic, or functionality from your backend services.

How API Gateway Works:  via - https://aws.amazon.com/api-gateway/

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

How Lambda function works:  via - https://aws.amazon.com/lambda/

API Gateway can expose Lambda functionality through RESTful APIs. Both are serverless options offered by AWS and hence the right choice for this scenario, considering all the functionality they offer.

Incorrect options:

Fargate with Lambda at the front - Lambda cannot directly handle RESTful API requests. You can invoke a Lambda function over HTTPS by defining a custom RESTful API using Amazon API Gateway. So, Fargate with Lambda as the front-facing service is a wrong combination, though both Fargate and Lambda are serverless.

Public-facing Application Load Balancer with ECS on Amazon EC2 - ECS on Amazon EC2 does not come under serverless and hence cannot be considered for this use case.

Route 53 with EC2 as backend - Amazon EC2 is not a serverless service and hence cannot be considered for this use case.

Джерела: <https://aws.amazon.com/serverless/> · <https://aws.amazon.com/api-gateway/>

</details>

---

<a id="t10-q25"></a>
### T10-Q25
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

> ⚠️ **Kinesis ESM → on-failure destination (SNS).** → [детальніше](../../06-contradictions-and-traps.md#c21)

A Developer is writing an AWS Lambda function that processes records from an Amazon Kinesis Data Stream. The Developer must write the function so that it sends a notice to Administrators if it fails to process a batch of records.

How should the Developer write the function?

- ▫️ Separate the Lambda handler from the core logic
- ▫️ Push the failed records to an Amazon SQS queue
- ▫️ Use Amazon CloudWatch Events to send the processed data
- ✅ Configure an Amazon SNS topic as an on-failure destination

<details><summary>Пояснення (оригінал, EN)</summary>

With Destinations, you can route asynchronous function results as an execution record to a destination resource without writing additional code. An execution record contains details about the request and response in JSON format including version, timestamp, request context, request payload, response context, and response payload.

For each execution status such as Success or Failure you can choose one of four destinations: another Lambda function, SNS, SQS, or EventBridge. Lambda can also be configured to route different execution results to different destinations.

In this scenario the Developer can publish the processed data to an Amazon SNS topic by configuring an Amazon SNS topic as an on-failure destination.

CORRECT: "Configure an Amazon SNS topic as an on-failure destination" is the correct answer.

INCORRECT: "Separate the Lambda handler from the core logic" is incorrect as this will not assist with sending a notification to administrators.

INCORRECT: "Use Amazon CloudWatch Events to send the processed data" is incorrect as CloudWatch Events is used for tracking state changes, not forwarding execution results

INCORRECT: "Push the failed records to an Amazon SQS queue" is incorrect as SQS will not notify the administrators, SNS should be used.

Джерела: <https://aws.amazon.com/blogs/compute/introducing-aws-lambda-destinations/>

</details>

---

<a id="t11-q48"></a>
### T11-Q48
*⏭ пропущено · домен: офіційна мітка*

A development team wants to deploy an AWS Lambda function that requires significant CPU utilization.

As a Developer Associate, which of the following would you suggest for reducing the average runtime of the function?

- ▫️ Deploy the function into multiple AWS Regions
- ▫️ Deploy the function with its CPU allocation set to the maximum amount
- ✅ Deploy the function with its memory allocation set to the maximum amount
- ▫️ Deploy the function using Lambda layers

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Deploy the function with its memory allocation set to the maximum amount - Lambda allocates CPU power in proportion to the amount of memory configured. Memory is the amount of memory available to your Lambda function at runtime. You can increase or decrease the memory and CPU power allocated to your function using the Memory (MB) setting. To configure the memory for your function, set a value between 128 MB and 10,240 MB in 1-MB increments. At 1,769 MB, a function has the equivalent of one vCPU (one vCPU-second of credits per second).

Configuring function memory from AWS console:  via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html

Incorrect options:

Deploy the function into multiple AWS Regions - Deploying the Lambda function to multiple AWS Regions does not increase the compute capacity or CPU utilization capacity of Lambda. So, this option is irrelevant.

Deploy the function using Lambda layers - A Lambda layer is a .zip file archive that can contain additional code or data. A layer can contain libraries, a custom runtime, data, or configuration files. Layers promote code sharing and separation of responsibilities so that you can iterate faster on writing business logic. Layers do not increase the computational capacity of Lambda.

Deploy the function with its CPU allocation set to the maximum amount - This statement is given as a distractor. CPU allocation is an invalid parameter. As discussed above, the CPU is allocated in proportion to the memory allocated to the function.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---
