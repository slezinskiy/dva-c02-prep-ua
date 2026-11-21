# Інше

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 5  
Теорія: [README.md](../../README.md) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t04-q04"></a>
### T04-Q04
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has released a new application on AWS. The company are concerned about security and require a tool that can automatically assess applications for exposure, vulnerabilities, and deviations from best practices.

Which AWS service should they use?

- ▫️ AWS WAF
- ▫️ AWS Shield
- ✅ Amazon Inspector
- ▫️ AWS Secrets Manager

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS. Amazon Inspector automatically assesses applications for exposure, vulnerabilities, and deviations from best practices.

After performing an assessment, Amazon Inspector produces a detailed list of security findings prioritized by level of severity. These findings can be reviewed directly or as part of detailed assessment reports which are available via the Amazon Inspector console or API.

CORRECT: "Amazon Inspector" is the correct answer.

INCORRECT: "AWS Shield" is incorrect as this service is used to protect from distributed denial of service (DDoS) attacks.

INCORRECT: "AWS WAF" is incorrect as this is a web application firewall.

INCORRECT: "AWS Secrets Manager" is incorrect as this service is used to store secure secrets.

Джерела: <https://aws.amazon.com/inspector/>

</details>

---

<a id="t05-q02"></a>
### T05-Q02
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

A development team manage a high-traffic e-Commerce site with dynamic pricing that is updated in real-time. There have been incidents where multiple updates occur simultaneously and cause an original editor’s updates to be overwritten. How can the developers ensure that overwriting does not occur?

- ✅ Use conditional writes
- ▫️ Use concurrent writes
- ▫️ Use atomic counters
- ▫️ Use batch operations

<details><summary>Пояснення (оригінал, EN)</summary>

By default, the DynamoDB write operations (PutItem, UpdateItem, DeleteItem) are unconditional: Each operation overwrites an existing item that has the specified primary key.

DynamoDB optionally supports conditional writes for these operations. A conditional write succeeds only if the item attributes meet one or more expected conditions. Otherwise, it returns an error. Conditional writes are helpful in many situations. For example, you might want a PutItem operation to succeed only if there is not already an item with the same primary key. Or you could prevent an UpdateItem operation from modifying an item if one of its attributes has a certain value.

Conditional writes can be idempotent if the conditional check is on the same attribute that is being updated. This means that DynamoDB performs a given write request only if certain attribute values in the item match what you expect them to be at the time of the request.

For example, suppose that you issue an UpdateItem request to increase the Price of an item by 3, but only if the Price is currently 20. After you send the request, but before you get the results back, a network error occurs, and you don't know whether the request was successful. Because this conditional write is idempotent, you can retry the same UpdateItem request, and DynamoDB updates the item only if the Price is currently 20.

The following example shows how to use the condition-expression parameter to achieve a conditional write with idempotence:

aws dynamodb update-item \
--table-name ProductCatalog \
--key '{"Id":{"N":"1"}}' \
--update-expression "SET Price = :newval" \
--condition-expression "Price = :currval" \
--expression-attribute-values file://expression-attribute-values.json
For this scenario, conditional writes with idempotence will mean that each writer can check the current price and update the price only if the price matches that price. If the price is updated by another writer before the write is made, it will fail as the item price has changed and will not reflect the expected price.

CORRECT: "Use conditional writes" is the correct answer.

INCORRECT: "Use concurrent writes" is incorrect as writing concurrently to the same items is exactly what we want to avoid.

INCORRECT: "Use atomic counters" is incorrect. An atomic counter is a numeric attribute that is incremented, unconditionally, without interfering with other write requests. This is used for cases such as tracking visitors to a website. This does not prevent recent updated from being overwritten.

INCORRECT: "Use batch operations" is incorrect. Batch operations can reduce the number of network round trips from your application to DynamoDB. However, this does not solve the problem of preventing recent updates from being overwritten.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WorkingWithItems.html#WorkingWithItems.ConditionalUpdate> · <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html>

</details>

---

<a id="t05-q50"></a>
### T05-Q50
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A programmer is creating an application that requires signed requests (Signature Version 4) for invoking other AWS services. Having constructed a canonical request, created the string to sign, and calculated the signing information, which strategies can the programmer apply to finalize a signed request? (Select TWO.)

- ▫️ Append the signature to a query string parameter known as "X-Amz-Credentials".
- ✅ Incorporate the signature into an HTTP header called "Authorization".
- ✅ Insert the signature into a query string parameter referred to as "X-Amz-Signature".
- ▫️ Add the signature to a query string parameter named "Signature-Token".
- ▫️ Embed the signature in an HTTP header labelled "Authorization-Key".

<details><summary>Пояснення (оригінал, EN)</summary>

When sending a request to AWS services using Signature Version 4, the signature should be included in the "Authorization" HTTP header or as a parameter "X-Amz-Signature" in the query string.

CORRECT: "Incorporate the signature into an HTTP header called "Authorization"" is a correct answer (as explained above.)

CORRECT: "Insert the signature into a query string parameter referred to as "X-Amz-Signature"" is also a correct answer (as explained above.)

INCORRECT: "Append the signature to a query string parameter known as "X-Amz-Credentials"" is incorrect.

"X-Amz-Credentials" is a query string parameter, but it's not meant for adding signatures. It's used to include access key ID and scoped credential details.

INCORRECT: "Embed the signature in an HTTP header labelled "Authorization-Key"" is incorrect.

"Authorization-Key" is not a recognized HTTP header for adding AWS Signature Version 4.

INCORRECT: "Add the signature to a query string parameter named "Signature-Token"" is incorrect.

"Signature-Token" is not a recognized query string parameter for adding AWS Signature Version 4.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-signing.html>

</details>

---

<a id="t09-q05"></a>
### T09-Q05
*⏭ пропущено · домен: офіційна мітка*

A business has purchased one m4.xlarge Reserved Instance but it has used three m4.xlarge instances concurrently for an hour.

As a Developer, explain how the instances are charged?

- ▫️ All instances are charged at one hour of Reserved Instance usage
- ▫️ All instances are charged at one hour of On-Demand Instance usage
- ▫️ One instance is charged at one hour of On-Demand usage and the other two instances are charged at two hours of Reserved Instance usage
- ✅ One instance is charged at one hour of Reserved Instance usage and the other two instances are charged at two hours of On-Demand usage

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

All Reserved Instances provide you with a discount compared to On-Demand pricing.

One instance is charged at one hour of Reserved Instance usage and the other two instances are charged at two hours of On-Demand usage

A Reserved Instance billing benefit can apply to a maximum of 3600 seconds (one hour) of instance usage per clock-hour. You can run multiple instances concurrently, but can only receive the benefit of the Reserved Instance discount for a total of 3600 seconds per clock-hour; instance usage that exceeds 3600 seconds in a clock-hour is billed at the On-Demand rate.

Please review this note on the EC2 Reserved Instance types:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/reserved-instances-types.html

High Level Overview of EC2 Instance Purchase Options:  via - https://aws.amazon.com/ec2/pricing/

Incorrect options:

All instances are charged at one hour of Reserved Instance usage - This is incorrect.

All instances are charged at one hour of On-Demand Instance usage - This is incorrect.

One instance is charged at one hour of On-Demand usage and the other two instances are charged at two hours of Reserved Instance usage - This is incorrect. If multiple eligible instances are running concurrently, the Reserved Instance billing benefit is applied to all the instances at the same time up to a maximum of 3600 seconds in a clock-hour; thereafter, On-Demand rates apply.

Reference:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts-reserved-instances-application.html

</details>

---

<a id="t11-q59"></a>
### T11-Q59
*⏭ пропущено · домен: офіційна мітка*

A multi-national company has just moved to AWS Cloud and it has configured forecast-based AWS Budgets alerts for cost management. However, no alerts have been received even though the account and the budgets have been created almost three weeks ago.

What could be the issue with the AWS Budgets configuration?

- ▫️ Account has to be part of AWS Organizations to receive AWS Budgets alerts
- ▫️ Amazon CloudWatch could be down and hence alerts are not being sent
- ▫️ Budget forecast has been created from an account that does not have enough privileges
- ✅ AWS requires approximately 5 weeks of usage data to generate budget forecasts

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Budgets lets customers set custom budgets and receive alerts if their costs or usage exceed (or are forecasted to exceed) their budgeted amount.

AWS requires approximately 5 weeks of usage data to generate budget forecasts - AWS requires approximately 5 weeks of usage data to generate budget forecasts. If you set a budget to alert based on a forecasted amount, this budget alert isn't triggered until you have enough historical usage information.

Incorrect options:

Budget forecast has been created from an account that does not have enough privileges - This is an incorrect statement. If the user account does not have enough privileges, the user will not be able to create the budget at all.

Amazon CloudWatch could be down and hence alerts are not being sent - Amazon CloudWatch is fully managed by AWS, this option has been added as a distractor.

Account has to be part of AWS Organizations to receive AWS Budget alerts - This is an incorrect statement. Stand-alone accounts too can create budgets and being part of an Organization is not mandatory to use AWS Budgets.

Reference:

https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-best-practices.html

</details>

---
