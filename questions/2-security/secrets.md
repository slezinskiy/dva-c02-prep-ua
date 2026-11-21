# Secrets Manager / Parameter Store

**Домен 2 — Безпека** · питань: 10  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#secrets) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q10"></a>
### T03-Q10
*⏭ пропущено · домен: офіційна мітка*

You are planning to build a fleet of EBS-optimized EC2 instances to handle the load of your new application. Due to security compliance, your organization wants any secret strings used in the application to be encrypted to prevent exposing values as clear text.

The solution requires that decryption events be audited and API calls to be simple. How can this be achieved? (select two)

- ▫️ Store the secret as PlainText in SSM Parameter Store
- ✅ Store the secret as SecureString in SSM Parameter Store
- ▫️ Encrypt first with KMS then store in SSM Parameter store
- ▫️ Audit using SSM Audit Trail
- ✅ Audit using CloudTrail

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Store the secret as SecureString in SSM Parameter Store

With AWS Systems Manager Parameter Store, you can create SecureString parameters, which are parameters that have a plaintext parameter name and an encrypted parameter value. Parameter Store uses AWS KMS to encrypt and decrypt the parameter values of Secure String parameters. Also, if you are using customer-managed CMKs, you can use IAM policies and key policies to manage to encrypt and decrypt permissions. To retrieve the decrypted value you only need to do one API call.

 via - https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html

Audit using CloudTrail

AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account. With CloudTrail, you can log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. CloudTrail provides an event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command-line tools, and other AWS services.

CloudTrail will allow you to see all API calls made to SSM and KMS.

Incorrect options:

Encrypt first with KMS then store in SSM Parameter store - This could work but will require two API calls to get the decrypted value instead of one. So this is not the right option.

Store the secret as PlainText in SSM Parameter Store - Plaintext parameters are not secure and shouldn't be used to store secrets.

Audit using SSM Audit Trail - This is a made-up option and has been added as a distractor.

Reference:

https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html

</details>

---

<a id="t05-q12"></a>
### T05-Q12
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application runs on a fleet of Amazon EC2 instances and stores data in a Microsoft SQL Server database hosted on Amazon RDS. The developer wants to avoid storing database connection credentials the application code. The developer would also like a solution that automatically rotates the credentials.

What is the MOST secure way to store and access the database credentials?

- ▫️ Create an IAM role that has permissions to access the database. Attach the role to the EC2 instance.
- ✅ Use AWS Secrets Manager to store the credentials. Retrieve the credentials from Secrets Manager as needed.
- ▫️ Use AWS Systems Manager Parameter store to store the credentials. Enable automatic rotation of the credentials.
- ▫️ Store the credentials in an encrypted source code repository. Retrieve the credentials from AWS CodeCommit as needed.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Secrets Manager can be used for secure storage of secrets such as database connection credentials. Automatic rotation is supported for several RDS database types including Microsoft SQL Server. This is the most secure solution for storing and retrieving the credentials.

CORRECT: "Use AWS Secrets Manager to store the credentials. Retrieve the credentials from Secrets Manager as needed" is the correct answer (as explained above.)

INCORRECT: "Use AWS Systems Manager Parameter store to store the credentials. Enable automatic rotation of the credentials" is incorrect.

With SSM Parameter Store you cannot enable automatic rotation. You can rotate the credentials but you would need to configure your own Lambda function.

INCORRECT: "Create an IAM role that has permissions to access the database. Attach the role to the EC2 instance" is incorrect.

RDS for SQL Server does support windows authentication using a managed Microsoft AD with IAM roles for permissions to the AD service, but this is not described in the solution.

INCORRECT: "Store the credentials in an encrypted source code repository. Retrieve the credentials from AWS CodeCommit as needed" is incorrect.

This is not a solution that is suitable for retrieving database connection credentials and it does not support automatic rotation.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html>

</details>

---

<a id="t05-q55"></a>
### T05-Q55
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application will use AWS Lambda and an Amazon RDS database. The Developer needs to secure the database connection string and enable automatic rotation every 30 days. What is the SIMPLEST way to achieve this requirement?

- ▫️ Store the connection string in an encrypted Amazon S3 bucket and use a scheduled CloudWatch Event to update the connection string every 30 days
- ▫️ Store a SecureString in Systems Manager Parameter Store and enable automatic rotation every 30 days
- ▫️ Store the connection string as an encrypted environment variable in Lambda and create a separate function that rotates the connection string every 30 days
- ✅ Store a secret in AWS Secrets Manager and enable automatic rotation every 30 days

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Secrets Manager encrypts secrets at rest using encryption keys that you own and store in AWS Key Management Service (KMS). When you retrieve a secret, Secrets Manager decrypts the secret and transmits it securely over TLS to your local environment.

With AWS Secrets Manager, you can rotate secrets on a schedule or on demand by using the Secrets Manager console, AWS SDK, or AWS CLI.

For example, to rotate a database password, you provide the database type, rotation frequency, and master database credentials when storing the password in Secrets Manager. Secrets Manager natively supports rotating credentials for databases hosted on Amazon RDS and Amazon DocumentDB and clusters hosted on Amazon Redshift.

CORRECT: "Store a secret in AWS Secrets Manager and enable automatic rotation every 30 days" is the correct answer.

INCORRECT: "Store a SecureString in Systems Manager Parameter Store and enable automatic rotation every 30 days" is incorrect as SSM Parameter Store does not support automatic key rotation.

INCORRECT: "Store the connection string as an encrypted environment variable in Lambda and create a separate function that rotates the connection string every 30 days" is incorrect as this is not the simplest solution. In this scenario using AWS Secrets Manager would be easier to implement as it provides native features for rotating the secret.

INCORRECT: "Store the connection string in an encrypted Amazon S3 bucket and use a scheduled CloudWatch Event to update the connection string every 30 days" is incorrect. There is no native capability of CloudWatch to update connection strings so you would need some other service such as a Lambda function to execute and rotate the connection string which is missing from this answer.

Джерела: <https://aws.amazon.com/secrets-manager/features/>

</details>

---

<a id="t06-q39"></a>
### T06-Q39
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

> ⚠️ **Secrets Manager (Parameter Store відсутній у варіантах).** → [детальніше](../../06-contradictions-and-traps.md#c06)

A programmer is rolling out a new solution to Amazon ECS. This solution necessitates secure handling and access to diverse variables such as credentials, remote API authentication details, and the API URL. The remote API authentication information and API URL should be accessible across all versions of the solution, spanning development, testing, and production environments.

What is the most efficient method for the programmer to access these variables with minimal modifications to the solution?

- ▫️ Include these variables directly in the application code and use version control system to manage different versions.
- ❌ Create environment variables for these details and access them within the application code.
- ▫️ Use AWS S3 to store these variables and access them via AWS SDK within the application.
- ✅ Utilize AWS Secrets Manager to store these variables and retrieve them directly within the application using AWS SDK.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Secrets Manager is designed to handle sensitive data like this. It enables you to easily rotate, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle. Using AWS Secrets Manager, you can secure and manage secrets used to access resources in the AWS Cloud, on third-party services, and on-premises.

CORRECT: "Utilize AWS Secrets Manager to store these variables and retrieve them directly within the application using AWS SDK" is the correct answer (as explained above.)

INCORRECT: "Include these variables directly in the application code and use version control system to manage different versions" is incorrect.

Including sensitive data directly in the application code is not a secure practice and doesn't comply with the principle of least privilege or best security practices.

INCORRECT: "Create environment variables for these details and access them within the application code" is incorrect.

Although environment variables can be used to pass some configuration to the application, they're typically visible in plaintext in multiple places such as ECS Task Definitions or the ECS console. This makes them unsuitable for sensitive data like credentials or API keys.

INCORRECT: "Use AWS S3 to store these variables and access them via AWS SDK within the application" is incorrect.

AWS S3 is primarily a storage service and using it to store and manage secrets doesn't align with security best practices.

Джерела: <https://aws.amazon.com/secrets-manager/>

</details>

---

<a id="t06-q44"></a>
### T06-Q44
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A developer wants to use a cron job to schedule key rotation for an Amazon RDS database. What steps should the developer take to complete this task?

- ▫️ Use AWS Identity and Access Management (IAM) to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.
- ✅ Use AWS Secrets Manager to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.
- ❌ Use AWS Systems Manager (SSM) Parameter Store to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.
- ▫️ Use AWS Relational Database Service to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression.

<details><summary>Пояснення (оригінал, EN)</summary>

Rotation is the process of periodically updating a secret. When you rotate a secret, you update the credentials in both the secret and the database or service. In Secrets Manager, you can set up automatic rotation for your secrets.

Some services offer managed rotation, where the service configures and manages rotation for you. With managed rotation, you don't use an AWS Lambda function to update the secret and the credentials in the database. Amazon RDS can be used with managed rotation.

CORRECT: "Use AWS Secrets Manager to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression" is the correct answer (as explained above.)

INCORRECT: "Use AWS Identity and Access Management (IAM) to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression" is incorrect. IAM is an identity and management services to help assign permissions to users, groups and roles. It does not manage secrets such as passwords or access keys.

INCORRECT: "Use AWS Relational Database Service to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression" is incorrect. RDS is a used to simplify the set-up of relational databases. It is not used for managing secrets.

INCORRECT: "Use AWS Systems Manager Parameter Store to create a managed rotation under the Rotation Schedule and schedule the time for the automatic rotation using a cron expression" is incorrect. The SSM Parameter store does not use cron jobs to manage rotations.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotate-secrets_schedule.html>

</details>

---

<a id="t09-q17"></a>
### T09-Q17
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Parameter Store (Secrets Manager відсутній у варіантах).** → [детальніше](../../06-contradictions-and-traps.md#c06)

A developer wants to securely store and retrieve various types of variables, such as remote API authentication information, API URL, and related credentials across different environments of an application deployed on Amazon Elastic Container Service (Amazon ECS).

What would be the best approach that needs minimal modifications in the application code?

- ▫️ Configure the application to fetch the variables from AWS KMS by storing the API URL and credentials as unique keys in KMS for each environment
- ▫️ Configure the application to fetch the variables from each of the deployed environments by defining the authentication information and API URL in the ECS task definition as unique names during the deployment process
- ▫️ Configure the application to fetch the variables from an encrypted file that is stored with the application by storing the API URL and credentials in unique files for each environment
- ✅ Configure the application to fetch the variables and credentials from AWS Systems Manager Parameter Store by leveraging hierarchical unique paths in Parameter Store for each variable in each environment

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure the application to fetch the variables and credentials from AWS Systems Manager Parameter Store by leveraging hierarchical unique paths in Parameter Store for each variable in each environment

Parameter Stores is a capability of AWS Systems Manager that provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, Amazon Machine Image (AMI) IDs, and license codes as parameter values. You can store values as plain text or encrypted data. You can reference Systems Manager parameters in your scripts, commands, SSM documents, and configuration and automation workflows by using the unique name that you specified when you created the parameter.

Managing dozens or hundreds of parameters as a flat list is time-consuming and prone to errors. It can also be difficult to identify the correct parameter for a task. This means you might accidentally use the wrong parameter, or you might create multiple parameters that use the same configuration data.

You can use parameter hierarchies to help you organize and manage parameters. A hierarchy is a parameter name that includes a path that you define by using forward slashes (/).

 via - https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-hierarchies.html

Incorrect options:

Configure the application to fetch the variables from AWS KMS by storing the API URL and credentials as unique keys in KMS for each environment - AWS KMS lets you create, manage, and control cryptographic keys across your applications and AWS services. KMS is not a key-value service that can be used for the given use case.

Configure the application to fetch the variables from an encrypted file that is stored with the application by storing the API URL and credentials in unique files for each environment - It is not considered a security best practice to store sensitive data and credentials in an encrypted file with the application. So this option is incorrect.

Configure the application to fetch the variables from each of the deployed environments by defining the authentication information and API URL in the ECS task definition as unique names during the deployment process - ECS task definition can be thought of as a blueprint for your application. Task definitions specify various parameters for your application. Examples of task definition parameters are which containers to use, which launch type to use, which ports should be opened for your application, and what data volumes should be used with the containers in the task. The specific parameters available for the task definition depend on which launch type you are using. The task definition is a text file, in JSON format, that describes one or more containers, up to a maximum of ten, that form your application. A task is the instantiation of a task definition within a cluster. After you create a task definition for your application within Amazon ECS, you can specify the number of tasks to run on your cluster.

AWS recommends storing your sensitive data in either AWS Secrets Manager secrets or AWS Systems Manager Parameter Store parameters. Environment variables specified in the task definition are readable by all users and roles that are allowed the DescribeTaskDefinition action for the task definition. So this option is incorrect.

 via - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/taskdef-envfiles.html

Джерела: <https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-hierarchies.html> · <https://aws.amazon.com/kms/> · <https://ecsworkshop.com/introduction/ecs_basics/task_definition/> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/taskdef-envfiles.html>

</details>

---

<a id="t09-q22"></a>
### T09-Q22
*⏭ пропущено · домен: офіційна мітка*

A company wants to share information with a third party via an HTTP API endpoint managed by the third party. The company has the necessary API key to access the endpoint and the integration of the API key with the company's application code must not impact the application's performance.

What is the most secure approach?

- ▫️ Keep the API credentials in an encrypted file in S3 and use the credentials to make the API call by fetching the API credentials from S3 at runtime by using the AWS SDK
- ▫️ Keep the API credentials in an encrypted table in MySQL RDS and use the credentials to make the API call by fetching the API credentials from RDS at runtime by using the AWS SDK
- ▫️ Keep the API credentials in a local code variable and use the local code variable at runtime to make the API call
- ✅ Keep the API credentials in AWS Secrets Manager and use the credentials to make the API call by fetching the API credentials at runtime by using the AWS SDK

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Keep the API credentials in AWS Secrets Manager and use the credentials to make the API call by fetching the API credentials at runtime by using the AWS SDK

Secrets Manager enables you to replace hardcoded credentials in your code, including passwords, with an API call to Secrets Manager to retrieve the secret programmatically. This helps ensure the secret can't be compromised by someone examining your code, because the secret no longer exists in the code. Also, you can configure Secrets Manager to automatically rotate the secret for you according to a specified schedule. This enables you to replace long-term secrets with short-term ones, significantly reducing the risk of compromise.

 via - https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html

In the past, when you created a custom application to retrieve information from a database, you typically embedded the credentials, the secret, for accessing the database directly in the application. When the time came to rotate the credentials, you had to do more than just create new credentials. You had to invest time to update the application to use the new credentials. Then you distributed the updated application. If you had multiple applications with shared credentials and you missed updating one of them, the application failed. Because of this risk, many customers choose not to regularly rotate credentials, which effectively substitutes one risk for another. You can also use caching with Secrets Manager to significantly improve the availability and latency of applications.

Incorrect options:

Keep the API credentials in an encrypted table in MySQL RDS and use the credentials to make the API call by fetching the API credentials from RDS at runtime by using the AWS SDK

Keep the API credentials in an encrypted file in S3 and use the credentials to make the API call by fetching the API credentials from S3 at runtime by using the AWS SDK

Keep the API credentials in a local code variable and use the local code variable at runtime to make the API call

It is considered a security bad practice to keep sensitive access credentials in code, database, or a flat file on a file system or object storage. Therefore, all three options are incorrect.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html> · <https://aws.amazon.com/blogs/security/improve-availability-and-latency-of-applications-by-using-aws-secret-managers-python-client-side-caching-library/>

</details>

---

<a id="t10-q14"></a>
### T10-Q14
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A healthcare service wants to exchange patient data securely with a partner organization through an HTTP API endpoint provided by the partner. The healthcare service has the requisite API key for accessing the HTTP API. The service needs a solution to manage the API key through code.

Which method will fulfill these requirements with maximum security?

- ▫️ Store the API key in an environment variable on the application server.
- ▫️ Store the API key in the application code.
- ✅ Use AWS Secrets Manager to store and retrieve the API key.
- ▫️ Use Amazon DynamoDB to store the API key and retrieve it when needed.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Secrets Manager is a secrets management service that helps you protect access to your applications, services, and IT resources. This service enables you to easily rotate, manage, and retrieve API keys, database credentials, and other secrets throughout their lifecycle, making it the most secure option.

CORRECT: "Use AWS Secrets Manager to store and retrieve the API key" is the correct answer (as explained above.)

INCORRECT: "Store the API key in the application code" is incorrect.

Storing the API key directly in the application code is a security risk and could lead to unintended exposure of the key.

INCORRECT: "Use Amazon DynamoDB to store the API key and retrieve it when needed" is incorrect.

While Amazon DynamoDB could be used to store the API key, it does not provide the same level of security management and key rotation capabilities that AWS Secrets Manager offers.

INCORRECT: "Store the API key in an environment variable on the application server" is incorrect.

Storing the API key in an environment variable on the application server could lead to unintentional exposure of the key if the environment variables were logged or leaked in some way.

Джерела: <https://aws.amazon.com/secrets-manager/>

</details>

---

<a id="t10-q61"></a>
### T10-Q61
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An organization is developing a data processing application that is hosted on AWS Lambda and utilizes a PostgreSQL database on Amazon RDS. The security team mandates a policy that requires rotating database credentials every week.

What strategy should the developer adopt to manage the database credentials for the application?

- ▫️ Enable IAM database authentication and manage weekly rotation manually.
- ✅ Deploy AWS Secrets Manager to store database credentials and set up automatic weekly rotation.
- ▫️ Store database credentials in Amazon S3 buckets with versioning enabled, rotating, and uploading new credentials weekly.
- ▫️ Store the credentials in environment variables of the Lambda function and manually update them every week.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Secrets Manager enables the creation, retrieval, and rotation of secrets like database credentials. Configuring automatic rotation weekly satisfies the security team's requirement, with the least manual effort.

CORRECT: "Deploy AWS Secrets Manager to store database credentials and set up automatic weekly rotation" is the correct answer (as explained above.)

INCORRECT: "Store database credentials in Amazon S3 buckets with versioning enabled, rotating and uploading new credentials weekly" is incorrect.

Storing credentials in Amazon S3 is not a secure practice as it does not have built-in mechanisms for rotating secrets.

INCORRECT: "Enable IAM database authentication and manage weekly rotation manually" is incorrect.

IAM database authentication allows you to use IAM users/roles to access your database, but it does not have an automatic mechanism for weekly rotation of IAM credentials.

INCORRECT: "Store the credentials in environment variables of the Lambda function and manually update them every week" is incorrect.

Storing credentials as environment variables in AWS Lambda functions could expose them to potential security risks, and manual rotation weekly would be labor-intensive.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotate-secrets_managed.html>

</details>

---

<a id="t11-q31"></a>
### T11-Q31
*⏭ пропущено · домен: офіційна мітка*

You are running workloads on AWS and have embedded RDS database connection strings within each web server hosting your applications. After failing a security audit, you are looking at a different approach to store your secrets securely and automatically rotate the database credentials.

Which AWS service can you use to address this use-case?

- ✅ Secrets Manager
- ▫️ Systems Manager
- ▫️ KMS
- ▫️ SSM Parameter Store

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Secrets Manager

AWS Secrets Manager enables you to easily rotate, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle. Users and applications retrieve secrets with a call to Secrets Manager APIs, eliminating the need to hardcode sensitive information in plain text. Secrets Manager offers secret rotation with built-in integration for Amazon RDS, Amazon Redshift, and Amazon DocumentDB.

Benefits of Secrets Manager:  via - https://aws.amazon.com/secrets-manager/

Incorrect options:

SSM Parameter Store - AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, and license codes as parameter values. SSM Parameter Store cannot be used to automatically rotate the database credentials.

Systems Manager - AWS Systems Manager gives you visibility and control of your infrastructure on AWS. Systems Manager provides a unified user interface so you can view operational data from multiple AWS services and allows you to automate operational tasks across your AWS resources. Systems Manager cannot be used to store your secrets securely and automatically rotate the database credentials.

KMS - AWS Key Management Service (KMS) makes it easy for you to create and manage cryptographic keys and control their use across a wide range of AWS services and in your applications. KMS cannot be used to store your secrets securely and automatically rotate the database credentials.

Джерела: <https://aws.amazon.com/secrets-manager/> · <https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-paramstore.html>

</details>

---
