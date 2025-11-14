# Proximity Message System Testing Guide

## Overview
The proximity message system prevents camping by showing graduated distance hints to infected players after 1 minute of no contact with survivors.

## Test Setup

### Prerequisites
- Medal of Honor: Allied Assault with mod loaded
- At least 2 players (1 survivor, 1 infected)
- Map with enough space to test different distances

### Configuration
Default values are:
- Timeout: 60 seconds (1 minute)
- Hot: 10 meters
- Warm: 25 meters
- Cold: 50 meters
- None: 100+ meters

## Test Scenarios

### Test 1: Basic Proximity Timer (Survival Mode)
**Objective:** Verify that proximity messages appear after 1 minute of no contact

**Steps:**
1. Start game in Survival Mode (`setcvar 28FragsLater 3`)
2. Select 1 survivor, 1+ infected
3. Have survivor and infected stay far apart (>100m)
4. Wait 60 seconds
5. Have infected player move to 45m from survivor (COLD range)
6. Wait 1 second

**Expected Result:**
- No messages shown for first 60 seconds
- After 60 seconds at 45m, infected player sees: "Proximity: COLD"
- Message appears every second while in range

### Test 2: Graduated Distance Messages
**Objective:** Verify correct messages at different distances

**Steps:**
1. Setup: Survivor and infected far apart (>100m) for 60+ seconds
2. Move infected to 80m from survivor
3. Move infected to 45m from survivor (COLD)
4. Move infected to 20m from survivor (WARM)
5. Move infected to 8m from survivor (HOT)

**Expected Results:**
- At 80m (beyond PROXIMITY_NONE): No message
- At 45m: "Proximity: COLD"
- At 20m: "Proximity: WARM"
- At 8m: "Proximity: HOT"

### Test 3: Timer Reset on Close Proximity
**Objective:** Verify timer resets when players get within 10m

**Steps:**
1. Survivor and infected >100m apart for 60 seconds
2. Infected sees proximity messages
3. Infected moves within 10m of survivor (HOT range)
4. Infected moves away to >100m
5. Wait 30 seconds
6. Check for proximity messages

**Expected Results:**
- Messages appear after initial 60 seconds
- Messages stop when within 10m
- Timer resets at 10m proximity
- No messages for another 60 seconds after moving away
- Messages reappear after full 60 seconds

### Test 4: Message Spam Prevention
**Objective:** Verify messages don't spam too frequently

**Steps:**
1. Get proximity messages showing (after 60 second timeout)
2. Stay at WARM range (20m)
3. Observe message frequency

**Expected Result:**
- Messages appear approximately once per second
- No rapid-fire message spam

### Test 5: Multiple Infected Players (Survival Mode)
**Objective:** Verify all infected players see the same messages

**Steps:**
1. Setup: 1 survivor, 3+ infected players
2. All infected stay >100m from survivor for 60 seconds
3. Closest infected is at 30m (WARM range)
4. Other infected are at 80m (no message range)

**Expected Result:**
- ALL infected players see "Proximity: WARM"
- Message based on closest infected player's distance
- All infected get the same hint

### Test 6: Infection Mode (Multiple Survivors)
**Objective:** Verify proximity works with multiple survivors

**Steps:**
1. Start Infection Mode (`setcvar 28FragsLater 2`)
2. Patient Zero and multiple survivors spread out >100m
3. Wait 60 seconds
4. Move infected to 40m from NEAREST survivor
5. Keep other survivors far away (>100m)

**Expected Result:**
- After 60 seconds, infected sees "Proximity: COLD"
- Distance calculated to NEAREST survivor, not all survivors

### Test 7: Outbreak Mode (3 Survivors)
**Objective:** Verify proximity works in Outbreak mode

**Steps:**
1. Start Outbreak Mode (`setcvar 28FragsLater 1`)
2. 3 survivors selected, rest infected
3. All players spread out >100m
4. Wait 60 seconds
5. Move infected toward nearest survivor

**Expected Result:**
- After 60 seconds, proximity messages appear
- Messages based on distance to closest survivor
- Works same as Infection mode

### Test 8: Edge Case - Survivor Camps in Corner
**Objective:** Verify system helps infected find campers

**Steps:**
1. Survivor hides in remote corner of map
2. Infected search but stay >100m away for 60+ seconds
3. Infected gradually move closer to survivor's area

**Expected Result:**
- After 60 seconds, infected start seeing hints
- "COLD" at 50m helps narrow search area
- "WARM" at 25m indicates getting close
- "HOT" at 10m means very close
- Timer resets once they reach survivor

### Test 9: Dynamic Gameplay
**Objective:** Verify timer behavior during normal gameplay

**Steps:**
1. Start round with normal gameplay
2. Infected chase survivor but lose track
3. >60 seconds pass without contact
4. Infected searches different areas of map

**Expected Result:**
- Timer accumulates time when players are far apart
- At 60 seconds, hints start appearing
- Hints help infected find general area
- Once contact made (within 10m), timer resets
- System encourages active gameplay

## Console Commands for Testing

```
setcvar 28FragsLater 3        # Survival mode
setcvar 28FragsLater 2        # Infection mode
setcvar 28FragsLater 1        # Outbreak mode
setcvar 28FragsLater 0        # Disable mod

setcvar g_gametype 2          # Team Deathmatch (required)
```

## Verification Checklist

- [ ] Messages appear after exactly 60 seconds of no proximity
- [ ] COLD message at 50m or less
- [ ] WARM message at 25m or less
- [ ] HOT message at 10m or less
- [ ] No message beyond 100m
- [ ] Timer resets when within 10m
- [ ] Messages show to infected players only
- [ ] Messages rate-limited (1 per second)
- [ ] Works in Survival mode
- [ ] Works in Infection mode
- [ ] Works in Outbreak mode
- [ ] Multiple infected see same message
- [ ] Distance calculated to closest survivor
- [ ] Previous proximity indicator cleared when in HOT range

## Expected Behavior Summary

1. **Normal Gameplay (0-60 seconds):** No proximity messages, timer running
2. **Camping Detected (60+ seconds, >100m):** Timer expires, but no message yet
3. **Getting Closer (60+ seconds, 50-100m):** "Proximity: COLD" message appears
4. **Closing In (60+ seconds, 25-50m):** "Proximity: WARM" message appears
5. **Very Close (60+ seconds, 10-25m):** "Proximity: HOT" message appears
6. **Contact Made (<10m):** Timer resets, messages stop
7. **Chase Lost (>10m):** Timer starts again from 0

## Notes

- The system is designed to help infected players find camping survivors WITHOUT giving exact locations
- Proximity messages are intentionally vague to maintain gameplay balance
- Timer reset at 10m encourages active engagement
- All infected players see the same proximity level (not individual)
- Works across all three game modes
