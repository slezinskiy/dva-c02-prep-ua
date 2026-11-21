# Amazon DynamoDB

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#dynamodb) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t08-q49"></a>
### T08-Q49
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

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

<details><summary>Пояснення (оригінал, EN)</summary>

The users are authenticated by web identity federation. The user_id value should be used to identify the user in the policy and the policy needs to then allow the user to change the user_name value when using the dynamodb:PutItem API call.

The key parts of the code to look for are the dynamodb:LeadingKeys which represents the partition key of the table and the dynamodb:Attributes which represents the items that can be changed.

CORRECT: The answer that includes dynamodb:LeadingKeys identifying user_id and dynamodb:Attributes identifying user_name is the correct answer.

INCORRECT: The other answers provide a few incorrect code samples where either the dynamodb:LeadingKeys identifies user_name (which is incorrect as it is the item to be changed) or dynamodb:Attributes identifying the wrong attributes for modification (should be user_name).

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html#FGAC_DDB.ConditionKeys>

</details>

---
