# AWS Step Functions

**Домен 2 — Безпека** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#stepfn) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q35"></a>
### T07-Q35
*домен: офіційна мітка*

An e-commerce company has an order processing workflow with several tasks to be done in parallel as well as decision steps to be evaluated for successful processing of the order. All the tasks are implemented via Lambda functions.

Which of the following is the BEST solution to meet these business requirements?

- ✅ Use AWS Step Functions state machines to orchestrate the workflow
- ▫️ Use AWS Step Functions activities to orchestrate the workflow
- ▫️ Use AWS Batch to orchestrate the workflow
- ▫️ Use AWS Glue to orchestrate the workflow

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Step Functions state machines to orchestrate the workflow»**

Step Functions координує компоненти розподілених застосунків і мікросервісів через візуальні workflows. Застосунок складається з окремих компонентів, кожен з яких виконує одну функцію, тож його легко масштабувати й змінювати.

Ключові поняття — tasks і state machines. State machine описують мовою Amazon States Language на основі JSON; вона складається зі станів і зв'язків між ними. Стани приймають рішення на основі вхідних даних, виконують дії й передають результат далі — так state machine оркеструє workflow.

Детальніше:
- https://aws.amazon.com/step-functions/
- https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-state-machine-structure.html

**❌ Неправильні варіанти:**
- *Step Functions activities* — activity пов'язує задачу state machine з кодом деінде (activity worker на EC2 чи в Lambda), який сам опитує задачі. Activity не оркеструє workflow.
- *AWS Glue* — ETL-сервіс, він не оркеструє workflows.
- *AWS Batch* — запускає пакетні обчислення й підбирає під них ресурси, але не оркеструє workflow.

</details>

---
