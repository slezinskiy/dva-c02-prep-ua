# Домен 3 — Розгортання (24%)

> Що перевіряє: підготовку артефактів, IaC (CloudFormation / SAM / CDK), CI/CD (Code*), стратегії деплою (Elastic Beanstalk, CodeDeploy, Lambda, API Gateway, ECS).

**Зміст:** [Стратегії деплою (зведена таблиця)](#strategies) · [CI/CD: CodeCommit / CodeBuild / CodeDeploy / CodePipeline](#cicd) · [CloudFormation / SAM / CDK](#iac) · [Elastic Beanstalk](#beanstalk) · [ECS / ECR / Fargate](#containers) · [Amplify / AppConfig / інше](#misc)

---

<a id="strategies"></a>
## 1. Стратегії деплою — головна таблиця

| Де | Стратегія | Що робить | Ключові слова питання |
|---|---|---|---|
| **Elastic Beanstalk** | All at once | Всі інстанси одночасно, **downtime** | «dev, швидко, downtime OK» [T03-Q12](questions/3-deployment/beanstalk.md#t03-q12) |
| | Rolling | Батчами на **існуючих** інстансах; потужність падає; **без доплат** | «без додаткових витрат» [T11-Q57](questions/3-deployment/beanstalk.md#t11-q57) |
| | **Rolling with additional batch** | Спершу додатковий батч → **повна потужність**, далі існуючі інстанси | «повна потужність + існуючі інстанси / дешево» [T02-Q31](questions/2-security/iam.md#t02-q31) [T05-Q47](questions/3-deployment/beanstalk.md#t05-q47) [T11-Q15](questions/3-deployment/beanstalk.md#t11-q15) [T01-Q58](questions/3-deployment/beanstalk.md#t01-q58) |
| | **Immutable** | Нова тимчасова ASG з новими інстансами; **найшвидший rollback** (термінувати нові) | «нові інстанси, zero downtime, quick rollback» [T04-Q15](questions/3-deployment/beanstalk.md#t04-q15) [T06-Q49](questions/3-deployment/beanstalk.md#t06-q49) [T08-Q27](questions/3-deployment/beanstalk.md#t08-q27) [T09-Q13](questions/3-deployment/iam.md#t09-q13) [T05-Q28](questions/3-deployment/beanstalk.md#t05-q28) |
| | Traffic splitting | Canary: % трафіку на нові інстанси | |
| | **Blue/Green** | Окреме середовище + **swap CNAME** (або Route 53) | «зміна платформи/версії runtime», «повна ізоляція» [T10-Q11](questions/3-deployment/beanstalk.md#t10-q11) [T12-Q19](questions/3-deployment/beanstalk.md#t12-q19) |
| **CodeDeploy EC2/on-prem** | In-place | Зупинка → оновлення на тих самих інстансах (з LB — по одному) | |
| | Blue/green | Нові інстанси (ASG copy), перемикання LB [T03-Q03](questions/3-deployment/network.md#t03-q03) [T07-Q55](questions/3-deployment/network.md#t07-q55) | |
| **CodeDeploy Lambda** | Canary / Linear / AllAtOnce | `LambdaCanary10Percent5Minutes`, `LambdaLinear10PercentEvery10Minutes` | [T04-Q11](questions/3-deployment/lambda.md#t04-q11) [T08-Q65](questions/3-deployment/lambda.md#t08-q65) |
| **CodeDeploy ECS** | **Лише blue/green** (з canary/linear/all-at-once) | `ECSCanary10Percent15Minutes` | [T02-Q11](questions/3-deployment/containers.md#t02-q11) [T05-Q56](questions/3-deployment/containers.md#t05-q56) [T08-Q35](questions/3-deployment/cicd.md#t08-q35) |
| **Lambda без CodeDeploy** | Weighted alias | % трафіку на нову версію | [T07-Q51](questions/1-development/lambda.md#t07-q51) [T08-Q13](questions/3-deployment/lambda.md#t08-q13) |
| **API Gateway** | Canary release на stage / новий stage | % трафіку / бета-доступ | [T01-Q21](questions/3-deployment/lambda.md#t01-q21) [T05-Q51](questions/3-deployment/apigw.md#t05-q51) [T11-Q01](questions/1-development/apigw.md#t11-q01) |
| **ECS (без CodeDeploy)** | Rolling update | `minimumHealthyPercent` / `maximumPercent` | |

> ⚠️ Immutable та traffic splitting створюють **нові** інстанси → накопичені **burst credits** T-інстансів зникають [T11-Q33](questions/3-deployment/beanstalk.md#t11-q33).
> Rolling: якщо деплой упав, а невдалі інстанси термінували, EB замінить їх інстансами з **останньою успішною версією** [T11-Q23](questions/3-deployment/beanstalk.md#t11-q23).

---

<a id="cicd"></a>
## 2. CI/CD сервіси

### CodeCommit
- Керовані приватні Git-репозиторії, шифрування at rest (KMS) + in transit — **автоматично** [T03-Q51](questions/2-security/kms.md#t03-q51) [T06-Q01](questions/3-deployment/cicd.md#t06-q01). Був закритий для нових клієнтів з 07.2024, **повернувся в GA 11.2025** → [o02](07-outdated-and-wrong-answers.md#o02).
- Доступ: **HTTPS → Git credentials, згенеровані в IAM** (або AWS CLI credential helper з access keys) [T02-Q56](questions/3-deployment/cicd.md#t02-q56) [T03-Q15](questions/2-security/cicd.md#t03-q15) [T06-Q38](questions/3-deployment/cicd.md#t06-q38); **SSH → завантаж публічний SSH-ключ в IAM-користувача** [T10-Q09](questions/3-deployment/cicd.md#t10-q09). Логін/пароль IAM-користувача **не підтримується** [T11-Q10](questions/3-deployment/cicd.md#t11-q10).
- Програмно: SDK `PutFile` / `CreateCommit` [T05-Q04](questions/3-deployment/cicd.md#t05-q04) [T10-Q57](questions/3-deployment/cicd.md#t10-q57). Права на гілки: `codecommit:CreateBranch`, `DeleteBranch` [T10-Q59](questions/3-deployment/cicd.md#t10-q59).
- Тригери на зміни → **EventBridge rules** (або CodeCommit triggers на SNS/Lambda) [T12-Q15](questions/4-troubleshooting/cicd.md#t12-q15).

### CodeBuild
- `buildspec.yml` **у корені** репозиторію [T12-Q51](questions/3-deployment/cicd.md#t12-q51). Фази: `install` → `pre_build` → `build` → `post_build`; секції `env`, `artifacts`, `cache`, `reports`.
- Пуш образу в ECR **лише при успішній збірці** → у `post_build` **`commands`** (блок `finally` виконується навіть після помилки) [T02-Q51](questions/3-deployment/containers.md#t02-q51).
- **Кешування залежностей в S3** (або local cache) [T03-Q08](questions/4-troubleshooting/cicd.md#t03-q08). **Timeouts** від завислих збірок [T11-Q46](questions/3-deployment/cicd.md#t11-q46). Масштабується автоматично, паралельні збірки «з коробки» [T07-Q05](questions/3-deployment/cicd.md#t07-q05).
- Помилки доступу (не тягне Docker-образ, не пушить в ECR, не пише в S3) → **IAM service role CodeBuild** [T01-Q30](questions/3-deployment/cicd.md#t01-q30) [T03-Q28](questions/3-deployment/cicd.md#t03-q28) [T12-Q29](questions/3-deployment/containers.md#t12-q29).
- Локальний дебаг → **CodeBuild local agent** [T12-Q09](questions/4-troubleshooting/cicd.md#t12-q09). Логи → CloudWatch Logs та/або S3 (аналіз в Athena) [T09-Q63](questions/1-development/observability.md#t09-q63). Артефакти шифруються KMS [T01-Q07](questions/2-security/cicd.md#t01-q07).

### CodeDeploy
- Платформи: **EC2/on-premises** (потрібен **CodeDeploy agent**) [T08-Q04](questions/3-deployment/cicd.md#t08-q04) [T09-Q16](questions/3-deployment/cicd.md#t09-q16), **Lambda**, **ECS**. Кількість ревізій на інстансі — параметр агента `:max_revisions:` [T07-Q13](questions/3-deployment/cicd.md#t07-q13).
- **`appspec.yml` у корені** бандла [T09-Q59](questions/3-deployment/cicd.md#t09-q59) [T04-Q43](questions/3-deployment/cicd.md#t04-q43): секції `files`, `permissions`, `hooks`.
- **Порядок hooks EC2 (in-place):** `ApplicationStop → DownloadBundle → BeforeInstall → Install → AfterInstall → ApplicationStart → ValidateService` (з LB додаються `BeforeBlockTraffic/BlockTraffic/AfterBlockTraffic` до і `BeforeAllowTraffic/AllowTraffic/AfterAllowTraffic` після) [T03-Q02](questions/3-deployment/cicd.md#t03-q02) [T03-Q64](questions/3-deployment/cicd.md#t03-q64) [T04-Q41](questions/3-deployment/cicd.md#t04-q41) [T09-Q65](questions/4-troubleshooting/cicd.md#t09-q65).
- **Lambda:** `BeforeAllowTraffic → AfterAllowTraffic` [T02-Q28](questions/3-deployment/cicd.md#t02-q28); resources: `name, alias, currentversion, targetversion` [T05-Q31](questions/3-deployment/cicd.md#t05-q31).
- **ECS:** `BeforeInstall → AfterInstall → AfterAllowTestTraffic → BeforeAllowTraffic → AfterAllowTraffic` [T10-Q65](questions/3-deployment/cicd.md#t10-q65); test listener на ALB; валідація **до** продакшн-трафіку [T02-Q58](questions/3-deployment/cicd.md#t02-q58) → [c08](06-contradictions-and-traps.md#c08).
- **Deployment groups** — набори цілей (теги, ASG) для різних середовищ або етапів [T12-Q59](questions/3-deployment/cicd.md#t12-q59).
- Rollback (автоматичний чи ручний) = **новий деплой останньої робочої ревізії з новим deployment ID** [T03-Q59](questions/3-deployment/cicd.md#t03-q59).
- Помилки авторизації після деплою (застосунок не має доступу до S3) → **роль EC2-інстанса**, не CodeDeploy [T03-Q05](questions/3-deployment/iam.md#t03-q05).
- Деплой стороннього пакета → **зааплоадь бандл в S3 (або GitHub)** і вкажи його в деплої [T02-Q39](questions/3-deployment/s3.md#t02-q39).

### CodePipeline
- Оркестрація source → build → test → deploy [T06-Q45](questions/3-deployment/cicd.md#t06-q45). Джерела: CodeCommit, S3, GitHub, ECR, Bitbucket; старт за подією (EventBridge) при push/оновленні об'єкта в S3 [T03-Q30](questions/3-deployment/s3.md#t03-q30).
- **Manual approval action** перед продом [T07-Q15](questions/3-deployment/cicd.md#t07-q15) [T08-Q39](questions/3-deployment/containers.md#t08-q39).
- Сповіщення про зміну стану stage/action → **EventBridge rule** (CodePipeline як джерело подій) → Lambda/SNS [T01-Q48](questions/4-troubleshooting/lambda.md#t01-q48) [T02-Q45](questions/3-deployment/lambda.md#t02-q45).
- Хто змінив конфігурацію pipeline → **CloudTrail** [T12-Q52](questions/4-troubleshooting/cicd.md#t12-q52).

---

<a id="iac"></a>
## 3. CloudFormation, SAM, CDK

### CloudFormation
- Секції шаблону: `AWSTemplateFormatVersion`, `Description`, `Metadata`, **`Parameters`**, `Rules`, **`Mappings`**, **`Conditions`**, `Transform`, **`Resources` (єдина обов'язкова)**, **`Outputs`**. Секції `Dependencies` **не існує** [T11-Q49](questions/3-deployment/iac.md#t11-q49).
- Conditions застосовуються до Resources, Outputs і властивостей ресурсів, **але не до Parameters** [T11-Q19](questions/3-deployment/iac.md#t11-q19).
- Параметри: типи `String`, `Number`, `List<Number>`, `CommaDelimitedList`, `AWS::EC2::KeyPair::KeyName`, `AWS::SSM::Parameter::Value<...>`… (`DependentParameter` — немає такого) [T09-Q54](questions/4-troubleshooting/iac.md#t09-q54); `AllowedValues` — список дозволених значень [T07-Q50](questions/3-deployment/iac.md#t07-q50).
- **Pseudo parameters:** `AWS::Region` [T03-Q04](questions/3-deployment/iac.md#t03-q04) [T06-Q63](questions/3-deployment/iac.md#t06-q63), `AWS::AccountId` [T11-Q58](questions/3-deployment/iac.md#t11-q58), `AWS::StackName`, `AWS::Partition`, `AWS::NoValue`.
- Intrinsic functions: `!Ref`, **`!GetAtt`** (атрибут ресурсу: endpoint RDS) [T12-Q07](questions/1-development/iac.md#t12-q07), **`!FindInMap [Map, TopKey, SecondKey]`** (AMI за регіоном) [T11-Q30](questions/1-development/iac.md#t11-q30), `!Sub`, `!Join`, `!Select`, `!If`, **`!ImportValue`**.
- **Cross-stack reference:** `Outputs` → `Export: Name` → в іншому стеку `!ImportValue` [T01-Q60](questions/1-development/iac.md#t01-q60) [T03-Q44](questions/3-deployment/iac.md#t03-q44) [T12-Q60](questions/3-deployment/iac.md#t12-q60). Імена exports **унікальні в межах регіону** [T11-Q21](questions/3-deployment/iac.md#t11-q21). Стек, з якого імпортують, не видалиш, доки є імпортери → видаляй спершу залежні [T09-Q52](questions/1-development/iac.md#t09-q52).
- **Change sets** — подивитися зміни перед застосуванням [T10-Q49](questions/3-deployment/iac.md#t10-q49). **StackSets** — один шаблон у кілька акаунтів/регіонів [T11-Q22](questions/3-deployment/iac.md#t11-q22). Шаблони зберігаються в **S3** [T12-Q28](questions/3-deployment/iac.md#t12-q28).
- **AMI — регіональний ресурс** → копіювати AMI в інший регіон і використати новий ID (Mappings) [T02-Q01](questions/3-deployment/iac.md#t02-q01).
- `DELETE_FAILED` (ресурс не видаляється) → `DeletionPolicy: Retain` / пропустити ресурс і видалити вручну [T06-Q21](questions/3-deployment/iac.md#t06-q21).
- Dynamic references до секретів: `{{resolve:secretsmanager:...}}` [T06-Q50](questions/3-deployment/iac.md#t06-q50).
- `aws cloudformation package` (вивантажує локальний код в S3 і переписує шаблон) → `aws cloudformation deploy` [T03-Q37](questions/3-deployment/iac.md#t03-q37) [T05-Q23](questions/3-deployment/iac.md#t05-q23).

### SAM
- **`Transform: AWS::Serverless-2016-10-31`** — обов'язкова секція, вона й робить шаблон SAM-шаблоном [T05-Q33](questions/3-deployment/iac.md#t05-q33) [T09-Q11](questions/3-deployment/iac.md#t09-q11) [T09-Q30](questions/1-development/iac.md#t09-q30) [T11-Q28](questions/1-development/iac.md#t11-q28).
- Ресурси: `AWS::Serverless::Function`, `::Api`, `::HttpApi`, `::SimpleTable`, `::LayerVersion`, `::Application`, `::StateMachine`, `::Connector`, `::GraphQLApi`. **`::UserPool` не існує** [T05-Q65](questions/3-deployment/iac.md#t05-q65) [T08-Q30](questions/3-deployment/iac.md#t08-q30) [T11-Q18](questions/3-deployment/iac.md#t11-q18).
- Policy templates: `S3ReadPolicy`, `DynamoDBCrudPolicy`, `SQSPollerPolicy`… [T01-Q61](questions/2-security/s3.md#t01-q61).
- CLI: `sam init` (quick-start шаблони) [T10-Q15](questions/3-deployment/iac.md#t10-q15) → `sam build` → `sam local invoke` / `sam local start-api` (локальне тестування, працює і з CDK) [T06-Q52](questions/3-deployment/iac.md#t06-q52) [T10-Q32](questions/3-deployment/iac.md#t10-q32) → `sam package` + `sam deploy` (= `aws cloudformation package/deploy`) [T04-Q06](questions/3-deployment/iac.md#t04-q06) [T08-Q12](questions/3-deployment/iac.md#t08-q12). **`sam sync`** — швидка синхронізація змін у хмару під час розробки [T09-Q08](questions/4-troubleshooting/iac.md#t09-q08).
- Порядок: написати шаблон → **запакувати (код у S3)** → задеплоїти [T01-Q37](questions/1-development/iac.md#t01-q37).
- `DeploymentPreference` у SAM → CodeDeploy canary/linear для Lambda + alarms + hooks [T08-Q65](questions/3-deployment/lambda.md#t08-q65).
- **SAR (Serverless Application Repository)** — готові serverless-застосунки [T11-Q03](questions/4-troubleshooting/iac.md#t11-q03). SAM і Elastic Beanstalk працюють поверх CloudFormation [T07-Q41](questions/3-deployment/iac.md#t07-q41).

### CDK
- IaC звичайними мовами (TypeScript, Python, Java, C#, Go) [T11-Q36](questions/3-deployment/iac.md#t11-q36). Порядок: `cdk init` (app з шаблону) → код стеків → build → **`cdk synth`** (генерує CloudFormation) → `cdk deploy` [T11-Q61](questions/3-deployment/iac.md#t11-q61). `cdk diff`, `cdk bootstrap`.
- Відкат — через CloudFormation (stack rollback) або повторний деплой попередньої версії коду [T06-Q51](questions/3-deployment/iac.md#t06-q51) → [d06](07-outdated-and-wrong-answers.md#d06).

---

<a id="beanstalk"></a>
## 4. AWS Elastic Beanstalk
- PaaS: вантажиш код, а EB сам керує EC2, ASG, ELB, моніторингом [T02-Q03](questions/3-deployment/beanstalk.md#t02-q03) [T11-Q47](questions/3-deployment/beanstalk.md#t11-q47).
- **Web server environment** vs **Worker environment** (читає SQS; періодичні задачі — **`cron.yaml`**) [T01-Q06](questions/1-development/beanstalk.md#t01-q06) [T07-Q27](questions/4-troubleshooting/messaging.md#t07-q27).
- Конфігурація: **`.ebextensions/*.config`** (YAML/JSON) у корені бандла [T01-Q44](questions/1-development/beanstalk.md#t01-q44) [T08-Q33](questions/3-deployment/beanstalk.md#t08-q33) [T09-Q09](questions/3-deployment/beanstalk.md#t09-q09) [T11-Q04](questions/1-development/beanstalk.md#t11-q04); HTTPS на LB, health check type ELB для ASG [T12-Q62](questions/4-troubleshooting/network.md#t12-q62); ресурси (ElastiCache) у `.ebextensions` [T11-Q64](questions/3-deployment/cache.md#t11-q64).
- **RDS всередині EB видаляється разом із середовищем** → для проду створюй RDS **окремо** і передавай endpoint через змінні середовища [T11-Q64](questions/3-deployment/cache.md#t11-q64); для тестового середовища — snapshot перед видаленням [T01-Q62](questions/3-deployment/beanstalk.md#t01-q62).
- Source bundle: **один ZIP або WAR**, **без кореневої папки**, ≤ 500 MB (у банку — 512 MB) [T02-Q50](questions/3-deployment/beanstalk.md#t02-q50).
- **Application version lifecycle policy** — автоматично прибирає старі версії (ліміт кількості версій) [T01-Q08](questions/3-deployment/beanstalk.md#t01-q08).
- Перенести середовище в інший акаунт → **saved configuration** [T12-Q05](questions/1-development/s3.md#t12-q05).
- Окремі середовища для dev і load test [T03-Q35](questions/1-development/beanstalk.md#t03-q35). Docker: single container / ECS-платформа для кількох контейнерів [T01-Q65](questions/3-deployment/containers.md#t01-q65).
- HTTPS: сертифікат на LB + порти 80/443 + редирект HTTP→HTTPS на інстансах [T01-Q55](questions/2-security/kms.md#t01-q55).

---

<a id="containers"></a>
## 5. ECS / ECR / Fargate (у DVA-C03 тема посилюється!)

### Основні поняття
- **Task definition** (JSON) — образ, CPU/пам'ять, **port mappings** [T04-Q36](questions/1-development/containers.md#t04-q36), **env vars** (`environment`, а секрети — `secrets` з посиланням на Secrets Manager/SSM) [T02-Q49](questions/1-development/containers.md#t02-q49), volumes, **task role**, `logConfiguration` (**`awslogs`** → CloudWatch Logs) [T12-Q16](questions/1-development/containers.md#t12-q16).
- **Контейнери, які ділять дані/ресурси (sidecar для логів і метрик) → один task definition + спільний volume** [T02-Q13](questions/4-troubleshooting/containers.md#t02-q13) [T12-Q03](questions/3-deployment/containers.md#t12-q03).
- **Service** — підтримує задану кількість задач (`--desired-count`) і інтегрується з ALB [T01-Q17](questions/3-deployment/containers.md#t01-q17) [T09-Q26](questions/3-deployment/containers.md#t09-q26).
- **Launch types:** EC2 (керуєш інстансами, оплата за інстанси) vs **Fargate** (serverless, **оплата за задачу**) [T02-Q22](questions/1-development/containers.md#t02-q22) [T07-Q21](questions/1-development/containers.md#t07-q21). Спільне сховище між AZ для Fargate → **EFS** [T11-Q11](questions/1-development/containers.md#t11-q11).
- **Dynamic port mapping:** `hostPort: 0` + ALB → кілька однакових контейнерів на одному хості [T02-Q23](questions/1-development/containers.md#t02-q23).
- Агент ECS: `/etc/ecs/ecs.config` → `ECS_CLUSTER=назва` (інакше інстанс потрапить у `default`) [T07-Q07](questions/4-troubleshooting/iac.md#t07-q07), `ECS_ENABLE_TASK_IAM_ROLE=true` [T12-Q46](questions/3-deployment/containers.md#t12-q46). Термінований у стані STOPPED інстанс може «висіти» в кластері [T07-Q37](questions/4-troubleshooting/containers.md#t07-q37).

### Розміщення задач (лише EC2 launch type)
| Strategy | Ефект |
|---|---|
| **binpack** (cpu/memory) | Мінімум інстансів, економія [T02-Q30](questions/1-development/containers.md#t02-q30) [T04-Q03](questions/1-development/containers.md#t04-q03) |
| **spread** (`attribute:ecs.availability-zone`, `instanceId`) | Рівномірно по AZ, потім по інстансах [T02-Q19](questions/1-development/containers.md#t02-q19) |
| **random** | Найменше налаштувань [T10-Q06](questions/3-deployment/containers.md#t10-q06) |

| Constraint | Ефект |
|---|---|
| **distinctInstance** | Кожна задача на окремому інстансі [T08-Q05](questions/1-development/containers.md#t08-q05) |
| **memberOf** (cluster query language) | `attribute:ecs.instance-type =~ t2.*` [T04-Q07](questions/1-development/containers.md#t04-q07), `task:group == databases` [T08-Q22](questions/1-development/containers.md#t08-q22) [T10-Q55](questions/1-development/containers.md#t10-q55) |

### ECR
- Логін: **`aws ecr get-login-password | docker login --username AWS --password-stdin <registry>`**, далі `docker pull <registry>/<repo>:<tag>` [T04-Q13](questions/3-deployment/containers.md#t04-q13) [T05-Q24](questions/3-deployment/containers.md#t05-q24). Старий `aws ecr get-login` — лише AWS CLI v1 [T12-Q24](questions/3-deployment/containers.md#t12-q24).
- Lifecycle policies (прибирання старих образів), image scanning, теги — у DVA-C03 окремий skill 3.1.2.
- Lambda з container image — образ в ECR; multi-arch образи Lambda не підтримує [T09-Q24](questions/3-deployment/containers.md#t09-q24).

### Інше
- **AWS Copilot** — CLI для швидкого запуску застосунків на ECS/Fargate [T06-Q35](questions/1-development/containers.md#t06-q35).
- X-Ray на Fargate → **sidecar-контейнер з daemon/ADOT collector** + IAM task role [T01-Q11](questions/4-troubleshooting/containers.md#t01-q11).
- Автоскейлінг за користувацьким навантаженням → high-resolution custom metric [T10-Q34](questions/4-troubleshooting/containers.md#t10-q34).

---

<a id="misc"></a>
## 6. Amplify, AppConfig, середовища
- **AWS Amplify Hosting** — фронтенд і статичні сайти з Git: гілка = середовище, деплой при merge, HTTPS [T05-Q30](questions/3-deployment/cicd.md#t05-q30); тестові фічі → підключити **нову гілку** (окремий URL) [T06-Q23](questions/3-deployment/cicd.md#t06-q23).
- **AWS AppConfig** — фіче-флаги та динамічна конфігурація з валідацією і поступовим розгортанням; шифрування AWS owned keys + опційно customer managed key [T06-Q10](questions/2-security/kms.md#t06-q10).
- **Cloud9** (браузерна IDE) і **CodeStar** закриті/припинені → [o03](07-outdated-and-wrong-answers.md#o03).
- Різні середовища в API Gateway → **stages + stage variables** [T05-Q18](questions/3-deployment/apigw.md#t05-q18) [T12-Q22](questions/3-deployment/apigw.md#t12-q22); у Lambda → **aliases** [T12-Q48](questions/1-development/lambda.md#t12-q48).
- **MOCK-інтеграції** API Gateway для паралельної розробки [T07-Q49](questions/3-deployment/other.md#t07-q49).
