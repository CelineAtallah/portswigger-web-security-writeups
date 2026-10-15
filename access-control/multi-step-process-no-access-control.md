# Vulnerability Report: Multi-Step Process with No Access Control on One Step (PortSwigger Web Security Academy - Access Control)

## 1. Executive Summary
A broken access control vulnerability was identified in a multi-step administrative workflow in a PortSwigger Web Security Academy lab, "Multi-Step Process with No Access Control on One Step".
The final role-change request did not properly enforce administrator permissions. By replaying the request with a regular user's session and changing the username to the same user's account, it was possible to gain administrator privileges.

## 2. Vulnerability Details
- **Platform:** PortSwigger Web Security Academy
- **Category:** Access Control
- **Vulnerability Type:** Broken Access Control
- **Affected Endpoint:** `POST /admin-roles`
- **CWE:** CWE-862 — Missing Authorization

### Impact
An authenticated regular user could promote their own account to administrator, potentially gaining access to administrative features and functionality restricted to privileged users.

## 3. Technical Analysis
The lab uses a multi-step process to change a user's role.
After a role change was initiated for `carlos` using an administrator account, the final confirmation request was captured in Burp Suite:

```http
POST /admin-roles HTTP/2
```

The original request body was:

```text
action=upgrade&confirmed=true&username=carlos
```

The request was then tested in Burp Repeater using a regular user's session (wiener's). After replacing the session cookie with the one belonging to `wiener`, and the username parameter to `wiener`,

The modified request body was:

```text
action=upgrade&confirmed=true&username=wiener
```

The request was processed without the expected authorization restriction, allowing the regular user to promote their own account to administrator.

## 4. Proof of Concept
### Step 1: Capture the original request
An administrator account was used to initiate a role change for `carlos`. The final confirmation request was captured using Burp Suite.

**Evidence:** `01-original-confirmation-request.png`


### Step 2: Capture the regular user's session
A separate private browser session was used to log in as wiener. The login traffic was inspected in Burp Suite to identify the session cookie associated with the regular user.

**Evidence:** `02-wiener-session-cookie.png`


### Step 3: Replay the request as a regular user
The request was modified to use the session cookie of `wiener`, and the username parameter was changed to `wiener`. The modified request was then sent through Burp Repeater.

**Evidence:** `03-unauthorized-role-change.png`


### Step 4: Verify the result
The server returned `302 Found` with a redirect to `/admin`. The lab subsequently displayed **Solved**, confirming that the role change had succeeded.

**Evidence:** `04-lab-solved.png`


## 5. Root Cause
The final role-change request did not properly verify whether the authenticated user had administrator privileges. Each request that performs a privileged action must enforce authorization independently, regardless of whether earlier steps in the workflow were completed legitimately.

## 6. Remediation
- Enforce server-side authorization checks on every role-change request.
- Restrict administrative role assignments to authorized users.
- Prevent regular users from granting administrator privileges to themselves or other accounts.
- Test each step of multi-step workflows to ensure that requests cannot bypass authorization when submitted directly or replayed.

## 7. Tools Used
- Burp Suite Community Edition
- Burp Proxy
- Burp Repeater
- PortSwigger Web Security Academy

## 8. Scope and Disclaimer
Testing was limited to the PortSwigger Web Security Academy lab. No real-world applications or systems were tested.
