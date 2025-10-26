## RustGuard Configuration

### Overview

RustGuard is configured using a YAML file called `config.yml`. This file defines all the necessary parameters for the operation of the proxy, cheat detection, and integration with external services.

### Main Sections

1. **proxy** – Configuration of the proxy server and network.
2. **database** – Storage parameters and log retention.
3. **detection** – Sensitivity settings for each cheat module.
4. **integration** – Connection with external services like Discord and RCON.

### Example `config.yml`

```yaml
proxy:
  bind_address: "0.0.0.0:25565"
  target_address: "127.0.0.1:25566"
  max_players: 100
  motd: "§6RustGuard §fProxy Active"

  rate_limit:
    enabled: true
    max_connections_per_ip: 5
    time_window: 10s

database:
  path: "./data/rustguard.db"
  log_retention_days: 30

detection:
  flyhack:
    enabled: true
    y_tolerance: 0.05
    alert_threshold: 0.85

  speedhack:
    enabled: true
    horizontal_tolerance: 0.12
    alert_threshold: 0.9

integration:
  discord:
    enabled: true
    webhook_url: "https://discord.com/api/webhooks/..."
    alert_format: "**{player}** detected by {module} (confidence: {confidence})"

  rcon:
    enabled: true
    address: "127.0.0.1:25575"
    password: "secure_password"
    on_detect_action: "kick {player} [RustGuard] Anomalous behavior detected"
```

### Configuration Validations

- **Structure and types:** Ensures that all expected keys exist and have the correct type.
    
- **Unit conversion:** Values with suffixes (`s`, `ms`, `d`) are automatically converted to duration types.
    
- **Default values:** If optional keys are missing, default values from the code are used.
    

### Error Handling

- Invalid YAML syntax or nonexistent paths will prevent RustGuard from starting.
- A clear message is displayed in the console indicating the detected error.