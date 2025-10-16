# 🛠 UTMStack Alerts API — Update Alert Tags

## Endpoint

```http
POST /api/utm-alerts/tags
```

## Summary

Adds or updates **tags** for one or multiple alerts.  
Tags can categorize alerts, help in filtering, and optionally trigger **automatic rules** based on tag creation.  
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

## 🧩 Request Body

**UpdateAlertTagsRequestBody**:

| Field | Type | Required | Description |
|-------|------|---------|-------------|
| alertIds | array[string] | ✅ | List of alert UUIDs to update |
| tags | array[string] | ❌ | List of tags to assign; can be empty |
| createRule | boolean | ✅ | If `true`, automatically create a tag rule when assigning tags |

### Example Payload

```json
{
  "alertIds": ["c1c4e32c-dd9f-4a15-98c4-0dac2af40740", "d2f5e12a-b5a4-4bcd-91d0-2a8f5b6d9e1f"],
  "tags": ["Investigation Needed", "SOC Review"],
  "createRule": true
}
```

### JSON Schema

```json
{
  "type": "object",
  "properties": {
    "alertIds": {
      "type": "array",
      "items": { "type": "string", "format": "uuid" }
    },
    "tags": {
      "type": "array",
      "items": { "type": "string" }
    },
    "createRule": { "type": "boolean" }
  },
  "required": ["alertIds", "createRule"]
}
```

---

## 📤 Example cURL

```bash
curl -X POST "https://192.168.1.20/api/utm-alerts/tags"   -H "Authorization: Bearer <your_access_token>"   -H "Content-Type: application/json"   -d '{
    "alertIds": ["c1c4e32c-dd9f-4a15-98c4-0dac2af40740", "d2f5e12a-b5a4-4bcd-91d0-2a8f5b6d9e1f"],
    "tags": ["Investigation Needed", "SOC Review"],
    "createRule": true
  }'
```

---

## 🟦 Example Node.js (Axios)

```javascript
import axios from "axios";

const token = "<your_access_token>";
const payload = {
  alertIds: ["c1c4e32c-dd9f-4a15-98c4-0dac2af40740", "d2f5e12a-b5a4-4bcd-91d0-2a8f5b6d9e1f"],
  tags: ["Investigation Needed", "SOC Review"],
  createRule: true
};

axios.post("https://192.168.1.20/api/utm-alerts/tags", payload, {
  headers: { Authorization: `Bearer ${token}`, "Content-Type": "application/json" }
})
.then(res => console.log("Tags updated", res.status))
.catch(err => console.error(err.response?.data || err.message));
```

---

## 🔹 Response

Returns **HTTP 200 OK** if tags were successfully updated.  
No body is returned.

### Response Example

```http
HTTP/1.1 200 OK
```

---

## ⚠️ Status Codes

| Code | Meaning |
|------|---------|
| 200  | Tags updated successfully |
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
  summary: "Update alert tags"
  tags:
    - Alerts
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/UpdateAlertTagsRequestBody'
  responses:
    '200':
      description: "Tags updated successfully"
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

- `alertIds` are UUID strings identifying the alerts.  
- `tags` may be empty or omitted.  
- Response body is empty; success is indicated by HTTP 200.  
- Common errors and exceptions follow `GlobalExceptionHandler`.
