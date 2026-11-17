# Structures

Field offsets the port reads, until the structures are declared under `include/`. Names in parentheses are
ctr-native's.

## Game tracker (`gGT`, static at `0x80096B20`, pointer `g_GameTrackerPointer` = gp+0x340)

| offset | field |
|---|---|
| `0x0` | game mode (`gameMode1`); bit `0x40000000` is set while a level loads |
| `0x10` | back buffer (`backBuffer`), a draw buffer below |
| `0x168` | push buffers (`pushBuffer[4]`), stride `0x110`, a view below |
| `0x25C` | push buffer 0's ordering table (`pushBuffer[0].ptrOT`); `DrawOTag` gets entry `+0xFFC` (`ClearOTagR` order) |
| `0x18F8` | instance pool (`JitPools.instance`): taken list `+0xC`, max items `+0x18`, item size `+0x1C`, pool data `+0x24` |
| `0x1A10` | level id; 0..0x11 are the race tracks |
| `0x1CE0` | VSync callback count the frame suffix waits on |
| `0x1D04` | elapsed milliseconds this frame |
| `0x1D30` | DrawSync pending byte, cleared by `DrawSyncCallback` |
| `0x24EC` | drivers (`drivers[8]`) |

## View (push buffer, `0x110` bytes)

| offset | field |
|---|---|
| `0x18` | screen distance H |
| `0x20` | viewport width (16-bit) |
| `0x22` | viewport height (16-bit) |

## Draw buffer

| offset | field |
|---|---|
| `0x80` | primitive cursor |
| `0x84` | primitive pool end; an allocation proceeds while end >= cursor |

## sdata (gp = `0x8008CF6C`)

| gp offset | symbol | meaning |
|---|---|---|
| `0x188` | `g_MainState` | `main`'s state switch; 3 runs the game |
| `0x18C` | `g_LoadRequest` | -1 none; `MainRaceTrack_RequestLoad` sets -4 and the level at `0x190` |
| `0x340` | `g_GameTrackerPointer` | |
| `0x344` | `g_GamepadsPointer` | |
| `0x348` | `g_FrameWaitFields` | two-field countdown drained by the vertical-blank callback |
| `0x4D8`-`0x4F0` | `g_RaceFlag*` | `RaceFlag_DrawSelf`: position, can-draw, text frame, elapsed, load-stage copy |

## RaceFlag_DrawSelf

35 columns of 10 RTPT rows (3 points each), projected with OFX 256, OFY 120, H 256. `g_RaceFlagWave` is five words:
angle 1, amplitude, angle 2, speed, phase. Each quad is a `POLY_G4` (code `0x38`, 9 words) from the back buffer's
pool; odd checker squares shade 105-160, even ones 130-255 by the column's light. The quad reject is packed: sign
mask `0x80008000`, window `0x00D80200` (216 rows by 512 columns).
