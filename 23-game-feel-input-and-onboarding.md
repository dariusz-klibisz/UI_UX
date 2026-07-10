# Game Feel, Input & Onboarding

> Part of the [UI/UX Reference](00-index.md). See index for the full documentation map.

## Scope

This file covers game-specific UX concerns not centered on a particular
screen: "juice"/game feel (hit-flash, screenshake, reward-moment feedback),
gamepad/controller navigation and remapping, progressive tutorialization for
deep systems, game-specific accessibility (colorblind-safe rarity/element
coding, damage-type iconography, text scaling for dense stats, reduced motion
for combat/idle VFX), and game localization workflow. Screen-level game UI
patterns (HUD, tooltips, inventory, formation editor, the Tactics/automation
editor) are in
[22-game-ui-and-hud.md](22-game-ui-and-hud.md). General motion, gesture, and
keyboard-interaction foundations are in
[04-interaction-design.md](04-interaction-design.md); general accessibility in
[05-accessibility.md](05-accessibility.md); general i18n/l10n in
[09-content-ux-writing.md](09-content-ux-writing.md).

## Contents

- [Game feel and juice](#game-feel-and-juice)
- [Reduced motion for combat and idle VFX](#reduced-motion-for-combat-and-idle-vfx)
- [Gamepad and controller navigation](#gamepad-and-controller-navigation)
- [Input remapping](#input-remapping)
- [Progressive tutorialization for deep systems](#progressive-tutorialization-for-deep-systems)
- [Colorblind-safe rarity and element coding](#colorblind-safe-rarity-and-element-coding)
- [Text scaling for dense stat displays](#text-scaling-for-dense-stat-displays)
- [Game localization](#game-localization)
- [Quick reference](#quick-reference)

---

## Game feel and juice

**Definition:** The layered, often-subliminal feedback — hit-flash/tint on
impact, brief screenshake, particle bursts, easing/overshoot on reward
moments (level-up, loot-drop, crit) — that makes actions feel impactful
independent of their functional outcome. Coined "juice" in game-design
practice; distinct from and additive to the functional feedback covered in
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#floating-combat-text).

**Reasoning / Evidence:** Juice is a direct extension of the microinteractions
principle ([04-interaction-design.md](04-interaction-design.md#microinteractions))
and the Peak-End rule ([01-core-principles.md](01-core-principles.md)) applied
to combat and reward moments specifically: players' *memory* of a play session
is disproportionately shaped by its peaks (a big crit, a legendary drop) and
its end, so those specific moments deserve deliberately amplified feedback even
though the underlying game-state change is "just a number." This is explicitly
named as a design pillar in this project's own feature spec ("stylized/
iconographic units with juicy UI feedback").

**When to use:**
- Impact feedback: brief hit-flash/tint on the target, a small screenshake
  scaled to the hit's significance (bigger for a crit or a killing blow than a
  routine hit) — proportional feedback lets big moments read as big without
  every hit feeling equally weighty (which would itself flatten the feeling of
  impact).
- Reward moments: level-up, rarity-tier loot drops (especially legendary/set
  items), ascension, and prestige/rebirth deserve their own distinct,
  amplified feedback sequence (motion + sound + a brief pause/beat) —
  proportional to the rarity/significance of what happened, per the item-
  chase design's own rarity ladder.
- Idle-state ambient feedback: subtle, low-intensity motion (a slow pulse,
  gentle particle drift) on the HUD/scene during AFK auto-combat so the
  screen reads as "alive," without competing for attention with actual combat
  feedback.

**When NOT to use / exceptions:**
- Never let juice obscure or delay the functional information it's attached
  to — a screenshake or flash that makes damage numbers harder to read
  defeats [22](22-game-ui-and-hud.md#floating-combat-text)'s legibility goal;
  juice augments comprehension, it doesn't compete with it.
- Scale down or disable juice at high time-multiplier/fast-forward combat
  speeds — full-intensity screenshake at 8x speed compounds into
  motion-sickness risk and visual noise, not amplified feeling.
- Always provide an intensity/off control (see
  [reduced motion](#reduced-motion-for-combat-and-idle-vfx)) — juice is an
  enhancement, and WCAG 2.3.3 (motion-actuation-adjacent) plus general
  vestibular-disorder accommodation both require an off-ramp for players
  sensitive to screen motion.

**Pros:** Meaningfully increases perceived game quality and reward
satisfaction for a comparatively low implementation cost per effect; directly
serves this project's own "juicy UI feedback" design pillar.
**Cons:** Compounds into sensory overload at high combat-speed multipliers or
dense multi-unit fights if not scaled/throttled; a genuine accessibility
concern for motion-sensitive/vestibular-disorder players if no reduction path
exists.

**Implementation guidance:**
- Tier feedback intensity explicitly to event significance (routine hit <
  crit < kill < rare-loot-drop < level-up < legendary-drop) rather than a flat
  intensity for every event — this is what makes rare moments *feel* rare.
- Scale or suppress screenshake/flash-frequency as a function of active
  combat speed multiplier — define an explicit intensity curve (e.g. full
  intensity at 1x, reduced at 2x, minimal/off above a threshold) rather than
  leaving it unscaled.
- Cap simultaneous juice instances the same way floating combat text is
  capped ([22](22-game-ui-and-hud.md#floating-combat-text)) — many
  simultaneous hits in a full-squad fight should not stack many simultaneous
  screenshakes into one nauseating compound effect.
- Reward-moment sequences (level-up, legendary drop) may briefly slow or
  pause auto-combat progression for emphasis, but must be skippable/
  interruptible — never force a non-skippable animation to block an
  otherwise-idle game's core promise of low time cost.

**Verification checklist:**
- [ ] Feedback intensity is explicitly tiered to event significance, not flat.
- [ ] Screenshake/flash intensity and frequency scale down at higher combat-
      speed multipliers, with a defined curve.
- [ ] A global juice-intensity or reduced-motion setting exists and is
      respected everywhere juice appears, not just in one screen.
- [ ] Reward-moment sequences are skippable/interruptible, never a forced
      block on play.
- [ ] Simultaneous juice instances are capped during dense multi-unit combat.

**Related:** [04-interaction-design.md](04-interaction-design.md#microinteractions),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#floating-combat-text),
[#reduced-motion-for-combat-and-idle-vfx](#reduced-motion-for-combat-and-idle-vfx).

**Sources:** [04-interaction-design.md](04-interaction-design.md#microinteractions)
(Saffer, *Microinteractions*), general "juice" game-design literature
(the widely-cited "Juice it or lose it" GDC talk by Martin Jonasson & Petri
Purho is the originating popular reference for this concept).

---

## Reduced motion for combat and idle VFX

**Definition:** A setting (ideally honoring the OS-level `prefers-reduced-
motion` signal where the platform exposes it, plus an explicit in-game
override) that suppresses or substantially reduces screenshake, camera
motion, and high-frequency particle/flash effects, distinct from disabling
functional feedback (damage numbers, status icons) entirely.

**Reasoning / Evidence:** This extends the general reduced-motion doctrine
([05-accessibility.md](05-accessibility.md#reduced-motion)) to a genre where
motion-heavy feedback (screenshake especially) is a deliberate, frequent
design element rather than an occasional transition — meaning the
vestibular-disorder/motion-sensitivity risk this accommodates is
correspondingly higher-frequency in a real-time combat game than in a typical
business-app context. This is squarely a case where accessibility must win
over visual-style ambition per the index's conflict-resolution rules
([00-index.md](00-index.md#conflict-resolution-rules)).

**When to use:**
- Always ship a reduced-motion path for: screenshake, camera punch/zoom,
  high-frequency flashing (especially anything that could approach
  photosensitive-seizure-trigger frequency — see WCAG 2.3.1's 3-flashes-per-
  second threshold, a hard ceiling regardless of any setting), and rapid
  particle bursts.
- Honor the platform's OS-level reduced-motion signal as the default on
  first launch where the platform exposes one, with an explicit in-game
  toggle so the player isn't locked to a system-wide setting that may also
  affect unrelated apps.

**When NOT to use / exceptions:**
- Reduced motion should not disable *informational* feedback (damage numbers,
  status-effect icons, health-bar changes) — only the purely-decorative
  motion/shake layer. A player who reduces motion still needs full combat
  legibility.

**Pros:** Makes the game playable for vestibular-disorder/motion-sensitive
players without removing the enhanced experience for everyone else.
**Cons:** Requires every juice effect to be authored with a defined
reduced-motion variant, not just an on/off flag bolted on afterward — worth
building into the effect system from the start rather than retrofitting.

**Implementation guidance:**
- Never exceed 3 flashes per second for any effect at any settings level —
  this is a hard photosensitive-safety ceiling (WCAG 2.3.1), not a
  reduced-motion-only concern.
- Reduced-motion mode: replace screenshake with a static or minimal-motion
  alternative (a brief flash/tint instead of camera displacement); cap
  particle-effect density; keep transition/menu animations brief and
  non-parallax.
- Surface the setting prominently in the initial settings/onboarding flow,
  not buried several menu levels deep, given how central motion-heavy
  feedback is to this genre's default experience.

**Verification checklist:**
- [ ] No effect at any setting exceeds 3 flashes/second (WCAG 2.3.1, hard
      ceiling).
- [ ] Reduced-motion mode suppresses decorative motion while preserving all
      informational feedback (damage numbers, status icons, bars).
- [ ] The setting is honored consistently across every screen with combat
      VFX, not just the primary combat view.
- [ ] OS-level reduced-motion preference is honored as the default where the
      platform exposes one.

**Related:** [05-accessibility.md](05-accessibility.md#reduced-motion),
[#game-feel-and-juice](#game-feel-and-juice).

**Sources:** [WCAG 2.3.1: Three Flashes or Below Threshold](https://www.w3.org/WAI/WCAG22/Understanding/three-flashes-or-below-threshold.html),
[05-accessibility.md](05-accessibility.md#reduced-motion).

---

## Gamepad and controller navigation

**Definition:** Full game navigability via gamepad/controller — d-pad or
analog-stick-driven **focus-neighbor navigation** through menus, inventory
grids, and the combat-formation grid, with a visible focus indicator and
predictable spatial movement, as an equal alternative to mouse/touch.

**Reasoning / Evidence:** This extends the general keyboard-navigation
doctrine ([04-interaction-design.md](04-interaction-design.md#keyboard-interaction-patterns),
[05-accessibility.md](05-accessibility.md#keyboard-navigation)) to
directional-input devices specifically. Godot (and most engines) implement
this via explicit **focus-neighbor** graphs on UI controls — each focusable
element declares its up/down/left/right neighbor — rather than relying on
DOM/tree order the way web tab-order does; a dense inventory/formation grid
UI must have this graph deliberately authored, or gamepad focus jumps
unpredictably between visually-adjacent elements.

**When to use:**
- Every screen reachable by mouse/touch must be fully operable by gamepad:
  menus, inventory, formation editor, the Tactics/automation rule editor
  (which otherwise leans heavily on drag/hover — see
  [22](22-game-ui-and-hud.md#rule-based-automation-editor-tacticsgambit-pattern)),
  and any drag-and-drop interaction (which needs its non-drag path per
  [22](22-game-ui-and-hud.md#drag-and-drop-inventory)).
- Grid-shaped UI (inventory, formation grid): focus-neighbor graph should
  match the *visual* grid layout exactly — up/down/left/right move to the
  visually adjacent cell, not to whatever the next element happens to be in
  scene-tree/DOM order.
- A persistently visible, high-contrast focus indicator at all times when
  gamepad input is active (distinct from a mouse-hover state, which shouldn't
  show the same indicator) — this is the gamepad equivalent of
  [05-accessibility.md](05-accessibility.md#focus-visible).

**When NOT to use / exceptions:**
- None — full gamepad operability is treated as a baseline requirement for a
  PC-targeted game with deep menu systems, not an optional nice-to-have,
  given how much of this game's core loop lives in menu-driven screens
  (itemization, character development) rather than direct action.

**Pros:** Serves controller-preference PC players directly and, as a side
effect, tends to also validate/improve keyboard-only and switch-device
accessibility paths, since all three share the same directional/discrete-
input navigation model.
**Cons:** Authoring an explicit, correct focus-neighbor graph for every dense
grid screen is real, screen-by-screen work that must be maintained as layouts
change — an easy thing to silently regress when a screen's layout is edited
without updating its focus graph.

**Implementation guidance:**
- Author focus-neighbor relationships explicitly for every grid/dense screen
  rather than relying on automatic/inferred focus order, which frequently
  guesses wrong on non-uniform grids (a formation grid with gaps, an
  inventory grid with a header row).
- Provide a stick-drag/d-pad-hold "fast scan" behavior for large grids
  (inventory with many items) so reaching a distant cell doesn't require
  dozens of discrete presses — mirrors the general efficiency-for-experts
  principle ([00-index.md](00-index.md#conflict-resolution-rules), efficiency
  vs learnability) applied to directional input.
- Every drag-and-drop and hover-dependent interaction identified in
  [22-game-ui-and-hud.md](22-game-ui-and-hud.md) needs its gamepad path
  explicitly designed as part of that feature, not bolted on after the
  mouse/touch version ships.
- Map a consistent "back/cancel" and "context menu/inspect" button across
  every screen — inconsistent button mapping between screens is one of the
  most common gamepad-UX complaints in menu-heavy games.

**Verification checklist:**
- [ ] Every screen reachable by mouse/touch is fully operable by gamepad,
      including drag-and-drop-driven interactions via their non-drag path.
- [ ] Focus-neighbor graphs on grid screens match the visual layout exactly
      (tested by navigating the full grid boundary-to-boundary).
- [ ] A visible, high-contrast focus indicator is always shown during
      gamepad input, distinct from mouse-hover styling.
- [ ] Back/cancel and inspect/context-menu buttons are mapped consistently
      across every screen.
- [ ] Large grids support a fast-scan/fast-scroll gamepad interaction, not
      only single-cell-at-a-time movement.

**Related:** [04-interaction-design.md](04-interaction-design.md#keyboard-interaction-patterns),
[05-accessibility.md](05-accessibility.md#keyboard-navigation),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#drag-and-drop-inventory).

**Sources:** [05-accessibility.md](05-accessibility.md#keyboard-navigation)
(the general keyboard-operability requirement this extends), Godot Engine
"GUI focus" documentation for the concrete focus-neighbor mechanism this
guidance assumes.

---

## Input remapping

**Definition:** Player-configurable key/button bindings for every
keyboard/gamepad-driven action, with sensible platform-appropriate defaults
and conflict detection.

**Reasoning / Evidence:** Extends the general customization/efficiency-for-
experts principle: fixed bindings that conflict with a player's existing
muscle memory, accessibility hardware, or regional keyboard layout (a
QWERTY-assumed binding on an AZERTY keyboard) create real barriers that
remapping directly removes. This matters proportionally more for a
manual-skill-triggering-optional game (per this project's control-spectrum
design) since the *set* of bindable actions is deliberately small — making
full, well-organized remapping cheap to support well.

**When to use:**
- Every discrete input action (manual skill trigger, pause/speed-toggle,
  menu navigation, formation-editor actions) should be individually
  rebindable, not bundled into an all-or-nothing preset swap.
- Provide platform-conventional defaults (see
  [14-platform-desktop.md](14-platform-desktop.md#keyboard-shortcuts-design)
  for the desktop shortcut-mapping conventions this should follow) as the
  starting point, not an empty/unbound state.

**When NOT to use / exceptions:**
- System-reserved combinations (OS-level shortcuts) should not be
  rebindable-into by the game, and the remap UI should warn/block an attempt
  to bind over one.

**Pros:** Removes a real accessibility and preference barrier at comparatively
low cost given the game's manual-input surface is intentionally small.
**Cons:** Conflict detection (two actions bound to the same key) and clear
communication of the conflict add UI complexity beyond a simple list of
bindings.

**Implementation guidance:**
- Detect and clearly flag binding conflicts at the moment of rebinding
  (not silently allow two actions on one key), with a clear resolution path
  (swap, cancel, or explicitly allow simultaneous-trigger where that's
  actually intended).
- Provide a one-click "reset to default" both globally and per-binding.
- Persist bindings per-profile/save rather than per-device-only, if the game
  supports multiple local profiles, so remapping isn't lost on a shared
  machine.

**Verification checklist:**
- [ ] Every discrete input action is individually rebindable.
- [ ] Binding conflicts are detected and surfaced at rebind time, not
      discovered later as a silent double-trigger.
- [ ] A reset-to-default path exists globally and per-binding.
- [ ] Defaults follow platform convention rather than an arbitrary scheme.

**Related:** [14-platform-desktop.md](14-platform-desktop.md#keyboard-shortcuts-design),
[#gamepad-and-controller-navigation](#gamepad-and-controller-navigation).

**Sources:** [14-platform-desktop.md](14-platform-desktop.md#keyboard-shortcuts-design).

---

## Progressive tutorialization for deep systems

**Definition:** Teaching a system as deep as this game's (itemization,
character trees, formation/grid tactics, the Tactics/automation editor)
through **staged, contextual** introduction tied to the moment each system
first matters — not a front-loaded tutorial sequence covering everything
before the player has touched any of it.

**Reasoning / Evidence:** Directly extends the mobile-onboarding evidence
base ([15-platform-mobile.md](15-platform-mobile.md#onboarding)): front-loaded
slide/tutorial sequences are poorly retained (recall of instructions given
before they're needed is close to nil —
[02-cognitive-foundations.md](02-cognitive-foundations.md#working-memory-and-chunking)) —
and this project's own systems are *unusually* numerous and deep for a single
front-loaded tutorial to cover honestly. The core answer is the same
progressive-disclosure principle
([02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure))
applied at the scale of an entire game's systems, not just one screen.

**When to use:**
- Gate each system's *introduction* to the moment it first becomes relevant:
  don't explain gems/sockets before the player has an item with a socket;
  don't explain the Tactics/automation editor before the player has more than
  one hero and a reason to want automated behavior.
- Use a small number of guided, dismissible callouts/highlights tied to a
  specific UI element the first time a screen is opened, rather than a modal
  wall of text — consistent with empty-state guidance
  ([04-interaction-design.md](04-interaction-design.md#empty-states)) and
  onboarding-copy conventions
  ([09-content-ux-writing.md](09-content-ux-writing.md)).
- Provide starter presets/defaults for the deepest systems (a default
  Tactics ruleset per class, sensible default attribute allocation) so a
  system's *value* is demonstrated before the player is asked to author it
  from scratch — this mirrors the "value-first" onboarding evidence
  directly ([15-platform-mobile.md](15-platform-mobile.md#onboarding)).

**When NOT to use / exceptions:**
- A single, short (1–3 screen) value-proposition intro for the game as a
  whole can still earn its place before any system-specific tutorialization —
  the exception named in the mobile-onboarding guidance
  ([15-platform-mobile.md](15-platform-mobile.md#onboarding)) — but every
  system-specific "how this works" explanation should wait for its moment.
- Don't gate progress on completing an optional tutorial callout — it should
  be dismissible and, ideally, revisitable later from a help/reference menu
  for a player who skipped it and later wants it.

**Pros:** Matches how players actually learn a deep system (in context, at
the moment of need) rather than asking for front-loaded memorization; keeps
first-session friction low despite the game's eventual system depth.
**Cons:** Requires tracking "has this player seen this system's intro" state
per system, and designing a dismissible/re-visitable callout for each —
meaningfully more design and engineering surface than one linear tutorial
sequence, but with a much better activation/retention payoff per the cited
evidence.

**Implementation guidance:**
- Maintain an explicit "systems introduced" flag set per player-save so a
  callout fires exactly once per system (or is manually re-invokable from a
  help menu), not repeatedly.
- Pair every new system's first exposure with a working default/starter
  configuration (starter Tactics presets, sensible default builds) rather
  than an empty, unconfigured state requiring immediate authorship.
- For the deepest systems specifically (itemization comparison, the Tactics
  editor, the formation grid), consider a short "try it" moment embedded in
  the callout itself (e.g. the first Tactics-editor callout walks the player
  through editing one real rule) rather than a purely descriptive tooltip —
  doing beats reading for retention
  ([15-platform-mobile.md](15-platform-mobile.md#onboarding)).
- Track step-level engagement/drop-off per system's intro (which callouts get
  dismissed unread vs. completed) the same way general onboarding funnels are
  measured ([15-platform-mobile.md](15-platform-mobile.md#onboarding)), so
  underperforming introductions can be identified and revised.

**Verification checklist:**
- [ ] Each deep system's introduction is gated to the moment it first becomes
      relevant, not front-loaded.
- [ ] Every system-specific callout is dismissible and, ideally,
      re-invokable later from a help/reference surface.
- [ ] New systems ship with a working default/starter configuration, not an
      empty state requiring immediate authorship.
- [ ] No progress gate blocks on completing an optional tutorial callout.
- [ ] Per-system intro engagement (seen/dismissed/completed) is trackable for
      later iteration.

**Related:** [02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure),
[15-platform-mobile.md](15-platform-mobile.md#onboarding),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#rule-based-automation-editor-tacticsgambit-pattern).

**Sources:** [15-platform-mobile.md](15-platform-mobile.md#onboarding)
(NN/g mobile-app onboarding research), [02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure).

---

## Colorblind-safe rarity and element coding

**Definition:** Encoding item-rarity tiers (common/magic/rare/legendary/set)
and damage/element types (physical/fire/cold/etc.) with redundant, non-color-
dependent cues — shape, icon, border pattern, or text label — alongside color,
so the encoding survives for players with color vision deficiency.

**Reasoning / Evidence:** This is a direct, high-stakes application of the
general color-independence principle
([03-visual-design.md](03-visual-design.md#color-blindness-and-color-independence),
[05-accessibility.md](05-accessibility.md#color-independence)) to the two
color-coding systems this game's design leans on most heavily. Rarity tiers
and elemental damage types are exactly the kind of "meaning conveyed by hue
alone" pattern WCAG 1.4.1 (Use of Color) prohibits, and the prevalence figure
is directly relevant here: **~8% of men of Northern European descent** have
some form of color vision deficiency — for a loot-chase game where correctly
perceiving rarity at a glance is core to the reward loop, that's a
substantial fraction of the audience for whom color-only coding silently
degrades the entire itemization power fantasy.

**When to use:**
- Rarity tiers: pair the tier color with a **distinct border/frame style**
  per tier (thickness, pattern, or corner ornamentation) and, ideally, a
  small icon/glyph — so rarity is identifiable from shape alone, not only
  from the color of the border/background/text.
- Elemental/damage types: pair each element's color with a distinct **icon**
  (a flame glyph for fire, a snowflake for cold, etc.) on every surface the
  type appears — floating combat text, tooltips, enemy resistance displays,
  skill icons — never color-only.
- Verify the full palette (rarity tiers × element types, which is a
  meaningful number of simultaneous colors) against at least deuteranopia
  and protanopia simulation, the two most common forms, not just a single
  "colorblind mode" spot-check.

**When NOT to use / exceptions:**
- None — this is a baseline correctness requirement (WCAG 1.4.1, Level A)
  for any UI using color to convey rarity/type meaning, not a judgment call.

**Pros:** Extends full itemization comprehension to a meaningful share of
players who would otherwise silently lose a core piece of the game's
information design; typically low additional cost if built in from the start
(icon/border variation the design likely wants for visual richness anyway).
**Cons:** Retrofitting redundant coding onto an already-shipped, color-only
system touches every screen that renders an item or damage type — meaningfully
cheaper to build in from the start than to add later.

**Implementation guidance:**
- Rarity: color + border treatment + optional corner glyph, consistently
  applied across inventory, tooltips, comparison views, and vendor/loot-drop
  screens (mirrors the cross-screen-consistency requirement in
  [22-game-ui-and-hud.md](22-game-ui-and-hud.md#dense-stat-and-inventory-screens)).
- Elements: a fixed icon-per-element mapping used everywhere that type
  appears; never introduce a second, inconsistent icon set for the same
  element on a different screen.
- Test with a simulator (e.g. a deuteranopia/protanopia filter applied to a
  full inventory screenshot) as part of the visual-design review for any new
  rarity tier or element type added to the game.
- Contrast: rarity/element text labels and icons still need to meet the
  general contrast minimums ([05-accessibility.md](05-accessibility.md#contrast-minimum))
  independent of the color-independence fix — a redundant icon that itself
  has poor contrast against its background doesn't fully solve the problem.

**Verification checklist:**
- [ ] Every rarity tier is distinguishable by border/shape/icon without
      relying on color.
- [ ] Every element/damage type has a consistent icon used on every screen it
      appears, not color-only.
- [ ] The full color set has been checked against a deuteranopia/protanopia
      simulation, not just eyeballed.
- [ ] Redundant icons/labels meet standard contrast minimums independently.

**Related:** [03-visual-design.md](03-visual-design.md#color-blindness-and-color-independence),
[05-accessibility.md](05-accessibility.md#color-independence),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#deep-item-comparison-tooltips).

**Sources:** [03-visual-design.md](03-visual-design.md#color-blindness-and-color-independence)
(prevalence figure), [WCAG 1.4.1: Use of Color](https://www.w3.org/WAI/WCAG22/Understanding/use-of-color.html).

---

## Text scaling for dense stat displays

**Definition:** Supporting the platform's text-size/zoom accommodation
(OS-level Dynamic Type on mobile, browser/OS zoom on desktop) across screens
that pack many small numeric stat values into a constrained grid or tooltip —
the hardest UI surfaces in this game to scale gracefully, precisely because
they're already information-dense at default size.

**Reasoning / Evidence:** This extends the general text-resize requirement
(WCAG 1.4.4, reflow at WCAG 1.4.10 —
[05-accessibility.md](05-accessibility.md#reflow)) to the specific case where
the content is *already* dense at 100% scale, meaning naive scaling produces
overlap/truncation faster than in typical prose-heavy UI. This is a real
tension named directly in the index's conflict-resolution rules
(minimalism/density vs accessibility) — and per that rule, accessibility
wins: the design must accommodate scaling, not exempt the densest screens
from it.

**When to use:**
- Every stat/affix display (comparison tooltips, character sheets, inventory
  grids) must reflow rather than truncate or overlap when text scales up —
  applies the same reflow principle
  ([05-accessibility.md](05-accessibility.md#reflow)) that general responsive
  design uses for viewport width, applied to *font*-size-driven reflow
  specifically.
- For the very densest displays (a full affix list at maximum text scale),
  prefer **progressive collapse** (abbreviate less-critical values, or
  paginate/scroll the list) over shrinking text below a legible floor to
  force a fit.

**When NOT to use / exceptions:**
- Purely decorative micro-text (a version-number watermark) is a reasonable
  exception to strict scaling requirements; anything conveying game-relevant
  information is not.

**Pros:** Extends full itemization/character-sheet comprehension to
low-vision players and anyone using platform text-scaling for comfort, not
just necessity.
**Cons:** Genuinely harder to design for than typical app UI given how dense
the baseline layouts already are — budget real design time for the
"how does this tooltip look at 150–200% text scale" question specifically,
not as an afterthought.

**Implementation guidance:**
- Design and test every dense screen at the platform's meaningful scale
  checkpoints (e.g. 150% and 200% text size, and WCAG's 400% reflow-zoom
  checkpoint for the web reference point — [05-accessibility.md](05-accessibility.md#reflow))
  as a standard part of that screen's review, not a one-off audit pass.
- Prefer wrapping/reflowing stat rows to a second line over horizontal
  truncation with an ellipsis — a truncated stat value is actively misleading
  in a game where exact numbers drive decisions, worse than a slightly taller
  row.
- Where a grid genuinely cannot reflow gracefully (e.g. a small inventory-
  grid cell's stack-count badge), keep only the single most critical value at
  small scale and move secondary detail to the on-demand tooltip
  ([22-game-ui-and-hud.md](22-game-ui-and-hud.md#deep-item-comparison-tooltips))
  rather than shrinking the visible text below a legible floor.

**Verification checklist:**
- [ ] Dense stat/tooltip screens tested at 150% and 200% platform text scale
      with no overlap or truncated numeric values.
- [ ] No numeric/stat value is ever truncated with an ellipsis — reflow or
      move to on-demand detail instead.
- [ ] A defined minimum legible text size floor exists even on the densest
      grid cells, with excess detail deferred to tooltips rather than shrunk
      below it.

**Related:** [05-accessibility.md](05-accessibility.md#reflow),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#dense-stat-and-inventory-screens).

**Sources:** [WCAG 1.4.4: Resize Text](https://www.w3.org/WAI/WCAG22/Understanding/resize-text.html),
[WCAG 1.4.10: Reflow](https://www.w3.org/WAI/WCAG22/Understanding/reflow.html).

---

## Game localization

**Definition:** Supporting multiple display languages for a large, numeric-
and-terminology-heavy vocabulary (class names, skill names, affix text,
condition/action labels in the Tactics editor, combat-log text) via Godot's
translation workflow (`tr()` and CSV/PO translation files), with layouts that
survive the resulting text-length variation.

**Reasoning / Evidence:** Extends the general i18n/l10n guidance
([09-content-ux-writing.md](09-content-ux-writing.md#global-content)) — text
expansion (+30–50% for paragraphs, +100–300% for short labels in some target
languages) and RTL mirroring — to a domain with an unusually large volume of
short, systematic strings (every affix, every skill, every condition/action
in the automation editor) authored as **data** (per this project's data-
driven content pipeline), which changes where translation work actually
happens: it's a content-pipeline problem as much as a UI problem.

**When to use:**
- Route every player-facing string — including strings generated from
  data-driven content (an affix's display template, a skill's tooltip
  template) — through the engine's translation system (`tr()` in Godot)
  rather than hardcoding language-specific text anywhere, including inside
  content `Resource` files (see the Coding library's
  [data-as-Resource guidance](../Coding/languages/gdscript.md#7-exports-resources--scene-composition))
  — a hardcoded string inside a content file is invisible to the translation
  pipeline and silently ships untranslated.
- Design number/stat formatting to respect locale conventions (decimal
  separator, digit grouping) per the general data-format guidance in
  [09-content-ux-writing.md](09-content-ux-writing.md#global-content), applied
  to the very large number of raw numeric stat displays this game has.
- Plan for the automation/Tactics editor's plain-language rule summary
  (§ [22](22-game-ui-and-hud.md#rule-based-automation-editor-tacticsgambit-pattern))
  as a **templated, translatable** construction (a sentence built from
  translatable fragments with correct grammatical agreement per locale, using
  ICU plural/selection rules — [09-content-ux-writing.md](09-content-ux-writing.md))
  rather than string-concatenation in one language, since naive concatenation
  breaks grammar in most non-English target languages.

**When NOT to use / exceptions:**
- Internal-only IDs (item IDs, condition-type enum keys) are correctly never
  translated — only player-facing display text routes through translation.

**Pros:** A translation pipeline designed around the data-driven content
system scales with content volume the same way the content pipeline itself
does (§ Design library's [data-driven content pipeline](../Design/10-game-architecture.md#6-data-driven-content-pipeline))
— new content ships with translatable fields by construction, not as an
afterthought per item.
**Cons:** Retrofitting translation routing onto content authored with
hardcoded display strings requires touching every content file — meaningfully
cheaper to require translatable fields in the content schema from the first
item/skill/affix authored, per the Design library's schema-versioning
guidance for the same pipeline.

**Implementation guidance:**
- Every data-driven content schema (item, affix, skill, condition, action)
  includes a translation-key field, not a raw display string, from the first
  version of the schema — enforced at content-authoring/validation time.
- Budget UI layout for +30–50% text expansion on labels/tooltips as standard
  practice ([09-content-ux-writing.md](09-content-ux-writing.md#global-content)),
  and specifically stress-test the densest surfaces
  ([dense stat/inventory screens](22-game-ui-and-hud.md#dense-stat-and-inventory-screens),
  the Tactics rule-row layout) against a long-expansion target language early,
  not only against English.
- Use ICU plural/selection message formatting
  ([09-content-ux-writing.md](09-content-ux-writing.md)) for any templated
  text with a variable count or subject (damage-type names, target counts in
  a rule summary) rather than string concatenation.

**Verification checklist:**
- [ ] Every player-facing string, including strings generated from
      data-driven content, routes through the translation system.
- [ ] Content schemas (item/affix/skill/condition/action) carry translation
      keys, not hardcoded display strings, from their first version.
- [ ] Dense UI surfaces are tested against a long-text-expansion target
      language, not only English.
- [ ] Templated/generated text (rule summaries, combat-log lines) uses
      ICU plural/selection formatting, not concatenation.
- [ ] Numeric/stat formatting respects locale decimal/grouping conventions.

**Related:** [09-content-ux-writing.md](09-content-ux-writing.md#global-content),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#rule-based-automation-editor-tacticsgambit-pattern),
the Design library's
[data-driven content pipeline](../Design/10-game-architecture.md#6-data-driven-content-pipeline).

**Sources:** [09-content-ux-writing.md](09-content-ux-writing.md#global-content),
Godot Docs, "Internationalizing games" —
https://docs.godotengine.org/en/stable/tutorials/i18n/internationalizing_games.html.

---

## Quick reference

| Topic | One-line guidance | Key numbers |
|---|---|---|
| Game feel / juice | Tier intensity to event significance; scale down at high combat speed | — |
| Reduced motion | Off-ramp for screenshake/flash; never disable informational feedback | ≤3 flashes/sec hard ceiling |
| Gamepad navigation | Full operability; author focus-neighbor graphs to match visual grids | — |
| Input remapping | Every discrete action individually rebindable; detect conflicts | — |
| Progressive tutorialization | Teach each system at the moment it first matters, not front-loaded | — |
| Colorblind-safe coding | Rarity/element = color + shape/icon, never color-only | ~8% color-blind men |
| Text scaling | Reflow dense stats, never truncate numeric values | 150% / 200% test checkpoints |
| Localization | Content schemas carry translation keys from v1; ICU plurals for templates | +30–50% text expansion |
