# 28 Frags Later — OpenMOHAA Bot Build

This branch is a port/rework of 28 Frags Later for **[OpenMOHAA](https://github.com/openmoh/openmohaa)**
(the open-source MOHAA engine) so the mode can be populated by **AI bots** — something
retail MOHAA can't do on DM maps (bots need a Recast navmesh, which OpenMOHAA provides).

It keeps the spirit of the original three modes but adapts them for a bot horde and adds
map+mode rotation and a session leaderboard.

## What's different from the retail script

- **Script dialect:** OpenMOHAA's compiler has no `return` keyword — values are returned
  with `end <value>` and captured with `waitthread` (not `thread`). Teams use `setteam`.
- **Bots are always the infected horde.** In every mode the AI bots are on the infected
  (axis) team. Humans fill the survivor roles.
- **Infected are melee-only** via an engine cvar (see ENGINE-PATCHES.md) — the colt45 is
  kept dry each frame so it falls back to its melee secondary. Sprinting + herding too.

## Game modes (rotate every 10 minutes)

| Mode | cvar `28FragsLater`* | Survivors | Weapon | Win |
|------|------|-----------|--------|-----|
| **Survival** | 3 | 1 (fair rotation) | shotgun | outlast the dynamic timer |
| **Outbreak** | 1 | up to 3, co-op | shotgun | last team standing |
| **Infection** | 2 | everyone at first | pistol | any survive 5 min |

*This build reads the mode from the rotation index (`g_28fl_rot`) unless `g_28fl_mode`
(1/2/3) forces a fixed mode. Dynamic Survival time = `300 * (1 - 0.05*infected)`, floored at 60s.

## Server cvars

| cvar | default | meaning |
|------|---------|---------|
| `g_28fl_mode` | 0 | 0 = rotate modes; 1/2/3 = force Outbreak/Infection/Survival |
| `g_28fl_rot` | 0 | rotation index (auto-advances each map; persists as a cvar) |
| `g_infected_disarm` | 1 | 1 = infected are melee-only |
| `g_infected_speed` | 1.35 | infected run-speed multiplier |
| `g_infected_herd` | 1 | 1 = idle bots herd toward a human infected leader + chain-follow |
| `sv_numbots` | 9 | zombie bots |

Rules for the current mode are shown on the TAB / objectives screen
(`g_obj_alliedtext1..3`) and the scoreboard title (`g_gametypestring`).

## Rotation

`sv_maplist` is not used; the script drives it: a 10-minute timer advances `g_28fl_rot`
and calls the `map` script command through the pool of bundled maps
(lowdownmall2, the_alley, megapole, snowhill, STB01_dm_underground, tpm_final), and the
next map's `main:` derives its mode from the new index.

## Leaderboard

`winner_tally` accumulates per-player survivor-seconds (keyed by name, in the `game`
namespace) and announces the session leader at each rotation.
