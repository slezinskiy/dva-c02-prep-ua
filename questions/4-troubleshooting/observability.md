# CloudWatch / X-Ray / CloudTrail

**Домен 4 — Усунення несправностей та оптимізація** · питань: 41  
Теорія: [04-domain4-troubleshooting.md](../../04-domain4-troubleshooting.md#observability) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q03"></a>
### T01-Q03
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **X-Ray SDK/daemon у maintenance з 25.02.2026 → ADOT/CloudWatch agent.** → [детальніше](../../07-outdated-and-wrong-answers.md#o05)

Which environment variable can be used by AWS X-Ray SDK to ensure that the daemon is correctly discovered on ECS?

- ✅ AWS_XRAY_DAEMON_ADDRESS
- ▫️ AWS_XRAY_TRACING_NAME
- ▫️ AWS_XRAY_DEBUG_MODE
- ▫️ AWS_XRAY_CONTEXT_MISSING

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS_XRAY_DAEMON_ADDRESS

Set the host and port of the X-Ray daemon listener. By default, the SDK uses 127.0.0.1:2000 for both trace data (UDP) and sampling (TCP). Use this variable if you have configured the daemon to listen on a different port or if it is running on a different host.

Incorrect options:

AWS_XRAY_TRACING_NAME - This sets a service name that the SDK uses for segments.

AWS_XRAY_CONTEXT_MISSING - This should be set to LOG_ERROR to avoid throwing exceptions when your instrumented code attempts to record data when no segment is open.

AWS_XRAY_DEBUG_MODE - This should be set to TRUE to configure the SDK to output logs to the console, instead of configuring a logger.

Reference:

https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-nodejs-configuration.html

</details>

---

<a id="t01-q18"></a>
### T01-Q18
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **On-prem → CloudWatch agent + IAM user.** → [детальніше](../../06-contradictions-and-traps.md#c15)

A development team has a mix of applications hosted on-premises as well as on EC2 instances. The on-premises application controls all applications deployed on the EC2 instances. In case of any errors, the team wants to leverage Amazon CloudWatch to monitor and troubleshoot the on-premises application.

As a Developer Associate, which of the following solutions would you suggest to address this use-case?

- ▫️ Upload log files from the on-premises server to S3 and let CloudWatch process the files from S3
- ✅ Configure the CloudWatch agent on the on-premises server by using IAM user credentials with permissions for CloudWatch
- ▫️ Configure CloudWatch Logs to directly read the logs from the on-premises server
- ▫️ Upload log files from the on-premises server to an EC2 instance which further forwards the logs to CloudWatch

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure the CloudWatch agent on the on-premises server by using IAM user credentials with permissions for CloudWatch

The CloudWatch agent enables you to do the following:

Collect system-level metrics from on-premises servers. These can include servers in a hybrid environment as well as servers not managed by AWS.

Collect logs from Amazon EC2 instances and on-premises servers, running either Linux or Windows Server.

To enable the CloudWatch agent to send data from an on-premises server, you must specify the access key and secret key of the IAM user that you created earlier.

 via - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html

Incorrect options:

Configure CloudWatch Logs to directly read the logs from the on-premises server - This is a made-up option as you cannot have CloudWatch Logs directly communicate with the on-premises server. You have to go via the CloudWatch Agent.

Upload log files from the on-premises server to an EC2 instance which further forwards the logs to CloudWatch

Upload log files from the on-premises server to S3 and let CloudWatch process the files from S3

Both these options require significant customizations and still will not be as neatly integrated with CloudWatch as compared to just using the CloudWatch Agent which is available off-the-shelf.

Reference:

https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html

</details>

---

<a id="t01-q38"></a>
### T01-Q38
*⏭ пропущено · домен: офіційна мітка*

A business-critical application is hosted on an Amazon EC2 instance and the latest update is in the testing phase. The business has requested a solution to track the average response time of the application and send a notification to the manager if it exceeds a particular threshold. The update will eventually be implemented in the production environment where the solution will be deployed on multiple EC2 instances.

Which of the following options would you combine to address the requirements of the business? (Select two)

- ▫️ Configure the application to write the response time to a log file on the instance. Install and configure the Amazon Inspector agent on the EC2 instances to read the logs and send the response time to Amazon EventBridge
- ▫️ Install and configure AWS Systems Manager Agent (SSM Agent) on the EC2 instances to monitor the response time and send the data to Amazon CloudWatch as a custom metric
- ▫️ Configure an EventBridge custom rule to send an Amazon Simple Notification Service (Amazon SNS) notification when the average of the response time metric exceeds the threshold
- ✅ Configure the application to write the response time to a log file on the EC2 instance. Install and configure the Amazon CloudWatch agent on the EC2 instance to stream the application logs to CloudWatch Logs. Create a metric filter for the response time from the log file
- ✅ Create a CloudWatch alarm to send an Amazon Simple Notification Service (Amazon SNS) notification when the average of the response time metric exceeds the threshold

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

Configure the application to write the response time to a log file on the EC2 instance. Install and configure the Amazon CloudWatch agent on the EC2 instance to stream the application logs to CloudWatch Logs. Create a metric filter for the response time from the log file

Create a CloudWatch alarm to send an Amazon Simple Notification Service (Amazon SNS) notification when the average of the response time metric exceeds the threshold

You can collect metrics and logs from Amazon EC2 instances and on-premises servers with the CloudWatch agent. The unified CloudWatch agent enables you to do the following:

Collect internal system-level metrics from Amazon EC2 instances across operating systems. The metrics can include in-guest metrics, in addition to the metrics for EC2 instances.

Collect system-level metrics from on-premises servers. These can include servers in a hybrid environment as well as servers not managed by AWS.

Retrieve custom metrics from your applications or services using the StatsD and collectd protocols. StatsD is supported on both Linux servers and servers running Windows Server. collectd is supported only on Linux servers.

Collect logs from Amazon EC2 instances and on-premises servers, running either Linux or Windows Server.

You can store and view the metrics that you collect with the CloudWatch agent in CloudWatch just as you can with any other CloudWatch metrics. The default namespace for metrics collected by the CloudWatch agent is CWAgent, although you can specify a different namespace when you configure the agent.

A metric alarm watches a single CloudWatch metric or the result of a math expression based on CloudWatch metrics. The alarm performs one or more actions based on the value of the metric or expression relative to a threshold over a number of time periods. The action can be sending a notification to an Amazon SNS topic, performing an Amazon EC2 action or an Amazon EC2 Auto Scaling action, or creating an OpsItem or incident in Systems Manager. For this use case, we will configure the metric alarm action to send an SNS notification when the alarm is in an ALARM state.

Incorrect options:

Configure the application to write the response time to a log file on the instance. Install and configure the Amazon Inspector agent on the EC2 instances to read the logs and send the response time to Amazon EventBridge - This statement is incorrect. Amazon Inspector is an automated vulnerability management service that continually scans Amazon Elastic Compute Cloud (EC2), AWS Lambda functions, and container workloads for software vulnerabilities and unintended network exposure. Inspector is not a log-collecting agent.

Configure an EventBridge custom rule to send an Amazon Simple Notification Service (Amazon SNS) notification when the average of the response time metric exceeds the threshold - Amazon EventBridge integrates with Amazon CloudWatch so that when CloudWatch alarms are triggered, a matching EventBridge rule can execute targets. So we are using CloudWatch alarms in this scenario too and hence a straightforward way would be to use CloudWatch alarm.

Install and configure AWS Systems Manager Agent (SSM Agent) on the EC2 instances to monitor the response time and send the data to Amazon CloudWatch as a custom metric - This statement is incorrect. AWS Systems Manager Agent (SSM Agent) is Amazon software that runs on Amazon Elastic Compute Cloud (Amazon EC2) instances, edge devices, on-premises servers, and virtual machines (VMs). SSM Agent makes it possible for the Systems Manager to update, manage, and configure these resources. SSM agent is required on each instance that needs to be managed from the Systems Manager service. SSM agents cannot collect log data and push it to CloudWatch logs. However, Systems Manager can be used to automate the task of CloudWatch agent installation on the instances.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html>

</details>

---

<a id="t01-q43"></a>
### T01-Q43
*⏭ пропущено · домен: офіційна мітка*

You have been collecting AWS X-Ray traces across multiple applications and you would now like to index your XRay traces to search and filter through them efficiently.

What should you use in your instrumentation?

- ▫️ Sampling
- ▫️ Metadata
- ▫️ Segments
- ✅ Annotations

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Annotations

AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

You can use X-Ray to collect data across AWS Accounts. The X-Ray agent can assume a role to publish data into an account different from the one in which it is running. This enables you to publish data from various components of your application into a central account.

How X-Ray Works:  via - https://aws.amazon.com/xray/

Annotations are simple key-value pairs that are indexed for use with filter expressions. Use annotations to record data that you want to use to group traces in the console, or when calling the GetTraceSummaries API.

X-Ray indexes up to 50 annotations per trace.

Incorrect options:

Metadata - Metadata are key-value pairs with values of any type, including objects and lists, but that is not indexed. Use metadata to record data you want to store in the trace but don't need to use for searching traces.

Segments - The computing resources running your application logic send data about their work as segments. A segment provides the resource's name, details about the request, and details about the work done.

Sampling - To ensure efficient tracing and provide a representative sample of the requests that your application serves, the X-Ray SDK applies a sampling algorithm to determine which requests get traced. By default, the X-Ray SDK records the first request each second, and five percent of any additional requests.

Reference:

https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html

</details>

---

<a id="t02-q02"></a>
### T02-Q02
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A critical application runs on an Amazon EC2 instance. A Developer has configured a custom Amazon CloudWatch metric that monitors application availability with a data granularity of 1 second. The Developer must be notified within 30 seconds if the application experiences any issues.

What should the Developer do to meet this requirement?

- ▫️ Specify an Amazon SNS topic for alarms when issuing the put-metric-data AWS CLI command.
- ✅ Configure a high-resolution CloudWatch alarm and use Amazon SNS to send the alert.
- ❌ Use a default CloudWatch metric, configure an alarm, and use Amazon SNS to send the alert.
- ▫️ Use Amazon CloudWatch Logs Insights and trigger an Amazon Eventbridge rule to send a notification.

<details><summary>Пояснення (оригінал, EN)</summary>

If you set an alarm on a high-resolution metric, you can specify a high-resolution alarm with a period of 10 seconds or 30 seconds, or you can set a regular alarm with a period of any multiple of 60 seconds. There is a higher charge for high-resolution alarms.

Amazon SNS can then be used to send notifications based on the CloudWatch alarm.

CORRECT: "Configure a high-resolution CloudWatch alarm and use Amazon SNS to send the alert" is the correct answer.

INCORRECT: "Specify an Amazon SNS topic for alarms when issuing the put-metric-data AWS CLI command" is incorrect. You cannot specify an SNS topic with this CLI command.

INCORRECT: "Use Amazon CloudWatch Logs Insights and trigger an Amazon Eventbridge rule to send a notification" is incorrect. Logs Insights cannot be used for alarms or alerting based on custom CloudWatch metrics.

INCORRECT: "Use a default CloudWatch metric, configure an alarm, and use Amazon SNS to send the alert" is incorrect. There is no default metric that would monitor the application uptime and the resolution would be lower.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#high-resolution-alarms>

</details>

---

<a id="t02-q10"></a>
### T02-Q10
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

An application serves customers in several different geographical regions. Information about the location users connect from is written to logs stored in Amazon CloudWatch Logs. The company needs to publish an Amazon CloudWatch custom metric that tracks connections for each location.

Which approach will meet these requirements?

- ▫️ Create a CloudWatch Logs Insights query to extract the location information from the logs and to create a custom metric with location as a dimension.
- ✅ Create a CloudWatch metric filter to extract metrics from the log files with location as a dimension.
- ▫️ Configure a CloudWatch Events rule that creates a custom metric from the CloudWatch Logs group.
- ▫️ Stream data to an Amazon Elasticsearch cluster in near-real time and export a custom metric.

<details><summary>Пояснення (оригінал, EN)</summary>

You can search and filter the log data coming into CloudWatch Logs by creating one or more metric filters. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs. CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on.

When you create a metric from a log filter, you can also choose to assign dimensions and a unit to the metric. In this case, the company can assign a dimension that uses the location information.

CORRECT: "Create a CloudWatch metric filter to extract metrics from the log files with location as a dimension" is the correct answer.

INCORRECT: "Create a CloudWatch Logs Insights query to extract the location information from the logs and to create a custom metric with location as a dimension" is incorrect. You cannot create a custom metric through CloudWatch Logs Insights.

INCORRECT: "Configure a CloudWatch Events rule that creates a custom metric from the CloudWatch Logs group" is incorrect. You cannot create a custom metric using a CloudWatch Events rule.

INCORRECT: "Stream data to an Amazon Elasticsearch cluster in near-real time and export a custom metric" is incorrect. This is not a valid way of creating a custom metric in CloudWatch.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t02-q34"></a>
### T02-Q34
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Скрипт/cron → put-metric-data.** → [детальніше](../../06-contradictions-and-traps.md#c15)

A Developer is writing code to run in a cron job on an Amazon EC2 instance that sends status information about the application to Amazon CloudWatch.

Which method should the Developer use?

- ▫️ Use the CloudWatch console with detailed monitoring.
- ✅ Use the AWS CLI put-metric-data command.
- ❌ Use the unified CloudWatch agent to publish custom metrics.
- ▫️ Use the AWS CLI put-metric-alarm command.

<details><summary>Пояснення (оригінал, EN)</summary>

The put-metric-data command publishes metric data points to Amazon CloudWatch. CloudWatch associates the data points with the specified metric. If the specified metric does not exist, CloudWatch creates the metric.

CORRECT: "Use the AWS CLI put-metric-data command" is the correct answer.

INCORRECT: "Use the AWS CLI put-metric-alarm command" is incorrect. This command creates or updates an alarm and associates it with the specified metric, metric math expression, or anomaly detection model.

INCORRECT: "Use the unified CloudWatch agent to publish custom metrics" is incorrect. It is not necessary to use the unified CloudWatch agent. In this case the Developer can use the AWS CLI with the cron job.

INCORRECT: "Use the CloudWatch console with detailed monitoring" is incorrect. You cannot collect custom metric data using the CloudWatch console with detailed monitoring. Detailed monitoring sends data at 1-minute rather than 5-minute frequencies but will not collect custom data.

Джерела: <https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-data.html>

</details>

---

<a id="t03-q06"></a>
### T03-Q06
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **RAM скриптом → custom metric.** → [детальніше](../../06-contradictions-and-traps.md#c15)

Your company manages hundreds of EC2 instances running on Linux OS. The instances are configured in several Availability Zones in the eu-west-3 region. Your manager has requested to collect system memory metrics on all EC2 instances using a script.

Which of the following solutions will help you collect this data?

- ▫️ Extract RAM statistics using the instance metadata
- ✅ Use a cron job on the instances that pushes the EC2 RAM statistics as a Custom metric into CloudWatch
- ▫️ Extract RAM statistics from the standard CloudWatch metrics for EC2 instances
- ▫️ Extract RAM statistics using X-Ray

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

"Use a cron job on the instances that pushes the EC2 RAM statistics as a Custom metric into CloudWatch"

The Amazon CloudWatch Monitoring Scripts for Amazon Elastic Compute Cloud (Amazon EC2) Linux-based instances demonstrate how to produce and consume Amazon CloudWatch custom metrics. These Perl scripts comprise a fully functional example that reports memory, swap, and disk space utilization metrics for a Linux instance. You can set a cron schedule for metrics reported to CloudWatch and report memory utilization to CloudWatch every x minutes.

Incorrect options:

"Extract RAM statistics using the instance metadata" - Instance metadata is data about your instance that you can use to configure or manage the running instance. Instance metadata is divided into categories, for example, hostname, events, and security groups. The instance metadata can only provide the ID of the RAM disk specified at launch time. So this option is incorrect.

"Extract RAM statistics from the standard CloudWatch metrics for EC2 instances" - Amazon EC2 sends metrics to Amazon CloudWatch. By default, each data point covers the 5 minutes that follow the start time of activity for the instance. If you've enabled detailed monitoring, each data point covers the next minute of activity from the start time. The standard CloudWatch metrics don't have any metrics for memory utilization details.

"Extract RAM statistics using X-Ray" - AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

How X-Ray Works:  via - https://aws.amazon.com/xray/

X-Ray cannot be used to extract RAM statistics for EC2 instances.

For more information visit https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html

</details>

---

<a id="t03-q34"></a>
### T03-Q34
*⏭ пропущено · домен: офіційна мітка*

A company has several Linux-based EC2 instances that generate various log files which need to be analyzed for security and compliance purposes. The company wants to use Kinesis Data Streams (KDS) to analyze this log data.

Which of the following is the most optimal way of sending log data from the EC2 instances to KDS?

- ▫️ Run cron job on each of the instances to collect log data and send it to Kinesis Data Streams
- ✅ Install and configure Kinesis Agent on each of the instances
- ▫️ Install AWS SDK on each of the instances and configure it to send the necessary files to Kinesis Data Streams
- ▫️ Use Kinesis Producer Library (KPL) to collect and ingest data from each EC2 instance

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Install and configure Kinesis Agent on each of the instances

Kinesis Agent is a stand-alone Java software application that offers an easy way to collect and send data to Kinesis Data Streams. The agent continuously monitors a set of files and sends new data to your stream. The agent handles file rotation, checkpointing, and retry upon failures. It delivers all of your data in a reliable, timely, and simple manner. It also emits Amazon CloudWatch metrics to help you better monitor and troubleshoot the streaming process.

You can install the agent on Linux-based server environments such as web servers, log servers, and database servers. After installing the agent, configure it by specifying the files to monitor and the stream for the data. After the agent is configured, it durably collects data from the files and reliably sends it to the stream.

The agent can also pre-process the records parsed from monitored files before sending them to your stream. You can enable this feature by adding the dataProcessingOptions configuration setting to your file flow. One or more processing options can be added and they will be performed in the specified order.

Incorrect options:

Run cron job on each of the instances to collect log data and send it to Kinesis Data Streams - This solution is possible, though not an optimal one. This solution requires writing custom code and tracking file/log changes, retry failures and so on. Kinesis Agent is built to handle all these requirements and integrates with Data Streams.

Install AWS SDK on each of the instances and configure it to send the necessary files to Kinesis Data Streams - Kinesis Data Streams APIs that are available in the AWS SDKs, helps you manage many aspects of Kinesis Data Streams, including creating streams, resharding, and putting and getting records. You will need to write custom code to handle new data in the log files and send it over to your stream. Kinesis Agent does it easily, as it is designed to continuously monitor a set of files and send new data to your stream.

Use Kinesis Producer Library (KPL) to collect and ingest data from each EC2 instance - The KPL is an easy-to-use, highly configurable library that helps you write to a Kinesis data stream. It acts as an intermediary between your producer application code and the Kinesis Data Streams API actions. This is not optimal compared to Kinesis Agent which is designed to continuously monitor a set of files and send new data to your stream.

Reference:

https://docs.aws.amazon.com/streams/latest/dev/writing-with-agents.html

</details>

---

<a id="t03-q62"></a>
### T03-Q62
*⏭ пропущено · домен: офіційна мітка*

An organization uses Alexa as its intelligent assistant to improve productivity throughout the organization. A group of developers manages custom Alexa Skills written in Node.Js to control conference-room equipment settings and start meetings using voice activation. The manager has requested developers that all functions code should be monitored for error rates with the possibility of creating alarms on top of them.

Which of the following options should be chosen? (select two)

- ▫️ X-Ray
- ▫️ CloudTrail
- ✅ CloudWatch Metrics
- ▫️ SSM
- ✅ CloudWatch Alarms

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

CloudWatch collects monitoring and operational data in the form of logs, metrics, and events, and visualizes it using automated dashboards so you can get a unified view of your AWS resources, applications, and services that run in AWS and on-premises. You can correlate your metrics and logs to better understand the health and performance of your resources. You can also create alarms based on metric value thresholds you specify, or that can watch for anomalous metric behavior based on machine learning algorithms.

How CloudWatch works:  via - https://aws.amazon.com/cloudwatch/

CloudWatch Metrics

Amazon CloudWatch monitors your Amazon Web Services (AWS) resources and the applications you run on AWS in real-time. You can use CloudWatch to collect and track metrics, which are variables you can measure for your resources and applications. Metric data is kept for 15 months, enabling you to view both up-to-the-minute data and historical data.

CloudWatch retains metric data as follows:

Data points with a period of less than 60 seconds are available for 3 hours. These data points are high-resolution custom metrics. Data points with a period of 60 seconds (1 minute) are available for 15 days Data points with a period of 300 seconds (5 minute) are available for 63 days Data points with a period of 3600 seconds (1 hour) are available for 455 days (15 months)

CloudWatch Alarms

You can use an alarm to automatically initiate actions on your behalf. An alarm watches a single metric over a specified time, and performs one or more specified actions, based on the value of the metric relative to a threshold over time. The action is a notification sent to an Amazon SNS topic or an Auto Scaling policy. You can also add alarms to dashboards.

CloudWatch alarms send notifications or automatically make changes to the resources you are monitoring based on rules that you define. Alarms work together with CloudWatch Metrics.

A metric alarm has the following possible states:

OK – The metric or expression is within the defined threshold.

ALARM – The metric or expression is outside of the defined threshold.

INSUFFICIENT_DATA – The alarm has just started, the metric is not available, or not enough data is available for the metric to determine the alarm state.

Incorrect options:

X-Ray - AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

How X-Ray Works:  via - https://aws.amazon.com/xray/

X-Ray cannot be used to capture metrics and set up alarms as per the given use-case, so this option is incorrect.

CloudTrail - CloudWatch is a monitoring service whereas CloudTrail is more of an audit service where you can find API calls made on services and by whom.

How CloudTrail Works:  via - https://aws.amazon.com/cloudtrail/

Systems Manager - Using AWS Systems Manager, you can group resources, like Amazon EC2 instances, Amazon S3 buckets, or Amazon RDS instances, by application, view operational data for monitoring and troubleshooting, and take action on your groups of resources. Systems Manager cannot be used to capture metrics and set up alarms as per the given use-case, so this option is incorrect.

Джерела: <https://aws.amazon.com/cloudwatch/> · <https://aws.amazon.com/cloudtrail/> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html>

</details>

---

<a id="t04-q02"></a>
### T04-Q02
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

A Development team wants to instrument their code to provide more detailed information to AWS X-Ray than simple outgoing and incoming requests. This will generate large amounts of data, so the Development team wants to implement indexing so they can filter the data.

What should the Development team do to achieve this?

- ▫️ Install required plugins for the appropriate AWS SDK
- ▫️ Configure the necessary X-Ray environment variables
- ✅ Add annotations to the segment document
- ▫️ Add metadata to the segment document

<details><summary>Пояснення (оригінал, EN)</summary>

AWS X-Ray makes it easy for developers to analyze the behavior of their production, distributed applications with end-to-end tracing capabilities. You can use X-Ray to identify performance bottlenecks, edge case errors, and other hard to detect issues.

When you instrument your application, the X-Ray SDK records information about incoming and outgoing requests, the AWS resources used, and the application itself. You can add other information to the segment document as annotations and metadata. Annotations and metadata are aggregated at the trace level and can be added to any segment or subsegment.

Annotations are simple key-value pairs that are indexed for use with filter expressions. Use annotations to record data that you want to use to group traces in the console, or when calling the GetTraceSummaries API. X-Ray indexes up to 50 annotations per trace.

Metadata are key-value pairs with values of any type, including objects and lists, but that are not indexed. Use metadata to record data you want to store in the trace but don't need to use for searching traces.

You can view annotations and metadata in the segment or subsegment details in the X-Ray console.

In this scenario, we need to add annotations to the segment document so that the data that needs to be filtered is indexed.

CORRECT: "Add annotations to the segment document" is the correct answer.

INCORRECT: "Add metadata to the segment document" is incorrect as metadata is not indexed for filtering.

INCORRECT: "Configure the necessary X-Ray environment variables" is incorrect as this will not result in indexing of the required data.

INCORRECT: "Install required plugins for the appropriate AWS SDK" is incorrect as there are no plugin requirements for the AWS SDK to support this solution as the annotations feature is available in AWS X-Ray.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-annotations>

</details>

---

<a id="t04-q31"></a>
### T04-Q31
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer is publishing custom metrics for Amazon EC2 using the Amazon CloudWatch CLI. The Developer needs to add further context to the metrics being published by organizing them by EC2 instance and Auto Scaling Group.

What should the Developer add to the CLI command when publishing the metrics using put-metric-data

- ▫️ The --statistic-values parameter
- ✅ 1. The --dimensions parameter
- ▫️ The --metric-name parameter
- ▫️ The --namespace parameter

<details><summary>Пояснення (оригінал, EN)</summary>

You can publish your own metrics to CloudWatch using the AWS CLI or an API. You can view statistical graphs of your published metrics with the AWS Management Console.

CloudWatch stores data about a metric as a series of data points. Each data point has an associated time stamp. You can even publish an aggregated set of data points called a statistic set.

In custom metrics, the --dimensions parameter is common. A dimension further clarifies what the metric is and what data it stores. You can have up to 10 dimensions in one metric, and each dimension is defined by a name and value pair.

As you can see in the above example there are two dimensions associated with the EC2 namespace. These organize the metrics by Auto Scaling Group and Per-Instance metrics. Therefore the Developer should the --dimensions parameter.

CORRECT: "The --dimensions parameter" is the correct answer.

INCORRECT: "The --namespace parameter" is incorrect as a namespace is a container for CloudWatch metrics. To add further context the Developer should use a dimension.

INCORRECT: "The --statistic-values parameter" is incorrect as this is a parameter associated with the publishing of statistic sets.

INCORRECT: "The --metric-name parameter" is incorrect as this simply provides the name for the metric that is being published.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html>

</details>

---

<a id="t04-q64"></a>
### T04-Q64
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Гібрид, системні метрики → CloudWatch agent.** → [детальніше](../../06-contradictions-and-traps.md#c15)

A Development team manage a hybrid cloud environment. They would like to collect system-level metrics from on-premises servers and Amazon EC2 instances. How can the Development team collect this information MOST efficiently?

- ▫️ Install the CloudWatch agent on the EC2 instances and use a cron job on the on-premises servers
- ✅ Install the CloudWatch agent on the on-premises servers and EC2 instances
- ▫️ Use CloudWatch detailed monitoring for both EC2 instances and on-premises servers
- ▫️ Use CloudWatch for monitoring EC2 instances and custom AWS CLI scripts using the put-metric-data API

<details><summary>Пояснення (оригінал, EN)</summary>

The unified CloudWatch agent can be installed on both on-premises servers and Amazon EC2 instances using multiple operating system versions. It enables you to do the following:

• Collect more system-level metrics from Amazon EC2 instances across operating systems. The metrics can include in-guest metrics, in addition to the metrics for EC2 instances.

• Collect system-level metrics from on-premises servers. These can include servers in a hybrid environment as well as servers not managed by AWS.

• Retrieve custom metrics from your applications or services using the StatsD and collectd protocols.

• Collect logs from Amazon EC2 instances and on-premises servers, running either Linux or Windows Server.

Therefore, the Development team should install the CloudWatch agent on the on-premises servers and EC2 instances. This will allow them to collect system-level metrics from servers and instances across the hybrid cloud environment.

CORRECT: "Install the CloudWatch agent on the on-premises servers and EC2 instances" is the correct answer.

INCORRECT: "Use CloudWatch for monitoring EC2 instances and custom AWS CLI scripts using the put-metric-data API" is incorrect as this is not the most efficient option as you must write and maintain custom scripts. It is better to use the CloudWatch agent as it provides all the functionality required.

INCORRECT: "Install the CloudWatch agent on the EC2 instances and use a cron job on the on-premises servers" is incorrect as the answer does not even specify what the cron job is going to do / use for gathering and sending the data.

INCORRECT: "Use CloudWatch detailed monitoring for both EC2 instances and on-premises servers" is incorrect as this would not do anything for the on-premises instances.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html>

</details>

---

<a id="t05-q15"></a>
### T05-Q15
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A company is deploying a new serverless application with an AWS Lambda function. A developer ran some test invocations using the AWS CLI. The function is invoking correctly and returning a success message, but not log data is being generated in Amazon CloudWatch Logs. The developer waited for 15 minutes but the log data is not showing up.

What is the most likely explanation for this issue?

- ▫️ The function configuration does not have CloudWatch Logs configured as a success destination.
- ▫️ A log group and log stream has not been configured for the function in CloudWatch Logs.
- ✅ The function execution role does not have permission to write log data to CloudWatch Logs.
- ▫️ The Lambda function does not have any explicit log statements for the log data to send it to CloudWatch Logs.

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Lambda automatically monitors Lambda functions on your behalf, reporting metrics through Amazon CloudWatch. To help you troubleshoot failures in a function, after you set up permissions, Lambda logs all requests handled by your function and automatically stores logs generated by your code through Amazon CloudWatch Logs.

You can insert logging statements into your code to help you validate that your code is working as expected. Lambda automatically integrates with CloudWatch Logs and pushes all logs from your code to a CloudWatch Logs group associated with a Lambda function, which is named /aws/lambda/&lt;function name&gt;.

It can take 5-10 minutes for logs to show up after a function invocation. If your Lambda function code is executing, but you don't see any log data being generated after several minutes, this could mean that your execution role for the Lambda function didn't grant permissions to write log data to CloudWatch Logs.

CORRECT: "The function execution role does not have permission to write log data to CloudWatch Logs" is the correct answer (as explained above.)

INCORRECT: "The Lambda function does not have any explicit log statements for the log data to send it to CloudWatch Logs" is incorrect.

You do need to have logging statements in your code to send meaningful data to CloudWatch Logs. However, the most likely cause of having nothing show up is that the permissions were not assigned.

INCORRECT: "The function configuration does not have CloudWatch Logs configured as a success destination" is incorrect.

CloudWatch Logs is not configured as a destination in a Lambda function.

INCORRECT: "A log group and log stream has not been configured for the function in CloudWatch Logs" is incorrect.

The log group and log stream are automatically created as long as permissions are assigned.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html>

</details>

---

<a id="t05-q21"></a>
### T05-Q21
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **On-prem → CloudWatch agent + IAM credentials.** → [детальніше](../../06-contradictions-and-traps.md#c15)

An application uses both Amazon EC2 instances and on-premises servers. The on-premises servers are a critical component of the application, and a developer wants to collect metrics and logs from these servers. The developer would like to use Amazon CloudWatch.

How can the developer accomplish this?

- ▫️ Write a batch script that uses system utilities to collect performance metrics and application logs. Upload the metrics and logs to CloudWatch.
- ✅ Install the CloudWatch agent on the on-premises servers and specify IAM credentials with permissions to CloudWatch.
- ▫️ Install an AWS SDK on the on-premises servers that automatically sends logs to CloudWatch.
- ▫️ Install the CloudWatch agent on the on-premises servers and specify an IAM role with permissions to CloudWatch.

<details><summary>Пояснення (оригінал, EN)</summary>

You can download the CloudWatch agent package using either Systems Manager Run Command or an Amazon S3 download link. You then install the agent and specify the IAM credentials to use. The IAM credentials are an access key and secret access key of an IAM user that has permissions to Amazon CloudWatch.

Once this has been completed the on-premises servers will automatically send metrics and log files to Amazon CloudWatch and can be centrally monitored along with AWS services.

CORRECT: Install the CloudWatch agent on the on-premises servers and specify IAM credentials with permissions to CloudWatch" is the correct answer (as explained above.)

INCORRECT: "Install the CloudWatch agent on the on-premises servers and specify an IAM role with permissions to CloudWatch" is incorrect.

You cannot specify a role with an on-premises server so you must use access keys instead.

INCORRECT: "Write a batch script that uses system utilities to collect performance metrics and application logs. Upload the metrics and logs to CloudWatch" is incorrect.

The CloudWatch agent would be a better solution and you must have permissions to send this information to CloudWatch.

INCORRECT: "Install an AWS SDK on the on-premises servers that automatically sends logs to CloudWatch" is incorrect.

The CloudWatch agent would be a better solution and you must have permissions to send this information to CloudWatch.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html>

</details>

---

<a id="t05-q25"></a>
### T05-Q25
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer wants to debug an application by searching and filtering log data. The application logs are stored in Amazon CloudWatch Logs. The Developer creates a new metric filter to count exceptions in the application logs. However, no results are returned from the logs.

What is the reason that no filtered results are being returned?

- ▫️ A setup of the Amazon CloudWatch interface VPC endpoint is required for filtering the CloudWatch Logs in the VPC
- ✅ CloudWatch Logs only publishes metric data for events that happen after the filter is created
- ▫️ Metric data points to logs groups can be filtered only after they are exported to an Amazon S3 bucket
- ▫️ The log group for CloudWatch Logs should be first streamed to Amazon Elasticsearch Service before filtering returns the results

<details><summary>Пояснення (оригінал, EN)</summary>

After the CloudWatch Logs agent begins publishing log data to Amazon CloudWatch, you can begin searching and filtering the log data by creating one or more metric filters. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs.

CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on. You can use any type of CloudWatch statistic, including percentile statistics, when viewing these metrics or setting alarms.

Filters do not retroactively filter data. Filters only publish the metric data points for events that happen after the filter was created. Filtered results return the first 50 lines, which will not be displayed if the timestamp on the filtered results is earlier than the metric creation time.

Therefore, the filtered results are not being returned as CloudWatch Logs only publishes metric data for events that happen after the filter is created.

CORRECT: "CloudWatch Logs only publishes metric data for events that happen after the filter is created" is the correct answer.

INCORRECT: "A setup of the Amazon CloudWatch interface VPC endpoint is required for filtering the CloudWatch Logs in the VPC" is incorrect as a VPC endpoint is not required.

INCORRECT: "The log group for CloudWatch Logs should be first streamed to Amazon Elasticsearch Service before filtering returns the results" is incorrect as you do not need to stream the results to Elasticsearch.

INCORRECT: "Metric data points to logs groups can be filtered only after they are exported to an Amazon S3 bucket" is incorrect as it is not necessary to export the logs to an S3 bucket.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t05-q35"></a>
### T05-Q35
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application is instrumented to generate traces using AWS X-Ray and generates a large amount of trace data. A Developer would like to use filter expressions to filter the results to specific key-value pairs added to custom subsegments.

How should the Developer add the key-value pairs to the custom subsegments?

- ▫️ Add metadata to the custom subsegments
- ✅ Add annotations to the custom subsegments
- ▫️ Add the key-value pairs to the Trace ID
- ▫️ Setup sampling for the custom subsegments

<details><summary>Пояснення (оригінал, EN)</summary>

You can record additional information about requests, the environment, or your application with annotations and metadata. You can add annotations and metadata to the segments that the X-Ray SDK creates, or to custom subsegments that you create.

Annotations are key-value pairs with string, number, or Boolean values. Annotations are indexed for use with filter expressions. Use annotations to record data that you want to use to group traces in the console, or when calling the GetTraceSummaries API.

Metadata are key-value pairs that can have values of any type, including objects and lists, but are not indexed for use with filter expressions. Use metadata to record additional data that you want stored in the trace but don't need to use with search.

Annotations can be used with filter expressions, so this is the best solution for this requirement. The Developer can add annotations to the custom subsegments and will then be able to use filter expressions to filter the results in AWS X-Ray.

CORRECT: "Add annotations to the custom subsegments" is the correct answer.

INCORRECT: "Add metadata to the custom subsegments" is incorrect as though you can add metadata to custom subsegments it is not indexed and cannot be used with filters.

INCORRECT: "Add the key-value pairs to the Trace ID" is incorrect as this is not something you can do.

INCORRECT: "Setup sampling for the custom subsegments " is incorrect as this is a mechanism used by X-Ray to send only statistically significant data samples to the API.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-java-segment.html>

</details>

---

<a id="t05-q43"></a>
### T05-Q43
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Development team has deployed several applications running on an Auto Scaling fleet of Amazon EC2 instances. The Operations team have asked for a display that shows a key performance metric for each application on a single screen for monitoring purposes.

What steps should a Developer take to deliver this capability using Amazon CloudWatch?

- ▫️ Create a custom alarm with a unique metric name for each application
- ▫️ Create a custom event with a unique metric name for each application
- ✅ Create a custom namespace with a unique metric name for each application
- ▫️ Create a custom dimension with a unique metric name for each application

<details><summary>Пояснення (оригінал, EN)</summary>

A namespace is a container for CloudWatch metrics. Metrics in different namespaces are isolated from each other, so that metrics from different applications are not mistakenly aggregated into the same statistics.

Therefore, the Developer should create a custom namespace with a unique metric name for each application. This namespace will then allow the metrics for each individual application to be shown in a single view through CloudWatch.

CORRECT: "Create a custom namespace with a unique metric name for each application" is the correct answer.

INCORRECT: "Create a custom dimension with a unique metric name for each application" is incorrect as a dimension further clarifies what a metric is and what data it stores.

INCORRECT: "Create a custom event with a unique metric name for each application" is incorrect as an event is not used to organize metrics for display.

INCORRECT: "Create a custom alarm with a unique metric name for each application" is incorrect as alarms are used to trigger actions when a threshold is reached, this is not relevant to organizing metrics for display.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html>

</details>

---

<a id="t05-q46"></a>
### T05-Q46
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application has been instrumented to use the AWS X-Ray SDK to collect data about the requests the application serves. The Developer has set the user field on segments to a string that identifies the user who sent the request.

How can the Developer search for segments associated with specific users?

- ▫️ By using the GetTraceGraph API with a filter expression
- ▫️ Use a filter expression to search for the user field in the segment annotations
- ✅ By using the GetTraceSummaries API with a filter expression
- ▫️ Use a filter expression to search for the user field in the segment metadata

<details><summary>Пояснення (оригінал, EN)</summary>

A segment document conveys information about a segment to X-Ray. A segment document can be up to 64 kB and contain a whole segment with subsegments, a fragment of a segment that indicates that a request is in progress, or a single subsegment that is sent separately. You can send segment documents directly to X-Ray by using the PutTraceSegments API.

Example minimally complete segment:

{
"name" : "example.com",
"id" : "70de5b6f19ff9a0a",
"start_time" : 1.478293361271E9,
"trace_id" : "1-581cf771-a006649127e371903a2de979",
"end_time" : 1.478293361449E9
}

A subset of segment fields are indexed by X-Ray for use with filter expressions. For example, if you set the user field on a segment to a unique identifier, you can search for segments associated with specific users in the X-Ray console or by using the GetTraceSummaries API.

CORRECT: "By using the GetTraceSummaries API with a filter expression" is the correct answer.

INCORRECT: "By using the GetTraceGraph API with a filter expression" is incorrect as this API action retrieves a service graph for one or more specific trace IDs.

INCORRECT: "Use a filter expression to search for the user field in the segment metadata" is incorrect as the user field is not part of the segment metadata and metadata is not is not indexed for search.

INCORRECT: "Use a filter expression to search for the user field in the segment annotations" is incorrect as the user field is not part of the segment annotations.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html>

</details>

---

<a id="t06-q06"></a>
### T06-Q06
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A Developer is attempting to call the Amazon CloudWatch API and is receiving HTTP 400: ThrottlingException errors intermittently. When a call fails, no data is retrieved.

What best practice should the Developer first attempt to resolve this issue?

- ▫️ Analyze the applications and remove the API call
- ▫️ Contact AWS Support for a limit increase
- ▫️ Use the AWS CLI to get the metrics
- ✅ Retry the call with exponential backoff

<details><summary>Пояснення (оригінал, EN)</summary>

Occasionally ,you may receive the 400 ThrottlingException error for PutMetricData API calls in Amazon CloudWatch with a detailed response similar the following:

CloudWatch requests are throttled for each Amazon Web Services (AWS) account on a per-Region basis to help service performance. For current PutMetricData API request limits, see CloudWatch Limits.

All calls to the PutMetricData API in an AWS Region count towards the maximum allowed request rate. This number includes calls from any custom or third-party application, such as calls from the CloudWatch Agent, the AWS Command Line Interface (AWS CLI), or the AWS Management Console.

Resolutions: It's a best practice to use the following methods to reduce your call rate and avoid API throttling:

- Distribute your API calls evenly over time rather than making several API calls in a short time span. If you require data to be available with a one-minute resolution, you have an entire minute to emit that metric. Use jitter (randomized delay) to send data points at various times.

- Combine as many metrics as possible into a single API call. For example, a single PutMetricData call can include 20 metrics and 150 data points. You can also use pre-aggregated data sets, such as StatisticSet, to publish aggregated data points, thus reducing the number of PutMetricData calls per second.

- Retry your call with exponential backoff and jitter.

Following attempting the above resolutions AWS suggest the following: “If you still require a higher limit, you can request a limit increase. Increasing the rate limit can have a high financial impact on your AWS bill.”

Therefore, the first thing the Developer should do, from the list of options presented, is to retry the call with exponential backoff.

CORRECT: "Retry the call with exponential backoff" is the correct answer.

INCORRECT: "Contact AWS Support for a limit increase" is incorrect. As mentioned above, there are other resolutions the Developer should attempt before contacting support to raise the limit.

INCORRECT: "Use the AWS CLI to get the metrics" is incorrect as this will still make the same API calls.

INCORRECT: "Analyze the applications and remove the API call" is incorrect as this is not a good resolution to the issue as this may mean that important monitoring and logging data is not recorded for the application.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-400-error-throttling/>

</details>

---

<a id="t06-q17"></a>
### T06-Q17
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

There are multiple AWS accounts across multiple regions managed by a company. The operations team require a single operational dashboard that displays some key performance metrics from these accounts and regions. What is the SIMPLEST solution?

- ▫️ Create an AWS Lambda function that collects metrics from each account and region and pushes the metrics to the account where the dashboard has been created
- ▫️ Create an Amazon CloudTrail trail that applies to all regions and deliver the logs to a single Amazon S3 bucket. Create a dashboard using the data in the bucket
- ✅ Create an Amazon CloudWatch cross-account cross-region dashboard
- ▫️ Create an Amazon CloudWatch dashboard in one account and region and import the data from the other accounts and regions

<details><summary>Пояснення (оригінал, EN)</summary>

You can create cross-account cross-Region dashboards, which summarize your CloudWatch data from multiple AWS accounts and multiple Regions into one dashboard. From this high-level dashboard you can get a view of your entire application, and also drill down into more specific dashboards without having to log in and out of accounts or switch Regions.

You can create cross-account cross-Region dashboards in the AWS Management Console and programmatically.

CORRECT: "Create an Amazon CloudWatch cross-account cross-region dashboard" is the correct answer.

INCORRECT: "Create an Amazon CloudWatch dashboard in one account and region and import the data from the other accounts and regions" is incorrect as this is more complex and unnecessary.

INCORRECT: "Create an AWS Lambda function that collects metrics from each account and region and pushes the metrics to the account where the dashboard has been created" is incorrect as this is not a simple solution.

INCORRECT: "Create an Amazon CloudTrail trail that applies to all regions and deliver the logs to a single Amazon S3 bucket. Create a dashboard using the data in the bucket" is incorrect as CloudTrail logs API activity, not performance metrics.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_xaxr_dashboard.html>

</details>

---

<a id="t06-q55"></a>
### T06-Q55
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

Customers who use a REST API have reported performance issues. A Developer needs to measure the time between when API Gateway receives a request from a client and when it returns a response to the client.

Which metric should the Developer monitor?

- ▫️ 5XXError
- ▫️ CacheHitCount
- ✅ Latency
- ▫️ IntegrationLatency

<details><summary>Пояснення (оригінал, EN)</summary>

The Latency metric measures the time between when API Gateway receives a request from a client and when it returns a response to the client. The latency includes the integration latency and other API Gateway overhead.

CORRECT: "Latency" is the correct answer.

INCORRECT: "IntegrationLatency" is incorrect. This measures the time between when API Gateway relays a request to the backend and when it receives a response from the backend.

INCORRECT: "CacheHitCount" is incorrect. This measures the number of requests served from the API cache in a given period.

INCORRECT: "5XXError" is incorrect. This measures the number of server-side errors captured in a given period.

Джерела: <https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-metrics-and-dimensions.html>

</details>

---

<a id="t07-q11"></a>
### T07-Q11
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **RAM → custom metric PutMetricData.** → [детальніше](../../06-contradictions-and-traps.md#c15)

As a developer, you are looking at creating a custom configuration for Amazon EC2 instances running in an Auto Scaling group. The solution should allow the group to auto-scale based on the metric of 'average RAM usage' for your Amazon EC2 instances.

Which option provides the best solution?

- ✅ Create a custom metric in CloudWatch and make your instances send data to it using PutMetricData. Then, create an alarm based on this metric
- ▫️ Migrate your application to AWS Lambda
- ▫️ Enable detailed monitoring for EC2 and ASG to get the RAM usage data and create a CloudWatch Alarm on top of it
- ▫️ Create a custom alarm for your ASG and make your instances trigger the alarm using PutAlarmData API

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a custom metric in CloudWatch and make your instances send data to it using PutMetricData. Then, create an alarm based on this metric - You can create a custom CloudWatch metric for your EC2 Linux instance statistics by creating a script through the AWS Command Line Interface (AWS CLI). Then, you can monitor that metric by pushing it to CloudWatch.

You can publish your own metrics to CloudWatch using the AWS CLI or an API. Metrics produced by AWS services are standard resolution by default. When you publish a custom metric, you can define it as either standard resolution or high resolution. When you publish a high-resolution metric, CloudWatch stores it with a resolution of 1 second, and you can read and retrieve it with a period of 1 second, 5 seconds, 10 seconds, 30 seconds, or any multiple of 60 seconds.

High-resolution metrics can give you more immediate insight into your application's sub-minute activity. But, every PutMetricData call for a custom metric is charged, so calling PutMetricData more often on a high-resolution metric can lead to higher charges.

Incorrect options:

Create a custom alarm for your ASG and make your instances trigger the alarm using PutAlarmData API - This solution will not work, your instances must be aware of each other's RAM utilization status, to know when the average RAM would be too high to trigger the alarm.

Enable detailed monitoring for EC2 and ASG to get the RAM usage data and create a CloudWatch Alarm on top of it - By enabling detailed monitoring you define the frequency at which the metric data has to be sent to CloudWatch, from 5 minutes to 1-minute frequency window. But, you still need to create and collect the custom metric you wish to track.

Migrate your application to AWS Lambda - This option has been added as a distractor. You cannot use Lambda for the given use-case.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html> · <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-instance-monitoring.html#CloudWatchAlarm>

</details>

---

<a id="t07-q17"></a>
### T07-Q17
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **X-Ray daemon у maintenance з 25.02.2026.** → [детальніше](../../07-outdated-and-wrong-answers.md#o05)

A developer wants to enable X-Ray tracing on an on-premises Linux server running a custom application that is accessed through Amazon API Gateway.

What is the most efficient solution that requires minimal configuration?

- ▫️ Install and run the CloudWatch Unified Agent on the on-premises servers to capture and relay the X-Ray data to the X-Ray service using the PutTraceSegments API call
- ▫️ Configure a Lambda function to analyze the incoming traffic data on the on-premises servers and then relay the X-Ray data to the X-Ray service using the PutTelemetryRecords API call
- ▫️ Install and run the X-Ray SDK on the on-premises servers to capture and relay the data to the X-Ray service
- ✅ Install and run the X-Ray daemon on the on-premises servers to capture and relay the data to the X-Ray service

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Install and run the X-Ray daemon on the on-premises servers to capture and relay the data to the X-Ray service

The AWS X-Ray daemon is a software application that listens for traffic on UDP port 2000, gathers raw segment data, and relays it to the AWS X-Ray API. The daemon works in conjunction with the AWS X-Ray SDKs and must be running so that data sent by the SDKs can reach the X-Ray service.

To run the X-Ray daemon locally, on-premises, or on other AWS services, download it, run it, and then give it permission to upload segment documents to X-Ray.

Incorrect options:

Install and run the X-Ray SDK on the on-premises servers to capture and relay the data to the X-Ray service - As mentioned above, you need to run the X-Ray daemon on the on-premises servers and give it the required permission to upload X-Ray data to the X-Ray service. So this option is incorrect.

Install and run the CloudWatch Unified Agent on the on-premises servers to capture and relay the X-Ray data to the X-Ray service using the PutTraceSegments API call - This option has been added as a distractor. CloudWatch Agent cannot relay X-Ray data to the X-Ray service using the PutTraceSegments API call.

Configure a Lambda function to analyze the incoming traffic data on the on-premises servers and then relay the X-Ray data to the X-Ray service using the PutTelemetryRecords API call - This option is incorrect as the Lambda function cannot process the X-Ray data for an on-premises instance and then relay it to the X-Ray service.

Reference:

https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html

</details>

---

<a id="t07-q63"></a>
### T07-Q63
*⏭ пропущено · домен: офіційна мітка*

An IT company has its serverless stack integrated with AWS X-Ray. The developer at the company has noticed a high volume of data going into X-Ray and the AWS monthly usage charges have skyrocketed as a result. The developer has requested changes to mitigate the issue.

As a Developer Associate, which of the following solutions would you recommend to obtain tracing trends while reducing costs with minimal disruption?

- ▫️ Custom configuration for the X-Ray agents
- ✅ Enable X-Ray sampling
- ▫️ Implement a network sampling rule
- ▫️ Use Filter Expressions in the X-Ray console

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

How X-Ray Works:  via - https://aws.amazon.com/xray/

Enable X-Ray sampling

To ensure efficient tracing and provide a representative sample of the requests that your application serves, the X-Ray SDK applies a sampling algorithm to determine which requests get traced. By default, the X-Ray SDK records the first request each second, and five percent of any additional requests. X-Ray sampling is enabled directly from the AWS console, hence your application code does not need to change.

You can also customize the X-Ray sampling rules:  via - https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html

Incorrect options:

Use Filter Expressions in the X-Ray console - When you choose a time period of traces to view in the X-Ray console, you might get more results than the console can display. You can narrow the results to just the traces that you want to find by using a filter expression. This option is not correct because it does not reduce the volume of data sent into the X-Ray console.

 via - https://docs.aws.amazon.com/xray/latest/devguide/xray-console-filters.html

Custom configuration for the X-Ray agents - You cannot do a custom configuration, instead you can do custom sampling rules. So this option is incorrect.

Implement a network sampling rule - This option has been added as a distractor.

Джерела: <https://aws.amazon.com/xray/> · <https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html>

</details>

---

<a id="t08-q06"></a>
### T08-Q06
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Application Integration)*

A Developer manages a monitoring service for a fleet of IoT sensors in a major city. The monitoring application uses an Amazon Kinesis Data Stream with a group of EC2 instances processing the data. Amazon CloudWatch custom metrics show that the instances a reaching maximum processing capacity and there are insufficient shards in the Data Stream to handle the rate of data flow.

What course of action should the Developer take to resolve the performance issues?

- ▫️ Increase the number of EC2 instances to match the number of shards
- ✅ Increase the EC2 instance size and add shards to the stream
- ▫️ Increase the EC2 instance size
- ▫️ Increase the number of open shards

<details><summary>Пояснення (оригінал, EN)</summary>

By increasing the instance size and number of shards in the Kinesis stream, the developer can allow the instances to handle more record processors, which are running in parallel within the instance. It also allows the stream to properly accommodate the rate of data being sent in. The data capacity of your stream is a function of the number of shards that you specify for the stream. The total capacity of the stream is the sum of the capacities of its shards.

Therefore, the best answer is to increase both the EC2 instance size and add shards to the stream.

CORRECT: "Increase the EC2 instance size and add shards to the stream" is the correct answer.

INCORRECT: "Increase the number of EC2 instances to match the number of shards" is incorrect as you can have an individual instance running multiple KCL workers.

INCORRECT: "Increase the EC2 instance size" is incorrect as the Developer would also need to add shards to the stream to increase the capacity of the stream.

INCORRECT: "Increase the number of open shards" is incorrect as this does not include increasing the instance size or quantity which is required as they are running at capacity.

https://docs.aws.amazon.com/streams/latest/dev/kinesis-record-processor-scaling.partial.html

https://docs.aws.amazon.com/streams/latest/dev/developing-consumers-with-kcl.html

</details>

---

<a id="t08-q17"></a>
### T08-Q17
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An AWS developer is building an application that processes sensitive personally identifiable information (PII). The application operates on AWS Lambda and writes diagnostic data to Amazon CloudWatch Logs. However, the developer wants to ensure that PII is not accidentally stored in CloudWatch Logs.
What strategy should the developer adopt to ensure this?

- ▫️ Manually insert logging commands into the application code while ensuring PII is not included.
- ▫️ Incorporate AWS X-Ray and configure it to filter out sensitive PII before logging.
- ✅ Configure Amazon CloudWatch Logs data protection to detect and mask PII in log events (using managed data identifiers) before the data is stored.
- ▫️ Implement AWS Secrets Manager for secure logging of sensitive information.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon CloudWatch Logs data protection can automatically detect and mask sensitive data (including common types of PII) in log events by using managed data identifiers and data protection policies. This helps prevent accidental storage of PII in CloudWatch Logs even if the application logs it by mistake.

CORRECT: “Configure Amazon CloudWatch Logs data protection to detect and mask PII in log events (using managed data identifiers) before the data is stored” is the correct answer (as explained above.)

INCORRECT: “Manually insert logging commands into the application code while ensuring PII is not included” is incorrect.
Good logging hygiene helps, but it relies on developers never making mistakes and does not provide an AWS-managed safeguard if PII is accidentally logged.

INCORRECT: “Implement AWS Secrets Manager for secure logging of sensitive information” is incorrect.
AWS Secrets Manager stores and rotates secrets (like passwords and API keys). It does not prevent PII from being written to CloudWatch Logs.

INCORRECT: “Incorporate AWS X-Ray and configure it to filter out sensitive PII before logging” is incorrect.
AWS X-Ray is for tracing and performance visibility. It does not filter or mask application log lines being written to CloudWatch Logs.

Джерела: <https://digitalcloud.training/amazon-cloudwatch/>

</details>

---

<a id="t08-q24"></a>
### T08-Q24
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A development team have deployed a new application and users have reported some performance issues. The developers need to enable monitoring for specific metrics with a data granularity of one second. How can this be achieved?

- ▫️ Do nothing, CloudWatch uses standard resolution metrics by default
- ▫️ Create custom metrics and configure them as standard resolution
- ✅ Create custom metrics and configure them as high resolution
- ▫️ Create custom metrics and enable detailed monitoring

<details><summary>Пояснення (оригінал, EN)</summary>

You can publish your own metrics to CloudWatch using the AWS CLI or an API. You can view statistical graphs of your published metrics with the AWS Management Console.

CloudWatch stores data about a metric as a series of data points. Each data point has an associated time stamp. You can even publish an aggregated set of data points called a statistic set.

Each metric is one of the following:

• Standard resolution, with data having a one-minute granularity

• High resolution, with data at a granularity of one second

Metrics produced by AWS services are standard resolution by default. When you publish a custom metric, you can define it as either standard resolution or high resolution. When you publish a high-resolution metric, CloudWatch stores it with a resolution of 1 second, and you can read and retrieve it with a period of 1 second, 5 seconds, 10 seconds, 30 seconds, or any multiple of 60 seconds.

High-resolution metrics can give you more immediate insight into your application's sub-minute activity. Keep in mind that every PutMetricData call for a custom metric is charged, so calling PutMetricData more often on a high-resolution metric can lead to higher charges.

Therefore, the best action to take is to Create custom metrics and configure them as high resolution. This will ensure that granularity can be down to 1 second.

CORRECT: "Create custom metrics and configure them as high resolution" is the correct answer.

INCORRECT: "Do nothing, CloudWatch uses standard resolution metrics by default" is incorrect as standard resolution has a granularity of one-minute.

INCORRECT: "Create custom metrics and configure them as standard resolution" is incorrect as standard resolution has a granularity of one-minute.

INCORRECT: "Create custom metrics and enable detailed monitoring" is incorrect as detailed monitoring has a granularity of one-minute.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html>

</details>

---

<a id="t08-q55"></a>
### T08-Q55
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A company is migrating several applications to the AWS cloud. The security team has strict security requirements and mandate that a log of all API calls to AWS resources must be maintained.

Which AWS service should be used to record this information for the security team?

- ▫️ Amazon CloudWatch
- ✅ AWS CloudTrail
- ▫️ Amazon CloudWatch Logs
- ▫️ AWS X-Ray

<details><summary>Пояснення (оригінал, EN)</summary>

AWS CloudTrail is a web service that records activity made on your account. A CloudTrail trail can be created which delivers log files to an Amazon S3 bucket. CloudTrail is about logging and saves a history of API calls for your AWS account. It enables governance, compliance, and operational and risk auditing of your AWS account.

Therefore, AWS CloudTrail is the best solution for maintaining a log of API calls for the security team.

CORRECT: "AWS CloudTrail" is the correct answer.

INCORRECT: "Amazon CloudWatch" is incorrect as this service records metrics related to performance.

INCORRECT: "Amazon CloudWatch Logs" is incorrect as this records log files from services and applications, it does not record a history of API activity.

INCORRECT: "AWS X-Ray" is incorrect as this is used for tracing applications to view performance-related statistics.

Джерела: <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-concepts.html>

</details>

---

<a id="t08-q64"></a>
### T08-Q64
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

A developer is debugging an application by sifting through log data stored in Amazon CloudWatch Logs. A fresh metric filter has been established to identify exceptions in these logs. Yet, the logs are not returning any results filtered through the new metric.

What could be the reason behind the absence of filtered results?

- ▫️ The CloudWatch Logs agent hasn't been installed on the EC2 instances.
- ▫️ The time range for the filter hasn't been properly configured.
- ▫️ The application logs have been archived to Amazon S3, making them non-filterable.
- ✅ CloudWatch Logs only publish metric data for events that occur after the filter has been established.

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon CloudWatch Logs starts to publish metric data points only for log events that are ingested after the metric filter is created. This means the filter does not apply retrospectively to log data ingested prior to its creation.

CORRECT: "CloudWatch Logs only publish metric data for events that occur after the filter has been established" is the correct answer (as explained above.)

INCORRECT: "The CloudWatch Logs agent hasn't been installed on the EC2 instances" is incorrect.

The CloudWatch Logs agent is responsible for sending logs to CloudWatch and does not need to be installed for filtering to work.

INCORRECT: "The application logs have been archived to Amazon S3, making them non-filterable" is incorrect.

CloudWatch Logs can still filter logs even if they have been archived to Amazon S3.

INCORRECT: "The time range for the filter hasn't been properly configured" is incorrect.

The time range for a metric filter is not relevant as it operates on new logs, regardless of their timestamp.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t09-q04"></a>
### T09-Q04
*⏭ пропущено · домен: офіційна мітка*

A serverless application built on AWS processes customer orders 24/7 using an AWS Lambda function and communicates with an external vendor's HTTP API for payment processing. The development team wants to notify the support team in near real-time using an existing Amazon Simple Notification Service (Amazon SNS) topic, but only when the external API error rate exceeds 5% of the total transactions processed in an hour.

As an AWS Certified Developer Associate, which option will you suggest as the most efficient solution?

- ▫️ Log the results of payment processing API calls to Amazon CloudWatch. Leverage Amazon CloudWatch Logs Insights to query the CloudWatch logs. Set up the Lambda function to check the output from CloudWatch Logs Insights on a schedule and send notification via the existing SNS topic when the error rate exceeds the specified rate
- ▫️ Configure CloudWatch metrics with detailed monitoring for the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate
- ▫️ Log the results of payment processing API calls to Amazon CloudWatch. Leverage Amazon CloudWatch Metric Filter to look at the CloudWatch logs. Set up the Lambda function to check the output from CloudWatch Metric Filter on a schedule and send notification via the existing SNS topic when the error rate exceeds the specified rate
- ✅ Configure and push high-resolution custom metrics to CloudWatch that record the failures of the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Configure and push high-resolution custom metrics to CloudWatch that record the failures of the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate

You can publish your own metrics, known as custom metrics, to CloudWatch using the AWS CLI or an API.

Each metric is one of the following:

Standard resolution, with data having a one-minute granularity

High resolution, with data at a granularity of one second

Metrics produced by AWS services are standard resolution by default. When you publish a custom metric, you can define it as either standard resolution or high resolution. When you publish a high-resolution metric, CloudWatch stores it with a resolution of 1 second, and you can read and retrieve it with a period of 1 second, 5 seconds, 10 seconds, 30 seconds, or any multiple of 60 seconds.

High-resolution metrics can give you more immediate insight into your application's sub-minute activity. Keep in mind that every PutMetricData call for a custom metric is charged, so calling PutMetricData more often on a high-resolution metric can lead to higher charges.

You can create metric and composite alarms in Amazon CloudWatch. For the given use case, you can set up a CloudWatch metric alarm that watches the custom metric that captures the API errors and then triggers the alarm when the API error rate exceeds the 5% threshold. The alarm then sends a notification via the existing SNS topic.

Incorrect options:

Configure CloudWatch metrics with detailed monitoring for the external payment processing API calls. Create a CloudWatch alarm that sends a notification via the existing SNS topic when the error rate exceeds the specified rate - CloudWatch provides two categories of monitoring: basic monitoring and detailed monitoring. Detailed monitoring options differ based on the services that offer it. For example, Amazon EC2 detailed monitoring provides more frequent metrics, published at one-minute intervals, instead of the five-minute intervals used in Amazon EC2 basic monitoring. Detailed monitoring is offered by only some services. As explained above, you need to use custom metrics to capture data for the external payment processing API calls since detailed monitoring for the standard CloudWatch metrics cannot be used for this scenario.

Log the results of payment processing API calls to Amazon CloudWatch. Leverage Amazon CloudWatch Logs Insights to query the CloudWatch logs. Set up the Lambda function to check the output from CloudWatch Logs Insights on a schedule and send notification via the existing SNS topic when the error rate exceeds the specified rate - CloudWatch Logs Insights enables you to interactively search and analyze your log data in Amazon CloudWatch Logs. You can perform queries to help you more efficiently and effectively respond to operational issues. This option is not the right fit for the given use case since Lambda cannot monitor the output of the CloudWatch Logs Insights on a real-time basis since it is being invoked on a schedule. Also, it is not an efficient solution since Lambda will need significant custom code to parse and compute the external API error rate from the CloudWatch Logs Insights data.

Log the results of payment processing API calls to Amazon CloudWatch. Leverage Amazon CloudWatch Metric Filter to look at the CloudWatch logs. Set up the Lambda function to check the output from CloudWatch Metric Filter on a schedule and send notification via the existing SNS topic when the error rate exceeds the specified rate - You can search and filter the log data coming into CloudWatch Logs by creating one or more metric filters. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs. CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on. This option is not the best fit for the given use case since Lambda cannot monitor the output of the CloudWatch Metric Filter on a real-time basis since it is being invoked on a schedule. Also, it is not an efficient solution since Lambda will need significant custom code to parse and compute the external API error rate from the CloudWatch Metric Filter data.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-push-custom-metrics/> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html>

</details>

---

<a id="t09-q57"></a>
### T09-Q57
*⏭ пропущено · домен: офіційна мітка*

Recently in your organization, the AWS X-Ray SDK was bundled into each Lambda function to record outgoing calls for tracing purposes. When your team leader goes to the X-Ray service in the AWS Management Console to get an overview of the information collected, they discover that no data is available.

What is the most likely reason for this issue?

- ✅ Fix the IAM Role
- ▫️ Change the security group rules
- ▫️ Enable X-Ray sampling
- ▫️ X-Ray only works with AWS Lambda aliases

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

How X-Ray Works:  via - https://aws.amazon.com/xray/

Fix the IAM Role

Create an IAM role with write permissions and assign it to the resources running your application. You can use AWS Identity and Access Management (IAM) to grant X-Ray permissions to users and compute resources in your account. This should be one of the first places you start by checking that your permissions are properly configured before exploring other troubleshooting options.

Here is an example of X-Ray Read-Only permissions via an IAM policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "xray:GetSamplingRules",
                "xray:GetSamplingTargets",
                "xray:GetSamplingStatisticSummaries",
                "xray:BatchGetTraces",
                "xray:GetServiceGraph",
                "xray:GetTraceGraph",
                "xray:GetTraceSummaries",
                "xray:GetGroups",
                "xray:GetGroup"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
Another example of write permissions for using X-Ray via an IAM policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "xray:PutTraceSegments",
                "xray:PutTelemetryRecords",
                "xray:GetSamplingRules",
                "xray:GetSamplingTargets",
                "xray:GetSamplingStatisticSummaries"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
Incorrect options:

Enable X-Ray sampling - If permissions are not configured correctly sampling will not work, so this option is not correct.

X-Ray only works with AWS Lambda aliases - This is not true, aliases are pointers to specific Lambda function versions. To use the X-Ray SDK on Lambda, bundle it with your function code each time you create a new version.

Change the security group rules - You grant permissions to your Lambda function to access other resources using an IAM role and not via security groups.

Reference:

https://docs.aws.amazon.com/xray/latest/devguide/security_iam_troubleshoot.html

</details>

---

<a id="t10-q19"></a>
### T10-Q19
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **CloudWatch Events schedule = EventBridge.** → [детальніше](../../06-contradictions-and-traps.md#c14)

A Developer has created a serverless function that processes log files. The function should be invoked once every 15 minutes. How can the Developer automatically invoke the function using serverless services?

- ▫️ Configure the Lambda scheduler to run based on recurring time value
- ✅ Create an Amazon CloudWatch Events rule that is scheduled to run and invoke the function
- ▫️ Launch an EC2 Linux instance and add a command to periodically invoke the function to its /etc/crontab file
- ▫️ Create an Amazon SNS rule to send a notification to Lambda to instruct it to run

<details><summary>Пояснення (оригінал, EN)</summary>

Amazon CloudWatch Events delivers a near real-time stream of system events that describe changes in Amazon Web Services (AWS) resources. Using simple rules that you can quickly set up, you can match events and route them to one or more target functions or streams.

You can use Amazon CloudWatch Events to invoke the Lambda function on a recurring schedule of 15 minutes. This solution is entirely automated and serverless.

CORRECT: "Create an Amazon CloudWatch Events rule that is scheduled to run and invoke the function" is the correct answer.

INCORRECT: "Launch an EC2 Linux instance and add a command to periodically invoke the function to its /etc/crontab file " is incorrect as this is automatic but it is not serverless.

INCORRECT: "Configure the Lambda scheduler to run based on recurring time value" is incorrect as there is no Lambda scheduler that can be used.

INCORRECT: "Create an Amazon SNS rule to send a notification to Lambda to instruct it to run" is incorrect as you cannot invoke a function by sending a notification to it from Amazon SNS.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/RunLambdaSchedule.html>

</details>

---

<a id="t10-q36"></a>
### T10-Q36
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Developer Tools)*

An application is being instrumented to send trace data using AWS X-Ray. A Developer needs to upload segment documents using JSON-formatted strings to X-Ray using the API. Which API action should the developer use?

- ✅ The PutTraceSegments API action
- ▫️ The GetTraceSummaries API action
- ▫️ The UpdateGroup API action
- ▫️ The PutTelemetryRecords API action

<details><summary>Пояснення (оригінал, EN)</summary>

You can send trace data to X-Ray in the form of segment documents. A segment document is a JSON formatted string that contains information about the work that your application does in service of a request. Your application can record data about the work that it does itself in segments, or work that uses downstream services and resources in subsegments.

Segments record information about the work that your application does. A segment, at a minimum, records the time spent on a task, a name, and two IDs. The trace ID tracks the request as it travels between services. The segment ID tracks the work done for the request by a single service.

Example Minimal complete segment:

You can upload segment documents with the PutTraceSegments API. The API has a single parameter, TraceSegmentDocuments, that takes a list of JSON segment documents.

Therefore, the Developer should use the PutTraceSegments API action.

CORRECT: "The PutTraceSegments API action" is the correct answer.

INCORRECT: "The PutTelemetryRecords API action" is incorrect as this is used by the AWS X-Ray daemon to upload telemetry.

INCORRECT: "The UpdateGroup API action" is incorrect as this updates a group resource.

INCORRECT: "The GetTraceSummaries API action" is incorrect as this retrieves IDs and annotations for traces available for a specified time frame using an optional filter.

Джерела: <https://docs.aws.amazon.com/xray/latest/devguide/xray-api-sendingdata.html>

</details>

---

<a id="t10-q41"></a>
### T10-Q41
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Management & Governance)*

> ⚠️ **Logs agent застарів → unified CloudWatch agent.** → [детальніше](../../07-outdated-and-wrong-answers.md#o15)

An application runs on Amazon EC2 and generates log files. A Developer needs to centralize the log files so they can be queried and retained. What is the EASIEST way for the Developer to centralize the log files?

- ✅ Install the Amazon CloudWatch Logs agent and collect the logs from the instances
- ▫️ Create a script that uses the AWS SDK to collect and send the log files to Amazon CloudWatch Logs
- ▫️ Create a script that copies the log files to Amazon S3 and use a cron job to run the script on a recurring schedule
- ▫️ Setup a CloudWatch Events rule to trigger an SNS topic when an application log file is generated

<details><summary>Пояснення (оригінал, EN)</summary>

You can use Amazon CloudWatch Logs to monitor, store, and access your log files from Amazon Elastic Compute Cloud (Amazon EC2) instances, AWS CloudTrail, Route 53, and other sources.

CloudWatch Logs enables you to centralize the logs from all of your systems, applications, and AWS services that you use, in a single, highly scalable service. You can then easily view them, search them for specific error codes or patterns, filter them based on specific fields, or archive them securely for future analysis.

To collect logs from Amazon EC2 and on-premises instances it is necessary to install an agent. There are two options: the unified CloudWatch Agent which collects logs and advanced metrics (such as memory usage), or the older CloudWatch Logs agent which only collects logs from Linux servers.

CORRECT: "Install the Amazon CloudWatch Logs agent and collect the logs from the instances" is the correct answer.

INCORRECT: "Create a script that copies the log files to Amazon S3 and use a cron job to run the script on a recurring schedule" is incorrect as the best place to move the log files to for querying and long term retention would be CloudWatch Logs. It is also easier to use the agent than to create and maintain a script.

INCORRECT: "Create a script that uses the AWS SDK to collect and send the log files to Amazon CloudWatch Logs" is incorrect as this is not the easiest way to achieve this outcome. It will be easier to use the CloudWatch Logs agent.

INCORRECT: "Setup a CloudWatch Events rule to trigger an SNS topic when an application log file is generated" is incorrect as CloudWatch Events does not collect log files, it monitors state changes in resources.

Джерела: <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html>

</details>

---

<a id="t10-q45"></a>
### T10-Q45
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Compute)*

A Developer created an AWS Lambda function for a serverless application. The Lambda function has been executing for several minutes and the Developer cannot find any log data in CloudWatch Logs.

What is the MOST likely explanation for this issue?

- ▫️ The Lambda function does not have any explicit log statements for the log data to send it to CloudWatch Logs
- ▫️ The Lambda function is missing CloudWatch Logs as a source trigger to send log data
- ▫️ The Lambda function is missing a target CloudWatch Logs group
- ✅ The execution role for the Lambda function is missing permissions to write log data to the CloudWatch Logs

<details><summary>Пояснення (оригінал, EN)</summary>

AWS Lambda automatically monitors Lambda functions on your behalf, reporting metrics through Amazon CloudWatch. To help you troubleshoot failures in a function, Lambda logs all requests handled by your function and also automatically stores logs generated by your code through Amazon CloudWatch Logs.

Lambda automatically integrates with CloudWatch Logs and pushes all logs from your code to a CloudWatch Logs group associated with a Lambda function, which is named /aws/lambda/&lt;function name&gt;.

An AWS Lambda function's execution role grants it permission to access AWS services and resources. You provide this role when you create a function, and Lambda assumes the role when your function is invoked. You can create an execution role for development that has permission to send logs to Amazon CloudWatch and upload trace data to AWS X-Ray.

For the lambda function to create log stream and publish logs to cloudwatch, the lambda execution role needs to have the following permissions:

The most likely cause of this issue is that the execution role assigned to the Lambda function does not have the permissions (shown above) to write to CloudWatch Logs.

CORRECT: "The execution role for the Lambda function is missing permissions to write log data to the CloudWatch Logs" is the correct answer.

INCORRECT: "The Lambda function does not have any explicit log statements for the log data to send it to CloudWatch Logs" is incorrect as this is not required, Lambda automatically logs data to CloudWatch logs and just needs the permissions to do so.

INCORRECT: "The Lambda function is missing a target CloudWatch Logs group" is incorrect as the CloudWatch Logs group will be created automatically if the function has sufficient permissions.

INCORRECT: "The Lambda function is missing CloudWatch Logs as a source trigger to send log data" is incorrect as CloudWatch Logs is a destination, not a source in this case. However, you do not need to configure CloudWatch Logs as a destination, it is automatic.

Джерела: <https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html>

</details>

---

<a id="t11-q39"></a>
### T11-Q39
*⏭ пропущено · домен: офіційна мітка*

An e-commerce company has developed an API that is hosted on Amazon ECS. Variable traffic spikes on the application are causing order processing to take too long. The application processes orders using Amazon SQS queues. The ApproximateNumberOfMessagesVisible metric spikes at very high values throughout the day which triggers the CloudWatch alarm. Other ECS metrics for the API containers are well within limits.

As a Developer Associate, which of the following will you recommend for improving performance while keeping costs low?

- ▫️ Use ECS step scaling policy
- ▫️ Use Docker swarm
- ✅ Use backlog per instance metric with target tracking scaling policy
- ▫️ Use ECS service scheduler

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use backlog per instance metric with target tracking scaling policy - If you use a target tracking scaling policy based on a custom Amazon SQS queue metric, dynamic scaling can adjust to the demand curve of your application more effectively.

The issue with using a CloudWatch Amazon SQS metric like ApproximateNumberOfMessagesVisible for target tracking is that the number of messages in the queue might not change proportionally to the size of the Auto Scaling group that processes messages from the queue. That's because the number of messages in your SQS queue does not solely define the number of instances needed. The number of instances in your Auto Scaling group can be driven by multiple factors, including how long it takes to process a message and the acceptable amount of latency (queue delay).

The solution is to use a backlog per instance metric with the target value being the acceptable backlog per instance to maintain. You can calculate these numbers as follows:

Backlog per instance: To calculate your backlog per instance, start with the ApproximateNumberOfMessages queue attribute to determine the length of the SQS queue (number of messages available for retrieval from the queue). Divide that number by the fleet's running capacity, which for an Auto Scaling group is the number of instances in the InService state, to get the backlog per instance.

Acceptable backlog per instance: To calculate your target value, first determine what your application can accept in terms of latency. Then, take the acceptable latency value and divide it by the average time that an EC2 instance takes to process a message.

To illustrate with an example, let's say that the current ApproximateNumberOfMessages is 1500 and the fleet's running capacity is 10. If the average processing time is 0.1 seconds for each message and the longest acceptable latency is 10 seconds, then the acceptable backlog per instance is 10 / 0.1, which equals 100. This means that 100 is the target value for your target tracking policy. If the backlog per instance is currently at 150 (1500 / 10), your fleet scales out, and it scales out by five instances to maintain proportion to the target value.

Incorrect options:

Use Docker swarm - A Docker swarm is a container orchestration tool, meaning that it allows the user to manage multiple containers deployed across multiple host machines. A swarm consists of multiple Docker hosts which run in swarm mode and act as managers (to manage membership and delegation) and workers (which run swarm services).

Use ECS service scheduler - Amazon ECS provides a service scheduler (for long-running tasks and applications), the ability to run tasks manually (for batch jobs or single run tasks), with Amazon ECS placing tasks on your cluster for you. You can specify task placement strategies and constraints that allow you to run tasks in the configuration you choose, such as spread out across Availability Zones. It is also possible to integrate with custom or third-party schedulers.

Use ECS step scaling policy - Although Amazon ECS Service Auto Scaling supports using Application Auto Scaling step scaling policies, AWS recommends using target tracking scaling policies instead. For example, if you want to scale your service when CPU utilization falls below or rises above a certain level, create a target tracking scaling policy based on the CPU utilization metric provided by Amazon ECS.

With step scaling policies, you create and manage the CloudWatch alarms that trigger the scaling process. If the target tracking alarms don't work for your use case, you can use step scaling. You can also use target tracking scaling with step scaling for an advanced scaling policy configuration. For example, you can configure a more aggressive response when utilization reaches a certain level.

Step Scaling scales your cluster on various lengths of steps based on different ranges of thresholds. Target tracking on the other hand intelligently picks the smart lengths needed for the given configuration.

Джерела: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-using-sqs-queue.html>

</details>

---

<a id="t11-q42"></a>
### T11-Q42
*⏭ пропущено · домен: офіційна мітка*

You have deployed a Java application to an EC2 instance where it uses the X-Ray SDK. When testing from your personal computer, the application sends data to X-Ray but when the application runs from within EC2, the application fails to send data to X-Ray.

Which of the following does NOT help with debugging the issue?

- ▫️ EC2 Instance Role
- ▫️ EC2 X-Ray Daemon
- ▫️ CloudTrail
- ✅ X-Ray sampling

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

X-Ray sampling

By customizing sampling rules, you can control the amount of data that you record, and modify sampling behavior on the fly without modifying or redeploying your code. Sampling rules tell the X-Ray SDK how many requests to record for a set of criteria. X-Ray SDK applies a sampling algorithm to determine which requests get traced however because our application is failing to send data to X-Ray it does not help in determining the cause of failure.

X-Ray Overview:  via - https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html

Incorrect options:

EC2 X-Ray Daemon - The AWS X-Ray daemon is a software application that listens for traffic on UDP port 2000, gathers raw segment data, and relays it to the AWS X-Ray API. The daemon logs could help with figuring out the problem.

EC2 Instance Role - The X-Ray daemon uses the AWS SDK to upload trace data to X-Ray, and it needs AWS credentials with permission to do that. On Amazon EC2, the daemon uses the instance's instance profile role automatically. Eliminates API permission issues (in case the role doesn't have IAM permissions to write data to the X-Ray service)

CloudTrail - With CloudTrail, you can log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. You can use AWS CloudTrail to answer questions such as - “Who made an API call to modify this resource?”. CloudTrail provides event history of your AWS account activity thereby enabling governance, compliance, operational auditing, and risk auditing of your AWS account. You can check CloudTrail to see if any API call is being denied on X-Ray.

Reference:

https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html

</details>

---

<a id="t11-q54"></a>
### T11-Q54
*⏭ пропущено · домен: офіційна мітка*

A multi-national company has multiple business units with each unit having its own AWS account. The development team at the company would like to debug and trace data across accounts and visualize it in a centralized account.

As a Developer Associate, which of the following solutions would you suggest for the given use-case?

- ▫️ EventBridge
- ✅ X-Ray
- ▫️ CloudTrail
- ▫️ VPC Flow Logs

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

X-Ray

AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.

You can use X-Ray to collect data across AWS Accounts. The X-Ray agent can assume a role to publish data into an account different from the one in which it is running. This enables you to publish data from various components of your application into a central account.

How X-Ray Works:  via - https://aws.amazon.com/xray/

Incorrect options:

VPC Flow Logs: VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data is used to analyze network traces and helps with network security. Flow log data can be published to Amazon CloudWatch Logs or Amazon S3. You cannot use VPC Flow Logs to debug and trace data across accounts.

EventBridge: Amazon EventBridge delivers a near real-time stream of system events that describe changes in Amazon Web Services (AWS) resources. These help to trigger notifications based on changes happening in AWS services. You cannot use EventBridge to debug and trace data across accounts.

CloudTrail: With CloudTrail, you can log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. You can use AWS CloudTrail to answer questions such as - “Who made an API call to modify this resource?”. CloudTrail provides event history of your AWS account activity thereby enabling governance, compliance, operational auditing, and risk auditing of your AWS account. You cannot use CloudTrail to debug and trace data across accounts.

Reference:

https://aws.amazon.com/xray/

</details>

---

<a id="t12-q32"></a>
### T12-Q32
*⏭ пропущено · домен: офіційна мітка*

A developer is configuring Amazon ECS container instances to send log information to CloudWatch Logs. For the container instances to be able to send log data to CloudWatch Logs, an IAM policy needs to be created that will allow the container instances to use the CloudWatch Logs APIs.

Which policy is the right fit for the given requirement?

- ▫️

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents,
                  "ecs:DescribeServices"
              ],
              "Resource": [
                  "arn:aws:logs:<ARN of the Log Group>"
              ]
          }
      ]
  }
  ```
- ✅

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents",
                  "logs:DescribeLogStreams"
              ],
              "Resource": [
                  "arn:aws:logs:*:*:*"
              ]
          }
      ]
  }
  ```
- ▫️

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents"
              ],
              "Resource": [
                  "arn:aws:logs:*:*:*"
              ]
          }
      ]
  }
  ```
- ▫️

  ```json
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents",
                  "logs:DescribeLogGroups"
              ],
              "Resource": [
                  "arn:aws:logs:*:*:*"
              ]
          }
      ]
  }
  ```

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
Before your container instances can send log data to CloudWatch Logs, you must create an IAM policy to allow your container instances to use the CloudWatch Logs APIs, and then you must attach that policy to ecsInstanceRole.

This policy has one statement that grants permissions to create log groups and log streams, to upload log events to log streams, and to list details about log streams.

The wildcard character () at the end of the Resource value means that the statement allows permission for the logs:CreateLogGroup, logs:CreateLogStream, logs:PutLogEvents, and logs:DescribeLogStreams actions on any log group. To limit this permission to a specific log group, replace the wildcard character () in the resource ARN with the specific log group ARN

Incorrect options:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
Permission to list details of the log stream needs to be attached to this policy.
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents,
                "ecs:DescribeServices"
            ],
            "Resource": [
                "arn:aws:logs:&lt;ARN of the Log Group>"
            ]
        }
    ]
}```

- ecs:DescribeServices permission is not needed, but logs:DescribeLogStreams permissions are needed for the policy to perform as expected.

{ "Version": "2012-10-17", "Statement": [ { "Effect": "Allow", "Action": [ "logs:CreateLogGroup", "logs:CreateLogStream", "logs:PutLogEvents", "logs:DescribeLogGroups" ], "Resource": [ "arn:aws:logs:::*" ] } ] } ```

logs:DescribeLogGroups is an erroneous permission here.

Джерела: <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_cloudwatch_logs.html> · <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-identity-based-access-control-cwl.html>

</details>

---

<a id="t12-q64"></a>
### T12-Q64
*⏭ пропущено · домен: офіційна мітка*

A multi-national company runs its technology operations on AWS Cloud. As part of their storage solution, they use a large number of EBS volumes, with AWS Config and CloudTrail activated. A manager has tried to find the user name that created an EBS volume by searching CloudTrail events logs but wasn't successful.

As a Developer Associate, which of the following would you recommend as the correct solution?

- ✅ AWS CloudTrail event logs for 'CreateVolume' aren't available for EBS volumes created during an Amazon EC2 launch
- ▫️ Amazon EBS CloudWatch metrics are disabled
- ▫️ AWS CloudTrail event logs for 'ManageVolume' aren't available for EBS volumes created during an Amazon EC2 launch
- ▫️ EBS volume status checks are disabled

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

AWS CloudTrail event logs for 'CreateVolume' aren't available for EBS volumes created during an Amazon EC2 launch - AWS CloudTrail event logs for 'CreateVolume' aren't available for EBS volumes created during an Amazon Elastic Compute Cloud (Amazon EC2) launch.

Incorrect options:

AWS CloudTrail event logs for 'ManageVolume' aren't available for EBS volumes created during an Amazon EC2 launch - Event 'ManageVolume' is a made-up option and has been added as a distractor.

Amazon EBS CloudWatch metrics are disabled - Amazon Elastic Block Store (Amazon EBS) sends data points to CloudWatch for several metrics. Data is only reported to CloudWatch when the volume is attached to an instance. CloudWatch metrics are useful in tracking the status or life cycle changes of an EBS volume, they are not useful in knowing about the metadata of EBS volumes.

EBS volume status checks are disabled - Volume status checks enable you to better understand, track and manage potential inconsistencies in the data on an Amazon EBS volume. They are designed to provide you with the information that you need to determine whether your Amazon EBS volumes are impaired, and to help you control how a potentially inconsistent volume is handled. Our current use case requires us to pull data about EBS volume metadata, which is not possible with this feature.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/find-ebs-user-config-cloudtrail/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using_cloudwatch_ebs.html> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-volume-status.html>

</details>

---
