# Film‑Inspired Subtractive Split Toning DCTL for DaVinci Resolve

A scene‑referred, wide‑gamut split‑toning effect that uses OKLab balance masks, physically‑accurate Beer–Lambert dye attenuation, and Kubelka–Munk‑inspired pigment mixing for realistic, density‑dependent hue separation.

---

## 🎞 Recreate the dye‑layer magic of celluloid

This DCTL brings subtractive split‑toning to DaVinci Resolve’s DWG Linear workflow using OKLab‑based tonal masks, Beer–Lambert dye attenuation, and Kubelka–Munk‑inspired pigment mixing. From density‑tied hue drift to midtone and highlight protection, it delivers the graceful, nonlinear color separation that makes film prints glow.

Most digital toning tools think in RGB, nudging pixel values additively. Kubelka–Munk flips that on its head, modelling how real pigments subtract light as it passes through them — accounting for both absorption and back‑scatter. In film, this interplay between dye layers is what lets a warm highlight breathe into a cool shadow without a hard seam. By borrowing from that model, this DCTL delivers density‑dependent hue shifts and cross‑channel “bleed” that feel like they’ve lived a life on celluloid, not just a curve on a scope.

---

## ✨ Feature highlights

| Feature | Description |
|---------|-------------|
| **Scene‑Referred, Wide Gamut Workflow** | Designed for DaVinci Wide Gamut Linear pipelines to preserve maximum color information. |
| **OKLab Balance Masks** | Perceptually uniform masks for shadows and highlights, with linked or decoupled balance modes for different grading styles. |
| **Physically‑Accurate Beer–Lambert Attenuation** | Models light absorption through dye layers for a genuinely subtractive toning feel. |
| **Kubelka–Munk‑Inspired Pigment Mixing** | Simulates cross‑channel “bleed” and density‑dependent hue drift, echoing the way film dyes interact. |
| **Density‑Tied Hue Separation** | Color separation responds organically to luminance, creating smooth, filmic transitions without harsh edges. |
| **Midtone & Highlight Protection** | Adjustable controls to prevent hue shifts in skin tones and specular highlights. |
| **Shadow Reach & Saturation Control** | Fine‑tune how deep toning extends into midtones and adjust chroma response in dark regions. |
| **Hybrid Pigment Shaping** | Control pigment richness, cross‑channel travel, and drift for both technical accuracy and creative freedom. |
| **Visual Debug Mode** | RGB chip chart display to quickly visualize balance masks and toning hues. |

---

## 🚀 Quick start: Using the DCTL in DaVinci Resolve

1. **Download & Install**  
   - Save the `.dctl` file into Resolve’s LUT folder or a dedicated subfolder for your custom effects.  
     - **macOS:** `/Library/Application Support/Blackmagic Design/DaVinci Resolve/LUT/`  
     - **Windows:** `C:\ProgramData\Blackmagic Design\DaVinci Resolve\Support\LUT\`  
   - Restart Resolve so it detects the new file.

2. **Set Your Timeline to DWG Linear**  
   - Project Settings → Color Management → Set **Working Color Space** to *DaVinci Wide Gamut / DaVinci Intermediate* (or DWG Linear variant if using node‑based transforms).  
   - This ensures the DCTL sees scene‑referred, wide‑gamut values for correct pigment modelling.

3. **Apply the DCTL**  
   - On the Color page, add a node.  
   - In the node’s context menu → LUT → navigate to your installed `.dctl` → click to apply.  
   - *Or*: Use **ResolveFX DCTL** from the OpenFX panel and select the file.

4. **Dial In Your Look**  
   - **Shadow/Highlight Hue:** pick toning colors in degrees (0–360°).  
   - **Strength** sliders: control overall and per‑zone blend amounts.  
   - **Balance / Width:** shift the crossover point and how soft the transition feels.  
   - Explore advanced controls (KM Strength, Pigment Richness, Density Drift) for more film‑like, non‑linear behavior.

5. **Debug & Fine‑Tune**  
   - Enable **RGB Chips** to visualize tonal masks and toning vectors.  
   - Protect skin tones via **Midtone Isolation** and **Highlight Protect** sliders.
## 🎨 Suggested starting points

> Use these as jumping‑off points — tweak to taste based on your footage and grading style.

| Look | Shadow Hue | Highlight Hue | Strength | Notes |
|------|------------|---------------|----------|-------|
| **Cinematic Teal‑Orange** | 200° (Teal) | 30° (Orange) | 0.6 | Cool shadows, warm highlights; friendly to skin tones. |
| **Warm Vintage** | 40° (Amber) | 350° (Rose) | 0.5 | Faded print vibe with gentle warmth and rosy highs. |
| **Cool Silver‑Gelatin** | 230° (Blue) | 50° (Warm White) | 0.4 | B&W‑toning feel; deep cool lows with a warm highlight kiss. |
| **Muted Pastel Wash** | 300° (Magenta) | 140° (Mint) | 0.3 | Soft, airy palette; low strength for delicate toning. |
| **Cross‑Processed Punch** | 120° (Green‑Cyan) | 290° (Purple) | 0.7 | Bold separation and attitude for stylized pieces. |

---

## 🎛 Parameter reference

| Parameter | Description |
|-----------|-------------|
| **Shadow Hue** | Target hue (°) for toning shadows. |
| **Highlight Hue** | Target hue (°) for toning highlights. |
| **Strength** | Global blend amount for the entire effect. |
| **Strength Shadows** | Blend amount applied only to shadow regions. |
| **Strength Highlights** | Blend amount applied only to highlight regions. |
| **Balance** | Cross‑over point between shadow and highlight zones. |
| **Balance Width** | Softness of the transition between zones. |
| **Film Density** | Overall optical “thickness” of dye layers; higher = stronger subtractive effect. |
| **Density Base** | Baseline density multiplier applied everywhere. |
| **Density Bias Shadows** | Extra density applied in shadows. |
| **Density Bias Highlights** | Extra density applied in highlights. |
| **Shadow Sat** | Saturation multiplier applied to deep shadows. |
| **Midtone Isolation** | Dampens toning around mid‑tones to preserve neutrality. |
| **Shadow Reach** | How far the shadow zone extends into the midtones (<1 pushes in, >1 confines). |
| **Shadow Reach Cut** | Cuts toning from brighter parts of the shadow range. |
| **Highlight Protect Start** | Lightness level where highlight protection begins. |
| **Highlight Protect End** | Lightness level where highlight protection is fully applied. |
| **Linked Balance Mode** | Toggle for linked/coupled shadow/highlight balance masks. |
| **Cross‑Channel Hue Travel** | Amount of “bleed” between shadow and highlight dye vectors. |
| **Density Drift** | Modulates density based on whether a pixel is more shadow‑ or highlight‑weighted. |
| **Pigment Richness** | Multiplier for subtractive strength; simulates dye “thickness.” |
| **Dye Saturation** | Sets the maximum chroma of the dye vectors themselves. |
| **KM Strength** | Controls density‑dependent hue drift inspired by Kubelka–Munk. |
| **KM Cap Override** | Caps the maximum hue shift in degrees for KM hue drift. |
| **RGB Chips** | Shows debug chart with mask zones and toning hues instead of the image. |

