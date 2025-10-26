## Detección de Fly-Hack

### Visión General

El detector de **Fly-Hack** analiza el movimiento vertical de un jugador (Y-Delta) a lo largo del tiempo, enfocándose especialmente en desviaciones respecto al modelo físico esperado del servidor de Minecraft. Es una verificación con **estado** (stateful), ya que depende del historial de posiciones almacenadas del jugador.

### Principio Central de Detección

La lógica principal consiste en comparar la distancia vertical observada entre dos paquetes (Y_delta) con la altura máxima que el jugador podría alcanzar de forma natural, considerando la gravedad y la velocidad del salto.

$$\text{EsSospechoso} = Y_{\text{delta observada}} > \text{AlturaMáxSalto}_{\text{Contexto}}$$

### Cálculo de la Altura Máxima de Salto

La altura máxima permitida es **dinámica** y se ajusta en función del estado actual del jugador, obtenido desde el **Context System**.

|Factor de Estado|Modificador|Descripción|
|---|---|---|
|Velocidad base de salto|+0.42|Impulso vertical estándar al saltar.|
|Efecto de Jump Boost|+0.1 × (Nivel + 1)|Aumenta la altura de salto según el nivel de la poción.|
|Corriendo (Sprinting)|+0.08|Incrementa ligeramente la altura al correr.|
|Agua/Escaleras|× 0.0|Se ignora el movimiento vertical si el jugador está en agua o escalera.|
|Elytra/Planeo|Ignorar chequeo|Se omite la detección si el jugador está planeando.|

### Estructura Simplificada para el Cálculo de Salto Máximo

```rust
fn calculate_max_jump(&self, player: &PlayerState) -> f64 {
    let mut max_jump = self.config.base_jump_velocity; // ~0.42

    // Aplicar efecto Jump Boost
    if let Some(effect) = player.active_effects.get("jump_boost") {
        max_jump += self.config.jump_boost_multiplier * (effect.amplifier as f64 + 1.0);
    }

    // Aplicar bono por sprint
    if player.is_sprinting {
        max_jump += self.config.sprint_jump_bonus;
    }

    // Añadir pequeña tolerancia para imprecisiones de punto flotante y latencia
    max_jump + self.config.y_tolerance
}

```

### Mitigación de Casos Extremos (Falsos Positivos)

La detección de vuelo es propensa a falsos positivos debido a interacciones ambientales. Por ello, el detector verifica los siguientes factores mitigantes:

- **Agua/Lava:** Si `player.in_water` o `player.in_lava` es verdadero, se omite el chequeo ya que el movimiento no es estándar.
    
- **Monturas:** Si el jugador está montado en una entidad (caballo, bote, etc.), se ignora el chequeo porque el servidor controla la física vertical.
    
- **"On Ground" del cliente:** Si el cliente reporta `player.on_ground = true`, el Y-Delta puede deberse a una pequeña colisión con bloques. Solo se marca como sospechoso si el Y-Delta excede la altura de paso estándar (0.6).
    
- **Teletransportación:** Se comprueba si hubo un cambio instantáneo y grande en la posición, indicando teletransporte. En ese caso, el Proxy Layer reinicia el historial de movimiento del jugador.
    

### Puntuación de Confianza

La **confianza** se calcula en función de cuánto excede el Y-Delta observado respecto a la altura máxima permitida.

$$\text{Confianza} = \left( \frac{Y_{\text{delta}}}{\text{AlturaMáxSalto}} - 1 \right) \times 2.0$$

El resultado se limita entre $0.0$ y $1.0$. Si la velocidad vertical observada es el doble de la máxima permitida, se obtiene una puntuación de confianza de $1.0$.

### Documentos Relacionados

- $$\[Speed-Hack$$
    
    ]
    
- $$\[KillAura$$
    
    ]
    
- $$\[Context-System$$
    
    ]