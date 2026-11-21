# Amazon Cognito

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#cognito) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q16"></a>
### T01-Q16
*домен: офіційна мітка*

A business-critical mobile application uses Amazon Cognito user pools with multi-factor authentication (MFA) enabled for all its users. The application manages confidential data about the company's sales forecasts and product launches. Considering the highly critical nature of the application, the company wants to track every user login activity via a notification sent as an email to the security team.

Which of the following would you recommend as the MOST optimal way of implementing this requirement within a short period?

- ✅ Create an AWS Lambda function that uses Amazon Simple Email Service to send an email notification to the concerned security team. Configure this function as Amazon Cognito post-authentication Lambda trigger
- ▫️ Configure an AWS Lambda function as a trigger to Amazon Cognito identity pools authenticated API operations. Create the Lambda function to utilize the Amazon Simple Email Service to send an email notification to the concerned security team
- ▫️ Create an AWS Lambda function that uses Amazon Simple Email Service to send an email notification to the concerned security team. Configure this function as Amazon Cognito pre-authentication Lambda trigger
- ▫️ Configure Amazon Cognito user pools authenticated API operations and MFA API operations to send all login data to Amazon Kinesis Data Streams. Configure an AWS Lambda function to analyze these streams and trigger an SNS notification to the security team based on user access

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an AWS Lambda function that uses Amazon SES to send an email notification to the concerned security team. Configure this function as Amazon Cognito post-authentication Lambda trigger»**

Cognito викликає Post authentication Lambda trigger після входу користувача. У ньому можна додати власну логіку, наприклад надіслати лист через SES.

**❌ Неправильні варіанти:**
- *Pre-authentication Lambda trigger* — викликається під час спроби входу, щоб прийняти чи відхилити запит. А нам треба відстежувати вже здійснені входи, тобто подію після автентифікації.
- *Lambda as a trigger to Amazon Cognito identity pools authenticated API operations* — identity pools (federated identities) створюють унікальні ідентичності та федерують їх з провайдерами. Вони про авторизацію, а не автентифікацію.
- *Send all login data to Kinesis Data Streams…* — вигаданий варіант, дистрактор.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-post-authentication.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pools-API-operations.html>

</details>

---
