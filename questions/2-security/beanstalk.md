# AWS Elastic Beanstalk

**Домен 2 — Безпека** · питань: 1  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#beanstalk) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q56"></a>
### T07-Q56
*⏭ пропущено · домен: офіційна мітка*

Your application is deployed automatically using AWS Elastic Beanstalk. Your YAML configuration files are stored in the folder .ebextensions and new files are added or updated often. The DevOps team does not want to re-deploy the application every time there are configuration changes, instead, they would rather manage configuration externally, securely, and have it load dynamically into the application at runtime.

What option allows you to do this?

- ▫️ Use Environment variables
- ▫️ Use S3
- ✅ Use SSM Parameter Store
- ▫️ Use Stage Variables

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use SSM Parameter Store

AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, and license codes as parameter values. For the given use-case, as the DevOps team does not want to re-deploy the application every time there are configuration changes, so they can use the SSM Parameter Store to store the configuration externally.

Incorrect options:

Use Environment variables - Environment variables provide another way to specify configuration options and credentials, and can be useful for scripting or temporarily setting a named profile as the default. Your application is not running AWS CLI. Since the use-case requires the configuration to be stored securely, so using Environment variables is ruled out, as these are not encrypted at rest and these are visible in clear text in the AWS Console as well as in the response of some actions of the Elastic BeanStalk API.

Use Stage Variables - You can use stage variables for managing multiple release stages for API Gateway, this is not what you are looking for here.

Use S3 - S3 offers the same benefit as the SSM Parameter Store where there are no servers to manage. With S3 you have to set encryption and choose other security options and there are more chances of misconfiguring security if you share your S3 bucket with other objects. You would have to create a custom setup to come close to the parameter store. Use Parameter Store and let AWS handle the rest.

Reference:

https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-paramstore.html

</details>

---
