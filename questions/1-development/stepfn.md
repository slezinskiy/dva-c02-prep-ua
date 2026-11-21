# AWS Step Functions

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 7  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#stepfn) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t04-q40"></a>
### T04-Q40
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A legacy application is being refactored into a microservices architecture running on AWS. The microservice will include several AWS Lambda functions. A Developer will use AWS Step Functions to coordinate function execution.

How should the Developer proceed?

- ▫️ Create an AWS CloudFormation stack using a YAML-formatted template
- ▫️ Create a layer in AWS Lambda and add the functions to the layer
- ✅ Create a state machine using the Amazon States Language
- ▫️ Create a workflow using the StartExecution API action

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a state machine using the Amazon States Language»**

AWS Step Functions координує компоненти розподілених застосунків і мікросервісів візуальними workflow. Основне:
- побудований на задачах (tasks) і state machines;
- state machine описують мовою **Amazon States Language** (JSON);
- консоль показує граф state machine для перевірки логіки й моніторингу виконань.

Щоб координувати Lambda-функції, треба створити state machine на ASL.

**❌ Неправильні варіанти:**
- *CloudFormation stack (YAML)* — Step Functions не вимагає CloudFormation; потрібна саме state machine.
- *Workflow using the StartExecution API* — «workflow» — поняття SWF, а `StartExecution` лише запускає вже створену state machine.
- *Lambda layer with the functions* — layer — ZIP з бібліотеками чи runtime, а не засіб оркестрації.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html>

</details>

---

<a id="t07-q25"></a>
### T07-Q25
*домен: офіційна мітка*

A team is checking the viability of using AWS Step Functions for creating a banking workflow for loan approvals. The web application will also have human approval as one of the steps in the workflow.

As a developer associate, which of the following would you identify as the key characteristics for AWS Step Function? (Select two)

- ✅ You should use Express Workflows for workloads with high event rates and short duration
- ✅ Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that can also support any human approval steps
- ▫️ Express Workflows have a maximum duration of five minutes and Standard workflows have a maximum duration of 180 days or 6 months
- ▫️ Both Standard and Express Workflows support all service integrations, activities, and design patterns
- ▫️ Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that do not support any human approval steps

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «You should use Express Workflows for workloads with high event rates and short duration» + «Standard Workflows on AWS Step Functions are suitable for long-running, durable, and auditable workflows that can also support any human approval steps»**

**Standard Workflows** підходять для довгих, надійних і аудитованих процесів, де повторити крок дорого (перезапуск довгого транскодування) або шкідливо (повторне списання з картки). Приклади: навчання й деплой ML-моделей, звіти, білінг, обробка платежів, замовлення. Підтримують кроки з ручним схваленням.

**Express Workflows** — для великої кількості подій і коротких виконань: понад 100 000 подій на секунду.

**❌ Неправильні варіанти:**
- *Standard does not support human approval* — підтримує, через callback із task token.
- *Express 5 min, Standard 180 days / 6 months* — Express справді до 5 хвилин, але Standard — до **1 року**.
- *Both support all integrations and patterns* — Express не підтримує activities, `.sync` (job-run) і callback (`.waitForTaskToken`).

</details>

---

<a id="t07-q44"></a>
### T07-Q44
*домен: офіційна мітка*

A company wants to automate and orchestrate a multi-source high-volume flow of data in a scalable data management solution built using AWS services. The solution must ensure that the business rules and transformations run in sequence, handle reprocessing of data in case of errors, and require minimal maintenance.

Which AWS service should the company use to manage and automate the orchestration of the data flows?

- ✅ AWS Step Functions
- ▫️ AWS Glue
- ▫️ Amazon Kinesis Data Streams
- ▫️ AWS Batch

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Step Functions»**

Step Functions — сервіс візуальних workflows. З ним будують розподілені застосунки з сервісів AWS, автоматизують процеси, оркеструють мікросервіси і створюють пайплайни даних та ML.

**❌ Неправильні варіанти:**
- *Kinesis Data Streams* — serverless-сервіс для збору, обробки й зберігання потоків даних; не оркеструє.
- *AWS Glue* — serverless-інтеграція даних (ETL) для аналітики й ML; не оркеструє.
- *AWS Batch* — пакетні обчислення з автоматичним підбором ресурсів; не оркеструє.

Джерела: <https://aws.amazon.com/step-functions/> · <https://aws.amazon.com/kinesis/data-streams/> · <https://aws.amazon.com/glue/> · <https://aws.amazon.com/batch/faqs/>

</details>

---

<a id="t09-q27"></a>
### T09-Q27
*домен: офіційна мітка*

A company wants to automate its order fulfillment and inventory tracking workflow. Starting from order creation to updating inventory to shipment, the entire process has to be tracked, managed and updated automatically.

Which of the following would you recommend as the most optimal solution for this requirement?

- ▫️ Use Amazon Simple Queue Service (Amazon SQS) queue to pass information from order management to inventory tracking workflow
- ✅ Use AWS Step Functions to coordinate and manage the components of order management and inventory tracking workflow
- ▫️ Use Amazon SNS to develop event-driven applications that can share information
- ▫️ Configure Amazon EventBridge to track the flow of work from order management to inventory tracking systems

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Step Functions to coordinate and manage the components of order management and inventory tracking workflow»**

Step Functions — serverless-оркестратор, який вибудовує Lambda-функції та сервіси AWS у бізнес-процеси. Через візуальний інтерфейс створюють workflows з контрольними точками, що зберігають стан. Вихід одного кроку стає входом наступного, і кроки виконуються в порядку бізнес-логіки. Крок може викликати Lambda чи контейнер, оновити DynamoDB або надіслати повідомлення в чергу.

Переваги:
- **Швидка розробка** — бізнес-вимоги перетворюються на візуальні workflows, компоненти легко міняти місцями.
- **Стійкість** — стан, контрольні точки й перезапуски керуються автоматично; є вбудовані try/catch, retry і rollback.
- **Менше коду** — розгалуження, паралельність і timeouts вбудовані.

Як працює: https://aws.amazon.com/step-functions/

**❌ Неправильні варіанти:**
- *SQS* — надійна черга для обміну повідомленнями, але відстеження стану workflow доведеться писати самим. Step Functions сам відстежує всі задачі й події.
- *EventBridge* — для застосунків, що реагують на події SaaS і сервісів AWS; єдиний подієвий сервіс з прямою інтеграцією із SaaS-партнерами. Але workflows не оркеструє.
- *SNS* — для високої пропускної здатності й fan-out на тисячі чи мільйони endpoints, а не для оркестрації.

Джерела: <https://aws.amazon.com/step-functions/faqs/> · <https://aws.amazon.com/eventbridge/faqs/>

</details>

---

<a id="t09-q38"></a>
### T09-Q38
*домен: офіційна мітка*

While defining a business workflow as state machine on AWS Step Functions, a developer has configured several states.

Which of the following would you identify as the state that represents a single unit of work performed by a state machine?

- ▫️

  ```json
  "No-op": {
    "Type": "Task",
    "Result": {
      "x-datum": 0.381018,
      "y-datum": 622.2269926397355
    },
    "ResultPath": "$.coords",
    "Next": "End"
  }
  ```
- ▫️

  ```json
  "wait_until" : {
    "Type": "Wait",
    "Timestamp": "2016-03-14T01:59:00Z",
    "Next": "NextState"
  }
  ```
- ▫️

  ```json
  "FailState": {
    "Type": "Fail",
    "Cause": "Invalid response.",
    "Error": "ErrorA"
  }
  ```
- ✅

  ```json
  "HelloWorld": {
    "Type": "Task",
    "Resource": "arn:aws:lambda:us-east-1:123456789012:function:HelloFunction",
    "Next": "AfterHelloWorldState",
    "Comment": "Run the HelloWorld Lambda function"
  }
  ```

<details><summary>Пояснення</summary>

**✅ Правильна відповідь:**
```json
"HelloWorld": {
  "Type": "Task",
  "Resource": "arn:aws:lambda:us-east-1:123456789012:function:HelloFunction",
  "Next": "AfterHelloWorldState",
  "Comment": "Run the HelloWorld Lambda function"
}
```

Стан Task (`"Type": "Task"`) — одна одиниця роботи state machine. Усю роботу в state machine виконують задачі: через activity, Lambda-функцію або виклик API інших сервісів. Step Functions може викликати Lambda напряму з Task-стану.

**❌ Неправильні варіанти:**
- *`"Type": "Wait"`* — стан Wait лише затримує виконання до заданого часу.
- *`"No-op"` з `Result`* — у Task обов'язкове поле `Resource`; без нього це по суті стан Pass.
- *`"Type": "Fail"`* — зупиняє виконання з помилкою, якщо її не перехопить Catch. Не має `Next`, і `End` йому не потрібен.

</details>

---

<a id="t10-q18"></a>
### T10-Q18
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

An IT automation architecture uses many AWS Lambda functions invoking one another as a large state machine. The coordination of this state machine is legacy custom code that breaks easily.
Which AWS Service can help refactor and manage the state machine?

- ▫️ AWS CloudFormation
- ▫️ AWS CodeBuild
- ▫️ AWS CodePipeline
- ✅ AWS Step Functions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Step Functions»**

Step Functions поєднує сервіси AWS (Lambda, Fargate, SageMaker тощо) у serverless-workflows. Workflow складається з кроків, і вихід одного стає входом наступного. Його видно як діаграму state machine, яку легко зрозуміти, пояснити й змінити.

Step Functions сам запускає й відстежує кожен крок і повторює його при помилках, тож застосунок виконується в правильному порядку. Підходить і для довгих процесів: навчання ML-моделей, звітів, ІТ-автоматизації. Тож це найкращий вибір для рефакторингу legacy-коду.

**❌ Неправильні варіанти:**
- *CloudFormation* — розгортає ресурси, але не автоматизує поточні процеси.
- *CodePipeline* — CI/CD для оновлень застосунку.
- *CodeBuild* — збирання й тестування.

Джерела: <https://aws.amazon.com/step-functions/>

</details>

---

<a id="t10-q40"></a>
### T10-Q40
*домен: визначено автоматично (у джерелі: AWS Application Integration)*

A company currently runs a number of legacy automated batch processes for system update management and operational activities. The company are looking to refactor these processes and require a service that can coordinate multiple AWS services into serverless workflows.

What is the MOST suitable service for this requirement?

- ▫️ Amazon SWF
- ▫️ AWS Batch
- ▫️ AWS Lambda
- ✅ AWS Step Functions

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Step Functions»**

Step Functions координує компоненти розподілених застосунків і мікросервісів через візуальні workflows. Що він робить:
- застосунок складається з окремих компонентів-задач, тож його легко масштабувати й змінювати;
- графічна консоль показує компоненти як послідовність кроків;
- кожен крок запускається і відстежується автоматично, з повторами при помилках;
- стан кожного кроку логується, тож проблеми швидко діагностуються.

**❌ Неправильні варіанти:**
- *Amazon SWF* — керований трекер стану і координатор задач, але не для serverless-workflows.
- *AWS Batch* — пакетні обчислення на EC2, не serverless.
- *AWS Lambda* — serverless, але не координує кілька сервісів.

Джерела: <https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html>

</details>

---
