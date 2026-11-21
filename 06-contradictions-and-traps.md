# Протиріччя у відповідях і як їх уникати

> У 780 питаннях є пари, де **схожі запитання мають різні «правильні» відповіді**. Майже завжди причина одна: **різняться одне-два ключові слова в умові або набір варіантів**. Нижче кожне протиріччя розібрано окремо і подано правило, яке його знімає.

## Загальний метод (5 кроків на кожне питання)
1. **Знайди «обмежувач»** — слово у верхньому регістрі або в кінці речення: `MOST cost-effective`, `LEAST operational overhead`, `MOST secure`, `LOWEST latency`, `MINIMUM changes to code`, `fastest`. Саме воно визначає відповідь, а не тема питання.
2. **Знайди «технічний тригер»**: «real-time», «ordering», «exactly once», «> 15 min», «sub-millisecond», «rotate», «cross-account», «without code changes», «stale data acceptable».
3. **Відкинь варіанти, що порушують факт** (не той сервіс, вигаданий параметр, неіснуючий API).
4. **Серед решти обери найбільш «керований AWS» і найпростіший** варіант. На DVA managed-рішення майже завжди перемагає самописне.
5. **Дивись на набір варіантів:** правильна відповідь — найкраща **з тих, що запропоновані**. Якщо ідеального сервісу немає серед варіантів, обирай наступний за якістю (див. [c01](#c01), [c06](#c06)).

### Словник обмежувачів
| Фраза | Що зазвичай перемагає |
|---|---|
| LEAST operational overhead / MOST efficient | Managed / serverless, вбудована функція сервісу |
| MINIMUM code changes | Конфігурація замість коду (encryption at rest, SSE, ESM, env vars, `.ebextensions`) |
| MOST cost-effective | Без нових ресурсів (Rolling у EB), serverless (оплата за використання), long polling, TTL |
| MOST secure | IAM role + тимчасові credentials, least privilege (конкретний ARN), Secrets Manager |
| LOWEST latency | In-memory: ElastiCache / DAX / provisioned concurrency / CloudFront |
| Fault tolerant / not lost | Персистентне, Multi-AZ сховище поза інстансом |
| "Which is NOT…" | Уважно: шукаєш **хибне** твердження [T09-Q34](questions/1-development/dynamodb.md#t09-q34) [T09-Q47](questions/4-troubleshooting/network.md#t09-q47) [T09-Q44](questions/1-development/kinesis.md#t09-q44) [T11-Q42](questions/4-troubleshooting/observability.md#t11-q42) |

---

<a id="c01"></a>
## c01. Де зберігати сесії: ElastiCache, DynamoDB чи sticky sessions?
| Питання | Відповідь | Чому |
|---|---|---|
| [T02-Q12](questions/1-development/cache.md#t02-q12) «LOWEST latency», 10 000 rps | ElastiCache Redis | in-memory |
| [T02-Q18](questions/1-development/cache.md#t02-q18) [T10-Q53](questions/1-development/cache.md#t10-q53) «fault tolerant, natively highly scalable, interruption не впливає» | ElastiCache | Розподілений кеш з реплікацією (Redis) |
| [T02-Q57](questions/1-development/network.md#t02-q57) [T04-Q30](questions/1-development/cache.md#t04-q30) [T03-Q46](questions/4-troubleshooting/network.md#t03-q46) | ElastiCache | Сесії поза інстансом |
| [T02-Q43](questions/1-development/network.md#t02-q43) [T05-Q49](questions/1-development/cache.md#t05-q49) «щоб сесії не загубились при падінні EC2» | **DynamoDB** | Серед варіантів **немає ElastiCache**; DynamoDB довговічна |
| [T08-Q42](questions/1-development/network.md#t08-q42) «більше еластичності» | DynamoDB | Те саме |
| [T07-Q14](questions/4-troubleshooting/network.md#t07-q14) «користувачів розлогінює між сторінками — у чому причина?» | **Немає stickiness** | Питають **причину**, а не найкращу архітектуру |
| [T08-Q50](questions/4-troubleshooting/cache.md#t08-q50) Memcached для сесій + RDS для даних | ElastiCache Memcached | Немає вимог HA; правильну відповідь обрано серед поганих варіантів |

**Правило:** найкраща архітектура → **ElastiCache** (latency) або **DynamoDB** (durability, serverless). Якщо є обидва варіанти, і умова каже «lowest latency» → ElastiCache; «durable / serverless / data must not be lost» → DynamoDB. **Sticky sessions** — правильна відповідь лише на питання «чому мене розлогінює?» і **ніколи** на питання про відмовостійкість.

<a id="c02"></a>
## c02. DynamoDB Scan: Parallel Scan чи менший page size?
| Питання | Відповідь |
|---|---|
| [T02-Q35](questions/1-development/dynamodb.md#t02-q35) «MINIMIZE the impact на provisioned throughput» | **Менший page size (`Limit`)** |
| [T03-Q14](questions/4-troubleshooting/dynamodb.md#t03-q14) «improve performance of Scan» | **Parallel scans** |
| [T02-Q14](questions/1-development/dynamodb.md#t02-q14) [T02-Q60](questions/1-development/dynamodb.md#t02-q60) «найкоротший час + не зачепити прод» | **Parallel Scan + rate limiting** |
| [T10-Q21](questions/1-development/dynamodb.md#t10-q21) Select TWO | `Limit` + Parallel Scan |

**Правило:** «швидше» → parallel; «менше впливу» → Limit; «обидва» → обидва. **Parallel scan сам по собі збільшує навантаження** — він ніколи не є відповіддю на «зменшити вплив».

<a id="c03"></a>
## c03. Стратегії кешування
| Питання | Відповідь | Тригер |
|---|---|---|
| [T04-Q45](questions/1-development/cache.md#t04-q45) | Lazy loading | «кешувати лише запитане», «знизити вартість» |
| [T01-Q40](questions/4-troubleshooting/cache.md#t01-q40) | Lazy loading + **TTL** | «застарілі дані допустимі до хвилини» |
| [T01-Q28](questions/4-troubleshooting/cache.md#t01-q28) [T05-Q32](questions/4-troubleshooting/cache.md#t05-q32) [T06-Q25](questions/1-development/cache.md#t06-q25) | **Write-through** | «завжди актуальні», «real-time», «low latency для всіх items» |
| [T01-Q53](questions/1-development/cache.md#t01-q53) | Запис у backend → **інвалідація кешу** | «кеш ніколи не розходиться з backend» |
| [T06-Q43](questions/1-development/cache.md#t06-q43) | Наслідок write-through | Кеш великий і дорогий (кешується все, навіть непотрібне) |

**Правило:** актуальність → write-through; економія і стійкість до падіння кешу → lazy loading; компроміс → TTL.

<a id="c04"></a>
## c04. Redis чи Memcached?
Redis — коли є **HA / Multi-AZ / шифрування / персистентність / реплікація / sorted sets / geospatial / pub/sub** [T02-Q05](questions/1-development/cache.md#t02-q05) [T07-Q28](questions/1-development/cache.md#t07-q28) [T08-Q38](questions/1-development/cache.md#t08-q38) [T03-Q49](questions/1-development/kms.md#t03-q49) [T03-Q52](questions/4-troubleshooting/cache.md#t03-q52). Memcached — лише «простий кеш, багатопотоковість, без HA» [T08-Q50](questions/4-troubleshooting/cache.md#t08-q50).
⚠️ Твердження «Memcached не підтримує шифрування» (пояснення до [T02-Q05](questions/1-development/cache.md#t02-q05)) застаріле → [o08](07-outdated-and-wrong-answers.md#o08). На екзамені для «encryption + HA» все одно обирай **Redis**.

<a id="c05"></a>
## c05. AWS managed policy чи customer managed policy з ARN?
- [T02-Q21](questions/2-security/iam.md#t02-q21) «read-only до **конкретної** таблиці, **least privilege**» → **customer managed policy з ARN таблиці** (managed ReadOnly дає доступ до **всіх** таблиць).
- [T03-Q58](questions/2-security/iam.md#t03-q58) [T04-Q28](questions/2-security/iam.md#t04-q28) «MOST secure / best practice» для EC2 → **IAM role з `AmazonDynamoDBReadOnlyAccess`**. Тут вибір іде між **роллю і ключами**, а не між managed і customer policy.

**Правило:** якщо в умові є «least privilege» + конкретний ресурс → customer managed з ARN. Якщо ключове питання «як передати права інстансу/застосунку» → **роль** (тип політики вторинний).

<a id="c06"></a>
## c06. Secrets Manager чи Parameter Store?
- [T06-Q39](questions/2-security/secrets.md#t06-q39) ECS, credentials + API URL для dev/test/prod → **Secrets Manager** (Parameter Store **немає серед варіантів**).
- [T09-Q17](questions/2-security/secrets.md#t09-q17) практично те саме питання → **Parameter Store з ієрархічними шляхами** (Secrets Manager **немає серед варіантів**).
- [T03-Q10](questions/2-security/secrets.md#t03-q10) [T07-Q56](questions/2-security/beanstalk.md#t07-q56) шифрований рядок/конфігурація з аудитом → Parameter Store (SecureString) + CloudTrail.
- [T05-Q12](questions/2-security/secrets.md#t05-q12) [T05-Q55](questions/2-security/secrets.md#t05-q55) [T06-Q44](questions/2-security/secrets.md#t06-q44) [T10-Q61](questions/2-security/secrets.md#t10-q61) [T11-Q31](questions/2-security/secrets.md#t11-q31) **автоматична ротація** → Secrets Manager.

**Правило:** rotation, cross-account, «database credentials» → **Secrets Manager**. Конфігурація, ієрархія, «дешево» → **Parameter Store**. Якщо є лише один з двох — він і правильний (KMS сам по собі секрети не зберігає; S3 і змінні середовища — гірші варіанти).

<a id="c07"></a>
## c07. Політики деплою Elastic Beanstalk
| Тригер в умові | Політика | Питання |
|---|---|---|
| «dev», «швидко», «downtime OK» | All at once | [T03-Q12](questions/3-deployment/beanstalk.md#t03-q12) |
| «без додаткових витрат», допустимо зниження потужності | Rolling | [T11-Q57](questions/3-deployment/beanstalk.md#t11-q57) |
| «**повна потужність** + **існуючі інстанси**», «cost-effective без втрати продуктивності» | **Rolling with additional batch** | [T02-Q31](questions/2-security/iam.md#t02-q31) [T05-Q47](questions/3-deployment/beanstalk.md#t05-q47) [T11-Q15](questions/3-deployment/beanstalk.md#t11-q15) [T01-Q58](questions/3-deployment/beanstalk.md#t01-q58) |
| «**нові** інстанси», «швидкий rollback», «не можна, щоб невдалий апдейт поклав сайт» | **Immutable** | [T04-Q15](questions/3-deployment/beanstalk.md#t04-q15) [T06-Q49](questions/3-deployment/beanstalk.md#t06-q49) [T08-Q27](questions/3-deployment/beanstalk.md#t08-q27) [T09-Q13](questions/3-deployment/iam.md#t09-q13) |
| «нові інстанси» (Select TWO) | Immutable + Blue/Green | [T05-Q28](questions/3-deployment/beanstalk.md#t05-q28) |
| «оновити **платформу/runtime**», «повна ізоляція», «swap URL» | **Blue/Green** (окреме середовище, CNAME swap) | [T10-Q11](questions/3-deployment/beanstalk.md#t10-q11) [T12-Q19](questions/3-deployment/beanstalk.md#t12-q19) |

**Пастка:** [T09-Q13](questions/3-deployment/iam.md#t09-q13) «maintains at least FULL capacity + MINIMAL impact of failed deployment» → **Immutable**, а не Rolling with additional batch: при збої Immutable просто термінує нову ASG.

<a id="c08"></a>
## c08. Порядок hooks CodeDeploy
Банк дає «скорочені» ланцюжки, які виглядають несумісними:
- [T03-Q02](questions/3-deployment/cicd.md#t03-q02) `DownloadBundle → BeforeInstall → ApplicationStart → ValidateService`
- [T03-Q64](questions/3-deployment/cicd.md#t03-q64) `ApplicationStop → BeforeInstall → ApplicationStart → ValidateService`

Обидва правильні, бо це **підпослідовності** повного порядку: `ApplicationStop → DownloadBundle → BeforeInstall → Install → AfterInstall → ApplicationStart → ValidateService`. Перевіряй **відносний порядок**, а не повноту.
- Lambda: `BeforeAllowTraffic → AfterAllowTraffic` [T02-Q28](questions/3-deployment/cicd.md#t02-q28)
- ECS: `BeforeInstall → AfterInstall → AfterAllowTestTraffic → BeforeAllowTraffic → AfterAllowTraffic` [T10-Q65](questions/3-deployment/cicd.md#t10-q65)
- EC2 з `BeforeInstall…ValidateService` [T04-Q41](questions/3-deployment/cicd.md#t04-q41); варіант з `Block/AllowTraffic` без `Install` — неповний.
- [T02-Q58](questions/3-deployment/cicd.md#t02-q58) валідація ECS «до деплою» → `BeforeAllowTraffic` (у реальності для тестового трафіку є ще `AfterAllowTestTraffic`; обирай з наявних варіантів).

<a id="c09"></a>
## c09. Очищення DynamoDB: TTL чи видалення таблиці?
- **Вся таблиця** має бути порожньою до певного часу (щоночі 10 млн записів) → **delete table + create** [T03-Q24](questions/4-troubleshooting/dynamodb.md#t03-q24) [T04-Q08](questions/1-development/dynamodb.md#t04-q08): швидко, без витрат WCU.
- **Окремі** застарілі items (сесії, ставки за тиждень) → **TTL** [T04-Q55](questions/1-development/lambda.md#t04-q55) [T05-Q57](questions/1-development/network.md#t05-q57) [T08-Q08](questions/1-development/dynamodb.md#t08-q08) [T11-Q17](questions/1-development/dynamodb.md#t11-q17).

<a id="c10"></a>
## c10. Максимальний розмір повідомлення SQS
- [T03-Q53](questions/1-development/messaging.md#t03-q53) (новіший банк) → **1024 KiB = 1 MiB** ✅ (зміна 08.2025).
- [T07-Q40](questions/1-development/messaging.md#t07-q40) (старий банк) → «ліміт 256 KB → Extended Client» ⚠️ застаріло → [o01](07-outdated-and-wrong-answers.md#o01).
- [T04-Q32](questions/1-development/messaging.md#t04-q32) файл 1 GB → **S3 + SQS Extended Client** — актуально за будь-якого ліміту.

**Правило:** зараз ліміт **1 MiB**. Все, що більше → S3 + Extended Client. Якщо на екзамені бачиш варіант «256 KB» і немає варіанта «1 MiB» — питання старе, обирай Extended Client.

<a id="c11"></a>
## c11. KMS ThrottlingException — що обирати (Select TWO)
- [T01-Q42](questions/2-security/kms.md#t01-q42) → backoff + **data key caching** (Encryption SDK)
- [T05-Q03](questions/2-security/kms.md#t05-q03) → **підвищення квоти** + **LocalCryptoMaterialsCache**
- [T10-Q24](questions/2-security/kms.md#t10-q24) → **backoff** + **підвищення квоти**

**Правило:** правильні **три** речі: backoff/retry, кешування data keys, збільшення квоти. Обирай ті дві, що є серед варіантів. Неправильні: «перейти на SSE-S3», «створити більше ключів», «використати SQS».

<a id="c12"></a>
## c12. `Encrypt` напряму чи `GenerateDataKey`?
- Дані **≤ 4 KB** (дрібні файли по 1 KB) → можна **шифрувати напряму KMS-ключем** [T06-Q62](questions/2-security/kms.md#t06-q62).
- **> 4 KB** (1 MB, 1 GB, 111 GB) → **GenerateDataKey** (envelope) [T02-Q15](questions/2-security/kms.md#t02-q15) [T04-Q57](questions/2-security/kms.md#t04-q57) [T07-Q64](questions/1-development/kms.md#t07-q64) [T06-Q58](questions/2-security/kms.md#t06-q58) [T09-Q58](questions/2-security/kms.md#t09-q58).
- Пояснення [T11-Q55](questions/2-security/kms.md#t11-q55) («KMS отримує дані й шифрує») правдиве **лише для ≤ 4 KB**.

<a id="c13"></a>
## c13. Поступовий реліз: Lambda alias, API Gateway canary чи новий stage?
| Умова | Відповідь |
|---|---|
| Новий **API** (endpoints) на частину трафіку | API Gateway **canary release** [T01-Q21](questions/3-deployment/lambda.md#t01-q21) [T05-Q51](questions/3-deployment/apigw.md#t05-q51) |
| Нова **версія функції**, 10% трафіку, rollback | Lambda **alias із вагами** [T07-Q51](questions/1-development/lambda.md#t07-q51) [T08-Q13](questions/3-deployment/lambda.md#t08-q13) [T04-Q52](questions/3-deployment/lambda.md#t04-q52) [T11-Q63](questions/2-security/lambda.md#t11-q63) |
| Бета для **конкретної групи** (розробників), несумісні зміни | **Окремий stage** (+ нова версія Lambda) [T11-Q01](questions/1-development/apigw.md#t11-q01) [T10-Q52](questions/3-deployment/lambda.md#t10-q52) |
| Не змінювати конфігурацію API при кожному деплої | **Alias** = статичний ARN [T10-Q13](questions/3-deployment/lambda.md#t10-q13) [T06-Q03](questions/3-deployment/lambda.md#t06-q03) |
| Prod/test/dev на одній функції | Stage variables + aliases [T12-Q48](questions/1-development/lambda.md#t12-q48) |

<a id="c14"></a>
## c14. «CloudWatch Events» чи «EventBridge»?
Це **один сервіс**. Старі питання кажуть «CloudWatch Events rule» [T02-Q45](questions/3-deployment/lambda.md#t02-q45) [T10-Q19](questions/4-troubleshooting/observability.md#t10-q19), нові — «EventBridge rule» [T01-Q48](questions/4-troubleshooting/lambda.md#t01-q48) [T01-Q04](questions/1-development/lambda.md#t01-q04). Обирай будь-який з цих варіантів, але **не** «CloudWatch alarm» (alarm — про метрики, а не про події зміни стану) і **не** «тригер Lambda з консолі CodePipeline» (такого немає).

<a id="c15"></a>
## c15. Custom metric через `put-metric-data` чи CloudWatch agent?
- Свій **скрипт/cron** надсилає статус або RAM → **`put-metric-data` / PutMetricData** [T02-Q34](questions/4-troubleshooting/observability.md#t02-q34) [T03-Q06](questions/4-troubleshooting/observability.md#t03-q06) [T07-Q11](questions/4-troubleshooting/observability.md#t07-q11).
- **Системні метрики та логи з багатьох серверів / on-prem** → **CloudWatch agent** [T04-Q64](questions/4-troubleshooting/observability.md#t04-q64) [T05-Q21](questions/4-troubleshooting/observability.md#t05-q21) [T01-Q18](questions/4-troubleshooting/observability.md#t01-q18).
- [T02-Q34](questions/4-troubleshooting/observability.md#t02-q34) прямо каже «не потрібно ставити агента — досить CLI в cron».

<a id="c16"></a>
## c16. Логін в ECR
Новий спосіб: `aws ecr get-login-password | docker login …` [T04-Q13](questions/3-deployment/containers.md#t04-q13) [T05-Q24](questions/3-deployment/containers.md#t05-q24). Старий [T12-Q24](questions/3-deployment/containers.md#t12-q24) — `$(aws ecr get-login --no-include-email)` (CLI v1). На екзамені обирай варіант **get-login-password**; якщо є лише старий — він і правильний.

<a id="c17"></a>
## c17. Мінімальні права DynamoDB для «оновити item або створити, якщо його немає»
- [T09-Q12](questions/1-development/lambda.md#t09-q12) → `UpdateItem` + `GetItem` ✅ (UpdateItem сам створює item — upsert).
- [T08-Q63](questions/2-security/lambda.md#t08-q63) → `UpdateItem` + `GetItem` + `PutItem`. PutItem тут **зайвий**; ключ банку суперечить least privilege. Якщо в умові «MINIMUM permissions» → обирай без PutItem.

<a id="c18"></a>
## c18. Розрахунок concurrency Lambda — не протиріччя, а пастка
Множ rps на тривалість: 400 [T02-Q36](questions/1-development/lambda.md#t02-q36), 30 [T04-Q19](questions/1-development/lambda.md#t04-q19). Якщо результат **> 1000** (ліміт регіону за замовчуванням) → правильна відповідь «**звернутися в AWS Support / Service Quotas для збільшення ліміту**» [T05-Q11](questions/1-development/lambda.md#t05-q11) [T10-Q54](questions/1-development/lambda.md#t10-q54), а не «збільшити memory/timeout».

<a id="c19"></a>
## c19. X-Ray daemon в контейнерах
- **Fargate** → daemon як **sidecar** у тому ж task definition + task role з правами X-Ray [T01-Q11](questions/4-troubleshooting/containers.md#t01-q11).
- **ECS на EC2** → daemon-контейнер на кожному інстансі (daemon service) або sidecar [T06-Q65](questions/3-deployment/containers.md#t06-q65).
⚠️ Сьогодні це роблять через ADOT collector / CloudWatch agent → [o05](07-outdated-and-wrong-answers.md#o05).

<a id="c20"></a>
## c20. Що НЕ підходить для авторизації API Gateway / контролю доступу
- **STS** — не механізм авторизації API Gateway [T01-Q02](questions/2-security/apigw.md#t01-q02) [T09-Q51](questions/2-security/lambda.md#t09-q51). Підходять: IAM (SigV4), Cognito User Pools authorizer, Lambda authorizer, resource policies.
- [T07-Q06](questions/2-security/apigw.md#t07-q06) «заборонити іншим доменам → CORS»: на екзамені це правильний ключ, але CORS обмежує лише **браузери** → [d05](07-outdated-and-wrong-answers.md#d05).

<a id="c21"></a>
## c21. Як зберігати невдалі виклики Lambda
| Джерело | Механізм |
|---|---|
| Async-виклик (S3, SNS, `Event`) | **On-failure destination** (SQS/SNS/Lambda/EventBridge/S3) або **Lambda DLQ** (SQS/SNS) [T07-Q30](questions/1-development/lambda.md#t07-q30) |
| SQS → Lambda | **DLQ на черзі-джерелі** (redrive policy) [T08-Q09](questions/4-troubleshooting/messaging.md#t08-q09) |
| Kinesis/DynamoDB Streams → Lambda | **On-failure destination** ESM (SQS/SNS/S3) [T10-Q25](questions/4-troubleshooting/lambda.md#t10-q25) |
| Результат успішного виклику кудись далі | **On-success destination** [T10-Q51](questions/1-development/lambda.md#t10-q51) |

[T02-Q47](questions/4-troubleshooting/messaging.md#t02-q47) у банку пропонує «redrive policy на SQS + DLQ як event source» для async-виклику → сумнівно, див. [d02](07-outdated-and-wrong-answers.md#d02).

---

## Інші типові пастки (не протиріччя, але часто плутають)

<a id="c22"></a>
### c22. Cognito User Pool vs Identity Pool
Sign-up/sign-in, MFA, JWT, hosted UI, social login для **застосунку** → **User Pool** [T04-Q60](questions/2-security/cognito.md#t04-q60) [T05-Q27](questions/2-security/cognito.md#t05-q27) [T10-Q47](questions/2-security/cognito.md#t10-q47) [T11-Q26](questions/2-security/cognito.md#t11-q26). Тимчасові **AWS credentials** (прямий доступ до S3/DynamoDB), гості, SAML → **Identity Pool** [T03-Q23](questions/1-development/dynamodb.md#t03-q23) [T04-Q62](questions/2-security/s3.md#t04-q62) [T08-Q26](questions/2-security/iam.md#t08-q26) [T06-Q24](questions/2-security/cognito.md#t06-q24) [T02-Q26](questions/2-security/cognito.md#t02-q26). Обидва — «керування користувачами + доступ до AWS» [T05-Q17](questions/2-security/cognito.md#t05-q17).

<a id="c23"></a>
### c23. IP клієнта за балансером
ALB → заголовок **X-Forwarded-For** [T02-Q55](questions/4-troubleshooting/network.md#t02-q55) [T07-Q04](questions/1-development/network.md#t07-q04) [T08-Q60](questions/1-development/network.md#t08-q60) [T12-Q25](questions/4-troubleshooting/network.md#t12-q25). Потрібна IP **без XFF** + мільйони з'єднань → **NLB** [T03-Q43](questions/1-development/network.md#t03-q43). `X-Forwarded-Proto` — це протокол, а не IP [T02-Q55](questions/4-troubleshooting/network.md#t02-q55).

<a id="c24"></a>
### c24. Заголовки шифрування S3
`AES256` = SSE-S3 [T03-Q19](questions/2-security/s3.md#t03-q19); `aws:kms` = SSE-KMS [T03-Q09](questions/2-security/kms.md#t03-q09) [T11-Q29](questions/2-security/s3.md#t11-q29); SSE-C працює **лише по HTTPS** [T03-Q31](questions/2-security/kms.md#t03-q31). Розрізняй «enforce encryption» (Deny без заголовка) і «enforce HTTPS» (`aws:SecureTransport=false`) [T05-Q34](questions/2-security/s3.md#t05-q34) [T02-Q61](questions/2-security/s3.md#t02-q61).

<a id="c25"></a>
### c25. Multi-AZ vs Read Replica vs Aurora Replica
Multi-AZ = **HA**, з standby не читають. Read Replica = **масштабування читань** (async, окремий endpoint) [T02-Q41](questions/1-development/cache.md#t02-q41) [T11-Q40](questions/1-development/cache.md#t11-q40). DR → cross-region replica + бекапи [T07-Q19](questions/4-troubleshooting/cache.md#t07-q19). Aurora Replicas у тому ж кластері + binlog в інший кластер [T02-Q62](questions/4-troubleshooting/cache.md#t02-q62).

<a id="c26"></a>
### c26. SQS vs SNS vs Kinesis vs EventBridge
- Одне повідомлення — одному обробнику, буфер → **SQS**
- Одне повідомлення — багатьом (push) → **SNS** (+ SQS = fan-out)
- Потік, **порядок**, replay, кілька незалежних читачів, real-time аналітика → **Kinesis** [T07-Q36](questions/1-development/kinesis.md#t07-q36) [T07-Q20](questions/1-development/observability.md#t07-q20)
- Події від AWS-сервісів / SaaS, розклади, маршрутизація за вмістом → **EventBridge**

<a id="c27"></a>
### c27. CloudFront signed URL vs S3 pre-signed URL
Контент роздається **через CloudFront** (глобально) → **CloudFront signed URL/cookies** [T09-Q01](questions/2-security/edge.md#t09-q01) [T04-Q29](questions/2-security/s3.md#t04-q29). Прямий тимчасовий доступ до приватного об'єкта S3 → **S3 pre-signed URL** [T04-Q10](questions/1-development/s3.md#t04-q10) [T09-Q43](questions/4-troubleshooting/s3.md#t09-q43).

<a id="c28"></a>
### c28. Elastic IP vs Route 53 Alias/CNAME
Single EC2 + DNS → **Elastic IP** в A-записі [T01-Q14](questions/4-troubleshooting/edge.md#t01-q14) [T05-Q58](questions/1-development/network.md#t05-q58). Чужий домен → **CNAME** [T11-Q02](questions/1-development/edge.md#t11-q02). AWS-ресурс (ELB, CloudFront, API GW) → **Alias**.
