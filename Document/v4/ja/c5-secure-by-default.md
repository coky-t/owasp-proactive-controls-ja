---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C5, Secure By Default Configurations
document: OWASP Top Ten Proactive Controls 2024
order: 409

---

# C5: デフォルト設定を安全にする (Secure By Default Configurations)

## 説明

"Secure-by-Default" とは製品が追加料金なしで、箱から出してすぐに、蔓延する悪用技法に対して耐性があることを意味します。<sup><sup>[\[2\]](#footnote-2)</sup></sup> アプリケーションを最初から安全にする利点は、システムをロックダウンする方法に関して開発者の負担がなくなり、すでに安全な製品を提供していることです。これにより製品を安全な方法でデプロイするために必要な労力を軽減し、長期間にわたって製品を安全に維持するという確信が高まります。

## 実装

現代のクラウドアプリケーションでは、開発者がアプリケーションを構築する際に、コードを作成しながら、セキュリティクリティカルな構成を含むインフラストラクチャの決定を行い、アプリケーションのインフラストラクチャも構築します。
これらは、アプリケーションレベル (ウェブサーバーやデータベースなど)、コンテナ、Function as a Service、インフラストラクチャレベルで適用される構成を使用して、コード、Infrastructure-as-code (IaC)、を介して作成および構成されたインフラストラクチャにデプロイされます。たとえば、ウェブアプリケーションの場合、フォルダパーミッションは最小権限の原則に従ってリソースのアクセス権を制限する必要があります。ウェブアプリケーションとモバイルアプリケーションを運用環境にデプロイする際には、デバッグを無効にすべきです。

開発者がインフラストラクチャコンポーネントをまとめる際に、以下を行うことが重要です。
1. 最小権限の原則に基づいた構成を実装します。たとえば、クラウドストレージ (S3 など) がプライベートであり、最小限の時間でアクセスされるように構成されるようにします。
2. アクセスはデフォルトで拒否され、許可リストによって許可します。
3. パッケージやコンポーネントの脆弱性がスキャンされ、プライベートコンテナレジストリから取り出した、コンテナイメージを使用します。
4. 手作業による構成作業よりも、宣言的なインフラストラクチャ構成を優先します。低レベルでは、Infrastructure-as-Code テンプレートを利用して、クラウドおよびオンプレミスのインフラストラクチャのプロビジョニングと構成を自動化します。高レベルでは、Policy-as-Code を利用して、権限割り当てを含むポリシーを適用します。
    宣言的な形式を使用することで、これらのポリシーをソースコードと同様に管理できます。ソースコード管理システム、バージョン管理、アクセス制御管理、変更対象管理などにチェックインします。
5. トラフィックをデフォルトで暗号化するか、最初から非暗号化通信チャネルを実装しません。

### 継続的な構成の検証

ソフトウェア開発の一環として、開発者はソフトウェアがアプリケーションレベルでデフォルトで安全に構成されていることを確保する必要があります。たとえば、以下のようにします。

- インフラストラクチャを定義するコードは最小権限の原則に従う必要があります。
- アカウント、ソフトウェア、デモ機能などの必要ではない構成や機能は無効にする必要があります。

## 防止される脆弱性

- [OWASP Top 10 2021 A05 – Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)

## 参考情報

- OWASP Cheat Sheet: [Infrastructure as Code Security Cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/Infrastructure_as_Code_Security_Cheat_Sheet.html)
- OWASP ASVS: [Application Security Verification Standard V14 Configuration](https://github.com/OWASP/ASVS/blob/master/5.0/en/0x22-V14-Config.md)
- [Cloud security guidance - NCSC.GOV.UK](https://www.ncsc.gov.uk/collection/cloud-security)

## ツール

- [Tfsec](https://github.com/aquasecurity/tfsec) - Terraform テンプレートのオープンソース静的解析
- [Terrascan](https://github.com/accurics/terrascan) - Infrastructure-as-Code 脆弱性のスキャン
- [Checkov](https://github.com/bridgecrewio/checkov) - オープンソースと Infrastructure-as-Code 脆弱性のスキャン
- [Scout Suite](https://github.com/nccgroup/ScoutSuite) はオープンソースのマルチクラウドセキュリティ監査ツールであり、現在 Amazon Web Services, Microsoft Azure, Google Cloud Platform をサポートしています
- [prowler](https://github.com/toniblyx/prowler)
- [Cloudmapper](https://github.com/duo-labs/cloudmapper)
- [Snyk](https://github.com/snyk/cli) - オープンソース、コード、コンテナ、Infrastructure-as-Code の脆弱性のスキャン
- [Trivy](https://github.com/aquasecurity/trivy) - オープンソース、コード、コンテナ、Infrastructure-as-Code の脆弱性のスキャン
- [KICS](https://github.com/Checkmarx/kics) - Infrastructure-as-Code の脆弱性のスキャン
- [Kubescape](https://github.com/kubescape/kubescape) - Kubernetes 脆弱性のスキャン
- [Kyverno](https://kyverno.io/docs/security/) - ポリシーを使用した Kubernetes の保護
