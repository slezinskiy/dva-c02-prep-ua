# AWS Step Functions

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#stepfn) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q35"></a>
### T07-Q35
*⏭ пропущено · домен: офіційна мітка*

An e-commerce company has an order processing workflow with several tasks to be done in parallel as well as decision steps to be evaluated for successful processing of the order. All the tasks are implemented via Lambda functions.

Which of the following is the BEST solution to meet these business requirements?

- ✅ Use AWS Step Functions state machines to orchestrate the workflow
- ▫️ Use AWS Step Functions activities to orchestrate the workflow
- ▫️ Use AWS Batch to orchestrate the workflow
- ▫️ Use AWS Glue to orchestrate the workflow

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use AWS Step Functions state machines to orchestrate the workflow

AWS Step Functions is a web service that enables you to coordinate the components of distributed applications and microservices using visual workflows. You build applications from individual components that each perform a discrete function, or task, allowing you to scale and change applications quickly.

How Step Functions Work:  via - https://aws.amazon.com/step-functions/

The following are key features of AWS Step Functions:

Step Functions are based on the concepts of tasks and state machines. You define state machines using the JSON-based Amazon States Language. A state machine is defined by the states it contains and the relationships between them. States are elements in your state machine. Individual states can make decisions based on their input, perform actions, and pass output to other states. In this way, a state machine can orchestrate workflows.

Please see this note for a simple example of a State Machine:  via - https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-state-machine-structure.html

Incorrect options:

Use AWS Step Functions activities to orchestrate the workflow - In AWS Step Functions, activities are a way to associate code running somewhere (known as an activity worker) with a specific task in a state machine. When a Step Function reaches an activity task state, the workflow waits for an activity worker to poll for a task. For example, an activity worker can be an application running on an Amazon EC2 instance or an AWS Lambda function. AWS Step Functions activities cannot orchestrate a workflow.

Use AWS Glue to orchestrate the workflow - AWS Glue is a fully managed extract, transform, and load (ETL) service that makes it easy for customers to prepare and load their data for analytics. AWS Glue cannot orchestrate a workflow.

Use AWS Batch to orchestrate the workflow - AWS Batch runs batch computing jobs on the AWS Cloud. AWS Batch dynamically provisions the optimal quantity and type of compute resources (e.g., CPU or memory optimized instances) based on the volume and specific resource requirements of the batch jobs submitted. AWS Batch cannot orchestrate a workflow.

Reference:

https://aws.amazon.com/step-functions/

https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-state-machine-structure.html

</details>

---
