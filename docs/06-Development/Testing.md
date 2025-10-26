## 🧪 06-Development/Testing.md

### Objetivo

Detallar la metodología de pruebas para garantizar la estabilidad del proxy y la precisión de las detecciones.

### Tipos de Pruebas

- **Unitarias:** Validan funciones críticas como cálculos de salto o velocidad.
- **Integración:** Simulan tráfico real de Minecraft con el proxy.
- **Carga:** Evalúan el rendimiento con cientos de jugadores concurrentes.

### Ejemplo de Prueba Unitaria (Rust)

```rust
#[tokio::test]
async fn test_jump_height_calculation() {
    let context = PlayerState::new();
    let jump = calculate_max_jump(&context);
    assert!(jump >= 0.42);
}
```

### Métricas a Monitorear

- Falsos positivos detectados
- Latencia promedio del proxy
- Uso de CPU y memoria bajo carga