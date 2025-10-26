# Webhooks de Eventos

## Descripción General

Los Webhooks de RustGuard proporcionan una manera de enviar notificaciones de eventos en tiempo real a sistemas externos (como bots de Discord, sistemas de monitorización o herramientas de moderación web). A diferencia de la API pública que es para consulta, los Webhooks son un mecanismo de **notificación de salida unidireccional** (outbound).

**Uso:** Notificar inmediatamente sobre acciones de alta gravedad (Kick, Ban, Advertencias).

## Configuración y Seguridad

1. **URL de Destino:** La URL de destino del Webhook se configura en el archivo `Configuration.md` bajo la sección `notifications.webhooks`.
    
2. **Firma del Mensaje (Opcional):** Para verificar que la solicitud proviene de una fuente legítima de RustGuard, se puede configurar un secreto. El hash SHA256 del cuerpo del mensaje se incluye en la cabecera.
    

|Cabecera|Valor|
|---|---|
|`Content-Type`|`application/json`|
|`X-RG-Signature`|`sha256=<hash_del_cuerpo>`|

## Estructura del Payload

Todos los eventos de Webhook se envían como una solicitud `POST` con un cuerpo JSON.

|Clave|Tipo|Descripción|
|---|---|---|
|`event_type`|String|Tipo de evento (ej. `player_kicked`, `player_banned`, `high_warning`).|
|`timestamp`|Integer|Marca de tiempo Unix del evento (segundos).|
|`data`|Objeto|Contiene los detalles específicos del evento.|

## Detalles del Objeto `data` (Evento de Sanción)

|Clave|Tipo|Descripción|
|---|---|---|
|`uuid`|String|UUID de Mojang del jugador afectado.|
|`username`|String|Nombre de usuario del jugador.|
|`action`|String|La acción ejecutada (`KICK`, `BAN`, o `WARN`).|
|`cheat_type`|String|El método de trampa detectado (ej. `fly_hack`, `killaura`).|
|`confidence`|Float|Puntuación final de confianza que llevó a la acción (0.0-1.0).|
|`reason`|String|Razón completa utilizada en el comando RCON/mensaje de advertencia.|

## Ejemplo de Payload (Evento `player_kicked`)

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

## Documentos Relacionados

- [[Configuration]]
- [[Discord-Webhooks]]
- [[Action-Handler]] 