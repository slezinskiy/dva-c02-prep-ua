# ECS / ECR / Fargate

**Домен 4 — Усунення несправностей та оптимізація** · питань: 4  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q11"></a>
### T01-Q11
*домен: офіційна мітка*

> ⚠️ **X-Ray на Fargate → sidecar.** → [детальніше](../../06-contradictions-and-traps.md#c19)

You would like to run the X-Ray daemon for your Docker containers deployed using AWS Fargate.

What do you need to do to ensure the setup will work? (Select two)

- ▫️ Deploy the X-Ray daemon agent as a process on your EC2 instance
- ✅ Provide the correct IAM task role to the X-Ray container
- ▫️ Deploy the X-Ray daemon agent as a daemon set on ECS
- ✅ Deploy the X-Ray daemon agent as a sidecar container
- ▫️ Provide the correct IAM instance role to the EC2 instance

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Deploy the X-Ray daemon agent as a sidecar container» та «Provide the correct IAM task role to the X-Ray container»**

В Amazon ECS створюють Docker-образ із X-Ray daemon, завантажують його в репозиторій образів і запускають у кластері. Port mappings і network mode в task definition дають застосунку змогу спілкуватися з контейнером daemon.

На AWS Fargate немає контролю над EC2-інстансами під капотом, тому не можна поставити агента на інстанс чи запустити його як daemon set (це можливо лише в ECS на EC2). Лишається варіант sidecar-контейнера в тому ж task definition.

У Fargate IAM-ролі можна надати лише задачам (task role). Це також найкраща практика безпеки й для EC2.

**❌ Неправильні варіанти:**
- *Deploy as a daemon set on ECS* — на Fargate немає контролю над EC2-інстансами.
- *Deploy as a process on your EC2 instance* та *Provide the correct IAM instance role to the EC2 instance* — з тієї ж причини: на Fargate немає доступу до інстансів.

</details>

---

<a id="t02-q13"></a>
### T02-Q13
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Development team wants to run their container workloads on Amazon ECS. Each application container needs to share data with another container to collect logs and metrics.

What should the Development team do to meet these requirements?

- ▫️ Create a single pod specification. Include both containers in the specification. Mount a persistent volume to both containers
- ✅ Create one task definition. Specify both containers in the definition. Mount a shared volume between those two containers
- ▫️ Create two pod specifications. Make one to include the application container and the other to include the other container. Link the two pods together
- ▫️ Create two task definitions. Make one to include the application container and the other to include the other container. Mount a shared volume between the two tasks

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create one task definition. Specify both containers in the definition. Mount a shared volume between those two containers»**

Задачі ECS підтримують volumes: у task definition описують volume (у секції `volumes`, з ім'ям і конфігурацією) і точки монтування (`mountPoints` у `containerDefinitions` з `containerPath`). Обидва контейнери мають бути в одному task definition і монтувати спільний volume. (У банку сказано, що Docker volumes підтримує лише EC2 launch type. Це так, але на Fargate для того ж є bind mounts та EFS.)

**❌ Неправильні варіанти:**
- *Two pod specifications…* та *Single pod specification…* — pods — поняття Kubernetes (EKS), а не ECS.
- *Two task definitions … shared volume between the two tasks* — обидва контейнери мають бути в одному task definition.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-volumes.html>

</details>

---

<a id="t07-q37"></a>
### T07-Q37
*домен: офіційна мітка*

A junior developer working on ECS instances terminated a container instance in Amazon Elastic Container Service (Amazon ECS) as per instructions from the team lead. But the container instance continues to appear as a resource in the ECS cluster.

As a Developer Associate, which of the following solutions would you recommend to fix this behavior?

- ▫️ You terminated the container instance while it was in RUNNING state, that lead to this synchronization issues
- ✅ You terminated the container instance while it was in STOPPED state, that lead to this synchronization issues
- ▫️ A custom software on the container instance could have failed and resulted in the container hanging in an unhealthy state till restarted again
- ▫️ The container instance has been terminated with AWS CLI, whereas, for ECS instances, Amazon ECS CLI should be used to avoid any synchronization issues

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «You terminated the container instance while it was in STOPPED state, that lead to this synchronization issues»**

Якщо термінувати container instance у стані STOPPED, він автоматично не видаляється з кластера. Його треба дереєструвати вручну через консоль ECS або CLI, і тоді він зникне з ресурсів кластера.

**❌ Неправильні варіанти:**
- *Terminated in RUNNING state* — інстанс у стані RUNNING при термінуванні дереєструється автоматично.
- *AWS CLI vs ECS CLI* — до проблеми не стосується.
- *Custom software failed* — за умовою інстанс уже термінували.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/deregister-ecs-instance/> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_instances.html>

</details>

---

<a id="t10-q34"></a>
### T10-Q34
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company is running a Docker application on Amazon ECS. The application must scale based on user load in the last 15 seconds.

How should the Developer instrument the code so that the requirement can be met?

- ✅ Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds
- ▫️ Create a standard-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds
- ▫️ Create a standard-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 30 seconds
- ▫️ Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 30 seconds

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a high-resolution custom Amazon CloudWatch metric for user activity data, then publish data every 5 seconds»**

Метрики сервісів AWS мають стандартну роздільність. Custom metric можна зробити standard (1 хвилина) або high resolution (1 секунда); high-resolution читають з періодом 1, 5, 10, 30 с або кратним 60 с. Активність користувачів — не стандартна метрика, тож потрібна власна high-resolution метрика з публікацією кожні 5 секунд.

**❌ Неправильні варіанти:**
- *High-resolution, every 30 s* — гранулярність нижча за потрібну.
- *Standard, every 30 s* — standard має гранулярність 1 хвилину.
- *Standard, every 5 s* — standard однаково агрегується до 1 хвилини.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html>

</details>

---
