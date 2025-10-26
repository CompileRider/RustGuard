# RustGuard Documentation 🛡️

> Minecraft Anti-Cheat Proxy escrito en Rust

---

## 🎯 Navegación Rápida

### 📐 Arquitectura

- [Architecture Overview](01-Architecture/Overview.md)
- [Packet Flow](01-Architecture/Packet-Flow.md)
- [Database Schema](01-Architecture/Database-Schema.md)
- [Technology Stack](01-Architecture/Technology-Stack.md)

### ⚙️ Componentes Principales

- [Proxy Layer](02-Core-Components/Proxy-Layer.md)
- [Detection Engine](02-Core-Components/Detection-Engine.md)
- [Action Handler](02-Core-Components/Action-Handler.md)
- [Context System](02-Core-Components/Context-System.md)

### 🎯 Métodos de Detección

- [Speed Hack Detection](03-Detection-Methods/Speed-Hack.md)
- [Fly Hack Detection](03-Detection-Methods/Fly-Hack.md)
- [KillAura Detection](03-Detection-Methods/KillAura.md)
- [XRay Detection](03-Detection-Methods/XRay.md)
- [Preventing False Positives](03-Detection-Methods/False-Positives.md)

### 🔌 Integración

- [RCON Setup](04-Integration/RCON-Setup.md)
- [Discord Webhooks](04-Integration/Discord-Webhooks.md)

### 📦 Configuración y Uso

- [Installation](05-Setup/Installation.md)
- [Configuration](05-Setup/Configuration.md)
- [First Run](05-Setup/First-Run.md)
- [Troubleshooting](05-Setup/Troubleshooting.md)

### 🛠️ Desarrollo

- [Project Roadmap](06-Development/Project-Roadmap.md)
- [Contributing Guide](06-Development/Contributing.md)
- [Testing Strategy](06-Development/Testing.md)
- [Performance Optimization](06-Development/Performance.md)

### 🌐 Referencia de API

- [HTTP Endpoints](07-API/HTTP-Endpoints.md)
- [RCON Commands](07-API/RCON-Commands.md)
- [Webhook Payloads](07-API/Webhooks.md)

---

## 📊 Estado del Proyecto

### ✅ Completado

- Estructura del proyecto
- Marco de documentación

### 🚧 En Progreso

- Implementación del Proxy TCP
- Análisis de paquetes con Azalea
- Detección de Speed hack

### 📅 Planificado

- Detección de Fly hack
- Detección de KillAura
- Integración RCON
- Webhooks de Discord
- Base de datos SQLite
- Sistema de contexto
- Interfaz de usuario (Dashboard UI)

---

## 🚀 Inicio Rápido

```bash
# Compilar
cargo build --release

# Ejecutar
./target/release/rustguard

# Probar
cargo test
