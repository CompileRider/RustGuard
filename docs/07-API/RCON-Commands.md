# Comandos RCON de RustGuard

## Descripción General

El **RCON Interface** de RustGuard es el componente que traduce una acción de sanción decidida por el Action Handler (como KICK o BAN) en comandos ejecutables que se envían directamente al servidor de Minecraft a través del protocolo RCON.

Esta documentación detalla la plantilla de los comandos que RustGuard ejecuta.

## Configuración de Plantillas

Los comandos RCON no están codificados de forma rígida (hardcoded); en su lugar, utilizan plantillas configurables. Esto permite a los administradores adaptar los comandos a diferentes plugins de gestión de permisos o de baneo (como LuckPerms, EssentialsX, BanManager, etc.).

Todas las plantillas se definen en el archivo de configuración principal (`Configuration.md`).

### Variables de Plantilla

RustGuard soporta las siguientes variables que se reemplazan dinámicamente en tiempo de ejecución:

|Variable|Descripción|Ejemplo de Valor|
|---|---|---|
|`{player_name}`|El nombre de usuario del jugador afectado.|`Cheater_42`|
|`{uuid}`|El UUID de Mojang del jugador.|`0123...456`|
|`{reason}`|La razón completa de la violación, generada por el Action Handler.|`fly_hack (Confidence: 0.96)`|
|`{cheat_type}`|El tipo de trampa principal detectada.|`fly_hack`|
|`{confidence}`|La puntuación numérica de confianza (0.0 a 1.0).|`0.96`|
|`{duration}`|Duración del baneo (solo para comandos BAN).|`1d` (1 día)|

## Comandos de Acción Predeterminados

Estas son las plantillas predeterminadas que RustGuard utiliza para cada acción de sanción.

### 1. Comando KICK (Expulsar)

Se usa para violaciones de confianza media o baja.

|Plantilla Predeterminada|Descripción|
|---|---|
|`kick {player_name} [RustGuard] Suspected violation: {reason}`|Utiliza el comando nativo de Minecraft.|

**Ejemplo de Comando Enviado:**

```
kick Cheater_42 [RustGuard] Suspected violation: fly_hack (Confidence: 0.96)
```

### 2. Comando BAN (Banear)

Se usa para violaciones de alta confianza o reincidencia. La duración por defecto es configurable, pero se usa `{duration}` para insertarla.

|Plantilla Predeterminada|Descripción|
|---|---|
|`ban {player_name} {duration} [RustGuard] Confirmed cheating: {reason}`|Asume que el plugin de baneo soporta el formato de duración.|

**Ejemplo de Comando Enviado (con duración de 7 días):**

```
ban Cheater_42 7d [RustGuard] Confirmed cheating: killaura (Confidence: 1.0)
```

### 3. Comando WARN (Advertir)

Se usa para notificar al jugador sobre una violación de baja confianza sin sanción.

|Plantilla Predeterminada|Descripción|
|---|---|
|`tellraw {player_name} ["",{"text":"[RustGuard] Warning: Do not use illegal modifications.","color":"red"}]`|Utiliza el comando nativo `tellraw` para enviar un mensaje con formato JSON.|

**Ejemplo de Comando Enviado:**

```json
tellraw PlayerName ["",{"text":"[RustGuard] Warning: Do not use illegal modifications.","color":"red"}]
```

## Documentos Relacionados

- [[Integration/RCON-Interface]]
- [[Action-Handler]]
- [[Configuration]] 