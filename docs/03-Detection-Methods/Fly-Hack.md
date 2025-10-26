## Fly-Hack Detection

### Overview

The **Fly-Hack** detector analyzes a player's vertical movement (Y-Delta) over time, focusing on deviations from the expected physics model of the Minecraft server. This is a **stateful** check since it relies on the player's stored position history.

### Core Detection Principle

The main logic compares the vertical distance observed between two packets (Y_delta) with the maximum height a player could naturally reach, considering gravity and jump velocity.

$$\text{IsSuspicious} = Y_{\text{observed delta}} > \text{MaxJumpHeight}_{\text{Context}}$$

### Maximum Jump Height Calculation

The maximum allowed jump height is **dynamic** and adjusts based on the player's current state, retrieved from the **Context System**.

|State Factor|Modifier|Description|
|---|---|---|
|Base Jump Velocity|+0.42|Standard vertical impulse when jumping.|
|Jump Boost Effect|+0.1 × (Level + 1)|Increases jump height based on potion level.|
|Sprinting|+0.08|Slightly increases jump height while sprinting.|
|Water/Ladders|× 0.0|Vertical movement is ignored if the player is in water or on a ladder.|
|Elytra/Gliding|Ignore check|Detection is skipped if the player is gliding.|

### Simplified Maximum Jump Calculation Structure

```rust
fn calculate_max_jump(&self, player: &PlayerState) -> f64 {
    let mut max_jump = self.config.base_jump_velocity; // ~0.42

    // Apply Jump Boost effect
    if let Some(effect) = player.active_effects.get("jump_boost") {
        max_jump += self.config.jump_boost_multiplier * (effect.amplifier as f64 + 1.0);
    }

    // Apply sprinting bonus
    if player.is_sprinting {
        max_jump += self.config.sprint_jump_bonus;
    }

    // Add small tolerance for floating-point inaccuracies and latency
    max_jump + self.config.y_tolerance
}
```

### Edge Case Mitigation (False Positives)

Fly detection is prone to false positives due to environmental interactions. The detector checks the following mitigating factors:

- **Water/Lava:** If `player.in_water` or `player.in_lava` is true, the check is skipped.
    
- **Mounts:** If the player is riding an entity (horse, boat, etc.), the check is ignored because vertical movement is governed by server physics.
    
- **Client "On Ground":** If `player.on_ground = true`, the Y-Delta may be caused by a small block collision. Only flagged if Y-Delta exceeds standard step height (0.6).
    
- **Teleportation:** Detects large instant position changes. The Proxy Layer resets the player's movement history in this case.
    

### Confidence Scoring

**Confidence** is calculated based on how much the observed Y-Delta exceeds the maximum allowed jump height.

$$\text{Confidence} = \left( \frac{Y_{\text{delta}}}{\text{MaxJumpHeight}} - 1 \right) \times 2.0$$

The result is clamped between $0.0$ and $1.0$. If the observed vertical velocity is twice the allowed maximum, the confidence score is `1.0`.

### Related Documents

- [[Speed-Hack]]
- [[KillAura]]
- [[Context-System]] 