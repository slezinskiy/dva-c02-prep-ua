# AWS Lambda

**Домен 3 — Розгортання** · питань: 10  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q21"></a>
### T01-Q21
*домен: офіційна мітка*

> ⚠️ **API Gateway canary.** → [детальніше](../../06-contradictions-and-traps.md#c13)

You are a developer working on AWS Lambda functions that are triggered by Amazon API Gateway and would like to perform testing on a low volume of traffic for new API versions.

Which of the following features will accomplish this task?

- ✅ Canary Deployment
- ▫️ Custom Authorizers
- ▫️ Stage Variables
- ▫️ Mapping Templates

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Canary Deployment»**

При canary release весь трафік API випадково ділиться в заданій пропорції між production release і canary release. Зазвичай canary отримує невеликий відсоток трафіку, решта йде на production. Оновлені функції API бачить лише трафік, що потрапляє на canary. Відсоток можна змінювати, балансуючи між покриттям тестами і продуктивністю.

**❌ Неправильні варіанти:**
- *Stage Variables* — працюють як змінні середовища в налаштуваннях API.
- *Mapping Templates* — скрипти, що перетворюють payload між method request і integration request (і назад для відповіді).
- *Custom Authorizers* — для автентифікації; повертають IAM-політики.

</details>

---

<a id="t02-q45"></a>
### T02-Q45
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **"CloudWatch Events" = EventBridge.** → [детальніше](../../06-contradictions-and-traps.md#c14)

A company has implemented AWS CodePipeline to automate its release pipelines. The Development team is writing an AWS Lambda function that will send notifications for state changes of each of the actions in the stages.

Which steps must be taken to associate the Lambda function with the event source?

- ▫️ Create an Amazon CloudWatch alarm that monitors status changes in CodePipeline and triggers the Lambda function
- ▫️ Create an event trigger and specify the Lambda function from the CodePipeline console
- ▫️ Create a trigger that invokes the Lambda function from the Lambda console by selecting CodePipeline as the event source
- ✅ Create an Amazon CloudWatch Events rule that uses CodePipeline as an event source

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Amazon CloudWatch Events rule that uses CodePipeline as an event source»**

CloudWatch Events (тепер EventBridge) реагують на зміни стану ресурсів AWS: ресурси автоматично надсилають події в потік, а правила вибирають потрібні й направляють, наприклад, у Lambda. CodePipeline може бути джерелом подій, а сповіщення надсилаються, наприклад, через SNS.

**❌ Неправильні варіанти:**
- *Trigger from the Lambda console with CodePipeline as the event source* і *Event trigger from the CodePipeline console* — CodePipeline не налаштовується як тригер Lambda.
- *CloudWatch alarm that monitors status changes* — для змін стану використовують CloudWatch Events, а не alarms.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html>

</details>

---

<a id="t03-q13"></a>
### T03-Q13
*домен: офіційна мітка*

A developer wants a seamless ability to return to older versions of a Lambda function that is being deployed.

Which of the following solutions offers the LEAST operational overhead?

- ✅ Use a Lambda function alias that can point to the different versions
- ▫️ Use Lambda function layers that can point to the different versions
- ▫️ Use a Route 53 weighted policy that can point to the different Lambda function versions
- ▫️ Use CodeDeploy to configure blue/green deployments for the different Lambda function versions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a Lambda function alias that can point to the different versions»**

Версії допомагають керувати деплоєм функцій: наприклад, можна опублікувати нову версію для бета-тестування, не зачіпаючи стабільну продакшн-версію. Кожна публікація створює нову незмінну версію — копію неопублікованого коду й конфігурації.

Alias — вказівник на конкретну версію з власним унікальним ARN. Alias може вказувати лише на версію, а не на інший alias. Щоб повернутися до старої версії, достатньо перенаправити alias.

**❌ Неправильні варіанти:**
- *Route 53 weighted policy pointing to Lambda versions* — дистрактор: weighted policy розподіляє трафік домену між ресурсами, але для версій Lambda не підходить.
- *CodeDeploy blue/green for Lambda versions* — деплой на Lambda і так завжди blue/green (canary/linear/all-at-once), але банк вважає, що повернення до старої версії тут не найпростіше; найменше накладних витрат дає alias.
- *Lambda layers pointing to versions* — layers пакують бібліотеки й залежності, на версії функції вони не вказують.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html#deployment-configuration-lambda> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---

<a id="t04-q11"></a>
### T04-Q11
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A serverless application composed of multiple Lambda functions has been deployed. A developer is setting up AWS CodeDeploy to manage the deployment of code updates. The developer would like a 10% of the traffic to be shifted to the new version in equal increments, 10 minutes apart.

Which setting should be chosen for configuring how traffic is shifted?

- ▫️ All-at-once
- ▫️ Blue/green
- ▫️ Canary
- ✅ Linear

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Linear»**

Deployment configuration — набір правил і умов успіху чи невдачі деплою CodeDeploy. Для Lambda є попередньо визначені конфігурації: canary (дві порції), linear (рівні порції через рівні інтервали), all-at-once. «10% трафіку рівними частками кожні 10 хвилин» — це linear: `CodeDeployDefault.LambdaLinear10PercentEvery10Minutes`.

**❌ Неправильні варіанти:**
- *Canary* — дві порції (наприклад, 10%, потім 90%), а не рівні частки.
- *All-at-once* — весь трафік одразу.
- *Blue/green* — тип деплою, а не налаштування перемикання трафіку.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html>

</details>

---

<a id="t04-q52"></a>
### T04-Q52
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Lambda alias 10%.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A Developer wants the ability to roll back to a previous version of an AWS Lambda function in the event of errors caused by a new deployment.

How can the Developer achieve this with MINIMAL impact on users?

- ▫️ Change the application to use the $LATEST version. Update and save code. If too many errors are encountered, modify and save the code
- ✅ Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to direct 10% of users to the newly deployed version. If too many errors are encountered, send 100% of traffic to the previous version
- ▫️ Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to use the newly deployed version. If too many errors are encountered, point the alias back to the previous version
- ▫️ Change the application to use a version ARN that points to the latest published version. Deploy the new version of the code. Update the application to point to the ARN of the new version of the code. If too many errors are encountered, point the application back to the ARN of the previous version

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to direct 10% of users to the newly deployed version. If too many errors are encountered, send 100% of traffic to the previous version»**

Alias Lambda — вказівник на конкретну версію; застосунок викликає функцію за ARN alias. Alias можна перенаправити на іншу версію або вказати дві версії з вагами, щоб частина трафіку йшла на нову. 10% на нову версію — мінімальний вплив на користувачів, а rollback простий: змінити вказівник alias.

**❌ Неправильні варіанти:**
- *Update the alias to use the new version (100%), roll back if errors* — усі користувачі одразу потраплять на нову версію; при помилках постраждає більше людей.
- *Use a version ARN in the application* — при кожному деплої доведеться змінювати застосунок; alias цього уникає.
- *Use $LATEST and modify code on errors* — суперечить best practice: `$LATEST` — неопублікована версія для змін; для деплою публікують версію.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html>

</details>

---

<a id="t06-q03"></a>
### T06-Q03
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Aliases + versions.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A developer is creating a serverless web application that includes AWS Lambda functions and a REST API deployed using Amazon API Gateway. The developer maintains multiple branches of code. The developer wants to avoid updating the API gateway target endpoint when a new code push is performed.

What solution would allow the developer to update the Lambda code without needing to update the REST API configuration?

- ▫️ Create multiple private API endpoints and use CNAMEs.
- ▫️ Create different tags for each Lambda function.
- ✅ Create aliases and versions in AWS Lambda.
- ▫️ Create multiple stages and deployments.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create aliases and versions in AWS Lambda.»**

Alias Lambda — це вказівник на конкретну версію функції, і до версії звертаються за ARN alias. Нові версії можна випускати, не змінюючи alias, який викликають застосунки. REST API налаштовують на aliases. Можна також зробити різні stages з різними endpoints, що використовують різні aliases: тоді іншу версію застосунку викликають, змінивши ім'я stage в URL.

**❌ Неправильні варіанти:**
- *Multiple stages and deployments* — stages і stage variables можуть посилатися на функції чи aliases. Але без aliases endpoint у REST API довелося б оновлювати з кожною новою версією.
- *Tags* — тегами не визначити Lambda-endpoints у REST API.
- *Multiple private API endpoints + CNAMEs* — це були б зовсім різні API; сенсу немає.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html>

</details>

---

<a id="t08-q13"></a>
### T08-Q13
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Lambda alias + traffic shifting.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A Developer has updated an AWS Lambda function and published a new version. To ensure the code is working as expected the Developer needs to initially direct a percentage of traffic to the new version and gradually increase this over time. It is important to be able to rollback if there are any issues reported.

What is the BEST way the Developer can implement the migration to the new version SAFELY?

- ▫️ Use an immutable update with a new ASG to deploy the new version in parallel, following testing cutover to the new version
- ▫️ Use an Amazon Elastic Load Balancer to direct a percentage of traffic to each target group containing the Lambda function versions
- ✅ Create an Alias, assign the current and new versions and use traffic shifting to assign a percentage of traffic to the new version
- ▫️ Create an Amazon Route 53 weighted routing policy pointing to the current and new versions, assign a lower weight to the new version

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Alias, assign the current and new versions and use traffic shifting to assign a percentage of traffic to the new version»**

Alias Lambda — вказівник на версію функції з власним унікальним ARN. Він вказує лише на версію, не на інший alias, і його можна перенаправити на нову версію. Traffic shifting (weighted alias) направляє заданий відсоток трафіку на нову версію. Це рекомендований спосіб тестувати оновлення на частині трафіку.

**❌ Неправильні варіанти:**
- *Route 53 weighted routing* — endpoints Lambda не є DNS-іменами, які можна маршрутизувати через Route 53.
- *Immutable update with ASG* — це політика Elastic Beanstalk, яка не деплоїть Lambda.
- *ELB with target groups per version* — зайвий шар, складність і вартість; alias простіший.

Джерела: <https://docs.amazonaws.cn/en_us/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/aliases-intro.html> · <https://docs.aws.amazon.com/lambda/latest/dg/lambda-traffic-shifting-using-aliases.html>

</details>

---

<a id="t08-q65"></a>
### T08-Q65
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Developer is deploying an update to a serverless application that includes AWS Lambda using the AWS Serverless Application Model (SAM). The traffic needs to move from the old Lambda version to the new Lambda version gradually, within the shortest period of time.

Which deployment configuration is MOST suitable for these requirements?

- ▫️ CodeDeployDefault.HalfAtATime
- ▫️ CodeDeployDefault.LambdaLinear10PercentEvery2Minutes
- ✅ CodeDeployDefault.LambdaCanary10Percent5Minutes
- ▫️ CodeDeployDefault.LambdaLinear10PercentEvery1Minute

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CodeDeployDefault.LambdaCanary10Percent5Minutes»**

SAM має вбудовану інтеграцію з CodeDeploy для поступових деплоїв Lambda. Кількома рядками конфігурації SAM:
- деплоїть нові версії й автоматично створює alias на нову версію;
- поступово переводить трафік і дозволяє відкотитися;
- запускає pre-traffic і post-traffic hooks для перевірки;
- відкочує деплой, якщо спрацювали CloudWatch alarms.

Способи перемикання трафіку:
- **Canary** — дві порції: заданий відсоток, а через N хвилин решта;
- **Linear** — рівні порції через рівні інтервали;
- **All-at-once** — увесь трафік одразу.

`LambdaCanary10Percent5Minutes` дає 10%, а через 5 хвилин решту. Весь деплой займає 5 хвилин.

**❌ Неправильні варіанти:**
- *HalfAtATime* — конфігурація для EC2 і on-premises, не для Lambda.
- *LambdaLinear10PercentEvery1Minute* — займе 10 хвилин.
- *LambdaLinear10PercentEvery2Minutes* — займе 20 хвилин.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/automating-updates-to-serverless-apps.html>

</details>

---

<a id="t10-q13"></a>
### T10-Q13
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

> ⚠️ **Alias = статичний ARN.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A Development team are deploying an AWS Lambda function that will be used by a production application. The function code will be updated regularly, and new versions will be published. The development team do not want to modify application code to point to each new version.

How can the Development team setup a static ARN that will point to the latest published version?

- ▫️ Use an unqualified ARN
- ▫️ Publish a mutable version and point it to the $LATEST version
- ▫️ Setup a Route 53 Alias record that points to the published version
- ✅ Setup an Alias that will point to the latest version

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Setup an Alias that will point to the latest version»**

Alias Lambda — вказівник на конкретну версію, і функцію викликають за ARN alias. Коли виходить нова версія, код застосунку не змінюють, а лише перенаправляють alias. Alias може вказувати на дві версії з вагами, що зручно для тестування нового коду.

**❌ Неправильні варіанти:**
- *Mutable version pointing to $LATEST* — опубліковані версії незмінні.
- *Unqualified ARN* — ARN без версії вказує на `$LATEST`, а не на опубліковану версію.
- *Route 53 Alias to Lambda* — Route 53 Alias-запис не може вказувати на Lambda.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html>

</details>

---

<a id="t10-q52"></a>
### T10-Q52
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Окремий stage для beta.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A software organization has developed a new feature in its serverless application hosted on AWS. This feature involves an AWS Lambda function that gets invoked by an Amazon API Gateway API. Currently, the API uses a specific Lambda alias to invoke the Lambda function. The organization wants to roll out this new feature to a select group of users for beta testing without affecting the application's existing users.

What would be the most efficient approach to meet these requirements?

- ▫️ Use AWS CodeDeploy to deploy the updated Lambda function. Split traffic between the new and old versions of the function for testing purposes.
- ▫️ Implement Amazon S3 bucket versioning on the Lambda function code. For testing purposes, link the API Gateway to the new version of the code stored in the S3 bucket.
- ✅ Create a new version of the Lambda function. Build a new stage on API Gateway integrated with this new Lambda version. Utilize this new API Gateway stage for beta testing.
- ▫️ Use the AWS CLI to manually switch between the old and new versions of the Lambda function during testing periods.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a new version of the Lambda function. Build a new stage on API Gateway integrated with this new Lambda version. Utilize this new API Gateway stage for beta testing.»**

Опубліковану версію Lambda змінити не можна, тож вона гарантує стабільність між деплоями. Stages API Gateway можуть спрямовувати запити на різні версії Lambda. Окремий stage для бета-тестування, прив'язаний до нової версії, не зачіпає звичайних користувачів, які працюють зі старим stage.

**❌ Неправильні варіанти:**
- *S3 versioning of Lambda code* — версіонування S3 не пов'язане з Lambda чи API Gateway; так вибрати версію функції неможливо.
- *CodeDeploy traffic splitting* — частина звичайних користувачів побачить бета-функції.
- *Manual CLI switching* — трудомістко, легко помилитися, і це зачепить користувачів.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html>

</details>

---
