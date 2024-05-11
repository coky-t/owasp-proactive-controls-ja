---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C4, Use Secure Architecture Patterns
document: OWASP Top Ten Proactive Controls 2024
order: 408
permalink: /v4/ja/c4-secure-architecture

---

# C4: セキュアアーキテクチャパターンを使用する (Use Secure Architecture Patterns)

## 説明

専門家はセキュアアーキテクチャパターンと呼ばれる理解しやすい形式でベストプラクティスに関する知恵を共有しています。アーキテクチャパターンは再利用可能であり、複数のアプリケーションに適用できます。

ソリューションがパターンとみなされるには、以下の特性を備えていなければいけません。
- 第一に、セキュアアーキテクチャパターンはセキュリティ問題を解決しなければなりません。
- 第二に、セキュアアーキテクチャパターンは特定のベンダーやテクノロジに縛られてはいけません。
- 第三に、セキュアアーキテクチャパターンは脅威を軽減する方法を示さなければなりません。
- 第四に、セキュアアーキテクチャパターンは再利用を容易にするために、脅威とコントロールについて標準化された用語を使用しなければなりません。 [^footnote-1]

アーキテクチャパターンは標準的なソリューションを使用して問題を解決する方法であり、カスタムソリューションを作成するものではありません。セキュアアーキテクチャパターンは既知のセキュリティ脅威に対してレビューされ強化された標準的なソリューションです。

## 実装

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



[^footnote-1]: https://securitypatterns.io/what-is-a-security-pattern/
