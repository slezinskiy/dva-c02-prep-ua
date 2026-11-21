# DVA-C03: що змінюється з 1 грудня 2026

> ⚠️ **Останній день здачі DVA-C02 — 30.11.2026.** З **01.12.2026** проводиться лише **DVA-C03**. Формат той самий: 65 питань (50 оцінюються), 130 хв, прохідний бал 720/1000, назва сертифіката та ж.
> **Якщо встигаєш здати до 30.11** — цей файл можна пропустити. Якщо екзамен пізніше — це обов'язковий додаток до файлів 01–08: ядро (Lambda, DynamoDB, IAM, KMS, CI/CD) лишається, але додаються AI-теми та контейнери.

## 1. Ваги доменів
| Домен | DVA-C02 | DVA-C03 |
|---|---|---|
| 1. Development with AWS Services | 32% | **30%** |
| 2. Security | 26% | 26% (+ нова задача 2.3 про AI) |
| 3. Deployment → **Testing and Deployment** | 24% | **22%** |
| 4. Troubleshooting and Optimization | 18% | **22%** ↑ |

**Висновок:** Домен 4 (observability, оптимізація) важить більше — посилено вчи [04-domain4-troubleshooting.md](04-domain4-troubleshooting.md).

## 2. Нові або посилені навички (з офіційного exam guide)

### Домен 1
- **1.1.2** sync vs async патерни (API GW sync, SQS async, EventBridge event-driven), **1.1.3** retry з backoff, graceful degradation, **1.1.8** event-driven (EventBridge, S3 Event Notifications, SQS, SNS) — вже покрито у [01](01-domain1-development.md#patterns).
- **1.1.9** AI-асистовані інструменти розробки: генерація коду за специфікацією (spec-driven), автоматичне code review, автодоповнення, рефакторинг, security scanning → **Kiro** (AI IDE зі spec-driven підходом).
- **1.1.10** інтеграція **керованих AI-сервісів** у застосунок → **Amazon Bedrock** (`InvokeModel`, `Converse` API через SDK).
- **1.2.2** конфігурація Lambda тепер явно включає **extensions**.
- **1.3.4** DynamoDB зведено в одну навичку (high-cardinality ключі, consistency, query/scan, індекси).

### Домен 2 — нова задача 2.3: безпека AI-сервісів
| Навичка | Що знати |
|---|---|
| 2.3.1 Доступ до AI-сервісів | IAM-політики на `bedrock:InvokeModel*` з ARN конкретних моделей; SCP для заборони моделей в організації; least privilege для ролей застосунку/агента |
| 2.3.2 Приватність даних | **VPC interface endpoint (PrivateLink)** для Bedrock (трафік не йде в інтернет); Bedrock **не використовує** prompts/відповіді для навчання моделей і не передає їх провайдерам моделей; шифрування KMS |
| 2.3.3 Фільтрація входів/виходів | **Amazon Bedrock Guardrails**: content filters, **denied topics**, word filters, **sensitive information filters (PII/PHI — mask або block)**, **prompt attack (injection) filter**, contextual grounding check; застосовується до вхідних і вихідних даних моделі |
| 2.3.4 Безпека AI-агентів | **Tool-use authorization** (агент викликає лише дозволені інструменти з мінімальними правами), **ізоляція сесій** (Bedrock AgentCore Runtime — окреме середовище на сесію), **human-in-the-loop** — підтвердження користувачем чутливих дій (return of control / user confirmation в Bedrock Agents), AgentCore Identity для делегованих OAuth-доступів |
| 2.3.5 Чутливі дані в логах | Bedrock **model invocation logging** (CloudWatch Logs / S3) + **CloudWatch Logs data protection** (маскування PII), обмеження доступу до log groups, KMS-шифрування логів |

- **2.1.4** presigned URLs (**S3 і CloudFront**) — тепер окрема навичка → [c27](06-contradictions-and-traps.md#c27).
- **2.2.2** сертифікати: **ACM і AWS Private CA** (обидва тепер у scope) → [02](02-domain2-security.md#certs).
- **2.2.5** генерація сертифікатів і SSH-ключів для розробки; **2.2.6** маскування PII/PHI (Macie, CloudWatch Logs data protection, Guardrails).

### Домен 3 — Testing and Deployment
- **3.1.2 (нове)** збірка та керування **образами контейнерів**: Dockerfile, **Amazon ECR** (теги, immutable tags, **lifecycle policies**, image scanning).
- **3.1.4** конфіг для різних середовищ: **AppConfig**, Parameter Store, Secrets Manager, API GW stages, Lambda env vars/aliases.
- **3.2.x** тестування: тести з **SAM/CDK/CloudFormation**, **mock API (API GW MOCK)**, **тестові події JSON** для Lambda/API GW, середовища (Lambda aliases, теги образів, **Amplify branches**, **Copilot environments**, API stages), **AI-інструменти для генерації тестів**.
- **3.3.2 (нове)** деплой контейнерів: **ECS task definitions, EKS manifests, Fargate, rolling updates**.
- **3.3.7** автоматичний/ручний **rollback** (CloudFormation, CodeDeploy + CloudWatch alarms).
- **3.3.9** AI-інструменти в CI/CD (автоматичні погодження, post-deployment validation).

### Домен 4
- **4.1.3** запити до логів (**CloudWatch Logs Insights**), **4.1.4** дебаг деплою за output-логами (CloudFormation events, CodeBuild/CodeDeploy logs).
- **4.2.1** **structured logging** (JSON-логи, Lambda advanced logging controls), **4.2.2** код, що емітить custom metrics (EMF/PutMetricData), **4.2.3** сповіщення (квоти, завершення деплою — EventBridge/SNS), **4.2.5** **health checks, liveness/readiness probes** (ELB, ECS container health checks, EKS probes).
- **4.3.1** concurrency: provisioned concurrency, ASG, **thread pool / connection pooling** (RDS Proxy); **4.3.4** кешування: ElastiCache, **DAX**, CloudFront.
- **4.1.5 / 4.3.6** AI-інструменти для аналізу помилок та оптимізації.

## 3. Сервіси
**Додано в scope:** Amazon Data Firehose, **Amazon Bedrock**, **Amazon Bedrock AgentCore**, Amazon Quick, **Kiro**, Amazon CloudWatch Logs, AWS SAM, **AWS PrivateLink**, **ACM**, **AWS Private CA**, Amazon S3 Glacier.
**Також у scope (повний список Developer Tools/Containers):** Amplify, CloudShell, CodeArtifact, CodeBuild, CodeDeploy, CodePipeline, X-Ray; ECR, ECS, EKS; AppConfig, CDK, CloudFormation, Systems Manager.
**Прибрано зі scope:** **AWS AppSync**, Amazon Q Developer.
**Поза scope:** SageMaker AI, Strands Agents, Transfer Family, Polly, Rekognition, Shield, Snow Family, Storage Gateway, QuickSight.
**Поза scope як задачі:** вибір foundation model, prompt engineering, проєктування RAG, проєктування AI-safety фреймворків/guardrails-політик, ETL.

> Тобто про Bedrock питатимуть з погляду **розробника**: як безпечно викликати модель, обмежити доступ, захистити дані, фільтрувати вхід і вихід, логувати без витоку PII. Не питатимуть, як обрати модель чи написати промпт.

## 4. Міні-шпаргалка Bedrock для розробника
- Виклик: SDK `bedrock-runtime` → `InvokeModel` / `Converse` (уніфікований API) / `*WithResponseStream` (стрімінг).
- Права: роль застосунку з `bedrock:InvokeModel` на ARN моделі (або inference profile).
- Приватність: VPC endpoint `bedrock-runtime`, KMS, дані не використовуються для навчання.
- Guardrails: створюєш guardrail → передаєш `guardrailIdentifier`/`guardrailVersion` у виклик → фільтри входу й виходу (PII mask/block, denied topics, prompt attack).
- Логи: model invocation logging → CloudWatch Logs/S3 → data protection policy для маскування.
- Агенти: AgentCore Runtime (ізоляція сесій), Identity (OAuth до інструментів), Gateway (інструменти з API/Lambda), Memory, Observability; human-in-the-loop для чутливих дій.
- Throttling Bedrock → backoff, як і для всіх AWS API.

Джерела: офіційний exam guide DVA-C03 — <https://docs.aws.amazon.com/pdfs/aws-certification/latest/developer-associate-03/developer-associate-03.pdf>, анонс AWS — <https://aws.amazon.com/blogs/training-and-certification/september-2026-new-offerings/>
