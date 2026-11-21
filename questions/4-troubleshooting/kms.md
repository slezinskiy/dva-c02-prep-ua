# KMS / шифрування / сертифікати

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#kms) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q42"></a>
### T09-Q42
*домен: офіційна мітка*

```json
An Accounting firm extensively uses Amazon EBS volumes for persistent storage of application data of Amazon EC2 instances. The volumes are encrypted to protect the critical data of the clients. As part of managing the security credentials, the project manager has come across a policy snippet that looks like the following:
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Allow for use of this Key",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:role/UserRole"
            },
            "Action": [
                "kms:GenerateDataKeyWithoutPlaintext",
                "kms:Decrypt"
            ],
            "Resource": "*"
        },
        {
            "Sid": "Allow for EC2 Use",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:role/UserRole"
            },
            "Action": [
                "kms:CreateGrant",
                "kms:ListGrants",
                "kms:RevokeGrant"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                "kms:ViaService": "ec2.us-west-2.amazonaws.com"
            }
        }
    ]
}
Which of the following options are correct regarding the policy?
```

- ▫️ The second statement in the policy mentions that all the resources stated in the first statement can take the specified role which will provide the ability to create, list, and revoke grants for Amazon EC2
- ✅ The first statement provides a specified IAM principal the ability to generate a data key and decrypt that data key from the CMK when necessary
- ▫️ The first statement provides the security group the ability to generate a data key and decrypt that data key from the CMK when necessary
- ▫️ The second statement in this policy provides the security group (mentioned in first statement of the policy), the ability to create, list, and revoke grants for Amazon EC2

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «The first statement provides a specified IAM principal the ability to generate a data key and decrypt that data key from the CMK when necessary»**

Для зашифрованих томів EBS key policy KMS-ключа має містити потрібні дозволи. Наведена політика — один із прикладів:
- **Перший statement** дозволяє вказаному IAM principal генерувати data key і розшифровувати його цим ключем. Ці дві дії потрібні, щоб шифрувати том EBS, поки він підключений до EC2.
- **Другий statement** дозволяє створювати, переглядати й відкликати grants для EC2. Grants делегують частину прав сервісам AWS чи іншим principals. Умова гарантує, що grants використовує лише EC2: він повторно підключає зашифрований том після планового чи непланового відключення. Ці події фіксує CloudTrail.

**❌ Неправильні варіанти:**
- *Security group in the first statement* — principal — це IAM-сутність, а не security group.
- *Second statement for the security group* — те саме.
- *Resources from the first statement take the role* — політика нічого подібного не описує.

</details>

---
