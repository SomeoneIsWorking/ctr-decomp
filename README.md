# Crash Team Racing decomp

A decompilation of **Crash Team Racing** (USA, `SCUS-94426`), set up with [splat](https://github.com/ethteck/splat).
It holds the split configuration for the executable and the code overlays 221-233 in `BIGFILE.BIG`, a symbol map from
our own reverse engineering, and the RE notes. No C is matched yet.

The PC port [psx-crash-team-racing](https://github.com/SomeoneIsWorking/psx-crash-team-racing) consumes this
repository as a submodule and reads its guest addresses from the symbol map.

## Bring your own disc

No game bytes are ever committed here: not the executable, not the archive, not the split assembly or data. Copy two
files from your own disc into `disc/` (gitignored):

```
disc/SCUS_944.26
disc/BIGFILE.BIG
```

Each config carries its file's SHA-1 and splat refuses a mismatch. The overlays are split straight out of
`BIGFILE.BIG` at their archive offsets; the image set is in `docs/images.md`.

## Split

```
uv run --frozen splat split config/scus_944.26.yaml
uv run --frozen splat split config/overlays/ov226.yaml     # one config per overlay, ov221..ov233
```

Output goes to `asm/<image>/` and `build/`, both gitignored. splat is pinned in `pyproject.toml`/`uv.lock`.

## Layout

| path | what |
|---|---|
| `config/scus_944.26.yaml`, `config/overlays/ov<n>.yaml` | splat configs, one per image |
| `config/symbols/<image>.txt` | the symbol map, splat `symbol_addrs` format; overlay configs also read `scus_944.26.txt` |
| `docs/images.md` | the image set, load slots and segment boundaries |
| `docs/structures.md` | field offsets of the game structures the port reads |
| `docs/our-names.md` | the port's earlier names for addresses the map names otherwise |
| `docs/re-frontier.md` | the ordered RE evidence chain (`re_frontier.py`) |
| `docs/prior-art.md` | other CTR work, licences and what we take |

Function names follow the community's (CTR-tools' `syms926.txt`) where it names the address, so notes read the same
across projects; that repository is GPL and only its names are used here.

## Licence

MIT (`LICENSE`), covering this repository's own files only. Crash Team Racing is © Sony Interactive Entertainment /
Naughty Dog; nothing of it is distributed here.

Built with AI assistance (Claude and other LLMs).
