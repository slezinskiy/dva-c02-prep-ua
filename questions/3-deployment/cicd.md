# CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify

**Домен 3 — Розгортання** · питань: 44  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#cicd) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q30"></a>
### T01-Q30
*⏭ пропущено · домен: офіційна мітка*

Your team lead has finished creating a CodeBuild project in the management console and a build spec has been defined for the project. After the build is run, CodeBuild fails to pull a Docker image into the build environment.

What is the most likely cause?

- ✅ Missing IAM permissions for the CodeBuild Service
- ▫️ The Docker image is missing some tags
- ▫️ CodeBuild cannot work with custom Docker images
- ▫️ The Docker image is too big

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Missing IAM permissions for the CodeBuild Service

By default, IAM users don't have permission to create or modify Amazon Elastic Container Registry (Amazon ECR) resources or perform tasks using the Amazon ECR API. A user who uses the AWS CodeBuild console must have a minimum set of permissions that allows the user to describe other AWS resources for the AWS account.

 via - https://docs.aws.amazon.com/codebuild/latest/userguide/sample-ecr.html

Incorrect options:

The Docker image is missing some tags - Tags are optional for naming purposes

CodeBuild cannot work with custom Docker images - Custom docker images are supported, so this option is incorrect.

The Docker image is too big - It is good to properly design the image but in this case, it does not affect the CodeBuild. You can also look at multi-stage builds, which are a new feature requiring Docker 17.05 or higher on the daemon and client. Multistage builds are useful to anyone who has struggled to optimize Dockerfiles while keeping them easy to read and maintain.

Reference:

https://docs.aws.amazon.com/codebuild/latest/userguide/sample-ecr.html

</details>

---

<a id="t01-q47"></a>
### T01-Q47
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Застаріле: AWS CodeStar припинено (липень 2024).** → [детальніше](../../07-outdated-and-wrong-answers.md#o03)

You would like to have a one-stop dashboard for all the CI/CD needs of one of your projects. You don't need heavy control of the individual configuration of each component in your CI/CD, but need to be able to get a holistic view of your projects.

Which service do you recommend?

- ▫️ CodeBuild
- ✅ CodeStar
- ▫️ CodePipeline
- ▫️ CodeDeploy

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

CodeStar

AWS CodeStar enables you to quickly develop, build, and deploy applications on AWS. AWS CodeStar provides a unified user interface, enabling you to easily manage your software development activities in one place. With AWS CodeStar, you can set up your entire continuous delivery toolchain in minutes, allowing you to start releasing code faster. AWS CodeStar makes it easy for your whole team to work together securely, allowing you to easily manage access and add owners, contributors, and viewers to your projects. Each AWS CodeStar project comes with a project management dashboard, including an integrated issue tracking capability powered by Atlassian JIRA Software. With the AWS CodeStar project dashboard, you can easily track progress across your entire software development process, from your backlog of work items to teams’ recent code deployments.

Incorrect options:

CodeBuild

CodeDeploy

CodePipeline

All these options are individual services encompassed by CodeStar when you deploy a project. They have to be used individually and don't provide a unified "project" view.

Reference:

https://aws.amazon.com/codestar/

</details>

---

<a id="t02-q28"></a>
### T02-Q28
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Хуки Lambda.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A Developer is deploying an AWS Lambda update using AWS CodeDeploy. In the appspec.yaml file, which of the following is a valid structure for the order of hooks that should be specified?

- ❌ BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService
- ✅ BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic

<details><summary>Пояснення (оригінал, EN)</summary>

The content in the 'hooks' section of the AppSpec file varies, depending on the compute platform for your deployment. The 'hooks' section for an EC2/On-Premises deployment contains mappings that link deployment lifecycle event hooks to one or more scripts.

The 'hooks' section for a Lambda or an Amazon ECS deployment specifies Lambda validation functions to run during a deployment lifecycle event. If an event hook is not present, no operation is executed for that event. This section is required only if you are running scripts or Lambda validation functions as part of the deployment.

The following code snippet shows a valid example of the structure of hooks for an AWS Lambda deployment:

Therefore, in this scenario a valid structure for the order of hooks that should be specified in the appspec.yml file is: BeforeAllowTraffic &gt; AfterAllowTraffic

CORRECT: "BeforeAllowTraffic &gt; AfterAllowTraffic" is the correct answer.

INCORRECT: "BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService" is incorrect as this would be valid for Amazon EC2.

INCORRECT: "BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this would be valid for Amazon ECS.

INCORRECT: "BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this is a partial listing of hooks for Amazon EC2 but is incomplete.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t02-q56"></a>
### T02-Q56
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team would use a GitHub repository and would like to migrate their application code to AWS CodeCommit.
What needs to be created before they can migrate a cloned repository to CodeCommit over HTTPS?

- ❌ An Amazon EC2 IAM role with CodeCommit permissions
- ▫️ A GitHub secure authentication token
- ▫️ A public and private SSH key file
- ✅ A set of Git credentials generated with IAM

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeCommit is a managed version control service that hosts private Git repositories in the AWS cloud. To use CodeCommit, you configure your Git client to communicate with CodeCommit repositories. As part of this configuration, you provide IAM credentials that CodeCommit can use to authenticate you. IAM supports CodeCommit with three types of credentials:

Git credentials, an IAM -generated user name and password pair you can use to communicate with CodeCommit repositories over HTTPS.

SSH keys, a locally generated public-private key pair that you can associate with your IAM user to communicate with CodeCommit repositories over SSH.

AWS access keys, which you can use with the credential helper included with the AWS CLI to communicate with CodeCommit repositories over HTTPS.

In this scenario the Development team need to connect to CodeCommit using HTTPS so they need either AWS access keys to use the AWS CLI or Git credentials generated by IAM. Access keys are not offered as an answer choice so the best answer is that they need to create a set of Git credentials generated with IAM

CORRECT: "A set of Git credentials generated with IAM" is the correct answer.

INCORRECT: "A GitHub secure authentication token" is incorrect as they need to authenticate to AWS CodeCommit, not GitHub (they have already accessed and cloned the repository).

INCORRECT: "A public and private SSH key file" is incorrect as these are used to communicate with CodeCommit repositories using SSH, not HTTPS.

INCORRECT: "An Amazon EC2 IAM role with CodeCommit permissions" is incorrect as you need the Git credentials generated through IAM to connect to CodeCommit.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html>

</details>

---

<a id="t02-q58"></a>
### T02-Q58
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **ECS: test listener на ALB; валідація до продакшн-трафіку.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A developer is updating an Amazon ECS app that uses an ALB with two target groups and a single listener. The developer has an AppSpec file in an S3 bucket and an AWS CodeDeploy deployment group tied to the ALB and AppSpec file. The developer needs to use an AWS Lambda function for update validation before deployment.

Which solution meets these requirements?

- ▫️ Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the AfterAllowTraffic lifecycle hook.
- ▫️ Attach a listener to the deployment group. Update the AppSpec file to link the Lambda function to the AfterAllowTraffic lifecycle hook.
- ❌ Attach a listener to the deployment group. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook.
- ✅ Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook.

<details><summary>Пояснення (оригінал, EN)</summary>

The AppSpec file allows a developer to specify scripts to be run at different lifecycle hooks. The BeforeAllowTraffic lifecycle event occurs before the updated task set is moved to the target group that is receiving live traffic. So, any validation before production deployment should be configured at this lifecycle event. The listener is configured at the ALB level, not at the deployment group.

CORRECT: "Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook" is the correct answer (as explained above.)

INCORRECT: "Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the AfterAllowTraffic lifecycle hook" is incorrect.

This is incorrect because AfterAllowTraffic lifecycle event occurs after the updated task set is moved to the target group that is receiving live traffic. Validation should be performed before the updated task set receives live traffic, not after.

INCORRECT: "Attach a listener to the deployment group. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifecycle hook" is incorrect.

This is incorrect because the listener is not attached to the deployment group. It is configured at the ALB level.

INCORRECT: "Attach a listener to the deployment group. Update the AppSpec file to link the Lambda function to the AfterAllowTraffic lifecycle hook" is incorrect.

Listeners are added to the ALB, not the deployment group, and validation should occur before the updated task set receives live traffic.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html>

</details>

---

<a id="t03-q02"></a>
### T03-Q02
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Хуки EC2: це підмножина повного порядку.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A communication platform serves millions of customers and deploys features in a production environment on AWS via CodeDeploy. You are reviewing scripts for the deployment process located in the AppSpec file.

Which of the following options lists the correct order of lifecycle events?

- ▫️ ValidateService =&gt; BeforeInstall =&gt;DownloadBundle =&gt; ApplicationStart
- ▫️ BeforeInstall =&gt; ApplicationStart =&gt; DownloadBundle =&gt; ValidateService
- ✅ DownloadBundle =&gt; BeforeInstall =&gt; ApplicationStart =&gt; ValidateService
- ▫️ BeforeInstall =&gt; ValidateService =&gt;DownloadBundle =&gt; ApplicationStart

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

DownloadBundle =&gt; BeforeInstall =&gt; ApplicationStart =&gt; ValidateService

AWS CodeDeploy is a fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications.

You can specify one or more scripts to run in a hook. Each hook for a lifecycle event is specified with a string on a separate line.

Please review the correct order of lifecycle events:  via - https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-hooks-run-order

Incorrect options:

BeforeInstall =&gt; ApplicationStart =&gt; DownloadBundle =&gt; ValidateService

ValidateService =&gt; BeforeInstall =&gt;DownloadBundle =&gt; ApplicationStart

BeforeInstall =&gt; ValidateService =&gt;DownloadBundle =&gt; ApplicationStart

These three options contradict the details provided in the explanation above, so these options are not correct.

Reference:

https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-hooks-run-order

</details>

---

<a id="t03-q28"></a>
### T03-Q28
*⏭ пропущено · домен: офіційна мітка*

You have been hired at a company that needs an experienced developer to help with a continuous integration/continuous delivery (CI/CD) workflow on AWS. You configure the company’s workflow to run an AWS CodePipeline pipeline whenever the application’s source code changes in a repository hosted in AWS Code Commit and compiles source code with AWS Code Build. You are configuring ProjectArtifacts in your build stage.

Which of the following should you do?

- ✅ Give AWS CodeBuild permissions to upload the build output to your Amazon S3 bucket
- ▫️ Contact AWS Support to allow AWS CodePipeline to manage build outputs
- ▫️ Configure AWS CodeBuild to store output artifacts on EC2 servers
- ▫️ Give AWS CodeCommit permissions to upload the build output to your Amazon S3 bucket

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Give AWS CodeBuild permissions to upload the build output to your Amazon S3 bucket

If you choose ProjectArtifacts and your value type is S3 then the build project stores build output in Amazon Simple Storage Service (Amazon S3). For that, you will need to give AWS CodeBuild permissions to upload.

Incorrect options:

Configure AWS CodeBuild to store output artifacts on EC2 servers - EC2 servers are not a valid output location, so this option is ruled out.

Give AWS CodeCommit permissions to upload the build output to your Amazon S3 bucket - AWS CodeCommit is the repository that holds source code and has no control over compiling the source code, so this option is incorrect.

Contact AWS Support to allow AWS CodePipeline to manage build outputs - You can set AWS CodePipeline to manage its build output locations instead of AWS CodeBuild. There is no need to contact AWS Support.

Reference:

https://docs.aws.amazon.com/codebuild/latest/userguide/create-project.html#create-project-cli

</details>

---

<a id="t03-q59"></a>
### T03-Q59
*⏭ пропущено · домен: офіційна мітка*

An e-commerce company has implemented AWS CodeDeploy as part of its AWS cloud CI/CD strategy. The company has configured automatic rollbacks while deploying a new version of its flagship application to Amazon EC2.

What occurs if the deployment of the new version fails?

- ▫️ AWS CodePipeline promotes the most recent working deployment with a SUCCEEDED status to production
- ✅ A new deployment of the last known working version of the application is deployed with a new deployment ID
- ▫️ The last known working deployment is automatically restored using the snapshot stored in Amazon S3
- ▫️ CodeDeploy switches the Route 53 alias records back to the known good green deployment and terminates the failed blue deployment

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

A new deployment of the last known working version of the application is deployed with a new deployment ID

AWS CodeDeploy is a service that automates code deployments to any instance, including Amazon EC2 instances and instances running on-premises. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during deployment, and handles the complexity of updating your applications.

CodeDeploy rolls back deployments by redeploying a previously deployed revision of an application as a new deployment. These rolled-back deployments are technically new deployments, with new deployment IDs, rather than restored versions of a previous deployment.

To roll back an application to a previous revision, you just need to deploy that revision. AWS CodeDeploy keeps track of the files that were copied for the current revision and removes them before starting a new deployment, so there is no difference between redeploy and rollback. However, you need to make sure that the previous revisions are available for rollback.

Incorrect options:

The last known working deployment is automatically restored using the snapshot stored in Amazon S3 - CodeDeploy deployment does not have a snapshot stored on S3, so this option is incorrect.

AWS CodePipeline promotes the most recent working deployment with a SUCCEEDED status to production - The use-case does not talk about using CodePipeline, so this option just acts as a distractor.

CodeDeploy switches the Route 53 alias records back to the known good green deployment and terminates the failed blue deployment - The use-case does not talk about the blue/green deployment, so this option has just been added as a distractor.

Reference:

https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-rollback-and-redeploy.html

</details>

---

<a id="t03-q64"></a>
### T03-Q64
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Хуки EC2: це підмножина повного порядку.** → [детальніше](../../06-contradictions-and-traps.md#c08)

What is the run order of the hooks for in-place deployments using CodeDeploy?

- ▫️ Before Install -&gt; Application Stop -&gt; Application Start -&gt; ValidateService
- ✅ Application Stop -&gt; Before Install -&gt; Application Start -&gt; ValidateService
- ▫️ Before Install -&gt; Application Stop -&gt; ValidateService -&gt; Application Start
- ▫️ Application Stop -&gt; Before Install -&gt; ValidateService -&gt; Application Start

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Application Stop -&gt; Before Install -&gt; Application Start -&gt; ValidateService

In CodeDeploy, a deployment is a process of installing content on one or more instances. This content can consist of code, web and configuration files, executables, packages, scripts, and so on. CodeDeploy deploys content that is stored in a source repository, according to the configuration rules you specify.

 via - https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html

The content in the 'hooks' section of the AppSpec file varies, depending on the compute platform for your deployment. The 'hooks' section for an EC2/On-Premises deployment contains mappings that link deployment lifecycle event hooks to one or more scripts. The 'hooks' section for a Lambda or an Amazon ECS deployment specifies Lambda validation functions to run during a deployment lifecycle event. If an event hook is not present, no operation is executed for that event.

 via - https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html

Incorrect options:

Before Install -&gt; Application Stop -&gt; ValidateService -&gt; Application Start

Application Stop -&gt; Before Install -&gt; ValidateService -&gt; Application Start

Before Install -&gt; Application Stop -&gt; Application Start -&gt; ValidateService

As explained above, these three options contradict the correct order of hooks, so these are incorrect.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t04-q41"></a>
### T04-Q41
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Хуки EC2.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A Developer is deploying an Amazon EC2 update using AWS CodeDeploy. In the appspec.yml file, which of the following is a valid structure for the order of hooks that should be specified?

- ▫️ BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeAllowTraffic &gt; AfterAllowTraffic
- ✅ BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService

<details><summary>Пояснення (оригінал, EN)</summary>

The content in the 'hooks' section of the AppSpec file varies, depending on the compute platform for your deployment. The 'hooks' section for an EC2/On-Premises deployment contains mappings that link deployment lifecycle event hooks to one or more scripts.

The 'hooks' section for a Lambda or an Amazon ECS deployment specifies Lambda validation functions to run during a deployment lifecycle event. If an event hook is not present, no operation is executed for that event. This section is required only if you are running scripts or Lambda validation functions as part of the deployment.

The following code snippet shows a valid example of the structure of hooks for an Amazon EC2 deployment:

Therefore, in this scenario a valid structure for the order of hooks that should be specified in the appspec.yml file is: BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService

CORRECT: "BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService" is the correct answer.

INCORRECT: "BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this would be valid for Amazon ECS.

INCORRECT: "BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this would be valid for AWS Lambda.

INCORRECT: "BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this is a partial listing of hooks for Amazon EC2 but is incomplete.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t04-q43"></a>
### T04-Q43
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to update an Amazon ECS application that was deployed using AWS CodeDeploy. What file does the Developer need to update to push the change through CodeDeploy?

- ✅ appspec.yml
- ▫️ dockerrun.aws.json
- ▫️ buildspec.yml
- ▫️ ebextensions.config

<details><summary>Пояснення (оригінал, EN)</summary>

In CodeDeploy, a revision contains a version of the source files CodeDeploy will deploy to your instances or scripts CodeDeploy will run on your instances. You plan the revision, add an AppSpec file to the revision, and then push the revision to Amazon S3 or GitHub. After you push the revision, you can deploy it.

For a deployment to an Amazon ECS compute platform:

• The AppSpec file specifies the Amazon ECS task definition used for the deployment, a container name and port mapping used to route traffic, and optional Lambda functions run after deployment lifecycle events.

• A revision is the same as an AppSpec file.

• An AppSpec file can be written using JSON or YAML.

• An AppSpec file can be saved as a text file or entered directly into a console when you create a deployment.

Therefore, the appspec.yml file needs to be updated by the Developer.

CORRECT: "appspec.yml" is the correct answer.

INCORRECT: "dockerrun.aws.json" is incorrect. A Dockerrun.aws.json file describes how to deploy a remote Docker image as an Elastic Beanstalk application.

INCORRECT: "buildspec.yaml" is incorrect. A build spec is a collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build using AWS CodeBuild.

INCORRECT: "ebextensions.config" is incorrect. The .ebextensions folder in the source code for an Elastic Beanstalk application is used for .config files that configure the environment and customize resources.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/application-revisions-appspec-file.html>

</details>

---

<a id="t04-q50"></a>
### T04-Q50
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **Застаріле: Cloud9 закритий для нових клієнтів (липень 2024).** → [детальніше](../../07-outdated-and-wrong-answers.md#o03)

A company will be hiring a large number of Developers for a series of projects. The Develops will bring their own devices to work and the company want to ensure consistency in tooling. The Developers must be able to write, run, and debug applications with just a browser, without needing to install or maintain a local Integrated Development Environment (IDE).

Which AWS service should the Developers use?

- ▫️ AWS CodeCommit
- ▫️ AWS X-Ray
- ✅ AWS Cloud9
- ▫️ AWS CodeDeploy

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Cloud9 is an integrated development environment, or IDE. The AWS Cloud9 IDE offers a rich code-editing experience with support for several programming languages and runtime debuggers, and a built-in terminal. It contains a collection of tools that you use to code, build, run, test, and debug software, and helps you release software to the cloud.

You access the AWS Cloud9 IDE through a web browser. You can configure the IDE to your preferences. You can switch color themes, bind shortcut keys, enable programming language-specific syntax coloring and code formatting, and more.

CORRECT: "AWS Cloud9" is the correct answer.

INCORRECT: "AWS CodeCommit" is incorrect. AWS CodeCommit is a fully-managed source control service that hosts secure Git-based repositories. It is not an IDE.

INCORRECT: "AWS CodeDeploy" is incorrect. CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.

INCORRECT: "AWS X-Ray" is incorrect. AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture.

Джерела: <https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html>

</details>

---

<a id="t05-q04"></a>
### T05-Q04
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A developer is creating a microservices application that includes and AWS Lambda function. The function generates a unique file for each execution and must commit the file to an AWS CodeCommit repository.

How should the developer accomplish this?

- ✅ Use an AWS SDK to instantiate a CodeCommit client. Invoke the PutFile method to add the file to the repository and execute a commit with CreateCommit.
- ▫️ After the new file is created in Lambda, use CURL to invoke the CodeCommit API. Send the file to the repository and automatically commit the change.
- ▫️ Send a message to an Amazon SQS queue with the file attached. Configure an AWS Step Function as a destination for messages in the queue. Configure the Step Function to add the new file to the repository and commit the change.
- ▫️ Upload the new file to an Amazon S3 bucket. Create an AWS Step Function to accept S3 events. Use AWS Lambda functions in the Step Function, to add the file to the repository and commit the change.

<details><summary>Пояснення (оригінал, EN)</summary>

The developer can instantiate a CodeCommit client using the AWS SDK. This provides the ability to programmatically work with the AWS CodeCommit repository. The PutFile method is used to add or modify a single file in a specified repository and branch. The CreateCommit method creates a commit for changes to a repository.

CORRECT: "Use an AWS SDK to instantiate a CodeCommit client. Invoke the PutFile method to add the file to the repository and execute a commit with CreateCommit" is the correct answer (as explained above.)

INCORRECT: "Send a message to an Amazon SQS queue with the file attached. Configure an AWS Step Function as a destination for messages in the queue. Configure the Step Function to add the new file to the repository and commit the change" is incorrect.

A Step Function cannot be a destination for messages in an SQS queue. There would need to be another Lambda function or other method to trigger the state machine and pass the information across. This would be a highly inefficient solution.

INCORRECT: "After the new file is created in Lambda, use CURL to invoke the CodeCommit API. Send the file to the repository and automatically commit the change" is incorrect.

CURL cannot be used to work with the CodeCommit API. The developer must use the AWS SDK.

INCORRECT: "Upload the new file to an Amazon S3 bucket. Create an AWS Step Function to accept S3 events. Use AWS Lambda functions in the Step Function, to add the file to the repository and commit the change" is incorrect.

Step Functions is not a supported destination for Amazon S3 event notifications. Supported destinations are SNS topics, SQS queues, Lambda functions, and EventBridge event buses.

Джерела: <https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/codecommit/AWSCodeCommitClient.html>

</details>

---

<a id="t05-q07"></a>
### T05-Q07
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An engineer wishes to share a software project they've developed with their team members for review. The shared application code needs to be preserved over time, with multiple versions and batch modifications being tracked. What is the most appropriate AWS service for this purpose?

- ▫️ AWS Cloud9
- ▫️ AWS CodeStar
- ▫️ AWS CodeDeploy
- ✅ AWS CodeCommit

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeCommit is a fully managed source control service that hosts secure Git-based repositories. It's designed to collaborate on code, maintain version control, and keep track of changes. This makes it an optimal choice for this scenario.

CORRECT: "AWS CodeCommit" is the correct answer (as explained above.)

INCORRECT: "AWS CodeStar" is incorrect.

AWS CodeStar enables you to quickly develop, build, and deploy applications on AWS. It isn't focused on source control and versioning, which is the primary need in this scenario.

INCORRECT: "AWS Cloud9" is incorrect.

AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser. While it allows for code sharing, it does not offer the robust version control and change tracking of CodeCommit.

INCORRECT: "AWS CodeDeploy" is incorrect.

AWS CodeDeploy is a deployment service that enables developers to automate software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers.

Джерела: <https://aws.amazon.com/codecommit/>

</details>

---

<a id="t05-q14"></a>
### T05-Q14
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company needs a version control system for collaborative software development. The solution must include support for batches of changes across multiple files and parallel branching.

Which AWS service will meet these requirements?

- ▫️ AWS CodeBuild
- ▫️ AWS CodePipeline
- ▫️ Amazon S3
- ✅ AWS CodeCommit

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeCommit is a version control service hosted by Amazon Web Services that you can use to privately store and manage assets (such as documents, source code, and binary files) in the cloud.

CodeCommit is optimized for team software development. It manages batches of changes across multiple files, which can occur in parallel with changes made by other developers.

CORRECT: "AWS CodeCommit" is the correct answer.

INCORRECT: "AWS CodeBuild" is incorrect as it is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy.

INCORRECT: "AWS CodePipeline" is incorrect as it is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates.

INCORRECT: "Amazon S3" is incorrect. Amazon S3 versioning supports the recovery of past versions of files, but it's not focused on collaborative file tracking features that software development teams need.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html>

</details>

---

<a id="t05-q20"></a>
### T05-Q20
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A team of Developers are building a continuous integration and delivery pipeline using AWS Developer Tools. Which services should they use for running tests against source code and installing compiled code on their AWS resources? (Select TWO.)

- ▫️ AWS CodeCommit for installing compiled code on their AWS resources
- ✅ AWS CodeDeploy for installing compiled code on their AWS resources
- ✅ AWS CodeBuild for running tests against source code
- ▫️ AWS Cloud9 for running tests against source code
- ▫️ AWS CodePipeline for running tests against source code

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeBuild is a fully managed build service in the cloud. CodeBuild compiles your source code, runs unit tests, and produces artifacts that are ready to deploy. CodeBuild eliminates the need to provision, manage, and scale your own build servers. It provides pre-packaged build environments for popular programming languages and build tools such as Apache Maven, Gradle, and more.

CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.

CORRECT: "AWS CodeBuild for running tests against source code" is a correct answer.

CORRECT: "AWS CodeDeploy for installing compiled code on their AWS resources" is also a correct answer.

INCORRECT: "AWS CodePipeline for running tests against source code" is incorrect. AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. This service works with the other Developer Tools to create a pipeline.

INCORRECT: "AWS CodeCommit for installing compiled code on their AWS resources" is incorrect as AWS CodeCommit is a fully-managed source control service that hosts secure Git-based repositories.

INCORRECT: "AWS Cloud9 for running tests against source code" is incorrect as AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html>

</details>

---

<a id="t05-q30"></a>
### T05-Q30
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company wants a serverless solution for phased release of static websites hosted on various version control systems. Deployments should be triggered by Git branch merges and all data exchange should be over HTTPS.

Which option offers the LOWEST operational overhead?

- ▫️ Use AWS Elastic Beanstalk for hosting and use AWS CodeStar to manage deployments and workflows.
- ▫️ Deploy websites on separate Amazon EC2 instances for each environment, use AWS CodeDeploy for automation, and link it with the version control systems.
- ✅ Use AWS Amplify for hosting, connect corresponding repository branches, and initiate deployments by merging changes to the needed branch.
- ▫️ Use Amazon S3 to host the websites, create a manual script to deploy changes when there are code merges in the version control systems.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Amplify is designed to host static websites with continuous deployment linked to Git repositories. It allows you to connect branches in your repository with environments in Amplify, and to automatically deploy changes when you merge code to those branches. This solution requires the least operational overhead among the options.

CORRECT: "Use AWS Amplify for hosting, connect corresponding repository branches, and initiate deployments by merging changes to the needed branch" is the correct answer (as explained above.)

INCORRECT: "Deploy websites on separate Amazon EC2 instances for each environment, use AWS CodeDeploy for automation, and link it with the version control systems" is incorrect.

While Amazon EC2 could host the websites and AWS CodeDeploy could manage deployments, this is not a serverless solution as requested. The EC2 instances would need to run continuously, creating more operational overhead.

INCORRECT: "Use Amazon S3 to host the websites, create a manual script to deploy changes when there are code merges in the version control systems" is incorrect.

While Amazon S3 can host static websites, creating a manual script to deploy changes adds unnecessary complexity and operational overhead compared to a service like Amplify that automates this process.

INCORRECT: "Use AWS Elastic Beanstalk for hosting and use AWS CodeStar to manage deployments and workflows" is incorrect.

AWS Elastic Beanstalk is typically used for dynamic, multi-tier web applications, and AWS CodeStar provides a unified user interface, enabling you to easily manage your software development activities in one place. However, this combination could be more complex and require more operational overhead than AWS Amplify for a static website.

Джерела: <https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html>

</details>

---

<a id="t05-q31"></a>
### T05-Q31
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company is planning to use AWS CodeDeploy to deploy a new AWS Lambda function

What are the MINIMUM properties required in the 'resources' section of the AppSpec file for CodeDeploy to deploy the function successfully?

- ▫️ name, alias, PlatformVersion, and type
- ✅ name, alias, currentversion, and targetversion
- ▫️ TaskDefinition, LoadBalancerInfo, and ContainerPort
- ▫️ TaskDefinition, PlatformVersion, and ContainerName

<details><summary>Пояснення (оригінал, EN)</summary>

The content in the 'resources' section of the AppSpec file varies, depending on the compute platform of your deployment. The 'resources' section for an AWS Lambda deployment contains the name, alias, current version, and target version of a Lambda function.

Here is an example of a 'resources' section with the minimum required properties:

CORRECT: "name, alias, currentversion, and targetversion" is the correct answer (as explained above.)

INCORRECT: "name, alias, PlatformVersion, and type" is incorrect (as explained above.)

INCORRECT: "TaskDefinition, LoadBalancerInfo, and ContainerPort" is incorrect.

These properties are related to ECS deployments.

INCORRECT: "TaskDefinition, PlatformVersion, and ContainerName" is incorrect.

These properties are related to ECS deployments.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-resources.html>

</details>

---

<a id="t06-q01"></a>
### T06-Q01
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A team of Developers are working on a shared project and need to be able to collaborate on code. The shared application code must be encrypted at rest, stored on a highly available and durable architecture, and support multiple versions and batch change tracking.

Which AWS service should the Developer use?

- ▫️ AWS CodeBuild
- ▫️ Amazon S3
- ▫️ AWS Cloud9
- ✅ AWS CodeCommit

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeCommit is a fully managed source control service that hosts secure Git-based repositories. It makes it easy for teams to collaborate on code in a secure and highly scalable ecosystem. AWS CodeCommit automatically encrypts your files in transit and at rest.

AWS CodeCommit helps you collaborate on code with teammates via pull requests, branching, and merging. You can implement workflows that include code reviews and feedback by default, and control who can make changes to specific branches.

CORRECT: "AWS CodeCommit" is the correct answer.

INCORRECT: "AWS CodeBuild" is incorrect. AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages

INCORRECT: "Amazon S3" is incorrect. Amazon S3 is an object-based storage system and does not support the features required here.

INCORRECT: "AWS Cloud9" is incorrect. AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser.

Джерела: <https://aws.amazon.com/codecommit/>

</details>

---

<a id="t06-q07"></a>
### T06-Q07
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **Застаріле: Cloud9 закритий для нових клієнтів.** → [детальніше](../../07-outdated-and-wrong-answers.md#o03)

A developer is planning on using AWS Cloud9 to build a new application. The developer wants to spend minimal time configuring resources. What solution should the developer choose?

- ▫️ Create an SSH environment that will provision many of the resources and manage the lifecycle.
- ❌ Use AWS Toolkit to create an SSH environment that will provision many of the resources and manage the lifecycle.
- ✅ Create an AWS Cloud9 Elastic Compute Cloud (EC2) environment that will provision many of the resources and manage the lifecycle.
- ▫️ Use AWS Toolkit to create an Elastic Compute Cloud (EC2) environment that will provision many of the resources and manage the lifecycle.

<details><summary>Пояснення (оригінал, EN)</summary>

An AWS Cloud9 Elastic Compute Cloud (EC2) Environment has many of the configuration and management taken care of by AWS. This includes creating and managing the instance lifecycle, setting up the Command Line Interface (CLI), and access to packages that are already installed and configured like Git, Node.js, and Python.

CORRECT: "Create an AWS Cloud9 Elastic Compute Cloud (EC2) environment that will provision many of the resources and manage the lifecycle" is the correct answer (as explained above.)

INCORRECT: "Create an SSH environment that will provision many of the resources and manage the lifecycle" is incorrect.

An AWS Cloud9 SSH environment does not provide instance lifecycle management and will require the developer to manually configure or add many features such as the Command Line Interface (CLI) and popular packages like Git, Node.js and Python.

INCORRECT: "Use AWS Toolkit to create an EC2 environment that will provision many of the resources and manage the lifecycle" is incorrect.

AWS Toolkit is an extension of the AWS Cloud9 integrated development environment (IDE). It is not used to create an EC2 environment.

INCORRECT: "Use AWS Toolkit to create an SSH environment that will provision many of the resources and manage the lifecycle" is incorrect.

AWS Toolkit is an extension of the AWS Cloud9 integrated development environment (IDE). It is not used to create an SSH environment.

Джерела: <https://docs.aws.amazon.com/cloud9/latest/user-guide/ec2-env-versus-ssh-env.html>

</details>

---

<a id="t06-q23"></a>
### T06-Q23
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A development team using AWS Amplify is testing new features for an application. How can the team test the features with the current production code without impacting the last published application version?

- ▫️ Connect the production environment into AWS Cloud9 and test the features after committing them.
- ▫️ Connect the production environment into AWS Cloud9 and test the features after publishing them.
- ✅ Connect a new branch that will deploy a version named https://dev.applicationId.amplifyapp.com to test new features.
- ▫️ Connect a new branch that will deploy a version named https://main.applicationId.amplifyapp.com to test new features.

<details><summary>Пояснення (оригінал, EN)</summary>

Features testing on an application should not be done on production code. AWS Amplify supports connecting branches from the production code environment that will clone a repository so that features can be developed, tested and rolled back, if necessary, without impacting the latest version of the published application.

CORRECT: “Connect a new branch that will deploy a version named https://dev.applicationId.amplifyapp.com to test new features” is the correct answer (as explained above.)

INCORRECT: "Connect a new branch that will deploy a version named https://main.applicationId.amplifyapp.com to test new features" is incorrect.

The “main” branch indicated in the URL indicates this is the main production environment and not the one to test features on.

INCORRECT: “Connect the production environment into AWS Cloud9 and test the features after publishing them" is incorrect.

AWS Cloud9 is an IDE that can be used to develop new features if the development chooses to use it. Other IDEs can also be used and integrated in the development process. Once features have passed all the checks, it can be pushed.

INCORRECT: "Connect the production environment into AWS Cloud9 and test the features after committing them" is incorrect.

AWS Cloud9 is an IDE that can be used to develop new features if the development chooses to use it. Other IDEs can also be used and integrated in the development process. Committing the features captures a snapshot of the staged changes.

Джерела: <https://docs.aws.amazon.com/amplify/latest/userguide/multi-environments.html>

</details>

---

<a id="t06-q38"></a>
### T06-Q38
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Development team would like to migrate their existing application code from a GitHub repository to AWS CodeCommit.

What needs to be created before they can migrate a cloned repository to CodeCommit over HTTPS?

- ❌ A GitHub secure authentication token
- ▫️ An Amazon EC2 IAM role with CodeCommit permissions
- ▫️ A public and private SSH key file
- ✅ A set of credentials generated from IAM

<details><summary>Пояснення (оригінал, EN)</summary>

The simplest way to set up connections to AWS CodeCommit repositories is to configure Git credentials for CodeCommit in the IAM console, and then use those credentials for HTTPS connections.

You can also use these same credentials with any third-party tool or individual development environment (IDE) that supports HTTPS authentication using a static user name and password. For examples, see For Connections from Development Tools.

CORRECT: "A set of credentials generated from IAM" is the correct answer.

INCORRECT: "A GitHub secure authentication token" is incorrect as this is not how you authenticated to CodeCommit.

INCORRECT: "A public and private SSH key file" is incorrect as that is required for accessing CodeCommit using SSH.

INCORRECT: "An Amazon EC2 IAM role with CodeCommit permissions" is incorrect as that would be used to provide access to administer CodeCommit. However, the question is asking how to authenticate a Git client to CodeCommit using HTTPS.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html>

</details>

---

<a id="t06-q45"></a>
### T06-Q45
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application will be hosted on the AWS Cloud. Developers will be using an Agile software development methodology with regular updates deployed through a continuous integration and delivery (CI/CD) model. Which AWS service can assist the Developers with automating the build, test, and deploy phases of the release process every time there is a code change?

- ▫️ AWS CodeBuild
- ▫️ AWS CloudFormation
- ✅ AWS CodePipeline
- ▫️ AWS Elastic Beanstalk

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodePipeline is a continuous delivery service you can use to model, visualize, and automate the steps required to release your software. You can quickly model and configure the different stages of a software release process. CodePipeline automates the steps required to release your software changes continuously.

Specifically, you can:

Automate your release processes: CodePipeline fully automates your release process from end to end, starting from your source repository through build, test, and deployment. You can prevent changes from moving through a pipeline by including a manual approval action in any stage except a Source stage. You can release when you want, in the way you want, on the systems of your choice, across one instance or multiple instances.

Establish a consistent release process: Define a consistent set of steps for every code change. CodePipeline runs each stage of your release according to your criteria.

Speed up delivery while improving quality: You can automate your release process to allow your developers to test and release code incrementally and speed up the release of new features to your customers.

Use your favorite tools: You can incorporate your existing source, build, and deployment tools into your pipeline.

View progress at a glance: You can review real-time status of your pipelines, check the details of any alerts, retry failed actions, view details about the source revisions used in the latest pipeline execution in each stage, and manually rerun any pipeline.

View pipeline history details: You can view details about executions of a pipeline, including start and end times, run duration, and execution IDs.

Therefore, AWS CodePipeline is the perfect tool for the Developer’s requirements.

CORRECT: "AWS CodePipeline" is the correct answer.

INCORRECT: "AWS CloudFormation" is incorrect as CloudFormation is not triggered by changes in a source code repository. You must create change sets for deploying updates.

INCORRECT: "AWS Elastic Beanstalk" is incorrect as this is a platform service that can be used to deploy code to managed runtimes such as Nodejs. It does not update automatically based on changes to source code. You must update that environment when you need to release new code.

INCORRECT: "AWS CodeBuild" is incorrect as CodeBuild is used for compiling code, running unit tests and creating the deployment package. It does not manage the deployment of the code.

Джерела: <https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome-what-can-I-do.html>

</details>

---

<a id="t07-q05"></a>
### T07-Q05
*⏭ пропущено · домен: офіційна мітка*

An organization is moving its on-premises resources to the cloud. Source code will be moved to AWS CodeCommit and AWS CodeBuild will be used for compiling the source code using Apache Maven as a build tool. The organization wants the build environment should allow for scaling and running builds in parallel.

Which of the following options should the organization choose for their requirement?

- ▫️ Run CodeBuild in an Auto Scaling group
- ▫️ Enable CodeBuild Auto Scaling
- ✅ CodeBuild scales automatically, the organization does not have to do anything for scaling or for parallel builds
- ▫️ Choose a high-performance instance type for your CodeBuild instances

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

CodeBuild scales automatically, the organization does not have to do anything for scaling or for parallel builds - AWS CodeBuild is a fully managed build service in the cloud. CodeBuild compiles your source code, runs unit tests, and produces artifacts that are ready to deploy. CodeBuild eliminates the need to provision, manage, and scale your own build servers. It provides prepackaged build environments for popular programming languages and build tools such as Apache Maven, Gradle, and more. You can also customize build environments in CodeBuild to use your own build tools. CodeBuild scales automatically to meet peak build requests.

Incorrect options:

Choose a high-performance instance type for your CodeBuild instances - For the current requirement, this is will not make any difference.

Run CodeBuild in an Auto Scaling Group - AWS CodeBuild is a managed service and scales automatically, does not need Auto Scaling Group to scale it up.

Enable CodeBuild Auto Scaling - This has been added as a distractor. CodeBuild scales automatically to meet peak build requests.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-compute-types.html>

</details>

---

<a id="t07-q13"></a>
### T07-Q13
*⏭ пропущено · домен: офіційна мітка*

You have a workflow process that pulls code from AWS CodeCommit and deploys to EC2 instances associated with tag group ProdBuilders. You would like to configure the instances to archive no more than two application revisions to conserve disk space.

Which of the following will allow you to implement this?

- ▫️ AWS CloudWatch Log Agent
- ▫️ Have a load balancer in front of your instances
- ✅ CodeDeploy Agent
- ▫️ Integrate with AWS CodePipeline

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"CodeDeploy Agent"

The CodeDeploy agent is a software package that, when installed and configured on an instance, makes it possible for that instance to be used in CodeDeploy deployments. The CodeDeploy agent archives revisions and log files on instances. The CodeDeploy agent cleans up these artifacts to conserve disk space. You can use the :max_revisions: option in the agent configuration file to specify the number of application revisions to the archive by entering any positive integer. CodeDeploy also archives the log files for those revisions. All others are deleted, except for the log file of the last successful deployment.

More info here:  via - https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent.html

Incorrect options:

AWS CloudWatch Log Agent - The CloudWatch Logs agent provides an automated way to send log data to CloudWatch Logs from Amazon EC2 instances. This is an incorrect choice for the current use case.

Integrate with AWS CodePipeline - AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodeCommit and CodePipeline are already integrated services. CodePipeline cannot help in version control and management of archives on an EC2 instance.

Have a load balancer in front of your instances - Load Balancer helps balance incoming traffic across different EC2 instances. It is an incorrect choice for the current use case.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AgentReference.html>

</details>

---

<a id="t07-q15"></a>
### T07-Q15
*⏭ пропущено · домен: офіційна мітка*

As part of employee skills upgrade, the developers of your team have been delegated few responsibilities of DevOps engineers. Developers now have full control over modeling the entire software delivery process, from coding to deployment. As the team lead, you are now responsible for any manual approvals needed in the process.

Which of the following approaches supports the given workflow?

- ✅ Create one CodePipeline for your entire flow and add a manual approval step
- ▫️ Create deeply integrated AWS CodePipelines for each environment
- ▫️ Use CodePipeline with Amazon Virtual Private Cloud
- ▫️ Create multiple CodePipelines for each environment and link them using AWS Lambda

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create one CodePipeline for your entire flow and add a manual approval step - You can add an approval action to a stage in a CodePipeline pipeline at the point where you want the pipeline to stop so someone can manually approve or reject the action. Approval actions can't be added to Source stages. Source stages can contain only source actions.

Incorrect options:

Create multiple CodePipelines for each environment and link them using AWS Lambda - You can create Lambda functions and add them as actions in your pipelines but the approval step is confined to a workflow process and cannot be outsourced to any other AWS service.

Create deeply integrated AWS CodePipelines for each environment - You can use an AWS CloudFormation template in conjunction with AWS CodePipeline and AWS CodeCommit to create a test environment that deploys to your production environment when the changes to your application are approved, helping you automate a continuous delivery workflow. This is a possible answer but not an optimized way of achieving what the client needs.

Use CodePipeline with Amazon Virtual Private Cloud - AWS CodePipeline supports Amazon Virtual Private Cloud (Amazon VPC) endpoints powered by AWS PrivateLink. This means you can connect directly to CodePipeline through a private endpoint in your VPC, keeping all traffic inside your VPC and the AWS network. This is a robust security feature but is of no value for our current use-case.

Джерела: <https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals-action-add.html> · <https://docs.aws.amazon.com/codepipeline/latest/userguide/vpc-support.html>

</details>

---

<a id="t08-q04"></a>
### T08-Q04
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company uses continuous integration and continuous delivery (CI/CD) systems. A Developer needs to automate the deployment of a software package to Amazon EC2 instances as well as to on-premises virtual servers.

Which AWS service can be used for the software deployment?

- ✅ AWS CodeDeploy
- ▫️ AWS CodePipeline
- ▫️ AWS Elastic Beanstalk
- ▫️ AWS CloudBuild

<details><summary>Пояснення (оригінал, EN)</summary>

CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.

CodeDeploy can deploy application content that runs on a server and is stored in Amazon S3 buckets, GitHub repositories, or Bitbucket repositories. CodeDeploy can also deploy a serverless Lambda function. You do not need to make changes to your existing code before you can use CodeDeploy.

The image below shows the flow of a typical CodeDeploy in-place deployment.

The above deployment could also be directed at on-premises servers. Therefore, the best answer is to use AWS CodeDeploy to deploy the software package to both EC2 instances and on-premises virtual servers.

CORRECT: "AWS CodeDeploy" is the correct answer.

INCORRECT: "AWS CodePipeline" is incorrect. AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. You can use CodeDeploy in a CodePipeline pipeline however it is actually CodeDeploy that deploys the software packages.

INCORRECT: "AWS CloudBuild" is incorrect as this is a build tool, not a deployment tool.

INCORRECT: "AWS Elastic Beanstalk" is incorrect as you cannot deploy software packages to on-premise virtual servers using Elastic Beanstalk

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html>

</details>

---

<a id="t08-q31"></a>
### T08-Q31
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A team of Developers have been assigned to a new project. The team will be collaborating on the development and delivery of a new application and need a centralized private repository for managing source code. The repository should support updates from multiple sources. Which AWS service should the development team use?

- ▫️ AWS CodePipeline
- ✅ AWS CodeCommit
- ▫️ AWS CodeDeploy
- ▫️ AWS CodeBuild

<details><summary>Пояснення (оригінал, EN)</summary>

CodeCommit is a secure, highly scalable, managed source control service that hosts private Git repositories. CodeCommit eliminates the need for you to manage your own source control system or worry about scaling its infrastructure.

You can use CodeCommit to store anything from code to binaries. It supports the standard functionality of Git, so it works seamlessly with your existing Git-based tools.

With CodeCommit, you can:

• Benefit from a fully managed service hosted by AWS. CodeCommit provides high service availability and durability and eliminates the administrative overhead of managing your own hardware and software. There is no hardware to provision and scale and no server software to install, configure, and update.

• Store your code securely. CodeCommit repositories are encrypted at rest as well as in transit.

• Work collaboratively on code. CodeCommit repositories support pull requests, where users can review and comment on each other's code changes before merging them to branches; notifications that automatically send emails to users about pull requests and comments; and more.

• Easily scale your version control projects. CodeCommit repositories can scale up to meet your development needs. The service can handle repositories with large numbers of files or branches, large file sizes, and lengthy revision histories.

• Store anything, anytime. CodeCommit has no limit on the size of your repositories or on the file types you can store.

• Integrate with other AWS and third-party services. CodeCommit keeps your repositories close to your other production resources in the AWS Cloud, which helps increase the speed and frequency of your development lifecycle. It is integrated with IAM and can be used with other AWS services and in parallel with other repositories. Easily migrate files from other remote repositories. You can migrate to CodeCommit from any Git-based repository.

• Use the Git tools you already know. CodeCommit supports Git commands as well as its own AWS CLI commands and APIs.

Therefore, the development team should select AWS CodeCommit as the repository they use for storing code related to the new project.

CORRECT: "AWS CodeCommit" is the correct answer.

INCORRECT: "AWS CodeBuild" is incorrect. AWS CodeBuild is a fully managed continuous integration (CI) service that compiles source code, runs tests, and produces software packages that are ready to deploy.

INCORRECT: "AWS CodeDeploy" is incorrect. CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.

INCORRECT: "AWS CodePipeline" is incorrect. AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html>

</details>

---

<a id="t08-q35"></a>
### T08-Q35
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A firm intends to utilize AWS CodeDeploy to deploy an application to Amazon Elastic Container Service (Amazon ECS). While deploying an updated version of the application, the company's initial requirement is to direct 10% of active traffic to the updated application version. Following a 15-minute interval, all remaining active traffic must be rerouted to the updated application.

Which predefined CodeDeploy configuration aligns with these needs?

- ▫️ CodeDeployDefault.LambdaLinear10PercentEvery1Minutes
- ✅ CodeDeployDefault.ECSCanary10Percent15Minutes
- ▫️ CodeDeployDefault.ECSLinear10PercentEvery10Minutes
- ▫️ CodeDeployDefault.ECSAllAtOnce

<details><summary>Пояснення (оригінал, EN)</summary>

CodeDeployDefault.ECSCanary10Percent15Minutes is a predefined CodeDeploy deployment configuration that will first direct 10% of traffic to the newly deployed application and then, after 15 minutes, route the rest of the traffic to the new version. This fits perfectly with the company's requirements.

CORRECT: "CodeDeployDefault.ECSCanary10Percent15Minutes" is the correct answer (as explained above.)

INCORRECT: "CodeDeployDefault.ECSAllAtOnce" is incorrect.

CodeDeployDefault.ECSAllAtOnce deploys the application revision to all instances at once. This does not meet the requirement to initially expose only 10% of live traffic to the new version.

INCORRECT: "CodeDeployDefault.LambdaLinear10PercentEvery1Minutes" is incorrect.

CodeDeployDefault.LambdaLinear10PercentEvery1Minutes is for AWS Lambda deployments, not Amazon ECS deployments.

INCORRECT: "CodeDeployDefault.ECSLinear10PercentEvery10Minutes" is incorrect.

CodeDeployDefault.ECSLinear10PercentEvery10Minutes increases the traffic routed to the new version by 10% every 10 minutes, not all at once after 15 minutes as per the requirements.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html>

</details>

---

<a id="t09-q16"></a>
### T09-Q16
*⏭ пропущено · домен: офіційна мітка*

A developer needs to automate software package deployment to both Amazon EC2 instances and virtual servers running on-premises, as part of continuous integration and delivery that the business has adopted.

Which AWS service should he use to accomplish this task?

- ▫️ AWS CodeBuild
- ✅ AWS CodeDeploy
- ▫️ AWS Elastic Beanstalk
- ▫️ AWS CodePipeline

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Continuous integration is a DevOps software development practice where developers regularly merge their code changes into a central repository, after which automated builds and tests are run.

Continuous delivery is a software development practice where code changes are automatically prepared for a release to production. A pillar of modern application development, continuous delivery expands upon continuous integration by deploying all code changes to a testing environment and/or a production environment after the build stage.

AWS CodeDeploy - AWS CodeDeploy is a fully managed "deployment" service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications. This is the right choice for the current use case.

Incorrect options:

AWS CodePipeline - AWS CodePipeline is a fully managed "continuous delivery" service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define. This enables you to rapidly and reliably deliver features and updates. Whereas CodeDeploy is a deployment service, CodePipeline is a continuous delivery service. For our current scenario, CodeDeploy is the correct choice.

AWS CodeBuild - AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. With CodeBuild, you don’t need to provision, manage, and scale your own build servers. CodeBuild scales continuously and processes multiple builds concurrently, so your builds are not left waiting in a queue.

AWS Elastic Beanstalk - AWS Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services developed with Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker on familiar servers such as Apache, Nginx, Passenger, and IIS. You can simply upload your code and Elastic Beanstalk automatically handles the deployment, from capacity provisioning, load balancing, auto-scaling to application health monitoring. At the same time, you retain full control over the AWS resources powering your application and can access the underlying resources at any time.

Джерела: <https://aws.amazon.com/codedeploy/> · <https://aws.amazon.com/codepipeline/> · <https://aws.amazon.com/codebuild/> · <https://aws.amazon.com/elasticbeanstalk/> · <https://aws.amazon.com/devops/continuous-delivery/> · <https://aws.amazon.com/devops/continuous-integration/>

</details>

---

<a id="t09-q39"></a>
### T09-Q39
*⏭ пропущено · домен: офіційна мітка*

A company needs a version control system for their fast development lifecycle with incremental changes, version control, and support to existing Git tools.

Which AWS service will meet these requirements?

- ✅ AWS CodeCommit
- ▫️ AWS CodeBuild
- ▫️ AWS CodePipeline
- ▫️ Amazon Versioned S3 Bucket

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CodeCommit - AWS CodeCommit is a fully-managed Source Control service that hosts secure Git-based repositories. It makes it easy for teams to collaborate on code in a secure and highly scalable ecosystem. AWS CodeCommit helps you collaborate on code with teammates via pull requests, branching and merging. AWS CodeCommit keeps your repositories close to your build, staging, and production environments in the AWS cloud. You can transfer incremental changes instead of the entire application. AWS CodeCommit supports all Git commands and works with your existing Git tools. You can keep using your preferred development environment plugins, continuous integration/continuous delivery systems, and graphical clients with CodeCommit.

Incorrect options:

Amazon Versioned S3 Bucket - AWS CodeCommit is designed for collaborative software development. It manages batches of changes across multiple files, offers parallel branching, and includes version differencing ("diffing"). In comparison, Amazon S3 versioning supports recovering past versions of individual files but doesn’t support tracking batched changes that span multiple files or other features needed for collaborative software development.

AWS CodePipeline - AWS CodePipeline is a fully managed "continuous delivery" service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define.

AWS CodeBuild - AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. With CodeBuild, you don’t need to provision, manage, and scale your own build servers. CodeBuild scales continuously and processes multiple builds concurrently, so your builds are not left waiting in a queue.

Джерела: <https://aws.amazon.com/codecommit/> · <https://aws.amazon.com/codepipeline/> · <https://aws.amazon.com/codebuild/> · <https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html>

</details>

---

<a id="t09-q59"></a>
### T09-Q59
*⏭ пропущено · домен: офіційна мітка*

A developer in your company was just promoted to Team Lead and will be in charge of code deployment on EC2 instances via AWS CodeCommit and AWS CodeDeploy. Per the new requirements, the deployment process should be able to change permissions for deployed files as well as verify the deployment success.

Which of the following actions should the new Developer take?

- ▫️ Define a buildspec.yml file in the codebuild/ directory
- ▫️ Define an appspec.yml file in the codebuild/ directory
- ▫️ Define a buildspec.yml file in the root directory
- ✅ Define an appspec.yml file in the root directory

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Define an appspec.yml file in the root directory: An AppSpec file must be a YAML-formatted file named appspec.yml and it must be placed in the root of the directory structure of an application's source code.

The AppSpec file is used to:

Map the source files in your application revision to their destinations on the instance.

Specify custom permissions for deployed files.

Specify scripts to be run on each instance at various stages of the deployment process.

During deployment, the CodeDeploy agent looks up the name of the current event in the hooks section of the AppSpec file. If the event is not found, the CodeDeploy agent moves on to the next step. If the event is found, the CodeDeploy agent retrieves the list of scripts to execute. The scripts are run sequentially, in the order in which they appear in the file. The status of each script is logged in the CodeDeploy agent log file on the instance.

If a script runs successfully, it returns an exit code of 0 (zero). If the CodeDeploy agent installed on the operating system doesn't match what's listed in the AppSpec file, the deployment fails.

Incorrect options:

Define a buildspec.yml file in the root directory - This is a file used by AWS CodeBuild to run a build. This is not relevant to the given use case.

Define a buildspec.yml file in the codebuild/ directory - This is a file used by AWS CodeBuild to run a build. This is not relevant to the given use case.

Define an appspec.yml file in the codebuild/ directory - This file is for AWS CodeDeploy and must be placed in the root of the directory structure of an application's source code.

Reference:

https://docs.aws.amazon.com/codedeploy/latest/userguide/application-specification-files.html

</details>

---

<a id="t10-q03"></a>
### T10-Q03
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A development team require a fully-managed source control service that is compatible with Git.

Which service should they use?

- ▫️ AWS Cloud9
- ▫️ AWS CodePipeline
- ▫️ AWS CodeDeploy
- ✅ AWS CodeCommit

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeCommit is a version control service hosted by Amazon Web Services that you can use to privately store and manage assets (such as documents, source code, and binary files) in the cloud. CodeCommit is a fully-managed service that hosts secure Git-based repositories.

CORRECT: "AWS CodeCommit" is the correct answer.

INCORRECT: "AWS CodeDeploy" is incorrect. CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.

INCORRECT: "AWS CodePipeline" is incorrect. AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates.

INCORRECT: "AWS Cloud9" is incorrect. AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html>

</details>

---

<a id="t10-q09"></a>
### T10-Q09
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has joined a team and needs to connect to the AWS CodeCommit repository using SSH. What should the Developer do to configure access using Git?

- ▫️ Create an account on Github and user those login credentials to login to AWS CodeCommit
- ✅ Generate an SSH public and private key. Upload the public key to the Developer’s IAM account
- ▫️ On the Developer’s IAM account, under security credentials, choose to create HTTPS Git credentials for AWS CodeCommit
- ▫️ On the Developer’s IAM account, under security credentials, choose to create an access key and secret ID

<details><summary>Пояснення (оригінал, EN)</summary>

You need to configure your Git client to communicate with CodeCommit repositories. As part of this configuration, you provide IAM credentials that CodeCommit can use to authenticate you. IAM supports CodeCommit with three types of credentials:

• Git credentials, an IAM -generated user name and password pair you can use to communicate with CodeCommit repositories over HTTPS.

• SSH keys, a locally generated public-private key pair that you can associate with your IAM user to communicate with CodeCommit repositories over SSH.

• AWS access keys, which you can use with the credential helper included with the AWS CLI to communicate with CodeCommit repositories over HTTPS.

As the Developer is going to use SSH, he first needs to generate an SSH private and public key. These can then be used for authentication. The method of creating these depends on the operating system the Developer is using. Then, the Developer can upload the public key (by copying the contents of the file) into his IAM account under security credentials.

CORRECT: "Generate an SSH public and private key. Upload the public key to the Developer’s IAM account" is the correct answer.

INCORRECT: "On the Developer’s IAM account, under security credentials, choose to create HTTPS Git credentials for AWS CodeCommit" is incorrect as this method is used for creating credentials when you want to connect to CodeCommit using HTTPS.

INCORRECT: "Create an account on Github and user those login credentials to login to AWS CodeCommit" is incorrect as you cannot login to AWS CodeCommit using credentials from Github.

INCORRECT: "On the Developer’s IAM account, under security credentials, choose to create an access key and secret ID" is incorrect as though you can use access keys to authenticated to CodeCommit, this requires the credential helper, and enables access over HTTPS.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html> · <https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-unixes.html>

</details>

---

<a id="t10-q17"></a>
### T10-Q17
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team have moved their continuous integration and delivery (CI/CD) pipeline into the AWS Cloud. The team is leveraging AWS CodeCommit for management of source code. The team need to compile their source code, run tests, and produce software packages that are ready for deployment.

Which AWS service can deliver these outcomes?

- ▫️ AWS CodeCommit
- ✅ AWS CodeBuild
- ▫️ AWS CodePipeline
- ▫️ AWS Cloud9

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeBuild is a fully managed build service in the cloud. CodeBuild compiles your source code, runs unit tests, and produces artifacts that are ready to deploy. CodeBuild eliminates the need to provision, manage, and scale your own build servers. It provides prepackaged build environments for popular programming languages and build tools such as Apache Maven, Gradle, and more.

You can also customize build environments in CodeBuild to use your own build tools. CodeBuild scales automatically to meet peak build requests.

CodeBuild provides these benefits:

• Fully managed – CodeBuild eliminates the need to set up, patch, update, and manage your own build servers.

• On demand – CodeBuild scales on demand to meet your build needs. You pay only for the number of build minutes you consume.

• Out of the box – CodeBuild provides preconfigured build environments for the most popular programming languages. All you need to do is point to your build script to start your first build.

Therefore, AWS CodeBuild is the best service to use to compile the Development team’s source code, run tests, and produce software packages that are ready for deployment.

CORRECT: "AWS CodeBuild" is the correct answer.

INCORRECT: "AWS CodeCommit" is incorrect. The team are already using CodeCommit for its correct purpose, which is to manage source code. CodeCommit cannot perform compiling of source code, testing, or package creation.

INCORRECT: "AWS CodePipeline" is incorrect. AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates.

INCORRECT: "AWS Cloud9" is incorrect. AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html>

</details>

---

<a id="t10-q57"></a>
### T10-Q57
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Developer is creating an AWS Lambda function that generates a new file each time it runs. Each new file must be checked into an AWS CodeCommit repository hosted in the same AWS account.

How should the Developer accomplish this?

- ▫️ Upload the new file to an Amazon S3 bucket. Create an AWS Step Function to accept S3 events. In the Step Function, add the new file to the repository
- ✅ Use an AWS SDK to instantiate a CodeCommit client. Invoke the put_file method to add the file to the repository
- ▫️ When the Lambda function starts, use the Git CLI to clone the repository. Check the new file into the cloned repository and push the change
- ▫️ After the new file is created in Lambda, use cURL to invoke the CodeCommit API. Send the file to the repository

<details><summary>Пояснення (оригінал, EN)</summary>

The Developer can use the AWS SDK to instantiate a CodeCommit client. For instance, the code might include the following:

The client can then be used with put_file which adds or updates a file in a branch in an AWS CodeCommit repository, and generates a commit for the addition in the specified branch.

The request syntax is as follows:

CORRECT: "Use an AWS SDK to instantiate a CodeCommit client. Invoke the put_file method to add the file to the repository" is the correct answer.

INCORRECT: "When the Lambda function starts, use the Git CLI to clone the repository. Check the new file into the cloned repository and push the change" is incorrect as there is no need to clone a repository, a file just needs to be added to an existing repository.

INCORRECT: "After the new file is created in Lambda, use cURL to invoke the CodeCommit API. Send the file to the repository" is incorrect as a URL cannot be used to invoke a CodeCommit client and upload and check in the file.

INCORRECT: "Upload the new file to an Amazon S3 bucket. Create an AWS Step Function to accept S3 events. In the Step Function, add the new file to the repository" is incorrect as Step Functions is not triggered by S3 events.

Джерела: <https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/codecommit.html>

</details>

---

<a id="t10-q59"></a>
### T10-Q59
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

```json
A Developer needs to access AWS CodeCommit over SSH. The SSH keys configured to access AWS CodeCommit are tied to a user with the following permissions:
{
"version": "2012-10-17"
"Statement": [
{
"Effect": "Allow",
"Action": [
"codecommit:BatchGetRepositories",
"codecommit:Get*"
"codecommit:List*",
"codecommit:GitPull"
],
"Resource": "*"
}
]
}
The Developer needs to create/delete branches.
Which specific IAM permissions need to be added based on the principle of least privilege?
```

- ▫️ “codecommit:Put\*:”
- ▫️ “codecommit:\*”
- ▫️ “codecommit:Update\*”
- ✅ “codecommit:CreateBranch” and “codecommit:DeleteBranch”

<details><summary>Пояснення (оригінал, EN)</summary>

The permissions assigned to the user account are missing the privileges to create and delete branches in AWS CodeCommit. The Developer needs to be assigned these permissions but according to the principal of least privilege it’s important to ensure no additional permissions are assigned.

The following API actions can be used to work with branches:

• CreateBranch , which creates a branch in a specified repository.

• DeleteBranch , which deletes the specified branch in a repository unless it is the default branch.

• GetBranch , which returns information about a specified branch.

• ListBranches , which lists all branches for a specified repository.

• UpdateDefaultBranch , which changes the default branch for a repository.

Therefore, the best answer is to add the “codecommit:CreateBranch” and “codecommit:DeleteBranch” permissions to the permissions policy.

CORRECT: "codecommit:CreateBranch” and “codecommit:DeleteBranch" is the correct answer.

INCORRECT: "codecommit:Put\*:" is incorrect. The wildcard (\*) will allow any API action starting with “Put”, however the only options are put-file and put-repository-triggers, neither of which is related to branches.

INCORRECT: "codecommit:Update\*" is incorrect. The wildcard (\*) will allow any API action starting with “Update”, however none of the options available are suitable for working with branches.

INCORRECT: "codecommit:\*" is incorrect as this would allow any API action which does not follow the principal of least privilege.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/codecommit/index.html#cli-aws-codecommit>

</details>

---

<a id="t10-q63"></a>
### T10-Q63
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A company needs a fully-managed source control service that will work in AWS. The service must ensure that revision control synchronizes multiple distributed repositories by exchanging sets of changes peer-to-peer. All users need to work productively even when not connected to a network.

Which source control service should be used?

- ▫️ AWS CodeBuild
- ▫️ AWS CodeStar
- ✅ AWS CodeCommit
- ▫️ Subversion

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CodeCommit is a version control service hosted by Amazon Web Services that you can use to privately store and manage assets (such as documents, source code, and binary files) in the cloud.

A repository is the fundamental version control object in CodeCommit. It's where you securely store code and files for your project. It also stores your project history, from the first commit through the latest changes. You can share your repository with other users so you can work together on a project. If you add AWS tags to repositories, you can set up notifications so that repository users receive email about events (for example, another user commenting on code).

You can also change the default settings for your repository, browse its contents, and more. You can create triggers for your repository so that code pushes or other events trigger actions, such as emails or code functions. You can even configure a repository on your local computer (a local repo) to push your changes to more than one repository.

CORRECT: "AWS CodeCommit" is the correct answer.

INCORRECT: "Subversion" is incorrect as this is not a fully managed source control system

INCORRECT: "AWS CodeBuild" is incorrect as this is a service used for building and testing code.

INCORRECT: "AWS CodeStar" is incorrect as this is not a source control system; it integrates with source control systems such as CodeCommit.

Джерела: <https://aws.amazon.com/codecommit/>

</details>

---

<a id="t10-q65"></a>
### T10-Q65
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Хуки ECS.** → [детальніше](../../06-contradictions-and-traps.md#c08)

A Developer is deploying an Amazon ECS update using AWS CodeDeploy. In the appspec.yaml file, which of the following is a valid structure for the order of hooks that should be specified?

- ▫️ BeforeAllowTraffic &gt; AfterAllowTraffic
- ✅ BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic
- ▫️ BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService

<details><summary>Пояснення (оригінал, EN)</summary>

The content in the 'hooks' section of the AppSpec file varies, depending on the compute platform for your deployment. The 'hooks' section for an EC2/On-Premises deployment contains mappings that link deployment lifecycle event hooks to one or more scripts.

The 'hooks' section for a Lambda or an Amazon ECS deployment specifies Lambda validation functions to run during a deployment lifecycle event. If an event hook is not present, no operation is executed for that event. This section is required only if you are running scripts or Lambda validation functions as part of the deployment.

The following code snippet shows a valid example of the structure of hooks for an Amazon ECS deployment:

Therefore, in this scenario a valid structure for the order of hooks that should be specified in the appspec.yml file is: BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic

CORRECT: "BeforeInstall &gt; AfterInstall &gt; AfterAllowTestTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic" is the correct answer.

INCORRECT: "BeforeInstall &gt; AfterInstall &gt; ApplicationStart &gt; ValidateService" is incorrect as this would be valid for Amazon EC2.

INCORRECT: "BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this would be valid for AWS Lambda.

INCORRECT: "BeforeBlockTraffic &gt; AfterBlockTraffic &gt; BeforeAllowTraffic &gt; AfterAllowTraffic" is incorrect as this is a partial listing of hooks for Amazon EC2 but is incomplete.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html>

</details>

---

<a id="t11-q10"></a>
### T11-Q10
*⏭ пропущено · домен: офіційна мітка*

CodeCommit is a managed version control service that hosts private Git repositories in the AWS cloud.

Which of the following credential types is NOT supported by IAM for CodeCommit?

- ✅ IAM username and password
- ▫️ SSH Keys
- ▫️ Git credentials
- ▫️ AWS Access Keys

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

IAM username and password - IAM username and password credentials cannot be used to access CodeCommit.

Incorrect options:

Git credentials - These are IAM -generated user name and password pair you can use to communicate with CodeCommit repositories over HTTPS.

SSH Keys - Are locally generated public-private key pair that you can associate with your IAM user to communicate with CodeCommit repositories over SSH.

AWS access keys - You can use these keys with the credential helper included with the AWS CLI to communicate with CodeCommit repositories over HTTPS.

Reference:

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html

</details>

---

<a id="t11-q46"></a>
### T11-Q46
*⏭ пропущено · домен: офіційна мітка*

You're a developer working on a large scale order processing application. After developing the features, you commit your code to AWS CodeCommit and begin building the project with AWS CodeBuild before it gets deployed to the server. The build is taking too long and the error points to an issue resolving dependencies from a third-party. You would like to prevent a build running this long in the future for similar underlying reasons.

Which of the following options represents the best solution to address this use-case?

- ▫️ Use VPC Flow Logs
- ▫️ Use Amazon CloudWatch
- ✅ Enable CodeBuild timeouts
- ▫️ Use AWS Lambda

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Enable CodeBuild timeouts

A build represents a set of actions performed by AWS CodeBuild to create output artifacts (for example, a JAR file) based on a set of input artifacts (for example, a collection of Java class files).

The following rules apply when you run multiple builds:

When possible, builds run concurrently. The maximum number of concurrently running builds can vary.

Builds are queued if the number of concurrently running builds reaches its limit. The maximum number of builds in a queue is five times the concurrent build limit.

A build in a queue that does not start after the number of minutes specified in its time out value is removed from the queue. The default timeout value is eight hours. You can override the build queue timeout with a value between five minutes and eight hours when you run your build.

By setting the timeout configuration, the build process will automatically terminate post the expiry of the configured timeout.

Incorrect options:

Use AWS Lambda - AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume. Lambda cannot be used to impact the code build process.

Use Amazon CloudWatch - Amazon CloudWatch allows you to monitor AWS cloud resources and the applications you run on AWS. Metrics are provided automatically for a number of AWS products and services. CloudWatch is good for monitoring and viewing logs. CloudWatch cannot be used to impact the code build process.

Use VPC Flow Logs - VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC but not for code compiling configuration. VPC Flow Logs cannot be used to impact the code build process.

Reference:

https://docs.aws.amazon.com/codebuild/latest/userguide/builds-working.html

</details>

---

<a id="t11-q52"></a>
### T11-Q52
*⏭ пропущено · домен: офіційна мітка*

A developer has been asked to create an application that can be deployed across a fleet of EC2 instances. The configuration must allow for full control over the deployment steps using the blue-green deployment.

Which service will help you achieve that?

- ▫️ CodeBuild
- ✅ CodeDeploy
- ▫️ Elastic Beanstalk
- ▫️ CodePipeline

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

CodeDeploy

AWS CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, or serverless Lambda functions. AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy.

The blue/green deployment type uses the blue/green deployment model controlled by CodeDeploy. This deployment type enables you to verify a new deployment of service before sending production traffic to it.

CodeDeploy offers lot of control over deployment steps. Please see this note for more details:  via - https://aws.amazon.com/about-aws/whats-new/2017/01/aws-codedeploy-introduces-blue-green-deployments/

Incorrect options:

CodeBuild - AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. It cannot be used to deploy applications.

Elastic Beanstalk - AWS Elastic Beanstalk offers hooks but not as much control as CodeDeploy. Because AWS Elastic Beanstalk performs an in-place update when you update your application versions, your application can become unavailable to users for a short period of time. You can avoid this downtime by performing a blue/green deployment, where you deploy the new version to a separate environment, and then swap CNAMEs of the two environments to redirect traffic to the new version instantly.

CodePipeline - CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change. CodePipeline by itself cannot deploy applications.

Reference:

https://docs.amazonaws.cn/en_us/codedeploy/latest/userguide/deployment-configurations.html

</details>

---

<a id="t12-q51"></a>
### T12-Q51
*⏭ пропущено · домен: офіційна мітка*

As a site reliability engineer, you work on building and running large-scale, distributed, fault-tolerant systems in the cloud using automation. You have just replaced the company's Jenkins based CI/CD platform with AWS CodeBuild and would like to programmatically define your build steps.

Which of the following options should you choose?

- ✅ Define a buildspec.yml file in the root directory
- ▫️ Define a buildspec.yml file in the codebuild/ directory
- ▫️ Define an appspec.yml file in the root directory
- ▫️ Define an appspec.yml file in the codebuild/ directory

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Define a buildspec.yml file in the root directory

AWS CodeDeploy is a fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications.

A build spec is a collection of build commands and related settings, in YAML format, that AWS CodeBuild uses to run a build. You can include a build spec as part of the source code or you can define a build spec when you create a build project.

 via - https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html

Incorrect options:

Define an appspec.yml file in the root directory - The AppSpec file is used for deployment in the CodeDeploy service.

Define a buildspec.yml file in the codebuild/ directory - The file is correct but must be in the root directory.

Define an appspec.yml file in the codebuild/ directory - The AppSpec file is used for deployment in the CodeDeploy service.

Reference:

https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html

</details>

---

<a id="t12-q59"></a>
### T12-Q59
*⏭ пропущено · домен: офіційна мітка*

As a site reliability engineer, you are responsible for improving the company’s deployment by scaling and automating applications. As new application versions are ready for production you ensure that the application gets deployed to different sets of EC2 instances at different times allowing for a smooth transition.

Using AWS CodeDeploy, which of the following options will allow you to do this?

- ▫️ CodeDeploy Agent
- ▫️ CodeDeploy Hooks
- ▫️ Define multiple CodeDeploy Applications
- ✅ CodeDeploy Deployment Groups

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

CodeDeploy Deployment Groups

You can specify one or more deployment groups for a CodeDeploy application. The deployment group contains settings and configurations used during the deployment. Most deployment group settings depend on the compute platform used by your application. Some settings, such as rollbacks, triggers, and alarms can be configured for deployment groups for any compute platform.

In an EC2/On-Premises deployment, a deployment group is a set of individual instances targeted for deployment. A deployment group contains individually tagged instances, Amazon EC2 instances in Amazon EC2 Auto Scaling groups, or both.

Incorrect options:

CodeDeploy Agent - The CodeDeploy agent is a software package that, when installed and configured on an instance, makes it possible for that instance to be used in CodeDeploy deployments. The agent connects the EC2 instances to the CodeDeploy service.

CodeDeploy Hooks - Hooks are found in the AppSec file used by AWS CodeDeploy to manage deployment. Hooks correspond to lifecycle events such as ApplicationStart, ApplicationStop, etc. to which you can assign a script.

Define multiple CodeDeploy Applications - This option has been added as a distractor. Instead, you want to use deployment groups to use the same deployment and maybe separate the times when a group of instances receives the software updates.

Reference:

https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html

</details>

---
