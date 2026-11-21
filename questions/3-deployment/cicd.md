# CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify

**Домен 3 — Розгортання** · питань: 44  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#cicd) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q30"></a>
### T01-Q30
*домен: офіційна мітка*

Your team lead has finished creating a CodeBuild project in the management console and a build spec has been defined for the project. After the build is run, CodeBuild fails to pull a Docker image into the build environment.

What is the most likely cause?

- ✅ Missing IAM permissions for the CodeBuild Service
- ▫️ The Docker image is missing some tags
- ▫️ CodeBuild cannot work with custom Docker images
- ▫️ The Docker image is too big

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Missing IAM permissions for the CodeBuild Service»**

За замовчуванням IAM-сутності не мають прав створювати чи змінювати ресурси Amazon ECR або виконувати дії через ECR API. Сервісній ролі CodeBuild потрібні дозволи на отримання образу з реєстру.

**❌ Неправильні варіанти:**
- *The Docker image is missing some tags* — теги опційні й потрібні лише для іменування.
- *CodeBuild cannot work with custom Docker images* — кастомні образи підтримуються.
- *The Docker image is too big* — розмір образу на CodeBuild тут не впливає (хоча оптимізувати образ, наприклад multi-stage build, корисно).

</details>

---

<a id="t01-q47"></a>
### T01-Q47
*домен: офіційна мітка*

> ⚠️ **Застаріле: AWS CodeStar припинено (липень 2024).** → [детальніше](../../07-outdated-and-wrong-answers.md#o03)

You would like to have a one-stop dashboard for all the CI/CD needs of one of your projects. You don't need heavy control of the individual configuration of each component in your CI/CD, but need to be able to get a holistic view of your projects.

Which service do you recommend?

- ▫️ CodeBuild
- ✅ CodeStar
- ▫️ CodePipeline
- ▫️ CodeDeploy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeStar»** *(застаріло: CodeStar припинено у 2024)*

AWS CodeStar давав змогу швидко розробляти, збирати й деплоїти застосунки з єдиного інтерфейсу: весь CI/CD-ланцюжок налаштовувався за хвилини, доступом команди керували в одному місці, а дашборд проєкту з інтеграцією issue tracking (Jira) показував увесь процес — від беклогу до останніх деплоїв.

**❌ Неправильні варіанти:** *CodeBuild*, *CodeDeploy*, *CodePipeline* — окремі сервіси, які CodeStar об'єднував. Кожен з них використовується окремо і не дає єдиного вигляду «проєкту».

</details>

---

<a id="t02-q28"></a>
### T02-Q28
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Хуки Lambda.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A Developer is deploying an AWS Lambda update using AWS CodeDeploy. In the appspec.yaml file, which of the following is a valid structure for the order of hooks that should be specified?

- ▫️ BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService
- ✅ BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «BeforeAllowTraffic > AfterAllowTraffic»**

Вміст секції `hooks` в AppSpec залежить від платформи. Для EC2/on-premises hooks пов'язують події життєвого циклу зі скриптами. Для Lambda й ECS hooks вказують Lambda-функції валідації, які запускаються на певних подіях. Якщо hook не задано, на цій події нічого не виконується. Для Lambda валідна послідовність — `BeforeAllowTraffic → AfterAllowTraffic`.

**❌ Неправильні варіанти:**
- *BeforeInstall > AfterInstall > ApplicationStart > ValidateService* — для EC2.
- *BeforeInstall > AfterInstall > AfterAllowTestTraffic > BeforeAllowTraffic > AfterAllowTraffic* — для ECS.
- *BeforeBlockTraffic > AfterBlockTraffic > BeforeAllowTraffic > AfterAllowTraffic* — неповний перелік hooks для EC2.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t02-q56"></a>
### T02-Q56
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team would use a GitHub repository and would like to migrate their application code to AWS CodeCommit.
What needs to be created before they can migrate a cloned repository to CodeCommit over HTTPS?

- ▫️ An Amazon EC2 IAM role with CodeCommit permissions
- ▫️ A GitHub secure authentication token
- ▫️ A public and private SSH key file
- ✅ A set of Git credentials generated with IAM

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A set of Git credentials generated with IAM»**

AWS CodeCommit — керований сервіс приватних Git-репозиторіїв. Git-клієнт налаштовують на CodeCommit і передають IAM-облікові дані для автентифікації. Підтримуються три типи:
- **Git credentials** — пара логін/пароль, згенерована в IAM, для HTTPS;
- **SSH keys** — локально згенерована пара ключів, публічний ключ прив'язується до IAM-користувача, для SSH;
- **AWS access keys** — з credential helper AWS CLI, для HTTPS.

Для HTTPS підходять або access keys з CLI, або Git credentials з IAM. Access keys серед варіантів немає, тож відповідь — Git credentials.

**❌ Неправильні варіанти:**
- *GitHub secure authentication token* — автентифікуватися треба в CodeCommit, а не GitHub.
- *Public and private SSH key file* — для SSH, а не HTTPS.
- *EC2 IAM role with CodeCommit permissions* — для підключення Git-клієнта потрібні Git credentials з IAM.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html>

</details>

---

<a id="t02-q58"></a>
### T02-Q58
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **ECS: test listener на ALB; валідація до продакшн-трафіку.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A developer is updating an Amazon ECS app that uses an ALB with two target groups and a single listener. The developer has an AppSpec file in an S3 bucket and an AWS CodeDeploy deployment group tied to the ALB and AppSpec file. The developer needs to use an AWS Lambda function for update validation before deployment.

Which solution meets these requirements?

- ▫️ Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the AfterAllowTraffic lifecycle hook.
- ▫️ Attach a listener to the deployment group. Update the AppSpec file to link the Lambda function to the AfterAllowTraffic lifecycle hook.
- ▫️ Attach a listener to the deployment group. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook.
- ✅ Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook»**

AppSpec задає, що запускати на різних lifecycle hooks. Подія `BeforeAllowTraffic` настає до того, як оновлений task set переводять у target group з живим трафіком, тож валідацію перед продакшном налаштовують саме тут. Listener налаштовується на рівні ALB, а не deployment group.

**❌ Неправильні варіанти:**
- *…AfterAllowTraffic* — ця подія вже після перемикання живого трафіку, а валідувати треба до нього.
- *Attach a listener to the deployment group…* (обидва варіанти) — listener не прикріплюють до deployment group, він налаштовується на ALB.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html>

</details>

---

<a id="t03-q02"></a>
### T03-Q02
*домен: офіційна мітка*

> ⚠️ **Хуки EC2: це підмножина повного порядку.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A communication platform serves millions of customers and deploys features in a production environment on AWS via CodeDeploy. You are reviewing scripts for the deployment process located in the AppSpec file.

Which of the following options lists the correct order of lifecycle events?

- ▫️ ValidateService =&gt; BeforeInstall =&gt;DownloadBundle =&gt; ApplicationStart
- ▫️ BeforeInstall =&gt; ApplicationStart =&gt; DownloadBundle =&gt; ValidateService
- ✅ DownloadBundle =&gt; BeforeInstall =&gt; ApplicationStart =&gt; ValidateService
- ▫️ BeforeInstall =&gt; ValidateService =&gt;DownloadBundle =&gt; ApplicationStart

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «DownloadBundle => BeforeInstall => ApplicationStart => ValidateService»**

AWS CodeDeploy — керований сервіс автоматизації деплою на EC2, Fargate, Lambda та on-premises сервери: допомагає швидко випускати нові функції й уникати простою. У hook можна вказати один чи кілька скриптів. Порядок подій (in-place): ApplicationStop → **DownloadBundle** → **BeforeInstall** → Install → AfterInstall → **ApplicationStart** → **ValidateService**. Цей варіант зберігає правильний відносний порядок.

**❌ Неправильні варіанти:** решта трьох варіантів порушують цей порядок (наприклад, DownloadBundle після ApplicationStart або ValidateService на початку).

</details>

---

<a id="t03-q28"></a>
### T03-Q28
*домен: офіційна мітка*

You have been hired at a company that needs an experienced developer to help with a continuous integration/continuous delivery (CI/CD) workflow on AWS. You configure the company’s workflow to run an AWS CodePipeline pipeline whenever the application’s source code changes in a repository hosted in AWS Code Commit and compiles source code with AWS Code Build. You are configuring ProjectArtifacts in your build stage.

Which of the following should you do?

- ✅ Give AWS CodeBuild permissions to upload the build output to your Amazon S3 bucket
- ▫️ Contact AWS Support to allow AWS CodePipeline to manage build outputs
- ▫️ Configure AWS CodeBuild to store output artifacts on EC2 servers
- ▫️ Give AWS CodeCommit permissions to upload the build output to your Amazon S3 bucket

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Give AWS CodeBuild permissions to upload the build output to your Amazon S3 bucket»**

Якщо в ProjectArtifacts вибрано тип S3, build-проєкт зберігає результат в S3, тож CodeBuild потрібні права на завантаження в бакет.

**❌ Неправильні варіанти:**
- *Store output artifacts on EC2 servers* — EC2 не є допустимим місцем для артефактів.
- *Give CodeCommit permissions to upload…* — CodeCommit лише зберігає код, компіляцією не займається.
- *Contact AWS Support to allow CodePipeline to manage outputs* — CodePipeline можна налаштувати керувати артефактами й без звернення в підтримку.

</details>

---

<a id="t03-q59"></a>
### T03-Q59
*домен: офіційна мітка*

An e-commerce company has implemented AWS CodeDeploy as part of its AWS cloud CI/CD strategy. The company has configured automatic rollbacks while deploying a new version of its flagship application to Amazon EC2.

What occurs if the deployment of the new version fails?

- ▫️ AWS CodePipeline promotes the most recent working deployment with a SUCCEEDED status to production
- ✅ A new deployment of the last known working version of the application is deployed with a new deployment ID
- ▫️ The last known working deployment is automatically restored using the snapshot stored in Amazon S3
- ▫️ CodeDeploy switches the Route 53 alias records back to the known good green deployment and terminates the failed blue deployment

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A new deployment of the last known working version of the application is deployed with a new deployment ID»**

CodeDeploy автоматизує деплой на EC2 і on-premises сервери. Rollback він робить, повторно розгортаючи попередню ревізію як **новий деплой з новим deployment ID**, а не відновлюючи старий деплой. CodeDeploy відстежує файли поточної ревізії і видаляє їх перед новим деплоєм, тож різниці між redeploy і rollback немає. Головне — щоб попередні ревізії були доступні.

**❌ Неправильні варіанти:**
- *Restored using the snapshot stored in Amazon S3* — snapshot'ів деплою в S3 немає.
- *CodePipeline promotes the most recent working deployment* — CodePipeline у сценарії не згадується.
- *Switches Route 53 alias records back…* — мова не про blue/green.

</details>

---

<a id="t03-q64"></a>
### T03-Q64
*домен: офіційна мітка*

> ⚠️ **Хуки EC2: це підмножина повного порядку.** → [детальніше](../../06-contradictions-and-traps.md#c08)

What is the run order of the hooks for in-place deployments using CodeDeploy?

- ▫️ Before Install -&gt; Application Stop -&gt; Application Start -&gt; ValidateService
- ✅ Application Stop -&gt; Before Install -&gt; Application Start -&gt; ValidateService
- ▫️ Before Install -&gt; Application Stop -&gt; ValidateService -&gt; Application Start
- ▫️ Application Stop -&gt; Before Install -&gt; ValidateService -&gt; Application Start

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Application Stop -> Before Install -> Application Start -> ValidateService»**

Деплой у CodeDeploy — встановлення вмісту (код, конфіги, пакети, скрипти) на інстанси за заданими правилами. Секція `hooks` в AppSpec для EC2/on-premises пов'язує події життєвого циклу зі скриптами, а для Lambda/ECS вказує Lambda-функції валідації. Якщо hook не задано, на цій події нічого не виконується. Повний порядок для in-place: ApplicationStop → DownloadBundle → BeforeInstall → Install → AfterInstall → ApplicationStart → ValidateService. Цей варіант зберігає відносний порядок.

**❌ Неправильні варіанти:** решта трьох варіантів порушують порядок (BeforeInstall раніше за ApplicationStop або ValidateService раніше за ApplicationStart).

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t04-q41"></a>
### T04-Q41
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Хуки EC2.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A Developer is deploying an Amazon EC2 update using AWS CodeDeploy. In the appspec.yml file, which of the following is a valid structure for the order of hooks that should be specified?

- ▫️ BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeAllowTraffic &gt; AfterAllowTraffic
- ✅ BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «BeforeInstall > AfterInstall > ApplicationStart > ValidateService»**

Секція `hooks` в AppSpec залежить від платформи: для EC2/on-premises вона пов'язує події зі скриптами, для Lambda/ECS — з Lambda-функціями валідації. Для EC2 валідна послідовність: `BeforeInstall → AfterInstall → ApplicationStart → ValidateService`.

**❌ Неправильні варіанти:**
- *…AfterAllowTestTraffic…* — для ECS.
- *BeforeAllowTraffic > AfterAllowTraffic* — для Lambda.
- *BeforeBlockTraffic > AfterBlockTraffic > BeforeAllowTraffic > AfterAllowTraffic* — неповний перелік hooks EC2.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t04-q43"></a>
### T04-Q43
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to update an Amazon ECS application that was deployed using AWS CodeDeploy. What file does the Developer need to update to push the change through CodeDeploy?

- ✅ appspec.yml
- ▫️ dockerrun.aws.json
- ▫️ buildspec.yml
- ▫️ ebextensions.config

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «appspec.yml»**

Ревізія CodeDeploy — це версія файлів і скриптів для деплою: її готують, додають AppSpec і завантажують в S3 або GitHub. Для ECS:
- AppSpec задає task definition, ім'я контейнера і порт для маршрутизації трафіку, а також опційні Lambda-функції на подіях життєвого циклу;
- ревізія і є AppSpec-файлом (JSON або YAML; можна ввести прямо в консолі).

Щоб задеплоїти зміну, оновлюють `appspec.yml`.

**❌ Неправильні варіанти:**
- *Dockerrun.aws.json* — опис розгортання Docker-образу в Elastic Beanstalk.
- *buildspec.yaml* — команди збірки CodeBuild.
- *ebextensions.config* — конфігурація середовища Elastic Beanstalk.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/application-revisions-appspec-file.html>

</details>

---

<a id="t04-q50"></a>
### T04-Q50
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **Застаріле: Cloud9 закритий для нових клієнтів (липень 2024).** → [детальніше](../../07-outdated-and-wrong-answers.md#o03)

A company will be hiring a large number of Developers for a series of projects. The Develops will bring their own devices to work and the company want to ensure consistency in tooling. The Developers must be able to write, run, and debug applications with just a browser, without needing to install or maintain a local Integrated Development Environment (IDE).

Which AWS service should the Developers use?

- ▫️ AWS CodeCommit
- ▫️ AWS X-Ray
- ✅ AWS Cloud9
- ▫️ AWS CodeDeploy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Cloud9»** *(застаріле: Cloud9 закритий для нових клієнтів з 07.2024)*

AWS Cloud9 — IDE в браузері з редактором коду для багатьох мов, дебагерами і вбудованим терміналом; інструменти для написання, збірки, запуску, тестування й дебагу, без встановлення локальної IDE. Налаштовується під себе: теми, гарячі клавіші, підсвітка синтаксису.

**❌ Неправильні варіанти:**
- *AWS CodeCommit* — Git-репозиторії, не IDE.
- *AWS CodeDeploy* — автоматизація деплою.
- *AWS X-Ray* — аналіз і дебаг розподілених застосунків у продакшні.

Джерела: <https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html>

</details>

---

<a id="t05-q04"></a>
### T05-Q04
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A developer is creating a microservices application that includes and AWS Lambda function. The function generates a unique file for each execution and must commit the file to an AWS CodeCommit repository.

How should the developer accomplish this?

- ✅ Use an AWS SDK to instantiate a CodeCommit client. Invoke the PutFile method to add the file to the repository and execute a commit with CreateCommit.
- ▫️ After the new file is created in Lambda, use CURL to invoke the CodeCommit API. Send the file to the repository and automatically commit the change.
- ▫️ Send a message to an Amazon SQS queue with the file attached. Configure an AWS Step Function as a destination for messages in the queue. Configure the Step Function to add the new file to the repository and commit the change.
- ▫️ Upload the new file to an Amazon S3 bucket. Create an AWS Step Function to accept S3 events. Use AWS Lambda functions in the Step Function, to add the file to the repository and commit the change.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an AWS SDK to instantiate a CodeCommit client. Invoke the PutFile method to add the file to the repository and execute a commit with CreateCommit»**

Через AWS SDK створюють клієнт CodeCommit для програмної роботи з репозиторієм. `PutFile` додає чи змінює один файл у вказаному репозиторії й гілці, `CreateCommit` створює коміт зі змінами.

**❌ Неправильні варіанти:**
- *SQS → Step Functions as a destination* — Step Functions не може бути призначенням повідомлень SQS напряму; дуже неефективно.
- *Use CURL to invoke the CodeCommit API* — за банком працювати з API CodeCommit треба через SDK (запити потребують підпису SigV4).
- *Upload to S3 → Step Functions on S3 events* — Step Functions не є прямим призначенням S3 event notifications (підтримуються SNS, SQS, Lambda, EventBridge).

Джерела: <https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/codecommit/AWSCodeCommitClient.html>

</details>

---

<a id="t05-q07"></a>
### T05-Q07
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An engineer wishes to share a software project they've developed with their team members for review. The shared application code needs to be preserved over time, with multiple versions and batch modifications being tracked. What is the most appropriate AWS service for this purpose?

- ▫️ AWS Cloud9
- ▫️ AWS CodeStar
- ▫️ AWS CodeDeploy
- ✅ AWS CodeCommit

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

CodeCommit — керований сервіс контролю версій із захищеними Git-репозиторіями: спільна робота над кодом, версіонування, відстеження змін.

**❌ Неправильні варіанти:**
- *AWS CodeStar* — швидка розробка, збірка й деплой; не фокусується на контролі версій (і вже припинений).
- *AWS Cloud9* — хмарна IDE; ділитися кодом можна, але повноцінного версіонування, як у CodeCommit, немає.
- *AWS CodeDeploy* — автоматизація деплою.

Джерела: <https://aws.amazon.com/codecommit/>

</details>

---

<a id="t05-q14"></a>
### T05-Q14
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company needs a version control system for collaborative software development. The solution must include support for batches of changes across multiple files and parallel branching.

Which AWS service will meet these requirements?

- ▫️ AWS CodeBuild
- ▫️ AWS CodePipeline
- ▫️ Amazon S3
- ✅ AWS CodeCommit

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

AWS CodeCommit — сервіс контролю версій від AWS для приватного зберігання файлів у хмарі: документів, коду, бінарних файлів. Він оптимізований для командної розробки: керує пакетами змін у багатьох файлах, які можуть вноситися паралельно з правками інших розробників.

ℹ️ У 2024 році CodeCommit закрили для нових клієнтів, але в листопаді 2025 повернули в GA. Для іспиту це валідна відповідь.

**❌ Неправильні варіанти:**
- *AWS CodeBuild* — керований CI-сервіс: компілює код, запускає тести, створює пакети для деплою.
- *AWS CodePipeline* — керований сервіс безперервної доставки, який автоматизує пайплайни релізів.
- *Amazon S3* — версіонування S3 дає відновити старі версії файлів, але не має функцій командної роботи з кодом.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html>

</details>

---

<a id="t05-q20"></a>
### T05-Q20
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A team of Developers are building a continuous integration and delivery pipeline using AWS Developer Tools. Which services should they use for running tests against source code and installing compiled code on their AWS resources? (Select TWO.)

- ▫️ AWS CodeCommit for installing compiled code on their AWS resources
- ✅ AWS CodeDeploy for installing compiled code on their AWS resources
- ✅ AWS CodeBuild for running tests against source code
- ▫️ AWS Cloud9 for running tests against source code
- ▫️ AWS CodePipeline for running tests against source code

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeDeploy for installing compiled code on their AWS resources» + «AWS CodeBuild for running tests against source code»**

- **CodeBuild** — повністю керований build-сервіс: компілює код, запускає unit-тести, створює артефакти для деплою. Власні build-сервери не потрібні, є готові середовища (Maven, Gradle тощо).
- **CodeDeploy** — автоматизує деплой на EC2, on-premises сервери, Lambda та ECS.

**❌ Неправильні варіанти:**
- *CodePipeline for running tests* — CodePipeline оркеструє реліз-пайплайн; тести в ньому запускає CodeBuild.
- *CodeCommit for installing code* — CodeCommit лише хостить Git-репозиторії.
- *Cloud9 for running tests* — Cloud9 — це хмарна IDE. ℹ️ Її закрили для нових клієнтів у 2024 році.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html>

</details>

---

<a id="t05-q30"></a>
### T05-Q30
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company wants a serverless solution for phased release of static websites hosted on various version control systems. Deployments should be triggered by Git branch merges and all data exchange should be over HTTPS.

Which option offers the LOWEST operational overhead?

- ▫️ Use AWS Elastic Beanstalk for hosting and use AWS CodeStar to manage deployments and workflows.
- ▫️ Deploy websites on separate Amazon EC2 instances for each environment, use AWS CodeDeploy for automation, and link it with the version control systems.
- ✅ Use AWS Amplify for hosting, connect corresponding repository branches, and initiate deployments by merging changes to the needed branch.
- ▫️ Use Amazon S3 to host the websites, create a manual script to deploy changes when there are code merges in the version control systems.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Amplify for hosting, connect corresponding repository branches, and initiate deployments by merging changes to the needed branch.»**

AWS Amplify (Amplify Hosting) хостить статичні сайти з безперервним деплоєм із Git. Гілки репозиторію прив'язуються до середовищ Amplify, і зміни деплояться автоматично після merge. Серед варіантів це рішення з найменшими операційними витратами.

**❌ Неправильні варіанти:**
- *EC2 per environment + CodeDeploy* — це не serverless; інстанси працюють постійно, а операційних витрат більше.
- *S3 + manual script* — S3 може хостити статичні сайти, але ручний скрипт додає зайву складність порівняно з Amplify.
- *Elastic Beanstalk + CodeStar* — Beanstalk призначений для динамічних багаторівневих застосунків, і ця зв'язка складніша за Amplify. ℹ️ CodeStar припинено в 2024 році.

Джерела: <https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html>

</details>

---

<a id="t05-q31"></a>
### T05-Q31
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company is planning to use AWS CodeDeploy to deploy a new AWS Lambda function

What are the MINIMUM properties required in the 'resources' section of the AppSpec file for CodeDeploy to deploy the function successfully?

- ▫️ name, alias, PlatformVersion, and type
- ✅ name, alias, currentversion, and targetversion
- ▫️ TaskDefinition, LoadBalancerInfo, and ContainerPort
- ▫️ TaskDefinition, PlatformVersion, and ContainerName

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «name, alias, currentversion, and targetversion»**

Вміст секції `resources` в AppSpec залежить від платформи. Для Lambda-деплою вона містить `name`, `alias`, `currentversion` і `targetversion` функції. Це мінімальний набір обов'язкових властивостей.

**❌ Неправильні варіанти:**
- *name, alias, PlatformVersion, type* — неправильний набір (див. вище).
- *TaskDefinition, LoadBalancerInfo, ContainerPort* — це властивості для ECS-деплою.
- *TaskDefinition, PlatformVersion, ContainerName* — це властивості для ECS-деплою.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-resources.html>

</details>

---

<a id="t06-q01"></a>
### T06-Q01
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A team of Developers are working on a shared project and need to be able to collaborate on code. The shared application code must be encrypted at rest, stored on a highly available and durable architecture, and support multiple versions and batch change tracking.

Which AWS service should the Developer use?

- ▫️ AWS CodeBuild
- ▫️ Amazon S3
- ▫️ AWS Cloud9
- ✅ AWS CodeCommit

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

CodeCommit — повністю керований сервіс контролю версій із приватними Git-репозиторіями. Файли автоматично шифруються під час передавання і в стані спокою. Команди працюють через pull requests, гілки, merge і code review, а дозволи можна налаштувати так, щоб лише певні люди змінювали конкретні гілки.

**❌ Неправильні варіанти:**
- *AWS CodeBuild* — керований CI-сервіс: компілює код, запускає тести, створює пакети.
- *Amazon S3* — об'єктне сховище без потрібних функцій командної роботи з кодом.
- *AWS Cloud9* — хмарна IDE.

Джерела: <https://aws.amazon.com/codecommit/>

</details>

---

<a id="t06-q07"></a>
### T06-Q07
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **Застаріле: Cloud9 закритий для нових клієнтів.** → [детальніше](../../07-outdated-and-wrong-answers.md#o03)

A developer is planning on using AWS Cloud9 to build a new application. The developer wants to spend minimal time configuring resources. What solution should the developer choose?

- ▫️ Create an SSH environment that will provision many of the resources and manage the lifecycle.
- ▫️ Use AWS Toolkit to create an SSH environment that will provision many of the resources and manage the lifecycle.
- ✅ Create an AWS Cloud9 Elastic Compute Cloud (EC2) environment that will provision many of the resources and manage the lifecycle.
- ▫️ Use AWS Toolkit to create an Elastic Compute Cloud (EC2) environment that will provision many of the resources and manage the lifecycle.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an AWS Cloud9 Elastic Compute Cloud (EC2) environment that will provision many of the resources and manage the lifecycle.»**

У середовищі Cloud9 типу EC2 багато налаштувань і керування бере на себе AWS: життєвий цикл інстансу, налаштований CLI, встановлені Git, Node.js і Python.

⚠️ Cloud9 закрито для нових клієнтів з липня 2024 року; на сучасному іспиті він малоймовірний. Див. 07.

**❌ Неправильні варіанти:**
- *SSH environment* — не керує життєвим циклом інстансу; CLI і пакети треба налаштовувати вручну.
- *AWS Toolkit → EC2 environment* — AWS Toolkit — це розширення IDE, воно не створює EC2-середовищ.
- *AWS Toolkit → SSH environment* — AWS Toolkit не створює й SSH-середовищ.

Джерела: <https://docs.aws.amazon.com/cloud9/latest/user-guide/ec2-env-versus-ssh-env.html>

</details>

---

<a id="t06-q23"></a>
### T06-Q23
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A development team using AWS Amplify is testing new features for an application. How can the team test the features with the current production code without impacting the last published application version?

- ▫️ Connect the production environment into AWS Cloud9 and test the features after committing them.
- ▫️ Connect the production environment into AWS Cloud9 and test the features after publishing them.
- ✅ Connect a new branch that will deploy a version named https://dev.applicationId.amplifyapp.com to test new features.
- ▫️ Connect a new branch that will deploy a version named https://main.applicationId.amplifyapp.com to test new features.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Connect a new branch that will deploy a version named https://dev.applicationId.amplifyapp.com to test new features.»**

Нові функції не тестують на продакшн-коді. Amplify дозволяє підключити окрему гілку, яка отримає своє середовище з URL вигляду `https://<branch>.<appId>.amplifyapp.com`. Там функції розробляють, тестують і за потреби відкочують, не зачіпаючи опубліковану версію.

**❌ Неправильні варіанти:**
- *main.applicationId…* — `main` у URL означає продакшн-середовище, а не тестове.
- *Cloud9, test after publishing* — Cloud9 — лише IDE, і тестувати після публікації в продакшн неправильно.
- *Cloud9, test after committing* — Cloud9 — лише IDE; commit просто фіксує знімок змін.

Джерела: <https://docs.aws.amazon.com/amplify/latest/userguide/multi-environments.html>

</details>

---

<a id="t06-q38"></a>
### T06-Q38
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Development team would like to migrate their existing application code from a GitHub repository to AWS CodeCommit.

What needs to be created before they can migrate a cloned repository to CodeCommit over HTTPS?

- ▫️ A GitHub secure authentication token
- ▫️ An Amazon EC2 IAM role with CodeCommit permissions
- ▫️ A public and private SSH key file
- ✅ A set of credentials generated from IAM

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «A set of credentials generated from IAM»**

Найпростіший спосіб підключитися до CodeCommit через HTTPS — згенерувати Git credentials для CodeCommit (логін і пароль) у консолі IAM. Ці ж облікові дані працюють у будь-якому сторонньому інструменті чи IDE з HTTPS-автентифікацією за статичним логіном і паролем.

ℹ️ Альтернатива для HTTPS — git-remote-codecommit (GRC) з тимчасовими обліковими даними.

**❌ Неправильні варіанти:**
- *GitHub token* — до CodeCommit так не автентифікуються.
- *SSH key pair* — потрібна для доступу через SSH, а питають про HTTPS.
- *EC2 IAM role with CodeCommit permissions* — дає права на керування CodeCommit, а питання про автентифікацію Git-клієнта через HTTPS.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html>

</details>

---

<a id="t06-q45"></a>
### T06-Q45
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application will be hosted on the AWS Cloud. Developers will be using an Agile software development methodology with regular updates deployed through a continuous integration and delivery (CI/CD) model. Which AWS service can assist the Developers with automating the build, test, and deploy phases of the release process every time there is a code change?

- ▫️ AWS CodeBuild
- ▫️ AWS CloudFormation
- ✅ AWS CodePipeline
- ▫️ AWS Elastic Beanstalk

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodePipeline»**

CodePipeline — сервіс безперервної доставки, який моделює, візуалізує й автоматизує кроки релізу. Що він дає:
- повна автоматизація від репозиторію через build і тести до деплою;
- manual approval можна додати в будь-який stage, окрім Source;
- однаковий набір кроків для кожної зміни коду;
- швидші й якісніші релізи;
- інтеграція з наявними інструментами для source, build і deploy;
- статус у реальному часі, повторення невдалих дій, ручний перезапуск;
- історія запусків: час, тривалість, execution IDs.

**❌ Неправильні варіанти:**
- *CloudFormation* — не запускається від змін у репозиторії коду; оновлення деплоять через change sets.
- *Elastic Beanstalk* — сам не оновлюється при змінах у коді; нову версію треба задеплоїти.
- *CodeBuild* — компілює, тестує й пакує код, але деплоєм не керує.

Джерела: <https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome-what-can-I-do.html>

</details>

---

<a id="t07-q05"></a>
### T07-Q05
*домен: офіційна мітка*

An organization is moving its on-premises resources to the cloud. Source code will be moved to AWS CodeCommit and AWS CodeBuild will be used for compiling the source code using Apache Maven as a build tool. The organization wants the build environment should allow for scaling and running builds in parallel.

Which of the following options should the organization choose for their requirement?

- ▫️ Run CodeBuild in an Auto Scaling group
- ▫️ Enable CodeBuild Auto Scaling
- ✅ CodeBuild scales automatically, the organization does not have to do anything for scaling or for parallel builds
- ▫️ Choose a high-performance instance type for your CodeBuild instances

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeBuild scales automatically, the organization does not have to do anything for scaling or for parallel builds»**

CodeBuild — повністю керований build-сервіс: компілює код, запускає тести, створює артефакти. Свої build-сервери не потрібні, є готові середовища (Maven, Gradle тощо) і можна налаштувати власні. CodeBuild автоматично масштабується під пікову кількість збірок.

**❌ Неправильні варіанти:**
- *High-performance instance type* — для цієї вимоги нічого не змінить.
- *CodeBuild in an ASG* — CodeBuild — керований сервіс, ASG йому не потрібна.
- *Enable CodeBuild Auto Scaling* — такого налаштування немає: масштабування автоматичне.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html>

</details>

---

<a id="t07-q13"></a>
### T07-Q13
*домен: офіційна мітка*

You have a workflow process that pulls code from AWS CodeCommit and deploys to EC2 instances associated with tag group ProdBuilders. You would like to configure the instances to archive no more than two application revisions to conserve disk space.

Which of the following will allow you to implement this?

- ▫️ AWS CloudWatch Log Agent
- ▫️ Have a load balancer in front of your instances
- ✅ CodeDeploy Agent
- ▫️ Integrate with AWS CodePipeline

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeDeploy Agent»**

CodeDeploy agent — програмний пакет на інстансі, без якого той не може брати участь у деплоях CodeDeploy. Агент зберігає архіви ревізій і лог-файли на інстансі та прибирає їх, щоб звільнити місце. Параметр `:max_revisions:` у конфігурації агента задає, скільки ревізій зберігати (будь-яке додатне число). Решта видаляються разом із логами, окрім логу останнього успішного деплою.

Детальніше: https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent.html

**❌ Неправильні варіанти:**
- *CloudWatch Logs agent* — надсилає логи з EC2 у CloudWatch Logs; архівами ревізій не керує.
- *Integrate with CodePipeline* — CodePipeline автоматизує релізи, а не керує архівами на EC2.
- *Load balancer* — розподіляє трафік і до задачі не стосується.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AgentReference.html>

</details>

---

<a id="t07-q15"></a>
### T07-Q15
*домен: офіційна мітка*

As part of employee skills upgrade, the developers of your team have been delegated few responsibilities of DevOps engineers. Developers now have full control over modeling the entire software delivery process, from coding to deployment. As the team lead, you are now responsible for any manual approvals needed in the process.

Which of the following approaches supports the given workflow?

- ✅ Create one CodePipeline for your entire flow and add a manual approval step
- ▫️ Create deeply integrated AWS CodePipelines for each environment
- ▫️ Use CodePipeline with Amazon Virtual Private Cloud
- ▫️ Create multiple CodePipelines for each environment and link them using AWS Lambda

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create one CodePipeline for your entire flow and add a manual approval step»**

Approval action додають у той stage пайплайна, де він має зупинитися, щоб хтось вручну схвалив або відхилив продовження. В stage Source approval додати не можна: там бувають лише source-дії.

**❌ Неправильні варіанти:**
- *Multiple pipelines linked via Lambda* — Lambda можна додати як дію в пайплайн, але схвалення — це частина workflow, і виносити його в інший сервіс не потрібно.
- *Deeply integrated pipelines per environment* — можливо (CloudFormation + CodePipeline + CodeCommit), але не оптимально.
- *CodePipeline with VPC* — VPC endpoints через PrivateLink підвищують безпеку, але до задачі не стосуються.

Джерела: <https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals-action-add.html> · <https://docs.aws.amazon.com/codepipeline/latest/userguide/vpc-support.html>

</details>

---

<a id="t08-q04"></a>
### T08-Q04
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company uses continuous integration and continuous delivery (CI/CD) systems. A Developer needs to automate the deployment of a software package to Amazon EC2 instances as well as to on-premises virtual servers.

Which AWS service can be used for the software deployment?

- ✅ AWS CodeDeploy
- ▫️ AWS CodePipeline
- ▫️ AWS Elastic Beanstalk
- ▫️ AWS CloudBuild

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeDeploy»**

CodeDeploy автоматизує деплой на EC2, on-premises сервери, Lambda та ECS. Він розгортає вміст, що зберігається в S3, GitHub або Bitbucket, і не потребує змін у наявному коді. In-place деплой однаково працює і на EC2, і на on-premises серверах, тож CodeDeploy розгорне пакет на обидва типи.

**❌ Неправильні варіанти:**
- *CodePipeline* — автоматизує пайплайн релізу; CodeDeploy може бути в ньому кроком, але деплоїть саме CodeDeploy.
- *AWS CloudBuild* — такого сервісу немає, мабуть, мався на увазі CodeBuild; це інструмент збирання, а не деплою.
- *Elastic Beanstalk* — не деплоїть на on-premises сервери.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html>

</details>

---

<a id="t08-q31"></a>
### T08-Q31
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A team of Developers have been assigned to a new project. The team will be collaborating on the development and delivery of a new application and need a centralized private repository for managing source code. The repository should support updates from multiple sources. Which AWS service should the development team use?

- ▫️ AWS CodePipeline
- ✅ AWS CodeCommit
- ▫️ AWS CodeDeploy
- ▫️ AWS CodeBuild

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

CodeCommit — захищений масштабований керований сервіс контролю версій з приватними Git-репозиторіями. Власну систему контролю версій розгортати й масштабувати не потрібно. Що він дає:
- **Повністю керований** — висока доступність і надійність, без обладнання та серверного ПЗ.
- **Безпека** — шифрування at rest і in transit.
- **Командна робота** — pull requests з рев'ю й коментарями, email-сповіщення.
- **Масштаб** — багато файлів і гілок, великі файли, довга історія.
- **Без лімітів** на розмір репозиторію і типи файлів.
- **Інтеграція** з IAM, іншими сервісами AWS і сторонніми інструментами; міграція з будь-якого Git-репозиторію.
- **Знайомі інструменти** — стандартні Git-команди, а також власні CLI і API.

ℹ️ CodeCommit знову в GA з листопада 2025 року.

**❌ Неправильні варіанти:**
- *CodeBuild* — CI-сервіс: компілює код, запускає тести, пакує артефакти.
- *CodeDeploy* — автоматизує деплой на EC2, on-premises, Lambda, ECS.
- *CodePipeline* — автоматизує пайплайни безперервної доставки.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html>

</details>

---

<a id="t08-q35"></a>
### T08-Q35
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A firm intends to utilize AWS CodeDeploy to deploy an application to Amazon Elastic Container Service (Amazon ECS). While deploying an updated version of the application, the company's initial requirement is to direct 10% of active traffic to the updated application version. Following a 15-minute interval, all remaining active traffic must be rerouted to the updated application.

Which predefined CodeDeploy configuration aligns with these needs?

- ▫️ CodeDeployDefault.LambdaLinear10PercentEvery1Minutes
- ✅ CodeDeployDefault.ECSCanary10Percent15Minutes
- ▫️ CodeDeployDefault.ECSLinear10PercentEvery10Minutes
- ▫️ CodeDeployDefault.ECSAllAtOnce

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeDeployDefault.ECSCanary10Percent15Minutes»**

Ця наперед визначена конфігурація CodeDeploy спочатку направляє 10% трафіку на нову версію, а через 15 хвилин — решту. Саме це вимагає компанія.

**❌ Неправильні варіанти:**
- *ECSAllAtOnce* — увесь трафік одразу.
- *LambdaLinear10PercentEvery1Minutes* — конфігурація для Lambda, а не ECS.
- *ECSLinear10PercentEvery10Minutes* — +10% кожні 10 хвилин, а не 10%, а потім решта.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html>

</details>

---

<a id="t09-q16"></a>
### T09-Q16
*домен: офіційна мітка*

A developer needs to automate software package deployment to both Amazon EC2 instances and virtual servers running on-premises, as part of continuous integration and delivery that the business has adopted.

Which AWS service should he use to accomplish this task?

- ▫️ AWS CodeBuild
- ✅ AWS CodeDeploy
- ▫️ AWS Elastic Beanstalk
- ▫️ AWS CodePipeline

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeDeploy»**

- **Continuous integration** — розробники регулярно зливають зміни в центральний репозиторій, після чого автоматично запускаються збірки й тести.
- **Continuous delivery** — розширює CI: після збірки зміни автоматично деплояться в тестове чи продакшн-середовище.

CodeDeploy — повністю керований сервіс **деплою** на EC2, Fargate, Lambda і on-premises. Він пришвидшує випуск нових функцій, допомагає уникнути простою і бере на себе складність оновлення застосунків.

**❌ Неправильні варіанти:**
- *CodePipeline* — сервіс безперервної доставки, що оркеструє build, test і deploy. Але сам деплоїть CodeDeploy.
- *CodeBuild* — CI: компілює, тестує, пакує; паралельні збірки без черги.
- *Elastic Beanstalk* — деплоїть і масштабує вебзастосунки, сам керує ресурсами. Але як сервіс деплою, що питають, — CodeDeploy.

Джерела: <https://aws.amazon.com/codedeploy/> · <https://aws.amazon.com/codepipeline/> · <https://aws.amazon.com/codebuild/> · <https://aws.amazon.com/elasticbeanstalk/> · <https://aws.amazon.com/devops/continuous-delivery/> · <https://aws.amazon.com/devops/continuous-integration/>

</details>

---

<a id="t09-q39"></a>
### T09-Q39
*домен: офіційна мітка*

A company needs a version control system for their fast development lifecycle with incremental changes, version control, and support to existing Git tools.

Which AWS service will meet these requirements?

- ✅ AWS CodeCommit
- ▫️ AWS CodeBuild
- ▫️ AWS CodePipeline
- ▫️ Amazon Versioned S3 Bucket

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

CodeCommit — повністю керований сервіс контролю версій із захищеними Git-репозиторіями. Що він дає:
- спільна робота через pull requests, гілки й merge;
- репозиторії поруч із build-, staging- і production-середовищами в AWS;
- передаються лише інкрементальні зміни, а не весь застосунок;
- усі Git-команди, ваші IDE-плагіни, CI/CD-системи та графічні клієнти.

**❌ Неправильні варіанти:**
- *Versioned S3 bucket* — відновлює старі версії окремих файлів, але не відстежує набори змін у кількох файлах і не має гілок та diff.
- *CodePipeline* — автоматизує пайплайн доставки: build, test, deploy.
- *CodeBuild* — CI-сервіс: компілює, тестує, пакує.

Джерела: <https://aws.amazon.com/codecommit/> · <https://aws.amazon.com/codepipeline/> · <https://aws.amazon.com/codebuild/> · <https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html>

</details>

---

<a id="t09-q59"></a>
### T09-Q59
*домен: офіційна мітка*

A developer in your company was just promoted to Team Lead and will be in charge of code deployment on EC2 instances via AWS CodeCommit and AWS CodeDeploy. Per the new requirements, the deployment process should be able to change permissions for deployed files as well as verify the deployment success.

Which of the following actions should the new Developer take?

- ▫️ Define a buildspec.yml file in the codebuild/ directory
- ▫️ Define an appspec.yml file in the codebuild/ directory
- ▫️ Define a buildspec.yml file in the root directory
- ✅ Define an appspec.yml file in the root directory

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Define an appspec.yml file in the root directory»**

AppSpec-файл має називатися `appspec.yml`, бути у форматі YAML і лежати в корені структури вихідного коду застосунку. Він:
- задає, куди на інстансі копіювати файли ревізії;
- встановлює дозволи для розгорнутих файлів;
- визначає скрипти для різних етапів деплою (hooks).

Під час деплою агент CodeDeploy шукає поточну подію в секції `hooks`. Якщо знаходить, виконує її скрипти послідовно в порядку з файлу, і статус кожного логується. Успішний скрипт повертає код 0. Якщо ОС на інстансі не відповідає тій, що вказана в AppSpec, деплой падає.

**❌ Неправильні варіанти:**
- *buildspec.yml in root* — це файл CodeBuild.
- *buildspec.yml in codebuild/* — це файл CodeBuild.
- *appspec.yml in codebuild/* — AppSpec має лежати в корені.

</details>

---

<a id="t10-q03"></a>
### T10-Q03
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A development team require a fully-managed source control service that is compatible with Git.

Which service should they use?

- ▫️ AWS Cloud9
- ▫️ AWS CodePipeline
- ▫️ AWS CodeDeploy
- ✅ AWS CodeCommit

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

CodeCommit — повністю керований сервіс контролю версій від AWS із захищеними Git-репозиторіями. У ньому приватно зберігають документи, вихідний код і бінарні файли.

**❌ Неправильні варіанти:**
- *CodeDeploy* — автоматизує деплой.
- *CodePipeline* — автоматизує пайплайн доставки.
- *Cloud9* — хмарна IDE.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html>

</details>

---

<a id="t10-q09"></a>
### T10-Q09
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has joined a team and needs to connect to the AWS CodeCommit repository using SSH. What should the Developer do to configure access using Git?

- ▫️ Create an account on Github and user those login credentials to login to AWS CodeCommit
- ✅ Generate an SSH public and private key. Upload the public key to the Developer’s IAM account
- ▫️ On the Developer’s IAM account, under security credentials, choose to create HTTPS Git credentials for AWS CodeCommit
- ▫️ On the Developer’s IAM account, under security credentials, choose to create an access key and secret ID

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Generate an SSH public and private key. Upload the public key to the Developer’s IAM account»**

Git-клієнт для CodeCommit налаштовують з обліковими даними IAM. Підтримуються три типи:
- **Git credentials** — логін і пароль, згенеровані IAM, для HTTPS;
- **SSH-ключі** — пара ключів, згенерована локально і прив'язана до IAM-користувача, для SSH;
- **AWS access keys** — для HTTPS через credential helper AWS CLI.

Для SSH розробник генерує пару ключів (спосіб залежить від ОС) і завантажує публічний ключ у свій IAM-обліковий запис у розділі Security credentials.

**❌ Неправильні варіанти:**
- *HTTPS Git credentials* — для HTTPS, а не SSH.
- *GitHub account* — облікові дані GitHub для CodeCommit не підходять.
- *Access key and secret* — працюють лише через credential helper і для HTTPS.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html> · <https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-unixes.html>

</details>

---

<a id="t10-q17"></a>
### T10-Q17
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team have moved their continuous integration and delivery (CI/CD) pipeline into the AWS Cloud. The team is leveraging AWS CodeCommit for management of source code. The team need to compile their source code, run tests, and produce software packages that are ready for deployment.

Which AWS service can deliver these outcomes?

- ▫️ AWS CodeCommit
- ✅ AWS CodeBuild
- ▫️ AWS CodePipeline
- ▫️ AWS Cloud9

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeBuild»**

CodeBuild — повністю керований build-сервіс: компілює код, запускає unit-тести, створює готові артефакти. Є готові середовища (Maven, Gradle тощо) і можна підключити власні інструменти. Переваги:
- **повністю керований** — не треба налаштовувати, оновлювати й підтримувати build-сервери;
- **на вимогу** — масштабується автоматично, оплата за хвилини збірки;
- **з коробки** — готові середовища для популярних мов, достатньо вказати build-скрипт.

**❌ Неправильні варіанти:**
- *CodeCommit* — команда вже використовує його за призначенням; компілювати й тестувати він не вміє.
- *CodePipeline* — оркеструє пайплайн доставки, а не збирає код.
- *Cloud9* — хмарна IDE.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html>

</details>

---

<a id="t10-q57"></a>
### T10-Q57
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Developer is creating an AWS Lambda function that generates a new file each time it runs. Each new file must be checked into an AWS CodeCommit repository hosted in the same AWS account.

How should the Developer accomplish this?

- ▫️ Upload the new file to an Amazon S3 bucket. Create an AWS Step Function to accept S3 events. In the Step Function, add the new file to the repository
- ✅ Use an AWS SDK to instantiate a CodeCommit client. Invoke the put_file method to add the file to the repository
- ▫️ When the Lambda function starts, use the Git CLI to clone the repository. Check the new file into the cloned repository and push the change
- ▫️ After the new file is created in Lambda, use cURL to invoke the CodeCommit API. Send the file to the repository

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an AWS SDK to instantiate a CodeCommit client. Invoke the put_file method to add the file to the repository»**

Розробник створює клієнт CodeCommit через SDK, наприклад `boto3.client('codecommit')`, і викликає `put_file`. Метод додає або оновлює файл у гілці репозиторію і створює commit. Основні параметри: `repositoryName`, `branchName`, `fileContent`, `filePath`, `parentCommitId`.

**❌ Неправильні варіанти:**
- *Git CLI clone and push* — клонувати репозиторій не потрібно; досить додати файл.
- *cURL to CodeCommit API* — так не працюють; для API потрібен підписаний запит через SDK.
- *S3 + Step Functions* — Step Functions напряму не запускається подіями S3, тільки через EventBridge, і це зайва складність.

Джерела: <https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/codecommit.html>

</details>

---

<a id="t10-q59"></a>
### T10-Q59
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

```json
A Developer needs to access AWS CodeCommit over SSH. The SSH keys configured to access AWS CodeCommit are tied to a user with the following permissions:
{
"version": "2012-10-17"
"Statement": [
{
"Effect": "Allow",
"Action": [
"codecommit:BatchGetRepositories",
"codecommit:Get*"
"codecommit:List*",
"codecommit:GitPull"
],
"Resource": "*"
}
]
}
The Developer needs to create/delete branches.
Which specific IAM permissions need to be added based on the principle of least privilege?
```

- ▫️ “codecommit:Put\*:”
- ▫️ “codecommit:\*”
- ▫️ “codecommit:Update\*”
- ✅ “codecommit:CreateBranch” and “codecommit:DeleteBranch”

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «“codecommit:CreateBranch” and “codecommit:DeleteBranch”»**

Користувачу бракує прав створювати й видаляти гілки. За принципом найменших привілеїв додають лише їх. API для гілок:
- `CreateBranch` — створює гілку;
- `DeleteBranch` — видаляє гілку, крім default;
- `GetBranch` — інформація про гілку;
- `ListBranches` — список гілок;
- `UpdateDefaultBranch` — змінює default-гілку.

**❌ Неправильні варіанти:**
- *codecommit:Put\** — `PutFile` і `PutRepositoryTriggers` не стосуються гілок.
- *codecommit:Update\** — ці дії не дають створювати й видаляти гілки.
- *codecommit:\** — порушує принцип найменших привілеїв.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/codecommit/index.html#cli-aws-codecommit>

</details>

---

<a id="t10-q63"></a>
### T10-Q63
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company needs a fully-managed source control service that will work in AWS. The service must ensure that revision control synchronizes multiple distributed repositories by exchanging sets of changes peer-to-peer. All users need to work productively even when not connected to a network.

Which source control service should be used?

- ▫️ AWS CodeBuild
- ▫️ AWS CodeStar
- ✅ AWS CodeCommit
- ▫️ Subversion

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CodeCommit»**

CodeCommit — сервіс контролю версій від AWS для приватного зберігання документів, коду і бінарних файлів. Репозиторій — основний об'єкт: він зберігає код, файли й історію від першого commit. Що ще можна:
- спільна робота з іншими користувачами;
- email-сповіщення про події, наприклад коментарі;
- triggers на push та інші події, що запускають листи чи функції;
- push з локального репозиторію в кілька віддалених.

**❌ Неправильні варіанти:**
- *Subversion* — не повністю керована система.
- *CodeBuild* — збирання і тестування.
- *CodeStar* — не система контролю версій, а інтеграція з ними. ℹ️ CodeStar припинено в 2024 році.

Джерела: <https://aws.amazon.com/codecommit/>

</details>

---

<a id="t10-q65"></a>
### T10-Q65
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Хуки ECS.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A Developer is deploying an Amazon ECS update using AWS CodeDeploy. In the appspec.yaml file, which of the following is a valid structure for the order of hooks that should be specified?

- ▫️ BeforeAllowTraffic &gt; AfterAllowTraffic
- ✅ BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «BeforeInstall > AfterInstall > AfterAllowTestTraffic > BeforeAllowTraffic > AfterAllowTraffic»**

Секція `hooks` в AppSpec залежить від платформи:
- **EC2/On-Premises** — hooks прив'язують lifecycle events до скриптів;
- **Lambda і ECS** — hooks вказують Lambda-функції для перевірки на певних етапах.

Якщо hook відсутній, для цього етапу нічого не виконується. Секція потрібна, лише коли є скрипти чи функції перевірки. Наведений порядок — валідна структура hooks для **ECS**.

**❌ Неправильні варіанти:**
- *BeforeInstall > AfterInstall > ApplicationStart > ValidateService* — це для EC2.
- *BeforeAllowTraffic > AfterAllowTraffic* — це для Lambda.
- *BeforeBlockTraffic > AfterBlockTraffic > BeforeAllowTraffic > AfterAllowTraffic* — неповний список для EC2.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t11-q10"></a>
### T11-Q10
*домен: офіційна мітка*

CodeCommit is a managed version control service that hosts private Git repositories in the AWS cloud.

Which of the following credential types is NOT supported by IAM for CodeCommit?

- ✅ IAM username and password
- ▫️ SSH Keys
- ▫️ Git credentials
- ▫️ AWS Access Keys

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «IAM username and password»**

Питання на пошук облікових даних, які **не підходять** для CodeCommit. Логін і пароль IAM для консолі для доступу до репозиторію не використовують.

**❌ Інші варіанти підходять:**
- *Git credentials* — логін і пароль, згенеровані IAM, для HTTPS.
- *SSH Keys* — пара ключів, прив'язана до IAM-користувача, для SSH.
- *AWS access keys* — через credential helper AWS CLI для HTTPS.

</details>

---

<a id="t11-q46"></a>
### T11-Q46
*домен: офіційна мітка*

You're a developer working on a large scale order processing application. After developing the features, you commit your code to AWS CodeCommit and begin building the project with AWS CodeBuild before it gets deployed to the server. The build is taking too long and the error points to an issue resolving dependencies from a third-party. You would like to prevent a build running this long in the future for similar underlying reasons.

Which of the following options represents the best solution to address this use-case?

- ▫️ Use VPC Flow Logs
- ▫️ Use Amazon CloudWatch
- ✅ Enable CodeBuild timeouts
- ▫️ Use AWS Lambda

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable CodeBuild timeouts»**

Збірка (build) — набір дій CodeBuild, що з вхідних артефактів (наприклад, Java-класів) створює вихідні (JAR). Правила для кількох збірок:
- збірки йдуть паралельно, наскільки можливо;
- коли досягнуто ліміту, вони стають у чергу, максимум у 5 разів більшу за ліміт;
- збірка, що не стартувала за час очікування в черзі, видаляється з черги. Цей timeout — від 5 хвилин до 8 годин, за замовчуванням 8 годин.

Окремо є build timeout — від 5 хвилин до 36 годин, за замовчуванням 60 хвилин. Коли він спливає, збірка автоматично зупиняється.

**❌ Неправильні варіанти:**
- *Lambda* — не впливає на процес збірки.
- *CloudWatch* — моніторинг і логи, процес збірки не змінює.
- *VPC Flow Logs* — мережевий трафік, до збірки не стосується.

</details>

---

<a id="t11-q52"></a>
### T11-Q52
*домен: офіційна мітка*

A developer has been asked to create an application that can be deployed across a fleet of EC2 instances. The configuration must allow for full control over the deployment steps using the blue-green deployment.

Which service will help you achieve that?

- ▫️ CodeBuild
- ✅ CodeDeploy
- ▫️ Elastic Beanstalk
- ▫️ CodePipeline

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeDeploy»**

CodeDeploy автоматизує деплой на EC2, on-premises і Lambda. Blue/green-деплой, яким він керує, дає перевірити нову версію до того, як на неї піде продакшн-трафік. CodeDeploy дає найбільше контролю над кроками деплою.

Детальніше: https://aws.amazon.com/about-aws/whats-new/2017/01/aws-codedeploy-introduces-blue-green-deployments/

**❌ Неправильні варіанти:**
- *CodeBuild* — CI: компілює, тестує, пакує; не деплоїть.
- *Elastic Beanstalk* — має hooks, але менше контролю. Оновлення версії йде in-place і може дати короткий простій. Уникнути його можна blue/green через swap CNAME.
- *CodePipeline* — автоматизує build, test, deploy, але сам не деплоїть.

</details>

---

<a id="t12-q51"></a>
### T12-Q51
*домен: офіційна мітка*

As a site reliability engineer, you work on building and running large-scale, distributed, fault-tolerant systems in the cloud using automation. You have just replaced the company's Jenkins based CI/CD platform with AWS CodeBuild and would like to programmatically define your build steps.

Which of the following options should you choose?

- ✅ Define a buildspec.yml file in the root directory
- ▫️ Define a buildspec.yml file in the codebuild/ directory
- ▫️ Define an appspec.yml file in the root directory
- ▫️ Define an appspec.yml file in the codebuild/ directory

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Define a buildspec.yml file in the root directory»**

Buildspec — набір команд і налаштувань збірки у форматі YAML, за яким CodeBuild виконує збірку. Його кладуть у вихідний код (за замовчуванням `buildspec.yml` у корені) або задають при створенні build project.

**❌ Неправильні варіанти:**
- *appspec.yml in root* — AppSpec використовує CodeDeploy для деплою, а не CodeBuild.
- *buildspec.yml in codebuild/* — файл правильний, але за замовчуванням CodeBuild шукає його в корені.
- *appspec.yml in codebuild/* — неправильний файл і неправильне місце.

</details>

---

<a id="t12-q59"></a>
### T12-Q59
*домен: офіційна мітка*

As a site reliability engineer, you are responsible for improving the company’s deployment by scaling and automating applications. As new application versions are ready for production you ensure that the application gets deployed to different sets of EC2 instances at different times allowing for a smooth transition.

Using AWS CodeDeploy, which of the following options will allow you to do this?

- ▫️ CodeDeploy Agent
- ▫️ CodeDeploy Hooks
- ▫️ Define multiple CodeDeploy Applications
- ✅ CodeDeploy Deployment Groups

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeDeploy Deployment Groups»**

Для застосунку CodeDeploy можна задати одну чи кілька deployment groups — налаштувань і конфігурацій для деплою. Більшість налаштувань залежить від платформи, а rollbacks, triggers і alarms є для всіх. Для EC2/On-Premises група — це набір інстансів для деплою: інстанси з тегами, EC2 в ASG або обидва. Так одну ревізію можна розгорнути на різні групи в різний час.

**❌ Неправильні варіанти:**
- *CodeDeploy Agent* — ПЗ на інстансі, що з'єднує його із сервісом CodeDeploy.
- *CodeDeploy Hooks* — lifecycle events в AppSpec (ApplicationStart, ApplicationStop тощо) зі скриптами.
- *Multiple CodeDeploy Applications* — для поділу інстансів потрібні deployment groups, а не окремі застосунки.

</details>

---
