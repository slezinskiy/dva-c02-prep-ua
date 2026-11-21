# Домен 4 — Усунення несправностей та оптимізація (18%; у DVA-C03 — 22%)

> Що перевіряє: root cause analysis, логи/метрики/трейси (CloudWatch, X-Ray, CloudTrail), коди помилок, throttling, оптимізацію продуктивності та вартості.

**Зміст:** [CloudWatch / X-Ray / CloudTrail](#observability) · [Коди помилок і їхні причини](#errors) · [Throttling і retries](#throttling) · [Оптимізація](#optimization) · [Плейбук дебагу](#playbook)

---

<a id="observability"></a>
## 1. Спостережуваність

### CloudWatch Metrics
- Стандартні метрики EC2 (CPU, мережа, диск, status checks) — кожні **5 хв**, з **detailed monitoring** — кожну **1 хв** [T07-Q38](questions/1-development/observability.md#t07-q38). **RAM і місце на диску «з коробки» не збираються** → потрібен **CloudWatch agent** або custom metric [T03-Q06](questions/4-troubleshooting/observability.md#t03-q06) [T07-Q11](questions/4-troubleshooting/observability.md#t07-q11).
- **Custom metrics:** `PutMetricData` / `aws cloudwatch put-metric-data` (скрипт, cron) [T02-Q34](questions/4-troubleshooting/observability.md#t02-q34); **dimensions** (InstanceId, AutoScalingGroupName…) для групування [T04-Q31](questions/4-troubleshooting/observability.md#t04-q31); **namespace** — свій для кожного застосунку [T05-Q43](questions/4-troubleshooting/observability.md#t05-q43).
- **High-resolution metrics** — точність 1 с (StorageResolution=1) [T08-Q24](questions/4-troubleshooting/observability.md#t08-q24) [T07-Q22](questions/4-troubleshooting/network.md#t07-q22) [T10-Q34](questions/4-troubleshooting/containers.md#t10-q34). **Alarm** на high-resolution метриці — з періодом **10 або 30 с** (платно) [T02-Q02](questions/4-troubleshooting/observability.md#t02-q02).
- **Embedded Metric Format (EMF)** — метрики через структурований лог (зручно для Lambda).
- Alarm → **SNS** (email/SMS), Auto Scaling, EC2 actions [T01-Q38](questions/4-troubleshooting/observability.md#t01-q38) [T06-Q28](questions/4-troubleshooting/messaging.md#t06-q28) [T09-Q04](questions/4-troubleshooting/observability.md#t09-q04). Відсоток помилок за годину → custom metric + alarm на SNS-топік [T06-Q28](questions/4-troubleshooting/messaging.md#t06-q28) [T09-Q04](questions/4-troubleshooting/observability.md#t09-q04).
- **Cross-account cross-region dashboards** [T06-Q17](questions/4-troubleshooting/observability.md#t06-q17).

### CloudWatch Logs
- **CloudWatch agent (unified)** — логи + системні метрики з EC2 **і on-premises** (для on-prem — IAM credentials користувача) [T01-Q18](questions/4-troubleshooting/observability.md#t01-q18) [T04-Q64](questions/4-troubleshooting/observability.md#t04-q64) [T05-Q21](questions/4-troubleshooting/observability.md#t05-q21) [T10-Q41](questions/4-troubleshooting/observability.md#t10-q41).
- **Metric filters** — витягують метрики з логів (з dimensions, напр. location) [T02-Q10](questions/4-troubleshooting/observability.md#t02-q10); **працюють лише для подій, що надійшли ПІСЛЯ створення фільтра** [T05-Q25](questions/4-troubleshooting/observability.md#t05-q25) [T08-Q64](questions/4-troubleshooting/observability.md#t08-q64).
- **Logs Insights** — запити до логів (ad-hoc). Метрики не створює, alarm не дає [T02-Q10](questions/4-troubleshooting/observability.md#t02-q10).
- **Subscription filters** → Kinesis / Firehose / Lambda / OpenSearch (стрімінг логів). Експорт у S3 (`CreateExportTask`) [T03-Q20](questions/1-development/s3.md#t03-q20).
- Шифрування log group через KMS (`associate-kms-key`) [T03-Q22](questions/2-security/kms.md#t03-q22); **data protection** — маскування PII [T08-Q17](questions/4-troubleshooting/observability.md#t08-q17).
- Lambda: `console.log`/`print` → CloudWatch Logs (потрібні права в execution role) [T07-Q45](questions/4-troubleshooting/lambda.md#t07-q45) [T05-Q15](questions/4-troubleshooting/observability.md#t05-q15). ECS: драйвер `awslogs` [T12-Q16](questions/1-development/containers.md#t12-q16) [T12-Q32](questions/4-troubleshooting/observability.md#t12-q32).

### AWS X-Ray (і перехід на OpenTelemetry)
- Distributed tracing: **service map**, traces → segments → subsegments; знаходить вузькі місця та помилки між сервісами [T02-Q06](questions/4-troubleshooting/lambda.md#t02-q06) [T08-Q21](questions/4-troubleshooting/apigw.md#t08-q21) [T12-Q01](questions/4-troubleshooting/apigw.md#t12-q01).
- **Annotations** — key-value, **індексуються** → фільтр-вирази (`annotation.user = "x"`) [T01-Q43](questions/4-troubleshooting/observability.md#t01-q43) [T04-Q02](questions/4-troubleshooting/observability.md#t04-q02) [T05-Q35](questions/4-troubleshooting/observability.md#t05-q35). **Metadata** — не індексуються (для довільних даних). Поле `user` на сегменті + `GetTraceSummaries` з filter expression [T05-Q46](questions/4-troubleshooting/observability.md#t05-q46).
- **Sampling rules** — менше трейсів, менші витрати, тренди залишаються [T07-Q63](questions/4-troubleshooting/observability.md#t07-q63). Для дебагу «чому не надсилаються дані» sampling **не допомагає** [T11-Q42](questions/4-troubleshooting/observability.md#t11-q42).
- **X-Ray daemon**: слухає UDP **2000**, пересилає в API (`PutTraceSegments`) [T10-Q36](questions/4-troubleshooting/observability.md#t10-q36). Адреса для SDK → `AWS_XRAY_DAEMON_ADDRESS` [T01-Q03](questions/4-troubleshooting/observability.md#t01-q03); `AWS_XRAY_TRACING_NAME`, `AWS_XRAY_CONTEXT_MISSING`.
- Де запускати: Lambda — галочка **Active tracing**; EB — опція в `.ebextensions`; ECS/EC2 — daemon як контейнер [T06-Q65](questions/3-deployment/containers.md#t06-q65); **Fargate — sidecar** [T01-Q11](questions/4-troubleshooting/containers.md#t01-q11); on-prem — встанови daemon [T07-Q17](questions/4-troubleshooting/observability.md#t07-q17).
- Немає даних → **IAM-права** (`xray:PutTraceSegments`, `PutTelemetryRecords`; `AWSXRayDaemonWriteAccess`) [T09-Q57](questions/4-troubleshooting/observability.md#t09-q57).
- Крос-акаунт: daemon припускає роль у центральному акаунті (`--role-arn`) [T01-Q29](questions/2-security/iam.md#t01-q29) [T11-Q54](questions/4-troubleshooting/observability.md#t11-q54).
- ⚠️ **X-Ray SDK і daemon у maintenance mode з 25.02.2026, кінець підтримки — 25.02.2027** → новий стандарт **ADOT / OpenTelemetry + CloudWatch agent** → [o05](07-outdated-and-wrong-answers.md#o05).

### CloudTrail
- Журнал **API-викликів** (хто, коли, звідки): аудит, «хто видалив/змінив» [T08-Q11](questions/1-development/s3.md#t08-q11) [T08-Q55](questions/4-troubleshooting/observability.md#t08-q55) [T11-Q53](questions/4-troubleshooting/secrets.md#t11-q53) [T12-Q52](questions/4-troubleshooting/cicd.md#t12-q52). Trail на всі регіони → один S3-бакет [T08-Q11](questions/1-development/s3.md#t08-q11). Event history — 90 днів.
- Деякі події не логуються окремо: наприклад, `CreateVolume` для томів, створених під час `RunInstances` [T12-Q64](questions/4-troubleshooting/observability.md#t12-q64).

### Інші джерела
- **VPC Flow Logs** — мережевий трафік (IP, порти, ACCEPT/REJECT) [T05-Q13](questions/1-development/network.md#t05-q13) [T06-Q37](questions/1-development/network.md#t06-q37) [T09-Q33](questions/4-troubleshooting/network.md#t09-q33).
- **ALB access logs** — IP клієнтів, latency, коди відповідей [T11-Q09](questions/4-troubleshooting/network.md#t11-q09).
- **S3 access logs**, **API Gateway execution/access logs** [T06-Q08](questions/4-troubleshooting/apigw.md#t06-q08).

---

<a id="errors"></a>
## 2. Коди помилок і їхні причини

| Помилка | Найімовірніша причина / рішення |
|---|---|
| ALB **503** | Немає зареєстрованих або здорових targets [T01-Q27](questions/1-development/network.md#t01-q27) [T12-Q31](questions/4-troubleshooting/network.md#t12-q31) |
| ALB **502 / 504** | Погана відповідь / таймаут бекенду |
| Timeout при зверненні до сайту на ELB | **Security groups** (і NACL) [T01-Q32](questions/4-troubleshooting/network.md#t01-q32) |
| Інстанси `unhealthy`, хоча сайт відкривається за IP | Шлях health check або SG інстанса не пускає SG ALB [T11-Q65](questions/4-troubleshooting/network.md#t11-q65) |
| API GW **500** зі stage variable → Lambda | Немає `lambda:InvokeFunction` (resource policy) для alias [T01-Q19](questions/4-troubleshooting/apigw.md#t01-q19) |
| API GW **429** / Lambda **429** | Throttling / concurrency |
| API GW **403** з API key | Ключ не в usage plan [T06-Q64](questions/1-development/apigw.md#t06-q64) |
| **CORS error** у браузері | Увімкнути CORS на API GW / бакеті [T06-Q42](questions/1-development/apigw.md#t06-q42) [T08-Q07](questions/1-development/s3.md#t08-q07) |
| S3 **403** на static website | Немає bucket policy з публічним `GetObject` [T01-Q59](questions/2-security/s3.md#t01-q59) |
| `AccessDenied` + закодоване повідомлення | `aws sts decode-authorization-message` [T03-Q45](questions/2-security/iam.md#t03-q45) [T08-Q45](questions/2-security/iam.md#t08-q45) |
| PutObject AccessDenied при SSE-KMS | Немає `kms:GenerateDataKey` [T07-Q39](questions/1-development/iam.md#t07-q39) |
| DynamoDB `ProvisionedThroughputExceededException` | Hot partition / GSI throttling / потрібен backoff [T07-Q10](questions/1-development/dynamodb.md#t07-q10) [T01-Q22](questions/1-development/dynamodb.md#t01-q22) [T05-Q45](questions/4-troubleshooting/dynamodb.md#t05-q45) |
| Kinesis `ProvisionedThroughputExceededException` | Backoff, більше шардів, кращий partition key [T09-Q25](questions/4-troubleshooting/kinesis.md#t09-q25) [T03-Q16](questions/1-development/kinesis.md#t03-q16) [T12-Q34](questions/4-troubleshooting/kinesis.md#t12-q34) |
| KMS `ThrottlingException` | Backoff, кеш data keys, квота [T01-Q42](questions/2-security/kms.md#t01-q42) [T05-Q03](questions/2-security/kms.md#t05-q03) |
| SES `Throttling – Maximum sending rate exceeded` | Exponential backoff [T09-Q48](questions/4-troubleshooting/messaging.md#t09-q48) |
| CloudWatch API `ThrottlingException` (400) | Retry з exponential backoff [T06-Q06](questions/4-troubleshooting/observability.md#t06-q06) |
| Kinesis `Internal service failure` | Зменшити частоту/розмір запитів + backoff [T11-Q25](questions/4-troubleshooting/kinesis.md#t11-q25) |
| Lambda `Memory Size … Max Memory Used` | Забракло RAM [T03-Q61](questions/1-development/lambda.md#t03-q61) |
| Lambda без логів | Права execution role на CloudWatch Logs [T05-Q15](questions/4-troubleshooting/observability.md#t05-q15) [T10-Q45](questions/4-troubleshooting/observability.md#t10-q45) |
| X-Ray без даних | Права ролі / daemon не запущено [T09-Q57](questions/4-troubleshooting/observability.md#t09-q57) [T11-Q42](questions/4-troubleshooting/observability.md#t11-q42) |
| CodeBuild не тягне/не пушить образ | Service role CodeBuild [T01-Q30](questions/3-deployment/cicd.md#t01-q30) [T12-Q29](questions/3-deployment/containers.md#t12-q29) |
| Застосунок після CodeDeploy отримує AccessDenied | Роль EC2-інстанса [T03-Q05](questions/3-deployment/iam.md#t03-q05) |
| Немає даних у Kinesis через 48 год | Retention 24 год [T10-Q01](questions/4-troubleshooting/kinesis.md#t10-q01) |
| Дублікати повідомлень SQS | Мала visibility timeout [T08-Q18](questions/1-development/messaging.md#t08-q18) |
| Повідомлення губляться після N спроб | DLQ [T12-Q36](questions/4-troubleshooting/messaging.md#t12-q36) |
| Застарілі дані в DynamoDB | `ConsistentRead=true` [T06-Q31](questions/1-development/network.md#t06-q31) |
| Застарілі дані в кеші | Немає інвалідації при оновленні [T05-Q48](questions/1-development/cache.md#t05-q48) |
| CloudFront віддає старий файл | Invalidation [T12-Q10](questions/1-development/s3.md#t12-q10) |
| Користувача «розлогінює» між сторінками | Немає sticky sessions / сесії в пам'яті інстанса [T07-Q14](questions/4-troubleshooting/network.md#t07-q14) [T03-Q46](questions/4-troubleshooting/network.md#t03-q46) |
| Немає internet на EC2 | Маршрут на IGW + NACL [T09-Q37](questions/4-troubleshooting/network.md#t09-q37) |
| Бакет «росте» сам | Access logs пишуться в той самий бакет [T09-Q53](questions/4-troubleshooting/s3.md#t09-q53) |

---

<a id="throttling"></a>
## 3. Throttling, retries, ідемпотентність
- **Exponential backoff + jitter** — універсальна відповідь на throttling будь-якого AWS API [T05-Q44](questions/4-troubleshooting/dynamodb.md#t05-q44) [T06-Q06](questions/4-troubleshooting/observability.md#t06-q06) [T09-Q48](questions/4-troubleshooting/messaging.md#t09-q48) [T10-Q24](questions/2-security/kms.md#t10-q24). AWS SDK робить це автоматично; без SDK — реалізуй сам.
- Зменш частоту запитів або батчі [T11-Q25](questions/4-troubleshooting/kinesis.md#t11-q25).
- Ідемпотентність: Lambda (async, ESM) і SQS standard можуть доставити подію **більше одного разу** → дедуплікація за ID (DynamoDB conditional put) [T11-Q20](questions/1-development/lambda.md#t11-q20), FIFO-дедуплікація [T04-Q56](questions/1-development/messaging.md#t04-q56).

---

<a id="optimization"></a>
## 4. Оптимізація

### Lambda
- CPU-bound → **більше пам'яті** [T07-Q32](questions/1-development/lambda.md#t07-q32) [T11-Q48](questions/4-troubleshooting/lambda.md#t11-q48). Cold start → **provisioned concurrency** (+ планове масштабування) [T01-Q49](questions/1-development/lambda.md#t01-q49) [T09-Q18](questions/4-troubleshooting/lambda.md#t09-q18), менший пакет, init поза handler [T03-Q01](questions/4-troubleshooting/lambda.md#t03-q01).
- Одна функція з'їдає concurrency інших → reserved concurrency [T12-Q53](questions/1-development/lambda.md#t12-q53).
- Масове опрацювання файлів → **асинхронні виклики паралельно** (`--invocation-type Event`) [T08-Q25](questions/1-development/lambda.md#t08-q25).

### Кешування (рівні)
CloudFront (edge) → API Gateway cache [T07-Q46](questions/1-development/apigw.md#t07-q46) → ElastiCache / DAX → read replicas. Обирай найближчий до користувача рівень, який задовольняє вимоги.

### Бази даних
- Читання → read replicas [T02-Q41](questions/1-development/cache.md#t02-q41) / ElastiCache [T04-Q18](questions/1-development/cache.md#t04-q18) / DAX [T04-Q09](questions/4-troubleshooting/dynamodb.md#t04-q09). З'єднання → RDS Proxy [T02-Q08](questions/4-troubleshooting/lambda.md#t02-q08) / `max_connections` [T02-Q53](questions/1-development/cache.md#t02-q53).
- DynamoDB: eventually consistent reads (вдвічі дешевше), global tables для глобальних користувачів [T12-Q58](questions/4-troubleshooting/dynamodb.md#t12-q58), Query замість Scan.

### Повідомлення
- SQS: long polling [T03-Q40](questions/4-troubleshooting/messaging.md#t03-q40), батчі (до 10), `MaxNumberOfMessages` [T05-Q29](questions/1-development/messaging.md#t05-q29). Kinesis: KPL-агрегація [T04-Q05](questions/1-development/kinesis.md#t04-q05), enhanced fan-out [T12-Q47](questions/2-security/kinesis.md#t12-q47).

### Мережа/доставка
- Статика з EC2 → S3 + CloudFront [T09-Q60](questions/4-troubleshooting/network.md#t09-q60). Глобальні користувачі → CloudFront / Route 53 latency routing [T06-Q09](questions/4-troubleshooting/edge.md#t06-q09) / DynamoDB global tables.
- Uploads здалеку → S3 Transfer Acceleration + multipart [T04-Q23](questions/1-development/s3.md#t04-q23) [T01-Q26](questions/1-development/s3.md#t01-q26).

### Масштабування
- ASG target tracking [T04-Q26](questions/2-security/network.md#t04-q26); для SQS — backlog per instance [T11-Q39](questions/4-troubleshooting/observability.md#t11-q39); на основі запитів за хвилину → custom metric + alarm [T03-Q38](questions/1-development/network.md#t03-q38).
- Вартість: Fargate (оплата за задачу), Lambda (оплата за використання), Reserved/Savings Plans для стабільних навантажень, AWS Budgets (для прогнозу потрібно ~5 тижнів даних) [T11-Q59](questions/1-development/other.md#t11-q59).

---

<a id="playbook"></a>
## 5. Плейбук: як мислити на питаннях Домену 4
1. **Спершу — права.** «Не пише логи / не бачить даних / AccessDenied» → IAM-роль (execution role, instance profile, task role, service role).
2. **Мережа.** «Timeout» → SG / NACL / маршрут / endpoint.
3. **Ліміти.** «Throttling / Exceeded / 429» → backoff, потім збільшення ресурсу (шарди, WCU, квота).
4. **Консистентність/кеш.** «Старі дані» → ConsistentRead, інвалідація, TTL.
5. **Інструмент.** «Хто зробив?» → CloudTrail. «Де повільно між сервісами?» → X-Ray. «Метрика/алерт?» → CloudWatch. «Чи доходить трафік?» → VPC Flow Logs.
