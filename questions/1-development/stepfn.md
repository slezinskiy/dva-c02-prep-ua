# AWS Step Functions

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 7  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#stepfn) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t04-q40"></a>
### T04-Q40
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A legacy application is being refactored into a microservices architecture running on AWS. The microservice will include several AWS Lambda functions. A Developer will use AWS Step Functions to coordinate function execution.

How should the Developer proceed?

- ▫️ Create an AWS CloudFormation stack using a YAML-formatted template
- ▫️ Create a layer in AWS Lambda and add the functions to the layer
- ✅ Create a state machine using the Amazon States Language
- ▫️ Create a workflow using the StartExecution API action

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Step Functions is a web service that enables you to coordinate the components of distributed applications and microservices using visual workflows. You build applications from individual components that each perform a discrete function, or task, allowing you to scale and change applications quickly.

The following are key features of AWS Step Functions:

• Step Functions is based on the concepts of tasks and state machines.

• You define state machines using the JSON-based Amazon States Language.

• The Step Functions console displays a graphical view of your state machine's structure. This provides a way to visually check your state machine's logic and monitor executions.

The Developer needs to create a state machine using the Amazon States Language as this is how you can create an executable state machine that includes the Lambda functions that must be coordinated.

CORRECT: "Create a state machine using the Amazon States Language" is the correct answer.

INCORRECT: "Create an AWS CloudFormation stack using a YAML-formatted template" is incorrect as AWS Step Functions does not use CloudFormation. The Developer needs to create a state machine.

INCORRECT: "Create a workflow using the StartExecution API action" is incorrect as workflows are associated with Amazon SWF whereas the StartExecution API action is a Step Functions action for executing a state machine.

INCORRECT: "Create a layer in AWS Lambda and add the functions to the layer" is incorrect as a layer is a ZIP archive that contains libraries, a custom runtime, or other dependencies that you can use to pull additional code into a Lambda function.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html>

</details>

---

<a id="t07-q25"></a>
### T07-Q25
*⏭ пропущено · домен: офіційна мітка*

A team is checking the viability of using AWS Step Functions for creating a banking workflow for loan approvals. The web application will also have human approval as one of the steps in the workflow.

As a developer associate, which of the following would you identify as the key characteristics for AWS Step Function? (Select two)

- ✅ You should use Express Workflows for workloads with high event rates and short duration
- ✅ Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that can also support any human approval steps
- ▫️ Express Workflows have a maximum duration of five minutes and Standard workflows have a maximum duration of 180 days or 6 months
- ▫️ Both Standard and Express Workflows support all service integrations, activities, and design patterns
- ▫️ Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that do not support any human approval steps

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that can also support any human approval steps - Standard Workflows on AWS Step Functions are more suitable for long-running, durable, and auditable workflows where repeating workflow steps is expensive (e.g., restarting a long-running media transcode) or harmful (e.g., charging a credit card twice). Example workloads include training and deploying machine learning models, report generation, billing, credit card processing, and ordering and fulfillment processes. Step functions also support any human approval steps.

You should use Express Workflows for workloads with high event rates and short duration\* - You should use Express Workflows for workloads with high event rates and short durations. Express Workflows support event rates of more than 100,000 per second.

Incorrect options:

Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that do not support any human approval steps - As Step functions support any human approval steps, so this option is incorrect.

Express Workflows have a maximum duration of five minutes and Standard workflows have a maximum duration of 180 days or 6 months - Express Workflows have a maximum duration of five minutes and Standard workflows have a maximum duration of one year.

Both Standard and Express Workflows support all service integrations, activities, and design patterns - Standard Workflows support all service integrations, activities, and design patterns. Express Workflows do not support activities, job-run (.sync), and Callback patterns.

Reference:

https://aws.amazon.com/step-functions/features/

https://aws.amazon.com/blogs/compute/implementing-serverless-manual-approval-steps-in-aws-step-functions-and-amazon-api-gateway/

</details>

---

<a id="t07-q44"></a>
### T07-Q44
*⏭ пропущено · домен: офіційна мітка*

A company wants to automate and orchestrate a multi-source high-volume flow of data in a scalable data management solution built using AWS services. The solution must ensure that the business rules and transformations run in sequence, handle reprocessing of data in case of errors, and require minimal maintenance.

Which AWS service should the company use to manage and automate the orchestration of the data flows?

- ✅ AWS Step Functions
- ▫️ AWS Glue
- ▫️ Amazon Kinesis Data Streams
- ▫️ AWS Batch

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Step Functions

AWS Step Functions is a visual workflow service that helps developers use AWS services to build distributed applications, automate processes, orchestrate microservices, and create data and machine learning (ML) pipelines.

 via - https://aws.amazon.com/step-functions/

Incorrect options:

Amazon Kinesis Data Streams - Amazon Kinesis Data Streams is a serverless streaming data service that makes it easy to capture, process, and store data streams at any scale.

AWS Glue - AWS Glue is a serverless data integration service that makes it easier to discover, prepare, move, and integrate data from multiple sources for analytics, machine learning (ML), and application development.

AWS Batch - AWS Batch is a set of batch management capabilities that enables developers, scientists, and engineers to easily and efficiently run hundreds of thousands of batch computing jobs on AWS. AWS Batch dynamically provisions the optimal quantity and type of compute resources (e.g., CPU or memory optimized compute resources) based on the volume and specific resource requirements of the batch jobs submitted.

Джерела: <https://aws.amazon.com/step-functions/> · <https://aws.amazon.com/kinesis/data-streams/> · <https://aws.amazon.com/glue/> · <https://aws.amazon.com/batch/faqs/>

</details>

---

<a id="t09-q27"></a>
### T09-Q27
*⏭ пропущено · домен: офіційна мітка*

A company wants to automate its order fulfillment and inventory tracking workflow. Starting from order creation to updating inventory to shipment, the entire process has to be tracked, managed and updated automatically.

Which of the following would you recommend as the most optimal solution for this requirement?

- ▫️ Use Amazon Simple Queue Service (Amazon SQS) queue to pass information from order management to inventory tracking workflow
- ✅ Use AWS Step Functions to coordinate and manage the components of order management and inventory tracking workflow
- ▫️ Use Amazon SNS to develop event-driven applications that can share information
- ▫️ Configure Amazon EventBridge to track the flow of work from order management to inventory tracking systems

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use AWS Step Functions to coordinate and manage the components of order management and inventory tracking workflow

AWS Step Functions is a serverless function orchestrator that makes it easy to sequence AWS Lambda functions and multiple AWS services into business-critical applications. Through its visual interface, you can create and run a series of checkpointed and event-driven workflows that maintain the application state. The output of one step acts as an input to the next. Each step in your application executes in order, as defined by your business logic.

AWS Step Functions enables you to implement a business process as a series of steps that make up a workflow. The individual steps in the workflow can invoke a Lambda function or a container that has some business logic, update a database such as DynamoDB or publish a message to a queue once that step or the entire workflow completes execution.

Benefits of Step Functions:

Build and update apps quickly: AWS Step Functions lets you build visual workflows that enable the fast translation of business requirements into technical requirements. You can build applications in a matter of minutes, and when needs change, you can swap or reorganize components without customizing any code.

Improve resiliency: AWS Step Functions manages state, checkpoints and restarts for you to make sure that your application executes in order and as expected. Built-in try/catch, retry and rollback capabilities deal with errors and exceptions automatically.

Write less code: AWS Step Functions manages the logic of your application for you and implements basic primitives such as branching, parallel execution, and timeouts. This removes extra code that may be repeated in your microservices and functions.

How Step Functions work:  via - https://aws.amazon.com/step-functions/

Incorrect options:

Use Amazon Simple Queue Service (Amazon SQS) queue to pass information from order management to inventory tracking workflow - You should consider AWS Step Functions when you need to coordinate service components in the development of highly scalable and auditable applications. You should consider using Amazon Simple Queue Service (Amazon SQS), when you need a reliable, highly scalable, hosted queue for sending, storing, and receiving messages between services. Step Functions keeps track of all tasks and events in an application. Amazon SQS requires you to implement your own application-level tracking, especially if your application uses multiple queues.

Configure Amazon EventBridge to track the flow of work from order management to inventory tracking systems - Both Amazon EventBridge and Amazon SNS can be used to develop event-driven applications, and your choice will depend on your specific needs. Amazon EventBridge is recommended when you want to build an application that reacts to events from SaaS applications and/or AWS services. Amazon EventBridge is the only event-based service that integrates directly with third-party SaaS partners.

Use Amazon SNS to develop event-driven applications that can share information - Amazon SNS is recommended when you want to build an application that reacts to high throughput or low latency messages published by other applications or microservices (as Amazon SNS provides nearly unlimited throughput), or for applications that need very high fan-out (thousands or millions of endpoints).

Джерела: <https://aws.amazon.com/step-functions/faqs/> · <https://aws.amazon.com/eventbridge/faqs/>

</details>

---

<a id="t09-q38"></a>
### T09-Q38
*⏭ пропущено · домен: офіційна мітка*

While defining a business workflow as state machine on AWS Step Functions, a developer has configured several states.

Which of the following would you identify as the state that represents a single unit of work performed by a state machine?

- ▫️

  ```json
  "No-op": {
    "Type": "Task",
    "Result": {
      "x-datum": 0.381018,
      "y-datum": 622.2269926397355
    },
    "ResultPath": "$.coords",
    "Next": "End"
  }
  ```
- ▫️

  ```json
  "wait_until" : {
    "Type": "Wait",
    "Timestamp": "2016-03-14T01:59:00Z",
    "Next": "NextState"
  }
  ```
- ▫️

  ```json
  "FailState": {
    "Type": "Fail",
    "Cause": "Invalid response.",
    "Error": "ErrorA"
  }
  ```
- ✅

  ```json
  "HelloWorld": {
    "Type": "Task",
    "Resource": "arn:aws:lambda:us-east-1:123456789012:function:HelloFunction",
    "Next": "AfterHelloWorldState",
    "Comment": "Run the HelloWorld Lambda function"
  }
  ```

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"HelloWorld": {
  "Type": "Task",
  "Resource": "arn:aws:lambda:us-east-1:123456789012:function:HelloFunction",
  "Next": "AfterHelloWorldState",
  "Comment": "Run the HelloWorld Lambda function"
}
A Task state ("Type": "Task") represents a single unit of work performed by a state machine.

All work in your state machine is done by tasks. A task performs work by using an activity or an AWS Lambda function, or by passing parameters to the API actions of other services.

AWS Step Functions can invoke Lambda functions directly from a task state. A Lambda function is a cloud-native task that runs on AWS Lambda. You can write Lambda functions in a variety of programming languages, using the AWS Management Console or by uploading code to Lambda.

Incorrect options:

"wait_until" : {
  "Type": "Wait",
  "Timestamp": "2016-03-14T01:59:00Z",
  "Next": "NextState"
}
A Wait state ("Type": "Wait") delays the state machine from continuing for a specified time.
"No-op": {
  "Type": "Task",
  "Result": {
    "x-datum": 0.381018,
    "y-datum": 622.2269926397355
  },
  "ResultPath": "$.coords",
  "Next": "End"
}
Resource field is a required parameter for Task state. This definition is not of a Task but of type Pass.
"FailState": {
  "Type": "Fail",
  "Cause": "Invalid response.",
  "Error": "ErrorA"
}
A Fail state ("Type": "Fail") stops the execution of the state machine and marks it as a failure unless it is caught by a Catch block. Because Fail states always exit the state machine, they have no Next field and don't require an End field.
Reference:

https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-task-state.html

</details>

---

<a id="t10-q18"></a>
### T10-Q18
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

An IT automation architecture uses many AWS Lambda functions invoking one another as a large state machine. The coordination of this state machine is legacy custom code that breaks easily.
Which AWS Service can help refactor and manage the state machine?

- ▫️ AWS CloudFormation
- ▫️ AWS CodeBuild
- ▫️ AWS CodePipeline
- ✅ AWS Step Functions

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Step Functions lets you coordinate multiple AWS services into serverless workflows so you can build and update apps quickly. Using Step Functions, you can design and run workflows that stitch together services, such as AWS Lambda, AWS Fargate, and Amazon SageMaker, into feature-rich applications.

Workflows are made up of a series of steps, with the output of one step acting as input into the next. Application development is simpler and more intuitive using Step Functions, because it translates your workflow into a state machine diagram that is easy to understand, easy to explain to others, and easy to change.

Step Functions automatically triggers and tracks each step, and retries when there are errors, so your application executes in order and as expected. With Step Functions, you can craft long-running workflows such as machine learning model training, report generation, and IT automation.

Therefore, AWS Step Functions is the best AWS service to use when refactoring the application away from the legacy code.

CORRECT: "AWS Step Functions" is the correct answer.

INCORRECT: "AWS CloudFormation" is incorrect as CloudFormation is used for deploying resources no AWS but not for ongoing automation.

INCORRECT: "AWS CodePipeline" is incorrect as this is used as part of a continuous integration and delivery (CI/CD) pipeline to deploy software updates to applications.

INCORRECT: "AWS CodeBuild" is incorrect as this an AWS build/test service.

Джерела: <https://aws.amazon.com/step-functions/>

</details>

---

<a id="t10-q40"></a>
### T10-Q40
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company currently runs a number of legacy automated batch processes for system update management and operational activities. The company are looking to refactor these processes and require a service that can coordinate multiple AWS services into serverless workflows.

What is the MOST suitable service for this requirement?

- ▫️ Amazon SWF
- ▫️ AWS Batch
- ▫️ AWS Lambda
- ✅ AWS Step Functions

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Step Functions is a web service that enables you to coordinate the components of distributed applications and microservices using visual workflows. You build applications from individual components that each perform a discrete function, or task, allowing you to scale and change applications quickly.

Step Functions provides a reliable way to coordinate components and step through the functions of your application. Step Functions offers a graphical console to visualize the components of your application as a series of steps. It automatically triggers and tracks each step, and retries when there are errors, so your application executes in order and as expected, every time. Step Functions logs the state of each step, so when things go wrong, you can diagnose and debug problems quickly.

CORRECT: "AWS Step Functions" is the correct answer.

INCORRECT: "Amazon SWF" is incorrect. You can think of Amazon SWF as a fully-managed state tracker and task coordinator in the Cloud. It does not coordinate serverless workflows.

INCORRECT: "AWS Batch" is incorrect as this is used to run batch computing jobs on Amazon EC2 and is therefore not serverless.

INCORRECT: "AWS Lambda" is incorrect as though it is serverless, it does not provide a native capability to coordinate multiple AWS services.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html>

</details>

---
