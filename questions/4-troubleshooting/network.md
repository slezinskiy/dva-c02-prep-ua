# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 4 — Усунення несправностей та оптимізація** · питань: 26  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q32"></a>
### T01-Q32
*домен: офіційна мітка*

You are responsible for an application that runs on multiple Amazon EC2 instances. In front of the instances is an Internet-facing load balancer that takes requests from clients over the internet and distributes them to the EC2 instances. A health check is configured to ping the index.html page found in the root directory for the health status. When accessing the website via the internet visitors of the website receive timeout errors.

What should be checked first to resolve the issue?

- ✅ Security Groups
- ▫️ IAM Roles
- ▫️ The application is down
- ▫️ The ALB is warming up

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Security Groups»**

Security group — віртуальний фаєрвол EC2-інстансу: inbound-правила контролюють вхідний трафік, outbound — вихідний. Таймаути найчастіше означають заблокований доступ, тож перевір, чи є правило, що дозволяє вхідний трафік на потрібний порт.

**❌ Неправильні варіанти:**
- *IAM Roles* — через ролі бувають помилки авторизації API, а не таймаути.
- *The application is down* — можливо, але таймаути зазвичай спричинені заблокованим фаєрволом. Health check тут показує, що все гаразд.
- *The ALB is warming up* — у ALB є slow start mode, але це не причина таймаутів.

</details>

---

<a id="t01-q36"></a>
### T01-Q36
*домен: офіційна мітка*

You are working with a t2.small instance bastion host that has the AWS CLI installed to help manage all the AWS services installed on it. You would like to know the security group and the instance id of the current instance.

Which of the following will help you fetch the needed information?

- ▫️ Query the user data at http://169.254.169.254/latest/user-data
- ✅ Query the metadata at http://169.254.169.254/latest/meta-data
- ▫️ Query the user data at http://254.169.254.169/latest/meta-data
- ▫️ Create an IAM role and attach it to your EC2 instance that helps you perform a 'describe' API call

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Query the metadata at http://169.254.169.254/latest/meta-data»**

Метадані доступні прямо із запущеного інстансу, тож консоль чи CLI не потрібні. Це зручно для скриптів на інстансі, наприклад щоб дізнатися локальну IP, instance id чи security groups. Адреса 169.254.169.254 — link-local, працює лише зсередини інстансу; метадані повертаються як текст (text/plain).

**❌ Неправильні варіанти:**
- *Create an IAM role … to perform a 'describe' API call* — `describe-instances` потребує instance ID, якого ми не знаємо.
- *Query the user data at …/latest/user-data* — повертає user data, задані при запуску, а не метадані.
- *…http://254.169.254.169/latest/meta-data* — неправильна IP-адреса.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html> · <https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-instances.html>

</details>

---

<a id="t02-q55"></a>
### T02-Q55
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer must identify the public IP addresses of clients connecting to Amazon EC2 instances behind a public Application Load Balancer (ALB). The EC2 instances run an HTTP server that logs all requests to a log file.

How can the developer ensure the client public IP addresses are captured in the log files on the EC2 instances?

- ▫️ Install the Amazon CloudWatch Logs agent on the EC2 instances and configure logging.
- ▫️ Install the AWS X-Ray daemon on the EC2 instances and configure request logging.
- ▫️ Configure the HTTP server to add the x-forwarded-proto request header to the logs.
- ✅ Configure the HTTP server to add the x-forwarded-for request header to the logs.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure the HTTP server to add the x-forwarded-for request header to the logs»**

Заголовок `X-Forwarded-For` балансер (HTTP/HTTPS) додає автоматично. Через балансер у логах сервера видно лише його IP, а справжню IP клієнта містить `X-Forwarded-For`. Треба налаштувати HTTP-сервер, щоб він писав цей заголовок у логи.

**❌ Неправильні варіанти:**
- *x-forwarded-proto* — визначає протокол (HTTP чи HTTPS), а не IP.
- *X-Ray daemon* — для трасування, IP клієнтів не дає.
- *CloudWatch Logs agent* — пересилає логи в CloudWatch, але IP клієнтів у них від цього не з'являться.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/x-forwarded-headers.html#x-forwarded-for>

</details>

---

<a id="t03-q46"></a>
### T03-Q46
*домен: офіційна мітка*

> ⚠️ **Сесії між blue/green → зовнішнє сховище (ElastiCache).** → [детальніше](../../06-contradictions-and-traps.md#c01)

An IT company uses a blue/green deployment policy to provision new Amazon EC2 instances in an Auto Scaling group behind a new Application Load Balancer for each new application version. The current set up requires the users to log in after every new deployment.

As a Developer Associate, what advice would you give to the company for resolving this issue?

- ▫️ Use rolling updates instead of a blue/green deployment
- ▫️ Enable sticky sessions in the Application Load Balancer
- ▫️ Use multicast to replicate session information
- ✅ Use ElastiCache to maintain user sessions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use ElastiCache to maintain user sessions»**

ElastiCache — керовані in-memory сховища з високою пропускною здатністю й низькою затримкою; популярні для кешування, сесій, ігор, геосервісів, real-time аналітики. Щоб сесії були доступні з будь-якого веб-сервера, їх виносять в окреме key-value сховище (Redis або Memcached через ElastiCache). Тоді нові інстанси після blue/green-деплою бачать ті самі сесії.

**❌ Неправильні варіанти:**
- *Rolling updates instead of blue/green* — при rolling батч від'єднується від балансера на час деплою, і сесії на цих інстансах теж губляться.
- *Sticky sessions in the ALB* — за умовою ALB замінюється при кожному деплої, тож stickiness не допоможе.
- *Multicast to replicate session information* — дистрактор.

Джерела: <https://aws.amazon.com/caching/session-management/> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html#environments-cfg-rollingdeployments-method>

</details>

---

<a id="t07-q01"></a>
### T07-Q01
*домен: офіційна мітка*

You have an Auto Scaling group configured to a minimum capacity of 1 and a maximum capacity of 5, designed to launch EC2 instances across 3 Availability Zones. During a low utilization period, an entire Availability Zone went down and your application experienced downtime.

What can you do to ensure that your application remains highly available?

- ▫️ Enable RDS Multi-AZ
- ▫️ Change the scaling metric of auto-scaling policy to network bytes
- ✅ Increase the minimum instance capacity of the Auto Scaling Group to 2
- ▫️ Configure ASG fast failover

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Increase the minimum instance capacity of the Auto Scaling Group to 2»**

Розмір ASG задають через minimum, maximum і desired capacity. Minimum і maximum обов'язкові, а desired, якщо його не вказано, дорівнює minimum. З minimum = 1 працював лише один інстанс в одній AZ, і коли ця AZ впала, застосунок впав разом з нею. З minimum = 2 ASG розподілить інстанси по двох AZ, і застосунок стане високодоступним.

**❌ Неправильні варіанти:**
- *Scaling metric = network bytes* — метрика масштабування не розподіляє інстанси по AZ. Для високої доступності потрібна правильна мінімальна кількість інстансів.
- *ASG fast failover* — такої функції не існує.
- *RDS Multi-AZ* — робить високодоступною базу даних, але не застосунок: для нього потрібно кілька інстансів у різних AZ.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-capacity-limits.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-maintain-instance-levels.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html>

</details>

---

<a id="t07-q08"></a>
### T07-Q08
*домен: офіційна мітка*

A developer from your team has configured the load balancer to route traffic equally between instances or across Availability Zones. However, Elastic Load Balancing (ELB) routes more traffic to one instance or Availability Zone than the others.

Why is this happening and how can it be fixed? (Select two)

- ▫️ After you disable an Availability Zone, the targets in that Availability Zone remain registered with the load balancer, thereby receiving random bursts of traffic
- ✅ Instances of a specific capacity type aren’t equally distributed across Availability Zones
- ▫️ There could be short-lived TCP connections between clients and instances
- ▫️ For Application Load Balancers, cross-zone load balancing is disabled by default
- ✅ Sticky sessions are enabled for the load balancer

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Instances of a specific capacity type aren’t equally distributed across Availability Zones» + «Sticky sessions are enabled for the load balancer»**

Обидві причини можуть пояснити нерівномірний розподіл трафіку.

**Sticky sessions** спрямовують запити клієнта до того самого таргету, що корисно для серверів зі станом сесії. Клієнт має підтримувати cookies. Як це працює:
1. На перший запит ALB вибирає таргет і створює зашифрований cookie `AWSALB` з інформацією про нього.
2. Клієнт надсилає цей cookie в наступних запитах.
3. ALB бачить cookie і спрямовує запит на той самий таргет.

Для duration-based stickiness задайте розумний час життя cookie. Якщо stickiness керує застосунок, по можливості використовуйте session cookies, а не persistent.

**Різна потужність інстансів.** Classic Load Balancer з HTTP/HTTPS може скеровувати більше трафіку на потужніші інстанси, щоб слабші не перевантажувалися. Найкраще використовувати однакові типи й конфігурації. Дисбаланс буває і тоді, коли інстанси однакової потужності працюють на різних AMI.

**❌ Неправильні варіанти:**
- *Short-lived TCP connections* — дисбаланс спричиняють навпаки **довгоживучі** з'єднання: нові інстанси довше набирають навантаження.
- *ALB cross-zone disabled by default* — для ALB cross-zone load balancing завжди увімкнено.
- *Disabled AZ targets get random bursts* — таргети у вимкненій AZ лишаються зареєстрованими, але трафік на них не йде.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#sticky-sessions> · <https://aws.amazon.com/premiumsupport/knowledge-center/elb-fix-unequal-traffic-routing/> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html#availability-zones>

</details>

---

<a id="t07-q14"></a>
### T07-Q14
*домен: офіційна мітка*

> ⚠️ **Тут sticky sessions — ПРИЧИНА проблеми, а не найкраща архітектура.** → [детальніше](../../06-contradictions-and-traps.md#c01)

You are running a cloud file storage website with an Internet-facing Application Load Balancer, which routes requests from users over the internet to 10 registered Amazon EC2 instances. Users are complaining that your website always asks them to re-authenticate when they switch pages. You are puzzled because this behavior is not seen in your local machine or dev environment.

What could be the reason?

- ▫️ The Load Balancer does not have TLS enabled
- ▫️ The EC2 instances are logging out the users because the instances never have access to the client IPs because of the Load Balancer
- ✅ The Load Balancer does not have stickiness enabled
- ▫️ Application Load Balancer is in slow-start mode, which gives ALB a little more time to read and write session data

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The Load Balancer does not have stickiness enabled»**

Sticky sessions спрямовують запити клієнта до того самого таргету. Це потрібно серверам, що зберігають стан сесії, і клієнт має підтримувати cookies. На перший запит ALB вибирає таргет, створює зашифрований cookie `AWSALB` і повертає його клієнту. У наступних запитах клієнт надсилає cookie, і ALB спрямовує їх на той самий таргет.

Без stickiness кожен запит може потрапити на інший інстанс, де немає сесії, тож користувача «розлогінює».

Детальніше: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#sticky-sessions

ℹ️ Питають про **причину** проблеми. Як **найкращу архітектуру** обрали б винесення сесій у ElastiCache або DynamoDB. Див. 06.

**❌ Неправильні варіанти:**
- *Slow-start mode* — дає новим таргетам час «прогрітися» перед повним навантаженням; сесіями не керує.
- *Instances don't see client IPs* — IP клієнта передається в `X-Forwarded-For`, і причина не в цьому.
- *TLS not enabled* — TLS шифрує з'єднання, а сесіями не керує.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#sticky-sessions> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#slow-start-mode>

</details>

---

<a id="t07-q22"></a>
### T07-Q22
*домен: офіційна мітка*

A telecommunications company that provides internet service for mobile device users maintains over 100 c4.large instances in the us-east-1 region. The EC2 instances run complex algorithms. The manager would like to track CPU utilization of the EC2 instances as frequently as every 10 seconds.

Which of the following represents the BEST solution for the given use-case?

- ▫️ Simply get it from the CloudWatch Metrics
- ▫️ Enable EC2 detailed monitoring
- ✅ Create a high-resolution custom metric and push the data using a script triggered every 10 seconds
- ▫️ Open a support ticket with AWS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a high-resolution custom metric and push the data using a script triggered every 10 seconds»**

High-resolution custom metrics публікуються з роздільністю 1 с, і їх видно через кілька секунд після публікації. High-resolution alarms оцінюють метрику з періодом 10 с, тож реагувати можна швидше. Доступні ті самі дії, що й для стандартних хвилинних alarms.

**❌ Неправильні варіанти:**
- *EC2 detailed monitoring* — лише 1 хвилина замість 5 і за додаткову плату; цього недостатньо.
- *Simply get it from CloudWatch Metrics* — стандартні метрики мають інтервал 5 хв (basic) або 1 хв (detailed).
- *Support ticket* — до задачі не стосується.

</details>

---

<a id="t09-q15"></a>
### T09-Q15
*домен: офіційна мітка*

You create an Auto Scaling group to work with an Application Load Balancer. The scaling group is configured with a minimum size value of 5, a maximum value of 20, and the desired capacity value of 10. One of the 10 EC2 instances has been reported as unhealthy.

Which of the following actions will take place?

- ▫️ The ASG will detach the EC2 instance from the group, and leave it running
- ▫️ The ASG will format the root EBS drive on the EC2 instance and run the User Data again
- ▫️ The ASG will keep the instance running and re-start the application
- ✅ The ASG will terminate the EC2 Instance

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The ASG will terminate the EC2 Instance»**

Щоб підтримувати задану кількість інстансів, EC2 Auto Scaling періодично перевіряє їх здоров'я. Нездоровий інстанс він термінує однією scaling activity, а наступною запускає новий на заміну.

**❌ Неправильні варіанти:**
- *Detach and leave running* — мета ASG — замінити поганий інстанс.
- *Keep running and restart the app* — ASG не керує вашим застосунком.
- *Format the root EBS and run User Data again* — ASG такого не робить, а User Data виконується лише при першому запуску.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/auto-scaling-terminate-instance> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-maintain-instance-levels.html#replace-unhealthy-instance>

</details>

---

<a id="t09-q33"></a>
### T09-Q33
*домен: офіційна мітка*

A company is using a Border Gateway Protocol (BGP) based AWS VPN connection to connect from its on-premises data center to Amazon EC2 instances in the company’s account. The development team can access an EC2 instance in subnet A but is unable to access an EC2 instance in subnet B in the same VPC.

Which logs can be used to verify whether the traffic is reaching subnet B?

- ▫️ Subnet logs
- ▫️ BGP logs
- ✅ VPC Flow Logs
- ▫️ VPN logs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «VPC Flow Logs»**

VPC Flow Logs записують інформацію про IP-трафік мережевих інтерфейсів у VPC. Дані публікуються в CloudWatch Logs або S3. Flow log створюють для VPC, підмережі або окремого інтерфейсу; для VPC чи підмережі моніториться кожен інтерфейс у ній. Записи — це лог-події з полями, що описують потік трафіку.

Під час створення вказують:
- ресурс;
- тип трафіку: accepted, rejected або all;
- куди публікувати.

**❌ Неправильні варіанти:**
- *VPN logs* — такої функції немає.
- *Subnet logs* — такої функції немає.
- *BGP logs* — такої функції немає.

</details>

---

<a id="t09-q37"></a>
### T09-Q37
*домен: офіційна мітка*

While troubleshooting, a developer realized that the Amazon EC2 instance is unable to connect to the Internet using the Internet Gateway.

Which conditions should be met for Internet connectivity to be established? (Select two)

- ▫️ The instance's subnet is associated with multiple route tables with conflicting configurations
- ✅ The network ACLs associated with the subnet must have rules to allow inbound and outbound traffic
- ▫️ The instance's subnet is not associated with any route table
- ▫️ The subnet has been configured to be Public and has no access to the internet
- ✅ The route table in the instance’s subnet should have a route to an Internet Gateway

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The network ACLs associated with the subnet must have rules to allow inbound and outbound traffic» + «The route table in the instance’s subnet should have a route to an Internet Gateway»**

- **NACL** підмережі має дозволяти вхідний і вихідний трафік на портах 80 (HTTP) і 443 (HTTPS). Без цього Internet Gateway не працюватиме.
- **Route table** підмережі містить маршрути, що визначають, куди йде трафік. Для доступу в інтернет потрібен маршрут на Internet Gateway (`0.0.0.0/0 → igw-…`).

**❌ Неправильні варіанти:**
- *Subnet not associated with any route table* — підмережа без явної асоціації неявно використовує main route table, тож таблиця маршрутів у неї є завжди.
- *Multiple route tables* — підмережа може мати лише одну route table одночасно.
- *Public subnet without internet* — публічна підмережа за визначенням має доступ в інтернет через IGW.

</details>

---

<a id="t09-q47"></a>
### T09-Q47
*домен: офіційна мітка*

A Developer is configuring Amazon EC2 Auto Scaling group to scale dynamically.

Which metric below is NOT part of Target Tracking Scaling Policy?

- ▫️ ASGAverageNetworkOut
- ▫️ ALBRequestCountPerTarget
- ▫️ ASGAverageCPUUtilization
- ✅ ApproximateNumberOfMessagesVisible

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «ApproximateNumberOfMessagesVisible»**

Питання на пошук метрики, яка **не підходить** для target tracking. `ApproximateNumberOfMessagesVisible` — метрика черги SQS, і кількість повідомлень не змінюється пропорційно розміру ASG, яка їх обробляє. Тому для target tracking її напряму не використовують. ℹ️ Рекомендований підхід — метрика backlog per instance: повідомлення ÷ кількість інстансів.

Target tracking: ви вибираєте метрику й цільове значення, а ASG сама створює alarms і рахує коригування. Політика масштабує вгору, коли метрика вища за ціль.

**❌ Інші варіанти — це наперед визначені метрики target tracking:**
- *ASGAverageCPUUtilization* — середнє завантаження CPU групи.
- *ASGAverageNetworkOut* — середня кількість байтів, надісланих усіма мережевими інтерфейсами групи.
- *ALBRequestCountPerTarget* — кількість запитів на ціль у target group ALB.

</details>

---

<a id="t09-q50"></a>
### T09-Q50
*домен: офіційна мітка*

A junior developer has been asked to configure access to an Amazon EC2 instance hosting a web application. The developer has configured a new security group to permit incoming HTTP traffic from 0.0.0.0/0 and retained any default outbound rules. A custom Network Access Control List (NACL) connected with the instance's subnet is configured to permit incoming HTTP traffic from 0.0.0.0/0 and retained any default outbound rules.

Which of the following solutions would you suggest if the EC2 instance needs to accept and respond to requests from the internet?

- ✅ An outbound rule must be added to the Network ACL (NACL) to allow the response to be sent to the client on the ephemeral port range
- ▫️ An outbound rule on the security group has to be configured, to allow the response to be sent to the client on the HTTP port
- ▫️ The configuration is complete on the EC2 instance for accepting and responding to requests
- ▫️ Outbound rules need to be configured both on the security group and on the NACL for sending responses to the Internet Gateway

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «An outbound rule must be added to the Network ACL (NACL) to allow the response to be sent to the client on the ephemeral port range»**

- **Security groups — stateful**: якщо вхідний трафік дозволено, відповідь пропускається автоматично.
- **NACL — stateless**: треба явно дозволити і вхід, і вихід. Кастомний NACL за замовчуванням блокує все.

Щоб сервіс на інстансі працював, NACL має дозволяти:
1. вхідний трафік на порт сервісу;
2. вихідний трафік на ephemeral-порти (1024–65535).

Клієнт підключається з випадкового ephemeral-порту, і відповідь сервісу йде саме на нього, тож вихід на ці порти треба дозволити в NACL.

**❌ Неправильні варіанти:**
- *Configuration complete* — бракує вихідного правила в NACL.
- *Outbound rule on the security group* — SG stateful, відповіді пропускаються автоматично.
- *Outbound rules on both SG and NACL* — для SG вихідне правило для відповідей не потрібне.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/resolve-connection-sg-acl-inbound/> · <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports>

</details>

---

<a id="t09-q60"></a>
### T09-Q60
*домен: офіційна мітка*

A business hosts its website on Amazon EC2 instances and employs Auto Scaling to adjust its resources according to traffic spikes. However, users globally report slow loading times because static content hosted on the EC2 instances takes too long to load, even outside of busy periods.

What pair of actions should be taken to improve the latency of the website? (Select two)

- ▫️ Migrate the application to AWS Lambda
- ✅ Transfer the application’s static content hosted on EC2 instances to Amazon S3
- ▫️ Double the Auto Scaling group’s desired capacity
- ✅ Set up an Amazon CloudFront distribution to cache the static content with Amazon S3 configured as the origin
- ▫️ Upgrade the CPU and RAM available to the EC2 instances

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Transfer the application’s static content hosted on EC2 instances to Amazon S3» + «Set up an Amazon CloudFront distribution to cache the static content with Amazon S3 configured as the origin»**

CloudFront пришвидшує доставку статичного й динамічного контенту (.html, .css, .js, зображення) через світову мережу edge-локацій: запит іде в найближчу локацію з найменшою затримкою. Рішення:
1. Перенести статичний контент з EC2 у S3.
2. Зробити S3 origin для CloudFront.

Origin зберігає оригінальну версію об'єктів: для HTTP це бакет S3 або HTTP-сервер.

**❌ Неправильні варіанти:**
- *Upgrade CPU/RAM* — контент повільний навіть поза піковими годинами, тож проблема в мережевій затримці, а не в залізі.
- *Double desired capacity* — та сама причина: затримка мережева, кількість інстансів не допоможе.
- *Migrate to Lambda* — Lambda не зберігає статичний контент.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/GettingStarted.SimpleDistribution.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html>

</details>

---

<a id="t11-q09"></a>
### T11-Q09
*домен: офіційна мітка*

An organization has offices across multiple locations and the technology team has configured an Application Load Balancer across targets in multiple Availability Zones. The team wants to analyze the incoming requests for latencies and the client's IP address patterns.

Which feature of the Load Balancer will help collect the required information?

- ▫️ CloudWatch metrics
- ▫️ CloudTrail logs
- ▫️ ALB request tracing
- ✅ ALB access logs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «ALB access logs»**

Access logs ELB містять детальну інформацію про кожен запит: час, IP клієнта, затримки, шлях, відповідь сервера. Ними аналізують трафік і шукають проблеми. За замовчуванням вимкнені.

Детальніше: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html

**❌ Неправильні варіанти:**
- *CloudTrail logs* — записують виклики API ELB: хто, коли, звідки. Самі запити до балансувальника там не фіксуються.
- *CloudWatch metrics* — агреговані часові ряди, а не дані окремих запитів.
- *ALB request tracing* — додає trace-заголовок до запитів, але затримки не аналізує.

</details>

---

<a id="t11-q41"></a>
### T11-Q41
*домен: офіційна мітка*

A firm runs its technology operations on a fleet of Amazon EC2 instances. The firm needs a certain software to be available on the instances to support their daily workflows. The developer team has been told to use the user data feature of EC2 instances.

Which of the following are true about the user data EC2 configuration? ( Select two)

By default, scripts entered as user data do not have root user privileges for executing

- ✅ By default, scripts entered as user data are executed with root user privileges
- ▫️ When an instance is running, you can update user data by using root user credentials
- ▫️ By default, user data is executed every time an EC2 instance is re-started
- ✅ By default, user data runs only during the boot cycle when you first launch an instance

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «By default, scripts entered as user data are executed with root user privileges» + «By default, user data runs only during the boot cycle when you first launch an instance»**

User Data використовують для автоматичного налаштування і скриптів при старті інстансу. Передати можна shell-скрипти або директиви cloud-init, текстом чи файлом.
- **Root-права.** Скрипти виконуються від root, тож `sudo` не потрібен. Створені файли належать root; для інших користувачів права змінюють у скрипті.
- **Лише перший запуск.** За замовчуванням User Data виконується тільки при першому завантаженні. Можна налаштувати виконання при кожному перезапуску.

**❌ Неправильні варіанти:**
- *Executed on every restart* — не за замовчуванням, лише якщо так налаштувати.
- *Update user data while running* — на запущеному інстансі User Data можна переглянути, але не змінити; спочатку інстанс зупиняють.
- *No root privileges* — виконується від root.

</details>

---

<a id="t11-q65"></a>
### T11-Q65
*домен: офіційна мітка*

You have created an Elastic Load Balancer that has marked all the EC2 instances in the target group as unhealthy. Surprisingly, when you enter the IP address of the EC2 instances in your web browser, you can access your website.

What could be the reason your instances are being marked as unhealthy? (Select two)

- ✅ The route for the health check is misconfigured
- ✅ The security group of the EC2 instance does not allow for traffic from the security group of the Application Load Balancer
- ▫️ Your web-app has a runtime that is not supported by the Application Load Balancer
- ▫️ You need to attach Elastic IP to the EC2 instances
- ▫️ The EBS volumes have been improperly mounted

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The route for the health check is misconfigured» + «The security group of the EC2 instance does not allow for traffic from the security group of the Application Load Balancer»**

Балансувальник має зв'язуватися з таргетами і на порту listener, і на порту health check. Коли додаєте listener або змінюєте порт health check, перевірте, що security groups дозволяють трафік на цьому порту в обидва боки. Інакше або health check йде на неправильний шлях, або SG інстансу не пропускає трафік від SG ALB, і таргети стають нездоровими.

Детальніше: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html

**❌ Неправильні варіанти:**
- *EBS improperly mounted* — сайт доступний за IP, тож диски в порядку.
- *Unsupported runtime* — ALB не залежить від runtime застосунку.
- *Attach Elastic IPs* — з ALB Elastic IP інстансам не потрібні.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html>

</details>

---

<a id="t12-q13"></a>
### T12-Q13
*домен: офіційна мітка*

A developer while working on Amazon EC2 instances, realized that an instance was not needed and had shut it down. But another instance of the same type automatically got launched in the account.

Which of the following options can attribute the given sequence of actions?

- ▫️ The user did not have the right permissions to shutdown the instance. User needs root permissions to terminate an instance
- ▫️ The instance could have been a part of Application Load Balancer and hence was automatically started
- ✅ Instance might be part of Auto Scaling Group and hence re-launched similar instance
- ▫️ The instance could have been a part of Network Load Balancer and hence was automatically started

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Instance might be part of Auto Scaling Group and hence re-launched similar instance»**

ASG підтримує desired capacity: якщо інстанс термінувати, вона запустить такий самий на заміну. Щоб справді прибрати інстанс, зменшують desired capacity групи або термінують із прапорцем `--should-decrement-desired-capacity`.

**❌ Неправильні варіанти:**
- *Needs root permissions* — root для термінування не потрібен; без прав дія була б просто недоступна.
- *Part of an ALB* — ALB сам інстанси не запускає.
- *Part of an NLB* — NLB сам інстанси не запускає.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/detach-instance-asg.html>

</details>

---

<a id="t12-q18"></a>
### T12-Q18
*домен: офіційна мітка*

A development team has noticed that one of the EC2 instances has been wrongly configured with the 'DeleteOnTermination' attribute set to True for its root EBS volume.

As a developer associate, can you suggest a way to disable this flag while the instance is still running?

- ▫️ The attribute cannot be updated when the instance is running. Stop the instance from Amazon EC2 console and then update the flag
- ✅ Set the DeleteOnTermination attribute to False using the command line
- ▫️ Update the attribute using AWS management console. Select the EC2 instance and then uncheck the Delete On Termination check box for the root EBS volume
- ▫️ Set the DisableApiTermination attribute of the instance using the API

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set the DeleteOnTermination attribute to False using the command line»**

Коли інстанс термінують, атрибут `DeleteOnTermination` кожного підключеного тому EBS визначає, видалити його чи зберегти. За замовчуванням `True` для root-тому і `False` для інших томів. Для вже запущеного інстансу атрибут змінюють через CLI: `aws ec2 modify-instance-attribute --block-device-mappings ...`.

**❌ Неправильні варіанти:**
- *Uncheck in the Console for a running instance* — у консолі це задають лише під час запуску інстансу.
- *DisableApiTermination* — захищає інстанс від термінування, а не том від видалення.
- *Stop the instance first* — атрибут можна змінити й на запущеному інстансі.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/deleteontermination-ebs/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#delete-on-termination-running-instance>

</details>

---

<a id="t12-q20"></a>
### T12-Q20
*домен: офіційна мітка*

A development team has inherited a web application running in the us-east-1 region with three availability zones (us-east-1a, us-east1-b, and us-east-1c) whose incoming web traffic is routed by a load balancer. When one of the EC2 instances hosting the web application crashes, the team realizes that the load balancer continues to route traffic to that instance causing intermittent issues.

Which of the following should the development team do to minimize this problem?

- ▫️ Enable Stickiness
- ▫️ Enable SSL
- ▫️ Enable Multi AZ deployments
- ✅ Enable Health Checks

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Enable Health Checks»**

Балансувальник регулярно перевіряє доступність інстансів: пінгує, підключається або надсилає запити. Це health checks. Здорові інстанси мають статус `InService`, нездорові — `OutOfService`, і трафік на них не йде.

Детальніше: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html

**❌ Неправильні варіанти:**
- *Stickiness* — прив'язує сесію до інстансу, а здоров'я не перевіряє.
- *Multi-AZ* — корисно, але перевіряти здоров'я все одно треба.
- *SSL* — шифрування трафіку, до здоров'я не стосується.

</details>

---

<a id="t12-q25"></a>
### T12-Q25
*домен: офіційна мітка*

Your web application front end consists of 5 EC2 instances behind an Application Load Balancer. You have configured your web application to capture the IP address of the client making requests. When viewing the data captured you notice that every IP address being captured is the same, which also happens to be the IP address of the Application Load Balancer.

What should you do to identify the true IP address of the client?

- ▫️ Modify the front-end of the website so that the users send their IP in the requests
- ✅ Look into the X-Forwarded-For header in the backend
- ▫️ Look into the client's cookie
- ▫️ Look into the X-Forwarded-Proto header in the backend

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Look into the X-Forwarded-For header in the backend»**

Балансувальник стоїть між клієнтом і сервером, тож у логах сервера видно лише IP балансувальника. ELB записує IP клієнта в заголовок `X-Forwarded-For` і передає його серверу.

**❌ Неправильні варіанти:**
- *Front-end sends IP* — змінювати застосунок не потрібно.
- *X-Forwarded-Proto* — протокол (HTTP чи HTTPS), яким клієнт підключився.
- *Client's cookie* — довелося б змінювати логіку клієнта й сервера.

</details>

---

<a id="t12-q26"></a>
### T12-Q26
*домен: офіційна мітка*

A development team has configured an Elastic Load Balancer for host-based routing. The idea is to support multiple subdomains and different top-level domains.

The rule \*.sample.com matches which of the following?

- ▫️ SAMPLE.COM
- ▫️ sample.test.com
- ✅ test.sample.com
- ▫️ sample.com

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «test.sample.com»**

Host conditions маршрутизують запити за іменем хоста в заголовку `Host` (host-based routing). Так один балансувальник обслуговує кілька субдоменів і доменів. Правила для hostname:
- не чутливий до регістру, до 128 символів;
- дозволені символи: `A–Z`, `a–z`, `0–9`, `-`, `.`, `*` (0 чи більше символів), `?` (рівно 1 символ);
- щонайменше одна крапка, після останньої — лише літери.

`*.sample.com` збігається з `test.sample.com`, але не з `sample.com`.

**❌ Неправильні варіанти:**
- *sample.com* — немає субдомену перед `.sample.com`.
- *sample.test.com* — інший домен.
- *SAMPLE.COM* — регістр не важливий, але субдомену немає.

</details>

---

<a id="t12-q31"></a>
### T12-Q31
*домен: офіційна мітка*

A developer has just completed configuring the Application Load Balancer for the EC2 instances. Just as he started testing his configuration, he realized that he has missed assigning target groups to his ALB.

Which error code should he expect in his debug logs?

- ▫️ HTTP 500
- ✅ HTTP 503
- ▫️ HTTP 403
- ▫️ HTTP 504

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «HTTP 503»**

HTTP 503 (Service Unavailable) на ALB означає, що в target group немає зареєстрованих таргетів.

**❌ Неправильні варіанти:**
- *HTTP 500* — внутрішня помилка балансувальника, наприклад запит без HTTP-протоколу, неможливо сформувати redirect URL або помилка правил WAF.
- *HTTP 504* — Gateway Timeout: не вдалося з'єднатися з таргетом або таргет не відповів за idle timeout.
- *HTTP 403* — Forbidden: запит заблокував WAF.

</details>

---

<a id="t12-q37"></a>
### T12-Q37
*домен: офіційна мітка*

A developer is configuring the redirect actions for an Application Load Balancer. The developer stumbled upon the following snippet of code.

Which of the following is an example of a query string condition that the developer can use on AWS CLI?

- ▫️

  ```json
  [
    {
        "Field": "query-string",
        "PathPatternConfig": {
            "Values": ["/img/*"]
        }
    }
  ]
  [
    {
        "Type": "redirect",
        "RedirectConfig": {
            "Protocol": "HTTPS",
            "Port": "443",
            "Host": "#{host}",
            "Path": "/#{path}",
            "Query": "#{query}",
            "StatusCode": "HTTP_301"
        }
    }
  ]
  ```
- ✅

  ```json
  [
    {
        "Field": "query-string",
        "QueryStringConfig": {
            "Values": [
              {
                  "Key": "version",
                  "Value": "v1"
              },
              {
                  "Value": "*example*"
              }
            ]
        }
    }
  ]
  ```
- ▫️

  ```json
  [
    {
        "Field": "query-string",
        "StringHeaderConfig": {
            "Values": ["*.example.com"]
        }
    }
  ]
  ```

<details><summary>Пояснення</summary>

**✅ Правильна відповідь:**
```json
[
  {
      "Field": "query-string",
      "QueryStringConfig": {
          "Values": [
            { "Key": "version", "Value": "v1" },
            { "Value": "*example*" }
          ]
      }
  }
]
```

Умови `query-string` маршрутизують запити за парами ключ–значення або значеннями в query string. Порівняння не чутливе до регістру; підтримуються `*` (0 чи більше символів) і `?` (рівно 1 символ). Query string починається після першого `?` в URI, пари розділяються `&`, наприклад `?version=A&gender=female`.

Ця умова спрацьовує, якщо в query string є пара `version=v1` **або** будь-яке значення, що містить `example`.

**❌ Неправильні варіанти:**
- *PathPatternConfig для query-string* — неправильна конфігурація.
- *StringHeaderConfig* — неправильна конфігурація.
- *RedirectConfig* — це дія redirect з HTTP на HTTPS (порт 443) зі збереженням хоста, шляху і query, а не умова.

</details>

---

<a id="t12-q50"></a>
### T12-Q50
*домен: офіційна мітка*

A company has configured an Auto Scaling group with health checks. The configuration is set to the desired capacity value of 3 and maximum capacity value of 3. The EC2 instances of your Auto Scaling group are configured to scale when CPU utilization is at 60 percent and is now running at 80 percent utilization.

Which of the following will take place?

- ✅ System will keep running as is
- ▫️ The desired capacity will go up to 4 and the maximum capacity will also go up to 4
- ▫️ System will trigger CloudWatch alarms to AWS support
- ▫️ The desired capacity will go up to 4 and the maximum capacity will stay at 3

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «System will keep running as is»**

Група вже працює на максимальній ємності. Після створення ASG запускає інстанси до minimum, або до desired, якщо його вказано. Без інших правил масштабування вона й далі підтримує цю кількість. Desired не може перевищити maximum.

Детальніше: https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-capacity-limits.html

**❌ Неправильні варіанти:**
- *Desired 4, max 3* — desired не може бути більшим за maximum.
- *Desired 4, max 4* — maximum сам не зміниться; його змінюють вручну.
- *Alarms to AWS Support* — AWS Support не втручається; варіант лише для відволікання.

</details>

---

<a id="t12-q62"></a>
### T12-Q62
*домен: офіційна мітка*

A startup manages its Cloud resources with Elastic Beanstalk. The environment consists of few Amazon EC2 instances, an Auto Scaling Group (ASG), and an Elastic Load Balancer. Even after the Load Balancer marked an EC2 instance as unhealthy, the ASG has not replaced it with a healthy instance.

As a Developer, suggest the necessary configurations to automate the replacement of unhealthy instance.

- ✅ The health check type of your instance's Auto Scaling group, must be changed from EC2 to ELB by using a configuration file
- ▫️ The ping path field of the Load Balancer is configured incorrectly
- ▫️ Auto Scaling group doesn't automatically replace the unhealthy instances marked by the load balancer. They have to be manually replaced from AWS Console
- ▫️ Health check parameters were configured for checking the instance health alone. The instance failed because of application failure which was not configured as a parameter for health check status

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The health check type of your instance's Auto Scaling group, must be changed from EC2 to ELB by using a configuration file»**

За замовчуванням ASG використовує health check типу **EC2**, тобто лише status checks самого інстансу. Якщо впав застосунок, а інстанс живий, ASG вважає його здоровим і не замінює. Щоб нездорові за ELB інстанси замінювалися автоматично, тип health check змінюють з EC2 на **ELB**. В Elastic Beanstalk це роблять конфігураційним файлом у `.ebextensions`, параметр `HealthCheckType: ELB`.

**❌ Неправильні варіанти:**
- *Parameters configured for instance health alone* — status checks за визначенням перевіряють лише інстанс, а не застосунок чи контейнери; параметрами health check це не налаштувати.
- *ASG never replaces ELB-unhealthy instances* — замінює, якщо тип health check — ELB.
- *Ping path misconfigured* — тоді нездоровими були б усі інстанси, а не один.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/elastic-beanstalk-instance-automation/> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-healthchecks.html>

</details>

---
