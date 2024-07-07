---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C3, Validate all Input and Handle Exceptions
document: OWASP Top Ten Proactive Controls 2024
order: 407
permalink: /v4/ja/c3-validate-all-input

---

# C3: すべての入力を検証して例外を処理する (Validate all Input & Handle Exceptions)

## 説明

入力バリデーションは、適切にフォーマットされたデータのみがソフトウェアシステムコンポーネントに入ることを確保するプログラミング技法です。インジェクション攻撃がクライアントをターゲットにしている場合 (たとえば JavaScript ベースの攻撃など)、ウェブサーバーは攻撃者が提供したデータをクライアントに転送する前にクォーティングやエンコーディングを実行できます。

インジェクション攻撃は、アプリケーションがデータ入力を実行可能なコマンドと混同して入力バリデーションを忘れたり間違って実装した場合に発生します。たとえば、ウェブアプリケーションがユーザーからの入力として電子メールアドレスを受け付けるとします。電子メールアドレスは期待される「データ」でしょう。そのとき攻撃者はアプリケーションが間違えてこの (想定した) データをコマンドとして実行する方法を探しています。インジェクション攻撃はそれぞれ異なる領域をターゲットとします。

- 攻撃者がアプリケーションを騙してユーザー入力 (データ) を SQL コマンド (またはその一部) として解釈させると、SQL インジェクション攻撃となります。注入されたコマンドはデータベースサーバー内で実行します。
- リモートコマンドインジェクション (RCE) は、アプリケーションがユーザーデータとウェブアプリケーションサーバーやホストで実行するコマンドを混同した場合に発生します。サーバーサイドテンプレートインジェクションはアプリケーションサーバー内で実行されるインジェクションのもう一つの例です。
- Javascript インジェクションが発生すると、ウェブアプリケーションはユーザーデータを受け入れますが、そのデータをコードとして実行するように強制されます。注入された javascript コードは一般的に他のユーザーのウェブブラウザ内で実行されるため、ウェブサーバーを直接攻撃するのではなく他のユーザーを攻撃します。

### 構文的妥当性と意味的妥当性

アプリケーションは何らかの方法でデータを使用する (ユーザーに表示することを含む) 前に、データが **構文的** かつ **意味的** に (この順序で) 妥当であることをチェックすべきです。

- **構文的妥当性** はデータが期待どおりの形式であることを意味します。たとえば、アプリケーションはユーザーが何らかの操作を行うために四桁の「アカウント ID」を選択できるかもしれません。アプリケーションは、ユーザーが SQL インジェクションペイロードを入力していると仮定し、(適切なクエリパラメータ化を利用することに加えて) ユーザーが入力したデータの長さが正確に四桁であり、数字だけで構成されていることをチェックすべきです。

- **意味的妥当性** はアプリケーションの機能とコンテキストの許容範囲内でのみ入力を受け付けることを含みます。たとえば、日付範囲を選択する場合、開始日は終了日より前でなければなりません。

## 実装

インジェクション攻撃に対する保護は、一般的に多層防御アプローチに基づき、入力フィルタリング、出力エスケープ、堅牢化メカニズムの利用を組み込んでいます。前者二つは実装されるセキュリティ対策にのみ依存し、後者は主にクライアントサポートに依存します。たとえば、XSS に対する保護では、入力から XSS をフィルタリングし、出力データをサーバーサイドでエスケープすることで、使用するウェブブラウザに関係なく XSS を防ぎます。Content-Security-Policy を追加することで XSS を防ぎますが、ユーザーのブラウザがサポートしている場合に限ります。このため、セキュリティはオプションの堅牢化対策だけに依存してはいけません。

### 悪意のあるデータがシステムに侵入するのを防ぐ

提供されたデータを信用してはいけません。すべてのデータに悪意のあるパターンがないかスクリーニングするか、さらに良い方法として、すべてのデータを許可リストと照合します。

#### 許可リストと拒否リスト
構文バリデーションを実行するには、一般に許可リストと拒否リストとして知られている、二つの一般的なアプローチがあります。

- 拒否リスト処理または **拒否リストバリデーション** は、与えられたデータが「既知の不正」コンテンツを含まないことをチェック試行します。たとえば、ウェブアプリケーションは XSS を防ぐために &lt;SCRIPT&gt; というテキストを含む入力をブロックするかもしれません。しかし、この防御は小文字の script タグや大文字小文字が混在する script タグで回避されるかもしれません。
- 許可リスト処理または **許可リストバリデーション** は、与えられたデータが一連の「既知の正しい」ルールにマッチするかどうかをチェック試行します。たとえば、米国の州の許可リストバリデーションルールは、有効な米国の州のうち一つだけである 2 文字のコードになるでしょう。
許可リスト処理は推奨される最小限のアプローチです。拒否リスト処理はエラーになりやすく、さまざまな回避技法でバイパスされる可能性があり、それ自体に依存する場合には危険です。拒否リスト処理は回避されることがよくありますが、明らかな攻撃を検出するのに役立つことがあります。つまり、許可リスト処理はデータが正しい構文的妥当性と意味的妥当性を持つことを確保することで攻撃対象領域を制限するのに役立つ一方で、拒否リスト処理は明らかな攻撃を検出して、潜在的に阻止するのに役立ちます。

#### クライアントサイドバリデーションとサーバーサイドバリデーション

セキュリティのために入力バリデーションは常にサーバーサイドで実行します。クライアントサイドバリデーションは機能面でもセキュリティ面でも役立ちますが、簡単にバイパスされます。したがって、クライアントサイドバリデーションはユーザビリティのために行われますが、アプリケーションのセキュリティはそれに依存してはいけません。たとえば、JavaScript バリデーションは特定のフィールドが数字で構成されなければならないことをユーザーに警告するかもしれません。それでも、サーバーサイドアプリケーションは送信されたデータがその機能に適した数値範囲の数字のみで構成していることを確認しなければなりません。クライアントサイドとサーバーサイドの両方のバリデーションを使用するもう一つの利点は、サーバーサイドバリデーションの警告がログ記録され、クライアントサイドバリデーションがバイパスした、潜在的なハッカーの操作を通知できることです。

#### 正規表現

正規表現はデータが特定のパターンにマッチするかどうかをチェックする方法を提供します。基本的な例から始めましょう。
以下の正規表現はユーザー名を検証するための許可リストルールを定義します。

~~~ regex
^\[a-z0-9_\]{3,16}$
~~~

この正規表現は小文字、数字、アンダースコア文字のみを許可します。また、ユーザー名は 3 文字から 16 文字の長さに制限されています。

注意: サービス拒否の可能性

正規表現を作成する際には注意が必要です。設計が不十分な表現は潜在的なサービス拒否状態 (別名 [ReDoS](https://www.owasp.org/index.php/Regular_expression_Denial_of_Service_-_ReDoS) ) を引き起こすかもしれません。さまざまなツールをテストして、正規表現が ReDoS に対して脆弱でないことを検証できます。

注意: 複雑性

正規表現はバリデーションを実行する方法の一つにすぎません。正規表現は開発者によっては保守や理解が難しいことがあります。他のバリデーション方法としてプログラムでバリデーションメソッドを記述するものがあり、開発者によっては保守が簡単になるかもしれません。

#### 予期せぬユーザー入力 (マスアサインメント)

フレームワークの中には HTTP リクエストパラメータをアプリケーションで使用されるサーバーサイドオブジェクトに自動バインディングすることをサポートしているものがあります。この自動バインディング機能によって、変更されることを意図していないサーバーサイドオブジェクトを攻撃者が更新できる可能性があります。攻撃者はこの機能によってアクセス制御レベルを変更したり、アプリケーションの意図したビジネスロジックを回避できる可能性があります。
この攻撃には、マスアサインメント、自動バインディング、オブジェクトインジェクションなど、さまざまな名前があります。
簡単な例として、ユーザーオブジェクトにアプリケーションにおけるユーザーの権限レベルを指定する privilege フィールドがある場合、悪意のあるユーザーはユーザーデータが変更されるページを探し、送信される HTTP パラメータに privilege=admin を追加できます。自動バインディングが安全でない方法で有効になっている場合、ユーザーを表すサーバーサイドオブジェクトがそれに応じて変更されます。

これを対処するには、以下の二つのアプローチがあります。

- 入力を直接バインドすることは避け、代わりにデータ転送オブジェクト (DTO) を使用します。
- 自動バインディングを有効にしますが、各ページまたは機能の許可リストルールを設定して、どのフィールドが自動バインドできるかを定義します。

より多くの例が [OWASP Mass Assignment Cheat Sheet](https://www.owasp.org/index.php/Mass_Assignment_Cheat_Sheet) にあります。

### 入力バリデーションの限界

複雑な入力フォームの中には「有効」であっても危険なものがあるため、入力バリデーションは必ずしもデータを「安全」にするわけではありません。たとえば、有効な電子メールアドレスに SQL インジェクション攻撃を含むことや、有効な URL にクロスサイトスクリプティング攻撃を含むことがあります。クエリパラメータ化やエスケープなど、入力バリデーション以外の追加の防御策を常にデータに適用する必要があります。

### データとコマンドの分離を維持するメカニズムを使用する

悪意のあるデータが入力チェックを通過したとしても、アプリケーションはそれらの悪意のあるデータをコマンドやコードとして実行しないことでインジェクション攻撃を防ぐことができます。この目標を達成するには複数の対策がありますが、そのほとんどはテクノロジに依存します。例を以下に示します。

- SQL を介してリレーショナルデータベースを使用する際、プリペアドステートメントを利用します。SQL インジェクション攻撃は一般的に、攻撃者が文字列連結によって作成された SQL コマンドから「エスケープ」する入力データを提供できる場合に発生します。プリペアドステートメントを使用することで、コンピュータは入力データをコマンドテンプレートから「エスケープ」できないないように自動的にエンコードできます。
- ORM を使用する際には、オブジェクトが SQL コマンドにどのようにマップされるかを把握してください。その間接層が一般的な SQLi を防ぐかもしれませんが、特別に準備された攻撃は依然として実行可能です。
- サーバーサイドテンプレートインジェクション (SSTI) はサーバーサイドのテンプレートエンジンを使用して、ユーザーに表示されるコンテンツを動的に生成します。SSTI エンジンでは多くの場合、サンドボックスのコンフィギュレーション、つまり限られた数のメソッドの実行のみを許可します。
- ユーザー入力をパラメータとしてシステムコマンドを実行すると、インジェクション攻撃を受けやすくなります。可能であれば、これを避けるべきです。

#### JavaScript インジェクション攻撃

特殊なケースとして JavaScript ベースのインジェクション攻撃 (XSS) があります。注入された悪意のあるコードは一般的に被害者のブラウザ内で実行されます。通常、攻撃者はブラウザからユーザーのセッション情報を盗もうとしますが、(サーバーサイドで行うような) コマンドを直接実行しようとはしません。サーバーサイドの入力フィルタリングと出力エスケープに加えて、複数のクライアントサイド堅牢化対策を講じることができます (これらは、サーバーサイドのロジックが関与せず、悪意のあるコードをフィルタリングできない DOM ベースの XSS の特殊なケースからも保護します)。
- 機密性の高い Cookie を httpOnly でマークして、JavaScript がアクセスできないようにします
- Content-Security-Policy を利用して、JavaScript ベースの攻撃の攻撃対象領域を減らします
- Angular のようなデフォルトで安全なフレームワークを使用します

#### Validating and Sanitizing HTML

Consider an application that needs to accept HTML from users (via a WYSIWYG editor that represents content as HTML or features that directly accept HTML in input). In this situation, validation or escaping will not help.
- Regular expressions are not expressive enough to understand the complexity of HTML5.
- Encoding or escaping HTML will not help since it will cause the HTML not to render properly.

Therefore, you need a library to parse and clean HTML formatted text. Please see the [XSS Prevention Cheat Sheet on HTML Sanitization](https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet#RULE_.236_-_Sanitize_HTML_Markup_with_a_Library_Designed_for_the_Job) for more information on HTML Sanitization.
### Special Case: Validate Data During Deserialization
Some forms of input are so complex that validation can only minimally protect the application. For example, it’s dangerous to deserialize untrusted data or data that can be manipulated by an attacker. The only safe architectural pattern is to not accept serialized objects from untrusted sources or to only deserialize in limited capacity for only simple data types. You should avoid processing serialized data formats and use easier to defend formats such as JSON when possible.

If that is not possible then consider a series of validation defenses when processing serialized data.

- Implement integrity checks and encryption of the serialized objects to prevent hostile object creation or data tampering.
- Enforce strict type constraints during deserialization before object creation; typically code is expecting a definable set of classes. Bypasses to this technique have been demonstrated.
- Isolate code that deserializes, such that it runs in very low privilege environments, such as temporary containers.
- Log security deserialization exceptions and failures, such as where the incoming type is not the expected type, or the deserialization throws exceptions.
- Restrict or monitor incoming and outgoing network connectivity from containers or servers that deserialize.
- Monitor deserialization, alerting if a user deserializes constantly.

## 防止される脆弱性

- Input validation reduces the attack surface of applications and can sometimes make attacks more difficult against an application.
- Input validation is a technique that provides security to certain forms of data, specific to certain attacks and cannot be reliably applied as a general security rule.
- Input validation should not be used as the primary method of preventing [XSS](https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet), [SQL Injection](https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet) and other attacks.
- [2023 CWE Top 25 - 3 Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')](https://cwe.mitre.org/data/definitions/89.html)
- [2023 CWE Top 25 - 5 Improper Neutralization of Special Elements used in an OS Command ('OS Command Injection')](https://cwe.mitre.org/data/definitions/89.html)
- [2023 CWE Top 25 - 16 Improper Neutralization of Special Elements used in a Command ('Command Injection')](https://cwe.mitre.org/data/definitions/77.html)
- [2023 CWE Top 25 - 23 Improper Control of Generation of Code ('Code Injection')](https://cwe.mitre.org/data/definitions/94.html)

## 参考情報

Regarding Input Validation:
- [OWASP Cheat Sheet: Input Validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)
- [OWASP Cheat Sheet: iOS - Security Decisions via Untrusted Inputs](https://www.owasp.org/index.php/IOS_Developer_Cheat_Sheet#Security_Decisions_via_Untrusted_Inputs_.28M7.29)
- [OWASP Testing Guide: Testing for Input Validation](https://www.owasp.org/index.php/Testing_for_Input_Validation)
- [Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html)
- [Injection Prevention Cheat Sheet in Java](https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_in_Java_Cheat_Sheet.html)
- Hardening with CSP: [CSP with Google](https://csp.withgoogle.com/docs/index.html)
- Deploying CSP in Single Page Applications

## ツール

Helping with Input Validation:
- [OWASP Java HTML Sanitizer Project](https://www.owasp.org/index.php/OWASP_Java_HTML_Sanitizer)
- [Java JSR-303/JSR-349 Bean Validation](http://beanvalidation.org/)
- [Java Hibernate Validator](http://hibernate.org/validator/)[Apache Commons Validator](https://commons.apache.org/proper/commons-validator/)PHP’s [filter functions](https://secure.php.net/manual/en/book.filter.php)

Testing for Injection Attacks:
- Sqlmap.py
- OWASP ZAP-based scans
Helping with Hardening:
- [CSP Evaluator](https://csp-evaluator.withgoogle.com/)
