# KMS / шифрування / сертифікати

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 3  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#kms) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q49"></a>
### T03-Q49
*домен: офіційна мітка*

For an application that stores personal health information (PHI) in an encrypted Amazon RDS for MySQL DB instance, a developer wants to improve its performance by caching frequently accessed data and adding the ability to sort or rank the cached datasets.

What is the best approach to meet these requirements subject to the constraint that the PHI stays encrypted at all times?

- ✅ Store the frequently accessed data in an Amazon ElastiCache for Redis instance with encryption enabled for data in transit and at rest
- ▫️ Migrate the frequently accessed data to DynamoDB Accelerator (DAX) that has encryption enabled for data in transit and at rest
- ▫️ Store the frequently accessed data in an Amazon ElastiCache for Memcached instance with encryption enabled for data in transit and at rest
- ▫️ Migrate the frequently accessed data to an EC2 Instance Store that has encryption enabled for data in transit and at rest

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Store the frequently accessed data in an Amazon ElastiCache for Redis instance with encryption enabled for data in transit and at rest»**

ElastiCache for Redis — сумісний з Redis in-memory сервіс структур даних (сховище або кеш) з високою доступністю, надійністю, безпекою і продуктивністю. Крім рядків, Redis підтримує списки, множини, **sorted sets**, хеші, бітові масиви і HyperLogLog. Sorted sets легко реалізують сортування й рейтинги (наприклад, таблицю лідерів). Шифрування in-transit і at-rest захищає PHI.

**❌ Неправильні варіанти:**
- *Memcached* — простий і не має складних структур даних для сортування чи ранжування.
- *DAX* — кеш для DynamoDB, з RDS MySQL не працює.
- *EC2 Instance Store* — тимчасове блочне сховище інстансу, не кеш-сервіс.

Джерела: <https://aws.amazon.com/elasticache/redis/> · <https://aws.amazon.com/elasticache/redis-vs-memcached/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html>

</details>

---

<a id="t07-q64"></a>
### T07-Q64
*домен: офіційна мітка*

> ⚠️ **GenerateDataKey.** → [детальніше](../../06-contradictions-and-traps.md#c12)

The development team at a company wants to encrypt a 111 GB object using AWS KMS.

Which of the following represents the best solution?

- ✅ Make a GenerateDataKey API call that returns a plaintext key and an encrypted copy of a data key. Use a plaintext key to encrypt the data
- ▫️ Make an Encrypt API call to encrypt the plaintext data as ciphertext using a customer master key (CMK) with imported key material
- ▫️ Make a GenerateDataKeyWithoutPlaintext API call that returns an encrypted copy of a data key. Use an encrypted key to encrypt the data
- ▫️ Make a GenerateDataKeyWithPlaintext API call that returns an encrypted copy of a data key. Use a plaintext key to encrypt the data

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Make a GenerateDataKey API call that returns a plaintext key and an encrypted copy of a data key. Use a plaintext key to encrypt the data»**

`GenerateDataKey` створює унікальний симетричний data key для клієнтського шифрування. Повертає відкриту копію (`Plaintext`) і копію, зашифровану вказаним KMS-ключем (`CiphertextBlob`). Кожен запит дає новий ключ, не пов'язаний ні з тим, хто викликає, ні з KMS-ключем.

Шифрування поза KMS:
1. Отримати data key через `GenerateDataKey`.
2. Зашифрувати дані plaintext-ключем і стерти його з пам'яті.
3. Зберегти зашифрований ключ поруч із зашифрованими даними.

Розшифрування:
1. Розшифрувати зашифрований ключ через `Decrypt`.
2. Розшифрувати ним дані і стерти ключ з пам'яті.

**❌ Неправильні варіанти:**
- *GenerateDataKeyWithPlaintext* — такого API не існує.
- *Encrypt with imported key material* — `Encrypt` шифрує малі обсяги (до 4 KB), наприклад пароль чи ідентифікатор, або переносить зашифровані дані між регіонами. Тут не підходить.
- *GenerateDataKeyWithoutPlaintext, encrypt with encrypted key* — повертає лише зашифрований ключ, а шифрувати ним дані неможливо. Цей API для систем, які шифруватимуть пізніше: тоді ключ розшифровують через `Decrypt`.

Джерела: <https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html> · <https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html> · <https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html>

</details>

---

<a id="t12-q61"></a>
### T12-Q61
*домен: офіційна мітка*

A developer at a university is encrypting a large XML payload transferred over the network using AWS KMS and wants to test the application before going to production.

What is the maximum data size supported by AWS KMS?

- ▫️ 1MB
- ▫️ 10MB
- ✅ 4KB
- ▫️ 16KB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «4KB»**

KMS напряму шифрує до **4 KB** (4 096 байт) довільних даних: RSA-ключ, пароль до БД тощо. Для більших даних використовують **envelope encryption**. Це ще й швидше: мережею йде лише маленький data key, а дані шифруються локально в застосунку чи сервісі AWS.

**❌ Неправильні варіанти:**
- *1 MB* — понад 4 KB потрібне envelope encryption.
- *10 MB* — понад 4 KB потрібне envelope encryption.
- *16 KB* — понад 4 KB потрібне envelope encryption.

</details>

---
