## Tech Stack

* **Language:** Rust (2021 edition)
* **Asynchronous runtime:** Tokio
* **Protocol:** Azalea (Minecraft protocol)
* **Database:** SQLx + SQLite
* **Configuration:** Serde + YAML
* **Logging:** Tracing
* **RCON:** rcon crate
* **HTTP:** Reqwest

## Deployment Architecture

```mermaid
graph LR
    subgraph "Network"
        I[Internet]
    end
    
    subgraph "Server"
        RP[RustGuard :25565]
        MS[Minecraft :25566]
        RCON[:25575]
    end
    
    subgraph "External Services"
        DC[Discord]
    end
    
    I --> RP
    RP <--> MS
    RP --> RCON
    RP --> DC
```
