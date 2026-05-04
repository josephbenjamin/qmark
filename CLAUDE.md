# CLAUDE.md — q-mark

Logo builder for Quiet Media. Single-page app served by Python stdlib HTTP server.

## Files

- `index.html` — entire frontend (HTML + CSS + JS, no build step)
- `main.py` — `http.server` wrapper, port 3000, silences access logs
- `pyproject.toml` — Python 3.12+, zero dependencies

## Architecture

All rendering is SVG, mutated live by JS on every input event. No framework, no state object — sliders read directly in `render()`.

### SVG coordinate convention

`CX=200, CY=200, R=130` (viewBox 400×400). Angle convention: **0° = up, clockwise positive** (compass bearing). Convert to SVG coords via:

```js
function pt(angle) {
  const a = toRad(angle);
  return [CX + R * Math.sin(a), CY - R * Math.cos(a)];
}
```

### Key render logic

**Bar**: two endpoints computed from center along direction vector `(sin θ, −cos θ)`. `innerReach` extends in `−dir`, `outerReach` in `+dir`, both as % of R.

**Ring**: filled annulus rendered as a compound SVG path (not a stroked arc). Explicit `ringInner` (Rin) and `ringOuter` (Rout) radii. `buildRingPath()` returns a closed path:
1. Outer arc CW (long way, sweep=1) from right gap edge to left gap edge
2. Line inward (left slot edge)
3. Inner arc CCW (long way, sweep=0) from left gap edge back to right gap edge
4. Line outward (right slot edge) → Z

`ptR(angle, radius)` maps compass-bearing angle + radius to SVG `[x, y]`.

### Gap modes

Two modes controlled by `#parallelGap` checkbox:

- **Radial** (default): same angular half-offset `gapSlider/2` at both Rin and Rout. Gap edges, if extended inward, converge at center.
- **Parallel to bar**: `gapSlider` = full angular gap at inner edge (Rin). Slot half-width `W = Rin · sin(gapSlider/2)`. Outer arc endpoints at `arcsin(W / Rout)`. Gap edges are lines parallel to the bar — straight, not converging.

`buildRingPath()` handles both modes. `onParallelToggle()` only updates the label; no slider conversion needed since both modes use degrees.

## Extending

To add a new parameter: add a `<input type="range">` in the panel, read it in `render()`, apply to the relevant SVG attribute. The generic input listener at the bottom auto-wires it.

To change circle radius: update `R` constant. `pt()`, all arc/bar math, and parallel-gap `arcsin` all derive from `R`.
