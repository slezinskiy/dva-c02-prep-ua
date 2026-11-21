# Домен 1 — Розробка з використанням сервісів AWS (32%)

> Найбільший домен. Що він перевіряє: чи вмієш ти обрати сервіс під патерн доступу, писати код для Lambda, працювати з DynamoDB, S3, чергами, стрімами та кешем.
> Посилання `Txx-Qyy` ведуть на реальні питання з банку (тест xx, питання yy).

**Зміст:** [Архітектурні патерни](#patterns) · [Lambda](#lambda) · [API Gateway / AppSync](#apigw) · [DynamoDB](#dynamodb) · [S3](#s3) · [SQS / SNS / EventBridge](#messaging) · [Kinesis / Firehose](#kinesis) · [Step Functions](#stepfn) · [ElastiCache / RDS / Aurora](#cache) · [EC2 / ASG / ELB / VPC](#network) · [CloudFront / Route 53](#edge)

---

<a id="patterns"></a>
## 1. Архітектурні патерни (Task 1.1)

| Ситуація у питанні | Відповідь |
|---|---|
| Компоненти «не встигають», губляться повідомлення між рівнями | **SQS між рівнями** (decoupling) [T08-Q43](questions/1-development/network.md#t08-q43) |
| Одне повідомлення → багатьом споживачам | **SNS → кілька SQS (fan-out)** [T05-Q62](questions/1-development/messaging.md#t05-q62) [T03-Q27](questions/1-development/messaging.md#t03-q27) |
| Різним партнерам/споживачам — лише «свої» повідомлення | SNS + **subscription filter policy** [T09-Q23](questions/3-deployment/messaging.md#t09-q23) |
| Реакція на зміну стану AWS-ресурсу, розклад (cron) | **EventBridge rule** (раніше «CloudWatch Events») [T01-Q04](questions/1-development/lambda.md#t01-q04) [T10-Q33](questions/1-development/lambda.md#t10-q33) |
| Оркестрація кроків, паралельні гілки, людське погодження | **Step Functions** [T07-Q35](questions/2-security/stepfn.md#t07-q35) [T09-Q27](questions/1-development/stepfn.md#t09-q27) |
| Потік даних у реальному часі, порядок, кілька споживачів, replay | **Kinesis Data Streams** [T05-Q64](questions/1-development/kinesis.md#t05-q64) [T07-Q36](questions/1-development/kinesis.md#t07-q36) |
| Доставити потік у S3/OpenSearch/Redshift без коду | **Amazon Data Firehose** [T04-Q44](questions/1-development/kinesis.md#t04-q44) [T04-Q51](questions/1-development/s3.md#t04-q51) |
| Обробка, що триває > 15 хв | **Не Lambda** → EC2/ASG + SQS, ECS/Fargate [T01-Q15](questions/4-troubleshooting/messaging.md#t01-q15) |
| Ідемпотентність запитів | Зберігати ID запиту (DynamoDB, conditional write) і перевіряти перед обробкою [T11-Q20](questions/1-development/lambda.md#t11-q20) |
| Повтори при throttling | **Exponential backoff + jitter** (SDK робить це автоматично) [T05-Q44](questions/4-troubleshooting/dynamodb.md#t05-q44) [T02-Q64](questions/4-troubleshooting/dynamodb.md#t02-q64) |
| Stateless-застосунок + еластичність | Сесії назовні (ElastiCache/DynamoDB) + ELB + ASG [T08-Q42](questions/1-development/network.md#t08-q42) [T10-Q64](questions/1-development/network.md#t10-q64) |

**Синхронне чи асинхронне виконання:** API Gateway → Lambda — синхронно; S3/SNS/EventBridge → Lambda — асинхронно; SQS, Kinesis, DynamoDB Streams → Lambda — через **event source mapping**: Lambda сама опитує джерело (poll), а функцію викликає синхронно.

---

<a id="lambda"></a>
## 2. AWS Lambda (Task 1.2)

### Ліміти (знати напам'ять)
| Параметр | Значення |
|---|---|
| Timeout | до **900 с (15 хв)** |
| Memory | 128 MB – **10 240 MB**; CPU росте **пропорційно пам'яті** (~1769 MB = 1 vCPU) |
| /tmp (ephemeral storage) | 512 MB за замовчуванням, до **10 240 MB** |
| Змінні середовища | сумарно **4 KB**, кількість не обмежена [T07-Q34](questions/1-development/lambda.md#t07-q34) |
| Пакет .zip | **50 MB** (zip, пряме завантаження), **250 MB** розпакований (разом із layers); більше — через S3 або container image |
| Container image | до **10 GB**, має реалізувати **Lambda Runtime API** [T09-Q24](questions/3-deployment/containers.md#t09-q24) |
| Layers | до **5** на функцію |
| Payload | синхронний **6 MB**; асинхронний — **1 MB** (з 2025 р., раніше 256 KB) |
| Concurrency | **1000** на регіон за замовчуванням (soft limit) |

### Concurrency
- **Формула:** `concurrency = запитів за секунду × тривалість (с)`. 20 rps × 20 с = **400** [T02-Q36](questions/1-development/lambda.md#t02-q36); 10 × 3 = **30** [T04-Q19](questions/1-development/lambda.md#t04-q19); 50 × 100 = 5000 > 1000 → **просити збільшення ліміту** [T05-Q11](questions/1-development/lambda.md#t05-q11) [T10-Q54](questions/1-development/lambda.md#t10-q54).
- **Reserved concurrency** — гарантує функції частину пулу і водночас обмежує її максимум (ним «приборкують» функцію, яка забирає всю concurrency [T12-Q53](questions/1-development/lambda.md#t12-q53)). Платити окремо не треба.
- **Provisioned concurrency** — заздалегідь «прогріті» середовища, без cold start [T01-Q49](questions/1-development/lambda.md#t01-q49). Можна масштабувати за розкладом через **Application Auto Scaling** [T09-Q18](questions/4-troubleshooting/lambda.md#t09-q18). Платна.
- Throttling → для sync-виклику помилка **429 TooManyRequestsException**; async-події Lambda повторює сама.

### Продуктивність
- Ініціалізуй SDK-клієнти та з'єднання з БД **поза handler** — так виконавче середовище використовується повторно [T03-Q01](questions/4-troubleshooting/lambda.md#t03-q01) [T05-Q36](questions/1-development/lambda.md#t05-q36) [T07-Q33](questions/4-troubleshooting/s3.md#t07-q33).
- Функція впирається в CPU → **збільш memory** (CPU додається разом із пам'яттю) [T07-Q32](questions/1-development/lambda.md#t07-q32) [T09-Q20](questions/1-development/lambda.md#t09-q20) [T11-Q48](questions/4-troubleshooting/lambda.md#t11-q48) [T12-Q17](questions/1-development/lambda.md#t12-q17).
- Великий файл, потрібний при кожному виклику → кешуй у **/tmp** [T08-Q56](questions/4-troubleshooting/lambda.md#t08-q56); тимчасові файли теж пиши в /tmp [T01-Q34](questions/1-development/lambda.md#t01-q34).
- Повідомлення `Memory Size: 10240 MB Max Memory Used: 10240 MB` = функції забракло RAM [T03-Q61](questions/1-development/lambda.md#t03-q61).

### Залежності та пакування
- Залежності поза runtime → **zip разом із кодом** [T01-Q24](questions/1-development/lambda.md#t01-q24) [T02-Q52](questions/1-development/lambda.md#t02-q52) [T04-Q17](questions/1-development/lambda.md#t04-q17); спільні бібліотеки для кількох функцій → **Lambda Layer** [T04-Q46](questions/1-development/lambda.md#t04-q46) [T08-Q01](questions/1-development/lambda.md#t08-q01).
- AWS SDK у runtime вже є (Python boto3, Node.js SDK v3), тож якщо треба мінімум змін, можна користуватися ним [T02-Q04](questions/1-development/lambda.md#t02-q04). Best practice — все ж бандлити свою версію.
- CloudFormation: код **inline** (`ZipFile`, без сторонніх залежностей) або **zip в S3** (`Code: S3Bucket/S3Key`) [T01-Q64](questions/1-development/lambda.md#t01-q64) [T08-Q51](questions/3-deployment/iac.md#t08-q51).

### Виклики, помилки, retries
| Тип виклику | Retry | Що відбувається після вичерпання retry |
|---|---|---|
| Синхронний (`RequestResponse`: API GW, ALB, SDK) | ні, повторює клієнт | помилка повертається клієнту |
| Асинхронний (`--invocation-type Event`: S3, SNS, EventBridge) | **2 повтори** (разом 3 спроби), подія в черзі до 6 год | **DLQ** (SQS/SNS) або **destination on-failure** (SQS, SNS, Lambda, EventBridge, S3) [T07-Q30](questions/1-development/lambda.md#t07-q30) |
| Event source mapping (SQS, Kinesis, DynamoDB Streams) | доки запис не протухне (Kinesis/DDB), для SQS — visibility timeout + redrive | Kinesis/DDB: **on-failure destination**, bisect batch; SQS: **DLQ на самій черзі** |

- `aws lambda invoke --invocation-type Event` → **StatusCode 202** = асинхронно прийнято [T08-Q20](questions/1-development/lambda.md#t08-q20); **200** = синхронно успішно [T10-Q50](questions/1-development/lambda.md#t10-q50).
- Дублікати в логах з тим самим request ID → Lambda **повторила** невдалий async-виклик [T02-Q48](questions/4-troubleshooting/lambda.md#t02-q48); дублікати з Kinesis → помилку не оброблено, батч оброблено повторно [T04-Q34](questions/4-troubleshooting/lambda.md#t04-q34). **Код має бути ідемпотентним.**
- **Destinations** (on-success / on-failure) — лише для async та stream-ESM. Передати результат у pub/sub → on-success destination = SNS [T10-Q51](questions/1-development/lambda.md#t10-q51). Сповістити про невдалий батч Kinesis → on-failure destination = SNS [T10-Q25](questions/4-troubleshooting/lambda.md#t10-q25).
- Унікальний ID виклику → `context.awsRequestId` (Node.js) / `context.aws_request_id` (Python) [T05-Q09](questions/1-development/lambda.md#t05-q09).

### Lambda і мережа
- Доступ до RDS у приватній підмережі → **Lambda у тій самій VPC** (subnets + security group) [T06-Q15](questions/1-development/network.md#t06-q15) [T07-Q52](questions/1-development/network.md#t07-q52).
- Lambda у VPC без інтернету: доступ до DynamoDB/S3 через **gateway VPC endpoint** [T10-Q23](questions/1-development/network.md#t10-q23), до SQS та інших сервісів — через **interface endpoint (PrivateLink)** [T04-Q59](questions/1-development/network.md#t04-q59).
- Багато короткочасних з'єднань до RDS → **RDS Proxy** (пул з'єднань) [T02-Q08](questions/4-troubleshooting/lambda.md#t02-q08).

### Версії та aliases
- **Версія** незмінна (`:1`, `:2`); `$LATEST` змінюється. **Alias** — це вказівник із **власним стабільним ARN** [T10-Q13](questions/3-deployment/lambda.md#t10-q13) [T06-Q03](questions/3-deployment/lambda.md#t06-q03).
- Alias із **вагами** (weighted alias, routing config) = поступове перемикання трафіку, наприклад 10%, і швидкий rollback [T07-Q51](questions/1-development/lambda.md#t07-q51) [T08-Q13](questions/3-deployment/lambda.md#t08-q13) [T04-Q52](questions/3-deployment/lambda.md#t04-q52) [T11-Q63](questions/2-security/lambda.md#t11-q63).
- API Gateway stage variable → alias (`function:myfn:${stageVariables.alias}`) [T12-Q48](questions/1-development/lambda.md#t12-q48). Не забудь `lambda add-permission` для кожного alias [T01-Q19](questions/4-troubleshooting/apigw.md#t01-q19).

### Інше
- Секрети в env vars → KMS + **encryption helpers** (шифрування на боці клієнта), тоді в консолі видно лише шифротекст [T08-Q23](questions/2-security/lambda.md#t08-q23).
- Шифрування даних у /tmp → згенерувати data key через KMS і шифрувати перед записом [T06-Q40](questions/2-security/kms.md#t06-q40).
- Немає логів у CloudWatch → execution role не має `logs:CreateLogGroup/CreateLogStream/PutLogEvents` (`AWSLambdaBasicExecutionRole`) [T05-Q15](questions/4-troubleshooting/observability.md#t05-q15) [T10-Q45](questions/4-troubleshooting/observability.md#t10-q45).
- Lambda не має прав на DynamoDB/S3 → виправ **execution role** [T07-Q29](questions/4-troubleshooting/dynamodb.md#t07-q29).
- `lambda add-permission --principal s3.amazonaws.com --source-arn ...` — це **resource-based policy** функції, яка дозволяє S3 її викликати [T04-Q20](questions/2-security/s3.md#t04-q20).
- **Lambda@Edge** деплоїться **лише з us-east-1** [T05-Q42](questions/3-deployment/iac.md#t05-q42).

---

<a id="apigw"></a>
## 3. Amazon API Gateway / AppSync

### Типи API
| Тип | Коли |
|---|---|
| **REST API** | Найбільше можливостей: API keys, usage plans, кешування, mapping templates, request validation, WAF, canary |
| **HTTP API** | Дешевше й швидше, JWT/Lambda authorizers, менше функцій (без кешу, без usage plans) |
| **WebSocket API** | Двосторонній real-time (чати, ігри, push-сповіщення без polling) [T07-Q23](questions/4-troubleshooting/apigw.md#t07-q23); маршрути `$connect`, `$disconnect`, `$default` + route selection expression `$request.body.action` [T06-Q13](questions/1-development/apigw.md#t06-q13) [T10-Q12](questions/1-development/apigw.md#t10-q12) |
| **AppSync (GraphQL)** | Один endpoint агрегує кілька джерел (Lambda + DynamoDB), менше викликів [T02-Q37](questions/1-development/apigw.md#t02-q37); real-time та офлайн-синхронізація [T09-Q62](questions/2-security/apigw.md#t09-q62) (у DVA-C03 AppSync виведено зі scope) |

### Інтеграції
- **Lambda proxy** — запит передається як є, найпростіше й найдешевше [T08-Q46](questions/1-development/lambda.md#t08-q46). **Non-proxy (custom)** — потрібні **mapping templates (VTL)**: перетворення query string → аргументи [T06-Q11](questions/1-development/lambda.md#t06-q11), форматування відповіді [T11-Q37](questions/4-troubleshooting/apigw.md#t11-q37), **JSON → XML для SOAP-бекенду** [T02-Q16](questions/1-development/apigw.md#t02-q16) [T05-Q37](questions/1-development/apigw.md#t05-q37).
- **MOCK** — повертає відповідь без бекенду (паралельна розробка, симуляція компонентів) [T07-Q49](questions/3-deployment/other.md#t07-q49).
- HTTP / AWS service / VPC link (приватні NLB/ALB).

### Stages, deployments, версіонування
- Після змін API треба **заново задеплоїти в stage**, інакше змін не видно [T09-Q14](questions/1-development/apigw.md#t09-q14).
- Різні версії/середовища → **окремі stages** + **stage variables** [T05-Q18](questions/3-deployment/apigw.md#t05-q18) [T08-Q41](questions/3-deployment/apigw.md#t08-q41) [T12-Q22](questions/3-deployment/apigw.md#t12-q22); бета-доступ без впливу на клієнтів → **новий stage** [T11-Q01](questions/1-development/apigw.md#t11-q01) [T10-Q52](questions/3-deployment/lambda.md#t10-q52).
- **Canary release** на stage (`canarySettings`, % трафіку) [T01-Q21](questions/3-deployment/lambda.md#t01-q21) [T05-Q51](questions/3-deployment/apigw.md#t05-q51).
- Перевірити request validation перед продом → задеплоїти в новий stage і там протестувати [T05-Q61](questions/3-deployment/apigw.md#t05-q61).

### Кешування (лише REST)
- TTL за замовчуванням **300 с**, максимум **3600 с**, TTL=0 вимикає кеш. Кешування — на рівні stage, можна перевизначити для окремого методу.
- Клієнт інвалідує кеш заголовком **`Cache-Control: max-age=0`**; щоб це працювало, потрібен дозвіл `execute-api:InvalidateCache` [T02-Q24](questions/1-development/apigw.md#t02-q24) [T03-Q18](questions/1-development/apigw.md#t03-q18) [T08-Q48](questions/1-development/apigw.md#t08-q48).
- Зменшити кількість викликів бекенду і latency → **увімкнути API caching** [T07-Q46](questions/1-development/apigw.md#t07-q46) [T08-Q34](questions/1-development/apigw.md#t08-q34) [T11-Q43](questions/4-troubleshooting/apigw.md#t11-q43).

### Безпека та доступ
- Авторизатори: **IAM (SigV4)**, **Cognito User Pool authorizer**, **Lambda authorizer** (token/request — сторонній механізм, власна таблиця користувачів у DynamoDB [T05-Q53](questions/2-security/apigw.md#t05-q53) [T12-Q30](questions/4-troubleshooting/apigw.md#t12-q30) [T11-Q34](questions/2-security/lambda.md#t11-q34)), resource policy. **STS не є механізмом авторизації API Gateway** [T01-Q02](questions/2-security/apigw.md#t01-q02) [T09-Q51](questions/2-security/lambda.md#t09-q51).
- **Usage plans + API keys** — монетизація та квоти/throttling для клієнтів [T11-Q14](questions/1-development/apigw.md#t11-q14). Новий ключ дає **403** → його не прив'язали до usage plan (`createUsagePlanKey`) [T06-Q64](questions/1-development/apigw.md#t06-q64). API key — НЕ механізм автентифікації.
- **CORS** — потрібен, коли браузер звертається з іншого домену (`Access-Control-Allow-Origin`) [T06-Q42](questions/1-development/apigw.md#t06-q42).
- Throttling: 10 000 rps / burst 5000 на акаунт-регіон (soft); помилка **429**.

### Коди помилок API Gateway
| Код | Причина |
|---|---|
| 400 | Bad request |
| 403 | Немає прав / неправильний API key / WAF / не той stage-ресурс |
| 429 | Throttling (Too Many Requests) |
| 500 | Помилка інтеграції; немає дозволу викликати Lambda (stage variable без `add-permission`) [T01-Q19](questions/4-troubleshooting/apigw.md#t01-q19) |
| 501 | Not Implemented: метод/функцію не реалізовано [T12-Q01](questions/4-troubleshooting/apigw.md#t12-q01) |
| 502 | Bad Gateway: Lambda повернула невалідну відповідь для proxy-формату |
| 504 | Integration timeout (за замовчуванням **29 с**) |

### Метрики
- **Latency** — від отримання запиту до відповіді клієнту (повний час) [T06-Q55](questions/4-troubleshooting/observability.md#t06-q55); **IntegrationLatency** — лише бекенд. `CacheHitCount` / `CacheMissCount`, `4XXError`, `5XXError`.
- Логи: **execution logs** (деталі виконання, помилки authorizer) + **access logs** (хто/що викликав) [T06-Q08](questions/4-troubleshooting/apigw.md#t06-q08); потрібна IAM-роль для CloudWatch на рівні акаунта [T12-Q40](questions/1-development/observability.md#t12-q40).

---

<a id="dynamodb"></a>
## 4. Amazon DynamoDB (Task 1.3) — найчастіша тема

### Ключі та індекси
- **Partition key (hash)** — розподіл даних, має бути **high-cardinality** [T12-Q06](questions/1-development/dynamodb.md#t12-q06). **Composite key** = partition key + **sort key (range)**: унікальність комбінації [T02-Q09](questions/1-development/dynamodb.md#t02-q09) [T08-Q40](questions/4-troubleshooting/dynamodb.md#t08-q40).
- **LSI** — той самий partition key, інший sort key; створюється **лише разом із таблицею**, ділить capacity з таблицею, до 5 на таблицю, підтримує strongly consistent reads [T03-Q07](questions/1-development/dynamodb.md#t03-q07).
- **GSI** — довільні PK/SK, можна додати будь-коли, **має власні RCU/WCU**, лише eventually consistent, до 20. Якщо GSI недопровізіонований, **throttling переноситься на базову таблицю** [T01-Q22](questions/1-development/dynamodb.md#t01-q22) [T05-Q45](questions/4-troubleshooting/dynamodb.md#t05-q45).
- «Топ гравців за грою» → **GSI: PK = game, SK = score** [T04-Q12](questions/1-development/dynamodb.md#t04-q12) [T05-Q41](questions/1-development/dynamodb.md#t05-q41) [T11-Q62](questions/1-development/dynamodb.md#t11-q62).

### Розрахунок capacity (буде 1–3 питання!)
- **1 RCU** = 1 strongly consistent read/с item'а до **4 KB**, або **2** eventually consistent reads/с. Транзакційне читання = **2 RCU**.
- **1 WCU** = 1 запис/с item'а до **1 KB**. Транзакційний запис = **2 WCU**.
- **Розмір завжди округлюй вгору** до 4 KB (читання) або до 1 KB (запис).

| Питання | Розрахунок | Відповідь |
|---|---|---|
| 10 strong reads × 4 KB | 10 × 1 | **10 RCU** [T12-Q27](questions/1-development/dynamodb.md#t12-q27) |
| 10 strong × 6 KB | 6→8 KB = 2 → 10 × 2 | **20 RCU** [T09-Q55](questions/1-development/dynamodb.md#t09-q55) |
| 16 eventual × 12 KB | 12 KB = 3 → 16 × 3 / 2 | **24 RCU** [T03-Q50](questions/1-development/dynamodb.md#t03-q50) |
| 100 strong × 5 KB | 5→8 = 2 → 100 × 2 | **200 RCU** [T05-Q39](questions/1-development/dynamodb.md#t05-q39) |
| 200 eventual × 12 KB | 3 × 200 / 2 | **300 RCU** [T08-Q59](questions/1-development/dynamodb.md#t08-q59) |
| 20 eventual × 4.5 KB; 12 writes × 4.5 KB | 2 × 20 / 2 = 20; 5 × 12 = 60 | **20 RCU / 60 WCU** [T04-Q42](questions/1-development/dynamodb.md#t04-q42) |
| 4 strong × 9 KB; 2 writes × 9 KB | 3 × 4 = 12; 9 × 2 = 18 | **12 RCU / 18 WCU** [T10-Q30](questions/1-development/dynamodb.md#t10-q30) |
| 6 writes × 4.5 KB | 5 × 6 | **30 WCU** [T12-Q08](questions/1-development/dynamodb.md#t12-q08) |

### Операції
| Операція | Особливості |
|---|---|
| `GetItem` | За повним ключем; `ConsistentRead=true` → strongly consistent [T06-Q31](questions/1-development/network.md#t06-q31) [T06-Q46](questions/1-development/network.md#t06-q46) [T09-Q31](questions/1-development/dynamodb.md#t09-q31) |
| `Query` | За PK (+ умова на SK); найдешевший спосіб читати багато записів; **Query на GSI з eventual consistency споживає найменше RCU** [T10-Q28](questions/1-development/dynamodb.md#t10-q28) |
| `Scan` | Читає всю таблицю. `FilterExpression` **не зменшує** спожиту capacity; `ProjectionExpression` (`--projection-expression`) обмежує атрибути у відповіді [T01-Q20](questions/1-development/dynamodb.md#t01-q20) |
| `BatchGetItem` | До **100** items / 16 MB за один round trip, з кількох таблиць [T05-Q08](questions/1-development/dynamodb.md#t05-q08) [T10-Q05](questions/1-development/dynamodb.md#t10-q05) |
| `BatchWriteItem` | До **25** put/delete; **не атомарний** (частина може не пройти) |
| `TransactWriteItems` / `TransactGetItems` | **All-or-nothing**, ідемпотентний (ClientRequestToken), **до 100 дій**, 4 MB, коштує ×2 [T01-Q57](questions/1-development/dynamodb.md#t01-q57) [T02-Q59](questions/1-development/dynamodb.md#t02-q59) [T09-Q32](questions/1-development/dynamodb.md#t09-q32) |
| `UpdateItem` | Оновлює або **створює** item, якщо його немає (upsert) [T09-Q12](questions/1-development/lambda.md#t09-q12) |
| Conditional writes | `ConditionExpression` — оптимістичне блокування, щоб записи не перетирали один одного [T05-Q02](questions/1-development/other.md#t05-q02) [T09-Q61](questions/1-development/dynamodb.md#t09-q61) |

### Scan: швидко vs обережно → [протиріччя c02](06-contradictions-and-traps.md#c02)
- **Parallel Scan** (`Segment`/`TotalSegments`) — швидше, але споживає більше throughput. Варто, коли таблиця ≥ 20 GB, throughput не використано повністю і послідовний scan повільний.
- **Менший page size (`Limit`)** — менший вплив на інших клієнтів таблиці.
- Мінімізувати час і не зачепити прод → **Parallel Scan + Limit (rate limiting)** [T10-Q21](questions/1-development/dynamodb.md#t10-q21).

### Гарячі партиції та throttling
- `ProvisionedThroughputExceededException`, хоча capacity сумарно вистачає → **hot partition** [T07-Q10](questions/1-development/dynamodb.md#t07-q10). Для записів → **write sharding** (випадковий/обчислюваний суфікс до PK) [T08-Q02](questions/4-troubleshooting/dynamodb.md#t08-q02); для читань → **DAX** [T01-Q50](questions/1-development/dynamodb.md#t01-q50).
- Throttling → SDK повторює з **exponential backoff**; якщо мова без SDK, реалізуй backoff сам [T05-Q44](questions/4-troubleshooting/dynamodb.md#t05-q44) [T10-Q02](questions/4-troubleshooting/dynamodb.md#t10-q02).
- Контроль ефективності provisioned → метрики **ConsumedRead/WriteCapacityUnits** [T06-Q18](questions/1-development/dynamodb.md#t06-q18).

### Streams, TTL, бекапи, глобальні таблиці
- **DynamoDB Streams** (24 год, порядок у межах item'а) → тригер Lambda на зміни: аудит, реплікація в іншу таблицю, near-real-time обробка [T02-Q27](questions/1-development/dynamodb.md#t02-q27) [T10-Q38](questions/1-development/dynamodb.md#t10-q38) [T03-Q36](questions/1-development/lambda.md#t03-q36) [T05-Q54](questions/1-development/lambda.md#t05-q54).
- **TTL** — безкоштовне автоматичне видалення окремих items за epoch-атрибутом (видаляються протягом кількох днів) [T04-Q55](questions/1-development/lambda.md#t04-q55) [T05-Q57](questions/1-development/network.md#t05-q57) [T08-Q08](questions/1-development/dynamodb.md#t08-q08) [T11-Q17](questions/1-development/dynamodb.md#t11-q17). Очистити **всю** таблицю → **drop & recreate** [T03-Q24](questions/4-troubleshooting/dynamodb.md#t03-q24) [T04-Q08](questions/1-development/dynamodb.md#t04-q08) → [c09](06-contradictions-and-traps.md#c09).
- Бекапи: on-demand / PITR. Отримати дані в S3 → **Export to S3** (on-demand backup **не** дає файлів у S3) [T09-Q34](questions/1-development/dynamodb.md#t09-q34).
- **Global tables** — мультирегіональні multi-active репліки для глобальних користувачів [T12-Q58](questions/4-troubleshooting/dynamodb.md#t12-q58).
- **DAX** — in-memory кеш **лише для DynamoDB**, мікросекундні затримки, API-сумісний [T04-Q09](questions/4-troubleshooting/dynamodb.md#t04-q09) [T01-Q56](questions/1-development/dynamodb.md#t01-q56). Перед RDS DAX не поставиш.

### Безпека DynamoDB
- Fine-grained access: `dynamodb:LeadingKeys` з `${cognito-identity.amazonaws.com:sub}` або `${www.amazon.com:user_id}` → користувач бачить лише свої items; `dynamodb:Attributes` → лише окремі атрибути [T04-Q37](questions/1-development/dynamodb.md#t04-q37) [T08-Q49](questions/2-security/dynamodb.md#t08-q49).
- Шифрування **до** відправлення → client-side: DynamoDB Encryption Client (зараз — AWS Database Encryption SDK) [T06-Q05](questions/2-security/kms.md#t06-q05).
- Доступ без інтернету → **gateway VPC endpoint** [T09-Q36](questions/4-troubleshooting/dynamodb.md#t09-q36) [T01-Q12](questions/2-security/network.md#t01-q12).

### Коли DynamoDB
Schemaless key-value, мілісекундна затримка, необмежений масштаб [T04-Q01](questions/1-development/dynamodb.md#t04-q01) [T05-Q22](questions/1-development/dynamodb.md#t05-q22), Streams для подієвої обробки. **Joins і складний SQL → RDS/Aurora.**

---

<a id="s3"></a>
## 5. Amazon S3

### Продуктивність
- **3 500 PUT/COPY/POST/DELETE** і **5 500 GET/HEAD** за секунду **на префікс** → 55 000 GET/с = **≥ 10 префіксів** [T05-Q19](questions/4-troubleshooting/s3.md#t05-q19).
- **Multipart upload**: рекомендовано від 100 MB, обов'язково понад 5 GB (максимальний об'єкт 5 TB). **Transfer Acceleration** (edge-локації) прискорює завантаження з далеких регіонів [T01-Q26](questions/1-development/s3.md#t01-q26) [T04-Q23](questions/1-development/s3.md#t04-q23) [T10-Q62](questions/4-troubleshooting/s3.md#t10-q62).
- **Byte-range fetch** — паралельне читання частинами.
- Вибрати частину CSV/JSON → S3 Select (**закритий для нових клієнтів**, замість нього — Athena) [T01-Q25](questions/1-development/s3.md#t01-q25) → [o04](07-outdated-and-wrong-answers.md#o04).

### Консистентність
- **Strong read-after-write** для PUT/DELETE об'єктів (з 12.2020): перезаписав → одразу читаєш нову версію [T12-Q43](questions/1-development/observability.md#t12-q43). **Список бакетів — eventually consistent** [T12-Q65](questions/4-troubleshooting/s3.md#t12-q65).

### Доступ
- **Pre-signed URL** — тимчасовий доступ до приватного об'єкта з правами того, хто підписав (download/upload) [T04-Q10](questions/1-development/s3.md#t04-q10) [T04-Q54](questions/2-security/s3.md#t04-q54) [T07-Q24](questions/1-development/s3.md#t07-q24) [T08-Q44](questions/2-security/s3.md#t08-q44) [T09-Q43](questions/4-troubleshooting/s3.md#t09-q43). Від hotlinking → прибрати публічний доступ і віддавати signed URLs [T10-Q10](questions/1-development/s3.md#t10-q10).
- Bucket policy (resource-based), IAM policy, ACL (за замовчуванням вимкнені, **Object Ownership: Bucket owner enforced**) [T07-Q47](questions/1-development/s3.md#t07-q47) [T07-Q54](questions/2-security/s3.md#t07-q54).
- 403 на static website → немає **bucket policy** з `s3:GetObject` для `*` / увімкнено Block Public Access [T01-Q59](questions/2-security/s3.md#t01-q59) [T06-Q22](questions/1-development/s3.md#t06-q22).
- **CORS** на бакеті, **з якого** вантажать ресурс (шрифти, картинки, відео з іншого бакета) [T03-Q42](questions/2-security/s3.md#t03-q42) [T07-Q57](questions/2-security/s3.md#t07-q57) [T08-Q07](questions/1-development/s3.md#t08-q07).
- Policy variables `${aws:username}` → «домашня папка» кожного користувача однією політикою [T01-Q46](questions/4-troubleshooting/s3.md#t01-q46) [T04-Q63](questions/2-security/s3.md#t04-q63) [T11-Q60](questions/2-security/iam.md#t11-q60).

### Static website
- Index document + публічне читання (bucket policy) [T06-Q22](questions/1-development/s3.md#t06-q22). Сайт S3 працює **лише по HTTP** → HTTPS дає **CloudFront + сертифікат ACM** [T02-Q20](questions/2-security/s3.md#t02-q20).

### Події, реплікація, логи
- Event notifications (`s3:ObjectCreated:*`) → Lambda / SQS / SNS / EventBridge [T03-Q26](questions/1-development/s3.md#t03-q26) [T06-Q19](questions/1-development/s3.md#t06-q19). Два одночасні записи в **неверсіонований** об'єкт можуть дати лише **одну** подію [T12-Q21](questions/4-troubleshooting/s3.md#t12-q21).
- **Replication (CRR/SRR)**: потрібен versioning; lifecycle-дії **не** реплікуються; фільтри за бакетом, префіксом або тегами [T07-Q60](questions/1-development/s3.md#t07-q60) [T08-Q16](questions/1-development/s3.md#t08-q16).
- Versioning вмикається **на весь бакет**, не на папку [T01-Q54](questions/1-development/s3.md#t01-q54).
- Access logging у **той самий** бакет → нескінченне зростання [T09-Q53](questions/4-troubleshooting/s3.md#t09-q53).
- Logs → **Athena** (SQL-запити просто по S3) [T02-Q29](questions/4-troubleshooting/s3.md#t02-q29); партиціювання (не надто дрібне й без перекосу) [T02-Q44](questions/1-development/s3.md#t02-q44), `MSCK REPAIR TABLE` для Hive-партицій [T02-Q42](questions/1-development/s3.md#t02-q42).

### Шифрування S3 → див. [Домен 2](02-domain2-security.md#s3enc)

---

<a id="messaging"></a>
## 6. SQS / SNS / EventBridge / SES

### SQS — числа
| Параметр | Значення |
|---|---|
| Розмір повідомлення | до **1 MiB** (з 08.2025; раніше 256 KB). Більше → **S3 + SQS Extended Client Library** (до 2 GB) [T04-Q32](questions/1-development/messaging.md#t04-q32) [T07-Q40](questions/1-development/messaging.md#t07-q40) |
| Retention | 1 хв – **14 днів**, за замовчуванням **4 дні** [T12-Q63](questions/1-development/messaging.md#t12-q63) |
| Visibility timeout | 0 – 12 год, за замовчуванням **30 с** [T11-Q12](questions/1-development/messaging.md#t11-q12) |
| Delay (delay queue / DelaySeconds) | 0 – **15 хв** [T03-Q33](questions/1-development/messaging.md#t03-q33) [T09-Q46](questions/1-development/messaging.md#t09-q46) [T10-Q04](questions/1-development/messaging.md#t10-q04) |
| Long polling (`WaitTimeSeconds` / `ReceiveMessageWaitTimeSeconds`) | 1 – **20 с** [T02-Q25](questions/1-development/messaging.md#t02-q25) [T08-Q62](questions/1-development/messaging.md#t08-q62) |
| Пакет ReceiveMessage | `MaxNumberOfMessages` 1–**10** (за замовчуванням 1) [T12-Q57](questions/1-development/messaging.md#t12-q57) [T05-Q29](questions/1-development/messaging.md#t05-q29) |
| Кількість повідомлень у черзі | **необмежена**; in-flight ≈ 120 000 (standard) [T09-Q03](questions/4-troubleshooting/messaging.md#t09-q03) |
| Тип черги (standard/FIFO) | **не можна змінити після створення** [T11-Q12](questions/1-development/messaging.md#t11-q12) |

### Ключові механізми
- **Long polling** — менше порожніх відповідей, менше витрат [T03-Q40](questions/4-troubleshooting/messaging.md#t03-q40) [T07-Q59](questions/2-security/messaging.md#t07-q59) [T10-Q26](questions/1-development/messaging.md#t10-q26) → завжди обирай його при словах «empty responses / cost».
- **Visibility timeout** — повідомлення обробляє інший consumer, поки перший ще працює → **збільш visibility timeout**, або в коді викликай `ChangeMessageVisibility` [T08-Q18](questions/1-development/messaging.md#t08-q18) [T12-Q39](questions/4-troubleshooting/messaging.md#t12-q39).
- **DLQ** + redrive policy (`maxReceiveCount`) — ізолювати «отруйні» повідомлення для дебагу [T01-Q09](questions/4-troubleshooting/messaging.md#t01-q09) [T08-Q09](questions/4-troubleshooting/messaging.md#t08-q09) [T09-Q64](questions/4-troubleshooting/messaging.md#t09-q64) [T12-Q36](questions/4-troubleshooting/messaging.md#t12-q36).
- **FIFO**: порядок у межах **`MessageGroupId`** [T01-Q31](questions/1-development/messaging.md#t01-q31), дедуплікація **`MessageDeduplicationId`** або content-based (вікно **5 хв**) [T01-Q35](questions/4-troubleshooting/messaging.md#t01-q35) [T04-Q56](questions/1-development/messaging.md#t04-q56). Продуктивність: 300 msg/с (3000 з батчами), high-throughput mode — значно більше.
- SQS сам масштабується, нічого робити не треба [T07-Q62](questions/1-development/messaging.md#t07-q62). Шифрування: **SSE-SQS / SSE-KMS** без змін у коді [T03-Q60](questions/1-development/messaging.md#t03-q60).
- `DeleteQueue` видаляє чергу разом з усіма повідомленнями; `PurgeQueue` — лише повідомлення [T11-Q08](questions/1-development/messaging.md#t11-q08).
- Доступ: IAM policy + **SQS access policy**; явний Deny перемагає [T03-Q17](questions/4-troubleshooting/iam.md#t03-q17) [T05-Q06](questions/2-security/messaging.md#t05-q06). Крос-акаунт — resource policy на черзі або роль [T03-Q57](questions/2-security/iam.md#t03-q57).

### SNS
- Pub/sub push: SQS, Lambda, HTTP/S, email, **SMS**, mobile push [T02-Q07](questions/1-development/messaging.md#t02-q07) [T04-Q35](questions/1-development/messaging.md#t04-q35). **SES** — лише email (масові розсилки), throttling SES → backoff [T09-Q48](questions/4-troubleshooting/messaging.md#t09-q48).
- **Fan-out**: SNS → кілька SQS [T05-Q62](questions/1-development/messaging.md#t05-q62) [T03-Q65](questions/4-troubleshooting/messaging.md#t03-q65). **Message filtering** (filter policy на підписці) [T09-Q23](questions/3-deployment/messaging.md#t09-q23).
- Payload до 1 MiB (налаштування `MaximumMessageSize` топіка, з 09.2026).

### EventBridge
- Rules (event pattern або schedule) → targets (Lambda, SQS, Step Functions…). **CloudWatch Events = EventBridge** [T02-Q45](questions/3-deployment/lambda.md#t02-q45) [T01-Q48](questions/4-troubleshooting/lambda.md#t01-q48) [T03-Q11](questions/4-troubleshooting/lambda.md#t03-q11) [T12-Q15](questions/4-troubleshooting/cicd.md#t12-q15).
- **EventBridge Scheduler** — cron/rate-розклади (наприклад, щомісячний snapshot RDS через Lambda) [T07-Q42](questions/1-development/lambda.md#t07-q42).
- Крос-акаунтні event bus'и (події в інші акаунти) [T10-Q29](questions/1-development/messaging.md#t10-q29).

---

<a id="kinesis"></a>
## 7. Kinesis Data Streams / Amazon Data Firehose

| | Kinesis Data Streams | Amazon Data Firehose |
|---|---|---|
| Призначення | Real-time стрім, власні consumers, replay | Доставка (near real-time, буферизація) без коду |
| Retention | **24 год за замовчуванням** (до 365 днів) [T10-Q01](questions/4-troubleshooting/kinesis.md#t10-q01) | немає зберігання |
| Масштабування | Шарди (provisioned) або on-demand | Автоматично |
| Призначення даних | Lambda, KCL-застосунки, Firehose… | S3, Redshift, OpenSearch, Splunk, HTTP; **ElastiCache — НЕ підтримується** [T09-Q44](questions/1-development/kinesis.md#t09-q44) |

### Шарди та ліміти
- Запис: **1 MB/с або 1000 записів/с на шард**; читання: **2 MB/с на шард** (спільні для всіх consumers, 5 GetRecords/с). Запис до 1 MiB за замовчуванням, можна підняти до **10 MiB** (з 2025).
- `ProvisionedThroughputExceededException` → **retry з exponential backoff**, **більше шардів** (`UpdateShardCount` / resharding) [T09-Q25](questions/4-troubleshooting/kinesis.md#t09-q25) [T10-Q20](questions/1-development/kinesis.md#t10-q20) [T12-Q34](questions/4-troubleshooting/kinesis.md#t12-q34), а якщо шарди недовантажені — **краще розподілений partition key** [T03-Q16](questions/1-development/kinesis.md#t03-q16).
- **Порядок гарантовано лише в межах шарда**, між шардами — ні [T10-Q60](questions/1-development/kinesis.md#t10-q60) [T07-Q36](questions/1-development/kinesis.md#t07-q36).
- **KCL**: один worker (EC2-інстанс) обробляє ≥ 1 шард; **максимум корисних інстансів = кількість шардів** [T01-Q45](questions/1-development/kinesis.md#t01-q45) [T10-Q56](questions/1-development/kinesis.md#t10-q56).
- **Enhanced fan-out** — окремі 2 MB/с на шард для кожного consumer'а (push, менша затримка) [T12-Q47](questions/2-security/kinesis.md#t12-q47).
- Producer: **KPL** (батчинг/агрегація, краще використання шардів) [T04-Q05](questions/1-development/kinesis.md#t04-q05); **Kinesis Agent** — логи з EC2 [T03-Q34](questions/4-troubleshooting/observability.md#t03-q34).
- Шифрування: **KMS (SSE) at rest + HTTPS in flight** — без змін у коді [T08-Q61](questions/2-security/kms.md#t08-q61) [T12-Q33](questions/2-security/kms.md#t12-q33).

---

<a id="stepfn"></a>
## 8. AWS Step Functions
- State machine мовою **Amazon States Language (JSON)** [T04-Q40](questions/1-development/stepfn.md#t04-q40). Стани: **Task** (одиниця роботи) [T09-Q38](questions/1-development/stepfn.md#t09-q38), Choice, **Parallel** (паралельні гілки) [T06-Q04](questions/4-troubleshooting/lambda.md#t06-q04), Map, Wait, Pass, Succeed, Fail.
- **Standard** — до 1 року, exactly-once, аудит, підходить для human approval; **Express** — до 5 хв, висока частота подій, at-least-once [T07-Q25](questions/1-development/stepfn.md#t07-q25).
- Обробка помилок: **Retry** (`ErrorEquals`, `IntervalSeconds`, `BackoffRate`, `MaxAttempts`) і **Catch**. Зберегти вхід разом із помилкою → **`ResultPath` у Catch** (напр. `"$.error"`) [T02-Q54](questions/4-troubleshooting/stepfn.md#t02-q54).
- Фільтри даних: `InputPath` → `Parameters` → (task) → `ResultSelector` → `ResultPath` → `OutputPath`.
- Заміна крихкого самописного коду оркестрації → Step Functions [T10-Q18](questions/1-development/stepfn.md#t10-q18) [T10-Q40](questions/1-development/stepfn.md#t10-q40) [T07-Q44](questions/1-development/stepfn.md#t07-q44).

---

<a id="cache"></a>
## 9. ElastiCache, RDS, Aurora

### ElastiCache: Redis (Valkey) vs Memcached
| Redis OSS / Valkey | Memcached |
|---|---|
| Реплікація, **Multi-AZ, автоматичний failover**, персистентність, бекапи | Без реплікації та персистентності |
| Складні структури: sorted sets (рейтинги), geospatial, pub/sub [T03-Q49](questions/1-development/kms.md#t03-q49) [T03-Q52](questions/4-troubleshooting/cache.md#t03-q52) | Прості key-value, **багатопотоковий** |
| Cluster mode = шардинг (горизонтальне масштабування) | Горизонтальне масштабування вузлами |
| Шифрування at-rest / in-transit, AUTH | Шифрування in-transit (новіші версії) |

→ Для **HA, шифрування, дорогого відновлення кешу** обирай **Redis** [T02-Q05](questions/1-development/cache.md#t02-q05) [T07-Q28](questions/1-development/cache.md#t07-q28) [T08-Q38](questions/1-development/cache.md#t08-q38). Memcached — простий кеш або сесії без вимог HA [T08-Q50](questions/4-troubleshooting/cache.md#t08-q50). В Redis cluster mode **replica вручну на primary не підвищиш**; всі вузли — в одному регіоні [T03-Q39](questions/3-deployment/cache.md#t03-q39).

### Стратегії кешування → [c03](06-contradictions-and-traps.md#c03)
| Стратегія | Плюси | Мінуси | Тригер у питанні |
|---|---|---|---|
| **Lazy loading (cache-aside)** | Кешується лише запитане, падіння кешу не фатальне | Cache miss = 3 походи, дані старіють | «кешувати лише те, що запитують» [T04-Q45](questions/1-development/cache.md#t04-q45) |
| **Write-through** | Дані завжди актуальні | Кеш роздувається рідко потрібними даними, запис повільніший [T06-Q43](questions/1-development/cache.md#t06-q43) | «завжди актуально», «real-time» [T01-Q28](questions/4-troubleshooting/cache.md#t01-q28) [T05-Q32](questions/4-troubleshooting/cache.md#t05-q32) [T06-Q25](questions/1-development/cache.md#t06-q25) |
| **TTL** (з будь-якою стратегією) | Обмежує застарілість | — | «допустимі застарілі дані до 1 хв» → lazy + TTL [T01-Q40](questions/4-troubleshooting/cache.md#t01-q40) |
| Запис у БД + **інвалідація** кешу | Кеш не розходиться з БД | — | [T01-Q53](questions/1-development/cache.md#t01-q53) [T05-Q48](questions/1-development/cache.md#t05-q48) |

### Сесії → [c01](06-contradictions-and-traps.md#c01)
**ElastiCache** (найменша затримка) або **DynamoDB** (довговічність, serverless). **Sticky sessions — не рішення** для відмовостійкості: при падінні інстанса сесія губиться [T02-Q18](questions/1-development/cache.md#t02-q18).

### RDS / Aurora
- **Multi-AZ** — синхронна standby-копія для **HA/DR**, з неї не читають; автоматичний failover, OS-оновлення спершу на standby [T04-Q58](questions/1-development/network.md#t04-q58) [T12-Q04](questions/1-development/cache.md#t12-q04).
- **Read replicas** — асинхронні, для **масштабування читань**; застосунок має використовувати **окремий endpoint** [T02-Q41](questions/1-development/cache.md#t02-q41) [T11-Q40](questions/1-development/cache.md#t11-q40). Cross-region replica → звітність в іншому регіоні / DR [T04-Q38](questions/4-troubleshooting/cache.md#t04-q38) [T07-Q19](questions/4-troubleshooting/cache.md#t07-q19).
- **Aurora**: до 15 Aurora Replicas у кластері (асинхронні, лаг < 100 мс), спільне сховище, reader endpoint, **Aurora Auto Scaling** реплік [T02-Q62](questions/4-troubleshooting/cache.md#t02-q62) [T02-Q65](questions/1-development/cache.md#t02-q65), binlog-реплікація в інший кластер. Failover на репліку в тому ж регіоні [T02-Q33](questions/1-development/cache.md#t02-q33).
- `max_connections` — ліміт з'єднань Aurora MySQL [T02-Q53](questions/1-development/cache.md#t02-q53); багато з'єднань від Lambda → **RDS Proxy** [T02-Q08](questions/4-troubleshooting/lambda.md#t02-q08).
- Storage auto scaling для RDS — коли закінчується місце [T01-Q05](questions/4-troubleshooting/cache.md#t01-q05).
- **IAM DB authentication** — MySQL, PostgreSQL (і Aurora) [T07-Q12](questions/2-security/cache.md#t07-q12).
- Довше зберігати бекапи (автоматичні — до 35 днів) → ручні snapshots за розкладом (EventBridge Scheduler + Lambda) [T07-Q42](questions/1-development/lambda.md#t07-q42).

---

<a id="network"></a>
## 10. EC2, Auto Scaling, ELB, VPC, EBS/EFS

### ELB
| ALB (L7) | NLB (L4) |
|---|---|
| HTTP/HTTPS/gRPC, маршрутизація за **path / host / query string / header / cookie** [T03-Q54](questions/1-development/network.md#t03-q54) [T04-Q48](questions/1-development/network.md#t04-q48) [T06-Q47](questions/3-deployment/network.md#t06-q47) [T12-Q37](questions/4-troubleshooting/network.md#t12-q37) | TCP/UDP/TLS, мільйони rps, статичні IP |
| IP клієнта — в заголовку **X-Forwarded-For** (`X-Forwarded-Proto` = протокол) [T02-Q55](questions/4-troubleshooting/network.md#t02-q55) [T07-Q04](questions/1-development/network.md#t07-q04) [T12-Q25](questions/4-troubleshooting/network.md#t12-q25) | **Зберігає IP і порт клієнта** без XFF [T03-Q43](questions/1-development/network.md#t03-q43) |
| Target types: instance, IP (**лише приватні IP**), **Lambda** [T03-Q48](questions/1-development/network.md#t03-q48) | |
| Автентифікація через **Cognito / OIDC** прямо на ALB [T12-Q12](questions/2-security/cognito.md#t12-q12) | |

- **503** = немає зареєстрованих/здорових targets [T01-Q27](questions/1-development/network.md#t01-q27) [T12-Q31](questions/4-troubleshooting/network.md#t12-q31); **504** = таймаут бекенду; **502** = погана відповідь бекенду.
- Wildcard host `*.sample.com` → `test.sample.com` (але не `sample.com`) [T12-Q26](questions/4-troubleshooting/network.md#t12-q26).
- **Cross-zone LB**: трафік ділиться порівну між **усіма** інстансами: 10 інстансів → по **10%** [T11-Q38](questions/1-development/network.md#t11-q38). Нерівномірний розподіл → sticky sessions або різна кількість інстансів у різних AZ [T07-Q08](questions/4-troubleshooting/network.md#t07-q08).
- SSL termination на ELB (сертифікат з ACM) знімає навантаження з CPU інстансів [T05-Q05](questions/2-security/network.md#t05-q05) [T10-Q48](questions/2-security/kms.md#t10-q48) [T12-Q56](questions/2-security/kms.md#t12-q56).
- Health checks: неправильний шлях або SG інстанса не пускає SG балансера → `unhealthy` [T11-Q65](questions/4-troubleshooting/network.md#t11-q65). ASG health check type **ELB**, щоб замінювати інстанси, які не відповідають [T12-Q62](questions/4-troubleshooting/network.md#t12-q62) [T12-Q20](questions/4-troubleshooting/network.md#t12-q20).
- DNS-ім'я ELB стабільне, IP змінюється → **не хардкодь IP** [T12-Q38](questions/1-development/network.md#t12-q38).

### Auto Scaling
- **Target tracking** (наприклад, CPU 70%) [T04-Q26](questions/2-security/network.md#t04-q26); метрики для target tracking: CPU, NetworkIn/Out, **ALBRequestCountPerTarget**; `ApproximateNumberOfMessagesVisible` — **не** вбудована метрика target tracking [T09-Q47](questions/4-troubleshooting/network.md#t09-q47) → для SQS рахують **backlog per instance** (custom metric) [T11-Q39](questions/4-troubleshooting/observability.md#t11-q39).
- Не перевищує **max**: на максимумі нічого не станеться [T12-Q50](questions/4-troubleshooting/network.md#t12-q50); політика «+3» при max 3 / current 2 додасть лише 1 [T09-Q45](questions/1-development/network.md#t09-q45).
- Unhealthy-інстанс ASG **термінує і замінює** [T09-Q15](questions/4-troubleshooting/network.md#t09-q15). Інстанс, який «сам перезапустився» після зупинки, — частина ASG [T12-Q13](questions/4-troubleshooting/network.md#t12-q13).
- Min = 1 в кількох AZ → при падінні AZ буде downtime → **min ≥ 2** [T07-Q01](questions/4-troubleshooting/network.md#t07-q01).
- ASG живе в одному регіоні, але охоплює кілька AZ [T11-Q05](questions/1-development/network.md#t11-q05); працює з ALB і NLB; не додає том до інстанса [T12-Q44](questions/1-development/network.md#t12-q44).
- Скрипт при запуску → **user data** (виконується **від root**, **лише при першому запуску** за замовчуванням) [T11-Q41](questions/4-troubleshooting/network.md#t11-q41) [T10-Q08](questions/1-development/network.md#t10-q08); launch **template** (launch configuration застарілі) [T12-Q35](questions/1-development/network.md#t12-q35).

### VPC
- **Security Group — stateful** (досить inbound), **NACL — stateless** (потрібні inbound + **outbound на ephemeral 1024–65535**) [T03-Q55](questions/1-development/network.md#t03-q55) [T09-Q50](questions/4-troubleshooting/network.md#t09-q50) [T11-Q13](questions/2-security/network.md#t11-q13).
- Інтернет: IGW + маршрут у route table + публічна IP + NACL/SG [T09-Q37](questions/4-troubleshooting/network.md#t09-q37).
- **Gateway endpoints — лише S3 і DynamoDB** (запис у route table) [T01-Q12](questions/2-security/network.md#t01-q12); інші сервіси → interface endpoints [T04-Q59](questions/1-development/network.md#t04-q59).
- **VPC Flow Logs** — трафік ENI/підмереж (дебаг «чи доходить трафік») [T05-Q13](questions/1-development/network.md#t05-q13) [T06-Q37](questions/1-development/network.md#t06-q37) [T09-Q33](questions/4-troubleshooting/network.md#t09-q33).
- Висока доступність → **підмережа в кожній AZ** [T04-Q22](questions/1-development/network.md#t04-q22).

### EC2
- Метадані: `http://169.254.169.254/latest/meta-data/` (IMDSv2 вимагає токен) [T01-Q36](questions/4-troubleshooting/network.md#t01-q36).
- **Elastic IP** — статична публічна IP (не змінюється після stop/start, можна перекинути на інший інстанс) [T01-Q14](questions/4-troubleshooting/edge.md#t01-q14) [T05-Q58](questions/1-development/network.md#t05-q58).
- **EBS прив'язаний до AZ** [T09-Q21](questions/1-development/network.md#t09-q21); шифрування EBS: KMS, у знімках і копіях, «encryption by default» — налаштування регіону [T07-Q26](questions/2-security/kms.md#t07-q26) [T12-Q49](questions/2-security/kms.md#t12-q49); `DeleteOnTermination` змінюється на запущеному інстансі через CLI [T12-Q18](questions/4-troubleshooting/network.md#t12-q18). gp2: 3 IOPS/GiB, максимум 16 000 → ~**5,3 TiB** [T09-Q10](questions/1-development/network.md#t09-q10); io1: до 50 IOPS/GiB (200 GiB → максимум 10 000, тож 15 000 невалідно) [T09-Q56](questions/1-development/network.md#t09-q56).
- **EFS** — спільна файлова система (NFS) для багатьох EC2/Lambda/Fargate, між AZ [T06-Q36](questions/1-development/network.md#t06-q36) [T11-Q11](questions/1-development/containers.md#t11-q11) [T06-Q54](questions/4-troubleshooting/lambda.md#t06-q54); класи Standard-IA [T12-Q23](questions/1-development/network.md#t12-q23).
- Детальний моніторинг: `aws ec2 monitor-instances` [T07-Q38](questions/1-development/observability.md#t07-q38); перевірка прав без виконання → `--dry-run` [T09-Q06](questions/4-troubleshooting/iam.md#t09-q06).
- Ціни: RI zonal = резервування потужності [T09-Q19](questions/1-development/network.md#t09-q19); Dedicated Instances — single-tenant [T11-Q35](questions/2-security/network.md#t11-q35); Reserved — для стабільного навантаження [T12-Q42](questions/1-development/network.md#t12-q42) [T09-Q05](questions/1-development/other.md#t09-q05).

---

<a id="edge"></a>
## 11. CloudFront, Route 53

### CloudFront
- Origins: S3, ALB, EC2, HTTP; **кілька origins + cache behaviors (path patterns)** для статики і динаміки [T03-Q32](questions/4-troubleshooting/s3.md#t03-q32) [T01-Q23](questions/2-security/s3.md#t01-q23).
- HTTPS: **Viewer Protocol Policy** (`Redirect HTTP to HTTPS` / `HTTPS Only`) + **Origin Protocol Policy** (`HTTPS Only`) = end-to-end [T03-Q25](questions/2-security/edge.md#t03-q25) [T05-Q60](questions/2-security/network.md#t05-q60) [T08-Q32](questions/2-security/edge.md#t08-q32).
- Застарілий вміст → **invalidation** (`/path/*`) [T04-Q33](questions/1-development/edge.md#t04-q33) [T08-Q14](questions/3-deployment/edge.md#t08-q14) [T12-Q10](questions/1-development/s3.md#t12-q10); краще — версіоновані імена файлів.
- Приватний вміст: **signed URLs** (один файл) / **signed cookies** (багато файлів) [T09-Q01](questions/2-security/edge.md#t09-q01) [T04-Q29](questions/2-security/s3.md#t04-q29); доступ до S3 лише через CloudFront → **OAC** (раніше OAI). Signer'и — **trusted key groups** (рекомендовано) або CloudFront key pairs root-акаунта (до 2 на акаунт) [T09-Q40](questions/2-security/edge.md#t09-q40) [T11-Q27](questions/2-security/iam.md#t11-q27).
- Query strings / headers / cookies у **cache key** (cache policy) — інакше всі мовні версії зіллються в одну [T01-Q52](questions/1-development/edge.md#t01-q52).
- **Origin groups (failover)** — лише для GET/HEAD/OPTIONS; кожен запит спершу йде на primary [T12-Q14](questions/4-troubleshooting/edge.md#t12-q14).
- **Lambda@Edge** (us-east-1): viewer request/response, origin request/response. Змінити відповідь origin (404 → редирект) → **origin response** [T06-Q34](questions/1-development/edge.md#t06-q34); маршрутизувати на найближчий бакет при cache miss → **origin request** з заголовком `CloudFront-Viewer-Country` [T12-Q54](questions/4-troubleshooting/s3.md#t12-q54). **CloudFront Functions** — легші, лише viewer-події.
- Глобальна продуктивність → CloudFront перед S3/ALB [T03-Q21](questions/2-security/network.md#t03-q21) [T06-Q41](questions/1-development/s3.md#t06-q41) [T07-Q58](questions/4-troubleshooting/s3.md#t07-q58) [T09-Q60](questions/4-troubleshooting/network.md#t09-q60).

### Route 53
| Політика | Коли |
|---|---|
| Simple | Один ресурс |
| **Weighted** | % трафіку (20% на новий регіон) [T10-Q39](questions/2-security/iam.md#t10-q39) |
| **Latency** | Мінімальна затримка для різних регіонів [T06-Q09](questions/4-troubleshooting/edge.md#t06-q09) |
| **Geolocation** | За країною користувача (контент або доступ лише для Бразилії) [T11-Q06](questions/2-security/edge.md#t11-q06) |
| Geoproximity | За відстанню + bias |
| **Failover** | Active-passive з health checks |
| Multivalue | До 8 здорових записів |

- **CNAME** — на чуже доменне ім'я (не для zone apex); **Alias** — на AWS-ресурси (ELB, CloudFront, S3, API GW), безкоштовний, працює на apex [T11-Q02](questions/1-development/edge.md#t11-q02) [T05-Q01](questions/2-security/kms.md#t05-q01).
- Health checks: параметр **failure threshold** [T06-Q59](questions/4-troubleshooting/edge.md#t06-q59); статус видно в дашборді Route 53, CloudWatch alarm — опційно [T06-Q02](questions/4-troubleshooting/edge.md#t06-q02) [T06-Q27](questions/1-development/edge.md#t06-q27).
