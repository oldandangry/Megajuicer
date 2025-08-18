# Changelog

All notable changes to this project are documented here.

## [0.03] - 2025-08-18

### Added
- Hue‑preserving highlight guard: luminance‑based roll‑off that compresses highlights without shifting hue or chroma.
- Internal exposure normalization (optional): Normalize_Internal and Normalize_EV stabilize masks and density across clips without changing final exposure.
- Chroma Protect slider: reduces dye density in high‑chroma regions to better preserve saturated colors.

### Changed
- Correct OKLab conversion (signed LMS’ cube‑root): improves hue fidelity and low‑chroma stability. Retuned low‑chroma gate thresholds to keep behavior perceptually consistent.
- Mask core refinement and renormalization: smoother shadow/highlight masks; restores complementary balance (tS + tH ≈ 1) after shaping.
- Linked/Decoupled mode logic: linked preserves overall “dye energy” more tightly; decoupled stays independent without bias drift.
- Order of operations: protections/dampening now apply after mask normalization for more predictable results.

### Fixed
- KM hue‑drift cap: prevents runaway hue shifts in deep shadows under high density.
- Cleaner highlights under strong toning: avoids specular hue pulling and maintains smooth gradients.

### Notes
- Existing looks should feel the same, but more stable in edge cases.
- In rare cases that relied on a previous midtone “dead zone,” you may want minor tweaks (Balance_Width or Midtone_Isolation ≈ −0.05 to −0.10).

