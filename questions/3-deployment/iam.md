# IAM / STS / Organizations

**Домен 3 — Розгортання** · питань: 2  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q05"></a>
### T03-Q05
*домен: офіційна мітка*

Your AWS CodeDeploy deployment to T2 instances succeed. The new application revision makes API calls to Amazon S3 however the application is not working as expected due to authorization exceptions and you were assigned to troubleshoot the issue.

Which of the following should you do?

- ✅ Fix the IAM permissions for the EC2 instance role
- ▫️ Make the S3 bucket public
- ▫️ Fix the IAM permissions for the CodeDeploy service role
- ▫️ Enable CodeDeploy Proxy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Fix the IAM permissions for the EC2 instance role»**

Для застосунків на EC2 тимчасові облікові дані дає IAM-роль інстансу: довгострокові ключі на інстанс не потрібні. Застосунок звертається до S3 з правами цієї ролі, тож треба переконатися, що роль має доступ до бакета.

**❌ Неправильні варіанти:**
- *Fix the IAM permissions for the CodeDeploy service role* — CodeDeploy успішно задеплоїв застосунок, тож між ним та EC2 проблем немає; проблема між EC2 і S3.
- *Make the S3 bucket public* — погана практика, порушує least privilege і відкриває дані.
- *Enable CodeDeploy Proxy* — проблема не в налаштуваннях CodeDeploy, а в правах EC2 на S3.

</details>

---

<a id="t09-q13"></a>
### T09-Q13
*домен: офіційна мітка*

> ⚠️ **EB: повна потужність + мінімальний вплив збою → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

The development team at an e-commerce company completed the last deployment for their application at a reduced capacity because of the deployment policy. The application took a performance hit because of the traffic spike due to an on-going sale.

Which of the following represents the BEST deployment option for the upcoming application version such that it maintains at least the FULL capacity of the application and MINIMAL impact of failed deployment?

- ▫️ Deploy the new application version using 'Rolling' deployment policy
- ▫️ Deploy the new application version using 'Rolling with additional batch' deployment policy
- ▫️ Deploy the new application version using 'All at once' deployment policy
- ✅ Deploy the new application version using 'Immutable' deployment policy

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Deploy the new application version using 'Immutable' deployment policy»**

Elastic Beanstalk бере на себе виділення потужностей, балансування, масштабування і моніторинг. Як він працює: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html

Immutable завжди деплоїть на **нові** інстанси:
- запускається друга ASG, і нова версія обслуговує трафік поруч зі старою, доки не пройде health checks;
- якщо деплой невдалий, нові інстанси термінуються, і вплив мінімальний;
- відкат швидкий і безпечний.

Огляд політик: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

**❌ Неправильні варіанти:**
- *All at once* — найшвидше, але застосунок недоступний, і при збої буде простій.
- *Rolling* — без простою, але відкат — ручним повторним деплоєм.
- *Rolling with additional batch* — повна потужність зберігається, але відкат теж ручним повторним деплоєм.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html>

</details>

---
