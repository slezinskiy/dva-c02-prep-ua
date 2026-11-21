# ElastiCache / RDS / Aurora

**Домен 3 — Розгортання** · питань: 2  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#cache) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q39"></a>
### T03-Q39
*домен: офіційна мітка*

A development team is considering Amazon ElastiCache for Redis as its in-memory caching solution for its relational database.

Which of the following options are correct while configuring ElastiCache? (Select two)

- ✅ All the nodes in a Redis cluster must reside in the same region
- ▫️ If you have no replicas and a node fails, you experience no loss of data when using Redis with cluster mode enabled
- ▫️ You can scale write capacity for Redis by adding replica nodes
- ▫️ While using Redis with cluster mode enabled, asynchronous replication mechanisms are used to keep the read replicas synchronized with the primary. If cluster mode is disabled, the replication mechanism is done synchronously
- ✅ While using Redis with cluster mode enabled, you cannot manually promote any of the replica nodes to primary

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «All the nodes in a Redis cluster must reside in the same region» та «While using Redis with cluster mode enabled, you cannot manually promote any of the replica nodes to primary»**

Усі вузли кластера Redis (з cluster mode чи без) мають бути в одному регіоні. Обмеження cluster mode enabled:
- репліку не можна вручну підвищити до primary;
- Multi-AZ обов'язковий;
- структуру кластера, тип і кількість вузлів можна змінити лише відновленням з бекапу (за даними банку; зараз доступне онлайн-масштабування).

**❌ Неправильні варіанти:**
- *Cluster mode enabled — асинхронна реплікація, disabled — синхронна* — в обох режимах реплікація асинхронна.
- *If you have no replicas and a node fails … no loss of data* — без реплік у cluster mode втрачаються всі дані шарда цього вузла, без cluster mode — усі дані.
- *Scale write capacity by adding replica nodes* — репліки масштабують лише читання.

</details>

---

<a id="t11-q64"></a>
### T11-Q64
*домен: офіційна мітка*

You have created a Java application that uses RDS for its main data storage and ElastiCache for user session storage. The application needs to be deployed using Elastic Beanstalk and every new deployment should allow the application servers to reuse the RDS database. On the other hand, user session data stored in ElastiCache can be re-created for every deployment.

Which of the following configurations will allow you to achieve this? (Select two)

- ✅ RDS database defined externally and referenced through environment variables
- ✅ ElastiCache defined in .ebextensions/
- ▫️ RDS database defined in .ebextensions/
- ▫️ ElastiCache database defined externally and referenced through environment variables
- ▫️ ElastiCache bundled with the application source code

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «RDS database defined externally and referenced through environment variables» + «ElastiCache defined in .ebextensions/»**

- **ElastiCache у `.ebextensions/`.** Ресурси з `.ebextensions` — частина шаблону середовища і видаляються разом із ним. За умовою втрата сесій прийнятна, тож це нормально.
- **RDS поза середовищем.** Базу створюють окремо, а застосунок підключається до неї через змінні середовища. Так одну БД можуть використовувати кілька середовищ, середовище можна видалити без втрати БД, і blue/green-деплой проходить безшовно. Щоб EC2 мали доступ до БД, до ASG середовища додають security group.

Детальніше: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.RDS.html

**❌ Неправильні варіанти:**
- *ElastiCache bundled with code* — сервіс AWS не можна запакувати разом із кодом.
- *RDS in .ebextensions/* — життя БД прив'язується до середовища, і вона видалиться разом із ним.
- *ElastiCache externally* — можливо, але втрата сесій прийнятна, тож `.ebextensions` доречніший.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.RDS.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-environment-resources-elasticache.html>

</details>

---
