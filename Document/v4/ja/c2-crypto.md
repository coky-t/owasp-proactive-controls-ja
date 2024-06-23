---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C2, Use Cryptography the proper way
document: OWASP Top Ten Proactive Controls 2024
order: 406
permalink: /v4/ja/c2-crypto

---
# C2: 暗号を正しい方法で使用する (Use Cryptography the proper way)

## 説明

パスワード、クレジットカード番号、医療記録、個人情報、企業秘密などの機密データは、特にそのデータがプライバシー法 (EU の一般データ保護規則 GDPR)、PCI データセキュリティ標準 (PCI DSS) などの金融データ保護規則、またはその他の規制に該当する場合、特別な保護が必要です。

攻撃者はさまざまな方法でウェブアプリケーションやウェブサービスアプリケーションからデータを窃取できます。たとえば、機密情報を通信セキュリティなしでインターネット経由で送信すると、共有ワイヤレス接続上の攻撃者が他のユーザーのデータを捕捉して窃取するかもしれません。また、攻撃者は SQL インジェクションを使用して、アプリケーションデータベースからパスワードやその他のクレデンシャルを窃取し、その情報を一般に公開するかもしれません。

プライバシーとは、ある主体に関する特定の情報の機密性とその情報へのアクセスが保護されることを保証することです。開発者が構築するもののユーザーは自分の情報が開示されないように保護されることを望んでいます。

パスワード、クレジットカード番号、医療記録、個人情報、企業秘密などの機密データを保護します。

企業に対してユーザーの個人情報を保護するように強制するための規制が存在します。欧州連合は個人のプライバシーを重視しており、EU 一般データ保護規則 (GDPR) を制定しました。PCI データセキュリティ標準 (PCI DSS) などの金融データ保護規則もカード所有者のプライバシーを保護するために存在します。

暗号技術とは、平文の情報を判読不能にし、暗号化された情報を判読可能な形式に復元するための原理、手段、手法に関する技術や化学です。個々のユーザーデータは、保存時に適切に管理されるように暗号化が必要です。

#### アプリケーションのデータ型を分類する

システム内のデータを分類し、各データがどの機密レベルに属するかを判断することが重要です。そうすることで、各データカテゴリは各機密レベルに必要な保護ルールにマップできます。たとえば、機密ではない公開マーケティング情報は公開データとして分類され、公開ウェブサイトに掲載しても問題ありません。クレジットカード番号は保存時、処理時、転送時に暗号化する必要があるプライベートユーザーデータとして分類されるかもしれません。

データ分類は、たとえば欧州連合のユーザーにサービスを提供する場合の GDPR のように、法律によって義務付けられることもあります。

システムで送信、処理、保存されるデータを分類し、データがどの機密レベルに属するかを判断します。データを分類して、タイプごとに特定の保護ルールを定義します。ルールの作成により、チームはデータの最小化を実行し、可能な限り機密データを保存しないようにできます。

たとえば、機密性のない公開マーケティング情報は公開データとして分類され、公開ウェブサイトに掲載しても問題ないため、暗号化する必要はありません。クレジットカード番号は保存時、処理時、転送時に暗号化する必要があります。

## 実装

When it comes to cryptography, there are a few simple rules:

- Never transmit plain-text data. The technical capability exists to easily encrypt all data that is sent between any point A and B. Embrace the use of cryptography to protect all data at rest and in transit.
- Do not create your own cryptographic protocols. The creation of a cryptographic protocol is a tricky proposition. When NIST created AES, they had an open competition where the best cryptographers worldwide submitted proposals and then looked for flaws in the other proposals. Instead of using your developer cycles to create a new crypto protocol, use an existing, battle-tested standard. Focus your innovation on making your feature or product better.
- Do not implement cryptographic routines. Use an existing library that implements cryptographic routines.

### 保存時のデータを保護する

The first rule of sensitive data management is to avoid storing sensitive data when at all possible. If you must store sensitive data then make sure it is cryptographically protected in some way to avoid unauthorized disclosure and modification.
Cryptography (or crypto) is one of the more advanced topics of information security and one whose understanding requires the most schooling and experience. It is difficult to get right because there are many approaches to encryption, each with advantages and disadvantages that need to be thoroughly understood by web solution architects and developers. In addition, serious cryptography research is typically based on advanced mathematics and number theory, providing a serious barrier to entry.
Designing or building cryptographic algorithms is very error-prone (see side-channel attacks). Instead of building cryptographic capability from scratch, it is strongly recommended that peer-reviewed and open solutions be used, such as the Google Tink project, Libsodium, and secure storage capability built into many software frameworks and cloud services.

#### パスワードを安全に保存する

Most web applications will face the challenge of storing user’s passwords to set up authentication services. Store the passwords safely to ensure an attacker cannot quickly obtain them.
Do not store the passwords in plain text anywhere in the database. Always use a hashing function to store passwords. Enhance the hashing function by adding a random salt for each item to increase the randomness of hashes.

#### 特殊なケース: アプリケーションシークレット管理

Applications contain numerous “secrets” that are needed for security operations. These include certificates, SQL connection passwords, third party service account credentials, passwords, SSH keys, encryption keys and more. The unauthorized disclosure or modification of these secrets could lead to complete system compromise. In managing application secrets, consider the following:
Don’t store secrets in code, config files or pass them through environment variables. Use tools like GitRob or TruffleHog to scan code repos for secrets. Your code should be written in a way that even if your code would be disclosed, e.g., due to a defective configured github repository, your running applications are still secure.
Keep keys and your other application-level secrets in a secrets vault like KeyWhiz or Hashicorp’s Vault project , Amazon KMS, or AWS Secrets Manager to provide secure storage and access to application-level secrets at run-time. Many web-frameworks such as Ruby on Rails provide integrated ways of dealing with secrets and credentials.

#### 鍵のライフサイクル

Secret keys are used in applications with a number of sensitive functions. For example, secret keys can be used to sign JWTs, protect credit cards, provide various forms of authentication as well as facilitate other sensitive security features. In managing keys, a number of rules should be followed including

- Ensure that any secret key is protected from unauthorized access
- All authorized access to a secret key is logged for forensic purposes
- Store keys in a proper secrets vault as described below
- Use independent keys when multiple keys are required
- Build support for changing cryptographic algorithms to prepare for future needed changes
- Build application features to support and handle key rotation gracefully. This can happen on a periodic base or after a key has been compromised.

### 転送時のデータを保護する

Sensitive data such as passwords, credit card numbers, health records, personal information and business secrets require extra protection, particularly if that data falls under privacy laws (EU’s General Data Protection Regulation GDPR), financial data protection rules such as PCI Data Security Standard (PCI DSS) or other regulations.
Attackers can steal data from web and web service applications in a number of ways. For example, if sensitive information is sent over the internet without communications security, then an attacker on a shared wireless connection could capture and steal another user’s data. Also, an attacker could use SQL Injection to steal passwords and other credentials from an applications database and expose that information to the public.

#### 現行の暗号プロトコルを使用する

When developing web applications, use TLSv1.2 or TLSv1.3, preferably TLSv1.3. If possible, investigate the usage of HTTP/2 or HTTP/3 as they warrant the usage of security TLS versions/algorithms.
- Directly turn off other older protocols to avoid protocol downgrade attacks.
- Do not offer HTTP. Disable both HTTP and SSL compression.
- Always utilize a secure random number generator (RNG).

#### クライアントにトランスポートレベルの暗号化を実施するように指示する

Web servers can instruct web browsers to uphold minimal transport-level security:
- Use the Strict-Transport-Security Header to enforce opportunistic encryption and certificate validation checks.
- Content-Security-Policy allows for automatic client-side upgrade from HTTP to HTTPS.
- When setting cookies, always utilize the “secure” flag to prevent transmission over HTTP.

### 暗号技術は時間とともに変化する

Cryptographic recommendations change over time. To allow for this, make cryptographic choices such as used algorithms or key sizes configurable. If the application needs to support high availability, design key-rollover procedures.

## 防止される脆弱性

- <https://owasp.org/Top10/A02_2021-Cryptographic_Failures/>
- <https://mas.owasp.org/MASVS/controls/MASVS-CRYPTO-1/>

## 参考情報

- [OWASP Cheat Sheet: Transport Layer Protection](https://www.owasp.org/index.php/Transport_Layer_Protection_Cheat_Sheet)
- [Ivan Ristic: SSL/TLS Deployment Best Practices](https://www.ssllabs.com/projects/best-practices/index.html)
- [OWASP Cheat Sheet: HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet)
- [OWASP Cheat Sheet: Cryptographic Storage](https://www.owasp.org/index.php/Cryptographic_Storage_Cheat_Sheet)
- [OWASP Cheat Sheet: Password Storage](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet)
- [OWASP Cheat Sheet: IOS Developer - Insecure Data Storage](https://www.owasp.org/index.php/IOS_Developer_Cheat_Sheet#Insecure_Data_Storage_.28M1.29)
- [OWASP Testing Guide: Testing for TLS](https://www.owasp.org/index.php/Testing_for_Weak_SSL/TLS_Ciphers,_Insufficient_Transport_Layer_Protection_%28OTG-CRYPST-001%29)
- [OWASP WrongSecrets](https://github.com/OWASP/wrongsecrets) : vulnerable application with example of how to NOT use secrets

## ツール

- <https://github.com/nabla-c0d3/sslyze>
- <https://testssl.sh/>
- [SSLyze](https://github.com/nabla-c0d3/sslyze) - SSL configuration scanning library and CLI tool
- [SSLLabs](https://www.ssllabs.com/ssltest/) - Free service for scanning and checking TLS/SSL configuration
- [OWASP O-Saft TLS Tool](https://www.owasp.org/index.php/O-Saft) - TLS connection testing tool
- [GitRob](https://github.com/michenriksen/gitrob) - Command line tool to find sensitive information in publicly available files on GitHub
- [TruffleHog](https://github.com/dxa4481/truffleHog) - Searches for secrets accidentally committed
- [Hashicorp Vault](https://www.vaultproject.io/) - Secrets manager
- [Amazon KMS](https://aws.amazon.com/kms/) - Manage keys on AWS
- [AWS Secrets Manager](https://aws.amazon.com/secrets-manager) - Manage secrets on AWS
- [Azure Key Vault](https://azure.microsoft.com/en-us/products/key-vault) - Manage keys and secrets on Azure
- [Google Cloud KMS](https://cloud.google.com/security-key-management) - Manage keys on Google Cloud Platform
- [Google Secret Manager](https://cloud.google.com/secret-manager) - Manage secrets on Google Cloud Platform
