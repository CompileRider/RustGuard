## Pila Tecnológica

- **Lenguaje:** Rust (edición 2021)
- **Ejecución asíncrona:** Tokio
- **Protocolo:** Azalea (protocolo de Minecraft
- **Base de datos:** SQLx + SQLite
- **Configuración:** Serde + YAML
- **Registro de logs:** Tracing
- **RCON:** crate rcon
- **HTTP:** Reqwest

## Arquitectura de Despliegue

```mermaid
graph LR
    subgraph "Red"
        I[Internet]
    end
    
    subgraph "Servidor"
        RP[RustGuard :25565]
        MS[Minecraft :25566]
        RCON[:25575]
    end
    
    subgraph "Servicios Externos"
        DC[Discord]
    end
    
    I --> RP
    RP <--> MS
    RP --> RCON
    RP --> DC
```

**Configuración de Puertos:**

- `25565` - Proxy de RustGuard (público)
- `25566` - Servidor de Minecraft (solo local)
- `25575` - RCON (solo local)
    

## Consideraciones de Seguridad

- La contraseña de RCON está en el archivo de configuración (**protegerla**)
- La base de datos contiene datos de jugadores (**cumplir con GDPR**)
- Los webhooks de Discord pueden ser **limitados por tasa**
- El proxy tiene acceso a la red (**asegurar con firewall**)

## Escalabilidad

**Instancia Única:**

- Maneja más de 1000 jugadores concurrentes
- ~50MB de uso de RAM
- <1% de CPU en hardware moderno

**Escalado Horizontal:**

- Se pueden usar múltiples proxies
- Base de datos compartida
- Balanceador de carga al frente

## Documentos Relacionados

- [[Flujo-de-Paquetes]]
- [[Esquema-de-Base-de-Datos]]
- [[Pila-Tecnológica]] 