# CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify

**Домен 2 — Безпека** · питань: 2  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#cicd) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q07"></a>
### T01-Q07
*⏭ пропущено · домен: офіційна мітка*

You were assigned to a project that requires the use of the AWS CLI to build a project with AWS CodeBuild. Your project's root directory includes the buildspec.yml file to run build commands and would like your build artifacts to be automatically encrypted at the end.

How should you configure CodeBuild to accomplish this?

- ✅ Specify a KMS key to use
- ▫️ Use the AWS Encryption SDK
- ▫️ Use an AWS Lambda Hook
- ▫️ Use In Flight encryption (SSL)

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Specify a KMS key to use

AWS Key Management Service (KMS) makes it easy for you to create and manage cryptographic keys and control their use across a wide range of AWS services and in your applications.

For AWS CodeBuild to encrypt its build output artifacts, it needs access to an AWS KMS customer master key (CMK). By default, AWS CodeBuild uses the AWS-managed CMK for Amazon S3 in your AWS account. The following environment variable provides these details:

CODEBUILD_KMS_KEY_ID: The identifier of the AWS KMS key that CodeBuild is using to encrypt the build output artifact (for example, arn:aws:kms:region-ID:account-ID:key/key-ID or alias/key-alias).

Incorrect options:

Use an AWS Lambda Hook - Code hook is used for integration with Lambda and is not relevant for the given use-case.

Use the AWS Encryption SDK - The SDK just makes it easier for you to implement encryption best practices in your application and is not relevant for the given use-case.

Use In-Flight encryption (SSL) - SSL is usually for internet traffic which in this case will be using internal traffic through AWS and is not relevant for the given use-case.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/setting-up.html>

</details>

---

<a id="t03-q15"></a>
### T03-Q15
*⏭ пропущено · домен: офіційна мітка*

A company would like to migrate the existing application code from a GitHub repository to AWS CodeCommit.

As an AWS Certified Developer Associate, which of the following would you recommend for migrating the cloned repository to CodeCommit over HTTPS?

- ▫️ Use IAM Multi-Factor authentication
- ▫️ Use authentication offered by GitHub secure tokens
- ▫️ Use IAM user secret access key and access key ID
- ✅ Use Git credentials generated from IAM

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use Git credentials generated from IAM - CodeCommit repositories are Git-based and support the basic functionalities of Git such as Git credentials. AWS recommends that you use an IAM user when working with CodeCommit. You can access CodeCommit with other identity types, but the other identity types are subject to limitations.

The simplest way to set up connections to AWS CodeCommit repositories is to configure Git credentials for CodeCommit in the IAM console, and then use those credentials for HTTPS connections. You can also use these same credentials with any third-party tool or individual development environment (IDE) that supports HTTPS authentication using a static user name and password.

An IAM user is an identity within your Amazon Web Services account that has specific custom permissions. For example, an IAM user can have permissions to create and manage Git credentials for accessing CodeCommit repositories. This is the recommended user type for working with CodeCommit. You can use an IAM user name and password to sign in to secure AWS webpages like the AWS Management Console, AWS Discussion Forums, or the AWS Support Center.

Authentication and access control for AWS CodeCommit:  via - https://docs.aws.amazon.com/codecommit/latest/userguide/auth-and-access-control.html

Incorrect options:

Use IAM Multi-Factor authentication - AWS Multi-Factor Authentication (MFA) is a simple best practice that adds an extra layer of protection on top of your user name and password. With MFA enabled, when a user signs in to an AWS Management Console, they will be prompted for their user name and password (the first factor—what they know), as well as for an authentication code from their AWS MFA device (the second factor—what they have). Taken together, these multiple factors provide increased security for your AWS account settings and resources.

Use IAM user secret access key and access key ID - Access keys are long-term credentials for an IAM user or the AWS account root user. You can use access keys to sign programmatic requests to the AWS CLI or AWS API (directly or using the AWS SDK). As a best practice, AWS suggests using temporary security credentials (IAM roles) instead of access keys.

Use authentication offered by GitHub secure tokens - Personal access tokens (PATs) are an alternative to using passwords for authentication to GitHub when using the GitHub API or the command line. This option is specific to GitHub only and hence not useful for the given use case.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/auth-and-access-control.html> · <https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html>

</details>

---
