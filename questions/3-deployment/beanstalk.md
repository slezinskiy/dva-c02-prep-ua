# AWS Elastic Beanstalk

**Домен 3 — Розгортання** · питань: 20  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#beanstalk) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q08"></a>
### T01-Q08
*⏭ пропущено · домен: офіційна мітка*

Your organization has set up a full CI/CD pipeline leveraging CodePipeline and the deployment is done on Elastic Beanstalk. This pipeline has worked for over a year now but you are approaching the limits of Elastic Beanstalk in terms of how many versions can be stored in the service.

How can you remove older versions that are not used by Elastic Beanstalk so that new versions can be created for your applications?

- ▫️ Use Worker Environments
- ✅ Use a Lifecycle Policy
- ▫️ Setup an .ebextensions file
- ▫️ Define a Lambda function

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use a Lifecycle Policy

Each time you upload a new version of your application with the Elastic Beanstalk console or the EB CLI, Elastic Beanstalk creates an application version. If you don't delete versions that you no longer use, you will eventually reach the application version limit and be unable to create new versions of that application.

You can avoid hitting the limit by applying an application version lifecycle policy to your applications. A lifecycle policy tells Elastic Beanstalk to delete old application versions or to delete application versions when the total number of versions for an application exceeds a specified number.

Elastic Beanstalk applies an application's lifecycle policy each time you create a new application version and deletes up to 100 versions each time the lifecycle policy is applied. Elastic Beanstalk deletes old versions after creating the new version and does not count the new version towards the maximum number of versions defined in the policy.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-lifecycle.html

Incorrect options: Setup an .ebextensions files - You can add AWS Elastic Beanstalk configuration files (.ebextensions) to your web application's source code to configure your environment and customize the AWS resources that it contains. This does not help with managing versions.

Define a Lambda function - This could work but would require a lot of manual scripting, to achieve the same desired effect as the Lifecycle Policy EB feature.

Use Worker Environments - This won't help. If your application performs operations or workflows that take a long time to complete, you can offload those tasks to a dedicated worker environment. Decoupling your web application front end from a process that performs blocking operations is a common way to ensure that your application stays responsive under load.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-lifecycle.html

</details>

---

<a id="t01-q58"></a>
### T01-Q58
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EB: Rolling with additional batch, batch=1.** → [детальніше](../../06-contradictions-and-traps.md#c07)

An e-commerce company has deployed its application on AWS Elastic Beanstalk. The Auto Scaling group associated with the Beanstalk environment has three Amazon EC2 instances. When the number of instances falls below two, it severely impacts the performance of the web application. The company currently uses the default all-at-once deployment policy and is looking for an effective strategy for future deployments.

Which of the following represents the most cost-effective deployment strategy for the company?

- ▫️ Opt for rolling deployment strategy. Set the batch size to 2
- ▫️ Opt for traffic-splitting deployment strategy with traffic split parameter set to 50% of the total traffic
- ▫️ Configure an Elastic Load Balancer to front the Auto Scaling Group and choose two different Availability Zones (AZs) for deployment
- ✅ Opt for rolling with additional batch deployment strategy. Set the batch size parameter to 1

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Opt for rolling with additional batch deployment strategy. Set the batch size parameter to 1

With rolling deployments, Elastic Beanstalk splits the environment's Amazon EC2 instances into batches and deploys the new version of the application to one batch at a time. It leaves the rest of the instances in the environment running the old version of the application. During a rolling deployment, some instances serve requests with the old version of the application, while instances in completed batches serve other requests with the new version.

To maintain full capacity during deployments, you can configure your environment to launch a new batch of instances before taking any instances out of service. This option is known as a rolling deployment with an additional batch. When the deployment completes, Elastic Beanstalk terminates the additional batch of instances.

For the rolling and rolling with additional batch deployment policies, you can configure Batch size – The size of the set of instances to deploy in each batch. Choose Percentage to configure a percentage of the total number of EC2 instances in the Auto Scaling group (up to 100 percent), or choose Fixed to configure a fixed number of instances (up to the maximum instance count in your environment's Auto Scaling configuration).

Example configuration for rolling With additional batch:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html#environments-cfg-rollingdeployments-method

Incorrect options:

Opt for a traffic-splitting deployment strategy with a traffic split parameter set to 50% of the total traffic - Traffic-splitting deployments let you perform canary testing as part of your application deployment. In a traffic-splitting deployment, Elastic Beanstalk launches a full set of new instances just like during an immutable deployment. It then forwards a specified percentage of incoming client traffic to the new application version for a specified evaluation period. This turns out to be a costly solution for the given use case.

Configure an Elastic Load Balancer to front the Auto Scaling Group and choose two different Availability Zones (AZs) for deployment - Adding AZs makes the entire configuration resilient for failures post-deployment. However, it does not represent a valid deployment strategy for the given use case. This option has been added as a distractor.

Opt for a rolling deployment strategy. Set the batch size to 2 - As already discussed, with rolling deployments, Elastic Beanstalk splits the environment's Amazon EC2 instances into batches and deploys the new version of the application to one batch at a time. With a batch size set to 2, only 1 instance will be left for traffic during deployment, impacting the performance of the e-commerce application. Hence, this option is incorrect.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t01-q62"></a>
### T01-Q62
*⏭ пропущено · домен: офіційна мітка*

You have created a test environment in Elastic Beanstalk and as part of that environment, you have created an RDS database.

How can you make sure the database can be explored after the environment is destroyed?

- ▫️ Change the Elastic Beanstalk environment variables
- ▫️ Make a selective delete in Elastic Beanstalk
- ▫️ Convert the Elastic Beanstalk environment to a worker environment
- ✅ Make a snapshot of the database before it gets deleted

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Make a snapshot of the database before it gets deleted

Use an Elastic Beanstalk blue (environment A)/green (environment B) deployment to decouple an RDS DB instance from environment.

Create a new Elastic Beanstalk environment (environment B) with the necessary information to connect to the RDS DB instance.

Note: An RDS DB instance attached to an Elastic Beanstalk environment is ideal for development and testing environments. However, it's not ideal for production environments because the lifecycle of the database instance is tied to the lifecycle of your application environment. If you terminate the environment, then you lose your data because the RDS DB instance is deleted by the environment. For more information, see Using Elastic Beanstalk with Amazon RDS.

This is the only way to recover the database data before it gets deleted by Elastic Beanstalk.

Please review this excellent document that addresses this use-case :

https://aws.amazon.com/premiumsupport/knowledge-center/decouple-rds-from-beanstalk/

Incorrect options:

Make a selective delete in Elastic Beanstalk - This is not a feature in Elastic Beanstalk.

Change the Elastic Beanstalk environment variables - Environment variables won't help with the provisioned RDS database.

Convert the Elastic Beanstalk environment to a worker environment - You can't convert Elastic Beanstalk environments, you can only change their settings.

Reference:

https://aws.amazon.com/premiumsupport/knowledge-center/decouple-rds-from-beanstalk/

</details>

---

<a id="t02-q03"></a>
### T02-Q03
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

An application on-premises uses Linux servers and a relational database using PostgreSQL. The company will be migrating the application to AWS and require a managed service that will take care of capacity provisioning, load balancing, and auto-scaling.

Which combination of services should the Developer use? (Select TWO.)

- ❌ Amazon EC2 with Auto Scaling
- ✅ AWS Elastic Beanstalk
- ▫️ AWS Lambda with CloudWatch Events
- ▫️ Amazon EC2 with PostgreSQL
- ✅ Amazon RDS with PostrgreSQL

<details><summary>Пояснення (оригінал, EN)</summary>

The company require a managed service therefore the Developer should choose to use Elastic Beanstalk for the compute layer and Amazon RDS with the PostgreSQL engine for the database layer.

AWS Elastic Beanstalk will handle all capacity provisioning, load balancing, and auto-scaling for the web front-end and Amazon RDS provides push-button scaling for the backend.

CORRECT: "AWS Elastic Beanstalk" is a correct answer.

CORRECT: "Amazon RDS with PostrgreSQL" is also a correct answer.

INCORRECT: "Amazon EC2 with Auto Scaling" is incorrect as though these services will be used to provide the automatic scalability required for the solution, they still need to be managed. The questions asks for a managed solution and Elastic Beanstalk will manage this for you. Also, there is no mention of a load balancer so connections cannot be distributed to instances.

INCORRECT: "Amazon EC2 with PostgreSQL" is incorrect as the question asks for a managed service and therefore the database should be run on Amazon RDS.

INCORRECT: "AWS Lambda with CloudWatch Events" is incorrect as there is no mention of refactoring application code to run on AWS Lambda.

Джерела: <https://aws.amazon.com/elasticbeanstalk/> · <https://aws.amazon.com/rds/postgresql/>

</details>

---

<a id="t02-q50"></a>
### T02-Q50
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Документація: 500 MB (у банку 512 MB).** → [детальніше](../../07-outdated-and-wrong-answers.md#o10)

A Developer is creating a new web application that will be deployed using AWS Elastic Beanstalk from the AWS Management Console. The Developer is about to create a source bundle which will be uploaded using the console.

Which of the following are valid requirements for creating the source bundle? (Select TWO.)

- ▫️ Must include a parent folder or top-level directory.
- ✅ Must not include a parent folder or top-level directory.
- ❌ Must consist of one or more ZIP files.
- ▫️ Must include the cron.yaml file.
- ✅ Must not exceed 512 MB.

<details><summary>Пояснення (оригінал, EN)</summary>

When you use the AWS Elastic Beanstalk console to deploy a new application or an application version, you'll need to upload a source bundle. Your source bundle must meet the following requirements:

  •  Consist of a single ZIP file or WAR file (you can include multiple WAR files inside your ZIP file)

  •  Not exceed 512 MB

  •  Not include a parent folder or top-level directory (subdirectories are fine)

If you want to deploy a worker application that processes periodic background tasks, your application source bundle must also include a cron.yaml file, but in other cases it is not required.

CORRECT: "Must not include a parent folder or top-level directory" is a correct answer.

CORRECT: "Must not exceed 512 MB" is also a correct answer.

INCORRECT: "Must include the cron.yaml file" is incorrect. As mentioned above, this is not required in all cases.

INCORRECT: "Must include a parent folder or top-level directory" is incorrect. A parent folder or top-level directory must NOT be included.

INCORRECT: "Must consist of one or more ZIP files" is incorrect. You bundle into a single ZIP or WAR file.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-sourcebundle.html>

</details>

---

<a id="t03-q12"></a>
### T03-Q12
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EB: dev, downtime OK → All at once.** → [детальніше](../../06-contradictions-and-traps.md#c07)

You have moved your on-premise infrastructure to AWS and are in the process of configuring an AWS Elastic Beanstalk deployment environment for production, development, and testing. You have configured your production environment to use a rolling deployment to prevent your application from becoming unavailable to users. For the development and testing environment, you would like to deploy quickly and are not concerned about downtime.

Which of the following deployment policies meet your needs?

- ▫️ Immutable
- ✅ All at once
- ▫️ Rolling with additional batches
- ▫️ Rolling

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

All at once

This is the quickest deployment method. Suitable if you can accept a short loss of service, and if quick deployments are important to you. With this method, Elastic Beanstalk deploys the new application version to each instance. Then, the web proxy or application server might need to restart. As a result, your application might be unavailable to users (or have low availability) for a short time.

Overview of Elastic Beanstalk Deployment Policies:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

Incorrect options:

Rolling - With this method, your application is deployed to your environment one batch of instances at a time. Most bandwidth is retained throughout the deployment. Avoids downtime and minimizes reduced availability, at a cost of a longer deployment time. Suitable if you can't accept any period of completely lost service.

Rolling with additional batches - With this method, Elastic Beanstalk launches an extra batch of instances, then performs a rolling deployment. Launching the extra batch takes time, and ensures that the same bandwidth is retained throughout the deployment. This policy also avoids any reduced availability, although at a cost of an even longer deployment time compared to the Rolling method. Finally, this option is suitable if you must maintain the same bandwidth throughout the deployment.

Immutable - A slower deployment method, that ensures your new application version is always deployed to new instances, instead of updating existing instances. It also has the additional advantage of a quick and safe rollback in case the deployment fails. With this method, Elastic Beanstalk performs an immutable update to deploy your application. In an immutable update, a second Auto Scaling group is launched in your environment and the new version serves traffic alongside the old version until the new instances pass health checks.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

</details>

---

<a id="t04-q15"></a>
### T04-Q15
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: швидкий rollback → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A company has a website that is developed in PHP and WordPress and is launched using AWS Elastic Beanstalk. There is a new version of the website that needs to be deployed in the Elastic Beanstalk environment. The company cannot tolerate having the website offline if an update fails. Deployments must have minimal impact and rollback as soon as possible.

What deployment method should be used?

- ▫️ Snapshots
- ✅ Immutable
- ▫️ Rolling
- ▫️ All at once

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.

All at once:

• Deploys the new version to all instances simultaneously.

Rolling:

• Update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy (downtime for 1 bucket at a time).

Rolling with additional batch:

• Like Rolling but launches new instances in a batch ensuring that there is full availability.

Immutable:

• Launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy.

• Zero downtime.

Blue / Green deployment:

• Zero downtime and release facility.

• Create a new “stage” environment and deploy updates there.

For this scenario, the best choice is Immutable as this is the safest option when you cannot tolerate downtime and also provides a simple way of rolling back should an issue occur.

CORRECT: "Immutable" is the correct answer.

INCORRECT: "All at once" is incorrect as this will take all instances down and cause a total outage.

INCORRECT: "Snapshots" is incorrect as this is not a deployment method you can use with Elastic Beanstalk.

INCORRECT: "Rolling" is incorrect as this will reduce the capacity of the application and it is more difficult to roll back as you must redeploy the old version to the instances.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t05-q28"></a>
### T05-Q28
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is planning the deployment of a new version of an application to AWS Elastic Beanstalk. The new version of the application should be deployed only to new EC2 instances.

Which deployment methods will meet these requirements? (Select TWO.)

- ▫️ Rolling
- ✅ Blue/green
- ▫️ Rolling with additional batch
- ✅ Immutable
- ▫️ All at once

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.

All at once:

• Deploys the new version to all instances simultaneously.

Rolling:

• Update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy (downtime for 1 bucket at a time).

Rolling with additional batch:

• Like Rolling but launches new instances in a batch ensuring that there is full availability.

Immutable:

• Launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy.

• Zero downtime.

Blue / Green deployment:

• Zero downtime and release facility.

• Create a new “stage” environment and deploy updates there.

The immutable and blue/green options both provide zero downtime as they will deploy the new version to a new version of the application. These are also the only two options that will ONLY deploy the updates to new EC2 instances.

CORRECT: "Immutable" is the correct answer.

CORRECT: "Blue/green" is the correct answer.

INCORRECT: "All-at-once" is incorrect as this will deploy the updates to existing instances.

INCORRECT: "Rolling" is incorrect as this will deploy the updates to existing instances.

INCORRECT: "Rolling with additional batch" is incorrect as this will launch new instances but will also update the existing instances as well (which is not allowed according to the requirements).

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html>

</details>

---

<a id="t05-q47"></a>
### T05-Q47
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: без втрати потужності, найдешевше → Rolling with additional batch.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A Developer has completed some code updates and needs to deploy the updates to an Amazon Elastic Beanstalk environment. The environment includes twelve Amazon EC2 instances and there can be no reduction in application performance and availability during the update.

Which deployment policy is the most cost-effective choice to suit these requirements?

- ▫️ Immutable
- ▫️ Rolling
- ▫️ All at once
- ✅ Rolling with additional batch

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.

Each deployment policy has advantages and disadvantages and it’s important to select the best policy to use for each situation. The following tables summarizes the different deployment policies:

The “rolling with additional batch” policy will add an additional batch of instances, updates those instances, then move onto the next batch.

Rolling with additional batch:

Like Rolling but launches new instances in a batch ensuring that there is full availability.

Application is running at capacity.

Can set the bucket size.

Application is running both versions simultaneously.

Small additional cost.

Additional batch is removed at the end of the deployment.

Longer deployment.

Good for production environments.

For this scenario there can be no reduction in application performance and availability during the update. The question also asks for the most cost-effective choice.

Therefore, the “rolling with additional batch” is the best choice as it will ensure fully availability of the application but minimize cost as the additional batch size can be kept small.

CORRECT: "Rolling with additional batch" is the correct answer.

INCORRECT: "Rolling" is incorrect as this will result in a reduction in capacity as there is no additional batch of instances introduced to the environment. This is a better choice if speed is required and a reduction in capacity of a batch size is acceptable.

INCORRECT: "All at once" is incorrect as this will take the application down and cause a complete outage of the application during the update.

INCORRECT: "Immutable" is incorrect as this is the most expensive option as it doubles capacity with a whole new set of instances attached to a new ASG.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t06-q49"></a>
### T06-Q49
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: без downtime + легкий rollback → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A company has a production application deployed using AWS Elastic Beanstalk. A new version of the application must be installed, and the company cannot tolerate any website downtime. If the application update fails, rollback should be fast and easy.

What deployment method should be used?

- ▫️ Incremental
- ▫️ Rolling
- ✅ Immutable
- ❌ All at once

<details><summary>Пояснення (оригінал, EN)</summary>

The immutable deployment type launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy. There is zero downtime and a quick rollback in case of failures.

CORRECT: "Immutable" is the correct answer (as explained above.)

INCORRECT: "Rolling" is incorrect.

With a rolling update a few instances are updated at a time (batch), and then the deployment moves onto the next batch once the first batch is healthy. Each batch is taken out of service during deployment leading to downtime. If the update fails, you need to perform an additional rolling update to roll back the changes. This would not be ideal for this production environment.

INCORRECT: "All at once" is incorrect.

This would take all instances down at the same time. This is not suitable for a production environment.

INCORRECT: "Incremental" is incorrect.

This is not actually a type of deployment model in Elastic Beanstalk.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environmentmgmt-updates-immutable.html>

</details>

---

<a id="t08-q27"></a>
### T08-Q27
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: пріоритет rollback → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A Developer has completed some code updates and needs to deploy the updates to an Amazon Elastic Beanstalk environment. Due to the criticality of the application, the ability to quickly roll back must be prioritized of any other considerations.

Which deployment policy should the Developer choose?

- ▫️ Rolling with additional batch
- ✅ Immutable
- ▫️ All at once
- ▫️ Rolling

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.

Each deployment policy has advantages and disadvantages and it’s important to select the best policy to use for each situation. The following tables summarizes the different deployment policies:

The “immutable” policy will create a new ASG with a whole new set of instances and deploy the updates there.

Immutable:

• Launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy.

• Zero downtime.

• New code is deployed to new instances using an ASG.

• High cost as double the number of instances running during updates.

• Longest deployment.

• Quick rollback in case of failures.

• Great for production environments.

For this scenario a quick rollback must be prioritized over all other considerations. Therefore, the best choice is “immutable”. This deployment policy is the most expensive and longest (duration) option. However, you can roll back quickly and safely as the original instances are all available and unmodified.

CORRECT: "Immutable" is the correct answer.

INCORRECT: "Rolling" is incorrect as this policy requires manual redeployment if there are any issues caused by the update.

INCORRECT: "Rolling with additional batch" is incorrect as this policy requires manual redeployment if there are any issues caused by the update.

INCORRECT: "All at once" is incorrect as this takes the entire environment down at once and requires manual redeployment if there are any issues caused by the update.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t08-q33"></a>
### T08-Q33
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to configure an Elastic Load Balancer that is deployed through AWS Elastic Beanstalk. Where should the Developer place the load-balancer.config file in the application source bundle?

- ▫️ In the bin folder
- ▫️ In the root of the source code
- ✅ In the .ebextensions folder
- ▫️ In the load-balancer.config.root

<details><summary>Пояснення (оригінал, EN)</summary>

You can add AWS Elastic Beanstalk configuration files (.ebextensions) to your web application's source code to configure your environment and customize the AWS resources that it contains.

Configuration files are YAML- or JSON-formatted documents with a .config file extension that you place in a folder named .ebextensions and deploy in your application source bundle.

For example, you could include a configuration file for setting the load balancer type into:

.ebextensions/load-balancer.config

This example makes a simple configuration change. It modifies a configuration option to set the type of your environment's load balancer to Network Load Balancer:

Requirements

•  Location – Place all of your configuration files in a single folder, named .ebextensions, in the root of your source bundle. Folders starting with a dot can be hidden by file browsers, so make sure that the folder is added when you create your source bundle.

•  Naming – Configuration files must have the .config file extension.

•  Formatting – Configuration files must conform to YAML or JSON specifications.

•  Uniqueness – Use each key only once in each configuration file.

Therefore, the Developer should place the file in the .ebextensions folder in the application source bundle.

CORRECT: "In the .ebextensions folder" is the correct answer.

INCORRECT: "In the root of the source code" is incorrect. You need to place .config files in the .ebextensions folder.

INCORRECT: "In the bin folder" is incorrect. You need to place .config files in the .ebextensions folder.

INCORRECT: "In the load-balancer.config.root" is incorrect. You need to place .config files in the .ebextensions folder.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html>

</details>

---

<a id="t09-q09"></a>
### T09-Q09
*⏭ пропущено · домен: офіційна мітка*

You are a developer working on a web application written in Java and would like to use AWS Elastic Beanstalk for deployment because it would handle deployment, capacity provisioning, load balancing, auto-scaling, and application health monitoring. In the past, you connected to your provisioned instances through SSH to issue configuration commands. Now, you would like a configuration mechanism that automatically applies settings for you.

Which of the following options would help do this?

- ✅ Include config files in .ebextensions/ at the root of your source code
- ▫️ Use an AWS Lambda hook
- ▫️ Deploy a CloudFormation wrapper
- ▫️ Use SSM parameter store as an input to your Elastic Beanstalk Configurations

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Include config files in .ebextensions/ at the root of your source code

The option_settings section of a configuration file defines values for configuration options. Configuration options let you configure your Elastic Beanstalk environment, the AWS resources in it, and the software that runs your application. Configuration files are only one of several ways to set configuration options.

 via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html

Incorrect options:

Deploy a CloudFormation wrapper - This is a made-up option. This has been added as a distractor.

Use SSM parameter store as an input to your Elastic Beanstalk Configurations - SSM parameter is still not supported for Elastic Beanstalk. So this option is incorrect.

Use an AWS Lambda hook - Lambda functions are not the best-fit to trigger these configuration changes as it would involve significant development effort.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions-optionsettings.html>

</details>

---

<a id="t10-q11"></a>
### T10-Q11
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: зміна платформи → Blue/Green (CNAME swap).** → [детальніше](../../06-contradictions-and-traps.md#c07)

A media company uses Amazon EC2 instances managed by AWS Elastic Beanstalk to run its high-traffic website. The engineering team needs to introduce a new feature, which requires upgrading the underlying platform to a newer version of Node.js. The deployment of the new code and the platform upgrade need to happen without causing any downtime.

Which strategy should the team adopt to fulfill these requirements?

- ▫️ Create a duplicate EC2 instance manually with the new version of Node.js and the updated code. Test the new instance and replace one of the instances behind the ELB once the testing is successful.
- ▫️ Use AWS CodeDeploy to deploy the new application code first, then manually update Node.js on the Elastic Beanstalk environment.
- ✅ Implement Blue/Green (CNAME Swap) deployment using Elastic Beanstalk. Prepare a separate environment with the new version of Node.js and the new code, and once testing is complete, swap the CNAMEs of the two environments.
- ▫️ Upgrade the platform version of the current Elastic Beanstalk environment, then deploy the new application code. Monitor the application and quickly roll back changes if any issues occur.

<details><summary>Пояснення (оригінал, EN)</summary>

The Blue/Green deployment strategy involves creating a separate environment (the 'green' environment) with the new application version and platform updates. It allows full testing and validation. Once you're confident in the 'green' environment, you can swap the environment URLs to redirect traffic to the new environment. This results in zero downtime.

CORRECT: "Implement Blue/Green (CNAME Swap) deployment using Elastic Beanstalk. Prepare a separate environment with the new version of Node.js and the new code, and once testing is complete, swap the CNAMEs of the two environments" is the correct answer (as explained above.)

INCORRECT: "Upgrade the platform version of the current Elastic Beanstalk environment, then deploy the new application code. Monitor the application and quickly roll back changes if any issues occur" is incorrect.

This approach introduces risks that could result in downtime during the upgrade and deployment process. If any issues occur during the upgrade or after the deployment of the new application code, rolling back these changes could cause further downtime.

INCORRECT: "Create a duplicate EC2 instance manually with the new version of Node.js and the updated code. Test the new instance and replace one of the instances behind the ELB once the testing is successful" is incorrect.

Managing the process manually and replacing instances behind the load balancer may result in inconsistencies and is not as streamlined or reliable as using the deployment and environment management capabilities of Elastic Beanstalk.

INCORRECT: "Use a rolling deployment for the new application code. Apply the code to a subset of EC2 instances until the tests pass. Redeploy the previous code if the tests fail" is incorrect.

While AWS CodeDeploy is a robust deployment service, performing the platform upgrade manually after the code deployment doesn't guarantee zero downtime, as the platform upgrade might require a restart of the EC2 instances, causing service interruptions.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.CNAMESwap.html>

</details>

---

<a id="t11-q15"></a>
### T11-Q15
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EB: Rolling with additional batch.** → [детальніше](../../06-contradictions-and-traps.md#c07)

As an AWS Certified Developer Associate, you have been asked to create an AWS Elastic Beanstalk environment to handle deployment for an application that has high traffic and high availability needs. You need to deploy the new version using Beanstalk while making sure that performance and availability are not affected.

Which of the following is the MOST optimal way to do this while keeping the solution cost-effective?

- ▫️ Deploy using 'All at once' deployment policy
- ▫️ Deploy using 'Rolling' deployment policy
- ✅ Deploy using 'Rolling with additional batch' deployment policy
- ▫️ Deploy using 'Immutable' deployment policy

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Elastic Beanstalk offers several deployment policies and settings. Choosing the right deployment policy for your application is a tradeoff based on a few considerations and depends on your business needs.

Deploy using 'Rolling with additional batch' deployment policy - With this method, Elastic Beanstalk launches an extra batch of instances, then performs a rolling deployment. Launching the extra batch takes time, and ensures that the same bandwidth is retained throughout the deployment. This policy also avoids any reduced availability, although at a cost of an even longer deployment time compared to the Rolling method. Finally, this option is suitable if you must maintain the same bandwidth throughout the deployment.

Overview of Elastic Beanstalk Deployment Policies:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

Incorrect options:

Deploy using 'Immutable' deployment policy - A slower deployment method, that ensures your new application version is always deployed to new instances, instead of updating existing instances. It also has the additional advantage of a quick and safe rollback in case the deployment fails. With this method, Elastic Beanstalk performs an immutable update to deploy your application. In an immutable update, a second Auto Scaling group is launched in your environment and the new version serves traffic alongside the old version until the new instances pass health checks.

Deploy using 'All at once' deployment policy - This is the quickest deployment method. Suitable if you can accept a short loss of service, and if quick deployments are important to you. With this method, Elastic Beanstalk deploys the new application version to each instance. Then, the web proxy or application server might need to restart. As a result, your application might be unavailable to users (or have low availability) for a short time.

Deploy using 'Rolling' deployment policy - With this method, your application is deployed to your environment one batch of instances at a time. Most bandwidth is retained throughout the deployment. Avoids downtime and minimizes reduced availability, at a cost of a longer deployment time. Suitable if you can't accept any period of completely lost service. The use case states that the application has high traffic and high availability requirements, so full capacity must be maintained during deployments, hence rolling with additional batch deployment is a better fit than the rolling deployment.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

</details>

---

<a id="t11-q23"></a>
### T11-Q23
*⏭ пропущено · домен: офіційна мітка*

When running a Rolling deployment in Elastic Beanstalk environment, only two batches completed the deployment successfully, while rest of the batches failed to deploy the updated version. Following this, the development team terminated the instances from the failed deployment.

What will be the status of these failed instances post termination?

- ✅ Elastic Beanstalk will replace the failed instances with instances running the application version from the most recent successful deployment
- ▫️ Elastic Beanstalk will not replace the failed instances
- ▫️ Elastic Beanstalk will replace the failed instances after the application version to be installed is manually chosen from AWS Console
- ▫️ Elastic Beanstalk will replace the failed instances with instances running the application version from the oldest successful deployment

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Elastic Beanstalk will replace them with instances running the application version from the most recent successful deployment

When processing a batch, Elastic Beanstalk detaches all instances in the batch from the load balancer, deploys the new application version, and then reattaches the instances. If you enable connection draining, Elastic Beanstalk drains existing connections from the Amazon EC2 instances in each batch before beginning the deployment.

If a deployment fails after one or more batches completed successfully, the completed batches run the new version of your application while any pending batches continue to run the old version. You can identify the version running on the instances in your environment on the health page in the console. This page displays the deployment ID of the most recent deployment that was executed on each instance in your environment. If you terminate instances from the failed deployment, Elastic Beanstalk replaces them with instances running the application version from the most recent successful deployment.

Incorrect options:

Elastic Beanstalk will not replace the failed instances

Elastic Beanstalk will replace the failed instances with instances running the application version from the oldest successful deployment

Elastic Beanstalk will replace the failed instances after the application version to be installed is manually chosen from AWS Console

These three options contradict the explanation provided above, so these options are incorrect.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html

</details>

---

<a id="t11-q33"></a>
### T11-Q33
*⏭ пропущено · домен: офіційна мітка*

After a test deployment in ElasticBeanstalk environment, a developer noticed that all accumulated Amazon EC2 burst balances were lost.

Which of the following options can lead to this behavior?

- ▫️ The deployment was run as a Rolling deployment, resulting in the resetting of EC2 burst balances
- ✅ The deployment was either run with immutable updates or in traffic splitting mode
- ▫️ The deployment was run as a All-at-once deployment, flushing all the accumulated EC2 burst balances
- ▫️ When a canary deployment fails, it resets the EC2 burst balances to zero

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The deployment was either run with immutable updates or in traffic splitting mode - Immutable deployments perform an immutable update to launch a full set of new instances running the new version of the application in a separate Auto Scaling group, alongside the instances running the old version. Immutable deployments can prevent issues caused by partially completed rolling deployments.

Traffic-splitting deployments let you perform canary testing as part of your application deployment. In a traffic-splitting deployment, Elastic Beanstalk launches a full set of new instances just like during an immutable deployment. It then forwards a specified percentage of incoming client traffic to the new application version for a specified evaluation period.

Some policies replace all instances during the deployment or update. This causes all accumulated Amazon EC2 burst balances to be lost. It happens in the following cases:

Managed platform updates with instance replacement enabled
Immutable updates
Deployments with immutable updates or traffic splitting enabled
Incorrect options:

The deployment was run as a Rolling deployment, resulting in the resetting of EC2 burst balances - With rolling deployments, Elastic Beanstalk splits the environment's Amazon EC2 instances into batches and deploys the new version of the application to one batch at a time. Rolling deployments do not result in loss of EC2 burst balances.

The deployment was run as a All-at-once deployment, flushing all the accumulated EC2 burst balances - The traditional All-at-once deployment, wherein all the instances are updated simultaneously, does not result in loss of EC2 burst balances.

When a canary deployment fails, it resets the EC2 burst balances to zero - This is incorrect and given only as a distractor.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html

</details>

---

<a id="t11-q47"></a>
### T11-Q47
*⏭ пропущено · домен: офіційна мітка*

A developer has been asked to create a web application to be deployed on EC2 instances. The developer just wants to focus on writing application code without worrying about server provisioning, configuration and deployment.

As a Developer Associate, which AWS service would you recommend for the given use-case?

- ▫️ Serverless Application Model
- ▫️ CodeDeploy
- ▫️ CloudFormation
- ✅ Elastic Beanstalk

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Elastic Beanstalk

AWS Elastic Beanstalk provides an environment to easily deploy and run applications in the cloud. It is integrated with developer tools and provides a one-stop experience for you to manage the lifecycle of your applications.

AWS Elastic Beanstalk lets you manage all of the resources that run your application as environments where each environment runs only a single application version at a time. When an environment is being created, Elastic Beanstalk provisions all the required resources needed to run the application version. You don't need to worry about server provisioning, configuration, and deployment as that's taken care of by Beanstalk.

Benefits of Elastic Beanstalk:  via - https://aws.amazon.com/elasticbeanstalk/

Incorrect options:

CloudFormation - AWS CloudFormation is a service that gives developers and businesses an easy way to create a collection of related AWS and third-party resources and provision them in an orderly and predictable fashion. With CloudFormation, you still need to create a template to specify the type of resources you need, hence this option is not correct.

How CloudFormation Works:  via - https://aws.amazon.com/cloudformation/

CodeDeploy - AWS CodeDeploy is a fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications. It can deploy an application to an instance but it cannot provision the instance.

Serverless Application Model - The AWS Serverless Application Model (AWS SAM) is an open-source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. You define the application you want with just a few lines per resource and model it using YAML. As the web application needs to be deployed on EC2 instances, so this option is ruled out.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/concepts.html> · <https://aws.amazon.com/cloudformation/>

</details>

---

<a id="t11-q57"></a>
### T11-Q57
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EB: без додаткових витрат → Rolling.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A company uses Elastic Beanstalk to manage its IT infrastructure on AWS Cloud and it would like to deploy the new application version to the EC2 instances. When the deployment is executed, some instances should serve requests with the old application version, while other instances should serve requests using the new application version until the deployment is completed.

Which deployment meets this requirement without incurring additional costs?

- ▫️ All at once
- ▫️ Rolling with additional batches
- ✅ Rolling
- ▫️ Immutable

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Rolling

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.

How Elastic BeanStalk Works:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html

The rolling deployment policy deploys the new version in batches. Each batch is taken out of service during the deployment phase, reducing your environment's capacity by the number of instances in a batch. The cost remains the same as the number of EC2 instances does not increase. This policy avoids downtime and minimizes reduced availability, at a cost of a longer deployment time.

Overview of Elastic Beanstalk Deployment Policies:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

Incorrect options:

Immutable - The 'Immutable' deployment policy ensures that your new application version is always deployed to new instances, instead of updating existing instances. It also has the additional advantage of a quick and safe rollback in case the deployment fails.

All at once - This policy deploys the new version to all instances simultaneously. Although 'All at once' is the quickest deployment method, but the application may become unavailable to users (or have low availability) for a short time.

Rolling with additional batches - This policy deploys the new version in batches, but first launches a new batch of instances to ensure full capacity during the deployment process. This policy avoids any reduced availability, at a cost of an even longer deployment time compared to the Rolling method. Suitable if you must maintain the same bandwidth throughout the deployment. These increase the costs as you're adding extra instances during the deployment.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

</details>

---

<a id="t12-q19"></a>
### T12-Q19
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EB: Blue/Green.** → [детальніше](../../06-contradictions-and-traps.md#c07)

A retail company manages its IT infrastructure on AWS Cloud via Elastic Beanstalk. The development team at the company is planning to deploy the next version with MINIMUM application downtime and the ability to rollback quickly in case deployment goes wrong.

As a Developer Associate, which of the following options would you recommend to the development team?

- ▫️ Deploy the new application version using 'All at once' deployment policy
- ▫️ Deploy the new application version using 'Rolling' deployment policy
- ▫️ Deploy the new application version using 'Rolling with additional batch' deployment policy
- ✅ Deploy the new version to a separate environment via Blue/Green Deployment, and then swap Route 53 records of the two environments to redirect traffic to the new version

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Deploy the new version to a separate environment via Blue/Green Deployment, and then swap Route 53 records of the two environments to redirect traffic to the new version

With deployment policies such as 'All at once', AWS Elastic Beanstalk performs an in-place update when you update your application versions and your application can become unavailable to users for a short period of time. You can avoid this downtime by performing a blue/green deployment, where you deploy the new version to a separate environment, and then swap CNAMEs (via Route 53) of the two environments to redirect traffic to the new version instantly. In case of any deployment issues, the rollback process is very quick via swapping the URLs for the two environments.

Overview of Elastic Beanstalk Deployment Policies:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

Incorrect options:

Deploy the new application version using 'All at once' deployment policy - Although 'All at once' is the quickest deployment method, but the application may become unavailable to users (or have low availability) for a short time. So this option is not correct.

Deploy the new application version using 'Rolling' deployment policy - This policy avoids downtime and minimizes reduced availability, at a cost of a longer deployment time. However rollback process is via manual redeploy, so it's not as quick as the Blue/Green deployment.

Deploy the new application version using 'Rolling with additional batch' deployment policy - This policy avoids any reduced availability, at a cost of an even longer deployment time compared to the Rolling method. Suitable if you must maintain the same bandwidth throughout the deployment. However rollback process is via manual redeploy, so it's not as quick as the Blue/Green deployment.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

</details>

---
