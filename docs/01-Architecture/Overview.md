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
- Runs as separate process
- Server never blocked by anti-cheat
- If proxy crashes, server continues

### 2. Transparent Operation
- Players connect normally
- No client modifications needed
- Seamless experience for legitimate players

### 3. Context-Aware Detection
- Understands server plugins
- Accounts for custom items
- Recognizes safe zones
- Prevents false positives

### 4. Performance First
- Async/await with Tokio
- Zero-copy packet handling
- Efficient memory usage
- Scales to thousands of players

## Data Flow

### Inbound (Client → Server)

```mermaid
flowchart LR
%% Edge / client
C["Client<br/>(Browser / Mobile / MC Client)"] -->|TCP:25565| CDN["Optional CDN / Proxy"]
CDN -->|Forward| LB["Load Balancer / Anycast"]


%% Fronting web layer
LB -->|Route TCP| L["TCP Listener<br/>:25565 (RustGuard)"]
L -->|Accept| CH["Connection Handler"]
CH -->|Parse frames| PP["Packet Parser"]
PP -->|Inspect| DE["Detection Engine"]


%% Action & side-effects
DE -->|Log / Persist| DB[("SQLite / WAL")]
DE -->|Alert| DW["Discord Webhook"]
DE -->|Mitigate| RC["RCON Client"]
RC -->|Command| RCON["RCON<br/>:25575"]
RCON -->|Control| MS["MC Server<br/>:25566"]


%% Direct proxying
CH <--> MS
%% Observability
DE -->|Metrics / Traces| Logging["Prometheus / OpenTelemetry"]
Logging --> Ops["Ops / SRE"]


classDef infra fill:#f3f4f6,stroke:#333,stroke-width:1px;
class L,CH,PP,DE,DB,RC,RCON,MS,Logging infra;
```
