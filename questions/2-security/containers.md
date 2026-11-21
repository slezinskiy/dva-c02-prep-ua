# ECS / ECR / Fargate

**Домен 2 — Безпека** · питань: 1  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t06-q32"></a>
### T06-Q32
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is developing a new online game that will run on top of Amazon ECS. Four distinct Amazon ECS services will be part of the architecture, each requiring specific permissions to various AWS services. The company wants to optimize the use of the underlying Amazon EC2 instances by bin packing the containers based on memory reservation.

Which configuration would allow the Development team to meet these requirements MOST securely

- ✅ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS task definition to reference the associated IAM role
- ▫️ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS service to reference the associated IAM role
- ▫️ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then, create an IAM group and configure the ECS cluster to reference that group
- ▫️ Create a new Identity and Access Management (IAM) instance profile containing the required permissions for the various ECS services, then associate that instance role with the underlying EC2 instances

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then configure each ECS task definition to reference the associated IAM role»**

IAM roles for tasks задають роль, яку використовують контейнери задачі ECS. Принцип той самий, що в instance profile для EC2. Роль пов'язують із task definition (`taskRoleArn`) або з викликом `RunTask` замість роздавати ключі контейнерам чи брати роль інстансу. Застосунки в контейнерах звертаються до AWS через SDK або CLI.

Кожен сервіс потребує доступу до різних сервісів AWS, тож за принципом найменших привілеїв кожна task definition отримує власну роль.

**❌ Неправильні варіанти:**
- *Instance profile on EC2* — найкраща практика — ролі для задач, а не для container instances.
- *Reference role in ECS service* — роль вказують у task definition, а не в service.
- *IAM group referenced by cluster* — роль вказують у task definition.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html>

</details>

---
