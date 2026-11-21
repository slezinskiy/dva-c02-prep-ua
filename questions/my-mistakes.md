# Мої помилки (❌) — повтори насамперед

Список питань, на які в пробних тестах дано неправильну відповідь. Згруповано за доменами.

## Домен 1 — Розробка з використанням сервісів AWS (20)

- [T02-Q37](1-development/apigw.md#t02-q37) · *Amazon API Gateway / AppSync* — …minimize the number of API calls that are required to retrieve the necessary data. Which solution should the developer use to meet these requirements?  
  **✅ GraphQL API on AWS AppSync**
- [T02-Q46](1-development/apigw.md#t02-q46) · *Amazon API Gateway / AppSync* — … amount of traffic, primarily during testing periods. Which method would be the most cost-effective for the developer to implement this REST endpoint?  
  **✅ Utilize AWS API Gateway with an AWS Lambda function as the backend and enable caching in API Gateway.**
- [T06-Q42](1-development/apigw.md#t06-q42) · *Amazon API Gateway / AppSync* — …igin’ header is present on the requested resource. Origin ‘null’ is therefore not allowed access.” What should the Developer do to resolve this issue?  
  **✅ Enable cross-origin resource sharing (CORS) for the method in API Gateway**
- [T06-Q64](1-development/apigw.md#t06-q64) · *Amazon API Gateway / AppSync* — …ives a 403 Forbidden error. Existing users are unaffected and can still call the API. What code updates will grant these new users’ access to the API?  
  **✅ The createUsagePlanKey method must be called to associate the newly created API key with the correct usage pla**
- [T02-Q18](1-development/cache.md#t02-q18) · *ElastiCache / RDS / Aurora* — …ant, natively highly scalable, and any service interruption should not affect the user experience. What is the best option to store the session state?  
  **✅ Store the session state in Amazon ElastiCache**
- [T02-Q33](1-development/cache.md#t02-q33) · *ElastiCache / RDS / Aurora* — …arters uses the data more frequently than the smaller offices. How should the developer configure their databases to meet high availability standards?  
  **✅ Create an Aurora database with the primary database in Virginia and specify the failover to the Aurora replica**
- [T06-Q25](1-development/cache.md#t06-q25) · *ElastiCache / RDS / Aurora* — …n dashboards. The data is sourced from a database and needs to be stored in the cache. What would be the ideal caching mechanism for this requirement?  
  **✅ A write-through cache**
- [T06-Q43](1-development/cache.md#t06-q43) · *ElastiCache / RDS / Aurora* — … configured ElastiCache to update the cache immediately after any writes to the primary database. What will be the result of this approach to caching?  
  **✅ The cache will become large and expensive because the infrequently requested data is also written to the cache**
- [T02-Q23](1-development/containers.md#t02-q23) · *ECS / ECR / Fargate* — …ST way the Developer can configure the task definition to ensure the web services run correctly and there are no port conflicts on the host instances?  
  **✅ Specify port 80 for the container port and port 0 for the host port**
- [T02-Q30](1-development/containers.md#t02-q30) · *ECS / ECR / Fargate* — …oper needs to choose the best task placement strategy to MINIMIZE the number of instances that are used. Which task placement strategy should be used?  
  **✅ binpack**
- [T02-Q49](1-development/containers.md#t02-q49) · *ECS / ECR / Fargate* — …nment variables that must be passed to a container for the application to initialize. How should the environment variables be passed to the container?  
  **✅ Use advanced container definition parameters and define environment variables under the environment parameter **
- [T02-Q14](1-development/dynamodb.md#t02-q14) · *Amazon DynamoDB* — … table in the shortest possible time whilst ensuring the normal workload is not affected. How would the Developer optimize this scan cost-effectively?  
  **✅ Use the Parallel Scan API operation and limit the rate.**
- [T02-Q35](1-development/dynamodb.md#t02-q35) · *Amazon DynamoDB* — …onsuming a lot of provisioned throughput. What technique can a Developer use to minimize the impact of the scan on the table's provisioned throughput?  
  **✅ Set a smaller page size for the scan**
- [T06-Q34](1-development/edge.md#t06-q34) · *CloudFront / Route 53 / WAF* — …minimum amount of resources. Which CloudFront event type should the Developer use to invoke the Lambda@Edge function that contains the redirect logic?  
  **✅ Origin response**
- [T02-Q04](1-development/lambda.md#t02-q04) · *AWS Lambda* — …eveloper must implement the upload functionality with the least possible change to the application code. Which solution BEST meets these requirements?  
  **✅ Use the AWS SDK for Python that is installed in the Lambda execution environment.**
- [T02-Q36](1-development/lambda.md#t02-q36) · *AWS Lambda* — …unction executes 20 times per second and takes 20 seconds to complete each execution. How many concurrent executions will the Lambda function require?  
  **✅ 400**
- [T02-Q43](1-development/network.md#t02-q43) · *EC2 / Auto Scaling / ELB / VPC / EBS / EFS* — …nstances and an Elastic Load Balancer. The Developer needs to ensure that if an EC2 instance fails session data is not lost. How can this be achieved?  
  **✅ Use Amazon DynamoDB to perform scalable session handling**
- [T02-Q57](1-development/network.md#t02-q57) · *EC2 / Auto Scaling / ELB / VPC / EBS / EFS* — …. A developer needs a store for session data so it can be reliably served across multiple requests. Where is the best place to store the session data?  
  **✅ Write the data to an Amazon ElastiCache cluster.**
- [T06-Q31](1-development/network.md#t06-q31) · *EC2 / Auto Scaling / ELB / VPC / EBS / EFS* — … The Developer needs to update the application to ensure the most up-to-date data is retrieved for all queries. How can the Developer accomplish this?  
  **✅ Set the ConsistentRead parameter to true when calling GetItem.**
- [T06-Q46](1-development/network.md#t06-q46) · *EC2 / Auto Scaling / ELB / VPC / EBS / EFS* — …times retrieves old data. The Developer wants to correct this by making sure the reads are strongly consistent. How can the Developer accomplish this?  
  **✅ Set ConsistentRead to true when calling GetItem**

## Домен 2 — Безпека (11)

- [T02-Q26](2-security/cognito.md#t02-q26) · *Amazon Cognito* — …ith the SAML directory and authorize each employee to access only their own data when using the application. Which approach should the Developer take?  
  **✅ Use an Amazon Cognito identity pool, federate with the SAML provider, and use a trust policy with an IAM condi**
- [T06-Q60](2-security/cognito.md#t06-q60) · *Amazon Cognito* — …source but wants to automatically block all sign-in attempts if the risk level is elevated. Which Amazon Cognito feature will meet these requirements?  
  **✅ Adaptive authentication.**
- [T06-Q32](2-security/containers.md#t06-q32) · *ECS / ECR / Fargate* — … bin packing the containers based on memory reservation. Which configuration would allow the Development team to meet these requirements MOST securely  
  **✅ Create four distinct IAM roles, each containing the required permissions for the associated ECS services, then**
- [T02-Q21](2-security/iam.md#t02-q21) · *IAM / STS / Organizations* — …elopers have been added to a group. What should an administrator do to provide the team with access whilst following the principal of least privilege?  
  **✅ Create a customer managed policy with read only access to DynamoDB and specify the ARN of the table for the “R**
- [T02-Q31](2-security/iam.md#t02-q31) · *IAM / STS / Organizations* — …apacity during deployment while using the existing instances. Which deployment policy will meet these requirements while using the existing instances?  
  **✅ Rolling with additional batch**
- [T02-Q32](2-security/kms.md#t02-q32) · *KMS / шифрування / сертифікати* — …se an IoT device. How can secure communication protocols be established over the internet to ensure the security of the IoT devices during the launch?  
  **✅ Use AWS Certificate Manager (ACM) to provide TLS secured communications to IoT devices and deploy X.509 certif**
- [T02-Q38](2-security/kms.md#t02-q38) · *KMS / шифрування / сертифікати* — …AWS CloudTrail. - Private certificates. - Subordinate certificate authorities (CAs). Which solution should the company use to meet these requirements?  
  **✅ AWS Private Certificate Authority.**
- [T06-Q57](2-security/kms.md#t06-q57) · *KMS / шифрування / сертифікати* — …lead has requested to optimize performance and reduce latency as much as possible. How can a developer meet the compliance and team lead requirements?  
  **✅ Set the API Cache for full request caching and adjust the settings for data encryption at rest and in transit.**
- [T06-Q61](2-security/kms.md#t06-q61) · *KMS / шифрування / сертифікати* — …ificate for their domain via AWS Certificate Manager (ACM). It was validated using DNS validation. What will ACM do prior to expiration? (Select TWO.)  
  **✅ If the domain is not validated, it will send Health events or EventBridge events to notify the domain owner pr / If the certificate is being used by an AWS service and ACM-provided CNAME records are accessible via the publi**
- [T06-Q39](2-security/secrets.md#t06-q39) · *Secrets Manager / Parameter Store* — …nd production environments. What is the most efficient method for the programmer to access these variables with minimal modifications to the solution?  
  **✅ Utilize AWS Secrets Manager to store these variables and retrieve them directly within the application using A**
- [T06-Q44](2-security/secrets.md#t06-q44) · *Secrets Manager / Parameter Store* — …A developer wants to use a cron job to schedule key rotation for an Amazon RDS database. What steps should the developer take to complete this task?  
  **✅ Use AWS Secrets Manager to create a managed rotation under the Rotation Schedule and schedule the time for the**

## Домен 3 — Розгортання (16)

- [T02-Q03](3-deployment/beanstalk.md#t02-q03) · *AWS Elastic Beanstalk* — … that will take care of capacity provisioning, load balancing, and auto-scaling. Which combination of services should the Developer use? (Select TWO.)  
  **✅ AWS Elastic Beanstalk / Amazon RDS with PostrgreSQL**
- [T02-Q50](3-deployment/beanstalk.md#t02-q50) · *AWS Elastic Beanstalk* — … a source bundle which will be uploaded using the console. Which of the following are valid requirements for creating the source bundle? (Select TWO.)  
  **✅ Must not include a parent folder or top-level directory. / Must not exceed 512 MB.**
- [T06-Q49](3-deployment/beanstalk.md#t06-q49) · *AWS Elastic Beanstalk* — …ompany cannot tolerate any website downtime. If the application update fails, rollback should be fast and easy. What deployment method should be used?  
  **✅ Immutable**
- [T02-Q28](3-deployment/cicd.md#t02-q28) · *CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify* — …da update using AWS CodeDeploy. In the appspec.yaml file, which of the following is a valid structure for the order of hooks that should be specified?  
  **✅ BeforeAllowTraffic &gt; AfterAllowTraffic**
- [T02-Q56](3-deployment/cicd.md#t02-q56) · *CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify* — …ke to migrate their application code to AWS CodeCommit. What needs to be created before they can migrate a cloned repository to CodeCommit over HTTPS?  
  **✅ A set of Git credentials generated with IAM**
- [T02-Q58](3-deployment/cicd.md#t02-q58) · *CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify* — … and AppSpec file. The developer needs to use an AWS Lambda function for update validation before deployment. Which solution meets these requirements?  
  **✅ Add a listener to the ALB. Update the AppSpec file to link the Lambda function to the BeforeAllowTraffic lifec**
- [T06-Q07](3-deployment/cicd.md#t06-q07) · *CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify* — …ing AWS Cloud9 to build a new application. The developer wants to spend minimal time configuring resources. What solution should the developer choose?  
  **✅ Create an AWS Cloud9 Elastic Compute Cloud (EC2) environment that will provision many of the resources and man**
- [T06-Q38](3-deployment/cicd.md#t06-q38) · *CodeCommit / CodeBuild / CodeDeploy / CodePipeline / Amplify* — …cation code from a GitHub repository to AWS CodeCommit. What needs to be created before they can migrate a cloned repository to CodeCommit over HTTPS?  
  **✅ A set of credentials generated from IAM**
- [T02-Q11](3-deployment/containers.md#t02-q11) · *ECS / ECR / Fargate* — …n ECS using AWS CodeDeploy. The Developer needs to complete the code update quickly. Which of the following deployment types should the Developer use?  
  **✅ Blue/green**
- [T02-Q01](3-deployment/iac.md#t02-q01) · *CloudFormation / SAM / CDK* — …xist. A developer is tasked with addressing this error through a method that minimizes operational complexity. Which action should the developer take?  
  **✅ Copy the AMI from the us-east-1 Region to the us-west-1 Region and use the new AMI ID in the CloudFormation te**
- [T06-Q21](3-deployment/iac.md#t06-q21) · *CloudFormation / SAM / CDK* — …n deployment: DELETE_FAILED (The following resource(s) failed to delete: (sg-11223344).) Which action should the developer take to resolve this error?  
  **✅ Modify the CloudFormation template to retain the security group resource. Then manually delete the resource af**
- [T06-Q50](3-deployment/iac.md#t06-q50) · *CloudFormation / SAM / CDK* — …or the DB instance needs to be auto generated. What approach can be taken to fulfill these prerequisites with the minimum possible development effort?  
  **✅ Use AWS Secrets Manager via the AWS SDK to generate a secure string. Use a dynamic reference to create the DB **
- [T06-Q51](3-deployment/iac.md#t06-q51) · *CloudFormation / SAM / CDK* — …aking updates to a current application using AWS Cloud Development Kit (CDK). How can the developer revert to a previous version if there is an error?  
  **✅ Only make changes in the deployment phase, and never during synthesis.**
- [T06-Q63](3-deployment/iac.md#t06-q63) · *CloudFormation / SAM / CDK* — …ly determining the AWS Region where it is being deployed. What is the MOST efficient method to ascertain the Region during deployment of the template?  
  **✅ Utilize the AWS::Region pseudo parameter.**
- [T02-Q45](3-deployment/lambda.md#t02-q45) · *AWS Lambda* — …otifications for state changes of each of the actions in the stages. Which steps must be taken to associate the Lambda function with the event source?  
  **✅ Create an Amazon CloudWatch Events rule that uses CodePipeline as an event source**
- [T02-Q39](3-deployment/s3.md#t02-q39) · *Amazon S3 / Athena* — …are package is stored in a local file system and must be deployed to Amazon EC2 instances. How can the application be deployed onto the EC2 instances?  
  **✅ Upload the bundle to an Amazon S3 bucket and specify the S3 location when doing a deployment using AWS CodeDep**

## Домен 4 — Усунення несправностей та оптимізація (12)

- [T06-Q08](4-troubleshooting/apigw.md#t06-q08) · *Amazon API Gateway / AppSync* — …rrors and execution traces for the Lambda authorizer. Which combination of actions should the Developer take to meet these requirements? (Select TWO.)  
  **✅ Enable API Gateway access logs. / Enable API Gateway execution logging.**
- [T02-Q62](4-troubleshooting/cache.md#t02-q62) · *ElastiCache / RDS / Aurora* — …king place on a primary database built on Amazon Aurora with MySQL. What can a developer do to improve the read scaling of the database? (Select TWO.)  
  **✅ Create Aurora Replicas in same cluster as the primary database instance. / Create a separate Aurora MySQL cluster and configure binlog replication.**
- [T02-Q13](4-troubleshooting/containers.md#t02-q13) · *ECS / ECR / Fargate* — …tion container needs to share data with another container to collect logs and metrics. What should the Development team do to meet these requirements?  
  **✅ Create one task definition. Specify both containers in the definition. Mount a shared volume between those two**
- [T02-Q64](4-troubleshooting/dynamodb.md#t02-q64) · *Amazon DynamoDB* — …ould the developer do to reduce the likelihood of these throttling issues without significantly over-provisioning the DynamoDB table's write capacity?  
  **✅ Implement exponential backoff in the Lambda function's error handling code to retry failed write operations.**
- [T02-Q08](4-troubleshooting/lambda.md#t02-q08) · *AWS Lambda* — …ompany wants to ensure resilience in the application, with no unscheduled downtime for the database. Which solution would best fit these requirements?  
  **✅ Use Amazon RDS Proxy and update the Lambda function to connect to the proxy.**
- [T06-Q54](4-troubleshooting/lambda.md#t06-q54) · *AWS Lambda* — … result and log files need to be accessible by other AWS services and on-premises resources. What should the developer use to meet these requirements?  
  **✅ Keep the result and log files in Amazon Elastic File System (EFS) accessible by Lambda functions.**
- [T02-Q47](4-troubleshooting/messaging.md#t02-q47) · *SQS / SNS / EventBridge / SES* — …hat the application can automatically retry processing them. What should the developer do to accomplish this goal with the LEAST operational overhead?  
  **✅ Configure a redrive policy on an Amazon SQS queue. Set the dead-letter queue as an event source to the Lambda **
- [T06-Q28](4-troubleshooting/messaging.md#t06-q28) · *SQS / SNS / EventBridge / SES* — …ement is to notify the support team via an existing Amazon SNS topic when the error rate of this API surpasses 10% per hour. How can this be achieved?  
  **✅ Implement Amazon CloudWatch Metrics with a custom threshold and link it to the existing SNS topic.**
- [T02-Q55](4-troubleshooting/network.md#t02-q55) · *EC2 / Auto Scaling / ELB / VPC / EBS / EFS* — … that logs all requests to a log file. How can the developer ensure the client public IP addresses are captured in the log files on the EC2 instances?  
  **✅ Configure the HTTP server to add the x-forwarded-for request header to the logs.**
- [T02-Q02](4-troubleshooting/observability.md#t02-q02) · *CloudWatch / X-Ray / CloudTrail* — …nd. The Developer must be notified within 30 seconds if the application experiences any issues. What should the Developer do to meet this requirement?  
  **✅ Configure a high-resolution CloudWatch alarm and use Amazon SNS to send the alert.**
- [T02-Q34](4-troubleshooting/observability.md#t02-q34) · *CloudWatch / X-Ray / CloudTrail* — … a cron job on an Amazon EC2 instance that sends status information about the application to Amazon CloudWatch. Which method should the Developer use?  
  **✅ Use the AWS CLI put-metric-data command.**
- [T02-Q54](4-troubleshooting/stepfn.md#t02-q54) · *AWS Step Functions* — … be included along with the error message in the state output. Which coding practice can preserve both the original input and the error for the state?  
  **✅ Use ResultPath in a Catch statement to include the original input with the error.**
