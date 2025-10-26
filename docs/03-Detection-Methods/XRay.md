# Detección de XRay

## Descripción General

El cheat XRay (Rayos X) permite a los jugadores ver y localizar bloques valiosos u ocultos (como diamantes o bases secretas). Dado que RustGuard es un proxy que solo ve el tráfico de red, no tiene acceso directo al estado del mundo del servidor. Por lo tanto, el detector de XRay se centra en el análisis de **patrones de interacción de bloques anómalos**.

## Principio de Detección

La detección de XRay se basa en la improbabilidad estadística de las acciones del jugador:

1. **Patrón de Minería No Natural (Mining Pattern Analysis):** La minería debe seguir un patrón lógico (ej. túneles rectos, cuevas exploradas). Minar directamente hacia un bloque valioso enterrado, ignorando cientos de bloques intermedios, es una señal de XRay.
    
2. **Frecuencia Anómala de Interacción con Bloques Valiosos (VBI - Valuable Block Interaction):** Monitorea la frecuencia con la que un jugador se detiene a minar un VBI (ej. Diamante, Esmeralda) inmediatamente después de minar un bloque "basura" (ej. Cobblestone, Dirt).
    

## Componente Clave: Historial de Bloques Minados

El `PlayerState` de cada jugador mantiene una cola de los últimos $N$ bloques minados para su análisis.

```rust
pub struct PlayerState {
    // ... otros campos
    pub mined_blocks_history: VecDeque<MinedBlock>, // N = 100
    pub valuable_blocks_mined: u32,
    pub non_valuable_blocks_mined: u32,
}

pub struct MinedBlock {
    location: Position,
    block_id: u32,
    timestamp: i64,
    was_valuable: bool,
}
```


## Algoritmo de Detección (VBI Ratio)

El detector calcula el **Valuable Block Ratio (VBR)** para el jugador en un período de tiempo rodante.

1. **Cálculo de la Razón de Éxito:**
    
    $$\\ \text{VBR} = \frac{\text{ValuableBlocksMinados}}{\text{TotalBlocksMinados en últimos 10 minutos}}$$
    
    $$$$
    
2. **Umbral de Sospecha:** Se compara el VBR contra un umbral dinámico.
    
    - Un jugador que minó 5 diamantes y 1000 de piedra tendrá un VBR bajo (0.005).
        
    - Un jugador XRay que minó 5 diamantes y solo 50 de piedra para llegar a ellos tendrá un VBR alto (0.09).
        

```rust
impl XRayDetector {
    async fn check(&self, player: &PlayerState, block: &MinedBlock) -> Option<Detection> {
        player.mined_blocks_history.push_back(block.clone());
        player.update_block_counts(block); // Actualiza los contadores VBI/Non-VBI

        if player.mined_blocks_history.len() < self.config.min_history_size {
            return None;
        }

        let vbr = player.get_valuable_block_ratio();
        let suspicious_threshold = self.config.base_vbr_threshold;

        if vbr > suspicious_threshold {
            let confidence = (vbr / suspicious_threshold - 1.0).clamp(0.0, 1.0);
            
            return Some(Detection {
                cheat_type: "xray_hack".to_string(),
                confidence,
                details: json!({
                    "vbr": vbr,
                    "threshold": suspicious_threshold,
                }),
                timestamp: Utc::now().timestamp(),
            });
        }
        
        None
    }
}
```


## Mitigación de Falsos Positivos

- **Minería de Superficie:** La minería en la superficie (Y > 60) no debe contar para el VBR, ya que el jugador puede ver los bloques valiosos (carbón, hierro expuesto).
    
- **Señales de Búsqueda:** Si el jugador mina túneles largos y rectos (indicando búsqueda), el umbral de VBR se relaja temporalmente.
    
- **Coordenadas:** Los jugadores con alta puntuación VBR a coordenadas Y muy bajas (Y < 10) son más sospechosos.
    

## Documentos Relacionados

[[Detection-Engine]] [[Context-System]] [[Configuration]]