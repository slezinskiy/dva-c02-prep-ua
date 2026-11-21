# CloudFormation / SAM / CDK

**Домен 4 — Усунення несправностей та оптимізація** · питань: 4  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#iac) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q07"></a>
### T07-Q07
*домен: офіційна мітка*

You are working for a shipping company that is automating the creation of ECS clusters with an Auto Scaling Group using an AWS CloudFormation template that accepts cluster name as its parameters. Initially, you launch the template with input value 'MainCluster', which deployed five instances across two availability zones. The second time, you launch the template with an input value 'SecondCluster'. However, the instances created in the second run were also launched in 'MainCluster' even after specifying a different cluster name.

What is the root cause of this issue?

- ▫️ The EC2 instance is missing IAM permissions to join the other clusters
- ✅ The cluster name Parameter has not been updated in the file /etc/ecs/ecs.config during bootstrap
- ▫️ The security groups on the EC2 instance are pointing to the wrong ECS cluster
- ▫️ The ECS agent Docker image must be re-built to connect to the other clusters

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The cluster name Parameter has not been updated in the file /etc/ecs/ecs.config during bootstrap»**

Щоб container instance зареєструвався в потрібному кластері, у `/etc/ecs/ecs.config` вказують `ECS_CLUSTER=your_cluster_name`. Якщо параметр не задано, інстанс реєструється в кластері `default`. Тут, найімовірніше, в User Data не оновили ім'я кластера.

**❌ Неправильні варіанти:**
- *Missing IAM permissions* — інстанси реєструються в першому кластері, тож із дозволами все гаразд.
- *Rebuild ECS agent image* — перші інстанси з того ж шаблону працюють, тож агент справний.
- *Security groups pointing to the wrong cluster* — security groups керують мережевим трафіком і на кластер не «вказують».

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/bootstrap_container_instance.html> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html>

</details>

---

<a id="t09-q08"></a>
### T09-Q08
*домен: офіційна мітка*

A developer is building a serverless application on AWS and wants to establish an accelerated development workflow. The workflow must allow the developer to deploy incremental changes for testing without deploying the entire application for every code commit. The developer wants to streamline the process while minimizing deployment time.

What should the developer do to meet these requirements?

- ▫️ Use the cdk diff command from the AWS Cloud Development Kit (AWS CDK) to deploy incremental changes to AWS for testing
- ✅ Use the sam sync command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes
- ▫️ Use the sam deploy command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes
- ▫️ Use the cdk deploy command from the AWS Cloud Development Kit (AWS CDK) to deploy incremental changes to AWS for testing

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the sam sync command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes»**

`sam sync` синхронізує локальні зміни з розгорнутим serverless-застосунком в AWS. Це дає швидку ітерацію: інкрементальні зміни перевіряються без повного перерозгортання стеку. Режим `sam sync --watch` стежить за змінами автоматично.

**❌ Неправильні варіанти:**
- *cdk deploy* — загальний деплой CDK-стеків, не оптимізований під інкрементальні зміни SAM-застосунку.
- *sam deploy* — повний деплой через CloudFormation навіть для дрібних змін, тож довше.
- *cdk diff* — лише показує різницю між локальним і розгорнутим стеком, нічого не деплоїть.

</details>

---

<a id="t09-q54"></a>
### T09-Q54
*домен: офіційна мітка*

Your team lead has asked you to learn AWS CloudFormation to create a collection of related AWS resources and provision them in an orderly fashion. You decide to provide AWS-specific parameter types to catch invalid values.

When specifying parameters which of the following is not a valid Parameter type?

- ▫️ AWS::EC2::KeyPair::KeyName
- ✅ DependentParameter
- ▫️ CommaDelimitedList
- ▫️ String

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «DependentParameter»**

Питання на пошук **недопустимого** типу параметра. Типи параметрів дають CloudFormation змогу перевірити вхідні дані раніше. Підтримуються:
- `String`, `Number`, `List<Number>`, `CommaDelimitedList`;
- `AWS::EC2::KeyPair::KeyName`, `AWS::EC2::SecurityGroup::Id`, `AWS::EC2::Subnet::Id`, `AWS::EC2::VPC::Id`;
- `List<AWS::EC2::VPC::Id>`, `List<AWS::EC2::SecurityGroup::Id>`, `List<AWS::EC2::Subnet::Id>`;
- а також SSM parameter types.

Параметри незалежні одне від одного, тож типу `DependentParameter` не існує.

**❌ Інші варіанти — допустимі типи:**
- *String*;
- *CommaDelimitedList*;
- *AWS::EC2::KeyPair::KeyName*.

</details>

---

<a id="t11-q03"></a>
### T11-Q03
*домен: офіційна мітка*

A company has built its technology stack on AWS serverless architecture for managing all its business functions. To expedite development for a new business requirement, the company is looking at using pre-built serverless applications.

Which AWS service represents the easiest solution to address this use-case?

- ▫️ AWS Service Catalog
- ✅ AWS Serverless Application Repository (SAR)
- ▫️ AWS Marketplace
- ▫️ AWS AppSync

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Serverless Application Repository (SAR)»**

SAR — керований репозиторій serverless-застосунків. Команди й розробники зберігають і поширюють готові застосунки та збирають з них serverless-архітектури. Клонувати, збирати й пакувати код перед деплоєм не потрібно: беруть готові застосунки. Так менше дублювання роботи, дотримуються внутрішні стандарти і швидше виходять на ринок. Через IAM можна ділитися застосунком публічно або лише з певними акаунтами.

Детальніше: https://aws.amazon.com/serverless/serverlessrepo/

**❌ Неправильні варіанти:**
- *AWS Marketplace* — магазин, де партнери продають ПЗ клієнтам AWS.
- *AWS AppSync* — керовані GraphQL API.
- *AWS Service Catalog* — каталог схвалених ІТ-сервісів для централізованого керування й governance.

</details>

---
