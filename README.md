# Airsoft Ballistics Calculator

A physics-based airsoft trajectory calculator. Input your gun's energy and BB weight, dial in hop-up until the trajectory is flat, then read exactly where to aim at 25, 30, and 50 yards.

**Live site:** coming soon

---

## What It Does

Simulates real airsoft ballistics with drag, Magnus lift (hop-up spin), spin decay, and temperature-corrected air density. Shows a 2D trajectory chart alongside an impact data table with plain-English aiming advice.

**Workflow:**
1. Select a preset or enter your gun's energy (joules) and BB weight
2. Adjust hop-up % until the trajectory line is flat (50% = flat baseline)
3. Read the drop table — it tells you where to aim at each distance

---

## Tech Stack

| Layer | Choice |
|-------|--------|
| Structure | HTML5 |
| Styling | CSS3 (custom properties, grid, flexbox, keyframe animations) |
| Interactivity | Vanilla JS (ES6, no frameworks) |
| Fonts | Share Tech Mono + Barlow Condensed + Barlow (Google Fonts) |
| Backend | None |
| Dependencies | Zero |
| Build step | None |

Single file application. All HTML, CSS, and JavaScript live in `index.html` (~1,519 lines).

---

## File Structure

```
BB_trajectory/
└── index.html      All HTML, embedded CSS, and inline JS (~1,519 lines)
```

---

## Physics Model

**Forces simulated per timestep (dt = 2ms):**

| Force | Formula |
|-------|---------|
| Drag | F_D = 0.5 × ρ × C_D × A × v² |
| Magnus lift (hop-up) | F_L = 0.5 × ρ × C_L × A × r × ω × v |
| Gravity | F_g = m × 9.81 |

**Constants:**

| Constant | Value | Note |
|----------|-------|------|
| C_D | 0.45 | Drag coefficient (sphere) |
| C_L | 0.28 | Magnus lift coefficient |
| BB diameter | 6 mm | Standard airsoft BB |
| Spin decay | 2.5s | Exponential time constant |

**Hop-up calibration:** 50% = lift exactly equals gravity at muzzle (flat trajectory). 0–50% scales 0–1× optimal spin. 50–100% scales 1–4× (rising shots).

**Air density** is corrected for temperature via the ideal gas law.

---

## Features

### Inputs
- **Muzzle energy** — 0.1–5.0J (numeric + slider)
- **BB weight** — 0.12g to 0.43g (10 presets via dropdown)
- **Hop-up** — 0–100% (numeric + slider)
- **Launch angle** — -10° to +15°
- **Wind speed** — -25 to +25 mph (headwind/tailwind)
- **Temperature** — 0–110°F (affects air density)

### Quick Presets

| Preset | Energy | BB Weight |
|--------|--------|-----------|
| Pistol | 1.0J | 0.20g |
| Rifle (AEG) | 1.5J | 0.25g |
| DMR | 2.32J | 0.28g |
| Sniper | 2.32J | 0.40g |

### Chart View
- 2D trajectory canvas, auto-scales to trajectory length (min 50 yd)
- Solid green = with hop-up / Dashed amber = no hop-up
- ±20cm hit zone band around aim line
- Effective range marker (where BB exits hit zone)
- Summary bar: muzzle FPS, effective range, max range, 50-yd flight time

### Data View
Drop table at 10, 20, 25, 30, 40, 50 yards:

| Column | Description |
|--------|-------------|
| BB Height | Height above/below aim line, color-coded |
| Where to Aim | Plain English: "Aim center", "Aim at waist", etc. |
| Velocity | Speed at that distance (fps) |
| Energy | Kinetic energy (joules) |
| Status | FLAT / OK / DROPPING / GROUNDED badge |

Advice card shows hop status (UNDER-HOP / OPTIMAL / OVER-HOP) with a plain-English tuning tip.

---

## JavaScript Functions

### Physics
- **`simulate()`** — core engine, runs 2ms timestep loop with drag + Magnus lift + gravity + spin decay
- **`airDensity(tempC)`** — temperature-corrected air density
- **`heightAt(pts, dist)`** — linear interpolation of height at distance
- **`velocityAt(pts, dist)`** — interpolated speed at distance
- **`timeAt(pts, dist)`** — interpolated time-of-flight at distance

### Rendering
- **`drawChart()`** — DPI-aware canvas renderer, auto-scaling, mobile-responsive

### UI
- **`calculate()`** — reads all inputs, runs simulation × 2 (with/without hop), updates all UI
- **`loadPreset(type)`** — fills inputs from pistol/rifle/dmr/sniper preset
- **`syncSlider()` / `syncInput()`** — keeps numeric inputs and range sliders in sync
- **`updateSliderTracks()`** — updates slider fill via CSS `--pct` variable
- **`switchTab(tab)`** — mobile tab switching (chart/params/data)
- **`toggleAdvanced()`** — expands/collapses Wind & Environment section

All input changes auto-trigger `calculate()`. No manual submit button.

---

## Responsive Layout

| Viewport | Layout |
|----------|--------|
| > 720px | 2-column (params left, chart/data right) |
| ≤ 720px | Single column |
| ≤ 480px portrait | Full-screen tabs (CHART / PARAMS / DATA) |
| ≤ 900px landscape | Compact 2-column mini layout |

---

## Design System

Dark terminal / military aesthetic with green accent.

| Variable | Value | Use |
|----------|-------|-----|
| `--bg` | `#0a0c0e` | Page background |
| `--green` | `#39ff14` | Primary accent (neon green) |
| `--amber` | `#ffb800` | Warning / no-hop trajectory |
| `--red` | `#ff3030` | Error / ground line |
| `--text` | `#c8d4c0` | Primary text |

---

## Deployment

No build step. No environment variables. Push and it's live.

```bash
git add index.html
git commit -m "your message"
git push
```

PAT is baked into the remote URL. Auto-deploys on push.

---

## Physics References

- ATP (Airsoft Trajectory Project) by Nathan
- cybersloth.org aerodynamic modeling
- Standard quadratic drag for spheres (C_D ≈ 0.45)
- Magnus force: standard spin-dependent aerodynamic lift formula
