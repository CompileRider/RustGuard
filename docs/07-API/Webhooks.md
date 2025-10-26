# Event Webhooks

## Overview

RustGuard Webhooks provide a way to send real-time event notifications to external systems (such as Discord bots, monitoring systems, or web moderation tools). Unlike the public API, which is for querying, Webhooks are a **one-way outbound notification mechanism**.

**Use Case:** Immediately notify about high-severity actions (Kick, Ban, Warnings).

## Configuration and Security

1. **Destination URL:** The Webhook target URL is configured in the `Configuration.md` file under the section `notifications.webhooks`.
    
2. **Message Signature (Optional):** To verify that the request comes from a legitimate RustGuard source, a secret can be configured. The SHA256 hash of the message body is included in the header.
    

|Header|Value|
|---|---|
|`Content-Type`|`application/json`|
|`X-RG-Signature`|`sha256=<body_hash>`|

## Payload Structure

All Webhook events are sent as a `POST` request with a JSON body.

|Key|Type|Description|
|---|---|---|
|`event_type`|String|Event type (e.g., `player_kicked`, `player_banned`, `high_warning`).|
|`timestamp`|Integer|Unix timestamp of the event (seconds).|
|`data`|Object|Contains the specific details of the event.|

## `data` Object Details (Punishment Event)

|Key|Type|Description|
|---|---|---|
|`uuid`|String|Mojang UUID of the affected player.|
|`username`|String|Username of the player.|
|`action`|String|Action executed (`KICK`, `BAN`, or `WARN`).|
|`cheat_type`|String|Type of cheat detected (e.g., `fly_hack`, `killaura`).|
|`confidence`|Float|Final confidence score that led to the action (0.0-1.0).|
|`reason`|String|Full reason used in the RCON command/warning message.|

## Example Payload (`player_kicked` Event)

```json
{
  "event_type": "player_kicked",
  "timestamp": 1678886400,
  "data": {
    "uuid": "01234567-89ab-cdef-0123-456789abcdef",
    "username": "Cheater_42",
    "action": "KICK",
    "cheat_type": "fly_hack",
    "confidence": 0.96,
    "reason": "[RustGuard] Kicked for high-confidence violation: fly_hack"
  }
}
```

## Related Documents

- [[Configuration]]
- [[Discord-Webhooks]]
- [[Action-Handler]]