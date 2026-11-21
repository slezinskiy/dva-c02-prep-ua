# CloudFront / Route 53 / WAF

**Домен 4 — Усунення несправностей та оптимізація** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q14"></a>
### T01-Q14
*домен: офіційна мітка*

You are running a public DNS service on an EC2 instance where the DNS name is pointing to the IP address of the instance. You wish to upgrade your DNS service but would like to do it without any downtime.

Which of the following options will help you accomplish this?

- ▫️ Create a Load Balancer and an auto scaling group
- ▫️ Use Route 53
- ▫️ Provide a static private IP
- ✅ Elastic IP

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Elastic IP»**

Route 53 — керований DNS від AWS, але ніщо не заважає запустити власний DNS (це просто програма) на EC2. Сенс питання в EC2 з програмою, якій потрібна фіксована IP, а не в Route 53. DNS-сервіс ідентифікується за публічною IP, тому потрібна Elastic IP: її можна перенести на новий інстанс без downtime.

**❌ Неправильні варіанти:**
- *Create a Load Balancer and an auto-scaling group* — балансер дає не IP, а DNS-ім'я.
- *Provide a static private IP* — приватна IP недоступна з інтернету.
- *Use Route 53* — за умовою DNS-сервіс працює саме на EC2.

</details>

---

<a id="t06-q02"></a>
### T06-Q02
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A start-up has launched a website using Route 53 for domain name, routing, and health checks. They failed to configure Amazon CloudWatch alarms with the health checks. How can the organization be made aware of any endpoint health issues?

- ✅ They can see the status of Route 53 health checks on the Route 53 dashboard.
- ▫️ They can subscribe to SNS topics to receive notifications.
- ▫️ They can create an alarm using CloudTrail and receive SNS notifications.
- ▫️ They can see the status of Route 53 health checks on the Shield dashboard.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «They can see the status of Route 53 health checks on the Route 53 dashboard.»**

Налаштовувати CloudWatch alarm із SNS-сповіщенням про нездоровий endpoint необов'язково. Стан health checks видно на дашборді Route 53.

**❌ Неправильні варіанти:**
- *Shield dashboard* — health checks Route 53 там не відображаються.
- *Subscribe to SNS topics* — SNS надсилає сповіщення, але спочатку їх має запустити CloudWatch alarm.
- *CloudTrail alarm* — CloudTrail відстежує API-активність, а не здоров'я ресурсів.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/welcome-health-checks.html>

</details>

---

<a id="t06-q09"></a>
### T06-Q09
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An organization wants to ensure that there is minimal latency for their customers on both the east and west coast of the United States. What can they do to provide optimal performance with minimal latency?

- ▫️ Use Amazon CloudWatch to monitor traffic spikes and identify how to scale horizontally.
- ▫️ Use Amazon Route 53 to direct the traffic based on health checks.
- ▫️ Use AWS CloudTrail to monitor traffic and identify where requests are coming from and deploy the application to those Regions.
- ✅ Use Amazon Route 53 with latency-based routing by creating latency records in multiple AWS Regions.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon Route 53 with latency-based routing by creating latency records in multiple AWS Regions.»**

Latency-based routing у Route 53 спрямовує кожного користувача в регіон із найменшою затримкою для нього. Для цього створюють latency-записи в кількох регіонах.

**❌ Неправильні варіанти:**
- *Route 53 health checks* — перевіряють лише здоров'я ресурсу і не вибирають найшвидший endpoint.
- *CloudTrail to monitor traffic* — CloudTrail відстежує API-виклики.
- *CloudWatch traffic spikes* — CloudWatch бачить піки, але затримку для користувачів не зменшує.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-latency.html>

</details>

---

<a id="t06-q59"></a>
### T06-Q59
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A developer is configuring health checks using Amazon Route 53 and needs to set values to determine the health of critical endpoints. What is the parameter that Amazon Route 53 reviews before deciding if an endpoint is unhealthy?

- ✅ failure threshold.
- ▫️ fault tolerance.
- ▫️ latency.
- ▫️ network response.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «failure threshold.»**

Failure threshold задає клієнт: скільки поспіль невдалих перевірок має статися, щоб Route 53 вважав endpoint нездоровим. Невдача — це коли endpoint не відповідає на запит.

**❌ Неправильні варіанти:**
- *latency* — не є параметром, за яким Route 53 визначає здоров'я endpoint.
- *fault tolerance* — не є параметром, за яким Route 53 визначає здоров'я endpoint.
- *network response* — не є параметром, за яким Route 53 визначає здоров'я endpoint.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/welcome-health-checks.html>

</details>

---

<a id="t12-q14"></a>
### T12-Q14
*домен: офіційна мітка*

A video streaming application uses Amazon CloudFront for its data distribution. The development team has decided to use CloudFront with origin failover for high availability.

Which of the following options are correct while configuring CloudFront with Origin Groups? (Select two)

- ▫️ To set up origin failover, you must have a distribution with at least three origins
- ▫️ In the Origin Group of your distribution, all the origins are defined as primary for automatic failover in case an origin fails
- ✅ CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET, HEAD or OPTIONS
- ▫️ When there’s a cache hit, CloudFront routes the request to the primary origin in the origin group
- ✅ CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET, HEAD or OPTIONS» + «CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin»**

Як працює origin failover:
- **Кожен запит починається з primary.** Навіть якщо попередній запит перейшов на secondary, наступний знову йде на primary. На secondary запит іде лише після невдачі на primary.
- **Лише GET, HEAD і OPTIONS.** Для POST, PUT та інших методів failover не відбувається.

Детальніше: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html

**❌ Неправильні варіанти:**
- *Cache hit → primary origin* — при cache hit CloudFront віддає файл із кешу; до primary запит іде лише при cache miss.
- *At least three origins* — досить двох.
- *All origins primary* — в origin group лише один primary.

</details>

---
