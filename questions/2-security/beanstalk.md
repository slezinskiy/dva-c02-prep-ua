# AWS Elastic Beanstalk

**Домен 2 — Безпека** · питань: 1  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#beanstalk) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q56"></a>
### T07-Q56
*домен: офіційна мітка*

Your application is deployed automatically using AWS Elastic Beanstalk. Your YAML configuration files are stored in the folder .ebextensions and new files are added or updated often. The DevOps team does not want to re-deploy the application every time there are configuration changes, instead, they would rather manage configuration externally, securely, and have it load dynamically into the application at runtime.

What option allows you to do this?

- ▫️ Use Environment variables
- ▫️ Use S3
- ✅ Use SSM Parameter Store
- ▫️ Use Stage Variables

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use SSM Parameter Store»**

SSM Parameter Store — безпечне ієрархічне сховище конфігурацій і секретів: паролів, рядків підключення, ліцензійних кодів. Якщо винести конфігурацію в Parameter Store, застосунок не доведеться повторно деплоїти при кожній зміні.

**❌ Неправильні варіанти:**
- *Environment variables* — не шифруються at rest і видні відкритим текстом у консолі та відповідях API Elastic Beanstalk. А конфігурацію треба зберігати безпечно.
- *Stage variables* — для керування stages API Gateway; до задачі не стосуються.
- *S3* — теж без серверів, але шифрування й безпеку доведеться налаштовувати вручну, і легше помилитися. Простіше використати Parameter Store.

</details>

---
