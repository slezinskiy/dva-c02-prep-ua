# Тригери: «бачу в умові → обираю»

> Швидка таблиця для повторення за 30 хвилин до екзамену. Читай ліву колонку, закривай праву й перевіряй себе.

## Обчислення / serverless
| Бачу в умові | Обираю |
|---|---|
| Обробка > 15 хв | EC2/ECS/Fargate (не Lambda) |
| Cold start, латентність при старті | Provisioned concurrency |
| Пік у відомий час (sale) | Provisioned concurrency за розкладом (Application Auto Scaling) |
| Функція з'їдає concurrency інших | Reserved concurrency |
| CPU-bound Lambda | Більше memory |
| Однакові бібліотеки для багатьох функцій | Lambda Layer |
| Залежності поза runtime | ZIP з кодом + залежностями / container image |
| Повторне використання з'єднань | Ініціалізація поза handler |
| Великий файл при кожному виклику | /tmp кеш |
| Стабільний ARN, rollback, % трафіку | Lambda alias (weighted) |
| Lambda → RDS у приватній мережі | Lambda у VPC + SG |
| Багато з'єднань Lambda → RDS | RDS Proxy |
| Подія без прямої інтеграції з Lambda / розклад | EventBridge rule |
| Async-подію не оброблено | DLQ / on-failure destination |
| StatusCode 202 | Async (`Event`) прийнято |
| Lambda@Edge не деплоїться | Лише us-east-1 |
| Зміна відповіді origin (404 → редирект) | Lambda@Edge origin response |
| Безсерверні контейнери, оплата за задачу | ECS Fargate |
| Мінімум EC2 під контейнери | binpack |
| По AZ, потім по інстансах | spread AZ + spread instanceId |
| Кожна задача на окремому інстансі | distinctInstance constraint |
| Кілька однакових контейнерів на хості | hostPort 0 (dynamic port mapping) |
| Два контейнери ділять дані | Один task definition + shared volume |
| Права для ECS-задач | Task role (окрема на сервіс) |
| Інстанси реєструються не в той кластер | `ECS_CLUSTER` в /etc/ecs/ecs.config |

## Дані
| Бачу в умові | Обираю |
|---|---|
| Key-value, мс-затримка, необмежений масштаб | DynamoDB |
| Мікросекунди для DynamoDB, hot read partition | DAX |
| Hot write partition | Write sharding (суфікс до PK) |
| Інший sort key, «тільки при створенні» | LSI |
| Нові запити за іншим атрибутом | GSI (свої RCU/WCU!) |
| All-or-nothing у кількох таблицях | TransactWriteItems |
| Не перезаписувати чужі зміни | Conditional write |
| Найсвіжіші дані | ConsistentRead=true |
| Видалити старі items автоматично | TTL |
| Очистити всю таблицю | Drop & recreate |
| Реакція на зміни таблиці | DynamoDB Streams + Lambda |
| Глобальні користувачі DynamoDB | Global tables |
| Кешування перед RDS | ElastiCache (Redis для HA/шифрування) |
| Рейтинги / geospatial у кеші | Redis (sorted sets / geo) |
| Масштабувати читання RDS | Read replicas (окремий endpoint) |
| HA RDS | Multi-AZ |
| Звіти в іншому регіоні | Cross-region read replica |
| Ротація паролів БД | Secrets Manager |
| Ієрархічна конфігурація | Parameter Store |

## S3 / доставка
| Бачу в умові | Обираю |
|---|---|
| Тимчасовий доступ до приватного об'єкта | S3 pre-signed URL |
| Платний контент через CDN | CloudFront signed URL/cookies |
| Повільні upload з інших континентів | Transfer Acceleration (+ multipart) |
| > 5500 GET/с | Кілька префіксів |
| Ресурси з іншого бакета/домену не вантажаться | CORS |
| HTTPS для S3 website | CloudFront + ACM |
| Старі файли після оновлення | CloudFront invalidation |
| Статика повільно на EC2 | S3 + CloudFront |
| SQL по логах в S3 | Athena |
| Заборонити HTTP | `aws:SecureTransport=false` → Deny |
| Вимагати SSE-KMS | Deny без `x-amz-server-side-encryption: aws:kms` |
| SSE-C | Лише HTTPS, ключ у кожному запиті |
| Об'єкти з інших акаунтів належать бакету | Object Ownership: bucket owner enforced |
| PII/фінансові дані в S3 | Macie |

## Інтеграція
| Бачу в умові | Обираю |
|---|---|
| Порожні відповіді, витрати на SQS | Long polling (WaitTimeSeconds=20) |
| Обробляють двічі | Збільшити visibility timeout / ChangeMessageVisibility |
| Ізолювати «погані» повідомлення | DLQ |
| Порядок для користувача | FIFO + MessageGroupId |
| Без дублікатів | FIFO + MessageDeduplicationId |
| Відкласти доставку | Delay queue / DelaySeconds |
| > 1 MiB у SQS | S3 + Extended Client |
| Одне → багатьом | SNS fan-out → SQS |
| Різним підписникам різне | SNS filter policy |
| SMS + email | SNS |
| Лише масовий email | SES |
| Real-time потік, порядок, replay | Kinesis Data Streams |
| Доставити потік у S3/OpenSearch без коду | Data Firehose |
| Шарди не встигають | Більше шардів / UpdateShardCount |
| Throughput exceeded при малому навантаженні | Кращий partition key |
| Кілька consumers, низька затримка | Enhanced fan-out |
| Максимум KCL-інстансів | = кількість шардів |
| Оркестрація з паралельними кроками | Step Functions (Parallel) |
| Зберегти вхід + помилку | ResultPath у Catch |
| Довгі workflow з людським погодженням | Step Functions Standard |
| Короткі, з високою частотою | Step Functions Express |

## API Gateway
| Бачу в умові | Обираю |
|---|---|
| SOAP/XML бекенд, JSON-клієнти | REST API + mapping templates |
| Перетворити запит/відповідь | Mapping templates (non-proxy) |
| Найпростіше Lambda за HTTP | Lambda proxy integration |
| Розробка без бекенду | MOCK integration |
| Власна логіка автентифікації / токени 3rd party | Lambda authorizer |
| Користувачі Cognito | Cognito User Pool authorizer |
| Real-time двосторонній зв'язок | WebSocket API |
| Квоти/монетизація API | Usage plans + API keys |
| 403 з новим API key | createUsagePlanKey |
| Інвалідація кешу клієнтом | Cache-Control: max-age=0 |
| Зміни не видно | Redeploy у stage |
| Кілька версій API | Stages + stage variables |
| % трафіку на нову версію API | Canary release |
| Один endpoint агрегує кілька джерел | AppSync GraphQL |

## Безпека
| Бачу в умові | Обираю |
|---|---|
| Доступ EC2 до AWS | IAM role (instance profile) |
| Інший акаунт | Cross-account role + sts:AssumeRole |
| Помилка з закодованим повідомленням | sts decode-authorization-message |
| MFA для API-викликів | GetSessionToken |
| Перевірити права ролі через CLI | sts assume-role |
| Перевірити без виконання | `--dry-run` |
| Обмежити все в акаунті організації | SCP |
| Папка кожного користувача | Policy variables |
| Лише свої items DynamoDB | `dynamodb:LeadingKeys` |
| Sign-up/sign-in, MFA, JWT | Cognito User Pool |
| Прямий доступ мобільного застосунку до AWS / гості | Cognito Identity Pool |
| SAML-федерація + доступ до AWS | Identity Pool |
| Власна автентифікація, унікальні ID між пристроями | Developer-authenticated identities |
| Блок ризикованих входів | Cognito adaptive authentication |
| Шифрувати > 4 KB | KMS GenerateDataKey (envelope) |
| Ротація ключа щороку | KMS automatic rotation |
| Повний контроль над ключами | Customer managed KMS key |
| Виділений HSM | CloudHSM |
| KMS ThrottlingException | Backoff + data key caching + квота |
| Свій PKI, subordinate CAs | AWS Private CA |
| Сторонній сертифікат для API GW | Імпорт в ACM |
| Імпортований сертифікат скоро спливає | Імпортувати новий / запросити ACM |
| SSL без навантаження на EC2 | SSL termination на ELB (ACM) |
| Блок країн | WAF geo match |
| Вразливості | Inspector |
| Хто викликав API | CloudTrail |
| Публічні ресурси / невикористані ролі | IAM Access Analyzer |
| PII в логах | CloudWatch Logs data protection |

## Деплой
| Бачу в умові | Обираю |
|---|---|
| EB, повна потужність, існуючі інстанси | Rolling with additional batch |
| EB, найшвидший rollback / нові інстанси | Immutable |
| EB, оновлення платформи | Blue/Green (CNAME swap) |
| EB, без додаткової вартості | Rolling |
| EB, dev, швидко | All at once |
| EB, конфігурація | `.ebextensions/*.config` |
| EB, періодичні задачі | Worker + cron.yaml |
| EB, БД не має зникати | RDS поза EB |
| EB, забагато версій | Lifecycle policy |
| ECS через CodeDeploy | Blue/green (canary/linear) |
| CodeDeploy EC2 + on-prem | CodeDeploy agent |
| Перевірка після деплою (EC2) | ValidateService |
| Валідація Lambda/ECS перед трафіком | BeforeAllowTraffic |
| Rollback CodeDeploy | Новий деплой з новим ID |
| Пуш образу лише при успіху | post_build → commands |
| Повільні залежності CodeBuild | Cache в S3 |
| CodeBuild AccessDenied | Service role |
| Дебаг buildspec локально | CodeBuild local agent |
| Ручне погодження перед продом | CodePipeline manual approval |
| CodeCommit по HTTPS | Git credentials з IAM |
| CodeCommit по SSH | Публічний ключ в IAM |
| SAM-шаблон | Transform: AWS::Serverless-2016-10-31 |
| Підготувати SAM/CFN з локальним кодом | package → deploy |
| Швидкі ітерації SAM | sam sync |
| Локальний тест Lambda | sam local invoke |
| Значення з іншого стеку | Export + !ImportValue |
| Регіон / акаунт у шаблоні | AWS::Region / AWS::AccountId |
| AMI за регіоном | Mappings + !FindInMap |
| Атрибут ресурсу (endpoint) | !GetAtt |
| Подивитися зміни | Change set |
| Кілька регіонів/акаунтів | StackSets |
| AMI в іншому регіоні | Copy AMI |
| IaC мовою програмування | CDK |
| Hosting фронтенду з Git | Amplify |
| Фіче-флаги, конфіг без деплою | AppConfig |

## Troubleshooting
| Бачу в умові | Обираю |
|---|---|
| RAM/disk EC2 | CloudWatch agent або custom metric |
| Метрика з логів | Metric filter (працює лише для нових подій) |
| Сповіщення за ≤ 30 с | High-resolution alarm |
| Трейсинг між сервісами | X-Ray (ADOT) |
| Фільтр трейсів | Annotations |
| Менше трейсів, менші витрати | Sampling |
| X-Ray на Fargate | Sidecar |
| Логи on-prem → CloudWatch | CloudWatch agent + IAM user creds |
| IP клієнта за ALB | X-Forwarded-For |
| Чи доходить трафік до підмережі | VPC Flow Logs |
| Timeout до сайту | Security group / NACL |
| 503 від ALB | Немає targets |
| Throttling будь-де | Exponential backoff |
