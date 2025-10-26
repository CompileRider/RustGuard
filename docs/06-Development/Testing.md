## Testing and QA

### Objective

To detail the testing methodology and Quality Assurance (QA) process to ensure the proxy's stability, network resilience, and, most importantly, the accuracy of cheat detections with a low false positive rate.

### Types of Tests

|Test Type|Description|Rust Tools|
|---|---|---|
|**Unit Tests**|Low-level tests that validate critical and deterministic functions. They focus on mathematical calculations (e.g., velocity delta, jump tolerance) and the internal logic of the detectors.|Standard Rust testing module (`#[test]`)|
|**Integration Tests**|Simulate a complete player flow by sending sequences of Minecraft packets through the proxy to verify that components (Detector, Context System, Action Handler) communicate correctly.|Tokio mock testing environment and protocol libraries (Azalea).|
|**Load Tests**|Evaluate the proxy's performance and stability with hundreds or thousands of concurrent players. The goal is to verify CPU/memory usage and ensure no significant _lag_ is introduced.|Benchmarking tools like `criterion` or network traffic simulators.|
|**End-to-End (E2E) with Client**|Use modified Minecraft clients or _bots_ to execute known cheat patterns and verify that RustGuard correctly detects the violation and applies the action (kick/ban).|Custom test bots that send predefined packets.|

### Unit Test Example (Rust)

Unit tests are essential to ensure the detection logic adheres to Minecraft physics. This example verifies the calculation of maximum jump height.

```rust
#[tokio::test]
async fn test_jump_height_calculation() {
    // Initialize a clean player state.
    let context = PlayerState::new(); 
    
    // Assume a jump without potion effects.
    let jump = calculate_max_jump(&context);

    // The base Minecraft jump (0.42 blocks) must be the minimum.
    assert!(jump >= 0.42, "Calculated jump height must be at least 0.42");
    
    // Logic to test the Jump Boost II effect
    let context_with_effect = context.add_effect("jump_boost", 2);
    let enhanced_jump = calculate_max_jump(&context_with_effect);
    // Verify that the enhanced jump is significantly higher.
    assert!(enhanced_jump > 0.6, "The jump with effect was not calculated correctly.");
}

```

### Quality Assurance (QA) Metrics

QA success is measured by these key metrics:

1. **False Positive Rate (FPR):** The number of erroneous detections per 1,000 player-hours. The goal is to keep this rate as close to **zero** as possible.
    
2. **Average Latency:** The additional latency introduced by the proxy (generally must be `< 1ms`).
    
3. **True Positive Rate (Detection Rate):** The percentage of known cheats that are correctly detected in E2E test environments.
    

### Related Documents

[False-Positives](../03-Detection-Methods/False-Positives.md)
[Performance](./Perfomance.md)
