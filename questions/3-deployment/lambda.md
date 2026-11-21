# AWS Lambda

**Домен 3 — Розгортання** · питань: 10  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q21"></a>
### T01-Q21
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **API Gateway canary.** → [детальніше](../../06-contradictions-and-traps.md#c13)

You are a developer working on AWS Lambda functions that are triggered by Amazon API Gateway and would like to perform testing on a low volume of traffic for new API versions.

Which of the following features will accomplish this task?

- ✅ Canary Deployment
- ▫️ Custom Authorizers
- ▫️ Stage Variables
- ▫️ Mapping Templates

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Canary Deployment

In a canary release deployment, total API traffic is separated at random into a production release and a canary release with a preconfigured ratio. Typically, the canary release receives a small percentage of API traffic and the production release takes up the rest. The updated API features are only visible to API traffic through the canary. You can adjust the canary traffic percentage to optimize test coverage or performance.

 via - https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html

Incorrect options:

Stage Variables - They act like environment variables and can be used in your API setup.

Mapping Templates - Its a script to map the payload from a method request to the corresponding integration request and also maps the integration response to the corresponding method response.

Custom Authorizers - Used for authentication purposes and must return AWS Identity and Access Management (IAM) policies.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html

</details>

---

<a id="t02-q45"></a>
### T02-Q45
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **"CloudWatch Events" = EventBridge.** → [детальніше](../../06-contradictions-and-traps.md#c14)

A company has implemented AWS CodePipeline to automate its release pipelines. The Development team is writing an AWS Lambda function that will send notifications for state changes of each of the actions in the stages.

Which steps must be taken to associate the Lambda function with the event source?

- ▫️ Create an Amazon CloudWatch alarm that monitors status changes in CodePipeline and triggers the Lambda function
- ❌ Create an event trigger and specify the Lambda function from the CodePipeline console
- ▫️ Create a trigger that invokes the Lambda function from the Lambda console by selecting CodePipeline as the event source
- ✅ Create an Amazon CloudWatch Events rule that uses CodePipeline as an event source

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon CloudWatch Events help you to respond to state changes in your AWS resources. When your resources change state, they automatically send events into an event stream. You can create rules that match selected events in the stream and route them to your AWS Lambda function to take action.

AWS CodePipeline can be configured as an event source in CloudWatch Events and can then send notifications using as service such as Amazon SNS.

Therefore, the best answer is to create an Amazon CloudWatch Events rule that uses CodePipeline as an event source.

CORRECT: "Create an Amazon CloudWatch Events rule that uses CodePipeline as an event source" is the correct answer.

INCORRECT: "Create a trigger that invokes the Lambda function from the Lambda console by selecting CodePipeline as the event source" is incorrect as CodePipeline cannot be configured as a trigger for Lambda.

INCORRECT: "Create an event trigger and specify the Lambda function from the CodePipeline console" is incorrect as CodePipeline cannot be configured as a trigger for Lambda.

INCORRECT: "Create an Amazon CloudWatch alarm that monitors status changes in CodePipeline and triggers the Lambda function" is incorrect as CloudWatch Events is used for monitoring state changes.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html>

</details>

---

<a id="t03-q13"></a>
### T03-Q13
*⏭ пропущено · домен: офіційна мітка*

A developer wants a seamless ability to return to older versions of a Lambda function that is being deployed.

Which of the following solutions offers the LEAST operational overhead?

- ✅ Use a Lambda function alias that can point to the different versions
- ▫️ Use Lambda function layers that can point to the different versions
- ▫️ Use a Route 53 weighted policy that can point to the different Lambda function versions
- ▫️ Use CodeDeploy to configure blue/green deployments for the different Lambda function versions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use a Lambda function alias that can point to the different versions

You can use versions to manage the deployment of your functions. For example, you can publish a new version of a function for beta testing without affecting users of the stable production version. Lambda creates a new version of your function each time that you publish the function. The new version is a copy of the unpublished version of the function.

By publishing a version of your function, you can store your code and configuration as a separate resource that cannot be changed.

A Lambda alias is like a pointer to a specific function version. Users can access the function version using the alias Amazon Resource Name (ARN). Each alias has a unique ARN. An alias can point only to a function version, not to another alias. You can update an alias to point to the different versions of the Lambda function.

Incorrect options:

Use a Route 53 weighted policy that can point to the different Lambda function versions - This option is a distractor, as Route 53 cannot be used for the given use case. Route 53 weighted policy lets you associate multiple resources with a single domain name (example.com) or subdomain name (acme.example.com) and choose how much traffic is routed to each resource. This can be useful for a variety of purposes, including load balancing and testing new versions of software.

Use CodeDeploy to configure blue/green deployments for the different Lambda function versions - A deployment to the AWS Lambda compute platform is always a blue/green deployment. You do not specify a deployment type option. When you deploy to an AWS Lambda compute platform, the deployment configuration specifies the way traffic is shifted to the new Lambda function versions in your application. You can shift traffic using a canary, linear, or all-at-once deployment configuration. Once deployed, you cannot go back to the previous versions of your Lambda function. So this option is incorrect.

Use Lambda function layers that can point to the different versions - Lambda layers provide a convenient way to package libraries and other dependencies that you can use with your Lambda functions. Using layers reduces the size of uploaded deployment archives and makes it faster to deploy your code. You cannot use the Lambda function layers to point to the different versions of the Lambda function.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html> · <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html#deployment-configuration-lambda> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---

<a id="t04-q11"></a>
### T04-Q11
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A serverless application composed of multiple Lambda functions has been deployed. A developer is setting up AWS CodeDeploy to manage the deployment of code updates. The developer would like a 10% of the traffic to be shifted to the new version in equal increments, 10 minutes apart.

Which setting should be chosen for configuring how traffic is shifted?

- ▫️ All-at-once
- ▫️ Blue/green
- ▫️ Canary
- ✅ Linear

<details><summary>Пояснення (оригінал, EN)</summary>

A deployment configuration is a set of rules and success and failure conditions used by CodeDeploy during a deployment. These rules and conditions are different, depending on whether you deploy to an EC2/On-Premises compute platform or an AWS Lambda compute platform.

The following table lists the predefined configurations available for AWS Lambda deployments.

As you can see from the table above, the linear option shifts a specific amount of traffic in equal increments of time. Therefore, the following option should be chosen:

CodeDeployDefault.LambdaLinear10PercentEvery10Minutes

CORRECT: "Linear" is the correct answer.

INCORRECT: "Canary" is incorrect as it does not shift traffic in equal increments.

INCORRECT: "All-at-once" is incorrect as it shifts all traffic at once.

INCORRECT: "Blue/green" is incorrect as it is a type of deployment, not a setting for traffic shifting.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html>

</details>

---

<a id="t04-q52"></a>
### T04-Q52
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Lambda alias 10%.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A Developer wants the ability to roll back to a previous version of an AWS Lambda function in the event of errors caused by a new deployment.

How can the Developer achieve this with MINIMAL impact on users?

- ▫️ Change the application to use the $LATEST version. Update and save code. If too many errors are encountered, modify and save the code
- ✅ Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to direct 10% of users to the newly deployed version. If too many errors are encountered, send 100% of traffic to the previous version
- ▫️ Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to use the newly deployed version. If too many errors are encountered, point the alias back to the previous version
- ▫️ Change the application to use a version ARN that points to the latest published version. Deploy the new version of the code. Update the application to point to the ARN of the new version of the code. If too many errors are encountered, point the application back to the ARN of the previous version

<details><summary>Пояснення (оригінал, EN)</summary>

You can create one or more aliases for your AWS Lambda function. A Lambda alias is like a pointer to a specific Lambda function version. Users can access the function version using the alias ARN.

You can update the versions that an alias points to and you can also add multiple versions and use weightings to direct a percentage of traffic to a new version of the code.

For this example the best choice is to use an alias and direct 10% of traffic to the new version. If errors are encountered the rollback is easy (change the pointer in the alias) and a minimum of impact has been made to users.

CORRECT: "Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to direct 10% of users to the newly deployed version. If too many errors are encountered, send 100% of traffic to the previous version" is the correct answer.

INCORRECT: "Change the application to use an alias that points to the current version. Deploy the new version of the code. Update the alias to use the newly deployed version. If too many errors are encountered, point the alias back to the previous version" is incorrect. This is not the best answer as 100% of the users will be directed to the new version so if any errors do occur more users will be affected.

INCORRECT: "Change the application to use a version ARN that points to the latest published version. Deploy the new version of the code. Update the application to point to the ARN of the new version of the code. If too many errors are encountered, point the application back to the ARN of the previous version" is incorrect. This answer involves a lot of updates to the application that could be completely avoided by using an alias.

INCORRECT: "Change the application to use the $LATEST version. Update and save code. If too many errors are encountered, modify and save the code" is incorrect as this is against best practice. The $LATEST is the unpublished version of the code where you make changes. You should publish to a version when the code is ready for deployment.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html>

</details>

---

<a id="t06-q03"></a>
### T06-Q03
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Aliases + versions.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A developer is creating a serverless web application that includes AWS Lambda functions and a REST API deployed using Amazon API Gateway. The developer maintains multiple branches of code. The developer wants to avoid updating the API gateway target endpoint when a new code push is performed.

What solution would allow the developer to update the Lambda code without needing to update the REST API configuration?

- ▫️ Create multiple private API endpoints and use CNAMEs.
- ▫️ Create different tags for each Lambda function.
- ✅ Create aliases and versions in AWS Lambda.
- ▫️ Create multiple stages and deployments.

<details><summary>Пояснення (оригінал, EN)</summary>

You can create one or more aliases for your Lambda function. A Lambda alias is like a pointer to a specific function version. Users can access the function version using the alias Amazon Resource Name (ARN). You can then release new versions of your code without needing to change the alias that applications use to invoke the function.

In this case the REST API could be configured with aliases for the functions. The developer could also use different stages with different endpoints using the aliases. This would enable calling different versions of the application by changing the stage name in the REST API URL.

CORRECT: "Create aliases and versions in AWS Lambda" is the correct answer (as explained above.)

INCORRECT: "Create multiple stages and deployments" is incorrect.

Stages and stage variables could be used to reference different functions or aliases. But if only stages and deployments are used (and not a Lambda alias) then the REST API would need to have the endpoint updated every time a new function version is released.

INCORRECT: "Create different tags for each Lambda function" is incorrect.

Tags cannot be used to define the Lambda endpoints in the REST API.

INCORRECT: "Create multiple private API endpoints and use CNAMEs" is incorrect.

There is no value here in creating multiple private API endpoints as that would be completely different APIs.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html>

</details>

---

<a id="t08-q13"></a>
### T08-Q13
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Lambda alias + traffic shifting.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A Developer has updated an AWS Lambda function and published a new version. To ensure the code is working as expected the Developer needs to initially direct a percentage of traffic to the new version and gradually increase this over time. It is important to be able to rollback if there are any issues reported.

What is the BEST way the Developer can implement the migration to the new version SAFELY?

- ▫️ Use an immutable update with a new ASG to deploy the new version in parallel, following testing cutover to the new version
- ▫️ Use an Amazon Elastic Load Balancer to direct a percentage of traffic to each target group containing the Lambda function versions
- ✅ Create an Alias, assign the current and new versions and use traffic shifting to assign a percentage of traffic to the new version
- ▫️ Create an Amazon Route 53 weighted routing policy pointing to the current and new versions, assign a lower weight to the new version

<details><summary>Пояснення (оригінал, EN)</summary>

You can create one or more aliases for your AWS Lambda function. A Lambda alias is like a pointer to a specific Lambda function version. Users can access the function version using the alias ARN.

Each alias has a unique ARN. An alias can only point to a function version, not to another alias. You can update an alias to point to a new version of the function. You can also use traffic shifting to direct a percentage of traffic to a specific version as showing in the image below:

This is the recommended way to direct traffic to multiple function versions and shift traffic when testing code updated. Therefore, the best answer is to create an Alias, assign the current and new versions and use traffic shifting to assign a percentage of traffic to the new version.

CORRECT: "Create an Alias, assign the current and new versions and use traffic shifting to assign a percentage of traffic to the new version" is the correct answer.

INCORRECT: "Create an Amazon Route 53 weighted routing policy pointing to the current and new versions, assign a lower weight to the new version" is incorrect. AWS Lambda endpoints are not DNS names that you can route to with Route 53. The best way to route traffic to multiple versions is using an alias.

INCORRECT: "Use an immutable update with a new ASG to deploy the new version in parallel, following testing cutover to the new version" is incorrect as immutable updates are associated with Amazon Elastic Beanstalk and this service does not deploy updates to AWS Lambda.

INCORRECT: "Use an Amazon Elastic Load Balancer to direct a percentage of traffic to each target group containing the Lambda function versions" is incorrect as this introduces an unnecessary layer (complexity and cost) to the architecture. The best choice is to use an alias instead.

Джерела: <https://docs.amazonaws.cn/en_us/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/aliases-intro.html> · <https://docs.aws.amazon.com/lambda/latest/dg/lambda-traffic-shifting-using-aliases.html>

</details>

---

<a id="t08-q65"></a>
### T08-Q65
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Developer is deploying an update to a serverless application that includes AWS Lambda using the AWS Serverless Application Model (SAM). The traffic needs to move from the old Lambda version to the new Lambda version gradually, within the shortest period of time.

Which deployment configuration is MOST suitable for these requirements?

- ▫️ CodeDeployDefault.HalfAtATime
- ▫️ CodeDeployDefault.LambdaLinear10PercentEvery2Minutes
- ✅ CodeDeployDefault.LambdaCanary10Percent5Minutes
- ▫️ CodeDeployDefault.LambdaLinear10PercentEvery1Minute

<details><summary>Пояснення (оригінал, EN)</summary>

If you use AWS SAM to create your serverless application, it comes built-in with CodeDeploy to provide gradual Lambda deployments. With just a few lines of configuration, AWS SAM does the following for you:

• Deploys new versions of your Lambda function, and automatically creates aliases that point to the new version.

• Gradually shifts customer traffic to the new version until you're satisfied that it's working as expected, or you roll back the update.

• Defines pre-traffic and post-traffic test functions to verify that the newly deployed code is configured correctly and your application operates as expected.

• Rolls back the deployment if CloudWatch alarms are triggered.

There are several options for how CodeDeploy shifts traffic to the new Lambda version. You can choose from the following:

• Canary: Traffic is shifted in two increments. You can choose from predefined canary options. The options specify the percentage of traffic that's shifted to your updated Lambda function version in the first increment, and the interval, in minutes, before the remaining traffic is shifted in the second increment.

• Linear: Traffic is shifted in equal increments with an equal number of minutes between each increment. You can choose from predefined linear options that specify the percentage of traffic that's shifted in each increment and the number of minutes between each increment.

All-at-once: All traffic is shifted from the original Lambda function to the updated Lambda function version at once.

Therefore CodeDeployDefault.LambdaCanary10Percent5Minutes is the best answer as this will shift 10 percent of the traffic and then after 5 minutes shift the remainder of the traffic. The entire deployment will take 5 minutes to cut over.

CORRECT: "CodeDeployDefault.LambdaCanary10Percent5Minutes" is the correct answer.

INCORRECT: "CodeDeployDefault.HalfAtATime" is incorrect as this is a CodeDeploy traffic shifting strategy that is not applicable to AWS Lambda. You can use Half at a Time with EC2 and on-premises instances.

INCORRECT: "CodeDeployDefault.LambdaLinear10PercentEvery1Minute" is incorrect as this option will take longer. CodeDeploy will shift 10 percent every 1 minute and therefore the deployment time will be 10 minutes.

INCORRECT: "CodeDeployDefault.LambdaLinear10PercentEvery2Minutes" is incorrect as this option will take longer. CodeDeploy will shift 10 percent every 2 minutes and therefore the deployment time will be 20 minutes.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/automating-updates-to-serverless-apps.html>

</details>

---

<a id="t10-q13"></a>
### T10-Q13
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

> ⚠️ **Alias = статичний ARN.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A Development team are deploying an AWS Lambda function that will be used by a production application. The function code will be updated regularly, and new versions will be published. The development team do not want to modify application code to point to each new version.

How can the Development team setup a static ARN that will point to the latest published version?

- ▫️ Use an unqualified ARN
- ▫️ Publish a mutable version and point it to the $LATEST version
- ▫️ Setup a Route 53 Alias record that points to the published version
- ✅ Setup an Alias that will point to the latest version

<details><summary>Пояснення (оригінал, EN)</summary>

You can create one or more aliases for your AWS Lambda function. A Lambda alias is like a pointer to a specific Lambda function version. Users can access the function version using the alias ARN.

This is the best way to setup the Lambda function so you don’t need to modify the application code when a new version is published. Instead, the developer will simply need to update the Alias to point to the new version:

As you can see above you can also point to multiple versions and send a percentage of traffic to each. This is great for testing new code.

CORRECT: "Setup an Alias that will point to the latest version" is the correct answer.

INCORRECT: "Publish a mutable version and point it to the $LATEST version" is incorrect as all published versions are immutable (cannot be modified) and you cannot modify a published version to point to the $LATEST version.

INCORRECT: "Use an unqualified ARN" is incorrect as this is an ARN that does not have a version number which means it points to the $LATEST version, not to a published version (as published versions always have version numbers).

INCORRECT: "Setup a Route 53 Alias record that points to the published version" is incorrect as you cannot point a Route 53 Alias record to an AWS Lambda function.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html>

</details>

---

<a id="t10-q52"></a>
### T10-Q52
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **Окремий stage для beta.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A software organization has developed a new feature in its serverless application hosted on AWS. This feature involves an AWS Lambda function that gets invoked by an Amazon API Gateway API. Currently, the API uses a specific Lambda alias to invoke the Lambda function. The organization wants to roll out this new feature to a select group of users for beta testing without affecting the application's existing users.

What would be the most efficient approach to meet these requirements?

- ▫️ Use AWS CodeDeploy to deploy the updated Lambda function. Split traffic between the new and old versions of the function for testing purposes.
- ▫️ Implement Amazon S3 bucket versioning on the Lambda function code. For testing purposes, link the API Gateway to the new version of the code stored in the S3 bucket.
- ✅ Create a new version of the Lambda function. Build a new stage on API Gateway integrated with this new Lambda version. Utilize this new API Gateway stage for beta testing.
- ▫️ Use the AWS CLI to manually switch between the old and new versions of the Lambda function during testing periods.

<details><summary>Пояснення (оригінал, EN)</summary>

In AWS, Lambda versions are an essential part of the deployment process. Once a version is published, the code within that version cannot be changed. It is ideal for ensuring stability and reliability between deployments.

API Gateway Stages can be used to route traffic to different Lambda versions. Hence, creating a new API Gateway stage for beta testing that is linked to the new version of the Lambda function will ensure that existing users (using the old stage) are not impacted by the new feature.

CORRECT: "Create a new version of the Lambda function. Build a new stage on API Gateway integrated with this new Lambda version. Utilize this new API Gateway stage for beta testing" is the correct answer (as explained above.)

INCORRECT: "Implement Amazon S3 bucket versioning on the Lambda function code. For testing purposes, link the API Gateway to the new version of the code stored in the S3 bucket" is incorrect.

While Amazon S3 versioning allows for keeping multiple variants of an object in the same bucket, it doesn't interact directly with AWS Lambda or API Gateway. Thus, you cannot control API Gateway to invoke different versions of Lambda function code stored in an S3 bucket.

INCORRECT: "Use AWS CodeDeploy to deploy the updated Lambda function. Split traffic between the new and old versions of the function for testing purposes" is incorrect.

AWS CodeDeploy can deploy new versions of Lambda functions and shift traffic gradually but using it for testing purposes might risk exposing beta features to the regular users, potentially impacting their experience.

INCORRECT: "Use the AWS CLI to manually switch between the old and new versions of the Lambda function during testing periods" is incorrect.

This approach is operationally intensive, prone to human error, and may impact regular users. AWS provides better ways of managing Lambda versions and aliases, which should be used instead of manual operations.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html>

</details>

---
