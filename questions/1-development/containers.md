# ECS / ECR / Fargate

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 16  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q19"></a>
### T02-Q19
*домен: визначено автоматично (у джерелі: AWS Compute)*

```json
A Developer has created a task definition that includes the following JSON code:
"placementStrategy": [
{
"field": "attribute:ecs.availability-zone",
"type": "spread"
},
{
"field": "instanceId",
"type": "spread"
}
]
What is the effect of this task placement strategy?
```

- ✅ It distributes tasks evenly across Availability Zones and then distributes tasks evenly across the instances within each Availability Zone
- ▫️ It distributes tasks evenly across Availability Zones and then distributes tasks evenly across distinct instances within each Availability Zone
- ▫️ It distributes tasks evenly across Availability Zones and then bin packs tasks based on memory within each Availability Zone
- ▫️ It distributes tasks evenly across Availability Zones and then distributes tasks randomly across instances within each Availability Zone

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «It distributes tasks evenly across Availability Zones and then distributes tasks evenly across the instances within each Availability Zone»**

Task placement strategy — алгоритм вибору інстансів для розміщення задач (або задач для термінації). Задається при запуску задачі чи створенні сервісу (`CreateService`, `UpdateService`, `RunTask`). Стратегії ECS:
- **binpack** — розміщує за найменшим залишком CPU або пам'яті, мінімізує кількість інстансів;
- **random** — випадково;
- **spread** — рівномірно за вказаним значенням: `instanceId` (або `host`) чи атрибутом інстансу, наприклад `attribute:ecs.availability-zone`.

Стратегії можна комбінувати: тут спершу spread по AZ, потім spread по інстансах усередині AZ.

**❌ Неправильні варіанти:**
- *…then bin packs tasks based on memory* — binpack тут не використовується.
- *…across distinct instances* — гарантію «окремий інстанс для кожної задачі» дає placement constraint `distinctInstance`, а не стратегія.
- *…randomly across instances* — random тут не використовується.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t02-q22"></a>
### T02-Q22
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company runs many microservices applications that use Docker containers. The company are planning to migrate the containers to Amazon ECS. The workloads are highly variable and therefore the company prefers to be charged per running task.

Which solution is the BEST fit for the company’s requirements?

- ▫️ Amazon ECS with the EC2 launch type
- ▫️ An Amazon ECS Cluster with Auto Scaling
- ✅ Amazon ECS with the Fargate launch type
- ▫️ An Amazon ECS Service with Auto Scaling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon ECS with the Fargate launch type»**

Ключова вимога — платити за запущені задачі. На Fargate AWS бере плату саме за задачі, а не за container instances. Fargate запускає контейнери без провізіонінгу й керування інфраструктурою: реєструєш task definition, і Fargate сам запускає контейнер. Це serverless-модель під керуванням AWS.

**❌ Неправильні варіанти:**
- *ECS with the EC2 launch type* — платиш за запущені EC2-інстанси.
- *ECS Service with Auto Scaling* і *ECS Cluster with Auto Scaling* — не вказано launch type: сервіс і кластер можуть працювати і на Fargate, і на EC2.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html>

</details>

---

<a id="t02-q23"></a>
### T02-Q23
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer will be launching several Docker containers on a new Amazon ECS cluster using the EC2 Launch Type. The containers will all run a web service on port 80.

What is the EASIEST way the Developer can configure the task definition to ensure the web services run correctly and there are no port conflicts on the host instances?

- ✅ Specify port 80 for the container port and port 0 for the host port
- ▫️ Leave both the container port and host port configuration blank
- ▫️ Specify port 80 for the container port and a unique port number for the host port
- ▫️ Specify a unique port number for the container port and port 80 for the host port

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Specify port 80 for the container port and port 0 for the host port»**

Port mappings у container definition дають контейнерам доступ до портів хоста. Container port — порт усередині контейнера; host port — порт на інстансі, зарезервований для контейнера (заданий або призначений автоматично).

Кілька контейнерів не можуть займати один host port, тож кожному потрібен інший. Найпростіше задати host port = 0: ECS сам призначить вільний порт (dynamic port mapping). А container port має бути 80, бо на ньому працює веб-сервіс.

**❌ Неправильні варіанти:**
- *Container 80, unique host port* — доводиться вручну призначати порти, складніше керувати.
- *Unique container port, host port 80* — сервіс має слухати 80 у контейнері, а на порт 80 хоста можна прив'язати лише один контейнер.
- *Leave both blank* — ECS призначить обидва порти динамічно, і сервіс не працюватиме на 80.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_PortMapping.html>

</details>

---

<a id="t02-q30"></a>
### T02-Q30
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is deploying an application in a microservices architecture on Amazon ECS. The Developer needs to choose the best task placement strategy to MINIMIZE the number of instances that are used. Which task placement strategy should be used?

- ▫️ weighted
- ✅ binpack
- ▫️ spread
- ▫️ random

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «binpack»**

Стратегії розміщення задач ECS:
- **binpack** — розміщує за найменшим залишком CPU або пам'яті, мінімізуючи кількість інстансів;
- **random** — випадково;
- **spread** — рівномірно за значенням (`instanceId`/`host` або атрибут на кшталт `attribute:ecs.availability-zone`). Задачі сервісу розподіляються відносно задач цього сервісу, окремі задачі — відносно задач тієї ж task group.

Мінімізувати кількість інстансів — це binpack.

**❌ Неправильні варіанти:**
- *random* — не мінімізує кількість інстансів.
- *spread* — розподіляє рівномірно, а не ущільнює.
- *weighted* — не стратегія ECS (це політика маршрутизації Route 53).

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t02-q49"></a>
### T02-Q49
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company is deploying a microservices application on AWS Fargate using Amazon ECS. The application has environment variables that must be passed to a container for the application to initialize.

How should the environment variables be passed to the container?

- ▫️ Use advanced container definition parameters and define environment variables under the environment parameter within the service definition.
- ▫️ Use standard container definition parameters and define environment variables under the WorkingDirectory parameter within the service definition.
- ✅ Use advanced container definition parameters and define environment variables under the environment parameter within the task definition.
- ▫️ Use standard container definition parameters and define environment variables under the secrets parameter within the task definition.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use advanced container definition parameters and define environment variables under the environment parameter within the task definition»**

При реєстрації task definition задають список container definitions, які передаються Docker daemon на інстансі. Змінні середовища передають через параметр `environment` (з розширених параметрів container definition) саме в **task definition**.

**❌ Неправильні варіанти:**
- *…within the service definition* — змінні середовища задають у task definition, а не в описі сервісу.
- *Standard parameters … under the secrets parameter* — потрібен параметр `environment` (`secrets` — для значень із Secrets Manager/SSM).
- *Standard parameters … under the WorkingDirectory parameter* — `WorkingDirectory` — робочий каталог, а не змінні.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/userguide/task_definition_parameters.html#container_definition_environment>

</details>

---

<a id="t04-q03"></a>
### T04-Q03
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is migrating Docker containers to Amazon ECS. A large number of containers will be deployed onto an existing ECS cluster that uses container instances of different instance types.

Which task placement strategy can be used to minimize the number of container instances used based on available memory?

- ▫️ spread
- ✅ binpack
- ▫️ distinctInstance
- ▫️ random

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «binpack»**

Для задач на EC2 launch type ECS вирішує, де їх розмістити з урахуванням CPU і пам'яті з task definition, а при зменшенні — які задачі зупинити. Це налаштовується стратегіями і обмеженнями розміщення. (На Fargate вони не підтримуються, задачі за замовчуванням розподіляються по AZ.)

Стратегії: **binpack** (за найменшим залишком CPU або пам'яті, мінімізує кількість інстансів), **random**, **spread** (рівномірно за `instanceId` або атрибутом). Тут потрібен binpack за пам'яттю.

**❌ Неправильні варіанти:**
- *random* — випадковий розподіл.
- *spread* — розподіляє задачі для доступності, а не ущільнює.
- *distinctInstance* — це обмеження (constraint), а не стратегія, і воно розміщує кожну задачу на окремому інстансі.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t04-q07"></a>
### T04-Q07
*домен: визначено автоматично (у джерелі: AWS Compute)*

```json
A Developer has created a task definition that includes the following JSON code:
"placementConstraints": [
{
"expression": "attribute:ecs.instance-type =~ t2.*",
"type": "memberOf"
}
]
What will be the effect for tasks using this task definition?
```

- ▫️ They will be added to distinct instances using the T2 instance type
- ▫️ They will be spread across all instances except for T2 instances
- ▫️ They will be placed only on container instances of T2 or T3 instance types
- ✅ They will be placed only on container instances using the T2 instance type

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «They will be placed only on container instances using the T2 instance type»**

Task placement constraint — правило, яке враховується при розміщенні задач. `memberOf` розміщує задачі лише на інстансах, що відповідають виразу. Його можна задати при запуску задачі, створенні сервісу, реєстрації task definition або її нової ревізії (`CreateService`, `UpdateService`, `RegisterTaskDefinition`, `RunTask`). Вираз `attribute:ecs.instance-type =~ t2.*` означає будь-який тип T2 (t2.micro, t2.large тощо).

**❌ Неправильні варіанти:**
- *Distinct instances using T2* — `memberOf` не гарантує окремих інстансів (це `distinctInstance`).
- *T2 or T3* — лише T2.
- *All instances except T2* — навпаки, лише T2.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t04-q36"></a>
### T04-Q36
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is building a Docker application on Amazon ECS that will use an Application Load Balancer (ALB). The developer needs to configure the port mapping between the host port and container port. Where is this setting configured?

- ▫️ Host definition
- ▫️ Container instance
- ✅ Task definition
- ▫️ Service scheduler

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Task definition»**

Port mappings дають контейнерам доступ до портів хоста. Вони задаються в container definition, а container definitions — у **task definition**:
- `containerPort` — порт у контейнері;
- `hostPort` — порт на інстансі, зарезервований для контейнера.

З ALB можна використати dynamic port mapping (`hostPort: 0`), щоб запускати кілька задач сервісу на одному інстансі.

**❌ Неправильні варіанти:**
- *Host definition* — такого немає.
- *Service scheduler* — планує й розміщує задачі.
- *Container instance* — на інстансі port mappings не налаштовують.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_PortMapping.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/dynamic-port-mapping-ecs/>

</details>

---

<a id="t04-q61"></a>
### T04-Q61
*домен: визначено автоматично (у джерелі: AWS Compute) · дублікат [T02-Q22](../../questions/1-development/containers.md#t02-q22)*

A company runs many microservices applications that use Docker containers. The company are planning to migrate the containers to Amazon ECS. The workloads are highly variable and therefore the company prefers to be charged per running task.

Which solution is the BEST fit for the company’s requirements?

- ✅ Amazon ECS with the Fargate launch type
- ▫️ An Amazon ECS Service with Auto Scaling
- ▫️ Amazon ECS with the EC2 launch type
- ▫️ An Amazon ECS Cluster with Auto Scaling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon ECS with the Fargate launch type»** *(дублікат T02-Q22)*

Ключова вимога — платити за запущені задачі. На Fargate AWS бере плату за задачі, а не за container instances. Fargate — serverless: реєструєш task definition, і контейнер запускається без провізіонінгу інфраструктури.

**❌ Неправильні варіанти:**
- *ECS with the EC2 launch type* — платиш за EC2-інстанси.
- *ECS Service with Auto Scaling* і *ECS Cluster with Auto Scaling* — launch type не вказано.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html>

</details>

---

<a id="t06-q35"></a>
### T06-Q35
*домен: визначено автоматично (у джерелі: AWS Compute)*

An organization has a new containerized application that needs to be launched quickly. The team lead has stated to choose the option that would reduce the burden of infrastructure maintenance for the team.

Which solution best meets this requirement?

- ✅ Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on AWS Fargate.
- ▫️ Use Amazon Elastic Container Service (ECS) to provide a template that supports a quick launch of AWS Fargate applications on Amazon Elastic Compute Cloud (EC2).
- ▫️ Use AWS Fargate to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on Amazon Elastic Compute Cloud (EC2).
- ▫️ Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on Amazon Elastic Compute Cloud (EC2).

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on AWS Fargate.»**

AWS Copilot — CLI для швидкого запуску контейнерних застосунків на ECS. Він може запускати задачі й сервіси на serverless-інфраструктурі Fargate, якою клієнтам не потрібно керувати.

**❌ Неправильні варіанти:**
- *Copilot → ECS on EC2* — EC2 не serverless і потребує обслуговування.
- *Fargate provides a template* — Fargate — serverless compute engine, шаблонів він не надає; до того ж EC2 не serverless.
- *ECS provides a template* — ECS — оркестратор контейнерів і шаблонів не надає; до того ж EC2 не serverless.

Джерела: <https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-a-clustered-application-to-amazon-ecs-by-using-aws-copilot.html>

</details>

---

<a id="t07-q21"></a>
### T07-Q21
*домен: офіційна мітка*

Your company is planning to move away from reserving EC2 instances and would like to adopt a more agile form of serverless architecture.

Which of the following is the simplest and the least effort way of deploying the Docker containers on this serverless architecture?

- ▫️ Amazon Elastic Kubernetes Service (Amazon EKS) on Fargate
- ✅ Amazon Elastic Container Service (Amazon ECS) on Fargate
- ▫️ AWS Elastic Beanstalk
- ▫️ Amazon Elastic Container Service (Amazon ECS) on EC2

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Elastic Container Service (Amazon ECS) on Fargate»**

ECS — швидкий масштабований сервіс для запуску й керування Docker-контейнерами в кластері. З launch type Fargate інфраструктура serverless і керується AWS. Fargate працює з ECS і EKS: сервери не потрібні, ресурси задаються й оплачуються для кожного застосунку, а ізоляція підвищує безпеку.

Огляд: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html

**❌ Неправильні варіанти:**
- *ECS on EC2* — використовує EC2-інстанси, тож це не serverless.
- *EKS on Fargate* — serverless, але Kubernetes складніший; для найпростішого рішення ECS на Fargate кращий.
- *Elastic Beanstalk* — простий деплой вебзастосунків, але працює на EC2, тож не serverless.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html> · <https://aws.amazon.com/eks/> · <https://aws.amazon.com/elasticbeanstalk/>

</details>

---

<a id="t08-q05"></a>
### T08-Q05
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating a service on Amazon ECS and needs to ensure that each task is placed on a different container instance.

How can this be achieved?

- ▫️ Create a cluster with multiple container instances
- ▫️ Create a service on Fargate
- ✅ Use a task placement constraint
- ▫️ Use a task placement strategy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a task placement constraint»**

Task placement constraint — правило, яке враховується під час розміщення задач. Його задають під час запуску задачі або створення сервісу. Типи:
- **`distinctInstance`** — кожна задача на окремому container instance.
- **`memberOf`** — задачі лише на інстансах, що відповідають виразу Cluster Query Language. Задається при запуску задачі, створенні сервісу, створенні task definition або її нової ревізії.

Приклад, щоб кожна задача працювала на окремому інстансі:
```json
"placementConstraints": [ { "type": "distinctInstance" } ]
```

**❌ Неправильні варіанти:**
- *Task placement strategy* — вибирає інстанси за алгоритмами binpack, random і spread, але не гарантує розміщення.
- *Service on Fargate* — Fargate розподіляє задачі по AZ, а container instances у ньому немає.
- *Multiple container instances* — кілька інстансів не гарантують, що кожна задача буде на окремому.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t08-q22"></a>
### T08-Q22
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is deploying an application using Docker containers on Amazon ECS. One of the containers runs a database and should be placed on instances in the “databases” task group.

What should the Developer use to control the placement of the database task?

- ▫️ Cluster Query Language
- ▫️ ECS Container Agent
- ▫️ IAM Group
- ✅ Task Placement Constraint

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Task Placement Constraint»**

Task placement constraint — правило, яке враховується під час розміщення задач. Його задають при запуску задачі чи створенні сервісу і можна оновлювати для наявних сервісів.
- **`distinctInstance`** — кожна задача на окремому інстансі.
- **`memberOf`** — задачі на інстансах, що відповідають виразу Cluster Query Language.

Приклад розміщення задач у групі `databases`; працює з `CreateService`, `UpdateService`, `RegisterTaskDefinition` і `RunTask`:
```json
"placementConstraints": [ { "expression": "task:group == databases", "type": "memberOf" } ]
```

**❌ Неправильні варіанти:**
- *Cluster Query Language* — це лише мова виразів для групування за AZ, типом інстансу чи метаданими. Використовується всередині constraint, а не замість нього.
- *IAM Group* — групує IAM-користувачів і не керує розміщенням задач.
- *ECS Container Agent* — підключає інстанси до кластера.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t10-q55"></a>
### T10-Q55
*домен: визначено автоматично (у джерелі: AWS Compute)*

```json
A Developer has created a task definition that includes the following JSON code:
"placementConstraints": [
{
"expression": "task:group == databases",
"type": "memberOf"
}
]
What will be the effect for tasks using this task definition?
```

- ✅ They will be placed on container instances in the “databases” task group
- ▫️ They will not be placed on container instances in the “databases” task group
- ▫️ They will become members of a task group called “databases”
- ▫️ They will not be allowed to run unless they have the “databases” tag assigned

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «They will be placed on container instances in the “databases” task group»**

Task placement constraint — правило для розміщення задач. `memberOf` розміщує задачі лише на container instances, що відповідають виразу. Його задають при запуску задачі, створенні сервісу, створенні task definition або нової ревізії; у API — `CreateService`, `UpdateService`, `RegisterTaskDefinition`, `RunTask`. Тут вираз `task:group == databases`, тож задачі розміщуються поряд із задачами групи «databases».

**❌ Неправильні варіанти:**
- *Become members of the group* — constraint визначає, де розмістити задачі, а не членство в групі.
- *Will not be placed* — навпаки: розміщуються саме там.
- *Require a “databases” tag* — до тегів не стосується.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t11-q11"></a>
### T11-Q11
*домен: офіційна мітка*

ECS Fargate container tasks are usually spread across Availability Zones (AZs) and the underlying workloads need persistent cross-AZ shared access to the data volumes configured for the container tasks.

Which of the following solutions is the best choice for these workloads?

- ✅ Amazon EFS volumes
- ▫️ Docker volumes
- ▫️ AWS Gateway Storage volumes
- ▫️ Bind mounts

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon EFS volumes»**

EFS дає просте, масштабоване й постійне файлове сховище для задач ECS. Ємність росте й зменшується автоматично. Підтримується і на Fargate, і на EC2. Задачі мають доступ до того самого сховища незалежно від інстансу, на якому запущені. Для EC2 AMI має монтувати EFS до старту Docker, а task definitions — посилатися на ці точки монтування. У сучасних ECS EFS задають прямо в task definition через `efsVolumeConfiguration`.

**❌ Неправильні варіанти:**
- *Docker volumes* — створюються в `/var/lib/docker/volumes` на хості, лише на EC2, не спільні.
- *Bind mounts* — тимчасове сховище на хості.
- *Storage Gateway volumes* — не для ECS.

</details>

---

<a id="t12-q16"></a>
### T12-Q16
*домен: офіційна мітка*

An application runs on Amazon Elastic Container Service (Amazon ECS) on AWS Fargate. The company's audit requirements mandate that logging and storing of application log data must be done centrally on AWS.

How will you configure this requirement?

- ▫️ Amazon ECS metric data is automatically sent to CloudWatch in 1-minute periods. Amazon ECS service using the Fargate launch type has CloudWatch CPU and memory utilization metrics that can be enabled from the ECS console
- ✅ Use the awslogs log driver to configure the containers in your tasks to send log information to CloudWatch Logs. Add the required logConfiguration parameters to your task definition
- ▫️ Use the awslogs log driver to send log information to CloudWatch Logs. To turn on the awslogs log driver, your Amazon ECS container instances require at least version 1.9.0 of the container agent
- ▫️ Download and install the unified CloudWatch agent on the ECS instances to collect internal system-level metrics and application logs from the instances. The logs collected by the unified CloudWatch agent are processed and stored in Amazon CloudWatch logs and can be queried for report generation

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the awslogs log driver to configure the containers in your tasks to send log information to CloudWatch Logs. Add the required logConfiguration parameters to your task definition»**

Драйвер логів `awslogs` змушує контейнери задачі надсилати логи в CloudWatch Logs. Для Fargate в task definition додають параметри `logConfiguration`, щоб увімкнути `awslogs`. Наприклад, кожен контейнер має свій `logConfiguration`: контейнер WordPress пише в log group `awslogs-wordpress`, MySQL — в `awslogs-mysql`, з префіксом stream `awslogs-example`.

Детальніше: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html#specify-log-config

**❌ Неправильні варіанти:**
- *Container agent version 1.9.0* — вимога щодо агента стосується EC2 launch type, а не Fargate.
- *ECS metrics every 1 minute* — метрики CPU і пам'яті для Fargate справді є автоматично, але це системні метрики, а потрібні логи застосунку.
- *Install CloudWatch agent on ECS instances* — Fargate serverless, і встановлювати ПЗ на інстанси неможливо.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html> · <https://docs.aws.amazon.com/AmazonECS/latest/userguide/cloudwatch-metrics.html#available_cloudwatch_metrics>

</details>

---
