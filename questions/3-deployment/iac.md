# CloudFormation / SAM / CDK

**Домен 3 — Розгортання** · питань: 33  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#iac) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q01"></a>
### T02-Q01
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A business operates a web app on Amazon EC2 instances utilizing a bespoke Amazon Machine Image (AMI). They employ AWS CloudFormation for deploying their app, which is currently active in the us-east-1 Region. However, their goal is to extend the deployment to the us-west-1 Region.

During an initial attempt to create an AWS CloudFormation stack in us-west-1, the action fails, and an error message indicates that the AMI ID does not exist. A developer is tasked with addressing this error through a method that minimizes operational complexity.

Which action should the developer take?

- ▫️ Use AWS Lambda to create an AMI in the us-west-1 Region during stack creation.
- ▫️ Create a new AMI in the us-west-1 Region and update the CloudFormation template with the new AMI ID.
- ✅ Copy the AMI from the us-east-1 Region to the us-west-1 Region and use the new AMI ID in the CloudFormation template.
- ❌ Modify the CloudFormation template to refer to the AMI in us-east-1 Region.

<details><summary>Пояснення (оригінал, EN)</summary>

This is the best option as it allows the developer to use the same AMI in a different region with minimal effort and maintenance.

CORRECT: "Copy the AMI from the us-east-1 Region to the us-west-1 Region and use the new AMI ID in the CloudFormation template" is the correct answer (as explained above.)

INCORRECT: "Create a new AMI in the us-west-1 Region and update the CloudFormation template with the new AMI ID" is incorrect.

This is incorrect as creating a new AMI would be operationally complex and time-consuming.

INCORRECT: "Modify the CloudFormation template to refer to the AMI in us-east-1 Region" is incorrect.

AMIs are regional resources and cannot be used directly in other regions.

INCORRECT: "Use AWS Lambda to create an AMI in the us-west-1 Region during stack creation" is incorrect.

This process would add unnecessary complexity and the new AMI would not be identical to the original one.

Джерела: <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html>

</details>

---

<a id="t03-q04"></a>
### T03-Q04
*⏭ пропущено · домен: офіційна мітка*

The development team at a company is looking at building an AWS CloudFormation template that self-populates the AWS Region variable while deploying the CloudFormation template.

What is the MOST operationally efficient way to determine the Region in which the template is being deployed?

- ✅ Use the AWS::Region pseudo parameter
- ▫️ Create a CloudFormation parameter for Region and let the desired value be populated at the time of deployment
- ▫️ Create an AWS Lambda-backed custom resource for Region and let the desired value be populated at the time of deployment by the Lambda
- ▫️ Set up a mapping containing the key and the named values for all AWS Regions and then have the CloudFormation template auto-select the desired value

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the AWS::Region pseudo parameter

Pseudo parameters are parameters that are predefined by AWS CloudFormation. You don't declare them in your template. Use them the same way as you would a parameter, as the argument for the Ref function.

You can access pseudo parameters in a CloudFormation template like so:

Outputs:
  MyStacksRegion:
    Value: !Ref "AWS::Region"
The AWS::Region pseudo parameter returns a string representing the Region in which the encompassing resource is being created, such as us-west-2.

Incorrect options:

Create a CloudFormation parameter for Region and let the desired value be populated at the time of deployment - Although it is certainly possible to use a CloudFormation parameter to populate the desired value of the Region at the time of deployment, however, this is not operationally efficient, as you can directly use the AWS::Region pseudo parameter for this.

Set up a mapping containing the key and the named values for all AWS Regions and then have the CloudFormation template auto-select the desired value - The Mappings section matches a key to a corresponding set of named values. For example, if you want to set values based on a Region, you can create a mapping that uses the Region name as a key and contains the values you want to specify for each specific Region. You use the Fn::FindInMap intrinsic function to retrieve values in a map. This option is incorrect as the CloudFormation template cannot auto-select the desired value of the Region from a mapping.

Create an AWS Lambda-backed custom resource for Region and let the desired value be populated at the time of deployment by the Lambda - Custom resources enable you to write custom provisioning logic in templates that AWS CloudFormation runs anytime you create, update (if you changed the custom resource), or delete stacks. When you associate a Lambda function with a custom resource, the function is invoked whenever the custom resource is created, updated, or deleted. This option is a distractor, as Region is not a custom resource that needs to be provisioned.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html>

</details>

---

<a id="t03-q37"></a>
### T03-Q37
*⏭ пропущено · домен: офіційна мітка*

Your company is in the process of building a DevOps culture and is moving all of its on-premise resources to the cloud using serverless architectures and automated deployments. You have created a CloudFormation template in YAML that uses an AWS Lambda function to pull HTML files from GitHub and place them into an Amazon Simple Storage Service (S3) bucket that you specify.

Which of the following AWS CLI commands can you use to upload AWS Lambda functions and AWS CloudFormation templates to AWS?

- ✅ cloudformation package and cloudformation deploy
- ▫️ cloudformation zip and cloudformation upload
- ▫️ cloudformation zip and cloudformation deploy
- ▫️ cloudformation package and cloudformation upload

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

cloudformation package and cloudformation deploy

AWS CloudFormation gives developers and businesses an easy way to create a collection of related AWS and third-party resources and provision them in an orderly and predictable fashion.

How CloudFormation Works:  via - https://aws.amazon.com/cloudformation/

The cloudformation package command packages the local artifacts (local paths) that your AWS CloudFormation template references. The command will upload local artifacts, such as your source code for your AWS Lambda function.

The cloudformation deploy command deploys the specified AWS CloudFormation template by creating and then executing a changeset.

Incorrect options:

cloudformation package and cloudformation upload - The cloudformation upload command does not exist.

cloudformation zip and cloudformation upload - Both commands do not exist, this is a made-up option.

cloudformation zip and cloudformation deploy - The cloudformation zip command does not exist, this is a made-up option.

Reference:

https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html

</details>

---

<a id="t03-q44"></a>
### T03-Q44
*⏭ пропущено · домен: офіційна мітка*

An IT company is using AWS CloudFormation to manage its IT infrastructure. It has created a template to provision a stack with a VPC and a subnet. The output value of this subnet has to be used in another stack.

As a Developer Associate, which of the following options would you suggest to provide this information to another stack?

- ✅ Use 'Export' field in the Output section of the stack's template
- ▫️ Use Fn::ImportValue
- ▫️ Use 'Expose' field in the Output section of the stack's template
- ▫️ Use Fn::Transform

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use 'Export' field in the Output section of the stack's template

To share information between stacks, export a stack's output values. Other stacks that are in the same AWS account and region can import the exported values.

To export a stack's output value, use the Export field in the Output section of the stack's template. To import those values, use the Fn::ImportValue function in the template for the other stacks.

Incorrect options:

Use 'Expose' field in the Output section of the stack's template - 'Expose' is a made-up option, and only given as a distractor.

Use Fn::ImportValue - To import the values exported by another stack, we use the Fn::ImportValue function in the template for the other stacks. This function is not useful for the current scenario.

Use Fn::Transform - The intrinsic function Fn::Transform specifies a macro to perform custom processing on part of a stack template. Macros enable you to perform custom processing on templates, from simple actions like find-and-replace operations to extensive transformations of entire templates. This function is not useful for the current scenario.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-exports.html

</details>

---

<a id="t04-q06"></a>
### T04-Q06
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer is creating a script to automate the deployment process for a serverless application. The Developer wants to use an existing AWS Serverless Application Model (SAM) template for the application.

What should the Developer use for the project? (Select TWO.)

- ▫️ Call aws s3 cp to upload the AWS SAM template to Amazon S3. Call aws lambda update-function-code to create the application
- ▫️ Create a ZIP package and upload it to Amazon S3. Call aws cloudformation create-stack to create the application
- ✅ Call sam package to create the deployment package. Call sam deploy to deploy the package afterward
- ▫️ Create a ZIP package locally and call aws serverlessrepo create-application to create the application
- ✅ Call aws cloudformation package to create the deployment package. Call aws cloudformation deploy to deploy the package afterward

<details><summary>Пояснення (оригінал, EN)</summary>

The AWS Serverless Application Model (SAM) is an open-source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. With just a few lines per resource, you can define the application you want and model it using YAML. During deployment, SAM transforms and expands the SAM syntax into AWS CloudFormation syntax, enabling you to build serverless applications faster.

To get started with building SAM-based applications, use the AWS SAM CLI. SAM CLI provides a Lambda-like execution environment that lets you locally build, test, and debug applications defined by SAM templates. You can also use the SAM CLI to deploy your applications to AWS.

With the SAM CLI you can package and deploy your source code using two simple commands:

• sam package

• sam deploy

Alternatively, you can use:

• aws cloudformation package

• aws cloudformation deploy

Therefore, the Developer can use either the sam or aws cloudformation CLI commands to package and deploy the serverless application.

CORRECT: "Call aws cloudformation package to create the deployment package. Call aws cloudformation deploy to deploy the package afterward" is a correct answer.

CORRECT: "Call sam package to create the deployment package. Call sam deploy to deploy the package afterward" is a correct answer.

INCORRECT: "Call aws s3 cp to upload the AWS SAM template to Amazon S3. Call aws lambda update-function-code to create the application" is incorrect as this is not how to use a SAM template. With SAM the commands mentioned above must be run.

INCORRECT: "Create a ZIP package locally and call aws serverlessrepo create-application to create the application" is incorrect as this is not the correct way to use a SAM template.

INCORRECT: "Create a ZIP package and upload it to Amazon S3. Call aws cloudformation create-stack to create the application" is incorrect as this is not required when deploying a SAM template.

Джерела: <https://aws.amazon.com/serverless/sam/>

</details>

---

<a id="t05-q23"></a>
### T05-Q23
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

The source code for an application is stored in a file named index.js that is in a folder along with a template file that includes the following code:

AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Resources:
LambdaFunctionWithAPI:
Type: AWS::Serverless::Function
Properties:
Handler: index.handler
Runtime: nodejs12.x
What does a Developer need to do to prepare the template so it can be deployed using an AWS CLI command?

- ✅ Run the aws cloudformation package command to upload the source code to an Amazon S3 bucket and produce a modified CloudFormation template
- ▫️ Run the aws lambda zip command to package the source file together with the CloudFormation template and deploy the resulting zip archive
- ▫️ Run the aws serverless create-package command to embed the source file directly into the existing CloudFormation template
- ▫️ Run the aws cloudformation compile command to base64 encode and embed the source file into a modified CloudFormation template

<details><summary>Пояснення (оригінал, EN)</summary>

The template shown is an AWS SAM template for deploying a serverless application. This can be identified by the template header: Transform: 'AWS::Serverless-2016-10-31'

The Developer will need to package and then deploy the template. To do this the source code must be available in the same directory or referenced using the “codeuri” parameter. Then, the Developer can use the “aws cloudformation package” or “sam package” commands to prepare the local artifacts (local paths) that your AWS CloudFormation template references.

The command uploads local artifacts, such as source code for an AWS Lambda function or a Swagger file for an AWS API Gateway REST API, to an S3 bucket. The command returns a copy of your template, replacing references to local artifacts with the S3 location where the command uploaded the artifacts.

Once that is complete the template can be deployed using the “aws cloudformation deploy” or “sam deploy” commands. Therefore, the next step in this scenario is for the Developer to run the “aws cloudformation” package command to upload the source code to an Amazon S3 bucket and produce a modified CloudFormation template. An example of this command is provided below:

aws cloudformation package --template-file /path_to_template/template.json --s3-bucket bucket-name --output-template-file packaged-template.json

CORRECT: "Run the aws cloudformation package command to upload the source code to an Amazon S3 bucket and produce a modified CloudFormation template" is the correct answer.

INCORRECT: "Run the aws cloudformation compile command to base64 encode and embed the source file into a modified CloudFormation template" is incorrect as the Developer should run the “aws cloudformation package” command.

INCORRECT: "Run the aws lambda zip command to package the source file together with the CloudFormation template and deploy the resulting zip archive" is incorrect as the Developer should run the “aws cloudformation package” command which will automatically copy the relevant files to Amazon S3.

INCORRECT: "Run the aws serverless create-package command to embed the source file directly into the existing CloudFormation template" is incorrect as the Developer has the choice to run either “aws cloudformation package” or “sam package”, but not “aws serverless create-package”.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html>

</details>

---

<a id="t05-q33"></a>
### T05-Q33
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

To include objects defined by the AWS Serverless Application Model (SAM) in an AWS CloudFormation template, in addition to Resources, what section MUST be included in the document root?

- ▫️ Conditions
- ▫️ Properties
- ✅ Transform
- ▫️ Globals

<details><summary>Пояснення (оригінал, EN)</summary>

The primary differences between AWS SAM templates and AWS CloudFormation templates are the following:

• Transform declaration. The declaration Transform: AWS::Serverless-2016-10-31 is required for AWS SAM templates. This declaration identifies an AWS CloudFormation template as an AWS SAM template.

• Globals section. The Globals section is unique to AWS SAM. It defines properties that are common to all your serverless functions and APIs. All the AWS::Serverless::Function, AWS::Serverless::Api, and AWS::Serverless::SimpleTable resources inherit the properties that are defined in the Globals section.

• Resources section. In AWS SAM templates the Resources section can contain a combination of AWS CloudFormation resources and AWS SAM resources.

Of these three sections, only the Transform section and Resources sections are required; the Globals section is optional.

CORRECT: "Transform" is the correct answer.

INCORRECT: "Globals" is incorrect as this is not a required section.

INCORRECT: "Conditions" is incorrect as this is an optional section.

INCORRECT: "Properties" is incorrect as this is not a section in a template, it is used within a resource.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html>

</details>

---

<a id="t05-q42"></a>
### T05-Q42
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is creating an AWS Serverless Application Model (AWS SAM) template. It includes several AWS Lambda functions, an Amazon S3 bucket, and an Amazon CloudFront distribution. One Lambda function, running on Lambda@Edge, is integrated with the CloudFront distribution, while the S3 bucket serves as an origin for the distribution.

However, upon deploying the AWS SAM blueprint in the us-west-1 Region, the stack's creation fails.

What could be the possible reason for this failure?

- ▫️ AWS Lambda functions integrated with CloudFront cannot be deployed using AWS SAM templates.
- ✅ Lambda@Edge functions can only be deployed in the us-east-1 Region.
- ▫️ AWS SAM templates are not supported in the us-west-1 Region.
- ▫️ Amazon S3 buckets serving as origins for CloudFront must be created in a separate Region from the CloudFront distribution.

<details><summary>Пояснення (оригінал, EN)</summary>

Lambda@Edge functions can only be created in the us-east-1 Region. If you want to deploy such functions, they must be done in this specific region.

CORRECT: "Lambda@Edge functions can only be deployed in the us-east-1 Region" is the correct answer (as explained above.)

INCORRECT: "AWS SAM templates are not supported in the us-west-1 Region" is incorrect.

This is not a restriction, and hence not a reason for failure.

INCORRECT: "Amazon S3 buckets serving as origins for CloudFront must be created in a separate Region from the CloudFront distribution" is incorrect.

Amazon S3 buckets serving as origins for CloudFront distributions can be in the same region as the distribution. Therefore, this would not cause a failure.

INCORRECT: "AWS Lambda functions integrated with CloudFront cannot be deployed using AWS SAM templates" is incorrect.

AWS Lambda functions can be integrated with Amazon CloudFront and deployed using AWS SAM templates. This statement is incorrect and would not lead to a stack creation failure.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-edge-how-it-works.html>

</details>

---

<a id="t05-q65"></a>
### T05-Q65
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is using AWS SAM to create a template for deploying a serverless application. The Developer plans deploy an AWS Lambda function and an Amazon DynamoDB table using the template.

Which resource types should the Developer specify? (Select TWO.)

- ✅ AWS::Serverless::SimpleTable
- ▫️ AWS::Serverless:API
- ▫️ AWS::Serverless::Application
- ✅ AWS::Serverless:Function
- ▫️ AWS::Serverless:LayerVersion

<details><summary>Пояснення (оригінал, EN)</summary>

A serverless application is a combination of Lambda functions, event sources, and other resources that work together to perform tasks. Note that a serverless application is more than just a Lambda function—it can include additional resources such as APIs, databases, and event source mappings.

AWS SAM templates are an extension of AWS CloudFormation templates, with some additional components that make them easier to work with.

To create a Lambda function using an AWS SAM template the Developer can use the AWS::Serverless::Function resource type. The AWS::Serverless::Function resource type can be used to Create a Lambda function, IAM execution role, and event source mappings that trigger the function.

To create a DynamoDB table using an AWS SAM template the Developer can use the AWS::Serverless::SimpleTable resource type which creates a DynamoDB table with a single attribute primary key. It is useful when data only needs to be accessed via a primary key.

CORRECT: "AWS::Serverless:Function" is a correct answer.

CORRECT: "AWS::Serverless:SimpleTable" is also a correct answer.

INCORRECT: "AWS::Serverless::Application" is incorrect as this embeds a serverless application from the AWS Serverless Application Repository or from an Amazon S3 bucket as a nested application.

INCORRECT: "AWS::Serverless:LayerVersion" is incorrect as this creates a Lambda LayerVersion that contains library or runtime code needed by a Lambda Function.

INCORRECT: "AWS::Serverless:API" is incorrect as this creates a collection of Amazon API Gateway resources and methods that can be invoked through HTTPS endpoints.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html>

</details>

---

<a id="t06-q21"></a>
### T06-Q21
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A developer received the following error message during an AWS CloudFormation deployment:

DELETE_FAILED (The following resource(s) failed to delete: (sg-11223344).)

Which action should the developer take to resolve this error?

- ▫️ Manually delete the security group. Then execute a change set to force deletion of the CloudFormation stack.
- ❌ Add a DependsOn attribute to the sg-11223344 resource in the CloudFormation template. Then delete the stack.
- ▫️ Update the logical ID of the security group resource with the security groups ARN. Then delete the stack.
- ✅ Modify the CloudFormation template to retain the security group resource. Then manually delete the resource after deployment.

<details><summary>Пояснення (оригінал, EN)</summary>

The stack may be stuck in the DELETE_FAILED state because the dependent object (security group), can't be deleted. This can be for many reasons, for example, the security group could have an ENI attached that’s not part of the CloudFormation stack.

To delete the stack you must choose to delete the stack in the console and then select to retain the resource(s) that failed to delete. This can also be achieved from the AWS CLI:

CORRECT: "Modify the CloudFormation template to retain the security group resource. Then manually delete the resource after deployment" is the correct answer (as explained above.)

INCORRECT: "Add a DependsOn attribute to the sg-11223344 resource in the CloudFormation template. Then delete the stack" is incorrect.

This creates a dependency for stack creation. It does not assist with resolving the issue that is preventing the stack from deleting successfully.

INCORRECT: "Manually delete the security group. Then execute a change set to force deletion of the CloudFormation stack" is incorrect.

You can manually delete the security group. However, you would not then use a change set to continue with the deletion. You would instead simply choose to delete the stack from the console or the CLI.

INCORRECT: "Update the logical ID of the security group resource with the security groups ARN. Then delete the stack" is incorrect.

The issue has nothing to do with logical IDs or ARNs. The resource cannot be deleted by CloudFormation so the developer simply needs to choose to retain the resource before continuing with the stack deletion process.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html>

</details>

---

<a id="t06-q50"></a>
### T06-Q50
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

In an organization, AWS CloudFormation templates are used to deploy all Amazon RDS DB instances as part of AWS CodePipeline CI/CD automation. As part of the deployment process, the main password for the DB instance needs to be auto generated.

What approach can be taken to fulfill these prerequisites with the minimum possible development effort?

- ❌ Use AWS KMS to generate a random encryption key, then use this key as the password for the DB instance.
- ✅ Use AWS Secrets Manager via the AWS SDK to generate a secure string. Use a dynamic reference to create the DB instance with the secret value.
- ▫️ Write custom AWS Lambda function code that, when triggered, creates an encrypted string, and uses it as the password for the DB instance.
- ▫️ Utilize AWS::EC2::KeyPair to generate a secure string and assign it as the password for the DB instance.

<details><summary>Пояснення (оригінал, EN)</summary>

This answer is correct because it leverages AWS Secrets Manager, a service designed specifically for secure storage and retrieval of secrets.

It does this without the added complexity and potential security concerns of using a custom Lambda function or CodeBuild action.

By using the secretsmanager dynamic reference in the CloudFormation template, you can retrieve the secret value directly, reducing the risk of exposure.

CORRECT: "Use AWS Secrets Manager via the AWS SDK to generate a secure string. Use a dynamic reference to create the DB instance with the secret value" is the correct answer (as explained above.)

INCORRECT: "Use AWS KMS to generate a random encryption key, then use this key as the password for the DB instance" is incorrect.

This is incorrect because AWS KMS is used for creating and managing cryptographic keys and controlling their use across a wide range of AWS services and in your applications, not for generating random strings.

INCORRECT: "Utilize AWS::EC2::KeyPair to generate a secure string and assign it as the password for the DB instance" is incorrect.

This approach is incorrect as the AWS::EC2::KeyPair is used for SSH login information for EC2 instances, not for generating passwords for RDS DB instances.

INCORRECT: "Write custom AWS Lambda function code that, when triggered, creates an encrypted string, and uses it as the password for the DB instance" is incorrect.

This approach introduces unnecessary complexity and development effort.

Джерела: <https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html>

</details>

---

<a id="t06-q51"></a>
### T06-Q51
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

> ⚠️ **Сумнівний ключ: реальний відкат — git revert + cdk deploy / rollback CloudFormation.** → [детальніше](../../07-outdated-and-wrong-answers.md#d06)

A junior developer has been assigned the project of making updates to a current application using AWS Cloud Development Kit (CDK).

How can the developer revert to a previous version if there is an error?

- ✅ Only make changes in the deployment phase, and never during synthesis.
- ▫️ Once CloudFormation has been installed, it can be used to track changes in application stacks.
- ❌ Once CloudFormation has been installed, it can be used to revert to previous versions.
- ▫️ Once CloudFormation has been added to the application, it can be used to manage the scope.

<details><summary>Пояснення (оригінал, EN)</summary>

Synthesis is the process of converting AWS CDK stacks to AWS CloudFormation templates and assets. Changes should only be during the deployment phase and after AWS CloudFormation template has been created. This will allow it to roll back the change if there are any problems.

CORRECT: "Only make changes in the deployment phase, and never during synthesis" is the correct answer (as explained above.)

INCORRECT: "Once CloudFormation has been installed, it can be used to revert to previous versions" is incorrect. The CDK Toolkit already provides the ability to convert CDK stacks into CloudFormation templates. There is no need to install CloudFormation.

INCORRECT: "Once CloudFormation has been added to the application, it can be used to manage the scope" is incorrect. The CDK Toolkit already provides the ability to convert CDK stacks into CloudFormation templates. It is not used to manage the scope.

INCORRECT: "Once synthesis is complete, it can be used to track drift changes" is incorrect. CloudFormation can detect drift changes if changes are made outside of CloudFormation management. Synthesis is the process of converting AWS CDK stacks to AWS CloudFormation templates.

Джерела: <https://docs.aws.amazon.com/cdk/v2/guide/best-practices.html>

</details>

---

<a id="t06-q52"></a>
### T06-Q52
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer is working on a serverless application using AWS Cloud Development Kit (AWS CDK) and intends to set up several AWS Lambda functions and Amazon API Gateway APIs during the creation of an AWS CloudFormation stack. The developer's local machine is equipped with AWS Serverless Application Model (AWS SAM) and the AWS CDK.

What is the best way for the developer to locally test a particular Lambda function?

- ▫️ Directly execute the Lambda function on the local machine.
- ✅ Use the sam local invoke command specifying the specific Lambda function.
- ▫️ Test the Lambda function through the AWS Management Console.
- ▫️ Use the AWS CDK command line to locally run the Lambda function.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS SAM CLI is a developer tool that allows you to locally test and debug your AWS Lambda functions defined by AWS Serverless Application Model (SAM) templates. Using the sam local invoke command with the specific Lambda function as an argument, you can execute the function locally.

CORRECT: "Use the sam local invoke command specifying the specific Lambda function" is the correct answer (as explained above.)

INCORRECT: "Test the Lambda function through the AWS Management Console" is incorrect.

The AWS Management Console allows you to create and manage AWS Lambda functions but doesn't provide a feature to test functions locally.

INCORRECT: "Use the AWS CDK command line to locally run the Lambda function" is incorrect.

AWS CDK is a software development framework to define cloud infrastructure in code and provision it through AWS CloudFormation, but it doesn't allow local execution of Lambda functions.

INCORRECT: "Directly execute the Lambda function on the local machine" is incorrect.

AWS Lambda functions cannot be executed directly on a local machine without a local testing tool like AWS SAM CLI.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-using-invoke.html>

</details>

---

<a id="t06-q63"></a>
### T06-Q63
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

An engineer is tasked with creating an AWS CloudFormation template that is capable of automatically determining the AWS Region where it is being deployed.

What is the MOST efficient method to ascertain the Region during deployment of the template?

- ▫️ Extract the Region from the AWS::StackId pseudo parameter by applying the Fn::Split intrinsic function.
- ▫️ Dynamically fetch the Region by referencing the corresponding entry in AWS Systems Manager Parameter Store.
- ❌ Request the Region as an input parameter during CloudFormation deployment.
- ✅ Utilize the AWS::Region pseudo parameter.

<details><summary>Пояснення (оригінал, EN)</summary>

In AWS CloudFormation, the AWS::Region pseudo parameter automatically resolves to the Region where the stack is being created or updated. This makes it the most operationally efficient way to determine the Region in which the template is being deployed, as it doesn't require any additional input, processing, or external service calls.

CORRECT: "Utilize the AWS::Region pseudo parameter" is the correct answer (as explained above.)

INCORRECT: "Request the Region as an input parameter during CloudFormation deployment" is incorrect.

This method is not operationally efficient as it relies on manual input from the user each time the template is deployed. It also increases the risk of human error or inconsistencies, especially in multi-region deployments.

INCORRECT: "Extract the Region from the AWS::StackId pseudo parameter by applying the Fn::Split intrinsic function" is incorrect.

Although this method technically works because the stack ID contains the region, it's not the most efficient approach. This requires additional processing to extract the region from the stack ID, which makes it less efficient than directly using the AWS::Region pseudo parameter.

INCORRECT: "Dynamically fetch the Region by referencing the corresponding entry in AWS Systems Manager Parameter Store" is incorrect.

This method requires an external service and assumes that the region has been correctly stored as a parameter. This adds additional complexity and dependency on another service, making it less efficient than using the built-in AWS::Region pseudo parameter.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html>

</details>

---

<a id="t07-q41"></a>
### T07-Q41
*⏭ пропущено · домен: офіційна мітка*

AWS CloudFormation helps model and provision all the cloud infrastructure resources needed for your business.

Which of the following services rely on CloudFormation to provision resources (Select two)?

- ▫️ AWS Lambda
- ✅ AWS Serverless Application Model (AWS SAM)
- ▫️ AWS CodeBuild
- ▫️ AWS Autoscaling
- ✅ AWS Elastic Beanstalk

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Elastic Beanstalk - AWS Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services developed with Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker on familiar servers such as Apache, Nginx, Passenger, and IIS. Elastic Beanstalk uses AWS CloudFormation to launch the resources in your environment and propagate configuration changes.

AWS Serverless Application Model (AWS SAM) - You use the AWS SAM specification to define your serverless application. AWS SAM templates are an extension of AWS CloudFormation templates, with some additional components that make them easier to work with. AWS SAM needs CloudFormation templates as a basis for its configuration.

Incorrect options:

AWS Lambda - AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume. Hence, Lamda does not need CloudFormation to run its services.

AWS Autoscaling - AWS Auto Scaling monitors your applications and automatically adjusts the capacity to maintain steady, predictable performance at the lowest possible cost. Using AWS Auto Scaling, it’s easy to setup application scaling for multiple resources across multiple services in minutes. Auto Scaling used CloudFormation but is not a mandatory requirement.

AWS CodeBuild - AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy. With CodeBuild, you don’t need to provision, manage, and scale your own build servers. AWS CodePipeline uses AWS CloudFormation as a deployment action but is not a mandatory service.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification.html> · <https://aws.amazon.com/elasticbeanstalk/>

</details>

---

<a id="t07-q50"></a>
### T07-Q50
*⏭ пропущено · домен: офіційна мітка*

A developer is designing an AWS CloudFormation template for deploying Amazon EC2 instances in numerous AWS accounts. The developer needs to select EC2 instances from a list of pre-approved instance types.

What measures could the developer take to integrate the list of authorized instance types into the CloudFormation template?

- ▫️ Configure separate parameters for each EC2 instance type in the CloudFormation template
- ▫️ Configure a pseudo parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template
- ▫️ Configure a mapping having a list of EC2 instance types as parameters in the CloudFormation template
- ✅ Configure a parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure a parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template

You can use the Parameters section to customize your templates. Parameters enable you to input custom values to your template each time you create or update a stack.

 via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html

AllowedValues refers to an array containing the list of values allowed for the parameter. When applied to a parameter of type String, the parameter value must be one of the allowed values. When applied to a parameter of type CommaDelimitedList, each value in the list must be one of the specified allowed values.

Incorrect options:

Configure separate parameters for each EC2 instance type in the CloudFormation template - Creating separate parameters for each instance type is semantically incorrect as the underlying value will point to the same resource but have multiple inputs.

Configure a mapping having a list of EC2 instance types as parameters in the CloudFormation template - The Mappings section matches a key to a corresponding set of named values. For example, if you want to set values based on a region, you can create a mapping that uses the region name as a key and contains the values you want to specify for each specific region. You use the Fn::FindInMap intrinsic function to retrieve values in a map. A mapping is not a list, rather, it consists of key value pairs. You can't include parameters, pseudo parameters, or intrinsic functions in the Mappings section. So, this option is incorrect.

Configure a pseudo parameter with the list of EC2 instance types as AllowedValues in the CloudFormation template - Pseudo parameters are parameters that are predefined by AWS CloudFormation. You don't declare them in your template. So, this option is incorrect.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html>

</details>

---

<a id="t08-q12"></a>
### T08-Q12
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer has created the code for a Lambda function saved the code in a file named lambda_function.py. He has also created a template that named template.yaml. The following code is included in the template file:

AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Resources:
microservicehttpendpointpython3:
Type: 'AWS::Serverless::Function'
Properties:
Handler: lambda_function.lambda_handler
CodeUri: .
What commands can the Developer use to prepare and then deploy this template? (Select TWO.)

- ▫️ Run aws serverless package and then aws serverless deploy
- ▫️ Run aws cloudformation compile and then aws cloudformation deploy
- ▫️ Run sam build and then sam package
- ✅ Run sam package and then sam deploy
- ✅ Run aws cloudformation package and then aws cloudformation deploy

<details><summary>Пояснення (оригінал, EN)</summary>

The template shown is an AWS SAM template for deploying a serverless application. This can be identified by the template header: Transform: 'AWS::Serverless-2016-10-31'

The Developer will need to package and then deploy the template. To do this the source code must be available in the same directory or referenced using the “codeuri” parameter. Then, the Developer can use the “aws cloudformation package” or “sam package” commands to prepare the local artifacts (local paths) that your AWS CloudFormation template references.

The command uploads local artifacts, such as source code for an AWS Lambda function or a Swagger file for an AWS API Gateway REST API, to an S3 bucket. The command returns a copy of your template, replacing references to local artifacts with the S3 location where the command uploaded the artifacts.

Once that is complete the template can be deployed using the “aws cloudformation deploy” or “sam deploy” commands. Therefore, the developer has two options to prepare and then deploy this package:

1. Run aws cloudformation package and then aws cloudformation deploy

2. Run sam package and then sam deploy

CORRECT: "Run aws cloudformation package and then aws cloudformation deploy" is a correct answer.

INCORRECT: "Run sam package and then sam deploy" is also a correct answer.

INCORRECT: "Run aws cloudformation compile and then aws cloudformation deploy" is incorrect as the “compile” command should be replaced with the “package” command.

INCORRECT: "Run sam build and then sam package" is incorrect as the Developer needs to run the “package” command first and then the “deploy” command to actually deploy the function.

INCORRECT: "Run aws serverless package and then aws serverless deploy" is incorrect as there is no AWS CLI command named “serverless”.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html>

</details>

---

<a id="t08-q30"></a>
### T08-Q30
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is using AWS SAM to create a template for deploying a serverless application. The Developer plans deploy a Lambda function using the template.

Which resource type should the Developer specify?

- ▫️ AWS::Serverless:API
- ✅ AWS::Serverless:Function
- ▫️ AWS::Serverless:LayerVersion
- ▫️ AWS::Serverless::Application

<details><summary>Пояснення (оригінал, EN)</summary>

A serverless application is a combination of Lambda functions, event sources, and other resources that work together to perform tasks. Note that a serverless application is more than just a Lambda function—it can include additional resources such as APIs, databases, and event source mappings.

AWS SAM templates are an extension of AWS CloudFormation templates, with some additional components that make them easier to work with. To create a Lambda function using an AWS SAM template the Developer can use the AWS::Serverless::Function resource type.

The AWS::Serverless::Function resource type can be used to Create a Lambda function, IAM execution role, and event source mappings that trigger the function.

CORRECT: "AWS::Serverless:Function" is the correct answer.

INCORRECT: "AWS::Serverless::Application" is incorrect as this embeds a serverless application from the AWS Serverless Application Repository or from an Amazon S3 bucket as a nested application.

INCORRECT: "AWS::Serverless:LayerVersion" is incorrect as this creates a Lambda LayerVersion that contains library or runtime code needed by a Lambda Function.

INCORRECT: "AWS::Serverless:API" is incorrect as this creates a collection of Amazon API Gateway resources and methods that can be invoked through HTTPS endpoints.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html>

</details>

---

<a id="t08-q51"></a>
### T08-Q51
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer must deploy a new AWS Lambda function using an AWS CloudFormation template.

Which procedures will deploy a Lambda function? (Select TWO.)

- ▫️ Upload a ZIP file to AWS CloudFormation containing the function code, then add a reference to it in an AWS::Lambda::Function resource in the template
- ▫️ 1. Upload the function code to a private Git repository, then add a reference to it in an AWS::Lambda::Function resource in the template
- ▫️ Upload the code to an AWS CodeCommit repository, then add a reference to it in an AWS::Lambda::Function resource in the template
- ✅ Upload a ZIP file containing the function code to Amazon S3, then add a reference to it in an AWS::Lambda::Function resource in the template
- ✅ Create an AWS::Lambda::Function resource in the template, then write the code directly inside the CloudFormation template

<details><summary>Пояснення (оригінал, EN)</summary>

Of the options presented there are two workable procedures for deploying the Lambda function.

Firstly, you can create an AWS::Lambda::Function resource in the template, then write the code directly inside the CloudFormation template. This is possible for simple functions using Node.js or Python which allow you to declare the code inline in the CloudFormation template. For example:

The other option is to upload a ZIP file containing the function code to Amazon S3, then add a reference to it in an AWS::Lambda::Function resource in the template. To declare this in your AWS CloudFormation template, you can use the following syntax (within AWS::Lambda::Function Code):

CORRECT: "Create an AWS::Lambda::Function resource in the template, then write the code directly inside the CloudFormation template" is a correct answer.

CORRECT: "Upload a ZIP file containing the function code to Amazon S3, then add a reference to it in an AWS::Lambda::Function resource in the template" is also a correct answer.

INCORRECT: "Upload the code to an AWS CodeCommit repository, then add a reference to it in an AWS::Lambda::Function resource in the template" is incorrect as you cannot add a reference to code in a CodeCommit repository.

INCORRECT: "Upload a ZIP file to AWS CloudFormation containing the function code, then add a reference to it in an AWS::Lambda::Function resource in the template" is incorrect as you cannot reference a zip file in CloudFormation.

INCORRECT: "Upload the function code to a private Git repository, then add a reference to it in an AWS::Lambda::Function resource in the template" is incorrect as you cannot reference the function code in a private Git repository.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-lambda-function-code.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-function.html>

</details>

---

<a id="t09-q11"></a>
### T09-Q11
*⏭ пропущено · домен: офіційна мітка*

Other than the Resources section, which of the following sections in a Serverless Application Model (SAM) Template is mandatory?

- ▫️ Parameters
- ✅ Transform
- ▫️ Globals
- ▫️ Mappings

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Transform

The AWS Serverless Application Model (AWS SAM) is an open-source framework that you can use to build serverless applications on AWS.

A serverless application is a combination of Lambda functions, event sources, and other resources that work together to perform tasks. Note that a serverless application is more than just a Lambda function—it can include additional resources such as APIs, databases, and event source mappings.

Serverless Application Model (SAM) Templates include several major sections. Transform and Resources are the only required sections.

Please review this note for more details:  via - https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html

Incorrect options:

Parameters

Mappings

Globals

These three options contradict the details provided in the explanation above, so these options are not correct.

Reference:

https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html

</details>

---

<a id="t10-q15"></a>
### T10-Q15
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is looking for a way to use shorthand syntax to express functions, APIs, databases, and event source mappings. The Developer will test using AWS SAM to create a simple Lambda function using Nodejs.12x.

What is the SIMPLEST way for the Developer to get started with a Hello World Lambda function?

- ▫️ Use AWS CloudFormation to deploy a Hello World stack using AWS SAM
- ▫️ Install the AWS CLI, run aws sam init and use one of the AWS Quick Start Templates
- ✅ Install the AWS SAM CLI, run sam init and use one of the AWS Quick Start Templates
- ▫️ Use the AWS Management Console to access AWS SAM and deploy a Hello World function

<details><summary>Пояснення (оригінал, EN)</summary>

The sam init command initializes a serverless application with an AWS SAM template. The template provides a folder structure for your Lambda functions and is connected to an event source such as APIs, S3 buckets, or DynamoDB tables. This application includes everything you need to get started and to eventually extend it into a production-scale application.

This is the simplest way for the Developer to quickly get started with testing AWS SAM. Before the Developer can use the “sam” commands it is necessary to install the AWS SAM CLI. This is separate to the AWS CLI.

CORRECT: "Install the AWS SAM CLI, run sam init and use one of the AWS Quick Start Templates" is the correct answer.

INCORRECT: "Install the AWS CLI, run aws sam init and use one of the AWS Quick Start Templates" is incorrect as “sam init” is not an AWS CLI command, therefore you cannot put “aws” in front of “sam”.

INCORRECT: "Use the AWS Management Console to access AWS SAM and deploy a Hello World function" is incorrect as you cannot access AWS SAM through the console. You can, however, access the Serverless Application Repository through the console and deploy SAM templates.

INCORRECT: "Use AWS CloudFormation to deploy a Hello World stack using AWS SAM" is incorrect as though AWS SAM does use CloudFormation you cannot deploy SAM templates through the AWS CloudFormation console. You must use the SAM CLI or deploy using the Serverless Application Repository.

https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html

</details>

---

<a id="t10-q32"></a>
### T10-Q32
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to setup a new serverless application that includes AWS Lambda and Amazon API Gateway as part of a single stack. The Developer needs to be able to locally build and test the serverless applications before deployment on AWS.

Which service should the Developer use?

- ▫️ AWS Elastic Beanstalk
- ✅ AWS Serverless Application Model (SAM)
- ▫️ AWS CodeBuild
- ▫️ AWS CloudFormation

<details><summary>Пояснення (оригінал, EN)</summary>

The AWS Serverless Application Model (AWS SAM) is an open-source framework that you can use to build serverless applications on AWS. A serverless application is a combination of Lambda functions, event sources, and other resources that work together to perform tasks.

AWS SAM provides you with a simple and clean syntax to describe the functions, APIs, permissions, configurations, and events that make up a serverless application.

The example AWS SAM template file below creates an AWS Lambda function and a simple Amazon API Gateway API with a Get method and a /greeting resource:

The AWS SAM CLI lets you locally build, test, and debug serverless applications that are defined by AWS SAM templates. The CLI provides a Lambda-like execution environment locally. It helps you catch issues upfront by providing parity with the actual Lambda execution environment.

CORRECT: "AWS Serverless Application Model (SAM)" is the correct answer.

INCORRECT: "AWS CloudFormation" is incorrect as you cannot perform local build and test with AWS CloudFormation.

INCORRECT: "AWS Elastic Beanstalk" is incorrect as you cannot deploy serverless applications or perform local build and test with Elastic Beanstalk.

INCORRECT: "AWS CodeBuild" is incorrect as you cannot perform local build and test with AWS CodeBuild.

Джерела: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html>

</details>

---

<a id="t10-q49"></a>
### T10-Q49
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

How can a Developer view a summary of proposed changes to an AWS CloudFormation stack without implementing the changes in production?

- ▫️ Use a direct update
- ▫️ Create a StackSet
- ✅ Create a Change Set
- ▫️ Use drift detection

<details><summary>Пояснення (оригінал, EN)</summary>

When you need to update a stack, understanding how your changes will affect running resources before you implement them can help you update stacks with confidence. Change sets allow you to preview how proposed changes to a stack might impact your running resources.

AWS CloudFormation makes the changes to your stack only when you decide to execute the change set, allowing you to decide whether to proceed with your proposed changes or explore other changes by creating another change set. You can create and manage change sets using the AWS CloudFormation console, AWS CLI, or AWS CloudFormation API.

CORRECT: "Create a Change Set" is the correct answer.

INCORRECT: "Create a StackSet" is incorrect as StackSets are used to create, update, or delete stacks across multiple accounts and regions with a single operation.

INCORRECT: "Use drift detection" is incorrect as this is used to detect when a configuration deviates from the template configuration.

INCORRECT: "Use a direct update" is incorrect as this will directly update the production resources.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html>

</details>

---

<a id="t11-q18"></a>
### T11-Q18
*⏭ пропущено · домен: офіційна мітка*

You are a developer in a manufacturing company that has several servers on-site. The company decides to move new development to the cloud using serverless technology. You decide to use the AWS Serverless Application Model (AWS SAM) and work with an AWS SAM template file to represent your serverless architecture.

Which of the following is NOT a valid serverless resource type?

- ▫️ AWS::Serverless::SimpleTable
- ▫️ AWS::Serverless::Api
- ▫️ AWS::Serverless::Function
- ✅ AWS::Serverless::UserPool

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS::Serverless::UserPool

The AWS Serverless Application Model (SAM) is an open-source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. With just a few lines per resource, you can define the application you want and model it using YAML.

SAM supports the following resource types:

AWS::Serverless::Api

AWS::Serverless::Application

AWS::Serverless::Function

AWS::Serverless::HttpApi

AWS::Serverless::LayerVersion

AWS::Serverless::SimpleTable

AWS::Serverless::StateMachine

UserPool applies to the Cognito service which is used for authentication for mobile app and web. There is no resource named UserPool in the Serverless Application Model.

Incorrect options:

AWS::Serverless::Function - This resource creates a Lambda function, IAM execution role, and event source mappings that trigger the function.

AWS::Serverless::Api - This creates a collection of Amazon API Gateway resources and methods that can be invoked through HTTPS endpoints. It is useful for advanced use cases where you want full control and flexibility when you configure your APIs.

AWS::Serverless::SimpleTable - This creates a DynamoDB table with a single attribute primary key. It is useful when data only needs to be accessed via a primary key.

Reference:

https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-resources-and-properties.html

</details>

---

<a id="t11-q19"></a>
### T11-Q19
*⏭ пропущено · домен: офіційна мітка*

A Developer at a company is working on a CloudFormation template to set up resources. Resources will be defined using code and provisioned based on certain conditions defined in the Conditions section.

Which section of a CloudFormation template cannot be associated with Condition?

- ▫️ Resources
- ✅ Parameters
- ▫️ Conditions
- ▫️ Outputs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Parameters

Parameters enable you to input custom values to your CloudFormation template each time you create or update a stack. Please see this note to understand how to define a parameter in a template:  via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html

The optional Conditions section contains statements that define the circumstances under which entities are created or configured. For example, you can create a condition and then associate it with a resource or output so that AWS CloudFormation only creates the resource or output if the condition is true.

You might use conditions when you want to reuse a template that can create resources in different contexts, such as a test environment versus a production environment. In your template, you can add an EnvironmentType input parameter, which accepts either prod or test as inputs. For the production environment, you might include Amazon EC2 instances with certain capabilities; however, for the test environment, you want to use reduced capabilities to save money.

Conditions cannot be used within the Parameters section. After you define all your conditions, you can associate them with resources and resource properties only in the Resources and Outputs sections of a template.

Please review this note for more details:  via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/conditions-section-structure.html

Please visit https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html for more information on the parameter structure.

Incorrect options:

Resources - Resources section describes the resources that you want to provision in your AWS CloudFormation stacks. You can associate conditions with the resources that you want to conditionally create.

Conditions - You actually define conditions in this section of the CloudFormation template

Outputs - The optional Outputs section declares output values that you can import into other stacks (to create cross-stack references), return in response (to describe stack calls), or view on the AWS CloudFormation console. For example, you can output the S3 bucket name for a stack to make the bucket easier to find. You can associate conditions with the outputs that you want to conditionally create.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/conditions-section-structure.html>

</details>

---

<a id="t11-q21"></a>
### T11-Q21
*⏭ пропущено · домен: офіційна мітка*

Your global organization has an IT infrastructure that is deployed using CloudFormation on AWS Cloud. One employee, in us-east-1 Region, has created a stack 'Application1' and made an exported output with the name 'ELBDNSName'. Another employee has created a stack for a different application 'Application2' in us-east-2 Region and also exported an output with the name 'ELBDNSName'. The first employee wanted to deploy the CloudFormation stack 'Application1' in us-east-2, but it got an error. What is the cause of the error?

- ▫️ Output Values in CloudFormation must have unique names within a single Region
- ▫️ Exported Output Values in CloudFormation must have unique names across all Regions
- ▫️ Output Values in CloudFormation must have unique names across all Regions
- ✅ Exported Output Values in CloudFormation must have unique names within a single Region

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"Exported Output Values in CloudFormation must have unique names within a single Region"

Using CloudFormation, you can create a template that describes all the AWS resources that you want (like Amazon EC2 instances or Amazon RDS DB instances), and AWS CloudFormation takes care of provisioning and configuring those resources for you.

A CloudFormation template has an optional Outputs section which declares output values that you can import into other stacks (to create cross-stack references), return in response (to describe stack calls), or view on the AWS CloudFormation console. For example, you can output the S3 bucket name for a stack to make the bucket easier to find.

You can use the Export Output Values to export the name of the resource output for a cross-stack reference. For each AWS account, export names must be unique within a region. In this case, we would have a conflict within us-east-2.

 via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html

Incorrect options:

"Output Values in CloudFormation must have unique names across all Regions"

"Exported Output Values in CloudFormation must have unique names across all Regions"

"Output Values in CloudFormation must have unique names within a single Region"

These three options contradict the explanation provided earlier, hence these options are incorrect.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html

</details>

---

<a id="t11-q22"></a>
### T11-Q22
*⏭ пропущено · домен: офіційна мітка*

A global e-commerce company wants to perform geographic load testing of its order processing API. The company must deploy resources to multiple AWS Regions to support the load testing of the API.

How can the company address these requirements without additional application code?

- ▫️ Set up an AWS Cloud Development Kit (CDK) ToolKit that defines the load test resources. Leverage the CDK CLI to create a stack from the template in each Region
- ▫️ Set up an AWS Organizations template that defines the load test resources across the organization. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions
- ✅ Set up an AWS CloudFormation template that defines the load test resources. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions
- ▫️ Set up an AWS CloudFormation template that defines the load test resources. Develop region-specific Lambda functions to create a stack from the AWS CloudFormation template in each Region when the respective function is invoked

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up an AWS CloudFormation template that defines the load test resources. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions

AWS CloudFormation StackSets extends the capability of stacks by enabling you to create, update, or delete stacks across multiple accounts and AWS Regions with a single operation. Using an administrator account, you define and manage an AWS CloudFormation template, and use the template as the basis for provisioning stacks into selected target accounts across specified AWS Regions.

 via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/what-is-cfnstacksets.html

A stack set lets you create stacks in AWS accounts across regions by using a single CloudFormation template. A stack set's CloudFormation template defines all the resources in each stack. As you create the stack set, specify the template to use, in addition to any parameters and capabilities that the template requires.

After you've defined a stack set, you can create, update, or delete stacks in the target accounts and AWS Regions you specify.

 via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html

Incorrect options:

Set up an AWS CloudFormation template that defines the load test resources. Develop region-specific Lambda functions to create a stack from the AWS CloudFormation template in each Region when the respective function is invoked - If you do not use a stack set, then you need to define the CloudFormation templates in each region as well as develop lambda functions in each region to create a stack from the corresponding CloudFormation template. This is unnecessary bloat that can be avoided by simply using the CloudFormation StackSets.

Set up an AWS Cloud Development Kit (CDK) ToolKit that defines the load test resources. Leverage the CDK CLI to create a stack from the template in each Region - AWS CDK is a framework for defining cloud infrastructure in code and provisioning it through AWS CloudFormation. The CDK Toolkit again poses regional limitations and is not the right fit for the given use case.

Set up an AWS Organizations template that defines the load test resources across the organization. Leverage the AWS CLI create-stack-set command to create a stack set in the desired Regions - This option acts as a distractor. AWS Organizations cannot be used to create templates for provisioning AWS infrastructure. AWS Organizations is an account management service that enables you to consolidate multiple AWS accounts into an organization that you create and centrally manage.

Джерела: <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/what-is-cfnstacksets.html>

</details>

---

<a id="t11-q36"></a>
### T11-Q36
*⏭ пропущено · домен: офіційна мітка*

A development team wants to build an application using serverless architecture. The team plans to use AWS Lambda functions extensively to achieve this goal. The developers of the team work on different programming languages like Python, .NET and Javascript. The team wants to model the cloud infrastructure using any of these programming languages.

Which AWS service/tool should the team use for the given use-case?

- ▫️ AWS Serverless Application Model (SAM)
- ▫️ AWS CodeDeploy
- ✅ AWS Cloud Development Kit (CDK)
- ▫️ AWS CloudFormation

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Cloud Development Kit (CDK) - The AWS Cloud Development Kit (AWS CDK) is an open-source software development framework to define your cloud application resources using familiar programming languages.

Provisioning cloud applications can be a challenging process that requires you to perform manual actions, write custom scripts, maintain templates, or learn domain-specific languages. AWS CDK uses the familiarity and expressive power of programming languages such as JavaScript/TypeScript, Python, Java, and .NET for modeling your applications. It provides you with high-level components called constructs that preconfigure cloud resources with proven defaults, so you can build cloud applications without needing to be an expert. AWS CDK provisions your resources in a safe, repeatable manner through AWS CloudFormation. It also enables you to compose and share your own custom constructs that incorporate your organization's requirements, helping you start new projects faster.

Incorrect options:

AWS CloudFormation - When AWS CDK applications are run, they compile down to fully formed CloudFormation JSON/YAML templates that are then submitted to the CloudFormation service for provisioning. Because the AWS CDK leverages CloudFormation, you still enjoy all the benefits CloudFormation provides such as safe deployment, automatic rollback, and drift detection. But, CloudFormation by itself is not sufficient for the current use case.

AWS Serverless Application Model (SAM) - The AWS Serverless Application Repository is a managed repository for serverless applications. It enables teams, organizations, and individual developers to store and share reusable applications, and easily assemble and deploy serverless architectures in powerful new ways. Using the Serverless Application Repository, you don't need to clone, build, package, or publish source code to AWS before deploying it.

AWS Serverless Application Model and AWS CDK both abstract AWS infrastructure as code making it easier for you to define your cloud infrastructure. If you prefer defining your serverless infrastructure in concise declarative templates, SAM is the better fit. If you want to define your AWS infrastructure in a familiar programming language, as is the requirement in the current use case, AWS CDK is the right fit.

AWS CodeDeploy - AWS CodeDeploy is a fully managed deployment service that automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. AWS CodeDeploy makes it easier for you to rapidly release new features, helps you avoid downtime during application deployment, and handles the complexity of updating your applications. CodeDeploy can be used with AWS CDK for deployments.

Reference:

https://aws.amazon.com/cdk/faqs/

</details>

---

<a id="t11-q49"></a>
### T11-Q49
*⏭ пропущено · домен: офіційна мітка*

The Technical Lead of your team has reviewed a CloudFormation YAML template written by a new recruit and specified that an invalid section has been added to the template.

Which of the following represents an invalid section of the CloudFormation template?

- ▫️ 'Parameters' section of the template
- ✅ 'Dependencies' section of the template
- ▫️ 'Conditions' section of the template
- ▫️ 'Resources' section of the template

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Templates include several major sections. The Resources section is the only required section.

Sample CloudFormation YAML template:  via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

'Dependencies' section of the template - As you can see, there is no section called 'Dependencies' in the template. Although dependencies can be mentioned, there is no section itself for dependencies.

Incorrect options:

'Conditions' section of the template - This optional section includes conditions that control whether certain resources are created or whether certain resource properties are assigned a value during stack creation or update. For example, you could conditionally create a resource that depends on whether the stack is for a production or test environment.

'Resources' section of the template - This is the only required section and specifies the stack resources and their properties, such as an Amazon Elastic Compute Cloud instance or an Amazon Simple Storage Service bucket. You can refer to resources in the Resources and Outputs sections of the template.

'Parameters' section of the template - This optional section is helpful in passing Values to your template at runtime (when you create or update a stack). You can refer to parameters from the Resources and Outputs sections of the template.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

</details>

---

<a id="t11-q58"></a>
### T11-Q58
*⏭ пропущено · домен: офіційна мітка*

Your company has configured AWS Organizations to manage multiple AWS accounts. Within each AWS account, there are many CloudFormation scripts running. Your manager has requested that each script output the account number of the account the script was executed in.

Which Pseudo parameter will you use to get this information?

- ▫️ AWS::StackName
- ▫️ AWS::NoValue
- ✅ AWS::AccountId
- ▫️ AWS::Region

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS::AccountId

Using CloudFormation, you can create a template that describes all the AWS resources that you want (like Amazon EC2 instances or Amazon RDS DB instances), and AWS CloudFormation takes care of provisioning and configuring those resources for you.

Pseudo parameters are parameters that are predefined by AWS CloudFormation. You do not declare them in your template. Use them the same way as you would a parameter, as the argument for the Ref function.

AWS::AccountId returns the AWS account ID of the account in which the stack is being created.

Incorrect options:

AWS::NoValue - This removes the corresponding resource property when specified as a return value in the Fn::If intrinsic function.

AWS::Region - Returns a string representing the AWS Region in which the encompassing resource is being created, such as us-west-2.

AWS::StackName - Returns the name of the stack as specified with the aws cloudformation create-stack command, such as "teststack".

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html

</details>

---

<a id="t11-q61"></a>
### T11-Q61
*⏭ пропущено · домен: офіційна мітка*

As a developer, you are working on creating an application using AWS Cloud Development Kit (CDK).

Which of the following represents the correct order of steps to be followed for creating an app using AWS CDK?

- ✅ Create the app from a template provided by AWS CDK -&gt; Add code to the app to create resources within stacks -&gt; Build the app (optional) -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account
- ▫️ Create the app from a template provided by AWS CDK -&gt; Add code to the app to create resources within stacks -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account -&gt; Build the app
- ▫️ Create the app from a template provided by AWS CloudFormation -&gt; Add code to the app to create resources within stacks -&gt; Build the app (optional) -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account
- ▫️ Create the app from a template provided by AWS CloudFormation -&gt; Add code to the app to create resources within stacks -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account -&gt; Build the app

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create the app from a template provided by AWS CDK -&gt; Add code to the app to create resources within stacks -&gt; Build the app (optional) -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account

The standard AWS CDK development workflow is similar to the workflow you're already familiar as a developer. There are a few extra steps:

Create the app from a template provided by AWS CDK - Each AWS CDK app should be in its own directory, with its own local module dependencies. Create a new directory for your app. Now initialize the app using the cdk init command, specifying the desired template ("app") and programming language. The cdk init command creates a number of files and folders inside the created home directory to help you organize the source code for your AWS CDK app.

Add code to the app to create resources within stacks - Add custom code as is needed for your application.

Build the app (optional) - In most programming environments, after making changes to your code, you'd build (compile) it. This isn't strictly necessary with the AWS CDK—the Toolkit does it for you so you can't forget. But you can still build manually whenever you want to catch syntax and type errors.

Synthesize one or more stacks in the app to create an AWS CloudFormation template - Synthesize one or more stacks in the app to create an AWS CloudFormation template. The synthesis step catches logical errors in defining your AWS resources. If your app contains more than one stack, you'd need to specify which stack(s) to synthesize.

Deploy one or more stacks to your AWS account - It is optional (though good practice) to synthesize before deploying. The AWS CDK synthesizes your stack before each deployment. If your code has security implications, you'll see a summary of these and need to confirm them before deployment proceeds. cdk deploy is used to deploy the stack using CloudFormation templates. This command displays progress information as your stack is deployed. When it's done, the command prompt reappears.

Incorrect options:

Create the app from a template provided by AWS CloudFormation -&gt; Add code to the app to create resources within stacks -&gt; Build the app (optional) -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account

Create the app from a template provided by AWS CloudFormation -&gt; Add code to the app to create resources within stacks -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account -&gt; Build the app

For both these options, you cannot use AWS CloudFormation to create the app. So these options are incorrect.

Create the app from a template provided by AWS CDK -&gt; Add code to the app to create resources within stacks -&gt; Synthesize one or more stacks in the app -&gt; Deploy stack(s) to your AWS account -&gt; Build the app - You cannot have the build step after deployment. So this option is incorrect.

Reference:

https://docs.aws.amazon.com/cdk/latest/guide/hello_world.html

</details>

---

<a id="t12-q28"></a>
### T12-Q28
*⏭ пропущено · домен: офіційна мітка*

A development team has created AWS CloudFormation templates that are reusable by taking advantage of input parameters to name resources based on client names.

You would like to save your templates on the cloud, which storage option should you choose?

- ✅ S3
- ▫️ EFS
- ▫️ ECR
- ▫️ EBS

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

S3

If you upload a local template file, AWS CloudFormation uploads it to an Amazon Simple Storage Service (Amazon S3) bucket in your AWS account. If you don't already have an S3 bucket that was created by AWS CloudFormation, it creates a unique bucket for each region in which you upload a template file. If you already have an S3 bucket that was created by AWS CloudFormation in your AWS account, AWS CloudFormation adds the template to that bucket.

Selecting a stack template for CloudFormation:  via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-template.html

Incorrect options:

EBS - An Amazon EBS volume is a durable, block-level storage device that you can attach to your instances. After you attach a volume to an instance, you can use it as you would use a physical hard drive. EBS volumes are flexible. Amazon EBS is a recommended storage option when data must be quickly accessible and requires long-term persistence. EBS cannot be used for selecting a stack template for CloudFormation.

EFS - EFS is a file storage service where you mount the file system on an Amazon EC2 Linux-based instance which is not an option for CloudFormation.

ECR - Amazon ECR eliminates the need to operate your container repositories or worry about scaling the underlying infrastructure which does not apply to CloudFormation.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-console-create-stack-template.html

</details>

---

<a id="t12-q60"></a>
### T12-Q60
*⏭ пропущено · домен: офіційна мітка*

The development team at an IT company uses CloudFormation to manage its AWS infrastructure. The team has created a network stack containing a VPC with subnets and a web application stack with EC2 instances and an RDS instance. The team wants to reference the VPC created in the network stack into its web application stack.

As a Developer Associate, which of the following solutions would you recommend for the given use-case?

- ▫️ Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Ref intrinsic function to reference the value of VPC into the web application stack
- ▫️ Create a cross-stack reference and use the Outputs output field to flag the value of VPC from the network stack. Then use Ref intrinsic function to reference the value of VPC into the web application stack
- ▫️ Create a cross-stack reference and use the Outputs output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack
- ✅ Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack

AWS CloudFormation gives developers and businesses an easy way to create a collection of related AWS and third-party resources and provision them in an orderly and predictable fashion.

How CloudFormation Works:  via - https://aws.amazon.com/cloudformation/

You can create a cross-stack reference to export resources from one AWS CloudFormation stack to another. For example, you might have a network stack with a VPC and subnets and a separate public web application stack. To use the security group and subnet from the network stack, you can create a cross-stack reference that allows the web application stack to reference resource outputs from the network stack. With a cross-stack reference, owners of the web application stacks don't need to create or maintain networking rules or assets.

To create a cross-stack reference, use the Export output field to flag the value of a resource output for export. Then, use the Fn::ImportValue intrinsic function to import the value.

You cannot use the Ref intrinsic function to import the value.

 via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-crossstackref.html

Incorrect options:

Create a cross-stack reference and use the Outputs output field to flag the value of VPC from the network stack. Then use Fn::ImportValue intrinsic function to import the value of VPC into the web application stack

Create a cross-stack reference and use the Outputs output field to flag the value of VPC from the network stack. Then use Ref intrinsic function to reference the value of VPC into the web application stack

Create a cross-stack reference and use the Export output field to flag the value of VPC from the network stack. Then use Ref intrinsic function to reference the value of VPC into the web application stack

These three options contradict the details provided in the explanation above, so these options are not correct.

Джерела: <https://aws.amazon.com/cloudformation/> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-crossstackref.html>

</details>

---
