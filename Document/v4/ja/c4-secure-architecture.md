---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C4, Use Secure Architecture Patterns
document: OWASP Top Ten Proactive Controls 2024
order: 408
permalink: /v4/ja/c4-secure-architecture

---

# C4: 最初からセキュリティに対処する (Address Security from the Start)

## 説明

When designing a new application, creating a secure architecture prevents vulnerabilties before they even become part of the application. This prevents costly repairs and repudiation problems.

There are design principles that lead to secure architectures:

- **keep it simple, stupid** (KISS): the easier an application is to understand, the easier it is to reason about its components and their interactions. This allows to reason about the application's security behavior.
- **Make it easy to do the right thing**: don't expect the user to read documentation or invest time to "do things the right way". By default the application should behave in a secure manner. To make it insecure, an explicit action by the user has to take place.
- **don't rely on obscurity**: if the only security is due to the intransparency of the application or its source code, the application is not secure at all.
- **Identify and minimize your exposed components** ("attack surface"): attackers cannot attack what's not there.
- **Design for Defense-in-Depth**: think about what happens, if a component is breached and about the potential blast radius of an attack.

## 脅威

- If the application is only protected by security-by-obscurity, an attacker that reverse-engineers the application has full permissions as soon the obfuscation is cleared-up. In addtion, an attacker is able to monitor network traffic: while the obfuscation might be performed on the code-level, the operations on the network level can easily be analyzed.
- A web-application with a complex authorization scheme is deployed. A new software developer is tasked with extending one of the components. Due to the complexity, they misconfigure the authorization scheme and an attacker is able to exploit IDOR.
- A web-application with a complex authorization scheme is deployed. A new software developer adds a new plugin to the system. The system makes it hard to do the right thing, and all security configuration must be manually added to the plugin, by-default no security measures are taken. The new developer is not configuring anything thus the new plugin introduces an IDOR into the system.
- A web-application has many components, all of which are exposed to the public internet. The resulting attack surface is massive. For example, a database management tool (e.g., phpmyadmin) is deployed. After a 0day was found in mysqladmin, the whole database was extracted. During normal use, nobody uses phpmyadmin.

## 実装

The mantra "Complexity is the enemy of enemy of security" can be seen throughout this implementation guidance.

### Design for Clarity and Transparency

Architecture should focus upon simplicity: the designed software should be only as complex as the intended user's requirements warant. Focusing upon simplicity brings multiple benefits for the created software:

- It is easier to reason about a simple system. This allows to reason about potential security impacts of changes.
- Long-term maintenance is aided through the simpler design.
- The design should focus upon transprency, i.e., the security should not depend upon security-by-obscurity.

### Make it easy to do the right thing

Two terms often heard are "security by design" and "security by default". The former implies, that the software system should be usable in a secure manner while the latter means that the initial configuration of the software system is secure.

This implies, that an system administrator has to make an explicit choice to introduce insecure configuration into the system. In constrast, the path of least resistance should always result in a secure system.

When focusing upon end-user interactions, this aspect is important for designing user interfaces and flows. When focusing upon developer interactions, develeper-facing facilities such as framework, APIs, network APIs should be designed that when using them with default values, only secure operations should occur. Think about this when designing your configuration files too

### Clearly articulate what's trusted to do what, and ensure those relationships are enforced 

Clearly articulate what's trusted to do what, and ensure those relationships are enforced, e.g., trust boundaries deliniate blast radius and are enforced by controls, such as firewalls or gateways.

Attenuate what's allowed by careful validation at each step. Go deeper with threat modeling mnemonics like stride or methodologies like stride per element.

### 公開されているコンポーネント (「攻撃対象領域」) を特定して最小化する

攻撃者がアクセスできるすべての領域を特定し、それらをレビューして、最小化するように努めます。攻撃者はそこにないものを攻撃することはできません。

さらに、最小限の操作セットのみを公開することで、長期的なメンテナンスが容易になります。

### よく知られたアーキテクチャパターンを使用する

専門家はセキュアアーキテクチャパターンと呼ばれる理解しやすい形式でベストプラクティスに関する知恵を共有しています。アーキテクチャパターンは再利用可能であり、複数のアプリケーションに適用できます。

ソリューションがパターンとみなされるには、以下の特性を備えていなければいけません。

- 第一に、セキュアアーキテクチャパターンはセキュリティ問題を解決しなければなりません。
- 第二に、セキュアアーキテクチャパターンは特定のベンダーやテクノロジに縛られてはいけません。
- 第三に、セキュアアーキテクチャパターンは脅威を軽減する方法を示さなければなりません。
- 第四に、セキュアアーキテクチャパターンは再利用を容易にするために、脅威とコントロールについて標準化された用語を使用しなければなりません。 <sup>[^footnote-1]</sup>

アーキテクチャパターンは標準的なソリューションを使用して問題を解決する方法であり、カスタムソリューションを作成するものではありません。セキュアアーキテクチャパターンは既知のセキュリティ脅威に対してレビューされ強化された標準的なソリューションです。

実装:

1. 解決すべき問題を特定します。
2. 利用可能なセキュアアーキテクチャパターンのカタログを検討します。
3. 設計にセキュアアーキテクチャパターンを選択します。
4. セキュアアーキテクチャパターンを実装します。

## 防止される脆弱性

- ビジネスロジックの欠陥: これらのパターンは複雑で見過ごされがちなビジネスロジックの脆弱性を回避するようにアプリケーションを構成する際に役立ちます。
- OWASP Top 10 2021-A04 (安全でない設計): セキュアアーキテクチャパターンは、OWASP が強調する主要な懸念事項である、安全でない設計に関連するリスクの軽減を直接のターゲットとしています。

## 参考情報

- <https://securitypatterns.io/what-is-a-security-pattern/>
- <https://owasp.org/www-pdf-archive/Vanhilst_owasp_140319.pdf>
- <https://cheatsheetseries.owasp.org/cheatsheets/Microservices_based_Security_Arch_Doc_Cheat_Sheet.html>
- <https://cheatsheetseries.owasp.org/cheatsheets/Secure_Product_Design_Cheat_Sheet.html>

## ツール

- maybe add some threat modeling tools here?

[^footnote-1]: https://securitypatterns.io/what-is-a-security-pattern/
