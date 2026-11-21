# Secrets Manager / Parameter Store

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#secrets) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t11-q53"></a>
### T11-Q53
*домен: офіційна мітка*

Your company has stored all application secrets in SSM Parameter Store. The audit team has requested to get a report to better understand when and who has issued API calls against SSM Parameter Store.

Which of the following options can be used to produce your report?

- ▫️ Use SSM Parameter Store Access Logs in S3 to get a record of actions taken by a user
- ✅ Use AWS CloudTrail to get a record of actions taken by a user
- ▫️ Use SSM Parameter Store Access Logs in CloudWatch Logs to get a record of actions taken by a user
- ▫️ Use SSM Parameter Store List feature to get a record of actions taken by a user

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS CloudTrail to get a record of actions taken by a user»**

Parameter Store — безпечне ієрархічне сховище конфігурацій і секретів: паролів, рядків підключення, ID AMI, ліцензійних кодів, відкритих чи зашифрованих. CloudTrail записує дії користувачів, ролей і сервісів у Systems Manager: який запит, з якого IP, хто, коли та інші деталі.

**❌ Неправильні варіанти:**
- *Parameter Store List feature* — такої функції немає.
- *Access Logs in CloudWatch Logs* — не показують, хто викликав API.
- *Access Logs in S3* — не показують, хто викликав API.

</details>

---
