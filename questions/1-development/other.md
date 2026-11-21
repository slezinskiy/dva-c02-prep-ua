# Інше

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 5  
Теорія: [README.md](../../README.md) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t04-q04"></a>
### T04-Q04
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has released a new application on AWS. The company are concerned about security and require a tool that can automatically assess applications for exposure, vulnerabilities, and deviations from best practices.

Which AWS service should they use?

- ▫️ AWS WAF
- ▫️ AWS Shield
- ✅ Amazon Inspector
- ▫️ AWS Secrets Manager

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Inspector»**

Amazon Inspector — автоматизований сервіс оцінки безпеки: перевіряє застосунки на вразливості, відкритість у мережі та відхилення від best practices. Результат — список findings, впорядкованих за критичністю, у консолі або через API.

**❌ Неправильні варіанти:**
- *AWS Shield* — захист від DDoS.
- *AWS WAF* — фаєрвол веб-застосунків.
- *AWS Secrets Manager* — зберігання секретів.

Джерела: <https://aws.amazon.com/inspector/>

</details>

---

<a id="t05-q02"></a>
### T05-Q02
*домен: визначено автоматично (у джерелі: AWS Database)*

A development team manage a high-traffic e-Commerce site with dynamic pricing that is updated in real-time. There have been incidents where multiple updates occur simultaneously and cause an original editor’s updates to be overwritten. How can the developers ensure that overwriting does not occur?

- ✅ Use conditional writes
- ▫️ Use concurrent writes
- ▫️ Use atomic counters
- ▫️ Use batch operations

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use conditional writes»**

За замовчуванням `PutItem`, `UpdateItem`, `DeleteItem` безумовні: кожна операція перезаписує item з тим самим первинним ключем. **Conditional write** виконується, лише якщо атрибути item'а відповідають умовам, інакше повертає помилку. Наприклад: `PutItem` лише якщо item з таким ключем ще немає, або `UpdateItem` лише якщо атрибут має очікуване значення.

Якщо умова перевіряє той самий атрибут, що оновлюється, запис ідемпотентний. Наприклад, «підняти ціну, лише якщо зараз вона 20»: після мережевої помилки той самий запит можна безпечно повторити. Тут кожен редактор оновлює ціну, лише якщо вона не змінилася з моменту читання; інакше запис відхиляється і чужі зміни не перезаписуються.

```
aws dynamodb update-item --table-name ProductCatalog --key '{"Id":{"N":"1"}}' \
  --update-expression "SET Price = :newval" --condition-expression "Price = :currval" ...
```

**❌ Неправильні варіанти:**
- *Concurrent writes* — саме цього й треба уникнути.
- *Atomic counters* — безумовний інкремент числового атрибута (лічильник відвідувачів); від перезапису не захищає.
- *Batch operations* — менше мережевих запитів, але перезапису не запобігає.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems.ConditionalUpdate> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html>

</details>

---

<a id="t05-q50"></a>
### T05-Q50
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A programmer is creating an application that requires signed requests (Signature Version 4) for invoking other AWS services. Having constructed a canonical request, created the string to sign, and calculated the signing information, which strategies can the programmer apply to finalize a signed request? (Select TWO.)

- ▫️ Append the signature to a query string parameter known as "X-Amz-Credentials".
- ✅ Incorporate the signature into an HTTP header called "Authorization".
- ✅ Insert the signature into a query string parameter referred to as "X-Amz-Signature".
- ▫️ Add the signature to a query string parameter named "Signature-Token".
- ▫️ Embed the signature in an HTTP header labelled "Authorization-Key".

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Incorporate the signature into an HTTP header called "Authorization".» + «Insert the signature into a query string parameter referred to as "X-Amz-Signature".»**

У Signature Version 4 підпис передають або в HTTP-заголовку `Authorization`, або в параметрі query string `X-Amz-Signature`. Другий спосіб використовують, зокрема, presigned URLs.

**❌ Неправильні варіанти:**
- *X-Amz-Credentials* — такий параметр існує (правильно `X-Amz-Credential`), але в ньому access key ID і scope, а не підпис.
- *Authorization-Key header* — такого заголовка для SigV4 немає.
- *Signature-Token parameter* — такого параметра для SigV4 немає.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-signing.html>

</details>

---

<a id="t09-q05"></a>
### T09-Q05
*домен: офіційна мітка*

A business has purchased one m4.xlarge Reserved Instance but it has used three m4.xlarge instances concurrently for an hour.

As a Developer, explain how the instances are charged?

- ▫️ All instances are charged at one hour of Reserved Instance usage
- ▫️ All instances are charged at one hour of On-Demand Instance usage
- ▫️ One instance is charged at one hour of On-Demand usage and the other two instances are charged at two hours of Reserved Instance usage
- ✅ One instance is charged at one hour of Reserved Instance usage and the other two instances are charged at two hours of On-Demand usage

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «One instance is charged at one hour of Reserved Instance usage and the other two instances are charged at two hours of On-Demand usage»**

Reserved Instances дають знижку відносно On-Demand. Знижка RI застосовується максимум до 3 600 секунд (1 години) використання інстансу за кожну годину. Можна запустити кілька інстансів одночасно, але знижку отримаєте лише на 3 600 секунд на годину; решта оплачується за On-Demand.

Тож одна година йде за ставкою RI, а дві інші інстанс-години — за On-Demand.

Детальніше:
- https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-types.html
- https://aws.amazon.com/ec2/pricing/

**❌ Неправильні варіанти:**
- *All at RI* — знижка RI лише на 1 інстанс-годину на годину.
- *All at On-Demand* — одна година покривається RI.
- *One On-Demand, two RI* — навпаки: RI покриває максимум 3 600 с на годину.

</details>

---

<a id="t11-q59"></a>
### T11-Q59
*домен: офіційна мітка*

A multi-national company has just moved to AWS Cloud and it has configured forecast-based AWS Budgets alerts for cost management. However, no alerts have been received even though the account and the budgets have been created almost three weeks ago.

What could be the issue with the AWS Budgets configuration?

- ▫️ Account has to be part of AWS Organizations to receive AWS Budgets alerts
- ▫️ Amazon CloudWatch could be down and hence alerts are not being sent
- ▫️ Budget forecast has been created from an account that does not have enough privileges
- ✅ AWS requires approximately 5 weeks of usage data to generate budget forecasts

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS requires approximately 5 weeks of usage data to generate budget forecasts»**

AWS Budgets дає змогу задати власні бюджети й отримувати сповіщення, коли витрати чи використання перевищують бюджет або за прогнозом перевищать його. Для прогнозу AWS потрібно приблизно **5 тижнів** історії використання. Поки історії недостатньо, алерт на прогнозовану суму не спрацює.

**❌ Неправильні варіанти:**
- *Insufficient privileges* — без прав бюджет узагалі не вдалося б створити.
- *CloudWatch down* — CloudWatch повністю керований AWS; цей варіант лише для відволікання.
- *Must be in AWS Organizations* — окремі акаунти теж можуть створювати бюджети.

</details>

---
