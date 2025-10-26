# RCON Interface

## Overview

The RCON (Remote Console) Interface is the communication channel RustGuard uses to execute punishment commands (Kick, Ban, Tempban) directly on the Minecraft server. It uses the standard Minecraft RCON protocol over TCP, ensuring instant execution of actions.

## Technology

Rust's `rcon` crate is used, providing an asynchronous client that integrates seamlessly with **Tokio**.

## Connection Implementation

The interface initializes once at startup and maintains a persistent connection if possible, although the RCON protocol often requires reconnecting per command.

```rust
use tokio_rcon::Connection;
use std::time::Duration;

pub struct RconInterface {
    address: String,
    password: String,
    timeout: Duration,
}

impl RconInterface {
    pub fn new(address: String, password: String, timeout_secs: u64) -> Self {
        RconInterface {
            address,
            password,
            timeout: Duration::from_secs(timeout_secs),
        }
    }

    /// Executes a command on the server.
    /// Uses a timeout to avoid blocking the main thread.
    pub async fn execute_command(&self, command: &str) -> Result<String, RconError> {
        let connect_future = Connection::connect(&self.address, &self.password);
        
        // Connect and authenticate
        let mut conn = match tokio::time::timeout(self.timeout, connect_future).await {
            Ok(Ok(c)) => c,
            Ok(Err(e)) => return Err(RconError::ConnectionFailed(e.to_string())),
            Err(_) => return Err(RconError::Timeout("RCON connection timeout".to_string())),
        };

        // Send command
        let response = match tokio::time::timeout(self.timeout, conn.cmd(command)).await {
            Ok(Ok(res)) => res,
            Ok(Err(e)) => return Err(RconError::CommandFailed(e.to_string())),
            Err(_) => return Err(RconError::Timeout("RCON command timeout".to_string())),
        };

        tracing::info!("RCON executed command: '{}', Response: {}", command, response);
        Ok(response)
    }
}
```

## Usage in the Action Handler

The `Action Handler` uses the `execute_command` function to apply high-confidence punishments.

- **Kick:** `kick <username> [reason]`
    
- **Ban:** `ban <username> [reason]`
    

**Example Kick Command:**

```
kick PlayerName [RustGuard] Kicked for high-confidence violation: speed_hack
```

## Required Configuration

For RCON to work, the Minecraft server must have `enable-rcon=true` and `rcon.password` set in `server.properties`. Connection details are defined in `config.yml`.

```yaml
integration:
  rcon:
    enabled: true
    address: "127.0.0.1:25575"
    password: "secure_rcon_password"
    timeout: 5 # RCON command timeout in seconds
```

## Related Documents

[[Action-Handler]] 
[[Configuration]] 