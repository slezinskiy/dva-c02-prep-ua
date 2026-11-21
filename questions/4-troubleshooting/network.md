# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 4 — Усунення несправностей та оптимізація** · питань: 26  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q32"></a>
### T01-Q32
*⏭ пропущено · домен: офіційна мітка*

You are responsible for an application that runs on multiple Amazon EC2 instances. In front of the instances is an Internet-facing load balancer that takes requests from clients over the internet and distributes them to the EC2 instances. A health check is configured to ping the index.html page found in the root directory for the health status. When accessing the website via the internet visitors of the website receive timeout errors.

What should be checked first to resolve the issue?

- ✅ Security Groups
- ▫️ IAM Roles
- ▫️ The application is down
- ▫️ The ALB is warming up

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Security Groups

A security group acts as a virtual firewall for your EC2 instances to control incoming and outgoing traffic. Inbound rules control the incoming traffic to your instance, and outbound rules control the outgoing traffic from your instance.

Check the security group rules of your EC2 instance. You need a security group rule that allows inbound traffic from your public IPv4 address on the proper port.

Incorrect options:

IAM Roles - Usually you run into issues with authorization of APIs with roles but not for timeout, so this option does not fit the given use-case.

The application is down - Although you can set a health check for application ping or HTTP, timeouts are usually caused by blocked firewall access.

The ALB is warming up - ALB has a slow start mode which allows a warm-up period before being able to respond to requests with optimal performance. So this is not the issue.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#TroubleshootingInstancesConnectionTimeout

</details>

---

<a id="t01-q36"></a>
### T01-Q36
*⏭ пропущено · домен: офіційна мітка*

You are working with a t2.small instance bastion host that has the AWS CLI installed to help manage all the AWS services installed on it. You would like to know the security group and the instance id of the current instance.

Which of the following will help you fetch the needed information?

- ▫️ Query the user data at http://169.254.169.254/latest/user-data
- ✅ Query the metadata at http://169.254.169.254/latest/meta-data
- ▫️ Query the user data at http://254.169.254.169/latest/meta-data
- ▫️ Create an IAM role and attach it to your EC2 instance that helps you perform a 'describe' API call

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Query the metadata at http://169.254.169.254/latest/meta-data - Because your instance metadata is available from your running instance, you do not need to use the Amazon EC2 console or the AWS CLI. This can be helpful when you're writing scripts to run from your instance. For example, you can access the local IP address of your instance from instance metadata to manage a connection to an external application. To view all categories of instance metadata from within a running instance, use the following URI - http://169.254.169.254/latest/meta-data/. The IP address 169.254.169.254 is a link-local address and is valid only from the instance. All instance metadata is returned as text (HTTP content type text/plain).

Incorrect options:

Create an IAM role and attach it to your EC2 instance that helps you perform a 'describe' API call - The AWS CLI has a describe-instances API call needs instance ID as an input. So, this will not work for the current use case wherein we do not know the instance ID.

Query the user data at http://169.254.169.254/latest/user-data - This address retrieves the user data that you specified when launching your instance.

Query the user data at http://254.169.254.169/latest/meta-data - The IP address specified is wrong.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html> · <https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-instances.html>

</details>

---

<a id="t02-q55"></a>
### T02-Q55
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer must identify the public IP addresses of clients connecting to Amazon EC2 instances behind a public Application Load Balancer (ALB). The EC2 instances run an HTTP server that logs all requests to a log file.

How can the developer ensure the client public IP addresses are captured in the log files on the EC2 instances?

- ▫️ Install the Amazon CloudWatch Logs agent on the EC2 instances and configure logging.
- ▫️ Install the AWS X-Ray daemon on the EC2 instances and configure request logging.
- ❌ Configure the HTTP server to add the x-forwarded-proto request header to the logs.
- ✅ Configure the HTTP server to add the x-forwarded-for request header to the logs.

<details><summary>Пояснення (оригінал, EN)</summary>

The X-Forwarded-For request header is automatically added and helps you identify the IP address of a client when you use an HTTP or HTTPS load balancer.

Because load balancers intercept traffic between clients and servers, your server access logs contain only the IP address of the load balancer. To see the IP address of the client, use the X-Forwarded-For request header.

The HTTP server may need to be configured to include the x-forwarded-for request header in the log files. Once this is done, the logs will contain the public IP addresses of the clients.

CORRECT: "Configure the HTTP server to add the x-forwarded-for request header to the logs" is the correct answer (as explained above.)

INCORRECT: "Configure the HTTP server to add the x-forwarded-proto request header to the logs" is incorrect.

This request header identifies the protocol (HTTP or HTTPS).

INCORRECT: "Install the AWS X-Ray daemon on the EC2 instances and configure request logging" is incorrect.

X-Ray is used for tracing applications; it will not help identify the public IP addresses of clients.

INCORRECT: "Install the Amazon CloudWatch Logs agent on the EC2 instances and configure logging" is incorrect.

The Amazon CloudWatch Logs agent will send application and system logs to CloudWatch Logs. This does not help to capture the client IP addresses of connections.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/x-forwarded-headers.html#x-forwarded-for>

</details>

---

<a id="t03-q46"></a>
### T03-Q46
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Сесії між blue/green → зовнішнє сховище (ElastiCache).** → [детальніше](../../06-contradictions-and-traps.md#c01)

An IT company uses a blue/green deployment policy to provision new Amazon EC2 instances in an Auto Scaling group behind a new Application Load Balancer for each new application version. The current set up requires the users to log in after every new deployment.

As a Developer Associate, what advice would you give to the company for resolving this issue?

- ▫️ Use rolling updates instead of a blue/green deployment
- ▫️ Enable sticky sessions in the Application Load Balancer
- ▫️ Use multicast to replicate session information
- ✅ Use ElastiCache to maintain user sessions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use ElastiCache to maintain user sessions

Amazon ElastiCache allows you to seamlessly set up, run, and scale popular open-Source compatible in-memory data stores in the cloud. Build data-intensive apps or boost the performance of your existing databases by retrieving data from high throughput and low latency in-memory data stores. Amazon ElastiCache is a popular choice for real-time use cases like Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics, and Queuing.

To address scalability and to provide a shared data storage for sessions that can be accessed from any individual web server, you can abstract the HTTP sessions from the web servers themselves. A common solution to for this is to leverage an In-Memory Key/Value store such as Redis and Memcached via ElastiCache.

 via - https://aws.amazon.com/caching/session-management/

Incorrect options:

Use rolling updates instead of a blue/green deployment - With rolling deployments, Elastic Beanstalk splits the environment's Amazon EC2 instances into batches and deploys the new version of the application to one batch at a time. It leaves the rest of the instances in the environment running the old version of the application. When processing a batch, Elastic Beanstalk detaches all instances in the batch from the load balancer, deploys the new application version, and then reattaches the instances.

This means that some of the users can experience session disruptions when the instances maintaining the sessions were detached as part of the given batch. So this option is incorrect.

Enable sticky sessions in the Application Load Balancer - As the Application Load Balancer itself is replaced on each new deployment, so maintaining sticky sessions via the Application Load Balancer will not work.

Use multicast to replicate session information - This option has been added as a distractor.

Джерела: <https://aws.amazon.com/caching/session-management/> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html#environments-cfg-rollingdeployments-method>

</details>

---

<a id="t07-q01"></a>
### T07-Q01
*⏭ пропущено · домен: офіційна мітка*

You have an Auto Scaling group configured to a minimum capacity of 1 and a maximum capacity of 5, designed to launch EC2 instances across 3 Availability Zones. During a low utilization period, an entire Availability Zone went down and your application experienced downtime.

What can you do to ensure that your application remains highly available?

- ▫️ Enable RDS Multi-AZ
- ▫️ Change the scaling metric of auto-scaling policy to network bytes
- ✅ Increase the minimum instance capacity of the Auto Scaling Group to 2
- ▫️ Configure ASG fast failover

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Increase the minimum instance capacity of the Auto Scaling Group to 2 -

You configure the size of your Auto Scaling group by setting the minimum, maximum, and desired capacity. The minimum and maximum capacity are required to create an Auto Scaling group, while the desired capacity is optional. If you do not define your desired capacity upfront, it defaults to your minimum capacity.

Since a minimum capacity of 1 was defined, an instance was launched in only one AZ. This AZ went down, taking the application with it. If the minimum capacity is set to 2. As per Auto Scale AZ configuration, it would have launched 2 instances- one in each AZ, making the architecture disaster-proof and hence highly available.

 via - https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-benefits.html

Incorrect options:

Change the scaling metric of auto-scaling policy to network bytes - With target tracking scaling policies, you select a scaling metric and set a target value. You can use predefined customized metrics. Setting the metric to network bytes will not help in this context since the instances have to be spread across different AZs for high availability. The optimized way of doing it, is by defining minimum and maximum instance capacities, as discussed above.

Configure ASG fast failover - This is a made-up option, given as a distractor.

Enable RDS Multi-AZ - This configuration will make your database highly available. But for the current scenario, you will need to have more than 1 instance in separate availability zones to keep the application highly available.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-capacity-limits.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-maintain-instance-levels.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html>

</details>

---

<a id="t07-q08"></a>
### T07-Q08
*⏭ пропущено · домен: офіційна мітка*

A developer from your team has configured the load balancer to route traffic equally between instances or across Availability Zones. However, Elastic Load Balancing (ELB) routes more traffic to one instance or Availability Zone than the others.

Why is this happening and how can it be fixed? (Select two)

- ▫️ After you disable an Availability Zone, the targets in that Availability Zone remain registered with the load balancer, thereby receiving random bursts of traffic
- ✅ Instances of a specific capacity type aren’t equally distributed across Availability Zones
- ▫️ There could be short-lived TCP connections between clients and instances
- ▫️ For Application Load Balancers, cross-zone load balancing is disabled by default
- ✅ Sticky sessions are enabled for the load balancer

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Sticky sessions are enabled for the load balancer - This can be the reason for potential unequal traffic routing by the load balancer. Sticky sessions are a mechanism to route requests to the same target in a target group. This is useful for servers that maintain state information in order to provide a continuous experience to clients. To use sticky sessions, the clients must support cookies.

When a load balancer first receives a request from a client, it routes the request to a target, generates a cookie named AWSALB that encodes information about the selected target, encrypts the cookie, and includes the cookie in the response to the client. The client should include the cookie that it receives in subsequent requests to the load balancer. When the load balancer receives a request from a client that contains the cookie, if sticky sessions are enabled for the target group and the request goes to the same target group, the load balancer detects the cookie and routes the request to the same target.

If you use duration-based session stickiness, configure an appropriate cookie expiration time for your specific use case. If you set session stickiness from individual applications, use session cookies instead of persistent cookies where possible.

Instances of a specific capacity type aren’t equally distributed across Availability Zones - A Classic Load Balancer with HTTP or HTTPS listeners might route more traffic to higher-capacity instance types. This distribution aims to prevent lower-capacity instance types from having too many outstanding requests. It’s a best practice to use similar instance types and configurations to reduce the likelihood of capacity gaps and traffic imbalances.

A traffic imbalance might also occur if you have instances of similar capacities running on different Amazon Machine Images (AMIs). In this scenario, the imbalance of the traffic in favor of higher-capacity instance types is desirable.

Incorrect options:

There could be short-lived TCP connections between clients and instances - This is an incorrect statement. Long-lived TCP connections between clients and instances can potentially lead to unequal distribution of traffic by the load balancer. Long-lived TCP connections between clients and instances cause uneven traffic load distribution by design. As a result, new instances take longer to reach connection equilibrium. Be sure to check your metrics for long-lived TCP connections that might be causing routing issues in the load balancer.

For Application Load Balancers, cross-zone load balancing is disabled by default - This is an incorrect statement. With Application Load Balancers, cross-zone load balancing is always enabled.

After you disable an Availability Zone, the targets in that Availability Zone remain registered with the load balancer, thereby receiving random bursts of traffic - This is an incorrect statement. After you disable an Availability Zone, the targets in that Availability Zone remain registered with the load balancer. However, even though they remain registered, the load balancer does not route traffic to them.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#sticky-sessions> · <https://aws.amazon.com/premiumsupport/knowledge-center/elb-fix-unequal-traffic-routing/> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html#availability-zones>

</details>

---

<a id="t07-q14"></a>
### T07-Q14
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Тут sticky sessions — ПРИЧИНА проблеми, а не найкраща архітектура.** → [детальніше](../../06-contradictions-and-traps.md#c01)

You are running a cloud file storage website with an Internet-facing Application Load Balancer, which routes requests from users over the internet to 10 registered Amazon EC2 instances. Users are complaining that your website always asks them to re-authenticate when they switch pages. You are puzzled because this behavior is not seen in your local machine or dev environment.

What could be the reason?

- ▫️ The Load Balancer does not have TLS enabled
- ▫️ The EC2 instances are logging out the users because the instances never have access to the client IPs because of the Load Balancer
- ✅ The Load Balancer does not have stickiness enabled
- ▫️ Application Load Balancer is in slow-start mode, which gives ALB a little more time to read and write session data

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The Load Balancer does not have stickiness enabled - Sticky sessions are a mechanism to route requests to the same target in a target group. This is useful for servers that maintain state information to provide a continuous experience to clients. To use sticky sessions, the clients must support cookies.

When a load balancer first receives a request from a client, it routes the request to a target, generates a cookie named AWSALB that encodes information about the selected target, encrypts the cookie, and includes the cookie in the response to the client. The client should include the cookie that it receives in subsequent requests to the load balancer. When the load balancer receives a request from a client that contains the cookie, if sticky sessions are enabled for the target group and the request goes to the same target group, the load balancer detects the cookie and routes the request to the same target.

More info here:  via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#sticky-sessions

Incorrect options:

Application Load Balancer is in slow-start mode, which gives ALB a little more time to read and write session data - This is an invalid statement. The load balancer serves as a single point of contact for clients and distributes incoming traffic across its healthy registered targets. By default, a target starts to receive its full share of requests as soon as it is registered with a target group and passes an initial health check. Using slow start mode gives targets time to warm up before the load balancer sends them a full share of requests. This does not help in session management.

The EC2 instances are logging out the users because the instances never have access to the client IPs because of the Load Balancer - This is an incorrect statement. Elastic Load Balancing stores the IP address of the client in the X-Forwarded-For request header and passes the header to the server. If needed, the server can read IP addresses from this data.

The Load Balancer does not have TLS enabled - To use an HTTPS listener, you must deploy at least one SSL/TLS server certificate on your load balancer. The load balancer uses a server certificate to terminate the front-end connection and then decrypt requests from clients before sending them to the targets. This does not help in session management.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#sticky-sessions> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#slow-start-mode>

</details>

---

<a id="t07-q22"></a>
### T07-Q22
*⏭ пропущено · домен: офіційна мітка*

A telecommunications company that provides internet service for mobile device users maintains over 100 c4.large instances in the us-east-1 region. The EC2 instances run complex algorithms. The manager would like to track CPU utilization of the EC2 instances as frequently as every 10 seconds.

Which of the following represents the BEST solution for the given use-case?

- ▫️ Simply get it from the CloudWatch Metrics
- ▫️ Enable EC2 detailed monitoring
- ✅ Create a high-resolution custom metric and push the data using a script triggered every 10 seconds
- ▫️ Open a support ticket with AWS

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a high-resolution custom metric and push the data using a script triggered every 10 seconds

Using high-resolution custom metric, your applications can publish metrics to CloudWatch with 1-second resolution. You can watch the metrics scroll across your screen seconds after they are published and you can set up high-resolution CloudWatch Alarms that evaluate as frequently as every 10 seconds. You can alert with High-Resolution Alarms, as frequently as 10-second periods. High-Resolution Alarms allow you to react and take actions faster and support the same actions available today with standard 1-minute alarms.

 via - https://aws.amazon.com/blogs/aws/new-high-resolution-custom-metrics-and-alarms-for-amazon-cloudwatch/

Incorrect options:

Enable EC2 detailed monitoring - As part of basic monitoring, Amazon EC2 sends metric data to CloudWatch in 5-minute periods. To send metric data for your instance to CloudWatch in 1-minute periods, you can enable detailed monitoring on the instance, however, this comes at an additional cost.

Simply get it from the CloudWatch Metrics - You can get data from metrics. The basic monitoring data is available automatically in a 5-minute interval and detailed monitoring data is available in a 1-minute interval.

Open a support ticket with AWS - This option has been added as a distractor.

Reference:

https://aws.amazon.com/blogs/aws/new-high-resolution-custom-metrics-and-alarms-for-amazon-cloudwatch/

</details>

---

<a id="t09-q15"></a>
### T09-Q15
*⏭ пропущено · домен: офіційна мітка*

You create an Auto Scaling group to work with an Application Load Balancer. The scaling group is configured with a minimum size value of 5, a maximum value of 20, and the desired capacity value of 10. One of the 10 EC2 instances has been reported as unhealthy.

Which of the following actions will take place?

- ▫️ The ASG will detach the EC2 instance from the group, and leave it running
- ▫️ The ASG will format the root EBS drive on the EC2 instance and run the User Data again
- ▫️ The ASG will keep the instance running and re-start the application
- ✅ The ASG will terminate the EC2 Instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The ASG will terminate the EC2 Instance

To maintain the same number of instances, Amazon EC2 Auto Scaling performs a periodic health check on running instances within an Auto Scaling group. When it finds that an instance is unhealthy, it terminates that instance and launches a new one. Amazon EC2 Auto Scaling creates a new scaling activity for terminating the unhealthy instance and then terminates it. Later, another scaling activity launches a new instance to replace the terminated instance.

Incorrect options:

The ASG will detach the EC2 instance from the group, and leave it running - The goal of the auto-scaling group is to get rid of the bad instance and replace it

The ASG will keep the instance running and re-start the application - The ASG does not have control of your application

The ASG will format the root EBS drive on the EC2 instance and run the User Data again - This will not happen, the ASG cannot assume the format of your EBS drive, and User Data only runs once at instance first boot.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/auto-scaling-terminate-instance> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-maintain-instance-levels.html#replace-unhealthy-instance>

</details>

---

<a id="t09-q33"></a>
### T09-Q33
*⏭ пропущено · домен: офіційна мітка*

A company is using a Border Gateway Protocol (BGP) based AWS VPN connection to connect from its on-premises data center to Amazon EC2 instances in the company’s account. The development team can access an EC2 instance in subnet A but is unable to access an EC2 instance in subnet B in the same VPC.

Which logs can be used to verify whether the traffic is reaching subnet B?

- ▫️ Subnet logs
- ▫️ BGP logs
- ✅ VPC Flow Logs
- ▫️ VPN logs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

VPC Flow Logs - VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data can be published to Amazon CloudWatch Logs or Amazon S3. After you've created a flow log, you can retrieve and view its data in the chosen destination.

You can create a flow log for a VPC, a subnet, or a network interface. If you create a flow log for a subnet or VPC, each network interface in that subnet or VPC is monitored.

Flow log data for a monitored network interface is recorded as flow log records, which are log events consisting of fields that describe the traffic flow.

To create a flow log, you specify:

The resource for which to create the flow log

The type of traffic to capture (accepted traffic, rejected traffic, or all traffic)

The destinations to which you want to publish the flow log data

Incorrect options:

VPN logs

Subnet logs

BGP logs

These three options are incorrect and have been added as distractors.

Reference:

https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html

</details>

---

<a id="t09-q37"></a>
### T09-Q37
*⏭ пропущено · домен: офіційна мітка*

While troubleshooting, a developer realized that the Amazon EC2 instance is unable to connect to the Internet using the Internet Gateway.

Which conditions should be met for Internet connectivity to be established? (Select two)

- ▫️ The instance's subnet is associated with multiple route tables with conflicting configurations
- ✅ The network ACLs associated with the subnet must have rules to allow inbound and outbound traffic
- ▫️ The instance's subnet is not associated with any route table
- ▫️ The subnet has been configured to be Public and has no access to the internet
- ✅ The route table in the instance’s subnet should have a route to an Internet Gateway

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

The network ACLs associated with the subnet must have rules to allow inbound and outbound traffic - The network access control lists (ACLs) that are associated with the subnet must have rules to allow inbound and outbound traffic on port 80 (for HTTP traffic) and port 443 (for HTTPs traffic). This is a necessary condition for Internet Gateway connectivity

The route table in the instance’s subnet should have a route to an Internet Gateway - A route table contains a set of rules, called routes, that are used to determine where network traffic from your subnet or gateway is directed. The route table in the instance’s subnet should have a route defined to the Internet Gateway.

Incorrect options:

The instance's subnet is not associated with any route table - This is an incorrect statement. A subnet is implicitly associated with the main route table if it is not explicitly associated with a particular route table. So, a subnet is always associated with some route table.

The instance's subnet is associated with multiple route tables with conflicting configurations - This is an incorrect statement. A subnet can only be associated with one route table at a time.

The subnet has been configured to be Public and has no access to internet - This is an incorrect statement. Public subnets have access to the internet via Internet Gateway.

Reference:

https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html

</details>

---

<a id="t09-q47"></a>
### T09-Q47
*⏭ пропущено · домен: офіційна мітка*

A Developer is configuring Amazon EC2 Auto Scaling group to scale dynamically.

Which metric below is NOT part of Target Tracking Scaling Policy?

- ▫️ ASGAverageNetworkOut
- ▫️ ALBRequestCountPerTarget
- ▫️ ASGAverageCPUUtilization
- ✅ ApproximateNumberOfMessagesVisible

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

ApproximateNumberOfMessagesVisible - This is a CloudWatch Amazon SQS queue metric. The number of messages in a queue might not change proportionally to the size of the Auto Scaling group that processes messages from the queue. Hence, this metric does not work for target tracking.

Incorrect options:

With target tracking scaling policies, you select a scaling metric and set a target value. Amazon EC2 Auto Scaling creates and manages the CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and the target value.

It is important to note that a target tracking scaling policy assumes that it should scale out your Auto Scaling group when the specified metric is above the target value. You cannot use a target tracking scaling policy to scale out your Auto Scaling group when the specified metric is below the target value.

ASGAverageCPUUtilization - This is a predefined metric for target tracking scaling policy. This represents the Average CPU utilization of the Auto Scaling group.

ASGAverageNetworkOut - This is a predefined metric for target tracking scaling policy. This represents the Average number of bytes sent out on all network interfaces by the Auto Scaling group.

ALBRequestCountPerTarget - This is a predefined metric for target tracking scaling policy. This represents the Number of requests completed per target in an Application Load Balancer target group.

Reference:

https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html

</details>

---

<a id="t09-q50"></a>
### T09-Q50
*⏭ пропущено · домен: офіційна мітка*

A junior developer has been asked to configure access to an Amazon EC2 instance hosting a web application. The developer has configured a new security group to permit incoming HTTP traffic from 0.0.0.0/0 and retained any default outbound rules. A custom Network Access Control List (NACL) connected with the instance's subnet is configured to permit incoming HTTP traffic from 0.0.0.0/0 and retained any default outbound rules.

Which of the following solutions would you suggest if the EC2 instance needs to accept and respond to requests from the internet?

- ✅ An outbound rule must be added to the Network ACL (NACL) to allow the response to be sent to the client on the ephemeral port range
- ▫️ An outbound rule on the security group has to be configured, to allow the response to be sent to the client on the HTTP port
- ▫️ The configuration is complete on the EC2 instance for accepting and responding to requests
- ▫️ Outbound rules need to be configured both on the security group and on the NACL for sending responses to the Internet Gateway

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

An outbound rule must be added to the Network ACL (NACL) to allow the response to be sent to the client on the ephemeral port range

Security groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic. By default, each custom Network ACL denies all inbound and outbound traffic until you add rules.

To enable the connection to a service running on an instance, the associated network ACL must allow both: 1. Inbound traffic on the port that the service is listening on 2. Outbound traffic to ephemeral ports

When a client connects to a server, a random port from the ephemeral port range (1024-65535) becomes the client's source port.

The designated ephemeral port becomes the destination port for return traffic from the service. Outbound traffic to the ephemeral port must be allowed in the network ACL.

Incorrect options:

The configuration is complete on the EC2 instance for accepting and responding to requests - As explained above, this is an incorrect statement.

An outbound rule on the security group has to be configured, to allow the response to be sent to the client on the HTTP port - Security groups are stateful. Therefore you don't need a rule that allows responses to inbound traffic.

Outbound rules need to be configured both on the security group and on the NACL for sending responses to the Internet Gateway\* - Security Groups are stateful. Hence, return traffic is automatically allowed, so there is no need to configure an outbound rule on the security group.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/resolve-connection-sg-acl-inbound/> · <https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports>

</details>

---

<a id="t09-q60"></a>
### T09-Q60
*⏭ пропущено · домен: офіційна мітка*

A business hosts its website on Amazon EC2 instances and employs Auto Scaling to adjust its resources according to traffic spikes. However, users globally report slow loading times because static content hosted on the EC2 instances takes too long to load, even outside of busy periods.

What pair of actions should be taken to improve the latency of the website? (Select two)

- ▫️ Migrate the application to AWS Lambda
- ✅ Transfer the application’s static content hosted on EC2 instances to Amazon S3
- ▫️ Double the Auto Scaling group’s desired capacity
- ✅ Set up an Amazon CloudFront distribution to cache the static content with Amazon S3 configured as the origin
- ▫️ Upgrade the CPU and RAM available to the EC2 instances

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up an Amazon CloudFront distribution to cache the static content with Amazon S3 configured as the origin

Transfer the application’s static content hosted on EC2 instances to Amazon S3

Amazon CloudFront is a web service that speeds up distribution of your static and dynamic web content, such as .html, .css, .js, and image files, to your users. CloudFront delivers your content through a worldwide network of data centers called edge locations. When a user requests content that you're serving with CloudFront, the request is routed to the edge location that provides the lowest latency (time delay), so that content is delivered with the best possible performance.

For the given use case, you can transfer the static content from EC2 instances to Amazon S3. Then, you can specify the origin as the Amazon S3 bucket from which CloudFront gets your files which will then be distributed from CloudFront edge locations all over the world. An origin stores the original, definitive version of your objects. If you're serving content over HTTP, your origin is either an Amazon S3 bucket or an HTTP server, such as a web server.

 via - https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html

Incorrect options:

Upgrade the CPU and RAM available to the EC2 instances - Since the static content takes too long to load even outside of busy periods, this implies that the underlying root cause is the high end-to-end network latency rather than the hardware of the EC2 instance.

Double the Auto Scaling group’s desired capacity - The desired capacity represents the initial capacity of the Auto Scaling group at the time of creation. An Auto Scaling group attempts to maintain the desired capacity. It starts by launching the number of instances that are specified for the desired capacity, and maintains this number of instances as long as there are no scaling policies or scheduled actions attached to the Auto Scaling group. Since the static content takes too long to load even outside of busy periods, so doubling the desired capacity would still not address the underlying root cause of high end-to-end network latency.

Migrate the application to AWS Lambda - You cannot store static content on AWS Lambda, so this option just serves as a distractor.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/GettingStarted.SimpleDistribution.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html>

</details>

---

<a id="t11-q09"></a>
### T11-Q09
*⏭ пропущено · домен: офіційна мітка*

An organization has offices across multiple locations and the technology team has configured an Application Load Balancer across targets in multiple Availability Zones. The team wants to analyze the incoming requests for latencies and the client's IP address patterns.

Which feature of the Load Balancer will help collect the required information?

- ▫️ CloudWatch metrics
- ▫️ CloudTrail logs
- ▫️ ALB request tracing
- ✅ ALB access logs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

ALB access logs - Elastic Load Balancing provides access logs that capture detailed information about requests sent to your load balancer. Each log contains information such as the time the request was received, the client's IP address, latencies, request paths, and server responses. You can use these access logs to analyze traffic patterns and troubleshoot issues. Access logging is an optional feature of Elastic Load Balancing that is disabled by default.

Access logs for your Application Load Balancer:  via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html

Incorrect options:

CloudTrail logs - Elastic Load Balancing is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in Elastic Load Balancing. CloudTrail captures all API calls for Elastic Load Balancing as events. You can use AWS CloudTrail to capture detailed information about the calls made to the Elastic Load Balancing API and store them as log files in Amazon S3. You can use these CloudTrail logs to determine which API calls were made, the source IP address where the API call came from, who made the call, when the call was made, and so on.

CloudWatch metrics - Elastic Load Balancing publishes data points to Amazon CloudWatch for your load balancers and your targets. CloudWatch enables you to retrieve statistics about those data points as an ordered set of time-series data, known as metrics. You can use metrics to verify that your system is performing as expected. This is the right feature if you wish to track a certain metric.

ALB request tracing - You can use request tracing to track HTTP requests. The load balancer adds a header with a trace identifier to each request it receives. Request tracing will not help you to analyze latency specific data.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-monitoring.html

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html

</details>

---

<a id="t11-q41"></a>
### T11-Q41
*⏭ пропущено · домен: офіційна мітка*

A firm runs its technology operations on a fleet of Amazon EC2 instances. The firm needs a certain software to be available on the instances to support their daily workflows. The developer team has been told to use the user data feature of EC2 instances.

Which of the following are true about the user data EC2 configuration? ( Select two)

By default, scripts entered as user data do not have root user privileges for executing

- ✅ By default, scripts entered as user data are executed with root user privileges
- ▫️ When an instance is running, you can update user data by using root user credentials
- ▫️ By default, user data is executed every time an EC2 instance is re-started
- ✅ By default, user data runs only during the boot cycle when you first launch an instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

User Data is generally used to perform common automated configuration tasks and even run scripts after the instance starts. When you launch an instance in Amazon EC2, you can pass two types of user data - shell scripts and cloud-init directives. You can also pass this data into the launch wizard as plain text or as a file.

By default, scripts entered as user data are executed with root user privileges - Scripts entered as user data are executed as the root user, hence do not need the sudo command in the script. Any files you create will be owned by root; if you need non-root users to have file access, you should modify the permissions accordingly in the script.

By default, user data runs only during the boot cycle when you first launch an instance - By default, user data scripts and cloud-init directives run only during the boot cycle when you first launch an instance. You can update your configuration to ensure that your user data scripts and cloud-init directives run every time you restart your instance.

Incorrect options:

By default, user data is executed every time an EC2 instance is re-started - As discussed above, this is not a default configuration of the system. But, can be achieved by explicitly configuring the instance.

When an instance is running, you can update user data by using root user credentials - You can't change the user data if the instance is running (even by using root user credentials), but you can view it.

By default, scripts entered as user data do not have root user privileges for executing - Scripts entered as user data are executed as the root user, hence do not need the sudo command in the script.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html

</details>

---

<a id="t11-q65"></a>
### T11-Q65
*⏭ пропущено · домен: офіційна мітка*

You have created an Elastic Load Balancer that has marked all the EC2 instances in the target group as unhealthy. Surprisingly, when you enter the IP address of the EC2 instances in your web browser, you can access your website.

What could be the reason your instances are being marked as unhealthy? (Select two)

- ✅ The route for the health check is misconfigured
- ✅ The security group of the EC2 instance does not allow for traffic from the security group of the Application Load Balancer
- ▫️ Your web-app has a runtime that is not supported by the Application Load Balancer
- ▫️ You need to attach Elastic IP to the EC2 instances
- ▫️ The EBS volumes have been improperly mounted

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options

The security group of the EC2 instance does not allow for traffic from the security group of the Application Load Balancer

The route for the health check is misconfigured

You must ensure that your load balancer can communicate with registered targets on both the listener port and the health check port. Whenever you add a listener to your load balancer or update the health check port for a target group used by the load balancer to route requests, you must verify that the security groups associated with the load balancer allow traffic on the new port in both directions.

Application Load Balancer Configuration for Security Groups and Health Check Routes:  via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html

Incorrect options:

The EBS volumes have been improperly mounted - You can access the website using the IP address which means there is no issue with the EBS volumes. So this option is not correct.

Your web-app has a runtime that is not supported by the Application Load Balancer - There is no connection between a web app and the application load balancer. This option has been added as a distractor.

You need to attach Elastic IP to the EC2 instances - This option is a distractor as Elastic IPs do not need to be assigned to EC2 instances while using an Application Load Balancer.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html>

</details>

---

<a id="t12-q13"></a>
### T12-Q13
*⏭ пропущено · домен: офіційна мітка*

A developer while working on Amazon EC2 instances, realized that an instance was not needed and had shut it down. But another instance of the same type automatically got launched in the account.

Which of the following options can attribute the given sequence of actions?

- ▫️ The user did not have the right permissions to shutdown the instance. User needs root permissions to terminate an instance
- ▫️ The instance could have been a part of Application Load Balancer and hence was automatically started
- ✅ Instance might be part of Auto Scaling Group and hence re-launched similar instance
- ▫️ The instance could have been a part of Network Load Balancer and hence was automatically started

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Instance might be part of Auto Scaling Group and hence re-launched similar instance - Auto Scaling groups can be configured to launch an instance to replace an instance that is undergoing maintenance. This could have been the reason why an instance of the same type got launched automatically. The size of an Auto Scaling group depends on the number of instances that you set as the desired capacity. If you wish to terminate an instance that is part of Auto Scaling Group, the configuration of the group should be changed to a reduced number of instances, so the automatic launch of instances does not happen when an unwanted instance is terminated.

Incorrect options:

The user did not have the right permissions to shutdown the instance. User needs root permissions to terminate an instance - This is an incorrect statement. If the user does not have enough permissions, then the action itself is unavailable for him. A user does not need root permissions to terminate an EC2 instance.

The instance could have been a part of the Application Load Balancer and hence was automatically started - Application Load Balancer is used to balance the incoming traffic requests equally among the available EC2 instances so keep the performance and availability at its best. ALBs are configured with Auto Scaling Groups, but this is not specified in the use-case. In the absence of Auto Scaling Group, ALB cannot launch instances by itself.

The instance could have been a part of Network Load Balancer and hence was automatically started - As explained above for ALB, a Network Load Balancer is not capable of launching instances by itself if it's not configured with an Auto Scaling Group.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/detach-instance-asg.html>

</details>

---

<a id="t12-q18"></a>
### T12-Q18
*⏭ пропущено · домен: офіційна мітка*

A development team has noticed that one of the EC2 instances has been wrongly configured with the 'DeleteOnTermination' attribute set to True for its root EBS volume.

As a developer associate, can you suggest a way to disable this flag while the instance is still running?

- ▫️ The attribute cannot be updated when the instance is running. Stop the instance from Amazon EC2 console and then update the flag
- ✅ Set the DeleteOnTermination attribute to False using the command line
- ▫️ Update the attribute using AWS management console. Select the EC2 instance and then uncheck the Delete On Termination check box for the root EBS volume
- ▫️ Set the DisableApiTermination attribute of the instance using the API

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

When an instance terminates, the value of the DeleteOnTermination attribute for each attached EBS volume determines whether to preserve or delete the volume. By default, the DeleteOnTermination attribute is set to True for the root volume and is set to False for all other volume types.

Set the DeleteOnTermination attribute to False using the command line - If the instance is already running, you can set DeleteOnTermination to False using the command line.

Incorrect options:

Update the attribute using AWS management console. Select the EC2 instance and then uncheck the Delete On Termination check box for the root EBS volume - You can set the DeleteOnTermination attribute to False when you launch a new instance. It is not possible to update this attribute of a running instance from the AWS console.

Set the DisableApiTermination attribute of the instance using the API - By default, you can terminate your instance using the Amazon EC2 console, command-line interface, or API. To prevent your instance from being accidentally terminated using Amazon EC2, you can enable termination protection for the instance. The DisableApiTermination attribute controls whether the instance can be terminated using the console, CLI, or API. This option cannot be used to control the delete status for the EBS volume when the instance terminates.

The attribute cannot be updated when the instance is running. Stop the instance from Amazon EC2 console and then update the flag - This statement is wrong and given only as a distractor.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/deleteontermination-ebs/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#delete-on-termination-running-instance>

</details>

---

<a id="t12-q20"></a>
### T12-Q20
*⏭ пропущено · домен: офіційна мітка*

A development team has inherited a web application running in the us-east-1 region with three availability zones (us-east-1a, us-east1-b, and us-east-1c) whose incoming web traffic is routed by a load balancer. When one of the EC2 instances hosting the web application crashes, the team realizes that the load balancer continues to route traffic to that instance causing intermittent issues.

Which of the following should the development team do to minimize this problem?

- ▫️ Enable Stickiness
- ▫️ Enable SSL
- ▫️ Enable Multi AZ deployments
- ✅ Enable Health Checks

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable Health Checks

To discover the availability of your EC2 instances, a load balancer periodically sends pings, attempts connections, or sends requests to test the EC2 instances. These tests are called health checks. The status of the instances that are healthy at the time of the health check is InService. The status of any instances that are unhealthy at the time of the health check is OutOfService.

Load Balancer Health Checks:  via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html

Incorrect options:

Enable Stickiness - Stickiness enables the load balancer to bind a user's session to a specific instance, it cannot be used for gauging the health of an instance.

Enable Multi-AZ deployments - It's a good practice to provision instances in more than one availability zone however you still need a way to check the health status of the instances, so this option is incorrect.

Enable SSL - This option has been added as a distractor. SSL encrypts the transmission of data between a web server and a browser.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html

</details>

---

<a id="t12-q25"></a>
### T12-Q25
*⏭ пропущено · домен: офіційна мітка*

Your web application front end consists of 5 EC2 instances behind an Application Load Balancer. You have configured your web application to capture the IP address of the client making requests. When viewing the data captured you notice that every IP address being captured is the same, which also happens to be the IP address of the Application Load Balancer.

What should you do to identify the true IP address of the client?

- ▫️ Modify the front-end of the website so that the users send their IP in the requests
- ✅ Look into the X-Forwarded-For header in the backend
- ▫️ Look into the client's cookie
- ▫️ Look into the X-Forwarded-Proto header in the backend

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Look into the X-Forwarded-For header in the backend

The X-Forwarded-For request header helps you identify the IP address of a client when you use an HTTP or HTTPS load balancer. Because load balancers intercept traffic between clients and servers, your server access logs contain only the IP address of the load balancer. To see the IP address of the client, use the X-Forwarded-For request header. Elastic Load Balancing stores the IP address of the client in the X-Forwarded-For request header and passes the header to your server.

Incorrect options:

Modify the front-end of the website so that the users send their IP in the requests - When a user makes a request the IP address is sent with the request to the server and the load balancer intercepts it. There is no need to modify the application.

Look into the X-Forwarded-Proto header in the backend - The X-Forwarded-Proto request header helps you identify the protocol (HTTP or HTTPS) that a client used to connect to your load balancer.

Look into the client's cookie - For this, we would need to modify the client-side logic and server-side logic, which would not be efficient.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/x-forwarded-headers.html

</details>

---

<a id="t12-q26"></a>
### T12-Q26
*⏭ пропущено · домен: офіційна мітка*

A development team has configured an Elastic Load Balancer for host-based routing. The idea is to support multiple subdomains and different top-level domains.

The rule \*.sample.com matches which of the following?

- ▫️ SAMPLE.COM
- ▫️ sample.test.com
- ✅ test.sample.com
- ▫️ sample.com

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

test.sample.com - You can use host conditions to define rules that route requests based on the hostname in the host header (also known as host-based routing). This enables you to support multiple subdomains and different top-level domains using a single load balancer.

A hostname is not case-sensitive, can be up to 128 characters in length, and can contain any of the following characters: 1. A–Z, a–z, 0–9 2. - . 3. \* (matches 0 or more characters) 4. ? (matches exactly 1 character)

You must include at least one "." character. You can include only alphabetical characters after the final "." character.

The rule \*.sample.com matches test.sample.com but doesn't match sample.com.

Incorrect options:

sample.com

sample.test.com

SAMPLE.COM

These three options contradict the explanation provided above, so these options are incorrect.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html

</details>

---

<a id="t12-q31"></a>
### T12-Q31
*⏭ пропущено · домен: офіційна мітка*

A developer has just completed configuring the Application Load Balancer for the EC2 instances. Just as he started testing his configuration, he realized that he has missed assigning target groups to his ALB.

Which error code should he expect in his debug logs?

- ▫️ HTTP 500
- ✅ HTTP 503
- ▫️ HTTP 403
- ▫️ HTTP 504

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

HTTP 503 - HTTP 503 indicates 'Service unavailable' error. This error in ALB is an indicator of the target groups for the load balancer having no registered targets.

Incorrect options:

HTTP 500 - HTTP 500 indicates 'Internal server' error. There are several reasons for their error: A client submitted a request without an HTTP protocol, and the load balancer was unable to generate a redirect URL, there was an error executing the web ACL rules.

HTTP 504 - HTTP 504 is 'Gateway timeout' error. Several reasons for this error, to quote a few: The load balancer failed to establish a connection to the target before the connection timeout expired, The load balancer established a connection to the target but the target did not respond before the idle timeout period elapsed.

HTTP 403 - HTTP 403 is 'Forbidden' error. You configured an AWS WAF web access control list (web ACL) to monitor requests to your Application Load Balancer and it blocked a request.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-troubleshooting.html

</details>

---

<a id="t12-q37"></a>
### T12-Q37
*⏭ пропущено · домен: офіційна мітка*

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

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

**

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
**

You can use query string conditions to configure rules that route requests based on key/value pairs or values in the query string. The match evaluation is not case-sensitive. The following wildcard characters are supported: * (matches 0 or more characters) and ? (matches exactly 1 character). You can specify conditions when you create or modify a rule.

Query parameters are often used along with the path component of the URL for applying a special logic to the resource being fetched.

The query string component starts after the first "?" in a URI. Typically query strings contain key-value pairs separated by a delimiter "&". Example: http://example.com/path/to/page?version=A&gender=female

The example condition given in the question is satisfied by requests with a query string that includes either a key/value pair of "version=v1" or any key set to "example".

Incorrect options:

**

[
  {
      "Field": "query-string",
      "PathPatternConfig": {
          "Values": ["/img/*"]
      }
  }
]
**

**

[
  {
      "Field": "query-string",
      "StringHeaderConfig": {
          "Values": ["*.example.com"]
      }
  }
]
**

These two options are malformed and are incorrect.

**

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
** - This action redirects an HTTP request to an HTTPS request on port 443, with the same hostname, path, and query string as the HTTP request.

Reference:

https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html#query-string-conditions

</details>

---

<a id="t12-q50"></a>
### T12-Q50
*⏭ пропущено · домен: офіційна мітка*

A company has configured an Auto Scaling group with health checks. The configuration is set to the desired capacity value of 3 and maximum capacity value of 3. The EC2 instances of your Auto Scaling group are configured to scale when CPU utilization is at 60 percent and is now running at 80 percent utilization.

Which of the following will take place?

- ✅ System will keep running as is
- ▫️ The desired capacity will go up to 4 and the maximum capacity will also go up to 4
- ▫️ System will trigger CloudWatch alarms to AWS support
- ▫️ The desired capacity will go up to 4 and the maximum capacity will stay at 3

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

System will keep running as is

You are already running at max capacity. After you have created your Auto Scaling group, the Auto Scaling group starts by launching enough EC2 instances to meet its minimum capacity (or its desired capacity, if specified). If there are no other scaling conditions attached to the Auto Scaling group, the Auto Scaling group maintains this number of running instances even if an instance becomes unhealthy.

Setting Capacity Limits for Your Auto Scaling Group:  via - https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-capacity-limits.html

Incorrect options:

The desired capacity will go up to 4 and the maximum capacity will stay at 3 - The desired capacity cannot go over the maximum capacity.

The desired capacity will go up to 4 and the maximum capacity will also go up to 4 - The maximum capacity cannot change on its own just because the desired capacity has been set to a higher value. You will have to make those changes to the maximum capacity manually.

System will trigger CloudWatch alarms to AWS support - This option has been added as a distractor. You already have alarms configured based on rules but AWS support will not intervene for you.

Reference:

https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-capacity-limits.html

</details>

---

<a id="t12-q62"></a>
### T12-Q62
*⏭ пропущено · домен: офіційна мітка*

A startup manages its Cloud resources with Elastic Beanstalk. The environment consists of few Amazon EC2 instances, an Auto Scaling Group (ASG), and an Elastic Load Balancer. Even after the Load Balancer marked an EC2 instance as unhealthy, the ASG has not replaced it with a healthy instance.

As a Developer, suggest the necessary configurations to automate the replacement of unhealthy instance.

- ✅ The health check type of your instance's Auto Scaling group, must be changed from EC2 to ELB by using a configuration file
- ▫️ The ping path field of the Load Balancer is configured incorrectly
- ▫️ Auto Scaling group doesn't automatically replace the unhealthy instances marked by the load balancer. They have to be manually replaced from AWS Console
- ▫️ Health check parameters were configured for checking the instance health alone. The instance failed because of application failure which was not configured as a parameter for health check status

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The health check type of your instance's Auto Scaling group, must be changed from EC2 to ELB by using a configuration file - By default, the health check configuration of your Auto Scaling group is set as an EC2 type that performs a status check of EC2 instances. To automate the replacement of unhealthy EC2 instances, you must change the health check type of your instance's Auto Scaling group from EC2 to ELB by using a configuration file.

Incorrect options:

Health check parameters were configured for checking the instance health alone. The instance failed because of application failure which was not configured as a parameter for health check status - This is an incorrect statement. Status checks, by definition, cover only an EC2 instance's health, and not the health of your application, server, or any Docker containers running on the instance.

Auto Scaling group doesn't automatically replace the unhealthy instances marked by the load balancer. They have to be manually replaced from AWS Console - Incorrect statement. As discussed above, if the health check type of ASG is changed from EC2 to ELB, Auto Scaling will be able to replace the unhealthy instance.

The ping path field of the Load Balancer is configured incorrectly - Ping path is a health check configuration field of Elastic Load Balancer. If the ping path is configured wrong, ELB will not be able to reach the instance and hence will consider the instance unhealthy. However, this would then apply to all instances, not just once instance. So it does not address the issue given in the use-case.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/elastic-beanstalk-instance-automation/> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-healthchecks.html>

</details>

---
