# Amazon Cognito

**Домен 2 — Безпека** · питань: 16  
Теорія: [02-domain2-security.md](../../02-domain2-security.md#cognito) · [Протиріччя](../../06-contradictions-and-traps.md) · [Застарілі/сумнівні відповіді](../../07-outdated-and-wrong-answers.md) · [Усі розділи](../README.md)

> Тексти питань і варіантів — англійською (як на екзамені), пояснення — українською. ✅ — правильна відповідь за ключем банку. ⚠️ — питання з протиріччям або сумнівним/застарілим ключем: читай примітку.

<a id="t02-q26"></a>
### T02-Q26
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is creating a web application that will be used by employees working from home. The company uses a SAML directory on-premises for storing user information. The Developer must integrate with the SAML directory and authorize each employee to access only their own data when using the application.

Which approach should the Developer take?

- ▫️ Create a unique IAM role for each employee and have each employee assume the role to access the application so they can access their personal data only.
- ▫️ Create the application within an Amazon VPC and use a VPC endpoint with a trust policy to grant access to the employees.
- ▫️ Use Amazon Cognito user pools, federate with the SAML provider, and use user pool groups with an IAM policy.
- ✅ Use an Amazon Cognito identity pool, federate with the SAML provider, and use a trust policy with an IAM condition key to limit employee access.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use an Amazon Cognito identity pool, federate with the SAML provider, and use a trust policy with an IAM condition key to limit employee access»**

Cognito через IAM-ролі генерує тимчасові облікові дані для користувачів застосунку; що вони можуть, визначає trust relationship ролі. Щоб обмежити доступ конкретними ідентичностями з SAML-каталогу, у trust policy додають IAM condition key, що перевіряє `cognito-identity.amazonaws.com:sub`.

**❌ Неправильні варіанти:**
- *Cognito user pools, federate with SAML, user pool groups with an IAM policy* — user pool автентифікує, а авторизований доступ до сервісів AWS надає identity pool.
- *VPC endpoint with a trust policy* — VPC endpoint не дає доступу до on-premises SAML-каталогу.
- *Unique IAM role for each employee* — це не інтеграція з SAML, і керувати цим дуже складно.

Джерела: <https://docs.aws.amazon.com/cognito/latest/Developerguide/role-trust-and-permissions.html> · <https://docs.aws.amazon.com/cognito/latest/Developerguide/iam-roles.html>

</details>

---

<a id="t04-q27"></a>
### T04-Q27
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A mobile application has thousands of users. Each user may use multiple devices to access the application. The Developer wants to assign unique identifiers to these users regardless of the device they use.

Which of the below is the BEST method to obtain unique identifiers?

- ✅ Implement developer-authenticated identities by using Amazon Cognito and get credentials for these identities
- ▫️ Create a user table in Amazon DynamoDB with key-value pairs of users and their devices. Use these keys as unique identifiers
- ▫️ Use IAM-generated access key IDs for the users as the unique identifier, but do not store secret keys
- ▫️ Assign IAM users and roles to the users. Use the unique IAM resource ID as the unique identifier

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement developer-authenticated identities by using Amazon Cognito and get credentials for these identities»**

Amazon Cognito дає веб- і мобільним застосункам реєстрацію, вхід, контроль доступу й керування користувачами. Identity pools створюють унікальні ідентичності й видають за ними тимчасові облікові дані AWS з обмеженими правами. Крім федерації через Facebook, Google чи Login with Amazon, Cognito підтримує **developer authenticated identities**: користувачів автентифікує твій власний бекенд, а Cognito синхронізує дані й надає доступ до ресурсів AWS. Так кожен користувач отримує унікальний ідентифікатор незалежно від пристрою.

**❌ Неправильні варіанти:**
- *User table in DynamoDB with users and devices* — більше власної реалізації і керування.
- *IAM-generated access key IDs as identifiers* — для мобільних користувачів не створюють IAM-сутностей; це задача Cognito.
- *IAM users and roles for each user* — те саме: користувачі мобільного застосунку не мають ідентичностей в IAM.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/developer-authenticated-identities.html>

</details>

---

<a id="t04-q60"></a>
### T04-Q60
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A developer is designing a web application that will be used by thousands of users. The users will sign up using their email addresses and the application will store attributes for each user.

Which service should the developer use to enable users to sign-up for the web application?

- ▫️ AWS Inspector
- ▫️ AWS AppSync
- ▫️ Amazon Cognito Sync
- ✅ Amazon Cognito user pool

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Cognito user pool»**

User pool — каталог користувачів у Cognito. Користувачі входять у веб- чи мобільний застосунок напряму або через соціальних провайдерів (Google, Facebook, Amazon, Apple) і SAML. Кожен має профіль, доступний через SDK. User pool дає:
- реєстрацію і вхід;
- вбудований веб-інтерфейс входу з можливістю кастомізації;
- соціальний вхід і SAML;
- каталог користувачів і профілі;
- безпеку: MFA, перевірку скомпрометованих облікових даних, захист від захоплення акаунта, верифікацію телефону й email;
- кастомні сценарії й міграцію користувачів через Lambda triggers.

Після автентифікації Cognito видає JWT для доступу до твоїх API або обміну на облікові дані AWS.

**❌ Неправильні варіанти:**
- *Amazon Cognito Sync* — синхронізація даних профілю між пристроями.
- *AWS Inspector* — оцінка безпеки застосунків.
- *AWS AppSync* — GraphQL API до джерел даних.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html>

</details>

---

<a id="t05-q17"></a>
### T05-Q17
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is creating an application that will require users to access AWS services and allow them to reset their own passwords. Which of the following would allow the company to manage users and authorization while allowing users to reset their own passwords?

- ✅ Amazon Cognito user pools and identity pools
- ▫️ Amazon Cognito identity pools and AWS IAM
- ▫️ Amazon Cognito identity pools and AWS STS
- ▫️ Amazon Cognito user pools and AWS KMS

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Cognito user pools and identity pools»**

Тут дві вимоги: керувати обліковими записами із самостійним скиданням пароля і давати користувачам доступ до сервісів AWS.
- **User Pool** — каталог користувачів із реєстрацією, входом і скиданням пароля.
- **Identity Pool** — видає тимчасові облікові дані з обмеженими правами для доступу до AWS; працює разом із user pool.

**❌ Неправильні варіанти:**
- *Identity pools + STS* — немає каталогу користувачів; потрібен user pool.
- *Identity pools + IAM* — каталогом користувачів має бути user pool. IAM — для облікових записів адміністрування AWS, а не для користувачів застосунку.
- *User pools + KMS* — KMS шифрує дані, а не автентифікує для доступу до AWS.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html>

</details>

---

<a id="t05-q27"></a>
### T05-Q27
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

An application developer is crafting a new software product. To streamline the registration process, they want new users to be able to set up their accounts using their existing social media profiles.

Which AWS service or feature would be the most appropriate for achieving this goal?

- ✅ Amazon Cognito User Pools
- ▫️ AWS Security Token Service
- ▫️ AWS Identity and Access Management (IAM)
- ▫️ AWS Managed Microsoft AD

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Cognito User Pools»**

Cognito User Pools — захищений масштабований каталог користувачів. Він підтримує вхід через сторонніх identity providers, наприклад соцмережі, тому підходить під вимогу.

**❌ Неправильні варіанти:**
- *AWS STS* — видає тимчасові облікові дані для IAM-користувачів чи федеративних користувачів; з реєстрацією через соцмережі це не пов'язано.
- *IAM* — керує доступом до ресурсів AWS. Федерацію підтримує, але не створений для реєстрації користувачів застосунку та соціального входу.
- *AWS Managed Microsoft AD* — керований Active Directory для корпоративних застосунків; реєстрацію через соцмережі не підтримує.

Джерела: <https://repost.aws/knowledge-center/cognito-user-pools-identity-pools>

</details>

---

<a id="t05-q52"></a>
### T05-Q52
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer has created an Amazon Cognito user pool and configured a domain for it. The Developer wants to add sign-up and sign-in pages to an app with a company logo.

What should the Developer do to meet these requirements?

- ▫️ Upload the company logo to an Amazon S3 bucket. Specify the S3 object path in the app client settings in Amazon Cognito.
- ▫️ Create a custom login page that includes the company logo and upload it to Amazon Cognito. Specify the login page in the app client settings.
- ✅ Customize the Amazon Cognito hosted web UI and add the company logo.
- ▫️ Create a REST API using Amazon API Gateway and add a Cognito authorizer. Upload the company logo to a stage in the API.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Customize the Amazon Cognito hosted web UI and add the company logo.»**

Після створення user pool і налаштування домену Cognito автоматично надає hosted web UI зі сторінками реєстрації та входу. У ньому можна додати власний логотип і змінити CSS.

ℹ️ Зараз hosted UI також називають Managed Login, і він має розширені можливості брендування.

**❌ Неправильні варіанти:**
- *REST API + Cognito authorizer + logo in stage* — REST API тут зовсім не потрібен.
- *Logo in S3, path in app client settings* — не потрібно: достатньо налаштувати hosted UI.
- *Custom login page uploaded to Cognito* — не потрібно: достатньо налаштувати hosted UI.

Джерела: <https://aws.amazon.com/premiumsupport/knowledge-center/cognito-hosted-web-ui/>

</details>

---

<a id="t06-q24"></a>
### T06-Q24
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is creating an application that must support Security Assertion Markup Language (SAML) and authentication with social identity providers. The application must also be authorized to access data in Amazon S3 buckets and Amazon DynamoDB tables.

Which AWS service or feature will meet these requirements with the LEAST amount of additional coding?

- ▫️ Amazon Cognito user pools.
- ✅ Amazon Cognito identity pools.
- ▫️ Amazon API Gateway REST API.
- ▫️ AWS AppSync GraphQL API.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Cognito identity pools.»**

Identity pools (federated identities) створюють унікальні ідентичності для користувачів і федерують їх з identity providers. Користувач отримує тимчасові облікові дані AWS з обмеженими правами для доступу до сервісів AWS. Підтримувані провайдери:
- публічні: Amazon, Facebook, Google, Apple;
- Cognito user pools;
- OpenID Connect;
- SAML;
- developer authenticated identities.

Identity pools підходять, коли потрібно автентифікувати користувача через один із цих IdP і авторизувати доступ до S3, DynamoDB тощо.

**❌ Неправильні варіанти:**
- *Cognito user pools* — дають автентифікацію, але для доступу до AWS все одно потрібен identity pool; коду буде більше.
- *AppSync GraphQL API* — API тут не потрібен, лише авторизація.
- *API Gateway REST API* — API тут не потрібен.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/cognito-user-pools-identity-pools/>

</details>

---

<a id="t06-q60"></a>
### T06-Q60
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company has deployed a new web application that uses Amazon Cognito for authentication. The company wants to allow sign-in from any source but wants to automatically block all sign-in attempts if the risk level is elevated.

Which Amazon Cognito feature will meet these requirements?

- ▫️ Case sensitive user pools.
- ▫️ Multi-factor authentication (MFA).
- ✅ Adaptive authentication.
- ▫️ Advanced security metrics.

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Adaptive authentication.»**

Adaptive authentication дозволяє user pool блокувати підозрілі входи або вимагати другий фактор, коли ризик зростає. Для кожної спроби входу Cognito обчислює оцінку ризику з урахуванням нового пристрою, місця, IP-адреси тощо. Для кожного рівня ризику можна вибрати дію:
- **Allow** — вхід без додаткового фактора;
- **Optional MFA** — другий фактор для тих, у кого він налаштований;
- **Require MFA** — другий фактор обов'язковий, а користувачі без нього блокуються;
- **Block** — блокувати всі спроби на цьому рівні ризику.

ℹ️ Зараз це частина Cognito **threat protection** (раніше «advanced security features»). Див. 07.

**❌ Неправильні варіанти:**
- *Advanced security metrics* — спроби входу і рівні ризику публікуються в CloudWatch для аналізу, але самі нічого не блокують.
- *MFA* — це не механізм блокування.
- *Case sensitive user pools* — визначає лише, чи враховується регістр імен і email.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pool-settings-adaptive-authentication.html>

</details>

---

<a id="t07-q16"></a>
### T07-Q16
*домен: офіційна мітка*

A developer wants to integrate user-specific file upload and download features in an application that uses both Amazon Cognito user pools and Cognito identity pools for secure access with Amazon S3. The developer also wants to ensure that only authorized users can access their own files and that the files are securely saved and retrieved. The files are 5 KB to 500 MB in size.

What do you recommend as the most efficient solution?

- ▫️ Integrate Amazon API Gateway with a Lambda function that validates that the given file is uploaded to S3 and downloaded from S3 only by the authorized user
- ✅ Leverage an IAM policy with the Amazon Cognito identity prefix to allow users access to just their own objects in Amazon S3
- ▫️ Use S3 Event Notifications to trigger a Lambda function that validates that the given file is uploaded and downloaded only by the authorized user
- ▫️ Use CloudFront Lambda@Edge to validate that the given file is uploaded to S3 and downloaded from S3 only by the authorized user

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Leverage an IAM policy with the Amazon Cognito identity prefix to allow users access to just their own objects in Amazon S3»**

Cognito identity pools (federated identities) створюють унікальні ідентичності для користувачів і видають їм тимчасові облікові дані AWS з обмеженими правами. Підтримувані провайдери:
- Login with Amazon, Facebook, Google, Sign in with Apple;
- Cognito user pools;
- OpenID Connect;
- SAML;
- developer authenticated identities.

Identity-based політика може дозволяти доступ лише до об'єктів із префіксом, що містить ID користувача — змінну `${cognito-identity.amazonaws.com:sub}`. Наприклад: `arn:aws:s3:::bucket/cognito/myapp/${cognito-identity.amazonaws.com:sub}/*`.

**❌ Неправильні варіанти:**
- *S3 Event Notifications + Lambda validation* — перевірка відбувається вже після завантаження, тож не запобігає запису в чужу папку.
- *API Gateway + Lambda validation* — можливо побудувати, але не оптимально, і запис у чужу папку все одно не заблоковано.
- *CloudFront + Lambda@Edge* — передбачає CloudFront, тож це зайві витрати й зайві переходи для кожного завантаження.

Джерела: <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_s3_cognito-bucket.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-integrating-user-pools-with-identity-pools.html>

</details>

---

<a id="t08-q10"></a>
### T08-Q10
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A company is developing a game for the Android and iOS platforms. The mobile game will securely store user game history and other data locally on the device. The company would like users to be able to use multiple mobile devices and synchronize data between devices.

Which service can be used to synchronize the data across mobile devices without the need to create a backend application?

- ▫️ Amazon API Gateway
- ✅ Amazon Cognito
- ▫️ AWS Lambda
- ▫️ Amazon DynamoDB

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Cognito»**

Cognito Sync зберігає дані користувача в datasets із парами key-value, прив'язаних до Cognito identity. Тож дані доступні з різних пристроїв і після повторного входу. Обмеження: dataset до 1 MB, до 20 datasets на identity.

Як працює синхронізація:
- Клієнт веде локальний кеш, тож зміни одразу доступні на пристрої, навіть офлайн.
- Виклик `synchronize` забирає зміни з сервісу і відправляє локальні зміни.
- Після цього зміни бачать інші пристрої.

⚠️ Cognito Sync застарів; AWS рекомендує AWS AppSync, зокрема для офлайн-синхронізації. Див. 07.

**❌ Неправильні варіанти:**
- *AWS Lambda* — запускає код; синхронізацію даних мобільних клієнтів не робить.
- *API Gateway* — надає API; синхронізацію не робить.
- *DynamoDB* — NoSQL-БД, сама по собі не синхронізує дані між пристроями.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/synchronizing-data.html>

</details>

---

<a id="t08-q28"></a>
### T08-Q28
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A mobile application has hundreds of users. Each user may use multiple devices to access the application. The Developer wants to assign unique identifiers to these users regardless of the device they use.

Which of the following methods should be used to obtain unique identifiers?

- ▫️ Assign IAM users and roles to the users. Use the unique IAM resource ID as the unique identifier
- ✅ Implement developer-authenticated identities by using Amazon Cognito, and get credentials for these identities
- ▫️ Create a user table in Amazon DynamoDB as key-value pairs of users and their devices. Use these keys as unique identifiers
- ▫️ Use IAM-generated access key IDs for the users as the unique identifier, but do not store secret keys

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Implement developer-authenticated identities by using Amazon Cognito, and get credentials for these identities»**

Окрім входу через Facebook, Google, Login with Amazon і Apple, Cognito підтримує **developer authenticated identities**. Користувачі реєструються й автентифікуються вашим власним процесом, а Cognito дає їм унікальні ідентичності та доступ до ресурсів AWS. У процесі беруть участь пристрій користувача, ваш бекенд автентифікації і Cognito.

**❌ Неправильні варіанти:**
- *DynamoDB user table* — потрібна додаткова логіка, і рішення складніше.
- *IAM access key IDs as identifiers* — погана практика: користувачам мобільного застосунку не видають IAM-облікові записи й ключі.
- *IAM users and roles per user* — для мобільних користувачів Cognito підходить краще.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/developer-authenticated-identities.html>

</details>

---

<a id="t08-q29"></a>
### T08-Q29
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is writing a web application that allows users to view images from an Amazon S3 bucket. The users will log in with their Amazon login, as well as Facebook and/or Google accounts.

How can the Developer provide this authentication capability?

- ▫️ Use AWS STS AssumeRole in the application code and assume a role with Get\* permissions on the S3 bucket
- ✅ Use Amazon Cognito with web identity federation
- ▫️ Use Amazon Cognito with SAML-based identity federation
- ▫️ Use AWS IAM Access/Secret keys in the application code to allow Get\* on the S3 bucket

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Amazon Cognito with web identity federation»**

Cognito identity pools (federated identities) створюють унікальні ідентичності й видають тимчасові облікові дані AWS з обмеженими правами. Підтримувані провайдери:
- Login with Amazon, Facebook, Google, Sign in with Apple;
- Cognito user pools;
- OpenID Connect;
- SAML;
- developer authenticated identities.

З отриманими обліковими даними користувачі матимуть доступ до зображень у S3.

**❌ Неправильні варіанти:**
- *SAML-based federation* — SAML використовують з корпоративними каталогами, як-от Active Directory, а не з Facebook чи Google.
- *Access/secret keys in code* — небезпечно й суперечить найкращим практикам.
- *STS AssumeRole in code* — напряму через вхід у Facebook чи Google так зробити не можна; потрібен Cognito identity pool.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html>

</details>

---

<a id="t09-q02"></a>
### T09-Q02
*домен: офіційна мітка*

Consider an application that enables users to store their mobile phone images in the cloud and supports tens of thousands of users. The application should utilize an Amazon API Gateway REST API that leverages AWS Lambda functions for photo processing while storing photo details in Amazon DynamoDB. The application should allow users to create an account, upload images, and retrieve previously uploaded images, with images ranging in size from 500 KB to 5 MB.

How will you design the application with the least operational overhead?

- ▫️ Use Cognito identity pools to create an IAM user for each user of the application during the sign-up process. Leverage IAM authentication in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key
- ▫️ Use Cognito identity pools to manage user accounts and set up an Amazon Cognito identity pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key
- ▫️ Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images as well as the image metadata in a DynamoDB table. Have the Lambda function retrieve previously uploaded images from DynamoDB
- ✅ Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Leverage Cognito user pools to manage user accounts and set up an Amazon Cognito user pool authorizer in API Gateway to control access to the API. Set up a Lambda function to store the images in Amazon S3 and save the image object's S3 key as part of the photo details in a DynamoDB table. Have the Lambda function retrieve previously uploaded images by querying DynamoDB for the S3 key»**

User pool — каталог користувачів Cognito. Користувачі входять напряму або через соцмережі (Google, Facebook, Amazon, Apple) чи SAML, і кожен має профіль, доступний через SDK. User pools дають:
- реєстрацію і вхід;
- вбудований веб-інтерфейс входу, який можна налаштувати;
- вхід через соцмережі та SAML;
- керування каталогом і профілями;
- MFA, перевірку скомпрометованих облікових даних, захист від захоплення акаунта, верифікацію телефону та email;
- власні сценарії й міграцію через Lambda triggers.

Для API Gateway створюють authorizer типу `COGNITO_USER_POOLS` і налаштовують на нього методи. Клієнт входить у user pool, отримує токен і передає його в `Authorization`.

Lambda зберігає зображення в S3, а метадані разом із S3-ключем — у DynamoDB. Отримують зображення через ту саму Lambda за ключем з DynamoDB.

**❌ Неправильні варіанти:**
- *Identity pools to manage accounts + identity pool authorizer* — identity pools не керують користувачами, і authorizer такого типу немає.
- *Identity pools create IAM users* — identity pools не створюють IAM-користувачів.
- *Images in DynamoDB* — елемент DynamoDB має ліміт 400 KB, а зображення 0,5–5 MB. До того ж зберігати зображення в DynamoDB — антипатерн.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html> · <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html> · <https://aws.amazon.com/premiumsupport/knowledge-center/cognito-user-pools-identity-pools/>

</details>

---

<a id="t10-q47"></a>
### T10-Q47
*домен: визначено автоматично (у джерелі: AWS Security, Identity, & Compliance)*

A Developer is creating a banking application that will be used to view financial transactions and statistics. The application requires multi-factor authentication to be added to the login protocol.

Which service should be used to meet this requirement?

- ✅ Amazon Cognito User Pool with MFA
- ▫️ AWS Directory Service
- ▫️ Amazon Cognito Identity Pool with MFA
- ▫️ AWS IAM with MFA

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Amazon Cognito User Pool with MFA»**

User pool — каталог користувачів Cognito. Вхід напряму або через Google, Facebook, Amazon, Apple чи SAML. User pools дають:
- реєстрацію і вхід;
- вбудований веб-інтерфейс входу, який можна налаштувати;
- вхід через соцмережі та SAML;
- керування каталогом і профілями;
- MFA, перевірку скомпрометованих облікових даних, захист від захоплення акаунта, верифікацію телефону та email;
- власні сценарії й міграцію через Lambda triggers.

MFA додає другий фактор (SMS, TOTP або email OTP). Для дуже чутливих даних — режим **Required**.

**❌ Неправильні варіанти:**
- *Identity Pool with MFA* — identity pool не автентифікує користувачів і не має MFA.
- *IAM with MFA* — IAM для облікових записів AWS, а не для користувачів застосунку.
- *Directory Service* — керований Active Directory; для веб- і мобільних застосунків краще Cognito.

Джерела: <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-mfa.html>

</details>

---

<a id="t11-q26"></a>
### T11-Q26
*домен: офіційна мітка*

You are a developer for a web application written in .NET which uses the AWS SDK. You need to implement an authentication mechanism that returns a JWT (JSON Web Token).

Which AWS service will help you with token handling and management?

- ▫️ API Gateway
- ▫️ Cognito Sync
- ▫️ Cognito Identity Pools
- ✅ Cognito User Pools

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Cognito User Pools»**

Після успішної автентифікації Cognito повертає токени user pool: ID, access і refresh за стандартом OpenID Connect. Ними дають доступ до власних серверних ресурсів або до API Gateway. **ID token** — це JWT з даними про користувача (name, email, phone_number), який можна використовувати в застосунку і для автентифікації на ресурсних серверах.

Порівняння user pools та identity pools: https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

**❌ Неправильні варіанти:**
- *API Gateway* — не видає JWT-токенів користувачам.
- *Identity Pools* — видають тимчасові облікові дані AWS, а не JWT.
- *Cognito Sync* — синхронізація даних між пристроями. ℹ️ Застарів; заміна — AppSync.

</details>

---

<a id="t12-q12"></a>
### T12-Q12
*домен: офіційна мітка*

A photo-sharing application manages its EC2 server fleet running behind an Application Load Balancer and the traffic is fronted by a CloudFront distribution. The development team wants to decouple the user authentication process for the application so that the application servers can just focus on the business logic.

As a Developer Associate, which of the following solutions would you recommend to address this use-case with minimal development effort?

- ✅ Use Cognito Authentication via Cognito User Pools for your Application Load Balancer
- ▫️ Use Cognito Authentication via Cognito User Pools for your CloudFront distribution
- ▫️ Use Cognito Authentication via Cognito Identity Pools for your CloudFront distribution
- ▫️ Use Cognito Authentication via Cognito Identity Pools for your Application Load Balancer

<details><summary>Пояснення</summary>

**✅ Правильна відповідь: «Use Cognito Authentication via Cognito User Pools for your Application Load Balancer»**

ALB може сам автентифікувати користувачів, і застосунок займається лише бізнес-логікою. Через Cognito User Pools можна входити через соцмережі (Amazon, Facebook, Google) або корпоративні ідентичності (SAML, LDAP, Microsoft AD). Для цього в правилі listener налаштовують дію `authenticate-cognito` або `authenticate-oidc`. Обидві працюють лише з HTTPS listeners.

Якщо перед ALB стоїть CloudFront, є додаткові вимоги: https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html

Порівняння user pools та identity pools: https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html

**❌ Неправильні варіанти:**
- *Identity Pools for ALB* — identity pools видають тимчасові облікові дані AWS гостям і автентифікованим користувачам, а не автентифікують.
- *User Pools for CloudFront* — напряму не інтегрується; потрібна власна Lambda@Edge, тобто більше розробки.
- *Identity Pools for CloudFront* — identity pools не автентифікують.

Джерела: <https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html> · <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> · <https://aws.amazon.com/blogs/networking-and-content-delivery/authorizationedge-using-cookies-protect-your-amazon-cloudfront-content-from-being-downloaded-by-unauthenticated-users/>

</details>

---
