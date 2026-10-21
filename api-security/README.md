# Lab: Finding and Exploiting an Unused API Endpoint - API Security

**Platform:** PortSwigger Web Security Academy  
**Category:** API Security  
**Difficulty:** Practitioner  
**Lab:** [Open the official lab](https://portswigger.net/web-security/api-testing/lab-exploiting-unused-api-endpoint)

## 1. Executive Summary
An API security vulnerability was identified in a PortSwigger Web Security Academy lab, "Finding and exploiting an unused API endpoint".
The application exposed an API endpoint that supported both retrieving and modifying product prices. Although the price modification operation required authentication, the application allowed an authenticated customer to change the price of the Lightweight "l33t" Leather Jacket by sending a crafted `PATCH` request.
By setting the `price` parameter to `0`, the product price was changed to `$0.00`. The updated price was reflected on the product page, and the jacket could be added to the basket and ordered at the modified price.

## 2. Vulnerability Details
- **Category:** API Security
- **Vulnerability Type:** Improper authorization of product price modification
- **Affected Endpoint:** `/api/products/1/price`
- **Affected HTTP Methods:** `GET`, `PATCH`
- **CWE:** CWE-862 — Missing Authorization

### Impact
An authenticated customer could modify a product's price without having the appropriate administrative permissions. This could allow products to be purchased at unintended prices, resulting in financial loss and undermining the integrity of the application's pricing and checkout processes.

## 3. Technical Analysis
The application used the API endpoint `/api/products/1/price` to retrieve the price of a product. The initial request was captured in Burp Proxy and sent to Burp Repeater for inspection.
The original request was:
```http
GET /api/products/1/price HTTP/2
```
The server returned `200 OK` with a JSON response containing the product price:
```json
{
  "price": "$1337.00",
  "message": "Your neighbor just bought 1 of these! Don't feel left out!"
}
```
This confirmed that the endpoint provided product pricing information.

### Identifying the Supported HTTP Methods
The request method was changed from `GET` to `OPTIONS` in Burp Repeater. The server returned:
```http
HTTP/2 405 Method Not Allowed
Allow: GET, PATCH
```
Although the server rejected the `OPTIONS` request, the `Allow` response header indicated that `GET` and `PATCH` were permitted methods for the endpoint.
The `PATCH` method was therefore investigated to determine whether it could be used to modify the product price.

### Identifying the Authentication Requirement
An initial `PATCH` request returned:
```http
HTTP/2 401 Unauthorized
```
This indicated that authentication was required for the price modification operation.
The application was then accessed using the lab-provided credentials `wiener:peter`. After authentication, a fresh API request for the Lightweight "l33t" Leather Jacket was captured and sent to Burp Repeater.

### Identifying the Required Request Format
The authenticated request was changed to use the `PATCH` method. The initial attempt returned an error indicating that the endpoint supported only the `application/json` content type:
```json
{
  "type": "ClientError",
  "code": 400,
  "error": "Only 'application/json' Content-Type is supported"
}
```
The following header was added to the request:
```http
Content-Type: application/json
```
An empty JSON object was then supplied as the request body:
```json
{}
```
The server returned another validation error:
```json
{
  "type": "ClientError",
  "code": 400,
  "error": "'price' parameter missing in body"
}
```
This response confirmed that the request body needed to contain a `price` parameter.

### Exploiting the Price Modification Endpoint
The request body was changed to include a price value of zero:
```json
{"price":0}
```
The resulting request was:
```http
PATCH /api/products/1/price HTTP/2
Content-Type: application/json
{"price":0}
```
The server returned `200 OK` with the following response:
```json
{"price":"$0.00"}
```
Reloading the product page confirmed that the displayed price had changed to `$0.00`. The jacket was then added to the basket, and the order was placed successfully to complete the lab.
This demonstrated that an authenticated customer could modify product pricing through an API endpoint without the appropriate authorization restrictions.

## 4. Proof of Concept
### Step 1: Capture the original product price request
The product page was accessed through Burp's browser. In Burp Proxy's HTTP history, the request to `/api/products/1/price` was identified and sent to Burp Repeater.
The original `GET` request returned `200 OK` and the product's current price in JSON format.

**Evidence:** `01-get-product-price.png`

### Step 2: Identify the supported HTTP methods
The request method was changed to `OPTIONS` and sent through Burp Repeater.
The server returned `405 Method Not Allowed` with the response header `Allow: GET, PATCH`, indicating that `PATCH` was an allowed method.

**Evidence:** `02-options-allowed-methods.png`

### Step 3: Identify the required JSON parameter
After authenticating to the application, a fresh request for the leather jacket was sent to Burp Repeater and changed to `PATCH`.
An empty JSON body was submitted with the header `Content-Type: application/json`:
```json
{}
```
The server returned `400 Bad Request` with the message `'price' parameter missing in body`, identifying the required parameter.

**Evidence:** `03-patch-missing-price.png`

### Step 4: Modify the product price
The request body was changed to:
```json
{"price":0}
```
The server returned `200 OK` with the response:
```json
{"price":"$0.00"}
```
This confirmed that the API accepted the price modification.

**Evidence:** `04-patch-price-updated.png`

### Step 5: Verify the modified price on the product page
The leather jacket product page was reloaded in Burp's browser. The displayed price was `$0.00`, confirming that the modification was reflected in the application interface.

**Evidence:** `05-product-price-zero.png`

### Step 6: Purchase the product at the modified price
The leather jacket was added to the basket. The basket was opened to verify the product and its modified price, and the order was placed to complete the lab.

**Evidence:** `06-basket-zero-price.png`

### Step 7: Verify lab completion
The PortSwigger Web Security Academy lab page was checked to confirm that the lab status changed to **Solved**.

**Evidence:** `07-lab-solved.png`

## 5. Root Cause
The API allowed an authenticated customer to invoke a product price modification operation without adequately enforcing authorization based on the user's role and permissions.
Authentication alone did not prevent the customer from changing a business-critical value. The endpoint accepted a client-supplied `price` parameter and applied the modification without verifying that the authenticated user was authorized to manage product pricing.

## 6. Remediation
- **Enforce server-side authorization:** Restrict product price modification to authorized administrators or other explicitly permitted roles. Do not rely on authentication alone.
- **Separate administrative functionality:** Expose price modification through a properly protected administrative API rather than making it available to ordinary customer accounts.
- **Validate permissions on every request:** Verify the authenticated user's authorization for the specific operation, regardless of whether the endpoint is used by the visible interface.
- **Protect product pricing integrity:** Store authoritative product prices on the server and prevent ordinary customers from modifying them through API requests.
- **Calculate order totals server-side:** Recalculate basket and checkout totals using trusted product records rather than accepting client-controlled prices.
- **Add authorization tests:** Verify that customer accounts cannot modify prices, even when they directly invoke API endpoints that are not used by the normal interface.
- **Log sensitive changes:** Record administrative price modifications and relevant authorization failures for monitoring and investigation.

## 7. Tools Used
- Burp Suite Community Edition
- Burp Proxy
- Burp Repeater
- Burp's browser
- PortSwigger Web Security Academy


This write-up documents the observed behavior of the training application and is intended for educational purposes. Testing against real systems should only be performed with explicit authorization.
