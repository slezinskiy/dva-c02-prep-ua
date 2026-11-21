# Amazon DynamoDB

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#dynamodb) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t08-q49"></a>
### T08-Q49
*домен: визначено автоматично (у джерелі: AWS Database)*

A gaming application stores scores for players in an Amazon DynamoDB table that has four attributes: user_id, user_name, user_score, and user_rank. The users are allowed to update their names only. A user is authenticated by web identity federation.

Which set of conditions should be added in the policy attached to the role for the dynamodb:PutItem API call?

- ✅

  ```json
  "Condition": {
  "ForAllValues:StringEquals": {
  "dynamodb:LeadingKeys": [
  "${www.amazon.com:user_id}"
  ],
  "dynamodb:Attributes": [
  "user_name"
  ]
  }
  }
  ```
- ▫️

  ```json
  "Condition": {
  "ForAllValues:StringEquals": {
  "dynamodb:LeadingKeys": [
  "${www.amazon.com:user_name}"
  ],
  "dynamodb:Attributes": [
  "user_id"
  ]
  }
  }
  ```
- ▫️

  ```json
  "Condition": {
  "ForAllValues:StringEquals": {
  "dynamodb:LeadingKeys": [
  "${www.amazon.com:user_id}"
  ],
  "dynamodb:Attributes": [
  "user_name", "user_id"
  ]
  }
  }
  ```
- ▫️

  ```json
  "Condition": {
  "ForAllValues:StringEquals": {
  "dynamodb:LeadingKeys": [
  "${www.amazon.com:user_name}"
  ],
  "dynamodb:Attributes": [
  "user_name", "user_id"
  ]
  }
  }
  ```

<details><summary>Пояснення</summary>

**✅ Правильна відповідь:**
```json
"Condition": {
  "ForAllValues:StringEquals": {
    "dynamodb:LeadingKeys": [ "${www.amazon.com:user_id}" ],
    "dynamodb:Attributes": [ "user_name" ]
  }
}
```

Користувачі входять через web identity federation (Login with Amazon). Політика має ідентифікувати користувача за `user_id` і дозволити йому змінювати лише `user_name` через `dynamodb:PutItem`. Ключові елементи:
- **`dynamodb:LeadingKeys`** — partition key: користувач бачить лише елементи зі своїм ID;
- **`dynamodb:Attributes`** — атрибути, які можна змінювати.

**❌ Неправильні варіанти:**
- В інших варіантах або `LeadingKeys` вказує на `user_name`, хоча це атрибут для зміни, а не ключ, або в `Attributes` вказано неправильні атрибути замість `user_name`.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html#FGAC_DDB.ConditionKeys>

</details>

---
