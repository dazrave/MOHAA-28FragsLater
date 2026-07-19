# OpenMOHAA engine patches used by this build

These four changes live in the **engine** (a fork of openmoh/openmohaa), not in the mod
script. They are all cvar-gated and server-side, so they replicate to any protocol-8
client (retail or OpenMOHAA). Build the dedicated server (`omohaaded` + `game` module)
and the cvars below become available.

### 1. `g_infected_disarm` — melee-only infected
`fgame/player.cpp`, `Player::Think()`: each frame, for a multiplayer axis player, force
the active weapon's clip **and** the reserve ammo pool to 0. The colt45 has a `melee`
secondary, so a bone-dry weapon makes the bot pistol-whip instead of shooting. Survivors
(allies) are untouched.

### 2. `g_infected_speed` — sprinting infected
`fgame/player.cpp`, `Player::GetRunSpeed()`: multiply run speed by the cvar when
`dm_team == TEAM_AXIS`. Default 1.35.

### 3. `g_botmodel_german` / `g_botmodel_allied` — bot skins
`fgame/playerbot.cpp` (userinfo apply): override `dm_playergermanmodel` /
`dm_playermodel` with the cvar value so bots wear the zombie / police skins.

### 4. `g_infected_herd` — herding + follow-the-leader chain
`fgame/playerbot.cpp`:
- `BotController::FindHumanLeader()` — nearest living human on the bot's team.
- `BotController::PickHerdLeader()` — commit to a leader with hysteresis (stable packs
  when several humans are infected; only switch when another is 1.5x closer or the
  current one dies).
- `BotController::UpdateHerdGoal()` — run each frame before the state machine. Rank 0 if
  the bot can see a target (survivor or human shepherd); otherwise it adopts the rank+1
  of the best-informed teammate bot it can see and trails it. This propagates a
  breadcrumb path through doorways so lost bots follow bots who can see bots who can see
  the target. Consumed by `State_Idle` (`MoveNear` the herd goal instead of wandering).

The new player skins referenced by the script (`models/player/german_zombie.tik` and
`_fps`) are a bracket-free rename of the prisoner model — bracketed filenames break the
`.scr` cache parser.
