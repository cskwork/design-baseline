# GAME_DESIGN.md

Universal baseline for building award-caliber games: uniting creative direction, greybox-first prototyping, authored pacing, tactile game feel, reward choreography, runtime engineering, verified 2026 platform/accessibility standards, and dual automated + human verification. Every gameplay, UI, audio, and visual change must comply with this document before completion.

---

## 0. Game Identity & Creative Lock (Fill Before Writing Feature Code)

Guidelines raise the floor; these locked decisions set the ceiling. Never start polishing visuals or meta-systems until this block is filled in and validated.

- **One-Sentence Hook:** [What makes a player stop scrolling and immediately understand why this game is uniquely fun?]
- **3 Non-Negotiable Pillars:** [e.g., 1. One-second tactical readability, 2. Synergies that feel gloriously broken, 3. Zero-downtime kinetic flow]
- **3 Explicit Anti-Pillars (What This Game Refuses to Be):** [e.g., No bloated inventory spreadsheets, no unskippable exposition, no spongy enemies]
- **Signature Visual & Audio Identity:**
  - **1-Screenshot Test:** [What silhouette, color palette/mood, and framing make this game recognizable in 1 second with the logo hidden?]
  - **2-Second Sound Test:** [What signature sound palette and reactive music mood define this world?]
- **Engine / Platforms / Input:** [e.g., Web Canvas / PixiJS / Three.js / Phaser / Godot 4 / Unity] | [PC, Steam Deck, Mobile] | [KB+M, Gamepad, Touch]
- **Frame Budget & Locales:** `60 FPS` target (`p95 <= 16.6ms`; `30 FPS` absolute floor on handheld/low-end) | Korean + English (`ko`, `en`)
- **Art-Direction Lock (Before Gate 2):** Pick the look by eye, not by description:
  - Produce `2–3` clearly different art directions as AI concept frames of the **same two scenes**: the core gameplay moment and the climax/reward moment.
  - Build a cheap live in-engine preview of that same scene with switchable presets (palette, lighting, outline, post) and an on-screen perf readout (FPS, p95, draw calls), so the chosen look is proven affordable on the target device.
  - The decision-maker picks one direction; record it here, and only then build the slice.
  - **Concept art is a mood reference only.** Image models get domain content wrong (e.g. two of three concepts drew a pentagonal-prism net with `4` or `6` side faces instead of `5`). Check every concept frame for factual accuracy, and generate real content (shapes, maps, diagrams, numbers) from data/code, never by tracing the concept.

---

## 1. Production Gates: Prove Fun Before Polish

AI coding agents can easily polish a dull prototype until it looks finished. Enforce strict stage gates so polish multiplies genuine fun rather than disguising weak mechanics:

1. **Gate 1 — Greybox Core Loop (Zero-Art Fun Test):** Build the 30-second core loop using untextured geometric shapes, zero particles, and zero meta-menus. If movement, timing, risk-reward, and core verbs are not genuinely compelling in greybox, fix the rules—do not cover boredom with shaders or juice.
2. **Gate 2 — Vertical Slice (Signature Identity Lock):** Build one complete, shippable slice (`Boot -> Play -> Escalation -> Climax -> Reward -> Retry`) featuring the locked art direction (§0 Art-Direction Lock), dynamic audio, HUD minimalism, feel tuning, and first-run onboarding taught inside that same slice (§3 Teach Inside the Real Run).
3. **Gate 3 — Depth Over Bloat (Combinatorial Expansion):** Expand content by introducing mechanics that interact multiplicatively with existing rules (synergies, counterplay, environmental combinations) rather than bolting on isolated one-off gimmicks.

---

## 2. Visual Identity, Figure-Ground & Minimum Viable HUD

### 3-Tier Figure-Ground Separation (Mandatory)
Every active frame must pass both the **1-Screenshot Identity Test** (distinctive mood and art direction) and the **3-Second Grayscale Silhouette Test** (instant threat/goal recognition with color stripped away):

1. **Tier 1 — Gameplay Foreground (Highest Contrast & Crispness):** Player, enemies, projectiles, hazards, interactables, and attack telegraphs. Distinct silhouettes, strong luminance contrast against the arena, clean rim/outline separation where needed.
2. **Tier 2 — Interactive World & VFX (Medium Contrast):** Terrain edges, pickups, hit sparks, footprints, environmental clues. Crisp, short-lived effects that never obscure Tier 1 silhouettes or telegraphs.
3. **Tier 3 — Environment Backdrop (Lowest Contrast):** Floors, parallax depth, ambient props. Restrained saturation and tighter value range so foreground combat never dissolves into visual noise.

### Minimum Viable HUD ("Start From Zero")
- **Earn every pixel:** Begin with an empty HUD. Keep an indicator permanently visible only if the player needs it moment-to-moment (e.g., HP in active combat). Contextual meters (stamina, ammo, currency) fade in on change and recede when idle.
- **Keep the center clear:** Never anchor persistent HUD boxes over the central playfield. Reserve screen center for gameplay, reticles, momentary interact prompts, and brief impact cues.
- **Intentional UI Layering (Prefer Immersion When Legible):**
  - **Diegetic (In-world):** Gauges built into characters, weapons, or environment.
  - **Spatial (World-space):** Entity health bars, damage numbers, world-anchored interact prompts.
  - **Meta (Screen-edge):** Directional hit arcs, subtle low-HP vignette or audio low-pass filter (respect motion settings).
  - **Non-Diegetic (Overlay HUD & Menus):** Corner vitals, pause, inventory, settings.

### Production Art Sourcing & IP
- **Generated art over code-drawn placeholders:** When visual quality matters, produce characters, environments, icons, and VFX sheets with an image model (or artists) in the locked direction. Code-drawn shapes are greybox only (Gate 1); if one ships, list it as a known gap.
- **Prompt and inspect for marks:** Every prompt says `no logos, no brand marks, no text`. Inspect every output at full size for trademarks, signatures, watermarks, and garbled or wrong text before it enters the build; render real text in-engine.
- **Style references describe a look, never a source:** "Clean sporty product look" is fine; copying a specific existing game, character, mascot, or brand (name, design, silhouette, logo) is not. Never name the referenced work in generation prompts.

---

## 3. Authored Pacing, Teaching & Combat Fairness

Award-winning games orchestrate tension and release—constant max-intensity action becomes numb within two minutes.

- **Tension-Release Rhythm:** Alternate high-pressure peaks (combat waves, boss phases, timed puzzles) with deliberate breather beats (post-clear collection, upgrade drafting, quiet traversal, discovery) so peaks feel dramatic.
- **Kishōtenketsu Level & Mechanic Design (Show, Don't Lecture):** Teach every major mechanic through level/encounter structure instead of text walls:
  1. **Introduce** in a safe, low-stakes space (one verb at a time).
  2. **Develop** with real stakes and spatial variation.
  3. **Twist** by combining it with a second mechanic or reversing expectations.
  4. **Resolve** with a memorable climax or mastery check.
- **Teach Inside the Real Run (Onboarding Is the Game):** The tutorial is the player's first real run, not a detour before it. It should feel like playing from the first second.
  - **Same flow, run 1 = run N:** First-time players go through the exact same skeleton as returning players (`Title -> Play -> Escalation -> Result -> Retry`) in the same scene, HUD, and rules. Never add a separate tutorial mode, practice level, sandbox, "How to Play" screen, or mandatory intro modal, and never insert extra screens or presses between `Title` and the first verb. Only prompt text and early stakes differ.
  - **Learn by doing the real verb:** Show a prompt when a verb first matters in play. Clear it when the player performs that verb in the live game, never through a `Next`/`OK`/`Got it` button or a page of text. A player who already knows the verb skips the prompt simply by doing it, so no "skip tutorial" dialog is needed.
  - **One verb, one anchored prompt:** Show at most one short prompt at a time (`<= 1` line + active glyph), anchored where the action happens (on the target, control, or world object) rather than in a centered box. Teach only what the current core loop needs. Introduce later mechanics in the run where they first appear (Kishōtenketsu *Introduce*).
  - **Safety through structure, not a frozen world:** Lower the early stakes inside the run. For example, the first threat waits until the first verb is done, windows are generous, and the first fail costs nothing. Do not stop the whole game behind an overlay. If something must hold, hold only the threat the player has not yet learned to answer, and keep animation, audio, and input alive.
  - **Tutorial progress is real progress:** Rewards, collection, records, and (in learning games) answers earned during onboarding count under the normal rules. Never use a throwaway practice score. In learning games, the first real question is the first tutorial step.
  - **Help on hesitation, never forced:** After visible hesitation on the current goal (about `5–8s` without the expected action), show a contextual hint or a `Help` control that points at the next real action in place. Hints never block input, rewind the run, or force a replay. Players with a save see no first-run prompts. Re-showing hints is a setting, not a separate lesson.
- **Combat & Interaction Fairness:**
  - **Anticipation -> Active -> Recovery:** Every lethal attack, hazard, and heavy player move must telegraph visually and audibly before becoming active. Never ship 0-frame cheap hits.
  - **Player-favorable hitboxes:** Inset the player's damage hurtbox slightly inside the visual sprite (`~75–85%`), and keep player attacks, ledge grabs, and pickup radii slightly generous so close calls feel heroic and fair.
  - **Readable enemy ecology:** Give each enemy archetype one unmistakable silhouette, sound cue, and movement/attack role so combinations create tactical puzzles rather than chaotic stat blobs.

---

## 4. Layout, Multi-Aspect Scaling & Typography (2026 Verified)

### Responsive Layout & Rendering Fidelity
- **Anchor-based responsive HUD:** Anchor HUD clusters to screen edges/corners relative to safe margins—never hardcode `16:9` pixel coordinates.
- **Required aspect ratios:** Support `16:9` (`1920x1080`) and `16:10` (`1280x800`, Steam Deck native; `1280x720` fallback) without stretching, cropping, or awkward letterboxed UI. Constrain HUD spread on ultrawide displays (`<= 21:9` safe container or configurable margin).
- **Safe areas:**
  - **Desktop / Console / TV:** Enforce a `5%` inner safe margin from all viewport edges for critical HUD elements (overscan protection).
  - **Mobile / Touch:** Respect OS notch, Dynamic Island, and home-indicator insets (`env(safe-area-inset-*)` on web or native `SafeArea`), with `>= 48dp` / `44px x 44px` hit regions and frequent controls placed in the lower thumb-reach zone.
- **Rendering crispness & DPR cap:**
  - **HD / Vector / 3D:** Cap canvas DPR at `Math.min(window.devicePixelRatio, 2)` on web/mobile to avoid GPU fill-rate throttling on 3x–4x screens.
  - **Pixel Art:** Enforce nearest-neighbor filtering (`image-rendering: pixelated`), integer viewport/sprite scaling where possible, and snapped pixel coordinates to prevent shimmer.

### Minimum Rendered Font Sizes (XAG 101 & Steam Deck Verified)
Measure actual rendered pixel height (body height) on screen, including tiny badges, item counts, and text inside icons/glyphs:

- **PC / Desktop:** Minimum `18px` at `1080p` (`36px` at `4K`).
- **Console / Living-Room TV:** Minimum `26px` at `1080p` (`52px` at `4K`).
- **Handheld / Steam Deck (`1280x800`):** Aim for `>= 12px` rendered (`9px` Valve hard approval floor). Scaling math: UI authored at `1920x1080` scales to `~67%` at `1280x800`, so keeping smallest 1080p text `>= 18px` preserves `>= 12px` on handhelds.
- **Mobile / Cloud Streaming:** Scale linearly with DPI (`18px` @ 100 DPI, `36px` @ 200 DPI, `72px` @ 400 DPI).
- **Text & Icon Scaling:** Support configurable text/UI scaling up to `200%` (scaling embedded icons/glyphs alongside text) with zero clipping, overlap, or dual-axis scrolling.

### Bilingual & Game Typography Rules (Korean + English)
- **Complete glyph coverage & readable option:** Stylized display fonts are welcome for titles/logos, but body copy, tooltips, stats, and menus must use (or offer a toggle to) a clean sans-serif font with complete Hangul + Latin character coverage (`Pretendard`, `Noto Sans KR`, or clean system sans). Zero missing-glyph tofu boxes (`□`).
- **Case, alignment & line measure:** Support proper sentence case on multi-word lines (avoid all-caps paragraphs), left-align body paragraphs (avoid centered/fully justified blocks), cap lines at `<= 40` CJK chars (`<= 80` Latin chars), and keep line-height `>= 1.5`.
- **Hangul ergonomics & tabular numbers:** Enforce word-boundary wrapping (`word-break: keep-all`) on titles, skills, tooltips, and dialogue; never italicize Hangul; apply `tabular-nums` to all HP, score, timer, cooldown, and damage counters.
- **Legibility over motion:** Never place bare text directly over moving gameplay. Pair HUD copy and subtitles with an adjustable dark scrim (`>= 4.5:1` WCAG AA contrast) or crisp high-contrast outline/shadow.

---

## 5. Control-First Ergonomics, Focus & Zero-Friction Loop

### Time-to-Fun & Friction Budgets
- **Fast boot:** Interactive Title screen in `<= 3s` (allow instant skip of splash/engine logos).
- **1-press entry & retry:** Title -> Play in `1` press. Defeat/Fail -> Instant Restart in `1` press (`<= 500ms` via in-memory state reset rather than full asset reload).
- **Onboarding adds zero friction:** A first-time player reaches the first meaningful verb in the real run within `<= 10s` of pressing Play, with the same number of presses as a returning player (`0` extra screens, modals, or confirmations).
- **1-frame input response:** Register player inputs and display initial visual/audio reaction within 1 frame (`<= 16.6ms` at 60fps).
- **Non-blocking menus:** Menu transitions `< 200ms` (`100–220ms`). Never lock out input during transitions or result banners—allow buffering or instant skip.
- **Zero-latency pause:** Immediately freeze gameplay timers, physics, AI, and looping danger SFX. Auto-pause on window blur, browser tab switch (`visibilitychange`), or controller disconnect.

### Full Input Parity, Menu Focus Contract & Live Glyphs
- **100% single-device navigability:** Every screen (launcher, title, menus, tabs, inventory, shop, map, popups, quit confirmation) must be completely playable using only Gamepad, only Keyboard, or only Touch.
- **Menu Focus Contract:**
  - Every screen/modal defines an explicit initial focused control.
  - Closing any popup/submenu restores focus to the exact trigger element.
  - Display a high-contrast focus ring (`>= 3:1`) and support universal Back (`Escape` / Gamepad `B` / `Circle`).
- **Cursor hygiene:** Hide the mouse cursor and ignore stale pointer hover events while navigating via gamepad/keyboard until real mouse movement exceeds a small threshold.
- **Active-device glyph switching:** Swap on-screen button prompts live based on the most recently used input device (KB+M, Steam Deck, Steam Controller, Xbox, PlayStation, Switch)—never assume handheld glyphs solely from OS detection. Provide a manual glyph override (`Auto / Xbox / PS / Switch / KB+M`) in Settings.
- **Virtual keyboard & Korean IME safety:** Invoke the platform on-screen keyboard (or built-in controller text entry) for text fields on handheld/console/touch. Suspend gameplay hotkeys whenever a text field has focus, and guard custom `Enter` handlers against active IME composition (`event.isComposing || event.keyCode === 229`).
- **Control customization:** Support action remapping, Toggle alternatives for Hold/mash inputs, and configurable analog deadzone, sensitivity, and axis inversion.

---

## 6. Game Feel ("Juice") & Adaptive Audio Identity

Juice must communicate weight, state, and spatial clarity—never arbitrary clutter. Centralize all feel constants in a single tunable config/data file (never scatter magic numbers across scripts).

### Starting Feel Heuristics (Tune Per Genre & Playtest)
| Mechanic | Purpose | Starting Default | Rules / Guardrails |
|---|---|---|---|
| Coyote Time | Forgive late ledge jumps | `100ms` (6f @ 60fps) | `60ms` (precision) – `130ms` (casual) |
| Input Buffering | Forgive early pre-landing/recovery inputs | `80–100ms` | `50ms` – `120ms` (keep `<=` coyote time) |
| Hitstop (Impact Freeze) | Communicate heavy impact weight | `50–80ms` (3–5f) | Heavy hits/parries/kills only; never on rapid micro-hits |
| Camera Shake | Reinforce heavy impacts/explosions | `0.2–0.35s` decay | Quadratic `trauma^2` decay; scaled by Motion slider (`0–100%`) |
| Squash & Stretch | Convey momentum on jump/land/hit | `15–25%` area-preserving | Visual sprite/mesh ONLY—never scale physics colliders/hitboxes |
| Invincibility Frames | Prevent unfair multi-hit melt | `0.6–1.2s` | Clear visual cadence (`<= 3 Hz` pulse, never rapid strobe) |
| SFX Pitch Variation | Prevent repetitive audio fatigue | `±5%–8%` random pitch | Apply to footsteps, attacks, pickups, UI clicks |

### Audio Architecture & Adaptive Music (50% of Perceived Polish)
- **Reactive music stems:** Layer or transition music dynamically across gameplay states (`Explore -> Tension/Combat -> Boss/Climax -> Low-pass/ducked Pause/Menu`) so the soundtrack responds directly to player stakes.
- **Independent audio channels:** Provide Master + 1-click Mute, plus separate sliders for Music (BGM), Gameplay SFX, Speech/Voice, Ambient, and UI SFX (when present).
- **In-game Mono Audio toggle:** Include stereo-to-mono downmix inside Settings (do not rely on OS toggles alone).
- **Polyphony caps & ducking:** Enforce per-sound cooldowns (`>= 30–50ms`) and voice caps so rapid multi-hits/pickups never distort or clip. Duck BGM/ambient cleanly during dialogue, pause, and critical warnings.
- **Clean audio lifecycle:** Unlock audio reliably on the first user gesture (web/mobile) and suspend/resume cleanly on focus loss without bursting queued cues.

### Asset Sourcing & Register
- **Real recorded SFX for core verbs:** Use recorded sounds (CC0 or licensed) over synthesized beeps for every core verb and impact (a gunshot must sound like a gun). Layer `2–3` samples per heavy action and keep `3–5` variations or pitch variation (`±5%–8%`) per cue.
- **Credit generated music:** AI-generated music is allowed when its license permits; credit the tool/model in the in-app Credits screen.
- **Asset register (every kept asset):** For each art, SFX, and music file record: source, license, author/account, date, prompt (if generated), file hash, and processing applied. No entry, no ship.
- **Lazy-load with fallback:** Load samples and music lazily; a failed or slow load falls back to silence or a lighter cue and never blocks input, boot, or the first interactive frame.

---

## 7. Reward Loops, Payoff Staging & Ethical Momentum

Memorable games choreograph anticipation, agency, and escalating payoff—never silent counter increments.

### 3-Beat Payoff Staging (Anticipation -> Climax -> Playable Reward)
- **Micro pickups (coins, XP, shards):** Pull smoothly via short-range pickup magnetism, play a crisp collection pop, and pulse the destination HUD counter (`~120ms` ease-out, never blocking input).
- **Major payoffs (level-up, rare drop, boss clear, combo cash-out):** Choreograph in three beats:
  1. **Anticipation (`120–250ms`):** Rising pitch sweep, charge glow, brief hitstop, or container rumble signaling an imminent reward.
  2. **Climax:** Proportionally scaled audio-visual reveal burst (respecting `<= 3 Hz` flash limits and `Reduced Motion`).
  3. **Immediate Playable Payoff:** Let players experience the reward immediately (new attack, synergy trigger, mobility boost). Never lock input behind unskippable `> 1.5s` celebration delays.

### 3-Tier Loop Cadence (Micro / Meso / Macro)
Maintain overlapping rhythm layers so a rewarding goal is always within reach:

- **Micro (`1–5s`):** Crisp hit confirmation, weak-point crits, parry sparks, pickup magnetism, rhythmic scoring ticks.
- **Meso (`1–5m`):** Room/wave clear, mini-boss chest, level-up draft, distinct power spike that visibly transforms combat or traversal.
- **Macro (`10–30m+`):** Permanent unlocks, new playable archetypes/modifiers, mastery milestones, or visible meta-progression surfaced between runs.
- **Replay for short sessions (class + home):** Where sessions are short (e.g. classroom), keep one run `~3 min` so a group fits `2–3` runs in `10–15 min`, and give home play long-term goals:
  - Collection cards per level, `3–4` explicit mastery challenges per level, and cosmetic unlocks on a visible ladder.
  - A randomized mode unlocked after the authored levels are complete.
  - A one-line "next goal" on every result screen naming the closest reachable reward.
  - Points as a secondary score with a visible breakdown (where each point came from). The Ethical Craft Boundary below still applies: no timers, energy, FOMO, or loot odds.

### Escalating Streaks, Meaningful Variance & "One More Run" Recovery
- **Musical pitch escalation:** Step SFX pitch upward along a clean musical scale (pentatonic or `+1` semitone per hit/pickup, capped at +1 octave) for consecutive combos/streaks, resolving with a satisfying bank/cash-out chime (with `>= 30–50ms` cooldown caps).
- **Transformative choices over spreadsheet math:** Prefer "Pick 1 of 3" upgrades or items that alter mechanics, trigger synergies, or create exciting risk-reward builds over dull "+2% attack" stat sticks.
- **Exciting variance with bad-luck protection:** Enable memorable high-roll moments paired with deterministic pity/floor guarantees so bad RNG never feels unfair.
- **Progress-forward end-of-run recap:** On defeat or clear, highlight tangible progress earned (unlocks advancing, personal best delta, build summary, clear cause of death) with Retry / Next Run pre-focused (`1` press, `<= 500ms`).
- **Ethical Craft Boundary (No Dark Patterns):** Hard bans on artificial wait timers, energy gates, pay-to-skip friction, deceptive odds, fake FOMO countdowns, and manipulative red notification dots.

---

## 7b. Learning Games: Knowledge as Power

In a learning game the lesson is a mechanic, not a quiz bolted between levels. Onboarding rules in §3 apply unchanged.

- **Knowledge-as-power:** A correctly stated rule unlocks a verb or power that makes play faster or more spectacular. E.g. after counting one group of edges by hand, stating the pattern paints all matching groups in one sweep.
- **Predict -> Verify loop:** Ask for a guess before the work (e.g. "how many edges?"), let the player do it, then confirm at the end with a stamp that shows the formula (e.g. `3 × 5 = 15`) next to the guess.
- **Questions at the moment of need:** Questions appear in the world where the knowledge is used, one at a time, with `3` choices. Never a question list or a separate test screen.
- **Cheap, teaching failure:** A wrong answer explains the underlying structure in `1` line and costs only the bonus, never progress, lives, or a forced replay.
- **Misconception-based distractors:** Each wrong choice reflects a real, common student error and carries its own one-line explanation of that error.
- **Transfer mode:** After the authored levels, randomize parameters beyond the textbook set (other sizes, counts, shapes) to test generalization, not memorization.
- **Educational correctness is a release gate:** Every displayed number, formula, label, and diagram is generated from the same model the rules use; no hand-typed or image-generated answers. Verify a sample of every level against an independent calculation before release.

---

## 8. Runtime Engineering, Telemetry & Data-Driven Balance

- **Fixed-timestep simulation + `dt` clamp:** Run physics/gameplay on a fixed timestep accumulator (e.g., `1/60s`) with render interpolation. Clamp raw frame `dt` (`<= 50ms`) so tab switches or lag spikes never cause tunneling or timer jumps. Use frame-rate independent exponential damping (`1 - Math.exp(-k * dt)`) for cameras and smooth HUD lerps.
- **Decouple HUD from the 60Hz loop:** Never trigger full DOM/React/UI layout re-renders every frame. Push HUD updates via dirty flags/events or throttle continuous bars/timers to `10–20Hz`.
- **Zero-GC hot loops:** Pool high-frequency entities (bullets, particles, floating numbers, audio voices) rather than allocating/destroying them inside `update()`/`render()`.
- **Deterministic seeds & balance telemetry:**
  - Support a reproducible run seed (`seed`) so bugs, physics edge cases, and unfair spawns can be replayed deterministically.
  - Instrument lightweight local dev telemetry (death positions/causes, damage taken per enemy, upgrade pick/win rates, time-to-clear per stage) and support headless/fast-forward balance simulations where genre-appropriate so tuning relies on evidence, not guesswork.
- **Save integrity & versioned schemas:** Separate manual saves from autosaves; never autosave during death or guaranteed-fail states; version all save payloads (`schemaVersion`) with atomic writes and safe migration fallbacks.
- **Low-end performance tier (web 3D on school/older laptops):** Default budgets per tier:

  | Budget | Normal | Low |
  |---|---|---|
  | DPR | `min(dpr, 2)` | `1` |
  | Full-screen post-processing | Off by default; fake glow with additive quads/thick lines | Off |
  | Shadows | One shadow map `<= 1024` | Blob shadows |
  | Draw calls / triangles | `<= ~100` / `<= ~100k` | Lower (e.g. `<= ~60` / `<= ~50k`) |
  | Textures | Procedural or atlas, `<= 1024 px` | Same, smaller where possible |
  | MSAA on render targets | Up to `4x` | Off (`1` sample) |

  - Merge static geometry. Step down a tier when p95 frame time over a `~2 s` window exceeds the budget; step up only after a longer calm window (hysteresis, e.g. `~10 s`). Always offer a manual `Low` toggle in Settings.
  - Set the step-up threshold above the display's frame interval. A 60 Hz display never reports less than `16.7 ms`, so a `< 16.6 ms` rule never steps up (e.g. use `<= 17.5 ms`). When a step-up fails within a short trial, block step-ups only for a limited time (e.g. `60 s`), not for the rest of the session.
  - Laptops without a usable GPU render in software, where fill rate dominates. MSAA is the first cost to drop (one measured scene went from `8.5` to `15–17` FPS with MSAA off). If that is not enough, add a governor-only floor below `Low` that renders the 3D view at `~0.75` internal resolution and keeps the DOM HUD at full resolution.
  - Lazy-load heavy assets (models, music, large textures) after the first input; the first interactive frame never waits for music.

---

## 9. Accessibility & Sensory Safety (XAG / WCAG / ESA 2026)

- **Multi-channel signaling:** Never convey vital state via color alone or sound alone—pair color with distinct shape/silhouette/pattern (verifying protanopia, deuteranopia, tritanopia legibility) and pair critical audio cues with directional visual cues or captions.
- **Complete subtitles & player-paced text:** Provide adjustable subtitle size, opaque/adjustable background scrim, speaker identification, and non-verbal SFX captions (e.g., `[발소리]`, `[경고음]`). Support instant text reveal and manual advance—never auto-dismiss critical instructions on an unpausable timer.
- **Photosensitivity hard safety (WCAG 2.3.1 / XAG 118):**
  - Never exceed **3 flashes per second** in any VFX, damage indicator, reward reveal, or UI animation.
  - Ban large-area (`>= 20%` viewport) high-contrast flashes and saturated red strobes; remove hazardous flashes by design rather than relying on warning splash screens.
  - Fine high-contrast patterns (ruled lines, stripes, text) under camera shake can count as flashes. Re-run the automated flash check after every texture contrast change (darker ruled lines on one page raised a shake from `3` to `4` flashes per second).
- **3-Tier Motion & Haptics Controls (XAG 117):**
  - Provide `Full / Reduced / None` motion settings (`Reduced` removes camera shake, wobble, rotation, parallax, and strobes while preserving semantic timing and readability; `None` eliminates decorative UI/background motion and locks subtitles still).
  - Provide a dedicated Haptics / Rumble toggle and intensity slider (`0–100%`).

---

## 10. AI Game Dev Anti-Slop Rules (Hard Bans)

Never introduce unless explicitly requested:

- **No polishing an unfun greybox:** Never pile shaders, bloom, particles, or meta-progression onto a core 30-second loop that has not proven fun on its own.
- **No SaaS / Web-App UI pasted onto games:** No generic glassmorphic Tailwind dashboard cards, neon purple/cyan AI gradients, or flat admin tables sitting over a game canvas. Match framing, typography, and motion to the game world.
- **No Frame-1 tutorial text walls:** Never dump 5+ mechanics in a launch modal. Teach one verb at a time in play (`<= 1` short prompt + active glyph).
- **No tutorial detours from the core flow:** No separate tutorial or practice mode, "learn first" gate, slideshow, or `Next`-button walkthrough that sits outside the real run, and no onboarding that freezes the whole game behind an overlay. The first run is the tutorial (§3 Teach Inside the Real Run).
- **No flat, silent reward loops or stat-stick filler:** Never ship progression that silently increments a tiny number with zero anticipation/payoff or offers only "+2%" spreadsheet boosts.
- **No unreadable combat chaos:** Never let particles, bloom, floating numbers, or busy floors obscure Tier 1 silhouettes or enemy telegraphs.
- **No missing game hygiene:** Always ship Title -> Play, Pause, Instant Restart, Settings (Audio/Controls/Accessibility), and Quit/Return to Title.
- **No FPS-coupled physics or mutated hitboxes:** Never tie speeds/timers to raw frame counts; never scale colliders during visual squash-and-stretch.
- **No dark patterns or stiff localization:** No fake timers or manipulative badges. Use natural, crisp Korean game copy (`게임 시작`, `이어하기`, `다시 시도`, `설정`, `일시정지`)—never stiff web-form prose or inconsistent tone.

---

## 11. Dual Verification Gate (Automated Runtime + Human Playtest)

Code compiling is not verification. Passing automated checks proves craft stability; passing human playtests proves award-worthy fun. Both gates are mandatory before release milestones.

### Part A — Automated Runtime & Visual Gate (Every UI / Feel / Scene Change)
Run via Playwright/headless browser (web games) or automated screenshot/test harness (native engines):

1. **Multi-Aspect & Downscale Capture:** Capture Title, Active Gameplay, Reward/Draft, Pause/Settings, and Game Over at `1920x1080` (16:9) and `1280x800` (16:10 Steam Deck) (plus mobile portrait/landscape if targeted). Confirm the smallest rendered glyph meets `>= 12px` target (`>= 9px` hard floor) at `1280x800` with zero clipped Korean text or overflow.
2. **Grayscale Silhouette & Identity Audit:** Inspect an active gameplay capture in grayscale:
   - Are Tier 1 entities and enemy telegraphs unmistakably separated from the backdrop?
   - Does every HUD label and subtitle stay crisp over the busiest frame?
3. **Frame-Pacing & `dt`-Spike Resilience:**
   - Zero console errors, uncaught exceptions, or missing asset warnings.
   - Verify `p95 <= 16.6ms` frame time (60fps target) during heavy combat, reward bursts, and HUD updates.
   - Simulate window blur / tab switch (`visibilitychange` / large `dt` gap) and verify clean auto-pause with zero physics tunneling or timer leaps.
   - **Low-end protocol (when a Low tier exists, §8):** `1366x768`, DPR `1`, CPU throttle `4x` (Chromium DevTools protocol), Low tier must hold `p95 <= 33 ms` (30 FPS floor). Record FPS, p95, draw calls, and triangles per scene in the verification notes. Run one pass with the GPU disabled (Chromium `--disable-gpu`, software rendering) to approximate a laptop without a usable GPU. Headless browsers cap at the display rate (`60 FPS`), so a `16.7 ms` p95 there means "at the cap", not "slow".
4. **Input, Reward & Safety Pass:**
   - Traverse Title -> Play -> Pause -> Settings -> Resume -> Restart -> Quit using only keyboard or gamepad (confirm initial focus, focus ring, focus restoration, cursor auto-hide, `Escape`/`B` back navigation, and `<= 500ms` 1-press retry).
   - Press every button with a mouse and with touch. A pressed state must not move the hit target: a button that jumps away under the pointer loses the click (on the web, put the press offset on the CSS `translate` property so a positioning `transform` stays).
   - Verify live glyph switching, audio Mute + Pause freeze, reward bursts free of `> 1.5s` unskippable lockouts, flash rate `<= 3/sec`, and `Reduced Motion` disabling shake.
5. **HUD Subtraction Audit:** Delete or fade any HUD counter, border, badge, or label that does not serve an immediate player decision, then re-capture to confirm.
6. **First-Run Onboarding Pass:** Start from a clean save and verify:
   - `Title -> Play` takes `1` press and lands in the real run, and the screens and presses before the first verb match a returning-player run.
   - At most one prompt is visible at a time. Each prompt clears only when the real verb is performed, and none needs a `Next`/`OK` press.
   - Input, animation, and audio stay live while prompts show.
   - Progress earned during onboarding is saved under the normal rules.
   - With an existing save, no first-run prompt appears.
7. **Scripted New-Player Run:** Passing tests does not prove fun or feel. (One build passed hundreds of tests and a thousand bot matches, yet a first-time player saw static tokens, toy sounds, no visible fight, and lost instantly.) Before calling a build playable, drive a clean-save run through the real UI and keep captures (frame strip or video) of the first `60 s`, showing core verbs, feedback, and audio in motion.

### Part B — Human Playtest & Fun Gate (Before Content Expansion & Release)
Automated tools cannot detect boredom, confusion, or emotional flatlines:

1. **Silent Observation Rule:** Put the build in front of real players and say nothing while they play. Record where they hesitate, misunderstand a telegraph, ignore a mechanic, frown, or smile.
2. **First-60-Seconds & First-Failure Audit:** Fix any friction in the opening minute (controls, goal clarity, visual hierarchy) and verify that the player's first death creates immediate curiosity ("I know how to beat that next try") rather than frustration. Also check that first-time players realize they are already playing, and do not wait for the game to finish explaining or look for a "start" after the tutorial.
3. **Independent Review Gate:** Before a build is called playable or released, a reviewer who did not build it (a different model or person, read-only) judges the Part A evidence and the first-`60 s` captures against this document. Each item passes only with cited evidence; missing evidence fails.
4. **Telemetry-Backed Iteration:** Combine silent playtest notes with logged telemetry (death hotspots, unused upgrades, dead-air stretches, drop-off points) and feed concrete, specific tuning tasks back into the build before adding new content.
