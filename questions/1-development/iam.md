# IAM / STS / Organizations

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 6  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q39"></a>
### T01-Q39
*⏭ пропущено · домен: офіційна мітка*

An EC2 instance has an IAM instance role attached to it, providing it read and write access to the S3 bucket 'my_bucket'. You have tested the IAM instance role and both reads and writes are working. You then remove the IAM role from the EC2 instance and test both read and write again. Writes stopped working but reads are still working.

What is the likely cause of this behavior?

- ✅ The S3 bucket policy authorizes reads
- ▫️ Removing an instance role from an EC2 instance can take a few minutes before being active
- ▫️ The EC2 instance is using cached temporary IAM credentials
- ▫️ When a read is done on a bucket, there's a grace period of 5 minutes to do the same read again

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The S3 bucket policy authorizes reads

When evaluating an IAM policy of an EC2 instance doing actions on S3, the least-privilege union of both the IAM policy of the EC2 instance and the bucket policy of the S3 bucket are taken into account.

For the given use-case, as IAM role has been removed, therefore only the S3 bucket policy comes into effect which authorizes reads.

Here is a great reference blog for understanding the various scenarios for using IAM policy vs S3 bucket policy - https://aws.amazon.com/blogs/security/iam-policies-and-bucket-policies-and-acls-oh-my-controlling-access-to-s3-resources/

Incorrect options:

The EC2 instance is using cached temporary IAM credentials - As the IAM instance role has been removed that wouldn't be the case

Removing an instance role from an EC2 instance can take a few minutes before being active - It is immediately active and even if it wasn't, it wouldn't make sense as we can still do reads but not writes.

When a read is done on a bucket, there's a grace period of 5 minutes to do the same read again - This is not true. Every single request is evaluated against IAM in the AWS model.

Reference:

https://aws.amazon.com/blogs/security/iam-policies-and-bucket-policies-and-acls-oh-my-controlling-access-to-s3-resources/

</details>

---

<a id="t07-q39"></a>
### T07-Q39
*⏭ пропущено · домен: офіційна мітка*

A development team has created a new IAM user that has s3:putObject permission to write to an S3 bucket. This S3 bucket uses server-side encryption with AWS KMS managed keys (SSE-KMS) as the default encryption. Using the access key ID and the secret access key of the IAM user, the application received an access denied error when calling the PutObject API.

As a Developer Associate, how would you resolve this issue?

- ▫️ Correct the policy of the IAM user to allow the s3:Encrypt action
- ▫️ Correct the ACL of the S3 bucket to allow the IAM user to upload encrypted objects
- ✅ Correct the policy of the IAM user to allow the kms:GenerateDataKey action
- ▫️ Correct the bucket policy of the S3 bucket to allow the IAM user to upload encrypted objects

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Correct the policy of the IAM user to allow the kms:GenerateDataKey action - You can protect data at rest in Amazon S3 by using three different modes of server-side encryption: SSE-S3, SSE-C, or SSE-KMS. SSE-KMS requires that AWS manage the data key but you manage the customer master key (CMK) in AWS KMS. You can choose a customer managed CMK or the AWS managed CMK for Amazon S3 in your account. If you choose to encrypt your data using the standard features, AWS KMS and Amazon S3 perform the following actions:

Amazon S3 requests a plaintext data key and a copy of the key encrypted under the specified CMK.

AWS KMS generates a data key, encrypts it under the CMK, and sends both the plaintext data key and the encrypted data key to Amazon S3.

Amazon S3 encrypts the data using the data key and removes the plaintext key from memory as soon as possible after use.

Amazon S3 stores the encrypted data key as metadata with the encrypted data.

The error message indicates that your IAM user or role needs permission for the kms:GenerateDataKey action. This permission is required for buckets that use default encryption with a custom AWS KMS key.

In the JSON policy documents, look for policies related to AWS KMS access. Review statements with "Effect": "Allow" to check if the user or role has permissions for the kms:GenerateDataKey action on the bucket's AWS KMS key. If this permission is missing, then add the permission to the appropriate policy.

In the JSON policy documents, look for statements with "Effect": "Deny". Then, confirm that those statements don't deny the s3:PutObject action on the bucket. The statements must also not deny the IAM user or role access to the kms:GenerateDataKey action on the key used to encrypt the bucket. Additionally, make sure the necessary KMS and S3 permissions are not restricted using a VPC endpoint policy, service control policy, permissions boundary, or session policy.

Incorrect options:

Correct the policy of the IAM user to allow the s3:Encrypt action - This is an invalid action given only as a distractor.

Correct the bucket policy of the S3 bucket to allow the IAM user to upload encrypted objects - The user already has access to the bucket. What the user lacks is access to generate a KMS key, which is mandatory when a bucket is enabled for default encryption.

Correct the ACL of the S3 bucket to allow the IAM user to upload encrypted objects - Amazon S3 access control lists (ACLs) enable you to manage access to buckets and objects. Each bucket and object has an ACL attached to it as a subresource. It defines which AWS accounts or groups are granted access and the type of access. ACL is another way of giving access to S3 bucket objects. Permissions to use KMS keys will still be needed.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/s3-access-denied-error-kms/> · <https://docs.aws.amazon.com/kms/latest/developerguide/services-s3.html>

</details>

---

<a id="t09-q35"></a>
### T09-Q35
*⏭ пропущено · домен: офіційна мітка*

The development team at a HealthCare company has deployed EC2 instances in AWS Account A. These instances need to access patient data with Personally Identifiable Information (PII) on multiple S3 buckets in another AWS Account B.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Create an IAM role (instance profile) in Account A and set Account B as a trusted entity. Attach this role to the EC2 instances in Account A and add an inline policy to this role to access S3 data from Account B
- ✅ Create an IAM role with S3 access in Account B and set Account A as a trusted entity. Create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B
- ▫️ Add a bucket policy to all the Amazon S3 buckets in Account B to allow access from EC2 instances in Account A
- ▫️ Copy the underlying AMI for the EC2 instances from Account A into Account B. Launch EC2 instances in Account B using this AMI and then access the PII data on Amazon S3 in Account B

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create an IAM role with S3 access in Account B and set Account A as a trusted entity. Create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B

You can give EC2 instances in one account ("account A") permissions to assume a role from another account ("account B") to access resources such as S3 buckets. You need to create an IAM role in Account B and set Account A as a trusted entity. Then attach a policy to this IAM role such that it delegates access to Amazon S3 like so -

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::awsexamplebucket1",
                "arn:aws:s3:::awsexamplebucket1/*",
                "arn:aws:s3:::awsexamplebucket2",
                "arn:aws:s3:::awsexamplebucket2/*"
            ]
        }
    ]
}
Then you can create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B like so -

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::AccountB_ID:role/ROLENAME"
        }
    ]
}
Incorrect options:

Create an IAM role (instance profile) in Account A and set Account B as a trusted entity. Attach this role to the EC2 instances in Account A and add an inline policy to this role to access S3 data from Account B - This option contradicts the explanation provided earlier in the explanation, hence this option is incorrect.

Copy the underlying AMI for the EC2 instances from Account A into Account B. Launch EC2 instances in Account B using this AMI and then access the PII data on Amazon S3 in Account B - Copying the AMI is a distractor as this does not solve the use-case outlined in the problem statement.

Add a bucket policy to all the Amazon S3 buckets in Account B to allow access from EC2 instances in Account A - Just adding a bucket policy in Account B is not enough, as you also need to create an IAM policy in Account A to access S3 objects in Account B.

Please review this reference material for a deep-dive on cross-account access to objects that are in Amazon S3 buckets - https://aws.amazon.com/premiumsupport/knowledge-center/cross-account-access-s3/

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/s3-instance-access-bucket/>

</details>

---

<a id="t11-q45"></a>
### T11-Q45
*⏭ пропущено · домен: офіційна мітка*

As part of his development work, an AWS Certified Developer Associate is creating policies and attaching them to IAM identities. After creating necessary Identity-based policies, he is now creating Resource-based policies.

Which is the only resource-based policy that the IAM service supports?

- ✅ Trust policy
- ▫️ AWS Organizations Service Control Policies (SCP)
- ▫️ Access control list (ACL)
- ▫️ Permissions boundary

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

You manage access in AWS by creating policies and attaching them to IAM identities (users, groups of users, or roles) or AWS resources. A policy is an object in AWS that, when associated with an identity or resource, defines their permissions. Resource-based policies are JSON policy documents that you attach to a resource such as an Amazon S3 bucket. These policies grant the specified principal permission to perform specific actions on that resource and define under what conditions this applies.

Trust policy - Trust policies define which principal entities (accounts, users, roles, and federated users) can assume the role. An IAM role is both an identity and a resource that supports resource-based policies. For this reason, you must attach both a trust policy and an identity-based policy to an IAM role. The IAM service supports only one type of resource-based policy called a role trust policy, which is attached to an IAM role.

Incorrect options:

AWS Organizations Service Control Policies (SCP) - If you enable all features of AWS organization, then you can apply service control policies (SCPs) to any or all of your accounts. SCPs are JSON policies that specify the maximum permissions for an organization or organizational unit (OU). The SCP limits permissions for entities in member accounts, including each AWS account root user. An explicit deny in any of these policies overrides the allow.

Access control list (ACL) - Access control lists (ACLs) are service policies that allow you to control which principals in another account can access a resource. ACLs cannot be used to control access for a principal within the same account. Amazon S3, AWS WAF, and Amazon VPC are examples of services that support ACLs.

Permissions boundary - AWS supports permissions boundaries for IAM entities (users or roles). A permissions boundary is an advanced feature for using a managed policy to set the maximum permissions that an identity-based policy can grant to an IAM entity. An entity's permissions boundary allows it to perform only the actions that are allowed by both its identity-based policies and its permissions boundaries.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies_resource-based> · <https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html>

</details>

---

<a id="t11-q56"></a>
### T11-Q56
*⏭ пропущено · домен: офіційна мітка*

As an AWS Certified Developer Associate, you have configured the AWS CLI on your workstation. Your default region is us-east-1 and your IAM user has permissions to operate commands on services such as EC2, S3 and RDS in any region. You would like to execute a command to stop an EC2 instance in the us-east-2 region.

What of the following is the MOST optimal solution to address this use-case?

- ▫️ You should create a new IAM user just for that other region
- ▫️ You need to override the default region by using aws configure
- ▫️ Use boto3 dependency injection
- ✅ Use the --region parameter

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the --region parameter: If the region parameter is not set, then the CLI command is executed against the default AWS region.

You can also review all general options for AWS CLI:  via - https://docs.aws.amazon.com/cli/latest/topic/config-vars.html#general-options

Incorrect options:

You need to override the default region by using aws configure - This is not the most optimal way as you will have to change it again to reset the default region.

You should create a new IAM user just for that other region - This is not the most optimal way as you would need to manage two IAM user profiles.

Use boto3 dependency injection - With the CLI you do not use boto3. This option is a distractor.

Reference:

https://docs.aws.amazon.com/cli/latest/topic/config-vars.html#general-options

</details>

---

<a id="t12-q02"></a>
### T12-Q02
*⏭ пропущено · домен: офіційна мітка*

The development team at a retail organization wants to allow a Lambda function in its AWS Account A to access a DynamoDB table in another AWS Account B.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Create an IAM role in Account B with access to DynamoDB. Modify the trust policy of the execution role in Account A to allow the execution role of Lambda to assume the IAM role in Account B. Update the Lambda function code to add the AssumeRole API call
- ▫️ Add a resource policy to the DynamoDB table in AWS Account B to give access to the Lambda function in Account A
- ✅ Create an IAM role in Account B with access to DynamoDB. Modify the trust policy of the role in Account B to allow the execution role of Lambda to assume this role. Update the Lambda function code to add the AssumeRole API call
- ▫️ Create a clone of the Lambda function in AWS Account B so that it can access the DynamoDB table in the same account

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create an IAM role in account B with access to DynamoDB. Modify the trust policy of the role in Account B to allow the execution role of Lambda to assume this role. Update the Lambda function code to add the AssumeRole API call

You can give a Lambda function created in one account ("account A") permissions to assume a role from another account ("account B") to access resources such as DynamoDB or S3 bucket. You need to create an execution role in Account A that gives the Lambda function permission to do its work. Then you need to create a role in account B that the Lambda function in account A assumes to gain access to the cross-account DynamoDB table. Make sure that you modify the trust policy of the role in Account B to allow the execution role of Lambda to assume this role. Finally, update the Lambda function code to add the AssumeRole API call.

Sample use-case to configure a Lambda function to assume a role from another AWS account:  via - https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role/

Incorrect options:

Create a clone of the Lambda function in AWS Account B so that it can access the DynamoDB table in the same account - Creating a clone of the Lambda function is a distractor as this does not solve the use-case outlined in the problem statement.

Add a resource policy to the DynamoDB table in AWS Account B to give access to the Lambda function in Account A - You cannot attach a resource policy to a DynamoDB table, so this option is incorrect.

Create an IAM role in Account B with access to DynamoDB. Modify the trust policy of the execution role in Account A to allow the execution role of Lambda to assume the IAM role in Account B. Update the Lambda function code to add the AssumeRole API call - As mentioned in the explanation above, you need to modify the trust policy of the IAM role in Account B so that it allows the execution role of Lambda function in account A to assume the IAM role in Account B.

Reference: https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role/

</details>

---
