# DESIGN.md

Universal UI/UX, design token, modern web platform, and verification baseline. Every UI change must comply with this document before completion.

---

## 0. Project Overrides (Customize Per Repo)

- **Theme Mode:** `light` (switch to `light dark` with `light-dark()` pairs when enabling dual theme)
- **Neutral Tone:** Cool Apple Neutral (`#FFFFFF` / `#F5F5F7`) — swappable to Warm Paper (`#FAF9F6` / `#F2F0EB`)
- **Brand Accent:** `#0066CC` — swap `--accent` in Section 2 to re-theme globally (must keep `>= 4.5:1` as text on every neutral surface)
- **Primary Locale(s):** Korean + English (`ko`, `en`)
- **Audience:** 20s to 60s by default (`10 to 60+` for student-facing screens). Presbyopia starts around 40, so the type floors in Section 3 are not optional for adult tools
- **UI Stack:** React + TypeScript + Tailwind CSS v4 (bound to CSS custom properties) + native HTML first, owned headless primitives (`src/components/ui/`) second
- **Baseline Target:** Baseline Widely Available (progressive enhancement with `< 50`-line fallbacks for Newly Available APIs; no heavy polyfills)

---

## 1. Visual Philosophy & Platform-First Policy

- **Quiet chrome, content first:** Navigation, toolbars, inputs, and containers stay neutral and recede. User content and the single primary action own the screen.
- **Hierarchy through space and type:** Separate groups with generous whitespace and clear typographic scale first. Use `1px` hairline dividers only when spacing alone leaves ambiguity.
- **Restraint over decoration:** Every border, badge, icon, and sentence must earn its pixels. When in doubt, subtract.
- **Platform-First engineering:** Prefer native HTML/CSS capabilities (`<dialog>`, Popover API, CSS Anchor Positioning, `<details>`, `@container`, `subgrid`, `@starting-style`, `:user-invalid`, `field-sizing`) over JavaScript libraries. Use headless UI primitives (Radix / Base UI / shadcn) only when native elements cannot express the required interaction (e.g., comboboxes, multi-selects, complex data tables).
- **Easy to learn for ages 10 to 60+:** A first-time user, from a 10-year-old student to someone in their 60s, must finish the core task without help or a manual. Use plain everyday words (no jargon, abbreviations, or internal codes), visible text labels on actions (icon-only only for universal icons such as close and search), familiar platform patterns over novel gestures, and one primary task per screen. Never hide required actions behind hover, long-press, or swipe alone. Prefer undo over confirmation dialogs, and make every error state the next step.
- **Emphasis people can find:** The single primary action is a filled, labeled button of at least `48px` height whose fill contrasts `>= 3:1` with its surroundings. Size, color, and containment beat subtlety for findability: in Google's 46-study program (18,000+ participants, self-reported), users over 45 located key controls as fast as younger users when the key action was larger and clearly contained, and usability dropped when text labels were removed or familiar patterns were broken.
- **Premium through finish, not decoration:** A view looks expensive when nothing is off. Check every view against this list instead of adding ornament:
  - One accent hue, one neutral ramp, at most two font weights per text block, at most three type sizes per region.
  - One *object surface* per view (document, board, book, canvas) may carry a soft two-layer shadow; everything else is flat and separated by space.
  - Optical alignment: text baselines, icon centers, and edges line up across regions; concentric radii everywhere.
  - Real content, real imagery at `>= 2x` density with intentional crops. No stock gradients, no placeholder art.
  - Display serif (for example Noto Serif KR) is allowed for at most one title per view; body and controls stay sans.
  - Motion is short, spring-like, and tied to a cause; nothing moves on its own.
- **Solid surfaces under text:** Never place text on translucent or blurred material except scrolled sticky chrome. Apple itself shipped an opaque "Tinted" option after Liquid Glass legibility complaints (iOS 26.1).
- **Icons:** Lucide or equivalent single-weight line icons at uniform `1.5px` stroke (`1rem` inline, `1.25rem` toolbar/nav). Do not pair icons with every label when text alone is clearer.

---

## 2. Semantic Color & Native Browser Theming

Never hardcode hex values or raw palette utilities (`bg-indigo-500`, `text-slate-600`) inside components. Reference semantic tokens only.

```css
:root {
  /* Align native scrollbars, form controls, autofill, and initial canvas */
  color-scheme: light;

  /* 1. Surface Hierarchy (3-step neutral ramp) */
  --bg-canvas: #FFFFFF;
  --bg-subtle: #F5F5F7;
  --bg-elevated: #FFFFFF;
  --bg-chrome: color-mix(in oklab, var(--bg-canvas) 78%, transparent);

  /* 2. Text Hierarchy (WCAG AA+ contrast) */
  --text-primary: #1D1D1F;     /* Headings, primary content (> 16:1) */
  --text-secondary: #57575C;   /* Metadata, supporting labels (> 6:1) */
  --text-tertiary: #66666B;    /* Captions, placeholders (> 4.9:1 on every neutral surface) */
  --text-on-accent: #FFFFFF;

  /* 3. State Tints & Hairlines
     RULE: Always use `in oklab` (NOT `in oklch`) when mixing neutrals, white,
     black, or `transparent` to prevent low-chroma hue shifts toward red. */
  --bg-hover: color-mix(in oklab, var(--text-primary) 5%, transparent);
  --bg-active: color-mix(in oklab, var(--text-primary) 9%, transparent);
  --border-subtle: color-mix(in oklab, var(--text-primary) 10%, transparent);
  --border-strong: color-mix(in oklab, var(--text-primary) 20%, transparent);
  --border-control: color-mix(in oklab, var(--text-primary) 52%, transparent); /* input outlines: >= 3:1 */

  /* 4. Swappable Brand Accent (Primary CTA, Active Nav, Focus Ring ONLY) */
  --accent: #0066CC;
  --accent-hover: color-mix(in oklab, var(--accent) 88%, black);
  --accent-soft: color-mix(in oklab, var(--accent) 10%, transparent);
  --focus-ring: color-mix(in oklab, var(--accent) 48%, transparent);

  /* 5. Semantic Status (Validation, feedback, alerts ONLY) */
  --status-destructive: #C80013;
  --status-destructive-soft: color-mix(in oklab, var(--status-destructive) 9%, transparent);
  --status-success: #18753A;
  --status-success-soft: color-mix(in oklab, var(--status-success) 9%, transparent);
  --status-warning: #9E4F08;
  --status-warning-soft: color-mix(in oklab, var(--status-warning) 11%, transparent);

  /* 6. Bounded Categorical Palette (Charts, user tags, categories ONLY) */
  --cat-blue-fg: #005BB5;   --cat-blue-bg: color-mix(in oklab, var(--cat-blue-fg) 10%, transparent);
  --cat-green-fg: #137333;  --cat-green-bg: color-mix(in oklab, var(--cat-green-fg) 10%, transparent);
  --cat-amber-fg: #9A4E04;  --cat-amber-bg: color-mix(in oklab, var(--cat-amber-fg) 12%, transparent);
  --cat-purple-fg: #7B329E; --cat-purple-bg: color-mix(in oklab, var(--cat-purple-fg) 10%, transparent);
  --cat-rose-fg: #B80012;   --cat-rose-bg: color-mix(in oklab, var(--cat-rose-fg) 9%, transparent);
  --cat-teal-fg: #0F766E;   --cat-teal-bg: color-mix(in oklab, var(--cat-teal-fg) 10%, transparent);

  /* 7. Elevation (Floating layers and the single object surface ONLY; never on static inline cards) */
  --shadow-popover: 0 4px 16px rgba(0, 0, 0, 0.08), 0 0 0 1px var(--border-subtle);
  --shadow-modal: 0 16px 40px rgba(0, 0, 0, 0.14), 0 0 0 1px var(--border-subtle);
  --shadow-object: 0 1px 1px rgba(0, 0, 0, 0.05), 0 24px 60px -22px rgba(0, 0, 0, 0.34); /* the one document/board/book surface */

  /* 8. Concentric Radii (inner radius = outer radius - padding) */
  --radius-sm: 0.5rem;    /* 8px: buttons, inputs, segmented controls */
  --radius-md: 0.75rem;   /* 12px: grouped panels, menus */
  --radius-lg: 1rem;      /* 16px: modals, sheets */
  --radius-pill: 9999px;

  /* 9. Motion */
  --ease-apple: cubic-bezier(0.25, 0.1, 0.25, 1);
  --ease-spring: cubic-bezier(0.22, 1, 0.36, 1);
  --duration-fast: 150ms;
  --duration-base: 220ms;

  /* Native Browser UI Alignment */
  accent-color: var(--accent);
  scrollbar-color: var(--text-tertiary) transparent;
}

::selection {
  background-color: var(--accent-soft);
  color: var(--text-primary);
}
```

### Color & Ergonomics Governance
- **Contrast floor:** All text, including captions, placeholders, and text on tinted backgrounds, is `>= 4.5:1`; aim for `>= 7:1` on body copy. Input outlines, icons, and state indicators are `>= 3:1`. Status is never color alone: add a word or a distinct shape.
- **One primary interactive accent per viewport:** Only one button per view or dialog uses `--accent` fill. Secondary actions use `--bg-subtle` fill or borderless text.
- **Color carries meaning:** Categorical tokens (`--cat-*`) are strictly for structured tags, categories, and data series—never for decorative card backgrounds or competing CTAs.
- **Respect native browser ergonomics:**
  - Never apply `user-select: none` to content text (limit to drag handles and non-text chrome).
  - Never hide scrollbars (`scrollbar-width: none`) on scrollable regions unless another visible control fully replaces scrolling; keep scrollbar thumb contrast `>= 3:1`.
  - When supporting dark mode, use `color-scheme: light dark` and CSS `light-dark()` token pairs following OS preference by default.

---

## 3. Accessible Bilingual Typography (Korean + English)

### Font Stacks
- **UI Sans:** `"Pretendard Variable", Pretendard, -apple-system, BlinkMacSystemFont, "Apple SD Gothic Neo", "Segoe UI", sans-serif`
- **Monospace:** `"SF Mono", "JetBrains Mono", ui-monospace, Menlo, monospace`

### Units, Wrapping & Hangul Rules (Mandatory)
- **Never use `px` for `font-size`:** Use `rem` (and `clamp(rem + vw)` for fluid headings) so browser font preferences and 200% zoom work without breaking layout.
- **Readable line measure:** Cap body prose blocks at `65ch` to `75ch` (`max-inline-size`). Avoid `text-align: justify`.
- **Hangul line breaking:** Apply `word-break: keep-all; overflow-wrap: break-word;` on headings, buttons, navigation, and short UI paragraphs so Korean words do not split mid-word while long URLs still wrap safely.
- **Optical wrapping:** Apply `text-wrap: balance` on unboxed headings (avoid on bordered/filled boxes where it creates empty right-edge voids) and `text-wrap: pretty` on body copy.
- **Hangul rhythm:** Keep body line-height `>= 1.55`. Never use `italic` for Korean emphasis; use `font-weight: 600` instead.
- **Tabular numerals:** Apply `font-variant-numeric: tabular-nums;` to counts, prices, dates, timers, and table columns.

### Type Scale (`1rem = 16px` root default)
| Role | Size | Weight | Line Height | Tracking | Usage |
|---|---|---|---|---|---|
| Page Title | `clamp(1.625rem, 1.45rem + 0.7vw, 2rem)` | 700 | 1.28 | -0.025em | Top-level view header (max 1 per view) |
| Section Title | `1.25rem` | 700 | 1.35 | -0.02em | Major section, panel, or dialog title |
| Group Title | `1.125rem` | 600 | 1.45 | -0.015em | Sub-headings, grouped list headers |
| Body | `1.0625rem` (17px) | 400 | 1.60 | -0.01em | Primary UI prose and descriptions |
| Control / Label | `1rem` (16px) | 600 | 1.40 | -0.01em | Buttons, inputs, tabs, nav items |
| Meta | `0.9375rem` (15px) | 400–500 | 1.50 | 0 | Timestamps, helper text |
| Floor | `0.875rem` (14px) | 500–600 | 1.45 | 0 | Badges and dense counters only; nothing is smaller |

- **Floors:** body `>= 16px` with 17px as the default, line-height `>= 150%` (Korea Design System, KRDS typography). Nothing user-facing is under 14px.
- **Bigger is not endlessly better:** let people scale text (browser zoom, root font size, an in-app size control for reading surfaces) instead of hardcoding very large copy. A review of 12 studies on older readers found a critical size above which reading slows again.
- **Reading and teaching surfaces** (documents, boards, e-books): `>= 1.125rem`, and prefer scrolling over shrinking below that.

---

## 4. Modern Layout, Spacing & Responsiveness

- **Spacing rhythm:** Strict `0.5rem` (`8px`) grid (`0.5, 1, 1.5, 2, 3, 4rem`). Use `0.25rem` (`4px`) only for tight icon-to-label pairing.
- **Logical properties:** Prefer `padding-inline`, `margin-block`, `max-inline-size`, and `inset-block-end` over physical directional properties.
- **Container widths:**
  - Focused reading, settings, forms: `42.5rem` to `47.5rem` (`680px–760px`)
  - Main workspace: `70rem` to `75rem` (`1120px–1200px`) with `1.5rem` desktop / `1rem` mobile inline padding
- **Component-level responsiveness:** Wrap reusable cards, toolbars, list rows, and panels in `container-type: inline-size` and adapt with `@container` queries so they work inside sidebars and split panes. Reserve viewport media queries (`40em`, `64em`) for macro page shells.
- **Cross-card alignment:** Use CSS `subgrid` when adjacent cards or grouped rows must share header/footer baselines.
- **Media stability:** Reserve explicit `aspect-ratio` (or intrinsic `width`/`height`) on all images, videos, thumbnails, and embeds to prevent layout shift.
- **Mobile viewport & safe areas:** Use `100dvh` / `100svh` (never `100vh`) for full-height shells and `env(safe-area-inset-bottom)` for sticky mobile bars.
- **Interactive targets:** `44px x 44px` minimum on every pointer type (laptops in classrooms and offices are touch screens too), `48px` for the primary action and inputs, `>= 4px` between adjacent targets. WCAG 2.2 AA allows 24px; this baseline does not.
- **Every drag, pinch, and slider has a tap path:** steppers, buttons, or a menu (WCAG 2.5.7).
- **Text-size-aware breakpoints:** write width media and container queries in `em`, so a 200% text setting gets the narrow layout instead of overflowing.
- **Translucent sticky chrome:** Apply `--bg-chrome` + `backdrop-filter: saturate(180%) blur(16px)` + `1px solid var(--border-subtle)` strictly on scrolled sticky navigation bars.

---

## 5. Forms, Korean IME Safety, States & Motion

### Form & Input Rules (Mandatory)
- **IME-safe `Enter` handling (Critical for Korean):** Every custom `Enter`-to-submit handler (chat, search, inline edit) MUST guard with `if (event.isComposing || event.keyCode === 229) return;` so Hangul syllable confirmation never double-submits or cuts characters. Keep a native `<button type="submit">`.
- **Non-hostile validation:** Style invalid/valid states with `:user-invalid` / `:user-valid` (never `:invalid` / `:valid`) so untouched required fields never flash red on load. Exclude range sliders (`input:not([type="range"]):user-valid`).
- **Native ergonomics:** Associate every control with a `<label>`, supply accurate `autocomplete` attributes, use `resize: vertical` on textareas, and use `field-sizing: content` (bounded with `min-inline-size` / `max-inline-size`) for auto-growing inputs.

### Required UI States
Every screen and interactive component must explicitly implement:
1. **Default:** Realistic Korean/English sample copy (never Lorem Ipsum).
2. **Hover & Active:** Subtle neutral tint (`--bg-hover`, `--bg-active`). No scale-up bounce on cards.
3. **Keyboard Focus (`:focus-visible`):** `2px` `--focus-ring` with `2px` offset. Never strip focus outlines without a visible replacement.
4. **Loading:** Geometry-matched `--bg-subtle` skeletons with zero layout shift.
5. **Empty:** One clear sentence + one direct primary CTA. No generic clip art.
6. **Error:** Inline `--status-destructive` message stating what failed and the exact recovery action.
7. **Overflow:** Long Korean titles and unbroken English strings/URLs truncate or clamp cleanly without horizontal scroll.

### Editing Surfaces (Documents, Slides, Questions, Boards)
Applies to any screen where people change content. Derived from a 2026-11 review of Wayground, Kahoot!, Canva, Google Docs, Microsoft Learning Zone, Notion, Gamma, and classroom tools.

1. **Same place, same size.** Switching between using and editing moves nothing and shrinks nothing: 0px shift of the content, text size ratio `1.0`, `CLS 0`.
2. **One tap to the thing.** Tap text and type. No separate "start editing text" command, no double-click-only path.
3. **Tools follow the selection, in one fixed place.** Show only the commands that work on the current selection, with text labels, in a bar that never moves and never covers the content. Never show a wall of disabled buttons (more than 8 commands at once is a defect). Do not make floating icon strips, slash commands, or hover handles the only path.
4. **Save by itself, prove it, and let people go back.** Autosave with a quiet "Saved" state, undo/redo always visible, and a history list that names each change and restores any point. The original source is never overwritten; offer a before/after comparison at the same scale.
5. **AI proposes, people accept.** Show the proposal in place, mark exactly what changes, put before and after side by side, and apply only on an explicit button as one undo step. Recompute anything computable in code; never treat the model's confidence as proof. Never overwrite a person's edit.
6. **Mark what nobody has checked.** Unverified AI output and failed checks get a word plus a color, a count in the toolbar, and a one-tap fix. Unresolved errors block publishing, not editing.
7. **Preview as the receiver.** A live, usable preview of what the student, reader, or customer gets, next to the editor.
8. **Details stay closed.** Numeric position, layering, and metadata live behind a named disclosure.

### Motion & Performance Budgets
- **Top-layer entry/exit (`<dialog>`, `[popover]`):** Animate natively using `@starting-style` (declared *after* the open selector), `transition-behavior: allow-discrete` (declared *after* `transition` shorthand), and `overlay`.
- **Smooth transitions:** Prefer CSS transitions (`transform`, `opacity`), CSS Anchor Positioning for sliding indicators, and scoped View Transitions (`startViewTransition()`) over heavy JS animation libraries.
- **Reduced motion:** Under `@media (prefers-reduced-motion: reduce)`, remove spatial movement (`transform: none`) and shorten transitions to a calm `~100ms` opacity fade.
- **Core Web Vitals budgets:** **INP < 200ms** (yield heavy main-thread work via `scheduler.yield()`), **CLS < 0.05**, **LCP < 2.5s**.

---

## 6. Anti-Slop Rules (Hard Bans)

Never introduce unless explicitly requested:
- **No default AI gradients:** No purple/indigo gradients, glowing radial meshes, or neon accents.
- **No box-inside-a-box nesting:** Use grouped rows separated by `1px` inset hairlines on `--bg-subtle` or `--bg-canvas` instead of bordered cards inside bordered cards.
- **No decorative KPI grids:** Prefer a single clean summary strip or list when 4-column stat cards add visual clutter.
- **No glassmorphism on content:** Reserve `backdrop-filter` strictly for sticky floating chrome, command palettes, and popovers.
- **No tool walls:** A toolbar where most buttons are disabled, or that takes more than two rows, is a layout failure.
- **No glass under text:** See Section 1, *Solid surfaces under text*.
- **No chat box as the default AI entry:** Offer named actions on the selected thing first; free text is secondary.
- **No redundant chrome:** No icons beside every heading/menu item; no decorative pill badges for plain metadata.
- **No marketing fluff or tone drift:** No exclamation marks or hype greetings in product UI. Keep Korean copy consistent per view (해요체 or concise noun labels—never mix 반말/존댓말 or inconsistent Korean/English nav labels).

---

## 7. External Verification Gate (Mandatory Before Completion)

Code compiling is not verification. Before reporting any UI task complete, verify rendered output in a real browser (Playwright CLI / Playwright MCP / Chrome DevTools):

1. **Multi-Viewport & Zoom Capture:** Verify affected routes at `1440x900` (desktop), `390x844` (mobile), and **200% browser text zoom** with zero clipped text and zero horizontal overflow (`scrollWidth <= innerWidth`).
2. **State & Top-Layer Capture:** Screenshot default, empty, loading/error (when applicable), and open `<dialog>` / `[popover]` states.
3. **Interactive & Korean IME Checks:**
   - Tab through all controls: visible `:focus-visible`, logical tab order, `Escape` closes overlays and restores focus to the trigger.
   - Verify custom `Enter`-to-submit inputs ignore `isComposing` / `keyCode 229`.
   - Confirm untouched required inputs show zero premature error styling.
4. **Readability Checks (20s to 60s):**
   - Computed `font-size`: body `>= 16px`, nothing `< 14px`; line-height of body `>= 1.5`.
   - Contrast `>= 4.5:1` for all text in every state, including placeholders, disabled explanations, and text on tints.
   - All interactive elements `>= 44px` on desktop *and* mobile, primary action `>= 48px`.
   - `720x450` (equals 200% browser zoom at 1440x900) and a 32px default font size: no horizontal overflow, no clipped text, primary action reachable.
   - Editing surfaces: use/edit switch shifts content 0px and keeps the text size.
5. **Automated Defect Checks:**
   - Zero console or uncaught page errors.
   - Zero critical/serious axe-core accessibility violations and **CLS < 0.05**.
6. **Ease-of-Learning Walkthrough:** Walk the core task as a first-time user (a 10-year-old student and a user in their 60s): every action has a visible text label, no step depends on hover/long-press/swipe alone, no jargon appears, and every error names the next action.
7. **Visual Subtraction Audit:** Inspect actual rendered screenshots. Remove competing focal points, redundant borders, broken `8px` spacing, or awkward Korean wraps, then re-screenshot to confirm.

---

## 8. Evidence

- KRDS typography (body 17px, minimum 16px, line-height 150%): https://www.krds.go.kr/html/site/style/style_03.html
- Google, expressive design research (46 studies, 18,000+ participants; vendor-reported): https://design.google/library/expressive-material-design-google-research
- Font size for older adults, systematic review (12 papers, 2022): https://pmc.ncbi.nlm.nih.gov/articles/PMC9376262/
- WCAG 2.2 target size and dragging: https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/
- Liquid Glass legibility and the Tinted option: https://tidbits.com/2025/10/21/ios-26-1-to-add-optional-opacity-to-liquid-glass/
- Teachers editing AI output: partial edits, inline comparison, accept/reject one by one (13 co-designers, 15 in the comparison study): https://arxiv.org/html/2602.22186v1
- Question type switching and per-question AI actions: https://help.wayground.com/support/solutions/articles/158000405019-edit-questions
- AI suggestions as tracked changes: https://support.google.com/docs/answer/13447609
