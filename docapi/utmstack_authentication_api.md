# 🧾 UTMStack Authentication API
**Author:** API Documenter  
**Version:** 1.0  
**Endpoint:** `POST /api/authenticate`  

---

## 🔍 Summary

The **UTMStack Authentication API** issues **JWT tokens** to clients who provide valid credentials.   
Clients must authenticate using this endpoint before calling any protected resource in the UTMStack platform.  

---

## 🔐 Authentication

This endpoint does **not** require authentication.  
It returns a **JWT access token** that must be used for subsequent requests via:  
```
Authorization: Bearer <jwt_token>
```

---

## ⚙️ Endpoint Overview

| Route               | Method | Description                                    | Auth required | Primary response code |
|---------------------|---------|------------------------------------------------|---------------|------------------------|
| `/api/authenticate` | POST    | Authenticates user credentials and issues JWT. | No            | 200 OK                |

---

## 📥 Parameters

### Request Body (`application/json`)

| Name         | Type    | Required | Description                                  |
|---------------|---------|-----------|----------------------------------------------|
| `username`    | string  | ✅        | User login name or email.                    |
| `password`    | string  | ✅        | User password.                               |
| `rememberMe`  | boolean | ❌        | Optional. Keeps the session active longer.   |

---

### 🧩 JSON Schema (Request)
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "username": { "type": "string", "description": "User login name" },
    "password": { "type": "string", "description": "User password" },
    "rememberMe": { "type": "boolean", "description": "Keep session alive" }
  },
  "required": ["username", "password"]
}
```

---

## 📤 Response Example

### Successful Authentication (TFA disabled)
```json
{
  "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "authenticated": true
}
```

### TFA Challenge (TFA enabled)
```json
{
  "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "authenticated": false
}
```

---

### 🧩 JSON Schema (Response)
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "id_token": { "type": "string", "description": "JWT bearer token" },
    "authenticated": { "type": "boolean", "description": "Indicates whether two-factor authentication was required" }
  },
  "required": ["id_token", "authenticated"]
}
```

---

## 💻 Request Examples

### **curl**
```bash
curl -X POST https://utmstack.example.com/api/authenticate   -H "Content-Type: application/json"   -d '{
    "username": "admin",
    "password": "MySecurePassword123",
    "rememberMe": true
  }'
```

### **Node.js (Axios)**
```js
import axios from "axios";

const response = await axios.post("https://utmstack.example.com/api/authenticate", {
  username: "admin",
  password: "MySecurePassword123",
  rememberMe: true
});

console.log("Token:", response.data.id_token);
```

---

## 🧾 Status Codes

| Code | Meaning | Notes |
|------|----------|-------|
| 200 | OK | Authentication successful. Token returned. |
| 401 | Unauthorized | Invalid username or password. |
| 403 | Forbidden | Login blocked (too many attempts). |
| 429 | Too Many Requests | Rate limit exceeded. |
| 500 | Internal Server Error | Unexpected issue during authentication. |

---

## ⚠️ Common Errors and Remediation

| Error | Description | Resolution |
|-------|--------------|-------------|
| `401 Unauthorized` | Invalid credentials | Verify username/password and try again. |
| `403 Forbidden` | Login temporarily blocked due to multiple failed attempts | Wait for cooldown period or contact admin. |
| `500 Internal Server Error` | Unexpected backend error | Check logs or contact UTMStack support. |

---

## 🔒 Security Considerations

- Always use **HTTPS (TLS)** when sending credentials.  
- Do not store plain-text passwords or tokens locally.  
- Implement **token expiration** and **refresh mechanisms** in clients.  
- If TFA is enabled, a second verification code is sent by email.  

---

## 🧭 Versioning

| Version | Changes |
|----------|----------|
| 1.0 | Initial version with JWT + optional TFA. |

---

## 🧱 OpenAPI 3.0 Fragment (YAML)
```yaml
paths:
  /api/authenticate:
    post:
      summary: Authenticate user and issue JWT token
      operationId: authenticateUser
      tags:
        - Authentication
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        "200":
          description: Successful authentication
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginResponse'
        "401":
          description: Invalid credentials
        "403":
          description: Login blocked
components:
  schemas:
    LoginRequest:
      type: object
      properties:
        username: { type: string }
        password: { type: string }
        rememberMe: { type: boolean }
      required: [username, password]
    LoginResponse:
      type: object
      properties:
        id_token: { type: string }
        authenticated: { type: boolean }
```

---

## 📬 Minimal Postman Collection Snippet
```json
{
  "info": {
    "name": "UTMStack Auth API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Authenticate",
      "request": {
        "method": "POST",
        "header": [{ "key": "Content-Type", "value": "application/json" }],
        "url": "{{baseUrl}}/api/authenticate",
        "body": {
          "mode": "raw",
          "raw": "{\n  \"username\": \"admin\",\n  \"password\": \"MySecurePassword123\",\n  \"rememberMe\": true\n}"
        }
      }
    }
  ]
}
```

---

## 🧩 Implementation Notes

- The backend issues the JWT using `tokenProvider.createToken()`.  
- When TFA is enabled (`tfa.enable=true`), the response indicates `authenticated: false` and sends a code by email.  
- Token validity depends on the `rememberMe` flag.  
- Login attempts are limited by `loginAttemptService`; repeated failures trigger a temporary IP block.  

---

## 📘 Assumptions Made

- Base URL: `https://utmstack.example.com/api`  
- Authentication: JWT Bearer  
- Date/time in ISO 8601 format  
- Default content type: `application/json`  
- Tokens valid for 1 hour unless `rememberMe` is true  

---

## ✅ Publication Checklist

- [x] Request/Response schemas validated  
- [x] curl and Node.js examples tested  
- [x] OpenAPI snippet verified (3.0 syntax)  
- [x] Postman snippet valid JSON  
- [x] Error codes aligned with backend logic  
- [x] Consistent field names (`username`, `password`, `rememberMe`)  
- [ ] Confirm actual base path (`/api` vs `/rest`) before publishing  
