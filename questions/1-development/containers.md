# ECS / ECR / Fargate

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 16  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q19"></a>
### T02-Q19
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

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

<details><summary>Пояснення (оригінал, EN)</summary>

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination. Task placement strategies can be specified when either running a task or creating a new service.

Amazon ECS supports the following task placement strategies:

binpack

Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

random

Place tasks randomly.

spread

Place tasks evenly based on the specified value. Accepted values are instanceId (or host, which has the same effect), or any platform or custom attribute that is applied to a container instance, such as attribute:ecs.availability-zone.

You can specify task placement strategies with the following actions: CreateService, UpdateService, and RunTask. You can also use multiple strategies together as in the example JSON code provided with the question.

CORRECT: "It distributes tasks evenly across Availability Zones and then distributes tasks evenly across the instances within each Availability Zone" is the correct answer.

INCORRECT: "It distributes tasks evenly across Availability Zones and then bin packs tasks based on memory within each Availability Zone" is incorrect as it does not use the binpack strategy.

INCORRECT: "It distributes tasks evenly across Availability Zones and then distributes tasks evenly across distinct instances within each Availability Zone" is incorrect as it does not spread tasks across distinct instances (use a task placement constraint).

INCORRECT: "It distributes tasks evenly across Availability Zones and then distributes tasks randomly across instances within each Availability Zone" is incorrect as it does not use the random strategy.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t02-q22"></a>
### T02-Q22
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A company runs many microservices applications that use Docker containers. The company are planning to migrate the containers to Amazon ECS. The workloads are highly variable and therefore the company prefers to be charged per running task.

Which solution is the BEST fit for the company’s requirements?

- ▫️ Amazon ECS with the EC2 launch type
- ▫️ An Amazon ECS Cluster with Auto Scaling
- ✅ Amazon ECS with the Fargate launch type
- ▫️ An Amazon ECS Service with Auto Scaling

<details><summary>Пояснення (оригінал, EN)</summary>

The key requirement is that the company should be charged per running task. Therefore, the best answer is to use Amazon ECS with the Fargate launch type as with this model AWS charge you for running tasks rather than running container instances.

The Fargate launch type allows you to run your containerized applications without the need to provision and manage the backend infrastructure. You just register your task definition and Fargate launches the container for you. The Fargate Launch Type is a serverless infrastructure managed by AWS.

CORRECT: "Amazon ECS with the Fargate launch type" is the correct answer.

INCORRECT: "Amazon ECS with the EC2 launch type" is incorrect as with this launch type you pay for running container instances (EC2 instances).

INCORRECT: "An Amazon ECS Service with Auto Scaling" is incorrect as this does not specify the launch type. You can run an ECS Service on the Fargate or EC2 launch types.

INCORRECT: "An Amazon ECS Cluster with Auto Scaling" is incorrect as this does not specify the launch type. You can run an ECS Cluster on the Fargate or EC2 launch types.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html>

</details>

---

<a id="t02-q23"></a>
### T02-Q23
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer will be launching several Docker containers on a new Amazon ECS cluster using the EC2 Launch Type. The containers will all run a web service on port 80.

What is the EASIEST way the Developer can configure the task definition to ensure the web services run correctly and there are no port conflicts on the host instances?

- ✅ Specify port 80 for the container port and port 0 for the host port
- ▫️ Leave both the container port and host port configuration blank
- ❌ Specify port 80 for the container port and a unique port number for the host port
- ▫️ Specify a unique port number for the container port and port 80 for the host port

<details><summary>Пояснення (оригінал, EN)</summary>

Port mappings allow containers to access ports on the host container instance to send or receive traffic. Port mappings are specified as part of the container definition. The container port is the port number on the container that is bound to the user-specified or automatically assigned host port. The host port is the port number on the container instance to reserve for your container.

As we cannot have multiple services bound to the same host port, we need to ensure that each container port mapping uses a different host port. The easiest way to do this is to set the host port number to 0 and ECS will automatically assign an available port. We also need to assign port 80 to the container port so that the web service is able to run.

CORRECT: "Specify port 80 for the container port and port 0 for the host port" is the correct answer.

INCORRECT: "Specify port 80 for the container port and a unique port number for the host port" is incorrect as this is more difficult to manage as you have to manually assign the port number.

INCORRECT: "Specify a unique port number for the container port and port 80 for the host port" is incorrect as the web service on the container needs to run on pot 80 and you can only bind one container to port 80 on the host so this would not allow more than one container to work.

INCORRECT: "Leave both the container port and host port configuration blank" is incorrect as this would mean that ECS would dynamically assign both the container and host port. As the web service must run on port 80 this would not work correctly.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_PortMapping.html>

</details>

---

<a id="t02-q30"></a>
### T02-Q30
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is deploying an application in a microservices architecture on Amazon ECS. The Developer needs to choose the best task placement strategy to MINIMIZE the number of instances that are used. Which task placement strategy should be used?

- ▫️ weighted
- ✅ binpack
- ❌ spread
- ▫️ random

<details><summary>Пояснення (оригінал, EN)</summary>

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination. Task placement strategies can be specified when either running a task or creating a new service.

Amazon ECS supports the following task placement strategies:

binpack - Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

random - Place tasks randomly.

spread - Place tasks evenly based on the specified value. Accepted values are instanceId (or host, which has the same effect), or any platform or custom attribute that is applied to a container instance, such as attribute:ecs.availability-zone. Service tasks are spread based on the tasks from that service. Standalone tasks are spread based on the tasks from the same task group.

The binpack task placement strategy is the most suitable for this scenario as it minimizes the number of instances used which is a requirement for this solution.

CORRECT: "binpack" is the correct answer.

INCORRECT: "random" is incorrect as this would assign tasks randomly to EC2 instances which would not result in minimizing the number of instances used.

INCORRECT: "spread" is incorrect as this would spread the tasks based on a specified value. This is not used for minimizing the number of instances used.

INCORRECT: "weighted" is incorrect as this is not an ECS task placement strategy. Weighted is associated with Amazon Route 53 routing policies.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t02-q49"></a>
### T02-Q49
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A company is deploying a microservices application on AWS Fargate using Amazon ECS. The application has environment variables that must be passed to a container for the application to initialize.

How should the environment variables be passed to the container?

- ▫️ Use advanced container definition parameters and define environment variables under the environment parameter within the service definition.
- ▫️ Use standard container definition parameters and define environment variables under the WorkingDirectory parameter within the service definition.
- ✅ Use advanced container definition parameters and define environment variables under the environment parameter within the task definition.
- ❌ Use standard container definition parameters and define environment variables under the secrets parameter within the task definition.

<details><summary>Пояснення (оригінал, EN)</summary>

When you register a task definition, you must specify a list of container definitions that are passed to the Docker daemon on a container instance.

The developer should use advanced container definition parameters and define environment variables to pass to the container.

CORRECT: "Use advanced container definition parameters and define environment variables under the environment parameter within the task definition" is the correct answer (as explained above.)

INCORRECT: "Use advanced container definition parameters and define environment variables under the environment parameter within the service definition" is incorrect.

The task definition is the correct place to define the environment variables to pass to the container.

INCORRECT: "Use standard container definition parameters and define environment variables under the secrets parameter within the task definition" is incorrect.

Advanced container definition parameters must be used to pass the environment variables to the container. The environment parameter should also be used.

INCORRECT: "Use standard container definition parameters and define environment variables under the WorkingDirectory parameter within the service definition" is incorrect.

Advanced container definition parameters must be used to pass the environment variables to the container. The environment parameter should also be used.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/userguide/task_definition_parameters.html#container_definition_environment>

</details>

---

<a id="t04-q03"></a>
### T04-Q03
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is migrating Docker containers to Amazon ECS. A large number of containers will be deployed onto an existing ECS cluster that uses container instances of different instance types.

Which task placement strategy can be used to minimize the number of container instances used based on available memory?

- ▫️ spread
- ✅ binpack
- ▫️ distinctInstance
- ▫️ random

<details><summary>Пояснення (оригінал, EN)</summary>

When a task that uses the EC2 launch type is launched, Amazon ECS must determine where to place the task based on the requirements specified in the task definition, such as CPU and memory. Similarly, when you scale down the task count, Amazon ECS must determine which tasks to terminate. You can apply task placement strategies and constraints to customize how Amazon ECS places and terminates tasks. Task placement strategies and constraints are not supported for tasks using the Fargate launch type. By default, Fargate tasks are spread across Availability Zones.

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination. For example, Amazon ECS can select instances at random, or it can select instances such that tasks are distributed evenly across a group of instances.

Amazon ECS supports the following task placement strategies:

• binpack

Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

• random

Place tasks randomly.

• spread

Place tasks evenly based on the specified value. Accepted values are instanceId (or host, which has the same effect), or any platform or custom attribute that is applied to a container instance, such as attribute:ecs.availability-zone. Service tasks are spread based on the tasks from that service. Standalone tasks are spread based on the tasks from the same task group.

The Developer should use the binpack task placement strategy using available memory to determine the placement of tasks. This will minimize the number of container instances required.

CORRECT: "binpack" is the correct answer.

INCORRECT: "random" is incorrect as this would just randomly assign the tasks across the available container instances in the cluster.

INCORRECT: "spread" is incorrect as this would attempt to spread the tasks across the cluster instances for better high availability.

INCORRECT: "distinctInstance" is incorrect as this is a task placement constraint, not a strategy. This constraint would result in the tasks being each placed on a separate instance which would not assist with meeting the requirements.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t04-q07"></a>
### T04-Q07
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

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

<details><summary>Пояснення (оригінал, EN)</summary>

A task placement constraint is a rule that is considered during task placement. Task placement constraints can be specified when either running a task or creating a new service.

The memberOf task placement constraint places tasks on container instances that satisfy an expression.

The memberOf task placement constraint can be specified with the following actions:

• Running a task

• Creating a new service

• Creating a new task definition

• Creating a new revision of an existing task definition

The example JSON code uses the memberOf constraint to place tasks on T2 instances. It can be specified with the following actions: CreateService, UpdateService, RegisterTaskDefinition, and RunTask.

CORRECT: "They will be placed only on container instances using the T2 instance type" is the correct answer.

INCORRECT: "They will be added to distinct instances using the T2 instance type" is incorrect. The memberOf constraint does not choose distinct instances.

INCORRECT: "They will be placed only on container instances of T2 or T3 instance types" is incorrect as only T2 instance types will be used. The wildcard means any T2 instance type such as t2.micro or t2.large.

INCORRECT: "They will be spread across all instances except for T2 instances" is incorrect as this code ensures the instances WILL be placed on T2 instance types.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t04-q36"></a>
### T04-Q36
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is building a Docker application on Amazon ECS that will use an Application Load Balancer (ALB). The developer needs to configure the port mapping between the host port and container port. Where is this setting configured?

- ▫️ Host definition
- ▫️ Container instance
- ✅ Task definition
- ▫️ Service scheduler

<details><summary>Пояснення (оригінал, EN)</summary>

Port mappings allow containers to access ports on the host container instance to send or receive traffic. Port mappings are specified as part of the container definition.

The container definition settings are specified within the task definition. The relevant settings are:

containerPort - the port number on the container that is bound to the user-specified or automatically assigned host port.

hostPort - the port number on the container instance to reserve for your container.

With an ALB you can use Dynamic port mapping which makes it easier to run multiple tasks on the same Amazon ECS service on an Amazon ECS cluster. This is configured by setting the host port to 0, as in the image below:

CORRECT: "Task definition" is the correct answer.

INCORRECT: "Host definition" is incorrect as there’s no such thing.

INCORRECT: "Service scheduler" is incorrect as the service scheduler is responsible for scheduling tasks and placing those tasks.

INCORRECT: "Container instance" is incorrect as you don’t specify any settings on the container instance to control the host and container port mappings.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_PortMapping.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/dynamic-port-mapping-ecs/>

</details>

---

<a id="t04-q61"></a>
### T04-Q61
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute) · дублікат [T02-Q22](../../questions/1-development/containers.md#t02-q22)*

A company runs many microservices applications that use Docker containers. The company are planning to migrate the containers to Amazon ECS. The workloads are highly variable and therefore the company prefers to be charged per running task.

Which solution is the BEST fit for the company’s requirements?

- ✅ Amazon ECS with the Fargate launch type
- ▫️ An Amazon ECS Service with Auto Scaling
- ▫️ Amazon ECS with the EC2 launch type
- ▫️ An Amazon ECS Cluster with Auto Scaling

<details><summary>Пояснення (оригінал, EN)</summary>

The key requirement is that the company should be charged per running task. Therefore, the best answer is to use Amazon ECS with the Fargate launch type as with this model AWS charge you for running tasks rather than running container instances.

The Fargate launch type allows you to run your containerized applications without the need to provision and manage the backend infrastructure. You just register your task definition and Fargate launches the container for you. The Fargate Launch Type is a serverless infrastructure managed by AWS.

CORRECT: "Amazon ECS with the Fargate launch type" is the correct answer.

INCORRECT: "Amazon ECS with the EC2 launch type" is incorrect as with this launch type you pay for running container instances (EC2 instances).

INCORRECT: "An Amazon ECS Service with Auto Scaling" is incorrect as this does not specify the launch type. You can run an ECS Service on the Fargate or EC2 launch types.

INCORRECT: "An Amazon ECS Cluster with Auto Scaling" is incorrect as this does not specify the launch type. You can run an ECS Cluster on the Fargate or EC2 launch types.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html>

</details>

---

<a id="t06-q35"></a>
### T06-Q35
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

An organization has a new containerized application that needs to be launched quickly. The team lead has stated to choose the option that would reduce the burden of infrastructure maintenance for the team.

Which solution best meets this requirement?

- ✅ Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on AWS Fargate.
- ▫️ Use Amazon Elastic Container Service (ECS) to provide a template that supports a quick launch of AWS Fargate applications on Amazon Elastic Compute Cloud (EC2).
- ▫️ Use AWS Fargate to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on Amazon Elastic Compute Cloud (EC2).
- ▫️ Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on Amazon Elastic Compute Cloud (EC2).

<details><summary>Пояснення (оригінал, EN)</summary>

Copilot can run tasks and services on serverless infrastructures such as AWS Fargate. This can provide the support of containers using ECS and serverless services like Fargate does not require customers to manage.

CORRECT: “Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on AWS Fargate” is the correct answer (as explained above.)

INCORRECT: "Use AWS Copilot to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on Amazon Elastic Compute Cloud (EC2)" is incorrect.

EC2 is not a serverless service and would require additional maintenance and support from the development team.

INCORRECT: "Use AWS Fargate to provide a template that supports a quick launch of Amazon Elastic Container Service (ECS) applications on Amazon Elastic Compute Cloud (EC2)" is incorrect.

EC2 is not a serverless service and would require additional maintenance and support from the development team. Fargate is a serverless compute engine. It would not provide a template to support a quick launch.

INCORRECT: "Use Amazon Elastic Container Service (ECS) to provide a template that supports a quick launch of AWS Fargate applications on Amazon Elastic Compute Cloud (EC2)" is incorrect.

EC2 is not a serverless service and would require additional maintenance and support from the development team. ECS is a container orchestration service that would not provide a template.

Джерела: <https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-a-clustered-application-to-amazon-ecs-by-using-aws-copilot.html>

</details>

---

<a id="t07-q21"></a>
### T07-Q21
*⏭ пропущено · домен: офіційна мітка*

Your company is planning to move away from reserving EC2 instances and would like to adopt a more agile form of serverless architecture.

Which of the following is the simplest and the least effort way of deploying the Docker containers on this serverless architecture?

- ▫️ Amazon Elastic Kubernetes Service (Amazon EKS) on Fargate
- ✅ Amazon Elastic Container Service (Amazon ECS) on Fargate
- ▫️ AWS Elastic Beanstalk
- ▫️ Amazon Elastic Container Service (Amazon ECS) on EC2

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon Elastic Container Service (Amazon ECS) on Fargate - Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster. You can host your cluster on a serverless infrastructure that is managed by Amazon ECS by launching your services or tasks using the Fargate launch type.

AWS Fargate is a serverless compute engine for containers that works with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS). Fargate makes it easy for you to focus on building your applications. Fargate removes the need to provision and manage servers, lets you specify and pay for resources per application, and improves security through application isolation by design.

ECS Fargate Overview:  via - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html

Incorrect options:

Amazon Elastic Container Service (Amazon ECS) on EC2 - Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster. ECS uses EC2 instances and hence cannot be called a serverless solution.

Amazon Elastic Kubernetes Service (Amazon EKS) on Fargate - Amazon Elastic Kubernetes Service (Amazon EKS) is a fully managed Kubernetes service. You can choose to run your EKS clusters using AWS Fargate, which is a serverless compute for containers. Since the use-case talks about the simplest and the least effort way to deploy Docker containers, EKS is not the best fit as you can use ECS Fargate to build a much easier solution. EKS is better suited to run Kubernetes on AWS without needing to install and operate your own Kubernetes control plane or worker nodes.

AWS Elastic Beanstalk - AWS Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services. You can simply upload your code and Elastic Beanstalk automatically handles the deployment, from capacity provisioning, load balancing, auto-scaling to application health monitoring. At the same time, you retain full control over the AWS resources powering your application and can access the underlying resources at any time. Beanstalk uses EC2 instances for its deployment, hence cannot be called a serverless architecture.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html> · <https://aws.amazon.com/eks/> · <https://aws.amazon.com/elasticbeanstalk/>

</details>

---

<a id="t08-q05"></a>
### T08-Q05
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating a service on Amazon ECS and needs to ensure that each task is placed on a different container instance.

How can this be achieved?

- ▫️ Create a cluster with multiple container instances
- ▫️ Create a service on Fargate
- ✅ Use a task placement constraint
- ▫️ Use a task placement strategy

<details><summary>Пояснення (оригінал, EN)</summary>

A task placement constraint is a rule that is considered during task placement. Task placement constraints can be specified when either running a task or creating a new service.

Amazon ECS supports the following types of task placement constraints:

distinctInstance

Place each task on a different container instance. This task placement constraint can be specified when either running a task or creating a new service.

memberOf

Place tasks on container instances that satisfy an expression. For more information about the expression syntax for constraints, see Cluster Query Language.

The memberOf task placement constraint can be specified with the following actions:

Running a task

Creating a new service

Creating a new task definition

Creating a new revision of an existing task definition

The following code can be used in a task definition to specify a task placement constraint that ensures that each task will run on a distinct instance:

"placementConstraints": [
{
"type": "distinctInstance"
}
]
CORRECT: "Use a task placement constraint" is the correct answer.

INCORRECT: "Use a task placement strategy" is incorrect as this is used to select instances for task placement using the binpack, random and spread algorithms.

INCORRECT: "Create a service on Fargate" is incorrect as Fargate spreads tasks across AZs but not instances.

INCORRECT: "Create a cluster with multiple container instances" is incorrect as this will not guarantee that each task runs on a different container instance.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t08-q22"></a>
### T08-Q22
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is deploying an application using Docker containers on Amazon ECS. One of the containers runs a database and should be placed on instances in the “databases” task group.

What should the Developer use to control the placement of the database task?

- ▫️ Cluster Query Language
- ▫️ ECS Container Agent
- ▫️ IAM Group
- ✅ Task Placement Constraint

<details><summary>Пояснення (оригінал, EN)</summary>

A task placement constraint is a rule that is considered during task placement. Task placement constraints can be specified when either running a task or creating a new service. The task placement constraints can be updated for existing services as well.

Amazon ECS supports the following types of task placement constraints:

distinctInstance

Place each task on a different container instance. This task placement constraint can be specified when either running a task or creating a new service.

memberOf

Place tasks on container instances that satisfy an expression. For more information about the expression syntax for constraints, see Cluster Query Language.

The memberOf task placement constraint can be specified with the following actions:

• Running a task

• Creating a new service

• Creating a new task definition

• Creating a new revision of an existing task definition

The example task placement constraint below uses the memberOf constraint to place tasks on instances in the databases task group. It can be specified with the following actions: CreateService, UpdateService, RegisterTaskDefinition, and RunTask.

"placementConstraints": [
{
"expression": "task:group == databases",
"type": "memberOf"
}
]
The Developer should therefore use task placement constraints as in the above example to control the placement of the database task.

CORRECT: "Task Placement Constraint" is the correct answer.

INCORRECT: "Cluster Query Language" is incorrect. Cluster queries are expressions that enable you to group objects. For example, you can group container instances by attributes such as Availability Zone, instance type, or custom metadata.

INCORRECT: "IAM Group" is incorrect as you cannot control task placement on ECS with IAM Groups. IAM groups are used for organizing IAM users and applying policies to them.

INCORRECT: "ECS Container Agent" is incorrect. The Amazon ECS container agent allows container instances to connect to your cluster.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t10-q55"></a>
### T10-Q55
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

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

<details><summary>Пояснення (оригінал, EN)</summary>

A task placement constraint is a rule that is considered during task placement. Task placement constraints can be specified when either running a task or creating a new service.

The memberOf task placement constraint places tasks on container instances that satisfy an expression.

The memberOf task placement constraint can be specified with the following actions:

• Running a task

• Creating a new service

• Creating a new task definition

• Creating a new revision of an existing task definition

The example JSON code uses the memberOf constraint to place tasks on instances in the databases task group. It can be specified with the following actions: CreateService, UpdateService, RegisterTaskDefinition, and RunTask.

CORRECT: "They will be placed on container instances in the “databases” task group" is the correct answer.

INCORRECT: "They will become members of a task group called “databases”" is incorrect. They will be placed on container instances in the “databases” task group.

INCORRECT: "They will not be placed on container instances in the “databases” task group" is incorrect. This statement ensures the tasks ARE placed on the container instances in the “databases” task group.

INCORRECT: "They will not be allowed to run unless they have the “databases” tag assigned" is incorrect. This JSON code is not related to tagging of the tasks.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-constraints.html>

</details>

---

<a id="t11-q11"></a>
### T11-Q11
*⏭ пропущено · домен: офіційна мітка*

ECS Fargate container tasks are usually spread across Availability Zones (AZs) and the underlying workloads need persistent cross-AZ shared access to the data volumes configured for the container tasks.

Which of the following solutions is the best choice for these workloads?

- ✅ Amazon EFS volumes
- ▫️ Docker volumes
- ▫️ AWS Gateway Storage volumes
- ▫️ Bind mounts

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon EFS volumes - EFS volumes provide a simple, scalable, and persistent file storage for use with your Amazon ECS tasks. With Amazon EFS, storage capacity is elastic, growing and shrinking automatically as you add and remove files. Your applications can have the storage they need, when they need it. Amazon EFS volumes are supported for tasks hosted on Fargate or Amazon EC2 instances.

You can use Amazon EFS file systems with Amazon ECS to export file system data across your fleet of container instances. That way, your tasks have access to the same persistent storage, no matter the instance on which they land. However, you must configure your container instance AMI to mount the Amazon EFS file system before the Docker daemon starts. Also, your task definitions must reference volume mounts on the container instance to use the file system.

Incorrect options:

Docker volumes - A Docker-managed volume that is created under /var/lib/docker/volumes on the host Amazon EC2 instance. Docker volume drivers (also referred to as plugins) are used to integrate the volumes with external storage systems, such as Amazon EBS. The built-in local volume driver or a third-party volume driver can be used. Docker volumes are only supported when running tasks on Amazon EC2 instances.

Bind mounts - A file or directory on the host, such as an Amazon EC2 instance or AWS Fargate, is mounted into a container. Bind mount host volumes are supported for tasks hosted on Fargate or Amazon EC2 instances. Bind mounts provide temporary storage, and hence these are a wrong choice for this use case.

AWS Storage Gateway volumes - This is an incorrect choice, given only as a distractor.

Reference:

https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_data_volumes.html

https://aws.amazon.com/blogs/containers/amazon-ecs-availability-best-practices/

</details>

---

<a id="t12-q16"></a>
### T12-Q16
*⏭ пропущено · домен: офіційна мітка*

An application runs on Amazon Elastic Container Service (Amazon ECS) on AWS Fargate. The company's audit requirements mandate that logging and storing of application log data must be done centrally on AWS.

How will you configure this requirement?

- ▫️ Amazon ECS metric data is automatically sent to CloudWatch in 1-minute periods. Amazon ECS service using the Fargate launch type has CloudWatch CPU and memory utilization metrics that can be enabled from the ECS console
- ✅ Use the awslogs log driver to configure the containers in your tasks to send log information to CloudWatch Logs. Add the required logConfiguration parameters to your task definition
- ▫️ Use the awslogs log driver to send log information to CloudWatch Logs. To turn on the awslogs log driver, your Amazon ECS container instances require at least version 1.9.0 of the container agent
- ▫️ Download and install the unified CloudWatch agent on the ECS instances to collect internal system-level metrics and application logs from the instances. The logs collected by the unified CloudWatch agent are processed and stored in Amazon CloudWatch logs and can be queried for report generation

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the awslogs log driver to configure the containers in your tasks to send log information to CloudWatch Logs. Add the required logConfiguration parameters to your task definition

Using the awslogs log driver you can configure the containers in your tasks to send log information to CloudWatch Logs. If you're using the Fargate launch type for your tasks, you need to add the required logConfiguration parameters to your task definition to turn on the awslogs log driver.

Before your containers can send logs to CloudWatch, you must specify the awslogs log driver for containers in your task definition. The example task definition JSON that follows has a logConfiguration object specified for each container. One is for the WordPress container that sends logs to a log group called awslogs-wordpress. The other is for a MySQL container that sends logs to a log group that's called awslogs-mysql. Both containers use the awslogs-example log stream. prefix.

Specifying a log configuration in your task definition:  via - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html#specify-log-config

Incorrect options:

Use the awslogs log driver to send log information to CloudWatch Logs. To turn on the awslogs log driver, your Amazon ECS container instances require at least version 1.9.0 of the container agent - This statement is incorrect. If you're using the EC2 launch type (and not Fargate) for your tasks and want to turn on the awslogs log driver, your Amazon ECS container instances require at least version 1.9.0 of the container agent.

Amazon ECS metric data is automatically sent to CloudWatch in 1-minute periods. Amazon ECS service using the Fargate launch type has CloudWatch CPU and memory utilization metrics that can be enabled from the ECS console - Indeed, Amazon ECS metric data is automatically sent to CloudWatch in 1-minute periods. Also, any Amazon ECS service using the Fargate launch type has CloudWatch CPU and memory utilization metrics automatically, so you don't need to take any manual steps. But, these are system logs and not the application logs as is needed in the current use case.

Download and install the unified CloudWatch agent on the ECS instances to collect internal system-level metrics and application logs from the instances. The logs collected by the unified CloudWatch agent are processed and stored in Amazon CloudWatch logs and can be queried for report generation - This statement is incorrect and given only as a distractor. ECS Fargate is serverless and hence the scope of downloading and installing software on the instance does not arise.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html> · <https://docs.aws.amazon.com/AmazonECS/latest/userguide/cloudwatch-metrics.html#available_cloudwatch_metrics>

</details>

---
