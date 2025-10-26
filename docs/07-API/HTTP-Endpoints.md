## HTTP Endpoints (REST API)

### Overview

RustGuard's REST interface allows external systems (such as administration panels or third-party applications) to query system data efficiently. This API is primarily used for **reading** status and metrics, not for executing sanction commands (commands are handled via RCON or Webhooks).

**Base URL:** `http://localhost:8080/api/v1` (The port is configurable)

### Authentication

All endpoints require an **API Token** configured in the `Configuration.md` file. This token must be provided in the request's `Authorization` header.

|Header|Value|
|---|---|
|`Authorization`|`Bearer <your_api_token>`|

### 1. Player Status (`/player/{uuid}`)

This endpoint provides the current status of a specific player within the **Context System**, including their risk score and the latest detections.

#### GET /player/{uuid}

|Route Parameter|Type|Description|
|---|---|---|
|`{uuid}`|String|The Mojang UUID of the player to query.|

#### Successful Response Example (200 OK)

```yaml
{
  "uuid": "01234567-89ab-cdef-0123-456789abcdef",
  "username": "PlayerName",
  "online": true,
  "risk_score": 0.45,
  "last_detections": [
    {
      "cheat": "speed_hack",
      "confidence": 0.35,
      "timestamp": 1678886400
    },
    {
      "cheat": "fly_hack",
      "confidence": 0.12,
      "timestamp": 1678886300
    }
  ]
}
```

### 2. System Metrics (`/metrics`)

This endpoint returns RustGuard's operational metrics, crucial for monitoring system health.

#### GET /metrics

|Field|Type|Description|
|---|---|---|
|`players_monitored`|Integer|Number of active players currently being monitored.|
|`detections_per_hour`|Float|Average rate of high-risk detections per hour.|
|`api_status`|String|Connection status of services (`RCON`, `Database`, `Discord`).|
|`uptime_seconds`|Integer|Time the system has been running.|

#### Successful Response Example (200 OK)

```yaml
{
  "players_monitored": 150,
  "detections_per_hour": 12.5,
  "api_status": {
    "rcon": "CONNECTED",
    "database": "CONNECTED",
    "discord": "CONNECTED"
  },
  "uptime_seconds": 86400
}
```

### Related Documents

  [[Configuration]]
  [[Database-Schema]] 
  [[RCON-Commands]] 