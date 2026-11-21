# IAM / STS / Organizations

**Домен 4 — Усунення несправностей та оптимізація** · питань: 2  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q17"></a>
### T03-Q17
*домен: офіційна мітка*

A user has an IAM policy as well as an Amazon SQS policy that apply to his account. The IAM policy grants his account permission for the ReceiveMessage action on example_queue, whereas the Amazon SQS policy gives his account permission for the SendMessage action on the same queue.

Considering the permissions above, which of the following options are correct? (Select two)

- ▫️ Either of IAM policies or Amazon SQS policies should be used to grant permissions. Both cannot be used together
- ✅ If you add a policy that denies the user access to all actions for the queue, the policy will override the other two policies and the user will not have access to example_queue
- ▫️ Adding only an IAM policy to deny the user of all actions on the queue is not enough. The SQS policy should also explicitly deny all action
- ✅ The user can send a ReceiveMessage request to example_queue, the IAM policy allows this action
- ▫️ If the user sends a SendMessage request to example_queue, the IAM policy will deny this action

<details><summary>Пояснення</summary>

**✅ Правильні відповіді: «The user can send a ReceiveMessage request to example_queue, the IAM policy allows this action» та «If you add a policy that denies the user access to all actions for the queue, the policy will override the other two policies»**

До користувача застосовуються і IAM-політика (дозволяє `ReceiveMessage`), і політика SQS (дозволяє `SendMessage`). Права з обох складаються. Щоб забрати весь доступ, найпростіше додати політику з явним Deny на всі дії з чергою: **явна заборона завжди перемагає дозвіл**. Те саме дасть Deny-statement у політиці SQS.

**❌ Неправильні варіанти:**
- *SendMessage — IAM policy will deny this action* — політика SQS дозволяє SendMessage, а явного Deny в IAM немає.
- *Either IAM or SQS policies, both cannot be used together* — можна використовувати одну з них або обидві.
- *Only an IAM deny is not enough, SQS policy must also deny* — явного Deny в будь-якій із політик досить.

</details>

---

<a id="t09-q06"></a>
### T09-Q06
*домен: офіційна мітка*

You are a development team lead setting permissions for other IAM users with limited permissions. On the AWS Management Console, you created a dev group where new developers will be added, and on your workstation, you configured a developer profile. You would like to test that this user cannot terminate instances.

Which of the following options would you execute?

- ✅ Use the AWS CLI --dry-run option
- ▫️ Retrieve the policy using the EC2 metadata service and use the IAM policy simulator
- ▫️ Using the CLI, create a dummy EC2 and delete it using another CLI call
- ▫️ Use the AWS CLI --test option

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use the AWS CLI --dry-run option»**

`--dry-run` перевіряє, чи є дозволи на дію, не виконуючи її, і завжди повертає помилку:
- **`DryRunOperation`** — дозволи є;
- **`UnauthorizedOperation`** — дозволів немає.

**❌ Неправильні варіанти:**
- *--test* — такої опції немає.
- *EC2 metadata + policy simulator* — metadata service повертає дані інстансу (instance-id, hostname тощо), але не перевіряє дозволи.
- *Create and delete a dummy EC2* — незграбно; `--dry-run` елегантніший.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html> · <https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html>

</details>

---
