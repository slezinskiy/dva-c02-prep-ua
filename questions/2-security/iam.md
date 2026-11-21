# IAM / STS / Organizations

**Домен 2 — Безпека** · питань: 39  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q10"></a>
### T01-Q10
*⏭ пропущено · домен: офіційна мітка*

When your company first created an AWS account, you began with a single sign-in principal called a root user account that had complete access to all AWS services and resources.

What should you do to adhere to best practices for using the root user account?

- ▫️ It should be accessible by 3 to 6 members of the IT team
- ✅ It should be accessible by one admin only after enabling Multi-factor authentication
- ▫️ It should be accessible using the access key id and secret access key
- ▫️ It should be accessible by no one, throw away the passwords after creating the account

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

It should be accessible by one admin only after enabling Multi-factor authentication

AWS Root Account Security Best Practices:  via - https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#lock-away-credentials

If you continue to use the root user credentials, we recommend that you follow the security best practice to enable multi-factor authentication (MFA) for your account. Because your root user can perform sensitive operations in your account, adding a layer of authentication helps you to better secure your account. Multiple types of MFA are available.

Incorrect options:

It should be accessible by 3 to 6 members of the IT team - Only the owner of the AWS account should have access to the root account credentials. You should create an IT group with admin permissions via IAM and then assign a few users to this group.

It should be accessible using the access key id and secret access key - AWS recommends that you should not use the access key id and secret access key for the AWS account root user.

It should be accessible by no one, throw away the passwords after creating the account - You will still need to store the password somewhere for your root account.

Reference:

https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#lock-away-credentials

</details>

---

<a id="t01-q29"></a>
### T01-Q29
*⏭ пропущено · домен: офіційна мітка*

Your company likes to operate multiple AWS accounts so that teams have their environments. Services deployed across these accounts interact with one another, and now there's a requirement to implement X-Ray traces across all your applications deployed on EC2 instances and AWS accounts.

As such, you would like to have a unified account to view all the traces. What should you in your X-Ray daemon set up to make this work? (Select two)

- ▫️ Create a user in the target unified account and generate access and secret keys
- ✅ Configure the X-Ray daemon to use an IAM instance role
- ▫️ Enable Cross Account collection in the X-Ray console
- ▫️ Configure the X-Ray daemon to use access and secret keys
- ✅ Create a role in the target unified account and allow roles in each sub-account to assume the role.

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

How X-Ray Works:  via - https://aws.amazon.com/xray/

Create a role in the target unified account and allow roles in each sub-account to assume the role

Configure the X-Ray daemon to use an IAM instance role

The X-Ray agent can assume a role to publish data into an account different from the one in which it is running. This enables you to publish data from various components of your application into a central account.

X-Ray can also track requests flowing through applications or services across multiple AWS Regions.

 via - https://aws.amazon.com/xray/faqs/

You can create the necessary configurations for cross-account access via this reference documentation - https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-configuration.html

Incorrect options:

Create a user in the target unified account and generate access and secret keys

Configure the X-Ray daemon to use access and secret keys

These two options combined together would work but wouldn't be a best-practice security-wise. Therefore these are not correct.

Enable Cross Account collection in the X-Ray console - This is a made-up option and has been added as a distractor.

Джерела: <https://aws.amazon.com/xray/faqs/> · <https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-configuration.html>

</details>

---

<a id="t02-q21"></a>
### T02-Q21
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **"Least privilege" + конкретна таблиця → customer managed policy з ARN.** → [детальніше](../../06-contradictions-and-traps.md#c05)

A team of Developers require read-only access to an Amazon DynamoDB table. The Developers have been added to a group. What should an administrator do to provide the team with access whilst following the principal of least privilege?

- ✅ Create a customer managed policy with read only access to DynamoDB and specify the ARN of the table for the “Resource” element. Attach the policy to the group
- ❌ Assign the AmazonDynamoDBReadOnlyAccess AWS managed policy to the group
- ▫️ Create a customer managed policy with read/write access to DynamoDB for all resources. Attach the policy to the group
- ▫️ Assign the AWSLambdaDynamoDBExecutionRole AWS managed policy to the group

<details><summary>Пояснення (оригінал, EN)</summary>

The key requirement is to provide read-only access to the team for a specific DynamoDB table. Therefore, the AWS managed policy cannot be used as it will provide access to all DynamoDB tables in the account which does not follow the principal of least privilege.

Therefore, a customer managed policy should be created that provides read-only access and specifies the ARN of the table. For instance, the resource element might include the following ARN:

arn:aws:dynamodb:us-west-1:515148227241:table/exampletable

This will lock down access to the specific DynamoDB table, following the principal of least privilege.

CORRECT: "Create a customer managed policy with read only access to DynamoDB and specify the ARN of the table for the “Resource” element. Attach the policy to the group" is the correct answer.

INCORRECT: "Assign the AmazonDynamoDBReadOnlyAccess AWS managed policy to the group" is incorrect as this will provide read-only access to all DynamoDB tables in the account.

INCORRECT: "Assign the AWSLambdaDynamoDBExecutionRole AWS managed policy to the group" is incorrect as this is a role used with AWS Lambda.

INCORRECT: "Create a customer managed policy with read/write access to DynamoDB for all resources. Attach the policy to the group" is incorrect as read-only access should be provided, not read/write.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t02-q31"></a>
### T02-Q31
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **EB: повна потужність + існуючі інстанси → Rolling with additional batch.** → [детальніше](../../06-contradictions-and-traps.md#c07)

An application deployed on AWS Elastic Beanstalk experienced increased error rates during deployments of new application versions, resulting in service degradation for users. The Development team believes that this is because of the reduction in capacity during the deployment steps. The team would like to change the deployment policy configuration of the environment to an option that maintains full capacity during deployment while using the existing instances.

Which deployment policy will meet these requirements while using the existing instances?

- ✅ Rolling with additional batch
- ❌ All at once
- ▫️ Rolling
- ▫️ Immutable

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.

All at once:

· Deploys the new version to all instances simultaneously.

Rolling:

· Update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy (downtime for 1 bucket at a time).

Rolling with additional batch:

· Like Rolling but launches new instances in a batch ensuring that there is full availability.

Immutable:

· Launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy.

· Zero downtime.

Blue / Green deployment:

· Zero downtime and release facility.

· Create a new “stage” environment and deploy updates there.

The rolling with additional batch launches a new batch to ensure capacity is not reduced and then updates the existing instances. Therefore, this is the best option to use for these requirements.

CORRECT: “Rolling with additional batch” is the correct answer.

INCORRECT: “Rolling” is incorrect as this will only use the existing instances without introducing an extra batch and therefore this will reduce the capacity of the application while the updates are taking place.

INCORRECT: “All at once” is incorrect as this will run the updates on all instances at the same time causing a total outage.

INCORRECT: “Immutable” is incorrect as this installs the updates on new instances, not existing instances.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html>

</details>

---

<a id="t03-q45"></a>
### T03-Q45
*⏭ пропущено · домен: офіційна мітка*

You are a manager for a tech company that has just hired a team of developers to work on the company's AWS infrastructure. All the developers are reporting to you that when using the AWS CLI to execute commands it fails with the following exception: You are not authorized to perform this operation. Encoded authorization failure message: 6h34GtpmGjJJUm946eDVBfzWQJk6z5GePbbGDs9Z2T8xZj9EZtEduSnTbmrR7pMqpJrVYJCew2m8YBZQf4HRWEtrpncANrZMsnzk.

Which of the following actions will help developers decode the message?

- ▫️ AWS Cognito Decoder
- ▫️ Use KMS decode-authorization-message
- ▫️ AWS IAM decode-authorization-message
- ✅ AWS STS decode-authorization-message

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS STS decode-authorization-message

Use decode-authorization-message to decode additional information about the authorization status of a request from an encoded message returned in response to an AWS request. If a user is not authorized to perform an action that was requested, the request returns a Client.UnauthorizedOperation response (an HTTP 403 response). The message is encoded because the details of the authorization status can constitute privileged information that the user who requested the operation should not see. To decode an authorization status message, a user must be granted permissions via an IAM policy to request the DecodeAuthorizationMessage (sts:DecodeAuthorizationMessage) action.

Incorrect options:

AWS IAM decode-authorization-message - The IAM service does not have this command, as it's a made-up option.

Use KMS decode-authorization-message - The KMS service does not have this command, as it's a made-up option.

AWS Cognito Decoder - The Cognito service does not have this command, as it's a made-up option.

Reference:

https://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html

</details>

---

<a id="t03-q47"></a>
### T03-Q47
*⏭ пропущено · домен: офіційна мітка*

You have a web application hosted on EC2 that makes GET and PUT requests for objects stored in Amazon Simple Storage Service (S3) using the SDK for PHP. As the security team completed the final review of your application for vulnerabilities, they noticed that your application uses hardcoded IAM access key and secret access key to gain access to AWS services. They recommend you leverage a more secure setup, which should use temporary credentials if possible.

Which of the following options can be used to address the given use-case?

- ✅ Use an IAM Instance Role
- ▫️ Use environment variables
- ▫️ Hardcode the credentials in the application code
- ▫️ Use the SSM parameter store

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use an IAM Instance Role

An instance profile is a container for an IAM role that you can use to pass role information to an EC2 instance when the instance starts. The AWS SDK will use the EC2 metadata service to obtain temporary credentials thanks to the IAM instance role. This is the most secure and common setup when deploying any kind of applications onto an EC2 instance.

Incorrect options:

Use environment variables - This is another option if you configure AWS CLI on the EC2 instance. When configuring the AWS CLI you will set the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY environment variables. This practice may not be bad for one instance but once you start running more EC2 instances this is not a good practice because you may have to change credentials on each instance whereas an IAM Role gets temporary permissions.

Hardcode the credentials in the application code - It will work for sure, but it's not a good practice from a security point of view.

Use the SSM parameter store - With parameter store you can store data such as passwords. The problem is that you need the SDK to access parameter store and without credentials, you cannot use the SDK. Use parameter store for other uses such as database connection strings or other secret codes when you have already authenticated to AWS.

Reference:

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html

</details>

---

<a id="t03-q57"></a>
### T03-Q57
*⏭ пропущено · домен: офіційна мітка*

An Amazon Simple Queue Service (SQS) has to be configured between two AWS accounts for shared access to the queue. AWS account A has the SQS queue in its account and AWS account B has to be given access to this queue.

Which of the following options need to be combined to allow this cross-account access? (Select three)

- ✅ The account A administrator attaches a trust policy to the role that identifies account B as the principal who can assume the role
- ✅ The account A administrator creates an IAM role and attaches a permissions policy
- ✅ The account B administrator delegates the permission to assume the role to any users in account B
- ▫️ The account B administrator creates an IAM role and attaches a trust policy to the role with account B as the principal
- ▫️ The account A administrator delegates the permission to assume the role to any users in account A
- ▫️ The account A administrator attaches a trust policy to the role that identifies account B as the AWS service principal who can assume the role

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

The account A administrator creates an IAM role and attaches a permissions policy

The account A administrator attaches a trust policy to the role that identifies account B as the principal who can assume the role

The account B administrator delegates the permission to assume the role to any users in account B

To grant cross-account permissions, you need to attach an identity-based permissions policy to an IAM role. For example, the AWS account A administrator can create a role to grant cross-account permissions to AWS account B as follows:

The account A administrator creates an IAM role and attaches a permissions policy—that grants permissions on resources in account A—to the role.

The account A administrator attaches a trust policy to the role that identifies account B as the principal who can assume the role.

The account B administrator delegates the permission to assume the role to any users in account B. This allows users in account B to create or access queues in account A.

Incorrect options:

The account B administrator creates an IAM role and attaches a trust policy to the role with account B as the principal - As mentioned above, the account A administrator needs to create an IAM role and then attach a permissions policy. So, this option is incorrect.

The account A administrator delegates the permission to assume the role to any users in account A - This is irrelevant, as users in account B need to be given access.

The account A administrator attaches a trust policy to the role that identifies account B as the AWS service principal who can assume the role - AWS service principal is given as principal in the trust policy when you need to grant the permission to assume the role to an AWS service. The given use case talks about giving permission to another account. So, service principal is not an option here.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-overview-of-managing-access.html

</details>

---

<a id="t03-q58"></a>
### T03-Q58
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Тут AWS managed ReadOnly — бо питання про роль vs ключі, не про конкретну таблицю.** → [детальніше](../../06-contradictions-and-traps.md#c05)

An IT company has a web application running on Amazon EC2 instances that needs read-only access to an Amazon DynamoDB table.

As a Developer Associate, what is the best-practice solution you would recommend to accomplish this task?

- ▫️ Run application code with AWS account root user credentials to ensure full access to all AWS services
- ▫️ Create a new IAM user with access keys. Attach an inline policy to the IAM user with read-only access to DynamoDB. Place the keys in the code. For security, redeploy the code whenever the keys rotate
- ✅ Create an IAM role with an AmazonDynamoDBReadOnlyAccess policy and apply it to the EC2 instance profile
- ▫️ Create an IAM user with Administrator access and configure AWS credentials for this user on the given EC2 instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create an IAM role with an AmazonDynamoDBReadOnlyAccess policy and apply it to the EC2 instance profile

As an AWS security best practice, you should not create an IAM user and pass the user's credentials to the application or embed the credentials in the application. Instead, create an IAM role that you attach to the EC2 instance to give temporary security credentials to applications running on the instance. When an application uses these credentials in AWS, it can perform all of the operations that are allowed by the policies attached to the role.

So for the given use-case, you should create an IAM role with an AmazonDynamoDBReadOnlyAccess policy and apply it to the EC2 instance profile.

 via - https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html

Incorrect options:

Create a new IAM user with access keys. Attach an inline policy to the IAM user with read-only access to DynamoDB. Place the keys in the code. For security, redeploy the code whenever the keys rotate

Create an IAM user with Administrator access and configure AWS credentials for this user on the given EC2 instance

Run application code with AWS account root user credentials to ensure full access to all AWS services

As mentioned in the explanation above, it is dangerous to pass an IAM user's credentials to the application or embed the credentials in the application. The security implications are even higher when you use an IAM user with admin privileges or use the AWS account root user. So all three options are incorrect.

Reference:

https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html

</details>

---

<a id="t04-q16"></a>
### T04-Q16
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has lost their access key ID and secret access key for programmatic access. What should the Developer do?

- ✅ Disable and delete the user's access key and generate a new set
- ▫️ Contact AWS support and request a password reset
- ▫️ Generate a new key pair from the EC2 management console
- ▫️ Reset the AWS account access keys

<details><summary>Пояснення (оригінал, EN)</summary>

Access keys consist of two parts:

The access key identifier. This is not a secret, and can be seen in the IAM console wherever access keys are listed, such as on the user summary page.

The secret access key. This is provided when you initially create the access key pair. Just like a password, it cannot be retrieved later. If you lost your secret access key, then you must create a new access key pair. If you already have the maximum number of access keys, you must delete an existing pair before you can create another.

Therefore, the Developer should disable and delete their access keys and generate a new set.

CORRECT: "Disable and delete the users’ access key and generate a new set" is the correct answer.

INCORRECT: "Contact AWS support and request a password reset" is incorrect as a user name and password are used for console access, not programmatic access.

INCORRECT: "Generate a new key pair from the EC2 management console" is incorrect as a key pair is used for accessing EC2 instances, not for programmatic access to work with AWS services.

INCORRECT: "Reset the AWS account access keys" is incorrect as these are the access keys associated with the root account rather than the users’ individual IAM account.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_general.html#troubleshoot_general_access-keys>

</details>

---

<a id="t04-q21"></a>
### T04-Q21
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has hired a team of remote Developers. The Developers need to work programmatically with AWS resources from their laptop computers.

Which security components MUST the Developers use to authenticate? (Select TWO.)

- ✅ Access key ID
- ✅ Secret access key
- ▫️ MFA device
- ▫️ Console password
- ▫️ IAM user ID

<details><summary>Пояснення (оригінал, EN)</summary>

Access keys consist of two parts: an access key ID (for example, AKIAIOSFODNN7EXAMPLE) and a secret access key (for example, wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY). You use access keys to sign programmatic requests that you make to AWS if you use AWS CLI commands (using the SDKs) or using AWS API operations.

For this scenario, the Developers will be connecting programmatically to AWS resources and will therefore be required to use an access key ID and secret access key.

CORRECT: "Access key ID" is a correct answer.

CORRECT: "Secret access key" is a correct answer.

INCORRECT: "Console password " is incorrect as this is used for accessing AWS via the console with an IAM user ID and is not used for programmatic access.

INCORRECT: "IAM user ID" is incorrect as the IAM user ID is used with the password (see above) to access the AWS management console.

INCORRECT: "MFA device" is incorrect as this is not required for making programmatic requests but can be added for additional security

Джерела: <https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html>

</details>

---

<a id="t04-q25"></a>
### T04-Q25
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is reviewing their security practices. According to AWS best practice, how should access keys be managed to improve security? (Select TWO.)

- ✅ Delete all access keys for the root account IAM user
- ✅ Use different access keys for different applications
- ▫️ Rotate access keys daily
- ▫️ Use the same access key in all applications for consistency
- ▫️ Embed access keys directly into code

<details><summary>Пояснення (оригінал, EN)</summary>

When you access AWS programmatically, you use an access key to verify your identity and the identity of your applications. An access key consists of an access key ID (something like AKIAIOSFODNN7EXAMPLE) and a secret access key (something like wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY).

Anyone who has your access key has the same level of access to your AWS resources that you do. Steps to protect access keys include the following:

• Remove (or Don't Generate) Account Access Key – this is especially important for the root account.

• Use Temporary Security Credentials (IAM Roles) Instead of Long-Term Access Keys.

• Don't embed access keys directly into code.

• Use different access keys for different applications.

• Rotate access keys periodically.

• Remove unused access keys.

• Configure multi-factor authentication for your most sensitive operations.

CORRECT: "Delete all access keys for the root account IAM user" is the correct answer.

CORRECT: "Use different access keys for different applications" is the correct answer.

INCORRECT: "Embed access keys directly into code" is incorrect. This is not a best practice as this is something that should be avoided as much as possible.

INCORRECT: "Rotate access keys daily" is incorrect. Though this would be beneficial from a security perspective it may be hard to manage so this is not an AWS recommended best practice. AWS recommend you rotate access keys “periodically”, not “daily”.

INCORRECT: "Use the same access key in all applications for consistency" is incorrect. The best practice is to use different access keys for different applications.

Джерела: <https://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html#iam-user-access-keys>

</details>

---

<a id="t04-q28"></a>
### T04-Q28
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

> ⚠️ **Тут AWS managed ReadOnly на ролі — ключ "IAM role", а не "least privilege".** → [детальніше](../../06-contradictions-and-traps.md#c05)

A Developer has code running on Amazon EC2 instances that needs read-only access to an Amazon DynamoDB table.

What is the MOST secure approach the Developer should take to accomplish this task?

- ▫️ Use an IAM role with Administrator access applied to the EC2 instance
- ✅ Use an IAM role with an AmazonDynamoDBReadOnlyAccess policy applied to the EC2 instances
- ▫️ Create a user access key for each EC2 instance with read-only access to DynamoDB. Place the keys in the code. Redeploy the code as keys rotate
- ▫️ Run all code with only AWS account root user access keys to ensure maximum access to services

<details><summary>Пояснення (оригінал, EN)</summary>

According to the principle of least privilege the Developer needs to provide the minimum permissions that application requires. The application needs read-only access and therefore an IAM role with an AmazonDynamoDBReadOnlyAccess policy applied that only provides read-only access to DynamoDB is secure.

This role can be applied to the EC2 instance through the management console or programmatically by creating an instance profile and attaching the role to the instance profile. The EC2 instance can then assume the role and get read-only access to DynamoDB.

CORRECT: "Use an IAM role with an AmazonDynamoDBReadOnlyAccess policy applied to the EC2 instances" is the correct answer.

INCORRECT: "Create a user access key for each EC2 instance with read-only access to DynamoDB. Place the keys in the code. Redeploy the code as keys rotate" is incorrect as access keys are less secure than using roles as the keys are stored in the code.

INCORRECT: "Run all code with only AWS account root user access keys to ensure maximum access to services" is incorrect as this is highly insecure as the access keys are stored in code and these access keys provide full permissions to the AWS account.

INCORRECT: "Use an IAM role with Administrator access applied to the EC2 instance" is incorrect as this does not follow the principle of least privilege and is therefore less secure. The role used should have read-only access to DynamoDB.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t04-q49"></a>
### T04-Q49
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Development team are developing a micro-services application that will use Docker containers on Amazon ECS. There will be 6 distinct services included in the architecture. Each service requires specific permissions to various AWS services.

What is the MOST secure way to grant the services the necessary permissions?

- ▫️ Create a new Identity and Access Management (IAM) instance profile containing the required permissions for the various ECS services, then associate that instance role with the underlying EC2 instances
- ▫️ Create a single IAM policy and use principal statements referencing the ECS tasks and assigning the required permissions, then apply the policy to the ECS service
- ✅ Create six separate IAM roles, each containing the required permissions for the associated ECS service, then configure each ECS task definition to reference the associated IAM role
- ▫️ Create six separate IAM roles, each containing the required permissions for the associated ECS service, then create an IAM group and configure the ECS cluster to reference that group

<details><summary>Пояснення (оригінал, EN)</summary>

With IAM roles for Amazon ECS tasks, you can specify an IAM role that can be used by the containers in a task. Applications must sign their AWS API requests with AWS credentials, and this feature provides a strategy for managing credentials for your applications to use, similar to the way that Amazon EC2 instance profiles provide credentials to EC2 instances.

Instead of creating and distributing your AWS credentials to the containers or using the EC2 instance’s role, you can associate an IAM role with an ECS task definition or RunTask API operation. The applications in the task’s containers can then use the AWS SDK or CLI to make API requests to authorized AWS services.

Therefore, the most secure solution is to use a separate IAM role with the specific permissions required for an individual service and associate that role to the relevant ECS task definition. This should then be repeated for the remaining 5 services.

CORRECT: "Create six separate IAM roles, each containing the required permissions for the associated ECS service, then configure each ECS task definition to reference the associated IAM role" is the correct answer.

INCORRECT: "Create six separate IAM roles, each containing the required permissions for the associated ECS service, then create an IAM group and configure the ECS cluster to reference that group" is incorrect. The IAM role should be applied to the ECS task definition, not the ECS cluster.

INCORRECT: "Create a new Identity and Access Management (IAM) instance profile containing the required permissions for the various ECS services, then associate that instance role with the underlying EC2 instances" is incorrect. With IAM Roles for Tasks you apply the permissions directly to the task definition. This means multiple services can share the underlying EC2 instance and only have the minimum privileges required.

INCORRECT: "Create a single IAM policy and use principal statements referencing the ECS tasks and assigning the required permissions, then apply the policy to the ECS service" is incorrect. Identity-based policies attached to the ECS service can be used to control permissions for viewing, launching, and managing resources within ECS. However, for this solution we need to control the permissions for an ECS task to access other AWS services. For this we need to use IAM Roles for Tasks.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html>

</details>

---

<a id="t04-q65"></a>
### T04-Q65
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

> ⚠️ **Сумнівно з т. з. least privilege: досить sqs:SendMessage на цю чергу.** → [детальніше](../../07-outdated-and-wrong-answers.md#d04)

A Developer created an AWS Lambda function and then attempted to add an on failure destination but received the following error:

The function's execution role does not have permissions to call SendMessage on arn:aws:sqs:us-east-1:515148212435:FailureDestination

How can the Developer resolve this issue MOST securely?

- ▫️ Add the Lambda function to a group with administrative privileges
- ▫️ Add the AWSLambdaSQSQueueExecutionRole AWS managed policy to the function’s execution role
- ▫️ Add a permissions policy to the SQS queue allowing the SendMessage action and specify the AWS account number
- ✅ Create a customer managed policy with all read/write permissions to SQS and attach the policy to the function’s execution role

<details><summary>Пояснення (оригінал, EN)</summary>

The Lambda function needs the privileges to use the SendMessage API action on the Amazon SQS queue. The permissions should be assigned to the function’s execution role. The AWSLambdaSQSQueueExecutionRole AWS managed policy cannot be used as this policy does not provide the SendMessage action.

The Developer should therefore create a customer managed policy with read/write permissions to SQS and attach the policy to the function’s execution role.

CORRECT: "Create a customer managed policy with all read/write permissions to SQS and attach the policy to the function’s execution role" is the correct answer.

INCORRECT: "Add the AWSLambdaSQSQueueExecutionRole AWS managed policy to the function’s execution role" is incorrect as this does not provide the necessary permissions.

INCORRECT: "Add a permissions policy to the SQS queue allowing the SendMessage action and specify the AWS account number" is incorrect as this would allow any resource in the AWS account to write to the queue which is less secure.

INCORRECT: "Add the Lambda function to a group with administrative privileges" is incorrect as you cannot add a Lambda function to an IAM group.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html>

</details>

---

<a id="t05-q10"></a>
### T05-Q10
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer plan to deploy an application on Amazon ECS that uses the AWS SDK to make API calls to Amazon DynamoDB. In the development environment the application was configured with access keys. The application is now ready for deployment to a production cluster.

How should the developer configure the application to securely authenticate to AWS services?

- ▫️ Add environment variables pointing to new access key credentials.
- ▫️ Configure the credentials file with a new access key/secret access key.
- ▫️ Add the necessary AWS service permissions to an ECS instance profile.
- ✅ Configure an ECS task IAM role for the application to use.

<details><summary>Пояснення (оригінал, EN)</summary>

Your Amazon ECS tasks can have an IAM role associated with them. The permissions granted in the IAM role are assumed by the containers running in the task. The following explain the benefits of using IAM roles with your tasks.

• Credential Isolation: A container can only retrieve credentials for the IAM role that is defined in the task definition to which it belongs; a container never has access to credentials that are intended for another container that belongs to another task.

• Authorization: Unauthorized containers cannot access IAM role credentials defined for other tasks.

• Auditability: Access and event logging is available through CloudTrail to ensure retrospective auditing. Task credentials have a context of taskArn that is attached to the session, so CloudTrail logs show which task is using which role.

CORRECT: "Configure an ECS task IAM role for the application to use" is the correct answer (as explained above.)

INCORRECT: "Add the necessary AWS service permissions to an ECS instance profile" is incorrect.

The privileges assigned to instance profiles on the Amazon ECS instances are available to all tasks running on the instance. This is not secure and AWS recommend that you limit the permissions you assign to the instance profile.

INCORRECT: "Configure the credentials file with a new access key/secret access key" is incorrect.

Access keys are not a secure way of providing authentication. It is better to use roles that obtain temporary security permissions using the AWS STS service.

INCORRECT: "Add environment variables pointing to new access key credentials" is incorrect.

As above, access keys should not be used, IAM roles should be used instead.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html>

</details>

---

<a id="t05-q38"></a>
### T05-Q38
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to create an instance profile for an Amazon EC2 instance using the AWS CLI. How can this be achieved? (Select THREE.)

- ✅ Run the aws ec2 associate-instance-profile command
- ▫️ Run the AddRoleToInstanceProfile API
- ✅ Run the aws iam create-instance-profile command
- ▫️ Run the CreateInstanceProfile API
- ✅ Run the aws iam add-role-to-instance-profile command
- ▫️ Run the AssignInstanceProfile API

<details><summary>Пояснення (оригінал, EN)</summary>

To add a role to an Amazon EC2 instance using the AWS CLI you must first create an instance profile. Then you need to add the role to the instance profile and finally assign the instance profile to the Amazon EC2 instance.

The following example commands would achieve this outcome:

aws iam create-instance-profile --instance-profile-name EXAMPLEPROFILENAME
aws iam add-role-to-instance-profile --instance-profile-name EXAMPLEPROFILENAME --role-name EXAMPLEROLENAME
aws ec2 associate-iam-instance-profile --iam-instance-profile Name=EXAMPLEPROFILENAME --instance-id i-012345678910abcde
CORRECT: "Run the aws iam create-instance-profile command" is a correct answer.

CORRECT: "Run the aws iam add-role-to-instance-profile command" is a correct answer.

CORRECT: "Run the aws ec2 associate-instance-profile command" is a correct answer.

INCORRECT: "Run the CreateInstanceProfile API" is incorrect as this is an API action, not an AWS CLI command.

INCORRECT: "Run the AddRoleToInstanceProfile API" is incorrect as this is an API action, not an AWS CLI command.

INCORRECT: "Run the AssignInstanceProfile API" is incorrect as this is an API action, not an AWS CLI command.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/attach-replace-ec2-instance-profile/>

</details>

---

<a id="t06-q12"></a>
### T06-Q12
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has an application that provides access to objects in Amazon S3 based on the type of user. The user types are registered user and guest user. The company has 30,000 users. Information is read from an S3 bucket depending on the user type.

Which approaches are recommended to provide access to both user types MOST efficiently? (Select TWO.)

- ✅ Use the AWS IAM service and let the application assume different roles depending on the type of user.
- ✅ Use Amazon Cognito to provide access using authenticated and unauthenticated roles.
- ▫️ Store separate access keys in the application code for registered users and guest users to provide access to the objects.
- ▫️ Create a new IAM user for each user and grant access to the S3 objects.
- ▫️ Use S3 bucket policies to restrict read access to specific IAM users.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito can be used with identity pools. A Cognito identity pool supports both authenticated and unauthenticated identities. Authenticated identities belong to users who are authenticated by any supported identity provider. Unauthenticated identities typically belong to guest users.

The most secure way of using the IAM service for this solution would be to use separate roles. IAM roles can be securely assumed based on the type of user. Each role can be configured with different permission sets as applicable to registered and guest users.

CORRECT: "Use Amazon Cognito to provide access using authenticated and unauthenticated roles" is a correct answer (as explained above.)

CORRECT: "Use the AWS IAM service and let the application assume different roles depending on the type of user" is also a correct answer (as explained above.)

INCORRECT: "Store separate access keys in the application code for registered users and guest users to provide access to the objects" is incorrect.

This is highly insecure. You should avoid embedding access keys in application code and use IAM roles instead.

INCORRECT: "Create a new IAM user for each user and grant access to the S3 objects" is incorrect.

This would be a lot of users and is an inefficient solution.

INCORRECT: "Use S3 bucket policies to restrict read access to specific IAM users" is incorrect.

This would also be highly complex with so many users and would need constant updating when users need to be added or removed.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/identity-pools.html> · <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html>

</details>

---

<a id="t06-q33"></a>
### T06-Q33
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A serverless application uses an IAM role to authenticate and authorize access to an Amazon DynamoDB table. A Developer is troubleshooting access issues affecting the application. The Developer has access to the IAM role that the application is using.

Which of the following commands will help the Developer to test the role permissions using the AWS CLI?

- ▫️ aws dynamodb describe-endpoints
- ▫️ aws sts get-session-token
- ▫️ aws iam get-role-policy
- ✅ aws sts assume-role

<details><summary>Пояснення (оригінал, EN)</summary>

The AWS CLI “aws sts assume role” command will enable the Developer to assume the role and gain temporary security credentials. The Developer can then use those security credentials to troubleshoot access issues that are affecting the application.

CORRECT: "aws sts assume-role" is the correct answer.

INCORRECT: "aws sts get-session-token" is incorrect. This is used to get temporary credentials for an AWS account or IAM user. It can subsequently be used to call the assume-role API.

INCORRECT: "aws iam get-role-policy" is incorrect. This command retrieves the specified inline policy document that is embedded with the specified IAM role.

INCORRECT: "aws dynamodb describe-endpoints" is incorrect. This command returns the regional endpoint information.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/sts/assume-role.html>

</details>

---

<a id="t06-q48"></a>
### T06-Q48
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization has an account for each environment: Production, Testing, Development. A Developer with an IAM user in the Development account needs to launch resources in the Production and Testing accounts. What is the MOST efficient way to provide access

- ▫️ Create an IAM permissions policy in the Production and Testing accounts and reference the IAM user in the Development account
- ▫️ Create a separate IAM user in each account and have the Developer login separately to each account
- ✅ Create a role with the required permissions in the Production and Testing accounts and have the Developer assume that role
- ▫️ Create an IAM group in the Production and Testing accounts and add the Developer’s user from the Development account to the groups

<details><summary>Пояснення (оригінал, EN)</summary>

You can grant your IAM users’ permission to switch to roles within your AWS account or to roles defined in other AWS accounts that you own. This is known as cross-account access.

In the image below a user in the Development account needs to access an S3 bucket in the Production account:

The user is able to assume the role in the Production account and access the S3 bucket. This is more efficient than providing the user with multiple accounts. In this scenario the user requests to switch to the role through either the console or the API/CLI.

CORRECT: "Create a role with the required permissions in the Production and Testing accounts and have the Developer assume that role" is the correct answer.

INCORRECT: "Create a separate IAM user in each account and have the Developer login separately to each account" is incorrect as this is not the most efficient method of providing access. Cross-account access is preferred .

INCORRECT: "Create an IAM group in the Production and Testing accounts and add the Developer’s user from the Development account to the groups" is incorrect as you cannot add an IAM user from another AWS account to a group.

INCORRECT: "Create an IAM permissions policy in the Production and Testing accounts and reference the IAM user in the Development account" is incorrect as you cannot reference an IAM user from another AWS account in a permissions policy.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html>

</details>

---

<a id="t06-q56"></a>
### T06-Q56
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Database)*

A development team is working with Amazon MemoryDB for Redis. The team lead wants to limit the default access of the service-linked role and implement a custom-managed policy instead.

What needs to be done prior to making this change?

- ▫️ The team lead would have to login as the root user to change the permission setting.
- ✅ The team lead would need to have permission to call `iam:createServiceLinkedRole`.
- ▫️ The team lead would edit the default policy named `AmazonMemoryDBFullAccess`.
- ▫️ The team lead would need permission from an AWS Technical Account Manager.

<details><summary>Пояснення (оригінал, EN)</summary>

Service-linked roles are tied to the resource with predefined permissions. The default policy associated Amazon MemoryDB for Redis is “AmazonMemoryDBFullAccess”. This comes pre-provisioned with permission that the service requires to create a service link that is used to create resources and access other AWS resources and services.

You might decide not to use the default policy and instead to use a custom-managed policy. In this case, make sure that you have either permissions to call iam:createServiceLinkedRole or that you have created the MemoryDB service-linked role.

CORRECT: "The team lead would need to have permission to call `iam:createServiceLinkedRole`" is the correct answer (as explained above.)

INCORRECT: "The team lead would have to login as the root user to change the permission setting" is incorrect.

The team lead may not be the root user. Logging in and accessing the root user should be limited to the tasks that are necessary and limited to the root user. A Cloud Administrator, for example, could update the IAM roles needed for the team lead to make the appropriate service-linked policy changes.

INCORRECT: "The team lead would need permission from an AWS Technical Account Manager" is incorrect.

There are some changes to an account or services that an organization would need to contact their technical account manager to change. An update to the IAM policy or changing a service-linked role is not one of them.

INCORRECT: "The team lead would edit the default policy named `AmazonMemoryDBFullAccess`" is incorrect.

AWS managed policies cannot be edited. A custom service policy would need to create and attached with the appropriate IAM role.

Джерела: <https://docs.aws.amazon.com/memorydb/latest/devguide/set-up.html>

</details>

---

<a id="t07-q03"></a>
### T07-Q03
*⏭ пропущено · домен: офіційна мітка*

Two policies are attached to an IAM user. The first policy states that the user has explicitly been denied all access to EC2 instances. The second policy states that the user has been allowed permission for EC2:Describe action.

When the user tries to use 'Describe' action on an EC2 instance using the CLI, what will be the output?

- ▫️ The order of the policy matters. If policy 1 is before 2, then the user is denied access. If policy 2 is before 1, then the user is allowed access
- ▫️ The IAM user stands in an invalid state, because of conflicting policies
- ▫️ The user will get access because it has an explicit allow
- ✅ The user will be denied access because one of the policies has an explicit deny on it

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The user will be denied access because the policy has an explicit deny on it - User will be denied access because any explicit deny overrides the allow.

Policy Evaluation explained:  via - https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html

Incorrect options:

The IAM user stands in an invalid state, because of conflicting policies - This is an incorrect statement. Access policies can have allow and deny permissions on them and based on policy rules they are evaluated. A user account does not get invalid because of policies.

The user will get access because it has an explicit allow - As discussed above, explicit deny overrides all other permissions and hence the user will be denied access.

The order of the policy matters. If policy 1 is before 2, then the user is denied access. If policy 2 is before 1, then the user is allowed access - If policies that apply to a request include an Allow statement and a Deny statement, the Deny statement trumps the Allow statement. The request is explicitly denied.

Reference:

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html

</details>

---

<a id="t07-q61"></a>
### T07-Q61
*⏭ пропущено · домен: офіційна мітка*

A developer wants to securely store an access token that allows a transaction-processing application running on Amazon EC2 instances to authenticate and send a chat message (via the chat API) to the company's support team when an invalid transaction is detected. While minimizing management overhead, the chat API access token must be encrypted both at rest and in transit, and also be accessible from other AWS accounts.

What is the most efficient solution to address this scenario?

- ✅ Leverage AWS Secrets Manager with an AWS KMS customer-managed key to store the access token as a secret and configure a resource-based policy for the secret to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Secrets Manager. Fetch the token from Secrets Manager and then use the decrypted access token to send the message to the chat
- ▫️ Leverage SSE-KMS to store the access token as an encrypted object on S3 and configure a resource-based policy for the S3 bucket to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access the S3 object. Fetch the token from S3 and then use the decrypted access token to send the message to the chat
- ▫️ Leverage AWS Systems Manager Parameter Store with an AWS KMS customer-managed key to store the access token as a SecureString parameter and configure a resource-based policy for the parameter to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Parameter Store. Fetch the token from Parameter Store using the with decryption flag and then use the decrypted access token to send the message to the chat
- ▫️ Store AWS KMS encrypted access token in a DynamoDB table and configure a resource-based policy for the DynamoDB table to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access the DynamoDB table. Fetch the token from the Dynamodb table and then use the decrypted access token to send the message to the chat

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Leverage AWS Secrets Manager with an AWS KMS customer-managed key to store the access token as a secret and configure a resource-based policy for the secret to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Secrets Manager. Fetch the token from Secrets Manager and then use the decrypted access token to send the message to the chat

AWS Secrets Manager is an AWS service that encrypts and stores your secrets, and transparently decrypts and returns them to you in plaintext. It's designed especially to store application secrets, such as login credentials, that change periodically and should not be hard-coded or stored in plaintext in the application. In place of hard-coded credentials or table lookups, your application calls Secrets Manager.

Secrets Manager also supports features that periodically rotate the secrets associated with commonly used databases. It always encrypts newly rotated secrets before they are stored.

Secrets Manager integrates with AWS Key Management Service (AWS KMS) to encrypt every version of every secret value with a unique data key that is protected by an AWS KMS key. This integration protects your secrets under encryption keys that never leave AWS KMS unencrypted. It also enables you to set custom permissions on the KMS key and audit the operations that generate, encrypt, and decrypt the data keys that protect your secrets.

To grant permission to retrieve secret values, you can attach policies to secrets or identities.

 via - https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_examples.html

For the given use case, you can use the resource-based policy to the secret to allow access from other accounts. Then you need to update the IAM role of the EC2 instances with permissions to access Secrets Manager which will retrieve the token from Secrets Manager and use the decrypted access token to send the message to the support team via the chat API.

Incorrect options:

Leverage AWS Systems Manager Parameter Store with an AWS KMS customer-managed key to store the access token as a SecureString parameter and configure a resource-based policy for the parameter to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access Parameter Store. Fetch the token from Parameter Store using the with decryption flag and then use the decrypted access token to send the message to the chat - You cannot use a resource-based policy with a parameter in the Parameter Store. Parameter Store supports parameter policies that are available for parameters that use the advanced parameters tier. Parameter policies help you manage a growing set of parameters by allowing you to assign specific criteria to a parameter such as an expiration date or time to live. Parameter policies are especially helpful in forcing you to update or delete passwords and configuration data stored in Parameter Store, a capability of AWS Systems Manager. So this option is incorrect.

Store AWS KMS encrypted access token in a DynamoDB table and configure a resource-based policy for the DynamoDB table to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access the DynamoDB table. Fetch the token from the Dynamodb table and then use the decrypted access token to send the message to the chat - You should note that DynamoDB does not support resource-based policies. Moreover, it's a security bad practice to keep sensitive access credentials in code, database or a flat file on a file system or object storage. Therefore, this option is incorrect.

Leverage SSE-KMS to store the access token as an encrypted object on S3 and configure a resource-based policy for the S3 bucket to allow access from other accounts. Modify the IAM role of the EC2 instances with permissions to access the S3 object. Fetch the token from S3 and then use the decrypted access token to send the message to the chat - It is considered a security bad practice to keep sensitive access credentials in code, database, or a flat file on a file system or object storage. Therefore, this option is incorrect.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_examples.html> · <https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-policies.html>

</details>

---

<a id="t08-q26"></a>
### T08-Q26
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A development team are creating a mobile application that customers will use to receive notifications and special offers. Users will not be required to log in.

What is the MOST efficient method to grant users access to AWS resources?

- ✅ Use Amazon Cognito to associate unauthenticated users with an IAM role that has limited access to resources
- ▫️ Use Amazon Cognito Federated Identities and setup authentication using a Cognito User Pool
- ▫️ Embed access keys in the application that have limited access to resources
- ▫️ Use an IAM SAML 2.0 identity provider to establish trust

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito Identity Pools can support unauthenticated identities by providing a unique identifier and AWS credentials for users who do not authenticate with an identity provider. If your application allows users who do not log in, you can enable access for unauthenticated identities.

This is the most efficient and secure way to allow unauthenticated access as the process to set it up is simple and the IAM role can be configured with permissions allowing only the access permitted for unauthenticated users.

CORRECT: "Use Amazon Cognito to associate unauthenticated users with an IAM role that has limited access to resources" is the correct answer.

INCORRECT: "Use an IAM SAML 2.0 identity provider to establish trust" is incorrect as we need to allow unauthenticated users access to the AWS resources, not those who have been authenticated elsewhere (i.e. Active Directory).

INCORRECT: "Use Amazon Cognito Federated Identities and setup authentication using a Cognito User Pool" is incorrect as we need to setup unauthenticated access, not authenticated access through a user pool.

INCORRECT: "Embed access keys in the application that have limited access to resources" is incorrect. We should try and avoid embedding access keys in application code, it is better to use the built-in features of Amazon Cognito.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/identity-pools.html>

</details>

---

<a id="t08-q36"></a>
### T08-Q36
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization has a new AWS account and is setting up IAM users and policies. According to AWS best practices, which of the following strategies should be followed? (Select TWO.)

- ▫️ Always use customer managed policies instead of AWS managed policies
- ✅ Create standalone policies instead of using inline policies
- ▫️ Use user accounts to delegate permissions
- ✅ Use groups to assign permissions to users
- ▫️ Create user accounts that can be shared for efficiency

<details><summary>Пояснення (оригінал, EN)</summary>

AWS provide a number of best practices for AWS IAM that help you to secure your resources. The key best practices referenced in this scenario are as follows:

• Use groups to assign permissions to users – this is correct as you should create permissions policies and assign them to groups. Users can be added to the groups to get the permissions they need to perform their jobs.

• Create standalone policies instead of using inline policies (Use Customer Managed Policies Instead of Inline Policies in the AWS best practices) – this refers to creating your own policies that are standalone policies which can be reused multiple times (assigned to multiple entities such as groups, and users). This is better than using inline policies which are directly attached to a single entity.

CORRECT: "Use groups to assign permissions to users" is the correct answer.

CORRECT: "Create standalone policies instead of using inline policies" is the correct answer.

INCORRECT: "Use user accounts to delegate permissions" is incorrect as you should use roles to delegate permissions.

INCORRECT: "Create user accounts that can be shared for efficiency" is incorrect as you should not share user accounts. Always create individual user accounts.

INCORRECT: "Always use customer managed policies instead of AWS managed policies" is incorrect as this is not a best practice. AWS recommend getting started by using AWS managed policies (Get Started Using Permissions with AWS Managed Policies).

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html>

</details>

---

<a id="t08-q45"></a>
### T08-Q45
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer received the following error when attempting to launch an Amazon EC2 instance using the AWS CLI.

An error occurred (UnauthorizedOperation) when calling the RunInstances operation: You are not authorized to perform this operation. Encoded authorization failure message: VNVaHFdCohROkbyT_rIXoRyNTp7vXFJCqnGiwPuyKnsSVf-WSSGK_06H3vKnrkUa3qx5D40hqj9HEG8kznr04Acmi6lvc8m51tfqtsomFSDylK15x96ZrxMW7MjDJLrMkM0BasPvy8ixo1wi6X2b0C-J1ThyWU9IcrGd7WbaRDOiGbBhJtKs1z01WSn2rVa5_7sr5PwEK-ARrC9y5Pl54pmeF6wh7QhSv2pFO0y39WVBajL2GmByFmQ4p8s-6Lcgxy23b4NJdJwWOF4QGxK9HcKof1VTVZ2oIpsI-dH6_0t2DI0BTwaIgmaT7ldontI1p7OGz-3wPgXm67x2NVNgaK63zPxjYNbpl32QuXLKUKNlB9DdkSdoLvsuFIvf-lQOXLPHnZKCWMqrkI87eqKHYpYKyV5c11TIZTAJ3MntTGO_TJ4U9ySYvTzU2LgswYOtKF_O76-13fryGG5dhgOW5NxwCWBj6WT2NSJvqOeLykAFjR_ET4lM6Dl1XYfQITWCqIzlvlQdLmHJ1jqjp4gW56VcQCdqozLv2UAg8IdrZIXd0OJ047RQcvvN1IyZN0ElL7dR6RzAAQrftoKMRhZQng6THZs8PZM6wep6-yInzwfg8J5_FW6G_PwYqO-4VunVtJSTzM_F_8kojGlRmzqy7eCk5or__bIisUoslw

What action should the Developer perform to make this error more human-readable?

- ▫️ Use an open source decoding library to decode the message
- ▫️ Make a call to AWS KMS to decode the message
- ▫️ Use the AWS IAM decode-authorization-message API to decode this message
- ✅ Use the AWS STS decode-authorization-message API to decode the message

<details><summary>Пояснення (оригінал, EN)</summary>

The AWS STS decode-authorization-message API decodes additional information about the authorization status of a request from an encoded message returned in response to an AWS request. The output is then decoded into a more human-readable output that can be viewed in a JSON editor.

The following example is the decoded output from the error shown in the question:

{
"DecodedMessage": "{\"allowed\":false,\"explicitDeny\":false,\"matchedStatements\":{\"items\":[]},\"failures\":{\"items\":[]},\"context\":{\"principal\":{\"id\":\"AIDAXP4J2EKU7YXXG3EJ4\",\"name\":\"Paul\",\"arn\":\"arn:aws:iam::515148227241:user/Paul\"},\"action\":\"ec2:RunInstances\",\"resource\":\"arn:aws:ec2:ap-southeast-2:515148227241:instance/*\",\"conditions\":{\"items\":[{\"key\":\"ec2:InstanceMarketType\",\"values\":{\"items\":[{\"value\":\"on-demand\"}]}},{\"key\":\"aws:Resource\",\"values\":{\"items\":[{\"value\":\"instance/*\"}]}},{\"key\":\"aws:Account\",\"values\":{\"items\":[{\"value\":\"515148227241\"}]}},{\"key\":\"ec2:AvailabilityZone\",\"values\":{\"items\":[{\"value\":\"ap-southeast-2a\"}]}},{\"key\":\"ec2:ebsOptimized\",\"values\":{\"items\":[{\"value\":\"false\"}]}},{\"key\":\"ec2:IsLaunchTemplateResource\",\"values\":{\"items\":[{\"value\":\"false\"}]}},{\"key\":\"ec2:InstanceType\",\"values\":{\"items\":[{\"value\":\"t2.micro\"}]}},{\"key\":\"ec2:RootDeviceType\",\"values\":{\"items\":[{\"value\":\"ebs\"}]}},{\"key\":\"aws:Region\",\"values\":{\"items\":[{\"value\":\"ap-southeast-2\"}]}},{\"key\":\"aws:Service\",\"values\":{\"items\":[{\"value\":\"ec2\"}]}},{\"key\":\"ec2:InstanceID\",\"values\":{\"items\":[{\"value\":\"*\"}]}},{\"key\":\"aws:Type\",\"values\":{\"items\":[{\"value\":\"instance\"}]}},{\"key\":\"ec2:Tenancy\",\"values\":{\"items\":[{\"value\":\"default\"}]}},{\"key\":\"ec2:Region\",\"values\":{\"items\":[{\"value\":\"ap-southeast-2\"}]}},{\"key\":\"aws:ARN\",\"values\":{\"items\":[{\"value\":\"arn:aws:ec2:ap-southeast-2:515148227241:instance/*\"}]}}]}}}"
}
Therefore, the best answer is to use the AWS STS decode-authorization-message API to decode the message.

CORRECT: "Use the AWS STS decode-authorization-message API to decode the message" is the correct answer.

INCORRECT: "Make a call to AWS KMS to decode the message" is incorrect as the message is not encrypted, it is base64 encoded.

INCORRECT: "Use an open source decoding library to decode the message" is incorrect as you can use the AWS STS decode-authorization-message API.

INCORRECT: "Use the AWS IAM decode-authorization-message API to decode this message" is incorrect as the decode-authorization-message API is associated with STS, not IAM.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html>

</details>

---

<a id="t08-q47"></a>
### T08-Q47
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A software firm is introducing a multimedia application that allows guest users to sample content before deciding to fully register. The company requires a mechanism that can identify users who have already created an account and to monitor the quantity of guest users who ultimately register.

What two actions would best satisfy these needs? (Select TWO.)

- ▫️ Use AWS Lambda to track the transitions from guest to full accounts.
- ▫️ Use AWS Glue to oversee user registration and account conversion.
- ✅ Use Amazon Cognito User Pools for managing user registration and authentication.
- ✅ Implement AWS IAM roles to provide distinct permissions for guest users and registered users.
- ▫️ Deploy Amazon S3 for managing user data and monitoring account conversions.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito User Pools serve as a user directory that offers the backend capabilities required for user registration, authentication, and account recovery. This makes it the right solution for identifying users who have created accounts.

AWS IAM roles can be used to provide distinct permissions for guest users and registered users. While they do not directly track account conversions, they can help delineate between different user states.

CORRECT: "Implement AWS IAM roles to provide distinct permissions for guest users and registered users" is a correct answer (as explained above.)

CORRECT: "Use Amazon Cognito User Pools for managing user registration and authentication" is also a correct answer (as explained above.)

INCORRECT: "Use AWS Glue to oversee user registration and account conversion" is incorrect.

AWS Glue is an ETL service designed for easy data preparation and loading for analytics, not for managing user registration or authentication.

INCORRECT: "Deploy Amazon S3 for managing user data and monitoring account conversions" is incorrect.

Amazon S3 is a storage service and is not designed for managing user registration or tracking account conversions.

INCORRECT: "Use AWS Lambda to track the transitions from guest to full accounts" is incorrect.

AWS Lambda is a serverless compute service and is not typically used for managing user registration or tracking conversions.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html>

</details>

---

<a id="t08-q52"></a>
### T08-Q52
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is trying to make API calls using AWS SDK. The IAM user credentials used by the application require multi-factor authentication for all API calls.

Which method should the Developer use to access the multi-factor authentication protected API?

- ▫️ GetFederationToken
- ✅ GetSessionToken
- ▫️ DecodeAuthorizationMessage
- ▫️ GetCallerIdentity

<details><summary>Пояснення (оригінал, EN)</summary>

The GetSessionToken API call returns a set of temporary credentials for an AWS account or IAM user. The credentials consist of an access key ID, a secret access key, and a security token. Typically, you use GetSessionToken if you want to use MFA to protect programmatic calls to specific AWS API operations

Therefore, the Developer can use GetSessionToken with an MFA device to make secure API calls using the AWS SDK.

CORRECT: "GetSessionToken" is the correct answer.

INCORRECT: "GetFederationToken" is incorrect as this is used with federated users to return a set of temporary security credentials (consisting of an access key ID, a secret access key, and a security token).

INCORRECT: "GetCallerIdentity" is incorrect as this API action returns details about the IAM user or role whose credentials are used to call the operation.

INCORRECT: "DecodeAuthorizationMessage" is incorrect as this API action decodes additional information about the authorization status of a request from an encoded message returned in response to an AWS request.

Джерела: <https://docs.aws.amazon.com/STS/latest/APIReference/API_GetSessionToken.html>

</details>

---

<a id="t08-q54"></a>
### T08-Q54
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A team of Developers require access to an AWS account that is a member account in AWS Organizations. The administrator of the master account needs to restrict the AWS services, resources, and API actions that can be accessed by the users in the account.

What should the administrator create?

- ▫️ A Tag Policy
- ✅ A Service Control Policy (SCP)
- ▫️ A Consolidated Billing account
- ▫️ An Organizational Unit

<details><summary>Пояснення (оригінал, EN)</summary>

As an administrator of the master account of an organization, you can use service control policies (SCPs) to specify the maximum permissions for member accounts in the organization.

In SCPs, you can restrict which AWS services, resources, and individual API actions the users and roles in each member account can access. You can also define conditions for when to restrict access to AWS services, resources, and API actions.

The following example shows how an SCP can be created to restrict the EC2 instance types that any user can run in the account:

These restrictions even override the administrators of member accounts in the organization. When AWS Organizations blocks access to a service, resource, or API action for a member account, a user or role in that account can't access it. This block remains in effect even if an administrator of a member account explicitly grants such permissions in an IAM policy.

CORRECT: "A Service Control Policy (SCP)" is the correct answer.

INCORRECT: "A Tag Policy" is incorrect as these are used to maintain consistent tags, including the preferred case treatment of tag keys and tag values.

INCORRECT: "An Organizational Unit" is incorrect as this is used to group accounts for administration.

INCORRECT: "A Consolidated Billing account" is incorrect as consolidated billing is not related to controlling access to resources within an account.

Джерела: <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html> · <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_about-scps.html>

</details>

---

<a id="t08-q57"></a>
### T08-Q57
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An Amazon DynamoDB table will store authentication credentials for a mobile app. The table must be secured so only a small group of Developers are able to access it.

How can table access be secured according to this requirement and following AWS best practice?

- ▫️ Create a shared user account and attach a permissions policy granting access to the table. Instruct the Developer’s to login with the user account
- ▫️ Create an AWS KMS resource-based policy to a CMK and grant the developer’s user accounts the permissions to decrypt data in the table using the CMK
- ✅ Attach a permissions policy to an IAM group containing the Developer’s IAM user accounts that grants access to the table
- ▫️ Attach a resource-based policy to the table and add an IAM group containing the Developer’s IAM user accounts as a Principal in the policy

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB supports identity-based policies only. The best practice method to assign permissions to the table is to create a permissions policy that grants access to the table and assigning that policy to an IAM group that contains the Developer’s user accounts.

This will provide all users with accounts in the IAM group with the access required to access the DynamoDB table.

CORRECT: "Attach a permissions policy to an IAM group containing the Developer’s IAM user accounts that grants access to the table" is the correct answer.

INCORRECT: "Attach a resource-based policy to the table and add an IAM group containing the Developer’s IAM user accounts as a Principal in the policy" is incorrect as you cannot assign resource-based policies to DynamoDB tables.

INCORRECT: "Create an AWS KMS resource-based policy to a CMK and grant the developer’s user accounts the permissions to decrypt data in the table using the CMK" is incorrect as the questions requires that the Developers can access the table, not to be able to decrypt data.

INCORRECT: "Create a shared user account and attach a permissions policy granting access to the table. Instruct the Developer’s to login with the user account" is incorrect as this is against AWS best practice. You should never share user accounts.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/using-identity-based-policies.html>

</details>

---

<a id="t08-q58"></a>
### T08-Q58
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

An application running on a fleet of EC2 instances use the AWS SDK for Java to copy files into several AWS buckets using access keys stored in environment variables. A Developer has modified the instances to use an assumed IAM role with a more restrictive policy that allows access to only one bucket.

However, after applying the change the Developer logs into one of the instances and is still able to write to all buckets. What is the MOST likely explanation for this situation?

- ✅ The AWS credential provider looks for instance profile credentials last
- ▫️ The AWS CLI is corrupt and needs to be reinstalled
- ▫️ An IAM inline policy is being used on the IAM role
- ▫️ An IAM managed policy is being used on the IAM role

<details><summary>Пояснення (оригінал, EN)</summary>

When you initialize a new service client without supplying any arguments, the AWS SDK for Java attempts to find AWS credentials by using the default credential provider chain implemented by the DefaultAWSCredentialsProviderChain class. The default credential provider chain looks for credentials in this order:

  1. Environment variables–AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY. The AWS SDK for Java uses the EnvironmentVariableCredentialsProvider class to load these credentials.

2. Java system properties–aws.accessKeyId and aws.secretKey. The AWS SDK for Java uses the SystemPropertiesCredentialsProvider to load these credentials.

3. The default credential profiles file– typically located at ~/.aws/credentials (location can vary per platform) and shared by many of the AWS SDKs and by the AWS CLI. The AWS SDK for Java uses the ProfileCredentialsProvider to load these credentials.

4. Amazon ECS container credentials– loaded from the Amazon ECS if the environment variable AWS_CONTAINER_CREDENTIALS_RELATIVE_URI is set. The AWS SDK for Java uses the ContainerCredentialsProvider to load these credentials. You can specify the IP address for this value.

5. Instance profile credentials– used on EC2 instances and delivered through the Amazon EC2 metadata service. The AWS SDK for Java uses the InstanceProfileCredentialsProvider to load these credentials. You can specify the IP address for this value.

Therefore, the AWS SDK for Java will find the credentials stored in environment variables before it checks for instance provide credentials and will allow access to the extra S3 buckets.

NOTE: The Default Credential Provider Chain is very similar for other SDKs and the CLI as well. Check the references below for an article showing the steps for the AWS CLI.

CORRECT: "The AWS credential provider looks for instance profile credentials last" is the correct answer.

INCORRECT: "An IAM inline policy is being used on the IAM role" is incorrect. If an inline policy was also applied to the role with a less restrictive policy it wouldn’t matter, as the most restrictive policy would be applied.

INCORRECT: "An IAM managed policy is being used on the IAM role" is incorrect. Though the managed policies are less restrictive by default (read-only or full access), this is not the most likely cause of the situation as we were told the policy is more restrictive and we know the environments variables have access keys in them which will be used before the policy is checked.

INCORRECT: "The AWS CLI is corrupt and needs to be reinstalled" is incorrect. There is a plausible explanation for this situation so no reason to suspect a software bug is to blame.

Джерела: <https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html> · <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html>

</details>

---

<a id="t10-q07"></a>
### T10-Q07
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A developer has a user account in the Development AWS account. He has been asked to modify resources in a Production AWS account. What is the MOST secure way to provide temporary access to the developer?

- ▫️ Add the user to a group in the second account that has a role attached granting the necessary permissions
- ▫️ Use AWS KMS to generate cross-account customer master keys and use those get short-lived credentials
- ✅ Create a cross-account access role, and use sts:AssumeRole API to get short-lived credentials
- ▫️ Generate an access key on the second account using the root account and share the access keys with the developer for API access

<details><summary>Пояснення (оригінал, EN)</summary>

This should be implemented using a role in the Production account and a group in the Development account. The developer in the Development account would then be added to the group. The role in the Production account would provide the necessary access and would allow the group in the Development account to assume the role.

The following image depicts this setup:

Therefore, the most secure way to achieve the required access is to use a role in the Production account that the user is able to assume and then the user can request short-lived credentials from the Security Token Service (STS).

CORRECT: "Create a cross-account access role, and use sts:AssumeRole API to get short-lived credentials" is the correct answer.

INCORRECT: "Generate an access key on the second account using the root account and share the access keys with the developer for API access" is incorrect as this is highly insecure. You should never share access keys across user accounts, and you should especially not use access keys associated with the root account.

INCORRECT: "Add the user to a group in the second account that has a role attached granting the necessary permissions" is incorrect as you cannot add a user to a group in a different AWS account.

INCORRECT: "Use AWS KMS to generate cross-account customer master keys and use those get short-lived credentials" is incorrect as you do not use AWS KMS CMKs for obtaining short-lived credentials from the STS service. CMKs are used for encrypting data.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html>

</details>

---

<a id="t10-q22"></a>
### T10-Q22
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer needs to restrict all users and roles from using a list of API actions within a member account in AWS Organizations. The Developer needs to deny access to a few specific API actions.

What is the MOST efficient way to do this?

- ▫️ Create an allow list and specify the API actions to deny
- ▫️ Create an IAM policy that allows only the unrestricted API actions
- ✅ Create a deny list and specify the API actions to deny
- ▫️ Create an IAM policy that denies the API actions for all users and roles

<details><summary>Пояснення (оригінал, EN)</summary>

Service control policies (SCPs) are one type of policy that you can use to manage your organization. SCPs offer central control over the maximum available permissions for all accounts in your organization, allowing you to ensure your accounts stay within your organization’s access control guidelines.

You can configure the SCPs in your organization to work as either of the following:

• A deny list – actions are allowed by default, and you specify what services and actions are prohibited

• An allow list – actions are prohibited by default, and you specify what services and actions are allowed

As there are only a few API actions to restrict the most efficient strategy for this scenario is to create a deny list and specify the specific actions that are prohibited.

CORRECT: "Create a deny list and specify the API actions to deny" is the correct answer.

INCORRECT: "Create an allow list and specify the API actions to deny" is incorrect as with an allow list you specify the API actions to allow.

INCORRECT: "Create an IAM policy that denies the API actions for all users and roles" is incorrect as you cannot create deny policies in IAM. IAM policies implicitly deny access unless you explicitly allow permissions.

INCORRECT: "Create an IAM policy that allows only the unrestricted API actions" is incorrect. This will not work for administrative users such as the root account (as they have extra permissions) so an SCP must be used.

Джерела: <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html> · <https://docs.aws.amazon.com/organizations/latest/userguide/SCP_strategies.html>

</details>

---

<a id="t10-q27"></a>
### T10-Q27
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has noticed some suspicious activity in her AWS account and is concerned that the access keys associated with her IAM user account may have been compromised. What is the first thing the Developer do in should do in this situation?

- ▫️ Report the incident to AWS Support
- ▫️ Delete her IAM user account
- ▫️ Change her IAM User account password
- ✅ Delete the compromised access keys

<details><summary>Пояснення (оригінал, EN)</summary>

In this case the Developer’s access keys may have been compromised so the first step would be to invalidate the access keys by deleting them.

The next step would then be to determine if any temporary security credentials have been issued an invalidating those too to prevent any further misuse.

The user account and user account password have not been compromised so they do not need to be deleted / changed as a first step. However, changing the account password would typically be recommended as a best practice in this situation.

CORRECT: "Delete the compromised access keys" is the correct answer.

INCORRECT: "Delete her IAM user account" is incorrect. This user account has not been compromised based on the available information, just the access keys. Deleting the access keys will prevent further misuse of the AWS account.

INCORRECT: "Report the incident to AWS Support" is incorrect is a good practice but not the first step. The Developer should first attempt to mitigate any further misuse of the account by deleting the access keys.

INCORRECT: "Change her IAM User account password" is incorrect as she does not have any evidence that the account has been compromised, just the access keys. However, it would be a good practice to change the password, just not the first thing to do.

Джерела: <https://aws.amazon.com/blogs/security/what-to-do-if-you-inadvertently-expose-an-aws-access-key/>

</details>

---

<a id="t10-q31"></a>
### T10-Q31
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

An online retail platform uses the AWS SDK for Python (Boto3) on the frontend to handle user authentication through AWS Security Token Service (AWS STS). The platform stores its digital assets in an Amazon S3 bucket and delivers them using an Amazon CloudFront distribution, which uses the S3 bucket as its origin.

Currently, the application holds its role credentials in plaintext within a Python file in the application code. The platform developers are looking to improve security by creating a mechanism that enables the application to retrieve user credentials without embedding any credentials in the application code.

What solution would meet these requirements?

- ✅ Integrate a Lambda@Edge function with the CloudFront distribution. Trigger the function upon each viewer request. Give the execution role of the function the required permissions to interact with AWS STS. Shift all SDK calls from the frontend to this function.
- ▫️ Attach a Lambda@Edge function to the CloudFront distribution. Invoke this function with each viewer request. Transfer the credentials from the Python file into this function and move all SDK calls from the frontend into this function.
- ▫️ Configure a CloudFront function within the distribution. Activate the function on viewer request. Grant the function's execution role the necessary permissions to access AWS STS. Relocate all SDK calls from the frontend to this function.
- ▫️ Set up a CloudFront function on the distribution. Initiate this function for each viewer request. Shift the credentials from the Python file into this function and relocate all SDK calls from the frontend into the function.

<details><summary>Пояснення (оригінал, EN)</summary>

The question seeks a solution where the application retrieves user credentials without storing any credentials within the application code. It's a security risk to embed credentials in code; it's safer to employ AWS managed services, like Lambda@Edge, that can securely manage and provide temporary credentials through AWS STS. Lambda@Edge also enables you to run your code closer to your users, reducing latency.

CORRECT: "Integrate a Lambda@Edge function with the CloudFront distribution. Trigger the function upon each viewer request. Give the execution role of the function the required permissions to interact with AWS STS. Shift all SDK calls from the frontend to this function" is the correct answer (as explained above.)

INCORRECT: "Configure a CloudFront function within the distribution. Activate the function on viewer request. Grant the function's execution role the necessary permissions to access AWS STS. Relocate all SDK calls from the frontend to this function" is incorrect.

CloudFront Functions are primarily used to perform lightweight HTTP(s) transformations and manipulations at AWS edge locations. These functions are designed to execute simple tasks like HTTP header manipulations, URL redirects, or cache key normalizations. They do not have permission to access AWS services like AWS STS, nor can they handle the more complex operations required for this use case. Therefore, this option is incorrect.

INCORRECT: "Attach a Lambda@Edge function to the CloudFront distribution. Invoke this function with each viewer request. Transfer the credentials from the Python file into this function and move all SDK calls from the frontend into this function" is incorrect.

This option suggests transferring the plaintext credentials into a Lambda@Edge function, which is not a secure practice. It implies hardcoding the credentials into the Lambda@Edge function, which is discouraged in AWS (or any other environment). Credentials should not be hardcoded because they could be exposed through logs or version control systems, leading to potential security risks. This option does not comply with the security best practices and thus is incorrect.

INCORRECT: "Set up a CloudFront function on the distribution. Initiate this function for each viewer request. Shift the credentials from the Python file into this function and relocate all SDK calls from the frontend into the function" is incorrect.

This option also proposes transferring the plaintext credentials into a CloudFront function, which is an insecure practice. Hardcoding credentials is considered risky as they could be unintentionally exposed, causing potential security vulnerabilities. Moreover, CloudFront Functions cannot access other AWS services and cannot perform complex tasks. Therefore, this option does not meet the security requirements and is incorrect.

Джерела: <https://aws.amazon.com/lambda/edge/>

</details>

---

<a id="t10-q39"></a>
### T10-Q39
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer manages a website running behind an Elastic Load Balancer in the us-east-1 region. The Developer has recently deployed an identical copy of the website in us-west-1 and needs to send 20% of the traffic to the new site.

How can the Developer achieve this requirement?

- ▫️ Use a blue/green deployment with Amazon Elastic Beanstalk
- ▫️ Use a blue/green deployment with Amazon CodeDeploy
- ✅ Use an Amazon Route 53 Weighted Routing Policy
- ▫️ Use an Amazon Route 53 Geolocation Routing Policy

<details><summary>Пояснення (оригінал, EN)</summary>

Weighted routing lets you associate multiple resources with a single domain name (example.com) or subdomain name (acme.example.com) and choose how much traffic is routed to each resource. This can be useful for a variety of purposes, including load balancing and testing new versions of software.

In this case the Developer can use a weighted routing policy to direct 20% of the incoming traffic to the new site as required.

CORRECT: "Use an Amazon Route 53 Weighted Routing Policy" is the correct answer.

INCORRECT: "Use an Amazon Route 53 Geolocation Routing Policy" is incorrect as the Developer should use a weighted routing policy for this requirement as a specified percentage of traffic needs to be directed to the new website.

INCORRECT: "Use a blue/green deployment with Amazon Elastic Beanstalk" is incorrect as the question does not state that Elastic Beanstalk is being used and the new website has already been deployed.

INCORRECT: "Use a blue/green deployment with Amazon CodeDeploy" is incorrect as the question does not state that Amazon CodeDeploy is being used and the website has already been deployed.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-weighted>

</details>

---

<a id="t11-q27"></a>
### T11-Q27
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Зараз рекомендовані trusted key groups (без root).** → [детальніше](../../07-outdated-and-wrong-answers.md#o21)

Which of the following security credentials can only be created by the AWS Account root user?

- ▫️ IAM User passwords
- ▫️ IAM User Access Keys
- ▫️ EC2 Instance Key Pairs
- ✅ CloudFront Key Pairs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

For Amazon CloudFront, you use key pairs to create signed URLs for private content, such as when you want to distribute restricted content that someone paid for.

CloudFront Key Pairs - IAM users can't create CloudFront key pairs. You must log in using root credentials to create key pairs.

To create signed URLs or signed cookies, you need a signer. A signer is either a trusted key group that you create in CloudFront, or an AWS account that contains a CloudFront key pair. AWS recommends that you use trusted key groups with signed URLs and signed cookies instead of using CloudFront key pairs.

Incorrect options:

EC2 Instance Key Pairs - You use key pairs to access Amazon EC2 instances, such as when you use SSH to log in to a Linux instance. These key pairs can be created from the IAM user login and do not need root user access.

IAM User Access Keys - Access keys consist of two parts: an access key ID and a secret access key. You use access keys to sign programmatic requests that you make to AWS if you use AWS CLI commands (using the SDKs) or using AWS API operations. IAM users can create their own Access Keys, does not need root access.

IAM User passwords - Every IAM user has access to his own credentials and can reset the password whenever they need to.

Джерела: <https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-trusted-signers.html>

</details>

---

<a id="t11-q32"></a>
### T11-Q32
*⏭ пропущено · домен: офіційна мітка*

The development team has just configured and attached the IAM policy needed to access AWS Billing and Cost Management for all users under the Finance department. But, the users are unable to see AWS Billing and Cost Management service in the AWS console.

What could be the reason for this issue?

- ▫️ Only root user has access to AWS Billing and Cost Management console
- ▫️ IAM user should be created under AWS Billing and Cost Management and not under AWS account to have access to Billing console
- ▫️ The users might have another policy that restricts them from accessing the Billing information
- ✅ You need to activate IAM user access to the Billing and Cost Management console for all the users who need access

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

You need to activate IAM user access to the Billing and Cost Management console for all the users who need access - By default, IAM users do not have access to the AWS Billing and Cost Management console. You or your account administrator must grant users access. You can do this by activating IAM user access to the Billing and Cost Management console and attaching an IAM policy to your users. Then, you need to activate IAM user access for IAM policies to take effect. You only need to activate IAM user access once.

Incorrect options:

The users might have another policy that restricts them from accessing the Billing information - This is an incorrect option, as deduced from the given use-case.

Only root user has access to AWS Billing and Cost Management console - This is an incorrect statement. AWS Billing and Cost Management access can be provided to any user through user activation and policies, as discussed above.

IAM user should be created under AWS Billing and Cost Management and not under the AWS account to have access to Billing console - IAM is a feature of your AWS account. All IAM users are created and managed from a single place, irrespective of the services they wish to you.

Reference:

https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/control-access-billing.html

</details>

---

<a id="t11-q51"></a>
### T11-Q51
*⏭ пропущено · домен: офіційна мітка*

A development team lead is responsible for managing access for her IAM principals. At the start of the cycle, she has granted excess privileges to users to keep them motivated for trying new things. She now wants to ensure that the team has only the minimum permissions required to finish their work.

Which of the following will help her identify unused IAM roles and remove them without disrupting any service?

- ▫️ AWS Security Hub
- ▫️ AWS Trusted Advisor
- ▫️ Amazon Inspector
- ✅ IAM Access Analyzer

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

IAM Access Analyzer

IAM Access Analyzer simplifies inspecting unused access to guide you toward least privilege. Security teams can use IAM Access Analyzer to gain visibility into unused access across their AWS organization and automate how they rightsize permissions. When the unused access analyzer is enabled, IAM Access Analyzer continuously analyzes your accounts to identify unused access and creates a centralized dashboard with findings. The findings highlight unused roles, unused access keys for IAM users, and unused passwords for IAM users. For active IAM roles and users, the findings provide visibility into unused services and actions.

Incorrect options:

AWS Trusted Advisor - AWS Trusted Advisor is an online tool that provides you real-time guidance to help you provision your resources following AWS best practices on cost optimization, security, fault tolerance, service limits, and performance improvement.

Amazon Inspector - Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS. Amazon Inspector automatically assesses applications for exposure, vulnerabilities, and deviations from best practices.

AWS Security Hub - AWS Security Hub provides you with a comprehensive view of your security state in AWS and helps you assess your AWS environment against security industry standards and best practices.

Reference:

https://aws.amazon.com/iam/access-analyzer/

</details>

---

<a id="t11-q60"></a>
### T11-Q60
*⏭ пропущено · домен: офіційна мітка*

The manager at an IT company wants to set up member access to user-specific folders in an Amazon S3 bucket - bucket-a. So, user x can only access files in his folder - bucket-a/user/user-x/ and user y can only access files in her folder - bucket-a/user/user-y/ and so on.

As a Developer Associate, which of the following IAM constructs would you recommend so that the policy snippet can be made generic for all team members and the manager does not need to create separate IAM policy for each team member?

- ✅ IAM policy variables
- ▫️ IAM policy condition
- ▫️ IAM policy principal
- ▫️ IAM policy resource

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

IAM policy variables

Instead of creating individual policies for each user, you can use policy variables and create a single policy that applies to multiple users (a group policy). Policy variables act as placeholders. When you make a request to AWS, the placeholder is replaced by a value from the request when the policy is evaluated.

As an example, the following policy gives each of the users in the group full programmatic access to a user-specific object (their own "home directory") in Amazon S3.

 via - https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html

Incorrect options:

IAM policy principal - You can use the Principal element in a policy to specify the principal that is allowed or denied access to a resource (In IAM, a principal is a person or application that can make a request for an action or operation on an AWS resource. The principal is authenticated as the AWS account root user or an IAM entity to make requests to AWS). You cannot use the Principal element in an IAM identity-based policy. You can use it in the trust policies for IAM roles and in resource-based policies.

IAM policy condition - The Condition element (or Condition block) lets you specify conditions for when a policy is in effect, like so - "Condition" : { "StringEquals" : { "aws:username" : "johndoe" }}. This can not be used to address the requirements of the given use-case.

IAM policy resource - The Resource element specifies the object or objects that the statement covers. You specify a resource using an ARN. This can not be used to address the requirements of the given use-case.

Reference:

https://aws.amazon.com/blogs/security/writing-iam-policies-grant-access-to-user-specific-folders-in-an-amazon-s3-bucket/

</details>

---
