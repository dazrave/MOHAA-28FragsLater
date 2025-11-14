# Logging & RCON Quick Start Guide

## 5-Minute Setup

### 1. Enable Logging

Add to your `server.cfg`:
```
set g_log "games.log"           // Enable MOHAA logging
set 28FL_LogLevel 2              // Set to Info level (recommended)
```

### 2. Start Your Server

Watch the console output for:
```
[28FL-INFO] ===================================================================
[28FL-INFO] 28 Frags Later Mod v1.0.1 - Starting
[28FL-INFO] Game Mode: 3
[28FL-INFO] Log Level: 2 (0=Off,1=Error,2=Info,3=Debug,4=Verbose)
[28FL-INFO] ===================================================================
[28FL-INFO] RCON command monitor started (use cvar 28FL_Command)
```

### 3. Monitor Logs

**Real-time console monitoring:**
- Watch your server console for `[28FL-INFO]`, `[28FL-DEBUG]`, etc. messages

**Log file monitoring (Linux/Mac):**
```bash
tail -f games.log | grep 28FL
```

**Log file monitoring (Windows PowerShell):**
```powershell
Get-Content games.log -Wait -Tail 50 | Select-String "28FL"
```

## Log Levels Quick Reference

| Level | Name | What It Logs | When To Use |
|-------|------|--------------|-------------|
| 0 | Off | Nothing | Not recommended |
| 1 | Error | Critical errors only | Minimal logging |
| 2 | Info | Important events (default) | Normal operation ✅ |
| 3 | Debug | Detailed debugging | Troubleshooting |
| 4 | Verbose | Everything | Development/testing |

**Change log level:**
```
rcon set 28FL_LogLevel 3
```

## RCON Commands Quick Reference

| Command | What It Does | Example |
|---------|--------------|---------|
| `status` | Show server status | `rcon set 28FL_Command "status"` |
| `listplayers` | List all players | `rcon set 28FL_Command "listplayers"` |
| `restart` | Restart game mode | `rcon set 28FL_Command "restart"` |
| `nextround` | Skip to next round | `rcon set 28FL_Command "nextround"` |

## Common Scenarios

### Debugging Player Spawn Issues
```bash
# Increase log level to verbose
rcon set 28FL_LogLevel 4

# Watch for spawn messages
tail -f games.log | grep "28FL.*spawn"
```

### Checking Round Status
```bash
rcon set 28FL_Command "status"
# Check server console for output
```

### Restarting a Stuck Game
```bash
rcon set 28FL_Command "restart"
```

### Finding Out Who's Playing
```bash
rcon set 28FL_Command "listplayers"
# Output appears in server log
```

## Key Log Messages to Watch For

### Round Events
- `>>> NEW SURVIVAL ROUND STARTED <<<`
- `>>> SURVIVAL ROUND ENDED <<<`
- `>>> NEW INFECTION ROUND STARTED <<<`
- `>>> NEW OUTBREAK ROUND STARTED <<<`

### Player Events
- `Player joined: PlayerName`
- `Player spawned: PlayerName`
- `Player disconnected: PlayerName`
- `Selected survivor: PlayerName`

### Issues
- `[28FL-ERROR]` - Something went wrong
- `Late joiner ... spawning as infected` - Player joined mid-round
- `Enforcing team: converting ... to infected` - Team balance enforcement

## Example Log Output

```
[28FL-INFO] 28 Frags Later Mod v1.0.1 - Starting
[28FL-INFO] Game Mode: 3
[28FL-INFO] >>> NEW SURVIVAL ROUND STARTED <<<
[28FL-INFO] Selected survivor: JohnDoe
[28FL-INFO] Spawning JohnDoe as survivor (shotgun)
[28FL-INFO] Survival time: 4m 30s (with 5 infected)
[28FL-VERBOSE] Player spawned: JaneDoe
[28FL-VERBOSE] Player spawned: BobSmith
[28FL-INFO] >>> SURVIVAL ROUND ENDED <<<
[28FL-INFO] Result: JohnDoe SURVIVED! (time expired)
```

## Troubleshooting

### Not Seeing Log Output?

1. **Check log level:** `rcon set 28FL_LogLevel 2` (or higher)
2. **Check if mod is running:** Look for startup message in console
3. **Check g_log cvar:** `rcon g_log` should show a filename

### RCON Commands Not Working?

1. **Verify RCON is working:** Try `rcon status` first
2. **Check command syntax:** Use exact command names (lowercase)
3. **Check server logs:** Commands are logged when received

### Too Much Log Output?

1. **Lower log level:** `rcon set 28FL_LogLevel 1` (errors only)
2. **Filter logs:** Use grep/Select-String to filter messages

## Full Documentation

See [SERVER_ADMINISTRATION.md](SERVER_ADMINISTRATION.md) for complete documentation.

---

**Quick help:** Most issues can be solved by setting log level to Verbose (4) and watching what happens when you reproduce the problem.
