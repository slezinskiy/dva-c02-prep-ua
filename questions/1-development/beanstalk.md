# AWS Elastic Beanstalk

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 4  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#beanstalk) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q06"></a>
### T01-Q06
*⏭ пропущено · домен: офіційна мітка*

As part of your video processing application, you are looking to perform a set of repetitive and scheduled tasks asynchronously. Your application is deployed on Elastic Beanstalk.

Which Elastic Beanstalk environment should you set up for performing the repetitive tasks?

- ▫️ Setup a Web Server environment and a cron.yaml file
- ▫️ Setup a Web Server environment and a .ebextensions file
- ✅ Setup a Worker environment and a cron.yaml file
- ▫️ Setup a Worker environment and a .ebextensions file

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.

Elastic BeanStalk Key Concepts:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html

Setup a Worker environment and a cron.yaml file

An environment is a collection of AWS resources running an application version. An environment that pulls tasks from an Amazon Simple Queue Service (Amazon SQS) queue runs in a worker environment tier.

If your AWS Elastic Beanstalk application performs operations or workflows that take a long time to complete, you can offload those tasks to a dedicated worker environment. Decoupling your web application front end from a process that performs blocking operations is a common way to ensure that your application stays responsive under load.

For a worker environment, you need a cron.yaml file to define the cron jobs and do repetitive tasks.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-tiers.html

Incorrect options:

Setup a Web Server environment and a cron.yaml file

Setup a Worker environment and a .ebextensions file

Setup a Web Server environment and a .ebextensions file

.ebextensions/ won't work to define cron jobs, and Web Server environments cannot be set up to perform repetitive and scheduled tasks. So these three options are incorrect.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-tiers.html>

</details>

---

<a id="t01-q44"></a>
### T01-Q44
*⏭ пропущено · домен: офіційна мітка*

You would like your Elastic Beanstalk environment to expose an HTTPS endpoint instead of an HTTP endpoint to get in-flight encryption between your clients and your web servers.

What must be done to set up HTTPS on Beanstalk?

- ✅ Create a config file in the .ebextensions folder to configure the Load Balancer
- ▫️ Configure Health Checks
- ▫️ Use a separate CloudFormation template to load the SSL certificate onto the Load Balancer
- ▫️ Open up the port 80 for the security group

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The simplest way to use HTTPS with an Elastic Beanstalk environment is to assign a server certificate to your environment's load balancer. When you configure your load balancer to terminate HTTPS, the connection between the client and the load balancer is secure. Backend connections between the load balancer and EC2 instances use HTTP, so no additional configuration of the instances is required.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https.html

Create a config file in the .ebextensions folder to configure the Load Balancer

To update your AWS Elastic Beanstalk environment to use HTTPS, you need to configure an HTTPS listener for the load balancer in your environment. Two types of load balancers support an HTTPS listener: Classic Load Balancer and Application Load Balancer.

Example .ebextensions/securelistener-alb.config

Use this example when your environment has an Application Load Balancer. The example uses options in the aws:elbv2:listener namespace to configure an HTTPS listener on port 443 with the specified certificate. The listener routes traffic to the default process.

option_settings:
  aws:elbv2:listener:443:
    ListenerEnabled: 'true'
    Protocol: HTTPS
    SSLCertificateArns: arn:aws:acm:us-east-2:1234567890123:certificate/####################################
Incorrect options:

Use a separate CloudFormation template to load the SSL certificate onto the Load Balancer - A separate CloudFormation template won't be able to mutate the state of a Load Balancer managed by Elastic Beanstalk, so this option is incorrect.

Open up the port 80 for the security group - Port 80 is for HTTP traffic, so this option is incorrect.

Configure Health Checks - Health Checks are not related to SSL certificates, so this option is ruled out.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https-elb.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https.html>

</details>

---

<a id="t03-q35"></a>
### T03-Q35
*⏭ пропущено · домен: офіційна мітка*

A .NET developer team works with many ASP.NET web applications that use EC2 instances to host them on IIS. The deployment process needs to be configured so that multiple versions of the application can run in AWS Elastic Beanstalk. One version would be used for development, testing, and another version for load testing.

Which of the following methods do you recommend?

- ▫️ You cannot have multiple development environments in Elastic Beanstalk, just one development and one production environment
- ▫️ Create an Application Load Balancer to route based on hostname so you can pass on parameters to the development Elastic Beanstalk environment. Create a file in .ebextensions/ to know how to handle the traffic coming from the ALB
- ✅ Define a dev environment with a single instance and a 'load test' environment that has settings close to production environment
- ▫️ Use only one Beanstalk environment and perform configuration changes using an Ansible script

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Define a dev environment with a single instance and a 'load test' environment that has settings close to production environment

AWS Elastic Beanstalk makes it easy to create new environments for your application. You can create and manage separate environments for development, testing, and production use, and you can deploy any version of your application to any environment. Environments can be long-running or temporary. When you terminate an environment, you can save its configuration to recreate it later.

It is common practice to have many environments for the same application. You can deploy multiple environments when you need to run multiple versions of an application. So for the given use-case, you can set up 'dev' and 'load test' environment.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.html

You cannot have multiple development environments in Elastic Beanstalk, just one development, and one production environment - Incorrect, use the Create New Environment wizard in the AWS Management Console for BeanStalk to guide you on this.

Use only one Beanstalk environment and perform configuration changes using an Ansible script - Ansible is an open-source deployment tool that integrates with AWS. It allows us to deploy the infrastructure. Elastic Beanstalk provisions the servers that you need for hosting the application and it also handles multiple environments, so Beanstalk is a better option.

Create an Application Load Balancer to route based on hostname so you can pass on parameters to the development Elastic Beanstalk environment. Create a file in .ebextensions/ to know how to handle the traffic coming from the ALB - This is not a good design if you need to load test because you will have two versions on the same instances and may not be able to access resources in the system due to the load testing.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.environments.html

</details>

---

<a id="t11-q04"></a>
### T11-Q04
*⏭ пропущено · домен: офіційна мітка*

You have chosen AWS Elastic Beanstalk to upload your application code and allow it to handle details such as provisioning resources and monitoring.

When creating configuration files for AWS Elastic Beanstalk which naming convention should you follow?

- ✅ .ebextensions/&lt;mysettings&gt;.config
- ▫️ .ebextensions_&lt;mysettings&gt;.config
- ▫️ .config/&lt;mysettings&gt;.ebextensions
- ▫️ .config_&lt;mysettings&gt;.ebextensions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

.ebextensions/&lt;mysettings&gt;.config : You can add AWS Elastic Beanstalk configuration files (.ebextensions) to your web application's source code to configure your environment and customize the AWS resources that it contains. Configuration files are YAML or JSON formatted documents with a .config file extension that you place in a folder named .ebextensions and deploy in your application source bundle.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html

Incorrect options:

.ebextensions_&lt;mysettings&gt;.config

.config/&lt;mysettings&gt;.ebextensions

.config_&lt;mysettings&gt;.ebextensions

These three options contradict the explanation provided earlier. So these are incorrect.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html

</details>

---
