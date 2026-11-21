# IAM / STS / Organizations

**Домен 3 — Розгортання** · питань: 2  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q05"></a>
### T03-Q05
*⏭ пропущено · домен: офіційна мітка*

Your AWS CodeDeploy deployment to T2 instances succeed. The new application revision makes API calls to Amazon S3 however the application is not working as expected due to authorization exceptions and you were assigned to troubleshoot the issue.

Which of the following should you do?

- ✅ Fix the IAM permissions for the EC2 instance role
- ▫️ Make the S3 bucket public
- ▫️ Fix the IAM permissions for the CodeDeploy service role
- ▫️ Enable CodeDeploy Proxy

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Fix the IAM permissions for the EC2 instance role

You should use an IAM role to manage temporary credentials for applications that run on an EC2 instance. When you use a role, you don't have to distribute long-term credentials (such as a user name and password or access keys) to an EC2 instance. Instead, the role supplies temporary permissions that applications can use when they make calls to other AWS resources. In this case, make sure your role has access to the S3 bucket.

Incorrect options:

Fix the IAM permissions for the CodeDeploy service role - The fact that CodeDeploy deployed the application to EC2 instances tells us that there was no issue between those two. The actual issue is between the EC2 instances and S3.

Make the S3 bucket public - This is not a good practice, you should strive to provide least privilege access. You may have files in here that should not be allowed public access and you are opening the door to security breaches.

Enable CodeDeploy Proxy - This is not correct as we don't need to look into CodeDeploy settings but rather between EC2 and S3 permissions.

Reference:

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html

</details>

---

<a id="t09-q13"></a>
### T09-Q13
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EB: повна потужність + мінімальний вплив збою → Immutable.** → [детальніше](../../06-contradictions-and-traps.md#c07)

The development team at an e-commerce company completed the last deployment for their application at a reduced capacity because of the deployment policy. The application took a performance hit because of the traffic spike due to an on-going sale.

Which of the following represents the BEST deployment option for the upcoming application version such that it maintains at least the FULL capacity of the application and MINIMAL impact of failed deployment?

- ▫️ Deploy the new application version using 'Rolling' deployment policy
- ▫️ Deploy the new application version using 'Rolling with additional batch' deployment policy
- ▫️ Deploy the new application version using 'All at once' deployment policy
- ✅ Deploy the new application version using 'Immutable' deployment policy

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Deploy the new application version using 'Immutable' deployment policy

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about the infrastructure that runs those applications. Elastic Beanstalk reduces management complexity without restricting choice or control. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.

How Elastic BeanStalk Works:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html

The 'Immutable' deployment policy ensures that your new application version is always deployed to new instances, instead of updating existing instances. It also has the additional advantage of a quick and safe rollback in case the deployment fails. In an immutable update, a second Auto Scaling group is launched in your environment and the new version serves traffic alongside the old version until the new instances pass health checks. In case of deployment failure, the new instances are terminated, so the impact is minimal.

Overview of Elastic Beanstalk Deployment Policies:  via - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html

Incorrect options:

Deploy the new application version using 'All at once' deployment policy - Although 'All at once' is the quickest deployment method, but the application may become unavailable to users (or have low availability) for a short time. Also in case of deployment failure, the application sees a downtime, so this option is not correct.

Deploy the new application version using 'Rolling' deployment policy - This policy avoids downtime and minimizes reduced availability, at a cost of a longer deployment time. However in case of deployment failure, the rollback process is via manual redeploy, so it's not as quick as the Immutable deployment.

Deploy the new application version using 'Rolling with additional batch' deployment policy - This policy avoids any reduced availability, at a cost of an even longer deployment time compared to the Rolling method. Suitable if you must maintain the same bandwidth throughout the deployment. However in case of deployment failure, the rollback process is via manual redeploy, so it's not as quick as the Immutable deployment.

Джерела: <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html>

</details>

---
