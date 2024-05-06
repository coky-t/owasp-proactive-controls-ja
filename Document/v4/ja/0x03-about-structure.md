---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, Document Structure
document: OWASP Top Ten Proactive Controls 2024
order: 403

---

# ドキュメント構成

このドキュメントはセキュリティコントロールのリストとして構成されています。リストは重要度順に並んでおり、リスト項目番号 1 が最も重要です。

* C1: アクセス制御を実装する (Implement Access Control)
* C2: 暗号を正しい方法で使用する (Use Cryptography the right way)
* C3: 信頼できないデータを検証、エスケープ、サニタイズ、パラメータ化する (Validate, Escape, Sanitize or Parameterize Untrusted Data)
* C4: セキュアアーキテクチャパターンを使用する (Use Secure Architecture Patterns)
* C5: デフォルト設定を安全にする (Secure By Default Configurations)
* C6: コンポーネントを評価して更新する (Assess and Update your Components)
* C7: デジタル ID を実装する (Implement Digital Identity)
* C8: ブラウザがユーザーを守るのを支援する (Help the Browser defend its User)
* C9: セキュリティログ記録とモニタリングを実装する (Implement Security Logging and Monitoring)
* C10: サーバーサイドリクエストフォージェリを阻止する (Stop Server Side Request Forgery)

## セキュリティコントロール

それぞれのコントロールの説明は同じ構成になっています。コントロール自体には、コントロール番号が前に付く一意の名前 **Cx: コントロール名**、例 *C1: アクセス制御を実装する (Implement Access Control)* があります。

それぞれのコントロールには同じセクションがあります。

- **説明 (Description)**: 考慮すべきベストプラクティスを含むコントロールの詳細な説明。
- **脅威 (Threat(s)):** このコントロールが対抗する脅威。
- **実装 (Implementation)**: それぞれのコントロールの実装方法を説明するベストプラクティスおよび事例。
- **防止される脆弱性 (Vulnerabilities Prevented)**: 防止される脆弱性や対処されるリスクのリスト (OWASP TOP 10 Risk, CWE, など)
- **参考情報 (References)**: さらなる研究のための参考情報のリスト (OWASP Cheat sheet, Security Hardening Guidelines, など)
- **ツール (Tools)**: ソフトウェアにセキュリティコントロールを簡単に導入/統合するためのツール/プロジェクトのセット。
