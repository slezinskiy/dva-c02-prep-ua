# AWS Elastic Beanstalk

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 4  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#beanstalk) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q06"></a>
### T01-Q06
*домен: офіційна мітка*

As part of your video processing application, you are looking to perform a set of repetitive and scheduled tasks asynchronously. Your application is deployed on Elastic Beanstalk.

Which Elastic Beanstalk environment should you set up for performing the repetitive tasks?

- ▫️ Setup a Web Server environment and a cron.yaml file
- ▫️ Setup a Web Server environment and a .ebextensions file
- ✅ Setup a Worker environment and a cron.yaml file
- ▫️ Setup a Worker environment and a .ebextensions file

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Setup a Worker environment and a cron.yaml file»**

Elastic Beanstalk дає змогу швидко розгортати й керувати застосунками, не вникаючи в інфраструктуру: ти завантажуєш застосунок, а EB сам займається провізіонінгом, балансуванням, масштабуванням і моніторингом.

Середовище (environment) — це набір ресурсів AWS, на яких працює певна версія застосунку. Середовище, що забирає задачі з черги Amazon SQS, належить до worker tier. Довгі операції варто виносити у worker environment: так веб-фронтенд лишається чутливим під навантаженням. Для повторюваних задач за розкладом у worker environment потрібен файл **cron.yaml** з описом cron-задач.

**❌ Неправильні варіанти:** будь-які комбінації з Web Server environment або з `.ebextensions`. `.ebextensions` не підходить для опису cron-задач, а Web Server environment не виконує повторювані задачі за розкладом.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-tiers.html>

</details>

---

<a id="t01-q44"></a>
### T01-Q44
*домен: офіційна мітка*

You would like your Elastic Beanstalk environment to expose an HTTPS endpoint instead of an HTTP endpoint to get in-flight encryption between your clients and your web servers.

What must be done to set up HTTPS on Beanstalk?

- ✅ Create a config file in the .ebextensions folder to configure the Load Balancer
- ▫️ Configure Health Checks
- ▫️ Use a separate CloudFormation template to load the SSL certificate onto the Load Balancer
- ▫️ Open up the port 80 for the security group

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a config file in the .ebextensions folder to configure the Load Balancer»**

Найпростіше ввімкнути HTTPS в Elastic Beanstalk — призначити сертифікат балансеру середовища. Якщо балансер термінує HTTPS, з'єднання клієнт–балансер захищене, а між балансером і EC2 лишається HTTP, тож інстанси додатково налаштовувати не треба. HTTPS listener підтримують Classic Load Balancer і ALB.

Приклад `.ebextensions/securelistener-alb.config` для ALB (namespace `aws:elbv2:listener`): listener на порту 443 з протоколом HTTPS і `SSLCertificateArns` (ARN сертифіката ACM).

**❌ Неправильні варіанти:**
- *Use a separate CloudFormation template to load the SSL certificate* — окремий шаблон не може змінювати балансер, яким керує Elastic Beanstalk.
- *Open up port 80* — порт 80 для HTTP.
- *Configure Health Checks* — health checks не мають стосунку до SSL.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https-elb.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https.html>

</details>

---

<a id="t03-q35"></a>
### T03-Q35
*домен: офіційна мітка*

A .NET developer team works with many ASP.NET web applications that use EC2 instances to host them on IIS. The deployment process needs to be configured so that multiple versions of the application can run in AWS Elastic Beanstalk. One version would be used for development, testing, and another version for load testing.

Which of the following methods do you recommend?

- ▫️ You cannot have multiple development environments in Elastic Beanstalk, just one development and one production environment
- ▫️ Create an Application Load Balancer to route based on hostname so you can pass on parameters to the development Elastic Beanstalk environment. Create a file in .ebextensions/ to know how to handle the traffic coming from the ALB
- ✅ Define a dev environment with a single instance and a 'load test' environment that has settings close to production environment
- ▫️ Use only one Beanstalk environment and perform configuration changes using an Ansible script

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Define a dev environment with a single instance and a 'load test' environment that has settings close to production environment»**

Elastic Beanstalk легко створює окремі середовища (dev, test, prod) і деплоїть будь-яку версію в будь-яке з них. Середовища можуть бути довготривалими або тимчасовими; при видаленні конфігурацію можна зберегти. Кілька середовищ для одного застосунку — звичайна практика.

**❌ Неправильні варіанти:**
- *You cannot have multiple development environments* — неправда, середовищ може бути скільки завгодно.
- *One environment + Ansible* — EB сам провізіонує сервери й керує кількома середовищами, це кращий варіант.
- *ALB with host-based routing to one environment* — погана схема: дві версії на тих самих інстансах, і навантажувальний тест заважатиме розробці.

</details>

---

<a id="t11-q04"></a>
### T11-Q04
*домен: офіційна мітка*

You have chosen AWS Elastic Beanstalk to upload your application code and allow it to handle details such as provisioning resources and monitoring.

When creating configuration files for AWS Elastic Beanstalk which naming convention should you follow?

- ✅ .ebextensions/&lt;mysettings&gt;.config
- ▫️ .ebextensions_&lt;mysettings&gt;.config
- ▫️ .config/&lt;mysettings&gt;.ebextensions
- ▫️ .config_&lt;mysettings&gt;.ebextensions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «.ebextensions/<mysettings>.config»**

Конфігураційні файли Elastic Beanstalk налаштовують середовище та його ресурси. Це документи YAML чи JSON з розширенням `.config` у папці `.ebextensions`, яку включають у source bundle.

**❌ Неправильні варіанти:**
- *.ebextensions_<mysettings>.config* — неправильний шлях.
- *.config/<mysettings>.ebextensions* — неправильний шлях.
- *.config_<mysettings>.ebextensions* — неправильний шлях.

</details>

---
