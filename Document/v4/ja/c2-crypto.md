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

暗号に関しては、いくつかのシンプルなルールがあります。

- 決してプレーンテキストデータを送信してはいけません。A と B 地点の間で送信されるすべてのデータを簡単に暗号化できる技術的能力が存在します。暗号化を使用して、保存時および転送時のすべてのデータを保護します。
- 独自の暗号プロトコルを作成してはいけません。暗号プロトコルの作成は難しい課題です。NIST が AES を作成した時、世界中の優秀な暗号技術者が提案を提出し、他の提案の欠陥を探す公開コンペを行いました。開発者サイクルを使用して新しい暗号プロトコルを作成するのではなく、既存の実践テスト済みの標準を使用してください。機能や製品を改善することにイノベーションを集中しましょう。
- 暗号ルーチンを実装してはいけません。暗号ルーチンを実装する既存のライブラリを使用します。

### 保存時のデータを保護する

機密データ管理の第一のルールは、可能な限り機密データを保存しないことです。機密データを保存しなければならない場合は、不正な開示や改変を避けるために、何らかの方法で暗号化保護されていることを確認してください。
暗号技術 (または暗号化) は情報セキュリティのより高度なトピックのの一つであり、その理解には最も多くの学識と経験が必要です。暗号化には多くのアプローチがあり、それぞれに長所と短所があり、ウェブソリューションアーキテクトと開発者がそれを十分に理解する必要があるため、正しく理解するのは困難です。さらに、本格的な暗号技術の研究は、一般的に高度な数学と数論に基づいており、参入への大きな障壁となっています。
暗号アルゴリズムの設計や構築は非常にエラーを起こしやすいものです (サイドチャネル攻撃を参照) 。暗号機能をゼロから構築するのではなく、Google Tink プロジェクト、Libsodium、多くのソフトウェアフレームワークやクラウドサービスに組み込まれている安全なストレージ機能など、ピアレビュー済みのオープンなソリューションを使用することを強くお勧めします。

#### パスワードを安全に保存する

ほとんどのウェブアプリケーションは認証サービスをセットアップするためにユーザーのパスワードを保存するという課題に直面します。パスワードを安全に保存して、攻撃者がパスワードをすぐに入手できないようにします。
パスワードをデータベースのどこにもプレーンテキストで保存してはいけません。パスワードを保存するには常にハッシュ関数を使用します。各アイテムにランダムソルトを追加してハッシュ関数を強化し、ハッシュのランダム性を高めます。

#### 特殊なケース: アプリケーションシークレット管理

アプリケーションはセキュリティ運用に必要な多数の「シークレット」を持っています。これらには、証明書、SQL 接続パスワード、サードパーティのサービスアカウントクレデンシャル、パスワード、SSH キー、暗号鍵などがあります。これらのシークレットの不正な開示や変更はシステムの完全な侵害につながるかもしれません。アプリケーションシークレットを管理するには、以下を考慮します。
シークレットをコードや設定ファイルに保存したり、環境変数を介して渡したりしてはいけません。GitRob や TruffleHog などのツールを使用して、コードリポジトリをスキャンしてシークレットを探します。たとえコードが公開されたとしても、たとえば github リポジトリの設定に不備があるとしても、実行中のアプリケーションが依然として安全であるようにコードを記述すべきです。
キーや他のアプリケーションレベルのシークレットは、安全なストレージを提供する KeyWhiz や Hashicorp の Vault プロジェクト、Amazon KMS、AWS Secrets Manager などのシークレットボールトに保管し、実行時にアプリケーションレベルのシークレットにアクセスできるようにします。Ruby on Rails などの多くのウェブフレームワークではシークレットとクレデンシャルを統合的に扱う方法を提供しています。

#### 鍵のライフサイクル

秘密鍵は多くの機密性の高い機能を備えたアプリケーションで使用します。たとえば、秘密鍵は、JWT を署名したり、クレジットカードを保護したり、さまざまな形式の認証を提供したり、その他の機密性の高いセキュリティ機能を促進するために使用できます。鍵を管理するには、以下のようにいくつかのルールに従うべきです。

- すべての秘密鍵が認可されていないアクセスから保護されていることを確保します
- 秘密鍵へのすべての認可されていないアクセスはフォレンジック目的のためにログ記録します
- 下記のように適切なシークレットボールトに鍵を保管します
- 複数の鍵が必要な場合は独立した鍵を使用します
- 暗号アルゴリズムの変更に対するサポートを構築して、将来必要とされる変更に備えます
- 鍵のローテーションを適切にサポートおよび処理するアプリケーション機能を構築します。これは定期的に、あるいは鍵が侵害された後に実行できます。

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
