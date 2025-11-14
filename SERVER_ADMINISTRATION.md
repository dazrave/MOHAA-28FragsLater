# Server Administration Guide - 28 Frags Later

## Overview

This guide covers the enhanced logging and RCON (Remote Console) features added to 28 Frags Later mod to help server administrators monitor and control their game servers.

## Logging System

### Log Levels

The mod includes a comprehensive logging system with configurable detail levels. Set the `28FL_LogLevel` cvar to control what gets logged:

- **0 = Off** - No logging (not recommended)
- **1 = Error** - Critical errors only
- **2 = Info** - Important events (default - recommended)
- **3 = Debug** - Detailed debugging information
- **4 = Verbose** - All events including player actions

### Setting Log Level

**In server.cfg or console:**
```
set 28FL_LogLevel 2
```

**Via RCON:**
```
rcon set 28FL_LogLevel 3
```

### Log Output

Logs are output to two locations:

1. **Server Console** - Visible in real-time in the server console
2. **Log File** - Written to the game log file (if `g_log` is enabled)

All log messages are prefixed with `[28FL-LEVEL]` where LEVEL is one of:
- `[28FL-ERROR]` - Critical errors
- `[28FL-INFO]` - Important events
- `[28FL-DEBUG]` - Debugging information
- `[28FL-VERBOSE]` - Detailed events

### What Gets Logged

#### Info Level (Default)
- Mod initialization and game mode start
- Round start/end events
- Survivor/Patient Zero selection
- Player conversions (survivor to infected)
- Round outcomes (who won, how)
- RCON commands executed

#### Debug Level
- Player team enforcement
- Late joiner handling
- Survivor selection process
- Player count checks

#### Verbose Level
- Every player spawn
- Every player join/disconnect
- Individual player state changes

### Example Log Output

```
[28FL-INFO] ===================================================================
[28FL-INFO] 28 Frags Later Mod v1.0.1 - Starting
[28FL-INFO] Game Mode: 3
[28FL-INFO] Log Level: 2 (0=Off,1=Error,2=Info,3=Debug,4=Verbose)
[28FL-INFO] ===================================================================
[28FL-INFO] RCON command monitor started (use cvar 28FL_Command)
[28FL-INFO] >>> NEW SURVIVAL ROUND STARTED <<<
[28FL-INFO] Selected survivor: PlayerName
[28FL-INFO] Spawning PlayerName as survivor (shotgun)
[28FL-INFO] Survival time: 5m 0s (with 3 infected)
[28FL-INFO] >>> SURVIVAL ROUND ENDED <<<
[28FL-INFO] Result: PlayerName SURVIVED! (time expired)
```

## RCON Commands

### Overview

Server administrators can control the game remotely using RCON commands via the `28FL_Command` cvar. This allows you to manage the game without being in-game.

### How to Use RCON Commands

**Basic syntax:**
```
rcon set 28FL_Command "command_name"
```

The command will be executed immediately and the cvar will be automatically cleared.

### Available Commands

#### `restart`
Restarts the current game mode (forces a new round).

**Usage:**
```
rcon set 28FL_Command "restart"
```

**What it does:**
- Resets the current game state
- Starts a new round in the current game mode
- Announces to all players that the server admin is restarting

#### `status`
Displays current server status in the server log.

**Usage:**
```
rcon set 28FL_Command "status"
```

**Output includes:**
- Current game mode
- Number of players
- Script version
- Current survivor (in Survival mode)

**Example output:**
```
[28FL-INFO] === SERVER STATUS ===
[28FL-INFO] Game Mode: 3
[28FL-INFO] Players: 8
[28FL-INFO] Script Version: 1.0.1
[28FL-INFO] Current Survivor: PlayerName
[28FL-INFO] ====================
```

#### `listplayers`
Lists all currently connected players and their team status.

**Usage:**
```
rcon set 28FL_Command "listplayers"
```

**Example output:**
```
[28FL-INFO] === PLAYER LIST ===
[28FL-INFO]   Player1 (survivor)
[28FL-INFO]   Player2 (infected)
[28FL-INFO]   Player3 (infected)
[28FL-INFO]   Player4 (spectator)
[28FL-INFO] ====================
```

#### `nextround`
Forces the current round to end and starts a new one.

**Usage:**
```
rcon set 28FL_Command "nextround"
```

**What it does:**
- Ends the current round immediately
- Announces to all players
- Starts a new round with new survivor/patient zero selection

### Command Examples

**Check server status:**
```bash
rcon set 28FL_Command "status"
# Wait a moment, then check the server log
```

**List all players:**
```bash
rcon set 28FL_Command "listplayers"
```

**Restart a stuck game:**
```bash
rcon set 28FL_Command "restart"
```

**Skip to next round:**
```bash
rcon set 28FL_Command "nextround"
```

## Monitoring Server Health

### Recommended Log Level by Use Case

**Normal operation:**
- Set to **Info (2)** - Provides good overview without spam

**Debugging issues:**
- Set to **Debug (3)** or **Verbose (4)** - Detailed information for troubleshooting

**High-traffic servers:**
- Set to **Info (2)** or **Error (1)** - Reduces log file size

**Testing new features:**
- Set to **Verbose (4)** - Maximum detail

### Key Events to Monitor

Watch your logs for these important events:

1. **Round Start/End** - Look for "NEW SURVIVAL/INFECTION/OUTBREAK ROUND STARTED"
2. **Player Issues** - Look for player spawn/disconnect messages
3. **Team Enforcement** - Look for "Enforcing team: converting" messages
4. **Round Outcomes** - Look for "ROUND ENDED" and result messages

### Troubleshooting with Logs

**Problem: Game seems stuck**
- Look for recent "NEW ROUND STARTED" messages
- Use `rcon set 28FL_Command "status"` to check state
- Use `rcon set 28FL_Command "restart"` if needed

**Problem: Players not spawning correctly**
- Increase log level to Debug or Verbose
- Look for spawn-related messages
- Check for team enforcement messages

**Problem: Survivor selection issues**
- Look for "Selected survivor:" messages
- Use `listplayers` command to see team assignments
- Use `nextround` to force reselection

## Server Configuration

### Recommended server.cfg Settings

```
// Enable game logging
set g_log "games.log"
set g_logSync 1

// Set 28 Frags Later game mode
set g_gametype 2
set 28FragsLater 3  // 1=Outbreak, 2=Infection, 3=Survival

// Set logging level
set 28FL_LogLevel 2  // Info level - recommended for production
```

### Development/Testing Server

```
// Maximum logging for testing
set 28FL_LogLevel 4

// Enable detailed MOHAA logging
set developer 1
set logfile 1
```

## Log File Analysis

### Filtering Logs

**On Linux/Unix:**
```bash
# View only 28FL logs
grep "28FL" games.log

# View only errors
grep "28FL-ERROR" games.log

# View recent activity (last 50 lines)
tail -n 50 games.log | grep "28FL"

# Watch logs in real-time
tail -f games.log | grep "28FL"
```

**On Windows (PowerShell):**
```powershell
# View only 28FL logs
Select-String -Path games.log -Pattern "28FL"

# View recent activity
Get-Content games.log -Tail 50 | Select-String "28FL"
```

## Security Considerations

### RCON Security

- Ensure your RCON password is strong
- The `28FL_Command` cvar requires RCON privileges
- Commands are logged for audit purposes
- Invalid commands are logged but ignored

### Log File Security

- Log files may contain player names and IP addresses
- Protect log files according to your privacy policy
- Rotate log files regularly to manage disk space
- Consider log retention policies

## Support and Debugging

If you encounter issues:

1. **Set log level to Verbose (4)**
2. **Reproduce the issue**
3. **Capture the relevant log output**
4. **Include log snippets when reporting issues**

The detailed logging will help identify:
- When the issue occurred
- What players were involved
- What game state transitions happened
- Any errors that occurred

## Version History

**v1.0.1** - Added comprehensive logging and RCON support
- Configurable log levels
- Server console output
- Log file integration
- RCON command system
- Player event tracking
- Round event tracking

---

For more information about the mod itself, see the main [README.md](README.md) file.

Website: www.28fragslater.co.uk
Contact: ophis@daphi.co.uk
