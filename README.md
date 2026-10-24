# PortSwigger Web Security Academy - Lab Writeups

A collection of selected web security lab writeups documenting hands-on vulnerability testing, technical analysis, and remediation using **PortSwigger Web Security Academy** and **Burp Suite Community Edition**.
Through these exercises, I'm developing a structured approach to web application security testing: understanding application behavior, investigating potential weaknesses, validating findings, assessing their impact, and identifying appropriate security controls.

## 📊 Lab Progress
I've completed **55 hands-on labs** across six areas of web security.

| Security Area | Labs Completed |
|---|---:|
| Cross-Site Scripting (XSS) | 17 |
| SQL Injection | 16 |
| Access Control | 13 |
| Authentication | 7 |
| Server-Side Request Forgery (SSRF) | 1 |
| API Testing | 1 |
| **Total** | **55** |

## 🔎 Selected Lab Writeups
This repository features detailed writeups for selected PortSwigger Web Security Academy labs across four areas of web security. Rather than documenting every completed lab, I focus on selected exercises to document the testing process, analyze technical findings, and explore remediation approaches.

### Access Control
Testing authorization enforcement and identifying weaknesses that may allow unauthorized actions.

### SQL Injection
Investigating SQL injection vulnerabilities and techniques for bypassing input filters.

### Server-Side Request Forgery (SSRF)
Examining server-side request validation and filter-bypass techniques involving open redirection.

### API Security
Testing API endpoints for potential authorization weaknesses and unintended access to functionality.

## Testing Methodology
Each write-up aims to document the investigation from initial observation to security recommendations:

1. **Reconnaissance:** Understanding the relevant application functionality and HTTP request flow.
2. **Hypothesis:** Identifying a potential vulnerability based on observed behavior.
3. **Testing:** Inspecting and modifying HTTP requests using Burp Suite and analyzing server responses.
4. **Validation:** Confirming the behavior and recording supporting technical evidence.
5. **Impact Assessment:** Explaining the potential security consequences within the lab scenario.
6. **Remediation:** Recommending security controls and defensive practices to address the weakness.

## Tools & Technologies
- **Burp Suite Community Edition:** HTTP interception and request manipulation
- **HTTP analysis:** Inspecting requests, responses, parameters, and application behavior
- **Web application security testing:** Investigating and validating potential vulnerabilities
- **OWASP web security concepts:** Understanding common vulnerability classes and defensive practices
