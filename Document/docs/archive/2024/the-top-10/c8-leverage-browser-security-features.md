# C8: ブラウザのセキュリティ機能を活用する (Leverage Browser Security Features)

## 説明

ブラウザはほとんどのユーザーにとってウェブへの入り口です。そのため、堅牢なセキュリティ対策を採用して、さまざまな脅威からユーザーを守ることが重要です。このセクションではブラウザのセキュリティを強化するために実装できる技法とポリシーを説明します。

現在、従来のウェブブラウザに焦点を当てていますが、モバイルアプリ、API クライアントからスマートテレビまで、他のクライアントプログラムの世界も多様であることに注意してください。クライアントでどのようなクライアントサイドセキュリティ機能がサポートされているかを検証し、それぞれの HTTP ヘッダを使用して設定することをお勧めします。

### 補強型セキュリティとブラウザサポート

ウェブブラウザにセキュリティ対策を実施するように指示することは常に場当たり的です。ウェブアプリケーションはブラウザが与えられたガイダンスに従うかどうかを検証できません。したがって、これらのセキュリティ対策は常に、攻撃者のライフをさらに複雑にする追加の (およびオプションの) **強化策** とみなすべきです。

さらに、ウェブブラウザはウェブアプリケーションが提供するセキュリティガイダンスを実際にサポートしなければなりません。サポートのレベルはブラウザやそのバージョンによって異なります。<https://caniuse.com> のようなウェブサイトを使用して、どのウェブブラウザ (のバージョン) がどの機能をサポートしているかを確認できます。サポートされるセキュリティ機能は時間の経過とともに変更される可能性があります。たとえば、X-XSS-Protection ヘッダはすべての主要なブラウザから削除されています。Referrer-Policy に見られるように、ブラウザのデフォルトの動作は時間の経過とともに変化します。また、X-Content-Type-Options で見られるように、既存ヘッダのセマンティクスさえも変化する可能性があります。

ブラウザの機能セットの変化が問題になることもありますが、一般的に新しいブラウザはより多くのセキュリティ機能を提供します。デフォルトで有効になっていることもあります。これらのセキュリティヘッダを明示的に設定することで、さまざまなブラウザの動作を統一でき、メンテナンスの労力を軽減できます。

完全に侵害されたブラウザはセキュリティガイダンスに従わないかもしれませんが、攻撃者がブラウザを完全にコントロールできたとすると、セキュリティガイダンスを無視するよりもはるかに大きな損害をもたらす攻撃経路をすでに有しています。

## 脅威

- 攻撃者は不適切な Content Security Policy 設定を悪用してクロスサイトスクリプティング (XSS) を実行し、ウェブページに悪意のあるスクリプトを挿入する可能性があります。
- 攻撃者は X-Frame-Options の欠落を利用してクリックジャッキング攻撃を実行し、ユーザーを騙して偽装されたウェブ要素と意図しないやり取りをさせる可能性があります。
- 適切な Referrer-Policy が設定されていない場合、攻撃者は `Referer` HTTP ヘッダを通じて機密情報を収集し、プライベートデータやユーザーアクティビティを開示する可能性があります。
- 攻撃者は X-Content-Type-Options ヘッダがない場合に MIME タイプ混同の脆弱性を悪用して、無害なファイルタイプに偽装した悪意のあるスクリプトを実行する可能性があります。
- 攻撃者は安全でない Cookie 設定を悪用してユーザーセッションをハイジャックし、ユーザーアカウントへの認可されていないアクセスを得る可能性があります。
- 攻撃者は適切な DNS ピン留めがない場合に DNS リバインディング攻撃を実行して、同一オリジンポリシー制限をバイパスする可能性があります。
- 攻撃者はクロスオリジンリソース共有 (CORS) の設定ミスを悪用してリソースへの認可されていないアクセスを得て、データの機密性と完全性を損なう可能性があります。

## 実装

一般的に、ウェブアプリケーションがウェブブラウザにセキュリティについて指示するために使用できる方法 (セキュリティヘッダ特有の方法) は二つあります。HTTP ヘッダと HTML タグです。

セキュリティディレクティブが複数回指定されている場合の動作はセキュリティヘッダによって異なります。たとえば、重複する X-Frame-Options ヘッダはその保護を無効にしますが、重複する Content-Security-Policy ヘッダはより厳格なポリシーを適用してそのセキュリティを強化します。
以下は潜在的な堅牢化メカニズムのリストですが、網羅はしていません。

### ブラウザを設定して情報漏洩を防ぐには

情報漏洩はブラウザが暗号化されていないチャネル (HTTPS ではなく HTTP) で情報を送信したり、そもそも過剰な情報を (Referer ヘッダなどを通じて) 送信する場合に発生します。以下のメカニズムは情報漏洩の可能性を低減します。

- **HTTP Strict Transport Security (HSTS)**: ブラウザが HTTPS でのみウェブサイトに接続するようにし、SSL ストリッピング攻撃を防ぎます。
- **Content Security Policy (CSP)**: CSP ポリシーは HTTP 接続を自動的に HTTPS にアップグレードするようブラウザに指示できます。さらに、'form-src' ディレクティブのようなディレクティブを使用して、フォームが外部サイトにデータを送信することを防ぐことができます。
- **Referrer-Policy**: ページ間を移動する際、ブラウザの HTTP リクエストは送信リクエスト内に現在の URL を含みます。この URL は機密情報を含むことがあります。Referrer-Policy を使用すると、ウェブサイトはブラウザの動作を統一して、ウェブサイト間でどの情報を送信するか選択できます。
- クッキーの **secure** フラグ: HTTP ヘッダではありませんが、このセキュリティフラグは情報漏洩に関連しています。設定した場合、ウェブブラウザは暗号化していない HTTP トランスポート経由でクッキーを送信しません。

### XSS の潜在的な影響を軽減するには

JavaScript ベースの XSS 攻撃は、何十年もの間、非常に一般的でした。脆弱性の潜在的な影響を軽減するために、ブラウザは XSS 攻撃の潜在的な影響を軽減する豊富な防御メカニズムを提供しています。

- **Content Security Policy (CSP)**: CSP はクロスサイトスクリプティング (XSS) やデータインジェクションなどのさまざまな攻撃を防ぐのに役立つ強力なツールです。Strict CSP ポリシーはインライン JavaScript とスタイルを効果的に無効化できるため、攻撃者が悪意のあるコンテンツを注入することをはるかに困難にします。
    **ホスト許可リスト CSP**: すべてのサードパーティ JavaScript をブロックすることで、攻撃対象領域を大幅に減らし、サードパーティライブラリの脆弱性の悪用を防止できます。
    **Strict CSP**: 'script-src' ディレクティブで nonce またはハッシュを使用する CSP ("strict CSP" と呼ばれることが多い) は XSS 脆弱性に対する強力な緩和策を提供します。オプションとして、CSP の 'strict-dynamic' キーワードを使用することで、strict CSP の実装を効率化し、必要に応じてサードパーティの JavaScript ライブラリとの互換性を確保できます。
    **Trusted Types**: これは安全なデータ型のみを DOM に挿入できるようにすることで、DOM ベースのクロスサイトスクリプティング脆弱性を防ぐのに役立つブラウザ API です。
- クッキーの **httpOnly** フラグ: HTTP ヘッダではありませんが、このフラグを設定すると JavaScript がこのクッキーにアクセスできなくなるため、特にセッションクッキーでは設定すべきです。

### クリックジャッキングを防ぐには

クリックジャッキングは、UI リドレス攻撃とも呼ばれ、悪意のあるサイトを無害なサイトの上に重ねることで、ユーザーを混乱させようとします。ユーザーは無害なサイトとやり取りしていると思い込んでいますが、実際には悪意のあるサイトとやり取りしています。

- **X-Frame-Options (XFO)**: コンテンツが他のサイトに埋め込まれないようにすることで、クリックジャッキング攻撃を防止します。このヘッダは扱いが難しく、たとえば二回使用すると無効になります。
- **Content Security Policy (CSP)**: さまざまな frame-\* ディレクティブにより、どのサイトが現在のウェブサイトを含むことができるか、他のどのサイトが現在のウェブサイト内に含められるかを細かく制御できます。

### ブラウザの高度な機能をコントロールするには

現代のブラウザは HTML コードを表示するだけでなく、ウェブカメラ、マイク、USB デバイスなどの複数のシステムコンポーネントとのインタフェースとしても使用されています。多くのウェブサイトではこれらの機能を利用していませんが、攻撃者はこれらを悪用できます。

- **Permission Policy**: パーミッションポリシーを通じて、ウェブサイトは定義された機能がそのウェブサイトで使用されないことをブラウザに指示できます。たとえば、ウェブサイトはユーザーの音声をキャプチャしないことを宣言できます。攻撃者が悪意のあるコードを注入できたとしても、ウェブブラウザに音声をキャプチャするように指示することはできません。

### CSRF 攻撃を防ぐには

CSRF 攻撃はウェブブラウザとウェブサイト間の既存の信頼関係を悪用します。

- Same-Origin クッキー: クッキーを SameSite としてマークすることで、クロスオリジン情報漏洩のリスクを軽減し、クロスサイトリクエストフォージェリ攻撃に対するある程度の保護も提供できます。
- Fetch Metadata リクエストヘッダ: サーバーサイドで Fetch Metadata リクエストヘッダをチェックすることで、強力な多層防御メカニズム (リソース分離ポリシー) を導入して、CSRF などの一般的なクロスオリジン攻撃からアプリケーションを保護できます。

## 防止される脆弱性

これらのブラウザ防御を実装することで以下のようなさまざまな脆弱性を軽減できます。

- クロスサイトスクリプティング (XSS)
- クロスサイトリクエストフォージェリ (CSRF)
- クリックジャッキング
- 安全でない通信によるデータ窃取
- セッションハイジャック
- 意図しないブラウザのハードウェアアクセス (マイク、カメラなど) の悪用

## ツール

- [Web Check](https://github.com/Lissy93/web-check)
- [Security Headers](https://securityheaders.com/)
- [Mozilla Observatory](https://observatory.mozilla.org/)
- [CSP Evaluator](https://csp-evaluator.withgoogle.com/)

## 参考情報

- [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [Strict Content Security Policy](https://web.dev/articles/strict-csp)
- [Trusted Types](https://web.dev/articles/trusted-types)
- [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
- [Security Headers Quick Reference](https://web.dev/articles/security-headers)
- [Fetch Metadata Request Headers](https://www.w3.org/TR/fetch-metadata/)
- [Fetch Metadata Resource Isolation Policy](https://web.dev/articles/fetch-metadata)
- [Caniuse.com](https://caniuse.com/)
- [OWASP Cheat Sheet Series: Clickjacking Defense](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html)
- [OWASP Cheat Sheet Series: Content Security Policy](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html)
- [OWASP Cheat Sheet Series: CSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [OWASP Cheat Sheet Series: HTTP Security Response Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
