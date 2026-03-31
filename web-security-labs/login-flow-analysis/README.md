#  Lab 1: Analyzing a Login Flow using Burp Suite

##  Objective
Analyze a web application's login request and identify security aspects such as:
- Transmission protocol (HTTP/HTTPS)
- Password security
- Authentication mechanism
- Response behavior

---

##  Setup

- Installed and configured **Burp Suite**
- Enabled **Intercept Mode**
- Navigated to a test login page

---

## Captured Request
POST /login HTTP/2
Host: web-security-academy.met
Cookie: session 
Content-Length: 30

{"username":"carlos","password":"12345"}
---

##  Analysis

### 1. Protocol Used
- The request is sent over **HTTP**
-  Not secure (data can be intercepted)

---

### 2. Password Transmission
- Password is sent in **plain text inside JSON**
-  Not encrypted at application level
- If HTTP is used → vulnerable to **MITM attacks**

---

### 3. Authentication Method
- API-based authentication using:
  - `POST /login`
  - JSON payload with username & password
- Likely using **token-based authentication (JWT)**

---

##  Server Response
HTTP/2 200 OK
Content-Type: test/html;charset=utf-8
content-length: 3244

---

##  Response Analysis

### 1. Status Code
- `200 OK` → Login successful

---

### 2. Session Handling
- Server sets a **session cookie**
- Cookie flags:
  -  `HttpOnly` → prevents JavaScript access
  -  `Secure` → sent only over HTTPS

---

### 3. Authentication Token
- Looks like a **JWT (JSON Web Token)**
- Used for maintaining session

---

##  Security Issues Identified

-  Login request sent over **HTTP (not HTTPS)**
-  Credentials exposed in plaintext during transmission
- Sensitive data could be intercepted

---

##  Recommendations

- Use **HTTPS** for all authentication requests
- Implement **TLS encryption**
- Add **rate limiting** to prevent brute force attacks
- Consider **multi-factor authentication (MFA)**

---

##  Key Learnings

- Importance of HTTPS in authentication
- How login APIs work
- Role of cookies and JWT in session management
- Identifying common web security flaws

---

##  Tools Used

- Burp Suite
- Browser Developer Tools

---

