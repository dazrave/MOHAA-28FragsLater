# Infection Mode (Mode 2) Testing Scenarios

## Overview
This document outlines test scenarios to validate the Infection Mode implementation based on issue requirements.

## Issue Requirements
1. Place everyone on allies
2. Give everyone pistol
3. Randomly place one person on axis (Patient Zero)
4. START game
5. If killed, switch to axis
6. If joined after START, switch to axis
7. If no one left on Allies END game
8. Restart after 5 minutes

## Test Scenarios

### Scenario 1: Basic Game Flow (2 Players)
**Setup:**
- 2 total players
- 1 Patient Zero (axis), 1 Survivor (allies)

**Expected Behavior:**
1. Patient Zero selected and spawned on axis team with pistol
2. Survivor spawned on allies team with pistol
3. Message displayed: "[PlayerName] is Patient Zero!"
4. Message displayed: "Infection round started! 5 minutes to survive!"
5. 5-minute countdown timer appears (white)
6. When survivor is killed:
   - Marked for infection
   - On respawn, converted to axis team
   - Message: "You have been infected!"
7. When all survivors infected:
   - Message: "All survivors have been infected!"
   - Round restarts after 10 seconds

### Scenario 2: Timer Expiry (Survivors Win)
**Setup:**
- 4 players: 1 infected, 3 survivors

**Expected Behavior:**
1. Round starts with 5-minute timer
2. Timer counts down properly (5:00 → 4:59 → etc.)
3. Timer turns yellow at 30 seconds remaining
4. Timer turns red at 10 seconds remaining
5. If survivors survive full 5 minutes:
   - Message: "Time expired! Survivors win!"
   - Timer hidden
   - Round restarts after 10 seconds

### Scenario 3: Late Joiner Handling
**Setup:**
- Round in progress with 1 infected, 2 survivors
- New player joins mid-round

**Expected Behavior:**
1. Late joiner spawns on axis team (infected)
2. Late joiner gets pistol
3. Message to late joiner: "Round in progress - you are infected!"
4. Late joiner cannot become survivor during this round

### Scenario 4: Death and Respawn Conversion
**Setup:**
- 3 players: 1 infected, 2 survivors

**Expected Behavior:**
1. Survivor A is killed by infected player
2. Survivor A marked with `needsInfection = 1` when dead
3. Survivor A respawns after delay
4. On respawn, Survivor A automatically:
   - Switches to axis team
   - Gets pistol (if not already equipped)
   - Sees message: "You have been infected!"
5. Survivor A can now infect Survivor B

### Scenario 5: All Infected Win
**Setup:**
- 4 players: 1 infected, 3 survivors

**Expected Behavior:**
1. Infected kills all 3 survivors one by one
2. Each survivor converts to infected on respawn
3. When last survivor converted:
   - Check shows 0 survivors, 4 infected
   - Message: "All survivors have been infected!"
   - Round ends and restarts after 10 seconds
4. New round begins with new Patient Zero selection

### Scenario 6: Fair Weapon Distribution
**Setup:**
- Any number of players

**Expected Behavior:**
1. Patient Zero gets: Axis team + Colt 45 pistol
2. All survivors get: Allies team + Colt 45 pistol
3. NO shotguns given (unlike other modes)
4. All weapons set to "not droppable"

### Scenario 7: Round Reset
**Setup:**
- Round completes (either all infected or timer expires)

**Expected Behavior:**
1. `level.patientZero = NIL` (reset)
2. `level.infectionActive = 0` (temporarily disabled)
3. `level.infectionRoundStarted = 0` (round ended)
4. Wait 10 seconds
5. `level.infectionActive = 1` (re-enabled)
6. Game loop selects new Patient Zero
7. New round begins

### Scenario 8: Minimum Player Check
**Setup:**
- Only 1 player in game

**Expected Behavior:**
1. Game waits for minimum 2 players
2. No Patient Zero selected
3. Round does not start
4. When 2nd player joins, round can begin

## Expected Messages

### Round Start
- "Infection round started! 5 minutes to survive!"
- "[PlayerName] is Patient Zero!"
- To Patient Zero: "You are Patient Zero! Infect everyone!"
- To Survivors: "You are a survivor! Avoid infection!"

### During Round
- To converted player: "You have been infected!"
- To late joiner: "Round in progress - you are infected!"

### Round End
- All infected: "All survivors have been infected!"
- Timer expired: "Time expired! Survivors win!"

## Technical Validation

### State Variables to Check
- `level.infectionActive` - Should be 1 during game
- `level.infectionRoundStarted` - Should be 1 during active round, 0 between rounds
- `level.patientZero` - Should be NIL between rounds, player reference during round
- `level.infectionRoundExpiry` - Should be set to level.time + 300 at round start
- `$player[i].needsInfection` - Should be 1 when dead survivor, 0 after conversion

### Function Call Sequence
1. `gameLoopInfection()` starts
2. `selectPatientZero()` called when needed
3. `spawnAsSurvivorWithPistol()` called for survivors
4. `spawnAsInfected()` called for Patient Zero
5. `monitorInfectionDeaths()` runs in background
6. `displayInfectionTimer()` runs in background
7. `monitorInfection()` checks win conditions
8. Round resets and repeats

## Edge Cases

### Edge Case 1: Player Disconnect During Round
**Expected:** Round continues, remaining players unaffected

### Edge Case 2: Last Survivor Disconnects
**Expected:** All infected, round ends normally

### Edge Case 3: Patient Zero Disconnects
**Expected:** Remaining survivors can win by surviving timer

### Edge Case 4: Multiple Deaths Simultaneously
**Expected:** All dead survivors marked for infection, converted on respawn

## Console Commands for Testing

```
// Set infection mode
setcvar 28FragsLater 2

// Set team deathmatch
setcvar g_gametype 2

// Enable developer mode for logging
setcvar developer 1
```

## Expected Log Output

```
[28FL] Starting 28 Frags Later Mod v1.0.0
[28FL] Game Mode: 2
[28FL] Starting Infection Mode
[28FL] Infection mode initialized
[28FL] First infected must infect all survivors
[28FL] Selecting Patient Zero
```

## Success Criteria

- ✅ Everyone starts with pistols (no shotguns)
- ✅ One player randomly selected as Patient Zero (axis)
- ✅ All others start as survivors (allies)
- ✅ 5-minute timer displays and counts down
- ✅ Killed survivors convert to infected on respawn
- ✅ Late joiners become infected automatically
- ✅ Round ends when all infected OR timer expires
- ✅ Round restarts after completion
- ✅ Fair Patient Zero selection (rotation)
