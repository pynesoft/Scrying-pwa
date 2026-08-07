# Scrying Field

A wave equation solved on a grid and rendered as a phosphor halftone lattice.
Tap to drop an impulse; ripples reflect off the edges and interfere. Optional
sonification reads a closed ring through the field and plays it as a waveform.

https://pynesoft.github.io/Scrying-pwa/

## Deploying to GitHub Pages

1. Put these files at the **root** of a repo (or in a `docs/` folder).
2. Settings → Pages → Source: *Deploy from a branch*, branch `main`, folder
   `/` (or `/docs`).
3. Wait a minute, then open `https://<user>.github.io/<repo>/` on the phone.
4. Chrome menu → *Add to Home screen*. Firefox → *Install*.

All paths are relative, so it works from a subdirectory without changes.

## Why it has to be hosted

Service workers and installability require HTTPS. A `file://` page can't
register a worker, so it can't be installed no matter what the manifest says.
GitHub Pages gives you HTTPS for free.

## After you change anything

Bump `CACHE` in `sw.js` (`scrying-v1` → `scrying-v2`). The service worker is
network-first, so you'll normally see edits immediately when online, but
bumping the version guarantees stale copies are dropped.

## The constants worth touching

All at the top of the `<script>` in `index.html`, grouped by what they affect.

### Lattice

| Constant | Does what |
| --- | --- |
| `S` | Dot spacing in css px. Also sets how far back the depth illusion sits — larger is further away. |
| `SUB` | Sim cells per dot, per axis. `1` is cheapest; `2`–`3` smooth the wavefront edges. Cost scales with the square. |

### Dot size

These are independent of `LEVELS` by design. Changing how finely the field is
graded should not change how big the dots are.

| Constant | Does what |
| --- | --- |
| `LEVELS` | Quantization steps, trough to crest. Only affects grading. |
| `DOT_FILL` | Largest dot as a fraction of `S`. |
| `MIN_DOT` | Smallest dot as a fraction of the largest. **This is the one that sets how heavy the resting lattice looks.** |
| `SIZE_GAMMA` | Below 1 fattens the middle of the range so mid values read as dots, not specks. |

### Field

| Constant | Does what |
| --- | --- |
| `C2` | Wave speed squared. Keep under ~0.33 or it goes unstable. |
| `DAMP_BASE` | How long ripples persist. Closer to 1 rings longer. |
| `STEPS_BASE` | Sim steps per frame. Raise for faster waves. |
| `DRAG_AMP` | Impulse strength while dragging. `0` disables drag entirely. |
| `REST_EPS` | Below this amplitude the lattice fades to black. |
| `STILL_EPS` | Below this it stops animating. Raise it on battery. |

`SUBSTEPS`, `DAMP`, and `IMPULSE_R` are *derived* from `SUB` so that speed,
decay, and tap size stay put on screen when you change it. Edit the base
values above, not the derived ones.

### Sound

| Constant | Does what |
| --- | --- |
| `RING_R` | Ring radius in cells. Small is rough and noisy, large is smoother and more tonal. |
| `BASE_FREQ` | Pitch in Hz. Independent of `RING_R`. |
| `HARMONICS` | How much of the ring's shape survives into the tone. Higher is grittier. |
| `CUTOFF` | Lowpass. 3200 is roughly a guitar speaker cabinet. |
| `MASTER` | Overall level. Deliberately low. |

## How the sound works

The field is a solution to the wave equation, which is the same equation that
governs sound in air — so nothing is synthesised. A closed ring of cells is
sampled around the last place you touched, and that sequence is handed to an
oscillator as one cycle of its waveform. A ring is used rather than a row
because it has no endpoints, so the waveform is periodic by construction and
there is no seam to click.

The ring is DC-stripped (an off-centre ring carries a constant offset that
thumps) and normalised to its own peak, so timbre stays audible while the
amplitude envelope comes separately from the field's real energy.

It uses `PeriodicWave` rather than an `AudioWorklet` on purpose: loading a
worklet module from a `file://` page needs a blob URL, and Firefox refuses
that from a null origin.

## Notes

The regular lattice produces a wallpaper illusion — let your eyes diverge
slightly and the pattern floats behind the screen. That is a side effect of
even dot spacing, not a deliberate stereogram. Jittering the dot positions
would remove it.

Licence: CC BY-NC 4.0
