# ECS / ECR / Fargate

**Домен 2 — Безпека** · питань: 1  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t06-q32"></a>
### T06-Q32
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is developing a new online game that will run on top of Amazon ECS. Four distinct Amazon ECS services will be part of the architecture, each requiring specific permissions to various AWS services. The company wants to optimize the use of the underlying Amazon EC2 instances by bin packing the containers based on memory reservation.

Which configuration would allow the Development team to meet these requirements MOST securely

- ✅ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS task definition to reference the associated IAM role
- ❌ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS service to reference the associated IAM role
- ▫️ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then, create an IAM group and configure the ECS cluster to reference that group
- ▫️ Create a new Identity and Access Management (IAM) instance profile containing the required permissions for the various ECS services, then associate that instance role with the underlying EC2 instances

<details><summary>Пояснення (оригінал, EN)</summary>

With IAM roles for Amazon ECS tasks, you can specify an IAM role that can be used by the containers in a task. Applications must sign their AWS API requests with AWS credentials, and this feature provides a strategy for managing credentials for your applications to use, similar to the way that Amazon EC2 instance profiles provide credentials to EC2 instances.

Instead of creating and distributing your AWS credentials to the containers or using the EC2 instance’s role, you can associate an IAM role with an ECS task definition or RunTask API operation. The applications in the task’s containers can then use the AWS SDK or CLI to make API requests to authorized AWS services.

In this case each service requires access to different AWS services so following the principal of least privilege it is best to assign as a separate role to each task definition.

CORRECT: "Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS task definition to reference the associated IAM role" is the correct answer.

INCORRECT: "Create a new Identity and Access Management (IAM) instance profile containing the required permissions for the various ECS services, then associate that instance role with the underlying EC2 instances" is incorrect. It is a best practice to use IAM roles for tasks instead of assigning the roles to the container instances.

INCORRECT: "Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS service to reference the associated IAM role" is incorrect as the reference should be made within the task definition.

INCORRECT: "Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then, create an IAM group and configure the ECS cluster to reference that group" is incorrect as the reference should be made within the task definition.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html>

</details>

---
