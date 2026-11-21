# KMS / шифрування / сертифікати

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 3  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#kms) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q49"></a>
### T03-Q49
*⏭ пропущено · домен: офіційна мітка*

For an application that stores personal health information (PHI) in an encrypted Amazon RDS for MySQL DB instance, a developer wants to improve its performance by caching frequently accessed data and adding the ability to sort or rank the cached datasets.

What is the best approach to meet these requirements subject to the constraint that the PHI stays encrypted at all times?

- ✅ Store the frequently accessed data in an Amazon ElastiCache for Redis instance with encryption enabled for data in transit and at rest
- ▫️ Migrate the frequently accessed data to DynamoDB Accelerator (DAX) that has encryption enabled for data in transit and at rest
- ▫️ Store the frequently accessed data in an Amazon ElastiCache for Memcached instance with encryption enabled for data in transit and at rest
- ▫️ Migrate the frequently accessed data to an EC2 Instance Store that has encryption enabled for data in transit and at rest

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Store the frequently accessed data in an Amazon ElastiCache for Redis instance with encryption enabled for data in transit and at rest

Amazon ElastiCache for Redis is a Redis-compatible in-memory data structure service that can be used as a data store or cache. It delivers the ease of use and power of Redis along with the availability, reliability, scalability, security, and performance suitable for the most demanding applications.

In addition to strings, Redis supports lists, sets, sorted sets, hashes, bit arrays, and hyperlog logs. Applications can use these more advanced data structures to support a variety of use cases. For example, you can use Redis Sorted Sets to easily implement a game leaderboard that keeps a list of players sorted by their rank.

Incorrect options:

Store the frequently accessed data in an Amazon ElastiCache for Memcached instance with encryption enabled for data in transit and at rest - Memcached is designed for simplicity and it does not offer support for advanced data structures and operations such as sort or rank.

 via - https://aws.amazon.com/elasticache/redis-vs-memcached/

Migrate the frequently accessed data to DynamoDB Accelerator (DAX) that has encryption enabled for data in transit and at rest - DAX is a DynamoDB-compatible caching service that enables you to benefit from fast in-memory performance for demanding applications. DAX cannot be used with RDS MySQL as a caching service, so this option is incorrect.

Migrate the frequently accessed data to an EC2 Instance Store that has encryption enabled for data in transit and at rest - This option is incorrect. EC2 instance store provides temporary block-level storage for your instance. This storage is located on disks that are physically attached to the host computer. Instance store is ideal for the temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content. It can also be used to store temporary data that you replicate across a fleet of instances, such as a load-balanced pool of web servers.

Джерела: <https://aws.amazon.com/elasticache/redis/> · <https://aws.amazon.com/elasticache/redis-vs-memcached/> · <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html>

</details>

---

<a id="t07-q64"></a>
### T07-Q64
*⏭ пропущено · домен: офіційна мітка*

> ⚠️ **GenerateDataKey.** → [детальніше](../../06-contradictions-and-traps.md#c12)

The development team at a company wants to encrypt a 111 GB object using AWS KMS.

Which of the following represents the best solution?

- ✅ Make a GenerateDataKey API call that returns a plaintext key and an encrypted copy of a data key. Use a plaintext key to encrypt the data
- ▫️ Make an Encrypt API call to encrypt the plaintext data as ciphertext using a customer master key (CMK) with imported key material
- ▫️ Make a GenerateDataKeyWithoutPlaintext API call that returns an encrypted copy of a data key. Use an encrypted key to encrypt the data
- ▫️ Make a GenerateDataKeyWithPlaintext API call that returns an encrypted copy of a data key. Use a plaintext key to encrypt the data

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Make a GenerateDataKey API call that returns a plaintext key and an encrypted copy of a data key. Use a plaintext key to encrypt the data - GenerateDataKey API, generates a unique symmetric data key for client-side encryption. This operation returns a plaintext copy of the data key and a copy that is encrypted under a customer master key (CMK) that you specify. You can use the plaintext key to encrypt your data outside of AWS KMS and store the encrypted data key with the encrypted data.

GenerateDataKey returns a unique data key for each request. The bytes in the plaintext key are not related to the caller or the CMK.

To encrypt data outside of AWS KMS:

Use the GenerateDataKey operation to get a data key.

Use the plaintext data key (in the Plaintext field of the response) to encrypt your data outside of AWS KMS. Then erase the plaintext data key from memory.

Store the encrypted data key (in the CiphertextBlob field of the response) with the encrypted data.

To decrypt data outside of AWS KMS:

Use the Decrypt operation to decrypt the encrypted data key. The operation returns a plaintext copy of the data key.

Use the plaintext data key to decrypt data outside of AWS KMS, then erase the plaintext data key from memory.

Incorrect options:

Make a GenerateDataKeyWithPlaintext API call that returns an encrypted copy of a data key. Use a plaintext key to encrypt the data - This is a made-up option, given only as a distractor.

Make an Encrypt API call to encrypt the plaintext data as ciphertext using a customer master key (CMK) with imported key material - Encrypt API is used to encrypt plaintext into ciphertext by using a customer master key (CMK). The Encrypt operation has two primary use cases:

To encrypt small amounts of arbitrary data, such as a personal identifier or database password, or other sensitive information.

To move encrypted data from one AWS Region to another.

Neither of the two is useful for the given scenario.

Make a GenerateDataKeyWithoutPlaintext API call that returns an encrypted copy of a data key. Use an encrypted key to encrypt the data - GenerateDataKeyWithoutPlaintext API, generates a unique symmetric data key. This operation returns a data key that is encrypted under a customer master key (CMK) that you specify.

GenerateDataKeyWithoutPlaintext is identical to the GenerateDataKey operation except that returns only the encrypted copy of the data key. This operation is useful for systems that need to encrypt data at some point, but not immediately. When you need to encrypt the data, you call the Decrypt operation on the encrypted copy of the key.

Джерела: <https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html> · <https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html> · <https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html>

</details>

---

<a id="t12-q61"></a>
### T12-Q61
*⏭ пропущено · домен: офіційна мітка*

A developer at a university is encrypting a large XML payload transferred over the network using AWS KMS and wants to test the application before going to production.

What is the maximum data size supported by AWS KMS?

- ▫️ 1MB
- ▫️ 10MB
- ✅ 4KB
- ▫️ 16KB

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

4 KB

You can encrypt up to 4 kilobytes (4096 bytes) of arbitrary data such as an RSA key, a database password, or other sensitive information.

While AWS KMS does support sending data up to 4 KB to be encrypted directly, envelope encryption can offer significant performance benefits. When you encrypt data directly with AWS KMS it must be transferred over the network. Envelope encryption reduces the network load since only the request and delivery of the much smaller data key go over the network. The data key is used locally in your application or encrypting AWS service, avoiding the need to send the entire block of data to AWS KMS and suffer network latency.

Incorrect options:

1MB - For anything over 4 KB, you may want to look at envelope encryption

10MB - For anything over 4 KB, you may want to look at envelope encryption

16KB - For anything over 4 KB, you may want to look at envelope encryption

Reference:

https://aws.amazon.com/kms/faqs/

</details>

---
