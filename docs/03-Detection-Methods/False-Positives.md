## Fly-Hack Detection

### Overview

The **Fly-Hack** detector analyzes the vertical movement of a player (Y-Delta) over time, focusing especially on deviations from the expected physics model of the Minecraft server. It is a **stateful** check, as it depends on the player's stored position history.

### Core Detection Principle

The main logic compares the vertical distance observed between two packets (Y_delta) with the maximum height the player could naturally reach, considering gravity and jump velocity.

$$\text{Suspicious} = Y_{\text{observed delta}} > \text{MaxJumpHeight}_{\text{Context}}$$

### Maximum Jump Height Calculation

The allowed maximum height is **dynamic** and adjusts based on the player's current state, obtained from the **Context System**.

|State Factor|Modifier|Description|
|---|---|---|
|Base jump velocity|+0.42|Standard vertical impulse when jumping.|
|Jump Boost Effect|+0.1 × (Level + 1)|Increases jump height according to potion level.|
|Sprinting|+0.08|Slightly increases height while running.|
|Water/Stairs|× 0.0|Vertical movement is ignored if the player is in water or on stairs.|
|Elytra/Gliding|Ignore check|Detection is skipped if the player is gliding.|

### Simplified Maximum Jump Calculation

```rust
fn calculate_max_jump(&self, player: &PlayerState) -> f64 {
    let mut max_jump = self.config.base_jump_velocity; // ~0.42

    // Apply Jump Boost effect
    if let Some(effect) = player.active_effects.get("jump_boost") {
        max_jump += self.config.jump_boost_multiplier * (effect.amplifier as f64 + 1.0);
    }

    // Apply sprint bonus
    if player.is_sprinting {
        max_jump += self.config.sprint_jump_bonus;
    }

    // Add small tolerance for floating point imprecision and latency
    max_jump + self.config.y_tolerance
}
```

### Extreme Case Mitigation (False Positives)

Fly detection is prone to false positives due to environmental interactions. The detector checks the following mitigating factors:

- **Water/Lava:** If `player.in_water` or `player.in_lava` is true, the check is skipped as movement is non-standard.
    
- **Mounts:** If the player is riding an entity (horse, boat, etc.), the check is ignored since the server controls vertical physics.
    
- **Client On Ground:** If the client reports `player.on_ground = true`, the Y-Delta may be due to small block collisions. It is only flagged as suspicious if the Y-Delta exceeds the standard step height (0.6).
    
- **Teleportation:** If there was an instant, large change in position indicating teleportation, the Proxy Layer resets the player's movement history.
    

### Confidence Score

The **confidence** is calculated based on how much the observed Y-Delta exceeds the allowed maximum height.

$$\text{Confidence} = \left( \frac{Y_{\text{delta}}}{\text{MaxJumpHeight}} - 1 \right) \times 2.0$$

The result is clamped between $0.0$ and $1.0$. If the observed vertical velocity is double the allowed maximum, the confidence score is 1.0.

### Related Documents

- [[Speed-Hack]]
- [[KillAura]]
- [[Context-System]] 