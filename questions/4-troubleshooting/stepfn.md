# AWS Step Functions

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#stepfn) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q54"></a>
### T02-Q54
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company is using an AWS Step Functions state machine. When testing the state machine errors were experienced in the Step Functions task state machine. To troubleshoot the issue a developer requires that the state input be included along with the error message in the state output.

Which coding practice can preserve both the original input and the error for the state?

- ❌ Use ErrorEquals in a Retry statement to include the original input with the error.
- ▫️ Use InputPath in a Catch statement to include the original input with the error.
- ✅ Use ResultPath in a Catch statement to include the original input with the error.
- ▫️ Use OutputPath in a Retry statement to include the original input with the error.

<details><summary>Пояснення (оригінал, EN)</summary>

A Step Functions execution receives a JSON text as input and passes that input to the first state in the workflow. Individual states receive JSON as input and usually pass JSON as output to the next state.

In the Amazon States Language, these fields filter and control the flow of JSON from state to state:

• InputPath

• OutputPath

• ResultPath

• Parameters

• ResultSelector

Use ResultPath to combine a task result with task input, or to select one of these. The path you provide to ResultPath controls what information passes to the output. Use ResultPath in a Catch to include the error with the original input, instead of replacing it. The following code is an example of this tactic:

CORRECT: "Use ResultPath in a Catch statement to include the original input with the error" is the correct answer (as explained above.)

INCORRECT: "Use InputPath in a Catch statement to include the original input with the error" is incorrect.

You can use InputPath to select a portion of the state input.

INCORRECT: "Use ErrorEquals in a Retry statement to include the original input with the error" is incorrect.

A retry is used to attempt to retry the process that caused the error based on the retry policy described by ErrorEquals.

INCORRECT: "Use OutputPath in a Retry statement to include the original input with the error" is incorrect.

OutputPath enables you to select a portion of the state output to pass to the next state. This enables you to filter out unwanted information and pass only the portion of JSON that you care about.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/input-output-resultpath.html> · <https://docs.aws.amazon.com/step-functions/latest/dg/tutorial-handling-error-conditions.html>

</details>

---
