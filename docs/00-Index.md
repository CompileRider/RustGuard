# Documentación de RustGuard

> Proxy Anti-Cheat para Minecraft escrito en Rust

## 🎯 Navegación Rápida

### 📐 Arquitectura

- [[01-Arquitectura/Vision-General|Visión General de la Arquitectura]]
- [[01-Arquitectura/Flujo-de-Paquetes|Flujo de Paquetes]]
- [[01-Arquitectura/Esquema-de-Base-de-Datos|Esquema de Base de Datos]]
- [[01-Arquitectura/Pila-Tecnologica|Pila Tecnológica]]

### ⚙️ Componentes Principales

- [[02-Componentes-Nucleares/Capa-Proxy|Capa Proxy]]
- [[02-Componentes-Nucleares/Motor-de-Deteccion|Motor de Detección]]
- [[02-Componentes-Nucleares/Manejador-de-Acciones|Manejador de Acciones]]
- [[02-Componentes-Nucleares/Sistema-de-Contexto|Sistema de Contexto]]

### 🎯 Métodos de Detección

- [[03-Metodos-de-Deteccion/Speed-Hack|Detección de Speed Hack]]
- [[03-Metodos-de-Deteccion/Fly-Hack|Detección de Fly Hack]]
- [[03-Metodos-de-Deteccion/KillAura|Detección de KillAura]]
- [[03-Metodos-de-Deteccion/XRay|Detección de XRay]]
- [[03-Metodos-de-Deteccion/Falsos-Positivos|Prevención de Falsos Positivos]]

### 🔌 Integración

- [[04-Integracion/Configuracion-RCON|Configuración de RCON]] 
- [[04-Integracion/Webhooks-Discord|Webhooks de Discord]]
- [[04-Integracion/Puente-Java|Plugin Puente Java]]
- [[04-Integracion/Compatibilidad-MythicMobs|Compatibilidad con MythicMobs]]

### 📦 Instalación y Uso

- [[05-Instalacion/Instalacion|Instalación]]    
- [[05-Instalacion/Configuracion|Configuración]]
- [[05-Instalacion/Primera-Ejecucion|Primera Ejecución]]
- [[05-Instalacion/Solucion-de-Problemas|Solución de Problemas]]

### 🛠️ Desarrollo

- [[06-Desarrollo/Hoja-de-Ruta|Hoja de Ruta del Proyecto]]
- [[06-Desarrollo/Guia-de-Contribucion|Guía de Contribución]]
- [[06-Desarrollo/Estrategia-de-Pruebas|Estrategia de Pruebas]]
- [[06-Desarrollo/Optimizacion-de-Rendimiento|Optimización de Rendimiento]]

### 🌐 Referencia API

- [[07-API/Endpoints-HTTP|Endpoints HTTP]]    
- [[07-API/Comandos-RCON|Comandos RCON]]
- [[07-API/Webhooks|Cargas Útiles de Webhook]]

---

## 📊 Estado del Proyecto

### Completado

-  Estructura del proyecto
-  Marco de documentación

### En Progreso

-  Implementación del proxy TCP
-  Análisis de paquetes con Azalea
-  Detección de speed hack

### Planeado

-  Detección de fly hack
-  Detección de KillAura
-  Integración RCON
-  Webhooks de Discord
-  Base de datos SQLite
-  Sistema de contexto
-  Interfaz de panel (Dashboard UI)

---

## 🚀 Inicio Rápido

```bash
# Compilar
cargo build --release

# Ejecutar
./target/release/rustguard

# Probar
cargo test
```

---

## 📝 Notas

- Este es un documento en constante evolución.
    
- Se actualiza conforme se implementan nuevas funciones.
    
- Consulta [[06-Desarrollo/Hoja-de-Ruta]] para conocer la línea de tiempo.