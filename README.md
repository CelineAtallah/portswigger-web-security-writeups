# PortSwigger Web Security Academy - Write-ups
A collection of selected web security lab write-ups documenting practical vulnerability testing, analysis, and remediation using PortSwigger Web Security Academy and Burp Suite Community Edition.
The goal of this repository is to develop and demonstrate a structured approach to web application penetration testing, from identifying potential weaknesses to validating their impact and recommending appropriate security controls.

## Areas Covered
* **Access Control:** Testing for missing authorization checks in multi-step workflows and identifying opportunities for unauthorized privilege escalation.
* **Server-Side Request Forgery (SSRF):** Testing blacklist-based input filters and investigating techniques that may allow restrictions on server-side requests to be bypassed.
* **JSON Web Tokens (JWT):** Examining JWT verification and testing how trust in attacker-controlled signing keys can lead to authentication bypass.
* **Business Logic:** Analyzing application workflows and state transitions to identify ways to bypass authentication or other intended restrictions.

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
* [Multi-Step Process with No Access Control on One Step](access-control/multi-step-process-no-access-control.md) — Practitioner

### Server-Side Request Forgery (SSRF)
* [SSRF with Blacklist-Based Input Filter](ssrf/ssrf-blacklist-filter.md) — Practitioner

### JSON Web Tokens (JWT)
* [JWT Authentication Bypass via JWK Header Injection](jwt/jwk-header-injection.md) — Practitioner

### Business Logic
* [Authentication Bypass via Flawed State Machine](business-logic/flawed-state-machine.md) — Practitioner
