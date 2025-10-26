## Configuración

### Descripción General

**RustGuard** se configura mediante un único archivo YAML llamado `config.yml`. Este formato se prefiere sobre JSON debido a su legibilidad para los humanos y su compatibilidad con comentarios. La configuración se carga al inicio mediante la librería **Serde** y permanece **inmutable** durante toda la ejecución del proceso.

### Estructura Raíz

El archivo de configuración está organizado en cuatro secciones principales:

1. **proxy** – Configuración de red y del servidor proxy principal.
2. **database** – Opciones de almacenamiento y registro de eventos.
3. **detection** – Parámetros de sensibilidad y ajustes de los módulos de detección.
4. **integration** – Configuraciones de conexión con servicios externos como Discord o RCON.

---

### Ejemplo de Configuración

```yaml
proxy:
  bind_address: "0.0.0.0:25565"  # Dirección y puerto donde escucha el proxy
  target_address: "127.0.0.1:25566"  # Servidor de Minecraft destino
  max_players: 100
  motd: "§6RustGuard §fProxy Activo"

  # Configuración de seguridad de conexión
  rate_limit:
    enabled: true
    max_connections_per_ip: 5
    time_window: 10s

database:
  path: "./data/rustguard.db"  # Ruta al archivo SQLite
  log_retention_days: 30        # Días que se conservarán los registros

detection:
  flyhack:
    enabled: true
    y_tolerance: 0.05           # Tolerancia al movimiento vertical (en bloques)
    alert_threshold: 0.85       # Nivel de confianza mínimo para generar una alerta

  speedhack:
    enabled: true
    horizontal_tolerance: 0.12  # Margen de error para el movimiento horizontal
    alert_threshold: 0.9

integration:
  discord:
    enabled: true
    webhook_url: "https://discord.com/api/webhooks/..."
    alert_format: "**{player}** detectado por {module} (confianza: {confidence})"

  rcon:
    enabled: true
    address: "127.0.0.1:25575"
    password: "clave_segura"
    on_detect_action: "kick {player} [RustGuard] Comportamiento anómalo detectado"
```

---

### Detalles de Carga

Durante la inicialización, RustGuard realiza las siguientes validaciones:

- **Estructura y tipos:** Verifica que todas las claves esperadas existan y sean del tipo correcto.
    
- **Conversión de unidades:** Los valores con sufijos como `s`, `ms` o `d` se convierten automáticamente a tipos de duración.
    
- **Valores por defecto:** Si alguna clave opcional falta, se aplican los valores por defecto definidos en el código fuente.
    

En caso de errores (por ejemplo, sintaxis YAML incorrecta o rutas inexistentes), RustGuard abortará el inicio y mostrará un mensaje claro en la consola indicando el problema.