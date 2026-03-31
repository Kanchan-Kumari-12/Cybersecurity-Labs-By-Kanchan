# Lab 3: Identifying Security Issues in Web Traffic

##  Objective
Analyze a vulnerable request and identify critical web security flaws such as:
- CSRF (Cross-Site Request Forgery)
- IDOR (Insecure Direct Object Reference)
- Missing authorization checks
- Insecure transmission (HTTP)

---

##  Captured Request
POST /admin/users/delete HTTP/1.1
Host: vulnerable-app.local
Cookie: session=user_cookie
Content-Type: application/x-www-form-urlencoded
user_id=15

---

##  Vulnerability Analysis

### 1.  Missing CSRF Protection

- No CSRF token present in request
- No anti-CSRF header or validation

#### Risk:
- Attacker can trick a logged-in user into sending this request
- Example: malicious website triggers deletion request

---

### 2.  Insecure Direct Object Reference (IDOR)

- `user_id=15` directly passed in request

#### Risk:
- Attacker can modify:

user_id=16
user_id=1
- May delete other users without authorization

---

### 3.  Missing Authorization Check

- No visible validation of user permissions

#### Risk:
- Any authenticated user may access:

/admin/users/delete 
- Leads to privilege escalation

---

### 4. HTTP Instead of HTTPS

- Request sent over **HTTP**

#### Risk:
- Session cookie exposed in plaintext
- Vulnerable to:
- MITM (Man-in-the-Middle) attacks
- Session hijacking

---

##  Combined Attack Scenario

An attacker can:
1. Intercept session over HTTP
2. Craft a malicious request
3. Change `user_id`
4. Trick victim into executing request (CSRF)

 Result: Unauthorized deletion of users

---

##  Recommended Fixes

### CSRF Protection
- Implement CSRF tokens
- Validate tokens on server side

---

### Access Control
- Enforce strict **authorization checks**
- Only admins should access `/admin/*` routes

---

### Prevent IDOR
- Do not trust user input directly
- Validate ownership/permissions on server

---

### Secure Communication
- Enforce **HTTPS**
- Use secure cookies:
- `Secure`
- `HttpOnly`
- `SameSite`

---

##  Key Learnings

- How multiple vulnerabilities combine into critical attacks
- Importance of access control in APIs
- Real-world risks of insecure endpoints
- Identifying OWASP Top 10 issues in traffic

---

##  Tools Used

- Burp Suite
- Browser Developer Tools

---
