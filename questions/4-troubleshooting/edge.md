# CloudFront / Route 53 / WAF

**Домен 4 — Усунення несправностей та оптимізація** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q14"></a>
### T01-Q14
*⏭ пропущено · домен: офіційна мітка*

You are running a public DNS service on an EC2 instance where the DNS name is pointing to the IP address of the instance. You wish to upgrade your DNS service but would like to do it without any downtime.

Which of the following options will help you accomplish this?

- ▫️ Create a Load Balancer and an auto scaling group
- ▫️ Use Route 53
- ▫️ Provide a static private IP
- ✅ Elastic IP

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Route 53 is a DNS managed by AWS, but nothing prevents you from running your own DNS (it's just a software) on an EC2 instance. The trick of this question is that it's about EC2, running some software that needs a fixed IP, and not about Route 53 at all.

Elastic IP

DNS services are identified by a public IP, so you need to use Elastic IP.

Incorrect options:

Create a Load Balancer and an auto-scaling group - Load balancers do not provide an IP, instead they provide a DNS name, so this option is ruled out.

Provide a static private IP - If you provide a private IP it will not be accessible from the internet, so this option is incorrect.

Use Route 53 - Route 53 is a DNS service from AWS but the use-case talks about offering a DNS service using an EC2 instance, so this option is incorrect.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-eips-associating-different

</details>

---

<a id="t06-q02"></a>
### T06-Q02
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A start-up has launched a website using Route 53 for domain name, routing, and health checks. They failed to configure Amazon CloudWatch alarms with the health checks. How can the organization be made aware of any endpoint health issues?

- ✅ They can see the status of Route 53 health checks on the Route 53 dashboard.
- ▫️ They can subscribe to SNS topics to receive notifications.
- ▫️ They can create an alarm using CloudTrail and receive SNS notifications.
- ▫️ They can see the status of Route 53 health checks on the Shield dashboard.

<details><summary>Пояснення (оригінал, EN)</summary>

Configuring CloudWatch alarms to send a SNS notification if there is an unhealthy endpoint is optional. AWS customers can also review the Route 53 dashboard to identify any health issues.

CORRECT: "They can see the status of Route 53 health checks on the Route 53 dashboard" is the correct answer (as explained above.)

INCORRECT: "They can see the status of Route 53 health checks on the Shield dashboard" is incorrect. Route 53 health checks are not available on the Shield dashboard.

INCORRECT: "They can subscribe to SNS topics to receive notifications" is incorrect. SNS topics can be used to send notifications but must be triggered by CloudWatch alarm first.

INCORRECT: "They can create an alarm using CloudTrail and receive SNS notifications" is incorrect. CloudTrail is used to track API activity not for monitoring healthy resources.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/welcome-health-checks.html>

</details>

---

<a id="t06-q09"></a>
### T06-Q09
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An organization wants to ensure that there is minimal latency for their customers on both the east and west coast of the United States. What can they do to provide optimal performance with minimal latency?

- ▫️ Use Amazon CloudWatch to monitor traffic spikes and identify how to scale horizontally.
- ▫️ Use Amazon Route 53 to direct the traffic based on health checks.
- ▫️ Use AWS CloudTrail to monitor traffic and identify where requests are coming from and deploy the application to those Regions.
- ✅ Use Amazon Route 53 with latency-based routing by creating latency records in multiple AWS Regions.

<details><summary>Пояснення (оригінал, EN)</summary>

CORRECT: "Use Amazon Route 53 with latency-based routing by creating latency records in multiple AWS Regions" is the correct answer (as explained above.)

INCORRECT: "Use Amazon Route 53 to direct the traffic based on health checks" is incorrect. Health checks just show that the resource is healthy, and do not ensure that the best performing endpoint is chosen. Reducing latency is the best option here.

INCORRECT: "Use AWS CloudTrail to monitor traffic and identify where requests are coming from and deploy the application to those Regions" is incorrect. CloudTrail is used to monitor API calls.

INCORRECT: "Use Amazon CloudWatch to monitor traffic spikes and identify how to scale horizontally" is incorrect. CloudWatch does monitor spikes in usage, but it is not used to improve latency to end users.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy-latency.html>

</details>

---

<a id="t06-q59"></a>
### T06-Q59
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A developer is configuring health checks using Amazon Route 53 and needs to set values to determine the health of critical endpoints. What is the parameter that Amazon Route 53 reviews before deciding if an endpoint is unhealthy?

- ✅ failure threshold.
- ▫️ fault tolerance.
- ▫️ latency.
- ▫️ network response.

<details><summary>Пояснення (оригінал, EN)</summary>

The failure threshold is specified by the AWS customer. A failure is when the endpoint does not respond to a request.

CORRECT: "failure threshold" is the correct answer (as explained above.)

INCORRECT: "latency" is incorrect. Latency is not a parameter that Route 53 uses to determine if an endpoint is healthy.

INCORRECT: "fault tolerance" is incorrect. Fault tolerance is not a parameter that Route 53 uses to determine if an endpoint is healthy.

INCORRECT: "network response" is incorrect. Network response is not a parameter that Route 53 uses to determine if an endpoint is healthy.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/welcome-health-checks.html>

</details>

---

<a id="t12-q14"></a>
### T12-Q14
*⏭ пропущено · домен: офіційна мітка*

A video streaming application uses Amazon CloudFront for its data distribution. The development team has decided to use CloudFront with origin failover for high availability.

Which of the following options are correct while configuring CloudFront with Origin Groups? (Select two)

- ▫️ To set up origin failover, you must have a distribution with at least three origins
- ▫️ In the Origin Group of your distribution, all the origins are defined as primary for automatic failover in case an origin fails
- ✅ CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET, HEAD or OPTIONS
- ▫️ When there’s a cache hit, CloudFront routes the request to the primary origin in the origin group
- ✅ CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin

CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the secondary origin. CloudFront only sends requests to the secondary origin after a request to the primary origin fails.

CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET, HEAD or OPTIONS

CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET, HEAD, or OPTIONS. CloudFront does not failover when the viewer sends a different HTTP method (for example POST, PUT, and so on).

How origin failover works:  via - https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html

Incorrect options:

When there’s a cache hit, CloudFront routes the request to the primary origin in the origin group - When there’s a cache miss, CloudFront routes the request to the primary origin in the origin group. When there’s a cache hit, CloudFront returns the requested file.

To set up origin failover, you must have a distribution with at least three origins - Two origins are enough to set up an origin failover.

In the Origin Group of your distribution, all the origins are defined as primary for automatic failover in case an origin fails - To set up origin failover, you must have a distribution with at least two origins. Next, you create an origin group for your distribution that includes two origins, setting one as the primary. Only one origin can be set as primary.

Reference:

https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html

</details>

---
