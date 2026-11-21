# CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify

**Домен 4 — Усунення несправностей та оптимізація** · питань: 5  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#cicd) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q08"></a>
### T03-Q08
*⏭ пропущено · домен: офіційна мітка*

You are working on a project that has over 100 dependencies. Every time your AWS CodeBuild runs a build step it has to resolve Java dependencies from external Ivy repositories which take a long time. Your manager wants to speed this process up in AWS CodeBuild.

Which of the following will help you do this with minimal effort?

- ▫️ Use Instance Store type of EC2 instances to facilitate internal dependency cache
- ✅ Cache dependencies on S3
- ▫️ Ship all the dependencies as part of the source code
- ▫️ Reduce the number of dependencies

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Cache dependencies on S3

AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. With CodeBuild, you don’t need to provision, manage, and scale your build servers.

Downloading dependencies is a critical phase in the build process. These dependent files can range in size from a few KBs to multiple MBs. Because most of the dependent files do not change frequently between builds, you can noticeably reduce your build time by caching dependencies in S3.

Best Practices for Caching Dependencies:  via - https://aws.amazon.com/blogs/devops/how-to-enable-caching-for-aws-codebuild/

Incorrect options:

Reduce the number of dependencies - This is ideal but sometimes you may not have control over this as your application needs those dependencies, so this option is ruled out.

Ship all the dependencies as part of the source code - This is not a good practice as doing this will increase your build time. If your dependencies are not changing then its best to cache them.

Use Instance Store type of EC2 instances to facilitate internal dependency cache - An instance store provides temporary block-level storage for your instance. This storage is located on disks that are physically attached to the host computer. Instance store is ideal for the temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content, or for data that is replicated across a fleet of instances, such as a load-balanced pool of web servers.

Instance Store cannot be used to facilitate the internal dependency cache for the code build process.

Reference:

https://aws.amazon.com/blogs/devops/how-to-enable-caching-for-aws-codebuild/

</details>

---

<a id="t09-q65"></a>
### T09-Q65
*⏭ пропущено · домен: офіційна мітка*

A company uses AWS CodeDeploy to deploy applications from GitHub to EC2 instances running Amazon Linux. The deployment process uses a file called appspec.yml for specifying deployment hooks. A final lifecycle event should be specified to verify the deployment success.

Which of the following hook events should be used to verify the success of the deployment?

- ▫️ ApplicationStart
- ▫️ AfterInstall
- ▫️ AllowTraffic
- ✅ ValidateService

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CodeDeploy is a fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications.

An EC2/On-Premises deployment hook is executed once per deployment to an instance. You can specify one or more scripts to run in a hook.

 via - https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-hooks-run-order

ValidateService: ValidateService is the last deployment lifecycle event. It is used to verify the deployment was completed successfully.

Incorrect options:

AfterInstall - You can use this deployment lifecycle event for tasks such as configuring your application or changing file permissions

ApplicationStart - You typically use this deployment lifecycle event to restart services that were stopped during ApplicationStop

AllowTraffic - During this deployment lifecycle event, internet traffic is allowed to access instances after a deployment. This event is reserved for the AWS CodeDeploy agent and cannot be used to run scripts

Reference:

https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-hooks-run-order

</details>

---

<a id="t12-q09"></a>
### T12-Q09
*⏭ пропущено · домен: офіційна мітка*

A developer in your company has configured a build using AWS CodeBuild. The build fails and the developer needs to quickly troubleshoot the issue to see which commands or settings located in the BuildSpec file are causing an issue.

Which approach will help them accomplish this?

- ▫️ Freeze the CodeBuild during its next execution
- ▫️ Enable detailed monitoring
- ✅ Run AWS CodeBuild locally using CodeBuild Agent
- ▫️ SSH into the CodeBuild Docker container

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Run AWS CodeBuild locally using CodeBuild Agent

AWS CodeBuild is a fully managed build service. There are no servers to provision and scale, or software to install, configure, and operate.

With the Local Build support for AWS CodeBuild, you just specify the location of your source code, choose your build settings, and CodeBuild runs build scripts for compiling, testing, and packaging your code. You can use the AWS CodeBuild agent to test and debug builds on a local machine.

By building an application on a local machine you can:

Test the integrity and contents of a buildspec file locally.

Test and build an application locally before committing.

Identify and fix errors quickly from your local development environment.

Incorrect options:

SSH into the CodeBuild Docker container - It is not possible to SSH into the CodeBuild Docker container, that's why you should test and fix errors locally.

Freeze the CodeBuild during its next execution - You cannot freeze the CodeBuild process but you can stop it. Please see more details on - https://docs.aws.amazon.com/codebuild/latest/userguide/stop-build.html

Enable detailed monitoring - Detailed monitoring is available for EC2 instances. You do not enable detailed monitoring but you can specify output logs to be captured via CloudTrail.

AWS CodeBuild is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in CodeBuild. CloudTrail captures all API calls for CodeBuild as events, including calls from the CodeBuild console and from code calls to the CodeBuild APIs. If you create a trail, you can enable continuous delivery of CloudTrail events to an S3 bucket, including events for CodeBuild.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html> · <https://aws.amazon.com/blogs/devops/announcing-local-build-support-for-aws-codebuild/> · <https://docs.aws.amazon.com/codebuild/latest/userguide/use-codebuild-agent.html>

</details>

---

<a id="t12-q15"></a>
### T12-Q15
*⏭ пропущено · домен: офіційна мітка*

Your organization has developers that merge code changes regularly to an AWS CodeCommit repository. Your pipeline has AWS CodeCommit as the source and you would like to configure a rule that reacts to changes in CodeCommit.

Which of the following options do you choose for this type of integration?

- ▫️ Use Lambda function with Amazon Simple Notification Service (SNS)
- ▫️ Use Lambda Event Rules
- ✅ Use Amazon EventBridge Rules
- ▫️ Use CloudTrail Event rules with Amazon Simple Email Service (SES)

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Amazon EventBridge Rules

Amazon EventBridge is a web service that monitors your AWS resources and the applications you run on AWS. You can use Amazon EventBridge to detect and react to changes in the state of a pipeline, stage, or action. Then, based on rules you create, EventBridge invokes one or more target actions when a pipeline, stage, or action enters the state you specify in a rule. Examples of Amazon EventBridge rules and targets:

A rule that sends a notification when the instance state changes, where an EC2 instance is the event source, and Amazon SNS is the event target.

A rule that sends a notification when the build phase changes, where a CodeBuild configuration is the event source, and Amazon SNS is the event target.

A rule that detects pipeline changes and invokes an AWS Lambda function.

Incorrect options:

Use CloudTrail Event rules with Amazon Simple Email Service (SES) - This is an incorrect statement. There is no such thing as CloudTrail Event Rule.

Use Lambda function with Amazon Simple Notification Service (SNS) - Lambda functions can be triggered by the use of Amazon EventBridge Rules as discussed above. AWS CodePipeline does not trigger Lambda functions directly.

Use Lambda Event Rules - This is an incorrect statement. There is no such thing as Lambda Event Rule.

Reference:

https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html

</details>

---

<a id="t12-q52"></a>
### T12-Q52
*⏭ пропущено · домен: офіційна мітка*

An AWS CodePipeline was configured to be triggered by Amazon EventBridge. Recently the pipeline failed and upon investigation, the Team Lead noticed that the source was changed from AWS CodeCommit to Amazon Simple Storage Service (S3). The Team Lead has requested you to find the user who had made the changes.

Which service will help you solve this?

- ✅ AWS CloudTrail
- ▫️ AWS X-Ray
- ▫️ Amazon Inspector
- ▫️ Amazon CloudWatch

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudTrail

AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account. With CloudTrail, you can log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. CloudTrail provides an event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command-line tools, and other AWS services.

AWS CloudTrail increases visibility into your user and resource activity by recording AWS Management Console actions and API calls. You can identify which users and accounts called AWS, the source IP address from which the calls were made, and when the calls occurred.

How CloudTrail works:  via - https://aws.amazon.com/cloudtrail/

Incorrect options:

Amazon CloudWatch - Amazon CloudWatch is a monitoring and management service that provides data and actionable insights for AWS, hybrid, and on-premises applications and infrastructure resources. CloudWatch can collect numbers and respond to AWS service-related events, but it does not help in user activity logging.

AWS X-Ray - AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray is a very important tool in troubleshooting but is not useful in logging user activity.

Amazon Inspector - Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS. Amazon Inspector security assessments help you check for unintended network accessibility of your Amazon EC2 instances and for vulnerabilities on those EC2 instances. This does not log User activity at the account level.

Джерела: <https://aws.amazon.com/xray/> · <https://aws.amazon.com/inspector/> · <https://aws.amazon.com/cloudwatch/>

</details>

---
