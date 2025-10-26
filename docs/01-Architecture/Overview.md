# Architecture Overview

## System Design

RustGuard operates as a transparent TCP proxy between Minecraft clients and the server.

```mermaid
graph TB
    subgraph "Client Layer"
        C1[MC Client 1]
        C2[MC Client 2]
        C3[MC Client N]
    end
    
    subgraph "RustGuard Proxy"
        L[TCP Listener :25565]
        CH[Connection Handler]
        PP[Packet Parser]
        DE[Detection Engine]
    end
    
    subgraph "Action Layer"
        RC[RCON Client]
        DW[Discord Webhook]
        DB[(SQLite DB)]
    end
    
    subgraph "Server"
        MS[MC Server :25566]
        RCON[RCON :25575]
    end
    
    C1 --> L
    C2 --> L
    C3 --> L
    
    L --> CH
    CH --> PP
    PP --> DE
    
    DE --> RC
    DE --> DW
    DE --> DB
    
    RC --> RCON
    RCON --> MS
    
    CH <--> MS
```

## Core Principles

### 1. Zero Server Impact

* Runs as separate process
* Server never blocked by anti-cheat
* If proxy crashes, server continues

### 2. Transparent Operation

* Players connect normally
* No client modifications needed
* Seamless experience for legitimate players

### 3. Context-Aware Detection

* Understands server plugins
* Accounts for custom items
* Recognizes safe zones
* Prevents false positives

### 4. Performance First

* Async/await with Tokio
* Zero-copy packet handling
* Efficient memory usage
* Scales to thousands of players

## Data Flow

### Inbound (Client → Server)

```mermaid
flowchart LR
%% Client → Proxy → Server
C["Minecraft Client"] -->|TCP :25565| L["RustGuard TCP Listener"]
L -->|Accept| CH["Connection Handler"]
CH -->|Decode packets| PP["Packet Parser"]
PP -->|Analyze| DE["Detection Engine"]

%% Detection Engine Actions
DE -->|Persist| DB[("SQLite / WAL")]
DE -->|Notify| DW["Discord Webhook"]
DE -->|Mitigate| RC["RCON Client"]

%% Server communication
RC -->|Send command| RCON["RCON :25575"]
RCON -->|Control| MS["MC Server :25566"]

%% Main TCP path
CH <--> MS

%% Observability
DE -->|Metrics / Logs| MON["Prometheus / OpenTelemetry"]
MON --> OPS["Ops / SRE"]

classDef infra fill:#f3f4f6,stroke:#333,stroke-width:1px;
class L,CH,PP,DE,DB,RC,RCON,MS,MON infra;
```

### Outbound (Server → Client)

```mermaid
flowchart RL
%% Server → Proxy → Client
MS["MC Server :25566"] -->|Game packets| CH["Connection Handler"]
CH -->|Inspect| PP["Packet Parser"]
PP -->|Filter / Validate| DE["Detection Engine"]
DE -->|Record| DB[("SQLite / WAL")]
DE -->|Alerts| DW["Discord Webhook"]
DE -->|Metrics| MON["Prometheus / OpenTelemetry"]

%% Return path
CH -->|Send packets| C["Minecraft Client"]

%% Observability
MON --> OPS["Ops / SRE"]

classDef infra fill:#f3f4f6,stroke:#333,stroke-width:1px;
class CH,PP,DE,DB,MON,MS,OPS infra;
```
