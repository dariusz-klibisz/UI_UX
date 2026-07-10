# Game Feel, Input & Onboarding

> Part of the [UI/UX Reference](00-index.md). See index for the full documentation map.

## Scope

This file covers game-specific UX concerns not centered on a particular
screen: "juice"/game feel (hit-flash, screenshake, reward-moment feedback),
gamepad/controller navigation and remapping, progressive tutorialization for
deep systems, game-specific accessibility (colorblind-safe rarity/element
coding, damage-type iconography, text scaling for dense stats, reduced motion
for combat/idle VFX, subtitles/captions, audio and communication
accessibility, screen-reader support), game localization workflow, and the
industry-standard baseline for a game's settings menu. Screen-level game UI
patterns (HUD, tooltips, inventory, formation editor, the Tactics/automation
editor) are in
[22-game-ui-and-hud.md](22-game-ui-and-hud.md). General motion, gesture, and
keyboard-interaction foundations are in
[04-interaction-design.md](04-interaction-design.md); general accessibility in
[05-accessibility.md](05-accessibility.md); general i18n/l10n in
[09-content-ux-writing.md](09-content-ux-writing.md).

Standards baseline used here, beyond WCAG 2.2: the
[Game Accessibility Guidelines](https://gameaccessibilityguidelines.com/)
(GAG — cross-studio collaborative reference), Microsoft's
[Xbox Accessibility Guidelines v3.2](https://learn.microsoft.com/en-us/gaming/accessibility/guidelines)
(XAG), AbleGamers'
[Accessible Player Experiences](https://accessible.games/accessible-player-experiences/)
(APX) design patterns, and
[Valve's Steam Deck compatibility criteria](https://partner.steamgames.com/doc/steamdeck/compat).

## Contents

- [Game feel and juice](#game-feel-and-juice)
- [Reduced motion for combat and idle VFX](#reduced-motion-for-combat-and-idle-vfx)
- [Gamepad and controller navigation](#gamepad-and-controller-navigation)
- [Input remapping](#input-remapping)
- [Progressive tutorialization for deep systems](#progressive-tutorialization-for-deep-systems)
- [Colorblind-safe rarity and element coding](#colorblind-safe-rarity-and-element-coding)
- [Text scaling for dense stat displays](#text-scaling-for-dense-stat-displays)
- [Subtitles and captions](#subtitles-and-captions)
- [Audio UX and communication accessibility](#audio-ux-and-communication-accessibility)
- [Screen readers and non-visual play](#screen-readers-and-non-visual-play)
- [Game localization](#game-localization)
- [Game settings baseline](#game-settings-baseline)
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
though the underlying game-state change is "just a number." Steve Swink's
*Game Feel* (2008) formalized the broader concept: perceived responsiveness
and impact come from the layered polish around an action, not only from the
action's mechanical outcome.

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
  enhancement; WCAG 2.3.3 Animation from Interactions (Level AAA, "motion
  animation triggered by interaction can be disabled") and general
  vestibular-disorder accommodation both call for an off-ramp for players
  sensitive to screen motion, and the Game Accessibility Guidelines list
  screen-motion/background-movement toggles as standard practice.

**Pros:** Meaningfully increases perceived game quality and reward
satisfaction for a comparatively low implementation cost per effect.
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
- Engine mechanics for layout-safe UI juice: animating a control's position/
  rotation/scale inside a layout container normally fights the container's
  own sorting. Godot 4.7's `Control.offset_transform_*` properties apply a
  visual-only transform on top of container layout (hover/input unaffected
  by default), and `pivot_offset_ratio` (4.6) keeps scale/rotation centered
  as controls resize — use these (or the equivalent in another engine/UI
  stack) instead of hand-repositioning controls per frame.

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
(Saffer, *Microinteractions*); Martin Jonasson & Petri Purho, "Juice it or
lose it" (2012) — the originating popular reference for "juice"; Steve
Swink, *Game Feel: A Game Designer's Guide to Virtual Sensation* (2008);
[Godot 4.7 release notes — Control offset transforms](https://godotengine.org/releases/4.7/).

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
- Treat each camera-motion effect as its own toggle where the genre uses
  them (screenshake, motion blur, head-bob, FOV kicks, background/parallax
  movement) — the Game Accessibility Guidelines list these individually, and
  bundling them into one switch forces players to give up effects they
  tolerate to remove the one that makes them sick. For 3D games, an
  adjustable field of view is the companion accommodation.
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
[WCAG 2.3.3: Animation from Interactions (AAA)](https://www.w3.org/WAI/WCAG22/Understanding/animation-from-interactions.html),
[Game Accessibility Guidelines (vision/motion items)](https://gameaccessibilityguidelines.com/full-list/),
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
- None — full gamepad operability is a baseline requirement for a PC- or
  console-targeted game with deep menu systems, not an optional nice-to-have,
  especially for genres whose core loop lives in menu-driven screens
  (itemization, character development) rather than direct action. It is also
  a storefront certification concern: Valve's Steam Deck "Verified" review
  requires that the default controller configuration reaches **all** game
  content with no settings changes, that launchers are controller-navigable,
  and that text entry invokes the on-screen keyboard.

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
- Show button glyphs that match the *active* input device, and switch them
  live when the player changes device — showing keyboard glyphs to a
  controller player is an explicit Steam Deck Verified failure ("on-screen
  glyphs must match the inputs being used").
- Style controller/keyboard focus separately from mouse hover: Godot 4.6+
  separates mouse and keyboard/controller focus natively, so clicking no
  longer forces the focus outline and each scheme can be themed
  independently — the engine-level mechanism for the "focus indicator
  distinct from hover" requirement above.
- Consider ignoring gamepad input when the game window is unfocused (a
  project setting in Godot 4.7+) — OS-level gamepad events are delivered to
  unfocused windows, and stray input to a backgrounded game is a common
  source of accidental actions on PC.

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
(the general keyboard-operability requirement this extends);
[Godot Docs: Keyboard/Controller Navigation and Focus](https://docs.godotengine.org/en/stable/tutorials/ui/gui_navigation.html)
(the concrete focus-neighbor mechanism this guidance assumes);
[Valve: Steam Deck Compatibility Review](https://partner.steamgames.com/doc/steamdeck/compat)
(controller support, glyph-matching, and text-input criteria).

---

## Input remapping

**Definition:** Player-configurable key/button bindings for every
keyboard/gamepad-driven action, with sensible platform-appropriate defaults
and conflict detection.

**Reasoning / Evidence:** Extends the general customization/efficiency-for-
experts principle: fixed bindings that conflict with a player's existing
muscle memory, accessibility hardware, or regional keyboard layout (a
QWERTY-assumed binding on an AZERTY keyboard) create real barriers that
remapping directly removes. Remapping is the single most-complained-about
accessibility issue in games per the Game Accessibility Guidelines' public
complaint tracking (ahead of text size, colorblindness, and subtitle
presentation). In games with a small set of discrete actions (auto-battlers,
turn-based and menu-driven genres), full remapping is comparatively cheap to
support well — there is little excuse to omit it.

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
- Avoid *requiring* held buttons or rapid repeated presses (button-mashing)
  for any action, or provide a toggle/alternative — a standard motor-
  accessibility guideline (GAG intermediate) that belongs to the same input
  layer as remapping.
- In engine terms, drive all gameplay input through an action-mapping layer
  (Godot's `InputMap` actions, rebindable at runtime) rather than raw
  key/button codes — remapping then edits data, not code. Newer input
  channels (e.g. controller gyro, readable in Godot 4.7+) should route
  through the same action layer and remain optional/supplementary, never the
  only way to perform an action.

**Verification checklist:**
- [ ] Every discrete input action is individually rebindable.
- [ ] Binding conflicts are detected and surfaced at rebind time, not
      discovered later as a silent double-trigger.
- [ ] A reset-to-default path exists globally and per-binding.
- [ ] Defaults follow platform convention rather than an arbitrary scheme.

**Related:** [14-platform-desktop.md](14-platform-desktop.md#keyboard-shortcuts-design),
[#gamepad-and-controller-navigation](#gamepad-and-controller-navigation).

**Sources:** [14-platform-desktop.md](14-platform-desktop.md#keyboard-shortcuts-design);
[Game Accessibility Guidelines: remapping](https://gameaccessibilityguidelines.com/allow-controls-to-be-remapped-reconfigured/)
(and its "most commonly complained about issues" note);
[Xbox Accessibility Guidelines](https://learn.microsoft.com/en-us/gaming/accessibility/guidelines)
(input remapping guideline).

---

## Progressive tutorialization for deep systems

**Definition:** Teaching a deep, multi-system game (itemization, character
trees, formation/grid tactics, rule-based automation editors) through
**staged, contextual** introduction tied to the moment each system first
matters — not a front-loaded tutorial sequence covering everything before
the player has touched any of it.

**Reasoning / Evidence:** Directly extends the mobile-onboarding evidence
base ([15-platform-mobile.md](15-platform-mobile.md#onboarding)): front-loaded
slide/tutorial sequences are poorly retained (recall of instructions given
before they're needed is close to nil —
[02-cognitive-foundations.md](02-cognitive-foundations.md#working-memory-and-chunking)) —
and deep-systems games have too many interacting systems for a single
front-loaded tutorial to cover honestly. Game-UX practice reaches the same
conclusion: interactive, in-context tutorialization is a Game Accessibility
Guidelines basic item, and Celia Hodent's *The Gamer's Brain* frames
onboarding as distributed learning-by-doing across the first hours of play.
The core answer is the same progressive-disclosure principle
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
- Keep current objectives and current controls reviewable at any time during
  play (an objectives panel, a controls-reminder screen) — players who put a
  game down for a week, and players with memory impairments, both need a
  non-punishing way to re-orient (GAG cognitive guidelines).
- Provide a means of practicing without failure (a practice/sandbox mode, or
  low-stakes early encounters) for mechanically demanding systems — also a
  GAG cognitive item and APX's "Training Ground" pattern.
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
(NN/g mobile-app onboarding research), [02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure);
Celia Hodent, *The Gamer's Brain: How Neuroscience and UX Can Impact Video
Game Design* (2017);
[Game Accessibility Guidelines: cognitive items](https://gameaccessibilityguidelines.com/full-list/)
(interactive tutorials, objective/control reminders, practice without
failure).

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
color-coding systems loot-driven games lean on most heavily. Rarity tiers
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

**Definition:** Supporting minimum legible text sizes and player-controlled
text scaling across screens that pack many small numeric stat values into a
constrained grid or tooltip — the hardest game-UI surfaces to scale
gracefully, precisely because they're already information-dense at default
size.

**Reasoning / Evidence:** This extends the general text-resize requirement
(WCAG 1.4.4, reflow at WCAG 1.4.10 —
[05-accessibility.md](05-accessibility.md#reflow)) to the specific case where
the content is *already* dense at 100% scale, meaning naive scaling produces
overlap/truncation faster than in typical prose-heavy UI. This is a real
tension named directly in the index's conflict-resolution rules
(minimalism/density vs accessibility) — and per that rule, accessibility
wins: the design must accommodate scaling, not exempt the densest screens
from it. The games industry has published concrete floors: Microsoft's Xbox
Accessibility Guidelines set **minimum default text sizes of 26px at 1080p
on console and 18px at 1080p on PC/VR** (body height, ascender to
descender), with player scaling **up to 200%** of those minimums without
loss of content or function; Valve's Steam Deck Verified review fails games
whose smallest text falls below **9px at 1280×800** (12px recommended).
Text size is one of the four most-complained-about accessibility issues in
games (GAG).

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
- Scale button glyphs and inline icons *with* the text (XAG: glyphs follow
  text scaling up to 200%) — scaled-up text next to fixed-size gamepad
  glyphs breaks prompt lines first.
- Beyond size: XAG's readability guidance also covers spacing (line ≤80
  characters, ~1.5 line spacing, letter spacing ≥0.12× and word spacing
  ≥0.16× the font size for text blocks), at least one sans-serif and one
  non-stylized font option, and sentence case over all-caps for running
  text.
- Expose a player-facing UI-scale setting where the engine supports it — in
  Godot, the root window's `content_scale_factor` scales the whole UI
  independent of resolution and is the recommended mechanism for a UI-size
  option; font oversampling keeps scaled text crisp.
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
- [ ] Default text sizes meet the platform floors (XAG: 26px console /
      18px PC at 1080p; Steam Deck: ≥9px at 1280×800, 12px recommended).
- [ ] Button glyphs and inline icons scale together with text.

**Related:** [05-accessibility.md](05-accessibility.md#reflow),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#dense-stat-and-inventory-screens).

**Sources:** [WCAG 1.4.4: Resize Text](https://www.w3.org/WAI/WCAG22/Understanding/resize-text.html),
[WCAG 1.4.10: Reflow](https://www.w3.org/WAI/WCAG22/Understanding/reflow.html),
[XAG 101: Text display](https://learn.microsoft.com/en-us/gaming/accessibility/xbox-accessibility-guidelines/101)
(minimum sizes, 200% scaling, spacing),
[Valve: Steam Deck Compatibility Review](https://partner.steamgames.com/doc/steamdeck/compat)
(9px/12px legibility criterion),
[Godot Docs: Multiple resolutions](https://docs.godotengine.org/en/stable/tutorials/rendering/multiple_resolutions.html)
(`content_scale_factor`, stretch modes, oversampling).

---

## Subtitles and captions

**Definition:** Text rendering of spoken dialogue (subtitles) and — for
captions — of significant non-speech audio (an off-screen explosion, an
approaching enemy's audio cue, speaker identification), shown during
gameplay, cutscenes, and any pre-rendered video.

**Reasoning / Evidence:** Subtitle presentation is one of the four
most-complained-about accessibility issues in games (Game Accessibility
Guidelines complaint tracking), and subtitles are not a niche feature:
large majorities of players enable them regardless of hearing status —
Ubisoft has reported that ~95% of *Assassin's Creed: Odyssey* players kept
subtitles on (players in noisy/muted environments, non-native speakers,
players who read faster than they parse speech). The most common
presentation failures are well documented: text too small, poor contrast
against a moving scene, and too much text on screen at once.

**When to use:**
- Subtitles for **all** speech (including incidental/ambient dialogue, not
  only cinematics), enabled or offered *before* any speech plays — a
  first-launch prompt or default-on, not a setting discovered after the
  opening scene (GAG intermediate).
- Captions (beyond subtitles) for gameplay-significant sounds: what the
  sound was and, where directional audio matters, where it came from —
  otherwise sound-conveyed information excludes deaf/hard-of-hearing
  players entirely (pairs with
  [#audio-ux-and-communication-accessibility](#audio-ux-and-communication-accessibility)).
- Speaker identification when more than one character can speak: a name
  label and/or per-speaker color, so overlapping dialogue is attributable.

**When NOT to use / exceptions:**
- Games with no speech and no gameplay-significant audio have nothing to
  subtitle — but verify the "no significant audio" premise (UI feedback
  sounds that carry meaning still warrant a visual equivalent).
- Don't burn subtitles into pre-rendered video at a fixed size — they can't
  scale, can't be restyled, and get scaled down with the video on small
  screens; render them at runtime.

**Pros:** Serves deaf/hard-of-hearing players and the much larger
subtitles-on-by-preference majority in one feature; cheap relative to its
reach.
**Cons:** Dense combat dialogue plus captions can outpace reading speed —
prioritize gameplay-relevant lines; caption volume needs editorial judgment,
not blanket transcription.

**Implementation guidance:**
- Size: **no smaller than 46px at 1080p** (GAG best practice), either by
  default or via options; scale with the game's text-size setting
  ([#text-scaling-for-dense-stat-displays](#text-scaling-for-dense-stat-displays)).
- Contrast: render on a solid or semi-opaque background box
  ("letterboxing"), ideally with an outline/shadow as well — never raw text
  over a moving scene.
- Volume of text: **≤40 characters per line, ≤2 lines per subtitle** (3 in
  exceptional cases); bottom-center placement, clear of other HUD elements.
- Typography: mixed case (not all-caps), a clean readable face; where art
  direction wants a stylized font, offer a plain-legibility option (GAG:
  readability wins, and customization satisfies both goals).
- Customization: expose size, background opacity, and speaker-color options
  — subtitle customization is its own GAG intermediate item, and consoles'
  system-level subtitle preferences set player expectations.

**Verification checklist:**
- [ ] All speech is subtitled, including ambient/incidental lines.
- [ ] Subtitles are on by default or offered before the first spoken line.
- [ ] ≥46px at 1080p (or player-scalable to that), letterboxed background,
      ≤40 chars/line, ≤2 lines.
- [ ] Speakers are identified when multiple characters speak.
- [ ] Gameplay-significant non-speech sounds have captions or a visual
      equivalent.
- [ ] Subtitles in pre-rendered video are rendered at runtime, not burned
      in.

**Related:** [#audio-ux-and-communication-accessibility](#audio-ux-and-communication-accessibility),
[#text-scaling-for-dense-stat-displays](#text-scaling-for-dense-stat-displays),
[09-content-ux-writing.md](09-content-ux-writing.md).

**Sources:** [GAG: subtitle presentation](https://gameaccessibilityguidelines.com/if-any-subtitles-captions-are-used-present-them-in-a-clear-easy-to-read-way/)
(46px, 40 chars, 2 lines, letterboxing),
[GAG: subtitles for all important speech](https://gameaccessibilityguidelines.com/provide-subtitles-for-all-important-speech/),
[XAG 101: Text display](https://learn.microsoft.com/en-us/gaming/accessibility/xbox-accessibility-guidelines/101),
[BBC Subtitle Guidelines](https://www.bbc.co.uk/accessibility/forproducts/guides/subtitles/)
(broadcast-grade reference for timing, line breaks, and speaker
identification).

---

## Audio UX and communication accessibility

**Definition:** The sound layer treated as an information channel — mixing
and volume controls, redundancy between audio and visual channels in both
directions, and the accessibility of player-to-player communication
(text/voice chat) where the game has it.

**Reasoning / Evidence:** Audio carries real information in games (incoming
damage direction, cooldown-ready cues, rare-drop stingers), so the general
color-independence logic applies symmetrically to sound: **no essential
information by sound alone** (excludes deaf/hard-of-hearing players) and
**no essential information by visuals alone where audio can reinforce it**
(supports blind/low-vision players and eyes-busy moments). Separate volume
controls are a GAG *basic* item because a single master slider forces
players to choose between hearing speech and drowning it in music. For
player-to-player communication this is not merely best practice: in the US,
the **CVAA** (21st Century Communications and Video Accessibility Act) has
applied to video games since January 1, 2019 — in-game text/voice chat and
the UI used to operate it must be accessible to players with disabilities,
enforced by the FCC on a complaint basis.

**When to use:**
- Separate volume sliders (with mutes) for at minimum music, sound effects,
  and speech/voice; UI feedback sounds ideally get their own channel.
- A mono-audio toggle (players deaf in one ear lose stereo-positioned
  information) and, where the game uses positional audio cues, a visual
  equivalent for them (damage-direction indicator, on-screen cue text).
- Distinct, non-interchangeable sounds for distinct key events (GAG:
  "ensure sound/music choices for each key object/event are distinct") —
  reinforced with UI sounds on focus/confirm/cancel, which double as
  navigation feedback for low-vision players.
- If the game has chat: text chat and voice chat as parallel channels
  (players who can't use one can use the other), with speech-to-text and
  text-to-speech options where feasible — this is the CVAA-regulated
  surface.

**When NOT to use / exceptions:**
- Purely decorative ambience doesn't need captions or visual mirroring —
  the requirement attaches to information, not to all sound.
- Single-player games with no communication features have no CVAA surface —
  the volume/redundancy guidance still applies.

**Pros:** Extends play to deaf/hard-of-hearing and blind/low-vision players;
better mixing controls are a mainstream-quality feature every player uses.
**Cons:** Visual equivalents for audio cues add HUD surface — budget them
into the HUD's glance economy ([22](22-game-ui-and-hud.md#hud-layout-during-live-action))
rather than bolting on indicator clutter.

**Implementation guidance:**
- Keep background music/noise low during speech (GAG), or duck it
  automatically while dialogue plays.
- Verify every gameplay-significant audio cue has a visual counterpart and
  vice versa — walk the cue inventory, don't spot-check.
- For chat UIs: caption voice chat where possible, make text chat fully
  operable by keyboard/gamepad/screen reader
  ([#screen-readers-and-non-visual-play](#screen-readers-and-non-visual-play)),
  and don't gate any gameplay-critical coordination on voice-only channels.
- Persist all audio settings; expose them from the pause menu, not only the
  main menu.

**Verification checklist:**
- [ ] Separate volume controls (or mutes) exist for music, SFX, and speech.
- [ ] A mono-audio toggle exists.
- [ ] No gameplay-essential information is conveyed by sound alone; each
      significant cue has a visual equivalent.
- [ ] Key events have distinct sounds; music/noise stays low under speech.
- [ ] If chat exists: text and voice are parallel channels, and the chat UI
      is operable by keyboard/gamepad/assistive tech (CVAA).

**Related:** [#subtitles-and-captions](#subtitles-and-captions),
[#screen-readers-and-non-visual-play](#screen-readers-and-non-visual-play),
[05-accessibility.md](05-accessibility.md).

**Sources:** [Game Accessibility Guidelines (hearing items)](https://gameaccessibilityguidelines.com/full-list/),
[FCC: 21st Century Communications and Video Accessibility Act](https://www.fcc.gov/consumers/guides/21st-century-communications-and-video-accessibility-act-cvaa)
(47 U.S.C. §617; in force for game communication features since Jan 1,
2019), [Xbox Accessibility Guidelines](https://learn.microsoft.com/en-us/gaming/accessibility/guidelines)
(audio and communication guidelines).

---

## Screen readers and non-visual play

**Definition:** Making a game's menu- and text-driven UI operable without
sight — either by exposing the UI to OS/assistive-technology screen readers
through the engine's accessibility tree, or by **self-voicing** (the game
speaks its own UI via built-in text-to-speech).

**Reasoning / Evidence:** Games historically shipped no screen-reader
support because engines drew UI as pixels with no accessibility tree —
unlike web/native apps, there was nothing for a screen reader to read. That
premise is now outdated: **Godot 4.5 (2025) integrated AccessKit**, exposing
`Control`-node UIs to OS screen readers (experimental), and Godot 4.7 added
landmark navigation so focus changes announce their context ("Inspector,
name field" rather than a bare "name field"). Console platforms ship system
narration (Xbox's Narrator, PlayStation's screen reader), and menu-heavy
genres — deep itemization, management, turn-based strategy, interactive
fiction — are exactly where non-visual play is both feasible and already
practiced (self-voicing has a long tradition in audio games and roguelikes).
Where the game has chat, an accessible chat UI is also the CVAA-regulated
surface (above).

**When to use:**
- Menu/UI-heavy games: character sheets, inventories, settings, and
  rule-editor screens are structured data — precisely what screen readers
  handle well. Build UI from real widgets (engine `Control` nodes) rather
  than hand-drawn text-as-texture, and give every focusable element an
  accessible name (and description where the label alone is ambiguous).
- Reuse the gamepad focus graph
  ([#gamepad-and-controller-navigation](#gamepad-and-controller-navigation))
  as the non-visual navigation order — a correct focus-neighbor graph is
  most of the navigation work for AT users too.
- Announce dynamic changes that don't move focus (a stat that updates, an
  error appearing) — silent visual-only updates are invisible non-visually.

**When NOT to use / exceptions:**
- Fully spatial real-time gameplay may not be meaningfully narratable —
  prioritize menus, settings, notifications, and communication first; that
  alone converts "unplayable" into "playable with effort" for many players.
- Engine AT bridges are young (Godot's is explicitly experimental):
  self-voicing (built-in TTS reading the focused element) is a
  well-established fallback that doesn't depend on OS screen-reader
  integration, at the cost of maintaining your own speech layer.

**Pros:** Opens menu-driven genres to blind/low-vision players; the required
discipline (structured widgets, named controls, correct focus order) also
improves gamepad UX and UI-test automation.
**Cons:** Real per-screen authoring work (names, descriptions, announce
rules); experimental engine support needs testing against real ATs (NVDA,
VoiceOver, Narrator) each release — treat it as a feature with a test
matrix, not a checkbox.

**Implementation guidance:**
- Set accessible names/descriptions on every interactive control; verify
  reading order matches visual/logical order on each screen.
- Test with at least one real screen reader per shipping platform; don't
  rely on the engine's accessibility inspector alone.
- Keep all information available as text somewhere — icon-only states and
  color-only states ([#colorblind-safe-rarity-and-element-coding](#colorblind-safe-rarity-and-element-coding))
  fail non-visual access for the same root cause.
- Ensure game manuals/wikis/companion content are screen-reader friendly
  (GAG intermediate) even before in-game support lands.

**Verification checklist:**
- [ ] All menu/settings/inventory screens are operable and legible with a
      screen reader or self-voicing enabled.
- [ ] Every focusable control exposes an accessible name; dynamic updates
      are announced.
- [ ] Navigation order matches the visual layout (shared with the gamepad
      focus graph).
- [ ] Chat UI (if any) is AT-operable (CVAA).
- [ ] Tested against a real AT (NVDA/VoiceOver/Narrator) per platform, per
      release.

**Related:** [#gamepad-and-controller-navigation](#gamepad-and-controller-navigation),
[#audio-ux-and-communication-accessibility](#audio-ux-and-communication-accessibility),
[05-accessibility.md](05-accessibility.md#screen-readers),
[06-aria-widget-reference.md](06-aria-widget-reference.md) (naming/role
concepts transfer).

**Sources:** [Godot 4.5 release notes — screen reader support via AccessKit](https://godotengine.org/releases/4.5/),
[Godot 4.7 release notes — landmark navigation](https://godotengine.org/releases/4.7/),
[AccessKit project](https://accesskit.dev/),
[Game Accessibility Guidelines (vision items)](https://gameaccessibilityguidelines.com/full-list/).

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
in an automation editor), typically authored as **data** in a data-driven
content pipeline, which changes where translation work actually happens:
it's a content-pipeline problem as much as a UI problem.

**When to use:**
- Route every player-facing string — including strings generated from
  data-driven content (an affix's display template, a skill's tooltip
  template) — through the engine's translation system (`tr()` in Godot)
  rather than hardcoding language-specific text anywhere, including inside
  data/content resource files — a hardcoded display string inside a content
  file is invisible to the translation pipeline and silently ships
  untranslated.
- Design number/stat formatting to respect locale conventions (decimal
  separator, digit grouping) per the general data-format guidance in
  [09-content-ux-writing.md](09-content-ux-writing.md#global-content), applied
  to the very large number of raw numeric stat displays stat-heavy games
  have.
- Plan for the automation/Tactics editor's plain-language rule summary
  (§ [22](22-game-ui-and-hud.md#rule-based-automation-editor-tacticsgambit-pattern))
  as a **templated, translatable** construction — a sentence built from
  translatable fragments with correct grammatical agreement per locale,
  using the stack's plural/selection machinery (gettext-style plural forms
  via `tr_n()` plus translation contexts in Godot; ICU MessageFormat
  plural/select rules in general-purpose stacks —
  [09-content-ux-writing.md](09-content-ux-writing.md)) rather than
  string-concatenation in one language, since naive concatenation breaks
  grammar in most non-English target languages.

**When NOT to use / exceptions:**
- Internal-only IDs (item IDs, condition-type enum keys) are correctly never
  translated — only player-facing display text routes through translation.

**Pros:** A translation pipeline designed around the data-driven content
system scales with content volume the same way the content pipeline itself
does — new content ships with translatable fields by construction, not as an
afterthought per item.
**Cons:** Retrofitting translation routing onto content authored with
hardcoded display strings requires touching every content file —
meaningfully cheaper to require translatable fields in the content schema
from the first item/skill/affix authored.

**Implementation guidance:**
- Every data-driven content schema (item, affix, skill, condition, action)
  includes a translation-key field, not a raw display string, from the first
  version of the schema — enforced at content-authoring/validation time.
- Budget UI layout for +30–50% text expansion on labels/tooltips as standard
  practice ([09-content-ux-writing.md](09-content-ux-writing.md#global-content)),
  and specifically stress-test the densest surfaces
  ([dense stat/inventory screens](22-game-ui-and-hud.md#dense-stat-and-inventory-screens),
  the Tactics rule-row layout) against a long-expansion target language early,
  not only against English. Godot ships **pseudolocalization** (a project
  setting that lengthens and accents every localizable string) precisely for
  this test — it also flags hardcoded, untranslatable strings — and, since
  4.5, an in-editor translation live preview.
- Use locale-aware plural/selection handling for any templated text with a
  variable count or subject (damage-type names, target counts in a rule
  summary) rather than string concatenation: in Godot, `tr_n()` plural
  forms and translation contexts — first-class in the CSV workflow since
  4.6 via optional `?plural` and `?context` columns and generated
  translation templates (C# `Tr()`/`TrN()` calls are parsed too); in
  general-purpose stacks, ICU plural/select formatting
  ([09-content-ux-writing.md](09-content-ux-writing.md)).
- Use named placeholders (`{character}`, `{weapon}`) rather than positional
  ones so translators can reorder sentence parts per locale — positional
  placeholders lock word order to the source language.

**Verification checklist:**
- [ ] Every player-facing string, including strings generated from
      data-driven content, routes through the translation system.
- [ ] Content schemas (item/affix/skill/condition/action) carry translation
      keys, not hardcoded display strings, from their first version.
- [ ] Dense UI surfaces are tested against a long-text-expansion target
      language, not only English.
- [ ] Templated/generated text (rule summaries, combat-log lines) uses
      plural/selection formatting (`tr_n()`/contexts or ICU), not
      concatenation; placeholders are named, not positional.
- [ ] Numeric/stat formatting respects locale decimal/grouping conventions.
- [ ] Dense screens pass a pseudolocalization pass (no clipped, overlapping,
      or untranslatable strings).

**Related:** [09-content-ux-writing.md](09-content-ux-writing.md#global-content),
[22-game-ui-and-hud.md](22-game-ui-and-hud.md#rule-based-automation-editor-tacticsgambit-pattern).

**Sources:** [09-content-ux-writing.md](09-content-ux-writing.md#global-content);
[Godot Docs: Internationalizing games](https://docs.godotengine.org/en/stable/tutorials/i18n/internationalizing_games.html)
(`tr()`, `tr_n()`, contexts, placeholders, pseudolocalization, RTL
mirroring); [Godot 4.6 release notes](https://godotengine.org/releases/4.6/)
(CSV `?context`/`?plural` columns, translation template generation, C#
parser support).

---

## Game settings baseline

**Definition:** The industry-consensus baseline for a shipped game's options
menu — the set of player-facing settings that cross-studio guidance (Game
Accessibility Guidelines, Xbox Accessibility Guidelines, AbleGamers APX)
converges on, consolidated here as a review checklist with links to each
topic's own entry.

**Reasoning / Evidence:** GAG's complaint tracking identifies the four most
common accessibility complaints as **remapping, text size, colorblindness,
and subtitle presentation** — all settings-surface issues. APX's framing
explains why the settings menu carries so much weight: it designs for
*barriers* rather than diagnoses, and most barrier-removal ships as player
configurability ("Flexible Displays", "Personal Interface", "Moderation In
All Things"). Two GAG basics are meta-requirements about the menu itself:
all settings must **persist**, and accessibility features must be
**discoverable** (documented in-game and on the store page).

**When to use:** As a review checklist per release. The baseline, grouped:
- **Input** — full remapping ([#input-remapping](#input-remapping));
  sensitivity adjustment; haptics toggle/slider; hold/mash alternatives;
  controller and keyboard both fully supported
  ([#gamepad-and-controller-navigation](#gamepad-and-controller-navigation)).
- **Display/motion** — text size / UI scale
  ([#text-scaling-for-dense-stat-displays](#text-scaling-for-dense-stat-displays));
  contrast/text-color options; colorblind-safe verification (not a
  "colorblind filter" bolted on —
  [#colorblind-safe-rarity-and-element-coding](#colorblind-safe-rarity-and-element-coding));
  per-effect motion toggles (screenshake, motion blur, head-bob, background
  movement) and FOV adjustment in 3D
  ([#reduced-motion-for-combat-and-idle-vfx](#reduced-motion-for-combat-and-idle-vfx));
  camera/juice intensity ([#game-feel-and-juice](#game-feel-and-juice)).
- **Audio/comms** — separate volume channels with mutes; mono toggle;
  subtitle/caption settings (size, background, speaker colors)
  ([#subtitles-and-captions](#subtitles-and-captions),
  [#audio-ux-and-communication-accessibility](#audio-ux-and-communication-accessibility)).
- **Gameplay** — difficulty adjustable *during* play, not only at
  new-game; assist modes (auto-aim, assisted steering, game-speed
  adjustment) as separate toggles rather than one "easy mode"; skip options
  for non-core gameplay elements (GAG intermediate).
- **Saves** — autosave *and* manual save; save-slot thumbnails/context so a
  returning player can recognize where they were (GAG cognitive; APX "Save
  Early, Save Often").

**When NOT to use / exceptions:**
- Not every item fits every genre (FOV in a 2D game, difficulty in a pure
  puzzle/sandbox) — the baseline is a review checklist, not a mandate;
  document *why* an item doesn't apply rather than silently skipping it.
- Settings are not a substitute for good defaults: ship the accessible
  default (readable text, subtitles offered up front, moderate motion) and
  let settings tune from there — don't hide baseline usability behind an
  options hunt.
- Competitive/multiplayer designs may constrain some options (e.g. game
  speed); GAG's answer is preference-based matchmaking, not removing the
  option for everyone.

**Pros:** A finite, industry-vetted checklist that catches the most common
complaints before launch; most items are cheap if planned early.
**Cons:** Settings sprawl has its own cost — every option multiplies the
test matrix and can hide poor defaults; group and search-enable a long
settings menu ([08-navigation-ia.md](08-navigation-ia.md#search)).

**Implementation guidance:**
- Make settings reachable from the pause menu, not only the main menu, and
  apply changes without restart where technically possible.
- Persist everything (per-profile where profiles exist); losing settings on
  update/reinstall is a GAG-documented failure.
- Present an accessibility summary at first launch (or an "accessibility"
  top-level settings group) so features are discoverable — players can't
  benefit from options they never find.
- Include players with disabilities in playtesting (GAG intermediate) —
  the checklist verifies presence, only testing verifies adequacy.

**Verification checklist:**
- [ ] The four top-complaint areas are covered: remapping, text size,
      colorblind-safe coding, subtitle presentation.
- [ ] All settings persist across sessions and are reachable mid-game.
- [ ] Difficulty/assist options are changeable during play and separated
      (not one bundled "easy mode").
- [ ] Autosave + manual save both exist; slots carry recognizable context.
- [ ] Accessibility features are documented in-game and on the store page.
- [ ] Genre-inapplicable items are consciously waived, not forgotten.

**Related:** every entry in this file;
[19-domain-ecommerce-saas.md](19-domain-ecommerce-saas.md) (settings IA and
save-behavior models); [21-agent-checklists.md](21-agent-checklists.md).

**Sources:** [Game Accessibility Guidelines — basic](https://gameaccessibilityguidelines.com/basic/)
and [intermediate](https://gameaccessibilityguidelines.com/intermediate/)
lists; [Xbox Accessibility Guidelines v3.2](https://learn.microsoft.com/en-us/gaming/accessibility/guidelines);
[AbleGamers: Accessible Player Experiences (APX)](https://accessible.games/accessible-player-experiences/)
(22 barrier-first design patterns: 12 Access, 10 Challenge).

---

## Quick reference

| Topic | One-line guidance | Key numbers |
|---|---|---|
| Game feel / juice | Tier intensity to event significance; scale down at high combat speed; layout-safe UI transforms | — |
| Reduced motion | Per-effect toggles for shake/blur/head-bob; never disable informational feedback | ≤3 flashes/sec hard ceiling |
| Gamepad navigation | Full operability; focus graphs match visual grids; glyphs match active device | Deck Verified: default config reaches all content |
| Input remapping | Every discrete action individually rebindable; detect conflicts; no forced hold/mash | #1 most-complained accessibility issue |
| Progressive tutorialization | Teach each system at the moment it first matters; objectives/controls reviewable anytime | — |
| Colorblind-safe coding | Rarity/element = color + shape/icon, never color-only | ~8% color-blind men |
| Text scaling | Reflow dense stats, never truncate numeric values; glyphs scale with text | XAG min 26px console / 18px PC @1080p; Deck ≥9px @1280×800; scale to 200% |
| Subtitles / captions | All speech subtitled; letterboxed background; speaker ID; runtime-rendered | ≥46px @1080p; ≤40 chars/line; ≤2 lines |
| Audio & comms | Separate volume channels; mono toggle; no info by sound alone; accessible chat UI | CVAA (US law, 2019) for chat |
| Screen readers | Structured widgets + accessible names; reuse gamepad focus order | Godot 4.5+ AccessKit (experimental) |
| Localization | Translation keys in content schemas from v1; `tr_n()`/ICU plurals; pseudolocalize | +30–50% text expansion |
| Settings baseline | Cover the top-4 complaints; persist everything; changeable mid-game | — |
