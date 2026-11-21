# AWS Step Functions

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#stepfn) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q54"></a>
### T02-Q54
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company is using an AWS Step Functions state machine. When testing the state machine errors were experienced in the Step Functions task state machine. To troubleshoot the issue a developer requires that the state input be included along with the error message in the state output.

Which coding practice can preserve both the original input and the error for the state?

- ▫️ Use ErrorEquals in a Retry statement to include the original input with the error.
- ▫️ Use InputPath in a Catch statement to include the original input with the error.
- ✅ Use ResultPath in a Catch statement to include the original input with the error.
- ▫️ Use OutputPath in a Retry statement to include the original input with the error.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use ResultPath in a Catch statement to include the original input with the error»**

Виконання Step Functions отримує JSON на вхід і передає його першому стану; стани отримують JSON і передають JSON далі. Потік JSON між станами контролюють поля `InputPath`, `OutputPath`, `ResultPath`, `Parameters`, `ResultSelector`.

`ResultPath` поєднує результат задачі з її входом або вибирає одне з них, тобто визначає, що піде на вихід. У `Catch` він дає змогу додати помилку до вихідного входу, а не замінити вхід нею (наприклад, `"ResultPath": "$.error"`).

**❌ Неправильні варіанти:**
- *InputPath in a Catch* — вибирає частину вхідних даних стану.
- *ErrorEquals in a Retry* — визначає, на які помилки робити повтор.
- *OutputPath in a Retry* — вибирає частину виходу для наступного стану (фільтрує зайве).

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/input-output-resultpath.html> · <https://docs.aws.amazon.com/step-functions/latest/dg/tutorial-handling-error-conditions.html>

</details>

---
