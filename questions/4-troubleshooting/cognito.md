# Amazon Cognito

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#cognito) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q29"></a>
### T09-Q29
*⏭ пропущено · домен: офіційна мітка*

The app development team at a social gaming mobile app wants to simplify the user sign up process for the app. The team is looking for a fully managed scalable solution for user management in anticipation of the rapid growth that the app foresees.

As a Developer Associate, which of the following solutions would you suggest so that it requires the LEAST amount of development effort?

- ▫️ Create a custom solution using Lambda and DynamoDB to facilitate sign up and user management for the mobile app
- ▫️ Create a custom solution using EC2 and DynamoDB to facilitate sign up and user management for the mobile app
- ▫️ Use Cognito Identity pools to facilitate sign up and user management for the mobile app
- ✅ Use Cognito User pools to facilitate sign up and user management for the mobile app

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Cognito User pools to facilitate sign up and user management for the mobile app

Amazon Cognito provides authentication, authorization, and user management for your web and mobile apps. Your users can sign in directly with a user name and password, or through a third party such as Facebook, Amazon, Google or Apple.

A user pool is a user directory in Amazon Cognito. With a user pool, your users can sign in to your web or mobile app through Amazon Cognito, or federate through a third-party identity provider (IdP). Whether your users sign-in directly or through a third party, all members of the user pool have a directory profile that you can access through an SDK.

Cognito is fully managed by AWS and works out of the box so it meets the requirements for the given use-case.

 via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Incorrect options:

Use Cognito Identity pools to facilitate sign up and user management for the mobile app - You can use Identity pools to grant your users access to other AWS services. With an identity pool, your users can obtain temporary AWS credentials to access AWS services, such as Amazon S3 and DynamoDB. Identity pools support anonymous guest users, as well as the specific identity providers that you can use to authenticate users for identity pools.

Exam Alert:

Please review the following note to understand the differences between Cognito User Pools and Cognito Identity Pools:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

Create a custom solution with EC2 and DynamoDB to facilitate sign up and user management for the mobile app

Create a custom solution with Lambda and DynamoDB to facilitate sign up and user management for the mobile app

As the problem statement mentions that the solution needs to be fully managed and should require the least amount of development effort, so you cannot use EC2 or Lambda functions with DynamoDB to create a custom solution.

Reference:

https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

</details>

---
