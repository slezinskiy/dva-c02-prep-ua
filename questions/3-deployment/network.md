# EC2 / Auto Scaling / ELB / VPC / EBS / EFS

**Домен 3 — Розгортання** · питань: 3  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#network) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q03"></a>
### T03-Q03
*⏭ пропущено · домен: офіційна мітка*

You have a Java-based application running on EC2 instances loaded with AWS CodeDeploy agents. You are considering different options for deployment, one is the flexibility that allows for incremental deployment of your new application versions and replaces existing versions in the EC2 instances. The other option is a strategy in which an Auto Scaling group is used to perform a deployment.

Which of the following options will allow you to deploy in this manner? (Select two)

- ✅ In-place Deployment
- ✅ Blue/green Deployment
- ▫️ Cattle Deployment
- ▫️ Warm Standby Deployment
- ▫️ Pilot Light Deployment

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

In-place Deployment

The application on each instance in the deployment group is stopped, the latest application revision is installed, and the new version of the application is started and validated. You can use a load balancer so that each instance is deregistered during its deployment and then restored to service after the deployment is complete.

Blue/green Deployment

With a blue/green deployment, you provision a new set of instances on which CodeDeploy installs the latest version of your application. CodeDeploy then re-routes load balancer traffic from an existing set of instances running the previous version of your application to the new set of instances running the latest version. After traffic is re-routed to the new instances, the existing instances can be terminated.

CodeDeploy Deployment Types:  via - https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html

Incorrect options:

Cattle Deployment - This is a good option if you have cattle in a farm

Warm Standby Deployment - This is not a valid CodeDeploy deployment option. The term "Warm Standby" is used to describe a Disaster Recovery scenario in which a scaled-down version of a fully functional environment is always running in the cloud.

Pilot Light Deployment - This is not a valid CodeDeploy deployment option. "Pilot Light" is a Disaster Recovery approach where you simply replicate part of your IT structure for a limited set of core services so that the AWS cloud environment seamlessly takes over in the event of a disaster.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html> · <https://aws.amazon.com/blogs/publicsector/rapidly-recover-mission-critical-systems-in-a-disaster/>

</details>

---

<a id="t06-q47"></a>
### T06-Q47
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A company operates a multimedia sharing service on AWS. The service is hosted on Amazon EC2 instances in an Auto Scaling group, serving as the target for an Application Load Balancer (ALB).

The multimedia files are stored in an Amazon S3 bucket. The company is developing a feature for system request testing, which will redirect these requests to a separate target group hosting a test version of the application.

How can this be accomplished most efficiently?

- ▫️ Replicate the existing application and host it on a distinct EC2 instance. Manually route test requests to this instance.
- ▫️ Create a separate AWS Lambda function to handle the test requests and direct them to the new target group.
- ▫️ Create a new S3 bucket to host the test variant of the application. Redirect all test requests to this new bucket.
- ✅ Use ALB content-based routing. Create a separate target group for the test version of the application and route requests identified by a specific cookie.

<details><summary>Пояснення (оригінал, EN)</summary>

The most efficient solution is to use the ALB's content-based routing feature with a cookie-based strategy. This way, test requests identified by a specific cookie can be directed to the new target group hosting the test variant of the application, without any significant changes to the existing setup.

CORRECT: "Use ALB content-based routing. Create a separate target group for the test version of the application and route requests identified by a specific cookie" is the correct answer (as explained above.)

INCORRECT: "Replicate the existing application and host it on a distinct EC2 instance. Manually route test requests to this instance" is incorrect.

This approach doesn't leverage the ALB's content-based routing and adds unnecessary manual work

INCORRECT: "Create a new S3 bucket to host the test variant of the application. Redirect all test requests to this new bucket" is incorrect.

This method doesn't make use of the ALB and EC2 instances for application hosting, thus inefficient

INCORRECT: "Create a separate AWS Lambda function to handle the test requests and direct them to the new target group" is incorrect.

This is inefficient as it adds unnecessary complexity and circumvents the routing features of the ALB

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html>

</details>

---

<a id="t07-q55"></a>
### T07-Q55
*⏭ пропущено · домен: офіційна мітка*

Your web application architecture consists of multiple Amazon EC2 instances running behind an Elastic Load Balancer with an Auto Scaling group having the desired capacity of 5 EC2 instances. You would like to integrate AWS CodeDeploy for automating application deployment. The deployment should re-route traffic from your application's original environment to the new environment.

Which of the following options will meet your deployment criteria?

- ▫️ Opt for Rolling deployment
- ▫️ Opt for Immutable deployment
- ✅ Opt for Blue/Green deployment
- ▫️ Opt for In-place deployment

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Opt for Blue/Green deployment - A Blue/Green deployment is used to update your applications while minimizing interruptions caused by the changes of a new application version. CodeDeploy provisions your new application version alongside the old version before rerouting your production traffic. The behavior of your deployment depends on which compute platform you use:

AWS Lambda: Traffic is shifted from one version of a Lambda function to a new version of the same Lambda function.
Amazon ECS: Traffic is shifted from a task set in your Amazon ECS service to an updated, replacement task set in the same Amazon ECS service.
EC2/On-Premises: Traffic is shifted from one set of instances in the original environment to a replacement set of instances.
Incorrect options:

Opt for Rolling deployment - This deployment type is present for AWS Elastic Beanstalk and not for EC2 instances directly.

Opt for Immutable deployment - This deployment type is present for AWS Elastic Beanstalk and not for EC2 instances directly.

Opt for In-place deployment - Under this deployment type, the application on each instance in the deployment group is stopped, the latest application revision is installed, and the new version of the application is started and validated. You can use a load balancer so that each instance is deregistered during its deployment and then restored to service after the deployment is complete.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html#welcome-deployment-overview-blue-green> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html>

</details>

---
