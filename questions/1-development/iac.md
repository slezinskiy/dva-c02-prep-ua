# CloudFormation / SAM / CDK

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 7  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#iac) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q37"></a>
### T01-Q37
*домен: офіційна мітка*

A media analytics company has built a streaming application on Lambda using Serverless Application Model (SAM).

As a Developer Associate, which of the following would you identify as the correct order of execution to successfully deploy the application?

- ▫️ Develop the SAM template locally =&gt; upload the template to CodeCommit =&gt; deploy your application to CodeDeploy
- ▫️ Develop the SAM template locally =&gt; deploy the template to S3 =&gt; use your application in the cloud
- ▫️ Develop the SAM template locally =&gt; upload the template to Lambda =&gt; deploy your application to the cloud
- ✅ Develop the SAM template locally =&gt; upload the template to S3 =&gt; deploy your application to the cloud

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Develop the SAM template locally => upload the template to S3 => deploy your application to the cloud»**

AWS SAM — open-source фреймворк для serverless-застосунків зі скороченим синтаксисом для функцій, API, баз даних і event source mappings (YAML). Застосунок розробляють і тестують локально, потім `sam deploy` пакує артефакти в zip, завантажує їх у S3 і розгортає застосунок в AWS. Під капотом SAM використовує CloudFormation.

**❌ Неправильні варіанти:** варіанти із завантаженням шаблону в Lambda, CodeCommit/CodeDeploy або «deploy template to S3 → use in the cloud» суперечать описаному процесу.

</details>

---

<a id="t01-q60"></a>
### T01-Q60
*домен: офіційна мітка*

An IT company uses AWS CloudFormation templates to provision their AWS infrastructure for Amazon EC2, Amazon VPC, and Amazon S3 resources. Using cross-stack referencing, a developer creates a stack called NetworkStack which will export the subnetId that can be used when creating EC2 instances in another stack.

To use the exported value in another stack, which of the following functions must be used?

- ▫️ !Ref
- ✅ !ImportValue
- ▫️ !GetAtt
- ▫️ !Sub

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «!ImportValue»**

Внутрішня функція `Fn::ImportValue` повертає значення output, експортованого іншим стеком. Її використовують для cross-stack references.

**❌ Неправильні варіанти:**
- *!Ref* — значення параметра або ресурсу поточного шаблону.
- *!GetAtt* — атрибут ресурсу з поточного шаблону.
- *!Sub* — підставляє змінні в рядок.

</details>

---

<a id="t09-q30"></a>
### T09-Q30
*домен: офіційна мітка*

As a Developer, you are given a document written in YAML that represents the architecture of a serverless application. The first line of the document contains Transform: 'AWS::Serverless-2016-10-31'.

What does the Transform section in the document represent?

- ▫️ It represents an intrinsic function
- ▫️ Presence of Transform section indicates it is a CloudFormation Parameter
- ✅ Presence of Transform section indicates it is a Serverless Application Model (SAM) template
- ▫️ It represents a Lambda function definition

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Presence of Transform section indicates it is a Serverless Application Model (SAM) template»**

Шаблон CloudFormation — текстовий файл JSON чи YAML, що описує інфраструктуру. Обов'язкова в ньому лише секція `Resources`. Опціональна секція `Transform` задає макроси для обробки шаблону. Макрос `AWS::Serverless` перетворює шаблон із синтаксисом SAM на звичайний шаблон CloudFormation, тож наявність `Transform` означає SAM-шаблон.

**❌ Неправильні варіанти:**
- *Lambda function definition* — функцію створює ресурс `AWS::Lambda::Function`, Transform тут ні до чого.
- *Intrinsic function* — intrinsic-функції підставляють значення, відомі лише під час виконання: `Fn::Sub`, `!Sub` тощо.
- *CloudFormation Parameter* — параметри описуються в секції `Parameters`.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-aws-serverless.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html>

</details>

---

<a id="t09-q52"></a>
### T09-Q52
*домен: офіційна мітка*

As an AWS certified developer associate, you are working on an AWS CloudFormation template that will create resources for a company's cloud infrastructure. Your template is composed of three stacks which are Stack-A, Stack-B, and Stack-C. Stack-A will provision a VPC, a security group, and subnets for public web applications that will be referenced in Stack-B and Stack-C.

After running the stacks you decide to delete them, in which order should you do it?

- ✅ Stack B, then Stack C, then Stack A
- ▫️ Stack C then Stack A then Stack B
- ▫️ Stack A, then Stack B, then Stack C
- ▫️ Stack A, Stack C then Stack B

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Stack B, then Stack C, then Stack A»**

CloudFormation створює набір пов'язаних ресурсів упорядковано і передбачувано. Stack A експортує значення, а B і C його імпортують. Поки хоч один стек імпортує значення, експортуючий стек не можна видалити і значення не можна змінити. Тож спочатку видаляють B і C, а потім A.

Детальніше: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-exports.html

**❌ Неправильні варіанти:**
- *A, B, C* — A не можна видалити першим: на нього посилаються.
- *A, C, B* — те саме.
- *C, A, B* — C першим можна, але B треба видалити раніше за A.

</details>

---

<a id="t11-q28"></a>
### T11-Q28
*домен: офіційна мітка*

As an AWS Certified Developer Associate, you are given a document written in YAML that represents the architecture of a serverless application. The first line of the document contains Transform: 'AWS::Serverless-2016-10-31'.

What does the Transform section in the document represent?

- ▫️ Presence of Transform section indicates it is a CloudFormation Parameter
- ✅ Presence of Transform section indicates it is a Serverless Application Model (SAM) template
- ▫️ It represents a Lambda function definition
- ▫️ It represents an intrinsic function

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Presence of Transform section indicates it is a Serverless Application Model (SAM) template»**

Шаблон CloudFormation — JSON чи YAML, де обов'язкова лише секція `Resources`. Опціональна `Transform` задає макроси для обробки шаблону. SAM — open-source фреймворк зі скороченим синтаксисом для функцій, API, БД і event source mappings. Макрос `AWS::Serverless` перетворює SAM-синтаксис на звичайний шаблон CloudFormation, тож `Transform` означає SAM-шаблон.

Анатомія шаблону: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

**❌ Неправильні варіанти:**
- *Lambda function definition* — функцію створює `AWS::Lambda::Function`.
- *Intrinsic function* — це `Fn::…` або `!…`, наприклад `!Sub`, і вони підставляють значення під час виконання.
- *CloudFormation Parameter* — параметри описуються в секції `Parameters`.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-aws-serverless.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html>

</details>

---

<a id="t11-q30"></a>
### T11-Q30
*домен: офіційна мітка*

You are creating a Cloud Formation template to deploy your CMS application running on an EC2 instance within your AWS account. Since the application will be deployed across multiple regions, you need to create a map of all the possible values for the base AMI.

How will you invoke the !FindInMap function to fulfill this use case?

- ▫️ !FindInMap [ MapName ]
- ▫️ !FindInMap [ MapName, TopLevelKey, SecondLevelKey, ThirdLevelKey ]
- ▫️ !FindInMap [ MapName, TopLevelKey ]
- ✅ !FindInMap [ MapName, TopLevelKey, SecondLevelKey ]

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]»**

`Fn::FindInMap` повертає значення з **дворівневої** мапи із секції `Mappings`. Повна форма — `Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]`, коротка — `!FindInMap [ ... ]`.
- **MapName** — логічне ім'я мапи.
- **TopLevelKey** — ключ верхнього рівня, наприклад регіон.
- **SecondLevelKey** — ключ другого рівня, наприклад архітектура.

```yaml
Mappings:
  RegionMap:
    us-east-1: { HVM64: "ami-0ff8a91507f77f867", HVMG2: "ami-0a584ac55a7631c0c" }
    us-west-1: { HVM64: "ami-0bdb828fd58c52235", HVMG2: "ami-066ee5fd4a9ef77f1" }
Resources:
  myEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !FindInMap [ RegionMap, !Ref 'AWS::Region', HVM64 ]
      InstanceType: m1.small
```

Для HVM64 в us-east-1 повернеться `ami-0ff8a91507f77f867`.

**❌ Неправильні варіанти:**
- *[ MapName, TopLevelKey ]* — потрібні рівно три аргументи.
- *[ MapName ]* — потрібні рівно три аргументи.
- *[ …, ThirdLevelKey ]* — мапа лише дворівнева.

</details>

---

<a id="t12-q07"></a>
### T12-Q07
*домен: офіційна мітка*

As an AWS Certified Developer Associate, you are writing a CloudFormation template in YAML. The template consists of an EC2 instance creation and one RDS resource. Once your resources are created you would like to output the connection endpoint for the RDS database.

Which intrinsic function returns the value needed?

- ▫️ !Sub
- ▫️ !Ref
- ▫️ !FindInMap
- ✅ !GetAtt

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «!GetAtt»**

Intrinsic-функції підставляють у шаблон значення, відомі лише під час виконання. `Fn::GetAtt` повертає значення атрибута ресурсу. Наприклад, DNS-ім'я балансувальника `myELB`:
- YAML: `!GetAtt myELB.DNSName`
- JSON: `"Fn::GetAtt": ["myELB", "DNSName"]`

**❌ Неправильні варіанти:**
- *!Sub* — підставляє змінні в рядок.
- *!Ref* — повертає значення параметра або ID ресурсу, а не довільний атрибут.
- *!FindInMap* — значення з дворівневої мапи в секції `Mappings`.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getatt.html>

</details>

---
