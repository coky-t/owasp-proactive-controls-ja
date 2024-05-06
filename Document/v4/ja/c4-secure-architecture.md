---

layout: col-document
tags: OWASP Top Ten Proactive Controls 2024, C4, Use Secure Architecture Patterns
document: OWASP Top Ten Proactive Controls 2024
order: 408
permalink: /v4/ja/c4-secure-architecture

---

# C4: セキュアアーキテクチャパターンを使用する (Use Secure Architecture Patterns)

## 説明

Experts have shared their wisdom about best practices in an easily digestible format called secure architecture patterns. Architecture patterns are reusable and can be applied across multiple applications.

For a solution to be considered a pattern, it must have these characteristics: 
- First, a secure architecture pattern must solve a security problem. 
- Second, a secure architecture pattern must not be tied to a specific vendor or technology. 
- Third, a secure architecture pattern must demonstrate how it mitigates threats. 
- Fourth, a secure architecture pattern must use standardized terms for threats and controls for easy reuse.<sup><sup>[\[1\]](#footnote-1)</sup></sup>. [^footnote-1]

An architecture pattern is a way to solve a problem using a standard solution versus creating a custom solution. A secure architecture pattern is a standard solution that has been reviewed and hardened against known security threats.

## 実装

1. Identify the problem that requires solving.
2. Consider the catalog of available secure architecture patterns.
3. Choose a secure architecture pattern for the design.
4. Implement the secure architecture pattern.

## 脆弱性の防止

- Business Logic Flaws: These patterns can help in structuring the application to avoid complex and often overlooked business logic vulnerabilities.
- OWASP Top 10 2021-A04 (Insecure Design): Secure architecture patterns directly target the mitigation of risks associated with insecure design, a key concern highlighted by OWASP.

## 参考情報

- <https://securitypatterns.io/what-is-a-security-pattern/>
- <https://owasp.org/www-pdf-archive/Vanhilst_owasp_140319.pdf>
- <https://cheatsheetseries.owasp.org/cheatsheets/Microservices_based_Security_Arch_Doc_Cheat_Sheet.html>
- <https://cheatsheetseries.owasp.org/cheatsheets/Secure_Product_Design_Cheat_Sheet.html>

## ツール



[^footnote-1]: https://securitypatterns.io/what-is-a-security-pattern/
