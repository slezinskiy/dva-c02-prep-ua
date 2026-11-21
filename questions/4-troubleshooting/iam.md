# IAM / STS / Organizations

**Домен 4 — Усунення несправностей та оптимізація** · питань: 2  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#iam) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q17"></a>
### T03-Q17
*⏭ пропущено · домен: офіційна мітка*

A user has an IAM policy as well as an Amazon SQS policy that apply to his account. The IAM policy grants his account permission for the ReceiveMessage action on example_queue, whereas the Amazon SQS policy gives his account permission for the SendMessage action on the same queue.

Considering the permissions above, which of the following options are correct? (Select two)

- ▫️ Either of IAM policies or Amazon SQS policies should be used to grant permissions. Both cannot be used together
- ✅ If you add a policy that denies the user access to all actions for the queue, the policy will override the other two policies and the user will not have access to example_queue
- ▫️ Adding only an IAM policy to deny the user of all actions on the queue is not enough. The SQS policy should also explicitly deny all action
- ✅ The user can send a ReceiveMessage request to example_queue, the IAM policy allows this action
- ▫️ If the user sends a SendMessage request to example_queue, the IAM policy will deny this action

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

The user can send a ReceiveMessage request to example_queue, the IAM policy allows this action

The user has both an IAM policy and an Amazon SQS policy that apply to his account. The IAM policy grants his account permission for the ReceiveMessage action on example_queue, whereas the Amazon SQS policy gives his account permission for the SendMessage action on the same queue.

How IAM policy and SQS policy work in tandem:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-using-identity-based-policies.html

If you add a policy that denies the user access to all actions for the queue, the policy will override the other two policies and the user will not have access to example_queue

To remove the user's full access to the queue, the easiest thing to do is to add a policy that denies him access to all actions for the queue. This policy overrides the other two because an explicit deny always overrides an allow.

You can also add an additional statement to the Amazon SQS policy that denies the user any type of access to the queue. It has the same effect as adding an IAM policy that denies the user access to the queue.

How IAM policy and SQS policy work in tandem:  via - https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-using-identity-based-policies.html

Incorrect options:

If the user sends a SendMessage request to example_queue, the IAM policy will deny this action - If the user sends a SendMessage request to example_queue, the Amazon SQS policy allows the action. The IAM policy has no explicit deny on this action, so it plays no part.

Either of IAM policies or Amazon SQS policies should be used to grant permissions. Both cannot be used together - There are two ways to give your users permissions to your Amazon SQS resources: using the Amazon SQS policy system and using the IAM policy system. You can use one or the other, or both. For the most part, you can achieve the same result with either one.

Adding only an IAM policy to deny the user of all actions on the queue is not enough. The SQS policy should also explicitly deny all action - The user can be denied access using any one of the policies. Explicit deny in any policy will override all other allow actions defined using either of the policies.

Reference:

https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-using-identity-based-policies.html

</details>

---

<a id="t09-q06"></a>
### T09-Q06
*⏭ пропущено · домен: офіційна мітка*

You are a development team lead setting permissions for other IAM users with limited permissions. On the AWS Management Console, you created a dev group where new developers will be added, and on your workstation, you configured a developer profile. You would like to test that this user cannot terminate instances.

Which of the following options would you execute?

- ✅ Use the AWS CLI --dry-run option
- ▫️ Retrieve the policy using the EC2 metadata service and use the IAM policy simulator
- ▫️ Using the CLI, create a dummy EC2 and delete it using another CLI call
- ▫️ Use the AWS CLI --test option

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the AWS CLI --dry-run option: The --dry-run option checks whether you have the required permissions for the action, without actually making the request, and provides an error response. If you have the required permissions, the error response is DryRunOperation, otherwise, it is UnauthorizedOperation.

Incorrect options:

Use the AWS CLI --test option - This is a made-up option and has been added as a distractor.

Retrieve the policy using the EC2 metadata service and use the IAM policy simulator - EC2 metadata service is used to retrieve dynamic information such as instance-id, local-hostname, public-hostname. This cannot be used to check whether you have the required permissions for the action.

Using the CLI, create a dummy EC2 and delete it using another CLI call - That would not work as the current EC2 may have permissions that the dummy instance does not have. If permissions were the same it can work but it's not as elegant as using the dry-run option.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html> · <https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html>

</details>

---
