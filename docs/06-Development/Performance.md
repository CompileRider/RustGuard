## 06-Development/Performance.md

### Objetivo

Optimizar el rendimiento del proxy, especialmente en entornos con alta concurrencia.

### Estrategias de Optimización

1. **Uso de Tokio:** Permite operaciones I/O asíncronas sin bloquear hilos.
2. **Batch Logging:** Agrupa múltiples logs para reducir operaciones de disco.
3. **Pre-cálculo de Contexto:** Cachea información estática del jugador.

```mermaid
graph LR
    A[Evento de Paquete] --> B[Preprocesador]
    B --> C[Context Cache]
    C --> D[Motor de Detección]
```


### Benchmark Interno

|             |                        |         |      |
| ----------- | ---------------------- | ------- | ---- |
| Escenario   | Jugadores Concurrentes | Uso CPU | RAM  |
| Prueba base | 50                     | 0.3%    | 20MB |
| Carga alta  | 1000                   | 0.9%    | 55MB |
