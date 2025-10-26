# Technology Stack

## Core Technologies

### Rust 🦀
**Version:** 1.75+
**Why:** Memory safety, performance, concurrency
```rust
// Zero-cost abstractions
let speed = positions.iter()
    .zip(positions.iter().skip(1))
    .map(|(a, b)| a.distance(b))
    .sum::<f64>();
```

### Tokio ⚡
**Version:** 1.x
**Purpose:** Async runtime

**Why Tokio:**
- Handle thousands of connections
- Non-blocking I/O
- Mature ecosystem
```rust
#[tokio::main]
async fn main() {
    // Concurrent connection handling
    let listener = TcpListener::bind("0.0.0.0:25565").await?;
    
    loop {
        let (socket, _) = listener.accept().await?;
        tokio::spawn(handle_connection(socket));
    }
}
```

### Azalea 📦
**Version:** 0.10+
**Purpose:** Minecraft protocol parsing

**Features:**
- Protocol version support
- Packet serialization/deserialization
- Type-safe packet handling
```rust
use azalea::protocol::packets::game::ClientboundGamePacket;

match packet {
    ClientboundGamePacket::PlayerPosition(pos) => {
        // Handle position
    }
    _ => {}
}
```

### SQLx 💾
**Version:** 0.8+
**Purpose:** Database operations

**Why SQLx over Diesel:**
- Async first
- Compile-time query checking
- Lighter weight
```rust
let player = sqlx::query_as!(
    Player,
    "SELECT * FROM players WHERE username = ?",
    username
)
.fetch_one(&pool)
.await?;
```

### Serde 📝
**Version:** 1.x
**Purpose:** Serialization

**Uses:**
- Config files (YAML)
- JSON logging
- Discord payloads
```rust
#[derive(Serialize, Deserialize)]
struct Config {
    proxy: ProxyConfig,
    detection: DetectionConfig,
}
```

## Supporting Libraries

### rcon
**Purpose:** Remote console client
```rust
let mut rcon = Connection::connect("127.0.0.1:25575", "password").await?;
rcon.cmd("kick Player").await?;
```

### reqwest
**Purpose:** HTTP client (Discord webhooks)
```rust
reqwest::Client::new()
    .post(webhook_url)
    .json(&payload)
    .send()
    .await?;
```

### tracing
**Purpose:** Structured logging
```rust
tracing::info!(player = %username, "Connection established");
tracing::warn!(speed = %speed, "Suspicious movement");
```

### chrono
**Purpose:** Timestamps
```rust
let now = Utc::now().timestamp();
```

## Development Tools

### cargo-watch
**Purpose:** Hot reload during development
```bash
cargo install cargo-watch
cargo watch -x run
```

### cargo-criterion
**Purpose:** Benchmarking
```bash
cargo install cargo-criterion
cargo criterion
```

### cargo-audit
**Purpose:** Security audits
```bash
cargo install cargo-audit
cargo audit
```

## Alternative Considerations

### Why NOT these?

**Diesel vs SQLx**
- ❌ Diesel: Sync-only, heavier
- ✅ SQLx: Async, lighter, compile-time checks

**Actix vs Tokio**
- ❌ Actix: Actor model overhead
- ✅ Tokio: Direct control, lighter

**PostgreSQL vs SQLite**
- ❌ PostgreSQL: Overkill, requires separate server
- ✅ SQLite: Embedded, zero-config

**JSON config vs YAML**
- ❌ JSON: No comments, verbose
- ✅ YAML: Human-friendly, comments

## Performance Characteristics

| Component | Latency | Memory | CPU |
|-----------|---------|--------|-----|
| Tokio runtime | ~0.1ms | 10MB | <1% |
| Packet parsing | ~0.2ms | negligible | <1% |
| Detection | ~0.5ms | 100KB/player | 1-2% |
| SQLite write | ~1ms | 20MB | <1% |
| RCON call | ~5ms | negligible | <0.1% |
| Discord webhook | ~100ms | negligible | <0.1% |

## Version Compatibility

### Rust Edition
- 2021 edition required
- async/await stable
- const generics

### Minecraft Versions
Via Azalea:
- 1.8.x ✅
- 1.12.x ✅
- 1.16.x ✅
- 1.19.x ✅
- 1.20.x ✅

## Build Configuration

### Cargo.toml
```toml
[profile.release]
opt-level = 3
lto = true
codegen-units = 1
panic = 'abort'
strip = true
```

### Compile Times
- Debug build: ~2 minutes
- Release build: ~5 minutes
- Incremental: ~10 seconds

## Related Documents
- [[Overview]]
- [[Packet-Flow]]