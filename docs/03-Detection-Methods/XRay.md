# XRay Detection

## Overview

XRay is a cheat that allows players to see and locate valuable or hidden blocks (like diamonds or secret bases). Since RustGuard operates as a network proxy, it does not have direct access to the server world state. Therefore, XRay detection focuses on analyzing **anomalous block interaction patterns**.

## Detection Principle

XRay detection relies on the statistical improbability of player actions:

1. **Mining Pattern Analysis:** Legitimate mining follows logical patterns (e.g., straight tunnels, explored caves). Mining directly toward a buried valuable block while ignoring hundreds of intermediate blocks is a strong XRay signal.
    
2. **Valuable Block Interaction (VBI) Frequency:** Monitors how often a player mines a valuable block (like Diamond or Emerald) immediately after mining non-valuable blocks (like Cobblestone or Dirt).
    

## Key Component: Mined Blocks History

Each `PlayerState` maintains a queue of the last $N$ mined blocks for analysis.

```rust
pub struct PlayerState {
    // ... other fields
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

## Detection Algorithm (VBI Ratio)

The detector calculates the **Valuable Block Ratio (VBR)** over a rolling time window.

1. **Calculate the Success Ratio:**
    

$$  
\text{VBR} = \frac{\text{ValuableBlocksMined}}{\text{TotalBlocksMined in last 10 minutes}}  
$$

2. **Suspicion Threshold:** Compare VBR against a dynamic threshold.
    

- A legitimate player who mined 5 diamonds out of 1000 stone has a low VBR (0.005).
    
- An XRay player who mined 5 diamonds out of only 50 stone to reach them has a high VBR (0.09).
    

```rust
impl XRayDetector {
    async fn check(&self, player: &PlayerState, block: &MinedBlock) -> Option<Detection> {
        player.mined_blocks_history.push_back(block.clone());
        player.update_block_counts(block); // Updates VBI/Non-VBI counters

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

## False Positive Mitigation

- **Surface Mining:** Mining above Y > 60 is ignored since valuable blocks are naturally visible.
    
- **Exploratory Tunnels:** If the player mines long straight tunnels (indicating exploration), the VBR threshold is temporarily relaxed.
    
- **Coordinates Check:** Players with high VBR at very low Y coordinates (Y < 10) are more suspicious.
