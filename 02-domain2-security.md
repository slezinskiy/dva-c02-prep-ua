# Домен 2 — Безпека (26%)

> Що перевіряє: автентифікація та авторизація (IAM, Cognito, STS), шифрування (KMS, S3, сертифікати), робота з секретами.
> Головне правило: **ролі замість ключів**, **least privilege**, **тимчасові credentials**.

**Зміст:** [IAM / STS / Organizations](#iam) · [Cognito](#cognito) · [KMS та шифрування](#kms) · [Шифрування S3](#s3enc) · [Сертифікати (ACM, Private CA)](#certs) · [Secrets Manager vs Parameter Store](#secrets) · [Інші сервіси безпеки](#other)

---

<a id="iam"></a>
## 1. IAM, STS, Organizations

### Логіка оцінки політик
1. За замовчуванням — **implicit deny**.
2. **Явний Deny перемагає будь-який Allow** [T07-Q03](questions/2-security/iam.md#t07-q03) [T03-Q17](questions/4-troubleshooting/iam.md#t03-q17).
3. Allow має бути хоч в одній політиці: identity-based **або** resource-based (в межах одного акаунта). Для крос-акаунтного доступу потрібен Allow **з обох боків**.
4. SCP (Organizations) та permission boundaries **обмежують** максимальні права, самі нічого не дають.

### Типи політик
| Тип | Де | Ключова особливість |
|---|---|---|
| AWS managed | Створює AWS | Зручно, але часто ширше, ніж треба |
| Customer managed | Створюєш ти | Можна вказати конкретний ARN → **least privilege** [T02-Q21](questions/2-security/iam.md#t02-q21) [T02-Q63](questions/2-security/s3.md#t02-q63); best practice — standalone замість inline [T08-Q36](questions/2-security/iam.md#t08-q36) |
| Inline | Вбудована в одну сутність | Жорсткий зв'язок 1:1 |
| Resource-based | S3 bucket, SQS, SNS, KMS key, Lambda, Secrets Manager | Має елемент **`Principal`** [T08-Q37](questions/2-security/s3.md#t08-q37); у самому IAM єдина resource-based policy — **trust policy ролі** [T11-Q45](questions/1-development/iam.md#t11-q45) |
| **SCP** | Organizations | Обмежує все в акаунті-члені, зокрема root; deny list [T08-Q54](questions/2-security/iam.md#t08-q54) [T10-Q22](questions/2-security/iam.md#t10-q22) |

- **Policy variables** (`${aws:username}`, `${cognito-identity.amazonaws.com:sub}`) — одна політика для всіх користувачів [T01-Q46](questions/4-troubleshooting/s3.md#t01-q46) [T04-Q63](questions/2-security/s3.md#t04-q63) [T11-Q60](questions/2-security/iam.md#t11-q60) [T07-Q16](questions/2-security/cognito.md#t07-q16).
- Умови: `aws:SecureTransport`, `aws:MultiFactorAuthAge` [T07-Q53](questions/1-development/s3.md#t07-q53), `s3:x-amz-server-side-encryption`, `kms:ViaService` [T09-Q42](questions/4-troubleshooting/kms.md#t09-q42), `dynamodb:LeadingKeys`.
- `NotResource` / Deny на префікс → доступ до всього, крім `private*` [T03-Q29](questions/2-security/s3.md#t03-q29).

### Ролі та тимчасові credentials
- EC2 → **instance profile з IAM role**, ніколи не ключі на інстансі [T01-Q13](questions/2-security/s3.md#t01-q13) [T03-Q47](questions/2-security/iam.md#t03-q47) [T05-Q63](questions/1-development/network.md#t05-q63) [T07-Q18](questions/2-security/network.md#t07-q18). Створення через CLI: `create-instance-profile` → `add-role-to-instance-profile` → `associate-iam-instance-profile` [T05-Q38](questions/2-security/iam.md#t05-q38).
- ECS → **task role** (окрема роль на кожен сервіс = least privilege) [T04-Q49](questions/2-security/iam.md#t04-q49) [T06-Q32](questions/2-security/containers.md#t06-q32) [T05-Q10](questions/2-security/iam.md#t05-q10); на EC2-інстансах контейнерів потрібен `ECS_ENABLE_TASK_IAM_ROLE=true` [T12-Q46](questions/3-deployment/containers.md#t12-q46).
- Lambda → **execution role**.
- **Ланцюжок credential provider у SDK:** env vars → shared credentials/config file → … → container credentials → **instance profile — останнім**. Тому стара `~/.aws/credentials` на інстансі «перемагає» роль [T08-Q58](questions/2-security/iam.md#t08-q58).
- **STS:**
  - `AssumeRole` — крос-акаунт, тест прав ролі (`aws sts assume-role`) [T06-Q33](questions/2-security/iam.md#t06-q33) [T06-Q48](questions/2-security/iam.md#t06-q48) [T10-Q07](questions/2-security/iam.md#t10-q07);
  - `GetSessionToken` — **MFA** для API-викликів [T08-Q52](questions/2-security/iam.md#t08-q52);
  - `AssumeRoleWithWebIdentity` / `AssumeRoleWithSAML` — федерація;
  - `decode-authorization-message` — розшифровує закодоване повідомлення про помилку авторизації [T03-Q45](questions/2-security/iam.md#t03-q45) [T08-Q45](questions/2-security/iam.md#t08-q45).
  - Тимчасові credentials мають строк дії (за замовчуванням 1 год) → застосунок має їх **оновлювати** [T01-Q63](questions/2-security/network.md#t01-q63).
- **Крос-акаунт (класичний сценарій):** в акаунті B (з ресурсом) роль з permissions policy + **trust policy**, де принципал — акаунт A або його роль; в акаунті A — дозвіл `sts:AssumeRole` [T03-Q57](questions/2-security/iam.md#t03-q57) [T09-Q35](questions/1-development/iam.md#t09-q35) [T12-Q02](questions/1-development/iam.md#t12-q02).

### Ключі доступу та root
- Root: MFA, не використовувати щодня, **видалити access keys root** [T01-Q10](questions/2-security/iam.md#t01-q10) [T04-Q25](questions/2-security/iam.md#t04-q25). Лише root може створювати **CloudFront key pairs** (старий механізм) [T11-Q27](questions/2-security/iam.md#t11-q27) і вмикати IAM-доступ до Billing [T11-Q32](questions/2-security/iam.md#t11-q32).
- Програмний доступ = **Access key ID + Secret access key** [T04-Q21](questions/2-security/iam.md#t04-q21). Загубив ключі → деактивуй/видали й створи нові [T04-Q16](questions/2-security/iam.md#t04-q16); скомпрометовані → **першим ділом видали/деактивуй** [T10-Q27](questions/2-security/iam.md#t10-q27). Різні ключі для різних застосунків [T04-Q25](questions/2-security/iam.md#t04-q25).
- Групи для призначення прав [T08-Q36](questions/2-security/iam.md#t08-q36) [T08-Q57](questions/2-security/iam.md#t08-q57).
- **IAM Access Analyzer** — зовнішній/публічний доступ до ресурсів [T11-Q24](questions/2-security/s3.md#t11-q24), невикористані ролі та права [T11-Q51](questions/2-security/iam.md#t11-q51).

---

<a id="cognito"></a>
## 2. Amazon Cognito — розрізняй User Pools і Identity Pools

| **User Pools** (автентифікація — «хто ти») | **Identity Pools** (авторизація в AWS — «що можеш в AWS») |
|---|---|
| Каталог користувачів: sign-up/sign-in, скидання пароля, **MFA**, hosted UI з логотипом [T05-Q52](questions/2-security/cognito.md#t05-q52) | Обмінює токен (User Pool, Google, Facebook, SAML, OIDC, developer-authenticated) на **тимчасові AWS credentials** (STS) |
| Повертає **JWT** (ID, access, refresh tokens) [T11-Q26](questions/2-security/cognito.md#t11-q26) | **Unauthenticated (guest) roles** — гості [T04-Q62](questions/2-security/s3.md#t04-q62) [T08-Q26](questions/2-security/iam.md#t08-q26) |
| Social login (Google, Facebook, Apple), SAML/OIDC IdP [T05-Q27](questions/2-security/cognito.md#t05-q27) | Прямий доступ мобільного застосунку до DynamoDB/S3 без ключів [T03-Q23](questions/1-development/dynamodb.md#t03-q23) [T09-Q28](questions/1-development/dynamodb.md#t09-q28) |
| **Cognito authorizer в API Gateway** [T07-Q43](questions/1-development/apigw.md#t07-q43) [T09-Q02](questions/2-security/cognito.md#t09-q02) та автентифікація на ALB [T12-Q12](questions/2-security/cognito.md#t12-q12) | Fine-grained доступ: `${cognito-identity.amazonaws.com:sub}` у політиці [T07-Q16](questions/2-security/cognito.md#t07-q16) |
| **Lambda triggers** (pre sign-up, post authentication…) → напр. лист через SES після входу [T01-Q16](questions/1-development/cognito.md#t01-q16) | **Developer-authenticated identities** — власна система автентифікації, унікальні ID незалежно від пристрою [T04-Q27](questions/2-security/cognito.md#t04-q27) [T08-Q28](questions/2-security/cognito.md#t08-q28) |
| Advanced security / **adaptive authentication** — блокує входи з високим ризиком [T06-Q60](questions/2-security/cognito.md#t06-q60) | SAML-федерація + IAM conditions [T02-Q26](questions/2-security/cognito.md#t02-q26) [T06-Q24](questions/2-security/cognito.md#t06-q24) |

- Самостійне скидання пароля + доступ до AWS → **User Pools + Identity Pools** разом [T05-Q17](questions/2-security/cognito.md#t05-q17).
- Mobile-застосунок без бекенду, синхронізація даних між пристроями → історично Cognito Sync, сьогодні **AppSync** [T08-Q10](questions/2-security/cognito.md#t08-q10) [T09-Q62](questions/2-security/apigw.md#t09-q62).
- Гості + зареєстровані користувачі з різними правами → User Pool + IAM-ролі для authenticated та unauthenticated [T08-Q47](questions/2-security/iam.md#t08-q47) [T06-Q12](questions/2-security/iam.md#t06-q12).

---

<a id="kms"></a>
## 3. AWS KMS та шифрування

### Основне
- **KMS key** (раніше «CMK») ніколи не залишає KMS. Типи: **AWS owned**, **AWS managed** (`aws/s3`, ротація щорічно автоматично), **customer managed** (повний контроль: key policy, ротація, видалення) [T07-Q09](questions/2-security/kms.md#t07-q09).
- **`Encrypt` / `Decrypt` напряму — лише до 4 KB даних** [T12-Q61](questions/1-development/kms.md#t12-q61) [T06-Q62](questions/2-security/kms.md#t06-q62).
- Більше 4 KB → **envelope encryption**: `GenerateDataKey` повертає **plaintext data key + зашифровану копію**; шифруєш дані локально plaintext-ключем, викидаєш його з пам'яті, зберігаєш зашифрований ключ поруч з даними [T02-Q15](questions/2-security/kms.md#t02-q15) [T04-Q57](questions/2-security/kms.md#t04-q57) [T07-Q64](questions/1-development/kms.md#t07-q64) [T08-Q03](questions/2-security/s3.md#t08-q03) [T06-Q58](questions/2-security/kms.md#t06-q58) [T09-Q58](questions/2-security/kms.md#t09-q58) [T04-Q53](questions/2-security/kms.md#t04-q53).
- `GenerateDataKeyWithoutPlaintext` — якщо шифрувати буде інша система пізніше.
- Щоб викликати `GenerateDataKey`, у key policy / IAM потрібен `kms:GenerateDataKey` [T10-Q43](questions/2-security/kms.md#t10-q43). Upload у S3 з SSE-KMS вимагає `kms:GenerateDataKey`, download — `kms:Decrypt` [T07-Q39](questions/1-development/iam.md#t07-q39).
- **Автоматична ротація** customer managed ключа (щорічно або із заданим періодом) — найпростіший спосіб виконати вимогу «ротувати ключі щороку» [T03-Q56](questions/2-security/kms.md#t03-q56) [T05-Q16](questions/2-security/kms.md#t05-q16).
- **ThrottlingException** від KMS → exponential backoff, **кешування data keys** (AWS Encryption SDK, `LocalCryptoMaterialsCache`), запит на підвищення квоти [T01-Q42](questions/2-security/kms.md#t01-q42) [T05-Q03](questions/2-security/kms.md#t05-q03) [T10-Q24](questions/2-security/kms.md#t10-q24) → [c11](06-contradictions-and-traps.md#c11).
- **CloudHSM** — виділений апаратний HSM (FIPS 140-2 L3, single-tenant), ключі під повним контролем клієнта [T10-Q16](questions/2-security/kms.md#t10-q16).
- Шифрування log group CloudWatch → `aws logs associate-kms-key` [T03-Q22](questions/2-security/kms.md#t03-q22).
- CodeBuild-артефакти шифруються вказаним KMS-ключем [T01-Q07](questions/2-security/cicd.md#t01-q07).
- Kinesis, SQS, SNS, DynamoDB, EBS, AppConfig — шифрування at rest через KMS **без змін у коді** [T03-Q60](questions/1-development/messaging.md#t03-q60) [T08-Q61](questions/2-security/kms.md#t08-q61) [T12-Q33](questions/2-security/kms.md#t12-q33) [T06-Q10](questions/2-security/kms.md#t06-q10) [T07-Q26](questions/2-security/kms.md#t07-q26) [T12-Q49](questions/2-security/kms.md#t12-q49).

<a id="s3enc"></a>
### 4. Шифрування S3 — заголовки (часто питають!)
| Тип | Ключі | Заголовок запиту |
|---|---|---|
| **SSE-S3** | Керує S3, AES-256, **за замовчуванням з 01.2023** [T12-Q41](questions/2-security/kms.md#t12-q41) | `x-amz-server-side-encryption: AES256` [T03-Q19](questions/2-security/s3.md#t03-q19) [T11-Q16](questions/2-security/s3.md#t11-q16) |
| **SSE-KMS** | Ключ KMS, аудит у CloudTrail, контроль ключа | `x-amz-server-side-encryption: aws:kms` (+ `...-aws-kms-key-id`) [T03-Q09](questions/2-security/kms.md#t03-q09) [T11-Q29](questions/2-security/s3.md#t11-q29) |
| DSSE-KMS | Двошарове шифрування KMS | `aws:kms:dsse` |
| **SSE-C** | Ключ надає **клієнт у кожному запиті**; **HTTPS обов'язковий**, інакше запит відхиляється [T01-Q33](questions/2-security/kms.md#t01-q33) [T03-Q31](questions/2-security/kms.md#t03-q31) | `x-amz-server-side-encryption-customer-algorithm/-key/-key-MD5` |
| Client-side | Шифруєш до відправлення (KMS data key / Encryption SDK) | — |

- Вимусити шифрування → bucket policy з **Deny `s3:PutObject`**, якщо заголовок відсутній або має невірне значення [T05-Q34](questions/2-security/s3.md#t05-q34) [T11-Q29](questions/2-security/s3.md#t11-q29) [T11-Q16](questions/2-security/s3.md#t11-q16).
- Вимусити **HTTPS (in-transit)** → bucket policy з Deny за умови **`aws:SecureTransport = false`** [T02-Q61](questions/2-security/s3.md#t02-q61) [T10-Q35](questions/2-security/s3.md#t10-q35) [T12-Q45](questions/2-security/s3.md#t12-q45).

<a id="certs"></a>
### 5. Сертифікати: ACM, Private CA, IAM
- **ACM** — безкоштовні публічні сертифікати для **ELB, CloudFront, API Gateway** (не на EC2 напряму). Для CloudFront та edge-optimized API сертифікат має бути в **us-east-1**.
- Сторонній сертифікат → **імпортуй в ACM** і прив'яжи до custom domain API Gateway; запис alias у Route 53 [T05-Q01](questions/2-security/kms.md#t05-q01).
- **Імпортовані сертифікати ACM не продовжує** → імпортуй новий або запроси сертифікат ACM [T06-Q29](questions/2-security/kms.md#t06-q29). Сертифікати, видані ACM з DNS-валідацією, **продовжуються автоматично**, якщо CNAME-записи на місці й сертифікат використовується; інакше ACM надсилає події через Health/EventBridge [T06-Q61](questions/2-security/kms.md#t06-q61).
- HTTPS на ALB → сертифікат ACM на HTTPS-listener [T06-Q16](questions/2-security/kms.md#t06-q16) [T05-Q05](questions/2-security/network.md#t05-q05) [T12-Q56](questions/2-security/kms.md#t12-q56). Server certificates можна тримати також в **IAM** (для регіонів/випадків без ACM) [T11-Q44](questions/2-security/kms.md#t11-q44).
- **AWS Private CA** — власна приватна PKI: підпорядковані CA, приватні сертифікати, IAM, CloudTrail [T02-Q38](questions/2-security/kms.md#t02-q38).
- IoT-пристрої: X.509-сертифікати пристроям видає **AWS IoT Core** → [d01](07-outdated-and-wrong-answers.md#d01) [T02-Q32](questions/2-security/kms.md#t02-q32).
- Сайт з довіреним сертифікатом → у браузері **замочок** [T06-Q20](questions/2-security/kms.md#t06-q20).
- SSH key pair в кількох регіонах → згенеруй публічний ключ із приватного й **імпортуй у кожен регіон** [T09-Q41](questions/2-security/network.md#t09-q41).

---

<a id="secrets"></a>
## 6. Secrets Manager vs SSM Parameter Store → [c06](06-contradictions-and-traps.md#c06)

| | **Secrets Manager** | **Parameter Store** |
|---|---|---|
| Призначення | Секрети: паролі БД, API keys, токени | Конфігурація + секрети (`SecureString` через KMS) |
| **Автоматична ротація** | **Так** (вбудована для RDS/Aurora/Redshift/DocumentDB, Lambda для решти; розклад rate/cron) [T05-Q12](questions/2-security/secrets.md#t05-q12) [T05-Q55](questions/2-security/secrets.md#t05-q55) [T06-Q44](questions/2-security/secrets.md#t06-q44) [T10-Q61](questions/2-security/secrets.md#t10-q61) [T11-Q31](questions/2-security/secrets.md#t11-q31) | Ні (лише власна логіка) |
| Крос-акаунтний доступ | **Resource-based policy** + customer managed KMS key [T07-Q61](questions/2-security/iam.md#t07-q61) | Обмежено |
| Генерація пароля | `GetRandomPassword`, **dynamic reference** у CloudFormation `{{resolve:secretsmanager:...}}` [T06-Q50](questions/3-deployment/iac.md#t06-q50) | `{{resolve:ssm:...}}`, `{{resolve:ssm-secure:...}}` |
| Ієрархія | — | **Шляхи** `/app/dev/db-url` для кожного середовища [T09-Q17](questions/2-security/secrets.md#t09-q17) |
| Ціна | Платно за секрет | Standard — безкоштовно |
| Аудит | CloudTrail | CloudTrail [T03-Q10](questions/2-security/secrets.md#t03-q10) [T11-Q53](questions/4-troubleshooting/secrets.md#t11-q53) |

- **Ключ до вибору:** «rotation», «cross-account», «database credentials», «API key третьої сторони» → **Secrets Manager** [T09-Q22](questions/2-security/secrets.md#t09-q22) [T10-Q14](questions/2-security/secrets.md#t10-q14) [T06-Q39](questions/2-security/secrets.md#t06-q39). «Конфігурація», «ієрархія», «дешево», «SecureString» → **Parameter Store** [T03-Q10](questions/2-security/secrets.md#t03-q10) [T07-Q56](questions/2-security/beanstalk.md#t07-q56) [T09-Q17](questions/2-security/secrets.md#t09-q17).
- ECS/EB: секрети підтягуються під час запуску (`secrets` у task definition, змінні середовища EB) — мінімальні зміни коду.

---

<a id="other"></a>
## 7. Інші сервіси безпеки
| Сервіс | Для чого | Питання |
|---|---|---|
| **AWS WAF** | Web ACL на CloudFront/ALB/API GW: **geo match** (блок країн), rate-based rules, SQLi/XSS | [T02-Q40](questions/1-development/network.md#t02-q40) |
| AWS Shield | Захист від DDoS (Standard — безкоштовно) | [T02-Q40](questions/1-development/network.md#t02-q40) |
| **Amazon Macie** | Пошук PII / фінансових даних в S3 (`SensitiveData:S3Object/Financial`) | [T01-Q51](questions/2-security/s3.md#t01-q51) [T10-Q44](questions/2-security/s3.md#t10-q44) |
| **Amazon Inspector** | Автоматичний пошук вразливостей (EC2, ECR, Lambda) | [T04-Q04](questions/1-development/other.md#t04-q04) |
| **CloudWatch Logs data protection** | Маскування PII в логах (managed data identifiers) | [T08-Q17](questions/4-troubleshooting/observability.md#t08-q17) |
| **CloudTrail** | Хто й коли викликав API (аудит) | [T08-Q11](questions/1-development/s3.md#t08-q11) [T08-Q55](questions/4-troubleshooting/observability.md#t08-q55) [T11-Q53](questions/4-troubleshooting/secrets.md#t11-q53) [T12-Q52](questions/4-troubleshooting/cicd.md#t12-q52) |
| IAM Access Analyzer | Зовнішній доступ, невикористані права | [T11-Q24](questions/2-security/s3.md#t11-q24) [T11-Q51](questions/2-security/iam.md#t11-q51) |
| **Signature Version 4** | Підпис запиту: у заголовку `Authorization` або в query string `X-Amz-Signature` (presigned) | [T05-Q50](questions/1-development/other.md#t05-q50) |
