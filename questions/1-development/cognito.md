# Amazon Cognito

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#cognito) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q16"></a>
### T01-Q16
*⏭ пропущено · домен: офіційна мітка*

A business-critical mobile application uses Amazon Cognito user pools with multi-factor authentication (MFA) enabled for all its users. The application manages confidential data about the company's sales forecasts and product launches. Considering the highly critical nature of the application, the company wants to track every user login activity via a notification sent as an email to the security team.

Which of the following would you recommend as the MOST optimal way of implementing this requirement within a short period?

- ✅ Create an AWS Lambda function that uses Amazon Simple Email Service to send an email notification to the concerned security team. Configure this function as Amazon Cognito post-authentication Lambda trigger
- ▫️ Configure an AWS Lambda function as a trigger to Amazon Cognito identity pools authenticated API operations. Create the Lambda function to utilize the Amazon Simple Email Service to send an email notification to the concerned security team
- ▫️ Create an AWS Lambda function that uses Amazon Simple Email Service to send an email notification to the concerned security team. Configure this function as Amazon Cognito pre-authentication Lambda trigger
- ▫️ Configure Amazon Cognito user pools authenticated API operations and MFA API operations to send all login data to Amazon Kinesis Data Streams. Configure an AWS Lambda function to analyze these streams and trigger an SNS notification to the security team based on user access

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create an AWS Lambda function that uses Amazon Simple Email Service to send an email notification to the concerned security team. Configure this function as Amazon Cognito post-authentication Lambda trigger

Amazon Cognito invokes Post authentication Lambda trigger after signing in a user, you can add custom logic after Amazon Cognito authenticates the user.

Post-authentication Lambda flows:  via - https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-post-authentication.html

Incorrect options:

Create an AWS Lambda function that uses Amazon Simple Email Service to send an email notification to the concerned security team. Configure this function as Amazon Cognito pre-authentication Lambda trigger - Pre-authentication Lambda trigger: Amazon Cognito invokes this trigger when a user attempts to sign in so that you can create custom validation that accepts or denies the authentication request. This is not useful for the current use case since we want to track user login activity which happens post-authentication.

Configure an AWS Lambda function as a trigger to Amazon Cognito identity pools authenticated API operations. Create the Lambda function to utilize the Amazon Simple Email Service to send an email notification to the concerned security team - This statement is incorrect. Amazon Cognito identity pools (federated identities) enable you to create unique identities for your users and federate them with identity providers. Cognito identity pools are for authorization and not for authentication.

Configure Amazon Cognito user pools authenticated API operations and MFA API operations to send all login data to Amazon Kinesis Data Streams. Configure an AWS Lambda function to analyze these streams and trigger an SNS notification to the security team based on user access - This is a made-up option given only as a distractor.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-post-authentication.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pools-API-operations.html>

</details>

---
