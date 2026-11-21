# Secrets Manager / Parameter Store

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#secrets) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t11-q53"></a>
### T11-Q53
*⏭ пропущено · домен: офіційна мітка*

Your company has stored all application secrets in SSM Parameter Store. The audit team has requested to get a report to better understand when and who has issued API calls against SSM Parameter Store.

Which of the following options can be used to produce your report?

- ▫️ Use SSM Parameter Store Access Logs in S3 to get a record of actions taken by a user
- ✅ Use AWS CloudTrail to get a record of actions taken by a user
- ▫️ Use SSM Parameter Store Access Logs in CloudWatch Logs to get a record of actions taken by a user
- ▫️ Use SSM Parameter Store List feature to get a record of actions taken by a user

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use AWS CloudTrail to get a record of actions taken by a user

AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, Amazon Machine Image (AMI) IDs, and license codes as parameter values. You can store values as plain text or encrypted data.

AWS CloudTrail provides a record of actions taken by a user, role, or an AWS service in Systems Manager. Using the information collected by AWS CloudTrail, you can determine the request that was made to Systems Manager, the IP address from which the request was made, who made the request, when it was made, and additional details.

Incorrect options:

Use SSM Parameter Store List feature to get a record of actions taken by a user - This option has been added as a distractor.

Use SSM Parameter Store Access Logs in CloudWatch Logs to get a record of actions taken by a user - CloudWatch Logs can be integrated but that will not help determine who issued API calls.

Use SSM Parameter Store Access Logs in S3 to get a record of actions taken by a user - S3 Access Logs can be integrated but that will not help determine who issued API calls.

</details>

---
