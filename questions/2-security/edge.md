# CloudFront / Route 53 / WAF

**Домен 2 — Безпека** · питань: 5  
Теорія: [01-domain1-development.md](../../01-domain1-development.md#edge) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і пояснення — оригінальні англійською (на екзамені теж англійська). ✅ — правильна відповідь за ключем банку, ❌ — твоя минула помилкова відповідь. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t03-q25"></a>
### T03-Q25
*⏭ пропущено · домен: офіційна мітка*

Your company leverages Amazon CloudFront to provide content via the internet to customers with low latency. Aside from latency, security is another concern and you are looking for help in enforcing end-to-end connections using HTTPS so that content is protected.

Which of the following options is available for HTTPS in AWS CloudFront?

- ▫️ Neither between clients and CloudFront nor between CloudFront and backend
- ▫️ Between clients and CloudFront only
- ✅ Between clients and CloudFront as well as between CloudFront and backend
- ▫️ Between CloudFront and backend only

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Between clients and CloudFront as well as between CloudFront and backend

For web distributions, you can configure CloudFront to require that viewers use HTTPS to request your objects, so connections are encrypted when CloudFront communicates with viewers.

Requiring HTTPS for Communication Between Viewers and CloudFront:  via - https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-viewers-to-cloudfront.html

You also can configure CloudFront to use HTTPS to get objects from your origin, so connections are encrypted when CloudFront communicates with your origin.

Requiring HTTPS for Communication Between CloudFront and Your Custom Origin:  via - https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-cloudfront-to-custom-origin.html

Incorrect options:

Between clients and CloudFront only - This is incorrect as you can choose to require HTTPS between CloudFront and your origin.

Between CloudFront and backend only - This is incorrect as you can choose to require HTTPS between viewers and CloudFront.

Neither between clients and CloudFront nor between CloudFront and backend - This is incorrect as you can choose HTTPS settings both for communication between viewers and CloudFront as well as between CloudFront and your origin.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/secure-connections-supported-viewer-protocols-ciphers.html#secure-connections-supported-ciphers-cloudfront-to-origin> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-viewers-to-cloudfront.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https-cloudfront-to-custom-origin.html>

</details>

---

<a id="t08-q32"></a>
### T08-Q32
*⏭ пропущено · домен: визначено автоматично (у джерелі: AWS Networking & Content Delivery)*

A company is using Amazon CloudFront to provide low-latency access to a web application to its global users. The organization must encrypt all traffic between users and CloudFront, and all traffic between CloudFront and the web application.

How can these requirements be met? (Select TWO.)

- ✅ Set the Origin Protocol Policy to “HTTPS Only”
- ✅ Set the Viewer Protocol Policy to “HTTPS Only” or “Redirect HTTP to HTTPS”
- ▫️ Enable the CloudFront option Restrict Viewer Access
- ▫️ Set the Origin’s HTTP Port to 443
- ▫️ Use AWS KMS to encrypt traffic between CloudFront and the web application

<details><summary>Пояснення (оригінал, EN)</summary>

This scenario requires encryption of in-flight data which can be done by implementing HTTPS. To do this the organization must configure the Origin Protocol Policy and the Viewer Protocol Policy on the CloudFront Distribution.

The Origin Protocol Policy can be used to select whether you want CloudFront to connect to your origin using only HTTP, only HTTPS, or to connect by matching the protocol used by the viewer. For example, if you select Match Viewer for the Origin Protocol Policy, and if the viewer connects to CloudFront using HTTPS, CloudFront will connect to your origin using HTTPS.

If you want CloudFront to allow viewers to access your web content using either HTTP or HTTPS, specify HTTP and HTTPS. If you want CloudFront to redirect all HTTP requests to HTTPS, specify Redirect HTTP to HTTPS. If you want CloudFront to require HTTPS, specify HTTPS Only.

CORRECT: “Set the Origin Protocol Policy to “HTTPS Only”” is a correct answer.

CORRECT: “Set the Viewer Protocol Policy to “HTTPS Only” or “Redirect HTTP to HTTPS”” is also a correct answer.

INCORRECT: “Use AWS KMS to encrypt traffic between CloudFront and the web application” is incorrect as KMS is used for encrypting data at rest.

INCORRECT: “Set the Origin’s HTTP Port to 443” is incorrect as you must configure the origin protocol policy to HTTPS. The HTTPS port should be set to 443.

INCORRECT: “Enable the CloudFront option Restrict Viewer Access” is incorrect as this is used to configure whether you want CloudFront to require users to access your content using a signed URL or a signed cookie.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html>

</details>

---

<a id="t09-q01"></a>
### T09-Q01
*⏭ пропущено · домен: офіційна мітка*

A pharmaceutical company uses Amazon EC2 instances for application hosting and Amazon CloudFront for content delivery. A new research paper with critical findings has to be shared with a research team that is spread across the world.

Which of the following represents the most optimal solution to address this requirement without compromising the security of the content?

- ▫️ Use CloudFront signed cookies feature to control access to the file
- ▫️ Using CloudFront's Field-Level Encryption to help protect sensitive data
- ✅ Use CloudFront signed URL feature to control access to the file
- ▫️ Configure AWS Web Application Firewall (WAF) to monitor and control the HTTP and HTTPS requests that are forwarded to CloudFront

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Use CloudFront signed URL feature to control access to the file

A signed URL includes additional information, for example, expiration date and time, that gives you more control over access to your content.

Here's an overview of how you configure CloudFront for signed URLs and how CloudFront responds when a user uses a signed URL to request a file:

In your CloudFront distribution, specify one or more trusted key groups, which contain the public keys that CloudFront can use to verify the URL signature. You use the corresponding private keys to sign the URLs.

Develop your application to determine whether a user should have access to your content and to create signed URLs for the files or parts of your application that you want to restrict access to.

A user requests a file for which you want to require signed URLs. Your application verifies that the user is entitled to access the file: they've signed in, they've paid for access to the content, or they've met some other requirement for access.

Your application creates and returns a signed URL to the user. The signed URL allows the user to download or stream the content.

This step is automatic; the user usually doesn't have to do anything additional to access the content. For example, if a user is accessing your content in a web browser, your application returns the signed URL to the browser. The browser immediately uses the signed URL to access the file in the CloudFront edge cache without any intervention from the user.

CloudFront uses the public key to validate the signature and confirm that the URL hasn't been tampered with. If the signature is invalid, the request is rejected. If the request meets the requirements in the policy statement, CloudFront does the standard operations: determines whether the file is already in the edge cache, forwards the request to the origin if necessary, and returns the file to the user.
Incorrect options:

Use CloudFront signed cookies feature to control access to the file - CloudFront signed cookies allow you to control who can access your content when you don't want to change your current URLs or when you want to provide access to multiple restricted files, for example, all of the files in the subscribers' area of a website. Our requirement has only one file that needs to be shared and hence signed URL is the optimal solution.

Signed URLs take precedence over signed cookies. If you use both signed URLs and signed cookies to control access to the same files and a viewer uses a signed URL to request a file, CloudFront determines whether to return the file to the viewer based only on the signed URL.

Configure AWS Web Application Firewall (WAF) to monitor and control the HTTP and HTTPS requests that are forwarded to CloudFront - AWS WAF is a web application firewall that lets you monitor the HTTP and HTTPS requests that are forwarded to CloudFront, and lets you control access to your content. Based on conditions that you specify, such as the values of query strings or the IP addresses that requests originate from, CloudFront responds to requests either with the requested content or with an HTTP status code 403 (Forbidden). A firewall is optimal for broader use cases than restricted access to a single file.

Using CloudFront's Field-Level Encryption to help protect sensitive data - CloudFront's field-level encryption further encrypts sensitive data in an HTTPS form using field-specific encryption keys (which you supply) before a POST request is forwarded to your origin. This ensures that sensitive data can only be decrypted and viewed by certain components or services in your application stack. This feature is not useful for the given use case.

Джерела: <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-signed-urls.html> · <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-awswaf.html> · <https://aws.amazon.com/about-aws/whats-new/2017/12/introducing-field-level-encryption-on-amazon-cloudfront/>

</details>

---

<a id="t09-q40"></a>
### T09-Q40
*⏭ пропущено · домен: офіційна мітка*

A developer is defining the signers that can create signed URLs for their Amazon CloudFront distributions.

Which of the following statements should the developer consider while defining the signers? (Select two)

- ✅ When you use the root user to manage CloudFront key pairs, you can only have up to two active CloudFront key pairs per AWS account
- ▫️ Both the signers (trusted key groups and CloudFront key pairs) can be managed using the CloudFront APIs
- ▫️ You can also use AWS Identity and Access Management (IAM) permissions policies to restrict what the root user can do with CloudFront key pairs
- ✅ When you create a signer, the public key is with CloudFront and private key is used to sign a portion of URL
- ▫️ CloudFront key pairs can be created with any account that has administrative permissions and full access to CloudFront resources

<details><summary>Пояснення (оригінал, EN)</summary>

Correct options:

When you create a signer, the public key is with CloudFront and private key is used to sign a portion of URL - Each signer that you use to create CloudFront signed URLs or signed cookies must have a public–private key pair. The signer uses its private key to sign the URL or cookies, and CloudFront uses the public key to verify the signature.

When you create signed URLs or signed cookies, you use the private key from the signer’s key pair to sign a portion of the URL or the cookie. When someone requests a restricted file, CloudFront compares the signature in the URL or cookie with the unsigned URL or cookie, to verify that it hasn’t been tampered with. CloudFront also verifies that the URL or cookie is valid, meaning, for example, that the expiration date and time haven’t passed.

When you use the root user to manage CloudFront key pairs, you can only have up to two active CloudFront key pairs per AWS account - When you use the root user to manage CloudFront key pairs, you can only have up to two active CloudFront key pairs per AWS account.

Whereas, with CloudFront key groups, you can associate a higher number of public keys with your CloudFront distribution, giving you more flexibility in how you use and manage the public keys. By default, you can associate up to four key groups with a single distribution, and you can have up to five public keys in a key group.

Incorrect options:

You can also use AWS Identity and Access Management (IAM) permissions policies to restrict what the root user can do with CloudFront key pairs - When you use the AWS account root user to manage CloudFront key pairs, you can’t restrict what the root user can do or the conditions in which it can do them. You can’t apply IAM permissions policies to the root user, which is one reason why AWS best practices recommend against using the root user.

CloudFront key pairs can be created with any account that has administrative permissions and full access to CloudFront resources - CloudFront key pairs can only be created using the root user account and hence is not a best practice to create CloudFront key pairs as signers.

Both the signers (trusted key groups and CloudFront key pairs) can be managed using the CloudFront APIs - With CloudFront key groups, you can manage public keys, key groups, and trusted signers using the CloudFront API. You can use the API to automate key creation and key rotation. When you use the AWS root user, you have to use the AWS Management Console to manage CloudFront key pairs, so you can’t automate the process.

Reference:

https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-trusted-signers.html

</details>

---

<a id="t11-q06"></a>
### T11-Q06
*⏭ пропущено · домен: офіційна мітка*

A media company has created a video streaming application and it would like their Brazilian users to be served by the company's Brazilian servers. Other users around the globe should not be able to access the servers through DNS queries.

Which Route 53 routing policy meets this requirement?

- ✅ Geolocation
- ▫️ Weighted
- ▫️ Latency
- ▫️ Failover

<details><summary>Пояснення (оригінал, EN)</summary>

Correct option:

Geolocation

Geolocation routing lets you choose the resources that serve your traffic based on the geographic location of your users, meaning the location that DNS queries originate from. For example, you might want all queries from Europe to be routed to an ELB load balancer in the Frankfurt region. You can also use geolocation routing to restrict distribution of content to only the locations in which you have distribution rights

You can create a default record that handles both queries from IP addresses that aren't mapped to any location and queries that come from locations that you haven't created geolocation records for. If you don't create a default record, Route 53 returns a "no answer" response for queries from those locations.

via - https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html

Incorrect options:

Failover - Failover routing lets you route traffic to a resource when the resource is healthy or to a different resource when the first resource is unhealthy.

Latency - If your application is hosted in multiple AWS Regions, you can improve performance for your users by serving their requests from the AWS Region that provides the lowest latency.

Weighted - Use this policy to route traffic to multiple resources in proportions that you specify.

Reference:

https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html

</details>

---
