# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 47  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q01"></a>
### T01-Q01
*⏭ пропущено · домен: офіційна мітка*

A company's e-commerce application becomes slow when traffic spikes. The application has a three-tier architecture (web, application and database tier) that uses synchronous transactions. The development team at the company has identified certain bottlenecks in the application tier and it is looking for a long term solution to improve the application's performance.

As a developer associate, which of the following solutions would you suggest to meet the required application response times while accounting for any traffic spikes?

- ▫️ Leverage SQS with asynchronous AWS Lambda calls to decouple the application and data tiers
- ▫️ Leverage horizontal scaling for the application's persistence layer by adding Oracle RAC on AWS
- ✅ Leverage horizontal scaling for the web and application tiers by using Auto Scaling groups and Application Load Balancer
- ▫️ Leverage vertical scaling for the application instance by provisioning a larger Amazon EC2 instance size

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Leverage horizontal scaling for the web and application tiers by using Auto Scaling groups and Application Load Balancer - A horizontally scalable system is one that can increase capacity by adding more computers to the system. This is in contrast to a vertically scalable system, which is constrained to running its processes on only one computer; in such systems, the only way to increase performance is to add more resources into one computer in the form of faster (or more) CPUs, memory or storage.

Horizontally scalable systems are oftentimes able to outperform vertically scalable systems by enabling parallel execution of workloads and distributing those across many different computers.

Elastic Load Balancing is used to automatically distribute your incoming application traffic across all the EC2 instances that you are running. You can use Elastic Load Balancing to manage incoming requests by optimally routing traffic so that no one instance is overwhelmed.

To use Elastic Load Balancing with your Auto Scaling group, you attach the load balancer to your Auto Scaling group to register the group with the load balancer. Your load balancer acts as a single point of contact for all incoming web traffic to your Auto Scaling group.

When you use Elastic Load Balancing with your Auto Scaling group, it's not necessary to register individual EC2 instances with the load balancer. Instances that are launched by your Auto Scaling group are automatically registered with the load balancer. Likewise, instances that are terminated by your Auto Scaling group are automatically deregistered from the load balancer.

This option will require fewer design changes, it's mostly configuration changes and the ability for the web/application tier to be able to communicate across instances. Hence, this is the right solution for the current use case.

Incorrect options:

Leverage SQS with asynchronous AWS Lambda calls to decouple the application and data tiers - This is incorrect as it uses asynchronous AWS Lambda calls and the application uses synchronous transactions. The question says there should be no change in the application architecture.

Leverage horizontal scaling for the application's persistence layer by adding Oracle RAC on AWS - The issue is not with the persistence layer at all. This option has only been used as a distractor.

You can deploy scalable Oracle Real Application Clusters (RAC) on Amazon EC2 using Amazon Machine Images (AMI) on AWS Marketplace. Oracle RAC is a shared-everything database cluster technology from Oracle that allows a single database (a set of data files) to be concurrently accessed and served by one or many database server instances.

Leverage vertical scaling for the application instance by provisioning a larger Amazon EC2 instance size - Vertical scaling is just a band-aid solution and will not work long term.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/autoscaling-load-balancer.html> · <https://aws.amazon.com/blogs/compute/operating-lambda-understanding-event-driven-architecture-part-1/>

</details>

---

<a id="t01-q27"></a>
### T01-Q27
*⏭ пропущено · домен: офіційна мітка*

A developer has created a new Application Load Balancer but has not registered any targets with the target groups.

Which of the following errors would be generated by the Load Balancer?

- ▫️ HTTP 502: Bad gateway
- ▫️ HTTP 500: Internal server error
- ✅ HTTP 503: Service unavailable
- ▫️ HTTP 504: Gateway timeout

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

HTTP 503: Service unavailable

The Load Balancer generates the HTTP 503: Service unavailable error when the target groups for the load balancer have no registered targets.

Incorrect options:

HTTP 500: Internal server error

HTTP 502: Bad gateway

HTTP 504: Gateway timeout

Here is a summary of the possible causes for these error types:

 via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-troubleshooting.html

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-troubleshooting.html

</details>

---

<a id="t02-q17"></a>
### T02-Q17
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An organization is hosting a website on an Amazon EC2 instance in a public subnet. The website should allow public access for HTTPS traffic on TCP port 443 but should only accept SSH traffic on TCP port 22 from a corporate address range accessible over a VPN.

Which security group configuration will support both requirements?

- ▫️ Allow traffic to both port 443 and port 22 from 0.0.0.0/0 and 192.168.0.0/16.
- ✅ Allow traffic to port 443 from 0.0.0.0/0 and allow traffic to port 22 from 192.168.0.0/16.
- ▫️ Allow traffic to port 22 from 0.0.0.0/0 and allow traffic to port 443 from 192.168.0.0/16.
- ▫️ Allow traffic to both port 443 and port 22 from the VPC CIDR block.

<details><summary>Пояснення (оригінал, EN)</summary>

Allowing traffic from 0.0.0.0/0 to port 443 will allow any traffic from the internet to access the website. Limiting the IP address to 192.168.0.0/16 for port 22 will only allow local organizational traffic.

CORRECT: "Allow traffic to port 443 from 0.0.0.0/0 and allow traffic to port 22 from 192.168.0.0/16" is the correct answer (as explained above.)

INCORRECT: “Allow traffic to port 22 from 0.0.0.0/0 and allow traffic to port 443 from 192.168.0.0/16" is incorrect. This will allow traffic from the Internet to port 22 and allow traffic to port 443 from the corporate address block only (192.168.0.0/16).

INCORRECT: "Allow traffic to both port 443 and port 22 from the VPC CIDR block" is incorrect. This would not satisfy either requirement as internet-based users will not be able to access the website and corporate users will not be able to manage the instance via SSH.

INCORRECT: "Allow traffic to both port 443 and port 22 from 0.0.0.0/0 and 192.168.0.0/16" is incorrect. This does not satisfy the requirement to restrict access to port 22 to only the corporate address block.

Джерела: <https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html>

</details>

---

<a id="t02-q40"></a>
### T02-Q40
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization is selling memorabilia that is illegal in specific countries. How can a developer restrict access to the website to countries where the memorabilia are illegal?

- ▫️ Create a Web ACL in AWS Shield with a rule that matches the specified countries and blocks access.
- ▫️ Create a Web ACL in AWS Shield with a rule that matches the specified countries and triggers an SNS notification.
- ✅ Create a Web ACL in AWS WAF with a rule that matches the specified countries and blocks access.
- ▫️ Create a Web ACL in AWS WAF with a rule that matches the specified countries and triggers an SNS notification.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS WAF can be used to set up a WEB ACL that can be used to block statements that originate from a specific country.

CORRECT: "Create a Web ACL in AWS WAF with a rule that matches the specified countries and blocks access" is the correct answer (as explained above.)

INCORRECT: "Create a Web ACL in AWS Shield with a rule that matches the specified countries and blocks access" is incorrect. AWS Shield is used to protect from DDoS attacks.

INCORRECT: "Create a Web ACL in AWS WAF with a rule that matches the specified countries and triggers an SNS notification" is incorrect. This will not block access to specific countries.

INCORRECT: " Create a Web ACL in AWS Shield with a rule that matches the specified countries and triggers an SNS notification" is incorrect. This will not block access to specific countries.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/waf-allow-block-country-geolocation/>

</details>

---

<a id="t02-q43"></a>
### T02-Q43
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → DynamoDB (бо серед варіантів немає ElastiCache; ключ — "не втратити").** → [детальніше](../../06-contradictions-and-traps.md#c01)

A Developer is designing a fault-tolerant application that will use Amazon EC2 instances and an Elastic Load Balancer. The Developer needs to ensure that if an EC2 instance fails session data is not lost. How can this be achieved?

- ✅ Use Amazon DynamoDB to perform scalable session handling
- ▫️ Use an EC2 Auto Scaling group to automatically launch new instances
- ❌ Enable Sticky Sessions on the Elastic Load Balancer
- ▫️ Use Amazon SQS to save session data

<details><summary>Пояснення (оригінал, EN)</summary>

For this scenario the key requirement is to ensure the data is not lost. Therefore, the data must be stored in a durable data store outside of the EC2 instances. Amazon DynamoDB is a suitable solution for storing session data. DynamoDB has a session handling capability for multiple languages as in the below example for PHP:

“The DynamoDB Session Handler is a custom session handler for PHP that allows developers to use Amazon DynamoDB as a session store. Using DynamoDB for session storage alleviates issues that occur with session handling in a distributed web application by moving sessions off of the local file system and into a shared location. DynamoDB is fast, scalable, easy to setup, and handles replication of your data automatically.”

Therefore, the best answer is to use DynamoDB to store the session data.

CORRECT: "Use Amazon DynamoDB to perform scalable session handling" is the correct answer.

INCORRECT: "Enable Sticky Sessions on the Elastic Load Balancer" is incorrect. Sticky sessions attempts to direct a user that has reconnected to the application to the same EC2 instance that they connected to previously. However, this does not ensure that the session data is going to be available.

INCORRECT: "Use an EC2 Auto Scaling group to automatically launch new instances" is incorrect as this does not provide a solution for storing the session data.

INCORRECT: "Use Amazon SQS to save session data" is incorrect as Amazon SQS is not suitable for storing session data.

Джерела: <https://docs.aws.amazon.com/aws-sdk-php/v2/guide/feature-dynamodb-session-handler.html>

</details>

---

<a id="t02-q57"></a>
### T02-Q57
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Сесії → ElastiCache.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A web application runs on a fleet of Amazon EC2 instances in an Auto Scaling group behind an Application Load Balancer (ALB). A developer needs a store for session data so it can be reliably served across multiple requests.

Where is the best place to store the session data?

- ❌ Write the data to the local instance store volumes.
- ▫️ Write the data to the root of the filesystem.
- ▫️ Write the data to a shared Amazon EBS volume.
- ✅ Write the data to an Amazon ElastiCache cluster.

<details><summary>Пояснення (оригінал, EN)</summary>

ElastiCache is a good solution for storing session state data as it has very low latency and high performance. DynamoDB is often used for the same purpose. In this case the session data can be written to the ElastiCache cluster and can then be easily retrieved from subsequent sessions on the same or a different EC2 instance. This decouples the data from the individual instance so if an instance fails, the data is not lost.

CORRECT: "Write the data to an Amazon ElastiCache cluster" is the correct answer (as explained above.)

INCORRECT: "Write the data to a shared Amazon EBS volume" is incorrect.

You cannot share an EBS volume except under specific circumstances and even then, you must share the volume from an EC2 instance which could fail.

INCORRECT: "Write the data to the root of the filesystem" is incorrect.

This will result in data loss if the instance fails.

INCORRECT: "Write the data to the local instance store volumes" is incorrect.

This will result in data loss if the instance fails.

Джерела: <https://aws.amazon.com/caching/session-management/>

</details>

---

<a id="t03-q38"></a>
### T03-Q38
*⏭ пропущено · домен: офіційна мітка*

Your company manages MySQL databases on EC2 instances to have full control. Applications on other EC2 instances managed by an ASG make requests to these databases to get information that displays data on dashboards viewed on mobile phones, tablets, and web browsers.

Your manager would like to scale your Auto Scaling group based on the number of requests per minute. How can you achieve this?

- ▫️ Attach an Elastic Load Balancer
- ✅ You create a CloudWatch custom metric and build an alarm to scale your ASG
- ▫️ You enable detailed monitoring and use that to scale your ASG
- ▫️ Attach additional Elastic File Storage

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

You create a CloudWatch custom metric and build an alarm to scale your ASG

Here we need to scale on the metric "number of requests per minute", which is a custom metric we need to create, as it's not readily available in CloudWatch.

Metrics produced by AWS services are standard resolution by default. When you publish a custom metric, you can define it as either standard resolution or high resolution. When you publish a high-resolution metric, CloudWatch stores it with a resolution of 1 second, and you can read and retrieve it with a period of 1 second, 5 seconds, 10 seconds, 30 seconds, or any multiple of 60 seconds.

Incorrect options:

Attach an Elastic Load Balancer - This is not what you need for auto-scaling. An Elastic Load Balancer distributes workloads across multiple compute resources and checks your instances' health status to name a few, but it does not automatically increase and decrease the number of instances based on the application requirement.

Attach additional Elastic File Storage - This is a file storage service designed for performance. Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic NFS file system for use with AWS Cloud services and on-premises resources. It is built to scale on-demand to petabytes without disrupting applications, growing and shrinking automatically as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth. This cannot be used to facilitate auto-scaling.

How EFS Works:  via - https://aws.amazon.com/efs/

You enable detailed monitoring and use that to scale your ASG - The detailed monitoring metrics won't provide information about database /application-level requests per minute, so this option is not correct.

Reference:

https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html

</details>

---

<a id="t03-q43"></a>
### T03-Q43
*⏭ пропущено · домен: офіційна мітка*

You are designing a high-performance application that requires millions of connections. You have several EC2 instances running Apache2 web servers and the application will require capturing the user’s source IP address and source port without the use of X-Forwarded-For.

Which of the following options will meet your needs?

- ▫️ Elastic Load Balancer
- ▫️ Classic Load Balancer
- ✅ Network Load Balancer
- ▫️ Application Load Balancer

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Network Load Balancer

A Network Load Balancer functions at the fourth layer of the Open Systems Interconnection (OSI) model. It can handle millions of requests per second. After the load balancer receives a connection request, it selects a target from the target group for the default rule. It attempts to open a TCP connection to the selected target on the port specified in the listener configuration. Incoming connections remain unmodified, so application software need not support X-Forwarded-For.

 via - https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html

Incorrect options:

Application Load Balancer - An Application Load Balancer functions at the application layer, the seventh layer of the Open Systems Interconnection (OSI) model. After the load balancer receives a request, it evaluates the listener rules in priority order to determine which rule to apply and then selects a target from the target group for the rule action.

One of many benefits of the Application Load Balancer is its support for path-based routing. You can configure rules for your listener that forward requests based on the URL in the request. This enables you to structure your application as smaller services, and route requests to the correct service based on the content of the URL. For needs relating to network traffic go with Network Load Balancer.

Elastic Load Balancer - Elastic Load Balancing is the service itself that offers different types of load balancers.

Classic Load Balancer - It is a basic load balancer that distributes traffic. If your account was created before 2013-12-04, your account supports EC2-Classic instances and you will benefit in using this type of load balancer. The classic load balancer can be used regardless of when your account was created and whether you use EC2-Classic or whether your instances are in a VPC but just remember its the basic load balancer AWS offers and not advanced as the others.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html

</details>

---

<a id="t03-q48"></a>
### T03-Q48
*⏭ пропущено · домен: офіційна мітка*

A developer is configuring an Application Load Balancer (ALB) to direct traffic to the application's EC2 instances and Lambda functions.

Which of the following characteristics of the ALB can be identified as correct? (Select two)

- ▫️ An ALB has three possible target types: Hostname, IP and Lambda
- ✅ An ALB has three possible target types: Instance, IP and Lambda
- ▫️ If you specify targets using an instance ID, traffic is routed to instances using any private IP address from one or more network interfaces
- ✅ You can not specify publicly routable IP addresses to an ALB
- ▫️ If you specify targets using IP addresses, traffic is routed to instances using the primary private IP address

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

An ALB has three possible target types: Instance, IP and Lambda

When you create a target group, you specify its target type, which determines the type of target you specify when registering targets with this target group. After you create a target group, you cannot change its target type. The following are the possible target types:

Instance - The targets are specified by instance ID
IP - The targets are IP addresses
Lambda - The target is a Lambda function
You can not specify publicly routable IP addresses to an ALB

When the target type is IP, you can specify IP addresses from specific CIDR blocks only. You can't specify publicly routable IP addresses.

Incorrect options:

If you specify targets using an instance ID, traffic is routed to instances using any private IP address from one or more network interfaces - If you specify targets using an instance ID, traffic is routed to instances using the primary private IP address specified in the primary network interface for the instance.

If you specify targets using IP addresses, traffic is routed to instances using the primary private IP address - If you specify targets using IP addresses, you can route traffic to an instance using any private IP address from one or more network interfaces. This enables multiple applications on an instance to use the same port.

An ALB has three possible target types: Hostname, IP and Lambda - This is incorrect, as described in the correct explanation above.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html

</details>

---

<a id="t03-q54"></a>
### T03-Q54
*⏭ пропущено · домен: офіційна мітка*

A firm maintains a highly available application that receives HTTPS traffic from mobile devices and web browsers. The main Developer would like to set up the Load Balancer routing to route traffic from web servers to smart.com/api and from mobile devices to smart.com/mobile. A developer advises that the previous recommendation is not needed and that requests should be sent to api.smart.com and mobile.smart.com instead.

Which of the following routing options were discussed in the given use-case? (select two)

- ▫️ Cookie value
- ✅ Path based
- ▫️ Client IP
- ▫️ Web browser version
- ✅ Host based

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Path based

You can create a listener with rules to forward requests based on the URL path. This is known as path-based routing. If you are running microservices, you can route traffic to multiple back-end services using path-based routing. For example, you can route general requests to one target group and request to render images to another target group.

This path-based routing allows you to route requests to, for example, /api to one set of servers (also known as target groups) and /mobile to another set. Segmenting your traffic in this way gives you the ability to control the processing environment for each category of requests. Perhaps /api requests are best processed on Compute Optimized instances, while /mobile requests are best handled by Memory Optimized instances.

Host based

You can create Application Load Balancer rules that route incoming traffic based on the domain name specified in the Host header. Requests to api.example.com can be sent to one target group, requests to mobile.example.com to another, and all others (by way of a default rule) can be sent to a third. You can also create rules that combine host-based routing and path-based routing. This would allow you to route requests to api.example.com/production and api.example.com/sandbox to distinct target groups.

 via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html#rule-condition-types

Incorrect options:

Client IP - This option has been added as a distractor. Routing is not based on the client's IP address.

Web browser version - Routing has nothing to do with the client's web browser, if it was then there is something sneaky going on.

Cookie value - Application Load Balancers support load balancer-generated cookies only and you cannot modify them. When routing sticky sessions to route requests to the same target then cookies are needed to be supported by the client's browser.

Reference:

https://aws.amazon.com/blogs/aws/new-host-based-routing-support-for-aws-application-load-balancers/

</details>

---

<a id="t03-q55"></a>
### T03-Q55
*⏭ пропущено · домен: офіційна мітка*

You have configured a Network ACL and a Security Group for the load balancer and Amazon EC2 instances to allow inbound traffic on port 80. However, users are still unable to connect to your website after launch.

Which additional configuration is required to make the website accessible to all users over the internet?

- ▫️ Add a rule to the Network ACLs to allow outbound traffic on ports 1025 - 5000
- ✅ Add a rule to the Network ACLs to allow outbound traffic on ports 1024 - 65535
- ▫️ Add a rule to the Security Group allowing outbound traffic on port 80
- ▫️ Add a rule to the Network ACLs to allow outbound traffic on ports 32768 - 61000

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Add a rule to the Network ACLs to allow outbound traffic on ports 1024 - 65535

A Network Access Control List (ACL) is an optional layer of security for your VPC that acts as a firewall for controlling traffic in and out of one or more subnets. You might set up network ACLs with rules similar to your security groups in order to add an additional layer of security to your VPC.

When you create a custom Network ACL and associate it with a subnet, by default, this custom Network ACL denies all inbound and outbound traffic until you add rules. A network ACL has separate inbound and outbound rules, and each rule can either allow or deny traffic. Network ACLs are stateless, which means that responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa).

The client that initiates the request chooses the ephemeral port range. The range varies depending on the client's operating system. Requests originating from Elastic Load Balancing use ports 1024-65535. List of ephemeral port ranges:

Many Linux kernels (including the Amazon Linux kernel) use ports 32768-61000.

Requests originating from Elastic Load Balancing use ports 1024-65535.

Windows operating systems through Windows Server 2003 use ports 1025-5000.

Windows Server 2008 and later versions use ports 49152-65535.

A NAT gateway uses ports 1024-65535.

AWS Lambda functions use ports 1024-65535.

Incorrect options:

Add a rule to the Network ACLs to allow outbound traffic on ports 1025 - 5000 - As discussed above, Windows operating systems through Windows Server 2003 use ports 1025-5000. ELB uses the port range 1024-65535.

Add a rule to the Network ACLs to allow outbound traffic on ports 32768 - 61000 - As discussed above, Linux kernels (including the Amazon Linux kernel) use ports 1025-5000. ELB uses the port range 1024-65535.

Add a rule to the Security Group allowing outbound traffic on port 80 - A security group acts as a virtual firewall for your instance to control inbound and outbound traffic. Security groups act at the instance level, not the subnet level. Security groups are stateful — if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules. Responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules.

Джерела: <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html> · <https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html>

</details>

---

<a id="t04-q22"></a>
### T04-Q22
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer is creating an application that uses Amazon EC2 instances and must be highly available and fault tolerant. How should the Developer configure the VPC?

- ✅ Create a subnet in each availability zone in the region
- ▫️ Create an Internet Gateway for every availability zone
- ▫️ Create a cluster placement group for the EC2 instances
- ▫️ Create multiple subnets within a single availability zone in the region

<details><summary>Пояснення (оригінал, EN)</summary>

To ensure high availability and fault tolerance the Developer should create a subnet within each availability zone. The EC2 instances should then be distributed between these subnets.

The Developer would likely use Amazon EC2 Auto Scaling which will automatically launch instances in each subnet and then Elastic Load Balancing to distributed incoming traffic.

CORRECT: "Create a subnet in each availability zone in the region" is the correct answer.

INCORRECT: "Create multiple subnets within a single availability zone in the region" is incorrect as this will not provide fault tolerance in the event that the AZ becomes unavailable.

INCORRECT: "Create an Internet Gateway for every availability zone" is incorrect as there is a single Internet Gateway per VPC.

INCORRECT: "Create a cluster placement group for the EC2 instances" is incorrect as this is used for ensuring low latency access between EC2 instances in a single availability zone.

Джерела: <https://d1.awsstatic.com/whitepapers/aws-building-fault-tolerant-applications.pdf>

</details>

---

<a id="t04-q48"></a>
### T04-Q48
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

An application includes multiple Auto Scaling groups of Amazon EC2 instances. Each group corresponds to a different subdomain of example.com, including forum.example.com and myaccount.example.com. An Elastic Load Balancer will be used to distribute load from a single HTTPS listener.

Which type of Elastic Load Balancer MUST a Developer use in this scenario?

- ✅ Application Load Balancer
- ▫️ Network Load Balancer
- ▫️ Task Load Balancer
- ▫️ Classic Load Balancer

<details><summary>Пояснення (оригінал, EN)</summary>

With an Application Load Balancer it is possible to route requests based on the domain name specified in the Host header. This means you can route traffic coming in to forum.example.com and myaccount.example.com to different target groups.

You can see an example of a couple of similar rules depicted below:

The Application Load Balancer is the only Elastic Load Balancer provided by AWS that can perform host-based routing.

CORRECT: "Application Load Balancer" is the correct answer.

INCORRECT: "Network Load Balancer" is incorrect as this type of ELB routes traffic based on information at the connection layer (L4).

INCORRECT: "Classic Load Balancer" is incorrect as it doesn't support any kind of host or path-based routing or even target groups.

INCORRECT: "Task Load Balancer" is incorrect as this is not a type of ELB.

Джерела: <https://aws.amazon.com/blogs/aws/new-host-based-routing-support-for-aws-application-load-balancers/>

</details>

---

<a id="t04-q58"></a>
### T04-Q58
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A company is migrating an application with a website and MySQL database to the AWS Cloud. The company require the application to be refactored so it offers high availability and fault tolerance.

How should a Developer refactor the application? (Select TWO.)

- ✅ Migrate the MySQL database to an Amazon RDS Multi-AZ deployment
- ▫️ Migrate the MySQL database to an Amazon DynamoDB with Global Tables
- ✅ Migrate the website to an Auto Scaling group of EC2 instances across multiple AZs and use an Elastic Load Balancer
- ▫️ Migrate the MySQL database to an Amazon RDS instance with a Read Replica in another AZ
- ▫️ Migrate the website to an Auto Scaling group of EC2 instances across a single AZ and use an Elastic Load Balancer

<details><summary>Пояснення (оригінал, EN)</summary>

The key requirements are to add high availability and fault tolerance to the application. To do this the Developer should put the website into an Auto Scaling group of EC2 instances across multiple AZs. An Elastic Load Balancer can be deployed in front of the EC2 instances to distribute incoming connections. This solution is highly available and fault tolerant.

For the MySQL database the Developer should use Amazon RDS with the MySQL engine. To provide fault tolerance the Developer should configure Amazon RDS as a Multi-AZ deployment which will create a standby instance in another AZ that can be failed over to.

CORRECT: "Migrate the website to an Auto Scaling group of EC2 instances across multiple AZs and use an Elastic Load Balancer" is a correct answer.

CORRECT: "Migrate the MySQL database to an Amazon RDS Multi-AZ deployment" is also a correct answer.

INCORRECT: "Migrate the website to an Auto Scaling group of EC2 instances across a single AZ and use an Elastic Load Balancer" is incorrect as to be fully fault tolerant the solution should be spread across multiple AZs.

INCORRECT: "Migrate the MySQL database to an Amazon RDS instance with a Read Replica in another AZ" is incorrect as read replicas are used for performance, not fault tolerance

INCORRECT: "Migrate the MySQL database to an Amazon DynamoDB with Global Tables" is incorrect as the MySQL database is a relational database so it is a better fit to be migrated to Amazon RDS rather than DynamoDB.

Джерела: <https://d1.awsstatic.com/whitepapers/aws-building-fault-tolerant-applications.pdf>

</details>

---

<a id="t04-q59"></a>
### T04-Q59
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An application uses Amazon EC2 instances, AWS Lambda functions and an Amazon SQS queue. The Developer must ensure all communications are within an Amazon VPC using private IP addresses. How can this be achieved? (Select TWO.)

- ✅ Create a VPC endpoint for Amazon SQS
- ▫️ Create the Amazon SQS queue within a VPC
- ▫️ Create a VPN and connect the services to the VPG
- ▫️ Create a VPC endpoint for AWS Lambda
- ✅ Add the AWS Lambda function to the VPC

<details><summary>Пояснення (оригінал, EN)</summary>

This solution can be achieved by adding the AWS Lambda function to a VPC through the function configuration, and by creating a VPC endpoint for Amazon SQS. This will result in the services using purely private IP addresses to communicate without traversing the public Internet.

CORRECT: "Add the AWS Lambda function to the VPC" is the correct answer.

CORRECT: "Create a VPC endpoint for Amazon SQS" is also correct.

INCORRECT: "Create the Amazon SQS queue within a VPC" is incorrect as you cannot create a queue within a VPC as Amazon SQS is a public service.

INCORRECT: "Create a VPC endpoint for AWS Lambda" is incorrect as you can't create a VPC endpoint for AWS Lambda. You can, however, connect a Lambda function to a VPC.

INCORRECT: "Create a VPN and connect the services to the VPG" is incorrect as you cannot create a VPN between each of these services.

Джерела: <https://aws.amazon.com/about-aws/whats-new/2018/12/amazon-sqs-vpc-endpoints-aws-privatelink/> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html>

</details>

---

<a id="t05-q13"></a>
### T05-Q13
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A financial application is hosted on an Auto Scaling group of EC2 instance with an Elastic Load Balancer. A Developer needs to capture information about the IP traffic going to and from network interfaces in the VPC.

How can the Developer capture this information?

- ✅ Create a flow log in the VPC and publish data to Amazon S3
- ▫️ Capture the information using a Network ACL
- ▫️ Capture the information directly into Amazon CloudWatch Logs
- ▫️ Create a flow log in the VPC and publish data to Amazon CloudTrail

<details><summary>Пояснення (оригінал, EN)</summary>

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data can be published to Amazon CloudWatch Logs or Amazon S3. After you've created a flow log, you can retrieve and view its data in the chosen destination.

Flow logs can help you with a number of tasks, such as:

• Diagnosing overly restrictive security group rules

• Monitoring the traffic that is reaching your instance

• Determining the direction of the traffic to and from the network interfaces

As you can see in the image below, you can create a flow log for a VPC, a subnet, or a network interface. If you create a flow log for a subnet or VPC, each network interface in that subnet or VPC is monitored.

Therefore, the Developer should create a flow log in the VPC and publish data to Amazon S3. The Developer could also choose CloudWatch Logs as a destination for publishing the data, but this is not presented as an option.

CORRECT: "Create a flow log in the VPC and publish data to Amazon S3" is the correct answer.

INCORRECT: "Capture the information directly into Amazon CloudWatch Logs" is incorrect as you cannot capture this information directly into CloudWatch Logs. You would need to capture with a flow log and then publish to CloudWatch Logs.

INCORRECT: "Capture the information using a Network ACL" is incorrect as you cannot capture data using a Network ACL as it is a subnet-level firewall.

INCORRECT: "Create a flow log in the VPC and publish data to Amazon CloudTrail" is incorrect as you cannot publish data from a flow log to CloudTrail. Amazon CloudTrail captures information about API calls.

Джерела: <https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html>

</details>

---

<a id="t05-q26"></a>
### T05-Q26
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Launch configuration застаріли → launch template.** → [детальніше](../../07-outdated-and-wrong-answers.md#o11)

An application is being migrated into the cloud. The application is stateless and will run on a fleet of Amazon EC2 instances. The application should scale elastically. How can a Developer ensure that the number of instances available is sufficient for current demand?

- ▫️ Create a task definition and use an Amazon ECS cluster
- ▫️ Create a task definition and use an AWS Fargate cluster
- ✅ Create a launch configuration and use Amazon EC2 Auto Scaling
- ▫️ Create a launch configuration and use Amazon CodeDeploy

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon EC2 Auto Scaling helps you maintain application availability and allows you to automatically add or remove EC2 instances according to conditions you define. You can use the fleet management features of EC2 Auto Scaling to maintain the health and availability of your fleet.

You can also use the dynamic and predictive scaling features of EC2 Auto Scaling to add or remove EC2 instances. Dynamic scaling responds to changing demand and predictive scaling automatically schedules the right number of EC2 instances based on predicted demand. Dynamic scaling and predictive scaling can be used together to scale faster.

A launch configuration is an instance configuration template that an Auto Scaling group uses to launch EC2 instances. When you create a launch configuration, you specify information for the instances. Include the ID of the Amazon Machine Image (AMI), the instance type, a key pair, one or more security groups, and a block device mapping. If you've launched an EC2 instance before, you specified the same information in order to launch the instance.

You can specify your launch configuration with multiple Auto Scaling groups. However, you can only specify one launch configuration for an Auto Scaling group at a time, and you can't modify a launch configuration after you've created it. To change the launch configuration for an Auto Scaling group, you must create a launch configuration and then update your Auto Scaling group with it.

Therefore, the Developer should create a launch configuration and use Amazon EC2 Auto Scaling.

CORRECT: "Create a launch configuration and use Amazon EC2 Auto Scaling" is the correct answer.

INCORRECT: "Create a launch configuration and use Amazon CodeDeploy" is incorrect as CodeDeploy is not used for auto scaling of Amazon EC2 instances.

INCORRECT: "Create a task definition and use an Amazon ECS cluster" is incorrect as the migrated application will be running on Amazon EC2 instances, not containers.

INCORRECT: "Create a task definition and use an AWS Fargate cluster" is incorrect as the migrated application will be running on Amazon EC2 instances, not containers.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchConfiguration.html>

</details>

---

<a id="t05-q57"></a>
### T05-Q57
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A company runs an application on a fleet of web servers running on Amazon EC2 instances. The web servers are behind an Elastic Load Balancer (ELB) and use an Amazon DynamoDB table for storing session state. A Developer has been asked to implement a mechanism for automatically deleting session state data that is older than 24 hours.

What is the SIMPLEST solution to this requirement?

- ▫️ Each day, create a new table to hold session data; delete the previous day's table
- ▫️ Write a script that deletes old records; schedule the scripts as a cron job on an Amazon EC2 instance
- ▫️ Add an attribute with the expiration time; name the attribute ItemExpiration
- ✅ Add an attribute with the expiration time; enable the Time To Live feature based on that attribute

<details><summary>Пояснення (оригінал, EN)</summary>

Time to Live (TTL) for Amazon DynamoDB lets you define when items in a table expire so that they can be automatically deleted from the database. With TTL enabled on a table, you can set a timestamp for deletion on a per-item basis, allowing you to limit storage usage to only those records that are relevant.

TTL is useful if you have continuously accumulating data that loses relevance after a specific time period (for example, session data, event logs, usage patterns, and other temporary data). If you have sensitive data that must be retained only for a certain amount of time according to contractual or regulatory obligations, TTL helps you ensure that it is removed promptly and as scheduled.

When Time to Live (TTL) is enabled on a table in Amazon DynamoDB, a background job checks the TTL attribute of items to determine whether they are expired.

DynamoDB compares the current time, in epoch time format, to the value stored in the user-defined Number attribute of an item. If the attribute’s value is in the epoch time format, is less than the current time, and is not older than 5 years, the item is deleted.

Processing takes place automatically, in the background, and doesn't affect read or write traffic to the table. In addition, deletes performed via TTL are not counted towards capacity units or request units. TTL deletes are available at no additional cost.

For this requirement, the Developer must add an attribute to each item with the expiration time in epoch format and then enable the Time To Live (TTL) feature based on that attribute.

CORRECT: "Add an attribute with the expiration time; enable the Time To Live feature based on that attribute" is the correct answer.

INCORRECT: "Each day, create a new table to hold session data; delete the previous day's table" is incorrect. This solution would delete some data that is not 24 hours old as it would have to run at a specific time.

INCORRECT: "Write a script that deletes old records; schedule the scripts as a cron job on an Amazon EC2 instance" is incorrect. This is not an elegant solution and would also cost more as it requires RCUs/WCUs to delete the items.

INCORRECT: "Add an attribute with the expiration time; name the attribute ItemExpiration" is incorrect as this is not a complete solution. You also need to enable the TTL feature on the table.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/howitworks-ttl.html>

</details>

---

<a id="t05-q58"></a>
### T05-Q58
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A website is running on a single Amazon EC2 instance. A Developer wants to publish the website on the Internet and is creating an A record on Amazon Route 53 for the website’s public DNS name.

What type of IP address MUST be assigned to the EC2 instance and used in the A record to ensure ongoing connectivity?

- ✅ Elastic IP address
- ▫️ Dynamic IP address
- ▫️ Private IP address
- ▫️ Public IP address

<details><summary>Пояснення (оригінал, EN)</summary>

In Amazon Route 53 when you create an A record you must supply an IP address for the resource to connect to. For a public hosted zone this must be a public IP address.

There are three types of IP address that can be assigned to an Amazon EC2 instance:

• Public – public address that is assigned automatically to instances in public subnets and reassigned if instance is stopped/started.

• Private – private address assigned automatically to all instances.

• Elastic IP – public address that is static.

To ensure ongoing connectivity the Developer needs to use an Elastic IP address for the EC2 instance and DNS A record as this is the only type of static, public IP address you can assign to an Amazon EC2 instance.

CORRECT: "Elastic IP address" is the correct answer.

INCORRECT: "Public IP address" is incorrect as though this is a public IP address, it is not static and will change every time the EC2 instance restarts. Therefore, connectivity would be lost until you update the Route 53 A record.

INCORRECT: "Dynamic IP address" is incorrect as a dynamic IP address is an IP address that will change over time. For this scenario a static, public address is required.

INCORRECT: "Private IP address" is incorrect as a public IP address is required for the public DNS A record.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-instance-addressing.html> · <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html>

</details>

---

<a id="t05-q63"></a>
### T05-Q63
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application is running on an Amazon EC2 Linux instance. The instance needs to make AWS API calls to several AWS services. What is the MOST secure way to provide access to the AWS services with MINIMAL management overhead?

- ✅ Use EC2 instance profiles
- ▫️ Store the credentials in the ~/.aws/credentials file
- ▫️ Store the credentials in AWS CloudHSM
- ▫️ Use AWS KMS to store and retrieve credentials

<details><summary>Пояснення (оригінал, EN)</summary>

An instance profile is a container for an IAM role that you can use to pass role information to an EC2 instance when the instance starts. Using an instance profile you can attach an IAM Role to an EC2 instance that the instance can then assume in order to gain access to AWS services.

CORRECT: "Use EC2 instance profiles" is the correct answer.

INCORRECT: "Use AWS KMS to store and retrieve credentials" is incorrect as KMS is used to manage encryption keys.

INCORRECT: "Store the credentials in AWS CloudHSM" is incorrect as CloudHSM is also used to manage encryption keys. It is similar to KMS but uses a dedicated hardware device that is not multi-tenant.

INCORRECT: "Store the credentials in the ~/.aws/credentials file" is incorrect as this is not the most secure option. The credentials file is associated with the AWS CLI and used for passing credentials in the form of an access key ID and secret access key when making programmatic requests from the command line.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html>

</details>

---

<a id="t06-q15"></a>
### T06-Q15
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

An organization is modernizing a software solution by migrating its database operations from Amazon EC2 instances to a serverless architecture. The software utilizes an Amazon RDS for PostgreSQL database and operates within a single VPC on AWS. Both the software and the database are currently deployed on a private subnet in the VPC. The organization needs to enable AWS Lambda functions to interact with the PostgreSQL database.

Which solution would securely satisfy these needs?

- ▫️ Establish an AWS Direct Connect link between the Lambda functions and the RDS instance.
- ▫️ Deploy an AWS AppRunner service in the VPC, which will host the Lambda functions and connect them to the RDS instance.
- ✅ Place the Lambda functions within the same VPC as the RDS instance and ensure they have an appropriate security group rule to access the RDS instance.
- ▫️ Configure the Lambda functions to connect directly to the RDS instance by using its public IP address.

<details><summary>Пояснення (оригінал, EN)</summary>

Placing the Lambda functions within the same VPC as the RDS instance and ensuring they have an appropriate security group rule to access the RDS instance would allow the Lambda functions to interact with the PostgreSQL database without exposing the database to the public internet, keeping the operations secure and in line with the existing infrastructure.

CORRECT: "Place the Lambda functions within the same VPC as the RDS instance and ensure they have an appropriate security group rule to access the RDS instance" is the correct answer (as explained above.)

INCORRECT: "Configure the Lambda functions to connect directly to the RDS instance by using its public IP address" is incorrect.

Configuring the Lambda functions to connect directly to the RDS instance using its public IP address is insecure and goes against best practices for keeping resources in private subnets isolated.

INCORRECT: "Deploy an AWS AppRunner service in the VPC, which will host the Lambda functions and connect them to the RDS instance" is incorrect.

AWS App Runner is primarily designed for building, deploying, and scaling containerized applications quickly, and is not suited to hosting Lambda functions or establishing connections with an RDS instance.

INCORRECT: "Establish an AWS Direct Connect link between the Lambda functions and the RDS instance" is incorrect.

AWS Direct Connect is used for establishing a dedicated network connection from your premises to AWS and would not be applicable or necessary for Lambda functions trying to connect to an RDS instance within the same AWS environment.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html>

</details>

---

<a id="t06-q31"></a>
### T06-Q31
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

An application runs on a fleet of Amazon EC2 instances in an Auto Scaling group. The application stores data in an Amazon DynamoDB table and all instances make updates to the table. When querying data, EC2 instances sometimes retrieve stale data. The Developer needs to update the application to ensure the most up-to-date data is retrieved for all queries.

How can the Developer accomplish this?

- ❌ Use the TransactWriteItems API when issuing PutItem actions.
- ▫️ Use the UpdateGlobalTable API to create a global secondary index.
- ▫️ Cache the database writes using Amazon DynamoDB Accelerator.
- ✅ Set the ConsistentRead parameter to true when calling GetItem.

<details><summary>Пояснення (оригінал, EN)</summary>

DynamoDB supports eventually consistent and strongly consistent reads. When using eventually consistent reads the response might not reflect the results of a recently completed write operation. The response might include some stale data.

When using strongly consistent reads DynamoDB returns a response with the most up-to-date data, reflecting the updates from all prior write operations that were successful.

DynamoDB uses eventually consistent reads unless you specify otherwise. Read operations (such as GetItem, Query, and Scan) provide a ConsistentRead parameter. If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation.

CORRECT: "Set the ConsistentRead parameter to true when calling GetItem" is the correct answer.

INCORRECT: "Cache the database writes using Amazon DynamoDB Accelerator" is incorrect. DynamoDB DAX caches items from DynamoDB to improve read performance but will not ensure the latest data is retrieved.

INCORRECT: "Use the TransactWriteItems API when issuing PutItem actions" is incorrect. This operation is used to group transactions in an all-or-nothing update.

INCORRECT: "Use the UpdateGlobalTable API to create a global secondary index" is incorrect. A GSI does not assist in any way in this solution.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/Developerguide/HowItWorks.ReadConsistency.html>

</details>

---

<a id="t06-q36"></a>
### T06-Q36
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Storage)*

An organization needs to create a central file storage solution that scales on demand and can be used by multiple Amazon Elastic Compute Cloud (EC2) instances and AWS Lambda functions. Which storage solution will meet these requirements?

- ▫️ Create an Amazon EBS Multi-Attach volume.
- ▫️ Create an Amazon ElastiCache cluster.
- ▫️ Create an AWS Storage Gateway volume gateway.
- ✅ Create an Amazon Elastic File System (EFS) file system.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Elastic File System (EFS) provides a scalable storage solution that does not require provisioning. It can scale up and down based on files stored. It can be used as a file source for multiple computing services such as Amazon Elastic Compute Cloud (EC2), Amazon Elastic Container Service (ECS), AWS Lambda.

CORRECT: "Create an Amazon Elastic File System (EFS) file system" is the correct answer (as explained above.)

INCORRECT: "Create an Amazon ElastiCache cluster" is incorrect.

Amazon ElastiCache is a database caching service; it cannot be used for file storage.

INCORRECT: "Create an Amazon EBS Multi-Attach volume" is incorrect.

Amazon EBS Multi-Attach Provisioned IOPS SSD can be used to attach a volume to multiple EC2 instances but not to other computing services such as Lambda.

INCORRECT: "Create an AWS Storage Gateway volume gateway" is incorrect.

Amazon Storage Gateway is used for hybrid infrastructure set-ups that allows on-premises infrastructure to use AWS cloud storage.

Джерела: <https://docs.aws.amazon.com/efs/latest/ug/whatisefs.html>

</details>

---

<a id="t06-q37"></a>
### T06-Q37
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An engineer is using a Border Gateway Protocol (BGP) enabled AWS VPN connection to establish communication between on-site servers and Amazon EC2 instances in their account. The engineer is successful in accessing an EC2 instance in Subnet-X but is facing issues when attempting to reach an EC2 instance in Subnet-Y within the same VPC.

Which logging mechanism can the engineer employ to confirm if the traffic is getting to Subnet-Y?

- ▫️ Use AWS CloudTrail logs to verify traffic to Subnet-Y. CloudTrail primarily logs API calls in AWS and not the traffic flow at the network interface level.
- ▫️ Consult AWS Config logs to trace the traffic. AWS Config tracks changes in resource configurations and doesn't provide insight into traffic flow.
- ✅ Use VPC Flow Logs to check the inbound and outbound traffic of Subnet-Y.
- ▫️ Check Amazon CloudWatch Logs for the EC2 instance in Subnet-Y. CloudWatch Logs for EC2 instances primarily provide information about instance status and operations, not about network traffic.

<details><summary>Пояснення (оригінал, EN)</summary>

In this scenario, AWS VPC Flow Logs would provide the required visibility. These logs capture information about the IP traffic going to and from network interfaces in the VPC. Therefore, it's the appropriate tool to verify if the traffic is reaching Subnet-Y.

CORRECT: "Use VPC Flow Logs to check the inbound and outbound traffic of Subnet-Y" is the correct answer (as explained above.)

INCORRECT: "Check Amazon CloudWatch Logs for the EC2 instance in Subnet-Y" is incorrect.

CloudWatch Logs for EC2 instances primarily provide information about instance status and operations, not about network traffic

INCORRECT: "Use AWS CloudTrail logs to verify traffic to Subnet-Y" is incorrect.

CloudTrail primarily logs API calls in AWS and not the traffic flow at the network interface level

INCORRECT: "Consult AWS Config logs to trace the traffic" is incorrect.

AWS Config tracks changes in resource configurations and doesn't provide insight into traffic flow.

Джерела: <https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html>

</details>

---

<a id="t06-q46"></a>
### T06-Q46
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

A Developer has deployed an application that runs on an Auto Scaling group of Amazon EC2 instances. The application data is stored in an Amazon DynamoDB table and records are constantly updated by all instances. An instance sometimes retrieves old data. The Developer wants to correct this by making sure the reads are strongly consistent.

How can the Developer accomplish this?

- ❌ Set consistency to strong when calling UpdateTable
- ▫️ Create a new DynamoDB Accelerator (DAX) table
- ✅ Set ConsistentRead to true when calling GetItem
- ▫️ Use the GetShardIterator command

<details><summary>Пояснення (оригінал, EN)</summary>

When you request a strongly consistent read, DynamoDB returns a response with the most up-to-date data, reflecting the updates from all prior write operations that were successful.

The GetItem operation returns a set of attributes for the item with the given primary key. If there is no matching item, GetItem does not return any data and there will be no Item element in the response.

GetItem provides an eventually consistent read by default. If your application requires a strongly consistent read, set ConsistentRead to true. Although a strongly consistent read might take more time than an eventually consistent read, it always returns the last updated value.

Therefore, the Developer should set ConsistentRead to true when calling GetItem.

CORRECT: "Set ConsistentRead to true when calling GetItem" is the correct answer.

INCORRECT: "Create a new DynamoDB Accelerator (DAX) table" is incorrect as DAX is not used to enable strongly consistent reads. DAX is used for improving read performance as it caches data in an in-memory cache.

INCORRECT: "Set consistency to strong when calling UpdateTable" is incorrect as you cannot use this API action to configure consistency at a table level.

INCORRECT: "Use the GetShardIterator command" is incorrect as this is not related to DynamoDB, it is related to Amazon Kinesis.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html>

</details>

---

<a id="t07-q04"></a>
### T07-Q04
*⏭ пропущено · домен: офіційна мітка*

Your company uses an Application Load Balancer to route incoming end-user traffic to applications hosted on Amazon EC2 instances. The applications capture incoming request information and store it in the Amazon Relational Database Service (RDS) running on Microsoft SQL Server DB engines.

As part of new compliance rules, you need to capture the client's IP address. How will you achieve this?

- ▫️ Use the header X-Forwarded-From
- ✅ Use the header X-Forwarded-For
- ▫️ You can get the Client IP addresses from server access logs
- ▫️ You can get the Client IP addresses from Elastic Load Balancing logs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the header X-Forwarded-For - The X-Forwarded-For request header helps you identify the IP address of a client when you use an HTTP or HTTPS load balancer. Because load balancers intercept traffic between clients and servers, your server access logs contain only the IP address of the load balancer. To see the IP address of the client, use the X-Forwarded-For request header. Elastic Load Balancing stores the IP address of the client in the X-Forwarded-For request header and passes the header to your server.

Incorrect options:

You can get the Client IP addresses from server access logs - As discussed above, Load Balancers intercept traffic between clients and servers, so server access logs will contain only the IP address of the load balancer.

Use the header X-Forwarded-From - This is a made-up option and given as a distractor.

You can get the Client IP addresses from Elastic Load Balancing logs - Elastic Load Balancing logs requests sent to the load balancer, including requests that never made it to the targets. For example, if a client sends a malformed request, or there are no healthy targets to respond to the request, the request is still logged. So, this is not the right option if we wish to collect the IP addresses of the clients that have access to the instances.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/x-forwarded-headers.html> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html>

</details>

---

<a id="t07-q52"></a>
### T07-Q52
*⏭ пропущено · домен: офіційна мітка*

You have migrated an on-premise SQL Server database to an Amazon Relational Database Service (RDS) database attached to a VPC inside a private subnet. Also, the related Java application, hosted on-premise, has been moved to an Amazon Lambda function.

Which of the following should you implement to connect AWS Lambda function to its RDS instance?

- ▫️ Use Lambda layers to connect to the internet and RDS separately
- ✅ Configure Lambda to connect to VPC with private subnet and Security Group needed to access RDS
- ▫️ Use Environment variables to pass in the RDS connection string
- ▫️ Configure lambda to connect to the public subnet that will give internet access and use Security Group to access RDS inside the private subnet

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure Lambda to connect to VPC with private subnet and Security Group needed to access RDS - You can configure a Lambda function to connect to private subnets in a virtual private cloud (VPC) in your account. Use Amazon Virtual Private Cloud (Amazon VPC) to create a private network for resources such as databases, cache instances, or internal services. Connect your lambda function to the VPC to access private resources during execution. When you connect a function to a VPC, Lambda creates an elastic network interface for each combination of the security group and subnet in your function's VPC configuration. This is the right way of giving RDS access to Lambda.

Lambda VPC Config:  via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html

Incorrect options:

Use Lambda layers to connect to the internet and RDS separately - You can configure your Lambda function to pull in additional code and content in the form of layers. A layer is a ZIP archive that contains libraries, a custom runtime, or other dependencies. Layers will not help in configuring access to RDS instance and hence is an incorrect choice.

Configure lambda to connect to the public subnet that will give internet access and use the Security Group to access RDS inside the private subnet - This is an incorrect statement. Connecting a Lambda function to a public subnet does not give it internet access or a public IP address. To grant internet access to your function, its associated VPC must have a NAT gateway (or NAT instance) in a public subnet.

Use Environment variables to pass in the RDS connection string - You can use environment variables to store secrets securely and adjust your function's behavior without updating code. You can use environment variables to exchange data with RDS, but you will still need access to RDS, which is not possible with just environment variables.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function/>

</details>

---

<a id="t08-q42"></a>
### T08-Q42
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Сесії → DynamoDB для еластичності.** → [детальніше](../../06-contradictions-and-traps.md#c01)

A company is migrating a stateful web service into the AWS cloud. The objective is to refactor the application to realize the benefits of cloud computing. How can the Developer leading the project refactor the application to enable more elasticity? (Select TWO.)

- ✅ Use an Elastic Load Balancer and Auto Scaling Group
- ▫️ Use Amazon CloudFormation and the Serverless Application Model
- ▫️ Use Amazon CloudFront with a Web Application Firewall
- ▫️ Store the session state in an Amazon RDS database
- ✅ Store the session state in an Amazon DynamoDB table

<details><summary>Пояснення (оригінал, EN)</summary>

As this is a stateful application the session data needs to be stored somewhere. Amazon DynamoDB is designed to be used for storing session data and it highly scalable. To add elasticity to the architecture an Amazon Elastic Load Balancer (ELB) and Amazon EC2 Auto Scaling group (ASG) can be used.

With this architecture the web service can scale elastically using the ASG and the ELB will distribute traffic to all new instances that the ASG launches. This is a good example of utilizing some of the key benefits of refactoring applications into the AWS cloud.

CORRECT: "Use an Elastic Load Balancer and Auto Scaling Group" is a correct answer.

CORRECT: "Store the session state in an Amazon DynamoDB table" is also a correct answer.

INCORRECT: "Use Amazon CloudFormation and the Serverless Application Model" is incorrect. AWS SAM is used in CloudFormation templates for expressing serverless applications using a simplified syntax. This application is not a serverless application.

INCORRECT: "Use Amazon CloudFront with a Web Application Firewall" is incorrect neither protection from web exploits nor improved performance for content delivery are requirements in this scenario.

INCORRECT: "Store the session state in an Amazon RDS database" is incorrect as RDS is not suitable for storing session state data. DynamoDB is a better fit for this use case.

Джерела: <https://docs.aws.amazon.com/aws-sdk-php/v2/guide/feature-dynamodb-session-handler.html>

</details>

---

<a id="t08-q43"></a>
### T08-Q43
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A three tier web application has been deployed on Amazon EC2 instances using Amazon EC2 Auto Scaling. The EC2 instances in the web tier sometimes receive bursts of traffic and the application tier cannot scale fast enough to keep up with messages sometimes resulting in message loss.

How can a Developer decouple the application to prevent loss of messages?

- ▫️ Add an Amazon SQS queue between the application tier and the database tier
- ▫️ Migrate the database tier to Amazon DynamoDB and enable scalable session handling
- ▫️ Configure the web tier to publish messages to an SNS topic and subscribe the application tier to the SNS topic
- ✅ Add an Amazon SQS queue between the web tier and the application tier

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon SQS queues messages received from one application component ready for consumption by another component. A queue is a temporary repository for messages that are awaiting processing. The queue acts as a buffer between the component producing and saving data, and the component receiving the data for processing.

With this scenario the best choice for the Developer is to implement an Amazon SQS queue between the web tier and the application tier. This will mean when the web tier receives bursts of traffic the messages will not overburden the application tier. Instead, they will be placed in the queue and can be processed by the app tier.

CORRECT: "Add an Amazon SQS queue between the web tier and the application tier" is the correct answer.

INCORRECT: "Add an Amazon SQS queue between the application tier and the database tier" is incorrect as the burst of messages are being received by the web tier and it is the application tier that is having difficulty keeping up with demand.

INCORRECT: "Configure the web tier to publish messages to an SNS topic and subscribe the application tier to the SNS topic" is incorrect as SNS is used for notifications and those notifications are not queued, they are sent to all subscribers. The messages being passed in this scenario are better suited to being placed in a queue.

INCORRECT: "Migrate the database tier to Amazon DynamoDB and enable scalable session handling" is incorrect as this is of no relevance to the situation. We don’t know what type of database is being used and there is not stated issue with the database layer.

Джерела: <https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html>

</details>

---

<a id="t08-q60"></a>
### T08-Q60
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A corporation plans to deploy an application on AWS utilizing an Elastic Load Balancer that operates with HTTP/HTTPS listeners. The application must have the ability to retrieve client IP addresses.

Which load-balancing solution would satisfy these needs?

- ▫️ Network Load Balancer with Proxy Protocol enabled.
- ✅ Application Load Balancer with X-Forwarded-For headers enabled.
- ▫️ Gateway Load Balancer with X-Forwarded-For headers enabled.
- ▫️ Application Load Balancer with Proxy Protocol enabled.

<details><summary>Пояснення (оригінал, EN)</summary>

Application Load Balancer operates at the application layer (Layer 7), and it supports path-based routing, and it can route requests to one or more ports on each container instance in your cluster. The X-Forwarded-For request header helps to preserve the client-side source IP address which is needed in this scenario.

CORRECT: "Application Load Balancer with X-Forwarded-For headers enabled" is the correct answer (as explained above.)

INCORRECT: "Network Load Balancer with Proxy Protocol enabled" is incorrect.

Network Load Balancer operates at the transport layer (Layer 4) and handles millions of requests per second. The NLB cannot use HTTP/HTTPS listeners so is not suitable for this solution.

INCORRECT: "Gateway Load Balancer with X-Forwarded-For headers enabled" is incorrect.

A gateway load balancer is used for distributing traffic to appliances such as IDS and IPS devices.

INCORRECT: "Application Load Balancer with Proxy Protocol enabled" is incorrect.

You cannot use the proxy protocol with a layer 7 load balancer.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/load-balancer-getting-started.html>

</details>

---

<a id="t09-q10"></a>
### T09-Q10
*⏭ пропущено · домен: офіційна мітка*

A business has their test environment built on Amazon EC2 configured on General purpose SSD volume.

At which gp2 volume size will their test environment hit the max IOPS?

- ▫️ 10.6 TiB
- ▫️ 2.7 TiB
- ✅ 5.3 TiB
- ▫️ 16 TiB

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The performance of gp2 volumes is tied to volume size, which determines the baseline performance level of the volume and how quickly it accumulates I/O credits; larger volumes have higher baseline performance levels and accumulate I/O credits faster.

5.3 TiB - General Purpose SSD (gp2) volumes offer cost-effective storage that is ideal for a broad range of workloads. These volumes deliver single-digit millisecond latencies and the ability to burst to 3,000 IOPS for extended periods of time. Between a minimum of 100 IOPS (at 33.33 GiB and below) and a maximum of 16,000 IOPS (at 5,334 GiB and above), baseline performance scales linearly at 3 IOPS per GiB of volume size.

Maximum IOPS vs Volume Size for General Purpose SSD (gp2) volumes:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html

Incorrect options:

10.6 TiB - As explained above, this is an incorrect option.

16 TiB - As explained above, this is an incorrect option.

2.7 TiB - As explained above, this is an incorrect option.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html

</details>

---

<a id="t09-q19"></a>
### T09-Q19
*⏭ пропущено · домен: офіційна мітка*

A media publishing company is using Amazon EC2 instances for running their business-critical applications. Their IT team is looking at reserving capacity apart from savings plans for the critical instances.

As a Developer Associate, which of the following reserved instance types you would select to provide capacity reservations?

- ▫️ Neither Regional Reserved Instances nor Zonal Reserved Instances
- ✅ Zonal Reserved Instances
- ▫️ Regional Reserved Instances
- ▫️ Both Regional Reserved Instances and Zonal Reserved Instances

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

When you purchase a Reserved Instance for a specific Availability Zone, it's referred to as a Zonal Reserved Instance. Zonal Reserved Instances provide capacity reservations as well as discounts.

Zonal Reserved Instances - A zonal Reserved Instance provides a capacity reservation in the specified Availability Zone. Capacity Reservations enable you to reserve capacity for your Amazon EC2 instances in a specific Availability Zone for any duration. This gives you the ability to create and manage Capacity Reservations independently from the billing discounts offered by Savings Plans or regional Reserved Instances.

Regional and Zonal Reserved Instances:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-scope.html

High Level Overview of EC2 Instance Purchase Options:  via - https://aws.amazon.com/ec2/pricing/

Incorrect options:

Regional Reserved Instances - When you purchase a Reserved Instance for a Region, it's referred to as a regional Reserved Instance. A regional Reserved Instance does not provide a capacity reservation.

Both Regional Reserved Instances and Zonal Reserved Instances - As discussed above, only Zonal Reserved Instances provide capacity reservation.

Neither Regional Reserved Instances nor Zonal Reserved Instances - As discussed above, Zonal Reserved Instances provide capacity reservation.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-scope.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html>

</details>

---

<a id="t09-q21"></a>
### T09-Q21
*⏭ пропущено · домен: офіційна мітка*

A developer with access to the AWS Management Console terminated an instance in the us-east-1a availability zone. The attached EBS volume remained and is now available for attachment to other instances. Your colleague launches a new Linux EC2 instance in the us-east-1e availability zone and is attempting to attach the EBS volume. Your colleague informs you that it is not possible and need your help.

Which of the following explanations would you provide to them?

- ▫️ The EBS volume is encrypted
- ▫️ The required IAM permissions are missing
- ▫️ EBS volumes are region locked
- ✅ EBS volumes are AZ locked

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

EBS volumes are AZ locked

An Amazon EBS volume is a durable, block-level storage device that you can attach to your instances. After you attach a volume to an instance, you can use it as you would use a physical hard drive. EBS volumes are flexible. For current-generation volumes attached to current-generation instance types, you can dynamically increase size, modify the provisioned IOPS capacity, and change volume type on live production volumes.

When you create an EBS volume, it is automatically replicated within its Availability Zone to prevent data loss due to the failure of any single hardware component. You can attach an EBS volume to an EC2 instance in the same Availability Zone.

![EBS Volume Overview]https://assets-pt.media.datacumulus.com/aws-dva-pt/assets/pt2-q62-i1.jpg) via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volumes.html

Incorrect options:

EBS volumes are region locked - It's confined to an Availability Zone and not by region.

The required IAM permissions are missing - This is a possibility as well but if permissions are not an issue then you are still confined to an availability zone.

The EBS volume is encrypted - This doesn't affect the ability to attach an EBS volume.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumes.html

</details>

---

<a id="t09-q45"></a>
### T09-Q45
*⏭ пропущено · домен: офіційна мітка*

An Auto Scaling group has a maximum capacity of 3, a current capacity of 2, and a scaling policy that adds 3 instances.

When executing this scaling policy, what is the expected outcome?

- ▫️ Amazon EC2 Auto Scaling adds 3 instances to the group
- ✅ Amazon EC2 Auto Scaling adds only 1 instance to the group
- ▫️ Amazon EC2 Auto Scaling adds 3 instances to the group and scales down 2 of those instances eventually
- ▫️ Amazon EC2 Auto Scaling does not add any instances to the group, but suggests changing the scaling policy to add one instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

A scaling policy instructs Amazon EC2 Auto Scaling to track a specific CloudWatch metric, and it defines what action to take when the associated CloudWatch alarm is in ALARM.

When a scaling policy is executed, if the capacity calculation produces a number outside of the minimum and maximum size range of the group, Amazon EC2 Auto Scaling ensures that the new capacity never goes outside of the minimum and maximum size limits.

 via - https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html

Amazon EC2 Auto Scaling adds only 1 instance to the group

For the given use-case, Amazon EC2 Auto Scaling adds only 1 instance to the group to prevent the group from exceeding its maximum size.

Incorrect options:

Amazon EC2 Auto Scaling adds 3 instances to the group - This is an incorrect statement. Auto Scaling ensures that the new capacity never goes outside of the minimum and maximum size limits.

Amazon EC2 Auto Scaling adds 3 instances to the group and scales down 2 of those instances eventually - This is an incorrect statement. Adding the instances initially and immediately downsizing them is impractical.

Amazon EC2 Auto Scaling does not add any instances to the group, but suggests changing the scaling policy to add one instance - This option has been added as a distractor.

Reference:

https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html

</details>

---

<a id="t09-q56"></a>
### T09-Q56
*⏭ пропущено · домен: офіційна мітка*

A pharmaceutical company runs their database workloads on Provisioned IOPS SSD (io1) volumes.

As a Developer Associate, which of the following options would you identify as an INVALID configuration for io1 EBS volume types?

- ✅ 200 GiB size volume with 15000 IOPS
- ▫️ 200 GiB size volume with 5000 IOPS
- ▫️ 200 GiB size volume with 2000 IOPS
- ▫️ 200 GiB size volume with 10000 IOPS

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

200 GiB size volume with 15000 IOPS - This is an invalid configuration. The maximum ratio of provisioned IOPS to requested volume size (in GiB) is 50:1. So, for a 200 GiB volume size, max IOPS possible is 200\*50 = 10000 IOPS.

Overview of Provisioned IOPS SSD (io1) volumes:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html

Incorrect options:

Provisioned IOPS SSD (io1) volumes allow you to specify a consistent IOPS rate when you create the volume, and Amazon EBS delivers the provisioned performance 99.9 percent of the time. An io1 volume can range in size from 4 GiB to 16 TiB. The maximum ratio of provisioned IOPS to the requested volume size (in GiB) is 50:1. For example, a 100 GiB volume can be provisioned with up to 5,000 IOPS.

200 GiB size volume with 2000 IOPS - As explained above, up to 10000 IOPS is a valid configuration for the given use-case.

200 GiB size volume with 10000 IOPS - As explained above, up to 10000 IOPS is a valid configuration for the given use-case.

200 GiB size volume with 5000 IOPS - As explained above, up to 10000 IOPS is a valid configuration for the given use-case.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html

</details>

---

<a id="t10-q08"></a>
### T10-Q08
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Launch configuration застаріли → launch template.** → [детальніше](../../07-outdated-and-wrong-answers.md#o11)

A Developer must run a shell script on Amazon EC2 Linux instances each time they are launched by an Amazon EC2 Auto Scaling group. What is the SIMPLEST way to run the script?

- ▫️ Configure Amazon CloudWatch Events to trigger the AWS CLI when an instance is launched and run the script
- ✅ Add the script to the user data when creating the launch configuration
- ▫️ Package the script in a zip file with some AWS Lambda source code. Upload to Lambda and run the function when instances are launched
- ▫️ Run the script using the AWS Systems Manager Run Command

<details><summary>Пояснення (оригінал, EN)</summary>

The simplest option is to add the script to the user data when creating the launch configuration. User data is information that is parsed when the EC2 instances are launched. When you add a script to the user data in a launch configuration all instances that are launched by that Auto Scaling group will run the script.

CORRECT: "Add the script to the user data when creating the launch configuration" is the correct answer.

INCORRECT: "Configure Amazon CloudWatch Events to trigger the AWS CLI when an instance is launched and run the script" is incorrect as you cannot trigger the AWS CLI using CloudWatch Events and the script may not involve AWS CLI commands.

INCORRECT: "Package the script in a zip file with some AWS Lambda source code. Upload to Lambda and run the function when instances are launched" is incorrect as Lambda does not run shell scripts. You could program the requirements into the function code however you still need a trigger which is not mentioned in this option.

INCORRECT: "Run the script using the AWS Systems Manager Run Command" is incorrect as this is not the simplest method. For most Linux AMIs (except Amazon Linux) the developer’s would need to install the agent on the operating system. They would also then need to create a mechanism of triggering the run command.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html>

</details>

---

<a id="t10-q23"></a>
### T10-Q23
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An AWS Lambda function must be connected to an Amazon VPC private subnet that does not have Internet access. The function also connects to an Amazon DynamoDB table. What MUST a Developer do to enable access to the DynamoDB table?

- ▫️ Attach an ENI to the DynamoDB table
- ✅ Configure a VPC endpoint
- ▫️ Create a route table
- ▫️ Attach an Internet Gateway

<details><summary>Пояснення (оригінал, EN)</summary>

To connect to AWS services from a private subnet with no internet access, use VPC endpoints. A VPC endpoint for DynamoDB enables resources in a VPC to use their private IP addresses to access DynamoDB with no exposure to the public internet.

When you create a VPC endpoint for DynamoDB, any requests to a DynamoDB endpoint within the Region (for example, dynamodb.us-west-2.amazonaws.com) are routed to a private DynamoDB endpoint within the Amazon network.

CORRECT: "Configure a VPC endpoint" is the correct answer.

INCORRECT: "Attach an Internet Gateway" is incorrect as you do not attach these to a private subnet.

INCORRECT: "Create a route table" is incorrect as a route table will exist for all subnets and it does not help to route out from a private subnet via the Internet unless an entry for a NAT Gateway or Instance is added.

INCORRECT: "Attach an ENI to the DynamoDB table" is incorrect as you do not attach Elastic Network Interfaces to DynamoDB tables.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/troubleshooting-networking.html> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/vpc-endpoints-dynamodb.html>

</details>

---

<a id="t10-q64"></a>
### T10-Q64
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A three-tier application is being migrated from an on-premises data center. The application includes an Apache Tomcat web tier, an application tier running on Linux, and a MySQL back end. A Developer must refactor the application to run on the AWS cloud. The cloud-based application must be fault tolerant and elastic.

How can the Developer refactor the web tier and application tier? (Select TWO.)

- ✅ Create an Auto Scaling group of EC2 instances for both the web tier and application tier
- ▫️ Use a multi-AZ Amazon RDS database for the back end using the MySQL engine
- ✅ Implement an Elastic Load Balancer for both the web tier and the application tier
- ▫️ Create an Amazon CloudFront distribution for the web tier
- ▫️ Implement an Elastic Load Balancer for the application tier

<details><summary>Пояснення (оригінал, EN)</summary>

The key requirements in this scenario are to add fault tolerances and elasticity to the web tier and application tier. Note that no specific requirements for the back end have been included.

To add elasticity to the web and application tiers the Developer should create Auto Scaling groups of EC2 instances. We know that the application tier runs on Linux and the web tier runs on Apache Tomcat (which could be on Linux or Windows). Therefore, these workloads are suitable for an ASG and this will ensure the number of instances dynamically scales out and in based on actual usage.

To add fault tolerance to the web and application tiers the Developer should add an Elastic Load Balancer. This will ensure that if the number of EC2 instances are changed by the ASG, the load balancer is able to distribute traffic to them. This also assists with elasticity.

CORRECT: "Create an Auto Scaling group of EC2 instances for both the web tier and application tier" is a correct answer.

CORRECT: "Implement an Elastic Load Balancer for both the web tier and the application tier" is also a correct answer.

INCORRECT: "Create an Amazon CloudFront distribution for the web tier" is incorrect as CloudFront is used for performance reasons, not elasticity or fault tolerance. You would use CloudFront to get content closer to end users around the world.

INCORRECT: "Use a multi-AZ Amazon RDS database for the back end using the MySQL engine" is incorrect as the question does not ask for fault tolerance of the back end, only the web tier and the application tier.

INCORRECT: "Implement an Elastic Load Balancer for the application tier" is incorrect. An Elastic Load Balancer should be implemented for both the web tier and the application tier as that is how we ensure fault tolerance and elasticity for both of those tiers.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html>

</details>

---

<a id="t11-q05"></a>
### T11-Q05
*⏭ пропущено · домен: офіційна мітка*

An IT company is configuring Auto Scaling for its Amazon EC2 instances spread across different AZs and Regions.

Which of the following scenarios are NOT correct about EC2 Auto Scaling? (Select two)

- ▫️ Amazon EC2 Auto Scaling attempts to distribute instances evenly between the Availability Zones that are enabled for your Auto Scaling group
- ✅ Auto Scaling groups that span across multiple Regions need to be enabled for all the Regions specified
- ▫️ For Auto Scaling groups in a VPC, the EC2 instances are launched in subnets
- ✅ An Auto Scaling group can contain EC2 instances in only one Availability Zone of a Region
- ▫️ An Auto Scaling group can contain EC2 instances in one or more Availability Zones within the same Region

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Auto Scaling groups that span across multiple Regions need to be enabled for all the Regions specified - This is not valid for Auto Scaling groups. Auto Scaling groups cannot span across multiple Regions.

An Auto Scaling group can contain EC2 instances in only one Availability Zone of a Region - This is not valid for Auto Scaling groups. An Auto Scaling group can contain EC2 instances in one or more Availability Zones within the same Region.

Amazon EC2 Auto Scaling Overview:  via - https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html

Incorrect options:

An Auto Scaling group can contain EC2 instances in one or more Availability Zones within the same Region - This is a valid statement. Auto Scaling groups can span across the availability Zones of a Region.

Amazon EC2 Auto Scaling attempts to distribute instances evenly between the Availability Zones that are enabled for your Auto Scaling group - When one Availability Zone becomes unhealthy or unavailable, Auto Scaling launches new instances in an unaffected Availability Zone. When the unhealthy Availability Zone returns to a healthy state, Auto Scaling automatically redistributes the application instances evenly across all of the designated Availability Zones.

For Auto Scaling groups in a VPC, the EC2 instances are launched in subnets - For Auto Scaling groups in a VPC, the EC2 instances are launched in subnets. Customers can select the subnets for your EC2 instances when you create or update the Auto Scaling group.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-benefits.html>

</details>

---

<a id="t11-q07"></a>
### T11-Q07
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Модель Free Tier змінилася у 2025 — питання застаріле.** → [детальніше](../../07-outdated-and-wrong-answers.md#o20)

A startup with newly created AWS account is testing different EC2 instances. They have used Burstable performance instance - T2.micro - for 35 seconds and stopped the instance.

At the end of the month, what is the instance usage duration that the company is charged for?

- ✅ 0 seconds
- ▫️ 30 seconds
- ▫️ 60 seconds
- ▫️ 35 seconds

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Burstable performance instances, which are T3, T3a, and T2 instances, are designed to provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload. Burstable performance instances are the only instance types that use credits for CPU usage.

0 seconds - AWS states that, if your AWS account is less than 12 months old, you can use a t2.micro instance for free within certain usage limits.

Incorrect options:

35 seconds

60 seconds

30 seconds

These three options contradict the explanation provided earlier, so these are incorrect.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html

</details>

---

<a id="t11-q38"></a>
### T11-Q38
*⏭ пропущено · домен: офіційна мітка*

An organization has hosted its EC2 instances in two AZs. AZ1 has two instances and AZ2 has 8 instances. The Elastic Load Balancer managing the instances in the two AZs has cross-zone load balancing enabled in its configuration.

What percentage traffic will each of the instances in AZ1 receive?

- ✅ 10
- ▫️ 15
- ▫️ 20
- ▫️ 25

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

A load balancer accepts incoming traffic from clients and routes requests to its registered targets (such as EC2 instances) in one or more Availability Zones.

The nodes for a load balancer distribute requests from clients to registered targets. When cross-zone load balancing is enabled, each load balancer node distributes traffic across the registered targets in all enabled Availability Zones. When cross-zone load balancing is disabled, each load balancer node distributes traffic only across the registered targets in its Availability Zone. With Application Load Balancers, cross-zone load balancing is always enabled.

10 - When cross-zone load balancing is enabled, each of the 10 targets receives 10% of the traffic. This is because each load balancer node can route its 50% of the client traffic to all 10 targets (present in both AZs).

Cross-Zone Load Balancing Overview:  via - https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html

Incorrect options:

25 - If cross-zone load balancing is disabled, each of the two targets in AZ1 will receive 25% of the traffic. Because the load balancer is only able to send to the targets registered in AZ1 (AZ2 instances are not accessible for load balancer on AZ1)

20 - Invalid option, given only as a distractor.

15 - Invalid option, given only as a distractor.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html

</details>

---

<a id="t11-q50"></a>
### T11-Q50
*⏭ пропущено · домен: офіційна мітка*

An E-commerce business, has its applications built on a fleet of Amazon EC2 instances, spread across various Regions and AZs. The technical team has suggested using Elastic Load Balancers for better architectural design.

What characteristics of an Elastic Load Balancer make it a winning choice? (Select two)

- ✅ Separate public traffic from private traffic
- ▫️ Deploy EC2 instances across multiple AWS Regions
- ▫️ Improve vertical scalability of the system
- ✅ Build a highly available system
- ▫️ The Load Balancer communicates with the underlying EC2 instances using their public IPs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

A load balancer accepts incoming traffic from clients and routes requests to its registered targets (such as EC2 instances) in one or more Availability Zones. The load balancer also monitors the health of its registered targets and ensures that it routes traffic only to healthy targets. When the load balancer detects an unhealthy target, it stops routing traffic to that target. It then resumes routing traffic to that target when it detects that the target is healthy again.

Elastic Load Balancing supports three types of load balancers:

Application Load Balancers

Network Load Balancers

Classic Load Balancers

Separate public traffic from private traffic - The nodes of an internet-facing load balancer have public IP addresses. Load balancers route requests to your targets using private IP addresses. Therefore, your targets do not need public IP addresses to receive requests from users over the internet.

Build a highly available system - Elastic Load Balancing provides fault tolerance for your applications by automatically balancing traffic across targets – Amazon EC2 instances, containers, IP addresses, and Lambda functions – in multiple Availability Zones while ensuring only healthy targets receive traffic.

Incorrect options:

The Load Balancer communicates with the underlying EC2 instances using their public IPs - This is an incorrect statement. The Load Balancer communicates with the underlying EC2 instances using their private IPs.

Improve vertical scalability of the system - This is an incorrect statement. Elastic Load Balancers can connect with Auto Scaling groups to provide horizontal scaling.

Deploy EC2 instances across multiple AWS Regions - A Load Balancer can target EC2 instances only within an AWS Region.

Джерела: <https://aws.amazon.com/elasticloadbalancing/> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html>

</details>

---

<a id="t12-q23"></a>
### T12-Q23
*⏭ пропущено · домен: офіційна мітка*

A company is looking at storing their less frequently accessed files on AWS that can be concurrently accessed by hundreds of EC2 instances. The company needs the most cost-effective file storage service that provides immediate access to data whenever needed.

Which of the following options represents the best solution for the given requirements?

- ✅ Amazon Elastic File System (EFS) Standard–IA storage class
- ▫️ Amazon Elastic File System (EFS) Standard storage class
- ▫️ Amazon Elastic Block Store (EBS)
- ▫️ Amazon S3 Standard-Infrequent Access (S3 Standard-IA) storage class

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon Elastic File System (EFS) Standard–IA storage class - Amazon EFS is a file storage service for use with Amazon compute (EC2, containers, serverless) and on-premises servers. Amazon EFS provides a file system interface, file system access semantics (such as strong consistency and file locking), and concurrently accessible storage for up to thousands of Amazon EC2 instances.

The Standard–IA storage class reduces storage costs for files that are not accessed every day. It does this without sacrificing the high availability, high durability, elasticity, and POSIX file system access that Amazon EFS provides. AWS recommends Standard-IA storage if you need your full dataset to be readily accessible and want to automatically save on storage costs for files that are less frequently accessed.

Incorrect options:

Amazon S3 Standard-Infrequent Access (S3 Standard-IA) storage class - Amazon S3 is an object storage service. Amazon S3 makes data available through an Internet API that can be accessed anywhere. It is not a file storage service, as is needed in the use case.

Amazon Elastic File System (EFS) Standard storage class - Amazon EFS Standard storage classes are ideal for workloads that require the highest levels of durability and availability. The EFS Standard storage class is used for frequently accessed files. It is the storage class to which customer data is initially written for Standard storage classes. The company is also looking at cutting costs by optimally storing the infrequently accessed data. Hence, EFS standard storage class is not the right solution for the given use case.

Amazon Elastic Block Store (EBS) - Amazon EBS is a block-level storage service for use with Amazon EC2. Amazon EBS can deliver performance for workloads that require the lowest latency access to data from a single EC2 instance. EBS volume cannot be accessed by hundreds of EC2 instances concurrently. It is not a file storage service, as is needed in the use case.

Reference:

https://docs.aws.amazon.com/efs/latest/ug/storage-classes.html

</details>

---

<a id="t12-q35"></a>
### T12-Q35
*⏭ пропущено · домен: офіційна мітка*

A developer is configuring an Amazon EC2 Auto Scaling Group that has to launch both Spot and On-Demand instances based on the requirement. Also, the CodeDeploy agent has to be automatically installed on these EC2 instances. All the EC2 instances are running on the Amazon Linux operating system.

What is the most operationally efficient way to configure this requirement?

- ✅ Use launch templates to configure the EC2 Auto Scaling Group for On-Demand and spot instances. When you create a launch template use the User data field to add a configuration script that runs when the instance starts. This shell script can, in turn, install the CodeDeploy agent
- ▫️ Configure AWS Resource Access Manager(RAM) to schedule the automatic install of CodeDeploy agent on the EC2 instances. RAM automatic schedules work on only Linux machines and not on Windows operating systems
- ▫️ Use AWS Systems Manager for installing and updating the CodeDeploy agent automatically for Spot and On-Demand instances
- ▫️ Use launch configurations to configure the EC2 Auto Scaling Group for On-Demand and spot instances. Add the shell script to the Launch configuration tab on the AWS console. This shell script will install the CodeDeploy agent

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use launch templates to configure the EC2 Auto Scaling Group for On-Demand and spot instances. When you create a launch template use the User data field to add a configuration script that runs when the instance starts. This shell script can, in turn, install the CodeDeploy agent

A launch template specifies instance configuration information that includes the ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and other parameters used to launch EC2 instances. Defining a launch template instead of a launch configuration allows you to have multiple versions of a launch template.

When you create a launch template, you can use the User data field to add a configuration script that runs when the instance starts. This shell script installs the CodeDeploy agent for all AWS Regions and supported Amazon Linux and Ubuntu distributions. You can configure CodeDeploy to auto-update on boot by setting the AUTOUPDATE variable to true.

Incorrect options:

Use launch configurations to configure the EC2 Auto Scaling Group for On-Demand and spot instances. Add the shell script to the Launch configuration tab on the AWS console. This shell script will install the CodeDeploy agent - This statement is incorrect. Not all Amazon EC2 Auto Scaling features are available when you use launch configurations. For example, you cannot create an Auto Scaling group that launches both Spot and On-Demand Instances or that specifies multiple instance types. You must use a launch template to configure these features. AWS strongly recommends not using launch configurations anymore.

Use AWS Systems Manager for installing and updating the CodeDeploy agent automatically for Spot and On-Demand instances - AWS Systems Manager also uses launch templates to automatically deploy CodeDeploy agent. However, for this use case, SSM is not operationally efficient since SSM agent needs to be installed first on all EC2 instances and then you can install the CodeDeploy agent.

Configure AWS Resource Access Manager(RAM) to schedule the automatic installation of CodeDeploy agent on the EC2 instances. RAM automatic schedules work on only Linux machines and not on Windows operating systems - AWS RAM helps you securely share your resources across AWS accounts, within your organization or organizational units (OUs), and with IAM roles and users for supported resource types. You can use AWS RAM to share resources with other AWS accounts. This eliminates the need to provision and manage resources in every account. RAM cannot be used to install the CodeDeploy agent, so this option is incorrect.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-templates.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/codedeploy-agent-launch-template/> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-configurations.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-ssm.html>

</details>

---

<a id="t12-q38"></a>
### T12-Q38
*⏭ пропущено · домен: офіційна мітка*

Your company has a load balancer in a VPC configured to be internet facing. The public DNS name assigned to the load balancer is myDns-1234567890.us-east-1.elb.amazonaws.com. When your client applications first load they capture the load balancer DNS name and then resolve the IP address for the load balancer so that they can directly reference the underlying IP.

It is observed that the client applications work well but unexpectedly stop working after a while. What is the reason for this?

- ▫️ You need to disable multi-AZ deployments
- ▫️ You need to enable stickiness
- ▫️ Your security groups are not stable
- ✅ The load balancer is highly available and its public IP may change. The DNS name is constant

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The load balancer is highly available and its public IP may change. The DNS name is constant

When your load balancer is created, it receives a public DNS name that clients can use to send requests. The DNS servers resolve the DNS name of your load balancer to the public IP addresses of the load balancer nodes for your load balancer. Never resolve the IP of a load balancer as it can change with time. You should always use the DNS name.

Incorrect options:

Your security groups are not stable - You security groups to allow your load balancer to work with registered instances. It is stable if set correctly. If your application is working and stops after a while, the issue is not with the security groups.

You need to enable stickiness - This enables the load balancer to bind a user's session to a specific instance, so this has no impact on the issue described in the given use-case.

You need to disable multi-AZ deployments - This has been added as a distractor and this has no bearing on the use-case. The change is happening with the IP of the load balancer.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-internet-facing-load-balancers.html

</details>

---

<a id="t12-q42"></a>
### T12-Q42
*⏭ пропущено · домен: офіційна мітка*

Your team has just signed up an year-long contract with a client maintaining a three-tier web application, that needs to be moved to AWS Cloud. The application has steady traffic throughout the day and needs to be on a reliable system with no down-time or access issues. The solution needs to be cost-optimal for this startup.

Which of the following options should you choose?

- ▫️ Amazon EC2 On Demand Instances
- ▫️ On-premise EC2 instance
- ✅ Amazon EC2 Reserved Instances
- ▫️ Amazon EC2 Spot Instances

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon EC2 Reserved Instances - Reserved instances can provide a capacity reservation, offering additional confidence in your ability to launch the number of instances you have reserved when you need them. You save money going with Reserved instances vs on-demand especially in a year's worth of time.

Reserved Instances are not physical instances, but rather a billing discount applied to the use of On-Demand Instances in your account. These On-Demand Instances must match certain attributes, such as instance type and Region, to benefit from the billing discount. So, there is no performance difference between an On-Demand instance or a Reserved instance.

How RIs work:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html

Incorrect options:

Amazon EC2 Spot Instances - A Spot Instance is an unused EC2 instance that is available for less than the On-Demand price. Because Spot Instances enable you to request unused EC2 instances at steep discounts, you can lower your Amazon EC2 costs significantly. Spot instances are useful if your applications can be interrupted, like data analysis, batch jobs, background processing, and optional tasks. Spot instances can be pulled down anytime without prior notice. Hence, not the right choice for the current scenario.

Amazon EC2 On-Demand Instances - With On-Demand Instances, you pay for compute capacity by the second with no long-term commitments. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it. But, On-Demand instances cost a lot more than Reserved instances. Here, in our use case, we already know that the systems are required for a complete year, so making use of Reserved Instances discount makes a lot more sense.

On-premise EC2 instance - On-premise implies the client has to maintain the physical machines, their capacity provisioning and maintenance. Not an option when the client is planning to move to AWS Cloud.

Джерела: <https://aws.amazon.com/ec2/pricing/reserved-instances/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html>

</details>

---

<a id="t12-q44"></a>
### T12-Q44
*⏭ пропущено · домен: офіційна мітка*

A new recruit is trying to understand the nuances of EC2 Auto Scaling. As an AWS Certified Developer Associate, you have been asked to mentor the new recruit.

Can you identify and explain the correct statements about Auto Scaling to the new recruit? (Select two).

- ▫️ You cannot use Amazon EC2 Auto Scaling for health checks (to replace unhealthy instances) if you are not using Elastic Load Balancing (ELB)
- ✅ Amazon EC2 Auto Scaling works with both Application Load Balancers and Network Load Balancers
- ▫️ EC2 Auto Scaling groups are regional constructs. They span across Availability Zones and AWS regions
- ▫️ Every time you create an Auto Scaling group from an existing instance, it creates a new AMI (Amazon Machine Image)
- ✅ Amazon EC2 Auto Scaling cannot add a volume to an existing instance if the existing volume is approaching capacity

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Amazon EC2 Auto Scaling is a fully managed service designed to launch or terminate Amazon EC2 instances automatically to help ensure you have the correct number of Amazon EC2 instances available to handle the load for your application.

Amazon EC2 Auto Scaling cannot add a volume to an existing instance if the existing volume is approaching capacity - A volume is attached to a new instance when it is added. Amazon EC2 Auto Scaling doesn't automatically add a volume when the existing one is approaching capacity. You can use the EC2 API to add a volume to an existing instance.

Amazon EC2 Auto Scaling works with both Application Load Balancers and Network Load Balancers - Amazon EC2 Auto Scaling works with Application Load Balancers and Network Load Balancers including their health check feature.

Incorrect options:

EC2 Auto Scaling groups are regional constructs. They span across Availability Zones and AWS regions - This is an incorrect statement. EC2 Auto Scaling groups are regional constructs. They can span Availability Zones, but not AWS regions.

Every time you create an Auto Scaling group from an existing instance, it creates a new AMI (Amazon Machine Image) - This is an incorrect statement. When you create an Auto Scaling group from an existing instance, it does not create a new AMI.

 via - https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-from-instance.html

You cannot use Amazon EC2 Auto Scaling for health checks (to replace unhealthy instances) if you are not using Elastic Load Balancing (ELB) - This is an incorrect statement. You don't have to use ELB to use Auto Scaling. You can use the EC2 health check to identify and replace unhealthy instances.

Джерела: <https://aws.amazon.com/ec2/autoscaling/faqs/> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-from-instance.html>

</details>

---
