# CloudFront / Route 53 / WAF

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q52"></a>
### T01-Q52
*домен: офіційна мітка*

A company has hosted its restaurant review website on an Amazon EC2 instance. The website supports multiple languages and the preferred language is added as a query string parameter to the request. The directory structure and file names for all versions of the website are identical. The website responds with the chosen language's webpage when accessed directly. However, when the same webpage is accessed through the configured CloudFront distribution, it defaults to a single language.

How will you fix this issue?

- ✅ Create a new cache policy for the CloudFront distribution and set the cache behavior to Query string forwarding and caching. In the Query string whitelist field include the language string. Update the CloudFront distribution to use the new cache policy
- ▫️ Create a new cache policy for the CloudFront distribution and set the cache behavior to None to improve caching performance. Update the CloudFront distribution to use the new cache policy
- ▫️ Create a new cache policy for the CloudFront distribution and set the cache behavior to Cache based on selected request headers. Use Whitelist Headers as the caching criteria
- ▫️ Choose the Customize option for the Object Caching setting and reduce the Default TTL value so that CloudFront forwards requests to your origin more frequently

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a new cache policy … set the cache behavior to Query string forwarding and caching. In the Query string whitelist field include the language string. Update the CloudFront distribution to use the new cache policy»**

CloudFront може кешувати різні версії вмісту залежно від значень query string параметрів. Якщо origin повертає різні версії об'єкта залежно від параметрів, обирають «Forward all, cache based on whitelist» і в полі Query string whitelist вказують параметри (тут — мову), за якими CloudFront розрізняє кеш.

**❌ Неправильні варіанти:**
- *Set the cache behavior to None* — підходить, лише коли origin повертає той самий об'єкт незалежно від query string: тоді частіше спрацьовує кеш. Тут саме через це всі отримують одну мову.
- *Cache based on selected request headers (Whitelist Headers)* — у задачі мова передається в query string, а не в заголовках.
- *Reduce the Default TTL* — TTL визначає, скільки об'єкт живе в кеші до перевірки на origin. Проблема «завжди одна мова» з TTL не пов'язана.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesQueryString> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesQueryStringWhiteList>

</details>

---

<a id="t04-q33"></a>
### T04-Q33
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company use Amazon CloudFront to deliver application content to users around the world. A Developer has made an update to some files in the origin however users have reported that they are still getting the old files.

How can the Developer ensure that the old files are replaced in the cache with the LEAST disruption?

- ▫️ Add code to Lambda@Edge that updates the files in the cache
- ✅ Invalidate the files from the edge caches
- ▫️ Disable the CloudFront distribution and enable it again to update all the edge locations
- ▫️ Create a new origin with the new files and remove the old origin

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Invalidate the files from the edge caches»**

Щоб прибрати файли з edge-кешів CloudFront до спливання TTL, роблять invalidation: шлях до конкретного файлу або з wildcard `*` (`/images/image1.jpg`, `/images/image*`, `/images/*`). Певна кількість шляхів щомісяця безкоштовна, понад неї — платно.

**❌ Неправильні варіанти:**
- *Create a new origin and remove the old one* — більше збоїв і витрат: оновлювати доведеться весь кеш.
- *Disable and re-enable the distribution* — простій, до того ж невичерпані файли не оновляться.
- *Lambda@Edge that updates files in the cache* — сенсу немає, досить invalidation.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html>

</details>

---

<a id="t06-q27"></a>
### T06-Q27
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A start-up organization is launching a new website. Which statement correctly describes how to set up the domain, routing, and health checks in AWS?

- ▫️ Use Route 53 to register a domain name and AWS Certificate Manager to perform routing to the domain and Shield to perform health checks on resources.
- ✅ Use Route 53 to register a domain name, route the internet traffic to domain, and specify the values to perform health checks on resources.
- ▫️ Use Route 53 to register a domain name and perform routing to the domain and Shield to perform health checks on resources.
- ▫️ Use Route 53 to specify the IP address to perform health checks, register a domain name, and route the internet traffic to domain.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Route 53 to register a domain name, route the internet traffic to domain, and specify the values to perform health checks on resources.»**

Route 53 як DNS-сервіс робить усі три речі: реєструє домен, маршрутизує трафік і перевіряє здоров'я ресурсів. Порядок має бути саме такий: реєстрація → маршрутизація → health checks. ACM і Shield тут не потрібні.

**❌ Неправильні варіанти:**
- *ACM for routing, Shield for health checks* — ACM і Shield цього не вміють; Route 53 робить усе сам.
- *Health checks first, then register* — порядок неправильний: спочатку реєструють домен.
- *Shield for health checks* — Shield не перевіряє здоров'я ресурсів.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html>

</details>

---

<a id="t06-q34"></a>
### T06-Q34
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company runs a popular website behind an Amazon CloudFront distribution that uses an Application Load Balancer as the origin. The Developer wants to set up custom HTTP responses to 404 errors for content that has been removed from the origin that redirects the users to another page.

The Developer wants to use an AWS Lambda@Edge function that is associated with the current CloudFront distribution to accomplish this goal. The solution must use a minimum amount of resources.

Which CloudFront event type should the Developer use to invoke the Lambda@Edge function that contains the redirect logic?

- ▫️ Origin request
- ▫️ Viewer request
- ▫️ Viewer response
- ✅ Origin response

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Origin response»**

Коли CloudFront отримує відповідь від origin, тригер origin-response дозволяє змінити її. Типові сценарії:
- замінити помилку origin (4xx чи 5xx) на 200 зі статичним вмістом;
- замінити помилку на 301 чи 302 і перенаправити користувача на інший сайт.

Відповідь можна сформувати і в подіях viewer request чи origin request. Але тут змінювати треба саме помилку 404, яку повертає origin для видаленої сторінки, тож потрібен origin response.

**❌ Неправильні варіанти:**
- *Origin request* — див. вище.
- *Viewer response* — див. вище.
- *Viewer request* — див. вище.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-updating-http-responses.html>

</details>

---

<a id="t11-q02"></a>
### T11-Q02
*домен: офіційна мітка*

An application is hosted by a 3rd party and exposed at yourapp.3rdparty.com. You would like to have your users access your application using www.mydomain.com, which you own and manage under Route 53.

What Route 53 record should you create?

- ▫️ Create an A record
- ▫️ Create an Alias Record
- ▫️ Create a PTR record
- ✅ Create a CNAME record

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Create a CNAME record»**

CNAME спрямовує DNS-запити з одного імені (`acme.example.com`) на інший домен чи субдомен. Обмеження: CNAME не можна створити для zone apex, тобто кореня зони, наприклад `example.com`. Для `www.example.com` чи `newproduct.example.com` — можна.

Різниця між CNAME і Alias: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html

**❌ Неправильні варіанти:**
- *A record* — вказує на IP-адресу, а не на інше доменне ім'я.
- *PTR record* — зворотний DNS: IP → ім'я.
- *Alias record* — лише на ресурси AWS (CloudFront, S3, ELB тощо) або на інший запис у тій самій зоні; на сторонній сайт не вкаже.

</details>

---
