# AWS Lambda

**Домен 2 — Безпека** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t08-q23"></a>
### T08-Q23
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

An AWS Lambda function requires several environment variables with secret values. The secret values should be obscured in the Lambda console and API output even for users who have permission to use the key.

What is the best way to achieve this outcome and MINIMIZE complexity and latency?

- ▫️ Store the encrypted values in an encrypted Amazon S3 bucket and reference them from within the code
- ✅ Encrypt the secret values client-side using encryption helpers
- ▫️ Encrypt the secret values with a customer-managed CMK
- ▫️ Use an external encryption infrastructure to encrypt the values and add them as environment variables

<details><summary>Пояснення (оригінал, EN)</summary>

You can use environment variables to store secrets securely for use with Lambda functions. Lambda always encrypts environment variables at rest.

Additionally, you can use the following features to customize how environment variables are encrypted.

• Key configuration – On a per-function basis, you can configure Lambda to use an encryption key that you create and manage in AWS Key Management Service. These are referred to as customer managed customer master keys (CMKs) or customer managed keys. If you don't configure a customer managed key, Lambda uses an AWS managed CMK named aws/lambda, which Lambda creates in your account.

• Encryption helpers – The Lambda console lets you encrypt environment variable values client side, before sending them to Lambda. This enhances security further by preventing secrets from being displayed unencrypted in the Lambda console, or in function configuration that's returned by the Lambda API. The console also provides sample code that you can adapt to decrypt the values in your function handler.

The configuration for using encryption helps to encrypt data client-side looks like this:

This is the best way to achieve this outcome and minimizes complexity as the encryption infrastructure will still use AWS KMS and be able to decrypt the values during function execution.

CORRECT: "Encrypt the secret values client-side using encryption helpers" is the correct answer.

INCORRECT: "Encrypt the secret values with a customer-managed CMK" is incorrect as this alone will not achieve the desired outcome as the environment variables should be encrypted client-side with the encryption helper to ensure users cannot see the secret values.

INCORRECT: "Store the encrypted values in an encrypted Amazon S3 bucket and reference them from within the code" is incorrect as this would introduce complexity and latency.

INCORRECT: "Use an external encryption infrastructure to encrypt the values and add them as environment variables" is incorrect as this would introduce complexity and latency.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/security-dataprotection.html>

</details>

---

<a id="t08-q63"></a>
### T08-Q63
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

> ⚠️ **Надлишково: PutItem не потрібен — UpdateItem створює item.** → [детальніше](../../06-contradictions-and-traps.md#c17)

A Developer is working on an AWS Lambda function that accesses Amazon DynamoDB. The Lambda function must retrieve an item and update some of its attributes or create the item if it does not exist. The Lambda function has access to the primary key.

Which IAM permission should the Developer request for the Lambda function to achieve this functionality?

- ✅ “dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”
- ▫️ “dynamodb:GetRecords”, “dynamodb:PutItem”, and “dynamodb:UpdateTable”
- ▫️ “dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:DescribeTable”
- ▫️ “dynamodb:DeleteItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”

<details><summary>Пояснення (оригінал, EN)</summary>

The Developer needs the permissions to retrieve items, update/modify items, and create items. Therefore permissions for the following API actions are required:

• GetItem - The GetItem operation returns a set of attributes for the item with the given primary key.

• UpdateItem - Edits an existing item's attributes, or adds a new item to the table if it does not already exist. You can put, delete, or add attribute values.

• PutItem - Creates a new item, or replaces an old item with a new item. If an item that has the same primary key as the new item already exists in the specified table, the new item completely replaces the existing item.

CORRECT: "“dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”" is the correct answer.

INCORRECT: "“dynamodb:DeleteItem”, “dynamodb:GetItem”, and “dynamodb:PutItem”" is incorrect as the Developer does not need the permission to delete items.

INCORRECT: "“dynamodb:UpdateItem”, “dynamodb:GetItem”, and “dynamodb:DescribeTable”" is incorrect as the Developer does not need to return information about the table (DescribeTable) such as the current status of the table, when it was created, the primary key schema, and any indexes on the table.

INCORRECT: "“dynamodb:GetRecords”, “dynamodb:PutItem”, and “dynamodb:UpdateTable”" is incorrect as GetRecords is not a valid API action/permission for DynamoDB.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Operations.html>

</details>

---

<a id="t09-q51"></a>
### T09-Q51
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **STS не є механізмом авторизації API Gateway.** → [детальніше](../../06-contradictions-and-traps.md#c20)

A developer is looking at establishing access control for an API that connects to a Lambda function downstream.

Which of the following represents a mechanism that CANNOT be used for authenticating with the API Gateway?

- ▫️ Cognito User Pools
- ✅ AWS Security Token Service (STS)
- ▫️ Lambda Authorizer
- ▫️ Standard AWS IAM roles and policies

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Amazon API Gateway is an AWS service for creating, publishing, maintaining, monitoring, and securing REST, HTTP, and WebSocket APIs at any scale. API developers can create APIs that access AWS or other web services, as well as data stored in the AWS Cloud.

How API Gateway Works:  via - https://aws.amazon.com/api-gateway/

AWS Security Token Service (STS) - AWS Security Token Service (AWS STS) is a web service that enables you to request temporary, limited-privilege credentials for AWS Identity and Access Management (IAM) users or for users that you authenticate (federated users). However, it is not supported by API Gateway.

API Gateway supports the following mechanisms for authentication and authorization:  via - https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html

Incorrect options:

Standard AWS IAM roles and policies - Standard AWS IAM roles and policies offer flexible and robust access controls that can be applied to an entire API or individual methods. IAM roles and policies can be used for controlling who can create and manage your APIs, as well as who can invoke them.

Lambda Authorizer - Lambda authorizers are Lambda functions that control access to REST API methods using bearer token authentication—as well as information described by headers, paths, query strings, stage variables, or context variables request parameters. Lambda authorizers are used to control who can invoke REST API methods.

Cognito User Pools - Amazon Cognito user pools let you create customizable authentication and authorization solutions for your REST APIs. Amazon Cognito user pools are used to control who can invoke REST API methods.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html> · <https://docs.aws.amazon.com/STS/latest/APIReference/welcome.html>

</details>

---

<a id="t11-q34"></a>
### T11-Q34
*⏭ пропущено · домен: офіційна мітка*

The development team at a company creates serverless solutions using AWS Lambda. Functions are invoked by clients via AWS API Gateway which anyone can access. The team lead would like to control access using a 3rd party authorization mechanism.

As a Developer Associate, which of the following options would you recommend for the given use-case?

- ✅ Lambda Authorizer
- ▫️ Cognito User Pools
- ▫️ API Gateway User Pools
- ▫️ IAM permissions with sigv4

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"Lambda Authorizer"

An Amazon API Gateway Lambda authorizer (formerly known as a custom authorizer) is a Lambda function that you provide to control access to your API. A Lambda authorizer uses bearer token authentication strategies, such as OAuth or SAML. Before creating an API Gateway Lambda authorizer, you must first create the AWS Lambda function that implements the logic to authorize and, if necessary, to authenticate the caller.

 via - https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html

Incorrect options:

"IAM permissions with sigv4" - Signature Version 4 is the process to add authentication information to AWS requests sent by HTTP. You will still need to provide permissions but our requirements have a need for 3rd party authentication which is where Lambda Authorizer comes in to play.

"Cognito User Pools" - A Cognito user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito, or federate through a third-party identity provider (IdP). Whether your users sign-in directly or through a third party, all members of the user pool have a directory profile that you can access through an SDK. This is managed by AWS, therefore, does not meet our requirements.

"API Gateway User Pools" - This is a made-up option, added as a distractor.

Reference:

https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html

</details>

---

<a id="t11-q63"></a>
### T11-Q63
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Lambda alias 10%.** → [детальніше](../../06-contradictions-and-traps.md#c13)

A development team at a social media company uses AWS Lambda functions for its serverless stack on AWS Cloud. For a new deployment, the Team Lead wants to send only a certain portion of the traffic to a new version of a Lambda function. In case the deployment goes wrong, the solution should also support the ability to roll back to a previous version of the Lambda function, with MIMINUM downtime for the application.

As a Developer Associate, which of the following options would you recommend to address this use-case?

- ✅ Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure the alias to send 10% of the users to this new version. If the deployment goes wrong, reset the alias to point all traffic to the current version
- ▫️ Set up the application to have multiple alias of the Lambda function. Deploy the new version of the code. Configure a new alias that points to the current alias of the Lambda function for handling 10% of the traffic. If the deployment goes wrong, reset the new alias to point all traffic to the most recent working alias of the Lambda function
- ▫️ Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure alias to send all users to this new version. If the deployment goes wrong, reset the alias to point to the current version
- ▫️ Set up the application to directly deploy the new Lambda version. If the deployment goes wrong, reset the application back to the current version using the version number in the ARN

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure the alias to send 10% of the users to this new version. If the deployment goes wrong, reset the alias to point all traffic to the current version

You can use versions to manage the deployment of your AWS Lambda functions. For example, you can publish a new version of a function for beta testing without affecting users of the stable production version. You can change the function code and settings only on the unpublished version of a function. When you publish a version, the code and most of the settings are locked to ensure a consistent experience for users of that version.

You can create one or more aliases for your AWS Lambda function. A Lambda alias is like a pointer to a specific Lambda function version. You can use routing configuration on an alias to send a portion of traffic to a Lambda function version. For example, you can reduce the risk of deploying a new version by configuring the alias to send most of the traffic to the existing version, and only a small percentage of traffic to the new version.

 via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html

 via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html

 via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html

Incorrect options:

Set up the application to use an alias that points to the current version. Deploy the new version of the code and configure alias to send all users to this new version. If the deployment goes wrong, reset the alias to point to the current version - In this case, the application uses an alias to send all traffic to the new version which does not meet the requirement of sending only a certain portion of the traffic to the new Lambda version. In addition, if the deployment goes wrong, the application would see a downtime. Hence this option is incorrect.

Set up the application to directly deploy the new Lambda version. If the deployment goes wrong, reset the application back to the current version using the version number in the ARN - In this case, the application sends all traffic to the new version which does not meet the requirement of sending only a certain portion of the traffic to the new Lambda version. In addition, if the deployment goes wrong, the application would see a downtime. Hence this option is incorrect.

Set up the application to have multiple alias of the Lambda function. Deploy the new version of the code. Configure a new alias that points to the current alias of the Lambda function for handling 10% of the traffic. If the deployment goes wrong, reset the new alias to point all traffic to the most recent working alias of the Lambda function - This option has been added as a distractor. The alias for a Lambda function can only point to a Lambda function version. It cannot point to another alias.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html>

</details>

---
