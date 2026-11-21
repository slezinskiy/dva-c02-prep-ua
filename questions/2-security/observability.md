# CloudWatch / X-Ray / CloudTrail

**Домен 2 — Безпека** · питань: 1  
Теорія: [04-domain4-troubleshooting.md](../../04-domain4-troubleshooting.md#observability) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q65"></a>
### T07-Q65
*⏭ пропущено · домен: офіційна мітка*

A HealthCare mobile app uses proprietary Machine Learning algorithms to provide early diagnosis using patient health metrics. To protect this sensitive data, the development team wants to transition to a scalable user management system with log-in/sign-up functionality that also supports Multi-Factor Authentication (MFA)

Which of the following options can be used to implement a solution with the LEAST amount of development effort? (Select two)

- ✅ Use Amazon Cognito for user-management and facilitating the log-in/sign-up process
- ▫️ Use Lambda functions and RDS to create a custom solution for user management
- ▫️ Use Lambda functions and DynamoDB to create a custom solution for user management
- ✅ Use Amazon Cognito to enable Multi-Factor Authentication (MFA) when users log-in
- ▫️ Use Amazon SNS to send Multi-Factor Authentication (MFA) code via SMS to mobile app users

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Use Amazon Cognito for user-management and facilitating the log-in/sign-up process

Use Amazon Cognito to enable Multi-Factor Authentication (MFA) when users log-in

Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. Amazon Cognito scales to millions of users and supports sign-in with social identity providers, such as Facebook, Google, and Amazon, and enterprise identity providers via SAML 2.0.

A Cognito user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito, or federate through a third-party identity provider (IdP). Whether your users sign-in directly or through a third party, all members of the user pool have a directory profile that you can access through an SDK.

Cognito user pools provide support for sign-up and sign-in services as well as security features such as multi-factor authentication (MFA).

 via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Incorrect options:

Use Lambda functions and DynamoDB to create a custom solution for user management

Use Lambda functions and RDS to create a custom solution for user management

As the problem statement mentions that the solution should require the least amount of development effort, so you cannot use Lambda functions with DynamoDB or RDS to create a custom solution. So both these options are incorrect.

Use Amazon SNS to send Multi-Factor Authentication (MFA) code via SMS to mobile app users - Amazon SNS cannot be used to send MFA codes via SMS to the user's mobile devices as this functionality is only meant to be used for IAM users. An SMS (short message service) MFA device can be any mobile device with a phone number that can receive standard SMS text messages. AWS will soon end support for SMS multi-factor authentication (MFA).

Please see this for more details:

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable_sms.html

Reference:

https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

</details>

---
