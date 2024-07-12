---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C7, Implement Digital Identity
document: OWASP Top Ten Proactive Controls 2024
order: 411

---
# C7: デジタルアイデンティティを実装する (Implement Digital Identity)

## 説明

デジタルアイデンティティはオンライン取引に関わる個人、組織 (または別の主体) の固有の表現です。認証は個人やエンティティが本人であることを検証するプロセスです。

セッション管理とは、ユーザーが再認証することなくシステムを使い続けられるように、サーバーがユーザーの認証状態を維持するプロセスです。
デジタルアイデンティティ、認証、セッション管理は非常に複雑なトピックです。ここではデジタルアイデンティティのトピックの表面だけを取り上げています。最も有能なエンジニアリング人材がほとんどのアイデンティティソリューションに関わる複雑さを維持する責任を負うようにします。
[NIST Special Publication 800-63B: Digital Identity Guidelines (Authentication and Lifecycle Management)](https://pages.nist.gov/800-63-3/sp800-63b.html) は、デジタルアイデンティティ、認証、セッション管理の実装に関する確かなガイダンスを提供しています。以下は、強力なデジタルアイデンティティコントロールをアプリケーションに実装されるようにするための、安全な実装に関する推奨事項です。

### 認証保証レベル

NIST 800-63b は認証保証レベル (Authentication Assurance Level, AAL) と呼ばれる三つの認証保証レベルについて説明している。

- **レベル 1 : パスワード**: 最初のレベルである AAL レベル 1 は PII やその他のプライベートデータを含まない低リスクのアプリケーションに予約されている。AAL レベル 1 では、通常、パスワード (あなたが知っているもの) を使用する単一要素認証のみが要求されます。パスワード (または一般的なクレデンシャル) のセキュリティは最も重要であり、これは安全なストレージ (鍵導出機能などを使用) と、安全なパスワードリセットフローなどの、対応するプロセスの両方を含みます。
- **レベル 2 : 多要素認証**: NIST 800-63b AAL レベル 2 は「自己申告された PII またはオンラインで利用可能なその他の個人情報」を含む、より高リスクのアプリケーションに予約されています。AAL レベル 2 では、OTP やその他の形式の多要素実装を含む多要素認証が要求されます。
- **レベル 3 : 暗号ベースの認証**: NIST 800-63b 認証保証レベル 3 (AAL3) は、危殆化したシステムの影響が個人の損害、重大な金銭的損失、公共の利益の損害、民事または刑事上の違反につながる可能性がある場合に要求されます。AAL3 は「暗号プロトコルによる鍵の所有証明に基づく」認証を要求します。このタイプの認証は最も強力なレベルの認証保証を実現するために使用されます。これは通常、ハードウェア暗号モジュールを通じて行われます。ウェブアプリケーションを開発する場合、これは一般的に WebAuthn や PassKeys につながります。

#### レベル 2 : 多要素認証

NIST 800-63b AAL レベル 2 は「自己申請された PII またはオンラインで利用可能なその他の個人情報」を含む、より高リスクのアプリケーションに予約されています。AAL レベル 2 では、OTP やその他の形式の多要素実装を含む多要素認証が要求されます。
多要素認証 (MFA) は以下を組み合わせて本人確認を要求することで、ユーザーが本人であることを確保します。

- あなたが知っているもの (Something you know) – パスワードや PIN
- あなたが持っているもの (Something you own) – トークンやスマホ。スマホを使用する場合には FIDO2 などの標準化されたプロトコルに従った標準認証アプリケーションを使用してください。
- あなた自身であるもの (Something you are) – 指紋などの生体認証
パスワードを唯一の要素として使用すると、セキュリティが弱くなります。多要素認証ソリューションは、攻撃者がサービスを認証するために複数の要素を取得することを要求するため、より堅牢なソリューションを提供します。
生体認証は、認証の単一要素として採用される場合、デジタル認証に許容されるシークレットとはみなされないことに留意してください。生体認証は、本人の許諾の有無にかかわらずオンラインやカメラ付きスマホで人物の写真を撮影したり (顔画像など)、本人が触れたものから採取したり (潜伏している指紋など)、高解像度画像からキャプチャしたり (虹彩パターンなど) できます。生体認証は、物理的な認証子 (あなたが持っているもの) を使用する多要素認証の一部としてのみ使用しなければなりません。たとえば、多要素ワンタイムパスワード (OTP) デバイスにアクセスすると、ワンタイムパスワードを生成し、ユーザーが検証器に対して手動で入力します。

#### レベル 3 : 暗号ベースの認証

NIST 800-63b 認証保証レベル 3 (AAL3) は、危殆化したシステムの影響が個人の損害、重大な金銭的損失、公共の利益の損害、民事または刑事上の違反につながる可能性がある場合に要求されます。AAL3 は「暗号プロトコルによる鍵の所有証明に基づく」認証を要求します。このタイプの認証は最も強力なレベルの認証保証を実現するために使用されます。これは通常、ハードウェア暗号モジュールを通じて行われます。ウェブアプリケーションを開発する場合、これは一般的に WebAuthn や PassKeys につながります。

### セッション管理: クライアントサイドセッションとサーバーサイドセッション

HTTP はそれ自体がセッションレスプロトコルです。つまり、リクエスト間でデータが共有されません。私たちがどのようにウェブを使用しているかを見てみると、たとえばウェブサイトにログインすると、後続のリクエストの間もログインを維持するため、ユーザーに見えるものではないことが明らかです。これはセッション管理が HTTP 上に実装されているから可能なのです。
最初のユーザー認証が成功すると、アプリケーションはこの認証状態を追跡して、一定時間維持することを選択できます。これにより、ユーザーはリクエストごとに再認証する必要なくアプリケーションを使い続けることができます。このユーザー状態の追跡をセッション管理と呼びます。
セッション管理はクライアントサイドとサーバーサイドのセッション管理に大別できます。前者では、すべてのセッションデータをクライアント内に保存し、リクエストごとにサーバーに送信します。後者では、セッション固有のデータをサーバー、たとえばデータベースなど、に保存し、識別子のみをクライアントに送信します。それから、クライアントはリクエストごとにセッション識別子のみを送信し、サーバーはサーバーサイドストレージからセッションデータを取り出します。

セキュリティの観点からみると、サーバーサイドセッションには複数の利点がある。
- データはクライアントに直接保存されません。これは、機密データを扱う場合などに問題となる可能性があります。さらに、クライアントサイドセッション管理ソリューションではクライアントサイドデータが改竄されていないことを確保しなければなりません。
- クライアントとサーバー間で送信されるデータが少なくなります (ネットワーク帯域幅が増加したため、それほど重要ではありません)
- サーバーサイドセッション管理はセッションの無効化を可能にします。たとえば、ユーザーはすべてのセッションからログアウトできます。
デフォルトでは、常にサーバーサイドセッション管理を使用しましょう。

## 実装

### パスワードの使用について

#### パスワードの要件
パスワードは少なくとも以下の要件に準拠すべきです。
- 多要素認証 (MFA) やその他のコントロールも使用する場合、少なくとも 8 文字の長さにします。MFA が不可能な場合、少なくとも 10 文字に増やすべきです
- 記録するシークレットには、表示可能な ASCII 文字とスペース文字を許容すべきです
- 長いパスワードやパスフレーズの使用を推奨します
- 複雑さの要件は効果が限定的であることが判明しているため削除します。その代わりに、MFA やより長いパスワード長の採用をお勧めします
- 使用するパスワードが、過去の侵害ですでに漏洩したことがある一般的に使用されるパスワードではないことを確認します。上記の長さ要件を満たし、侵害されたパスワードリストにある最も一般的な上位 1000 または 10000 のパスワードをブロックすることを選択できます。次のリンクには最も一般的に見られるパスワードがあります: <https://github.com/danielmiessler/SecLists/tree/master/Passwords>
- パスワードのローテーションを強制して、同じパスワードが非常に長期間使用されることによる潜在的な侵害を避けます

#### Implement Secure Password Recovery Mechanism

It is common for an application to have a mechanism for a user to gain access to their account in the event they forget their password. A good design workflow for a password recovery feature will use multi-factor authentication elements. For example, it may ask a security question - something they know, and then send a generated token to a device - something they own.
Please see the [Forgot_Password_Cheat_Sheet](https://www.owasp.org/index.php/Forgot_Password_Cheat_Sheet) and [Choosing_and_Using_Security_Questions_Cheat_Sheet](https://www.owasp.org/index.php/Choosing_and_Using_Security_Questions_Cheat_Sheet) for further details.

#### Implement Secure Password Storage

In order to provide strong authentication controls, an application must securely store user credentials. Furthermore, cryptographic controls should be in place such that if a credential (e.g., a password) is compromised, the attacker does not immediately have access to this information. Please see the [OWASP Password Storage Cheat Sheet](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet) for further details.

### Server-Side Session-Management

Typically server-side session management is implemented with HTTP cookies which are used to store a session-identifier. When a new session is requested, the server generates a new session-identifier and transmits it to the client (browser). On each subsequent request, the session-identifier is transmitted from the client to the server, and the server uses this session-identifier to lookup session-data within a server-side database.

#### Session Generation and Expiration

User state is tracked in a session. This session is typically stored on the server for traditional web based session management. A session identifier is then given to the user so the user can identify which server-side session contains the correct user data. The client only needs to maintain this session identifier, which also keeps sensitive server-side session data off of the client.
Here are a few controls to consider when building or implementing session management solutions:
- Ensure that the session id is long, unique and random, i.e., is of high entropy.
- The application should generate a new session during authentication and re-authentication.
- The application should implement an idle timeout after a period of inactivity and an absolute maximum lifetime for each session, after which users must re-authenticate. The length of the timeouts should be inversely proportional with the value of the data protected.

Please see the [Session Management Cheat Sheet](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet) further details. ASVS Section 3 covers additional session management requirements.

### Client-Side Session-Management

Server-side sessions can be limiting for some forms of authentication. "Stateless services" allow for client side management of session data for performance purposes so the server has less of a burden to store user sessions.
These "stateless" applications typically generate a short-lived access token containing all of the current user’s access permissions which is then included in all subsequent requests. Cryptography must be employed so that the client cannot alter the permissions stored within the token. When a client requests a server operation, the client includes the retrieved access token and the server verifies that the token has not been tampered with and extracts the permissions from the token. These permissions are then used for subsequent permission checks.

#### JWT (JSON Web Tokens)

JSON Web Token (JWT) is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted as long as it is digitally signed by a trusted authority. A JWT token is created during authentication and is verified by the server (or servers) before any processing. However, JWTs are often not saved by the server after initial creation. JWTs are typically created and then handed to a client without being saved by the server in any way. The integrity of the token is maintained through the use of digital signatures so a server can later verify that the JWT is still valid and was not tampered with since its creation.
This approach is both stateless and portable in the way that client and server technologies can be different yet still interact.
Please note, that if you are using JWTs you have to make sure that the returned JWT is actually using one of the signing algorithms that you are using. Otherwise, an attacker could try to create a JWT signed with the NULL algorithm, use a MAC-vs-Signature confusion attack, or provide a custom JWS key for signing. When you are issuing JWTs, make double-sure that you are using a secure private key for signing the JWTs: each output JWT gives an attacker all information needed to perform an offline cracking attack, so you should rotate keys frequently too.

### Browser Cookies

Browser cookies are a common method for web applications to store session identifiers for web applications implementing standard session management techniques. Here are some defenses to consider when using browser cookies.

- When browser cookies are used as the mechanism for tracking the session of an authenticated user, these should be accessible to a minimum set of domains and paths and should be tagged to expire at, or soon after, the session’s validity period.
  - Please be aware that not explicitly stating a domain during cookie setup will use the current origin as domain. This is a sensible default.
  - Please be aware, that while stating a path during cookie setup will limit the browser to only submit the cookie if the request lies within the stated path. This protects the cookie of one application from being accessed by another application within a different path on the same server. This protection is brittle: if the “other” application has an XSS vulnerability and the attacker can introduce iframes, the “path” protection can be circumvented.
- The ‘secure’ flag should be set to ensure the transfer is done via secure channel only (TLS).
- HttpOnly flag should be set to prevent the cookie from being accessed via JavaScript.
- Adding “[samesite](https://www.owasp.org/index.php/SameSite)” attributes to cookies prevents [some modern browsers](https://caniuse.com/#search=samesite) from sending cookies with cross-site requests and provides protection against cross-site request forgery and information leakage attacks.

## 防止される脆弱性

- [A07:2021 – Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)
- [OWASP Mobile Top 10 2016-M4- Insecure Authentication](https://owasp.org/www-project-mobile-top-10/2016-risks/m4-insecure-authentication)

## 参考情報

- [OWASP Cheat Sheet: Authentication](https://www.owasp.org/index.php/Authentication_Cheat_Sheet)
- [OWASP Cheat Sheet: Password Storage](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet)
- [OWASP Cheat Sheet: Forgot Password](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet)
- [OWASP Cheat Sheet: Choosing and Using Security Questions](https://www.owasp.org/index.php/Choosing_and_Using_Security_Questions_Cheat_Sheet)
- [OWASP Cheat Sheet: Session Management](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet)
- [NIST Special Publication 800-63 Revision 3 - Digital Identity Guidelines](https://pages.nist.gov/800-63-3/sp800-63-3.html)

## ツール

- Daniel Miessler: [Most commonly found passwords](https://github.com/danielmiessler/SecLists/tree/master/Passwords)
