# ElastiCache / RDS / Aurora

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q12"></a>
### T07-Q12
*домен: офіційна мітка*

The Development team at a media company is working on securing their databases.

Which of the following AWS database engines can be configured with IAM Database Authentication? (Select two)

- ✅ RDS PostGreSQL
- ▫️ RDS Oracle
- ▫️ RDS SQL Server
- ✅ RDS MySQL
- ▫️ RDS Db2

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «RDS PostGreSQL» + «RDS MySQL»**

IAM database authentication дозволяє підключатися до БД без пароля, з автентифікаційним токеном. Токен — це унікальний рядок, який RDS генерує на запит; діє 15 хвилин. Зберігати облікові дані в БД не потрібно, бо автентифікацією керує IAM. Підтримувані рушії:
- Aurora MySQL і Aurora PostgreSQL;
- RDS for MySQL, MariaDB і PostgreSQL.

**❌ Неправильні варіанти:**
- *RDS Oracle* — IAM DB authentication не підтримується.
- *RDS SQL Server* — IAM DB authentication не підтримується.
- *RDS Db2* — IAM DB authentication не підтримується. ⚠️ В оригіналі сказано, що RDS не підтримує Db2 взагалі. Це застаріло: RDS for Db2 доступний із грудня 2023 року, але IAM-автентифікації для нього немає.

</details>

---
