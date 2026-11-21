# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 2 — Безпека** · питань: 10  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q12"></a>
### T01-Q12
*домен: офіційна мітка*

An e-commerce company has multiple EC2 instances operating in a private subnet which is part of a custom VPC. These instances are running an image processing application that needs to access images stored on S3. Once each image is processed, the status of the corresponding record needs to be marked as completed in a DynamoDB table.

How would you go about providing private access to these AWS resources which are not part of this custom VPC?

- ▫️ Create a gateway endpoint for S3 and add it as a target in the route table of the custom VPC. Create an interface endpoint for DynamoDB and then connect to the DynamoDB service using the private IP address
- ▫️ Create a gateway endpoint for DynamoDB and add it as a target in the route table of the custom VPC. Create an API endpoint for S3 and then connect to the S3 service using the private IP address
- ✅ Create a separate gateway endpoint for S3 and DynamoDB each. Add two new target entries for these two gateway endpoints in the route table of the custom VPC
- ▫️ Create a separate interface endpoint for S3 and DynamoDB each. Then connect to these services using the private IP address

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a separate gateway endpoint for S3 and DynamoDB each. Add two new target entries for these two gateway endpoints in the route table of the custom VPC»**

VPC endpoints — віртуальні, горизонтально масштабовані, резервовані та високодоступні компоненти VPC. Вони дають змогу приватно з'єднати VPC з підтримуваними сервісами AWS без internet gateway, NAT, VPN чи Direct Connect. Інстансам не потрібні публічні IP, а трафік не виходить за межі мережі Amazon.

Типів два:
- **Interface endpoint** — мережевий інтерфейс (ENI) з приватною IP з діапазону твоєї підмережі.
- **Gateway endpoint** — ціль маршруту в route table; підтримується лише для **Amazon S3** і **DynamoDB**.

(Зауваж, що S3 тепер підтримує обидва типи.)

**❌ Неправильні варіанти:**
- Варіанти з **interface endpoint для DynamoDB** — у банку вважаються хибними, бо (на момент написання) DynamoDB підтримувала лише gateway endpoint.
- *Create an API endpoint for S3* — такого поняття немає: API endpoints стосуються API Gateway. Це дистрактор.

Джерела: <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html> · <https://docs.aws.amazon.com/AmazonS3/latest/userguide/privatelink-interface-endpoints.html>

</details>

---

<a id="t01-q63"></a>
### T01-Q63
*домен: офіційна мітка*

A developer created an online shopping application that runs on EC2 instances behind load balancers. The same web application version is hosted on several EC2 instances and the instances run in an Auto Scaling group. The application uses STS to request credentials but after an hour your application stops working.

What is the most likely cause of this issue?

- ✅ Your application needs to renew the credentials after 1 hour when they expire
- ▫️ The IAM service is experiencing downtime once an hour
- ▫️ Your IAM policy is wrong
- ▫️ A lambda function revokes your access every hour

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Your application needs to renew the credentials after 1 hour when they expire»**

AWS STS видає тимчасові облікові дані з обмеженими правами для IAM-користувачів або федеративних користувачів. Облікові дані, створені за допомогою облікових даних акаунта, діють від 900 секунд (15 хв) до 3600 секунд (1 год), за замовчуванням 1 год. Після спливання їх треба оновити.

**❌ Неправильні варіанти:**
- *Your IAM policy is wrong* — тоді не допоміг би й перезапуск, і застосунок не працював би з самого початку.
- *A Lambda function revokes your access every hour* — відкликати доступ може IAM-політика, а не Lambda.
- *The IAM service is experiencing downtime once an hour* — IAM надійний керований сервіс.

</details>

---

<a id="t03-q21"></a>
### T03-Q21
*домен: офіційна мітка*

A global education provider runs its Learning Management System (LMS) application on Amazon EC2 instances behind an Application Load Balancer (ALB), with its domain name managed in Amazon Route 53. The LMS is heavily dependent on static assets such as images, style sheets, and JavaScript files, and the application is currently deployed in a single AWS Region. The provider wants to deliver faster performance for students worldwide while minimizing ongoing operational overhead.

Which solution will improve global performance with the least operational effort?

- ✅ Create an Amazon CloudFront distribution with the ALB as the origin, and point the application’s Route 53 alias to the CloudFront domain
- ▫️ Enable Amazon S3 Transfer Acceleration and move static assets to S3; update the application to fetch assets via the accelerated S3 endpoint
- ▫️ Deploy the application in multiple Regions and use Route 53 latency-based routing to direct users to the nearest ALB
- ▫️ Put AWS Global Accelerator in front of the ALB and update Route 53 to alias the app’s domain to the accelerator

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an Amazon CloudFront distribution with the ALB as the origin, and point the application's Route 53 alias to the CloudFront domain»**

CloudFront ставить CDN перед ALB: вміст віддається з edge-локацій ближче до користувачів, затримка падає, origin розвантажується. ALB нативно підтримується як origin, а CloudFront прискорює і статичний, і динамічний вміст. Операційно просто: створити distribution і направити DNS на неї, застосунок лишається в одному регіоні.

**❌ Неправильні варіанти:**
- *S3 Transfer Acceleration + перенести статику в S3* — прискорює передачу файлів у/з S3, але не динамічні відповіді ALB, і вимагає переробки.
- *Multi-Region + Route 53 latency routing* — допомагає, але підтримувати кілька регіонів (дані, стан, failover, деплої) — значно більше роботи.
- *AWS Global Accelerator* — веде трафік магістраллю AWS і дає статичні anycast IP, але не кешує вміст. Для веб-застосунку зі статикою CloudFront дає більший ефект із меншим налаштуванням.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/HowCloudFrontWorks.html> · <https://docs.aws.amazon.com/global-accelerator/latest/dg/what-is-global-accelerator.html?utm_source=chatgpt.com>

</details>

---

<a id="t04-q26"></a>
### T04-Q26
*домен: визначено автоматично (у джерелі: AWS Compute)*

An Auto Scaling Group (ASG) of Amazon EC2 instances is being created for processing messages from an Amazon SQS queue. To ensure the EC2 instances are cost-effective a Developer would like to configure the ASG to maintain aggregate CPU utilization at 70%.

Which type of scaling policy should the Developer choose?

- ✅ Target Tracking Scaling Policy
- ▫️ Scheduled Scaling Policy
- ▫️ Step Scaling Policy
- ▫️ Simple Scaling Policy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Target Tracking Scaling Policy»**

У target tracking обирають метрику й цільове значення, а EC2 Auto Scaling сам створює alarms CloudWatch і розраховує, скільки додати чи прибрати потужності, щоб тримати метрику біля цілі, підлаштовуючись під зміни навантаження. Приклади: середній CPU ASG на рівні 40% або кількість запитів на ціль ELB — 1000. «Тримати CPU 70%» — саме цей випадок.

**❌ Неправильні варіанти:**
- *Step Scaling Policy* і *Simple Scaling Policy* — пороги alarms і величину масштабування задаєш вручну; для «тримати значення» менш зручно.
- *Scheduled Scaling Policy* — масштабування за розкладом, а не за навантаженням.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html>

</details>

---

<a id="t05-q05"></a>
### T05-Q05
*домен: визначено автоматично (у джерелі: AWS Compute)*

A company is running a web application on Amazon EC2 behind an Elastic Load Balancer (ELB). The company is concerned about the security of the web application and would like to secure the application with SSL certificates. The solution should not have any performance impact on the EC2 instances.

What steps should be taken to secure the web application? (Select TWO.)

- ✅ Add an SSL certificate to the Elastic Load Balancer
- ▫️ Configure the Elastic Load Balancer with SSL passthrough
- ▫️ Install SSL certificates on the EC2 instances
- ✅ Configure the Elastic Load Balancer for SSL termination
- ▫️ Configure Server-Side Encryption with KMS managed keys

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Add an SSL certificate to the Elastic Load Balancer» та «Configure the Elastic Load Balancer for SSL termination»**

За умовою продуктивність EC2 не можна погіршувати, тож сертифікати на інстанси не ставлять: шифрування й розшифрування навантажують CPU. Лишається SSL на балансері: додати сертифікат і налаштувати SSL termination. HTTPS listener (SSL offload) шифрує трафік між клієнтами і балансером; балансер термінує з'єднання і розшифровує запити перед відправкою на цілі.

**❌ Неправильні варіанти:**
- *SSL passthrough* — зашифровані пакети йдуть на інстанси, і розшифровують їх саме інстанси.
- *Install SSL certificates on the EC2 instances* — додаткове навантаження на CPU.
- *Server-Side Encryption with KMS keys* — стосується S3, а не ELB.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html>

</details>

---

<a id="t05-q60"></a>
### T05-Q60
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An application uses an Auto Scaling group of Amazon EC2 instances, an Application Load Balancer (ALB), and an Amazon Simple Queue Service (SQS) queue. An Amazon CloudFront distribution caches content for global users. A Developer needs to add in-transit encryption to the data by configuring end-to-end SSL between the CloudFront Origin and the end users.

How can the Developer meet this requirement? (Select TWO.)

- ▫️ Add a certificate to the Auto Scaling Group
- ✅ Configure the Viewer Protocol Policy
- ▫️ Create an Origin Access Identity (OAI)
- ✅ Configure the Origin Protocol Policy
- ▫️ Create an encrypted distribution

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure the Viewer Protocol Policy» + «Configure the Origin Protocol Policy»**

CloudFront можна налаштувати так, щоб HTTPS вимагався і для глядачів, і для звернень до origin. Тоді запит обробляється так:
1. Глядач надсилає HTTPS-запит.
2. Якщо об'єкт є в edge-кеші, CloudFront шифрує відповідь і повертає її.
3. Якщо об'єкта немає, CloudFront встановлює TLS із origin і передає запит у зашифрованому вигляді.
4. Origin розшифровує запит, шифрує об'єкт і повертає його.
5. CloudFront розшифровує, повторно шифрує, передає глядачеві й кешує об'єкт.
6. Глядач розшифровує відповідь.

Відповідно:
- **Origin Protocol Policy** вмикає TLS між CloudFront і origin. Кроки залежать від домену: стандартний CloudFront чи власний.
- **Viewer Protocol Policy** вмикає TLS між користувачем і CloudFront.

**❌ Неправильні варіанти:**
- *Origin Access Identity (OAI)* — спеціальний користувач для захисту S3-origin; до HTTPS не стосується. ℹ️ OAI застарів, тепер використовують OAC.
- *Certificate on the ASG* — до ASG сертифікати не додають; у цьому сценарії сертифікат має бути на listener ALB.
- *Encrypted distribution* — такого поняття не існує.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-viewers-to-cloudfront.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-cloudfront-to-custom-origin.html>

</details>

---

<a id="t07-q18"></a>
### T07-Q18
*домен: офіційна мітка*

An application runs on an EC2 instance and processes orders on a nightly basis. This EC2 instance needs to access the orders that are stored in S3.

How would you recommend the EC2 instance access the orders securely?

- ✅ Use an IAM role
- ▫️ Use EC2 User Data
- ▫️ Create an S3 bucket policy that authorises public access
- ▫️ Create an IAM programmatic user and store the access key and secret access key on the EC2 ~/.aws/credentials file.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an IAM role»**

IAM-ролі дають застосункам на інстансі безпечно викликати API AWS без збережених облікових даних. EC2 використовує instance profile як контейнер для ролі. Якщо створити роль у консолі IAM, instance profile з тим самим ім'ям з'являється автоматично. Це найбезпечніший варіант: роль дає доступ до S3, а на інстансі не зберігаються жодні ключі.

**❌ Неправильні варіанти:**
- *Access keys in ~/.aws/credentials* — працюватиме, але небезпечно: будь-хто з доступом до інстансу вкраде ключі.
- *EC2 User Data* — це скрипти для першого запуску інстансу; до задачі не стосується.
- *Public bucket policy* — відкриває бакет для всіх.

</details>

---

<a id="t09-q41"></a>
### T09-Q41
*домен: офіційна мітка*

A company runs its flagship application on a fleet of Amazon EC2 instances. After misplacing a couple of private keys from the SSH key pairs, they have decided to re-use their SSH key pairs for the different instances across AWS Regions.

As a Developer Associate, which of the following would you recommend to address this use-case?

- ✅ Generate a public SSH key from a private SSH key. Then, import the key into each of your AWS Regions
- ▫️ Encrypt the private SSH key and store it in the S3 bucket to be accessed from any AWS Region
- ▫️ Store the public and private SSH key pair in AWS Trusted Advisor and access it across AWS Regions
- ▫️ It is not possible to reuse SSH key pairs across AWS Regions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Generate a public SSH key from a private SSH key. Then, import the key into each of your AWS Regions»**

Як використати той самий SSH-ключ в інших регіонах:
1. Згенерувати публічний ключ (`.pub`) з приватного (`.pem`), наприклад `ssh-keygen -y -f key.pem > key.pub`.
2. Вибрати регіон.
3. Імпортувати публічний ключ: `aws ec2 import-key-pair`.

**❌ Неправильні варіанти:**
- *Not possible to reuse* — можливо, через ручний імпорт.
- *Store in Trusted Advisor* — Trusted Advisor дає рекомендації щодо витрат, продуктивності й безпеки, але ключі не зберігає.
- *Encrypted private key in S3* — зберегти можна, але це не зробить ключ доступним для EC2 в усіх регіонах.

</details>

---

<a id="t11-q13"></a>
### T11-Q13
*домен: офіційна мітка*

A development team has configured inbound traffic for the relevant ports in both the Security Group of the EC2 instance as well as the Network Access Control List (NACL) of the subnet for the EC2 instance. The team is, however, unable to connect to the service running on the Amazon EC2 instance.

As a developer associate, which of the following will you recommend to fix this issue?

- ▫️ Network ACLs are stateful, so allowing inbound traffic to the necessary ports enables the connection. Security Groups are stateless, so you must allow both inbound and outbound traffic
- ▫️ IAM Role defined in the Security Group is different from the IAM Role that is given access in the Network ACLs
- ✅ Security Groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic
- ▫️ Rules associated with Network ACLs should never be modified from the command line. An attempt to modify rules from the command line blocks the rule and results in an erratic behavior

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Security Groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic»**

- **Security groups — stateful**: досить дозволити вхідний трафік, і відповідь пропускається автоматично.
- **NACL — stateless**: потрібно дозволити і вхід на порт сервісу, і вихід на ephemeral-порти (1024–65535). Клієнт підключається з випадкового ephemeral-порту, і відповідь іде саме на нього.

Default NACL дозволяє весь трафік, а суворіший NACL вимагає явних правил. Для трафіку з інтернету потрібен маршрут через IGW, для VPN чи Direct Connect — через virtual private gateway.

**❌ Неправильні варіанти:**
- *NACL stateful, SG stateless* — навпаки.
- *IAM Role in SG vs NACL* — IAM-ролей у SG чи NACL немає.
- *Never modify NACL via CLI* — вигадка: правила NACL можна змінювати через CLI.

</details>

---

<a id="t11-q35"></a>
### T11-Q35
*домен: офіційна мітка*

A cybersecurity firm wants to run their applications on single-tenant hardware to meet security guidelines.

Which of the following is the MOST cost-effective way of isolating their Amazon EC2 instances to a single tenant?

- ▫️ Spot Instances
- ▫️ Dedicated Hosts
- ✅ Dedicated Instances
- ▫️ On-Demand Instances

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Dedicated Instances»**

Dedicated Instances — EC2 на обладнанні, виділеному одному клієнту. Інстанси різних акаунтів фізично ізольовані, навіть якщо акаунти мають спільного платника. Але вони можуть ділити обладнання з не-dedicated інстансами того самого акаунта.

Dedicated Host — теж виділений фізичний сервер, але з видимістю і контролем розміщення інстансів.

Порівняння: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html#dedicated-hosts-dedicated-instances

**❌ Неправильні варіанти:**
- *Spot Instances* — дешеві, але без виділеного обладнання.
- *Dedicated Hosts* — дорожчі за Dedicated Instances. Потрібні для BYOL-ліцензій і контролю розміщення, але не тут.
- *On-Demand* — без ізоляції обладнання і дорогі.

Огляд варіантів купівлі: https://aws.amazon.com/ec2/pricing/

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html>

</details>

---
