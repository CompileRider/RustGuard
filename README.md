# RustGuard - Anti-Cheat Proxy para Minecraft

![Estado del Proyecto](https://img.shields.io/badge/Estado-En%20desarrollo-yellow)
![Lenguaje](https://img.shields.io/badge/Lenguaje-Rust-blue)
![Versión](https://img.shields.io/badge/Versión-0.1.0-lightgrey)
![License](https://img.shields.io/badge/License-GPL--3.0-green)
![Discord](https://img.shields.io/badge/Discord-Online-7289DA)
![Issues](https://img.shields.io/github/issues/usuario/RustGuard-red)
![Forks](https://img.shields.io/github/forks/usuario/RustGuard-blue)
![Stars](https://img.shields.io/github/stars/usuario/RustGuard-yellow)

## Descripción

**RustGuard** es un sistema de detección de trampas en tiempo real para servidores de Minecraft. Funciona como un proxy transparente, sin modificar el servidor existente, y es compatible con cualquier versión de Minecraft configurando correctamente el protocolo.

Detecta cheats automáticamente como:
- Speed hacks
- Fly hacks
- Kill Aura
- X-Ray
- Auto-clicker
- Inventory hacks

Cada anomalía genera alertas inmediatas en Discord con datos concretos: coordenadas, velocidad, timestamp y tipo de hack detectado.

## Ventajas

### Para administradores:
- Detección 24/7 sin supervisión manual
- Alertas instantáneas en Discord
- Datos objetivos y verificables
- Historial de jugadores sospechosos
- Menos trabajo manual y especulativo

### Para la comunidad:
- Juego más justo
- Mejor experiencia
- Mayor confianza en el servidor

### Técnico:
- No modifica el servidor
- Compatible con múltiples versiones
- Escalable y eficiente
- Personalizable

## Estado del proyecto

Actualmente en desarrollo, con archivos iniciales preparados. Próximamente:
- Proxy básico
- Parsing de posición de jugadores
- Detección de cheats básicos
- Webhook de alertas a Discord

## Posibles expansiones futuras
- Dashboard web para estadísticas en tiempo real
- Base de datos de historial de infracciones
- Integración de Machine Learning
- API para nuevas detecciones
- Modelo SaaS para otros servidores

## Cómo contribuir

Cualquier idea, sugerencia o contribución será bienvenida.

## Licencia

Este proyecto está bajo **GNU General Public License v3.0 (GPL-3.0)**
