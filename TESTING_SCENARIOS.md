# Testing Scenarios for Custom Player Scoring System

## Overview
This document outlines test scenarios to validate the custom player scoring system (Option 3) implementation.

## Scoring System Rules
- **Default Survival Time:** 5 minutes (300 seconds)
- **Time Reduction:** 5% per infected player
- **Minimum Time:** 1 minute (60 seconds)
- **Winner:** Survivor who completes the full adjusted time

## Test Scenarios

### Scenario 1: Minimum Players (2 Players)
**Setup:**
- 2 total players
- 1 survivor, 1 infected

**Expected Behavior:**
- Survival time: 300 - (300 × 0.05 × 1) = 285 seconds (4m 45s)
- Message: "Survival time: 4m 45s (1 infected)"
- If survivor survives 285 seconds: Display "[PlayerName] SURVIVED! Winner!"
- If survivor dies before time: Display "The Survivor has been infected!"

### Scenario 2: Small Game (4 Players)
**Setup:**
- 4 total players
- 1 survivor, 3 infected

**Expected Behavior:**
- Survival time: 300 - (300 × 0.05 × 3) = 255 seconds (4m 15s)
- Message: "Survival time: 4m 15s (3 infected)"
- Winner if survivor completes 255 seconds

### Scenario 3: Medium Game (8 Players)
**Setup:**
- 8 total players
- 1 survivor, 7 infected

**Expected Behavior:**
- Survival time: 300 - (300 × 0.05 × 7) = 195 seconds (3m 15s)
- Message: "Survival time: 3m 15s (7 infected)"
- Winner if survivor completes 195 seconds

### Scenario 4: Large Game (12 Players)
**Setup:**
- 12 total players
- 1 survivor, 11 infected

**Expected Behavior:**
- Survival time: 300 - (300 × 0.05 × 11) = 135 seconds (2m 15s)
- Message: "Survival time: 2m 15s (11 infected)"
- Winner if survivor completes 135 seconds

### Scenario 5: Extreme Game (20 Players) - Tests Minimum Cap
**Setup:**
- 20 total players
- 1 survivor, 19 infected

**Expected Calculation:**
- Base calculation: 300 - (300 × 0.05 × 19) = 15 seconds
- Capped to minimum: 60 seconds (1m 0s)

**Expected Behavior:**
- Survival time: 60 seconds (1m 0s) - enforced minimum
- Message: "Survival time: 1m 0s (19 infected)"
- Winner if survivor completes 60 seconds

### Scenario 6: Very Large Game (40 Players) - Tests Minimum Cap
**Setup:**
- 40 total players
- 1 survivor, 39 infected

**Expected Calculation:**
- Base calculation: 300 - (300 × 0.05 × 39) = -285 seconds (negative!)
- Capped to minimum: 60 seconds (1m 0s)

**Expected Behavior:**
- Survival time: 60 seconds (1m 0s) - enforced minimum
- Message: "Survival time: 1m 0s (39 infected)"
- Winner if survivor completes 60 seconds

## Expected Behavior Summary

### Time Calculation Table
| Infected Players | Base Calculation | Actual Time | Display |
|-----------------|------------------|-------------|---------|
| 1 | 300 - 15 = 285s | 285s | 4m 45s |
| 2 | 300 - 30 = 270s | 270s | 4m 30s |
| 3 | 300 - 45 = 255s | 255s | 4m 15s |
| 5 | 300 - 75 = 225s | 225s | 3m 45s |
| 10 | 300 - 150 = 150s | 150s | 2m 30s |
| 15 | 300 - 225 = 75s | 75s | 1m 15s |
| 19 | 300 - 285 = 15s | **60s** | 1m 0s |
| 20 | 300 - 300 = 0s | **60s** | 1m 0s |
| 30+ | Negative | **60s** | 1m 0s |

## Testing Checklist

### Functional Tests
- [ ] Verify time calculation is correct for 1-5 infected players
- [ ] Verify time calculation is correct for 10+ infected players
- [ ] Verify minimum time cap (60s) is enforced for 20+ infected players
- [ ] Verify winner message displays correctly when time expires
- [ ] Verify survivor name is shown in winner message
- [ ] Verify "You won!" message is shown to survivor
- [ ] Verify infected message is shown when survivor dies

### Display Tests
- [ ] Verify time announcement at round start
- [ ] Verify infected count is displayed correctly
- [ ] Verify timer counts down properly during round
- [ ] Verify timer color changes (white → yellow → red)

### Edge Cases
- [ ] Test with player joining/leaving during round
- [ ] Test with spectators (should not affect calculation)
- [ ] Test round transitions
- [ ] Test with maximum supported players

## Manual Test Commands

If the game supports console commands, test with:
```
// Set mode to Survival
setcvar 28FragsLater 3

// Set team deathmatch
setcvar g_gametype 2

// Add bots if supported
addbot
```

## Expected Log Output

```
[28FL] Default survival time: 300 seconds
[28FL] Starting Survival Mode
[28FL] Selected survivor: PlayerName
Survival time: 4m 45s (1 infected)
[28FL] Time limit reached
PlayerName SURVIVED! Winner!
```

## Notes for Developers

- The `calculateSurvivalTime()` function is called each time a survivor is selected
- Time is recalculated based on current infected player count at round start
- Players can only win by surviving the full time - there is no point accumulation
- The minimum cap prevents impossible scenarios with very large player counts
