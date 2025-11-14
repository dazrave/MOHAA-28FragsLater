# 28 Frags Later

**A Zombie/Infection Survival Mod for Medal of Honor: Allied Assault**

Originally created 2004-2006 by Ophis (with help from Falco)
**Completely refactored and modernized in 2025**

## Overview

28 Frags Later transforms MOHAA's Team Deathmatch into an intense asymmetric horror survival experience. Inspired by zombie/infection game modes, players face off in three distinct game modes where survival is everything.

## Game Modes

### 🎯 Survival Mode (Mode 3)
**One survivor vs everyone else - with dynamic scoring**

- One player is randomly selected as the **Survivor** (Allied team)
- Survivor gets a shotgun and must survive to win
- All other players are **Infected** (Axis team) with pistols
- **Dynamic Survival Time:** Base time is 5 minutes, reduced by 5% per infected player
- Minimum survival time is 1 minute (even with many infected players)
- If survivor survives the full time, they win the round
- If infected kill the survivor, new survivor is selected
- Fair selection algorithm ensures everyone gets a turn

### 🦠 Outbreak Mode (Mode 1)
**Multiple survivors, work together or die alone**

- 3 random players selected as survivors
- Survivors must work together against the infected horde
- Requires minimum 4 players
- Round ends when all survivors are eliminated
- Cooperative gameplay encouraged

### 💀 Infection Mode (Mode 2)
**Classic infection - one infected spreads the plague**

- One "Patient Zero" selected as first infected
- All players start with pistols (infected and survivors)
- Survivors must survive for 5 minutes to win
- When a survivor is killed, they become infected on respawn
- Late joiners automatically become infected
- Round ends when all survivors are infected OR time expires
- Survivors win if any survive the 5-minute timer
- Infection spreads exponentially

## Installation

1. Download/clone this repository
2. Copy the contents to your MOHAA mod folder
3. Set `g_gametype 2` (Team Deathmatch)
4. Set `28FragsLater` to desired mode:
   - `0` = Off (normal TDM)
   - `1` = Outbreak Mode
   - `2` = Infection Mode
   - `3` = Survival Mode

## Features

### Visual Atmosphere
- Dynamic storm effects with lightning flashes
- Fog/farplane settings for eerie atmosphere
- Custom survivor and infected player skins

### Gameplay Mechanics
- **Dynamic Survival Time** - Time adjusts based on infected player count (5% reduction per infected)
- **Custom Scoring System** - Survivor wins by surviving the full duration; time scales with difficulty
- **Fair survivor selection** - Tracks how many times each player has been survivor
- **Proximity alerts** - Infected players alerted when near survivor
- **Timer display** - Color-coded countdown (white → yellow at 30s → red at 10s)
- **Auto team enforcement** - Players automatically placed on correct teams

### Custom Assets
- **Survivor Skin:** Police-themed Allied model
- **Infected Skin:** Prisoner-themed Axis model
- **Weapons:** Shotgun for survivors, Colt 45 for infected
- **Sounds:** Ambient thunder effects

## Configuration

All gameplay settings can be easily adjusted at the top of `global/28fragslater.scr`:

```
// Dynamic Survival Time (Option 3 Scoring System)
local.DEFAULT_SURVIVAL_TIME = 300       // Default 5 minutes
local.TIME_REDUCTION_PERCENT = 5        // 5% reduction per infected player
local.MIN_SURVIVAL_TIME = 60            // Minimum 1 minute

local.MIN_PLAYERS = 2                   // Minimum players to start
local.PROXIMITY_DISTANCE = 500          // Distance for proximity alerts
local.TIMER_WARNING_YELLOW = 30         // Yellow warning threshold
local.TIMER_WARNING_RED = 10            // Red warning threshold
```

## Refactoring (2025)

The original 598-line script from 15+ years ago has been completely refactored:

### What Was Fixed
✅ Eliminated `goto` statement anti-pattern
✅ Removed massive code duplication (~50 lines duplicated)
✅ Removed 50+ commented debug statements
✅ Replaced magic numbers with named constants
✅ Fixed race conditions in survivor selection
✅ Fixed memory leaks with trigger cleanup
✅ Optimized proximity checking (O(n²) → O(n))
✅ Proper function separation and modularity

### What Was Added
✅ **Outbreak Mode** - Fully implemented (was just a stub)
✅ **Infection Mode** - Fully implemented (was just a stub)
✅ Comprehensive documentation
✅ Consistent code style and formatting
✅ Reusable utility functions
✅ Better error messages and logging

**See [REFACTORING_NOTES.md](REFACTORING_NOTES.md) for complete details.**

## Technical Details

- **Language:** MOHAA script language
- **Required Gametype:** Team Deathmatch (g_gametype 2)
- **Minimum Players:** 2 (4 for Outbreak mode)
- **Script Version:** 1.0.0 (refactored) / 0.7.3 (original)

## File Structure

```
MOHAA-28FragsLater/
├── global/
│   ├── 28fragslater.scr       # Main game logic (820 lines, refactored)
│   ├── dmprecache.scr          # Asset pre-loading
│   └── localization.txt        # UI text strings
├── models/
│   ├── player/                 # Custom survivor/infected skins
│   ├── weapons/                # Weapon models
│   └── ...
├── sound/
│   └── amb/                    # Thunder sound effects
├── README.md
└── REFACTORING_NOTES.md        # Detailed refactoring documentation
```

## Credits

**Original Author:** Ophis
**Original Help:** Falco
**Refactored:** 2025
**Contact:** ophis@daphi.co.uk
**Website:** www.28fragslater.co.uk

## License

Same as original 28 Frags Later mod.

## Contributing

This is an archived mod from 2004-2006, now refactored for modern maintainability. Feel free to fork and create your own variations!

---

*Created sometime back around 2004 to 2006 and pushed to GitHub for storage!*
*Completely refactored and modernized in 2025 with two new fully-functional game modes.*
