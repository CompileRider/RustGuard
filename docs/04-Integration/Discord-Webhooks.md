# Webhooks de Discord

## Descripción General

La integración con Discord proporciona notificaciones en tiempo real al equipo de moderación cuando RustGuard detecta una acción sospechosa o ejecuta un castigo (Warn, Kick, Ban). Esto utiliza un **Webhook de Discord** estándar y la crate `reqwest` para peticiones HTTP asíncronas.

## Estructura del Payload

El formato de notificación utiliza el objeto `Embed` de Discord para presentar información clara y estructurada. Utilizamos la crate `serde_json` para construir el payload JSON.

### Ejemplo de Payload (Embed)

```yaml
{
  "embeds": [
    {
      "title": "🚨 VIOLACIÓN DETECTADA: KillAura",
      "color": 16711680,
      "fields": [
        {"name": "Jugador", "value": "PlayerName (UUID)", "inline": true},
        {"name": "Acción", "value": "KICKED", "inline": true},
        {"name": "Confianza", "value": "97.5%", "inline": true},
        {"name": "Detalles", "value": "Reach: 4.2 | Suspicious Indicators: 3", "inline": false}
      ],
      "footer": {"text": "RustGuard Anti-Cheat System"},
      "timestamp": "2024-01-01T12:00:00Z"
    }
  ]
}
```


## Lógica de Notificación

El `Action Handler` llama a la función `notify_discord` si el nivel de acción supera el `min_alert_level` configurado (ej. `Warn`).

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

    /// Envía el payload de detección a Discord.
    pub async fn notify_discord(&self, player: &PlayerState, detection: &Detection, action_type: &str) {
        let (color, title_prefix) = match action_type {
            "BAN" => (0xFF0000, "🚨 EXTREME VIOLACIÓN (BAN)"), // Rojo
            "KICK" => (0xFFA500, "⚠️ VIOLACIÓN ALTA (KICK)"), // Naranja
            _ => (0x00BFFF, "🔔 Detección Registrada"),       // Azul claro
        };

        let embed = json!({
            "title": format!("{} - {}", title_prefix, detection.cheat_type),
            "color": color,
            "fields": [
                {
                    "name": "👤 Jugador", 
                    "value": format!("{} (`{}`)", player.username, player.uuid), 
                    "inline": false
                },
                {
                    "name": "📈 Confianza", 
                    "value": format!("{:.1}%", detection.confidence * 100.0), 
                    "inline": true
                },
                {
                    "name": "🔨 Acción", 
                    "value": action_type, 
                    "inline": true
                },
                {
                    "name": "🔬 Detalles del Detector", 
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


## Configuración Requerida

La URL del Webhook debe proporcionarse en el archivo `config.yml`.

```yaml
integration:
  discord_webhook:
    enabled: true
    url: "[https://discord.com/api/webhooks/YOUR_UNIQUE_ID/YOUR_TOKEN](https://discord.com/api/webhooks/YOUR_UNIQUE_ID/YOUR_TOKEN)" 
    min_alert_level: "Warn" # Solo notificar si la acción es Warn, Kick o Ban
```


## Documentos Relacionados

[[Action-Handler]] [[Configuration]]