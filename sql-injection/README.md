# Vulnerability Report: SQL Injection with Filter Bypass via XML Encoding (PortSwigger Web Security Academy - SQL Injection)

## 1. Executive Summary
A SQL injection vulnerability was identified in the XML-based stock-check functionality in a PortSwigger Web Security Academy lab, "SQL injection with filter bypass via XML encoding".
The application's security filter blocked a direct SQL injection attempt. However, encoding the payload using XML hexadecimal character references allowed it to bypass the filter. The database then processed the injected query, allowing usernames and associated password values to be retrieved from the `users` table.

## 2. Vulnerability Details
- **Platform:** PortSwigger Web Security Academy
- **Category:** SQL Injection
- **Vulnerability Type:** SQL Injection with Filter Bypass
- **Affected Endpoint:** `POST /product/stock`
- **CWE:** CWE-89 - Improper Neutralization of Special Elements Used in an SQL Command ('SQL Injection')

### Impact
An attacker able to exploit this vulnerability could retrieve sensitive database information, including usernames and password values. Depending on the application's database privileges and the data accessible through the injection point, this could expose user accounts and other sensitive application information.

## 3. Technical Analysis

The `storeId` parameter in the stock-check request was tested for SQL injection using Burp Suite Repeater.
An initial test using `1+1` returned `30 units`. This result was inconclusive and did not independently confirm SQL injection.

A UNION-based payload was then submitted:
```sql
1 UNION SELECT NULL
```
The server returned `403 Forbidden` with the message `Attack detected`, indicating that the payload was blocked by a security filter.
The payload was then encoded using XML hexadecimal character references with the Hackvertor extension. After the encoded payload was submitted, the server returned `200 OK`, with the stock result followed by `null`. This indicated that the encoded request was accepted and was consistent with the injected query being processed.

To verify whether database information could be retrieved, the payload was modified to select usernames and password values from the `users` table:
```sql
1 UNION SELECT username || '~' || password FROM users
```
After encoding and submitting this payload, the response returned records for `administrator`, `carlos`, and `wiener`, with each username and its associated password value separated by `~`. This confirmed that the SQL injection could be used to retrieve data from the database.

## 4. Proof of Concept

### Step 1: Capture the original stock-check request
The stock-check request was captured using Burp Suite and sent to Repeater for testing.

**Evidence:** `01-original-stock-check-request.png`

### Step 2: Test the `storeId` parameter
The `storeId` value was modified from `1` to `1+1` to observe how the application handled the altered input. The response returned `30 units`, which was inconclusive and did not independently confirm SQL injection.

**Evidence:** `02-storeid-injection-test.png`

### Step 3: Identify the filter restriction
The following SQL injection payload was submitted directly in the `storeId` element:
```sql
1 UNION SELECT NULL
```
The server returned `403 Forbidden` with the message `Attack detected`, indicating that the payload was blocked by a security filter.

**Evidence:** `03-waf-blocked-request.png`

### Step 4: Encode the payload to bypass the filter
The following SQL injection payload was encoded using Hackvertor's `hex_entities` transformation:
```sql
1 UNION SELECT NULL
```
The encoded output was copied into the XML `storeId` element in Burp Repeater and submitted. The server returned `200 OK`, with the stock result followed by `null`, consistent with the injected query being processed.

**Evidence:** `04-encoded-payload-in-repeater.png`

### Step 5: Retrieve database records
The payload was modified to retrieve usernames and password values from the `users` table:
```sql
1 UNION SELECT username || '~' || password FROM users
```
The payload was encoded using the same method and submitted in the `storeId` element. The response returned records for `administrator`, `carlos`, and `wiener`, demonstrating that database information could be retrieved through the injection point.

**Evidence:** `05-database-records-retrieved.png`

### Step 6: Verify lab completion

After the lab objective was completed, the PortSwigger Web Security Academy interface displayed the **Solved** status.

**Evidence:** `06-lab-solved.png`
## 5. Root Cause
The application processed user-controlled input in a database query without adequate protection against SQL injection. The security filter blocked recognizable SQL syntax but could be bypassed through XML character-reference encoding.
The underlying issue was unsafe SQL query construction and insufficient protection against SQL injection, rather than the encoding technique itself.

## 6. Remediation
- Use parameterized queries or prepared statements instead of concatenating untrusted input into SQL statements.
- Validate the `storeId` input against the expected data type and format.
- Ensure XML parsing and input validation are handled consistently before database operations.
- Apply least-privilege permissions to the database account.
- Avoid relying on WAF rules or keyword filters as the primary defense against SQL injection.
- Store passwords using an appropriate salted password-hashing algorithm rather than plaintext or reversibly stored values.

## 7. Tools Used
- Burp Suite Community Edition
- Burp Proxy
- Burp Repeater
- Hackvertor extension
- PortSwigger Web Security Academy
