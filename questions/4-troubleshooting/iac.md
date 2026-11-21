# CloudFormation / SAM / CDK

**Домен 4 — Усунення несправностей та оптимізація** · питань: 4  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#iac) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t07-q07"></a>
### T07-Q07
*⏭ пропущено · домен: офіційна мітка*

You are working for a shipping company that is automating the creation of ECS clusters with an Auto Scaling Group using an AWS CloudFormation template that accepts cluster name as its parameters. Initially, you launch the template with input value 'MainCluster', which deployed five instances across two availability zones. The second time, you launch the template with an input value 'SecondCluster'. However, the instances created in the second run were also launched in 'MainCluster' even after specifying a different cluster name.

What is the root cause of this issue?

- ▫️ The EC2 instance is missing IAM permissions to join the other clusters
- ✅ The cluster name Parameter has not been updated in the file /etc/ecs/ecs.config during bootstrap
- ▫️ The security groups on the EC2 instance are pointing to the wrong ECS cluster
- ▫️ The ECS agent Docker image must be re-built to connect to the other clusters

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The cluster name Parameter has not been updated in the file /etc/ecs/ecs.config during bootstrap - In the ecs.config file you have to configure the parameter ECS_CLUSTER='your_cluster_name' to register the container instance with a cluster named 'your_cluster_name'.

Sample config for ECS Container Agent:  via - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/bootstrap_container_instance.html

Incorrect options:

The EC2 instance is missing IAM permissions to join the other clusters - EC2 instances are getting registered to the first cluster, so permissions are not an issue here and hence this statement is an incorrect choice for the current use case.

The ECS agent Docker image must be re-built to connect to the other clusters - Since the first set of instances got created from the template without any issues, there is no issue with the ECS agent here.

The security groups on the EC2 instance are pointing to the wrong ECS cluster - Security groups govern the rules about the incoming network traffic to your ECS containers. The issue here is not about user access and hence is a wrong choice for the current use case.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/bootstrap_container_instance.html> · <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html>

</details>

---

<a id="t09-q08"></a>
### T09-Q08
*⏭ пропущено · домен: офіційна мітка*

A developer is building a serverless application on AWS and wants to establish an accelerated development workflow. The workflow must allow the developer to deploy incremental changes for testing without deploying the entire application for every code commit. The developer wants to streamline the process while minimizing deployment time.

What should the developer do to meet these requirements?

- ▫️ Use the cdk diff command from the AWS Cloud Development Kit (AWS CDK) to deploy incremental changes to AWS for testing
- ✅ Use the sam sync command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes
- ▫️ Use the sam deploy command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes
- ▫️ Use the cdk deploy command from the AWS Cloud Development Kit (AWS CDK) to deploy incremental changes to AWS for testing

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the sam sync command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes

The sam sync command is specifically designed to enable rapid iteration by synchronizing local changes with the deployed serverless application on AWS. This allows developers to quickly test incremental changes without the overhead of redeploying the entire stack, aligning perfectly with the developer's requirements.

Incorrect options:

Use the cdk deploy command from the AWS Cloud Development Kit (AWS CDK) to deploy incremental changes to AWS for testing - The cdk deploy command is a general-purpose deployment tool for AWS CDK stacks. While it can deploy changes, it does not offer the same level of optimization for incremental changes as sam sync for serverless applications. This command is more suitable for CDK projects rather than SAM-based serverless applications.

Use the sam deploy command from the AWS Serverless Application Model (AWS SAM) to deploy incremental changes - The sam deploy command performs a full deployment of the application or updated resources, even for small changes. It does not specifically optimize for incremental deployments, which results in longer deployment times.

Use the cdk diff command from the AWS Cloud Development Kit (AWS CDK) to deploy incremental changes to AWS for testing - The cdk diff command is not a deployment command. It is used to generate a difference report between the deployed and local stacks. It does not synchronize or deploy incremental changes, making it irrelevant for this requirement.use-case.

Reference:

https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-sync.html

</details>

---

<a id="t09-q54"></a>
### T09-Q54
*⏭ пропущено · домен: офіційна мітка*

Your team lead has asked you to learn AWS CloudFormation to create a collection of related AWS resources and provision them in an orderly fashion. You decide to provide AWS-specific parameter types to catch invalid values.

When specifying parameters which of the following is not a valid Parameter type?

- ▫️ AWS::EC2::KeyPair::KeyName
- ✅ DependentParameter
- ▫️ CommaDelimitedList
- ▫️ String

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudFormation gives developers and businesses an easy way to create a collection of related AWS and third-party resources and provision them in an orderly and predictable fashion.

How CloudFormation Works:  via - https://aws.amazon.com/cloudformation/

Parameter types enable CloudFormation to validate inputs earlier in the stack creation process.

CloudFormation currently supports the following parameter types:

String – A literal string
Number – An integer or float
List&lt;Number&gt; – An array of integers or floats
CommaDelimitedList – An array of literal strings that are separated by commas
AWS::EC2::KeyPair::KeyName – An Amazon EC2 key pair name
AWS::EC2::SecurityGroup::Id – A security group ID
AWS::EC2::Subnet::Id – A subnet ID
AWS::EC2::VPC::Id – A VPC ID
List&lt;AWS::EC2::VPC::Id&gt; – An array of VPC IDs
List&lt;AWS::EC2::SecurityGroup::Id&gt; – An array of security group IDs
List&lt;AWS::EC2::Subnet::Id&gt; – An array of subnet IDs
DependentParameter

In CloudFormation, parameters are all independent and cannot depend on each other. Therefore, this is an invalid parameter type.

Incorrect options:

String

CommaDelimitedList

AWS::EC2::KeyPair::KeyName

As mentioned in the explanation above, these are valid parameter types.

Reference:

https://aws.amazon.com/blogs/devops/using-the-new-cloudformation-parameter-types/

</details>

---

<a id="t11-q03"></a>
### T11-Q03
*⏭ пропущено · домен: офіційна мітка*

A company has built its technology stack on AWS serverless architecture for managing all its business functions. To expedite development for a new business requirement, the company is looking at using pre-built serverless applications.

Which AWS service represents the easiest solution to address this use-case?

- ▫️ AWS Service Catalog
- ✅ AWS Serverless Application Repository (SAR)
- ▫️ AWS Marketplace
- ▫️ AWS AppSync

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Serverless Application Repository (SAR) - The AWS Serverless Application Repository is a managed repository for serverless applications. It enables teams, organizations, and individual developers to store and share reusable applications, and easily assemble and deploy serverless architectures in powerful new ways. Using the Serverless Application Repository, you don't need to clone, build, package, or publish source code to AWS before deploying it. Instead, you can use pre-built applications from the Serverless Application Repository in your serverless architectures, helping you and your teams reduce duplicated work, ensure organizational best practices, and get to market faster. Integration with AWS Identity and Access Management (IAM) provides resource-level control of each application, enabling you to publicly share applications with everyone or privately share them with specific AWS accounts.

Deploying applications using SAR:  via - https://aws.amazon.com/serverless/serverlessrepo/

Incorrect options:

AWS Marketplace - The AWS Marketplace enables qualified partners to market and sell their software to AWS Customers. AWS Marketplace is an online software store that helps customers find, buy, and immediately start using the software and services that run on AWS. AWS Marketplace is designed for Independent Software Vendors (ISVs), Value-Added Resellers (VARs), and Systems Integrators (SIs) who have software products they want to offer to customers in the cloud.

AWS AppSync - AWS AppSync is a fully managed service that makes it easy to develop GraphQL APIs by handling the heavy lifting of securely connecting to data sources like AWS DynamoDB, Lambda, and more. Organizations choose to build APIs with GraphQL because it helps them develop applications faster, by giving front-end developers the ability to query multiple databases, microservices, and APIs with a single GraphQL endpoint.

AWS Service Catalog - AWS Service Catalog allows organizations to create and manage catalogs of IT services that are approved for use on AWS. These IT services can include everything from virtual machine images, servers, software, and databases to complete multi-tier application architectures. AWS Service Catalog allows you to centrally manage deployed IT services and your applications, resources, and metadata. This helps you achieve consistent governance and meet your compliance requirements while enabling users to quickly deploy only the approved IT services they need.

Reference:

https://aws.amazon.com/serverless/serverlessrepo/

</details>

---
