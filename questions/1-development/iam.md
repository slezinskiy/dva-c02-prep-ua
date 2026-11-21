# IAM / STS / Organizations

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 6  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q39"></a>
### T01-Q39
*домен: офіційна мітка*

An EC2 instance has an IAM instance role attached to it, providing it read and write access to the S3 bucket 'my_bucket'. You have tested the IAM instance role and both reads and writes are working. You then remove the IAM role from the EC2 instance and test both read and write again. Writes stopped working but reads are still working.

What is the likely cause of this behavior?

- ✅ The S3 bucket policy authorizes reads
- ▫️ Removing an instance role from an EC2 instance can take a few minutes before being active
- ▫️ The EC2 instance is using cached temporary IAM credentials
- ▫️ When a read is done on a bucket, there's a grace period of 5 minutes to do the same read again

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The S3 bucket policy authorizes reads»**

Коли EC2-інстанс звертається до S3, враховуються і IAM-політика інстансу, і bucket policy бакета. Роль з інстансу прибрали, тож лишилася тільки bucket policy, яка дозволяє читання (але не запис).

**❌ Неправильні варіанти:**
- *The EC2 instance is using cached temporary IAM credentials* — роль видалено, тому це не так.
- *Removing an instance role can take a few minutes* — зміна діє одразу; і навіть якби ні, це не пояснювало б, чому читання працює, а запис — ні.
- *Grace period of 5 minutes to do the same read again* — неправда: кожен запит окремо перевіряється IAM.

</details>

---

<a id="t07-q39"></a>
### T07-Q39
*домен: офіційна мітка*

A development team has created a new IAM user that has s3:putObject permission to write to an S3 bucket. This S3 bucket uses server-side encryption with AWS KMS managed keys (SSE-KMS) as the default encryption. Using the access key ID and the secret access key of the IAM user, the application received an access denied error when calling the PutObject API.

As a Developer Associate, how would you resolve this issue?

- ▫️ Correct the policy of the IAM user to allow the s3:Encrypt action
- ▫️ Correct the ACL of the S3 bucket to allow the IAM user to upload encrypted objects
- ✅ Correct the policy of the IAM user to allow the kms:GenerateDataKey action
- ▫️ Correct the bucket policy of the S3 bucket to allow the IAM user to upload encrypted objects

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Correct the policy of the IAM user to allow the kms:GenerateDataKey action»**

S3 має три режими серверного шифрування: SSE-S3, SSE-C і SSE-KMS. У SSE-KMS data key керує AWS, а KMS-ключем (customer managed або AWS managed `aws/s3`) — ви. Як це працює:
1. S3 запитує в KMS plaintext data key і його копію, зашифровану KMS-ключем.
2. KMS генерує data key і повертає обидві копії.
3. S3 шифрує дані plaintext-ключем і якомога швидше видаляє його з пам'яті.
4. S3 зберігає зашифрований data key у метаданих поруч із даними.

Помилка означає, що користувачу чи ролі бракує `kms:GenerateDataKey` на ключі бакета. Цей дозвіл потрібен для бакетів з default encryption на власному KMS-ключі.

Що перевірити:
- у політиках з `"Effect": "Allow"` — чи є `kms:GenerateDataKey` на ключі бакета; якщо немає, додати;
- у політиках з `"Effect": "Deny"` — чи не забороняють вони `s3:PutObject` або `kms:GenerateDataKey`;
- чи не обмежують права VPC endpoint policy, SCP, permissions boundary або session policy.

**❌ Неправильні варіанти:**
- *s3:Encrypt* — такої дії не існує.
- *Fix bucket policy* — доступ до бакета користувач уже має; бракує прав на KMS.
- *Fix ACL* — ACL дає доступ до бакета чи об'єктів, але права на KMS-ключ однаково потрібні.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/s3-access-denied-error-kms/> · <https://docs.aws.amazon.com/kms/latest/developerguide/services-s3.html>

</details>

---

<a id="t09-q35"></a>
### T09-Q35
*домен: офіційна мітка*

The development team at a HealthCare company has deployed EC2 instances in AWS Account A. These instances need to access patient data with Personally Identifiable Information (PII) on multiple S3 buckets in another AWS Account B.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Create an IAM role (instance profile) in Account A and set Account B as a trusted entity. Attach this role to the EC2 instances in Account A and add an inline policy to this role to access S3 data from Account B
- ✅ Create an IAM role with S3 access in Account B and set Account A as a trusted entity. Create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B
- ▫️ Add a bucket policy to all the Amazon S3 buckets in Account B to allow access from EC2 instances in Account A
- ▫️ Copy the underlying AMI for the EC2 instances from Account A into Account B. Launch EC2 instances in Account B using this AMI and then access the PII data on Amazon S3 in Account B

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an IAM role with S3 access in Account B and set Account A as a trusted entity. Create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B»**

EC2 в акаунті A може прийняти роль з акаунта B, щоб отримати доступ до бакетів S3 у B:

1. **У B** — роль із довірою до акаунта A і політикою доступу до S3:
```json
{ "Version": "2012-10-17",
  "Statement": [{ "Effect": "Allow", "Action": "s3:*",
    "Resource": ["arn:aws:s3:::awsexamplebucket1", "arn:aws:s3:::awsexamplebucket1/*",
                 "arn:aws:s3:::awsexamplebucket2", "arn:aws:s3:::awsexamplebucket2/*"] }] }
```

2. **В A** — роль (instance profile) для EC2 з inline-політикою на прийняття ролі з B:
```json
{ "Version": "2012-10-17",
  "Statement": [{ "Effect": "Allow", "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::AccountB_ID:role/ROLENAME" }] }
```

**❌ Неправильні варіанти:**
- *Role in A trusting B* — довіра налаштована навпаки.
- *Copy AMI to B* — це не вирішує задачу.
- *Bucket policy only* — потрібна ще IAM-політика в акаунті A для доступу до об'єктів у B. Детальніше: https://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/s3-instance-access-bucket/>

</details>

---

<a id="t11-q45"></a>
### T11-Q45
*домен: офіційна мітка*

As part of his development work, an AWS Certified Developer Associate is creating policies and attaching them to IAM identities. After creating necessary Identity-based policies, he is now creating Resource-based policies.

Which is the only resource-based policy that the IAM service supports?

- ✅ Trust policy
- ▫️ AWS Organizations Service Control Policies (SCP)
- ▫️ Access control list (ACL)
- ▫️ Permissions boundary

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Trust policy»**

Політики прикріплюють до IAM-ідентичностей (користувачів, груп, ролей) або до ресурсів. Resource-based політика — JSON на ресурсі, наприклад бакеті S3. Вона дає вказаному principal право на дії з умовами.

**Trust policy** визначає, хто може прийняти роль: акаунти, користувачі, ролі, федеративні користувачі. Роль одночасно є ідентичністю і ресурсом, тож має і trust policy, і identity-based політику. Trust policy — єдиний тип resource-based політики в самому IAM.

**❌ Неправильні варіанти:**
- *SCP* — задають максимальні дозволи для акаунтів чи OU організації, включно з root; явний Deny переважає.
- *ACL* — визначають, які principals з інших акаунтів мають доступ до ресурсу (S3, WAF, VPC); у межах акаунта не працюють.
- *Permissions boundary* — обмежує максимум, який можуть дати identity-based політики; сам дозволів не дає.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_resource-based> · <https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html>

</details>

---

<a id="t11-q56"></a>
### T11-Q56
*домен: офіційна мітка*

As an AWS Certified Developer Associate, you have configured the AWS CLI on your workstation. Your default region is us-east-1 and your IAM user has permissions to operate commands on services such as EC2, S3 and RDS in any region. You would like to execute a command to stop an EC2 instance in the us-east-2 region.

What of the following is the MOST optimal solution to address this use-case?

- ▫️ You should create a new IAM user just for that other region
- ▫️ You need to override the default region by using aws configure
- ▫️ Use boto3 dependency injection
- ✅ Use the --region parameter

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the --region parameter»**

Якщо `--region` не вказано, команда CLI виконується в регіоні за замовчуванням із профілю. Параметр `--region` перевизначає його для однієї команди.

Загальні параметри CLI: https://docs.aws.amazon.com/cli/latest/topic/config-vars.html#general-options

**❌ Неправильні варіанти:**
- *aws configure to change default* — потім доведеться повертати старе значення.
- *New IAM user for another region* — IAM глобальний, і два профілі лише ускладнять керування.
- *boto3 dependency injection* — CLI не використовує boto3 таким чином.

</details>

---

<a id="t12-q02"></a>
### T12-Q02
*домен: офіційна мітка*

The development team at a retail organization wants to allow a Lambda function in its AWS Account A to access a DynamoDB table in another AWS Account B.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Create an IAM role in Account B with access to DynamoDB. Modify the trust policy of the execution role in Account A to allow the execution role of Lambda to assume the IAM role in Account B. Update the Lambda function code to add the AssumeRole API call
- ▫️ Add a resource policy to the DynamoDB table in AWS Account B to give access to the Lambda function in Account A
- ✅ Create an IAM role in Account B with access to DynamoDB. Modify the trust policy of the role in Account B to allow the execution role of Lambda to assume this role. Update the Lambda function code to add the AssumeRole API call
- ▫️ Create a clone of the Lambda function in AWS Account B so that it can access the DynamoDB table in the same account

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create an IAM role in Account B with access to DynamoDB. Modify the trust policy of the role in Account B to allow the execution role of Lambda to assume this role. Update the Lambda function code to add the AssumeRole API call»**

Lambda в акаунті A може прийняти роль з акаунта B, щоб працювати з DynamoDB там:
1. В акаунті A — execution role для Lambda з правом `sts:AssumeRole`.
2. В акаунті B — роль із доступом до DynamoDB.
3. У trust policy ролі в B — дозвіл execution role з A приймати цю роль.
4. У коді Lambda — виклик `AssumeRole`.

Детальніше: https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role/

**❌ Неправильні варіанти:**
- *Clone Lambda to Account B* — не вирішує задачу.
- *Resource policy on DynamoDB table* — в оригіналі сказано, що таблиці DynamoDB не підтримують resource-based policies. ⚠️ Це застаріло: з 2024 року підтримують. Але ключ тесту — AssumeRole.
- *Modify trust policy in Account A* — змінювати треба trust policy ролі в B, а не в A.

</details>

---
