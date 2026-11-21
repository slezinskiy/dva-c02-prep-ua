# CloudFront / Route 53 / WAF

**Домен 3 — Розгортання** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t08-q14"></a>
### T08-Q14
*домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A website is being delivered using Amazon CloudFront and a Developer recently modified some images that are displayed on website pages. Upon testing the changes, the Developer noticed that the new versions of the images are not displaying.

What should the Developer do to force the new images to be displayed?

- ▫️ Invalidate the old versions of the images on the origin
- ▫️ Delete the images from the origin and then save the new version on the origin
- ▫️ Force an update of the cache
- ✅ Invalidate the old versions of the images on the edge caches

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Invalidate the old versions of the images on the edge caches»**

Прибрати файл з edge-кешу CloudFront до спливання TTL можна двома способами:
- **Invalidation** — наступний запит піде в origin по нову версію.
- **Версіоновані імена файлів** — нова версія отримує інше ім'я.

Для інвалідації вказують шлях до файлу або шаблон з `*`: `/images/image1.jpg`, `/images/image*`, `/images/*`. Після інвалідації кешовані копії зникнуть, і нові версії зображень закешуються при наступному запиті.

**❌ Неправильні варіанти:**
- *Delete from origin and re-save* — кеш від цього не скинеться.
- *Invalidate on the origin* — інвалідують edge-кеш, а не origin.
- *Force an update of the cache* — напряму оновити кеш неможливо; лише інвалідувати.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html>

</details>

---
