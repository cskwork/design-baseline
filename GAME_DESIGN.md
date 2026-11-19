# GAME_DESIGN.md

Universal baseline for game visual craft, HUD/UX architecture, core loop ergonomics, game feel, reward pacing, accessibility, runtime engineering, and verification. Every gameplay, UI, audio, and visual change must comply with this document before completion.

---

## 0. Project Overrides (Customize Per Game)

- **Engine / Runtime:** [e.g., Web Canvas / PixiJS / Three.js / Phaser / Godot 4 / Unity]
- **Target Platforms:** [e.g., Desktop Web + Mobile Web / PC + Steam Deck / iOS + Android]
- **Genre & Core Loop:** [e.g., 2D Action Roguelite — Move -> Dodge -> Strike -> Upgrade]
- **Art Style & Rendering Mode:** [e.g., Clean Vector / Stylized 3D / Pixel Art (integer scale, nearest-neighbor)]
- **Primary Input Schemes:** [e.g., Keyboard+Mouse, Gamepad, Touch]
- **Frame Budget:** `60 FPS` target (`<= 16.6ms` frame time; `p95 <= 16.6ms`; `30 FPS` absolute floor on low-end/handheld)
- **Primary Locales:** Korean + English (`ko`, `en`)

---

## 1. Visual Hierarchy, Fairness & Minimum Viable HUD

### 3-Tier Figure-Ground Separation (Mandatory)
Every active frame must pass the **3-second silhouette & grayscale test**—threats, player position, and objectives must read instantly with all color removed:

1. **Tier 1 — Gameplay Foreground (Highest Contrast & Crispness):** Player, enemies, projectiles, hazards, interactables, and attack telegraphs. Clear silhouettes, high value contrast against the arena, clean outlines or rim separation where needed.
2. **Tier 2 — Interactive World & VFX (Medium Contrast):** Terrain edges, pickups, hit sparks, footprints, environmental cues. Short-lived effects that never obscure Tier 1 silhouettes or telegraphs.
3. **Tier 3 — Environment Backdrop (Lowest Contrast):** Floors, parallax layers, ambient props. Restrained saturation and narrow luminance range so foreground gameplay never dissolves into visual clutter.

### Combat & Interaction Fairness
- **Anticipation -> Active -> Recovery:** Every enemy attack, hazard, and major player action must visually and audibly telegraph before becoming lethal. Never ship 0-frame un-telegraphed hits.
- **Player-favorable hitboxes:** Make the player's damage hurtbox slightly smaller than the visual sprite (`~75–85%`), and make player attacks, ledge grabs, and pickup radii slightly generous so near-misses feel fair and earned.

### Minimum Viable HUD ("Start From Zero")
- **Earn every pixel:** Start with zero permanent HUD. Keep an element always visible only if the player needs it moment-to-moment (e.g., HP during combat). Fade or hide contextual meters (stamina, ammo, currency) when full or inactive.
- **Keep the center clear:** Never anchor persistent HUD panels over the central play area. Reserve screen center for gameplay, reticles, momentary interact prompts, and brief combat feedback.
- **Intentional UI layering:**
  - **Diegetic (In-world):** Gauges built into characters or equipment (preferred when clearly legible).
  - **Spatial (World-space):** Entity health bars, damage numbers, world-anchored interact prompts.
  - **Meta (Screen-edge):** Directional threat arcs, subtle low-HP vignette or audio low-pass filter (respect motion settings).
  - **Non-Diegetic (Overlay HUD & Menus):** Corner vitals, pause, inventory, settings.

---

## 2. Layout, Scaling & Rendering Fidelity

- **Anchor-based responsive HUD:** Anchor HUD clusters to screen edges/corners relative to safe margins—never hardcode `16:9` pixel coordinates.
- **Required aspect ratios:** Support `16:9` (`1920x1080`) and `16:10` (`1280x800`, Steam Deck native) with zero stretching, cropping, or awkward letterboxed UI. Constrain HUD spread on ultrawide monitors (`<= 21:9` safe container or configurable HUD margin).
- **Safe areas:**
  - **Desktop / Console / TV:** Enforce a `5%` inner safe margin from all viewport edges for critical HUD elements (overscan safety).
  - **Mobile / Touch:** Respect OS notch, Dynamic Island, and home-indicator safe insets (`env(safe-area-inset-*)` on web or native `SafeArea`).
- **Standard HUD Zone Map:**
  - **Top-Left / Bottom-Left:** Player vitals (HP, shield, status) or minimap.
  - **Top-Center:** Boss bar, wave/objective indicator, match timer.
  - **Top-Right:** Secondary status (score, currency, pause/settings trigger).
  - **Bottom-Center:** Subtitles/dialogue and hotbar (never overlapping one another).
- **Mobile Thumb-Zone & Touch Targets:**
  - Minimum interactive hit region: `48dp` / `44px x 44px` (expand invisible hit bounds around compact visual icons).
  - Place frequent actions in the lower thumb-reach zone; isolate Pause, Restart, and Quit in upper corners or guarded confirmations.
- **Rendering crispness & DPR cap:**
  - **HD / Vector / 3D:** Cap canvas pixel ratio at `Math.min(window.devicePixelRatio, 2)` on web/mobile to prevent GPU fill-rate throttling on 3x–4x displays.
  - **Pixel Art:** Enforce nearest-neighbor filtering (`image-rendering: pixelated`), integer viewport/sprite scaling where possible, and snapped pixel coordinates so sprites never shimmer.

---

## 3. Readability, Typography & Korean/English Localization

### Minimum Rendered Font Sizes (Account for Downscaling)
Measure actual rendered pixel height on screen, especially on the smallest labels (badges, item counts, keycap labels, tooltips—not just body copy):

- **Design-to-Handheld Scaling Rule:** UI designed at `1920x1080` shrinks to `~67%` when scaled down to `1280x800`. Keep smallest readable text at `>= 18px` in a 1080p layout so it stays `>= 12px` on `1280x800` handhelds (`9px` absolute Valve hard floor).
- **Console / Living-Room TV (1080p):** Minimum `26px` (`52px` at 4K).
- **Mobile:** Equivalent to `>= 14–16pt` readable UI copy scaled cleanly by DPI.
- **UI & Text Scaling:** Support configurable UI/text scaling (`0.75x` to `2.0x` where feasible) without clipping, overlap, or dual-axis scrolling.

### Bilingual & Game Typography Rules
- **Complete glyph coverage:** Stylized display fonts are permitted for titles/logos, but body copy, stats, tooltips, and menus must use (or offer a toggle to) a crisp sans-serif with full Hangul + Latin support (`Pretendard`, `Noto Sans KR`, or clean system sans). Never ship missing-glyph tofu boxes (`□`).
- **Line measure & rhythm:** Cap dialogue/tooltip lines at `<= 40` CJK characters (`<= 80` Latin characters) and keep line-height `>= 1.5`.
- **Hangul ergonomics:** Enforce word-boundary wrapping (`word-break: keep-all`) on headings, skill names, tooltips, and dialogue. Never apply `italic` styling to Hangul.
- **Tabular numerals:** Apply `tabular-nums` (or fixed-width numeric glyphs) to all HP bars, timers, scores, cooldowns, and damage counters so numbers never jitter horizontally.
- **Legibility over motion:** Never place raw text directly over moving gameplay. Back HUD copy and subtitles with an adjustable dark scrim (`>= 4.5:1` WCAG AA contrast) or crisp high-contrast outline/shadow.

---

## 4. Controls, Menu Focus & Core Loop Friction

### Time-to-Fun & Friction Budgets
- **Fast boot:** Launch to interactive Title screen in `<= 3s` (allow instant skip of studio/engine logos, especially on repeat launches).
- **1-press entry & retry:** Title -> Play in `1` confirm press. Death/Fail -> Instant Restart in `1` press (`<= 500ms` reset by resetting state in memory rather than reloading all assets).
- **Immediate input response:** Register player actions and emit initial visual/audio confirmation within 1 frame (`<= 16.6ms` at 60fps).
- **Non-blocking menus:** Keep menu transitions under `200ms` (`100–220ms`). Never lock out input during transitions or victory/defeat banners—buffer or allow instant skip.
- **Zero-latency pause:** Pause must immediately freeze gameplay timers, physics, AI, and looping danger SFX. Auto-pause on window blur, browser tab switch (`visibilitychange`), or controller disconnect.

### Full Input Parity, Focus Contract & Dynamic Glyphs
- **100% single-device navigability:** Every screen (launcher, title, menus, tabs, inventory, map, shop, popups, quit dialog) must be fully operable via Gamepad-only, Keyboard-only, or Touch-only.
- **Menu Focus Contract:**
  - Every screen/modal defines an explicit initial focused element.
  - Closing any modal/submenu restores focus to the exact element that opened it.
  - Display an unmistakable focus indicator (`>= 3:1` contrast) and support universal Back (`Escape` / Gamepad `B` / `Circle`).
- **Cursor hygiene:** Hide the mouse cursor and ignore stale pointer hover events while navigating with gamepad/keyboard until real mouse motion exceeds a small threshold.
- **Active-device glyph switching:** Dynamically swap prompt glyphs based on the most recent input device (Keyboard/Mouse, Xbox, PlayStation, Switch, Steam Deck)—never hardcode handheld glyphs solely from OS detection (handles docked handhelds with external controllers). Provide a manual glyph override (`Auto / Xbox / PS / Switch / KB+M`) in Settings.
- **Virtual keyboard & Korean IME safety:** Trigger the platform on-screen keyboard for text fields on handheld/console/touch. While any text input has focus, suspend gameplay hotkeys and guard `Enter` submission against active IME composition (`event.isComposing || event.keyCode === 229`).
- **Control customization:** Support action remapping across supported inputs, Toggle alternatives for Hold/mash mechanics, and configurable stick deadzone, sensitivity, and axis inversion.

---

## 5. Game Feel ("Juice") & Audio Architecture

Juice must clarify state, impact, and spatial position—never add arbitrary clutter. Store all feel constants in a single central config/data file (never scatter magic numbers in gameplay logic).

### Starting Feel Heuristics (Tune Per Genre)
| Mechanic | Purpose | Starting Default | Rules / Guardrails |
|---|---|---|---|
| Coyote Time | Forgive late ledge jumps | `100ms` (6f @ 60fps) | `60ms` (precision) – `130ms` (casual) |
| Input Buffering | Forgive early pre-landing/recovery inputs | `80–100ms` | `50ms` – `120ms` (keep `<=` coyote time) |
| Hitstop (Impact Freeze) | Communicate heavy impact weight | `50–80ms` (3–5f) | Heavy hits/parries only; never on rapid micro-hits |
| Camera Shake | Reinforce heavy impacts/explosions | `0.2–0.35s` decay | Quadratic `trauma^2` decay; scaled by Motion slider (`0–100%`) |
| Squash & Stretch | Convey momentum on jump/land/hit | `15–25%` area-preserving | Visual sprite/mesh ONLY—never scale physics colliders/hitboxes |
| Invincibility Frames | Prevent unfair multi-hit melt | `0.6–1.2s` | Clear visual cadence (`<= 3 Hz` pulse, never rapid strobe) |
| SFX Pitch Variation | Prevent repetitive audio fatigue | `±5%–8%` random pitch | Apply to footsteps, attacks, pickups, UI clicks |

### Audio Architecture (Mandatory)
- **Independent audio channels:** Provide a Master volume slider + 1-click Mute, plus separate sliders for Music (BGM), Gameplay SFX, Speech/Voice, Ambient, and UI SFX (when present).
- **In-game Mono Audio toggle:** Provide a stereo-to-mono downmix toggle inside Settings (do not rely on OS settings alone).
- **Polyphony limiting & ducking:** Enforce per-sound cooldowns (`>= 30–50ms`) and max simultaneous voice caps so multi-hits/pickups never clip speakers. Duck music/ambient slightly during dialogue, pause, or critical alerts.
- **Clean audio lifecycle:** Unlock audio context reliably on the first user gesture (web/mobile) and suspend/resume cleanly on focus loss without blasting queued sounds.

---

## 6. Reward Loops, Payoff Staging & Pacing

Engagement comes from anticipation, earned agency, and escalating payoff, not silent counter increments. Design reward timing as deliberately as combat controls.

### 3-Beat Payoff Staging (Anticipation -> Climax -> Playable Reward)
- **Micro pickups (coins, XP, shards):** Pull items smoothly toward the player via short-range pickup magnetism, animate a crisp collection pop, and pulse the destination HUD counter (`~120ms` ease-out, never blocking input).
- **Major payoffs (level-up, rare chest, boss clear, combo cash-out):** Stage every high-value moment in three beats:
  1. **Anticipation (`120–250ms`):** Rising pitch sweep, charge glow, brief slowdown/hitstop, or container rumble that tells the player something good is about to land.
  2. **Climax:** Distinct audio-visual reveal burst scaled proportionally to rarity/impact (keep flashes `<= 3 Hz` and respect `Reduced Motion`).
  3. **Immediate Playable Payoff:** Let the player feel the reward immediately (new attack pattern, speed burst, synergy trigger). Never trap players in unskippable `> 1.5s` celebration lockouts.

### 3-Tier Loop Cadence (Micro / Meso / Macro)
Every game should maintain overlapping rhythm layers so players always have a near-term goal within reach:

- **Micro Loop (`1–5s`):** Instant hit confirmation, weak-point crits, dodge/parry sparks, pickup magnetism, rhythmic scoring ticks.
- **Meso Loop (`1–5m`):** Wave/room clear, mini-boss reward, level-up draft, noticeable power spike that visibly transforms how the player handles threats.
- **Macro Loop (`10–30m+`):** Permanent unlocks, new playable tools/modifiers, mastery milestones, or visible progression meters surfaced cleanly between runs.

### Escalating Streak & Combo Momentum
- **Musical pitch escalation:** For consecutive hits, chains, multi-kills, or rapid pickups, step SFX pitch upward along a clean musical scale (e.g., pentatonic intervals or `+1` semitone per step) up to an octave cap, rather than repeating one flat sound.
- **Controlled crescendo & resolution:** Intensify subtle visual cadence as streaks climb (glow tier, crisp combo counter pulse) and play a distinct bank/cash-out chime when a chain resolves or completes—while enforcing polyphony caps (`>= 30–50ms` cooldown) so high-speed combos sound exhilarating, never harsh.

### Meaningful Variable Rewards & Agency
- **Transformative choices over spreadsheet math:** Prefer "Pick 1 of 3" upgrades or drops that alter gameplay behavior, unlock synergies, or create risk-reward decisions over dull flat increments (`+2% attack`).
- **Exciting variance with bad-luck protection:** Allow memorable high-roll moments (rare drops, critical chains, bonus rooms), paired with deterministic pity/floor rules so dry streaks never feel punishing or rigged.

### Zero-Friction "One More Run" Recovery
- **Meaningful end-of-run recap:** On defeat or stage clear, immediately surface tangible progress earned (meta-unlock meter advancing, new personal best delta, synergy/build summary) and make the cause of failure clear and learnable.
- **Instant momentum handoff:** Place Retry / Next Run as the default focused action (`1` press, `<= 500ms` reset) so players channel near-miss excitement straight into the next attempt.

### Ethical Craft Boundary (No Dark Patterns)
Dopamine design must serve player joy and skill mastery, never coercion. Hard bans:

- No artificial wait timers, energy gates, or pay-to-skip friction in core loops.
- No deceptive loot odds, fake scarcity timers, FOMO countdown spam, or unearned red notification dot traps.

---

## 7. Runtime Engineering, Performance & Save Integrity

- **Fixed-timestep simulation + `dt` clamp:** Step physics/gameplay on a fixed timestep accumulator (e.g., `1/60s`) with render interpolation for smooth visuals. Clamp raw frame `dt` (e.g., `<= 50ms`) so lag spikes or tab switches never cause wall tunneling or timer leaps. Use frame-rate independent exponential smoothing (`1 - Math.exp(-k * dt)`) for camera follow and UI lerps.
- **Decouple HUD from the 60Hz loop:** Never trigger full DOM/React/UI layout re-renders every frame. Push HUD updates only on state change (dirty flag/event) or throttle continuous bars/timers to `10–20Hz`.
- **Zero-GC hot loop discipline:** Pool high-frequency objects (bullets, particles, damage numbers, audio nodes) instead of allocating/destroying them every frame inside `update()`/`render()`.
- **Save integrity & versioned schemas:**
  - Keep manual save slots separate from autosave—never let autosave silently overwrite a manual save.
  - Never trigger autosave during death animations, mid-fall, or guaranteed-fail states.
  - Version all save payloads (`schemaVersion`) with safe fallback migration and atomic writes so updates or crashes never corrupt player progress.

---

## 8. Accessibility & Sensory Safety (XAG / WCAG / ESA)

- **Multi-channel signaling:** Never communicate critical gameplay state through color alone or sound alone:
  - Pair color with distinct shape, silhouette, icon, or pattern (support color-vision differentiation for protanopia, deuteranopia, tritanopia).
  - Pair critical audio cues (off-screen warnings, dialogue, alarms) with directional visual indicators or captions.
- **Subtitles & captions:** Support adjustable font size, background scrim opacity, speaker identification, and non-verbal SFX captions (e.g., `[발소리]`, `[경고음]`). Allow manual advance and instant text reveal on dialogue/tutorials.
- **Photosensitivity hard safety (WCAG 2.3.1 / XAG 118):**
  - Never exceed **3 flashes per second** in any VFX, damage indicator, reward reveal, or UI alert.
  - Ban full-screen or large-area (`> 20%` viewport) high-contrast flashes and saturated red strobes. Prefer localized rim flashes, subtle desaturation, or slow pulses.
- **3-Tier Motion & Haptics Controls:**
  - Provide `Full / Reduced / None` motion settings (`Reduced` removes camera shake, wobble, heavy parallax, and burst clutter while keeping semantic gameplay telegraphs; `None` removes all non-essential motion).
  - Provide a dedicated Haptics / Rumble toggle and intensity slider (`0–100%`).

---

## 9. AI Game Dev Anti-Slop Rules (Hard Bans)

Never introduce unless explicitly requested:

- **No SaaS / Web-App chrome pasted over games:** No generic glassmorphic Tailwind dashboard cards, purple/cyan AI gradients, or flat admin tables floating over a game canvas. Match framing, typography, and motion to the game's art direction.
- **No Frame-1 tutorial text walls:** Never open with a multi-paragraph modal explaining 5+ mechanics. Teach one verb at a time in a safe space via brief contextual cues (`<= 1` short line + active input glyph).
- **No silent, flat reward loops:** Never ship progression that only increments a tiny unstyled number with zero audio/visual anticipation, no streak escalation, or boring "+2%" stat-only upgrades.
- **No unreadable combat chaos:** Never let particles, bloom, floating numbers, or busy floors overpower Tier 1 silhouettes or attack telegraphs.
- **No missing game hygiene:** Always ship Title -> Play, Pause, Instant Restart, Settings (Audio/Controls/Accessibility), and Quit/Return to Title.
- **No FPS-coupled physics or mutated hitboxes:** Never tie speeds/timers to raw frame counts; never scale collision shapes during visual squash-and-stretch.
- **No predatory dark patterns or tone drift:** Never add fake urgency timers or manipulative notification badges. Use crisp, natural Korean game terminology (`게임 시작`, `이어하기`, `다시 시도`, `설정`, `일시정지`)—never stiff web-form prose or inconsistent tone.

---

## 10. External Verification Gate (Mandatory Before Completion)

Code compiling is not verification. Before reporting any game UI, feel, reward, or scene task complete, run a real runtime verification pass (Playwright/headless browser for web games, or automated test/screenshot harness for native engines):

1. **Multi-Aspect & Downscale Capture:** Capture Title, Active Gameplay, Reward/Level-Up, Pause/Settings, and Game Over states at `1920x1080` (16:9) and `1280x800` (16:10 Steam Deck) (plus mobile portrait/landscape if targeting mobile). Confirm the smallest rendered HUD/badge glyph remains legible at `1280x800` (`>= 12px` recommended, `>= 9px` hard floor) with zero clipped Korean text or overflow.
2. **Grayscale Silhouette & Contrast Audit:** Convert an active gameplay capture to grayscale and inspect directly:
   - Are Tier 1 entities and enemy telegraphs immediately readable against the backdrop?
   - Does every HUD label and subtitle stay crisp over the busiest frame?
3. **Runtime, Frame-Pacing & `dt`-Spike Integrity:**
   - Zero console errors, uncaught exceptions, or missing asset warnings.
   - Verify stable frame pacing (`p95 <= 16.6ms` at 60fps target) with no GC spikes during heavy VFX, reward bursts, or HUD updates.
   - Simulate window blur / tab switch (`visibilitychange` / large `dt` gap) and verify the game auto-pauses cleanly with zero physics tunneling or timer jumps.
4. **Reward Pacing, Input & Safety Pass:**
   - Verify reward moments deliver clear anticipation -> payoff feedback without unskippable input lockouts (`<= 1.5s`) and that Death -> Retry completes in `1` press (`<= 500ms`).
   - Traverse Title -> Play -> Pause -> Settings -> Resume -> Restart -> Quit using only keyboard or gamepad (verify initial focus, visible focus indicator, focus restoration, cursor auto-hide, and `Escape`/`B` back navigation).
   - Verify live glyph switching, audio Mute + Pause freeze, flash frequency `<= 3/sec`, and `Reduced Motion` disabling camera shake.
5. **HUD Subtraction Audit:** Inspect actual gameplay screenshots and remove or fade any counter, border, badge, or label that does not serve an immediate player decision, then re-capture to confirm.
