# PortSwigger Web Security Academy - Write-ups
A collection of selected web security lab write-ups documenting practical vulnerability testing, analysis, and remediation using PortSwigger Web Security Academy and Burp Suite Community Edition.
The goal of this repository is to develop and demonstrate a structured approach to web application penetration testing, from identifying potential weaknesses to validating their impact and recommending appropriate security controls.

## Areas Covered
- **Access Control:** Testing for missing authorization checks in multi-step workflows and identifying opportunities for unauthorized privilege escalation.
- **SQL Injection:** Testing for SQL injection in application inputs, bypassing input filters through XML entity encoding, and assessing the potential for unauthorized database data retrieval.
- **Server-Side Request Forgery (SSRF):** Testing for insufficient validation of server-side requests and bypassing blacklist-based input filters to access restricted internal resources.

## Methodology
Each write-up aims to document:
1. **Reconnaissance:** Understanding the relevant application functionality and request flow.
2. **Hypothesis:** Identifying a potential vulnerability based on observed behavior.
3. **Testing:** Using Burp Suite to inspect and modify HTTP requests and analyze responses.
4. **Validation:** Confirming the observed behavior and documenting evidence.
5. **Impact Assessment:** Explaining the potential security consequences within the demonstrated scenario.
6. **Remediation:** Recommending appropriate security controls and defensive practices.

## Tools & Technologies
* Burp Suite Community Edition
* HTTP request and response analysis
* Web application security testing
* OWASP web security concepts

## Lab Write-ups
### Access Control
- [Multi-Step Process with No Access Control on One Step](https://github.com/CelineAtallah/portswigger-web-security-writeups/blob/main/access-control/README.md) - Practitioner
### SQL Injection
- [SQL Injection with Filter Bypass via XML Encoding](https://github.com/CelineAtallah/portswigger-web-security-writeups/blob/main/sql-injection/README.md) - Practitioner
### Server-Side Request Forgery (SSRF)
- [SSRF with Blacklist-Based Input Filter](https://github.com/CelineAtallah/portswigger-web-security-writeups/blob/main/server-side-request-forgery/README.md) - Practitioner
