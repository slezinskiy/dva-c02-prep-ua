# ECS / ECR / Fargate

**Домен 4 — Усунення несправностей та оптимізація** · питань: 4  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q11"></a>
### T01-Q11
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **X-Ray на Fargate → sidecar.** → [детальніше](../../06-contradictions-and-traps.md#c19)

You would like to run the X-Ray daemon for your Docker containers deployed using AWS Fargate.

What do you need to do to ensure the setup will work? (Select two)

- ▫️ Deploy the X-Ray daemon agent as a process on your EC2 instance
- ✅ Provide the correct IAM task role to the X-Ray container
- ▫️ Deploy the X-Ray daemon agent as a daemon set on ECS
- ✅ Deploy the X-Ray daemon agent as a sidecar container
- ▫️ Provide the correct IAM instance role to the EC2 instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Deploy the X-Ray daemon agent as a sidecar container

In Amazon ECS, create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to your Amazon ECS cluster. You can use port mappings and network mode settings in your task definition file to allow your application to communicate with the daemon container.

As we are using AWS Fargate, we do not have control over the underlying EC2 instance and thus we can't deploy the agent on the EC2 instance or run an X-Ray agent container as a daemon set (only available for ECS classic).

 via - https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-ecs.html

Provide the correct IAM task role to the X-Ray container

For Fargate, we can only provide IAM roles to tasks, which is also the best security practice should we use EC2 instances.

Incorrect options:

Deploy the X-Ray daemon agent as a daemon set on ECS - As explained above, since we are using AWS Fargate, we do not have control over the underlying EC2 instance and thus we can't run an X-Ray agent container as a daemon set.

Deploy the X-Ray daemon agent as a process on your EC2 instance

Provide the correct IAM instance role to the EC2 instance

As we are using AWS Fargate, we do not have control over the underlying EC2 instance, so both these options are incorrect.

Reference:

https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-ecs.html

</details>

---

<a id="t02-q13"></a>
### T02-Q13
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A Development team wants to run their container workloads on Amazon ECS. Each application container needs to share data with another container to collect logs and metrics.

What should the Development team do to meet these requirements?

- ▫️ Create a single pod specification. Include both containers in the specification. Mount a persistent volume to both containers
- ✅ Create one task definition. Specify both containers in the definition. Mount a shared volume between those two containers
- ▫️ Create two pod specifications. Make one to include the application container and the other to include the other container. Link the two pods together
- ❌ Create two task definitions. Make one to include the application container and the other to include the other container. Mount a shared volume between the two tasks

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon ECS tasks support Docker volumes. To use data volumes, you must specify the volume and mount point configurations in your task definition. Docker volumes are supported for the EC2 launch type only.

To configure a Docker volume, in the task definition volumes section, define a data volume with name and DockerVolumeConfiguration values. In the containerDefinitions section, define multiple containers with mountPoints values that reference the name of the defined volume and the containerPath value to mount the volume at on the container.

The containers should both be specified in the same task definition. Therefore, the Development team should create one task definition, specify both containers in the definition and then mount a shared volume between those two containers

CORRECT: "Create one task definition. Specify both containers in the definition. Mount a shared volume between those two containers" is the correct answer.

INCORRECT: "Create two pod specifications. Make one to include the application container and the other to include the other container. Link the two pods together" is incorrect as pods are a concept associated with the Elastic Kubernetes Service (EKS).

INCORRECT: "Create two task definitions. Make one to include the application container and the other to include the other container. Mount a shared volume between the two tasks" is incorrect as a single task definition should be created with both containers.

INCORRECT: "Create a single pod specification. Include both containers in the specification. Mount a persistent volume to both containers" is incorrect as pods are a concept associated with the Elastic Kubernetes Service (EKS).

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-volumes.html>

</details>

---

<a id="t07-q37"></a>
### T07-Q37
*⏭ пропущено · домен: офіційна мітка*

A junior developer working on ECS instances terminated a container instance in Amazon Elastic Container Service (Amazon ECS) as per instructions from the team lead. But the container instance continues to appear as a resource in the ECS cluster.

As a Developer Associate, which of the following solutions would you recommend to fix this behavior?

- ▫️ You terminated the container instance while it was in RUNNING state, that lead to this synchronization issues
- ✅ You terminated the container instance while it was in STOPPED state, that lead to this synchronization issues
- ▫️ A custom software on the container instance could have failed and resulted in the container hanging in an unhealthy state till restarted again
- ▫️ The container instance has been terminated with AWS CLI, whereas, for ECS instances, Amazon ECS CLI should be used to avoid any synchronization issues

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

You terminated the container instance while it was in STOPPED state, that lead to this synchronization issues - If you terminate a container instance while it is in the STOPPED state, that container instance isn't automatically removed from the cluster. You will need to deregister your container instance in the STOPPED state by using the Amazon ECS console or AWS Command Line Interface. Once deregistered, the container instance will no longer appear as a resource in your Amazon ECS cluster.

Incorrect options:

You terminated the container instance while it was in RUNNING state, that lead to this synchronization issues - This is an incorrect statement. If you terminate a container instance in the RUNNING state, that container instance is automatically removed, or deregistered, from the cluster.

The container instance has been terminated with AWS CLI, whereas, for ECS instances, Amazon ECS CLI should be used to avoid any synchronization issues - This is incorrect and has been added as a distractor.

A custom software on the container instance could have failed and resulted in the container hanging in an unhealthy state till restarted again - This is an incorrect statement. It is already mentioned in the question that the developer has terminated the instance.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/deregister-ecs-instance/> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_instances.html>

</details>

---

<a id="t10-q34"></a>
### T10-Q34
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A company is running a Docker application on Amazon ECS. The application must scale based on user load in the last 15 seconds.

How should the Developer instrument the code so that the requirement can be met?

- ✅ Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds
- ▫️ Create a standard-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds
- ▫️ Create a standard-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 30 seconds
- ▫️ Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 30 seconds

<details><summary>Пояснення (оригінал, EN)</summary>

Metrics produced by AWS services are standard resolution by default. When you publish a custom metric, you can define it as either standard resolution or high resolution. When you publish a high-resolution metric, CloudWatch stores it with a resolution of 1 second, and you can read and retrieve it with a period of 1 second, 5 seconds, 10 seconds, 30 seconds, or any multiple of 60 seconds.

User activity is not a standard CloudWatch metric and as stated above for the resolution we need in this scenario a custom CloudWatch metric is required anyway. Therefore, for this scenario the Developer should create a high-resolution custom Amazon CloudWatch metric for user activity data and publish the data every 5 seconds.

CORRECT: "Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds" is the correct answer.

INCORRECT: "Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 30 seconds" is incorrect as the resolution is lower than required which will not provide the granularity required.

INCORRECT: "Create a standard-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 30 seconds" is incorrect as standard resolution metrics have a granularity of one minute.

INCORRECT: "Create a standard-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds" is incorrect as standard resolution metrics have a granularity of one minute.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html>

</details>

---
