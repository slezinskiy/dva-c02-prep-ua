# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 3 — Розгортання** · питань: 3  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q03"></a>
### T03-Q03
*домен: офіційна мітка*

You have a Java-based application running on EC2 instances loaded with AWS CodeDeploy agents. You are considering different options for deployment, one is the flexibility that allows for incremental deployment of your new application versions and replaces existing versions in the EC2 instances. The other option is a strategy in which an Auto Scaling group is used to perform a deployment.

Which of the following options will allow you to deploy in this manner? (Select two)

- ✅ In-place Deployment
- ✅ Blue/green Deployment
- ▫️ Cattle Deployment
- ▫️ Warm Standby Deployment
- ▫️ Pilot Light Deployment

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «In-place Deployment» та «Blue/green Deployment»**

- **In-place:** на кожному інстансі deployment group застосунок зупиняють, встановлюють нову ревізію, запускають і валідують. З балансером кожен інстанс на час деплою знімається з реєстрації і потім повертається.
- **Blue/green:** створюється новий набір інстансів (зокрема, копія ASG), CodeDeploy ставить на них нову версію і перемикає трафік балансера зі старих інстансів на нові; старі потім можна термінувати.

**❌ Неправильні варіанти:**
- *Cattle Deployment* — такого типу немає (жарт банку).
- *Warm Standby Deployment* — термін з disaster recovery (зменшена копія середовища постійно працює), а не тип деплою CodeDeploy.
- *Pilot Light Deployment* — теж термін DR (реплікується лише ядро сервісів).

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html> · <https://aws.amazon.com/blogs/publicsector/rapidly-recover-mission-critical-systems-in-a-disaster/>

</details>

---

<a id="t06-q47"></a>
### T06-Q47
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company operates a multimedia sharing service on AWS. The service is hosted on Amazon EC2 instances in an Auto Scaling group, serving as the target for an Application Load Balancer (ALB).

The multimedia files are stored in an Amazon S3 bucket. The company is developing a feature for system request testing, which will redirect these requests to a separate target group hosting a test version of the application.

How can this be accomplished most efficiently?

- ▫️ Replicate the existing application and host it on a distinct EC2 instance. Manually route test requests to this instance.
- ▫️ Create a separate AWS Lambda function to handle the test requests and direct them to the new target group.
- ▫️ Create a new S3 bucket to host the test variant of the application. Redirect all test requests to this new bucket.
- ✅ Use ALB content-based routing. Create a separate target group for the test version of the application and route requests identified by a specific cookie.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use ALB content-based routing. Create a separate target group for the test version of the application and route requests identified by a specific cookie.»**

Найефективніше рішення — content-based routing в ALB за cookie. Тестові запити з певним cookie йдуть в окрему target group з тестовою версією застосунку, а наявна конфігурація майже не змінюється.

**❌ Неправильні варіанти:**
- *Replicate on a distinct EC2, manually route* — не використовує маршрутизацію ALB і додає ручну роботу.
- *S3 bucket for test variant* — не використовує ALB та EC2, тож неефективно.
- *Lambda to route test requests* — зайва складність в обхід маршрутизації ALB.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html>

</details>

---

<a id="t07-q55"></a>
### T07-Q55
*домен: офіційна мітка*

Your web application architecture consists of multiple Amazon EC2 instances running behind an Elastic Load Balancer with an Auto Scaling group having the desired capacity of 5 EC2 instances. You would like to integrate AWS CodeDeploy for automating application deployment. The deployment should re-route traffic from your application's original environment to the new environment.

Which of the following options will meet your deployment criteria?

- ▫️ Opt for Rolling deployment
- ▫️ Opt for Immutable deployment
- ✅ Opt for Blue/Green deployment
- ▫️ Opt for In-place deployment

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Opt for Blue/Green deployment»**

Blue/Green-деплой мінімізує перерви під час оновлення: CodeDeploy розгортає нову версію поруч зі старою і лише потім перемикає продакшн-трафік. Як це працює на різних платформах:
- **Lambda** — трафік переходить з однієї версії функції на нову версію тієї самої функції.
- **ECS** — трафік переходить з task set сервісу на новий task set того самого сервісу.
- **EC2/On-Premises** — трафік переходить з початкового набору інстансів на новий.

**❌ Неправильні варіанти:**
- *Rolling* — це політика Elastic Beanstalk, а не CodeDeploy для EC2.
- *Immutable* — теж політика Elastic Beanstalk.
- *In-place* — застосунок на кожному інстансі зупиняють, ставлять нову ревізію і запускають. З load balancer інстанс знімають з обслуговування на час деплою, тож перерви можливі.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html#welcome-deployment-overview-blue-green> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html>

</details>

---
