# RustGuard Documentation

> Minecraft Anti-Cheat Proxy written in Rust

## 🎯 Quick Navigation

### 📐 Architecture

- [Architecture Overview](01-Architecture/Overview.md)
- [Packet Flow](01-Architecture/Packet-Flow.md)
- [Database Schema](01-Architecture/Database-Schema.md)
- [Technology Stack](01-Architecture/Technology-Stack.md)

### ⚙️ Core Components

- [Proxy Layer](02-Core-Components/Proxy-Layer.md)
- [Detection Engine](02-Core-Components/Detection-Engine.md)
- [Action Handler](02-Core-Components/Action-Handler.md)
- [Context System](02-Core-Components/Context-System.md)

### 🎯 Detection Methods

- [Speed Hack Detection](03-Detection-Methods/Speed-Hack.md)
- [Fly Hack Detection](03-Detection-Methods/Fly-Hack.md)
- [KillAura Detection](03-Detection-Methods/KillAura.md)
- [XRay Detection](03-Detection-Methods/XRay.md)
- [Preventing False Positives](03-Detection-Methods/False-Positives.md)

### 🔌 Integration

- [RCON Setup](04-Integration/RCON-Setup.md)
- [Discord Webhooks](04-Integration/Discord-Webhooks.md)

### 📦 Setup & Usage

- [Installation](05-Setup/Installation.md)
- [Configuration](05-Setup/Configuration.md)
- [First Run](05-Setup/First-Run.md)
- [Troubleshooting](05-Setup/Troubleshooting.md)

### 🛠️ Development

- [Project Roadmap](06-Development/Project-Roadmap.md)
- [Contributing Guide](06-Development/Contributing.md)
- [Testing Strategy](06-Development/Testing.md)
- [Performance Optimization](06-Development/Performance.md)

### 🌐 API Reference

- [HTTP Endpoints](07-API/HTTP-Endpoints.md)
- [RCON Commands](07-API/RCON-Commands.md)
- [Webhook Payloads](07-API/Webhooks.md)

---

## 📊 Project Status

### Completed

- Project structure
- Documentation framework

### In Progress

- TCP Proxy implementation
- Packet parsing with Azalea
- Speed hack detection

### Planned

- Fly hack detection
- KillAura detection
- RCON integration
- Discord webhooks
- SQLite database
- Context system
- Dashboard UI

---

## 🚀 Quick Start

```bash
# Build
cargo build --release

# Run
./target/release/rustguard

# Test
cargo test
