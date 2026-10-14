# PortSwigger Web Security Academy - Write-ups
A collection of selected web security lab write-ups documenting practical vulnerability testing, analysis, and remediation using PortSwigger Web Security Academy and Burp Suite Community Edition.
The goal of this repository is to develop and demonstrate a structured approach to web application penetration testing, from identifying potential weaknesses to validating their impact and recommending appropriate security controls.

## Areas Covered
* **Access Control & IDOR:** Testing object-level authorization and identifying unauthorized access to application resources.
* **Authentication & JWT:** Examining authentication mechanisms, token validation, and common implementation weaknesses.
* **Server-Side Request Forgery (SSRF):** Investigating server-side request behavior and weaknesses in input validation.
* **Business Logic:** Identifying flaws in application workflows and state transitions.
* **Information Disclosure:** Assessing unintended exposure of sensitive information through application responses and error messages.

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
### Access Control & IDOR
* [User ID Controlled by Request Parameter](access-control/user-id-controlled-by-request-parameter.md)

### Authentication & JWT
* [JWT Authentication Bypass via Unverified Signature](jwt/unverified-signature.md)

### Server-Side Request Forgery (SSRF)
* [Basic SSRF Against the Local Server](ssrf/basic-ssrf-local-server.md)

### Business Logic
* [Authentication Bypass via Flawed State Machine](business-logic/flawed-state-machine.md)

*Write-ups will be added as the corresponding labs are completed and documented.*

