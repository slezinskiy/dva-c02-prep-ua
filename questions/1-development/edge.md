# CloudFront / Route 53 / WAF

**Домен 1 — Розробка з використанням сервісів AWS** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t01-q52"></a>
### T01-Q52
*⏭ пропущено · домен: офіційна мітка*

A company has hosted its restaurant review website on an Amazon EC2 instance. The website supports multiple languages and the preferred language is added as a query string parameter to the request. The directory structure and file names for all versions of the website are identical. The website responds with the chosen language's webpage when accessed directly. However, when the same webpage is accessed through the configured CloudFront distribution, it defaults to a single language.

How will you fix this issue?

- ✅ Create a new cache policy for the CloudFront distribution and set the cache behavior to Query string forwarding and caching. In the Query string whitelist field include the language string. Update the CloudFront distribution to use the new cache policy
- ▫️ Create a new cache policy for the CloudFront distribution and set the cache behavior to None to improve caching performance. Update the CloudFront distribution to use the new cache policy
- ▫️ Create a new cache policy for the CloudFront distribution and set the cache behavior to Cache based on selected request headers. Use Whitelist Headers as the caching criteria
- ▫️ Choose the Customize option for the Object Caching setting and reduce the Default TTL value so that CloudFront forwards requests to your origin more frequently

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a new cache policy for the CloudFront distribution and set the cache behavior to Query string forwarding and caching. In the Query string whitelist field include the language string. Update the CloudFront distribution to use the new cache policy

CloudFront can cache different versions of your content based on the values of query string parameters. Forward all, cache based on whitelist option should be chosen if your origin server returns different versions of your objects based on one or more query string parameters. Then specify the parameters that you want CloudFront to use as a basis for caching in the Query string whitelist field.

Incorrect options:

Create a new cache policy for the CloudFront distribution and set the cache behavior to None to improve caching performance. Update the CloudFront distribution to use the new cache policy - None improves Caching. Choose this option if your origin returns the same version of an object regardless of the values of query string parameters. This increases the likelihood that CloudFront can serve a request from the cache, which improves performance and reduces the load on your origin.

Create a new cache policy for the CloudFront distribution and set the cache behavior to Cache based on selected request headers. Use Whitelist Headers as the caching criteria - Cache based on selected request headers is not a valid option since the use case mentions using query string parameters.

Choose the Customize option for the Object Caching setting and reduce the Default TTL value so that CloudFront forwards requests to your origin more frequently - Default TTL specifies the default amount of time, in seconds, that you want objects to stay in CloudFront caches before CloudFront forwards another request to your origin to determine whether the object has been updated. This option is irrelevant for the current use case since the response returned defaulting to the same language is not a TTL issue.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesQueryString> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesQueryStringWhiteList>

</details>

---

<a id="t04-q33"></a>
### T04-Q33
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company use Amazon CloudFront to deliver application content to users around the world. A Developer has made an update to some files in the origin however users have reported that they are still getting the old files.

How can the Developer ensure that the old files are replaced in the cache with the LEAST disruption?

- ▫️ Add code to Lambda@Edge that updates the files in the cache
- ✅ Invalidate the files from the edge caches
- ▫️ Disable the CloudFront distribution and enable it again to update all the edge locations
- ▫️ Create a new origin with the new files and remove the old origin

<details><summary>Пояснення (оригінал, EN)</summary>

If you need to remove files from CloudFront edge caches before they expire you can invalidate the files from the edge caches. To invalidate files, you can specify either the path for individual files or a path that ends with the \* wildcard, which might apply to one file or to many, as shown in the following examples:

• /images/image1.jpg

• /images/image\*

• /images/\*

You can submit a specified number of invalidation paths each month for free. If you submit more than the allotted number of invalidation paths in a month, you pay a fee for each invalidation path that you submit.

CORRECT: "Invalidate the files from the edge caches" is the correct answer.

INCORRECT: "Create a new origin with the new files and remove the old origin" is incorrect as this would be more disruptive and costly as the entire cache would need to be updated.

INCORRECT: "Disable the CloudFront distribution and enable it again to update all the edge locations" is incorrect as this will cause an outage (disruption) and will not replace files that have not yet expired.

INCORRECT: "Add code to Lambda@Edge that updates the files in the cache" is incorrect as there’s no value in running code in Lambda@Edge to update the files. Instead the files in the cache can be invalidated.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html>

</details>

---

<a id="t06-q27"></a>
### T06-Q27
*✅ відповів правильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A start-up organization is launching a new website. Which statement correctly describes how to set up the domain, routing, and health checks in AWS?

- ▫️ Use Route 53 to register a domain name and AWS Certificate Manager to perform routing to the domain and Shield to perform health checks on resources.
- ✅ Use Route 53 to register a domain name, route the internet traffic to domain, and specify the values to perform health checks on resources.
- ▫️ Use Route 53 to register a domain name and perform routing to the domain and Shield to perform health checks on resources.
- ▫️ Use Route 53 to specify the IP address to perform health checks, register a domain name, and route the internet traffic to domain.

<details><summary>Пояснення (оригінал, EN)</summary>

Route 53 can be used as a DNS to register a domain name, route the internet traffic, and perform health checks on resources. If being used for all three tasks, the order of register domain, route the traffic, and perform health checks must be sequential. ACM and Shield are not needed for this task.

CORRECT: "Use Route 53 to register a domain name, route the internet traffic to domain, and specify the values to perform health checks on resources" is the correct answer (as explained above.)

INCORRECT: "Use Route 53 to register a domain name and AWS Certificate Manager to perform routing to the domain and Shield to perform health checks on resources" is incorrect. Route 53 can do all the tasks. ACM and Shield cannot perform these tasks.

INCORRECT: "Use Route 53 to specify the IP address to perform health checks, register a domain name, and route the internet traffic to domain" is incorrect. Route 53 can be used to perform these tasks but must done in a specific order starting with registering a domain.

INCORRECT: "Use Route 53 to register a domain name and perform routing to the domain and Shield to perform health checks on resources" is incorrect. Route 53 can do all these tasks. Shield does not perform health checks on resources.

Джерела: <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html>

</details>

---

<a id="t06-q34"></a>
### T06-Q34
*❌ відповів неправильно · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company runs a popular website behind an Amazon CloudFront distribution that uses an Application Load Balancer as the origin. The Developer wants to set up custom HTTP responses to 404 errors for content that has been removed from the origin that redirects the users to another page.

The Developer wants to use an AWS Lambda@Edge function that is associated with the current CloudFront distribution to accomplish this goal. The solution must use a minimum amount of resources.

Which CloudFront event type should the Developer use to invoke the Lambda@Edge function that contains the redirect logic?

- ▫️ Origin request
- ❌ Viewer request
- ▫️ Viewer response
- ✅ Origin response

<details><summary>Пояснення (оригінал, EN)</summary>

When CloudFront receives an HTTP response from the origin server, if there is an origin-response trigger associated with the cache behavior, you can modify the HTTP response to override what was returned from the origin.

Some common scenarios for updating HTTP responses include the following:

   • Changing the status to set an HTTP 200 status code and creating static body content to return to the viewer when an origin returns an error status code (4xx or 5xx)

   • Changing the status to set an HTTP 301 or HTTP 302 status code, to redirect the user to another website when an origin returns an error status code (4xx or 5xx)

You can also replace the HTTP responses in viewer and origin request events. However, in this case it is the error response being returned from the origin that must be modified when a 404 error is encountered for a page that has been removed.

CORRECT: "Origin response" is the correct answer.

INCORRECT: "Origin request" is incorrect as explained above.

INCORRECT: "Viewer response" is incorrect as explained above.

INCORRECT: "Viewer request" is incorrect as explained above.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-updating-http-responses.html>

</details>

---

<a id="t11-q02"></a>
### T11-Q02
*⏭ пропущено · домен: офіційна мітка*

An application is hosted by a 3rd party and exposed at yourapp.3rdparty.com. You would like to have your users access your application using www.mydomain.com, which you own and manage under Route 53.

What Route 53 record should you create?

- ▫️ Create an A record
- ▫️ Create an Alias Record
- ▫️ Create a PTR record
- ✅ Create a CNAME record

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Create a CNAME record

A CNAME record maps DNS queries for the name of the current record, such as acme.example.com, to another domain (example.com or example.net) or subdomain (acme.example.com or zenith.example.org).

CNAME records can be used to map one domain name to another. Although you should keep in mind that the DNS protocol does not allow you to create a CNAME record for the top node of a DNS namespace, also known as the zone apex. For example, if you register the DNS name example.com, the zone apex is example.com. You cannot create a CNAME record for example.com, but you can create CNAME records for www.example.com, newproduct.example.com, and so on.

Please review the major differences between CNAME and Alias Records:  via - https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html

Incorrect options:

Create an A record - Used to point a domain or subdomain to an IP address. 'A record' cannot be used to map one domain name to another.

Create a PTR record - A Pointer (PTR) record resolves an IP address to a fully-qualified domain name (FQDN) as an opposite to what A record does. PTR records are also called Reverse DNS records. 'PTR record' cannot be used to map one domain name to another.

Create an Alias Record - Alias records let you route traffic to selected AWS resources, such as CloudFront distributions and Amazon S3 buckets. They also let you route traffic from one record in a hosted zone to another record. 3rd party websites do not qualify for these as we have no control over those. 'Alias record' cannot be used to map one domain name to another.

Reference:

https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html

</details>

---
