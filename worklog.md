# Subtractive Split Toning — Design Log

A consolidated record of what we explored, why **Normalize EV** was causing artifacts, the options we weighed, and the lightweight implementation we landed on for testing.

---

## Overview

- Observed “rough edges” or seam-like artifacts around the shadow/highlight split when using Normalize EV in tandem with Balance.
- Core issue: Normalize EV scales scene-linear RGB before mask construction, shifting perceptual lightness and density inputs so the split seam rides across steep parts of the mask curve.
- Evaluated three fixes (A/B/C) and chose a no‑UI adaptive layer that re‑centers Balance and Balance Width from EV, preserving the workflow while reducing seam harshness.

---

## Problem and Symptoms

- Rough transitions along the shadow/highlight boundary when:
  - `Normalize_Internal` is enabled and `Normalize_EV` is moved
  - Balance and/or Balance Width are set narrow
- Artifacts most visible near the balance pivot zone, midtone isolation, and shadow‑reach cutoff.

---

## Root Causes

- **Shifted mask domain**  
  `Normalize_Internal` applies a scalar `kNorm` before masks.  
  OKLab lightness `Ln` and `Yrel` are computed from the scaled image, so EV moves pixels across the Balance/Width smoothsteps.

- **Non‑inverted masks**  
  Exposure is restored post‑effect via `inv_kNorm`, but masks remain in the scaled domain.

- **Steep transitions**  
  Narrow Balance Width means small EV changes push pixels across the steepest smoothstep region.

- **Compounded weighting**  
  Extra shaping multiplies into split masks, amplifying shifts.

---

## Options Considered

- **Option A — Move normalization after mask calc**  
  Masks from unnormalized lightness; density/hue in normalized space.  
  Pro: Mask edges lock; seams stop sliding.  
  Con: Density shaping loses normalization re‑centering.

- **Option B — Undo normalization just for mask inputs**  
  Invert scale for `Ln`/`Yrel` used in masks.  
  Pro: Masks stable; density/hue still normalized.  
  Con: EV no longer moves the split creatively.

- **Option C — Normalize in perceptual space upstream**  
  Shift exposure in OKLab L before masks/density.  
  Pro: EV influences Balance coverage perceptually.  
  Con: Alters tonal distribution; may need re‑tuning.

**Decision:** Keep EV as a creative lever. Option B kills that; Option C aligns but may require re‑tuning. Chose minimal compromise — adaptive auto‑offsets for Balance/Width driven by EV.

---

## Final Direction

- Keep `Normalize_Internal` + `Normalize_EV` behavior.
- Add silent adaptive layer:
  - Interpret `Normalize_EV` as intent signal.
  - Offset Balance toward a mid target.
  - Modulate Balance Width with EV.
- No UI changes; compile‑time toggle for easy A/B.

---

## Implementation Summary

Injected EV→mask‑centering step in `transform()` after `kNorm` is computed, before `applySplitToneSubtractive()`. Conservative defaults, clamps, no‑op if `Normalize_Internal` is off.

### Compile‑time Toggles

```c
#define ADAPTIVE_ENABLE        1
#define ADAPTIVE_STRENGTH      0.50f
#define ADAPTIVE_MID_TARGET    0.50f
#define ADAPTIVE_EV_TO_MID     0.12f
#define ADAPTIVE_EV_TO_WIDTH   0.20f
```

### EV Mapping Helper

```c
__DEVICE__ float squashSym(float x){
    float ax = fabsf(x);
    return (ax > 0.0f) ? (x / (1.0f + ax)) : 0.0f;
}
```

### Adaptive Block

```c
#if ADAPTIVE_ENABLE
    if (normalizeInternal_local > 0.5f) {
        float ev = normalizeEV_local;
        float s  = squashSym(ev);

        float b_base = balance_local;
        float b_auto = clamp01(ADAPTIVE_MID_TARGET + ADAPTIVE_EV_TO_MID * s);

        float w_base = clamp01(balanceWidth_local);
        float w_auto = clamp01(w_base * (1.0f - ADAPTIVE_EV_TO_WIDTH * s));

        float k = clamp01(ADAPTIVE_STRENGTH);
        balance_local      = b_base + (b_auto - b_base) * k;
        balanceWidth_local = w_base + (w_auto - w_base) * k;
    }
#endif
```

---

## Testing Plan

- Baseline A/B: `ADAPTIVE_ENABLE` 0 vs 1; sweep EV ±2; check seam smoothness.
- Scene profiles:
  - Low‑key (+EV): Balance drifts up, Width tightens.
  - High‑key (−EV): Balance drifts down, Width relaxes.
- Stress points: narrow Width; high `midtoneIsolation`; `shadowReachCut`; `linkedBalanceMode` on/off.
- Reversibility: Adaptive disabled → identical output to current build.

---

## Risks & Mitigations

- Unpredictability: Same Balance value lands differently with EV. Mitigated by conservative coefficients.
- Compensation strength: Tune `ADAPTIVE_EV_TO_MID` / `ADAPTIVE_EV_TO_WIDTH` in ±0.02 steps.
- Edge cases: Skewed histograms may still give odd coverage.

---

## Future Work

- Histogram‑aware adaptive mode (percentile‑based split centering).
- Option B “lock mode” for EV‑insensitive masks.
- Perceptual‑normalized variant (Option C) with re‑tuned density.
- Optional UX indicator for auto mid shift.
