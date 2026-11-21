# AWS Certified Developer – Associate (DVA-C02) — швидка підготовка 🇺🇦

Конспект підготовки до сертифікації **AWS DVA-C02** на основі аналізу **780 питань** з 12 пробних тестів: питання згруповано за офіційними доменами, зібрано повний перелік знань, розібрано протиріччя між відповідями та застарілі ключі.

> ## ⚠️ Терміново: дати екзамену
> **DVA-C02 можна здати до 30.11.2026 включно.** З **01.12.2026** проводиться лише **DVA-C03** (нові ваги доменів, безпека AI-сервісів, контейнери/ECR).
> - Здаєш до 30.11 → план нижче, файли 00–08.
> - Здаєш пізніше → ті самі файли **+ [09-dva-c03-delta.md](09-dva-c03-delta.md)**.

## Структура репозиторію
```
.
├── README.md                          ← ти тут: план навчання
├── 00-exam-overview.md                ← формат, домени, тактика на екзамені
├── 01-domain1-development.md          ← Домен 1 (32%): Lambda, API GW, DynamoDB, S3, SQS/SNS, Kinesis, кеш, мережа
├── 02-domain2-security.md             ← Домен 2 (26%): IAM/STS, Cognito, KMS, шифрування S3, ACM, секрети
├── 03-domain3-deployment.md           ← Домен 3 (24%): стратегії деплою, Code*, CloudFormation/SAM/CDK, Beanstalk, ECS
├── 04-domain4-troubleshooting.md      ← Домен 4 (18%): CloudWatch, X-Ray, CloudTrail, коди помилок, оптимізація
├── 05-numbers-and-limits.md           ← усі цифри й ліміти + формули RCU/WCU
├── 06-contradictions-and-traps.md     ← ⭐ протиріччя у відповідях і як їх уникати
├── 07-outdated-and-wrong-answers.md   ← ⭐ застарілі та сумнівні ключі банку
├── 08-keyword-triggers.md             ← «бачу в умові → обираю» (повторення за 30 хв)
├── 09-dva-c03-delta.md                ← що додати, якщо екзамен з 01.12.2026
└── questions/
    ├── README.md                      ← індекс банку за доменами й темами
    ├── my-mistakes.md                 ← ⭐ 59 питань, де була помилка
    ├── 1-development/*.md             ← 300 питань
    ├── 2-security/*.md                ← 173 питання
    ├── 3-deployment/*.md              ← 141 питання
    └── 4-troubleshooting/*.md         ← 166 питань
```

Позначки `[Txx-Qyy]` у теорії — посилання на конкретні питання банку (тест xx, питання yy). У кожному питанні є примітка ⚠️, якщо його відповідь суперечить іншим питанням або застаріла.

## План на 7–9 днів (екзамен до 30.11)
| День | Що робити | Час |
|---|---|---|
| 1 | [00](00-exam-overview.md) + [06 Протиріччя](06-contradictions-and-traps.md) (метод) + [Домен 1](01-domain1-development.md): Lambda, API GW | 3–4 год |
| 2 | Домен 1: DynamoDB (розрахунки!), S3, SQS/SNS/Kinesis, кеш, мережа → питання з [questions/1-development](questions/README.md#1-development) | 4 год |
| 3 | [Домен 2](02-domain2-security.md) повністю → питання з [questions/2-security](questions/README.md#2-security) | 3–4 год |
| 4 | [Домен 3](03-domain3-deployment.md) (таблиця стратегій!) → [questions/3-deployment](questions/README.md#3-deployment) | 3–4 год |
| 5 | [Домен 4](04-domain4-troubleshooting.md) + [05 Числа](05-numbers-and-limits.md) → [questions/4-troubleshooting](questions/README.md#4-troubleshooting) | 3 год |
| 6 | [07 Застарілі](07-outdated-and-wrong-answers.md) + [my-mistakes](questions/my-mistakes.md) + повторний прогін 1–2 пробних тестів на час (130 хв) | 4 год |
| 7 | Слабкі теми за результатами, [08 Тригери](08-keyword-triggers.md), [05 Числа](05-numbers-and-limits.md) | 2–3 год |
| Перед екзаменом | Лише 08 + 05 + розділи «Правило» в 06. Сон > зубріння | 1 год |

**Метод роботи з питаннями:** спершу прочитай теорію теми, потім розв'язуй питання **не дивлячись на ✅** (закрий рукою). Пояснення сховано в `<details>`. Ціль — ≥ 80% на пробних тестах перед реальним екзаменом.

## Статистика банку
| Домен | Вага | Питань | Твоїх помилок |
|---|---|---|---|
| 1. Development | 32% | 300 | 20 |
| 2. Security | 26% | 173 | 11 |
| 3. Deployment | 24% | 141 | 16 |
| 4. Troubleshooting | 18% | 166 | 12 |

Зі 780 питань: 66 ✅, 59 ❌, 655 пропущено. Два питання — дублікати.

## Як зроблено
- 6 тестів мали офіційні мітки доменів DVA-C02, 6 інших — мітки за категоріями сервісів (AWS Compute, AWS Database…). Другі віднесено до офіційних доменів **автоматично за ключовими словами**; у кожному такому питанні це позначено.
- Тексти питань та пояснень збережено **англійською** (на екзамені теж англійська); теорію, протиріччя та примітки написано **українською**.
- Актуальність лімітів і статусу сервісів перевірено станом на **21.11.2026** (джерела — у [07](07-outdated-and-wrong-answers.md#джерела-перевірки) та [09](09-dva-c03-delta.md)).

## Офіційні джерела
- Exam guide DVA-C02: <https://docs.aws.amazon.com/aws-certification/latest/developer-associate-02/developer-associate-02.html>
- Exam guide DVA-C03: <https://docs.aws.amazon.com/pdfs/aws-certification/latest/developer-associate-03/developer-associate-03.pdf>
- Безкоштовний офіційний practice question set: AWS Skill Builder.

> Матеріал для самопідготовки. Питання з пробних тестів третіх сторін; ключі не є офіційними відповідями AWS.
