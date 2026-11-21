# Екзамен AWS Certified Developer – Associate: формат і стратегія

## Формат (DVA-C02 і DVA-C03 однакові)
| | |
|---|---|
| Питань | 65 (50 оцінюються + 15 неоцінюваних, які не відрізнити від інших) |
| Час | 130 хв (≈ 2 хв на питання) |
| Типи | Multiple choice (1 з 4) і multiple response (2+ з 5+) |
| Прохідний бал | 720 з 1000, **компенсаторне оцінювання** (не треба проходити кожен домен окремо) |
| Штраф за неправильну відповідь | Немає → **відповідай на все** |
| Мова | Англійська (+ кілька інших). Для неносіїв англійської можна запросити **ESL +30 хв** заздалегідь, у профілі AWS Certification |

## Дати
- **DVA-C02** — останній день здачі **30.11.2026**.
- **DVA-C03** — з **01.12.2026** (зміни → [09-dva-c03-delta.md](09-dva-c03-delta.md)).

## Домени DVA-C02 та задачі
| Домен | Вага | Задачі | Файл |
|---|---|---|---|
| 1. Development with AWS Services | 32% | 1.1 Код для застосунків на AWS · 1.2 Код для Lambda · 1.3 Сховища даних | [01](01-domain1-development.md) |
| 2. Security | 26% | 2.1 Автентифікація/авторизація · 2.2 Шифрування · 2.3 Чутливі дані | [02](02-domain2-security.md) |
| 3. Deployment | 24% | 3.1 Артефакти · 3.2 Тестування в dev-середовищах · 3.3 Автоматизація тестування · 3.4 Деплой через CI/CD | [03](03-domain3-deployment.md) |
| 4. Troubleshooting and Optimization | 18% | 4.1 Root cause analysis · 4.2 Інструментування для observability · 4.3 Оптимізація | [04](04-domain4-troubleshooting.md) |

## Що реально найчастіше в банку (780 питань)
Топ-тем за частотою: **EC2/ASG/ELB/VPC**, **Lambda**, **S3**, **DynamoDB** (зокрема розрахунок RCU/WCU), **CI/CD (CodeDeploy hooks, CodeBuild)**, **IAM/STS**, **KMS/шифрування S3**, **SQS/SNS**, **CloudFormation/SAM**, **API Gateway**, **ECS**, **ElastiCache** (стратегії кешування та сесії), **Elastic Beanstalk** (політики деплою), **Cognito**, **X-Ray/CloudWatch**.

## Тактика на екзамені
1. **Перший прохід — швидко (≈ 80 хв):** відповідай на все; сумнівне — **Flag for review** і рухайся далі.
2. **Другий прохід (≈ 40 хв):** лише позначені питання.
3. **Читай останнє речення першим** — там обмежувач (`MOST cost-effective`, `LEAST operational overhead`…), → [метод](06-contradictions-and-traps.md).
4. **Елімінація:** зазвичай 2 варіанти явно хибні (вигаданий API/параметр, не той сервіс). Обирай з двох решти за обмежувачем.
5. **Select TWO/THREE:** кожен варіант оцінюй незалежно як «правда/неправда» для умови.
6. **Не переускладнюй:** відповідь DVA зазвичай — найпростіша керована функція AWS, а не кастомний код.
7. Не знаєш — **вгадуй**: порожня відповідь = гарантований нуль.

## Типові «дистрактори» (майже завжди хибні)
- «Write custom code to…», якщо є вбудована функція сервісу.
- «Store access keys on the instance / in code / in environment variables» (замість ролі).
- «Use AWS Shield / Inspector / Trusted Advisor» для задач, де потрібні WAF / X-Ray / CloudWatch.
- «Increase instance size», коли проблема в архітектурі (з'єднання, гарячі ключі).
- Неіснуючі речі: «SOAP API в API Gateway», «STS authorizer», «INVALIDATE_CACHE query string», «secure listener у WAF», «Dependencies section у CloudFormation», «AWS::Serverless::UserPool», «DependentParameter».
