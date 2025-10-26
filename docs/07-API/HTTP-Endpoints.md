# Puntos Finales HTTP (REST API)

## Descripción General

La interfaz REST de RustGuard permite a los sistemas externos (como paneles de administración o aplicaciones de terceros) consultar datos del sistema de forma eficiente. Esta API se utiliza principalmente para **lectura** de estado y métricas, no para la ejecución de comandos de sanción (los comandos se manejan vía RCON o Webhooks).

**Base URL:** `http://localhost:8080/api/v1` (El puerto es configurable)

## Autenticación

Todos los puntos finales requieren un **Token de API** configurado en el archivo `Configuration.md`. Este token debe ser proporcionado en la cabecera `Authorization` de la solicitud.

|Cabecera|Valor|
|---|---|
|`Authorization`|`Bearer <your_api_token>`|

## 1. Estado del Jugador (`/player/{uuid}`)

Este endpoint proporciona el estado actual de un jugador específico dentro del **Context System**, incluyendo su puntuación de riesgo y las últimas detecciones.

### GET /player/{uuid}

|Parámetro de Ruta|Tipo|Descripción|
|---|---|---|
|`{uuid}`|String|El UUID de Mojang del jugador a consultar.|

### Ejemplo de Respuesta Exitosa (200 OK)

```json
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

## 2. Métricas del Sistema (`/metrics`)

Este endpoint devuelve métricas operacionales de RustGuard, cruciales para la monitorización de la salud del sistema.

### GET /metrics

|Campo|Tipo|Descripción|
|---|---|---|
|`players_monitored`|Integer|Número de jugadores activos siendo monitoreados.|
|`detections_per_hour`|Float|Tasa promedio de detecciones de alto riesgo por hora.|
|`api_status`|String|Estado de conexión de los servicios (`RCON`, `Database`, `Discord`).|
|`uptime_seconds`|Integer|Tiempo que el sistema lleva corriendo.|

### Ejemplo de Respuesta Exitosa (200 OK)

```json
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

## Documentos Relacionados

- [[Configuration]]
- [[Database-Schema]]
- [[07-API/Comandos-RCON]] 