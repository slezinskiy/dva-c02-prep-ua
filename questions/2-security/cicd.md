# CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify

**Домен 2 — Безпека** · питань: 2  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#cicd) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q07"></a>
### T01-Q07
*домен: офіційна мітка*

You were assigned to a project that requires the use of the AWS CLI to build a project with AWS CodeBuild. Your project's root directory includes the buildspec.yml file to run build commands and would like your build artifacts to be automatically encrypted at the end.

How should you configure CodeBuild to accomplish this?

- ✅ Specify a KMS key to use
- ▫️ Use the AWS Encryption SDK
- ▫️ Use an AWS Lambda Hook
- ▫️ Use In Flight encryption (SSL)

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Specify a KMS key to use»**

AWS KMS дає змогу створювати криптографічні ключі й керувати їх використанням у сервісах AWS і застосунках. Щоб CodeBuild шифрував вихідні артефакти збірки, йому потрібен доступ до ключа KMS. За замовчуванням CodeBuild використовує AWS managed ключ для S3 у твоєму акаунті. Змінна середовища `CODEBUILD_KMS_KEY_ID` містить ідентифікатор ключа, яким CodeBuild шифрує артефакти (ARN або alias).

**❌ Неправильні варіанти:**
- *Use an AWS Lambda Hook* — hook для інтеграції з Lambda, до задачі не стосується.
- *Use the AWS Encryption SDK* — SDK лише спрощує впровадження шифрування у власному застосунку, тут не потрібен.
- *Use In-Flight encryption (SSL)* — SSL стосується мережевого трафіку, а тут ідеться про шифрування артефактів at rest.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/setting-up.html>

</details>

---

<a id="t03-q15"></a>
### T03-Q15
*домен: офіційна мітка*

A company would like to migrate the existing application code from a GitHub repository to AWS CodeCommit.

As an AWS Certified Developer Associate, which of the following would you recommend for migrating the cloned repository to CodeCommit over HTTPS?

- ▫️ Use IAM Multi-Factor authentication
- ▫️ Use authentication offered by GitHub secure tokens
- ▫️ Use IAM user secret access key and access key ID
- ✅ Use Git credentials generated from IAM

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Git credentials generated from IAM»**

Репозиторії CodeCommit засновані на Git і підтримують Git credentials. AWS рекомендує працювати з CodeCommit через IAM-користувача. Найпростіше налаштувати Git credentials для CodeCommit у консолі IAM і використовувати їх для HTTPS. Ті самі облікові дані працюють у будь-якому сторонньому інструменті чи IDE, що підтримує HTTPS-автентифікацію за статичним логіном і паролем.

**❌ Неправильні варіанти:**
- *Use IAM Multi-Factor authentication* — MFA додає другий фактор для входу в консоль, але не є способом підключення Git по HTTPS.
- *Use IAM user secret access key and access key ID* — довгострокові ключі для CLI/API (через credential helper теж можна, але рекомендовано Git credentials).
- *GitHub secure tokens* — PAT стосуються лише GitHub.

Джерела: <https://docs.aws.amazon.com/codecommit/latest/userguide/auth-and-access-control.html> · <https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html>

</details>

---
