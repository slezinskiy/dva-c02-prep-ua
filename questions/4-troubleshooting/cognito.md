# Amazon Cognito

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#cognito) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q29"></a>
### T09-Q29
*домен: офіційна мітка*

The app development team at a social gaming mobile app wants to simplify the user sign up process for the app. The team is looking for a fully managed scalable solution for user management in anticipation of the rapid growth that the app foresees.

As a Developer Associate, which of the following solutions would you suggest so that it requires the LEAST amount of development effort?

- ▫️ Create a custom solution using Lambda and DynamoDB to facilitate sign up and user management for the mobile app
- ▫️ Create a custom solution using EC2 and DynamoDB to facilitate sign up and user management for the mobile app
- ▫️ Use Cognito Identity pools to facilitate sign up and user management for the mobile app
- ✅ Use Cognito User pools to facilitate sign up and user management for the mobile app

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Cognito User pools to facilitate sign up and user management for the mobile app»**

Cognito дає автентифікацію, авторизацію і керування користувачами для веб- і мобільних застосунків. Вхід — за логіном і паролем або через Facebook, Amazon, Google, Apple. User pool — каталог користувачів, і кожен має профіль, доступний через SDK. Cognito повністю керований і працює з коробки.

**❌ Неправильні варіанти:**
- *Identity pools* — видають тимчасові облікові дані AWS (включно з гостями), але реєстрацією і каталогом користувачів не займаються.
- *EC2 + DynamoDB custom* — потрібне повністю кероване рішення з мінімумом розробки.
- *Lambda + DynamoDB custom* — те саме.

</details>

---
