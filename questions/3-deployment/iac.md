# CloudFormation / SAM / CDK

**Домен 3 — Розгортання** · питань: 33  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#iac) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q01"></a>
### T02-Q01
*домен: визначено автоматично (у джерелі: AWS Compute)*

A business operates a web app on Amazon EC2 instances utilizing a bespoke Amazon Machine Image (AMI). They employ AWS CloudFormation for deploying their app, which is currently active in the us-east-1 Region. However, their goal is to extend the deployment to the us-west-1 Region.

During an initial attempt to create an AWS CloudFormation stack in us-west-1, the action fails, and an error message indicates that the AMI ID does not exist. A developer is tasked with addressing this error through a method that minimizes operational complexity.

Which action should the developer take?

- ▫️ Use AWS Lambda to create an AMI in the us-west-1 Region during stack creation.
- ▫️ Create a new AMI in the us-west-1 Region and update the CloudFormation template with the new AMI ID.
- ✅ Copy the AMI from the us-east-1 Region to the us-west-1 Region and use the new AMI ID in the CloudFormation template.
- ▫️ Modify the CloudFormation template to refer to the AMI in us-east-1 Region.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Copy the AMI from the us-east-1 Region to the us-west-1 Region and use the new AMI ID in the CloudFormation template»**

AMI — регіональний ресурс. Копіювання AMI в інший регіон дає той самий образ там з мінімальними зусиллями; лишається вказати новий AMI ID у шаблоні (зазвичай через Mappings).

**❌ Неправильні варіанти:**
- *Create a new AMI in the us-west-1 Region* — операційно складно й довго.
- *Modify the template to refer to the AMI in us-east-1* — AMI не можна напряму використати в іншому регіоні.
- *Use AWS Lambda to create an AMI during stack creation* — зайва складність, і новий AMI не буде ідентичним оригіналу.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html>

</details>

---

<a id="t03-q04"></a>
### T03-Q04
*домен: офіційна мітка*

The development team at a company is looking at building an AWS CloudFormation template that self-populates the AWS Region variable while deploying the CloudFormation template.

What is the MOST operationally efficient way to determine the Region in which the template is being deployed?

- ✅ Use the AWS::Region pseudo parameter
- ▫️ Create a CloudFormation parameter for Region and let the desired value be populated at the time of deployment
- ▫️ Create an AWS Lambda-backed custom resource for Region and let the desired value be populated at the time of deployment by the Lambda
- ▫️ Set up a mapping containing the key and the named values for all AWS Regions and then have the CloudFormation template auto-select the desired value

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the AWS::Region pseudo parameter»**

Pseudo parameters — заздалегідь визначені CloudFormation параметри, які не треба оголошувати в шаблоні. Їх використовують, як звичайні параметри, наприклад через `!Ref "AWS::Region"`. `AWS::Region` повертає регіон, у якому створюється ресурс, наприклад `us-west-2`.

**❌ Неправильні варіанти:**
- *CloudFormation parameter for Region* — можливо, але менш ефективно, ніж готовий pseudo parameter.
- *Mapping with all Regions* — Mappings зіставляють ключ із набором значень (через `Fn::FindInMap`), але регіон з них «автоматично» не вибирається.
- *Lambda-backed custom resource* — custom resources потрібні для власної логіки провізіонінгу; регіон не є ресурсом, який треба створювати. Дистрактор.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html>

</details>

---

<a id="t03-q37"></a>
### T03-Q37
*домен: офіційна мітка*

Your company is in the process of building a DevOps culture and is moving all of its on-premise resources to the cloud using serverless architectures and automated deployments. You have created a CloudFormation template in YAML that uses an AWS Lambda function to pull HTML files from GitHub and place them into an Amazon Simple Storage Service (S3) bucket that you specify.

Which of the following AWS CLI commands can you use to upload AWS Lambda functions and AWS CloudFormation templates to AWS?

- ✅ cloudformation package and cloudformation deploy
- ▫️ cloudformation zip and cloudformation upload
- ▫️ cloudformation zip and cloudformation deploy
- ▫️ cloudformation package and cloudformation upload

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «cloudformation package and cloudformation deploy»**

`aws cloudformation package` пакує локальні артефакти, на які посилається шаблон (наприклад, код Lambda), завантажує їх в S3 і переписує шаблон з посиланнями на S3. `aws cloudformation deploy` розгортає шаблон, створюючи і виконуючи change set.

**❌ Неправильні варіанти:** команд `cloudformation upload` і `cloudformation zip` не існує.

</details>

---

<a id="t03-q44"></a>
### T03-Q44
*домен: офіційна мітка*

An IT company is using AWS CloudFormation to manage its IT infrastructure. It has created a template to provision a stack with a VPC and a subnet. The output value of this subnet has to be used in another stack.

As a Developer Associate, which of the following options would you suggest to provide this information to another stack?

- ✅ Use 'Export' field in the Output section of the stack's template
- ▫️ Use Fn::ImportValue
- ▫️ Use 'Expose' field in the Output section of the stack's template
- ▫️ Use Fn::Transform

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use 'Export' field in the Output section of the stack's template»**

Щоб передати дані між стеками, значення output експортують: поле `Export` у секції Outputs. Інші стеки того ж акаунта й регіону імпортують його через `Fn::ImportValue`.

**❌ Неправильні варіанти:**
- *'Expose' field* — вигадано.
- *Fn::ImportValue* — використовується в іншому стеку для імпорту, а питання про те, як надати значення.
- *Fn::Transform* — вказує макрос для обробки частини шаблону.

</details>

---

<a id="t04-q06"></a>
### T04-Q06
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer is creating a script to automate the deployment process for a serverless application. The Developer wants to use an existing AWS Serverless Application Model (SAM) template for the application.

What should the Developer use for the project? (Select TWO.)

- ▫️ Call aws s3 cp to upload the AWS SAM template to Amazon S3. Call aws lambda update-function-code to create the application
- ▫️ Create a ZIP package and upload it to Amazon S3. Call aws cloudformation create-stack to create the application
- ✅ Call sam package to create the deployment package. Call sam deploy to deploy the package afterward
- ▫️ Create a ZIP package locally and call aws serverlessrepo create-application to create the application
- ✅ Call aws cloudformation package to create the deployment package. Call aws cloudformation deploy to deploy the package afterward

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «Call sam package … then sam deploy» та «Call aws cloudformation package … then aws cloudformation deploy»**

AWS SAM — open-source фреймворк для serverless-застосунків зі скороченим YAML-синтаксисом; при деплої SAM перетворює його на CloudFormation. SAM CLI дає локальне середовище, схоже на Lambda, для збірки, тестування й дебагу, а також деплой в AWS. Запакувати й задеплоїти можна двома командами — `sam package` + `sam deploy` — або їхніми аналогами `aws cloudformation package` + `aws cloudformation deploy`.

**❌ Неправильні варіанти:**
- *aws s3 cp + aws lambda update-function-code* — так SAM-шаблони не використовують.
- *aws serverlessrepo create-application* — створює застосунок у Serverless Application Repository, а не розгортає шаблон.
- *Upload ZIP to S3 + aws cloudformation create-stack* — для SAM-шаблону не потрібно, є package/deploy.

Джерела: <https://aws.amazon.com/serverless/sam/>

</details>

---

<a id="t05-q23"></a>
### T05-Q23
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

The source code for an application is stored in a file named index.js that is in a folder along with a template file that includes the following code:

AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Resources:
LambdaFunctionWithAPI:
Type: AWS::Serverless::Function
Properties:
Handler: index.handler
Runtime: nodejs12.x
What does a Developer need to do to prepare the template so it can be deployed using an AWS CLI command?

- ✅ Run the aws cloudformation package command to upload the source code to an Amazon S3 bucket and produce a modified CloudFormation template
- ▫️ Run the aws lambda zip command to package the source file together with the CloudFormation template and deploy the resulting zip archive
- ▫️ Run the aws serverless create-package command to embed the source file directly into the existing CloudFormation template
- ▫️ Run the aws cloudformation compile command to base64 encode and embed the source file into a modified CloudFormation template

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Run the aws cloudformation package command to upload the source code to an Amazon S3 bucket and produce a modified CloudFormation template»**

Це SAM-шаблон: на це вказує заголовок `Transform: 'AWS::Serverless-2016-10-31'`. Шаблон треба спочатку запакувати, потім задеплоїти. Код має лежати в тій самій директорії або бути вказаним через `CodeUri`.

Команди `aws cloudformation package` або `sam package`:
- завантажують локальні артефакти (код Lambda, Swagger-файл API Gateway) в S3;
- повертають копію шаблону, де локальні шляхи замінено на S3-адреси.

Далі шаблон деплоять командою `aws cloudformation deploy` або `sam deploy`. Приклад:

`aws cloudformation package --template-file /path_to_template/template.json --s3-bucket bucket-name --output-template-file packaged-template.json`

**❌ Неправильні варіанти:**
- *aws cloudformation compile (base64)* — такої команди немає; потрібен `package`.
- *aws lambda zip* — такої команди немає; `package` сам копіює потрібні файли в S3.
- *aws serverless create-package* — такої команди немає; є лише `aws cloudformation package` або `sam package`.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html>

</details>

---

<a id="t05-q33"></a>
### T05-Q33
*домен: визначено автоматично (у джерелі: AWS Compute)*

To include objects defined by the AWS Serverless Application Model (SAM) in an AWS CloudFormation template, in addition to Resources, what section MUST be included in the document root?

- ▫️ Conditions
- ▫️ Properties
- ✅ Transform
- ▫️ Globals

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Transform»**

Чим SAM-шаблон відрізняється від CloudFormation:
- **Transform** — `Transform: AWS::Serverless-2016-10-31` обов'язковий і саме він робить шаблон SAM-шаблоном.
- **Globals** — є лише в SAM; задає спільні властивості для всіх `AWS::Serverless::Function`, `Api` і `SimpleTable`.
- **Resources** — може містити і ресурси CloudFormation, і ресурси SAM.

Обов'язкові лише Transform і Resources; Globals — опціональна секція.

**❌ Неправильні варіанти:**
- *Globals* — секція не обов'язкова.
- *Conditions* — секція опціональна.
- *Properties* — це не секція шаблону, а частина ресурсу.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html>

</details>

---

<a id="t05-q42"></a>
### T05-Q42
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is creating an AWS Serverless Application Model (AWS SAM) template. It includes several AWS Lambda functions, an Amazon S3 bucket, and an Amazon CloudFront distribution. One Lambda function, running on Lambda@Edge, is integrated with the CloudFront distribution, while the S3 bucket serves as an origin for the distribution.

However, upon deploying the AWS SAM blueprint in the us-west-1 Region, the stack's creation fails.

What could be the possible reason for this failure?

- ▫️ AWS Lambda functions integrated with CloudFront cannot be deployed using AWS SAM templates.
- ✅ Lambda@Edge functions can only be deployed in the us-east-1 Region.
- ▫️ AWS SAM templates are not supported in the us-west-1 Region.
- ▫️ Amazon S3 buckets serving as origins for CloudFront must be created in a separate Region from the CloudFront distribution.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Lambda@Edge functions can only be deployed in the us-east-1 Region.»**

Функції Lambda@Edge створюються лише в регіоні us-east-1 (N. Virginia), звідки CloudFront реплікує їх у edge-локації. Стек у us-west-1 тому й впав.

**❌ Неправильні варіанти:**
- *SAM not supported in us-west-1* — такого обмеження немає.
- *S3 origin must be in a separate Region* — бакет-origin може бути в будь-якому регіоні, причиною збою це не буде.
- *Lambda integrated with CloudFront cannot be deployed via SAM* — можна, і збою це не спричинило б.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-edge-how-it-works.html>

</details>

---

<a id="t05-q65"></a>
### T05-Q65
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is using AWS SAM to create a template for deploying a serverless application. The Developer plans deploy an AWS Lambda function and an Amazon DynamoDB table using the template.

Which resource types should the Developer specify? (Select TWO.)

- ✅ AWS::Serverless::SimpleTable
- ▫️ AWS::Serverless:API
- ▫️ AWS::Serverless::Application
- ✅ AWS::Serverless:Function
- ▫️ AWS::Serverless:LayerVersion

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS::Serverless::SimpleTable» + «AWS::Serverless::Function»**

Serverless-застосунок — це не лише Lambda-функції, а й джерела подій, API, БД та інші ресурси. SAM-шаблони розширюють CloudFormation спрощеними типами ресурсів:
- **`AWS::Serverless::Function`** — створює функцію Lambda, її execution role і event source mappings.
- **`AWS::Serverless::SimpleTable`** — створює таблицю DynamoDB з первинним ключем з одного атрибута; підходить, коли доступ лише за ключем.

**❌ Неправильні варіанти:**
- *AWS::Serverless::Application* — вбудовує вкладений застосунок із Serverless Application Repository або S3.
- *AWS::Serverless::LayerVersion* — створює Lambda Layer із бібліотеками чи runtime.
- *AWS::Serverless::Api* — створює ресурси й методи API Gateway.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html>

</details>

---

<a id="t06-q21"></a>
### T06-Q21
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A developer received the following error message during an AWS CloudFormation deployment:

DELETE_FAILED (The following resource(s) failed to delete: (sg-11223344).)

Which action should the developer take to resolve this error?

- ▫️ Manually delete the security group. Then execute a change set to force deletion of the CloudFormation stack.
- ▫️ Add a DependsOn attribute to the sg-11223344 resource in the CloudFormation template. Then delete the stack.
- ▫️ Update the logical ID of the security group resource with the security groups ARN. Then delete the stack.
- ✅ Modify the CloudFormation template to retain the security group resource. Then manually delete the resource after deployment.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Modify the CloudFormation template to retain the security group resource. Then manually delete the resource after deployment.»**

Стек застряг у `DELETE_FAILED`, бо залежний ресурс — security group — не вдається видалити. Наприклад, до нього прикріплено ENI, що не належить стеку.

Щоб видалити стек, повторюють видалення і вказують ресурси, які слід зберегти (retain). Це можна зробити в консолі або в CLI: `aws cloudformation delete-stack --retain-resources`. Після цього ресурс видаляють вручну.

**❌ Неправильні варіанти:**
- *DependsOn* — задає порядок створення ресурсів, а проблему видалення не вирішує.
- *Delete SG manually, then change set* — SG справді можна видалити вручну, але change set для видалення стеку не використовують; стек просто видаляють.
- *Update logical ID with ARN* — проблема не в logical ID чи ARN.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html>

</details>

---

<a id="t06-q50"></a>
### T06-Q50
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

In an organization, AWS CloudFormation templates are used to deploy all Amazon RDS DB instances as part of AWS CodePipeline CI/CD automation. As part of the deployment process, the main password for the DB instance needs to be auto generated.

What approach can be taken to fulfill these prerequisites with the minimum possible development effort?

- ▫️ Use AWS KMS to generate a random encryption key, then use this key as the password for the DB instance.
- ✅ Use AWS Secrets Manager via the AWS SDK to generate a secure string. Use a dynamic reference to create the DB instance with the secret value.
- ▫️ Write custom AWS Lambda function code that, when triggered, creates an encrypted string, and uses it as the password for the DB instance.
- ▫️ Utilize AWS::EC2::KeyPair to generate a secure string and assign it as the password for the DB instance.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use AWS Secrets Manager via the AWS SDK to generate a secure string. Use a dynamic reference to create the DB instance with the secret value.»**

Secrets Manager призначений для безпечного зберігання й отримання секретів. Не потрібна ні власна Lambda, ні дія в CodeBuild, тож менше складності й ризиків. Dynamic reference `{{resolve:secretsmanager:...}}` у шаблоні CloudFormation підставляє значення секрету напряму, і воно не потрапляє в шаблон.

ℹ️ Згенерувати пароль можна й у самому шаблоні: `AWS::SecretsManager::Secret` з `GenerateSecretString`. Ще один варіант — `ManageMasterUserPassword: true` для RDS.

**❌ Неправильні варіанти:**
- *KMS random key as password* — KMS керує криптографічними ключами, а не генерує паролі.
- *AWS::EC2::KeyPair* — це SSH-ключ для входу на EC2, а не пароль для RDS.
- *Custom Lambda* — зайва складність і розробка.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html>

</details>

---

<a id="t06-q51"></a>
### T06-Q51
*домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **Сумнівний ключ: реальний відкат — git revert + cdk deploy / rollback CloudFormation.** → [детальніше](../../07-outdated-and-wrong-answers.md#d06)

A junior developer has been assigned the project of making updates to a current application using AWS Cloud Development Kit (CDK).

How can the developer revert to a previous version if there is an error?

- ✅ Only make changes in the deployment phase, and never during synthesis.
- ▫️ Once CloudFormation has been installed, it can be used to track changes in application stacks.
- ▫️ Once CloudFormation has been installed, it can be used to revert to previous versions.
- ▫️ Once CloudFormation has been added to the application, it can be used to manage the scope.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Only make changes in the deployment phase, and never during synthesis.»**

Synthesis — це перетворення стеків CDK на шаблони CloudFormation та assets. Зміни слід вносити лише на етапі деплою, коли шаблон уже створено. Тоді CloudFormation зможе відкотити зміну, якщо щось піде не так.

⚠️ Формулювання питання й ключа нечітке. Див. 07 (d06).

**❌ Неправильні варіанти:**
- *Install CloudFormation to revert* — CloudFormation не встановлюють; CDK Toolkit і так перетворює стеки на шаблони.
- *Add CloudFormation to manage scope* — CloudFormation не «додають» до застосунку і не використовують для керування scope.
- *After synthesis, track drift* — drift виявляє CloudFormation, коли ресурси змінюють поза ним; synthesis — це лише генерація шаблону.

Джерела: <https://docs.aws.amazon.com/cdk/v2/guide/best-practices.html>

</details>

---

<a id="t06-q52"></a>
### T06-Q52
*домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is working on a serverless application using AWS Cloud Development Kit (AWS CDK) and intends to set up several AWS Lambda functions and Amazon API Gateway APIs during the creation of an AWS CloudFormation stack. The developer's local machine is equipped with AWS Serverless Application Model (AWS SAM) and the AWS CDK.

What is the best way for the developer to locally test a particular Lambda function?

- ▫️ Directly execute the Lambda function on the local machine.
- ✅ Use the sam local invoke command specifying the specific Lambda function.
- ▫️ Test the Lambda function through the AWS Management Console.
- ▫️ Use the AWS CDK command line to locally run the Lambda function.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the sam local invoke command specifying the specific Lambda function.»**

AWS SAM CLI дозволяє локально тестувати й налагоджувати Lambda-функції, описані в SAM-шаблоні. Команда `sam local invoke <FunctionLogicalId>` виконує вказану функцію локально в Docker-контейнері.

**❌ Неправильні варіанти:**
- *Test in the Console* — консоль тестує функцію в хмарі, а не локально.
- *AWS CDK CLI* — CDK визначає інфраструктуру в коді й не запускає функції локально. ℹ️ Локально запустити CDK-застосунок можна через `sam local invoke` разом із `cdk synth`.
- *Directly execute locally* — без інструмента на зразок SAM CLI середовище Lambda локально не відтворити.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-using-invoke.html>

</details>

---

<a id="t06-q63"></a>
### T06-Q63
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

An engineer is tasked with creating an AWS CloudFormation template that is capable of automatically determining the AWS Region where it is being deployed.

What is the MOST efficient method to ascertain the Region during deployment of the template?

- ▫️ Extract the Region from the AWS::StackId pseudo parameter by applying the Fn::Split intrinsic function.
- ▫️ Dynamically fetch the Region by referencing the corresponding entry in AWS Systems Manager Parameter Store.
- ▫️ Request the Region as an input parameter during CloudFormation deployment.
- ✅ Utilize the AWS::Region pseudo parameter.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Utilize the AWS::Region pseudo parameter.»**

Pseudo parameter `AWS::Region` автоматично повертає регіон, де створюється чи оновлюється стек. Це найефективніший спосіб: не потрібні ні введення, ні обробка, ні звернення до зовнішніх сервісів.

**❌ Неправильні варіанти:**
- *Region as an input parameter* — потрібне ручне введення при кожному деплої, а з ним ризик помилок, особливо в мультирегіональних деплоях.
- *Split AWS::StackId* — технічно працює, бо stack ID містить регіон, але потребує зайвої обробки.
- *SSM Parameter Store* — зовнішня залежність і припущення, що регіон там правильно збережено.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html>

</details>

---

<a id="t07-q41"></a>
### T07-Q41
*домен: офіційна мітка*

AWS CloudFormation helps model and provision all the cloud infrastructure resources needed for your business.

Which of the following services rely on CloudFormation to provision resources (Select two)?

- ▫️ AWS Lambda
- ✅ AWS Serverless Application Model (AWS SAM)
- ▫️ AWS CodeBuild
- ▫️ AWS Autoscaling
- ✅ AWS Elastic Beanstalk

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Serverless Application Model (AWS SAM)» + «AWS Elastic Beanstalk»**

- **Elastic Beanstalk** деплоїть і масштабує вебзастосунки на Java, .NET, PHP, Node.js, Python, Ruby, Go і Docker. Ресурси середовища він запускає і змінює через CloudFormation.
- **AWS SAM**-шаблони — це розширення CloudFormation, і SAM працює на його основі.

**❌ Неправильні варіанти:**
- *AWS Lambda* — запускає код без серверів і CloudFormation для роботи не потребує.
- *AWS Auto Scaling* — може використовуватися з CloudFormation, але не залежить від нього.
- *AWS CodeBuild* — CI-сервіс, CloudFormation не потребує. CodePipeline може використовувати CloudFormation як дію деплою, але це необов'язково.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification.html> · <https://aws.amazon.com/elasticbeanstalk/>

</details>

---

<a id="t07-q50"></a>
### T07-Q50
*домен: офіційна мітка*

A developer is designing an AWS CloudFormation template for deploying Amazon EC2 instances in numerous AWS accounts. The developer needs to select EC2 instances from a list of pre-approved instance types.

What measures could the developer take to integrate the list of authorized instance types into the CloudFormation template?

- ▫️ Configure separate parameters for each EC2 instance type in the CloudFormation template
- ▫️ Configure a pseudo parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template
- ▫️ Configure a mapping having a list of EC2 instance types as parameters in the CloudFormation template
- ✅ Configure a parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Configure a parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template»**

Секція Parameters дає змогу вводити власні значення під час кожного створення чи оновлення стека. `AllowedValues` — масив дозволених значень:
- для параметра типу String значення має бути одним із них;
- для CommaDelimitedList кожен елемент списку має бути з цього масиву.

**❌ Неправильні варіанти:**
- *Separate parameter per instance type* — семантично неправильно: одне значення з кількома входами.
- *Mapping with a list* — Mappings — це пари ключ–значення (наприклад, регіон → AMI), з яких читають через `Fn::FindInMap`. У Mappings не можна використовувати параметри, pseudo parameters чи intrinsic-функції.
- *Pseudo parameter with AllowedValues* — pseudo parameters заздалегідь визначені CloudFormation, у шаблоні їх не оголошують.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html>

</details>

---

<a id="t08-q12"></a>
### T08-Q12
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer has created the code for a Lambda function saved the code in a file named lambda_function.py. He has also created a template that named template.yaml. The following code is included in the template file:

AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Resources:
microservicehttpendpointpython3:
Type: 'AWS::Serverless::Function'
Properties:
Handler: lambda_function.lambda_handler
CodeUri: .
What commands can the Developer use to prepare and then deploy this template? (Select TWO.)

- ▫️ Run aws serverless package and then aws serverless deploy
- ▫️ Run aws cloudformation compile and then aws cloudformation deploy
- ▫️ Run sam build and then sam package
- ✅ Run sam package and then sam deploy
- ✅ Run aws cloudformation package and then aws cloudformation deploy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Run sam package and then sam deploy» + «Run aws cloudformation package and then aws cloudformation deploy»**

Це SAM-шаблон: на це вказує `Transform: 'AWS::Serverless-2016-10-31'`. Спочатку його пакують, потім деплоять. Код має лежати поруч або бути вказаним через `CodeUri`.

Команда `package` (`aws cloudformation package` або `sam package`):
- завантажує локальні артефакти (код Lambda, Swagger для API Gateway) в S3;
- повертає шаблон, у якому локальні шляхи замінено на S3-адреси.

Потім виконують `deploy` (`aws cloudformation deploy` або `sam deploy`).

ℹ️ Сучасний `sam deploy` пакує автоматично, тож окремий `sam package` уже не обов'язковий.

**❌ Неправильні варіанти:**
- *aws cloudformation compile* — такої команди немає; потрібен `package`.
- *sam build + sam package* — бракує кроку `deploy`.
- *aws serverless package/deploy* — у CLI немає команди `serverless`.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html>

</details>

---

<a id="t08-q30"></a>
### T08-Q30
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is using AWS SAM to create a template for deploying a serverless application. The Developer plans deploy a Lambda function using the template.

Which resource type should the Developer specify?

- ▫️ AWS::Serverless:API
- ✅ AWS::Serverless:Function
- ▫️ AWS::Serverless:LayerVersion
- ▫️ AWS::Serverless::Application

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS::Serverless::Function»**

Serverless-застосунок — це не лише функції Lambda, а й джерела подій, API, БД, event source mappings. SAM-шаблони розширюють CloudFormation. Ресурс `AWS::Serverless::Function` створює функцію Lambda, її execution role і event source mappings, що її запускають.

**❌ Неправильні варіанти:**
- *AWS::Serverless::Application* — вкладений застосунок із Serverless Application Repository або S3.
- *AWS::Serverless::LayerVersion* — Lambda Layer з бібліотеками чи runtime.
- *AWS::Serverless::Api* — ресурси й методи API Gateway.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html>

</details>

---

<a id="t08-q51"></a>
### T08-Q51
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer must deploy a new AWS Lambda function using an AWS CloudFormation template.

Which procedures will deploy a Lambda function? (Select TWO.)

- ▫️ Upload a ZIP file to AWS CloudFormation containing the function code, then add a reference to it in an AWS::Lambda::Function resource in the template
- ▫️ 1. Upload the function code to a private Git repository, then add a reference to it in an AWS::Lambda::Function resource in the template
- ▫️ Upload the code to an AWS CodeCommit repository, then add a reference to it in an AWS::Lambda::Function resource in the template
- ✅ Upload a ZIP file containing the function code to Amazon S3, then add a reference to it in an AWS::Lambda::Function resource in the template
- ✅ Create an AWS::Lambda::Function resource in the template, then write the code directly inside the CloudFormation template

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Upload a ZIP file containing the function code to Amazon S3, then add a reference to it in an AWS::Lambda::Function resource in the template» + «Create an AWS::Lambda::Function resource in the template, then write the code directly inside the CloudFormation template»**

Робочих способів два:
- **Inline-код.** Для простих функцій на Node.js чи Python код пишуть прямо в шаблоні (`Code: ZipFile: |`). Розмір обмежений, приблизно 4 KB.
- **ZIP в S3.** ZIP-файл завантажують у S3 і посилаються на нього в `Code` ресурсу: `S3Bucket` і `S3Key`.

**❌ Неправильні варіанти:**
- *CodeCommit reference* — CloudFormation не може посилатися на код у CodeCommit.
- *Upload ZIP to CloudFormation* — ZIP у CloudFormation не завантажують.
- *Private Git repository* — CloudFormation не може посилатися на приватний Git-репозиторій.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-lambda-function-code.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html>

</details>

---

<a id="t09-q11"></a>
### T09-Q11
*домен: офіційна мітка*

Other than the Resources section, which of the following sections in a Serverless Application Model (SAM) Template is mandatory?

- ▫️ Parameters
- ✅ Transform
- ▫️ Globals
- ▫️ Mappings

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Transform»**

AWS SAM — open-source фреймворк для serverless-застосунків. Такий застосунок — це Lambda-функції, джерела подій, API, БД та event source mappings. У SAM-шаблоні обов'язкові лише секції **Transform** і **Resources**.

Детальніше: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html

**❌ Неправильні варіанти:**
- *Parameters* — опціональна секція.
- *Mappings* — опціональна секція.
- *Globals* — опціональна секція.

</details>

---

<a id="t10-q15"></a>
### T10-Q15
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is looking for a way to use shorthand syntax to express functions, APIs, databases, and event source mappings. The Developer will test using AWS SAM to create a simple Lambda function using Nodejs.12x.

What is the SIMPLEST way for the Developer to get started with a Hello World Lambda function?

- ▫️ Use AWS CloudFormation to deploy a Hello World stack using AWS SAM
- ▫️ Install the AWS CLI, run aws sam init and use one of the AWS Quick Start Templates
- ✅ Install the AWS SAM CLI, run sam init and use one of the AWS Quick Start Templates
- ▫️ Use the AWS Management Console to access AWS SAM and deploy a Hello World function

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Install the AWS SAM CLI, run sam init and use one of the AWS Quick Start Templates»**

`sam init` створює новий serverless-застосунок із SAM-шаблону: структуру папок для функцій, підключених до джерела подій (API, S3, DynamoDB). У ньому є все для старту і подальшого розвитку до продакшну. Спершу треба встановити **SAM CLI**, окремо від AWS CLI.

**❌ Неправильні варіанти:**
- *aws sam init* — `sam` — окремий CLI, а не команда AWS CLI.
- *SAM via the Console* — у консолі немає SAM, але через Serverless Application Repository можна задеплоїти готові SAM-застосунки.
- *CloudFormation Hello World with SAM* — SAM працює на основі CloudFormation, але правильний старт — SAM CLI.

</details>

---

<a id="t10-q32"></a>
### T10-Q32
*домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to setup a new serverless application that includes AWS Lambda and Amazon API Gateway as part of a single stack. The Developer needs to be able to locally build and test the serverless applications before deployment on AWS.

Which service should the Developer use?

- ▫️ AWS Elastic Beanstalk
- ✅ AWS Serverless Application Model (SAM)
- ▫️ AWS CodeBuild
- ▫️ AWS CloudFormation

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Serverless Application Model (SAM)»**

AWS SAM — open-source фреймворк для serverless-застосунків. Простим синтаксисом він описує функції, API, дозволи, конфігурації та події. Наприклад, шаблон може створювати Lambda і API Gateway з ресурсом `/greeting` і методом GET. **SAM CLI** дає збирати, тестувати й налагоджувати застосунок локально в середовищі, близькому до Lambda.

**❌ Неправильні варіанти:**
- *CloudFormation* — не дає локально збирати й тестувати.
- *Elastic Beanstalk* — не для serverless-застосунків і без локального тестування.
- *CodeBuild* — хмарний CI, а не локальне тестування.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html>

</details>

---

<a id="t10-q49"></a>
### T10-Q49
*домен: визначено автоматично (у джерелі: AWS Management & Governance)*

How can a Developer view a summary of proposed changes to an AWS CloudFormation stack without implementing the changes in production?

- ▫️ Use a direct update
- ▫️ Create a StackSet
- ✅ Create a Change Set
- ▫️ Use drift detection

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a Change Set»**

Change sets показують, як запропоновані зміни вплинуть на запущені ресурси стеку. CloudFormation застосує зміни лише коли ви виконаєте change set; можна передумати або створити інший. Працює з консоллю, CLI і API.

**❌ Неправильні варіанти:**
- *StackSet* — розгортає стеки в кількох акаунтах і регіонах однією операцією.
- *Drift detection* — показує, де реальна конфігурація відхилилася від шаблону.
- *Direct update* — одразу змінює продакшн-ресурси без попереднього перегляду.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html>

</details>

---

<a id="t11-q18"></a>
### T11-Q18
*домен: офіційна мітка*

You are a developer in a manufacturing company that has several servers on-site. The company decides to move new development to the cloud using serverless technology. You decide to use the AWS Serverless Application Model (AWS SAM) and work with an AWS SAM template file to represent your serverless architecture.

Which of the following is NOT a valid serverless resource type?

- ▫️ AWS::Serverless::SimpleTable
- ▫️ AWS::Serverless::Api
- ▫️ AWS::Serverless::Function
- ✅ AWS::Serverless::UserPool

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS::Serverless::UserPool»**

Питання на пошук типу ресурсу, якого в SAM **немає**. SAM дає скорочений синтаксис для функцій, API, БД і event source mappings. Типи ресурсів SAM:
- `AWS::Serverless::Api`;
- `AWS::Serverless::Application`;
- `AWS::Serverless::Function`;
- `AWS::Serverless::HttpApi`;
- `AWS::Serverless::LayerVersion`;
- `AWS::Serverless::SimpleTable`;
- `AWS::Serverless::StateMachine`;
- а також новіші `Connector` і `GraphQLApi`.

User pool належить Cognito (`AWS::Cognito::UserPool`), а `AWS::Serverless::UserPool` не існує.

**❌ Інші варіанти — справжні ресурси SAM:**
- *AWS::Serverless::Function* — функція Lambda, execution role і event source mappings.
- *AWS::Serverless::Api* — API Gateway з повним контролем налаштувань.
- *AWS::Serverless::SimpleTable* — таблиця DynamoDB з простим первинним ключем.

</details>

---

<a id="t11-q19"></a>
### T11-Q19
*домен: офіційна мітка*

A Developer at a company is working on a CloudFormation template to set up resources. Resources will be defined using code and provisioned based on certain conditions defined in the Conditions section.

Which section of a CloudFormation template cannot be associated with Condition?

- ▫️ Resources
- ✅ Parameters
- ▫️ Conditions
- ▫️ Outputs

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Parameters»**

Питання на пошук секції, де **не можна** використовувати Conditions. Секція `Conditions` визначає, за яких умов ресурси чи outputs створюються. Наприклад, параметр `EnvironmentType` (prod чи test) дає змогу створювати для prod потужніші інстанси, а для test — дешевші. Умови прив'язують лише до ресурсів, їх властивостей і outputs, тобто в секціях `Resources` і `Outputs`. У `Parameters` умов немає.

Детальніше:
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/conditions-section-structure.html

**❌ Інші варіанти — там умови працюють:**
- *Resources* — умови прив'язують до ресурсів.
- *Conditions* — тут умови визначають.
- *Outputs* — умови прив'язують до outputs; самі outputs можна імпортувати в інші стеки, повертати у відповіді чи бачити в консолі.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/conditions-section-structure.html>

</details>

---

<a id="t11-q21"></a>
### T11-Q21
*домен: офіційна мітка*

Your global organization has an IT infrastructure that is deployed using CloudFormation on AWS Cloud. One employee, in us-east-1 Region, has created a stack 'Application1' and made an exported output with the name 'ELBDNSName'. Another employee has created a stack for a different application 'Application2' in us-east-2 Region and also exported an output with the name 'ELBDNSName'. The first employee wanted to deploy the CloudFormation stack 'Application1' in us-east-2, but it got an error. What is the cause of the error?

- ▫️ Output Values in CloudFormation must have unique names within a single Region
- ▫️ Exported Output Values in CloudFormation must have unique names across all Regions
- ▫️ Output Values in CloudFormation must have unique names across all Regions
- ✅ Exported Output Values in CloudFormation must have unique names within a single Region

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Exported Output Values in CloudFormation must have unique names within a single Region»**

Секція `Outputs` оголошує значення, які можна імпортувати в інші стеки (cross-stack references), повертати у відповіді або бачити в консолі. **Export** робить output доступним для інших стеків, і імена експортів мають бути унікальними в межах регіону для акаунта. Тут конфлікт в us-east-2.

**❌ Неправильні варіанти:**
- *Outputs unique across all Regions* — неправильно.
- *Exports unique across all Regions* — унікальність потрібна лише в межах регіону.
- *Outputs unique within a Region* — вимога стосується саме **exports**.

</details>

---

<a id="t11-q22"></a>
### T11-Q22
*домен: офіційна мітка*

A global e-commerce company wants to perform geographic load testing of its order processing API. The company must deploy resources to multiple AWS Regions to support the load testing of the API.

How can the company address these requirements without additional application code?

- ▫️ Set up an AWS Cloud Development Kit (CDK) ToolKit that defines the load test resources. Leverage the CDK CLI to create a stack from the template in each Region
- ▫️ Set up an AWS Organizations template that defines the load test resources across the organization. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions
- ✅ Set up an AWS CloudFormation template that defines the load test resources. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions
- ▫️ Set up an AWS CloudFormation template that defines the load test resources. Develop region-specific Lambda functions to create a stack from the AWS CloudFormation template in each Region when the respective function is invoked

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set up an AWS CloudFormation template that defines the load test resources. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions»**

**StackSets** створюють, оновлюють і видаляють стеки в кількох акаунтах і регіонах однією операцією. З адміністративного акаунта керують одним шаблоном, який розгортається в цільові акаунти й регіони. Під час створення stack set вказують шаблон, параметри й capabilities, а потім можна керувати стеками в потрібних акаунтах і регіонах.

**❌ Неправильні варіанти:**
- *Region-specific Lambda functions* — шаблони й функції в кожному регіоні — зайва складність.
- *CDK Toolkit per Region* — CDK розгортає через CloudFormation, але тут потрібен саме мультирегіональний механізм StackSets.
- *AWS Organizations template* — Organizations керує акаунтами і не створює шаблонів інфраструктури.

Джерела: <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/what-is-cfnstacksets.html>

</details>

---

<a id="t11-q36"></a>
### T11-Q36
*домен: офіційна мітка*

A development team wants to build an application using serverless architecture. The team plans to use AWS Lambda functions extensively to achieve this goal. The developers of the team work on different programming languages like Python, .NET and Javascript. The team wants to model the cloud infrastructure using any of these programming languages.

Which AWS service/tool should the team use for the given use-case?

- ▫️ AWS Serverless Application Model (SAM)
- ▫️ AWS CodeDeploy
- ✅ AWS Cloud Development Kit (CDK)
- ▫️ AWS CloudFormation

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS Cloud Development Kit (CDK)»**

AWS CDK — open-source фреймворк, у якому хмарну інфраструктуру описують звичними мовами програмування: TypeScript/JavaScript, Python, Java, .NET. Без нього розгортання часто вимагає ручних дій, власних скриптів, підтримки шаблонів або вивчення спеціальних мов. CDK дає високорівневі компоненти — **constructs** — із перевіреними налаштуваннями за замовчуванням, тож не треба бути експертом. Ресурси розгортаються безпечно й повторювано через CloudFormation. Власні constructs з вимогами організації можна поширювати між проєктами.

**❌ Неправильні варіанти:**
- *CloudFormation* — CDK компілюється в шаблони CloudFormation і отримує всі його переваги: безпечний деплой, автоматичний відкат, drift detection. Але сам CloudFormation — це JSON/YAML, а не мова програмування.
- *SAM* — декларативні шаблони для serverless. Якщо хочете стислий YAML — SAM, якщо мову програмування — CDK. ⚠️ В оригіналі SAM помилково описано як Serverless Application Repository.
- *CodeDeploy* — сервіс деплою на EC2, Fargate, Lambda, on-premises; може працювати разом із CDK, але інфраструктуру не описує.

</details>

---

<a id="t11-q49"></a>
### T11-Q49
*домен: офіційна мітка*

The Technical Lead of your team has reviewed a CloudFormation YAML template written by a new recruit and specified that an invalid section has been added to the template.

Which of the following represents an invalid section of the CloudFormation template?

- ▫️ 'Parameters' section of the template
- ✅ 'Dependencies' section of the template
- ▫️ 'Conditions' section of the template
- ▫️ 'Resources' section of the template

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «'Dependencies' section of the template»**

Питання на пошук **недопустимої** секції. Обов'язкова в шаблоні лише `Resources`. Секції `Dependencies` не існує: залежності задають атрибутом `DependsOn` у ресурсі.

Анатомія шаблону: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

**❌ Інші варіанти — валідні секції:**
- *Conditions* — опціональна; визначає, чи створювати ресурси або властивості, наприклад для prod чи test.
- *Resources* — єдина обов'язкова; описує ресурси стеку та їх властивості.
- *Parameters* — опціональна; значення, які передають під час створення чи оновлення стеку.

</details>

---

<a id="t11-q58"></a>
### T11-Q58
*домен: офіційна мітка*

Your company has configured AWS Organizations to manage multiple AWS accounts. Within each AWS account, there are many CloudFormation scripts running. Your manager has requested that each script output the account number of the account the script was executed in.

Which Pseudo parameter will you use to get this information?

- ▫️ AWS::StackName
- ▫️ AWS::NoValue
- ✅ AWS::AccountId
- ▫️ AWS::Region

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «AWS::AccountId»**

CloudFormation створює й налаштовує ресурси, описані в шаблоні. Pseudo parameters — це параметри, наперед визначені CloudFormation. У шаблоні їх не оголошують, а використовують як звичайні параметри через `Ref`. `AWS::AccountId` повертає ID акаунта, в якому створюється стек.

**❌ Неправильні варіанти:**
- *AWS::NoValue* — якщо повернути його з `Fn::If`, відповідна властивість ресурсу прибирається.
- *AWS::Region* — регіон стеку, наприклад `us-west-2`.
- *AWS::StackName* — ім'я стеку, наприклад «teststack».

</details>

---

<a id="t11-q61"></a>
### T11-Q61
*домен: офіційна мітка*

As a developer, you are working on creating an application using AWS Cloud Development Kit (CDK).

Which of the following represents the correct order of steps to be followed for creating an app using AWS CDK?

- ✅ Create the app from a template provided by AWS CDK -&gt; Add code to the app to create resources within stacks -&gt; Build the app (optional) -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account
- ▫️ Create the app from a template provided by AWS CDK -&gt; Add code to the app to create resources within stacks -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account -&gt; Build the app
- ▫️ Create the app from a template provided by AWS CloudFormation -&gt; Add code to the app to create resources within stacks -&gt; Build the app (optional) -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account
- ▫️ Create the app from a template provided by AWS CloudFormation -&gt; Add code to the app to create resources within stacks -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account -&gt; Build the app

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create the app from a template provided by AWS CDK -> Add code to the app to create resources within stacks -> Build the app (optional) -> Synthesize one or more stacks in the app -> Deploy stack(s) to your AWS account»**

Стандартний робочий процес AWS CDK:
1. **Створити застосунок із шаблону CDK.** Кожен застосунок лежить у власній директорії з власними залежностями. Команда `cdk init app --language <мова>` створює структуру файлів і папок.
2. **Додати код ресурсів у стеки** — те, що потрібно застосунку.
3. **Зібрати (необов'язково).** CDK Toolkit збирає сам, але вручну можна ловити синтаксичні помилки й помилки типів.
4. **Синтезувати стеки** (`cdk synth`) у шаблон CloudFormation. Цей крок виявляє логічні помилки в описі ресурсів. Якщо стеків кілька, вказують, які синтезувати.
5. **Задеплоїти** (`cdk deploy`). CDK і так синтезує перед кожним деплоєм. Зміни, що стосуються безпеки, треба підтвердити; прогрес показується в консолі.

**❌ Неправильні варіанти:**
- *Template provided by CloudFormation* — CloudFormation не створює CDK-застосунки.
- *CloudFormation template … → Build after deploy* — неправильне джерело шаблону і неправильний порядок.
- *Build after deploy* — збирати після деплою не можна.

</details>

---

<a id="t12-q28"></a>
### T12-Q28
*домен: офіційна мітка*

A development team has created AWS CloudFormation templates that are reusable by taking advantage of input parameters to name resources based on client names.

You would like to save your templates on the cloud, which storage option should you choose?

- ✅ S3
- ▫️ EFS
- ▫️ ECR
- ▫️ EBS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «S3»**

Коли ви завантажуєте локальний шаблон, CloudFormation кладе його в бакет S3 у вашому акаунті. Якщо такого бакета ще немає, створюється окремий бакет для кожного регіону; якщо є — шаблон додається туди.

Детальніше: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-template.html

**❌ Неправильні варіанти:**
- *EBS* — блокове сховище для EC2.
- *EFS* — файлова система, яку монтують на EC2.
- *ECR* — реєстр контейнерних образів.

</details>

---

<a id="t12-q60"></a>
### T12-Q60
*домен: офіційна мітка*

The development team at an IT company uses CloudFormation to manage its AWS infrastructure. The team has created a network stack containing a VPC with subnets and a web application stack with EC2 instances and an RDS instance. The team wants to reference the VPC created in the network stack into its web application stack.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Ref intrinsic function to reference the value of VPC into the web application stack
- ▫️ Create a cross-stack reference and use the Outputs output field to flag the value of VPC from the network stack. Then use Ref intrinsic function to reference the value of VPC into the web application stack
- ▫️ Create a cross-stack reference and use the Outputs output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack
- ✅ Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack»**

**Cross-stack reference** експортує значення з одного стеку в інший. Наприклад, мережевий стек має VPC і підмережі, а стек вебзастосунку використовує їх, не створюючи й не підтримуючи власних мережевих ресурсів. Як це зробити:
1. У стеку-джерелі позначити output полем **`Export`**.
2. У стеку-споживачі імпортувати значення через **`Fn::ImportValue`**.

`Ref` для імпорту з іншого стеку не працює.

Як працює CloudFormation: https://aws.amazon.com/cloudformation/

**❌ Неправильні варіанти:**
- *Outputs field + ImportValue* — без `Export` значення не можна імпортувати.
- *Outputs field + Ref* — потрібні `Export` і `Fn::ImportValue`.
- *Export + Ref* — `Ref` не імпортує значення з іншого стеку.

Джерела: <https://aws.amazon.com/cloudformation/> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-crossstackref.html>

</details>

---
