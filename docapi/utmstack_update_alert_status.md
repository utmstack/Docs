# 🛠 UTMStack Alerts API — Update Alert Status

## Endpoint

```http
POST /api/utm-alerts/status
```

## Summary

Updates the **status** of one or more alerts.  
Allows analysts to change the alert state (e.g., Open, In Review, Completed) and optionally add an observation note.  
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

### Example Payload

```json
{
  "alertIds": ["c1c4e32c-dd9f-4a15-98c4-0dac2af40740", "7a12c4f3-894c-4e2a-9f1b-c7c7a0b84522"],
  "status": 3,
  "statusObservation": "Reviewed and confirmed as false positive",
  "addFalsePositiveTag": true
}
```

### Status Codes Reference

| Status Name   | Value | Description                  |
|---------------|-------|------------------------------|
| OPEN          | 2     | Alert is open and pending review |
| IN_REVIEW     | 3     | Alert is currently being reviewed |
| COMPLETED     | 5     | Alert has been resolved/completed |

### JSON Schema

```json
{
  "type": "object",
  "properties": {
    "alertIds": {
      "type": "array",
      "items": { "type": "string", "format": "uuid" }
    },
    "status": { "type": "integer", "enum": [2,3,5] },
    "statusObservation": { "type": "string" },
    "addFalsePositiveTag": { "type": "boolean" }
  },
  "required": ["alertIds", "status"]
}
```

---

## 📤 Example cURL

```bash
curl -X POST "https://192.168.1.20/api/utm-alerts/status"   -H "Authorization: Bearer <your_access_token>"   -H "Content-Type: application/json"   -d '{
        "alertIds": ["c1c4e32c-dd9f-4a15-98c4-0dac2af40740"],
        "status": 3,
        "statusObservation": "Reviewed and confirmed as false positive",
        "addFalsePositiveTag": true
      }'
```

---

## 🟦 Example Node.js (Axios)

```javascript
import axios from "axios";

const token = "<your_access_token>";

const payload = {
  alertIds: ["c1c4e32c-dd9f-4a15-98c4-0dac2af40740"],
  status: 3,
  statusObservation: "Reviewed and confirmed as false positive",
  addFalsePositiveTag: true
};

axios.post("https://192.168.1.20/api/utm-alerts/status", payload, {
  headers: { Authorization: `Bearer ${token}` }
})
.then(res => console.log("Status updated", res.status))
.catch(err => console.error(err.response?.data || err.message));
```

---

## 🔹 Response

Returns **HTTP 200 OK** if the status was successfully updated.  
No body is returned.

### Response Example

```http
HTTP/1.1 200 OK
```

---

## ⚠️ Status Codes

| Code | Meaning |
|------|---------|
| 200  | Status updated successfully |
| 400  | Invalid request payload |
| 401  | Unauthorized / Invalid token |
| 404  | Alert not found |
| 500  | Internal server error |

---

## 🔐 Security Considerations

- Requires Bearer token authentication.  
- Changes are **audited** using `ApplicationEventService` for traceability.  
- Users without proper permissions will receive **401 Unauthorized**.

---

## ✅ OpenAPI 3.0 Fragment (YAML)

```yaml
post:
  summary: "Update alert status"
  tags:
    - Alerts
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/UpdateAlertStatusRequestBody'
  responses:
    '200':
      description: "Status updated successfully"
    '400':
      description: "Invalid request payload"
    '401':
      description: "Unauthorized"
    '404':
      description: "Alert not found"
    '500':
      description: "Internal server error"
```

---

## Assumptions Made

- `status` field uses the enum `AlertStatusEnum`:
  - `OPEN = 2`  
  - `IN_REVIEW = 3`  
  - `COMPLETED = 5`  
- `alertIds` are UUID strings.  
- `addFalsePositiveTag` is optional; defaults to `false`.  
- Response body is empty; success is indicated by HTTP status only.  
