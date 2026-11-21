# CloudFront / Route 53 / WAF

**Домен 3 — Розгортання** · питань: 1  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t08-q14"></a>
### T08-Q14
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A website is being delivered using Amazon CloudFront and a Developer recently modified some images that are displayed on website pages. Upon testing the changes, the Developer noticed that the new versions of the images are not displaying.

What should the Developer do to force the new images to be displayed?

- ▫️ Invalidate the old versions of the images on the origin
- ▫️ Delete the images from the origin and then save the new version on the origin
- ▫️ Force an update of the cache
- ✅ Invalidate the old versions of the images on the edge caches

<details><summary>Пояснення (оригінал, EN)</summary>

If you need to remove a file from CloudFront edge caches before it expires, you can do one of the following:

• Invalidate the file from edge caches. The next time a viewer requests the file, CloudFront returns to the origin to fetch the latest version of the file.

• Use file versioning to serve a different version of the file that has a different name. For more information, see Updating Existing Files Using Versioned File Names.

To invalidate files, you can specify either the path for individual files or a path that ends with the \* wildcard, which might apply to one file or to many, as shown in the following examples:

• /images/image1.jpg

• /images/image\*

• /images/\*

Therefore, the Developer should invalidate the old versions of the images on the edge cache as this will remove the cached images and the new versions of the images will then be cached when the next request is received.

CORRECT: "Invalidate the old versions of the images on the edge caches" is the correct answer.

INCORRECT: "Delete the images from the origin and then save the new version on the origin" is incorrect as this will not cause the cache entries to expire. The Developer needs to remove the cached entries to cause a cache miss to occur which will then result in the updated images being cached.

INCORRECT: "Invalidate the old versions of the images on the origin" is incorrect as the Developer needs to invalidate the cache entries on the edge caches, not the images on the origin.

INCORRECT: "Force an update of the cache" is incorrect as there is no way to directly update the cache. The Developer should invalidate the relevant cache entries and then the cache will be updated next time a request is received for the images.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html>

</details>

---
