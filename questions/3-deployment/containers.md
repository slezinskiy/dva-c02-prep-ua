# ECS / ECR / Fargate

**Домен 3 — Розгортання** · питань: 16  
Теорія: [03-domain3-deployment.md](../../03-domain3-deployment.md#containers) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q17"></a>
### T01-Q17
*⏭ пропущено · домен: офіційна мітка*

Your company is shifting towards Elastic Container Service (ECS) to deploy applications. The process should be automated using the AWS CLI to create a service where at least ten instances of a task definition are kept running under the default cluster.

Which of the following commands should be executed?

- ✅ aws ecs create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10
- ▫️ docker-compose create ecs-simple-service
- ▫️ aws ecs run-task --cluster default --task-definition ecs-demo
- ▫️ aws ecr create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

aws ecs create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10

To create a new service you would use this command which creates a service in your default region called ecs-simple-service. The service uses the ecs-demo task definition and it maintains 10 instantiations of that task.

Incorrect options:

aws ecr create-service --service-name ecs-simple-service --task-definition ecs-demo --desired-count 10 - This command is referencing a different service called Amazon Elastic Container Registry (ECR) which's is a fully-managed Docker container registry

docker-compose create ecs-simple-service - This is a docker command to create containers for a service.

aws ecs run-task --cluster default --task-definition ecs-demo - This is a valid command but used for starting a new task using a specified task definition.

Reference:

https://docs.aws.amazon.com/cli/latest/reference/ecs/create-service.html

</details>

---

<a id="t01-q65"></a>
### T01-Q65
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Застаріле: Multicontainer Docker (AL1) знято → ECS-платформа EB.** → [детальніше](../../07-outdated-and-wrong-answers.md#o14)

Your client wants to deploy a service on EC2 instances, and as EC2 instances are added into an ASG, each EC2 instance should be running 3 different Docker Containers simultaneously.

What Elastic Beanstalk platform should they choose?

- ▫️ Third-party platform
- ▫️ Docker single-container platform
- ▫️ Custom platform
- ✅ Docker multi-container platform

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Docker multi-container platform

Docker is a container platform that allows you to define your software stack and store it in an image that can be downloaded from a remote repository. Use the Multicontainer Docker platform if you need to run multiple containers on each instance. The Multicontainer Docker platform does not include a proxy server. Elastic Beanstalk uses Amazon Elastic Container Service (Amazon ECS) to coordinate container deployments to multi-container Docker environments.

Incorrect options:

Docker single-container platform - Docker is a container platform that allows you to define your software stack and store it in an image that can be downloaded from a remote repository. Use the Single Container Docker platform if you only need to run a single Docker container on each instance in your environment. The single container platform includes an Nginx proxy server.

Custom Platform - Elastic Beanstalk supports custom platforms. A custom platform provides more advanced customization than a custom image in several ways. A custom platform lets you develop an entirely new platform from scratch, customizing the operating system, additional software, and scripts that Elastic Beanstalk runs on platform instances. This flexibility enables you to build a platform for an application that uses a language or other infrastructure software, for which Elastic Beanstalk doesn't provide a managed platform. Compare that to custom images, where you modify an Amazon Machine Image (AMI) for use with an existing Elastic Beanstalk platform, and Elastic Beanstalk still provides the platform scripts and controls the platform's software stack. Besides, with custom platforms, you use an automated, scripted way to create and maintain your customization, whereas with custom images you make the changes manually over a running instance.

Third Party Platform - This is a made-up option.

Reference:

https://docs.aws.amazon.com/elasticbeanstalk/latest/platforms/platforms-supported.html#platforms-supported.mcdocker

</details>

---

<a id="t02-q11"></a>
### T02-Q11
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is setting up a code update to Amazon ECS using AWS CodeDeploy. The Developer needs to complete the code update quickly. Which of the following deployment types should the Developer use?

- ▫️ Linear
- ❌ In-place
- ✅ Blue/green
- ▫️ Canary

<details><summary>Пояснення (оригінал, EN)</summary>

CodeDeploy provides two deployment type options – in-place and blue/green. Note that AWS Lambda and Amazon ECS deployments cannot use an in-place deployment type.

The Blue/green deployment type on an Amazon ECS compute platform works like this:

Traffic is shifted from the task set with the original version of an application in an Amazon ECS service to a replacement task set in the same service.

You can set the traffic shifting to linear or canary through the deployment configuration.

The protocol and port of a specified load balancer listener is used to reroute production traffic.

During a deployment, a test listener can be used to serve traffic to the replacement task set while validation tests are run.

CORRECT: "Blue/green" is the correct answer.

INCORRECT: "Canary" is incorrect as this is a traffic shifting option, not a deployment type. Traffic is shifted in two increments.

INCORRECT: "Linear" is incorrect as this is a traffic shifting option, not a deployment type. Traffic is shifted in two increments.

INCORRECT: "In-place" is incorrect as AWS Lambda and Amazon ECS deployments cannot use an in-place deployment type.

Джерела: <https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments.html>

</details>

---

<a id="t02-q51"></a>
### T02-Q51
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A developer is using AWS CodeBuild to build an application into a Docker image. The buildspec file is used to run the application build. The developer needs to push the Docker image to an Amazon ECR repository only upon the successful completion of each build.

- ▫️ Add a post_build phase to the buildspec file that uses the finally block to push the Docker image.
- ✅ Add a post_build phase to the buildspec file that uses the commands block to push the Docker image.
- ▫️ Add a post_build phase to the buildspec file that uses the artifacts sequence to find the build artifacts and push to Amazon ECR.
- ▫️ Add an install phase to the buildspec file that uses the commands block to push the Docker image.

<details><summary>Пояснення (оригінал, EN)</summary>

The post_build phase is an optional sequence. It represents the commands, if any, that CodeBuild runs after the build. For example, you might use Maven to package the build artifacts into a JAR or WAR file, or you might push a Docker image into Amazon ECR. Then you might send a build notification through Amazon SNS.

Here is an example of a buildspec file with a post_build phase that pushes a Docker image to Amazon ECR:

CORRECT: "Add a post_build phase to the buildspec file that uses the commands block to push the Docker image" is the correct answer (as explained above.)

INCORRECT: "Add a post_build phase to the buildspec file that uses the finally block to push the Docker image" is incorrect.

Commands specified in a finally block are run after commands in the commands block. The commands in a finally block are run even if a command in the commands block fails. This would not be ideal as this would push the image to ECR even if commands in previous sequences failed.

INCORRECT: "Add an install phase to the buildspec file that uses the commands block to push the Docker image" is incorrect.

These are commands that are run during installation. The develop would want to push the image only after all installations have succeeded. Therefore, the post_build phase should be used.

INCORRECT: "Add a post_build phase to the buildspec file that uses the artifacts sequence to find the build artifacts and push to Amazon ECR" is incorrect.

The artifacts sequence is not required if you are building and pushing a Docker image to Amazon ECR, or you are running unit tests on your source code, but not building it.

Джерела: <https://docs.aws.amazon.com/codebuild/latest/userguide/sample-docker.html> · <https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html>

</details>

---

<a id="t04-q13"></a>
### T04-Q13
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **ECR: get-login-password.** → [детальніше](../../06-contradictions-and-traps.md#c16)

A developer has created a Docker image and uploaded it to an Amazon Elastic Container Registry (ECR) repository. How can the developer pull the image to his workstation using the docker client?

- ▫️ Run the docker pull command specifying the image name using registry/repository[:tag]
- ✅ Run aws ecr get-login-password use the output to login in then issue a docker pull command specifying the image name using registry/repository[:tag]
- ▫️ Run docker login with an IAM key pair then issue a docker pull command specifying the image name using registry/repository[@digest]
- ▫️ Run aws ecr describe-images --repository-name repositoryname

<details><summary>Пояснення (оригінал, EN)</summary>

If you would like to run a Docker image that is available in Amazon ECR, you can pull it to your local environment with the docker pull command. You can do this from either your default registry or from a registry associated with another AWS account.

Docker CLI does not support standard AWS authentication methods, so client authentication must be handled so that ECR knows who is requesting to push or pull an image. To do this you can issue the aws ecr get-login or aws ecr get-login-password (AWS CLI v2) and then use the output to login using docker login and then issue a docker pull command specifying the image name using registry/repository[:tag]

CORRECT: "Run aws ecr get-login-password use the output to login in then issue a docker pull command specifying the image name using registry/repository[:tag]" is the correct answer.

INCORRECT: "Run the docker pull command specifying the image name using registry/repository[:tag]" is incorrect as you first need to authenticate to get an access token so you can pull the image down.

INCORRECT: "Run aws ecr describe-images --repository-name repositoryname" is incorrect as this would just list the images available in the repository.

INCORRECT: "Run docker login with an IAM key pair then issue a docker pull command specifying the image name using registry/repository[@digest]" is incorrect as you cannot run docker login with an IAM key pair.

Джерела: <https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-pull-ecr-image.html>

</details>

---

<a id="t05-q24"></a>
### T05-Q24
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **ECR: get-login-password.** → [детальніше](../../06-contradictions-and-traps.md#c16)

AWS CodeBuild builds code for an application, creates a Docker image, pushes the image to Amazon Elastic Container Registry (ECR), and tags the image with a unique identifier.

If the Developers already have AWS CLI configured on their workstations, how can the Docker images be pulled to the workstations?

- ▫️ Run the output of the following: aws ecr get-download-url-for-layer, and then run: docker pull REPOSITORY URI : TAG
- ✅ Run the output of the following: aws ecr get-login-password, and then run: docker pull REPOSITORY URI : TAG
- ▫️ Run the following: aws ecr get-login-password, and then run: docker pull REPOSITORY URI : TAG
- ▫️ Run the following: docker pull REPOSITORY URI : TAG

<details><summary>Пояснення (оригінал, EN)</summary>

If you would like to run a Docker image that is available in Amazon ECR, you can pull it to your local environment with the docker pull command. You can do this from either your default registry or from a registry associated with another AWS account.

Docker CLI does not support standard AWS authentication methods, so client authentication must be handled so that ECR knows who is requesting to push or pull an image. To do this you can issue the aws ecr get-login-password AWS CLI command and then use the output to login using docker login and then issue a docker pull command specifying the image name using registry/repository[:tag].

CORRECT: "Run the output of the following: aws ecr get-login-password, and then run docker pull REPOSITORY URI : TAG" is the correct answer.

INCORRECT: "Run the following: docker pull REPOSITORY URI : TAG" is incorrect as the Developers first need to authenticate before they can pull the image.

INCORRECT: "Run the following: aws ecr get-login-password, and then run: docker pull REPOSITORY URI : TAG" is incorrect. The Developers need to not just run the login command but run the output of the login command which contains the authentication token required to log in.

INCORRECT: "Run the output of the following: aws ecr get-download-url-for-layer, and then run docker pull REPOSITORY URI : TAG" is incorrect as this command retrieves a pre-signed Amazon S3 download URL corresponding to an image layer.

Джерела: <https://docs.aws.amazon.com/AmazonECR/latest/userguide/Registries.html#registry_auth> · <https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-pull-ecr-image.html>

</details>

---

<a id="t05-q56"></a>
### T05-Q56
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A developer must deploy an update to Amazon ECS using AWS CodeDeploy. The deployment should expose 10% of live traffic to the new version. Then after a period of time, route all remaining traffic to the new version.

Which ECS deployment should the company use to meet these requirements?

- ✅ Blue/green with canary
- ▫️ Rolling update
- ▫️ Blue/green with all at once
- ▫️ Blue/green with linear

<details><summary>Пояснення (оригінал, EN)</summary>

The blue/green deployment type uses the blue/green deployment model controlled by CodeDeploy. This deployment type enables you to verify a new deployment of a service before sending production traffic to it.

There are three ways traﬃc can shift during a blue/green deployment:

• Canary — Traﬃc is shifted in two increments. You can choose from predeﬁned canary options that specify the percentage of traﬃc shifted to your updated task set in the ﬁrst increment and the interval, in minutes, before the remaining traﬃc is shifted in the second increment.

• Linear — Traﬃc is shifted in equal increments with an equal number of minutes between each increment. You can choose from predeﬁned linear options that specify the percentage of traﬃc shifted in each increment and the number of minutes between each increment.

• All-at-once — All traﬃc is shifted from the original task set to the updated task set all at once.

The best choice for this use case would be to use the canary traffic shifting strategy. You can see the predefined canary options in the table below:

CORRECT: "Blue/green with canary" is the correct answer (as explained above.)

INCORRECT: "Blue/green with linear" is incorrect.

With this option traffic is shifted in equal increments with an equal amount of time between increments.

INCORRECT: "Blue/green with all at once" is incorrect.

With this option all traffic is shifted at once.

INCORRECT: "Rolling update" is incorrect.

This is a native ECS deployment model. It does not deploy in two increments with 10% first.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/deployment-type-bluegreen.html>

</details>

---

<a id="t06-q65"></a>
### T06-Q65
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Compute)*

> ⚠️ **X-Ray на ECS EC2 → daemon-контейнер.** → [детальніше](../../06-contradictions-and-traps.md#c19)

A Developer is deploying an application using Docker containers running on the Amazon Elastic Container Service (ECS). The Developer is testing application latency and wants to capture trace information between the microservices.

Which solution will meet these requirements?

- ▫️ Install the AWS X-Ray daemon on each of the Amazon ECS instances.
- ▫️ Install the AWS X-Ray daemon locally on an Amazon EC2 instance and instrument the Amazon ECS microservices using the X-Ray SDK.
- ▫️ Install the Amazon CloudWatch agent on the container image. Use the CloudWatch SDK to publish custom metrics from each of the microservices.
- ✅ Create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to the Amazon ECS cluster.

<details><summary>Пояснення (оригінал, EN)</summary>

In Amazon ECS, create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to your Amazon ECS cluster. You can use port mappings and network mode settings in your task definition file to allow your application to communicate with the daemon container.

CORRECT: "Create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to the Amazon ECS cluster." is the correct answer.

INCORRECT: "Install the Amazon CloudWatch agent on the container image. Use the CloudWatch SDK to publish custom metrics from each of the microservices" is incorrect. The CloudWatch agent does not capture trace information between Docker containers.

INCORRECT: "Install the AWS X-Ray daemon on each of the Amazon ECS instances" is incorrect. The X-Ray daemon must be installed on the Docker containers, not the ECS hosts.

INCORRECT: "Install the AWS X-Ray daemon locally on an Amazon EC2 instance and instrument the Amazon ECS microservices using the X-Ray SDK" is incorrect. You cannot trace Docker microservices from an Amazon EC2 instance.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-ecs.html>

</details>

---

<a id="t08-q39"></a>
### T08-Q39
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team is creating a microservices application running on Amazon ECS. The release process workflow of the application requires a manual approval step before the code is deployed into the production environment.
What is the BEST way to achieve this using AWS CodePipeline?

- ▫️ Use an Amazon SNS notification from the deployment stage
- ✅ Use an approval action in a stage before deployment
- ▫️ Disable the stage transition to allow manual approval
- ▫️ Disable a stage just prior the deployment stage

<details><summary>Пояснення (оригінал, EN)</summary>

In AWS CodePipeline, you can add an approval action to a stage in a pipeline at the point where you want the pipeline execution to stop so that someone with the required AWS Identity and Access Management permissions can approve or reject the action.

If the action is approved, the pipeline execution resumes. If the action is rejected—or if no one approves or rejects the action within seven days of the pipeline reaching the action and stopping—the result is the same as an action failing, and the pipeline execution does not continue.

In this scenario, the manual approval stage would be placed in the pipeline before the deployment stage that deploys the application update into production:

Therefore, the best answer is to use an approval action in a stage before deployment to production

CORRECT: "Use an approval action in a stage before deployment" is the correct answer.

INCORRECT: "Use an Amazon SNS notification from the deployment stage" is incorrect as this would send a notification when the actual deployment is already occurring.

INCORRECT: "Disable the stage transition to allow manual approval" is incorrect as this requires manual intervention as could be easily missed and allow the deployment to continue.

INCORRECT: "Disable a stage just prior the deployment stage" is incorrect as disabling the stage prior would prevent that stage from running, which may be necessary (could be the build / test stage). It is better to use an approval action in a stage in the pipeline before the deployment occurs

Джерела: <https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals.html>

</details>

---

<a id="t09-q24"></a>
### T09-Q24
*⏭ пропущено · домен: офіційна мітка*

A developer wants to package the code and dependencies for the application-specific Lambda functions as container images to be hosted on Amazon Elastic Container Registry (ECR).

Which of the following options are correct for the given requirement? (Select two)

- ▫️ You can deploy Lambda function as a container image, with a maximum size of 15 GB
- ✅ AWS Lambda service does not support Lambda functions that use multi-architecture container images
- ▫️ You can test the containers locally using the Lambda Runtime API
- ✅ To deploy a container image to Lambda, the container image must implement the Lambda Runtime API
- ▫️ Lambda supports both Windows and Linux-based container images

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

To deploy a container image to Lambda, the container image must implement the Lambda Runtime API - To deploy a container image to Lambda, the container image must implement the Lambda Runtime API. The AWS open-source runtime interface clients implement the API. You can add a runtime interface client to your preferred base image to make it compatible with Lambda.

AWS Lambda service does not support Lambda functions that use multi-architecture container images - Lambda provides multi-architecture base images. However, the image you build for your function must target only one of the architectures. Lambda does not support functions that use multi-architecture container images.

Incorrect options:

Lambda supports both Windows and Linux-based container images - Lambda currently supports only Linux-based container images.

You can test the containers locally using the Lambda Runtime API - You can test the containers locally using the Lambda Runtime Interface Emulator.

You can deploy Lambda function as a container image, with a maximum size of 15 GB - You can deploy Lambda function as container image with the maximum size of 10GB.

Reference:

https://docs.aws.amazon.com/lambda/latest/dg/images-create.html

</details>

---

<a id="t09-q26"></a>
### T09-Q26
*⏭ пропущено · домен: офіційна мітка*

Your company has embraced cloud-native microservices architectures. New applications must be dockerized and stored in a registry service offered by AWS. The architecture should support dynamic port mapping and support multiple tasks from a single service on the same container instance. All services should run on the same EC2 instance.

Which of the following options offers the best-fit solution for the given use-case?

- ▫️ Classic Load Balancer + ECS
- ✅ Application Load Balancer + ECS
- ▫️ Application Load Balancer + Beanstalk
- ▫️ Classic Load Balancer + Beanstalk

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Application Load Balancer + ECS

Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster. You can host your cluster on a serverless infrastructure that is managed by Amazon ECS by launching your services or tasks using the Fargate launch type. For more control over your infrastructure, you can host your tasks on a cluster of Amazon Elastic Compute Cloud (Amazon EC2) instances that you manage by using the EC2 launch type.

 via - https://aws.amazon.com/ecs/

An Application load balancer distributes incoming application traffic across multiple targets, such as EC2 instances, in multiple Availability Zones. A listener checks for connection requests from clients, using the protocol and port that you configure. The rules that you define for a listener determine how the load balancer routes requests to its registered targets. Each rule consists of a priority, one or more actions, and one or more conditions.

 via - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html

When you deploy your services using Amazon Elastic Container Service (Amazon ECS), you can use dynamic port mapping to support multiple tasks from a single service on the same container instance. Amazon ECS manages updates to your services by automatically registering and deregistering containers with your target group using the instance ID and port for each container.

 via - https://aws.amazon.com/premiumsupport/knowledge-center/dynamic-port-mapping-ecs

Incorrect options:

Classic Load Balancer + Beanstalk - The Classic Load Balancer doesn't allow you to run multiple copies of a task on the same instance. Instead, with the Classic Load Balancer, you must statically map port numbers on a container instance. So this option is ruled out.

Application Load Balancer + Beanstalk - You can create docker environments that support multiple containers per Amazon EC2 instance with a multi-container Docker platform for Elastic Beanstalk. However, ECS gives you finer control.

Classic Load Balancer + ECS - The Classic Load Balancer doesn't allow you to run multiple copies of a task in the same instance. Instead, with the Classic Load Balancer, you must statically map port numbers on a container instance. So this option is ruled out.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/dynamic-port-mapping-ecs> · <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/tutorial-target-ecs-containers.html> · <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html>

</details>

---

<a id="t10-q06"></a>
### T10-Q06
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer is migrating Docker containers to Amazon ECS. A large number of containers will be deployed across some newly deployed ECS containers instances using the same instance type. High availability is provided within the microservices architecture. Which task placement strategy requires the LEAST configuration for this scenario?

- ▫️ spread
- ▫️ Fargate
- ✅ random
- ▫️ binpack

<details><summary>Пояснення (оригінал, EN)</summary>

When a task that uses the EC2 launch type is launched, Amazon ECS must determine where to place the task based on the requirements specified in the task definition, such as CPU and memory. Similarly, when you scale down the task count, Amazon ECS must determine which tasks to terminate. You can apply task placement strategies and constraints to customize how Amazon ECS places and terminates tasks. Task placement strategies and constraints are not supported for tasks using the Fargate launch type. By default, Fargate tasks are spread across Availability Zones.

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination. For example, Amazon ECS can select instances at random, or it can select instances such that tasks are distributed evenly across a group of instances.

Amazon ECS supports the following task placement strategies:

• binpack

Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.

• random

Place tasks randomly.

• spread

Place tasks evenly based on the specified value. Accepted values are instanceId (or host, which has the same effect), or any platform or custom attribute that is applied to a container instance, such as attribute:ecs.availability-zone. Service tasks are spread based on the tasks from that service. Standalone tasks are spread based on the tasks from the same task group.

Therefore, for this scenario the random task placement strategy is most suitable as it requires the least configuration.

CORRECT: "random" is the correct answer.

INCORRECT: "spread" is incorrect. As high availability is taken care of within the containers there is no need to use a spread strategy to ensure HA.

INCORRECT: "binpack" is incorrect as there is no need to pack the containers onto the fewest instances based on CPU or memory.

INCORRECT: "Fargate" is incorrect as this is not a task placement strategy, it is a serverless service for running containers.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-placement-strategies.html>

</details>

---

<a id="t12-q03"></a>
### T12-Q03
*⏭ пропущено · домен: офіційна мітка*

The development team at an e-commerce company wants to run a serverless data store service on two docker containers that share resources.

Which of the following ECS configurations can be used to facilitate this use-case?

- ✅ Put the two containers into a single task definition using a Fargate Launch Type
- ▫️ Put the two containers into two separate task definitions using a Fargate Launch Type
- ▫️ Put the two containers into two separate task definitions using an EC2 Launch Type
- ▫️ Put the two containers into a single task definition using an EC2 Launch Type

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Put the two containers into a single task definition using a Fargate Launch Type

Amazon Elastic Container Service (Amazon ECS) is a highly scalable, fast, container management service that makes it easy to run, stop, and manage Docker containers on a cluster. You can host your cluster on a serverless infrastructure that is managed by Amazon ECS by launching your services or tasks using the Fargate launch type. For more control over your infrastructure, you can host your tasks on a cluster of Amazon Elastic Compute Cloud (Amazon EC2) instances that you manage by using the EC2 launch type.

 via - https://aws.amazon.com/ecs/

As the development team is looking for a serverless data store service, therefore the two containers should be launched into a single task definition using a Fargate Launch Type. Using a single task definition allows the two containers to share resources. Please see these use-cases for Fargate Launch type when you should put multiple containers into the same task definition:

 via - https://docs.aws.amazon.com/AmazonECS/latest/developerguide/application_architecture.html

For a deep-dive on understanding how Amazon ECS manages CPU and memory resources, please review this excellent blog- https://aws.amazon.com/blogs/containers/how-amazon-ecs-manages-cpu-and-memory-resources/

Incorrect options:

Put the two containers into two separate task definitions using a Fargate Launch Type - This option contradicts the details provided in the explanation above, so this option is ruled out.

Put the two containers into two separate task definitions using an EC2 Launch Type

Put the two containers into a single task definition using an EC2 Launch Type

As the development team is looking for a serverless data store service, therefore EC2 Launch Type is ruled out. So both these options are incorrect.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/application_architecture.html> · <https://aws.amazon.com/blogs/containers/how-amazon-ecs-manages-cpu-and-memory-resources/>

</details>

---

<a id="t12-q24"></a>
### T12-Q24
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **Застаріле: aws ecr get-login (CLI v1). Зараз get-login-password.** → [детальніше](../../07-outdated-and-wrong-answers.md#o13)

You are working for a technology startup building web and mobile applications. You would like to pull Docker images from the ECR repository called demo so you can start running local tests against the latest application version.

Which of the following commands must you run to pull existing Docker images from ECR? (Select two)

- ✅ docker pull 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest
- ▫️ docker build -t 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest
- ✅ $(aws ecr get-login --no-include-email)
- ▫️ docker login -u $AWS_ACCESS_KEY_ID -p $AWS_SECRET_ACCESS_KEY
- ▫️ aws docker push 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

$(aws ecr get-login --no-include-email)

docker pull 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest

The get-login command retrieves a token that is valid for a specified registry for 12 hours, and then it prints a docker login command with that authorization token. You can execute the printed command to log in to your registry with Docker, or just run it automatically using the $() command wrapper. After you have logged in to an Amazon ECR registry with this command, you can use the Docker CLI to push and pull images from that registry until the token expires. The docker pull command is used to pull an image from the ECR registry.

Incorrect options:

docker login -u $AWS_ACCESS_KEY_ID -p $AWS_SECRET_ACCESS_KEY - You cannot login to AWS ECR this way. AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY are only used by the CLI and not by docker.

aws docker push 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest - docker push here is the wrong answer, you need to use docker pull.

docker build -t 1234567890.dkr.ecr.eu-west-1.amazonaws.com/demo:latest - This is a docker command that is used to build Docker images from a Dockerfile.

Reference:

https://docs.aws.amazon.com/cli/latest/reference/ecr/get-login.html

</details>

---

<a id="t12-q29"></a>
### T12-Q29
*⏭ пропущено · домен: офіційна мітка*

Your e-commerce company needs to improve its software delivery process and is moving away from the waterfall methodology. You decided that every application should be built using the best CI/CD practices and every application should be packaged and deployed as a Docker container. The Docker images should be stored in ECR and pushed with AWS CodePipeline and AWS CodeBuild.

When you attempt to do this, the last step fails with an authorization issue. What is the most likely issue?

- ▫️ CodeBuild cannot talk to ECR because of security group issues
- ▫️ The ECR repository is stale, you must delete and re-create it
- ▫️ The ECS instances are misconfigured and must contain additional data in /etc/ecs/ecs.config
- ✅ The IAM permissions are wrong for the CodeBuild service

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

The IAM permissions are wrong for the CodeBuild service

You can push your Docker or Open Container Initiative (OCI) images to an Amazon ECR repository with the docker push command.

Amazon ECR users require permission to call ecr:GetAuthorizationToken before they can authenticate to a registry and push or pull any images from any Amazon ECR repository. Amazon ECR provides several managed policies to control user access at varying levels

Incorrect options:

The ECR repository is stale, you must delete and re-create it - You can delete a repository when you are done using it, stale is not a concept within ECR. This option has been added as a distractor.

CodeBuild cannot talk to ECR because of security group issues - A security group acts as a virtual firewall at the instance level and it is not related to pushing Docker images, so this option does not fit the given use-case.

The ECS instances are misconfigured and must contain additional data in /etc/ecs/ecs.config - The error Authorization is an indication that there is an access issue, therefore you should not look at your configuration first but rather permissions.

Джерела: <https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html> · <https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr_managed_policies.html>

</details>

---

<a id="t12-q46"></a>
### T12-Q46
*⏭ пропущено · домен: офіційна мітка*

A company wants to automate the creation of ECS clusters using CloudFormation. The process has worked for a while, but after creating task definitions and assigning roles, the development team discovers that the tasks for containers are not using the permissions assigned to them.

Which ECS config must be set in /etc/ecs/ecs.config to allow ECS tasks to use IAM roles?

- ▫️ ECS_CLUSTER
- ▫️ ECS_ENGINE_AUTH_DATA
- ▫️ ECS_AVAILABLE_LOGGING_DRIVERS
- ✅ ECS_ENABLE_TASK_IAM_ROLE

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

ECS_ENABLE_TASK_IAM_ROLE

This configuration item is used to enable IAM roles for tasks for containers with the bridge and default network modes.

Incorrect options:

ECS_ENGINE_AUTH_DATA - This refers to the authentication data within a Docker configuration file, so this is not the correct option.

ECS_AVAILABLE_LOGGING_DRIVERS - The Amazon ECS container agent running on a container instance must register the logging drivers available on that instance with this variable. This configuration item refers to the logging driver.

ECS_CLUSTER - This refers to the ECS cluster that the ECS agent should check into. This is passed to the container instance at launch through Amazon EC2 user data.

Reference:

https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html

</details>

---
