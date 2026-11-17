# The cutscene Start skip (overlay 233, `CS_Thread_UseOpcode` `0x800AC840`)

`CS_Thread_UseOpcode(instance a0, cutscene a1)` copies the cutscene's decoded opcode (a1 +0x4C..+0x5F) to its frame
at entry. The skip test sits on the path with no instance (`bnez $s4` at `0x800ACB74` leaves it), after that call's
camera step, at `0x800ACD60`: gamepad 0's tapped word (`g_GamepadsPointer` +0x14) against Start (`0x1000`) only.

On Start it clears bit 0 of the tracker's halfword at +0x2570 (the camera path's clock effect), then by level id
(tracker +0x1A10):

- 0x2C-0x3F (credits): `func_800B93F4` ends the credits.
- 0x29 (the Naughty Dog crate): refused while `msInThisLEV` (tracker +0x1D00) >> 5 < 0xB5 (5,792 ms); otherwise
  `RaceFlag_SetCanDraw(1)`, and `RaceFlag_SetFullyOffScreen()` unless `RaceFlag_IsTransitioning()` or
  `RaceFlag_IsFullyOnScreen()`; then `CseqMusic_StopAll`, `CDSYS_XAPauseRequest`, `RaceFlag_SetDrawOrder(0)`,
  `MainRaceTrack_RequestLoad(0x27)` (main menu).
- 0x2A-0x2B: the same flag steps, then `MainRaceTrack_RequestLoad(0x2C)` (credits) without the music stop.
- any other: the same flag steps and the crate's tail.

Every taken skip then sets `CS_FINISHED` (`0x800B7760`) to 1, clears bit 2 (`VEH_FREEZE_PODIUM`) of tracker +0x8,
restores the decoded opcode from its entry copy and returns 1.
