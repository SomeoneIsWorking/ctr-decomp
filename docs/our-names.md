# Our earlier names

psx-crash-team-racing named addresses after the role it saw at the call site before this map existed. The map keeps
the community's name (or a name for what the code is); the port adopts the map's names when its constants are
generated (its plan 0047).

| address | map | port constant |
|---|---|---|
| `0x8001D06C` | `CDSYS_XAPauseAtEnd` | `kStartupAudioService` |
| `0x8001CF98` | `CDSYS_XAPauseRequest` | `kXaPauseRequest` |
| `0x800251AC` | `GAMEPAD_Init` | `kStartupDisplayInit` |
| `0x80025208` | `GAMEPAD_SetMainMode` | `kShutdownDisplay` |
| `0x800293B8` | `Bank_AssignSpuAddrs` | `kBootResourcePumpPoll` |
| `0x800297A0` | `Bank_Load` | `kBootResourcePumpBegin` |
| `0x80029C40` | `howl_SetSong` | `kBootResourcePumpCommit` |
| `0x80029CA4` | `howl_LoadSong` | `kBootResourcePumpCommitPoll` |
| `0x8002DD24` | `Music_SetIntro` | `kBootResourcePump` |
| `0x80031EE4` | `LOAD_VramFileCallback` | `kBootResourceCommit` |
| `0x80031FDC` | `LOAD_VramFile` | `kBootResourceWait` |
| `0x80032110` | `LOAD_ReadFileASyncCallback` | `kBigfileCompletionCallback` |
| `0x800321B4` | `LOAD_ReadFile` | `kBootResourceSetup` |
| `0x80035E70` | `MainFrame_RenderFrame` | `kFrameOwner` |
| `0x8003C58C` | `main` | `kGuestMain` |
| `0x8003CFC0` | `MainRaceTrack_RequestLoad` | `kRequestLevelLoad` |
| `0x8003D7D8` | `MEMCARD_InitCard` | `kStartupGpuInit` |
| `0x8003E978` | `MEMPACK_PushState` | `kBootResourcePreWaitHelper` |
| `0x80042910` | `PushBuffer_SetPsyqGeom` | `kProjectionProducer` |
| `0x800440A0` | `RaceFlag_GetOT` | `kRaceFlagOrderingTable` |
| `0x8004B3A4` | `Timer_GetTime_Total` | `kFrameTiming` |
| `0x8008044C` | `LibApi_DMACallback` | `kDmaCallback` (ctr-native's `DMACallback` is `0x80077224`) |
| `0x8008D708` | `g_XaState` | `kStartupAudioWaitState` |
| `0x80096B20` | `g_GameTracker` | `kGameTracker` (ctr-native: `gGT`) |

Return sites are labels named for the call they follow (`mainMemcardInitReturn` is the port's
`kStartupGpuInitReturn`); `config/symbols/scus_944.26.txt` lists them in address order.
