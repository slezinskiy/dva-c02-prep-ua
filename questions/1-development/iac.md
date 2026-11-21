# CloudFormation / SAM / CDK

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 7  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#iac) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q37"></a>
### T01-Q37
*⏭ пропущено · домен: офіційна мітка*

A media analytics company has built a streaming application on Lambda using Serverless Application Model (SAM).

As a Developer Associate, which of the following would you identify as the correct order of execution to successfully deploy the application?

- ▫️ Develop the SAM template locally =&gt; upload the template to CodeCommit =&gt; deploy your application to CodeDeploy
- ▫️ Develop the SAM template locally =&gt; deploy the template to S3 =&gt; use your application in the cloud
- ▫️ Develop the SAM template locally =&gt; upload the template to Lambda =&gt; deploy your application to the cloud
- ✅ Develop the SAM template locally =&gt; upload the template to S3 =&gt; deploy your application to the cloud

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Develop the SAM template locally =&gt; upload the template to S3 =&gt; deploy your application to the cloud

The AWS Serverless Application Model (SAM) is an open-source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. With just a few lines per resource, you can define the application you want and model it using YAML.

You can develop and test your serverless application locally, and then you can deploy your application by using the sam deploy command. The sam deploy command zips your application artifacts, uploads them to Amazon Simple Storage Service (Amazon S3), and deploys your application to the AWS Cloud. AWS SAM uses AWS CloudFormation as the underlying deployment mechanism.

 via - https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-deploying.html

Incorrect options:

Develop the SAM template locally =&gt; upload the template to Lambda =&gt; deploy your application to the cloud

Develop the SAM template locally =&gt; upload the template to CodeCommit =&gt; deploy your application to CodeDeploy

Develop the SAM template locally =&gt; deploy the template to S3 =&gt; use your application in the cloud

These three options contradict the details provided in the explanation above, so these are incorrect.

Reference:

https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-deploying.html

</details>

---

<a id="t01-q60"></a>
### T01-Q60
*⏭ пропущено · домен: офіційна мітка*

An IT company uses AWS CloudFormation templates to provision their AWS infrastructure for Amazon EC2, Amazon VPC, and Amazon S3 resources. Using cross-stack referencing, a developer creates a stack called NetworkStack which will export the subnetId that can be used when creating EC2 instances in another stack.

To use the exported value in another stack, which of the following functions must be used?

- ▫️ !Ref
- ✅ !ImportValue
- ▫️ !GetAtt
- ▫️ !Sub

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

!ImportValue

The intrinsic function Fn::ImportValue returns the value of an output exported by another stack. You typically use this function to create cross-stack references.

Incorrect options:

!Ref - Returns the value of the specified parameter or resource.

!GetAtt - Returns the value of an attribute from a resource in the template.

!Sub - Substitutes variables in an input string with values that you specify.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-importvalue.html

</details>

---

<a id="t09-q30"></a>
### T09-Q30
*⏭ пропущено · домен: офіційна мітка*

As a Developer, you are given a document written in YAML that represents the architecture of a serverless application. The first line of the document contains Transform: 'AWS::Serverless-2016-10-31'.

What does the Transform section in the document represent?

- ▫️ It represents an intrinsic function
- ▫️ Presence of Transform section indicates it is a CloudFormation Parameter
- ✅ Presence of Transform section indicates it is a Serverless Application Model (SAM) template
- ▫️ It represents a Lambda function definition

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudFormation template is a JSON- or YAML-formatted text file that describes your AWS infrastructure. Templates include several major sections. The "Resources" section is the only required section. The optional "Transform" section specifies one or more macros that AWS CloudFormation uses to process your template.

Presence of Transform section indicates it is a Serverless Application Model (SAM) template - The AWS::Serverless transform, which is a macro hosted by AWS CloudFormation, takes an entire template written in the AWS Serverless Application Model (AWS SAM) syntax and transforms and expands it into a compliant AWS CloudFormation template. So, the presence of the Transform section indicates, the document is a SAM template.

Incorrect options:

It represents a Lambda function definition - Lambda function is created using "AWS::Lambda::Function" resource and has no connection to Transform section.

It represents an intrinsic function - Intrinsic Functions in templates are used to assign values to properties that are not available until runtime. They usually start with Fn:: or !. Example: !Sub or Fn::Sub.

Presence of 'Transform' section indicates it is a CloudFormation Parameter - CloudFormation parameters are part of Parameters block of the template, similar to below code:

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-aws-serverless.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html>

</details>

---

<a id="t09-q52"></a>
### T09-Q52
*⏭ пропущено · домен: офіційна мітка*

As an AWS certified developer associate, you are working on an AWS CloudFormation template that will create resources for a company's cloud infrastructure. Your template is composed of three stacks which are Stack-A, Stack-B, and Stack-C. Stack-A will provision a VPC, a security group, and subnets for public web applications that will be referenced in Stack-B and Stack-C.

After running the stacks you decide to delete them, in which order should you do it?

- ✅ Stack B, then Stack C, then Stack A
- ▫️ Stack C then Stack A then Stack B
- ▫️ Stack A, then Stack B, then Stack C
- ▫️ Stack A, Stack C then Stack B

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudFormation gives developers and businesses an easy way to create a collection of related AWS and third-party resources and provision them in an orderly and predictable fashion.

How CloudFormation Works:  via - https://aws.amazon.com/cloudformation/

Stack B, then Stack C, then Stack A

All of the imports must be removed before you can delete the exporting stack or modify the output value. In this case, you must delete Stack B as well as Stack C, before you delete Stack A.

 via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-exports.html

Incorrect options:

Stack A, then Stack B, then Stack C - All of the imports must be removed before you can delete the exporting stack or modify the output value. In this case, you cannot delete Stack A first because that's being referenced in the other Stacks.

Stack A, Stack C then Stack B - All of the imports must be removed before you can delete the exporting stack or modify the output value. In this case, you cannot delete Stack A first because that's being referenced in the other Stacks.

Stack C then Stack A then Stack B - Stack C is fine but you should delete Stack B before Stack A because all of the imports must be removed before you can delete the exporting stack or modify the output value.

Reference: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-exports.html

</details>

---

<a id="t11-q28"></a>
### T11-Q28
*⏭ пропущено · домен: офіційна мітка*

As an AWS Certified Developer Associate, you are given a document written in YAML that represents the architecture of a serverless application. The first line of the document contains Transform: 'AWS::Serverless-2016-10-31'.

What does the Transform section in the document represent?

- ▫️ Presence of Transform section indicates it is a CloudFormation Parameter
- ✅ Presence of Transform section indicates it is a Serverless Application Model (SAM) template
- ▫️ It represents a Lambda function definition
- ▫️ It represents an intrinsic function

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudFormation template is a JSON- or YAML-formatted text file that describes your AWS infrastructure. Templates include several major sections. The "Resources" section is the only required section. The optional "Transform" section specifies one or more macros that AWS CloudFormation uses to process your template.

The AWS Serverless Application Model (SAM) is an open-source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. With just a few lines per resource, you can define the application you want and model it using YAML.

Presence of 'Transform' section indicates it is a Serverless Application Model (SAM) template - The AWS::Serverless transform, which is a macro hosted by AWS CloudFormation, takes an entire template written in the AWS Serverless Application Model (AWS SAM) syntax and transforms and expands it into a compliant AWS CloudFormation template. So, presence of "Transform" section indicates, the document is a SAM template.

Sample CloudFormation YAML template:  via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html

Incorrect options:

It represents a Lambda function definition - Lambda function is created using "AWS::Lambda::Function" resource and has no connection to 'Transform' section.

It represents an intrinsic function - Intrinsic Functions in templates are used to assign values to properties that are not available until runtime. They usually start with Fn:: or !. Example: !Sub or Fn::Sub.

Presence of 'Transform' section indicates it is a CloudFormation Parameter - CloudFormation parameters are part of Parameters block of the template, like so:

Parameters in YAML:  via - https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-aws-serverless.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html>

</details>

---

<a id="t11-q30"></a>
### T11-Q30
*⏭ пропущено · домен: офіційна мітка*

You are creating a Cloud Formation template to deploy your CMS application running on an EC2 instance within your AWS account. Since the application will be deployed across multiple regions, you need to create a map of all the possible values for the base AMI.

How will you invoke the !FindInMap function to fulfill this use case?

- ▫️ !FindInMap [ MapName ]
- ▫️ !FindInMap [ MapName, TopLevelKey, SecondLevelKey, ThirdLevelKey ]
- ▫️ !FindInMap [ MapName, TopLevelKey ]
- ✅ !FindInMap [ MapName, TopLevelKey, SecondLevelKey ]

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

!FindInMap [ MapName, TopLevelKey, SecondLevelKey ] - The intrinsic function Fn::FindInMap returns the value corresponding to keys in a two-level map that is declared in the Mappings section. YAML Syntax for the full function name: Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]

Short form of the above syntax is : !FindInMap [ MapName, TopLevelKey, SecondLevelKey ]

Where,

MapName - Is the logical name of a mapping declared in the Mappings section that contains the keys and values. TopLevelKey - The top-level key name. Its value is a list of key-value pairs. SecondLevelKey - The second-level key name, which is set to one of the keys from the list assigned to TopLevelKey.

Consider the following YAML template:

Mappings:
  RegionMap:
    us-east-1:
      HVM64: "ami-0ff8a91507f77f867"
      HVMG2: "ami-0a584ac55a7631c0c"
    us-west-1:
      HVM64: "ami-0bdb828fd58c52235"
      HVMG2: "ami-066ee5fd4a9ef77f1"
    eu-west-1:
      HVM64: "ami-047bb4163c506cd98"
      HVMG2: "ami-31c2f645"
    ap-southeast-1:
      HVM64: "ami-08569b978cc4dfa10"
      HVMG2: "ami-0be9df32ae9f92309"
    ap-northeast-1:
      HVM64: "ami-06cd52961ce9f0d85"
      HVMG2: "ami-053cdd503598e4a9d"
Resources:
  myEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !FindInMap
        - RegionMap
        - !Ref 'AWS::Region'
        - HVM64
      InstanceType: m1.small
The example template contains an AWS::EC2::Instance resource whose ImageId property is set by the FindInMap function.

MapName is set to the map of interest, "RegionMap" in this example. TopLevelKey is set to the region where the stack is created, which is determined by using the "AWS::Region" pseudo parameter. SecondLevelKey is set to the desired architecture, "HVM64" for this example.

FindInMap returns the AMI assigned to FindInMap. For a HVM64 instance in us-east-1, FindInMap would return "ami-0ff8a91507f77f867".

Incorrect options:

!FindInMap [ MapName, TopLevelKey ]

!FindInMap [ MapName ]

!FindInMap [ MapName, TopLevelKey, SecondLevelKey, ThirdLevelKey ]

These three options contradict the explanation provided above, hence these options are incorrect.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html

</details>

---

<a id="t12-q07"></a>
### T12-Q07
*⏭ пропущено · домен: офіційна мітка*

As an AWS Certified Developer Associate, you are writing a CloudFormation template in YAML. The template consists of an EC2 instance creation and one RDS resource. Once your resources are created you would like to output the connection endpoint for the RDS database.

Which intrinsic function returns the value needed?

- ▫️ !Sub
- ▫️ !Ref
- ▫️ !FindInMap
- ✅ !GetAtt

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudFormation provides several built-in functions that help you manage your stacks. Intrinsic functions are used in templates to assign values to properties that are not available until runtime.

!GetAtt - The Fn::GetAtt intrinsic function returns the value of an attribute from a resource in the template. This example snippet returns a string containing the DNS name of the load balancer with the logical name myELB - YML : !GetAtt myELB.DNSName JSON : "Fn::GetAtt" : [ "myELB" , "DNSName" ]

Incorrect options:

!Sub - The intrinsic function Fn::Sub substitutes variables in an input string with values that you specify. In your templates, you can use this function to construct commands or outputs that include values that aren't available until you create or update a stack.

!Ref - The intrinsic function Ref returns the value of the specified parameter or resource.

!FindInMap - The intrinsic function Fn::FindInMap returns the value corresponding to keys in a two-level map that is declared in the Mappings section. For example, you can use this in the Mappings section that contains a single map, RegionMap, that associates AMIs with AWS regions.

Джерела: <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html> · <https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getatt.html>

</details>

---
