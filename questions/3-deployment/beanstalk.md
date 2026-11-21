# AWS Elastic Beanstalk

**Домен 3 — Розгортання** · питань: 20  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#beanstalk) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q08"></a>
### T01-Q08
*домен: офіційна мітка*

Your organization has set up a full CI/CD pipeline leveraging CodePipeline and the deployment is done on Elastic Beanstalk. This pipeline has worked for over a year now but you are approaching the limits of Elastic Beanstalk in terms of how many versions can be stored in the service.

How can you remove older versions that are not used by Elastic Beanstalk so that new versions can be created for your applications?

- ▫️ Use Worker Environments
- ✅ Use a Lifecycle Policy
- ▫️ Setup an .ebextensions file
- ▫️ Define a Lambda function

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use a Lifecycle Policy»**

Щоразу, коли ти завантажуєш нову версію застосунку через консоль Elastic Beanstalk або EB CLI, створюється application version. Якщо не видаляти непотрібні версії, рано чи пізно ти досягнеш ліміту і не зможеш створювати нові.

Щоб цього уникнути, налаштовують application version lifecycle policy: вона видаляє старі версії (за віком) або зайві, коли їх більше заданої кількості. Політика застосовується щоразу при створенні нової версії, видаляє до 100 версій за раз і не враховує нову версію в ліміт.

**❌ Неправильні варіанти:**
- *Setup an .ebextensions file* — конфігурує середовище та його ресурси, але не керує версіями.
- *Define a Lambda function* — спрацює, але вимагатиме багато ручних скриптів, щоб повторити вбудовану функцію Lifecycle Policy.
- *Use Worker Environments* — призначені для довгих фонових задач і не допомагають керувати версіями.

</details>

---

<a id="t01-q58"></a>
### T01-Q58
*домен: офіційна мітка*

> ⚠️ **EB: Rolling with additional batch, batch=1.** → [детальніше](../../06-contradictions-and-traps.md#c07)

An e-commerce company has deployed its application on AWS Elastic Beanstalk. The Auto Scaling group associated with the Beanstalk environment has three Amazon EC2 instances. When the number of instances falls below two, it severely impacts the performance of the web application. The company currently uses the default all-at-once deployment policy and is looking for an effective strategy for future deployments.

Which of the following represents the most cost-effective deployment strategy for the company?

- ▫️ Opt for rolling deployment strategy. Set the batch size to 2
- ▫️ Opt for traffic-splitting deployment strategy with traffic split parameter set to 50% of the total traffic
- ▫️ Configure an Elastic Load Balancer to front the Auto Scaling Group and choose two different Availability Zones (AZs) for deployment
- ✅ Opt for rolling with additional batch deployment strategy. Set the batch size parameter to 1

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Opt for rolling with additional batch deployment strategy. Set the batch size parameter to 1»**

При rolling-деплої Elastic Beanstalk ділить інстанси на батчі й оновлює їх по черзі; решта тим часом працює на старій версії. Щоб зберегти повну потужність, середовище спершу запускає додатковий батч нових інстансів і лише потім виводить старі з роботи — це **rolling with additional batch**. Після завершення додатковий батч термінується. Розмір батча задають у відсотках від ASG або фіксованою кількістю. Batch size = 1 — найдешевший варіант із повною потужністю.

**❌ Неправильні варіанти:**
- *Traffic-splitting with 50%* — EB запускає повний набір нових інстансів (як при immutable) і частину трафіку направляє на нову версію. Для цього випадку задорого.
- *ELB перед ASG і два AZ* — підвищує стійкість, але не є стратегією деплою. Дистрактор.
- *Rolling with batch size 2* — під час деплою трафік обслуговуватиме лише один інстанс, і продуктивність постраждає.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t01-q62"></a>
### T01-Q62
*домен: офіційна мітка*

You have created a test environment in Elastic Beanstalk and as part of that environment, you have created an RDS database.

How can you make sure the database can be explored after the environment is destroyed?

- ▫️ Change the Elastic Beanstalk environment variables
- ▫️ Make a selective delete in Elastic Beanstalk
- ▫️ Convert the Elastic Beanstalk environment to a worker environment
- ✅ Make a snapshot of the database before it gets deleted

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Make a snapshot of the database before it gets deleted»**

RDS, створена всередині середовища Elastic Beanstalk, підходить для dev і тестування, але не для продакшну: життєвий цикл БД прив'язаний до середовища, і при його видаленні база теж видаляється разом з даними. Єдиний спосіб зберегти дані — зробити snapshot до видалення. Для продакшну БД відокремлюють від середовища (наприклад, через blue/green: нове середовище B підключають до наявної RDS).

**❌ Неправильні варіанти:**
- *Make a selective delete in Elastic Beanstalk* — такої функції немає.
- *Change the Elastic Beanstalk environment variables* — змінні середовища не захистять RDS від видалення.
- *Convert the environment to a worker environment* — конвертувати середовища не можна, лише змінювати їхні налаштування.

</details>

---

<a id="t02-q03"></a>
### T02-Q03
*домен: визначено автоматично (у джерелі: AWS Compute)*

An application on-premises uses Linux servers and a relational database using PostgreSQL. The company will be migrating the application to AWS and require a managed service that will take care of capacity provisioning, load balancing, and auto-scaling.

Which combination of services should the Developer use? (Select TWO.)

- ▫️ Amazon EC2 with Auto Scaling
- ✅ AWS Elastic Beanstalk
- ▫️ AWS Lambda with CloudWatch Events
- ▫️ Amazon EC2 with PostgreSQL
- ✅ Amazon RDS with PostrgreSQL

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «AWS Elastic Beanstalk» та «Amazon RDS with PostgreSQL»**

Потрібен керований сервіс: Elastic Beanstalk для обчислень (сам провізіонує потужності, балансує навантаження, масштабує) і Amazon RDS з рушієм PostgreSQL для БД (масштабування «натисканням кнопки»).

**❌ Неправильні варіанти:**
- *Amazon EC2 with Auto Scaling* — дає масштабування, але це треба керувати самостійно; до того ж немає балансера.
- *Amazon EC2 with PostgreSQL* — це не керований сервіс; БД має бути на RDS.
- *AWS Lambda with CloudWatch Events* — про переписування застосунку під Lambda не йдеться.

Джерела: <https://aws.amazon.com/elasticbeanstalk/> · <https://aws.amazon.com/rds/postgresql/>

</details>

---

<a id="t02-q50"></a>
### T02-Q50
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Документація: 500 MB (у банку 512 MB).** → [детальніше](../../07-outdated-and-wrong-answers.md#o10)

A Developer is creating a new web application that will be deployed using AWS Elastic Beanstalk from the AWS Management Console. The Developer is about to create a source bundle which will be uploaded using the console.

Which of the following are valid requirements for creating the source bundle? (Select TWO.)

- ▫️ Must include a parent folder or top-level directory.
- ✅ Must not include a parent folder or top-level directory.
- ▫️ Must consist of one or more ZIP files.
- ▫️ Must include the cron.yaml file.
- ✅ Must not exceed 512 MB.

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Must not include a parent folder or top-level directory» та «Must not exceed 512 MB»**

Для деплою через консоль Elastic Beanstalk source bundle має:
- складатися з одного ZIP або WAR файлу (усередині ZIP може бути кілька WAR);
- не перевищувати 512 MB (у поточній документації — 500 MB);
- не містити кореневої (батьківської) папки; підкаталоги допускаються.

`cron.yaml` потрібен лише для worker-застосунків з періодичними задачами.

**❌ Неправильні варіанти:**
- *Must include the cron.yaml file* — потрібен не завжди.
- *Must include a parent folder* — навпаки, батьківської папки бути НЕ повинно.
- *Must consist of one or more ZIP files* — лише один ZIP або WAR.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-sourcebundle.html>

</details>

---

<a id="t03-q12"></a>
### T03-Q12
*домен: офіційна мітка*

> ⚠️ **EB: dev, downtime OK → All at once.** → [детальніше](../../06-contradictions-and-traps.md#c07)

You have moved your on-premise infrastructure to AWS and are in the process of configuring an AWS Elastic Beanstalk deployment environment for production, development, and testing. You have configured your production environment to use a rolling deployment to prevent your application from becoming unavailable to users. For the development and testing environment, you would like to deploy quickly and are not concerned about downtime.

Which of the following deployment policies meet your needs?

- ▫️ Immutable
- ✅ All at once
- ▫️ Rolling with additional batches
- ▫️ Rolling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «All at once»**

Найшвидший метод деплою; підходить, якщо короткий простій прийнятний, а важлива швидкість. EB ставить нову версію на всі інстанси одночасно; веб-проксі або сервер застосунку може перезапуститися, тож застосунок на короткий час недоступний.

**❌ Неправильні варіанти:**
- *Rolling* — деплой батчами; більша частина потужності зберігається, повного простою немає, але деплой довший.
- *Rolling with additional batch* — спершу запускається додатковий батч, потужність повністю зберігається, але деплой ще довший.
- *Immutable* — повільніший: нова версія завжди на нових інстансах (друга ASG), з швидким і безпечним rollback.

</details>

---

<a id="t04-q15"></a>
### T04-Q15
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: швидкий rollback → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A company has a website that is developed in PHP and WordPress and is launched using AWS Elastic Beanstalk. There is a new version of the website that needs to be deployed in the Elastic Beanstalk environment. The company cannot tolerate having the website offline if an update fails. Deployments must have minimal impact and rollback as soon as possible.

What deployment method should be used?

- ▫️ Snapshots
- ✅ Immutable
- ▫️ Rolling
- ▫️ All at once

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Immutable»**

Політики деплою Elastic Beanstalk:
- **All at once** — усі інстанси одночасно;
- **Rolling** — батчами, один батч щоразу недоступний;
- **Rolling with additional batch** — як Rolling, але з додатковим батчем для повної доступності;
- **Immutable** — нові інстанси в новій ASG, трафік перемикається, коли вони healthy; zero downtime;
- **Blue/Green** — окреме середовище, zero downtime.

Immutable — найбезпечніший варіант без простою і з простим rollback (досить видалити нові інстанси).

**❌ Неправильні варіанти:**
- *All at once* — повний простій.
- *Snapshots* — не метод деплою EB.
- *Rolling* — знижує потужність, а rollback складніший: доведеться заново розгортати стару версію.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t05-q28"></a>
### T05-Q28
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is planning the deployment of a new version of an application to AWS Elastic Beanstalk. The new version of the application should be deployed only to new EC2 instances.

Which deployment methods will meet these requirements? (Select TWO.)

- ▫️ Rolling
- ✅ Blue/green
- ▫️ Rolling with additional batch
- ✅ Immutable
- ▫️ All at once

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Blue/green» + «Immutable»**

Політики деплою Elastic Beanstalk:
- **All at once** — нова версія на всі інстанси одночасно.
- **Rolling** — оновлює групи інстансів (buckets) по черзі; поки оновлюється одна група, вона недоступна.
- **Rolling with additional batch** — як Rolling, але спочатку запускає додаткову групу, тож повна потужність зберігається.
- **Immutable** — нові інстанси в новій ASG; трафік перемикається, коли вони здорові. Zero downtime.
- **Blue/Green** — окреме нове середовище, потім swap URL. Zero downtime.

Лише Immutable і Blue/Green деплоять оновлення виключно на нові EC2.

**❌ Неправильні варіанти:**
- *All-at-once* — оновлює наявні інстанси.
- *Rolling* — оновлює наявні інстанси.
- *Rolling with additional batch* — запускає нові інстанси, але оновлює й наявні, що за умовою заборонено.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html>

</details>

---

<a id="t05-q47"></a>
### T05-Q47
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: без втрати потужності, найдешевше → Rolling with additional batch.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A Developer has completed some code updates and needs to deploy the updates to an Amazon Elastic Beanstalk environment. The environment includes twelve Amazon EC2 instances and there can be no reduction in application performance and availability during the update.

Which deployment policy is the most cost-effective choice to suit these requirements?

- ▫️ Immutable
- ▫️ Rolling
- ▫️ All at once
- ✅ Rolling with additional batch

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Rolling with additional batch»**

Кожна політика деплою Elastic Beanstalk має свої плюси й мінуси. Rolling with additional batch спочатку додає нову групу інстансів, оновлює її, а потім переходить до наступної групи. Особливості:
- застосунок весь час працює на повній потужності;
- розмір групи можна задати;
- певний час одночасно працюють обидві версії;
- невелика додаткова вартість; зайва група видаляється наприкінці;
- деплой триває довше;
- добре підходить для продакшну.

За умовою продуктивність і доступність не можуть знижуватися, а рішення має бути найдешевшим. Ця політика дає повну доступність за мінімальних витрат, бо додаткову групу можна зробити маленькою.

**❌ Неправильні варіанти:**
- *Rolling* — потужність тимчасово зменшується на одну групу. Добрий вибір, коли важлива швидкість і таке зменшення допустиме.
- *All at once* — повний простій під час оновлення.
- *Immutable* — найдорожчий: подвоює потужність повним набором інстансів у новій ASG.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t06-q49"></a>
### T06-Q49
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: без downtime + легкий rollback → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A company has a production application deployed using AWS Elastic Beanstalk. A new version of the application must be installed, and the company cannot tolerate any website downtime. If the application update fails, rollback should be fast and easy.

What deployment method should be used?

- ▫️ Incremental
- ▫️ Rolling
- ✅ Immutable
- ▫️ All at once

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Immutable»**

Immutable-деплой запускає нові інстанси в новій ASG, розгортає на них нову версію і перемикає трафік, коли вони здорові. Простою немає, а при збої відкат швидкий: нові інстанси просто видаляються.

**❌ Неправильні варіанти:**
- *Rolling* — оновлює групи інстансів по черзі, і кожна група під час деплою виведена з обслуговування. При збої потрібен ще один rolling update для відкату. Для продакшну не ідеально.
- *All at once* — усі інстанси недоступні одночасно; для продакшну не підходить.
- *Incremental* — такої політики деплою в Elastic Beanstalk немає.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environmentmgmt-updates-immutable.html>

</details>

---

<a id="t08-q27"></a>
### T08-Q27
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: пріоритет rollback → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A Developer has completed some code updates and needs to deploy the updates to an Amazon Elastic Beanstalk environment. Due to the criticality of the application, the ability to quickly roll back must be prioritized of any other considerations.

Which deployment policy should the Developer choose?

- ▫️ Rolling with additional batch
- ✅ Immutable
- ▫️ All at once
- ▫️ Rolling

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Immutable»**

Immutable-деплой створює нову ASG з новим набором інстансів і розгортає оновлення туди. Особливості:
- трафік перемикається, коли нові інстанси здорові;
- простою немає;
- дорого: під час оновлення інстансів удвічі більше;
- найдовший деплой;
- **швидкий відкат**, бо старі інстанси лишаються незмінними;
- чудово для продакшну.

Тут швидкий відкат важливіший за все інше, тож Immutable — найкращий вибір, хоч він найдорожчий і найдовший.

**❌ Неправильні варіанти:**
- *Rolling* — при проблемах потрібен ручний повторний деплой.
- *Rolling with additional batch* — при проблемах теж потрібен ручний повторний деплой.
- *All at once* — повний простій і ручний повторний деплой при проблемах.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t08-q33"></a>
### T08-Q33
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to configure an Elastic Load Balancer that is deployed through AWS Elastic Beanstalk. Where should the Developer place the load-balancer.config file in the application source bundle?

- ▫️ In the bin folder
- ▫️ In the root of the source code
- ✅ In the .ebextensions folder
- ▫️ In the load-balancer.config.root

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «In the .ebextensions folder»**

Конфігураційні файли `.ebextensions` у вихідному коді налаштовують середовище Elastic Beanstalk і його ресурси. Наприклад, файл `.ebextensions/load-balancer.config` може змінити тип балансувальника на Network Load Balancer.

Вимоги до файлів:
- **Розташування** — одна папка `.ebextensions` у корені source bundle. Папки з крапкою можуть бути приховані, тож переконайтеся, що вона потрапила в архів.
- **Ім'я** — розширення `.config`.
- **Формат** — YAML або JSON.
- **Унікальність** — кожен ключ лише один раз у файлі.

**❌ Неправильні варіанти:**
- *Root of the source code* — `.config` файли кладуть у `.ebextensions`.
- *bin folder* — `.config` файли кладуть у `.ebextensions`.
- *load-balancer.config.root* — такого розташування не існує.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html>

</details>

---

<a id="t09-q09"></a>
### T09-Q09
*домен: офіційна мітка*

You are a developer working on a web application written in Java and would like to use AWS Elastic Beanstalk for deployment because it would handle deployment, capacity provisioning, load balancing, auto-scaling, and application health monitoring. In the past, you connected to your provisioned instances through SSH to issue configuration commands. Now, you would like a configuration mechanism that automatically applies settings for you.

Which of the following options would help do this?

- ✅ Include config files in .ebextensions/ at the root of your source code
- ▫️ Use an AWS Lambda hook
- ▫️ Deploy a CloudFormation wrapper
- ▫️ Use SSM parameter store as an input to your Elastic Beanstalk Configurations

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Include config files in .ebextensions/ at the root of your source code»**

Секція `option_settings` конфігураційного файлу задає значення опцій конфігурації: середовища Elastic Beanstalk, ресурсів у ньому та ПЗ застосунку. Файли в `.ebextensions/` — один із кількох способів задати ці опції.

**❌ Неправильні варіанти:**
- *CloudFormation wrapper* — такої функції немає.
- *SSM Parameter Store as input* — у логіці тесту не підтримується. ℹ️ Новіші версії Elastic Beanstalk уміють брати змінні середовища із Secrets Manager або Parameter Store, але на іспиті для конфігурації середовища ключ — `.ebextensions`.
- *Lambda hook* — забагато розробки.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions-optionsettings.html>

</details>

---

<a id="t10-q11"></a>
### T10-Q11
*домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: зміна платформи → Blue/Green (CNAME swap).** → [детальніше](../../06-contradictions-and-traps.md#c07)

A media company uses Amazon EC2 instances managed by AWS Elastic Beanstalk to run its high-traffic website. The engineering team needs to introduce a new feature, which requires upgrading the underlying platform to a newer version of Node.js. The deployment of the new code and the platform upgrade need to happen without causing any downtime.

Which strategy should the team adopt to fulfill these requirements?

- ▫️ Create a duplicate EC2 instance manually with the new version of Node.js and the updated code. Test the new instance and replace one of the instances behind the ELB once the testing is successful.
- ▫️ Use AWS CodeDeploy to deploy the new application code first, then manually update Node.js on the Elastic Beanstalk environment.
- ✅ Implement Blue/Green (CNAME Swap) deployment using Elastic Beanstalk. Prepare a separate environment with the new version of Node.js and the new code, and once testing is complete, swap the CNAMEs of the two environments.
- ▫️ Upgrade the platform version of the current Elastic Beanstalk environment, then deploy the new application code. Monitor the application and quickly roll back changes if any issues occur.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement Blue/Green (CNAME Swap) deployment using Elastic Beanstalk. Prepare a separate environment with the new version of Node.js and the new code, and once testing is complete, swap the CNAMEs of the two environments.»**

Blue/Green-деплой:
1. Створюють окреме green-середовище з новою версією застосунку і оновленою платформою (Node.js).
2. Повністю його тестують.
3. Міняють місцями URL середовищ (Swap environment URLs).

Простою немає, а відкат — зворотний swap.

**❌ Неправильні варіанти:**
- *Upgrade the platform in place* — ризик простою під час оновлення, і відкат теж може спричинити простій.
- *Duplicate EC2 manually* — ручний процес, що веде до розбіжностей і менш надійний за інструменти Beanstalk.
- *Rolling deployment, platform later* — оновлення платформи може перезапускати інстанси, тож zero downtime не гарантовано.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.CNAMESwap.html>

</details>

---

<a id="t11-q15"></a>
### T11-Q15
*домен: офіційна мітка*

> ⚠️ **EB: Rolling with additional batch.** → [детальніше](../../06-contradictions-and-traps.md#c07)

As an AWS Certified Developer Associate, you have been asked to create an AWS Elastic Beanstalk environment to handle deployment for an application that has high traffic and high availability needs. You need to deploy the new version using Beanstalk while making sure that performance and availability are not affected.

Which of the following is the MOST optimal way to do this while keeping the solution cost-effective?

- ▫️ Deploy using 'All at once' deployment policy
- ▫️ Deploy using 'Rolling' deployment policy
- ✅ Deploy using 'Rolling with additional batch' deployment policy
- ▫️ Deploy using 'Immutable' deployment policy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy using 'Rolling with additional batch' deployment policy»**

Вибір політики деплою Elastic Beanstalk — це компроміс, що залежить від потреб бізнесу. **Rolling with additional batch** спочатку запускає додаткову групу інстансів, а потім виконує rolling-деплой. Потужність під час деплою не падає, хоч деплой і довший, ніж Rolling. Підходить, коли треба зберегти повну потужність.

Огляд політик: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

**❌ Неправильні варіанти:**
- *Immutable* — повільніше й дорожче: друга ASG з повним набором нових інстансів. Перевага — швидкий безпечний відкат, але тут найкращий баланс дає rolling з додатковою групою.
- *All at once* — найшвидше, але можливий короткий простій.
- *Rolling* — без простою, але потужність падає на одну групу. Трафік високий, тож потрібна повна потужність.

</details>

---

<a id="t11-q23"></a>
### T11-Q23
*домен: офіційна мітка*

When running a Rolling deployment in Elastic Beanstalk environment, only two batches completed the deployment successfully, while rest of the batches failed to deploy the updated version. Following this, the development team terminated the instances from the failed deployment.

What will be the status of these failed instances post termination?

- ✅ Elastic Beanstalk will replace the failed instances with instances running the application version from the most recent successful deployment
- ▫️ Elastic Beanstalk will not replace the failed instances
- ▫️ Elastic Beanstalk will replace the failed instances after the application version to be installed is manually chosen from AWS Console
- ▫️ Elastic Beanstalk will replace the failed instances with instances running the application version from the oldest successful deployment

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Elastic Beanstalk will replace the failed instances with instances running the application version from the most recent successful deployment»**

Під час rolling-деплою Beanstalk відключає інстанси групи від балансувальника, розгортає нову версію і знову їх підключає. З connection draining спершу дочікується завершення наявних з'єднань.

Якщо деплой зірвався після кількох успішних груп:
- завершені групи працюють на новій версії, решта — на старій;
- версію кожного інстансу видно на сторінці health в консолі;
- якщо термінувати інстанси невдалого деплою, Beanstalk замінить їх інстансами з версією **останнього успішного деплою**.

**❌ Неправильні варіанти:**
- *Will not replace* — замінює.
- *Oldest successful deployment* — береться останній успішний деплой.
- *After manual choice in Console* — замінює автоматично.

</details>

---

<a id="t11-q33"></a>
### T11-Q33
*домен: офіційна мітка*

After a test deployment in ElasticBeanstalk environment, a developer noticed that all accumulated Amazon EC2 burst balances were lost.

Which of the following options can lead to this behavior?

- ▫️ The deployment was run as a Rolling deployment, resulting in the resetting of EC2 burst balances
- ✅ The deployment was either run with immutable updates or in traffic splitting mode
- ▫️ The deployment was run as a All-at-once deployment, flushing all the accumulated EC2 burst balances
- ▫️ When a canary deployment fails, it resets the EC2 burst balances to zero

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The deployment was either run with immutable updates or in traffic splitting mode»**

- **Immutable** запускає повний набір нових інстансів з новою версією в окремій ASG поруч зі старими. Так уникають проблем частково завершених rolling-деплоїв.
- **Traffic splitting** — canary-тестування в Beanstalk: так само повний набір нових інстансів, на які на заданий час іде вказаний відсоток трафіку.

Політики, що замінюють **усі** інстанси, втрачають накопичені EC2 burst credits:
- керовані оновлення платформи із заміною інстансів;
- immutable-оновлення;
- деплої з immutable або traffic splitting.

**❌ Неправильні варіанти:**
- *Rolling* — оновлює наявні інстанси групами, тож burst credits не втрачаються.
- *All-at-once* — оновлює наявні інстанси одночасно, credits теж не втрачаються.
- *Failed canary resets balances* — вигадка.

</details>

---

<a id="t11-q47"></a>
### T11-Q47
*домен: офіційна мітка*

A developer has been asked to create a web application to be deployed on EC2 instances. The developer just wants to focus on writing application code without worrying about server provisioning, configuration and deployment.

As a Developer Associate, which AWS service would you recommend for the given use-case?

- ▫️ Serverless Application Model
- ▫️ CodeDeploy
- ▫️ CloudFormation
- ✅ Elastic Beanstalk

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Elastic Beanstalk»**

Elastic Beanstalk легко розгортає й запускає застосунки в хмарі та керує їхнім життєвим циклом з одного місця. Ресурси застосунку об'єднані в середовища, і кожне середовище запускає одну версію застосунку. Під час створення середовища Beanstalk сам виділяє всі ресурси, тож про сервери, налаштування й деплой думати не треба.

Переваги: https://aws.amazon.com/elasticbeanstalk/

**❌ Неправильні варіанти:**
- *CloudFormation* — створює набори ресурсів, але шаблон доводиться писати самому. Як працює: https://aws.amazon.com/cloudformation/
- *CodeDeploy* — деплоїть на інстанс, але сам інстанс не створює.
- *SAM* — для serverless, а тут потрібні EC2.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html> · <https://aws.amazon.com/cloudformation/>

</details>

---

<a id="t11-q57"></a>
### T11-Q57
*домен: офіційна мітка*

> ⚠️ **EB: без додаткових витрат → Rolling.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A company uses Elastic Beanstalk to manage its IT infrastructure on AWS Cloud and it would like to deploy the new application version to the EC2 instances. When the deployment is executed, some instances should serve requests with the old application version, while other instances should serve requests using the new application version until the deployment is completed.

Which deployment meets this requirement without incurring additional costs?

- ▫️ All at once
- ▫️ Rolling with additional batches
- ✅ Rolling
- ▫️ Immutable

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Rolling»**

Elastic Beanstalk бере на себе виділення потужностей, балансування, масштабування й моніторинг. Як працює: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html

**Rolling** деплоїть нову версію групами інстансів. Кожна група на час деплою виводиться з обслуговування, тож потужність падає на розмір групи. Вартість не змінюється, бо нових інстансів немає. Без простою, але деплой довший.

Огляд політик: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

**❌ Неправильні варіанти:**
- *Immutable* — нові інстанси в новій ASG, тож дорожче.
- *All at once* — можливий простій.
- *Rolling with additional batch* — додаткові інстанси, тож дорожче.

</details>

---

<a id="t12-q19"></a>
### T12-Q19
*домен: офіційна мітка*

> ⚠️ **EB: Blue/Green.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A retail company manages its IT infrastructure on AWS Cloud via Elastic Beanstalk. The development team at the company is planning to deploy the next version with MINIMUM application downtime and the ability to rollback quickly in case deployment goes wrong.

As a Developer Associate, which of the following options would you recommend to the development team?

- ▫️ Deploy the new application version using 'All at once' deployment policy
- ▫️ Deploy the new application version using 'Rolling' deployment policy
- ▫️ Deploy the new application version using 'Rolling with additional batch' deployment policy
- ✅ Deploy the new version to a separate environment via Blue/Green Deployment, and then swap Route 53 records of the two environments to redirect traffic to the new version

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy the new version to a separate environment via Blue/Green Deployment, and then swap Route 53 records of the two environments to redirect traffic to the new version»**

З політиками на зразок All at once Elastic Beanstalk оновлює наявні інстанси in-place, і застосунок може ненадовго стати недоступним. Blue/green-деплой цього уникає: нову версію розгортають в окремому середовищі, а потім міняють CNAME середовищ, і трафік миттєво переходить на нову версію. Відкат — такий самий швидкий swap назад.

Огляд політик: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

**❌ Неправильні варіанти:**
- *All at once* — можливий короткий простій.
- *Rolling* — без простою, але відкат — ручним повторним деплоєм.
- *Rolling with additional batch* — повна потужність, але відкат теж ручним повторним деплоєм.

</details>

---
