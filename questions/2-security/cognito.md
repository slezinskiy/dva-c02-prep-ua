# Amazon Cognito

**Домен 2 — Безпека** · питань: 16  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#cognito) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q26"></a>
### T02-Q26
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is creating a web application that will be used by employees working from home. The company uses a SAML directory on-premises for storing user information. The Developer must integrate with the SAML directory and authorize each employee to access only their own data when using the application.

Which approach should the Developer take?

- ▫️ Create a unique IAM role for each employee and have each employee assume the role to access the application so they can access their personal data only.
- ▫️ Create the application within an Amazon VPC and use a VPC endpoint with a trust policy to grant access to the employees.
- ❌ Use Amazon Cognito user pools, federate with the SAML provider, and use user pool groups with an IAM policy.
- ✅ Use an Amazon Cognito identity pool, federate with the SAML provider, and use a trust policy with an IAM condition key to limit employee access.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito leverages IAM roles to generate temporary credentials for your application's users. Access to permissions is controlled by a role's trust relationships.

In this example the Developer must limit access to specific identities in the SAML directory. The Developer can create a trust policy with an IAM condition key that limits access to a specific set of app users by checking the value of cognito-identity.amazonaws.com:sub:

CORRECT: "Use an Amazon Cognito identity pool, federate with the SAML provider, and use a trust policy with an IAM condition key to limit employee access" is the correct answer.

INCORRECT: "Use Amazon Cognito user pools, federate with the SAML provider, and use user pool groups with an IAM policy" is incorrect. A user pool can be used to authenticate but the identity pool is used to provide authorized access to AWS services.

INCORRECT: "Create the application within an Amazon VPC and use a VPC endpoint with a trust policy to grant access to the employees" is incorrect. You cannot provide access to an on-premises SAML directory using a VPC endpoint.

INCORRECT: "Create a unique IAM role for each employee and have each employee assume the role to access the application so they can access their personal data only" is incorrect. This is not an integration into the SAML directory and would be very difficult to manage.

Джерела: <https://docs.aws.amazon.com/cognito/latest/Developerguide/role-trust-and-permissions.html> · <https://docs.aws.amazon.com/cognito/latest/Developerguide/iam-roles.html>

</details>

---

<a id="t04-q27"></a>
### T04-Q27
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A mobile application has thousands of users. Each user may use multiple devices to access the application. The Developer wants to assign unique identifiers to these users regardless of the device they use.

Which of the below is the BEST method to obtain unique identifiers?

- ✅ Implement developer-authenticated identities by using Amazon Cognito and get credentials for these identities
- ▫️ Create a user table in Amazon DynamoDB with key-value pairs of users and their devices. Use these keys as unique identifiers
- ▫️ Use IAM-generated access key IDs for the users as the unique identifier, but do not store secret keys
- ▫️ Assign IAM users and roles to the users. Use the unique IAM resource ID as the unique identifier

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. Amazon Cognito provides authentication, authorization, and user management for your web and mobile apps.

Amazon Cognito identity pools enable you to create unique identities for your users and authenticate them with identity providers. With an identity, you can obtain temporary, limited-privilege AWS credentials to access other AWS services.

Amazon Cognito supports developer authenticated identities, in addition to web identity federation through Facebook (Identity Pools), Google (Identity Pools), and Login with Amazon (Identity Pools).

With developer authenticated identities, you can register and authenticate users via your own existing authentication process, while still using Amazon Cognito to synchronize user data and access AWS resources. Using developer authenticated identities involves interaction between the end user device, your backend for authentication, and Amazon Cognito.

In this scenario, this would be the best method of obtaining unique identifiers for each user. This is natively supported through Amazon Cognito.

CORRECT: "Implement developer-authenticated identities by using Amazon Cognito and get credentials for these identities" is the correct answer.

INCORRECT: "Create a user table in Amazon DynamoDB with key-value pairs of users and their devices. Use these keys as unique identifiers" is incorrect. This is not the best method of implementing this requirement as it requires more custom implementation and management.

INCORRECT: "Use IAM-generated access key IDs for the users as the unique identifier, but do not store secret keys" is incorrect. As this is a mobile application it is a good use case for Amazon Cognito so authentication can be handled without needing to create IAM users.

INCORRECT: "Assign IAM users and roles to the users. Use the unique IAM resource ID as the unique identifier" is incorrect as this mobile application is a good use case for Amazon Cognito. With Cognito the authentication can be handled using identities in Cognito itself or a federated identity provider. Therefore, the users will not have identities in IAM.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/developer-authenticated-identities.html>

</details>

---

<a id="t04-q60"></a>
### T04-Q60
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A developer is designing a web application that will be used by thousands of users. The users will sign up using their email addresses and the application will store attributes for each user.

Which service should the developer use to enable users to sign-up for the web application?

- ▫️ AWS Inspector
- ▫️ AWS AppSync
- ▫️ Amazon Cognito Sync
- ✅ Amazon Cognito user pool

<details><summary>Пояснення (оригінал, EN)</summary>

A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers.

Whether your users sign in directly or through a third party, all members of the user pool have a directory profile that you can access through a Software Development Kit (SDK).

User pools provide:

• Sign-up and sign-in services.

• A built-in, customizable web UI to sign in users.

• Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity providers from your user pool.

• User directory management and user profiles.

• Security features such as multi-factor authentication (MFA), checks for compromised credentials, account takeover protection, and phone and email verification.

• Customized workflows and user migration through AWS Lambda triggers.

After successfully authenticating a user, Amazon Cognito issues JSON web tokens (JWT) that you can use to secure and authorize access to your own APIs, or exchange for AWS credentials.

Therefore, an Amazon Cognito user pool is the best solution for enabling sign-up to the new web application.

CORRECT: "Amazon Cognito user pool" is the correct answer.

INCORRECT: "Amazon Cognito Sync" is incorrect as it is used to synchronize user profile data across mobile devices and the web without requiring your own backend.

INCORRECT: "AWS Inspector" is incorrect. Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS.

INCORRECT: "AWS AppSync" is incorrect. AWS AppSync simplifies application development by letting you create a flexible API to securely access, manipulate, and combine data from one or more data sources.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html>

</details>

---

<a id="t05-q17"></a>
### T05-Q17
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is creating an application that will require users to access AWS services and allow them to reset their own passwords. Which of the following would allow the company to manage users and authorization while allowing users to reset their own passwords?

- ✅ Amazon Cognito user pools and identity pools
- ▫️ Amazon Cognito identity pools and AWS IAM
- ▫️ Amazon Cognito identity pools and AWS STS
- ▫️ Amazon Cognito user pools and AWS KMS

<details><summary>Пояснення (оригінал, EN)</summary>

There are two key requirements in this scenario. Firstly the company wants to manage user accounts using a system that allows users to reset their own passwords. The company also wants to authorize users to access AWS services.

The first requirement is provided by an Amazon Cognito User Pool. With a Cognito user pool you can add sign-up and sign-in to mobile and web apps and it also offers a user directory so user accounts can be created directly within the user pool. Users also have the ability to reset their passwords.

To access AWS services you need a Cognito Identity Pool. An identity pool can be used with a user pool and enables a user to obtain temporary limited-privilege credentials to access AWS services.

Therefore, the best answer is to use Amazon Cognito user pools and identity pools.

CORRECT: "Amazon Cognito user pools and identity pools" is the correct answer.

INCORRECT: "Amazon Cognito identity pools and AWS STS" is incorrect as there is no user directory in this solution. A Cognito user pool is required.

INCORRECT: "Amazon Cognito identity pools and AWS IAM" is incorrect as a Cognito user pool should be used as the directory source for creating and managing users. IAM is used for accounts that are used to administer AWS services, not for application user access.

INCORRECT: "Amazon Cognito user pools and AWS KMS" is incorrect as KMS is used for encryption, not for authentication to AWS services.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html>

</details>

---

<a id="t05-q27"></a>
### T05-Q27
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application developer is crafting a new software product. To streamline the registration process, they want new users to be able to set up their accounts using their existing social media profiles.

Which AWS service or feature would be the most appropriate for achieving this goal?

- ✅ Amazon Cognito User Pools
- ▫️ AWS Security Token Service
- ▫️ AWS Identity and Access Management (IAM)
- ▫️ AWS Managed Microsoft AD

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito User Pools provide a secure and scalable directory to manage users. User Pools also support the ability for users to sign in through third-party identity providers, like social media platforms, which makes it the right choice for this requirement.

CORRECT: "Amazon Cognito User Pools" is the correct answer (as explained above.)

INCORRECT: "AWS Security Token Service" is incorrect.

AWS Security Token Service (STS) is primarily used to grant temporary, limited-privilege credentials for IAM users or for users that you authenticate (federated users), which is not related to the registration of application users via social media.

INCORRECT: "AWS Identity and Access Management (IAM)" is incorrect.

AWS IAM manages access to AWS services and resources securely. While IAM can federate with external identity providers, it is not designed to manage application user registration and social sign-in functionality directly.

INCORRECT: "AWS Managed Microsoft AD" is incorrect.

AWS Managed Microsoft AD is a service that is used to enable directory-aware workloads and AWS resources to use managed Active Directory in AWS. It's used for traditional enterprise applications and does not directly support social media-based user registration.

Джерела: <https://repost.aws/knowledge-center/cognito-user-pools-identity-pools>

</details>

---

<a id="t05-q52"></a>
### T05-Q52
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has created an Amazon Cognito user pool and configured a domain for it. The Developer wants to add sign-up and sign-in pages to an app with a company logo.

What should the Developer do to meet these requirements?

- ▫️ Upload the company logo to an Amazon S3 bucket. Specify the S3 object path in the app client settings in Amazon Cognito.
- ▫️ Create a custom login page that includes the company logo and upload it to Amazon Cognito. Specify the login page in the app client settings.
- ✅ Customize the Amazon Cognito hosted web UI and add the company logo.
- ▫️ Create a REST API using Amazon API Gateway and add a Cognito authorizer. Upload the company logo to a stage in the API.

<details><summary>Пояснення (оригінал, EN)</summary>

When you create a user pool in Amazon Cognito and then configure a domain for it, Amazon Cognito automatically provisions a hosted web UI to let you add sign-up and sign-in pages to your app. You can add a custom logo or customize the CSS for the hosted web UI.

CORRECT: "Customize the Amazon Cognito hosted web UI and add the company logo" is the correct answer.

INCORRECT: "Create a REST API using Amazon API Gateway and add a Cognito authorizer. Upload the company logo to a stage in the API" is incorrect. There is no need to add a REST API to this solution.

INCORRECT: "Upload the company logo to an Amazon S3 bucket. Specify the S3 object path in the app client settings in Amazon Cognito" is incorrect. This is not required as the hosted web UI can be used.

INCORRECT: "Create a custom login page that includes the company logo and upload it to Amazon Cognito. Specify the login page in the app client settings" is incorrect. This is not required as the hosted web UI can be used.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/cognito-hosted-web-ui/>

</details>

---

<a id="t06-q24"></a>
### T06-Q24
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is creating an application that must support Security Assertion Markup Language (SAML) and authentication with social identity providers. The application must also be authorized to access data in Amazon S3 buckets and Amazon DynamoDB tables.

Which AWS service or feature will meet these requirements with the LEAST amount of additional coding?

- ▫️ Amazon Cognito user pools.
- ✅ Amazon Cognito identity pools.
- ▫️ Amazon API Gateway REST API.
- ▫️ AWS AppSync GraphQL API.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito identity pools (federated identities) enable you to create unique identities for your users and federate them with identity providers. With an identity pool, you can obtain temporary, limited-privilege AWS credentials to access other AWS services.

Amazon Cognito identity pools support the following identity providers:

• Public providers: Amazon, Facebook, Google, Apple

• Amazon Cognito user pools

• Open ID Connect providers (identity pools)

• SAML identity providers (identity pools)

• Developer authenticated identities (identity pools)

Identity pools are well suited to use cases where you need to authenticate users through one of the above IdPs and then authorize access to AWS services such as Amazon S3 and DynamoDB.

CORRECT: "Amazon Cognito identity pools" is the correct answer (as explained above.)

INCORRECT: "Amazon Cognito user pools" is incorrect.

You can use a user pool for authentication but you would then need to use the identity pool for authorization to AWS services. Therefore, this option would require more additional coding.

INCORRECT: "AWS AppSync GraphQL API" is incorrect.

There is no need to implement an API for this use case. The developer simply needs a solution for authorization and access control.

INCORRECT: "Amazon API Gateway REST API" is incorrect.

There is no need to implement an API for this use case. The developer simply needs a solution for authorization and access control.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/cognito-user-pools-identity-pools/>

</details>

---

<a id="t06-q60"></a>
### T06-Q60
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has deployed a new web application that uses Amazon Cognito for authentication. The company wants to allow sign-in from any source but wants to automatically block all sign-in attempts if the risk level is elevated.

Which Amazon Cognito feature will meet these requirements?

- ▫️ Case sensitive user pools.
- ▫️ Multi-factor authentication (MFA).
- ✅ Adaptive authentication.
- ❌ Advanced security metrics.

<details><summary>Пояснення (оригінал, EN)</summary>

With adaptive authentication, you can configure your user pool to block suspicious sign-ins or add second factor authentication in response to an increased risk level.

For each sign-in attempt, Amazon Cognito generates a risk score for how likely the sign-in request is to be from a compromised source. This risk score is based on many factors, including whether it detects a new device, user location, or IP address.

For each risk level, you can choose from the following options:

•  Allow - Users can sign in without an additional factor.

•  Optional MFA - Users who have a second factor configured must complete a second factor challenge to sign in.

•  Require MFA - Users who have a second factor configured must complete a second factor challenge to sign in. Amazon Cognito blocks sign-in for users who don't have a second factor configured.

•  Block - Amazon Cognito blocks all sign-in attempts at the designated risk level.

In this case the company should use adaptive authentication and configure Cognito to block sign-in attempts at the specific risk level they feel is appropriate.

CORRECT: "Adaptive authentication" is the correct answer (as explained above.)

INCORRECT: "Advanced security metrics" is incorrect.

Amazon Cognito publishes sign-in attempts, their risk levels, and failed challenges to Amazon CloudWatch. These are known as advanced security metrics. This information is useful for analysis, but adaptive authentication is required to automatically block sign-in attempts.

INCORRECT: "Multi-factor authentication (MFA)" is incorrect.

This is not a method of blocking. In this case adaptive authentication with a block response should be configured.

INCORRECT: "Case sensitive user pools" is incorrect.

This has nothing to do with responding to security threats. This is a configuration that determines whether Cognito considers the case of email addresses and usernames.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pool-settings-adaptive-authentication.html>

</details>

---

<a id="t07-q16"></a>
### T07-Q16
*⏭ пропущено · домен: офіційна мітка*

A developer wants to integrate user-specific file upload and download features in an application that uses both Amazon Cognito user pools and Cognito identity pools for secure access with Amazon S3. The developer also wants to ensure that only authorized users can access their own files and that the files are securely saved and retrieved. The files are 5 KB to 500 MB in size.

What do you recommend as the most efficient solution?

- ▫️ Integrate Amazon API Gateway with a Lambda function that validates that the given file is uploaded to S3 and downloaded from S3 only by the authorized user
- ✅ Leverage an IAM policy with the Amazon Cognito identity prefix to allow users access to just their own objects in Amazon S3
- ▫️ Use S3 Event Notifications to trigger a Lambda function that validates that the given file is uploaded and downloaded only by the authorized user
- ▫️ Use CloudFront Lambda@Edge to validate that the given file is uploaded to S3 and downloaded from S3 only by the authorized user

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Leverage an IAM policy with the Amazon Cognito identity prefix to allow users access to just their own objects in Amazon S3

Amazon Cognito identity pools (federated identities) enable you to create unique identities for your users and federate them with identity providers. With an identity pool, you can obtain temporary, limited-privilege AWS credentials to access other AWS services. Amazon Cognito identity pools support the following identity providers:

Public providers: Login with Amazon (identity pools), Facebook (identity pools), Google (identity pools), Sign in with Apple (identity pools).

Amazon Cognito user pools

OpenID Connect providers (identity pools)

SAML identity providers (identity pools)

Developer authenticated identities (identity pools)

You can create an identity-based policy that allows Amazon Cognito users to access objects in a specific S3 bucket. This policy allows access only to objects with a name that includes Cognito, the name of the application, and the federated user's ID, represented by the ${cognito-identity.amazonaws.com:sub} variable.

 via - https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_s3_cognito-bucket.html

Incorrect options:

Use S3 Event Notifications to trigger a Lambda function that validates that the given file is uploaded and downloaded only by the authorized user - While it is certainly possible to build this solution, however, it is not the most optimal solution as it does not prevent an invalid upload of a file into another user's designated folder. So this option is incorrect.

Integrate Amazon API Gateway with a Lambda function that validates that the given file is uploaded to S3 and downloaded from S3 only by the authorized user - Again, it is certainly possible to build this solution, however, it is not the most optimal solution as it does not prevent an invalid upload of a file into another user's designated folder. So this option is incorrect.

Use CloudFront Lambda@Edge to validate that the given file is uploaded to S3 and downloaded from S3 only by the authorized user - This option assumes that the solution comprises a CloudFront distribution. This introduces inefficiency in the solution, as one needs to pay for CloudFront/Lambda@Edge and adds unnecessary hops in the data flow for both uploads and downloads.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_s3_cognito-bucket.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-integrating-user-pools-with-identity-pools.html>

</details>

---

<a id="t08-q10"></a>
### T08-Q10
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is developing a game for the Android and iOS platforms. The mobile game will securely store user game history and other data locally on the device. The company would like users to be able to use multiple mobile devices and synchronize data between devices.

Which service can be used to synchronize the data across mobile devices without the need to create a backend application?

- ▫️ Amazon API Gateway
- ✅ Amazon Cognito
- ▫️ AWS Lambda
- ▫️ Amazon DynamoDB

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito lets you save end user data in datasets containing key-value pairs. This data is associated with an Amazon Cognito identity, so that it can be accessed across logins and devices. To sync this data between the Amazon Cognito service and an end user’s devices, invoke the synchronize method. Each dataset can have a maximum size of 1 MB. You can associate up to 20 datasets with an identity.

The Amazon Cognito Sync client creates a local cache for the identity data. Your app talks to this local cache when it reads and writes keys. This guarantees that all of your changes made on the device are immediately available on the device, even when you are offline. When the synchronize method is called, changes from the service are pulled to the device, and any local changes are pushed to the service. At this point the changes are available to other devices to synchronize.

CORRECT: "Amazon Cognito" is the correct answer.

INCORRECT: "AWS Lambda" is incorrect. AWS Lambda provides serverless functions that run your code, it is not used for mobile client data synchronization.

INCORRECT: "Amazon API Gateway" is incorrect as API Gateway provides APIs for traffic coming into AWS. It is not used for mobile client data synchronization.

INCORRECT: "Amazon DynamoDB" is incorrect as DynamoDB is a NoSQL database. It is not used for mobile client data synchronization.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/synchronizing-data.html>

</details>

---

<a id="t08-q28"></a>
### T08-Q28
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A mobile application has hundreds of users. Each user may use multiple devices to access the application. The Developer wants to assign unique identifiers to these users regardless of the device they use.

Which of the following methods should be used to obtain unique identifiers?

- ▫️ Assign IAM users and roles to the users. Use the unique IAM resource ID as the unique identifier
- ✅ Implement developer-authenticated identities by using Amazon Cognito, and get credentials for these identities
- ▫️ Create a user table in Amazon DynamoDB as key-value pairs of users and their devices. Use these keys as unique identifiers
- ▫️ Use IAM-generated access key IDs for the users as the unique identifier, but do not store secret keys

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Cognito supports developer authenticated identities, in addition to web identity federation through Facebook (Identity Pools), Google (Identity Pools), Login with Amazon (Identity Pools), and Sign in with Apple (identity Pools).

With developer authenticated identities, you can register and authenticate users via your own existing authentication process, while still using Amazon Cognito to synchronize user data and access AWS resources.

Using developer authenticated identities involves interaction between the end user device, your backend for authentication, and Amazon Cognito.

Therefore, the Developer can implement developer-authenticated identities by using Amazon Cognito, and get credentials for these identities.

CORRECT: "Implement developer-authenticated identities by using Amazon Cognito, and get credentials for these identities" is the correct answer.

INCORRECT: "Create a user table in Amazon DynamoDB as key-value pairs of users and their devices. Use these keys as unique identifiers" is incorrect as this solution would require additional application logic and would be more complex.

INCORRECT: "Use IAM-generated access key IDs for the users as the unique identifier, but do not store secret keys" is incorrect as it is not a good practice to provide end users of mobile applications with IAM user accounts and access keys. Cognito is a better solution for this use case.

INCORRECT: "Assign IAM users and roles to the users. Use the unique IAM resource ID as the unique identifier" is incorrect. AWS Cognito is better suited to mobile users and with developer authenticated identities the users can be assigned unique identities.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/developer-authenticated-identities.html>

</details>

---

<a id="t08-q29"></a>
### T08-Q29
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is writing a web application that allows users to view images from an Amazon S3 bucket. The users will log in with their Amazon login, as well as Facebook and/or Google accounts.

How can the Developer provide this authentication capability?

- ▫️ Use AWS STS AssumeRole in the application code and assume a role with Get\* permissions on the S3 bucket
- ✅ Use Amazon Cognito with web identity federation
- ▫️ Use Amazon Cognito with SAML-based identity federation
- ▫️ Use AWS IAM Access/Secret keys in the application code to allow Get\* on the S3 bucket

<details><summary>Пояснення (оригінал, EN)</summary>

Explanation:

Amazon Cognito identity pools (federated identities) enable you to create unique identities for your users and federate them with identity providers. With an identity pool, you can obtain temporary, limited-privilege AWS credentials to access other AWS services. Amazon Cognito identity pools support the following identity providers:

• Public providers: Login with Amazon (Identity Pools), Facebook (Identity Pools), Google (Identity Pools) Sign in with Apple (Identity Pools).

• Amazon Cognito User Pools

• Open ID Connect Providers (Identity Pools)

• SAML Identity Providers (Identity Pools)

• Developer Authenticated Identities (Identity Pools)

With the temporary, limited-privilege AWS credentials users will be able to access the images in the S3 bucket. Therefore, the Developer should use Amazon Cognito with web identity federation

CORRECT: "Use Amazon Cognito with web identity federation" is the correct answer.

INCORRECT: "Use Amazon Cognito with SAML-based identity federation" is incorrect as SAML is used with directory sources such as Microsoft Active Directory, not Facebook or Google.

INCORRECT: "Use AWS IAM Access/Secret keys in the application code to allow Get\* on the S3 bucket" is incorrect as this insecure and against best practice. Always try to avoid embedding access keys in application code.

INCORRECT: "Use AWS STS AssumeRole in the application code and assume a role with Get\* permissions on the S3 bucket" is incorrect as you cannot do this directly through a Facebook or Google login. For this scenario, a Cognito Identity Pool is required to authenticate the user from the social IdP and provide access to the AWS services.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html>

</details>

---

<a id="t09-q02"></a>
### T09-Q02
*⏭ пропущено · домен: офіційна мітка*

Consider an application that enables users to store their mobile phone images in the cloud and supports tens of thousands of users. The application should utilize an Amazon API Gateway REST API that leverages AWS Lambda functions for photo processing while storing photo details in Amazon DynamoDB. The application should allow users to create an account, upload images, and retrieve previously uploaded images, with images ranging in size from 500 KB to 5 MB.

How will you design the application with the least operational overhead?

- ▫️ Use Cognito identity pools to create an IAM user for each user of the application during the sign-up process. Leverage IAM authentication in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key
- ▫️ Use Cognito identity pools to manage user accounts and set up an Amazon Cognito identity pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key
- ▫️ Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images as well as the image metadata in a DynamoDB table. Have the Lambda function retrieve previously uploaded images from DynamoDB
- ✅ Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key

A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and SAML identity providers. Whether your users sign in directly or through a third party, all members of the user pool have a directory profile that you can access through a Software Development Kit (SDK).

User pools provide:

Sign-up and sign-in services.

A built-in, customizable web UI to sign in users.

Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity providers from your user pool.

User directory management and user profiles.

Security features such as multi-factor authentication (MFA), checks for compromised credentials, account takeover protection, and phone and email verification.

Customized workflows and user migration through AWS Lambda triggers.

To use an Amazon Cognito user pool with your Amazon API Gateway API, you must first create an authorizer of the COGNITO_USER_POOLS type and then configure an API method to use that authorizer. After the API is deployed, the client must first sign the user into the user pool, obtain an identity or access token for the user, and then call the API method with one of the tokens, which are typically set to the request's Authorization header.

For the given use case, you can use a Cognito user pool to manage user accounts and configure an Amazon Cognito user pool authorizer in API Gateway to control access to the API. You should use a Lambda function to store the actual images on S3 and the image metadata on DynamoDB. Finally, you can get the images using the Lambda function that leverages the metadata stored in DynamoDB.

Incorrect options:

Use Cognito identity pools to manage user accounts and set up an Amazon Cognito identity pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key

Use Cognito identity pools to create an IAM user for each user of the application during the sign-up process. Leverage IAM authentication in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key

Amazon Cognito identity pools (federated identities) enable you to create unique identities for your users and federate them with identity providers. With an identity pool, you can obtain temporary, limited-privilege AWS credentials to access other AWS services. You cannot use identity pools to manage users or to create IAM users. So both of these options are incorrect.

 via - https://aws.amazon.com/premiumsupport/knowledge-center/cognito-user-pools-identity-pools/

Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images as well as the image metadata in a DynamoDB table. Have the Lambda function retrieve previously uploaded images from DynamoDB - You cannot use DynamoDB to store images as the maximum allowed item size is 400KB and the images range in size from 500KB to 5MB. You should also note that storing images on DynamoDB is an anti-pattern. So this option is incorrect.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/cognito-user-pools-identity-pools/>

</details>

---

<a id="t10-q47"></a>
### T10-Q47
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is creating a banking application that will be used to view financial transactions and statistics. The application requires multi-factor authentication to be added to the login protocol.

Which service should be used to meet this requirement?

- ✅ Amazon Cognito User Pool with MFA
- ▫️ AWS Directory Service
- ▫️ Amazon Cognito Identity Pool with MFA
- ▫️ AWS IAM with MFA

<details><summary>Пояснення (оригінал, EN)</summary>

A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito. Your users can also sign in through social identity providers like Google, Facebook, Amazon, or Apple, and through SAML identity providers.

User pools provide:

• Sign-up and sign-in services.

• A built-in, customizable web UI to sign in users.

• Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity providers from your user pool.

• User directory management and user profiles.

• Security features such as multi-factor authentication (MFA), checks for compromised credentials, account takeover protection, and phone and email verification.

• Customized workflows and user migration through AWS Lambda triggers.

Multi-factor authentication (MFA) increases security for your app by adding another authentication method, and not relying solely on username and password. You can choose to use SMS text messages, or time-based one-time (TOTP) passwords as second factors in signing in your users.

For this scenario you would want to set the MFA setting to “Required” as the data is highly secure.

CORRECT: "Amazon Cognito User Pool with MFA" is the correct answer.

INCORRECT: "Amazon Cognito Identity Pool with MFA" is incorrect

INCORRECT: "AWS IAM with MFA" is incorrect. With IAM your user accounts are maintained in your AWS account rather than in a Cognito User Pool. For logging into a web or mobile app it is better to create and manage your users in a Cognito User Pool and add MFA to the User Pool for extra security.

INCORRECT: "AWS Directory Service" is incorrect as this is a managed Active Directory service. For a web or mobile application using AWS Cognito User Pools is a better solution for storing your user accounts and authenticating to the application.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-mfa.html>

</details>

---

<a id="t11-q26"></a>
### T11-Q26
*⏭ пропущено · домен: офіційна мітка*

You are a developer for a web application written in .NET which uses the AWS SDK. You need to implement an authentication mechanism that returns a JWT (JSON Web Token).

Which AWS service will help you with token handling and management?

- ▫️ API Gateway
- ▫️ Cognito Sync
- ▫️ Cognito Identity Pools
- ✅ Cognito User Pools

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"Cognito User Pools"

After successful authentication, Amazon Cognito returns user pool tokens to your app. You can use the tokens to grant your users access to your own server-side resources, or to the Amazon API Gateway.

Amazon Cognito user pools implement ID, access, and refresh tokens as defined by the OpenID Connect (OIDC) open standard.

The ID token is a JSON Web Token (JWT) that contains claims about the identity of the authenticated user such as name, email, and phone_number. You can use this identity information inside your application. The ID token can also be used to authenticate users against your resource servers or server applications.

 via - https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html

Incorrect options:

"API Gateway" - If you are processing tokens server-side and using other programming languages not supported in AWS it may be a good choice. Other than that, go with a service already providing the functionality.

"Cognito Identity Pools" - You can use Identity pools to grant your users access to other AWS services. With an identity pool, your users can obtain temporary AWS credentials to access AWS services, such as Amazon S3 and DynamoDB. Identity pools support anonymous guest users, as well as the specific identity providers that you can use to authenticate users for identity pools.

"Cognito Sync" - Amazon Cognito Sync is an AWS service and client library that enables cross-device syncing of application-related user data. You can use it to synchronize user profile data across mobile devices and the web without requiring your own backend.

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Reference:

https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html

</details>

---

<a id="t12-q12"></a>
### T12-Q12
*⏭ пропущено · домен: офіційна мітка*

A photo-sharing application manages its EC2 server fleet running behind an Application Load Balancer and the traffic is fronted by a CloudFront distribution. The development team wants to decouple the user authentication process for the application so that the application servers can just focus on the business logic.

As a Developer Associate, which of the following solutions would you recommend to address this use-case with minimal development effort?

- ✅ Use Cognito Authentication via Cognito User Pools for your Application Load Balancer
- ▫️ Use Cognito Authentication via Cognito User Pools for your CloudFront distribution
- ▫️ Use Cognito Authentication via Cognito Identity Pools for your CloudFront distribution
- ▫️ Use Cognito Authentication via Cognito Identity Pools for your Application Load Balancer

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Cognito Authentication via Cognito User Pools for your Application Load Balancer

Application Load Balancer can be used to securely authenticate users for accessing your applications. This enables you to offload the work of authenticating users to your load balancer so that your applications can focus on their business logic. You can use Cognito User Pools to authenticate users through well-known social IdPs, such as Amazon, Facebook, or Google, through the user pools supported by Amazon Cognito or through corporate identities, using SAML, LDAP, or Microsoft AD, through the user pools supported by Amazon Cognito. You configure user authentication by creating an authenticate action for one or more listener rules. The authenticate-cognito and authenticate-oidc action types are supported only with HTTPS listeners.

 via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html

Please make sure that you adhere to the following configurations while using CloudFront distribution in front of your Application Load Balancer:  via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Incorrect options:

Use Cognito Authentication via Cognito Identity Pools for your Application Load Balancer - There is no such thing as using Cognito Authentication via Cognito Identity Pools for managing user authentication for the application. Application-specific user authentication can be provided via Cognito User Pools. Amazon Cognito identity pools provide temporary AWS credentials for users who are guests (unauthenticated) and for users who have been authenticated and received a token.

Use Cognito Authentication via Cognito User Pools for your CloudFront distribution - You cannot directly integrate Cognito User Pools with CloudFront distribution as you have to create a separate Lambda@Edge function to accomplish the authentication via Cognito User Pools. This involves additional development effort, so this option is not the best fit for the given use-case.

Use Cognito Authentication via Cognito Identity Pools for your CloudFront distribution - You cannot use Cognito Identity Pools for managing user authentication, so this option is not correct.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://aws.amazon.com/blogs/networking-and-content-delivery/authorizationedge-using-cookies-protect-your-amazon-cloudfront-content-from-being-downloaded-by-unauthenticated-users/>

</details>

---
