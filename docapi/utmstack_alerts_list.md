# 🚨 UTMStack Alerts API — List Alerts

## Endpoint

```http
POST /api/elasticsearch/search?page=1&size=25&top=100000000&indexPattern=alert-*&sort=@timestamp,desc
```

## Description

This endpoint retrieves alerts from UTMStack’s Elasticsearch index.  
It supports advanced filtering, pagination, and sorting, allowing analysts to query alerts within specific time ranges or by defined conditions.

---

## 🔐 Authorization

> ⚠️ **Authorization Required**  
> All requests must include a valid **Bearer Token** obtained from the `/auth/login` endpoint.

**Example Header:**

```http
Authorization: Bearer <your_access_token>
Content-Type: application/json
```

---

## 🧩 Request Parameters

| Parameter       | Type     | Required | Description |
|-----------------|----------|-----------|--------------|
| `page`          | Integer  | Yes       | Current page number (starts at 1). |
| `size`          | Integer  | Yes       | Number of results per page. |
| `top`           | Integer  | Yes       | Maximum number of records to retrieve. |
| `indexPattern`  | String   | Yes       | Elasticsearch index pattern (e.g., `alert-*`). |
| `sort`          | String   | Optional  | Sorting field and direction (e.g., `@timestamp,desc`). |

---

## 🧠 Request Body

The request body is a JSON array of filter definitions used to refine the search.

### Example Payload

```json
[
  { "field": "status", "operator": "IS_NOT", "value": 1 },
  { "field": "tags", "operator": "IS_NOT", "value": "False positive" },
  { "field": "@timestamp", "operator": "IS_BETWEEN", "value": ["now-7d", "now"] }
]
```

| Field       | Type     | Description |
|--------------|----------|-------------|
| `field`     | String   | Name of the alert field to filter. |
| `operator`  | String   | Filter operator (`IS`, `IS_NOT`, `IS_BETWEEN`, etc.). |
| `value`     | Any      | Filter value (string, number, or array). |

---

## 📤 Example cURL Request

```bash
curl -X POST "https://192.168.1.20/api/elasticsearch/search?page=1&size=25&top=100000000&indexPattern=alert-*&sort=@timestamp,desc"   -H "Authorization: Bearer <your_access_token>"   -H "Content-Type: application/json"   -d '[
        {"field": "status", "operator": "IS_NOT", "value": 1},
        {"field": "tags", "operator": "IS_NOT", "value": "False positive"},
        {"field": "@timestamp", "operator": "IS_BETWEEN", "value": ["now-7d", "now"]}
      ]'
```

---

## 📦 Response

Returns a JSON array of alert objects.  
Each alert includes metadata, source/destination information, and contextual details.

### Example Response

```json
[
  {
    "severity": 2,
    "severityLabel": "Medium",
    "status": 2,
    "statusLabel": "Open",
    "TagRulesApplied": null,
    "notes": "",
    "dataType": "wineventlog",
    "name": "Windows: Multiple Windows logon failures",
    "description": "Adversaries may use brute force techniques to gain access to accounts when passwords are unknown or when password hashes are obtained.",
    "solution": "Set account lockout policies after a certain number of failed login attempts to prevent passwords from being guessed. Use multi-factor authentication.",
    "statusObservation": "This alert has been evaluated by the tag rules engine",
    "tactic": "Brute Force",
    "category": "Potentially Malicious Activity",
    "dataSource": "dev-wsrv-2016",
    "tags": null,
    "reference": ["https://attack.mitre.org/techniques/T1110/"],
    "@timestamp": "2025-10-15T18:43:38.001187004Z",
    "destination": {
      "ip": "192.168.1.10",
      "host": "dev-wsrv-2016.demo.utmstack",
      "user": "rick",
      "port": 0,
      "country": "",
      "city": "",
      "countryCode": "",
      "aso": "",
      "asn": 0,
      "coordinates": [0, 0],
      "isAnonymousProxy": false,
      "isSatelliteProvider": false,
      "accuracyRadius": 0
    },
    "source": {
      "ip": "",
      "host": "",
      "user": "-",
      "port": 0,
      "country": "",
      "city": "",
      "countryCode": "",
      "aso": "",
      "asn": 0,
      "coordinates": [0, 0],
      "isAnonymousProxy": false,
      "isSatelliteProvider": false,
      "accuracyRadius": 0
    },
    "incidentDetail": {
      "createdBy": "",
      "observation": "",
      "source": "",
      "creationDate": ""
    },
    "isIncident": false,
    "logs": [
      "3d5ec5a7-4e86-4e10-94cd-af805522f0ad",
      "cda84192-b9c6-4cdc-8393-e0a0922eccfa",
      "09394640-b490-4f98-8c8e-8f484f7e5c91"
    ],
    "id": "c1c4e32c-dd9f-4a15-98c4-0dac2af40740"
  }
]
```

---

## ⚙️ Implementation Notes

- Pagination is handled through standard Spring `Pageable` parameters (`page`, `size`).
- `top` limits the total number of documents retrieved from Elasticsearch.
- If no results are found, the endpoint returns an **empty array** (`[]`) with status `200 OK`.
- The index pattern `alert-*` can be changed to query other datasets (e.g., `event-*`).
