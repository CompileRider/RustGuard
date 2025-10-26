# Interfaz RCON

## Descripción General

La Interfaz RCON (Remote Console) es el canal de comunicación que RustGuard utiliza para ejecutar comandos de castigo (Kick, Ban, Tempban) directamente en el servidor de Minecraft. Utiliza el protocolo RCON estándar de Minecraft a través de TCP, garantizando la ejecución instantánea de las acciones.

## Tecnología

Se utiliza la crate `rcon` de Rust, la cual proporciona un cliente asíncrono que se integra perfectamente con **Tokio**.

## Implementación de Conexión

La interfaz se inicializa una vez al inicio y mantiene una conexión persistente si es posible, aunque el protocolo RCON a menudo requiere reconexiones por comando.

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

    /// Ejecuta un comando en el servidor.
    /// Utiliza un timeout para evitar bloqueos del hilo principal.
    pub async fn execute_command(&self, command: &str) -> Result<String, RconError> {
        let connect_future = Connection::connect(
            &self.address, 
            &self.password
        );
        
        // Conectar y autenticar
        let mut conn = match tokio::time::timeout(self.timeout, connect_future).await {
            Ok(Ok(c)) => c,
            Ok(Err(e)) => return Err(RconError::ConnectionFailed(e.to_string())),
            Err(_) => return Err(RconError::Timeout("RCON connection timeout".to_string())),
        };

        // Enviar comando
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


## Uso en el Action Handler

El `Action Handler` utiliza la función `execute_command` para aplicar castigos de alta confianza.

- **Kick:** `kick <username> [reason]`
    
- **Ban:** `ban <username> [reason]`
    

**Ejemplo de Comando de Kick:**

```
kick PlayerName [RustGuard] Kicked for high-confidence violation: speed_hack
```

## Configuración Requerida

Para que RCON funcione, el servidor de Minecraft debe tener `enable-rcon=true` y `rcon.password` configurados en `server.properties`. Los detalles de conexión se definen en `config.yml`.

```yaml
integration:
  rcon:
    enabled: true
    address: "127.0.0.1:25575"
    password: "secure_rcon_password"
    timeout: 5 # RCON command timeout in seconds
```


## Documentos Relacionados

[[Action-Handler]] [[Configuration]]