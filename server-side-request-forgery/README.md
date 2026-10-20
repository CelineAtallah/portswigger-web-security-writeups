# Lab: SSRF with Filter Bypass via Open Redirection Vulnerability - Server-Side Request Forgery

**Platform:** PortSwigger Web Security Academy  
**Category:** Server-Side Request Forgery (SSRF)  
**Difficulty:** Practitioner  
**Lab:** [Open the official lab](https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-open-redirection)

## 1. Executive Summary
A server-side request forgery (SSRF) vulnerability was identified in a PortSwigger Web Security Academy lab, "SSRF with Filter Bypass via Open Redirection Vulnerability".
The stock-checking functionality restricted direct requests to internal URLs. However, an open redirect vulnerability in the product navigation endpoint allowed the restriction to be bypassed. By supplying a crafted redirect URL through the stock-check parameter, it was possible to access the internal admin panel and delete the user `carlos`.

## 2. Vulnerability Details
- **Category:** Server-Side Request Forgery (SSRF)
- **Vulnerability Type:** SSRF via open redirect filter bypass
- **Affected Endpoints:** `POST /product/stock` and `GET /product/nextProduct`
- **CWE:** CWE-918 — Server-Side Request Forgery (SSRF)

### Impact
An attacker could bypass the stock checker's URL restrictions and cause the server to access an internal administrative endpoint. Depending on the internal services exposed and the permissions available to the server-side request, this could enable unauthorized access to internal functionality or execution of privileged actions.

## 3. Technical Analysis
The application used the `stockApi` parameter in the `POST /product/stock` request to determine which URL the server should fetch to retrieve stock information.
The original request body contained:
```text
stockApi=%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```
A direct attempt to set `stockApi` to the internal admin URL was rejected with the following error:
```text
Invalid external stock check url 'Invalid URL'
```
This indicated that the application validated the supplied stock-check URL.

### Identifying the Open Redirect
Inspection of the product navigation functionality revealed the following request when the **Next product** link was clicked:
```http
GET /product/nextProduct?currentProductId=1&path=/product?productId=2 HTTP/2
```
The server responded with:
```http
HTTP/2 302 Found
Location: /product?productId=2
```
The `currentProductId` parameter identified the current product, while `path` specified the redirect destination. The server used the supplied `path` value in its `Location` response header, revealing an open redirect vulnerability.

### Understanding the Filter Bypass
Instead of supplying the internal admin URL directly to `stockApi`, the redirect endpoint was used as an intermediary.
The intended redirect URL was:
```text
/product/nextProduct?currentProductId=1&path=http://192.168.0.12:8080/admin
```
This URL uses the existing `/product/nextProduct` endpoint. The `currentProductId=1` parameter identifies the current product, while `path` specifies the destination of the redirect. By setting `path` to the internal admin URL, the request caused the server to redirect to the internal admin page.
The URL was supplied as the value of the `stockApi` parameter in the POST request. The encoded request body sent through Burp Repeater was:
```text
stockApi=%2Fproduct%2FnextProduct%3FcurrentProductId%3D1%26path%3Dhttp%3A%2F%2F192.168.0.12%3A8080%2Fadmin
```
The stock checker accepted the redirect endpoint as the initial URL and followed the redirect to the internal admin page. The server returned the admin page's HTML, including the Users list containing `wiener` and `carlos`. This confirmed that the redirect bypassed the URL restriction.

### Exploiting the Internal Endpoint
The same technique was used to target the admin deletion endpoint:
```text
http://192.168.0.12:8080/admin/delete?username=carlos
```
The modified stock-check request returned `200 OK` and the message `User deleted successfully!`. The response's Users list no longer contained `carlos`, confirming that the administrative action succeeded.

## 4. Proof of Concept

### Step 1: Capture the original stock-check request
The stock-check request was intercepted using Burp Proxy and sent to Burp Repeater for testing.

**Evidence:** `01-original-stock-check-request.png`

### Step 2: Test direct access to the internal admin URL
The `stockApi` parameter was modified to point directly to the internal admin URL. The application rejected the request and returned an invalid URL error.

**Evidence:** `02-direct-internal-url-blocked.png`

### Step 3: Identify the open redirect
The product navigation functionality was inspected by clicking **Next product**. The request to `/product/nextProduct` contained a `path` parameter controlling the redirect destination. The server returned `302 Found` with a corresponding `Location` header.

**Evidence:** `03-next-product-open-redirect.png`

### Step 4: Bypass the URL restriction and access the admin panel
The `stockApi` parameter was modified to use the open redirect endpoint, with the `path` parameter pointing to the internal admin URL:
```text
stockApi=%2Fproduct%2FnextProduct%3FcurrentProductId%3D1%26path%3Dhttp%3A%2F%2F192.168.0.12%3A8080%2Fadmin
```
The server returned `200 OK` and the HTML content of the internal admin page. The response displayed the Users section, including the accounts `wiener` and `carlos`.

**Evidence:**
- `04-redirect-filter-bypass(1).png`
- `04-redirect-filter-bypass(2).png`

### Step 5: Delete the target user
The redirect destination was changed to the internal admin deletion endpoint, targeting the user `carlos`:
```text
stockApi=%2Fproduct%2FnextProduct%3Fpath%3Dhttp%3A%2F%2F192.168.0.12%3A8080%2Fadmin%2Fdelete%3Fusername%3Dcarlos
```
The server returned `200 OK` with the message `User deleted successfully!`. The response's Users list contained `wiener` but no longer contained `carlos`.

**Evidence:**
- `05-delete-carlos(1).png`
- `05-delete-carlos(2).png`

### Step 6: Verify lab completion
The lab page was checked to confirm that the status changed to **Solved**.

**Evidence:** `06-lab-solved.png`

## 5. Root Cause
The stock-checking functionality relied on URL validation that could be bypassed through an open redirect within the same application. The server followed the redirect to an internal administrative endpoint without adequately preventing the request from reaching a restricted destination.

## 6. Remediation
- **Allow only trusted URLs:** Restrict the stock checker to approved URLs instead of accepting arbitrary destinations.
- **Check redirect destinations:** Validate the final URL after every redirect, not just the original URL.
- **Control redirects:** Disable automatic redirects for server-side requests when they are not needed.
- **Block access to internal systems:** Prevent the stock checker from accessing internal IP addresses and sensitive services unless explicitly required.
- **Restrict outgoing requests:** Configure network rules so the server cannot make requests to sensitive internal systems unnecessarily.
- **Protect administrative endpoints:** Require proper authorization for administrative actions, such as deleting users, regardless of how the request reaches the endpoint.

## 7. Tools Used
- Burp Suite Community Edition
- Burp Proxy
- Burp Repeater
- PortSwigger Web Security Academy
