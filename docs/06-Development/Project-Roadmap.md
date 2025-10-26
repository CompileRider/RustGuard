# Project Roadmap

## Current Version: 0.1.0-alpha

---

## Phase 1: Foundation ✅ (Week 1-2)

### Goals
- [x] Project structure
- [x] Documentation framework
- [x] Basic configuration system
- [x] Git repository setup

### Deliverables
- Project scaffolding
- Complete documentation templates
- README and contributing guides

---

## Phase 2: Core Proxy 🚧 (Week 2-4)

### Goals
- [ ] TCP listener with Tokio
- [ ] Bidirectional packet forwarding
- [ ] Connection state management
- [ ] Basic error handling
- [ ] Logging system

### Deliverables
```rust
// Basic proxy that forwards all traffic
let listener = TcpListener::bind("0.0.0.0:25565").await?;
loop {
    let (client, _) = listener.accept().await?;
    tokio::spawn(handle_connection(client));
}
```

### Success Criteria
- Players can connect through proxy
- No noticeable latency
- Graceful disconnection handling

---

## Phase 3: Packet Parsing 🔜 (Week 4-6)

### Goals
- [ ] Integrate azalea library
- [ ] Parse PlayerPosition packets
- [ ] Parse PlayerLook packets
- [ ] Parse PlayerAbilities packets
- [ ] Parse UseEntity packets

### Deliverables
```rust
match packet {
    ClientboundPacket::PlayerPosition(pos) => {
        println!("Player at: {:?}", pos);
    }
    _ => {}
}
```

### Success Criteria
- Successfully parse major packet types
- Extract movement data
- No packet corruption

---

## Phase 4: First Detection ⏳ (Week 6-8)

### Goals
- [ ] Speed hack detector
- [ ] Player state tracking
- [ ] Context system (effects, zones)
- [ ] Confidence scoring
- [ ] Unit tests

### Deliverables
```rust
if speed > max_allowed && !has_valid_reason(player) {
    log_detection("speed_hack", player, confidence);
}
```

### Success Criteria
- Detect obvious speed hacks (>2x normal speed)
- Zero false positives on vanilla movement
- Handle potion effects correctly

---

## Phase 5: Action System ⏳ (Week 8-10)

### Goals
- [ ] RCON client integration
- [ ] Kick functionality
- [ ] Ban functionality
- [ ] Discord webhook alerts
- [ ] Action logging

### Deliverables
```rust
// When cheat detected
rcon.cmd(&format!("kick {} Speed Hack", username)).await?;
send_discord_alert(username, cheat_type).await?;
log_to_database(detection).await?;
```

### Success Criteria
- Successfully kick players via RCON
- Discord alerts with proper formatting
- All actions logged to database

---

## Phase 6: Database Layer ⏳ (Week 10-12)

### Goals
- [ ] SQLite integration with SQLx
- [ ] Detection logging
- [ ] Player statistics
- [ ] Session tracking
- [ ] Trust score system
- [ ] Database migrations

### Deliverables
```rust
sqlx::query!(
    "INSERT INTO detections (player_id, type, confidence) VALUES (?, ?, ?)",
    player_id, cheat_type, confidence
).execute(&pool).await?;
```

### Success Criteria
- All detections persisted
- Query interface for stats
- Automatic schema migrations

---

## Phase 7: More Detectors ⏳ (Week 12-16)

### Goals
- [ ] Fly hack detection
- [ ] KillAura detection
- [ ] X-Ray detection (basic)
- [ ] Auto-clicker detection
- [ ] Reach hack detection

### Deliverables
Each detector with:
- Core algorithm
- Context awareness
- Unit tests
- Documentation

### Success Criteria
- Each detector >95% accuracy
- <0.5% false positive rate
- Comprehensive test coverage

---

## Phase 8: Context System ⏳ (Week 16-18)

### Goals
- [ ] Safe zone system
- [ ] Effect whitelist
- [ ] Custom item detection
- [ ] Learning mode
- [ ] False positive tracking

### Deliverables
```yaml
whitelist:
  safe_zones:
    - name: "spawn"
      allow_fly: true
  allowed_effects:
    - name: "mythicmobs:lightning_boots"
      max_speed: 30.0
```

### Success Criteria
- Zero false positives with MythicMobs
- Automatic pattern learning
- Admin override system

---

## Phase 9: Java Bridge (Optional) ⏳ (Week 18-20)

### Goals
- [ ] Lightweight Spigot plugin
- [ ] Effect notification API
- [ ] Item registration
- [ ] Zone management
- [ ] Bidirectional communication

### Deliverables
```java
// Java plugin
RustGuardAPI.notifyEffect(player, "speed_boost", 30.0, 5000);
```

### Success Criteria
- <10KB plugin size
- <1ms notification latency
- Works on Spigot 1.8-1.20

---

## Phase 10: Dashboard (Future) 🔮

### Goals
- [ ] Web UI (React/Svelte)
- [ ] Real-time statistics
- [ ] Detection history
- [ ] Player management
- [ ] Configuration editor

### Mockup