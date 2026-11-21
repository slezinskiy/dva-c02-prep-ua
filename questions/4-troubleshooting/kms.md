# KMS / шифрування / сертифікати

**Домен 4 — Усунення несправностей та оптимізація** · питань: 1  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#kms) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t09-q42"></a>
### T09-Q42
*⏭ пропущено · домен: офіційна мітка*

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

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The first statement provides a specified IAM principal the ability to generate a data key and decrypt that data key from the CMK when necessary - To create and use an encrypted Amazon Elastic Block Store (EBS) volume, you need permissions to use Amazon EBS. The key policy associated with the CMK would need to include these. The above policy is an example of one such policy.

In this CMK policy, the first statement provides a specified IAM principal the ability to generate a data key and decrypt that data key from the CMK when necessary. These two APIs are necessary to encrypt the EBS volume while it’s attached to an Amazon Elastic Compute Cloud (EC2) instance.

The second statement in this policy provides the specified IAM principal the ability to create, list, and revoke grants for Amazon EC2. Grants are used to delegate a subset of permissions to AWS services, or other principals, so that they can use your keys on your behalf. In this case, the condition policy explicitly ensures that only Amazon EC2 can use the grants. Amazon EC2 will use them to re-attach an encrypted EBS volume back to an instance if the volume gets detached due to a planned or unplanned outage. These events will be recorded within AWS CloudTrail when, and if, they do occur for your auditing.

Incorrect options:

The first statement provides the security group the ability to generate a data key and decrypt that data key from the CMK when necessary

The second statement in this policy provides the security group (mentioned in the first statement of the policy), the ability to create, list, and revoke grants for Amazon EC2

The second statement in the policy mentions that all the resources stated in the first statement can take the specified role which will provide the ability to create, list, and revoke grants for Amazon EC2

These three options contradict the explanation provided above, so these options are incorrect.

Reference:

https://d0.awsstatic.com/whitepapers/aws-kms-best-practices.pdf

</details>

---
