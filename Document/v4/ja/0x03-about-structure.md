---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, Document Structure
document: OWASP Top Ten Proactive Controls 2024
order: 403

---

# ドキュメント構成

This document is structured as a list of security controls. The list is ordered by importance with list item number 1 being the most important:

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

## Security Controls

The description of each control has the same structure. The control itself has an unique name preceeded by the control number: **Cx: Control Name**, e.g., *C1: Implement Access Control*.

Each control has the same sections:

- **説明 (Description)**: A detailed description of the control including some best practices to consider.
- **脅威 (Threat(s)):** A threat or threats that this control counters.
- **実装 (Implementation)**: Best practices and examples to illustrate how to implement each control.
- **脆弱性の防止 (Vulnerabilities Prevented)**: List of prevented vulnerabilities or risks addressed (OWASP TOP 10 Risk, CWE, etc.)
- **参考情報 (References)**: List of references for further study (OWASP Cheat sheet, Security Hardening Guidelines, etc.)
- **ツール (Tools)**: Set of tools/projects to easily introduce/integrate security controls into your software.
