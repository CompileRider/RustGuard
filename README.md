# RustGuard - Anti-Cheat Proxy para Minecraft

![Estado del Proyecto](https://img.shields.io/badge/Estado-En%20desarrollo-yellow)
![Lenguaje](https://img.shields.io/badge/Lenguaje-Rust-blue)
![Versión](https://img.shields.io/badge/Versión-0.1.0-lightgrey)

## Descripción

**RustGuard** es un sistema de detección de trampas en tiempo real para servidores de Minecraft. Funciona como un proxy transparente, sin modificar el servidor existente, y es compatible con cualquier versión de Minecraft configurando correctamente el protocolo.

El sistema analiza automáticamente las acciones de los jugadores para detectar cheats como:

- Speed hacks (movimiento demasiado rápido)
- Fly hacks (vuelo no autorizado)
- Kill Aura (ataques automáticos a jugadores)
- X-Ray (minería sospechosa)
- Auto-clicker (clics perfectos o demasiado rápidos)
- Inventory hacks (ítems prohibidos)

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
- Mejor experiencia sin ventajas injustas
- Mayor confianza en el servidor

### Técnico:
- No modifica el servidor
- Compatible con múltiples versiones de Minecraft
- Escalable y eficiente
- Personalizable según necesidades del servidor

## Estado del proyecto

Actualmente en desarrollo. Solo se han preparado los archivos iniciales. Próximamente se añadirá:

- Proxy básico para reenvío de paquetes
- Parsing de posición de jugadores
- Detección de cheats básicos
- Webhook de alertas a Discord

## Posibles expansiones futuras

- Dashboard web para estadísticas en tiempo real
- Base de datos de historial de infracciones
- Integración de Machine Learning para detectar patrones sutiles
- API para que otros desarrolladores agreguen nuevas detecciones
- Modelo SaaS para ofrecer el servicio a otros servidores

## Cómo contribuir

Todavía estamos en la fase inicial, pero cualquier idea, sugerencia o contribución será bienvenida.

## Licencia

Este proyecto se encuentra bajo la licencia **GNU General Public License v3.0 (GPL-3.0)**
