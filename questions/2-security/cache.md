# ElastiCache / RDS / Aurora

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q12"></a>
### T07-Q12
*⏭ пропущено · домен: офіційна мітка*

The Development team at a media company is working on securing their databases.

Which of the following AWS database engines can be configured with IAM Database Authentication? (Select two)

- ✅ RDS PostGreSQL
- ▫️ RDS Oracle
- ▫️ RDS SQL Server
- ✅ RDS MySQL
- ▫️ RDS Db2

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

You can authenticate to your DB instance using AWS Identity and Access Management (IAM) database authentication. With this authentication method, you don't need to use a password when you connect to a DB instance. Instead, you use an authentication token. An authentication token is a unique string of characters that Amazon RDS generates on request. Each token has a lifetime of 15 minutes. You don't need to store user credentials in the database, because authentication is managed externally using IAM.

RDS MySQL - IAM database authentication works with MySQL and PostgreSQL engines for Aurora as well as MySQL, MariaDB and RDS PostgreSQL engines for RDS.

RDS PostGreSQL - IAM database authentication works with MySQL and PostgreSQL engines for Aurora as well as MySQL, MariaDB and RDS PostgreSQL engines for RDS.

Incorrect options:

RDS Oracle

RDS SQL Server

These two options contradict the details in the explanation above, so these are incorrect.

RDS Db2 - This option has been added as a distractor. Db2 is a family of data management products, including database servers, developed by IBM. RDS does not support Db2 database engine.

Reference:

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAMDBAuth.html

</details>

---
