# CloudFront / Route 53 / WAF

**Домен 2 — Безпека** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q25"></a>
### T03-Q25
*домен: офіційна мітка*

Your company leverages Amazon CloudFront to provide content via the internet to customers with low latency. Aside from latency, security is another concern and you are looking for help in enforcing end-to-end connections using HTTPS so that content is protected.

Which of the following options is available for HTTPS in AWS CloudFront?

- ▫️ Neither between clients and CloudFront nor between CloudFront and backend
- ▫️ Between clients and CloudFront only
- ✅ Between clients and CloudFront as well as between CloudFront and backend
- ▫️ Between CloudFront and backend only

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Between clients and CloudFront as well as between CloudFront and backend»**

CloudFront можна налаштувати вимагати HTTPS від глядачів (шифрування між клієнтом і CloudFront) і використовувати HTTPS для отримання об'єктів з origin (шифрування між CloudFront і backend).

**❌ Неправильні варіанти:**
- *Between clients and CloudFront only* і *Between CloudFront and backend only* — HTTPS можна вимагати на обох ділянках.
- *Neither…* — неправда.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/secure-connections-supported-viewer-protocols-ciphers.html#secure-connections-supported-ciphers-cloudfront-to-origin> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-viewers-to-cloudfront.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-cloudfront-to-custom-origin.html>

</details>

---

<a id="t08-q32"></a>
### T08-Q32
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company is using Amazon CloudFront to provide low-latency access to a web application to its global users. The organization must encrypt all traffic between users and CloudFront, and all traffic between CloudFront and the web application.

How can these requirements be met? (Select TWO.)

- ✅ Set the Origin Protocol Policy to “HTTPS Only”
- ✅ Set the Viewer Protocol Policy to “HTTPS Only” or “Redirect HTTP to HTTPS”
- ▫️ Enable the CloudFront option Restrict Viewer Access
- ▫️ Set the Origin’s HTTP Port to 443
- ▫️ Use AWS KMS to encrypt traffic between CloudFront and the web application

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Set the Origin Protocol Policy to “HTTPS Only”» + «Set the Viewer Protocol Policy to “HTTPS Only” or “Redirect HTTP to HTTPS”»**

Дані в дорозі шифруються через HTTPS, тож налаштовують обидві політики дистрибуції CloudFront:
- **Origin Protocol Policy** визначає, як CloudFront з'єднується з origin: HTTP Only, HTTPS Only або Match Viewer (той самий протокол, що й у глядача).
- **Viewer Protocol Policy** визначає, як глядачі звертаються до CloudFront: HTTP and HTTPS, Redirect HTTP to HTTPS або HTTPS Only.

**❌ Неправильні варіанти:**
- *KMS for traffic* — KMS шифрує дані at rest, а не трафік.
- *Origin's HTTP Port 443* — потрібна HTTPS Only в Origin Protocol Policy, а 443 — це HTTPS-порт, а не HTTP.
- *Restrict Viewer Access* — вимагає signed URLs або signed cookies, тобто про доступ, а не шифрування.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html>

</details>

---

<a id="t09-q01"></a>
### T09-Q01
*домен: офіційна мітка*

A pharmaceutical company uses Amazon EC2 instances for application hosting and Amazon CloudFront for content delivery. A new research paper with critical findings has to be shared with a research team that is spread across the world.

Which of the following represents the most optimal solution to address this requirement without compromising the security of the content?

- ▫️ Use CloudFront signed cookies feature to control access to the file
- ▫️ Using CloudFront's Field-Level Encryption to help protect sensitive data
- ✅ Use CloudFront signed URL feature to control access to the file
- ▫️ Configure AWS Web Application Firewall (WAF) to monitor and control the HTTP and HTTPS requests that are forwarded to CloudFront

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use CloudFront signed URL feature to control access to the file»**

Signed URL містить додаткову інформацію, наприклад термін дії, і так дає більше контролю над доступом. Як це працює:
1. У дистрибуції вказують trusted key groups з публічними ключами для перевірки підпису; URL підписують відповідними приватними ключами.
2. Застосунок вирішує, чи має користувач доступ: увійшов, оплатив тощо.
3. Застосунок створює signed URL і повертає його користувачу, наприклад браузер одразу ним користується.
4. CloudFront перевіряє підпис публічним ключем: якщо підпис невалідний, запит відхиляється. Інакше файл віддається з кешу або з origin.

**❌ Неправильні варіанти:**
- *Signed cookies* — потрібні, коли не хочуть змінювати URL або коли треба дати доступ до багатьох файлів, наприклад до всієї зони підписників. Тут лише один файл, тож оптимальний signed URL. Якщо є і signed URL, і signed cookie, CloudFront враховує лише URL.
- *AWS WAF* — фільтрує запити за IP, query string тощо і повертає 403. Підходить для ширших задач, а не для доступу до одного файлу.
- *Field-Level Encryption* — шифрує окремі поля POST-форм перед передачею в origin; до задачі не стосується.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-signed-urls.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-awswaf.html> · <https://aws.amazon.com/about-aws/whats-new/2017/12/introducing-field-level-encryption-on-amazon-cloudfront/>

</details>

---

<a id="t09-q40"></a>
### T09-Q40
*домен: офіційна мітка*

A developer is defining the signers that can create signed URLs for their Amazon CloudFront distributions.

Which of the following statements should the developer consider while defining the signers? (Select two)

- ✅ When you use the root user to manage CloudFront key pairs, you can only have up to two active CloudFront key pairs per AWS account
- ▫️ Both the signers (trusted key groups and CloudFront key pairs) can be managed using the CloudFront APIs
- ▫️ You can also use AWS Identity and Access Management (IAM) permissions policies to restrict what the root user can do with CloudFront key pairs
- ✅ When you create a signer, the public key is with CloudFront and private key is used to sign a portion of URL
- ▫️ CloudFront key pairs can be created with any account that has administrative permissions and full access to CloudFront resources

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «When you use the root user to manage CloudFront key pairs, you can only have up to two active CloudFront key pairs per AWS account» + «When you create a signer, the public key is with CloudFront and private key is used to sign a portion of URL»**

- **Signer.** Кожен signer для signed URLs чи cookies має пару ключів: приватним ключем підписують частину URL чи cookie, а CloudFront перевіряє підпис публічним. При запиті CloudFront перевіряє, що URL не підроблено і він ще чинний, наприклад не сплив термін.
- **Root key pairs.** Через root можна мати не більше двох активних CloudFront key pairs на акаунт.

**Trusted key groups** гнучкіші: до 4 key groups на дистрибуцію і до 5 публічних ключів у групі (за замовчуванням).

ℹ️ Зараз рекомендують саме trusted key groups: ними керують через IAM і API, root не потрібен. Див. 07.

**❌ Неправильні варіанти:**
- *IAM policies restrict root* — IAM-політики на root не діють, і це одна з причин не використовувати root.
- *Any admin account can create key pairs* — CloudFront key pairs створює лише root.
- *Both signers managed via API* — через API керують лише key groups; root key pairs — тільки через консоль, тож їх не автоматизувати.

</details>

---

<a id="t11-q06"></a>
### T11-Q06
*домен: офіційна мітка*

A media company has created a video streaming application and it would like their Brazilian users to be served by the company's Brazilian servers. Other users around the globe should not be able to access the servers through DNS queries.

Which Route 53 routing policy meets this requirement?

- ✅ Geolocation
- ▫️ Weighted
- ▫️ Latency
- ▫️ Failover

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Geolocation»**

Geolocation routing вибирає ресурс залежно від місця, звідки надходить DNS-запит. Наприклад, усі запити з Європи йдуть на балансувальник у Франкфурті. Так також обмежують поширення контенту регіонами, де є права. Default-запис обробляє запити з нерозпізнаних IP і з локацій без власного запису; без нього Route 53 відповідає «no answer».

**❌ Неправильні варіанти:**
- *Failover* — на резервний ресурс, коли основний нездоровий.
- *Latency* — на регіон із найменшою затримкою.
- *Weighted* — розподіл трафіку в заданих пропорціях.

</details>

---
