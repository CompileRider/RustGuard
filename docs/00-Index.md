# RustGuard Documentation

> Minecraft Anti-Cheat Proxy written in Rust

## 🎯 Quick Navigation

### 📐 Architecture

- [[01-Architecture/Overview|Architecture Overview]]
- [[01-Architecture/Packet-Flow|Packet Flow]]
- [[01-Architecture/Database-Schema|Database Schema]]
- [[01-Architecture/Technology-Stack|Technology Stack]]

### ⚙️ Core Components

- [[02-Core-Components/Proxy-Layer|Proxy Layer]]
- [[02-Core-Components/Detection-Engine|Detection Engine]]
- [[02-Core-Components/Action-Handler|Action Handler]]
- [[02-Core-Components/Context-System|Context System]]

### 🎯 Detection Methods

- [[03-Detection-Methods/Speed-Hack|Speed Hack Detection]]
- [[03-Detection-Methods/Fly-Hack|Fly Hack Detection]]
- [[03-Detection-Methods/KillAura|KillAura Detection]]
- [[03-Detection-Methods/XRay|XRay Detection]]
- [[03-Detection-Methods/False-Positives|Preventing False Positives]]

### 🔌 Integration

- [[04-Integration/RCON-Setup|RCON Setup]] 
- [[04-Integration/Discord-Webhooks|Discord Webhooks]] 

### 📦 Setup & Usage

- [[05-Setup/Installation|Installation]]    
- [[05-Setup/Configuration|Configuration]]
- [[05-Setup/First-Run|First Run]]
- [[05-Setup/Troubleshooting|Troubleshooting]]

### 🛠️ Development

- [[06-Development/Project-Roadmap|Project Roadmap]]
- [[06-Development/Contributing|Contributing Guide]]
- [[06-Development/Testing|Testing Strategy]]
- [[06-Development/Performance|Performance Optimization]]

### 🌐 API Reference

- [[07-API/HTTP-Endpoints|HTTP Endpoints]]    
- [[07-API/RCON-Commands|RCON Commands]]
- [[07-API/Webhooks|Webhook Payloads]]

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
