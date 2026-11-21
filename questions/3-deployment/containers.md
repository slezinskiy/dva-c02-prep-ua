# ECS / ECR / Fargate

**Домен 3 — Розгортання** · питань: 16  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q17"></a>
### T01-Q17
*домен: офіційна мітка*

Your company is shifting towards Elastic Container Service (ECS) to deploy applications. The process should be automated using the AWS CLI to create a service where at least ten instances of a task definition are kept running under the default cluster.

Which of the following commands should be executed?

- ✅ aws ecs create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10
- ▫️ docker-compose create ecs-simple-service
- ▫️ aws ecs run-task --cluster default --task-definition ecs-demo
- ▫️ aws ecr create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «aws ecs create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10»**

Команда створює в регіоні за замовчуванням сервіс `ecs-simple-service`, який використовує task definition `ecs-demo` і постійно підтримує 10 запущених екземплярів задачі.

**❌ Неправильні варіанти:**
- *aws ecr create-service …* — `ecr` стосується Elastic Container Registry (реєстру образів), а не ECS.
- *docker-compose create ecs-simple-service* — команда Docker для створення контейнерів сервісу.
- *aws ecs run-task --cluster default --task-definition ecs-demo* — валідна команда, але вона запускає разову задачу, а не сервіс, що підтримує задану кількість задач.

</details>

---

<a id="t01-q65"></a>
### T01-Q65
*домен: офіційна мітка*

> ⚠️ **Застаріле: Multicontainer Docker (AL1) знято → ECS-платформа EB.** → [детальніше](../../07-outdated-and-wrong-answers.md#o14)

Your client wants to deploy a service on EC2 instances, and as EC2 instances are added into an ASG, each EC2 instance should be running 3 different Docker Containers simultaneously.

What Elastic Beanstalk platform should they choose?

- ▫️ Third-party platform
- ▫️ Docker single-container platform
- ▫️ Custom platform
- ✅ Docker multi-container platform

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Docker multi-container platform»** *(застаріла платформа EB; зараз — ECS-платформа)*

Docker дає змогу описати програмний стек у образі, який завантажується з репозиторію. Платформу Multicontainer Docker використовують, коли на кожному інстансі має працювати кілька контейнерів. Proxy-сервера вона не містить, а розгортання контейнерів EB координує через Amazon ECS.

**❌ Неправильні варіанти:**
- *Docker single-container platform* — лише один контейнер на інстанс (з proxy Nginx).
- *Custom Platform* — повністю власна платформа (ОС, ПЗ, скрипти) для мов чи інфраструктури, яких EB не підтримує. Це складніше, ніж custom image (зміна AMI для наявної платформи), і для задачі не потрібно.
- *Third Party Platform* — вигадано.

</details>

---

<a id="t02-q11"></a>
### T02-Q11
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is setting up a code update to Amazon ECS using AWS CodeDeploy. The Developer needs to complete the code update quickly. Which of the following deployment types should the Developer use?

- ▫️ Linear
- ▫️ In-place
- ✅ Blue/green
- ▫️ Canary

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Blue/green»**

CodeDeploy має два типи деплою: in-place і blue/green. Для Lambda та ECS in-place недоступний. Blue/green на ECS працює так:
- трафік переводиться з task set зі старою версією на replacement task set у тому ж сервісі;
- перемикання може бути linear або canary (через deployment configuration);
- продакшн-трафік перенаправляється через протокол і порт listener'а балансера;
- поки йдуть валідаційні тести, test listener може направляти на новий task set тестовий трафік.

**❌ Неправильні варіанти:**
- *Canary* і *Linear* — це способи перемикання трафіку, а не типи деплою.
- *In-place* — недоступний для ECS і Lambda.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html>

</details>

---

<a id="t02-q51"></a>
### T02-Q51
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A developer is using AWS CodeBuild to build an application into a Docker image. The buildspec file is used to run the application build. The developer needs to push the Docker image to an Amazon ECR repository only upon the successful completion of each build.

- ▫️ Add a post_build phase to the buildspec file that uses the finally block to push the Docker image.
- ✅ Add a post_build phase to the buildspec file that uses the commands block to push the Docker image.
- ▫️ Add a post_build phase to the buildspec file that uses the artifacts sequence to find the build artifacts and push to Amazon ECR.
- ▫️ Add an install phase to the buildspec file that uses the commands block to push the Docker image.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add a post_build phase to the buildspec file that uses the commands block to push the Docker image»**

`post_build` — опційна фаза з командами, які CodeBuild виконує після збірки: наприклад, запакувати артефакти через Maven у JAR/WAR, запушити Docker-образ в ECR, надіслати сповіщення через SNS. Блок `commands` у `post_build` виконується лише тоді, коли збірка успішна.

**❌ Неправильні варіанти:**
- *post_build with the finally block* — команди `finally` виконуються навіть після помилки в `commands`, тож образ потрапив би в ECR і після невдалої збірки.
- *install phase with the commands block* — це фаза встановлення залежностей; пушити треба після того, як усе успішно зібрано.
- *post_build with the artifacts sequence* — `artifacts` не потрібні, якщо ти будуєш і пушиш Docker-образ в ECR.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/sample-docker.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html>

</details>

---

<a id="t04-q13"></a>
### T04-Q13
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **ECR: get-login-password.** → [детальніше](../../06-contradictions-and-traps.md#c16)

A developer has created a Docker image and uploaded it to an Amazon Elastic Container Registry (ECR) repository. How can the developer pull the image to his workstation using the docker client?

- ▫️ Run the docker pull command specifying the image name using registry/repository[:tag]
- ✅ Run aws ecr get-login-password use the output to login in then issue a docker pull command specifying the image name using registry/repository[:tag]
- ▫️ Run docker login with an IAM key pair then issue a docker pull command specifying the image name using registry/repository[@digest]
- ▫️ Run aws ecr describe-images --repository-name repositoryname

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Run aws ecr get-login-password, use the output to login, then issue a docker pull command specifying the image name using registry/repository[:tag]»**

Щоб запустити образ з ECR локально, його витягують `docker pull`. Docker CLI не підтримує стандартну автентифікацію AWS, тож спершу отримують токен (`aws ecr get-login-password` у CLI v2; старе `get-login` — CLI v1), виконують `docker login`, а потім `docker pull registry/repository[:tag]`.

**❌ Неправильні варіанти:**
- *docker pull без логіну* — спершу треба автентифікуватися.
- *aws ecr describe-images* — лише перелічує образи.
- *docker login with an IAM key pair* — IAM-ключами в `docker login` не входять.

Джерела: <https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-pull-ecr-image.html>

</details>

---

<a id="t05-q24"></a>
### T05-Q24
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **ECR: get-login-password.** → [детальніше](../../06-contradictions-and-traps.md#c16)

AWS CodeBuild builds code for an application, creates a Docker image, pushes the image to Amazon Elastic Container Registry (ECR), and tags the image with a unique identifier.

If the Developers already have AWS CLI configured on their workstations, how can the Docker images be pulled to the workstations?

- ▫️ Run the output of the following: aws ecr get-download-url-for-layer, and then run: docker pull REPOSITORY URI : TAG
- ✅ Run the output of the following: aws ecr get-login-password, and then run: docker pull REPOSITORY URI : TAG
- ▫️ Run the following: aws ecr get-login-password, and then run: docker pull REPOSITORY URI : TAG
- ▫️ Run the following: docker pull REPOSITORY URI : TAG

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Run the output of the following: aws ecr get-login-password, and then run: docker pull REPOSITORY URI : TAG»**

Образ з ECR, зі свого реєстру або з реєстру іншого акаунта, тягнуть командою `docker pull`. Docker CLI не підтримує стандартну автентифікацію AWS, тож клієнт має спочатку автентифікуватися:
1. Отримати токен командою `aws ecr get-login-password`.
2. Передати його в `docker login`.
3. Виконати `docker pull registry/repository[:tag]`.

Типова команда: `aws ecr get-login-password | docker login --username AWS --password-stdin <registry>`.

**❌ Неправильні варіанти:**
- *Just docker pull* — без автентифікації pull не спрацює.
- *Run aws ecr get-login-password, then docker pull* — мало виконати команду: її вивід, тобто токен, треба використати для входу.
- *get-download-url-for-layer* — ця команда повертає pre-signed URL для окремого шару образу.

Джерела: <https://docs.aws.amazon.com/AmazonECR/latest/userguide/Registries.html#registry_auth> · <https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-pull-ecr-image.html>

</details>

---

<a id="t05-q56"></a>
### T05-Q56
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A developer must deploy an update to Amazon ECS using AWS CodeDeploy. The deployment should expose 10% of live traffic to the new version. Then after a period of time, route all remaining traffic to the new version.

Which ECS deployment should the company use to meet these requirements?

- ✅ Blue/green with canary
- ▫️ Rolling update
- ▫️ Blue/green with all at once
- ▫️ Blue/green with linear

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Blue/green with canary»**

Blue/green-деплой ECS, яким керує CodeDeploy, дозволяє перевірити нову версію сервісу, перш ніж пустити на неї продакшн-трафік. Трафік перемикається одним із трьох способів:
- **Canary** — дві порції: спочатку заданий відсоток, потім через N хвилин решта. Наприклад, `ECSCanary10Percent5Minutes`.
- **Linear** — рівні порції через однакові інтервали.
- **All-at-once** — увесь трафік одразу.

Вимога «спочатку 10%, потім решта» — це canary.

**❌ Неправильні варіанти:**
- *Blue/green with linear* — трафік іде рівними частинами через рівні інтервали.
- *Blue/green with all at once* — увесь трафік одразу.
- *Rolling update* — нативний деплой ECS; не ділить трафік на дві порції з 10% спочатку.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-bluegreen.html>

</details>

---

<a id="t06-q65"></a>
### T06-Q65
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **X-Ray на ECS EC2 → daemon-контейнер.** → [детальніше](../../06-contradictions-and-traps.md#c19)

A Developer is deploying an application using Docker containers running on the Amazon Elastic Container Service (ECS). The Developer is testing application latency and wants to capture trace information between the microservices.

Which solution will meet these requirements?

- ▫️ Install the AWS X-Ray daemon on each of the Amazon ECS instances.
- ▫️ Install the AWS X-Ray daemon locally on an Amazon EC2 instance and instrument the Amazon ECS microservices using the X-Ray SDK.
- ▫️ Install the Amazon CloudWatch agent on the container image. Use the CloudWatch SDK to publish custom metrics from each of the microservices.
- ✅ Create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to the Amazon ECS cluster.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to the Amazon ECS cluster.»**

В ECS створюють Docker-образ з X-Ray daemon, завантажують його в реєстр (наприклад, ECR) і деплоять у кластер ECS. Port mappings і network mode у task definition дозволяють застосунку звертатися до контейнера з daemon (UDP 2000).

⚠️ В оригіналі сказано, що daemon «має бути в контейнерах, а не на хостах ECS». Це спрощення: на ECS з EC2 його часто запускають як daemon service, по одному на інстанс, а на Fargate — як sidecar. Крім того, підтримка X-Ray daemon і SDK завершується 25.02.2027; заміна — ADOT або CloudWatch agent. Див. 06 і 07.

**❌ Неправильні варіанти:**
- *CloudWatch agent + custom metrics* — метрики не дають трасування запитів між контейнерами.
- *Install daemon on each ECS instance* — за логікою питання daemon запускають як контейнер, а не встановлюють на хост.
- *Daemon on a separate EC2* — з окремого EC2-інстансу мікросервіси в Docker не трасують.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-ecs.html>

</details>

---

<a id="t08-q39"></a>
### T08-Q39
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team is creating a microservices application running on Amazon ECS. The release process workflow of the application requires a manual approval step before the code is deployed into the production environment.
What is the BEST way to achieve this using AWS CodePipeline?

- ▫️ Use an Amazon SNS notification from the deployment stage
- ✅ Use an approval action in a stage before deployment
- ▫️ Disable the stage transition to allow manual approval
- ▫️ Disable a stage just prior the deployment stage

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an approval action in a stage before deployment»**

Approval action зупиняє пайплайн, доки користувач з потрібними правами IAM не схвалить чи не відхилить продовження. Якщо схвалено, виконання продовжується. Якщо відхилено або ніхто не відповів за **7 днів**, це рівнозначно збою дії, і пайплайн зупиняється. Тут approval ставлять перед stage, що деплоїть у продакшн.

**❌ Неправильні варіанти:**
- *SNS from the deployment stage* — сповіщення прийде, коли деплой уже йде.
- *Disable stage transition* — ручне втручання, яке легко пропустити.
- *Disable a stage before deployment* — цей stage, наприклад build чи test, тоді взагалі не виконається.

Джерела: <https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals.html>

</details>

---

<a id="t09-q24"></a>
### T09-Q24
*домен: офіційна мітка*

A developer wants to package the code and dependencies for the application-specific Lambda functions as container images to be hosted on Amazon Elastic Container Registry (ECR).

Which of the following options are correct for the given requirement? (Select two)

- ▫️ You can deploy Lambda function as a container image, with a maximum size of 15 GB
- ✅ AWS Lambda service does not support Lambda functions that use multi-architecture container images
- ▫️ You can test the containers locally using the Lambda Runtime API
- ✅ To deploy a container image to Lambda, the container image must implement the Lambda Runtime API
- ▫️ Lambda supports both Windows and Linux-based container images

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Lambda service does not support Lambda functions that use multi-architecture container images» + «To deploy a container image to Lambda, the container image must implement the Lambda Runtime API»**

- **Lambda Runtime API.** Контейнерний образ для Lambda має реалізовувати Runtime API. Це роблять open-source runtime interface clients, які можна додати до будь-якого базового образу.
- **Одна архітектура.** Lambda має мультиархітектурні базові образи, але образ функції має бути зібраний лише під одну архітектуру: x86_64 або arm64.

**❌ Неправильні варіанти:**
- *Windows and Linux images* — підтримуються лише Linux-образи.
- *Test locally with the Runtime API* — локально тестують через Lambda Runtime Interface Emulator (RIE).
- *Max size 15 GB* — максимум 10 GB.

</details>

---

<a id="t09-q26"></a>
### T09-Q26
*домен: офіційна мітка*

Your company has embraced cloud-native microservices architectures. New applications must be dockerized and stored in a registry service offered by AWS. The architecture should support dynamic port mapping and support multiple tasks from a single service on the same container instance. All services should run on the same EC2 instance.

Which of the following options offers the best-fit solution for the given use-case?

- ▫️ Classic Load Balancer + ECS
- ✅ Application Load Balancer + ECS
- ▫️ Application Load Balancer + Beanstalk
- ▫️ Classic Load Balancer + Beanstalk

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Application Load Balancer + ECS»**

ECS запускає Docker-контейнери на serverless-інфраструктурі (Fargate) або на керованих вами EC2. ALB розподіляє трафік між цілями в кількох AZ. Listener приймає з'єднання на заданому протоколі й порту, а правила listener (пріоритет, умови, дії) визначають, куди йде запит.

З ALB в ECS працює **dynamic port mapping**: кілька задач одного сервісу на одному інстансі. ECS сам реєструє й дереєструє контейнери в target group за instance ID і портом.

**❌ Неправильні варіанти:**
- *CLB + Beanstalk* — CLB не підтримує dynamic port mapping, тож порти прив'язуються статично.
- *ALB + Beanstalk* — Multi-container Docker у Beanstalk можливий, але ECS дає точніший контроль.
- *CLB + ECS* — CLB не підтримує dynamic port mapping.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/dynamic-port-mapping-ecs> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/tutorial-target-ecs-containers.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html>

</details>

---

<a id="t10-q06"></a>
### T10-Q06
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is migrating Docker containers to Amazon ECS. A large number of containers will be deployed across some newly deployed ECS containers instances using the same instance type. High availability is provided within the microservices architecture. Which task placement strategy requires the LEAST configuration for this scenario?

- ▫️ spread
- ▫️ Fargate
- ✅ random
- ▫️ binpack

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «random»**

Для задач на EC2 ECS вирішує, де їх розмістити з урахуванням вимог до CPU і пам'яті, а при зменшенні кількості — які задачі завершити. На це впливають placement strategies і constraints. На Fargate вони не підтримуються: задачі там розподіляються по AZ за замовчуванням.

Стратегії:
- **binpack** — щільно, за найменшим вільним CPU чи пам'яттю, щоб використати мінімум інстансів;
- **random** — випадково;
- **spread** — рівномірно за `instanceId` (або `host`) чи атрибутом, наприклад `attribute:ecs.availability-zone`.

Високу доступність забезпечують самі контейнери, тож random підходить найкраще: він потребує найменше налаштувань.

**❌ Неправильні варіанти:**
- *spread* — HA вже забезпечено, рівномірний розподіл не потрібен.
- *binpack* — не потрібно пакувати задачі на мінімум інстансів.
- *Fargate* — це serverless-сервіс для контейнерів, а не стратегія розміщення.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t12-q03"></a>
### T12-Q03
*домен: офіційна мітка*

The development team at an e-commerce company wants to run a serverless data store service on two docker containers that share resources.

Which of the following ECS configurations can be used to facilitate this use-case?

- ✅ Put the two containers into a single task definition using a Fargate Launch Type
- ▫️ Put the two containers into two separate task definitions using a Fargate Launch Type
- ▫️ Put the two containers into two separate task definitions using an EC2 Launch Type
- ▫️ Put the two containers into a single task definition using an EC2 Launch Type

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Put the two containers into a single task definition using a Fargate Launch Type»**

ECS запускає Docker-контейнери на serverless-інфраструктурі (Fargate) або на керованих вами EC2. Потрібне serverless-рішення, тож Fargate. Контейнери, які мають ділити ресурси (мережу, томи) і працювати разом, кладуть в одну task definition.

Як ECS керує CPU і пам'яттю: https://aws.amazon.com/blogs/containers/how-amazon-ecs-manages-cpu-and-memory-resources/

**❌ Неправильні варіанти:**
- *Two task definitions on Fargate* — контейнери не зможуть ділити ресурси.
- *Two task definitions on EC2* — EC2 не serverless.
- *Single task definition on EC2* — EC2 не serverless.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/application_architecture.html> · <https://aws.amazon.com/blogs/containers/how-amazon-ecs-manages-cpu-and-memory-resources/>

</details>

---

<a id="t12-q24"></a>
### T12-Q24
*домен: офіційна мітка*

> ⚠️ **Застаріле: aws ecr get-login (CLI v1). Зараз get-login-password.** → [детальніше](../../07-outdated-and-wrong-answers.md#o13)

You are working for a technology startup building web and mobile applications. You would like to pull Docker images from the ECR repository called demo so you can start running local tests against the latest application version.

Which of the following commands must you run to pull existing Docker images from ECR? (Select two)

- ✅ docker pull 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest
- ▫️ docker build -t 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest
- ✅ $(aws ecr get-login --no-include-email)
- ▫️ docker login -u $AWS_ACCESS_KEY_ID -p $AWS_SECRET_ACCESS_KEY
- ▫️ aws docker push 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «docker pull 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest» + «$(aws ecr get-login --no-include-email)»**

`get-login` отримує токен для реєстру, дійсний 12 годин, і друкує готову команду `docker login`. Обгортка `$()` одразу її виконує. Після входу можна робити push і pull, доки токен не спливе. `docker pull` завантажує образ.

⚠️ `aws ecr get-login` прибрано в AWS CLI v2. Актуальний спосіб:
`aws ecr get-login-password | docker login --username AWS --password-stdin <registry>`. Див. 07.

**❌ Неправильні варіанти:**
- *docker login with access keys* — access keys використовує лише CLI; для ECR потрібен токен.
- *aws docker push* — такої команди немає, і потрібен pull, а не push.
- *docker build* — збирає образ з Dockerfile, а не завантажує його.

</details>

---

<a id="t12-q29"></a>
### T12-Q29
*домен: офіційна мітка*

Your e-commerce company needs to improve its software delivery process and is moving away from the waterfall methodology. You decided that every application should be built using the best CI/CD practices and every application should be packaged and deployed as a Docker container. The Docker images should be stored in ECR and pushed with AWS CodePipeline and AWS CodeBuild.

When you attempt to do this, the last step fails with an authorization issue. What is the most likely issue?

- ▫️ CodeBuild cannot talk to ECR because of security group issues
- ▫️ The ECR repository is stale, you must delete and re-create it
- ▫️ The ECS instances are misconfigured and must contain additional data in /etc/ecs/ecs.config
- ✅ The IAM permissions are wrong for the CodeBuild service

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The IAM permissions are wrong for the CodeBuild service»**

Образи Docker або OCI завантажують в ECR командою `docker push`. Перш ніж автентифікуватися в реєстрі, той, хто звертається, має отримати дозвіл `ecr:GetAuthorizationToken`. Для push потрібні ще `ecr:BatchCheckLayerAvailability`, `ecr:InitiateLayerUpload`, `ecr:UploadLayerPart`, `ecr:CompleteLayerUpload` і `ecr:PutImage`. Помилка авторизації означає, що цих прав бракує ролі CodeBuild. ECR має managed-політики для різних рівнів доступу.

**❌ Неправильні варіанти:**
- *Stale repository* — такого поняття в ECR немає.
- *Security group issues* — помилка авторизації, а не мережева.
- */etc/ecs/ecs.config* — помилка стосується доступу, а не конфігурації ECS.

Джерела: <https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html> · <https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr_managed_policies.html>

</details>

---

<a id="t12-q46"></a>
### T12-Q46
*домен: офіційна мітка*

A company wants to automate the creation of ECS clusters using CloudFormation. The process has worked for a while, but after creating task definitions and assigning roles, the development team discovers that the tasks for containers are not using the permissions assigned to them.

Which ECS config must be set in /etc/ecs/ecs.config to allow ECS tasks to use IAM roles?

- ▫️ ECS_CLUSTER
- ▫️ ECS_ENGINE_AUTH_DATA
- ▫️ ECS_AVAILABLE_LOGGING_DRIVERS
- ✅ ECS_ENABLE_TASK_IAM_ROLE

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «ECS_ENABLE_TASK_IAM_ROLE»**

Параметр `ECS_ENABLE_TASK_IAM_ROLE=true` в `/etc/ecs/ecs.config` вмикає IAM-ролі для задач (task roles) у контейнерах з мережевими режимами `bridge` і `default`.

**❌ Неправильні варіанти:**
- *ECS_ENGINE_AUTH_DATA* — дані автентифікації для приватних Docker-реєстрів.
- *ECS_AVAILABLE_LOGGING_DRIVERS* — драйвери логування, доступні на інстансі.
- *ECS_CLUSTER* — кластер, до якого реєструється агент; задається через User Data.

</details>

---
