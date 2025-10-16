# 🛠 UTMStack Alerts API — Update Alert Notes

## Endpoint

```http
POST /api/utm-alerts/notes
```

## Summary

Adds or updates **notes** for a specific alert.  
Notes allow analysts to document observations, investigations, or remediation steps related to an alert.  
Supports auditing for traceability.

---

## 🔐 Authorization

> ⚠️ **Authorization Required**  
> Include a valid **Bearer Token** in the `Authorization` header.

```http
Authorization: Bearer <your_access_token>
Content-Type: application/json
```

---

## 🧩 Request Parameters

- **alertId** (query parameter, required) — UUID of the alert to update.  
- **notes** (body, optional) — Text notes for the alert. Can be empty or omitted.

### Example Payload

```json
"Investigated alert, determined as false positive."
```

### Query Example

```http
/api/utm-alerts/notes?alertId=c1c4e32c-dd9f-4a15-98c4-0dac2af40740
```

### JSON Schema

```json
{
  "type": "string"
}
```

---

## 📤 Example cURL

```bash
curl -X POST "https://192.168.1.20/api/utm-alerts/notes?alertId=c1c4e32c-dd9f-4a15-98c4-0dac2af40740"   -H "Authorization: Bearer <your_access_token>"   -H "Content-Type: application/json"   -d '"Investigated alert, determined as false positive."'
```

---

## 🟦 Example Node.js (Axios)

```javascript
import axios from "axios";

const token = "<your_access_token>";
const alertId = "c1c4e32c-dd9f-4a15-98c4-0dac2af40740";
const notes = "Investigated alert, determined as false positive.";

axios.post(`https://192.168.1.20/api/utm-alerts/notes?alertId=${alertId}`, JSON.stringify(notes), {
  headers: { Authorization: `Bearer ${token}`, "Content-Type": "application/json" }
})
.then(res => console.log("Notes updated", res.status))
.catch(err => console.error(err.response?.data || err.message));
```

---

## 🔹 Response

Returns **HTTP 200 OK** if notes were successfully updated.  
No body is returned.

### Response Example

```http
HTTP/1.1 200 OK
```

---

## ⚠️ Status Codes

| Code | Meaning |
|------|---------|
| 200  | Notes updated successfully |
| 400  | Invalid request payload / NoAlertsProvidedException |
| 401  | Unauthorized / Invalid token (BadCredentialsException) |
| 404  | Alert not found (NoSuchElementException) |
| 409  | Conflict (IncidentAlertConflictException) |
| 412  | Precondition failed (TfaVerificationException) |
| 423  | Locked (TooMuchLoginAttemptsException) |
| 429  | Too many requests (TooManyRequestsException) |
| 500  | Internal server error / Generic Exception |

---

## 🔐 Security Considerations

- Requires Bearer token authentication.  
- Changes are **audited** using `ApplicationEventService`.  
- Users without proper permissions receive **401 Unauthorized**.

---

## ✅ OpenAPI 3.0 Fragment (YAML)

```yaml
post:
  summary: "Update alert notes"
  tags:
    - Alerts
  security:
    - bearerAuth: []
  parameters:
    - name: alertId
      in: query
      required: true
      schema:
        type: string
        format: uuid
  requestBody:
    required: false
    content:
      application/json:
        schema:
          type: string
  responses:
    '200':
      description: "Notes updated successfully"
    '400':
      description: "Invalid request payload / NoAlertsProvidedException"
    '401':
      description: "Unauthorized / BadCredentialsException"
    '404':
      description: "Alert not found / NoSuchElementException"
    '409':
      description: "Conflict / IncidentAlertConflictException"
    '412':
      description: "Precondition failed / TfaVerificationException"
    '423':
      description: "Locked / TooMuchLoginAttemptsException"
    '429':
      description: "Too many requests / TooManyRequestsException"
    '500':
      description: "Internal server error / Generic Exception"
```

---

## Assumptions Made

- `alertId` is a UUID string identifying the alert.  
- `notes` can be empty or omitted.  
- Response body is empty; success is indicated by HTTP 200.  
- Common errors and exceptions follow `GlobalExceptionHandler`.
