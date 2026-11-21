# CloudWatch / X-Ray / CloudTrail

**Домен 2 — Безпека** · питань: 1  
Теорія: [04-domain4-troubleshooting.md](../../04-domain4-troubleshooting.md#observability) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q65"></a>
### T07-Q65
*домен: офіційна мітка*

A HealthCare mobile app uses proprietary Machine Learning algorithms to provide early diagnosis using patient health metrics. To protect this sensitive data, the development team wants to transition to a scalable user management system with log-in/sign-up functionality that also supports Multi-Factor Authentication (MFA)

Which of the following options can be used to implement a solution with the LEAST amount of development effort? (Select two)

- ✅ Use Amazon Cognito for user-management and facilitating the log-in/sign-up process
- ▫️ Use Lambda functions and RDS to create a custom solution for user management
- ▫️ Use Lambda functions and DynamoDB to create a custom solution for user management
- ✅ Use Amazon Cognito to enable Multi-Factor Authentication (MFA) when users log-in
- ▫️ Use Amazon SNS to send Multi-Factor Authentication (MFA) code via SMS to mobile app users

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon Cognito for user-management and facilitating the log-in/sign-up process» + «Use Amazon Cognito to enable Multi-Factor Authentication (MFA) when users log-in»**

Cognito швидко додає до веб- і мобільних застосунків реєстрацію, вхід і контроль доступу. Він масштабується до мільйонів користувачів і підтримує вхід через соцмережі (Facebook, Google, Amazon) та корпоративні IdP через SAML 2.0.

User pool — це каталог користувачів. Користувачі входять напряму або через сторонній IdP, і в кожного є профіль, доступний через SDK. User pools підтримують реєстрацію, вхід і функції безпеки, зокрема MFA.

Порівняння user pools та identity pools: https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

**❌ Неправильні варіанти:**
- *Lambda + DynamoDB custom solution* — потрібно мінімум розробки, а це власне рішення.
- *Lambda + RDS custom solution* — те саме.
- *SNS for MFA SMS* — окремо налаштовувати SNS для MFA не потрібно, бо MFA вмикають у Cognito. ⚠️ Пояснення в оригіналі неточне: Cognito сам надсилає SMS із кодами MFA через Amazon SNS. А зауваження про припинення SMS MFA стосується IAM-користувачів, а не Cognito.

</details>

---
