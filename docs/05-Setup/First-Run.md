### 🧩 First-Run.md

````
# First Run

RustGuard is ready for its first execution after configuring `config.yml`. This document guides the user to start the proxy, verify its operation, and validate that the connection to Minecraft is correct.

## 🚀 Initial Execution

```bash
cargo run --release
````

Or, if already compiled:

```
./target/release/rustguard
```

### ✅ Verification

- The proxy listens on `25565`.
- The Minecraft server must be at `localhost:25566`.
- Expected console message:

```
[INFO] RustGuard proxy started on :25565
```

### 🧠 Recommendations

- Run inside `tmux` or `screen`.
- Check logs at `logs/latest.log`.
- Connect with Minecraft client to test functionality.
### 🌐 Initial Flow

```mermaid
graph TB
    C[Minecraft Client] --> RP[RustGuard Proxy :25565]
    RP --> MS[Minecraft Server :25566]
    RP --> DE[Detection Engine]
    DE --> RC[RCON Client]
    DE --> DW[Discord Webhook]
```
