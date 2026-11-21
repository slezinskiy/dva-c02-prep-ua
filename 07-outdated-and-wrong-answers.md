# Застарілі та сумнівні відповіді в банку питань

> Пробні тести писали у 2022–2024 роках. AWS відтоді змінила кілька лімітів і закрила частину сервісів, а деякі ключі в банку були сумнівні з самого початку.
> **Як діяти на екзамені:** реальні питання AWS оновлює, тож орієнтуйся на **актуальні** факти. Якщо ж питання очевидно старе (серед варіантів немає актуального), обирай найкращий з наявних.
> Перевірено станом на **21.11.2026**.

## Частина 1. Застарілі факти (o)

<a id="o01"></a>
### o01. SQS: максимальний розмір повідомлення — 1 MiB (не 256 KB)
З 08.2025 SQS (standard і FIFO) приймає до **1 MiB**; Lambda ESM для SQS теж підтримує 1 MiB. Async-виклик Lambda та EventBridge — теж 1 MB, SNS — 1 MiB (з 09.2026).
Зачіпає: [T07-Q40](questions/1-development/messaging.md#t07-q40) (ключ «Extended Client через ліміт 256 KB»), [T03-Q53](questions/1-development/messaging.md#t03-q53) (вже оновлено: 1024 KiB). Extended Client лишається відповіддю для **> 1 MiB** [T04-Q32](questions/1-development/messaging.md#t04-q32).

<a id="o02"></a>
### o02. CodeCommit — знову GA
07.2024 CodeCommit закрили для нових клієнтів, **24.11.2025 повернули в повну GA**. Питання про CodeCommit (Git credentials з IAM, SSH-ключі, шифрування) актуальні. У DVA-C03 CodeCommit немає у списку in-scope Developer Tools, але `source control` лишився.

<a id="o03"></a>
### o03. Cloud9 і CodeStar
- **AWS CodeStar** припинено (07.2024) — [T01-Q47](questions/3-deployment/cicd.md#t01-q47) застаріле (зараз: CodePipeline + CodeBuild).
- **AWS Cloud9** закритий для нових клієнтів (07.2024) — [T04-Q50](questions/3-deployment/cicd.md#t04-q50) [T06-Q07](questions/3-deployment/cicd.md#t06-q07). Альтернатива: CloudShell, IDE з AWS Toolkit.

<a id="o04"></a>
### o04. S3 Select (і S3 Glacier Select) закрито для нових клієнтів (2024)
[T01-Q25](questions/1-development/s3.md#t01-q25) — на старому екзамені це правильна відповідь; у реальному житті використовуй **Athena** або фільтрацію на клієнті.

<a id="o05"></a>
### o05. X-Ray SDK і daemon → OpenTelemetry
**25.02.2026** X-Ray SDK і daemon перейшли в **maintenance mode**, **25.02.2027** закінчується їхня підтримка. Рекомендовано **AWS Distro for OpenTelemetry (ADOT)** / OpenTelemetry SDK + **CloudWatch agent / OTel collector**. Сам сервіс X-Ray (service map, traces, annotations) працює й далі.
Зачіпає: [T01-Q03](questions/4-troubleshooting/observability.md#t01-q03) (`AWS_XRAY_DAEMON_ADDRESS`), [T01-Q11](questions/4-troubleshooting/containers.md#t01-q11), [T06-Q65](questions/3-deployment/containers.md#t06-q65), [T07-Q17](questions/4-troubleshooting/observability.md#t07-q17) — концептуально правильні, але на DVA-C03 можуть з'явитися варіанти з ADOT.

<a id="o07"></a>
### o07. Aurora Replicas не «синхронні»
Пояснення до [T02-Q62](questions/4-troubleshooting/cache.md#t02-q62) каже «synchronously updates». Насправді Aurora Replicas читають **спільне сховище**, а лаг становить зазвичай **< 100 мс**; реплікацію вважають асинхронною. Сама відповідь (Aurora Replicas + binlog replication) правильна.

<a id="o08"></a>
### o08. Memcached і шифрування
Пояснення до [T02-Q05](questions/1-development/cache.md#t02-q05) «Memcached не підтримує шифрування» застаріле: ElastiCache Memcached (1.6.12+) підтримує шифрування in-transit, serverless — і at-rest. Але **HA/реплікації в Memcached немає**, тому відповідь «Redis cluster mode» правильна.

<a id="o09"></a>
### o09. DynamoDB transactions — до 100 дій
Пояснення до [T02-Q59](questions/1-development/dynamodb.md#t02-q59) каже «up to 25». З 09.2022 `TransactWriteItems` / `TransactGetItems` — **до 100 дій**, 4 MB.

<a id="o10"></a>
### o10. Elastic Beanstalk source bundle — 500 MB
Документація: **≤ 500 MB** (банк [T02-Q50](questions/3-deployment/beanstalk.md#t02-q50) пише «512 MB»). На екзамені обирай варіант, де найближча цифра в парі з «no parent folder».

<a id="o11"></a>
### o11. Launch configurations → launch templates
[T05-Q26](questions/1-development/network.md#t05-q26) [T10-Q08](questions/1-development/network.md#t10-q08) згадують launch configuration. AWS більше не рекомендує їх (нові акаунти не можуть їх створювати), тож використовуй **launch templates** (user data там теж є) [T12-Q35](questions/1-development/network.md#t12-q35).

<a id="o12"></a>
### o12. CloudFront OAI → OAC
[T04-Q29](questions/2-security/s3.md#t04-q29) — Origin Access **Identity** застарів; зараз **Origin Access Control (OAC)** (підтримує SSE-KMS, усі регіони, PUT/DELETE).

<a id="o13"></a>
### o13. `aws ecr get-login` (CLI v1)
[T12-Q24](questions/3-deployment/containers.md#t12-q24) — у CLI v2 цієї команди немає. Актуально: `aws ecr get-login-password | docker login --username AWS --password-stdin <registry>` [T04-Q13](questions/3-deployment/containers.md#t04-q13) [T05-Q24](questions/3-deployment/containers.md#t05-q24).

<a id="o14"></a>
### o14. Multicontainer Docker у Elastic Beanstalk
[T01-Q65](questions/3-deployment/containers.md#t01-q65) — платформа «Multicontainer Docker (Amazon Linux AMI)» знята; зараз це **ECS running on Amazon Linux 2/2023** або Docker-платформа з `docker-compose`.

<a id="o15"></a>
### o15. CloudWatch Logs agent → unified CloudWatch agent
[T10-Q41](questions/4-troubleshooting/observability.md#t10-q41) — старий Logs agent застарів; використовуй **CloudWatch agent** (логи + метрики).

<a id="o16"></a>
### o16. Перейменування сервісів (вплинуть на текст варіантів)
| Старе | Нове |
|---|---|
| Kinesis Data Firehose | **Amazon Data Firehose** [T04-Q44](questions/1-development/kinesis.md#t04-q44) [T04-Q51](questions/1-development/s3.md#t04-q51) |
| Amazon Elasticsearch Service | **Amazon OpenSearch Service** [T04-Q44](questions/1-development/kinesis.md#t04-q44) |
| CMK (customer master key) | **KMS key** (customer managed key) |
| CloudWatch Events | **Amazon EventBridge** |
| ElastiCache for Redis | **ElastiCache for Redis OSS / Valkey** |
| DynamoDB Encryption Client | **AWS Database Encryption SDK** [T06-Q05](questions/2-security/kms.md#t06-q05) |
| Cognito Sync | **AppSync** [T08-Q10](questions/2-security/cognito.md#t08-q10) |
| Cognito «advanced security features» | Cognito **threat protection** (adaptive authentication) [T06-Q60](questions/2-security/cognito.md#t06-q60) |

<a id="o17"></a>
### o17. Kinesis: записи до 10 MiB
З 10.2025 максимальний розмір запису налаштовується до **10 MiB** (за замовчуванням 1 MiB); Lambda читає з Kinesis payload до 6 MiB.

<a id="o18"></a>
### o18. S3: шифрування за замовчуванням та ACL
- З 01.2023 всі нові об'єкти шифруються **SSE-S3** автоматично. Питання «як гарантувати шифрування» [T05-Q34](questions/2-security/s3.md#t05-q34) тепер мають сенс переважно для **SSE-KMS** [T11-Q29](questions/2-security/s3.md#t11-q29).
- З 04.2023 нові бакети мають **Object Ownership = Bucket owner enforced** (ACL вимкнені) [T07-Q47](questions/1-development/s3.md#t07-q47).

<a id="o19"></a>
### o19. Lambda runtimes
`nodejs12.x` [T05-Q23](questions/3-deployment/iac.md#t05-q23) та інші старі runtimes знято з підтримки; на суть питань це не впливає.

<a id="o20"></a>
### o20. Free Tier
[T11-Q07](questions/1-development/network.md#t11-q07) (t2.micro 35 с → $0) спирається на стару модель 12-місячного Free Tier. 07.2025 AWS змінила Free Tier для нових акаунтів (кредитна модель). На DVA таке питання малоймовірне.

<a id="o21"></a>
### o21. CloudFront key pairs
[T11-Q27](questions/2-security/iam.md#t11-q27) — key pairs CloudFront справді може створювати **лише root**, але зараз рекомендовано **trusted key groups** (керуються через IAM, root не потрібен) [T09-Q40](questions/2-security/edge.md#t09-q40).

<a id="o22"></a>
### o22. ECS Docker volumes «лише для EC2»
Пояснення до [T02-Q13](questions/4-troubleshooting/containers.md#t02-q13) каже, що Docker volumes підтримує лише EC2 launch type. Це так, але Fargate підтримує **bind mounts** (ephemeral storage) та **EFS** — для обміну даними між контейнерами в одному task definition. Відповідь («один task definition + спільний volume») правильна.

---

## Частина 2. Сумнівні ключі (d) — запам'ятай правильну логіку

<a id="d01"></a>
### d01. [T02-Q32](questions/2-security/kms.md#t02-q32) TLS для IoT-пристроїв → ключ банку «ACM»
X.509-сертифікати для пристроїв видає й реєструє **AWS IoT Core** (`CreateKeysAndCertificate`, fleet provisioning), він же забезпечує mutual TLS. ACM видає сертифікати для ELB/CloudFront/API GW. Пояснення банку «IoT Core is not a certificate authority» хибне. На DVA-C03 IoT не в scope.

<a id="d02"></a>
### d02. [T02-Q47](questions/4-troubleshooting/messaging.md#t02-q47) Повтор невдалих async-викликів Lambda → ключ «redrive policy SQS + DLQ як event source»
Для async-виклику найпростіше — **on-failure destination = SQS** (або Lambda DLQ) і окремий обробник цієї черги. «Redrive policy» стосується черги-джерела SQS, а тут черги-джерела немає. Якщо такий варіант на екзамені єдиний «із SQS DLQ» — обирай його.

<a id="d03"></a>
### d03. [T02-Q65](questions/1-development/cache.md#t02-q65) Еластичність запису замовлень → ключ «Aurora Auto Scaling read replicas за кількістю з'єднань»
Замовлення — це **записи**, а read replicas не масштабують записи. Інженерно краще: **SQS-буфер + reserved concurrency Lambda = максимум з'єднань БД** (або RDS Proxy). Ключ банку — «Aurora Auto Scaling + AverageConnections». Запам'ятай обидва; якщо в умові явно «read-heavy» → replicas.

<a id="d04"></a>
### d04. [T04-Q65](questions/2-security/iam.md#t04-q65) Lambda не може надіслати в SQS on-failure destination → ключ «customer managed policy з усіма read/write правами SQS»
За least privilege досить `sqs:SendMessage` на ARN цієї черги. Серед варіантів банку це найкращий (інші — admin-група, managed-політика без SendMessage, політика черги на весь акаунт).

<a id="d05"></a>
### d05. [T07-Q06](questions/2-security/apigw.md#t07-q06) «Заборонити неавторизованим доменам доступ до API» → ключ «CORS»
CORS обмежує лише **браузерні** запити; curl/сервери його ігнорують. Реальний захист: authorizers, API keys + usage plans (throttling), WAF, resource policy. На старому екзамені очікують «CORS».

<a id="d06"></a>
### d06. [T06-Q51](questions/3-deployment/iac.md#t06-q51) CDK: «як відкотитись» → ключ «змінюй лише на етапі deployment, а не synthesis»
Формулювання нелогічне. По суті: CDK генерує CloudFormation, тож невдалий деплой **автоматично відкочує CloudFormation**, а повернення до попередньої версії = `git revert` + `cdk deploy`.

<a id="d07"></a>
### d07. [T07-Q31](questions/3-deployment/apigw.md#t07-q31) Промоут test → prod в API Gateway → ключ «змінити stage variable з test на prod»
Зазвичай роблять так: задеплоїти **той самий deployment** у prod-stage (`create-deployment`/`update-stage --deployment-id`) або **promote canary**. Ключ банку спирається на stage variables, що вказують на бекенд (alias Lambda).

<a id="d08"></a>
### d08. [T10-Q58](questions/1-development/messaging.md#t10-q58) Найшвидше оновлення лідерборду → ключ «long polling кожні 15 с»
Формулювання дивне, але суть правильна: **long polling** повертає повідомлення **одразу**, як воно надходить, без порожніх відповідей; short polling раз на 10 с дає затримку до 10 с.

<a id="d09"></a>
### d09. [T05-Q29](questions/1-development/messaging.md#t05-q29) Lambda обробляє по одному повідомленню SQS → ключ «ReceiveMessage MaxNumberOfMessages > 1»
Якщо Lambda тригериться через **event source mapping**, реальний параметр — **`BatchSize`** ESM (за замовчуванням 10). `MaxNumberOfMessages` — коли код сам викликає `ReceiveMessage`. Обирай за варіантами.

### Також зверни увагу
- [T11-Q55](questions/2-security/kms.md#t11-q55) «KMS отримує дані від клієнта й шифрує» — правда лише для ≤ 4 KB; для більшого — envelope encryption [T04-Q53](questions/2-security/kms.md#t04-q53).
- [T10-Q31](questions/2-security/iam.md#t10-q31) Lambda@Edge + STS замість credentials у фронтенді — нетипове рішення, але серед варіантів єдине без хардкоду ключів.
- [T08-Q50](questions/4-troubleshooting/cache.md#t08-q50) Memcached для сесій — прийнятно, бо інші варіанти гірші (instance store, MySQL на EC2).

---

## Джерела перевірки
- SQS 1 MiB: <https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-sqs-max-payload-size-1mib/>
- Payload 1 MB для async Lambda / EventBridge: <https://aws.amazon.com/blogs/compute/more-room-to-build-serverless-services-now-support-payloads-up-to-1-mb>
- CodeCommit знову GA: <https://aws.amazon.com/blogs/devops/aws-codecommit-returns-to-general-availability>
- X-Ray SDK/daemon end-of-support: <https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon-eos.html>
- Kinesis 10 MiB: <https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-kinesis-data-streams-10x-larger-record-sizes>
- DynamoDB transactions (100 дій): <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html>
- EB source bundle (500 MB): <https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/applications-sourcebundle.html>
