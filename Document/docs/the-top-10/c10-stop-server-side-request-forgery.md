# C10: サーバーサイドリクエストフォージェリを阻止する (Stop Server Side Request Forgery)

## 説明

インジェクション攻撃は一般的に被害者のサーバー自体をターゲットにしますが、サーバーサイドリクエストフォージェリ (SSRF) 攻撃は攻撃者に代わってサーバーにリクエストを強制的に実行させようとします。これはなぜ攻撃者にとって有益なのでしょうか？送信リクエストは被害者サーバーの ID で実行されるため、攻撃者は昇格した操作でオペレーションを実行する可能性があります。

## 脅威

この例には以下があります。
- SSRF 攻撃が DMZ 内のサーバーで可能である場合、攻撃者は境界ファイアウォールを通さずに DMZ 内の他のサーバーにアクセスできるかもしれません。
- 多くのサーバーは localhost 上で動作するローカルサービスを持ち、多くの場合、localhost として認証/認可はありません。これは SSRF 攻撃によって悪用される可能性があります。
- SSO が使用されている場合、SSRF を使用してサーバーからトークン/チケット/ハッシュを抽出できます。
- その他いろいろ

## 実装

SSRF を防ぐ方法は複数あります。
- 入力バリデーション
- 送信リクエストを作成する必要がある場合、ターゲットを許可リストと照合します
- XML を使用する場合、XEE を防ぐためにパーサーを安全に構成します
入力バリデーションを実行する際には [Unicode およびその他の文字変換](https://cheatsheetseries.owasp.org/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_Orange_Tsai_Talk.pdf) に注意してください。

## 防止される脆弱性

- [A10:2021 – Server-Side Request Forgery (SSRF)](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)

## 参考情報

- [Server-Side Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
- [A New Era of SSRF - Exploiting URL Parser in Trending Programming Languages!](https://cheatsheetseries.owasp.org/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_Orange_Tsai_Talk.pdf)

## ツール

- [SSRFmap](https://github.com/swisskyrepo/SSRFmap)
