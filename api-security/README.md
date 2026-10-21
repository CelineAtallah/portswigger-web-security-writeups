
# Lab: Finding and Exploiting an Unused API Endpoint - API Security

**Platform:** PortSwigger Web Security Academy  
**Category:** API Security  
**Difficulty:** Practitioner  
**Lab:** [Open the official lab](https://portswigger.net/web-security/api-testing/lab-exploiting-unused-api-endpoint)

## 1. Executive Summary
This lab demonstrates an API authorization vulnerability that allows an authenticated customer to modify a product's price through an API endpoint intended to support price updates.
By inspecting the API's allowed HTTP methods and testing the `PATCH` method, it was possible to identify an endpoint that accepted a customer-supplied price. Using the authenticated `wiener` account, the product price was changed from `$1337.00` to `$0.00`. The modified price was reflected on the product page, allowing the item to be purchased at the altered price and the lab to be completed.

## 2. Vulnerability Details
- **Category:** API Security
- **Vulnerability Type:** Improper authorization of product price modification
- **Affected Endpoint:** `/api/products/1/price`
- **Affected HTTP Methods:** `GET`, `PATCH`
- **CWE:** CWE-862 - Missing Authorization

### Impact
An authenticated customer could modify a product's price without having the appropriate administrative permissions. This could allow products to be purchased at unintended prices, resulting in financial loss and undermining the integrity of the application's pricing and checkout processes.

## 3. Technical Analysis

### 3.1 Inspecting the Product Price Endpoint
The product page was accessed through Burp's browser while authenticated as `wiener`. In Burp Proxy's HTTP history, the request retrieving the product's price was identified and sent to Burp Repeater.

The original request was:
```http
GET /api/products/1/price HTTP/2
```
The server returned `200 OK` with the product's current price in JSON format:
```json
{"price":"$1337.00","message":"This item is in high demand - 6 purchased recently"}
```
This established the API endpoint used to retrieve the product's current price.

### 3.2 Identifying the Supported HTTP Methods
The request method was changed from `GET` to `OPTIONS` in Burp Repeater. The server returned:
```http
HTTP/2 405 Method Not Allowed
Allow: GET, PATCH
```
Although the server did not accept the `OPTIONS` request, the `Allow` response header indicated that `GET` and `PATCH` were supported methods. This suggested that the endpoint could support price modifications, so the `PATCH` method was tested.

### 3.3 Testing the PATCH Request and Required Parameters
An initial `PATCH` request returned `401 Unauthorized`. The request was then recaptured from the authenticated browser session, and testing continued using that request.
The first authenticated `PATCH` attempt returned an error indicating that only `application/json` content was supported. After setting the `Content-Type` header to `application/json` and submitting an empty JSON body, the server returned:
```http
HTTP/2 400 Bad Request
Content-Type: application/json
{"type":"ClientError","code":400,"error":"'price' parameter missing in body"}
```
This response indicated that the endpoint expected a `price` parameter in the JSON request body.

### 3.4 Modifying the Product Price
The request body was changed to specify a price of zero:
```http
PATCH /api/products/1/price HTTP/2
Content-Type: application/json
{"price":0}
```
The server accepted the request and returned:
```http
HTTP/2 200 OK
Content-Type: application/json
{"price":"$0.00"}
```
The successful response confirmed that the API processed the customer-supplied price modification.
The product page was then reloaded, and the displayed price had changed to `$0.00`. The leather jacket was added to the basket, and the order was placed successfully, completing the lab.
These results demonstrate that an authenticated customer account could modify a product's price through the API without adequate authorization controls restricting this sensitive operation.

## 4. Proof of Concept
### Step 1: Capture the original product price request
The product page was accessed through Burp's browser while authenticated as `wiener`. In Burp Proxy's HTTP history, the request to `/api/products/1/price` was identified and sent to Burp Repeater.
The original `GET` request returned `200 OK` and the product's current price, `$1337.00`, in JSON format.

**Evidence:** `01-get-product-price.png`

### Step 2: Identify the supported HTTP methods
The request method was changed to `OPTIONS` and sent through Burp Repeater.
The server returned `405 Method Not Allowed` with the response header `Allow: GET, PATCH`, indicating that `PATCH` was an allowed method for the endpoint.

**Evidence:** `02-options-allowed-methods.png`

### Step 3: Identify the required JSON parameter
Using a fresh request captured after authenticating as `wiener`, the request method was changed to `PATCH` and the header `Content-Type: application/json` was added. An empty JSON body was submitted:
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
This confirmed that the authenticated customer account could modify the product's price through the API.

**Evidence:** `04-patch-price-updated.png`

### Step 5: Verify the modified price on the product page
The leather jacket product page was reloaded in Burp's browser. The displayed price was `$0.00`, confirming that the modification was reflected in the application interface.

**Evidence:** `05-product-price-zero.png`

### Step 6: Purchase the product at the modified price
The leather jacket was added to the basket. The basket was opened to verify the product and its modified price, and the order was placed successfully to complete the lab.

**Evidence:** `06-basket-zero-price.png`

### Step 7: Verify lab completion
The PortSwigger Web Security Academy lab page was checked to confirm that the lab status changed to **Solved**.

**Evidence:** `07-lab-solved.png`

## 5. Root Cause
The API endpoint accepted a customer-supplied price and processed the update without adequately restricting the operation to an authorized role. Authentication alone was insufficient to protect this sensitive action: the application needed to verify that the account was permitted to modify product pricing.

## 6. Remediation
- **Enforce role-based authorization:** Restrict product-price modifications to authorized administrative roles. Ordinary customer accounts must not be able to change prices.
- **Use trusted server-side pricing:** Retrieve product prices from trusted server-side records and calculate order totals on the server. Do not accept a customer-supplied price as authoritative during checkout.
- **Test authorization boundaries:** Add regression tests to verify that ordinary customer accounts cannot modify product prices through API endpoints, including endpoints not exposed in the user interface.
- **Audit sensitive changes:** Log product-price modifications with the responsible account, affected product, and change details to support investigation and accountability.

## 7. Tools Used
- Burp Suite Community Edition
- Burp Proxy
- Burp Repeater
- Burp's browser
- PortSwigger Web Security Academy
