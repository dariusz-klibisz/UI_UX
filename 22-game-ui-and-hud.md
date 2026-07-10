# Game UI & HUD

> Part of the [UI/UX Reference](00-index.md). See index for the full documentation map.

## Scope

This file covers UI patterns specific to real-time/idle games with dense
itemization and squad/grid combat: HUD layout and information hierarchy during
live action, floating combat text (damage numbers), deep comparison tooltips
for nested item affixes, drag-and-drop inventory management, radial/context
menus, grid-based formation editors, rule-based automation/scripting editors
(the "Tactics"/gambit pattern), and dense stat/inventory screens. General
component behavior is in [11-components-and-overlays.md](11-components-and-overlays.md);
dense-data layout foundations are in
[12-data-tables-dashboards.md](12-data-tables-dashboards.md); platform touch/
input specifics in [15-platform-mobile.md](15-platform-mobile.md) and
[14-platform-desktop.md](14-platform-desktop.md); "juice"/feedback, gamepad
input, and onboarding are in
[23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md).

## Contents

- [HUD layout during live action](#hud-layout-during-live-action)
- [Floating combat text](#floating-combat-text)
- [Deep item comparison tooltips](#deep-item-comparison-tooltips)
- [Drag-and-drop inventory](#drag-and-drop-inventory)
- [Radial and context menus](#radial-and-context-menus)
- [Grid formation editor](#grid-formation-editor)
- [Rule-based automation editor (Tactics/gambit pattern)](#rule-based-automation-editor-tacticsgambit-pattern)
- [Dense stat and inventory screens](#dense-stat-and-inventory-screens)
- [Quick reference](#quick-reference)

---

## HUD layout during live action

**Definition:** The always-visible combat overlay — health/resource bars,
active-skill/cooldown indicators, squad-status icons, speed/pause controls —
that must communicate state at a glance while the underlying scene is
animating and (in an auto-battler) requires no continuous input.

**Reasoning / Evidence:** A HUD competes with the game world for attention
under the same visibility/glanceability constraints as mobile's glanceable-
usage model ([15-platform-mobile.md](15-platform-mobile.md#how-mobile-differs))
— even on desktop, players in an *auto*-battler are watching, not constantly
interacting, so status must be readable without hunting. Nielsen's visibility-
of-system-status heuristic ([01-core-principles.md](01-core-principles.md#1-visibility-of-system-status))
applies directly: the player must always be able to answer "is my squad
winning, and does anything need my attention" without opening a menu.

**When to use:**
- Persistent health/resource bars per unit, positioned near the unit's
  on-grid position (spatially co-located feedback reads faster than a
  separate list — Gestalt proximity, [01-core-principles.md](01-core-principles.md#gestalt-principles)).
- A compact "needs attention" affordance (a icon/banner) for states the
  automation system can't or shouldn't resolve alone — e.g. a wipe risk, a
  rare-drop notification — surfaced without blocking the auto-running combat.
- Speed/pause controls grouped and persistent (not buried), since adjusting
  playback speed is a core AFK-idle interaction, not an edge case.

**When NOT to use / exceptions:**
- Don't surface every numeric stat live in the HUD — reserve the HUD for the
  handful of values a glance must answer; push detail to on-demand
  inspection (tooltips, a stats panel) per progressive disclosure
  ([02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure)).
- Full-screen combat log/details are a deliberate drill-down, not part of the
  ambient HUD.

**Pros:** Fast state comprehension without interrupting the auto-battle;
supports the "watch, don't manage" idle-game feel.
**Cons:** HUD clutter is the most common game-UI failure mode — every added
persistent element taxes the glance budget for every other element.

**Implementation guidance:**
- Health/resource bars: color-coded by state (healthy/warning/critical) using
  a scheme that also satisfies colorblind-safe requirements
  ([23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md#colorblind-safe-rarity-and-element-coding)) —
  never color alone; pair with bar-fill position and, for critical states, an
  icon or pulse.
- Group by information type, not by chance layout: unit status clustered near
  units, squad/global status (currency ticking, timer, wave counter) in one
  consistent HUD region (commonly top or bottom edge, out of the direct-
  manipulation grid area).
- Respect the response-time thresholds when the HUD reflects a live value
  updating rapidly (e.g. DPS counters): update visibly but don't re-flow
  layout on every tick — reflowing text at high frequency reads as noise, not
  information ([04-interaction-design.md](04-interaction-design.md#feedback-and-response-time-thresholds)).

**Verification checklist:**
- [ ] Every persistent HUD element earns its permanent screen space (tested:
      remove it, does gameplay comprehension measurably suffer?).
- [ ] Critical-state indicators (low HP, wipe risk) don't rely on color alone.
- [ ] HUD layout stable at 1x speed and at maximum fast-forward speed (numbers
      updating faster doesn't cause layout thrash).
- [ ] HUD scales/repositions correctly across the game's target aspect ratios
      (desktop widescreen, mobile portrait) without occluding the grid.

**Related:** [01-core-principles.md](01-core-principles.md#1-visibility-of-system-status),
[02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure),
[#floating-combat-text](#floating-combat-text).

**Sources:** [Nielsen: 10 Usability Heuristics](https://www.nngroup.com/articles/ten-usability-heuristics/),
general HUD-design practice literature (GDC "UI/UX" talks corpus).

---

## Floating combat text

**Definition:** Damage numbers, heal amounts, status-effect labels ("Poisoned",
"Crit!") that appear transiently near the unit they affect and fade/rise away
— the primary moment-to-moment feedback channel for combat outcomes in a game
with no manual per-hit input.

**Reasoning / Evidence:** In an auto-battler, the player isn't the one
triggering each hit, so floating text is the feedback loop that makes
automated combat *legible* rather than a black box — it's the game's
equivalent of direct-manipulation feedback ([04-interaction-design.md](04-interaction-design.md#affordances-and-signifiers-in-practice))
even though the player didn't directly cause the individual action. Overuse is
the most common failure: dense squad-vs-squad combat with many simultaneous
hits can produce enough simultaneous floating text to become unreadable noise
rather than information — the exact failure mode
[02-cognitive-foundations.md](02-cognitive-foundations.md#cognitive-load-theory)
predicts when too many simultaneous signals compete for attention.

**When to use:**
- Every damage/heal event, differentiated by type (physical/elemental/heal)
  via color + icon, not color alone (pairs with
  [23](23-game-feel-input-and-onboarding.md#colorblind-safe-rarity-and-element-coding)).
- Critical hits and other statistically-rare, build-relevant events get a
  distinct visual treatment (size, color, or a brief emphasis animation) so
  they're findable in a wall of numbers — this is the moment a min-maxing
  player's build choices become visible.
- Status-effect application/expiry as brief labels, not just numbers, so
  "why is this unit taking damage every tick" is answerable from the HUD
  alone.

**When NOT to use / exceptions:**
- At high combat-speed multipliers (fast-forward), consider throttling or
  aggregating floating text (e.g. batched per-tick totals rather than
  per-individual-hit) rather than rendering an unreadable, performance-costly
  flood — see the frame-budget concern in the Coding library's
  [game-runtime doc](../Coding/13-game-runtime-and-determinism.md#4-object-pooling)
  for the pooling implementation this requires.
- Don't rely on floating text as the *only* record of what happened — pair
  with a persistent combat log for anything the player may want to review
  after the fact (post-fight summary, damage-source breakdown).

**Pros:** Makes automated combat legible and satisfying without requiring
manual input; reinforces build feedback (crits, elemental effectiveness)
exactly when it happens.
**Cons:** Unmanaged volume becomes noise; competes for the same rendering/
frame budget as everything else on screen during the busiest moments.

**Implementation guidance:**
- Motion: brief rise-and-fade (100–300ms rise, longer fade) — short enough
  not to linger and clutter, long enough to register consciously (aligns with
  the functional-animation duration range,
  [04-interaction-design.md](04-interaction-design.md#motion-and-animation)).
- Pool and cap: use object pooling for spawned text instances (never
  instantiate/destroy per hit — see the Coding library's
  [object pooling guidance](../Coding/13-game-runtime-and-determinism.md#4-object-pooling)),
  and define a per-frame/per-unit cap with aggregation (e.g. "+340" summed)
  once volume exceeds the cap.
- Provide a settings toggle for combat-text density/volume (off / reduced /
  full) — this doubles as a reduced-motion accommodation
  ([23](23-game-feel-input-and-onboarding.md#reduced-motion-for-combat-and-idle-vfx)) and a
  performance option for lower-end devices.

**Verification checklist:**
- [ ] Floating text is pooled, not instanced-and-destroyed per event.
- [ ] A density/volume cap with aggregation exists for high-multi-hit moments.
- [ ] Damage-type and crit distinctions are visible without relying on color
      alone.
- [ ] A settings toggle exists for players who find combat text overwhelming
      or want to reduce it for performance/motion-sensitivity reasons.

**Related:** [23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md#colorblind-safe-rarity-and-element-coding),
[04-interaction-design.md](04-interaction-design.md#motion-and-animation).

**Sources:** [02-cognitive-foundations.md](02-cognitive-foundations.md#cognitive-load-theory),
general combat-text-design practice (GDC talks on ARPG/idle-game UI feedback).

---

## Deep item comparison tooltips

**Definition:** A hover/tap-revealed panel showing an item's full stat block —
base stats, rolled affixes with value ranges, set-bonus progress, socketed
gems — with an inline **comparison** against the currently-equipped item in
the same slot, the core information tool for a deep-itemization loot game.

**Reasoning / Evidence:** This is the single highest-stakes information
surface in a Diablo-like itemization system: the player's core decision loop
("is this upgrade worth equipping") depends entirely on being able to compare
nested, multi-value data at a glance. This is a specialized case of the
general tooltip pattern ([11-components-and-overlays.md](11-components-and-overlays.md))
and a specialized case of dense-data comparison
([12-data-tables-dashboards.md](12-data-tables-dashboards.md)), but the
domain-specific need — comparing *rolled ranges*, not just flat values, and
surfacing whether an affix is near its roll ceiling — has no general-UI
equivalent to draw from directly.

**When to use:**
- Any item-bearing UI surface (inventory grid, loot drop, vendor, character
  sheet equipment slot): hover on desktop, tap-and-hold or a dedicated
  "inspect" affordance on touch (hover has no touch equivalent —
  [04-interaction-design.md](04-interaction-design.md#hover-dependence)).
- Delta indicators (▲/▼ with color, not color alone) on every stat line when
  comparing against the equipped item, so the net effect of a swap is
  scannable in one pass rather than requiring the player to mentally diff two
  separate tooltips.
- Roll-quality indication (e.g. a fill bar or "close to max roll" label) for
  affixes with a value range, since "perfect rolls" are an explicit chase
  goal in this itemization design — the tooltip is where that chase becomes
  legible.

**When NOT to use / exceptions:**
- Don't force a full comparison tooltip in contexts with no meaningful
  comparison target (an item in a sell-only vendor context with nothing
  equippable) — show the plain stat block instead of an empty/misleading
  delta.
- On very small touch screens, a full side-by-side comparison may not fit —
  fall back to a stacked/expandable layout rather than truncating stat rows
  silently.

**Pros:** Directly enables the core min-max decision loop; a well-designed
comparison tooltip *is* a meaningful chunk of this game's core-loop UX.
**Cons:** High information density risks overwhelming a new player (mitigate
via progressive disclosure and onboarding — [23](23-game-feel-input-and-onboarding.md#progressive-tutorialization-for-deep-systems));
touch parity (no hover) requires deliberate interaction design.

**Implementation guidance:**
- Structure: item name + rarity color/icon header → base stats → affix list
  (each with current roll value, and roll-range or roll-quality indicator) →
  set-bonus progress (if applicable) → socketed-gem summary → delta-vs-
  equipped block at the bottom or inline per stat row.
- Delta rows: `+12 Attack (▲ +3 vs equipped)` pattern — the absolute new
  value and the signed delta both visible, not delta-only (a player evaluating
  from scratch, e.g. after a respec, needs the absolute number too).
- Rarity and affix-type color-coding must pass the colorblind-safe treatment
  in [23](23-game-feel-input-and-onboarding.md#colorblind-safe-rarity-and-element-coding)
  — rarity tiers are exactly the kind of color-only signal
  ([03-visual-design.md](03-visual-design.md#color-blindness-and-color-independence))
  that fails for ~8% of men with color vision deficiency if color is the only
  cue.
- Touch: tap-and-hold to reveal (consistent with the platform's long-press
  convention, [15-platform-mobile.md](15-platform-mobile.md#gestures)); provide
  an explicit pinned/compare mode for deliberate side-by-side inspection
  rather than relying on hold-to-view for a decision that may take longer
  than a hold gesture comfortably allows.

**Verification checklist:**
- [ ] Every stat/affix row includes both the absolute value and, when a
      comparison target exists, a signed delta.
- [ ] Delta direction (upgrade/downgrade) is legible without color alone.
- [ ] Roll-quality/roll-range indication exists for ranged affixes.
- [ ] Touch has a non-hover-dependent path to the full comparison (tap-and-
      hold or an explicit inspect/pin affordance), not hover-only.
- [ ] Tooltip content reflows/stacks rather than truncating on small screens.

**Related:** [11-components-and-overlays.md](11-components-and-overlays.md),
[12-data-tables-dashboards.md](12-data-tables-dashboards.md),
[03-visual-design.md](03-visual-design.md#color-blindness-and-color-independence).

**Sources:** [04-interaction-design.md](04-interaction-design.md#hover-dependence),
general ARPG itemization-UI practice (Diablo/Path of Exile-style comparison
tooltip conventions, widely documented in game-UX case studies).

---

## Drag-and-drop inventory

**Definition:** Direct-manipulation item management — dragging items between
inventory slots, onto equipment slots, onto crafting/salvage targets, or onto
squad members — as the primary interaction model for a grid-based inventory.

**Reasoning / Evidence:** Direct manipulation ([04-interaction-design.md](04-interaction-design.md#direct-manipulation))
is the strongest affordance match for spatial inventory management, but the
general drag-and-drop guidance in
[04-interaction-design.md](04-interaction-design.md#drag-and-drop) and
[14-platform-desktop.md](14-platform-desktop.md#desktop-drag-and-drop) must be
paired with a **non-drag equivalent** for every action — touch devices,
motor-impaired players, and gamepad-only input all need a functioning
alternative (WCAG 2.5.7 Dragging Movements requires this at Level AA).

**When to use:**
- Item ↔ equipment slot, item ↔ item (swap/stack), item → salvage/sell target,
  item → shared stash: all core drag targets in a deep-itemization game.
- Always paired with a discrete alternative: tap-to-select-then-tap-target,
  a context menu ("Equip", "Salvage", "Move to stash") for every drag action,
  or both.

**When NOT to use / exceptions:**
- Don't make drag the *only* path to any action — this is a hard accessibility
  requirement (WCAG 2.5.7), not a nice-to-have, and it directly serves
  gamepad-only play (§ [23](23-game-feel-input-and-onboarding.md#gamepad-and-controller-navigation)),
  which has no native drag gesture at all.
- Avoid drag for destructive actions (salvage/delete) without a confirmation
  or undo — an accidental drop target is a slip
  ([02-cognitive-foundations.md](02-cognitive-foundations.md#slips-vs-mistakes)),
  not a deliberate choice, and destructive slips need the same protection as
  any other destructive action
  ([04-interaction-design.md](04-interaction-design.md#undoredo-vs-confirmation-dialogs)).

**Pros:** Fast, spatially intuitive for sighted mouse/touch users; matches
players' mental model from the wider ARPG/inventory-management genre
(Jakob's Law, [01-core-principles.md](01-core-principles.md#jakobs-law)).
**Cons:** Unusable by itself for gamepad-only and many assistive-technology
users; touch drag has smaller, less precise hit-targets than desktop
pointer-drag.

**Implementation guidance:**
- Every drag target also accepts a context-menu/action-button path:
  select item (click/tap) → radial or list context menu with the same actions
  a drag would perform (§ [radial and context menus](#radial-and-context-menus)).
- Valid-drop-target highlighting while dragging (equipment slots that accept
  the dragged item's type light up; incompatible slots visibly dim/disable) —
  this is affordance-in-practice feedback
  ([04-interaction-design.md](04-interaction-design.md#affordances-and-signifiers-in-practice)).
- Destructive drop targets (salvage, sell, discard) require either a
  confirmation step or a short undo window, matching the general destructive-
  action doctrine ([04-interaction-design.md](04-interaction-design.md#undoredo-vs-confirmation-dialogs)),
  rather than being one accidental drag away from an item loss the design
  otherwise treats as valuable (perfect rolls, uniques).
- Touch hit-targets for drag handles meet the 44pt/48dp minimum
  ([15-platform-mobile.md](15-platform-mobile.md#touch-targets)) even inside a
  dense inventory grid — shrinking icons below that floor to fit more cells
  trades accuracy for density and should be avoided.

**Verification checklist:**
- [ ] Every drag-driven action has a working non-drag equivalent (WCAG 2.5.7).
- [ ] Valid/invalid drop targets are visually distinguished during drag.
- [ ] Destructive drop targets require confirmation or offer undo.
- [ ] Drag handles/grid cells meet touch-target minimums even at maximum
      inventory density.
- [ ] The full flow (select → equip/salvage/move) is completable via gamepad
      with no drag gesture at all.

**Related:** [04-interaction-design.md](04-interaction-design.md#drag-and-drop),
[05-accessibility.md](05-accessibility.md), [#radial-and-context-menus](#radial-and-context-menus),
[23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md#gamepad-and-controller-navigation).

**Sources:** [WCAG 2.5.7: Dragging Movements](https://www.w3.org/WAI/WCAG22/Understanding/dragging-movements.html),
[04-interaction-design.md](04-interaction-design.md#drag-and-drop).

---

## Radial and context menus

**Definition:** A circular (radial) or list-form (context) menu surfacing an
item's or unit's available actions on selection — the non-drag action path
required alongside drag-and-drop (above), and a fast, spatially-compact
alternative on gamepad and touch.

**Reasoning / Evidence:** Radial menus put every option within a short,
roughly-equal reach from the selection point, which suits gamepad
stick/d-pad input (directional selection, no fine pointer precision needed)
and touch (thumb-reachable arc) better than a linear dropdown list — but they
cap out in scalability faster than a list (roughly 8 items before radial
segments become too thin to hit reliably), which is where the general
Hick's-Law choice-count guidance applies
([01-core-principles.md](01-core-principles.md#hicks-law)).

**When to use:**
- Radial: gamepad/touch item- or unit-action menus with a small, stable
  action count (Equip / Inspect / Salvage / Move — 4–6 typical actions).
- List-form context menu: desktop mouse/right-click contexts, or any action
  set that exceeds the radial's practical item count.
- Both: as the required non-drag alternative for every drag-and-drop action
  (above).

**When NOT to use / exceptions:**
- Don't use a radial menu for a long or frequently-changing action list —
  fall back to a list-form menu (desktop) or a bottom sheet (mobile,
  [15-platform-mobile.md](15-platform-mobile.md)) once past ~6–8 items.
- Avoid radial menus as the *only* path to a critical action needed under
  time pressure unless the segments are large enough to hit reliably in that
  context — since this is an idle/auto-battler (no combat time pressure on
  action selection), this is a smaller concern here than in a twitch-action
  game, but still worth testing at the smallest supported screen size.

**Pros:** Fast, precise, low-travel selection on gamepad/touch; visually
compact; matches players' expectations from the wider ARPG genre.
**Cons:** Doesn't scale past a handful of items; less discoverable for
first-time users than a labeled list (mitigate with a brief onboarding
moment — [23](23-game-feel-input-and-onboarding.md#progressive-tutorialization-for-deep-systems)).

**Implementation guidance:**
- Segment count: keep to ≤8 for a radial menu; beyond that, use a scrollable
  list-form menu instead.
- Every segment has a visible icon *and* label (icon-only fails the same way
  icon-only tab bars fail — [15-platform-mobile.md](15-platform-mobile.md#mobile-navigation-patterns))
  at minimum on first exposure/tutorial, with icon-only acceptable afterward
  behind a settings/familiarity toggle if desired.
- Keyboard/gamepad: segments navigable via d-pad/stick direction with a clear
  focus indicator on the selected segment
  ([05-accessibility.md](05-accessibility.md#focus-visible)); confirm via the
  standard accept button.
- Dismissal: a clear, consistent cancel path (release without selecting, a
  back/cancel button) that never accidentally triggers the nearest segment.

**Verification checklist:**
- [ ] Radial menus capped at ~8 segments; longer lists use a list-form menu.
- [ ] Every segment has icon + label, not icon-only, at least on first use.
- [ ] Full keyboard/gamepad navigation with a visible focus indicator.
- [ ] A reliable cancel/dismiss path that doesn't accidentally select the
      nearest segment.

**Related:** [01-core-principles.md](01-core-principles.md#hicks-law),
[11-components-and-overlays.md](11-components-and-overlays.md),
[#drag-and-drop-inventory](#drag-and-drop-inventory).

**Sources:** [01-core-principles.md](01-core-principles.md#hicks-law),
general radial-menu UX practice (widely used in ARPG/action-RPG inventory and
ability-selection UI).

---

## Grid formation editor

**Definition:** The UI for placing heroes on the positional combat grid —
drag-to-place (with a non-drag equivalent, see above), visual indication of
each cell's targeting/range implications, and a save/preview mechanism so
formation changes can be evaluated before committing.

**Reasoning / Evidence:** This is a domain-specific, high-stakes spatial
editor unique to grid-based squad combat: the player must understand not just
*where* a unit sits but *what that position means* (front-line exposure,
melee reach, AoE-line risk) — a placement decision with combat consequences
that aren't visible from the grid alone unless the UI makes them so. This
combines direct manipulation ([04-interaction-design.md](04-interaction-design.md#direct-manipulation))
with the comparison/preview instincts of dense-data tools
([12-data-tables-dashboards.md](12-data-tables-dashboards.md)).

**When to use:**
- Pre-combat squad setup and any mid-session formation-adjustment screen.
- Show range/threat overlays on hover/select of a unit or cell (e.g. "this
  cell is within reach of typical enemy melee," "this AoE skill's shape
  from this cell") so the placement decision is informed, not guesswork.
- Provide named/save-able formation presets once a player has more heroes
  than fit one squad, so switching between prepared setups doesn't require
  manual re-placement each time (an idle-game-appropriate convenience feature,
  parallel to the automation-preset pattern in
  [23](23-game-feel-input-and-onboarding.md#progressive-tutorialization-for-deep-systems)).

**When NOT to use / exceptions:**
- Don't require drag as the only placement method — tap-source-then-tap-
  destination (or a gamepad cursor + confirm) must work identically, per the
  same drag-alternative requirement as inventory (above).
- Avoid auto-applying a formation change mid-combat without explicit
  confirmation — formation edits belong in the "tinker between fights" loop,
  not as a silent live mutation a player could trigger by accident.

**Pros:** Makes the grid's tactical consequences legible before committing;
reduces trial-and-error respec cycles.
**Cons:** Overlay information (range/threat visualizations) risks cluttering
the grid itself if not carefully scoped to hover/select state rather than
always-on.

**Implementation guidance:**
- Cell affordance: empty cells clearly interactive (hover/focus state per
  [04-interaction-design.md](04-interaction-design.md#component-states));
  occupied cells show the unit's icon/portrait plus a compact class/role
  indicator readable at grid-thumbnail size.
- Placement swap: dragging a unit onto an occupied cell swaps the two units'
  positions rather than silently failing or stacking — this matches the
  "least surprising" outcome and avoids a dead-end interaction.
- Overlay toggle: range/threat overlays appear on select/hover, not
  permanently, to keep the base grid legible; provide an explicit
  "show all" toggle for players who want it persistent (accessibility/
  preference, not a forced default).
- Touch target size for each grid cell meets the 44pt/48dp minimum
  ([15-platform-mobile.md](15-platform-mobile.md#touch-targets)) even at the
  largest supported squad-grid dimensions — if the grid genuinely can't fit
  minimum-size touch targets on the smallest supported device, provide a
  zoom/pan or a list-based alternate placement mode rather than shrinking
  below the floor.

**Verification checklist:**
- [ ] Placement is completable via tap-source/tap-destination and via
      gamepad cursor+confirm, not drag-only.
- [ ] Range/threat overlays are hover/select-scoped by default, with an
      explicit persistent-display option.
- [ ] Dragging onto an occupied cell swaps units predictably (not a silent
      no-op or unexpected stack).
- [ ] Grid cells meet touch-target minimums on the smallest supported device,
      or an alternate placement mode exists when they can't.
- [ ] Formation presets can be saved/named/switched without manual
      re-placement.

**Related:** [04-interaction-design.md](04-interaction-design.md#direct-manipulation),
[#drag-and-drop-inventory](#drag-and-drop-inventory), the tech-agnostic combat-
grid/targeting model in the Design library's
[game architecture doc](../Design/10-game-architecture.md).

**Sources:** [04-interaction-design.md](04-interaction-design.md#direct-manipulation),
general tactics/grid-RPG UI practice (Fire Emblem/XCOM-style placement-UI
conventions, widely documented in genre UX analyses).

---

## Rule-based automation editor (Tactics/gambit pattern)

**Definition:** The UI for authoring conditional combat-automation rules
(priority-ordered "if condition then action" entries per hero) — a domain-
specific rule-builder that must stay approachable for simple cases while
supporting deep, precise conditions for advanced players.

**Reasoning / Evidence:** This is the UI-design analog of the deterministic
rule-evaluation system in the feature spec: a list of prioritized,
reorderable condition→action rules per hero. No general UI-pattern library
covers this domain directly, but it decomposes into well-covered primitives —
a reorderable list ([11-components-and-overlays.md](11-components-and-overlays.md)),
structured form inputs for condition-building
([07-forms-and-input.md](07-forms-and-input.md)), and the progressive-
disclosure principle that determines how much of the system a new player sees
at once ([02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure)).
The core design risk is the classic power-vs-approachability tension named in
the index's conflict-resolution rules ([00-index.md](00-index.md#conflict-resolution-rules)):
*efficiency vs learnability* — support both, never make the expert path the
only path.

**When to use:**
- Per-hero rule lists with explicit priority order (first-match-fires),
  drag-to-reorder with a non-drag reorder alternative (up/down buttons or a
  "move to position" control — same drag-alternative requirement as above).
- A condition-builder using structured pickers (dropdowns/selects for
  condition type and comparator, a value input) rather than free-text —
  free-text rule authoring reintroduces syntax-error risk this domain
  doesn't need to accept, and structured pickers are inherently more
  screen-reader- and gamepad-navigable.
- Live validation and a plain-language rule summary ("If Hero's HP < 30%,
  cast Heal on lowest-HP ally") rendered from the structured rule, so the
  player can read back what they built in natural language, not just as a
  form.

**When NOT to use / exceptions:**
- Don't expose the full condition/action vocabulary on a new player's first
  encounter with the system — start with a small curated set of common
  rules (a few starter presets: "auto-heal below 30%", "focus lowest-HP
  enemy") and reveal the full builder progressively (see
  [23](23-game-feel-input-and-onboarding.md#progressive-tutorialization-for-deep-systems)).
- Avoid silently auto-saving a rule change mid-combat without confirmation if
  the change could meaningfully alter an in-progress fight's outcome — align
  with the general destructive/consequential-change caution in
  [07-forms-and-input.md](07-forms-and-input.md).

**Pros:** Turns an otherwise code-like automation system into an approachable,
visual, screen-reader-navigable tool; the plain-language rule summary is a
strong comprehension aid unique to this pattern.
**Cons:** Genuinely complex to design well — the condition/action vocabulary
can grow large (per the feature spec's own scope-risk note), and every added
condition type is one more thing the picker UI and the plain-language
summary generator must both handle correctly.

**Implementation guidance:**
- Rule row layout: priority number/handle → condition summary → action
  summary → enable/disable toggle → reorder controls → delete. Keep row
  height consistent and touch-target-compliant even in a dense rule list.
- Condition/action pickers: grouped, searchable dropdowns once the vocabulary
  grows past ~10–15 options (mirrors the general search-vs-browse guidance in
  [08-navigation-ia.md](08-navigation-ia.md#search)); group by category
  (HP/resource conditions, status conditions, enemy-action conditions) so
  the list is scannable rather than flat.
- Empty/starter state: a new hero with no rules configured should ship with,
  or offer one-tap access to, a small set of sensible starter presets rather
  than an intimidating blank list (empty-state guidance,
  [04-interaction-design.md](04-interaction-design.md#empty-states)).
- Testing/preview affordance: consider a "preview" or "simulate" mode showing
  what the current ruleset *would* do against a sample scenario, since a
  misconfigured rule (a rule that never fires because an earlier rule always
  matches first) is otherwise a silent, hard-to-diagnose failure — this
  maps directly onto the general error-prevention principle
  ([01-core-principles.md](01-core-principles.md#5-error-prevention)) applied
  to a rule-priority system's most common authoring mistake.
- Import/export or preset-sharing (named in the feature spec as
  "shareable/importable rule presets") should reuse the same plain-language
  summary for review before import, so a player isn't importing an opaque
  rule blob sight-unseen.

**Verification checklist:**
- [ ] Rule reordering has a non-drag alternative (buttons/explicit position
      control), not drag-only.
- [ ] Every rule renders a plain-language summary alongside its structured
      form.
- [ ] Condition/action pickers are structured (dropdown/select), not
      free-text, and screen-reader/gamepad navigable.
- [ ] A curated starter-preset path exists so a new hero doesn't present a
      blank, intimidating rule list.
- [ ] Some form of preview/validation surfaces unreachable rules (a rule that
      can never fire because an earlier rule's condition always matches
      first) rather than failing silently.
- [ ] Imported rule presets are reviewable (via the plain-language summary)
      before being applied.

**Related:** [02-cognitive-foundations.md](02-cognitive-foundations.md#progressive-disclosure),
[07-forms-and-input.md](07-forms-and-input.md),
[00-index.md](00-index.md#conflict-resolution-rules) (efficiency vs
learnability), [23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md#progressive-tutorialization-for-deep-systems).

**Sources:** General rule-builder/no-code-automation UX practice (comparable
in structure to email-filter and workflow-automation rule builders); FF12
Gambit system and Path of Exile flask-automation as the domain's originating
design references (named in the project's own feature spec).

---

## Dense stat and inventory screens

**Definition:** Character-sheet, inventory-grid, and squad-roster screens
presenting many numeric/categorical values at once — the everyday "tinker
between fights" surfaces of a deep-itemization, deep-character-development
game.

**Reasoning / Evidence:** This is a direct application of
[12-data-tables-dashboards.md](12-data-tables-dashboards.md) (table/grid
selection, density rules, sorting/filtering) and
[03-visual-design.md](03-visual-design.md#density-modes) (density modes) to
game-specific content types (items, heroes, affixes) rather than business
data — the general density/scanability guidance transfers directly; the
domain adds item-rarity visual coding and multi-hero comparison as
game-specific needs.

**When to use:**
- Inventory grids: icon-forward grid layout (matches the genre convention,
  Jakob's Law) with rarity-coded borders/backgrounds, stack-count badges, and
  a compact affordance for the deep tooltip (above) on inspect.
- Character/hero sheets: grouped stat sections (base attributes, derived
  combat stats, equipped-gear summary, skill/talent summary) with consistent
  ordering across every hero so comparison across heroes is fast.
- Roster/squad-management screens: sortable/filterable list or grid
  (by class, rarity, power level, role) once the hero collection grows past a
  single screen — reuse the general sortable-table guidance
  ([12-data-tables-dashboards.md](12-data-tables-dashboards.md)) rather than
  inventing a bespoke sort UI.

**When NOT to use / exceptions:**
- Don't default every screen to maximum density — offer a density toggle
  (comfortable/compact) per [03-visual-design.md](03-visual-design.md#density-modes),
  since a new player benefits from more breathing room than a min-maxing
  veteran managing a hundred items.

**Pros:** High information throughput for the player's core "am I better
equipped now" decision loop; consistent structure across screens builds a
transferable mental model (recognition over recall,
[01-core-principles.md](01-core-principles.md#6-recognition-rather-than-recall)).
**Cons:** Easy to overwhelm a new player if every dense screen appears at
full complexity from the first session — pair with progressive
tutorialization (below/[23](23-game-feel-input-and-onboarding.md#progressive-tutorialization-for-deep-systems)).

**Implementation guidance:**
- Rarity coding: consistent color + border-style + (where relevant) icon
  treatment across every screen an item appears on (inventory, tooltip,
  comparison, vendor) — an inconsistent rarity treatment between screens
  breaks the recognition-over-recall benefit.
- Sorting/filtering: default sort that matches the player's most common task
  (e.g. newest-first for loot review, power-level-first for equip decisions),
  with explicit user-controlled overrides persisted per screen.
- Bulk actions (salvage all below rarity X, compare all candidates for a
  slot) follow the general bulk-action safeguards in
  [12-data-tables-dashboards.md](12-data-tables-dashboards.md) — a bulk
  salvage is exactly the kind of destructive, hard-to-reverse action that
  needs a confirmation step given valuable-item loss is a real cost in this
  game's economy.
- Touch density: on mobile, default to a lower density than desktop
  (larger touch targets take priority over information-per-screen —
  [15-platform-mobile.md](15-platform-mobile.md#touch-targets)) even though
  this means more scrolling for the same content.

**Verification checklist:**
- [ ] Rarity/item-type visual coding is consistent across every screen an
      item can appear on.
- [ ] A density toggle exists, or mobile defaults to lower density than
      desktop automatically.
- [ ] Bulk destructive actions (salvage/sell-all) require confirmation and
      show what will be affected before committing.
- [ ] Sort/filter state persists per screen across sessions.

**Related:** [12-data-tables-dashboards.md](12-data-tables-dashboards.md),
[03-visual-design.md](03-visual-design.md#density-modes),
[#deep-item-comparison-tooltips](#deep-item-comparison-tooltips).

**Sources:** [12-data-tables-dashboards.md](12-data-tables-dashboards.md),
[03-visual-design.md](03-visual-design.md#density-modes).

---

## Quick reference

| Topic | One-line guidance | Key numbers |
|---|---|---|
| HUD layout | Persistent = earns its space; detail on demand | — |
| Floating combat text | Pool instances; cap/aggregate at high combat speed | Rise/fade 100–300ms+ |
| Comparison tooltips | Absolute value + signed delta on every stat row | ~8% color-blind men |
| Drag-and-drop inventory | Every drag action needs a non-drag equivalent | WCAG 2.5.7; 44pt/48dp targets |
| Radial/context menus | Radial caps ~8 segments; icon + label | — |
| Grid formation editor | Tap-place + gamepad cursor, not drag-only | 44pt/48dp cells |
| Automation/Tactics editor | Structured pickers + plain-language summary; starter presets | — |
| Dense stat/inventory screens | Consistent rarity coding; density toggle; confirm bulk destructive actions | — |
