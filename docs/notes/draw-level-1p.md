# DrawLevelOvr1P (overlay 226, `0x800A0CBC`)

The 1P level renderer, hand-written assembly. `MainFrame_RenderFrame` calls it at `0x80036F3C` with a0 = the
tracker's bucket list (gGT+0x1808), a1 = the view (push buffer 0, gGT+0x168), a2 = the mesh, a3 = the back buffer's
primitive memory (back buffer +0x74: `cursor` at +0xC, `guardEnd` at +0x10, a primitive count at +0x14). Overlays
227-229 (2P-4P) load at the same address and share the layout.

## The view's rect

Two setup blocks read the view's rect, both inside this call: the entry (`0x800A0D8C`-`0x800A0DC0`) and
`func_800AA790` (`0x800AA7D8`-`0x800AA820`, reached from the entry at `0x800A0EAC`). Each loads OFX = rect.w << 15
and OFY = rect.h << 15 into GTE control 24/25, H from view +0x18 into control 26, and stores the rect's
(w, h) word (view +0x20) to scratchpad `0x1F80006C` as the packed clip window. Nothing else in the overlay reads
the rect.

## The window reject

23 sites (one per primitive kind, near `nclip`) load the window from `0x6C($at)` and reject a polygon whose packed
screen words all have a sign bit set (wholly left of column 0 or above row 0) or whose `sxy - window` are all
non-negative in a half (wholly at or right of column w, or at or below row h):

```
reject = ~(or of (sxy - window)) | (and of sxy);   rejected when bit 31 or bit 15 is set
```

The left bound is a sign bit, so it is always column 0; only OFX moves which columns land there.
