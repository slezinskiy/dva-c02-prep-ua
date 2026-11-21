# CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify

**Домен 4 — Усунення несправностей та оптимізація** · питань: 5  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#cicd) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q08"></a>
### T03-Q08
*домен: офіційна мітка*

You are working on a project that has over 100 dependencies. Every time your AWS CodeBuild runs a build step it has to resolve Java dependencies from external Ivy repositories which take a long time. Your manager wants to speed this process up in AWS CodeBuild.

Which of the following will help you do this with minimal effort?

- ▫️ Use Instance Store type of EC2 instances to facilitate internal dependency cache
- ✅ Cache dependencies on S3
- ▫️ Ship all the dependencies as part of the source code
- ▫️ Reduce the number of dependencies

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Cache dependencies on S3»**

CodeBuild — керований CI-сервіс: компілює код, запускає тести й створює пакети для деплою без власних серверів збірки. Завантаження залежностей — важлива фаза збірки (від кількох KB до багатьох MB). Більшість залежностей між збірками не змінюється, тож кешування в S3 помітно скорочує час збірки.

**❌ Неправильні варіанти:**
- *Reduce the number of dependencies* — ідеально, але не завжди можливо: застосунку вони потрібні.
- *Ship all the dependencies as part of the source code* — погана практика і довші збірки; незмінні залежності краще кешувати.
- *Instance Store EC2 instances for the dependency cache* — instance store — тимчасове блочне сховище інстансу, для кешу CodeBuild його не використовують.

</details>

---

<a id="t09-q65"></a>
### T09-Q65
*домен: офіційна мітка*

A company uses AWS CodeDeploy to deploy applications from GitHub to EC2 instances running Amazon Linux. The deployment process uses a file called appspec.yml for specifying deployment hooks. A final lifecycle event should be specified to verify the deployment success.

Which of the following hook events should be used to verify the success of the deployment?

- ▫️ ApplicationStart
- ▫️ AfterInstall
- ▫️ AllowTraffic
- ✅ ValidateService

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «ValidateService»**

CodeDeploy автоматизує деплой на EC2, Fargate, Lambda і on-premises. Для EC2/On-Premises кожен hook виконується один раз на інстанс за деплой, і в ньому можна запустити один чи кілька скриптів. `ValidateService` — **останній** hook життєвого циклу, де перевіряють, що деплой пройшов успішно.

**❌ Неправильні варіанти:**
- *AfterInstall* — налаштування застосунку, зміна прав на файли.
- *ApplicationStart* — запуск сервісів, зупинених у `ApplicationStop`.
- *AllowTraffic* — відкриває трафік на інстанси; цей event зарезервований агентом CodeDeploy, скрипти в ньому не запускаються.

</details>

---

<a id="t12-q09"></a>
### T12-Q09
*домен: офіційна мітка*

A developer in your company has configured a build using AWS CodeBuild. The build fails and the developer needs to quickly troubleshoot the issue to see which commands or settings located in the BuildSpec file are causing an issue.

Which approach will help them accomplish this?

- ▫️ Freeze the CodeBuild during its next execution
- ▫️ Enable detailed monitoring
- ✅ Run AWS CodeBuild locally using CodeBuild Agent
- ▫️ SSH into the CodeBuild Docker container

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Run AWS CodeBuild locally using CodeBuild Agent»**

CodeBuild — повністю керований build-сервіс без серверів і ПЗ для налаштування. Local build: вказуєте вихідний код і налаштування, а CodeBuild agent виконує скрипти збирання на вашій машині. Що це дає:
- перевірити `buildspec` локально;
- зібрати й протестувати до commit;
- швидко знаходити й виправляти помилки в локальному середовищі.

**❌ Неправильні варіанти:**
- *SSH into the container* — SSH у контейнер CodeBuild неможливий. ℹ️ Зараз є налагодження через Session Manager: збірка зупиняється командою `codebuild-breakpoint`.
- *Freeze CodeBuild* — збірку можна лише зупинити, а не «заморозити». Див. https://docs.aws.amazon.com/codebuild/latest/userguide/stop-build.html
- *Detailed monitoring* — це функція EC2. CloudTrail для CodeBuild записує лише API-виклики, а не процес збірки.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html> · <https://aws.amazon.com/blogs/devops/announcing-local-build-support-for-aws-codebuild/> · <https://docs.aws.amazon.com/codebuild/latest/userguide/use-codebuild-agent.html>

</details>

---

<a id="t12-q15"></a>
### T12-Q15
*домен: офіційна мітка*

Your organization has developers that merge code changes regularly to an AWS CodeCommit repository. Your pipeline has AWS CodeCommit as the source and you would like to configure a rule that reacts to changes in CodeCommit.

Which of the following options do you choose for this type of integration?

- ▫️ Use Lambda function with Amazon Simple Notification Service (SNS)
- ▫️ Use Lambda Event Rules
- ✅ Use Amazon EventBridge Rules
- ▫️ Use CloudTrail Event rules with Amazon Simple Email Service (SES)

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon EventBridge Rules»**

EventBridge моніторить ресурси й застосунки і реагує на зміни стану pipeline, stage чи action у CodePipeline. За вашими правилами він запускає цілі. Приклади правил:
- зміна стану інстансу EC2 → сповіщення в SNS;
- зміна фази збірки CodeBuild → сповіщення в SNS;
- зміна pipeline → виклик Lambda.

**❌ Неправильні варіанти:**
- *CloudTrail Event rules with SES* — такої функції не існує.
- *Lambda with SNS* — CodePipeline не запускає Lambda напряму для сповіщень; це робиться через EventBridge.
- *Lambda Event Rules* — такої функції не існує.

</details>

---

<a id="t12-q52"></a>
### T12-Q52
*домен: офіційна мітка*

An AWS CodePipeline was configured to be triggered by Amazon EventBridge. Recently the pipeline failed and upon investigation, the Team Lead noticed that the source was changed from AWS CodeCommit to Amazon Simple Storage Service (S3). The Team Lead has requested you to find the user who had made the changes.

Which service will help you solve this?

- ✅ AWS CloudTrail
- ▫️ AWS X-Ray
- ▫️ Amazon Inspector
- ▫️ Amazon CloudWatch

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS CloudTrail»**

CloudTrail потрібен для governance, compliance, операційного аудиту й аудиту ризиків. Він веде історію подій в акаунті: дії в консолі, через SDK, CLI та інші сервіси. З нього видно, який користувач чи акаунт викликав AWS, з якого IP і коли.

Як працює: https://aws.amazon.com/cloudtrail/

**❌ Неправильні варіанти:**
- *CloudWatch* — метрики й події; активність користувачів не логує.
- *X-Ray* — трасування застосунків.
- *Inspector* — оцінка вразливостей і мережевої доступності EC2.

Джерела: <https://aws.amazon.com/xray/> · <https://aws.amazon.com/inspector/> · <https://aws.amazon.com/cloudwatch/>

</details>

---
