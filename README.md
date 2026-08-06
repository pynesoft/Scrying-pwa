# Scrying Field

A wave equation solved on a grid and rendered as a phosphor halftone lattice.
Tap to drop an impulse; ripples reflect off the edges and interfere. Optional
sonification reads a closed ring through the field and plays it as a waveform.

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

All at the top of the `<script>` in `index.html`.

| Constant | Does what |
| --- | --- |
| `S` | Dot spacing in css px. Sets how far back the depth illusion sits. |
| `SUB` | Sim cells per dot, per axis. `1` is fastest, `2`–`3` smooth the wavefront edges. |
| `C2` | Wave speed squared. Keep under ~0.33. |
| `DAMP_BASE` | How long ripples persist. Closer to 1 rings longer. |
| `REST_EPS` | Below this amplitude the lattice fades to black. |
| `STILL_EPS` | Below this it stops animating. Raise it on battery. |
| `RING_R` | Sound: ring radius in cells. Small is rough, large is smooth. |
| `HARMONICS` | Sound: how much of the ring's shape survives into the tone. |
| `MASTER` | Sound: overall level. |

## Notes

The regular lattice produces a wallpaper illusion — let your eyes diverge
slightly and the pattern floats behind the screen. That is a side effect of
even dot spacing, not a deliberate stereogram. Jittering the dot positions
would remove it.

Licence: CC BY-NC 4.0
