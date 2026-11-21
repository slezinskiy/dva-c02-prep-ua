# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 2 — Безпека** · питань: 10  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q12"></a>
### T01-Q12
*⏭ пропущено · домен: офіційна мітка*

An e-commerce company has multiple EC2 instances operating in a private subnet which is part of a custom VPC. These instances are running an image processing application that needs to access images stored on S3. Once each image is processed, the status of the corresponding record needs to be marked as completed in a DynamoDB table.

How would you go about providing private access to these AWS resources which are not part of this custom VPC?

- ▫️ Create a gateway endpoint for S3 and add it as a target in the route table of the custom VPC. Create an interface endpoint for DynamoDB and then connect to the DynamoDB service using the private IP address
- ▫️ Create a gateway endpoint for DynamoDB and add it as a target in the route table of the custom VPC. Create an API endpoint for S3 and then connect to the S3 service using the private IP address
- ✅ Create a separate gateway endpoint for S3 and DynamoDB each. Add two new target entries for these two gateway endpoints in the route table of the custom VPC
- ▫️ Create a separate interface endpoint for S3 and DynamoDB each. Then connect to these services using the private IP address

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a separate gateway endpoint for S3 and DynamoDB each. Add two new target entries for these two gateway endpoints in the route table of the custom VPC

Endpoints are virtual devices. They are horizontally scaled, redundant, and highly available VPC components. They allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic.

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by AWS PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection. Instances in your VPC do not require public IP addresses to communicate with resources in the service. Traffic between your VPC and the other service does not leave the Amazon network.

There are two types of VPC endpoints: interface endpoints and gateway endpoints. An interface endpoint is an elastic network interface with a private IP address from the IP address range of your subnet that serves as an entry point for traffic destined to a supported service.

A gateway endpoint is a gateway that you specify as a target for a route in your route table for traffic destined to a supported AWS service. The following AWS services are supported:

Amazon S3

DynamoDB

You should note that S3 now supports both gateway endpoints as well as the interface endpoints.

Incorrect options:

Create a gateway endpoint for S3 and add it as a target in the route table of the custom VPC. Create an interface endpoint for DynamoDB and then connect to the DynamoDB service using the private IP address

Create a separate interface endpoint for S3 and DynamoDB each. Then connect to these services using the private IP address

DynamoDB does not support interface endpoints, so these two options are incorrect.

Create a gateway endpoint for DynamoDB and add it as a target in the route table of the custom VPC. Create an API endpoint for S3 and then connect to the S3 service using the private IP address - There is no such thing as an API endpoint for S3. API endpoints are used with AWS API Gateway. This option has been added as a distractor.

Джерела: <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html> · <https://docs.aws.amazon.com/AmazonS3/latest/userguide/privatelink-interface-endpoints.html>

</details>

---

<a id="t01-q63"></a>
### T01-Q63
*⏭ пропущено · домен: офіційна мітка*

A developer created an online shopping application that runs on EC2 instances behind load balancers. The same web application version is hosted on several EC2 instances and the instances run in an Auto Scaling group. The application uses STS to request credentials but after an hour your application stops working.

What is the most likely cause of this issue?

- ✅ Your application needs to renew the credentials after 1 hour when they expire
- ▫️ The IAM service is experiencing downtime once an hour
- ▫️ Your IAM policy is wrong
- ▫️ A lambda function revokes your access every hour

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Your application needs to renew the credentials after 1 hour when they expire

AWS Security Token Service (AWS STS) is a web service that enables you to request temporary, limited-privilege credentials for AWS Identity and Access Management (IAM) users or for users that you authenticate (federated users). By default, AWS Security Token Service (STS) is available as a global service, and all AWS STS requests go to a single endpoint at https://sts.amazonaws.com.

Credentials that are created by using account credentials can range from 900 seconds (15 minutes) up to a maximum of 3,600 seconds (1 hour), with a default of 1 hour. Hence you need to renew the credentials post expiry.

 via - https://docs.aws.amazon.com/STS/latest/APIReference/API_GetSessionToken.html

Incorrect options:

Your IAM policy is wrong - If your policy was wrong, a reboot would not solve the issue.

A lambda function revokes your access every hour - Revoking can be done by an IAM policy. Lambda function cannot revoke access.

The IAM service is experiencing downtime once an hour - The IAM service is reliable as it's managed by AWS.

Reference:

https://docs.aws.amazon.com/STS/latest/APIReference/API_GetSessionToken.html

</details>

---

<a id="t03-q21"></a>
### T03-Q21
*⏭ пропущено · домен: офіційна мітка*

A global education provider runs its Learning Management System (LMS) application on Amazon EC2 instances behind an Application Load Balancer (ALB), with its domain name managed in Amazon Route 53. The LMS is heavily dependent on static assets such as images, style sheets, and JavaScript files, and the application is currently deployed in a single AWS Region. The provider wants to deliver faster performance for students worldwide while minimizing ongoing operational overhead.

Which solution will improve global performance with the least operational effort?

- ✅ Create an Amazon CloudFront distribution with the ALB as the origin, and point the application’s Route 53 alias to the CloudFront domain
- ▫️ Enable Amazon S3 Transfer Acceleration and move static assets to S3; update the application to fetch assets via the accelerated S3 endpoint
- ▫️ Deploy the application in multiple Regions and use Route 53 latency-based routing to direct users to the nearest ALB
- ▫️ Put AWS Global Accelerator in front of the ALB and update Route 53 to alias the app’s domain to the accelerator

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create an Amazon CloudFront distribution with the ALB as the origin, and point the application’s Route 53 alias to the CloudFront domain

CloudFront places a CDN in front of your ALB so content is served from AWS edge locations close to users, reducing latency and offloading traffic from the origin. It natively supports ALB as a (custom/VPC) origin and is specifically designed to accelerate both static and dynamic content delivery globally with low operational effort—just deploy the distribution and point DNS at it. This directly addresses “global performance” while keeping the app single-Region behind the scenes.

Incorrect options:

Enable Amazon S3 Transfer Acceleration and move static assets to S3; update the application to fetch assets via the accelerated S3 endpoint - Transfer Acceleration speeds uploads/downloads to S3 buckets using the CloudFront edge network, which can help media/file transfer use cases. But the LMS runs behind an ALB on EC2, and end-to-end page delivery (including dynamic requests) won’t be accelerated unless the entire site is refactored to serve from S3. Even then, Transfer Acceleration doesn’t accelerate ALB-backed dynamic responses. This adds refactoring effort while not addressing the core need as directly as CloudFront in front of the ALB.

Deploy the application in multiple Regions and use Route 53 latency-based routing to direct users to the nearest ALB - Latency-based routing improves global performance when you already run endpoints in multiple Regions; Route 53 then answers with the lowest-latency Region. However, standing up and operating a multi-Region LMS (compute, data replication, state, failover, deployments) is significantly higher operational overhead than adding a global CDN layer. Because the requirement emphasizes the least ops burden, CloudFront in front of the existing single-Region ALB is the simpler and more appropriate solution.

Put AWS Global Accelerator in front of the ALB and update Route 53 to alias the app’s domain to the accelerator - Global Accelerator improves network pathing by onboarding traffic to the AWS global backbone near users and routing it to optimal regional endpoints; it also provides static anycast IPs and fast failover. However, it does not cache content. For a web application heavy on static assets and page resources, CloudFront typically yields greater latency reduction with less tuning because it both shortens the network path and serves cached content from edge PoPs. Global Accelerator can help for purely dynamic, TCP/UDP workloads, but it’s not the “least-overhead” win for broad web acceleration versus CloudFront in this scenario.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/HowCloudFrontWorks.html> · <https://docs.aws.amazon.com/global-accelerator/latest/dg/what-is-global-accelerator.html?utm_source=chatgpt.com>

</details>

---

<a id="t04-q26"></a>
### T04-Q26
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

An Auto Scaling Group (ASG) of Amazon EC2 instances is being created for processing messages from an Amazon SQS queue. To ensure the EC2 instances are cost-effective a Developer would like to configure the ASG to maintain aggregate CPU utilization at 70%.

Which type of scaling policy should the Developer choose?

- ✅ Target Tracking Scaling Policy
- ▫️ Scheduled Scaling Policy
- ▫️ Step Scaling Policy
- ▫️ Simple Scaling Policy

<details><summary>Пояснення (оригінал, EN)</summary>

With target tracking scaling policies, you select a scaling metric and set a target value. Amazon EC2 Auto Scaling creates and manages the CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and the target value. The scaling policy adds or removes capacity as required to keep the metric at, or close to, the specified target value. In addition to keeping the metric close to the target value, a target tracking scaling policy also adjusts to the changes in the metric due to a changing load pattern.

For example, you can use target tracking scaling to:

• Configure a target tracking scaling policy to keep the average aggregate CPU utilization of your Auto Scaling group at 40 percent.

• Configure a target tracking scaling policy to keep the request count per target of your Elastic Load Balancing target group at 1000 for your Auto Scaling group.

The target tracking scaling policy is therefore the best choice for this scenario.

CORRECT: "Target Tracking Scaling Policy" is the correct answer.

INCORRECT: "Step Scaling Policy" is incorrect. (explanation below)

INCORRECT: "Simple Scaling Policy" is incorrect. (explanation below)

With step scaling and simple scaling, you choose scaling metrics and threshold values for the CloudWatch alarms that trigger the scaling process. You also define how your Auto Scaling group should be scaled when a threshold is in breach for a specified number of evaluation periods.

INCORRECT: "Scheduled Scaling Policy" is incorrect as this is used to schedule a scaling action at a specific time and date rather than dynamically adjusting according to load.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html>

</details>

---

<a id="t05-q05"></a>
### T05-Q05
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A company is running a web application on Amazon EC2 behind an Elastic Load Balancer (ELB). The company is concerned about the security of the web application and would like to secure the application with SSL certificates. The solution should not have any performance impact on the EC2 instances.

What steps should be taken to secure the web application? (Select TWO.)

- ✅ Add an SSL certificate to the Elastic Load Balancer
- ▫️ Configure the Elastic Load Balancer with SSL passthrough
- ▫️ Install SSL certificates on the EC2 instances
- ✅ Configure the Elastic Load Balancer for SSL termination
- ▫️ Configure Server-Side Encryption with KMS managed keys

<details><summary>Пояснення (оригінал, EN)</summary>

The requirements clearly state that we cannot impact the performance of the EC2 instances at all. Therefore, we will not be able to add certificates to the EC2 instances as that would place a burden on the CPU when encrypting and decrypting data.

We are therefore left with configuring SSL on the Elastic Load Balancer itself. For this we need to add an SSL certificate to the ELB and then configure the ELB for SSL termination.

You can create an HTTPS listener, which uses encrypted connections (also known as SSL offload). This feature enables traffic encryption between your load balancer and the clients that initiate SSL or TLS sessions.

To use an HTTPS listener, you must deploy at least one SSL/TLS server certificate on your load balancer. The load balancer uses a server certificate to terminate the front-end connection and then decrypt requests from clients before sending them to the targets.

This is the most secure solution we can created without adding any performance impact to the EC2 instances.

CORRECT: "Add an SSL certificate to the Elastic Load Balancer" is a correct answer.

CORRECT: "Configure the Elastic Load Balancer for SSL termination" is also a correct answer.

INCORRECT: "Configure the Elastic Load Balancer with SSL passthrough" is incorrect as this would be used to forward encrypted packets directly to the EC2 instance for termination but we do not want to add SSL certificates to the EC2 instances due to the extra processing required.

INCORRECT: "Install SSL certificates on the EC2 instances" is incorrect as we do not want to add SSL certificates to the EC2 instances due to the extra processing required.

INCORRECT: "Configure Server-Side Encryption with KMS managed keys" is incorrect as this applies to Amazon S3, not ELB.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html>

</details>

---

<a id="t05-q60"></a>
### T05-Q60
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An application uses an Auto Scaling group of Amazon EC2 instances, an Application Load Balancer (ALB), and an Amazon Simple Queue Service (SQS) queue. An Amazon CloudFront distribution caches content for global users. A Developer needs to add in-transit encryption to the data by configuring end-to-end SSL between the CloudFront Origin and the end users.

How can the Developer meet this requirement? (Select TWO.)

- ▫️ Add a certificate to the Auto Scaling Group
- ✅ Configure the Viewer Protocol Policy
- ▫️ Create an Origin Access Identity (OAI)
- ✅ Configure the Origin Protocol Policy
- ▫️ Create an encrypted distribution

<details><summary>Пояснення (оригінал, EN)</summary>

For web distributions, you can configure CloudFront to require that viewers use HTTPS to request your objects, so that connections are encrypted when CloudFront communicates with viewers. You also can configure CloudFront to use HTTPS to get objects from your origin, so that connections are encrypted when CloudFront communicates with your origin.

If you configure CloudFront to require HTTPS both to communicate with viewers and to communicate with your origin, here's what happens when CloudFront receives a request for an object:

1. A viewer submits an HTTPS request to CloudFront. There's some SSL/TLS negotiation here between the viewer and CloudFront. In the end, the viewer submits the request in an encrypted format.

2. If the object is in the CloudFront edge cache, CloudFront encrypts the response and returns it to the viewer, and the viewer decrypts it.

3. If the object is not in the CloudFront cache, CloudFront performs SSL/TLS negotiation with your origin and, when the negotiation is complete, forwards the request to your origin in an encrypted format.

4. Your origin decrypts the request, encrypts the requested object, and returns the object to CloudFront.

5. CloudFront decrypts the response, re-encrypts it, and forwards the object to the viewer. CloudFront also saves the object in the edge cache so that the object is available the next time it's requested.

6. The viewer decrypts the response.

To enable SSL between the origin and the distribution the Developer can configure the Origin Protocol Policy. Depending on the domain name used (CloudFront default or custom), the steps are different. To enable SSL between the end-user and CloudFront the Viewer Protocol Policy should be configured.

CORRECT: "Configure the Origin Protocol Policy" is a correct answer.

CORRECT: "Configure the Viewer Protocol Policy" is also a correct answer.

INCORRECT: "Create an Origin Access Identity (OAI)" is incorrect as this is a special user used for securing objects in Amazon S3 origins.

INCORRECT: "Add a certificate to the Auto Scaling Group" is incorrect as you do not add certificates to an ASG. The certificate should be located on the ALB listener in this scenario.

INCORRECT: "Create an encrypted distribution" is incorrect as there’s no such thing as an encrypted distribution

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-viewers-to-cloudfront.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-cloudfront-to-custom-origin.html>

</details>

---

<a id="t07-q18"></a>
### T07-Q18
*⏭ пропущено · домен: офіційна мітка*

An application runs on an EC2 instance and processes orders on a nightly basis. This EC2 instance needs to access the orders that are stored in S3.

How would you recommend the EC2 instance access the orders securely?

- ✅ Use an IAM role
- ▫️ Use EC2 User Data
- ▫️ Create an S3 bucket policy that authorises public access
- ▫️ Create an IAM programmatic user and store the access key and secret access key on the EC2 ~/.aws/credentials file.

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use an IAM role

IAM roles have been incorporated so that your applications can securely make API requests from your instances, without requiring you to manage the security credentials that the applications use. Instead of creating and distributing your AWS credentials, you can delegate permission to make API requests using IAM roles.

Amazon EC2 uses an instance profile as a container for an IAM role. When you create an IAM role using the IAM console, the console creates an instance profile automatically and gives it the same name as the role to which it corresponds.

This is the most secure option as the role assigned to EC2 can be used to access S3 without storing any credentials onto the EC2 instance.

Incorrect options:

Create an IAM programmatic user and store the access key and secret access key on the EC2 ~/.aws/credentials file. - While this would work, this is highly insecure as anyone gaining access to the EC2 instance would be able to steal the credentials stored in that file.

Use EC2 User Data - EC2 User Data is used to run bootstrap scripts at an instance's first launch. This option is not the right fit for the given use-case.

Create an S3 bucket policy that authorizes public access - While this would work, it would allow anyone to access your S3 bucket files. So this option is ruled out.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html

</details>

---

<a id="t09-q41"></a>
### T09-Q41
*⏭ пропущено · домен: офіційна мітка*

A company runs its flagship application on a fleet of Amazon EC2 instances. After misplacing a couple of private keys from the SSH key pairs, they have decided to re-use their SSH key pairs for the different instances across AWS Regions.

As a Developer Associate, which of the following would you recommend to address this use-case?

- ✅ Generate a public SSH key from a private SSH key. Then, import the key into each of your AWS Regions
- ▫️ Encrypt the private SSH key and store it in the S3 bucket to be accessed from any AWS Region
- ▫️ Store the public and private SSH key pair in AWS Trusted Advisor and access it across AWS Regions
- ▫️ It is not possible to reuse SSH key pairs across AWS Regions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Generate a public SSH key from a private SSH key. Then, import the key into each of your AWS Regions

Here is the correct way of reusing SSH keys in your AWS Regions:

Generate a public SSH key (.pub) file from the private SSH key (.pem) file.

Set the AWS Region you wish to import to.

Import the public SSH key into the new Region.

Incorrect options:

It is not possible to reuse SSH key pairs across AWS Regions - As explained above, it is possible to reuse with manual import.

Store the public and private SSH key pair in AWS Trusted Advisor and access it across AWS Regions - AWS Trusted Advisor is an application that draws upon best practices learned from AWS' aggregated operational history of serving hundreds of thousands of AWS customers. Trusted Advisor inspects your AWS environment and makes recommendations for saving money, improving system performance, or closing security gaps. It does not store key pair credentials.

Encrypt the private SSH key and store it in the S3 bucket to be accessed from any AWS Region - Storing private key to Amazon S3 is possible. But, this will not make the key accessible for all AWS Regions, as is the need in the current use case.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html

</details>

---

<a id="t11-q13"></a>
### T11-Q13
*⏭ пропущено · домен: офіційна мітка*

A development team has configured inbound traffic for the relevant ports in both the Security Group of the EC2 instance as well as the Network Access Control List (NACL) of the subnet for the EC2 instance. The team is, however, unable to connect to the service running on the Amazon EC2 instance.

As a developer associate, which of the following will you recommend to fix this issue?

- ▫️ Network ACLs are stateful, so allowing inbound traffic to the necessary ports enables the connection. Security Groups are stateless, so you must allow both inbound and outbound traffic
- ▫️ IAM Role defined in the Security Group is different from the IAM Role that is given access in the Network ACLs
- ✅ Security Groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic
- ▫️ Rules associated with Network ACLs should never be modified from the command line. An attempt to modify rules from the command line blocks the rule and results in an erratic behavior

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Security Groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic - Security groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic.

To enable the connection to a service running on an instance, the associated network ACL must allow both inbound traffic on the port that the service is listening on as well as allow outbound traffic from ephemeral ports. When a client connects to a server, a random port from the ephemeral port range (1024-65535) becomes the client's source port.

The designated ephemeral port then becomes the destination port for return traffic from the service, so outbound traffic from the ephemeral port must be allowed in the network ACL.

By default, network ACLs allow all inbound and outbound traffic. If your network ACL is more restrictive, then you need to explicitly allow traffic from the ephemeral port range.

If you accept traffic from the internet, then you also must establish a route through an internet gateway. If you accept traffic over VPN or AWS Direct Connect, then you must establish a route through a virtual private gateway.

Incorrect options:

Network ACLs are stateful, so allowing inbound traffic to the necessary ports enables the connection. Security Groups are stateless, so you must allow both inbound and outbound traffic - This is incorrect as already discussed.

IAM Role defined in the Security Group is different from the IAM Role that is given access in the Network ACLs - This is a made-up option and just added as a distractor.

Rules associated with Network ACLs should never be modified from the command line. An attempt to modify rules from the command line blocks the rule and results in an erratic behavior - This option is a distractor. AWS does not support modifying rules of Network ACLs from the command line tool.

Reference:

https://aws.amazon.com/premiumsupport/knowledge-center/resolve-connection-sg-acl-inbound/

</details>

---

<a id="t11-q35"></a>
### T11-Q35
*⏭ пропущено · домен: офіційна мітка*

A cybersecurity firm wants to run their applications on single-tenant hardware to meet security guidelines.

Which of the following is the MOST cost-effective way of isolating their Amazon EC2 instances to a single tenant?

- ▫️ Spot Instances
- ▫️ Dedicated Hosts
- ✅ Dedicated Instances
- ▫️ On-Demand Instances

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Dedicated Instances - Dedicated Instances are Amazon EC2 instances that run in a virtual private cloud (VPC) on hardware that's dedicated to a single customer. Dedicated Instances that belong to different AWS accounts are physically isolated at a hardware level, even if those accounts are linked to a single-payer account. However, Dedicated Instances may share hardware with other instances from the same AWS account that are not Dedicated Instances.

A Dedicated Host is also a physical server that's dedicated for your use. With a Dedicated Host, you have visibility and control over how instances are placed on the server.

Differences between Dedicated Hosts and Dedicated Instances:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html#dedicated-hosts-dedicated-instances

Incorrect options:

Spot Instances - A Spot Instance is an unused EC2 instance that is available for less than the On-Demand price. Your Spot Instance runs whenever capacity is available and the maximum price per hour for your request exceeds the Spot price. Any instance present with unused capacity will be allocated. Even though this is cost-effective, it does not fulfill the single-tenant hardware requirement of the client and hence is not the correct option.

Dedicated Hosts - An Amazon EC2 Dedicated Host is a physical server with EC2 instance capacity fully dedicated to your use. Dedicated Hosts allow you to use your existing software licenses on EC2 instances. With a Dedicated Host, you have visibility and control over how instances are placed on the server. This option is costlier than the Dedicated Instance and hence is not the right choice for the current requirement.

On-Demand Instances - With On-Demand Instances, you pay for compute capacity by the second with no long-term commitments. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it. Hardware isolation is not possible and on-demand has one of the costliest instance charges and hence is not the correct answer for current requirements.

High Level Overview of EC2 Instance Purchase Options:  via - https://aws.amazon.com/ec2/pricing/

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html>

</details>

---
