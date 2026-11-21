# AWS Lambda

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 45  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#lambda) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q04"></a>
### T01-Q04
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EventBridge schedule.** → [детальніше](../../06-contradictions-and-traps.md#c14)

You are looking to invoke an AWS Lambda function every hour (similar to a cron job) in a serverless way.

Which event source should you use for your AWS Lambda function?

- ▫️ Amazon S3
- ✅ EventBridge
- ▫️ SQS
- ▫️ Kinesis

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

EventBridge

You can create a Lambda function and direct EventBridge to execute it on a regular schedule. You can specify a fixed rate (for example, execute a Lambda function every hour or 15 minutes), or you can specify a Cron expression.

EventBridge Key Concepts:  via - https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html

Schedule Expressions for EventBridge Rules:  via - https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html

Incorrect options:

Amazon S3

SQS

Kinesis

These three AWS services don't have cron capabilities, so these options are incorrect.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html> · <https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html>

</details>

---

<a id="t01-q24"></a>
### T01-Q24
*⏭ пропущено · домен: офіційна мітка*

Your Lambda function must use the Node.js drivers to connect to your RDS PostgreSQL database in your VPC.

How do you bundle your Lambda function to add the dependencies?

- ▫️ Zip the function as-is with a package.json file so that AWS Lambda can resolve the dependencies for you
- ▫️ Upload the code through the AWS console and upload the dependencies as a zip
- ▫️ Zip the function and the dependencies separately and upload them in AWS Lambda as two parts
- ✅ Put the function and the dependencies in one folder and zip them together

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

How Lambda function works:  via - https://aws.amazon.com/lambda/

Put the function and the dependencies in one folder and zip them together

A deployment package is a ZIP archive that contains your function code and dependencies. You need to create a deployment package if you use the Lambda API to manage functions, or if you need to include libraries and dependencies other than the AWS SDK. You can upload the package directly to Lambda, or you can use an Amazon S3 bucket, and then upload it to Lambda. If the deployment package is larger than 50 MB, you must use Amazon S3. This is the standard way of packaging Lambda functions.

Incorrect options:

Zip the function as-is with a package.json file so that AWS Lambda can resolve the dependencies for you

Upload the code through the AWS console and upload the dependencies as a zip

Zip the function and the dependencies separately and upload them in AWS Lambda as two parts

These three options are incorrect as there's only one way of deploying a Lambda function, which is to provide the zip file with all dependencies that it'll need.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/nodejs-create-deployment-pkg.html

</details>

---

<a id="t01-q34"></a>
### T01-Q34
*⏭ пропущено · домен: офіційна мітка*

Your Lambda function processes files for your customers and as part of that process, it creates a lot of intermediary files it needs to store on its disk and then discard.

What is the best way to store temporary files for your Lambda functions that will be discarded when the function stops running?

- ▫️ Use an S3 bucket
- ▫️ Create a tmp/ directory in the source zip file and use it
- ▫️ Use the local directory /opt
- ✅ Use the local directory /tmp

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use the local directory /tmp

This is 512MB of temporary space you can use for your Lambda functions.

Incorrect options:

Create a tmp/ directory in the source zip file and use it - This option has been added as a distractor, as you can't access a directory within a zip file.

Use the local directory /opt - This option has been added as a distractor. This path is not accessible.

Use an S3 bucket - This won't be temporary after the Lambda function is deleted, so this option is incorrect.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/limits.html

</details>

---

<a id="t01-q49"></a>
### T01-Q49
*⏭ пропущено · домен: офіційна мітка*

The customer feedback functionality for a company's flagship web application is handled via an Amazon API Gateway based REST API that invokes an AWS Lambda function for further processing. Although the performance of the function is satisfactory, the development team has been tasked to optimize the startup time of the Lambda function to further improve the customer experience.

How will you optimize the Lambda function for faster initialization?

- ✅ Configure provisioned concurrency for the Lambda function to respond immediately to the function's invocations
- ▫️ Configure an interface VPC endpoint powered by AWS PrivateLink to access the Amazon API Gateway REST API with milliseconds latency
- ▫️ Configure reserved concurrency to guarantee the maximum number of concurrent instances of the Lambda function
- ▫️ Enable API caching in Amazon API Gateway to cache AWS Lambda function response

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure provisioned concurrency for the Lambda function to respond immediately to the function's invocations

When Lambda allocates an instance of your function, the runtime loads your function's code and runs the initialization code that you define outside of the handler. If your code and dependencies are large, or you create SDK clients during initialization, this process can take some time. When your function has not been used for some time, needs to scale up, or when you update a function, Lambda creates new execution environments. This causes the portion of requests that are served by new instances to have higher latency than the rest, otherwise known as a cold start.

By allocating provisioned concurrency before an increase in invocations, you can ensure that all requests are served by initialized instances with low latency. Lambda functions configured with provisioned concurrency run with consistent start-up latency, making them ideal for building interactive mobile or web backends, latency-sensitive microservices, and synchronously invoked APIs.

Functions with Provisioned Concurrency differ from on-demand functions in some important ways:

Initialization code does not need to be optimized. Since this happens long before the invocation, lengthy initialization does not impact the latency of invocations. If you are using runtimes that typically take longer to initialize, like Java, the performance of these can benefit from using Provisioned Concurrency.

Initialization code is run more frequently than the total number of invocations. Since Lambda is highly available, for every one unit of Provisioned Concurrency, there are a minimum of two execution environments prepared in separate Availability Zones. This is to ensure that your code is available in the event of a service disruption. As environments are reaped and load balancing occurs, Lambda over-provisions environments to ensure availability. You are not charged for this activity. If your code initializer implements logging, you will see additional log files anytime that this code is run, even though the main handler is not invoked.

Provisioned Concurrency cannot be used with the $LATEST version. This feature can only be used with published versions and aliases of a function. If you see cold starts for functions configured to use Provisioned Concurrency, you may be invoking the $LATEST version, instead of the version or alias with Provisioned Concurrency configured.

Reducing cold starts with Provisioned Concurrency:  via - https://aws.amazon.com/blogs/compute/operating-lambda-performance-optimization-part-1/

Incorrect options:

Configure reserved concurrency to guarantee the maximum number of concurrent instances of the Lambda function - Reserved concurrency guarantees the maximum number of concurrent instances for the function. When a function has reserved concurrency, no other function can use that concurrency. There is no charge for configuring reserved concurrency for a function. Whereas, provisioned concurrency initializes a requested number of execution environments so that they are prepared to respond immediately to your function's invocations.

Configure an interface VPC endpoint powered by AWS PrivateLink to access the Amazon API Gateway REST API with milliseconds latency - An interface VPC endpoint can be used to connect your VPC resources to the AWS Lambda function without crossing the public internet. VPC endpoint is irrelevant to the current discussion.

Enable API caching in Amazon API Gateway to cache AWS Lambda function response - With caching, you can reduce the number of calls made to your AWS Lambda function and also improve the latency of requests to your API. Caching is best-effort and applications making frequent API calls to retrieve static data can benefit from a caching layer. Caching does not reduce the initialization time Lambda takes and hence is not an optimal solution for this use case.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html> · <https://aws.amazon.com/blogs/compute/operating-lambda-performance-optimization-part-1/> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html>

</details>

---

<a id="t01-q64"></a>
### T01-Q64
*⏭ пропущено · домен: офіційна мітка*

Your company wants to move away from manually managing Lambda in the AWS console and wants to upload and update them using AWS CloudFormation.

How do you declare an AWS Lambda function in CloudFormation? (Select two)

- ▫️ Upload all the code to CodeCommit and refer to the CodeCommit Repository in AWS::Lambda::Function block
- ▫️ Write the AWS Lambda code inline in CloudFormation in the AWS::Lambda::Function block and reference the dependencies as a zip file stored in S3
- ✅ Upload all the code as a zip to S3 and refer the object in AWS::Lambda::Function block
- ✅ Write the AWS Lambda code inline in CloudFormation in the AWS::Lambda::Function block as long as there are no third-party dependencies
- ▫️ Upload all the code as a folder to S3 and refer the folder in AWS::Lambda::Function block

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

How Lambda function works:  via - https://aws.amazon.com/lambda/

Upload all the code as a zip to S3 and refer the object in AWS::Lambda::Function block

You can upload all the code as a zip to S3 and refer the object in AWS::Lambda::Function block.

The AWS::Lambda::Function resource creates a Lambda function. To create a function, you need a deployment package and an execution role. The deployment package contains your function code.

Write the AWS Lambda code inline in CloudFormation in the AWS::Lambda::Function block as long as there are no third-party dependencies

The other option is to write the code inline for Node.js and Python as long as there are no dependencies for your code, besides the dependencies already provided by AWS in your Lambda Runtime (aws-sdk and cfn-response and many other AWS related libraries are preloaded via, for example, boto3 (python) in the lambda instances.)

YAML template for creating a Lambda function:

Type: AWS::Lambda::Function
Properties:
  Code:
    Code
  DeadLetterConfig:
    DeadLetterConfig
  Description: String
  Environment:
    Environment
  FileSystemConfigs:
    - FileSystemConfig
  FunctionName: String
  Handler: String
  KmsKeyArn: String
  Layers:
    - String
  MemorySize: Integer
  ReservedConcurrentExecutions: Integer
  Role: String
  Runtime: String
  Tags:
    - Tag
  Timeout: Integer
  TracingConfig:
    TracingConfig
  VpcConfig:
    VpcConfig
Incorrect options:

Upload all the code to CodeCommit and refer to the CodeCommit Repository in AWS::Lambda::Function block

Upload all the code as a folder to S3 and refer the folder in AWS::Lambda::Function block

Write the AWS Lambda code inline in CloudFormation in the AWS::Lambda::Function block and reference the dependencies as a zip file stored in S3

These three options contradict the explanation provided earlier. So these are incorrect.

Reference:

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-lambda-function-code.html

</details>

---

<a id="t02-q04"></a>
### T02-Q04
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A developer has deployed an application on AWS Lambda. The application uses Python and must generate and then upload a file to an Amazon S3 bucket. The developer must implement the upload functionality with the least possible change to the application code.

Which solution BEST meets these requirements?

- ▫️ Include the AWS SDK for Python in the Lambda function code.
- ✅ Use the AWS SDK for Python that is installed in the Lambda execution environment.
- ▫️ Use the AWS CLI that is installed in the Lambda execution environment.
- ❌ Make an HTTP request directly to the S3 API to upload the file.

<details><summary>Пояснення (оригінал, EN)</summary>

The best practice for Lambda development is to bundle all dependencies used by your Lambda function, including the AWS SDK. However, since this question specifically requests that the least possible changes are made to the application code, the developer can instead use the SDK for Python that is installed in the Lambda environment to upload the file to Amazon S3.

CORRECT: "Use the AWS SDK for Python that is installed in the Lambda execution environment" is the correct answer (as explained above.)

INCORRECT: "Include the AWS SDK for Python in the Lambda function code" is incorrect.

This is the best practice for deployment. However, in this case the developer must minimize changes to code and including the SDK as a dependency in the code would require potential updates to existing Python code.

INCORRECT: "Make an HTTP request directly to the S3 API to upload the file" is incorrect.

AWS supports uploads to S3 using the console, AWS SDKs, REST API, and the AWS CLI.

INCORRECT: "Use the AWS CLI that is installed in the Lambda execution environment" is incorrect.

The AWS CLI is not installed in the Lambda execution environment.

Джерела: <https://aws.amazon.com/blogs/compute/upcoming-changes-to-the-python-sdk-in-aws-lambda/>

</details>

---

<a id="t02-q36"></a>
### T02-Q36
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **20×20=400.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A serverless application uses an AWS Lambda function to process Amazon S3 events. The Lambda function executes 20 times per second and takes 20 seconds to complete each execution.

How many concurrent executions will the Lambda function require?

- ▫️ 40
- ❌ 20
- ▫️ 5
- ✅ 400

<details><summary>Пояснення (оригінал, EN)</summary>

Concurrency is the number of requests that your function is serving at any given time. When your function is invoked, Lambda allocates an instance of it to process the event. When the function code finishes running, it can handle another request. If the function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency.

To calculate the concurrency requirements for the Lambda function simply multiply the number of executions per second (20) by the time it takes to complete the execution (20).

Therefore, for this scenario, the calculation is 20 x 20 = 400.

CORRECT: "400" is the correct answer.

INCORRECT: "5" is incorrect. Please use the formula above to calculate concurrency requirements.

INCORRECT: "40" is incorrect. Please use the formula above to calculate concurrency requirements.

INCORRECT: "20" is incorrect. Please use the formula above to calculate concurrency requirements.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html>

</details>

---

<a id="t02-q52"></a>
### T02-Q52
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is writing an imaging microservice on AWS Lambda. The service is dependent on several libraries that are not available in the Lambda runtime environment.

Which strategy should the Developer follow to create the Lambda deployment package?

- ▫️ Create a ZIP file with the source code and an appspec.yml file. Add the libraries to the appspec.yml file and upload to Amazon S3. Deploy using CloudFormation
- ✅ Create a ZIP file with the source code and all dependent libraries
- ▫️ Create a ZIP file with the source code and a script that installs the dependent libraries at runtime
- ▫️ Create a ZIP file with the source code and a buildspec.yml file that installs the dependent libraries on AWS Lambda

<details><summary>Пояснення (оригінал, EN)</summary>

A deployment package is a ZIP archive that contains your function code and dependencies. You need to create a deployment package if you use the Lambda API to manage functions, or if you need to include libraries and dependencies other than the AWS SDK.

You can upload the package directly to Lambda, or you can use an Amazon S3 bucket, and then upload it to Lambda. If the deployment package is larger than 50 MB, you must use Amazon S3.

CORRECT: "Create a ZIP file with the source code and all dependent libraries" is the correct answer.

INCORRECT: "Create a ZIP file with the source code and a script that installs the dependent libraries at runtime" is incorrect as the Developer should not run a script at runtime as this will cause latency. Instead, the Developer should include the dependent libraries in the ZIP package.

INCORRECT: "Create a ZIP file with the source code and an appspec.yml file. Add the libraries to the appspec.yml file and upload to Amazon S3. Deploy using CloudFormation" is incorrect. The appspec.yml file is used with CodeDeploy, you cannot add libraries into it, and it is not deployed using CloudFormation.

INCORRECT: "Create a ZIP file with the source code and a buildspec.yml file that installs the dependent libraries on AWS Lambda" is incorrect as the buildspec.yml file is used with CodeBuild for compiling source code and running tests. It cannot be used to install dependent libraries within Lambda.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/python-package.html>

</details>

---

<a id="t03-q36"></a>
### T03-Q36
*⏭ пропущено · домен: офіційна мітка*

You are assigned as the new project lead for a web application that processes orders for customers. You want to integrate event-driven processing anytime data is modified or deleted and use a serverless approach using AWS Lambda for processing stream events.

Which of the following databases should you choose from?

- ▫️ ElastiCache
- ▫️ RDS
- ✅ DynamoDB
- ▫️ Kinesis

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

DynamoDB

A DynamoDB stream is an ordered flow of information about changes to items in a DynamoDB table. When you enable a stream on a table, DynamoDB Streams captures a time-ordered sequence of item-level modifications in any DynamoDB table, and stores this information in a log for up to 24 hours. Applications can access this log and view the data items as they appeared before and after they were modified, in near real-time.

Whenever an application creates, updates, or deletes items in the table, DynamoDB Streams writes a stream record with the primary key attributes of the items that were modified.

DynamoDB Streams Overview:  via - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html

Incorrect options:

RDS - By itself, RDS cannot be used to stream events like DynamoDB, so this option is ruled out. However, you can use Amazon Kinesis for streaming data from RDS.

Please refer to this excellent blog for more details on using Kinesis for streaming data from RDS: https://aws.amazon.com/blogs/database/streaming-changes-in-a-database-with-amazon-kinesis/

ElastiCache - ElastiCache works as an in-memory data store and cache, it cannot be used to stream data like DynamoDB.

Kinesis - Kinesis is not a database, so this option is ruled out.

Amazon Kinesis Data Streams enables you to build custom applications that process or analyze streaming data for specialized needs. You can continuously add various types of data such as clickstreams, application logs, and social media to an Amazon Kinesis data stream from hundreds of thousands of sources.

How Kinesis Data Streams Work  via - https://aws.amazon.com/kinesis/data-streams/

Reference:

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html

</details>

---

<a id="t03-q61"></a>
### T03-Q61
*⏭ пропущено · домен: офіційна мітка*

You have uploaded a zip file to AWS Lambda that contains code files written in Node.Js. When your function is executed you receive the following output, 'Error: Memory Size: 10,240 MB Max Memory Used'.

Which of the following explains the problem?

- ▫️ The uncompressed zip file exceeds AWS Lambda limits
- ✅ Your Lambda function ran out of RAM
- ▫️ Your zip file is corrupt
- ▫️ You have uploaded a zip file larger than 50 MB to AWS Lambda

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Your Lambda function ran out of RAM

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

How Lambda function works:  via - https://aws.amazon.com/lambda/

The maximum amount of memory available to the Lambda function at runtime is 10,240 MB. Your Lambda function was deployed with 10,240 MB of RAM, but it seems your code requested or used more than that, so the Lambda function failed.

 via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-console.html

Incorrect options:

Your zip file is corrupt - A memory size error states that Lambda was able to extract so the file is not corrupt

The uncompressed zip file exceeds AWS Lambda limits - This is not correct as your function was able to execute.

You have uploaded a zip file larger than 50 MB to AWS Lambda - This is not correct as your lambda function was able to execute

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/configuration-console.html

</details>

---

<a id="t04-q17"></a>
### T04-Q17
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating an AWS Lambda function that will process medical images. The function is dependent on several libraries that are not available in the Lambda runtime environment. Which strategy should be used to create the Lambda deployment package?

- ▫️ Create a ZIP file with the source code and a buildspec.yaml file that installs the dependent libraries on AWS Lambda
- ✅ Create a ZIP file with the source code and all dependent libraries
- ▫️ Create a ZIP file with the source code and a script that installs the dependent libraries at runtime
- ▫️ Create a ZIP file with the source code. Stage the dependent libraries on an Amazon S3 bucket indicated by the Lambda environment variable LIBRARY_PATH

<details><summary>Пояснення (оригінал, EN)</summary>

A deployment package is a ZIP archive that contains your function code and dependencies. You need to create a deployment package if you use the Lambda API to manage functions, or if you need to include libraries and dependencies other than the AWS SDK.

You can upload the package directly to Lambda, or you can use an Amazon S3 bucket, and then upload it to Lambda. If the deployment package is larger than 50 MB, you must use Amazon S3.

If your function depends on libraries not included in the Lambda runtime, you can install them to a local directory and include them in your deployment package.

CORRECT: "Create a ZIP file with the source code and all dependent libraries" is the correct answer.

INCORRECT: "Create a ZIP file with the source code and a script that installs the dependent libraries at runtime" is incorrect as though it is possible to call a script within the function code, this would need to run every time and pull in the files which would cause latency.

INCORRECT: "Create a ZIP file with the source code. Stage the dependent libraries on an Amazon S3 bucket indicated by the Lambda environment variable LIBRARY_PATH" is incorrect as you cannot map an external path to a Lambda function using an environment variable.

INCORRECT: "Create a ZIP file with the source code and a buildspec.yaml file that installs the dependent libraries on AWS Lambda" is incorrect as a buildspec.yaml file that is used by AWS CodeBuild to run a build. The libraries need to be included in the package zip file for the Lambda function.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/python-package.html>

</details>

---

<a id="t04-q19"></a>
### T04-Q19
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **10×3=30.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A serverless application uses an AWS Lambda function, Amazon API Gateway API and an Amazon DynamoDB table. The Lambda function executes 10 times per second and takes 3 seconds to complete each execution.

How many concurrent executions will the Lambda function require?

- ✅ 30
- ▫️ 12
- ▫️ 10
- ▫️ 3

<details><summary>Пояснення (оригінал, EN)</summary>

Concurrency is the number of requests that your function is serving at any given time. When your function is invoked, Lambda allocates an instance of it to process the event. When the function code finishes running, it can handle another request. If the function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency.

To calculate the concurrency requirements for the Lambda function simply multiply the number of executions per second (10) by the time it takes to complete the execution (3).

Therefore, for this scenario the calculation is 10 x 3 = 30.

CORRECT: "30" is the correct answer.

INCORRECT: "10" is incorrect. Please use the formula above to calculate concurrency requirements.

INCORRECT: "12" is incorrect. Please use the formula above to calculate concurrency requirements.

INCORRECT: "3" is incorrect. Please use the formula above to calculate concurrency requirements.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html>

</details>

---

<a id="t04-q39"></a>
### T04-Q39
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Storage)*

An application resizes images that are uploaded to an Amazon S3 bucket. Amazon S3 event notifications are used to trigger an AWS Lambda function that resizes the images. The processing time for each image is less than one second. A large amount of images are expected to be received in a short burst of traffic. How will AWS Lambda accommodate the workload?

- ▫️ Lambda will scale the memory allocated to the function to increase the amount of CPU available to process many images
- ▫️ Lambda will process the images sequentially in the order they are received
- ▫️ Lambda will collect and then batch process the images in a single execution
- ✅ Lambda will scale out and execute the requests concurrently

<details><summary>Пояснення (оригінал, EN)</summary>

The first time you invoke your function, AWS Lambda creates an instance of the function and runs its handler method to process the event. When the function returns a response, it stays active and waits to process additional events. If you invoke the function again while the first event is being processed, Lambda initializes another instance, and the function processes the two events concurrently.

Your functions’ concurrency is the number of instances that serve requests at a given time. For an initial burst of traffic, your functions’ cumulative concurrency in a Region can reach an initial level of between 500 and 3000, which varies per Region.

Burst Concurrency Limits:

• 3000 – US West (Oregon), US East (N. Virginia), Europe (Ireland).

• 1000 – Asia Pacific (Tokyo), Europe (Frankfurt).

• 500 – Other Regions.

After the initial burst, your functions’ concurrency can scale by an additional 500 instances each minute. This continues until there are enough instances to serve all requests, or until a concurrency limit is reached.

The default account limit is up to 1000 executions per second, per region (can be increased).

CORRECT: "Lambda will scale out and execute the requests concurrently" is the correct answer.

INCORRECT: "Lambda will process the images sequentially in the order they are received" is incorrect as Lambda uses concurrency to process multiple events in parallel.

INCORRECT: "Lambda will collect and then batch process the images in a single execution" is incorrect as Lambda never collects requests and then processes them at a later time. Lambda always uses concurrency to process requests in parallel.

INCORRECT: "Lambda will scale the memory allocated to the function to increase the amount of CPU available to process many images" is incorrect as Lambda does not automatically scale memory/CPU and processes requests in parallel, not sequentially.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html>

</details>

---

<a id="t04-q46"></a>
### T04-Q46
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is creating multiple AWS Lambda functions that will be using an external library that is not included in the standard Lambda libraries. What is the BEST way to make these libraries available to the functions?

- ✅ Create a layer in Lambda that includes the external library
- ▫️ Include the external library with the function code
- ▫️ Create a deployment package that includes the external library
- ▫️ Store the files in Amazon S3 and reference them from your function code

<details><summary>Пояснення (оригінал, EN)</summary>

You can configure your Lambda function to pull in additional code and content in the form of layers. A layer is a ZIP archive that contains libraries, a custom runtime, or other dependencies. With layers, you can use libraries in your function without needing to include them in your deployment package.

Layers let you keep your deployment package small, which makes development easier. You can avoid errors that can occur when you install and package dependencies with your function code.

When a Lambda function configured with a Lambda layer is executed, AWS downloads any specified layers and extracts them to the /opt directory on the function execution environment. Each runtime then looks for a language-specific folder under the /opt directory.

One of the best practices for AWS Lambda functions is to minimize your deployment package size to its runtime necessities in order to reduce the amount of time that it takes for your deployment package to be downloaded and unpacked ahead of invocation.

Therefore, it is preferable to use layers to store the external libraries to optimize performance of the function. Using layers means that the external library will also be available to all of the Lambda functions that the Developer is creating.

CORRECT: "Create a layer in Lambda that includes the external library" is the correct answer.

INCORRECT: "Include the external library with the function code" is incorrect as you should not include an external library within the function code. Even if possible this would result in bloated code that could slow down execution time.

INCORRECT: "Create a deployment package that includes the external library" is incorrect as the best practice is to minimize package sizes to runtime necessities. Also, this would require including the library in all function deployment packages whereas with layers we can create a single layer that is used by all functions.

INCORRECT: "Store the files in Amazon S3 and reference them from your function code" is incorrect as this would likely result in increased latency of your function execution. Instead you should either package the library in the deployment package for your function or use layers (preferable in this scenario).

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---

<a id="t04-q47"></a>
### T04-Q47
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A manufacturing company is creating a new RESTful API that their customers can use to query the status of orders. The endpoint for customer queries will be https://www.manufacturerdomain.com/status/customerID

Which of the following application designs will meet the requirements? (Select TWO.)

- ✅ Amazon API Gateway; AWS Lambda
- ▫️ Amazon ElastiCache; Amazon Elacticsearch Service
- ▫️ Amazon S3; Amazon CloudFront
- ▫️ Amazon SQS; Amazon SNS
- ✅ Elastic Load Balancing; Amazon EC2

<details><summary>Пояснення (оригінал, EN)</summary>

This scenario includes a web application that will use RESTful API calls to determine the status of orders and dynamically return the results back to the company’s customers. Therefore, the two best options are as per below:

• Amazon API Gateway; AWS Lambda – this choice includes API Gateway which is provides managed REST APIs and Lambda which can run the backend code for the application. This is a good solution for this scenario.

• Elastic Load Balancing; Amazon EC2 – with this choice the ELB can load balance to one or more EC2 instances which can run the RESTful APIs and compute functions. This is also a good choice but could be more costly (operationally and financially).

None of the other options provide a workable solution for this scenario.

CORRECT: "Elastic Load Balancing; Amazon EC2" is a correct answer.

CORRECT: "Amazon API Gateway; AWS Lambda" is a correct answer.

INCORRECT: "Amazon SQS; Amazon SNS" is incorrect as these services are used for queuing and sending notifications. They are not suitable for hosting a REST API.

INCORRECT: "Amazon ElastiCache; Amazon Elacticsearch Service" is incorrect as ElastiCache is an in-memory caching service and Elasticsearch is used for searching. These do not provide a suitable solution for this scenario.

INCORRECT: "Amazon S3; Amazon CloudFront" is incorrect as though you can host a static website on Amazon S3 with CloudFront caching the content, this is a static website only and you cannot host an API.

Джерела: <https://aws.amazon.com/ec2/features/> · <https://aws.amazon.com/elasticloadbalancing/> · <https://aws.amazon.com/api-gateway/features/> · <https://aws.amazon.com/lambda/features/>

</details>

---

<a id="t04-q55"></a>
### T04-Q55
*⏭ пропущено · домен: визначено автоматично (у джерелі: —)*

> ⚠️ **Окремі застарілі items → TTL.** → [детальніше](../../06-contradictions-and-traps.md#c09)

A serverless application uses Amazon API Gateway, AWS Lambda and DynamoDB. The application writes statistical data that is constantly received from sensors. The data is analyzed soon after it is written to the database and is then not required.

What is the EASIEST method to remove stale data and optimize database size?

- ▫️ Use atomic counters to decrement the data when it becomes stale
- ▫️ Scan the table for stale data and delete it once every hour
- ✅ Enable the TTL attribute and add expiry timestamps to items
- ▫️ Delete the table and recreate it every hour

<details><summary>Пояснення (оригінал, EN)</summary>

Time to Live (TTL) for Amazon DynamoDB lets you define when items in a table expire so that they can be automatically deleted from the database. With TTL enabled on a table, you can set a timestamp for deletion on a per-item basis, allowing you to limit storage usage to only those records that are relevant.

TTL is useful if you have continuously accumulating data that loses relevance after a specific time period (for example, session data, event logs, usage patterns, and other temporary data). If you have sensitive data that must be retained only for a certain amount of time according to contractual or regulatory obligations, TTL helps you ensure that it is removed promptly and as scheduled.

Therefore, the best answer is to enable the TTL attribute and add expiry timestamps to items.

CORRECT: "Enable the TTL attribute and add expiry timestamps to items" is the correct answer.

INCORRECT: "Use atomic counters to decrement the data when it becomes stale" is incorrect. Atomic counters are useful for incrementing or decrementing the value of an attribute. A good use case is counting website visitors.

INCORRECT: "Scan the table for stale data and delete it once every hour" is incorrect as this is costly in terms of RCUs and WCUs. It also may result in data that has just been written but not analyzed yet.

INCORRECT: "Delete the table and recreate it every hour" is incorrect. The table is constantly being written to and the analysis of data happens soon after the data is written. Therefore, there isn’t a good time to delete and recreate the table as data loss is likely to occur at any time.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html>

</details>

---

<a id="t05-q09"></a>
### T05-Q09
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

An engineer is constructing an AWS Lambda function and intends to log specific key events that transpire during the function's execution. To correlate the events with a particular function invocation, the engineer is looking to incorporate a unique identifier.

The following code segment has been added to the Lambda function:

- ▫️ function handler (event, context) {
- ▫️ }
- ✅ Use context.awsRequestId within the function to fetch the unique identifier associated with each invocation.
- ▫️ Use context.invocationId to extract the unique identifier tied to each function run.
- ▫️ Use event.requestId to obtain the unique identifier for each function execution.
- ▫️ Use context.lambdaId to get the unique identifier corresponding to each function invocation.

<details><summary>Пояснення (оригінал, EN)</summary>

The context object in a Lambda function provides metadata about the function and the current invocation, including a unique identifier for the request, awsRequestId, which can be used to correlate logs from a specific invocation.

CORRECT: "Use context.awsRequestId within the function to fetch the unique identifier associated with each invocation" is the correct answer (as explained above.)

INCORRECT: "Use event.requestId to obtain the unique identifier for each function execution" is incorrect.

event.requestId is incorrect as the event object does not contain a property called requestId.

INCORRECT: "Use context.invocationId to extract the unique identifier tied to each function run" is incorrect.

context.invocationId is not valid as there is no such property in the context object of a Lambda function.

INCORRECT: "Use context.lambdaId to get the unique identifier corresponding to each function invocation" is incorrect.

context.lambdaId is not a valid property within the context object for a Lambda function.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/nodejs-context.html>

</details>

---

<a id="t05-q11"></a>
### T05-Q11
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **50×100=5000 > 1000 → ліміт.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A Developer is creating an AWS Lambda function that will process data from an Amazon Kinesis data stream. The function is expected to be invoked 50 times per second and take 100 seconds to complete each request.

What MUST the Developer do to ensure the functions runs without errors?

- ▫️ Increase the concurrency limit for the function
- ✅ Contact AWS and request to increase the limit for concurrent executions
- ▫️ No action is required as AWS Lambda can easily accommodate this requirement
- ▫️ Implement exponential backoff in the function code

<details><summary>Пояснення (оригінал, EN)</summary>

Concurrency is the number of requests that your function is serving at any given time. When your function is invoked, Lambda allocates an instance of it to process the event. When the function code finishes running, it can handle another request. If the function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency.

Concurrency is subject to a Regional limit that is shared by all functions in a Region. For an initial burst of traffic, your functions' cumulative concurrency in a Region can reach an initial level of between 500 and 3000, which varies per Region:

• 3000 – US West (Oregon), US East (N. Virginia), Europe (Ireland)

• 1000 – Asia Pacific (Tokyo), Europe (Frankfurt)

• 500 – Other Regions

After the initial burst, your functions' concurrency can scale by an additional 500 instances each minute. This continues until there are enough instances to serve all requests, or until a concurrency limit is reached. When requests come in faster than your function can scale, or when your function is at maximum concurrency, additional requests fail with a throttling error (429 status code).

The function continues to scale until the account's concurrency limit for the function's Region is reached. The function catches up to demand, requests subside, and unused instances of the function are stopped after being idle for some time. Unused instances are frozen while they're waiting for requests and don't incur any charges.

The regional concurrency limit starts at 1,000. You can increase the limit by submitting a request in the Support Center console.

Calculating concurrency requirements for this scenario

To calculate the concurrency requirements for this scenario, simply multiply the invocation requests per second (50) with the average execution time in seconds (100). This calculation is 50 x 100 = 5,000.

Therefore, 5,000 concurrent executions is over the default limit and the Developer will need to request in the AWS Support Center console.

CORRECT: "Contact AWS and request to increase the limit for concurrent executions" is the correct answer.

INCORRECT: "No action is required as AWS Lambda can easily accommodate this requirement" is incorrect as by default the AWS account will be limited. Lambda can easily scale to this level of demand however the account limits must first be increased.

INCORRECT: "Increase the concurrency limit for the function" is incorrect as the default account limit of 1,000 concurrent executions will mean you can only assign up to 900 executions to the function (100 must be left unreserved). This is insufficient for this requirement to the account limit must be increased.

INCORRECT: "Implement exponential backoff in the function code" is incorrect. Exponential backoff means configuring the application to wait longer between API calls, slowing the demand. However, this is not a good resolution to this issue as it will have negative effects on the application. The correct choice is to raise the account limits so the function can concurrently execute according to its requirements.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html>

</details>

---

<a id="t05-q36"></a>
### T05-Q36
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A company is creating a serverless application that uses AWS Lambda functions. The developer has written the code to initialize the AWS SDK outside of the Lambda handler function.

What is PRIMARY benefit of this action?

- ▫️ It minimizes the deployment package size.
- ▫️ Improves readability and reduces complexity.
- ✅ Takes advantage of execution environment reuse.
- ▫️ Creates a new SDK instance for each invocation.

<details><summary>Пояснення (оригінал, EN)</summary>

You should initialize SDK clients and database connections outside of the function handler, and cache static assets locally in the /tmp directory. Subsequent invocations processed by the same instance of your function can reuse these resources. This saves cost by reducing function run time.

The primary benefit of this technique is to take advantage of execution environment reuse to improve the performance of your function.

CORRECT: "Takes advantage of execution environment reuse" is the correct answer (as explained above.)

INCORRECT: "Creates a new SDK instance for each invocation" is incorrect.

This is the opposite of what we are trying to achieve here.

INCORRECT: "It minimizes the deployment package size" is incorrect.

This technique does not affect the deployment package size.

INCORRECT: "Improves readability and reduces complexity" is incorrect.

It may improve readability but that is debatable. This is not the primary reason you would use this technique.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html>

</details>

---

<a id="t05-q54"></a>
### T05-Q54
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Database)*

An application uses Amazon API Gateway, an AWS Lambda function and a DynamoDB table. The developer requires that another Lambda function is triggered when an item lifecycle activity occurs in the DynamoDB table.

How can this be achieved?

- ▫️ Configure an Amazon CloudWatch alarm that sends an Amazon SNS notification. Trigger the Lambda function asynchronously from the SNS notification
- ▫️ Enable a DynamoDB stream and trigger the Lambda function asynchronously from the stream
- ✅ Enable a DynamoDB stream and trigger the Lambda function synchronously from the stream
- ▫️ Configure an Amazon CloudTrail API alarm that sends a message to an Amazon SQS queue. Configure the Lambda function to poll the queue and invoke the function synchronously

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon DynamoDB is integrated with AWS Lambda so that you can create triggers—pieces of code that automatically respond to events in DynamoDB Streams. With triggers, you can build applications that react to data modifications in DynamoDB tables.

If you enable DynamoDB Streams on a table, you can associate the stream Amazon Resource Name (ARN) with an AWS Lambda function that you write. Immediately after an item in the table is modified, a new record appears in the table's stream. AWS Lambda polls the stream and invokes your Lambda function synchronously when it detects new stream records.

CORRECT: "Enable a DynamoDB stream and trigger the Lambda function synchronously from the stream" is the correct answer.

INCORRECT: "Enable a DynamoDB stream and trigger the Lambda function asynchronously from the stream" is incorrect as the invocation should be synchronous.

INCORRECT: "Configure an Amazon CloudWatch alarm that sends an Amazon SNS notification. Trigger the Lambda function asynchronously from the SNS notification" is incorrect as you cannot configure a CloudWatch alarm that notifies based on item lifecycle events. It is better to use DynamoDB streams and integrate Lambda.

INCORRECT: "Configure an Amazon CloudTrail API alarm that sends a message to an Amazon SQS queue. Configure the Lambda function to poll the queue and invoke the function synchronously" is incorrect. There is no such alarm that notifies from Amazon CloudTrail relating to item lifecycle events.

Джерела: <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.Lambda.html>

</details>

---

<a id="t05-q59"></a>
### T05-Q59
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is designing a cloud native application. The application will use several AWS Lambda functions that will process items that the functions read from an event source. Which AWS services are supported for Lambda event source mappings? (Select THREE.)

- ▫️ Another Lambda function
- ✅ Amazon Kinesis
- ✅ Amazon DynamoDB
- ✅ Amazon Simple Queue Service (SQS)
- ▫️ Amazon Simple Storage Service (S3)
- ▫️ Amazon Simple Notification Service (SNS)

<details><summary>Пояснення (оригінал, EN)</summary>

An event source mapping is an AWS Lambda resource that reads from an event source and invokes a Lambda function. You can use event source mappings to process items from a stream or queue in services that don't invoke Lambda functions directly. Lambda provides event source mappings for the following services.

Services That Lambda Reads Events From

• Amazon Kinesis

• Amazon DynamoDB

• Amazon Simple Queue Service

An event source mapping uses permissions in the function's execution role to read and manage items in the event source. Permissions, event structure, settings, and polling behavior vary by event source.

CORRECT: "Amazon Kinesis, Amazon DynamoDB, and Amazon Simple Queue Service (SQS)" are the correct answers.

INCORRECT: "Amazon Simple Notification Service (SNS)" is incorrect as SNS should be used as destination for asynchronous invocation.

INCORRECT: "Amazon Simple Storage Service (S3)" is incorrect as Lambda does not read from Amazon S3, you must configure the event notification on the S3 side.

INCORRECT: "Another Lambda function" is incorrect as another function should be invoked asynchronously.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html>

</details>

---

<a id="t06-q11"></a>
### T06-Q11
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer is creating a REST service using Amazon API Gateway with AWS Lambda integration. The service adds data to a spreadsheet and the data is sent as query string parameters in the method request.

How should the Developer convert the query string parameters to arguments for the Lambda function?

- ▫️ Change the integration type
- ✅ Create a mapping template
- ▫️ Include the Amazon Resource Name (ARN) of the Lambda function
- ▫️ Enable request validation

<details><summary>Пояснення (оригінал, EN)</summary>

Standard API Gateway parameter and response code mapping templates allow you to map parameters one-to-one and map a family of integration response status codes (matched by a regular expression) to a single response status code.

Mapping template overrides provides you with the flexibility to perform many-to-one parameter mappings; override parameters after standard API Gateway mappings have been applied; conditionally map parameters based on body content or other parameter values; programmatically create new parameters on the fly; and override status codes returned by your integration endpoint.

Any type of request parameter, response header, or response status code may be overridden.

Following are example uses for a mapping template override:

To create a new header (or overwrite an existing header) as a concatenation of two parameters

To override the response code to a success or failure code based on the contents of the body

To conditionally remap a parameter based on its contents or the contents of some other parameter

To iterate over the contents of a json body and remap key value pairs to headers or query strings

Therefore, the Developer can convert the query string parameters by creating a mapping template.

CORRECT: "Create a mapping template" is the correct answer.

INCORRECT: "Enable request validation" is incorrect as this is used to configure API Gateway to perform basic validation of an API request before proceeding with the integration request.

INCORRECT: "Include the Amazon Resource Name (ARN) of the Lambda function" is incorrect as that doesn’t assist with converting the query string parameters.

INCORRECT: "Change the integration type" is incorrect as to perform a conversion the Lambda integration does not need to have a different integration type such as Lambda proxy.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-override-request-response-parameters.html>

</details>

---

<a id="t06-q26"></a>
### T06-Q26
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

An application must be refactored for the cloud. The application data is stored in an Amazon DynamoDB table and is processed by a Lambda function which prepares the data for analytics. The data processing currently takes place once a day, but the data analysts require it to be performed in near-real time.

Which architecture pattern could be used to enable the data to be processed as it is received?

- ✅ Use an event-driven architecture.
- ▫️ Use a microservices architecture.
- ▫️ Use a fan-out architecture.
- ▫️ Use a scheduled architecture.

<details><summary>Пояснення (оригінал, EN)</summary>

An event driven architecture will ensure that the records are processed as they are received. This can be achieved by creating a DynamoDB Stream for the existing table and then configuring the Lambda function to retrieve messages from the stream. This would be an event-driven architecture as the event (a record being written to the stream) causes the processing layer to do work.

CORRECT: "Use an event-driven architecture" is the correct answer (as explained above.)

INCORRECT: "Use a microservices architecture" is incorrect.

A microservices architecture is where you have many small individual components of an application that are loosely coupled. Though the architecture described may have components of a microservices architecture, this is not the defining characteristic that meets the requirement of near-real time processing.

INCORRECT: "Use a fan-out architecture" is incorrect.

An example of a fan-out architecture is using SNS to send a single notification to many SQS queues.

INCORRECT: "Use a scheduled architecture" is incorrect.

A scheduled architecture would not process events as they occur. It would only process them on a fixed schedule.

Джерела: <https://aws.amazon.com/event-driven-architecture/>

</details>

---

<a id="t07-q30"></a>
### T07-Q30
*⏭ пропущено · домен: офіційна мітка*

A new member of your team is working on creating Dead Letter Queue (DLQ) for AWS Lambda functions.

As a Developer Associate, can you help him identify the use cases, wherein AWS Lambda will add a message into a DLQ after being processed? (Select two)

- ▫️ The Lambda function invocation is synchronous
- ▫️ The Lambda function invocation failed only once but succeeded thereafter
- ▫️ The event has been processed successfully
- ✅ The event fails all processing attempts
- ✅ The Lambda function invocation is asynchronous

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The Lambda function invocation is asynchronous - When an asynchronous invocation event exceeds the maximum age or fails all retry attempts, Lambda discards it. Or sends it to dead-letter queue if you have configured one.

The event fails all processing attempt - A dead-letter queue acts the same as an on-failure destination in that it is used when an event fails all processing attempts or expires without being processed.

Incorrect options:

The Lambda function invocation is synchronous - When you invoke a function synchronously, Lambda runs the function and waits for a response. Queues are generally used with asynchronous invocations since queues implement the decoupling feature of various connected systems. It does not make sense to use queues when the calling code will wait on it for a response.

The event has been processed successfully - A successfully processed event is not sent to the dead-letter queue.

The event processing failed only once but succeeded thereafter - A successfully processed event is not sent to the dead-letter queue.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html

</details>

---

<a id="t07-q32"></a>
### T07-Q32
*⏭ пропущено · домен: офіційна мітка*

A cybersecurity company is running a serverless backend with several compute-heavy workflows running on Lambda functions. The development team has noticed a performance lag after analyzing the performance metrics for the Lambda functions.

As a Developer Associate, which of the following options would you suggest as the BEST solution to address the compute-heavy workloads?

- ▫️ Use reserved concurrency to account for the compute-heavy workflows
- ▫️ Invoke the Lambda functions asynchronously to process the compute-heavy workflows
- ▫️ Use provisioned concurrency to account for the compute-heavy workflows
- ✅ Increase the amount of memory available to the Lambda functions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Increase the amount of memory available to the Lambda functions

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

In the AWS Lambda resource model, you choose the amount of memory you want for your function which allocates proportional CPU power and other resources. This means you will have access to more compute power when you choose one of the new larger settings. You can set your memory in 64MB increments from 128MB to 3008MB. You access these settings when you create a function or update its configuration. The settings are available using the AWS Management Console, AWS CLI, or SDKs.

 via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-console.html

Therefore, by increasing the amount of memory available to the Lambda functions, you can run the compute-heavy workflows.

Incorrect options:

Invoke the Lambda functions asynchronously to process the compute-heavy workflows - When you invoke a function asynchronously, you don't wait for a response from the function code. You hand off the event to Lambda and Lambda handles the rest. You can configure how Lambda handles errors and can send invocation records to a downstream resource to chain together components of your application. The method of invocation has no bearing on the Lambda function's ability to process the compute-heavy workflows.

Use reserved concurrency to account for the compute-heavy workflows

Use provisioned concurrency to account for the compute-heavy workflows

Concurrency is the number of requests that your function is serving at any given time. When your function is invoked, Lambda allocates an instance of it to process the event. When the function code finishes running, it can handle another request. If the function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency. The type of concurrency has no bearing on the Lambda function's ability to process the compute-heavy workflows. So both these options are incorrect.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/configuration-console.html

</details>

---

<a id="t07-q34"></a>
### T07-Q34
*⏭ пропущено · домен: офіційна мітка*

You are a developer working with the AWS CLI to create Lambda functions that contain environment variables. Your functions will require over 50 environment variables consisting of sensitive information of database table names.

What is the total set size/number of environment variables you can create for AWS Lambda?

- ▫️ The total size of all environment variables shouldn't exceed 8 KB. There is no limit on the number of variables
- ▫️ The total size of all environment variables shouldn't exceed 8 KB. The maximum number of variables that can be created is 50
- ▫️ The total size of all environment variables shouldn't exceed 4 KB. The maximum number of variables that can be created is 35
- ✅ The total size of all environment variables shouldn't exceed 4 KB. There is no limit on the number of variables

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The total size of all environment variables shouldn't exceed 4 KB. There is no limit on the number of variables

An environment variable is a pair of strings that are stored in a function's version-specific configuration. The Lambda runtime makes environment variables available to your code and sets additional environment variables that contain information about the function and invocation request. The total size of all environment variables doesn't exceed 4 KB. There is no limit defined on the number of variables that can be used.

Incorrect options:

The total size of all environment variables shouldn't exceed 8 KB. The maximum number of variables that can be created is 50 - Incorrect option. The total size of environment variables cannot exceed 4 KB with no restriction on the number of variables.

The total size of all environment variables shouldn't exceed 8 KB. There is no limit on the number of variables - Incorrect option. The total size of environment variables cannot exceed 4 KB with no restriction on the number of variables.

The total size of all environment variables shouldn't exceed 4 KB. The maximum number of variables that can be created is 35 - Incorrect option. The total size of environment variables cannot exceed 4 KB with no restriction on the number of variables.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html

</details>

---

<a id="t07-q42"></a>
### T07-Q42
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **EventBridge Scheduler.** → [детальніше](../../06-contradictions-and-traps.md#c14)

Your company has a three-year contract with a healthcare provider. The contract states that monthly database backups must be retained for the duration of the contract for compliance purposes. Currently, the limit on backup retention for automated backups, on Amazon Relational Database Service (RDS), does not meet your requirements.

Which of the following solutions can help you meet your requirements?

- ▫️ Enable RDS Read replicas
- ▫️ Enable RDS Multi-AZ
- ✅ Create a cron expression in EventBridge Scheduler, which triggers an AWS Lambda function that triggers the database snapshot
- ▫️ Enable RDS automatic backups

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a cron expression in EventBridge Scheduler, which triggers an AWS Lambda function that triggers the database snapshot - There are multiple ways to run periodic jobs in AWS. Amazon EventBridge with Lambda is the simplest of all solutions. To do this, create a schedule in EventBridge Scheduler and select “Recurring schedule” as the occurrence of the schedule option. You can then use a cron expression as the schedule type. Next, select “Lambda Function” as the Target. Your Lambda will have the necessary code for snapshot functionality.

Incorrect options:

Enable RDS automatic backups - You can enable automatic backups but as of 2020, the retention period is 0 to 35 days.

Enable RDS Read replicas - Amazon RDS server's built-in replication functionality to create a special type of DB instance called a read replica from a source DB instance. Updates made to the source DB instance are asynchronously copied to the read replica. Read replicas are useful for heavy read-only data workloads. These are not suitable for the given use-case.

Enable RDS Multi-AZ - Multi-AZ allows you to create a highly available application with RDS. It does not directly help in database backups or retention periods.

Reference:

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html

https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html

</details>

---

<a id="t07-q51"></a>
### T07-Q51
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Lambda alias weighted.** → [детальніше](../../06-contradictions-and-traps.md#c13)

Recently, you started an online learning platform using AWS Lambda and AWS Gateway API. Your first version was successful, and you began developing new features for the second version. You would like to gradually introduce the second version by routing only 10% of the incoming traffic to the new Lambda version.

Which solution should you opt for?

- ✅ Use AWS Lambda aliases
- ▫️ Deploy your Lambda in a VPC
- ▫️ Use environment variables
- ▫️ Use Tags to distinguish the different versions

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use AWS Lambda aliases - A Lambda alias is like a pointer to a specific Lambda function version. You can create one or more aliases for your AWS Lambda function. Users can access the function version using the alias ARN. An alias can only point to a function version, not to another alias. You can update an alias to point to a new version of the function. Event sources such as Amazon S3 invoke your Lambda function. These event sources maintain a mapping that identifies the function to invoke when events occur. If you specify a Lambda function alias in the mapping configuration, you don't need to update the mapping when the function version changes. This is the right choice for the current requirement.

Incorrect options:

Use Tags to distinguish the different versions - You can tag Lambda functions to organize them by owner, project or department. Tags are freeform key-value pairs that are supported across AWS services for use in filtering resources and adding detail to billing reports. This does not address the given use-case.

Use environment variables - You can use environment variables to store secrets securely and adjust your function's behavior without updating code. An environment variable is a pair of strings that are stored in a function's version-specific configuration. The Lambda runtime makes environment variables available to your code and sets additional environment variables that contain information about the function and invocation request. For example, you can use environment variables to point to test, development or production databases by passing it as an environment variable during runtime. This option does not address the given use-case.

Deploy your Lambda in a VPC - Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a virtual network that you've defined. This adds another layer of security for your entire architecture. Not the right choice for the given scenario.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-tags.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html>

</details>

---

<a id="t08-q01"></a>
### T08-Q01
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Storage)*

A developer is in the process of revising multiple AWS Lambda functions and notes that these functions utilize the same bespoke libraries. The developer intends to centralize these libraries, implement updates with minimal effort, and keep the libraries version controlled.

Which solution aligns with these needs while requiring the least development effort?

- ▫️ Create an Amazon S3 bucket to store all the custom libraries.
- ▫️ Create a custom Amazon Machine Image (AMI) that includes the custom libraries.
- ▫️ Create an AWS CodeCommit repository for storing the custom libraries.
- ✅ Create a Lambda layer including all the custom libraries.

<details><summary>Пояснення (оригінал, EN)</summary>

Lambda layers allow the sharing of code, libraries, or other resources across multiple Lambda functions, making it easier to manage common code across multiple functions. This enables a developer to maintain versioned libraries that can be easily updated and shared, thus requiring the least development effort.

CORRECT: "Create a Lambda layer including all the custom libraries" is the correct answer (as explained above.)

INCORRECT: "Create an AWS CodeCommit repository for storing the custom libraries" is incorrect.

AWS CodeCommit is a version control service hosted by AWS; it is not designed to serve as a central library for Lambda functions.

INCORRECT: "Create a custom Amazon Machine Image (AMI) that includes the custom libraries" is incorrect.

Creating a custom Amazon Machine Image (AMI) is not related to Lambda functions as Lambda runs without requiring a server setup.

INCORRECT: "Create an Amazon S3 bucket to store all the custom libraries" is incorrect.

Amazon S3 buckets are primarily used for storage and not optimal for serving as a centralized library for Lambda functions.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html>

</details>

---

<a id="t08-q19"></a>
### T08-Q19
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to write some code to invoke an AWS Lambda function using the AWS Command Line Interface (CLI). Which option must be specified to cause the function to be invoked asynchronously?

- ✅ Set the –invocation-type option to Event
- ▫️ Set the –qualifier option to Asynchronous
- ▫️ Set the –payload option to Asynchronous
- ▫️ Set the –invocation-type option to Invoke

<details><summary>Пояснення (оригінал, EN)</summary>

Several AWS services, such as Amazon Simple Storage Service (Amazon S3) and Amazon Simple Notification Service (Amazon SNS), invoke functions asynchronously to process events.

When you invoke a function asynchronously, you don't wait for a response from the function code. You hand off the event to Lambda and Lambda handles the rest. You can configure how Lambda handles errors and can send invocation records to a downstream resource to chain together components of your application.

The following code snippet is an example of invoking the “my-function” function asynchronously:

The Developer will therefore need to set the –invocation-type option to Event.

CORRECT: "Set the –invocation-type option to Event " is the correct answer.

INCORRECT: "Set the –invocation-type option to Invoke" is incorrect as this is not valid value for this option.

INCORRECT: "Set the –payload option to Asynchronous" is incorrect as this option is used to provide the JSON blob that you want to provide to your Lambda function as input. You cannot supply “asynchronous” as a value.

INCORRECT: "Set the –qualifier option to Asynchronous" is incorrect as this is used to specify a version or alias to invoke a published version of the function. You cannot supply “asynchronous” as a value.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html> · <https://docs.aws.amazon.com/cli/latest/reference/lambda/invoke.html>

</details>

---

<a id="t08-q20"></a>
### T08-Q20
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

```json
Based on the following AWS CLI command the resulting output, what has happened here?
$ aws lambda invoke --function-name MyFunction --invocation-type Event --payload ewogICJrZXkxIjogInZhbHVlMSIsCiAgImtleTIiOiAidmFsdWUyIiwKICAia2V5MyI6ICJ2YWx1ZTMiCn0= response.json
{
"StatusCode": 202
}
```

- ▫️ An AWS Lambda function has been invoked asynchronously and has not completed successfully
- ✅ An AWS Lambda function has been invoked asynchronously and has completed successfully
- ▫️ An AWS Lambda function has been invoked synchronously and has not completed successfully
- ▫️ An AWS Lambda function has been invoked synchronously and has completed successfully

<details><summary>Пояснення (оригінал, EN)</summary>

Several AWS services, such as Amazon Simple Storage Service (Amazon S3) and Amazon Simple Notification Service (Amazon SNS), invoke functions asynchronously to process events.

When you invoke a function asynchronously, you don't wait for a response from the function code. You hand off the event to Lambda and Lambda handles the rest. You can configure how Lambda handles errors, and can send invocation records to a downstream resource to chain together components of your application.

The following diagram shows clients invoking a Lambda function asynchronously. Lambda queues the events before sending them to the function.

For asynchronous invocation, Lambda places the event in a queue and returns a success response without additional information. A separate process reads events from the queue and sends them to your function. To invoke a function asynchronously, set the invocation type parameter to Event.

In this scenario the Event parameter has been used so we know the function has been invoked asynchronously. For asynchronous invocation the status code 202 indicates a successful execution.

CORRECT: "An AWS Lambda function has been invoked asynchronously and has completed successfully" is the correct answer.

INCORRECT: "An AWS Lambda function has been invoked synchronously and has completed successfully" is incorrect as the Event parameter indicates an asynchronous invocation.

INCORRECT: "An AWS Lambda function has been invoked synchronously and has not completed successfully" is incorrect as the Event parameter indicates an asynchronous invocation (a status code 200 would be a successful execution for a synchronous invocation).

INCORRECT: "An AWS Lambda function has been invoked asynchronously and has not completed successfully" is incorrect as the status code 202 indicates a successful execution.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-async.html>

</details>

---

<a id="t08-q25"></a>
### T08-Q25
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

An application uses AWS Lambda to process many files. The Lambda function takes approximately 3 minutes to process each file and does not return any important data. A Developer has written a script that will invoke the function using the AWS CLI.

What is the FASTEST way to process all the files?

- ▫️ Invoke the Lambda function synchronously with the invocation type Event and process the files in parallel
- ▫️ Invoke the Lambda function synchronously with the invocation type RequestResponse and process the files sequentially
- ▫️ Invoke the Lambda function asynchronously with the invocation type RequestResponse and process the files sequentially
- ✅ Invoke the Lambda function asynchronously with the invocation type Event and process the files in parallel

<details><summary>Пояснення (оригінал, EN)</summary>

You can invoke Lambda functions directly with the Lambda console, the Lambda API, the AWS SDK, the AWS CLI, and AWS toolkits.

You can also configure other AWS services to invoke your function, or you can configure Lambda to read from a stream or queue and invoke your function.

When you invoke a function, you can choose to invoke it synchronously or asynchronously.

• Synchronous invocation:

o You wait for the function to process the event and return a response.

o To invoke a function synchronously with the AWS CLI, use the invoke command.

o The Invocation-type can be used to specify a value of “RequestResponse”. This instructs AWS to execute your Lambda function and wait for the function to complete.

• Asynchronous invocation:

o When you invoke a function asynchronously, you don’t wait for a response from the function code.

o For asynchronous invocation, Lambda handles retries and can send invocation records to a destination.

o To invoke a function asynchronously, set the invocation type parameter to Event.

The fastest way to process all the files is to use asynchronous invocation and process the files in parallel. To do this you should specify the invocation type of Event

CORRECT: "Invoke the Lambda function asynchronously with the invocation type Event and process the files in parallel" is the correct answer.

INCORRECT: "Invoke the Lambda function synchronously with the invocation type Event and process the files in parallel" is incorrect as the invocation type for a synchronous invocation should be RequestResponse.

INCORRECT: "Invoke the Lambda function synchronously with the invocation type RequestResponse and process the files sequentially" is incorrect as this is not the fastest way of processing the files as Lambda will wait for completion of once file before moving on to the next one.

INCORRECT: "Invoke the Lambda function asynchronously with the invocation type RequestResponse and process the files sequentially" is incorrect as the invocation type RequestResponse is used for synchronous invocations.

Джерела: <https://aws.amazon.com/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/>

</details>

---

<a id="t08-q46"></a>
### T08-Q46
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A Developer has deployed an AWS Lambda function and an Amazon DynamoDB table. The function code returns data from the DynamoDB table when it receives a request. The Developer needs to implement a front end that can receive HTTP GET requests and proxy the request information to the Lambda function.

What is the SIMPLEST and most COST-EFFECTIVE solution?

- ▫️ Implement an API Gateway API with a POST method
- ▫️ Implement an Elastic Load Balancer with a Lambda function target
- ✅ Implement an API Gateway API with Lambda proxy integration
- ▫️ Implement an Amazon Cognito User Pool with a Lambda proxy integration

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon API Gateway Lambda proxy integration is a simple, powerful, and nimble mechanism to build an API with a setup of a single API method. The Lambda proxy integration allows the client to call a single Lambda function in the backend. The function accesses many resources or features of other AWS services, including calling other Lambda functions.

In Lambda proxy integration, when a client submits an API request, API Gateway passes to the integrated Lambda function the raw request as-is, except that the order of the request parameters is not preserved. This request data includes the request headers, query string parameters, URL path variables, payload, and API configuration data.

This solution provides a front end that can listen for HTTP GET requests and then proxy them to the Lambda function and is the simplest option to implement and also the most cost-effective.

CORRECT: "Implement an API Gateway API with Lambda proxy integration" is the correct answer.

INCORRECT: "Implement an API Gateway API with a POST method" is incorrect as a GET method should be implemented. A GET method is a request for data whereas a POST method is a request to upload data.

INCORRECT: "Implement an Elastic Load Balancer with a Lambda function target" is incorrect as though you can do this it is not the simplest or most cost-effective solution.

INCORRECT: "Implement an Amazon Cognito User Pool with a Lambda proxy integration" is incorrect as you cannot create Lambda proxy integrations with Cognito.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html>

</details>

---

<a id="t08-q53"></a>
### T08-Q53
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer has written some code that will connect and pull information from several hundred websites. The code needs to run on a daily schedule and execution time will be less than 60 seconds.

Which AWS service will be most suitable and cost-effective?

- ✅ AWS Lambda
- ▫️ Amazon EC2
- ▫️ Amazon ECS Fargate
- ▫️ Amazon API Gateway

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Lambda is a serverless service with a maximum execution time of 900 seconds. This will be the most suitable and cost-effective option for this use case. You can also schedule Lambda functions to run using Amazon CloudWatch Events.

CORRECT: "AWS Lambda" is the correct answer.

INCORRECT: "Amazon ECS Fargate" is incorrect as this is used for running Docker containers and is a better fit for microservices applications rather than running code for a short period of time.

INCORRECT: "Amazon EC2" is incorrect as this would require running EC2 instances which would not be cost-effective.

INCORRECT: "Amazon API Gateway" is incorrect as this service is used for creating APIs, not running code.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html>

</details>

---

<a id="t09-q12"></a>
### T09-Q12
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Мінімум: UpdateItem + GetItem.** → [детальніше](../../06-contradictions-and-traps.md#c17)

A development team is working on an AWS Lambda function that accesses DynamoDB. The Lambda function must do an upsert, that is, it must retrieve an item and update some of its attributes or create the item if it does not exist.

Which of the following represents the solution with MINIMUM IAM permissions that can be used for the Lambda function to achieve this functionality?

- ▫️ dynamodb:UpdateItem, dynamodb:GetItem, dynamodb:PutItem
- ▫️ dynamodb:AddItem, dynamodb:GetItem
- ✅ dynamodb:UpdateItem, dynamodb:GetItem
- ▫️ dynamodb:GetRecords, dynamodb:PutItem, dynamodb:UpdateTable

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

dynamodb:UpdateItem, dynamodb:GetItem - With Amazon DynamoDB transactions, you can group multiple actions together and submit them as a single all-or-nothing TransactWriteItems or TransactGetItems operation.

You can use AWS Identity and Access Management (IAM) to restrict the actions that transactional operations can perform in Amazon DynamoDB. Permissions for Put, Update, Delete, and Get actions are governed by the permissions used for the underlying PutItem, UpdateItem, DeleteItem, and GetItem operations. For the ConditionCheck action, you can use the dynamodb:ConditionCheck permission in IAM policies.

UpdateItem action of DynamoDB APIs, edits an existing item's attributes or adds a new item to the table if it does not already exist. You can put, delete, or add attribute values. You can also perform a conditional update on an existing item (insert a new attribute name-value pair if it doesn't exist, or replace an existing name-value pair if it has certain expected attribute values).

There is no need to inlcude the dynamodb:PutItem action for the given use-case.

So, the IAM policy must include permissions to get and update the item in the DynamoDB table.

Actions defined by DynamoDB:  via - https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html

Incorrect options:

dynamodb:AddItem, dynamodb:GetItem

dynamodb:GetRecords, dynamodb:PutItem, dynamodb:UpdateTable

dynamodb:UpdateItem, dynamodb:GetItem, dynamodb:PutItem

These three options contradict the explanation provided above, so these options are incorrect.

Джерела: <https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html> · <https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/transaction-apis-iam.html> · <https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazondynamodb.html>

</details>

---

<a id="t09-q20"></a>
### T09-Q20
*⏭ пропущено · домен: офіційна мітка*

What steps can a developer take to optimize the performance of a CPU-bound AWS Lambda function and ensure fast response time?

- ▫️ Increase the function's CPU
- ▫️ Increase the function's timeout
- ✅ Increase the function's memory
- ▫️ Increase the function's provisioned concurrency

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Increase the function's memory

Memory is the principal lever available to Lambda developers for controlling the performance of a function. You can configure the amount of memory allocated to a Lambda function, between 128 MB and 10,240 MB. The Lambda console defaults new functions to the smallest setting and many developers also choose 128 MB for their functions.

The amount of memory also determines the amount of virtual CPU available to a function. Adding more memory proportionally increases the amount of CPU, increasing the overall computational power available. If a function is CPU-, network- or memory-bound, then changing the memory setting can dramatically improve its performance.

Incorrect options:

Increase the function's provisioned concurrency

Increase the function's reserved concurrency

In Lambda, concurrency is the number of requests your function can handle at the same time. There are two types of concurrency controls available:

Reserved concurrency – Reserved concurrency guarantees the maximum number of concurrent instances for the function. When a function has reserved concurrency, no other function can use that concurrency. There is no charge for configuring reserved concurrency for a function.

Provisioned concurrency – Provisioned concurrency initializes a requested number of execution environments so that they are prepared to respond immediately to your function's invocations. Note that configuring provisioned concurrency incurs charges to your AWS account.

Neither reserved concurrency nor provisioned concurrency has any impact on the CPU available to a function, so both these options are incorrect

Increase the function's CPU - This is a distractor as you cannot directly increase the CPU available to a function.

Джерела: <https://docs.aws.amazon.com/lambda/latest/operatorguide/computing-power.html> · <https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html>

</details>

---

<a id="t10-q33"></a>
### T10-Q33
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

Every time an Amazon EC2 instance is launched, certain metadata about the instance should be recorded in an Amazon DynamoDB table. The data is gathered and written to the table by an AWS Lambda function.

What is the MOST efficient method of invoking the Lambda function?

- ✅ Create a CloudWatch Event with an event pattern looking for EC2 state changes and a target set to use the Lambda function
- ▫️ Configure detailed monitoring on Amazon EC2 and create an alarm that triggers the Lambda function in initialization
- ▫️ Create a CloudWatch alarm that triggers the Lambda function based on log streams indicating an EC2 state change in CloudWatch logs
- ▫️ Create a CloudTrail trail alarm that triggers the Lambda function based on the RunInstances API action

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon CloudWatch Events delivers a near real-time stream of system events that describe changes in Amazon Web Services (AWS) resources. Using simple rules that you can quickly set up, you can match events and route them to one or more target functions or streams. CloudWatch Events becomes aware of operational changes as they occur. CloudWatch Events responds to these operational changes and takes corrective action as necessary, by sending messages to respond to the environment, activating functions, making changes, and capturing state information.

In this scenario the only workable solution is to create a CloudWatch Event with an event pattern looking for EC2 state changes and a target set to use the Lambda function.

CORRECT: "Create a CloudWatch Event with an event pattern looking for EC2 state changes and a target set to use the Lambda function" is the correct answer.

INCORRECT: "Create a CloudWatch alarm that triggers the Lambda function based on log streams indicating an EC2 state change in CloudWatch logs" is incorrect as Amazon EC2 does not create a log group or log stream by default.

INCORRECT: "Create a CloudTrail trail alarm that triggers the Lambda function based on the RunInstances API action" is incorrect as you would need to create a CloudWatch alarm for CloudTrail events (CloudTrail does not have its own alarm feature).

INCORRECT: "Configure detailed monitoring on Amazon EC2 and create an alarm that triggers the Lambda function in initialization" is incorrect as you cannot trigger a Lambda function on EC2 instances initialization using detailed monitoring (or the EC2 console).

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html>

</details>

---

<a id="t10-q50"></a>
### T10-Q50
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

```json
Based on the following AWS CLI command the resulting output, what has happened here?
$ aws lambda invoke --function-name MyFunction --payload ewogICJrZXkxIjogInZhbHVlMSIsCiAgImtleTIiOiAidmFsdWUyIiwKICAia2V5MyI6ICJ2YWx1ZTMiCn0= response.json
{
"StatusCode": 200
}
```

- ▫️ An AWS Lambda function has been invoked synchronously and has not completed successfully
- ▫️ An AWS Lambda function has been invoked asynchronously and has not completed successfully
- ✅ An AWS Lambda function has been invoked synchronously and has completed successfully
- ▫️ An AWS Lambda function has been invoked asynchronously and has completed successfully

<details><summary>Пояснення (оригінал, EN)</summary>

When you invoke a function synchronously, Lambda runs the function and waits for a response. When the function execution ends, Lambda returns the response from the function's code with additional data, such as the version of the function that was executed. To invoke a function synchronously with the AWS CLI, use the invoke command.

The following diagram shows clients invoking a Lambda function synchronously. Lambda sends the events directly to the function and sends the function's response back to the invoker.

We know the function has been run synchronously as the --invocation-type Event parameter has not been included. Also, the status code 200 indicates a successful execution of a synchronous execution.

CORRECT: "An AWS Lambda function has been invoked synchronously and has completed successfully" is the correct answer.

INCORRECT: "An AWS Lambda function has been invoked synchronously and has not completed successfully" is incorrect as the status code 200 indicates a successful execution.

INCORRECT: "An AWS Lambda function has been invoked asynchronously and has completed successfully" is incorrect as the --invocation-type Event has parameter is not included so this is not an asynchronous invocation.

INCORRECT: "An AWS Lambda function has been invoked asynchronously and has not completed successfully" is incorrect as the --invocation-type Event has parameter is not included so this is not an asynchronous invocation.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/invocation-sync.html>

</details>

---

<a id="t10-q51"></a>
### T10-Q51
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer needs to run some code using Lambda in response to an event and forward the execution result to another application using a pub/sub notification.

How can the Developer accomplish this?

- ✅ Configure a Lambda “on success” destination and route the execution results to Amazon SNS
- ▫️ Configure a CloudWatch Events alarm the triggers based on Lambda execution success and route the execution results to Amazon SQS
- ▫️ Configure a Lambda “on success” destination and route the execution results to Amazon SQS
- ▫️ Configure a CloudWatch Events alarm the triggers based on Lambda execution success and route the execution results to Amazon SNS

<details><summary>Пояснення (оригінал, EN)</summary>

With Destinations, you can send asynchronous function execution results to a destination resource without writing code. A function execution result includes version, timestamp, request context, request payload, response context, and response payload. For each execution status (i.e. Success and Failure), you can choose one destination from four options: another Lambda function, an SNS topic, an SQS standard queue, or EventBridge.

For this scenario, the code will be run by Lambda and the execution result will then be sent to the SNS topic. The application that is subscribed to the SNS topics will then receive the notification.

CORRECT: "Configure a Lambda “on success” destination and route the execution results to Amazon SNS" is the correct answer.

INCORRECT: "Configure a CloudWatch Events alarm the triggers based on Lambda execution success and route the execution results to Amazon SNS" is incorrect as CloudWatch Events is used to track changes in the state of AWS resources. To forward execution results from Lambda a destination should be used.

INCORRECT: "Configure a Lambda “on success” destination and route the execution results to Amazon SQS" is incorrect as SQS is a message queue not a pub/sub notification service.

INCORRECT: "Configure a CloudWatch Events alarm the triggers based on Lambda execution success and route the execution results to Amazon SQS" is incorrect as CloudWatch Events is used to track changes in the state of AWS resources. To forward execution results from Lambda a destination should be used (with an SNS topic).

Джерела: <https://aws.amazon.com/about-aws/whats-new/2019/11/aws-lambda-supports-destinations-for-asynchronous-invocations/>

</details>

---

<a id="t10-q54"></a>
### T10-Q54
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **40×100=4000 > 1000 → ліміт.** → [детальніше](../../06-contradictions-and-traps.md#c18)

A Developer has created an AWS Lambda function in a new AWS account. The function is expected to be invoked 40 times per second and the execution duration will be around 100 seconds. What MUST the Developer do to ensure there are no errors?

- ▫️ Implement error handling within the function code
- ▫️ Implement a Dead Letter Queue to capture invocation errors
- ▫️ Implement tracing with X-Ray
- ✅ Contact AWS Support to increase the concurrent execution limits

<details><summary>Пояснення (оригінал, EN)</summary>

Concurrency is the number of requests that your function is serving at any given time. When your function is invoked, Lambda allocates an instance of it to process the event. When the function code finishes running, it can handle another request. If the function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency.

In this scenario the Lambda function will be invoked 40 times per second and run for 100 seconds. Therefore, there can be up to 4,000 executions running concurrently which is above the default per-region limit of 1,000 concurrent executions.

This can be easily rectified by contacting AWS support and requesting the concurrent execution limit to be increased.

CORRECT: "Contact AWS Support to increase the concurrent execution limits" is the correct answer.

INCORRECT: "Implement error handling within the function code" is incorrect. Though this could be useful it is not something that must be done based on what we know about this scenario.

INCORRECT: "Implement a Dead Letter Queue to capture invocation errors" is incorrect as this would be implemented for message handling requirements.

INCORRECT: "Implement tracing with X-Ray" is incorrect. X-Ray can be used to analyze and debug distributed applications. We don’t know of any specific issues with this function yet so this is not something that must be done.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html>

</details>

---

<a id="t11-q20"></a>
### T11-Q20
*⏭ пропущено · домен: офіційна мітка*

A company is creating a gaming application that will be deployed on mobile devices. The application will send data to a Lambda function-based RESTful API. The application will assign each API request a unique identifier. The volume of API requests from the application can randomly vary at any given time of day. During request throttling, the application might need to retry requests. The API must be able to address duplicate requests without inconsistencies or data loss.

Which of the following would you recommend to handle these requirements?

- ▫️ Persist the unique identifier for each request in an RDS MySQL table. Change the Lambda function to check the table for the identifier before processing the request
- ▫️ Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to send a client error response when the function receives a duplicate request
- ✅ Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to check the table for the identifier before processing the request
- ▫️ Persist the unique identifier for each request in an ElastiCache for Memcached cache. Change the Lambda function to check the cache for the identifier before processing the request

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to check the table for the identifier before processing the request

DynamoDB is a fully managed, serverless, key-value NoSQL database designed to run high-performance applications at any scale. DynamoDB offers built-in security, continuous backups, automated multi-Region replication, in-memory caching, and data import and export tools. On-demand backup and restore allows you to create full backups of your DynamoDB. Point-in-time recovery (PITR) helps protect your DynamoDB tables from accidental write or delete operations. PITR provides continuous backups of your DynamoDB table data, and you can restore that table to any point in time up to the second during the preceding 35 days.

These features ensure that there is no data loss for the application, thereby meeting a key requirement for the given use case. The solution should also be able to address any duplicate requests without inconsistencies, so the Lambda function should be changed to inspect the table for the given identifier and process the request only if the identifier is unique.

DynamoDB Overview:  via - https://aws.amazon.com/dynamodb/

DynamoDB

Incorrect options:

Persist the unique identifier for each request in an ElastiCache for Memcached cache. Change the Lambda function to check the cache for the identifier before processing the request - Memcached is designed for simplicity and it does not offer any snapshot or replication features. This can lead to data loss for applications. Therefore, this option is not the right fit for the given use case.

 via - https://aws.amazon.com/elasticache/redis-vs-memcached/

Persist the unique identifier for each request in an RDS MySQL table. Change the Lambda function to check the table for the identifier before processing the request - DynamoDB is a better fit than RDS MySQL to handle massive traffic spikes for write requests. DynamoDB is a key-value and document database that supports tables of virtually any size with horizontal scaling. DynamoDB scales to more than 10 trillion requests per day and with tables that have more than ten million read and write requests per second and petabytes of data storage. DynamoDB can be used to build applications that need consistent single-digit millisecond performance. MySQL RDS can be scaled vertically, however, it cannot match the performance benefits offered by DynamoDB for the given use case.

Persist the unique identifier for each request in a DynamoDB table. Change the Lambda function to send a client error response when the function receives a duplicate request - The solution should be able to address any duplicates without any inconsistencies. If Lambda sends a client error response upon receiving a duplicate request, it represents an inconsistent response. So this option is incorrect.

Джерела: <https://aws.amazon.com/dynamodb/> · <https://aws.amazon.com/elasticache/redis-vs-memcached/>

</details>

---

<a id="t12-q17"></a>
### T12-Q17
*⏭ пропущено · домен: офіційна мітка*

A company has AWS Lambda functions where each is invoked by other AWS services such as Amazon Kinesis Data Firehose, Amazon API Gateway, Amazon Simple Storage Service, or Amazon EventBridge. What these Lambda functions have in common is that they process heavy workloads such as big data analysis, large file processing, and statistical computations.

What should you do to improve the performance of your AWS Lambda functions without changing your code?

- ▫️ Change the instance type for your Lambda function
- ▫️ Change your Lambda function runtime to use Golang
- ▫️ Increase the Lambda function timeout
- ✅ Increase the RAM assigned to your Lambda function

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Increase the RAM assigned to your Lambda function

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

In the AWS Lambda resource model, you choose the amount of memory you want for your function which allocates proportional CPU power and other resources. This means you will have access to more compute power when you choose one of the new larger settings. To configure the memory for your function, set a value between 128 MB and 10,240 MB in 1-MB increments. At 1,769 MB, a function has the equivalent of one vCPU (one vCPU-second of credits per second). You access these settings when you create a function or update its configuration. The settings are available using the AWS Management Console, AWS CLI, or SDKs.

Therefore, by increasing the amount of memory available to the Lambda functions, you can run the compute-heavy workflows.

Incorrect options:

Change the instance type for your Lambda function - Instance types apply to the EC2 service and not to Lambda function as its a serverless service.

Change your Lambda function runtime to use Golang - This changes programming language which requires code changes, so this option is not correct. Besides, changing the runtime may not even address the performance issues.

Increase the Lambda function timeout - This option would increase the amount of time for which the Lambda function executes, which may help in case you have some heavy processing, but won't help with the actual performance of your Lambda function.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/configuration-console.html

</details>

---

<a id="t12-q48"></a>
### T12-Q48
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Stage variables + aliases.** → [детальніше](../../06-contradictions-and-traps.md#c13)

You are a developer working at a cloud company that embraces serverless. You have performed your initial deployment and would like to work towards adding API Gateway stages and associate them with existing deployments. Your stages will include prod, test, and dev and will need to match a Lambda function variant that can be updated over time.

Which of the following features must you add to achieve this? (select two)

- ▫️ Mapping Templates
- ▫️ Lambda Versions
- ▫️ Lambda X-Ray integration
- ✅ Stage Variables
- ✅ Lambda Aliases

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Stage Variables

Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of an API. They act like environment variables and can be used in your API setup and mapping templates. With deployment stages in API Gateway, you can manage multiple release stages for each API, such as alpha, beta, and production. Using stage variables you can configure an API deployment stage to interact with different backend endpoints.

For example, your API can pass a GET request as an HTTP proxy to the backend web host (for example, http://example.com). In this case, the backend web host is configured in a stage variable so that when developers call your production endpoint, API Gateway calls example.com. When you call your beta endpoint, API Gateway uses the value configured in the stage variable for the beta stage and calls a different web host (for example, beta.example.com).

Lambda Aliases

A Lambda alias is like a pointer to a specific Lambda function version. Users can access the function version using the alias ARN.

Lambda Aliases allow you to create a "mutable" Lambda version that points to whatever version you want in the backend. This allows you to have a "dev", "test", prod" Lambda alias that can remain stable over time.

Incorrect options:

Lambda Versions - Versions are immutable and cannot be updated over time. So this option is not correct.

Lambda X-Ray integration - This is good for tracing and debugging requests so it can be looked at as a good option for troubleshooting issues in the future. This is not the right fit for the given use-case.

Mapping Templates - Mapping template overrides provides you with the flexibility to perform many-to-one parameter mappings; override parameters after standard API Gateway mappings have been applied; conditionally map parameters based on body content or other parameter values; programmatically create new parameters on the fly, and override status codes returned by your integration endpoint. This is not the right fit for the given use-case.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html> · <https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-override-request-response-parameters.html>

</details>

---

<a id="t12-q53"></a>
### T12-Q53
*⏭ пропущено · домен: офіційна мітка*

The development team at an IT company has configured an Application Load Balancer (ALB) with a Lambda function A as the target but the Lambda function A is not able to process any request from the ALB. Upon investigation, the team finds that there is another Lambda function B in the AWS account that is exceeding the concurrency limits.

How can the development team address this issue?

- ✅ Set up reserved concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit
- ▫️ Use an API Gateway instead of an Application Load Balancer (ALB) for Lambda function A
- ▫️ Set up provisioned concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit
- ▫️ Use a Cloudfront Distribution instead of an Application Load Balancer (ALB) for Lambda function A

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Set up reserved concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit

Concurrency is the number of requests that a Lambda function is serving at any given time. If a Lambda function is invoked again while a request is still being processed, another instance is allocated, which increases the function's concurrency.

To ensure that a function can always reach a certain level of concurrency, you can configure the function with reserved concurrency. When a function has reserved concurrency, no other function can use that concurrency. More importantly, reserved concurrency also limits the maximum concurrency for the function, and applies to the function as a whole, including versions and aliases.

Please review this note to understand how reserved concurrency works:

 via - https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html#configuration-concurrency-reserved

Therefore using reserved concurrency for Lambda function B would limit its maximum concurrency and allow Lambda function A to execute without getting throttled.

Incorrect options:

Set up provisioned concurrency for the Lambda function B so that it throttles if it goes above a certain concurrency limit - You should use provisioned concurrency to enable your function to scale without fluctuations in latency. By allocating provisioned concurrency before an increase in invocations, you can ensure that all requests are served by initialized instances with very low latency. Provisioned concurrency is not used to limit the maximum concurrency for a given Lambda function, so this option is incorrect.

Use an API Gateway instead of an Application Load Balancer (ALB) for Lambda function A - This has been added as a distractor as using an API Gateway for Lambda function A has no bearing on limiting the concurrency of Lambda function B, so this option is incorrect.

Use a Cloudfront Distribution instead of an Application Load Balancer (ALB) for Lambda function A - When you associate a CloudFront distribution with a Lambda function (known as Lambda@Edge), CloudFront intercepts requests and responses at CloudFront edge locations and runs the function. Again, this has no bearing on limiting the concurrency of Lambda function B, so this option is incorrect.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html#configuration-concurrency-reserved> · <https://aws.amazon.com/blogs/networking-and-content-delivery/lambda-functions-as-targets-for-application-load-balancers/>

</details>

---

<a id="t12-q55"></a>
### T12-Q55
*⏭ пропущено · домен: офіційна мітка*

A company that specializes in cloud communications platform as a service allows software developers to programmatically use their services to send and receive text messages. The initial platform did not have a scalable architecture as all components were hosted on one server and should be redesigned for high availability and scalability.

Which of the following options can be used to implement the new architecture? (select two)

- ▫️ EBS + RDS
- ▫️ SES + S3
- ▫️ CloudWatch + CloudFront
- ✅ API Gateway + Lambda
- ✅ ALB + ECS

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

ALB + ECS

Amazon Elastic Container Service (ECS) is a highly scalable, high-performance container management service that supports Docker containers and allows you to easily run applications on a managed cluster of Amazon EC2 instances.

How ECS Works:  via - https://aws.amazon.com/ecs/

Elastic Load Balancing automatically distributes incoming application traffic across multiple targets, such as Amazon EC2 instances, containers, IP addresses, and Lambda functions. It can handle the varying load of your application traffic in a single Availability Zone or across multiple Availability Zones.

When you use ECS with a load balancer such as ALB deployed across multiple Availability Zones, it helps provide a scalable and highly available REST API.

API Gateway + Lambda

Amazon API Gateway is a fully managed service that makes it easy for developers to publish, maintain, monitor, and secure APIs at any scale. Using API Gateway, you can create an API that acts as a “front door” for applications to access data, business logic, or functionality from your back-end services, such as EC2 or Lambda functions.

How API Gateway Works:  via - https://aws.amazon.com/api-gateway/

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume.

How Lambda function works:  via - https://aws.amazon.com/lambda/

API Gateway and Lambda help achieve the same purpose integrating some capabilities such as authentication in a serverless fashion, with fully scalable and highly available architectures.

Incorrect options:

SES + S3 - The combination of these services only provide email and object storage services.

CloudWatch + CloudFront - The combination of these services only provide monitoring and fast content delivery network (CDN) services.

EBS + RDS - The combination of these services only provide elastic block storage and database services.

Джерела: <https://aws.amazon.com/getting-started/projects/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/module-4/> · <https://aws.amazon.com/blogs/compute/microservice-delivery-with-amazon-ecs-and-application-load-balancers/>

</details>

---
