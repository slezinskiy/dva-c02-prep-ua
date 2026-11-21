# Числа та ліміти — шпаргалка на останній вечір

> Ці цифри напряму перевіряють в питаннях. ⚠️ — значення змінилося, і старі банки питань подають його по-старому.

## Lambda
| | |
|---|---|
| Timeout | 15 хв (900 с) |
| Memory | 128 MB – 10 240 MB (CPU пропорційно) |
| /tmp | 512 MB – 10 240 MB |
| Env vars | 4 KB сумарно |
| Deployment package | 50 MB zip (пряме завантаження) / 250 MB розпакований / container image 10 GB |
| Layers | 5 |
| Payload | sync 6 MB; async ⚠️ **1 MB** (раніше 256 KB) |
| Concurrency | 1000 / регіон (soft) |
| Async retries | 2 (разом 3 спроби), подія живе до 6 год |
| Формула concurrency | rps × тривалість (с) |

## API Gateway
| | |
|---|---|
| Integration timeout | 29 с за замовчуванням |
| Payload | 10 MB |
| Cache TTL | 300 с за замовчуванням, максимум 3600 с, 0 = вимкнено |
| Throttling | 10 000 rps, burst 5000 (account / region, soft) |
| Інвалідація кешу клієнтом | `Cache-Control: max-age=0` |

## DynamoDB
| | |
|---|---|
| Item | 400 KB |
| RCU | 1 strong/с ≤ 4 KB = 2 eventual/с; транзакційне читання = 2 RCU |
| WCU | 1 запис/с ≤ 1 KB; транзакційний запис = 2 WCU |
| Query/Scan page | 1 MB |
| BatchGetItem | 100 items / 16 MB |
| BatchWriteItem | 25 items / 16 MB (не атомарний) |
| TransactWrite/GetItems | ⚠️ **100 дій** (раніше 25), 4 MB |
| GSI / LSI | 20 / 5 (LSI — лише при створенні таблиці) |
| Streams retention | 24 год |
| TTL | Видаляє протягом кількох днів, безкоштовно |
| PITR | 35 днів |

## SQS / SNS / EventBridge
| | |
|---|---|
| Повідомлення SQS | ⚠️ **1 MiB** (з 08.2025, раніше 256 KB); більше → Extended Client + S3 (до 2 GB) |
| Retention | 1 хв – 14 днів (за замовчуванням 4 дні) |
| Visibility timeout | 0 – 12 год (за замовчуванням 30 с) |
| Delay | 0 – 15 хв |
| Long polling | 1 – 20 с |
| Receive batch | 1 – 10 (за замовчуванням 1) |
| FIFO | 300 msg/с (3000 з батчами), дедуплікація за 5 хв |
| Кількість повідомлень у черзі | Необмежена |
| SNS payload | 1 MiB (налаштування топіка, з 09.2026) |

## Kinesis Data Streams
| | |
|---|---|
| Запис у шард | 1 MB/с або 1000 записів/с |
| Читання з шарда | 2 MB/с (спільно), 5 GetRecords/с; enhanced fan-out — 2 MB/с на кожного consumer'а |
| Retention | 24 год за замовчуванням, до 365 днів |
| Запис | ⚠️ 1 MiB за замовчуванням, до **10 MiB** (з 10.2025) |
| Consumers KCL | ≤ кількості шардів |

## S3
| | |
|---|---|
| Об'єкт | 5 TB; single PUT 5 GB; multipart рекомендовано від 100 MB |
| Продуктивність | 3500 PUT / 5500 GET на секунду **на префікс** |
| Консистентність | Strong read-after-write |
| Шифрування за замовчуванням | SSE-S3 (з 01.2023) |

## KMS / секрети
| | |
|---|---|
| Encrypt/Decrypt напряму | ≤ 4 KB |
| Parameter Store | Standard 4 KB / 10 000 параметрів; Advanced 8 KB |
| Secrets Manager | 64 KB на секрет, ротація за розкладом |
| STS credentials | За замовчуванням 1 год (AssumeRole: 15 хв – 12 год) |

## CloudWatch / X-Ray
| | |
|---|---|
| EC2 metrics | 5 хв (basic), 1 хв (detailed) |
| High-res custom metric | 1 с; alarm 10 с або 30 с |
| X-Ray daemon | UDP 2000; SDK/daemon у maintenance з 25.02.2026, кінець підтримки 25.02.2027 |
| CloudTrail event history | 90 днів |

## Step Functions / Elastic Beanstalk / інше
| | |
|---|---|
| Step Functions Standard | До 1 року; Express — до 5 хв |
| EB source bundle | 1 ZIP/WAR, без кореневої папки, ≤ 500 MB (у банку «512 MB») |
| ECS dynamic port | `hostPort: 0` |
| ELB: немає targets | 503 |
| NACL ephemeral ports | 1024–65535 |
| EBS gp2 | 3 IOPS/GiB, максимум 16 000 (~5,3 TiB); io1 — 50 IOPS/GiB |
| Route 53 health check | failure threshold |
| ACM для CloudFront / edge API | Сертифікат у us-east-1 |
| Lambda@Edge | Деплой лише з us-east-1 |

## Формули DynamoDB (потренуйся)
```
RCU (strong)   = кількість_читань/с × ceil(розмір_KB / 4)
RCU (eventual) = RCU(strong) / 2
WCU            = кількість_записів/с × ceil(розмір_KB / 1)
Транзакції     = × 2
```
Приклади: 16 eventual × 12 KB = 16 × 3 / 2 = **24**; 10 strong × 6 KB = 10 × 2 = **20**; 6 записів × 4.5 KB = 6 × 5 = **30 WCU**.
