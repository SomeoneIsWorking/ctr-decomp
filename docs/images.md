# Images and overlays

The disc boots `SCUS_944.26`; the game loads its code overlays from `BIGFILE.BIG`, whose index (from word 2 of its
first three sectors) gives each entry a sector offset and byte size. Overlays are raw images (no header) loaded at a
fixed slot. psx-crash-team-racing's `tools/extract_overlays.py` reads the same index.

## Executable

| image | load | entry | text | rodata | code | data | bss | gp | SHA-1 |
|---|---|---|---|---|---|---|---|---|---|
| `SCUS_944.26` | `0x80010000` | `0x8007793C` | `0x7D800` | `0x80010000` | `0x800123E0` | `0x800809A0` | `0x8008D800`-`0x8009F6FC` | `0x8008CF6C` | `1fd69c9f69890d66778d10fdfd8ed5c9013f4b3a` |

SHA-256 `7b4aac0bf2f6310984e599295df17b457da5a23b270c20200cefef6079efb838`, LBA 24, 516,096 bytes, header stack
`0x801FFFF0`. crt0 sets the stack top to `0x807FFFF8`, the heap from `0x8009F6FC` (`0x007588FC` bytes) and calls the
libc initializer `0x80080620`. The code ends with three BIOS call stubs (`li t2; jr t2; li t1`) at
`0x80080970`-`0x8008099F`. Six words inside the code are PsyQ library markers (`0x00xx7350`, `0x00440000`) at
`0x8007784C`, `0x80077D08` and `0x8007C9A0`; they stay as `.word`.

## Overlays

`BIGFILE.BIG`: 270,360,576 bytes, SHA-1 `5501bef2521df6a930d6bf4f8f3a951e56af8ab7`. Each image's first word is
a small per-image number (221-225: 5-9, 226-229: 0xB-0xE, 230-233: 0x10-0x13).

| entry | slot | archive offset | size | code from | code to | SHA-1 of the image |
|---|---|---|---|---|---|---|
| 221 | `0x8009F6FC` | `0x684E800` | `0x530` | `0x14` | `0x4F0` | `bcb216a32ace63aa4c4fd056d2debc7294fc25e8` |
| 222 | `0x8009F6FC` | `0x684F000` | `0x14B4` | `0x8` | `0x143C` | `91c180a5708bebe7cc28acc7a68222c0d8b668d7` |
| 223 | `0x8009F6FC` | `0x6850800` | `0x15BC` | `0x5D4` | `0x15B8` | `72ef5bbadeae066f1dee7f72e49a2b04888de88c` |
| 224 | `0x8009F6FC` | `0x6852000` | `0xDD8` | `0x8` | `0xD38` | `40be4e8f753fc935b1eccc9597b4eac807fb7f6c` |
| 225 | `0x8009F6FC` | `0x6853000` | `0xB0C` | `0x1C` | `0xA48` | `bbaec5eefd9233d63a5ac627be21d9f155d23c5b` |
| 226 | `0x800A0CB8` | `0x6854000` | `0xACB8` | `0x4` | `0xA754` | `ce2729438334fc794fe62bbc3e00fb462d69f4d5` |
| 227 | `0x800A0CB8` | `0x685F000` | `0xAD38` | `0x4` | `0xA7D4` | `02a08621eaa6df5fff892f4bd0aea3f53928c36f` |
| 228 | `0x800A0CB8` | `0x686A000` | `0x85A0` | `0x4` | `0x8180` | `013f37e8d6721f9929ebd0b0091f8a9f72635c6c` |
| 229 | `0x800A0CB8` | `0x6872800` | `0x8654` | `0x4` | `0x8234` | `e9b24c195c9cb4c7cfc2e1a863fd3f3f65e80349` |
| 230 | `0x800AB9F0` | `0x687B000` | `0xAE44` | `0xB8` | `0xADBC` | `bb3a476b4c6b1b483fa5e5833e0fda497668a727` |
| 231 | `0x800AB9F0` | `0x6886000` | `0xEB58` | `0xC0` | `0xEB58` | `df367b8b00b30122af8916fa547e4f65ffc7f4bc` |
| 232 | `0x800AB9F0` | `0x6895000` | `0x9C84` | `0x10C` | `0x93EC` | `1a8aad6fa61d3e1d2c0b78792b08ff1f33cf2232` |
| 233 | `0x800AB9F0` | `0x689F000` | `0xDE0C` | `0x3E4` | `0xDA98` | `1e288e6a74305d85bfed2ae576407993c581fdea` |

Slots (roles as ctr-native's `disc.json` names them): 221-225 are the end-of-race menus, 226-229 the 1-4 player level renderers, 230-233 the main menu, race,
adventure hub and cutscene threads (one of each group is resident at a time).

A boundary is the first and last word of the code run: code starts where the image's leading table or strings end
(the first instruction word, which is also the lowest call target or prologue), and ends after the last `jr ra` and
its delay slot. Data islands inside the code (strings and tables between functions, image offsets):

| entry | islands |
|---|---|
| 230 | `0x8AF4`-`0xA07C` |
| 231 | `0x6F6C`-`0x7730`, `0x7EEC`-`0x7EF4`, `0x81D0`-`0x8314`, `0x9F78`-`0x9F94`, `0xA954`-`0xAAD0`, `0xB0A4`-`0xB510`, `0xB93C`-`0xB948`, `0xC130`-`0xC19C`, `0xC6F4`-`0xC7F8`, `0xD0DC`-`0xD210`, `0xD8B0`-`0xD8BC`, `0xDBF4`-`0xDC0C`, `0xE3B4`-`0xE51C`, `0xE8C8`-`0xE8D0` |
| 233 | `0x518C`-`0xBD94`, `0xCBA8`-`0xCC78` |

Each island holds an undecodable word, starts after a function's `jr ra` and delay slot, and ends at the next call
target, prologue or pointed-to code word; with them carved out no code subsegment of any overlay holds an
undecodable word.
