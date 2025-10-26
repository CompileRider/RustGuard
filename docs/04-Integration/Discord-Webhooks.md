# Discord Webhooks Integration

## Overview

The Discord integration provides real-time notifications to the moderation team when RustGuard detects suspicious behavior or executes an action (Warn, Kick, Ban). It uses a standard **Discord Webhook** and the `reqwest` crate for asynchronous HTTP requests.

## Payload Structure

The notification format uses Discord's `Embed` object for clear and structured information. `serde_json` is used to build the JSON payload.

### Example Payload (Embed)

```json
{
  "embeds": [
    {
      "title": "🚨 DETECTION: KillAura",
      "color": 16711680,
      "fields": [
        {"name": "Player", "value": "PlayerName (UUID)", "inline": true},
        {"name": "Action", "value": "KICKED", "inline": true},
        {"name": "Confidence", "value": "97.5%", "inline": true},
        {"name": "Details", "value": "Reach: 4.2 | Suspicious Indicators: 3", "inline": false}
      ],
      "footer": {"text": "RustGuard Anti-Cheat System"},
      "timestamp": "2024-01-01T12:00:00Z"
    }
  ]
}
```

## Notification Logic

The `Action Handler` calls `notify_discord` if the action level exceeds the configured `min_alert_level` (e.g., `Warn`).

```rust
use reqwest::Client;
use serde_json::{json, Value};

pub struct DiscordNotifier {
    client: Client,
    webhook_url: String,
}

impl DiscordNotifier {
    pub fn new(webhook_url: String) -> Self {
        DiscordNotifier {
            client: Client::new(),
            webhook_url,
        }
    }

    /// Sends a detection payload to Discord.
    pub async fn notify_discord(&self, player: &PlayerState, detection: &Detection, action_type: &str) {
        let (color, title_prefix) = match action_type {
            "BAN" => (0xFF0000, "🚨 EXTREME VIOLATION (BAN)"), // Red
            "KICK" => (0xFFA500, "⚠️ HIGH VIOLATION (KICK)"),  // Orange
            _ => (0x00BFFF, "🔔 Detection Logged"),             // Light Blue
        };

        let embed = json!({
            "title": format!("{} - {}", title_prefix, detection.cheat_type),
            "color": color,
            "fields": [
                {
                    "name": "👤 Player", 
                    "value": format!("{} (`{}`)", player.username, player.uuid), 
                    "inline": false
                },
                {
                    "name": "📈 Confidence", 
                    "value": format!("{:.1}%", detection.confidence * 100.0), 
                    "inline": true
                },
                {
                    "name": "🔨 Action", 
                    "value": action_type, 
                    "inline": true
                },
                {
                    "name": "🔬 Detector Details", 
                    "value": detection.details.to_string(), 
                    "inline": false
                }
            ],
            "footer": {"text": "RustGuard Anti-Cheat System"},
            "timestamp": Utc::now().to_rfc3339()
        });

        let payload = json!({"embeds": [embed]});

        match self.client.post(&self.webhook_url).json(&payload).send().await {
            Ok(res) if res.status().is_success() => {
                tracing::debug!("Discord notification sent successfully.")
            }
            Ok(res) => {
                tracing::error!("Discord notification failed (HTTP {}): {:?}", res.status(), res.text().await)
            }
            Err(e) => {
                tracing::error!("Failed to send Discord notification: {}", e)
            }
        }
    }
}
```

## Required Configuration

The webhook URL must be provided in `config.yml`.

```yaml
integration:
  discord_webhook:
    enabled: true
    url: "https://discord.com/api/webhooks/YOUR_UNIQUE_ID/YOUR_TOKEN"
    min_alert_level: "Warn" # Notify only if action is Warn, Kick, or Ban
```

## Related Documents

[Action-Handler](../02-Core-Components/Action-Handler.md)

[Configuration](../05-Setup/Configuration.md)
