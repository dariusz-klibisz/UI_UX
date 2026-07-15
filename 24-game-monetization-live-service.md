# Game Monetization & Live-Service Ethics

> Part of the [UI/UX Reference](00-index.md). See index for the full documentation map.

## Scope

This file covers the UX and ethics of paid mechanics in live-service and
free-to-play games: loot boxes and gacha, premium/virtual-currency design,
battle/season passes, storefront scarcity and countdown timers, energy/timer
gates and pay-to-skip design, an ethical in-game storefront baseline, and
minors' spend protection. It is the game-specific companion to
[18-patterns-antipatterns.md](18-patterns-antipatterns.md), which covers
dark patterns generally (its fake-scarcity, drip-pricing, and confirmshaming
entries apply directly to game storefronts and are cross-linked below rather
than repeated) and the general persuasion/manipulation spectrum test used
throughout this file. Cognitive-bias mechanics (loss aversion, sunk cost,
variable-ratio reinforcement) are covered generally in
[02-cognitive-foundations.md](02-cognitive-foundations.md#cognitive-biases-in-ux);
conversion ethics for conventional e-commerce/SaaS in
[19-domain-ecommerce-saas.md](19-domain-ecommerce-saas.md#conversion-ethics).
Settings-menu spend/parental surfaces connect to
[23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md#game-settings-baseline).
Entries follow this file's own dark-pattern template variant (*Definition /
Why it's harmful / Detection heuristic / Ethical alternative / Legal status /
Sources*, matching [18](18-patterns-antipatterns.md)) for manipulative
mechanics, and the corpus's standard template (*Definition / Reasoning /
When to use / When NOT to use / Pros / Cons / Implementation guidance /
Verification checklist / Related / Sources*) for constructive/defensible
patterns.

## Contents

- [Loot boxes and gacha](#loot-boxes-and-gacha)
- [Premium and virtual currency design](#premium-and-virtual-currency-design)
- [Battle passes and season passes](#battle-passes-and-season-passes)
- [Storefront scarcity and countdown timers](#storefront-scarcity-and-countdown-timers)
- [Energy gates and pay-to-skip](#energy-gates-and-pay-to-skip)
- [Ethical in-game storefront](#ethical-in-game-storefront)
- [Minors and spend protection](#minors-and-spend-protection)
- [Quick reference](#quick-reference)

---

## Loot boxes and gacha

**Definition:** Paying real money (directly or via a premium currency) for a
randomized reward from a defined pool, with the odds usually hidden or
buried — loot boxes (item/currency crates), gacha (Japanese-originated
capsule-toy mechanic, often with rarity tiers and "pity" counters), card
packs, prize wheels, and mystery/treasure-chest purchases are the same
mechanic wearing different skins.

**Why it's harmful:** The mechanic reproduces the structural features of
slot-machine gambling — a real-money stake, an uncertain outcome, and a
variable-ratio reward schedule, the single most persistence-inducing
reinforcement pattern in operant-conditioning research — without the age
gating, spend limits, or odds-disclosure regulation that licensed gambling
carries in most jurisdictions. Hidden or absent odds prevent players from
making an informed value judgment before paying; "near-miss" reward displays
(landing one slot short of the jackpot tier) are a documented technique for
prolonging engagement independent of actual reward value. The population at
greatest risk — younger players and players already showing problem-gambling
markers — is also the population empirical research finds spends
disproportionately through these mechanics.

**Detection heuristic:** Is there a real-money (or real-money-convertible)
stake with an uncertain outcome and a defined reward pool? Are exact
probabilities disclosed at the point of purchase, not buried in a support
article? Is there a "pity"/bad-luck-protection counter, and is *it*
disclosed? Does the reveal animation include near-miss framing (visually
landing adjacent to the top-tier reward) more often than probability alone
would produce? Can a player determine, before paying, the worst-case and
average value they're buying?

**Ethical alternative:** Direct purchase of the specific item wanted (a
storefront, not a randomizer) is the non-manipulative default. Where
randomization is kept for design reasons (surprise/collection appeal),
disclose exact per-item probabilities at the point of purchase, cap "pity"
counters and disclose them, and let the "near-miss" reveal reflect the true
odds rather than being tuned to feel closer than it is. Cosmetic-only reward
pools (no gameplay advantage) substantially reduce — though do not
eliminate — the ethical stakes relative to power-affecting loot.

**Legal status:** Contested and jurisdiction-specific, not settled. Belgium's
Gaming Commission ruled in April 2018 that *paid* loot boxes meet its
gambling-law definition of a game of chance (stake + chance + win/loss);
it found *Overwatch*, *FIFA 18*, and *CS:GO* in violation, with penalties up
to €800,000 and up to five years' imprisonment for unlicensed gambling
provision — EA, Blizzard, and Valve subsequently removed paid randomized
mechanics for Belgian accounts. The Netherlands' gambling authority (KSA)
took a similar position in 2018 and fined EA €10 million over FIFA Ultimate
Team packs, but the Netherlands' highest administrative court **overturned**
that ruling in March 2022, holding that opening packs within a broader skill
game is not itself an independent game of chance — a reminder that this
area's case law moves and reverses. China has required probability
disclosure since May 1, 2017 (Ministry of Culture: exact drop rates
published in-game or on the official site, plus 90-day spend-history
publication, and a bar on paying with real currency directly rather than
via converted virtual currency) — though published compliance research
finds disclosure spotty in practice. Japan does not ban gacha/loot boxes
generally, but has banned one specific compound mechanic since July 2012:
**kompu gacha** ("complete gacha," collecting a full set of randomized items
to unlock a further prize), ruled illegal under the Act against Unjustifiable
Premiums and Misleading Representations after a wave of parental complaints.
The US has no federal loot-box law; the ESRB added an
**"In-Game Purchases (Includes Random Items)"** interactive-element label in
April 2020 (covering loot boxes, gacha, card packs, prize wheels, and mystery
awards) and PEGI added a parallel **"Paid Random Items"** descriptor the same
month — both are ratings-board disclosure labels, not purchase-time odds
disclosure, and both are self-regulatory rather than legally mandated. The
UK's DCMS considered legislation and in July 2022 opted for industry
self-regulation instead, with trade body Ukie publishing 11 Industry
Principles in July 2023 (aimed at keeping loot-box purchases unavailable to
minors without parental enablement, and at spend-control transparency for
all players) — the government reserved the right to legislate if progress
proves insufficient.

**Sources:** [CMS Law: Loot boxes — a treasure trove of gambling regulatory issues](https://cms.law/en/aut/publication/loot-boxes-a-treasure-trove-of-gambling-regulatory-issues),
[Belgian Gaming Commission ruling coverage](https://wccftech.com/belgian-gaming-commission-lootboxes-gambling/),
[CMS Law: Dutch court overturns EA loot box ruling](https://cms.law/en/nld/legal-updates/Dutch-court-rules-FIFA-loot-boxes-not-a-game-of-chance-revokes-EA-penalty),
[GameDeveloper: China loot box odds disclosure](https://www.gamedeveloper.com/game-platforms/online-games-will-be-required-to-disclose-random-loot-box-odds-in-china),
[Monolith Law: Kompu gacha and the Prize Display Act](https://monolith.law/en/general-corporate/game-random-complete-illegal),
[ESRB: In-Game Purchases (Includes Random Items)](https://www.esrb.org/blog/in-game-purchases-includes-random-items/),
[PlayStation LifeStyle: PEGI paid random items label](https://www.playstationlifestyle.net/2020/04/14/pegi-in-game-purchases-new-label/),
[GOV.UK: Loot boxes — industry-led protections update](https://www.gov.uk/guidance/loot-boxes-in-video-games-update-on-improvements-to-industry-led-protections),
[18-patterns-antipatterns.md](18-patterns-antipatterns.md#the-deceptive-neutral-persuasive-spectrum).

---

## Premium and virtual currency design

**Definition:** A store-issued currency (gems, crystals, coins) purchased
with real money at fixed bundle sizes, then spent on in-game items — as
distinct from directly pricing items in real currency.

**Why it's harmful:** Decoupling the spend decision (buy currency) from the
purchase decision (spend currency) is a deliberate friction: it obscures the
real-money cost of any given item behind a conversion step, weakening
price-comparison ability the same way unclear unit pricing does in
conventional retail. Bundle sizes are frequently set so that a desired
item's cost falls *between* bundle denominations, forcing an oversized
purchase and stranding a small leftover balance too small to spend on
anything — a deliberate "orphaned balance" that both inflates realized
revenue per transaction and nudges the next purchase (spend the leftover,
buy more currency). Dual-currency systems (a free-earned currency plus a
paid one, both spendable on overlapping items) compound the confusion about
what anything actually costs.

**Detection heuristic:** Can a player see an item's real-money cost without
doing their own mental currency-conversion math? Do bundle denominations
align with common item prices, or consistently force a purchase larger than
needed? Is there a currency balance that's structurally unspendable (too
small for the cheapest item)? Are there two or more currencies with
overlapping, confusing spend rules?

**Ethical alternative:** Show the real-money-equivalent price alongside (or
instead of) the virtual-currency price at every purchase point. Size currency
bundles to cleanly cover common item prices with minimal remainder, and
provide a "top up exactly what you need" option at checkout for an
underfunded purchase rather than forcing the next bundle up. Where a single
currency suffices, prefer it over a dual-currency system.

**Legal status:** Not separately regulated in most jurisdictions as of this
writing, but squarely covered by general consumer-protection law on
transparent pricing (the EU's Unfair Commercial Practices Directive
blacklists practices that obscure the real cost of a transaction) and by the
same "comparison prevention" logic as
[18-patterns-antipatterns.md](18-patterns-antipatterns.md#comparison-prevention-hard-to-compare-pricing).
The UK's 2023 industry loot-box principles explicitly call for clear,
in-context spend information as part of the self-regulatory package (see
[above](#loot-boxes-and-gacha)).

**Sources:** [18-patterns-antipatterns.md](18-patterns-antipatterns.md#comparison-prevention-hard-to-compare-pricing),
[18-patterns-antipatterns.md](18-patterns-antipatterns.md#hidden-costs-and-drip-pricing).

---

## Battle passes and season passes

**Definition:** A time-boxed (typically 4–12 week) track of rewards unlocked
by earning in-game progress points, with a free tier available to all players
and a paid tier unlocking additional/better rewards along the same track.

**Reasoning / Evidence:** The mechanic is genuinely double-edged, which is
why it belongs alongside
[gamification and streaks](18-patterns-antipatterns.md#gamification-and-streaks)
as a *controversial* pattern rather than a flat dark pattern: a well-run pass
gives paying players a transparent, bounded, cosmetic-forward reward for play
they'd do anyway, and a free tier that keeps non-payers meaningfully engaged.
The manipulative twin uses the same [goal-gradient](01-core-principles.md#goal-gradient-effect)
and loss-aversion mechanics to punish rather than reward: passes tuned so
default play-time cannot realistically complete the track before expiry
(manufacturing a "catch-up" purchase), expiring progress that erases sunk
time investment, and reward curves back-loaded so the most desirable items
sit at the far end specifically to maximize the fear of losing accumulated
but unredeemed progress.

**When to use:** A pass whose full track is completable by an average player
within its time window through ordinary play; a permanently-purchasable or
long-window pass rather than one that vanishes with unclaimed progress;
reward curves that don't require near-maximal daily engagement to complete
("no-life" pacing).

**When NOT to use / exceptions:** Passes tuned to require paid "catch-up"
boosts for an average player to complete; passes that erase (rather than
bank or carry forward) partially-earned progress on expiry; stacking
multiple simultaneous passes/tracks to fragment attention and multiply
spend pressure.

**Pros:** Bounded, transparent spend (unlike loot boxes, the player knows
exactly what they're buying and can preview the entire track before paying);
gives non-payers a real free-tier reward path; can be built cosmetic-only,
avoiding pay-to-win entirely.

**Cons:** Time-boxed design inherently creates FOMO pressure even when run
honestly; running multiple concurrent passes (battle pass + event pass +
season pass) reproduces subscription-fatigue dynamics on a shorter cycle;
expiring content trains completionist anxiety independent of intent.

**Implementation guidance:**
- Publish the full reward track and its point requirements before the
  window opens, so players can judge completability before committing.
- Tune completion pacing against realistic average play patterns for the
  target audience, not against the most engaged players' patterns.
- Bank or carry forward unclaimed progress where feasible, rather than
  hard-deleting it at expiry; at minimum, warn clearly and with enough lead
  time before progress is lost.
- Prefer cosmetic-only reward tracks; where a pass includes
  power-affecting rewards, disclose that explicitly ([pay-to-win vs cosmetic-only](#energy-gates-and-pay-to-skip)).
- Limit concurrent overlapping passes; audit total weekly "required" play
  time across all active passes against a realistic session budget.

**Verification checklist:**
- [ ] An average player can complete the free and paid tracks within the
      window through ordinary play, without a "catch-up" purchase.
- [ ] The full reward track is visible before the window opens or purchase
      is required.
- [ ] Unclaimed progress is banked/carried forward, or its loss is
      prominently warned well before expiry.
- [ ] No more than one time-pressured pass is active at once per player,
      or their combined pacing has been audited together.

**Related:** [18-patterns-antipatterns.md](18-patterns-antipatterns.md#gamification-and-streaks),
[01-core-principles.md](01-core-principles.md#goal-gradient-effect),
[02-cognitive-foundations.md](02-cognitive-foundations.md#cognitive-biases-in-ux).

**Sources:** [18-patterns-antipatterns.md](18-patterns-antipatterns.md#gamification-and-streaks)
(the general mechanic this extends).

---

## Storefront scarcity and countdown timers

**Definition:** In-game storefront application of
[fake scarcity and fake urgency](18-patterns-antipatterns.md#fake-scarcity-fake-urgency-and-fake-social-proof):
countdown timers on cosmetic bundles, "limited" skins that reappear in
rotation, and "X players bought this" social-proof claims on the item shop.

**Why it's harmful:** Identical mechanism to the general dark pattern, with
a game-specific twist: a "limited-time" skin that quietly returns to the
rotation next season trains players that urgency claims from this specific
storefront are false, which both erodes trust in genuinely limited
anniversary/collaboration content and pressures real-money decisions with
manufactured rather than real time pressure.

**Detection heuristic:** Does the "limited" item reappear in a later
rotation? Is the countdown timer counting down to an actual removal, or does
it reset/reappear? Are "trending"/"X bought this" claims backed by real
aggregate purchase data or hardcoded?

**Ethical alternative:** Reserve "limited" framing for items genuinely
retired for good (anniversary/collaboration exclusives); use "rotating" or
"available now" framing for a shop that cycles the same catalog — an honest
label costs nothing and preserves credibility for the times scarcity is
real. Real timers only, tied to an actual rotation change.

**Legal status:** Same footing as the general pattern —
[18-patterns-antipatterns.md](18-patterns-antipatterns.md#fake-scarcity-fake-urgency-and-fake-social-proof)
notes false urgency/scarcity claims are illegal under the EU's Unfair
Commercial Practices Directive blacklist and FTC-actionable in the US as
deceptive practices; nothing about a game storefront exempts it.

**Sources:** [18-patterns-antipatterns.md](18-patterns-antipatterns.md#fake-scarcity-fake-urgency-and-fake-social-proof).

---

## Energy gates and pay-to-skip

**Definition:** Mechanics that throttle play with a regenerating "energy"/
"stamina" resource (consumed per action, refilling on a timer) or a
real-time wait (a building/craft/upgrade timer), with a paid option to
refill energy instantly or skip the wait.

**Reasoning / Evidence:** The design intentionally creates a friction point
whose entire purpose is to be sold away, which is a materially different
ethical position from a pass or cosmetic purchase that adds value on top of
an already-complete game: here, the "problem" being sold as a solution was
manufactured by the same product. This sits on a spectrum from mild (a
short, skippable wait with no other cost to skipping) to severe
(pay-to-win, where the paid skip provides a direct competitive/power
advantage over non-paying players in a game with any competitive or social
comparison surface).

**When to use:** A pacing mechanic with a real design purpose beyond
monetization (session-length shaping in an idle/incremental game genre
where players expect it) and a wait/energy cost low enough that skipping is
a genuine convenience, not a necessity — and, critically, one that does not
create a power/competitive advantage over players who don't pay.

**When NOT to use / exceptions:**
- Never in a genre with head-to-head competition or leaderboards without a
  parallel non-paying path to the same power level — pay-to-win skip
  purchases in a competitive context directly damage the non-paying
  majority's experience, not merely their spend.
- Never tune wait/energy costs upward specifically to increase skip-purchase
  pressure after launch (a bait-and-switch on the original pacing promise).

**Pros:** Can genuinely serve session-pacing design goals (idle games,
mobile play-in-short-bursts contexts) without being predatory, if the paid
skip stays a convenience rather than a requirement.
**Cons:** Structurally the hardest monetization mechanic to distinguish from
"we broke the experience on purpose and charge to fix it" — bears the
heaviest burden of proof that the underlying pacing has independent design
merit.

**Implementation guidance:**
- Keep any power-affecting reward reachable through free play within a
  reasonable timeframe; a paid skip should buy convenience/time, not
  power unavailable to non-payers.
- Disclose plainly whether a purchase affects competitive standing
  ("cosmetic only" vs "affects gameplay") — this is the single most
  important line separating defensible pass/skip design from pay-to-win.
- A/B-test pacing changes against player sentiment and churn, not purchase
  conversion alone — a pacing change that increases skip-purchase revenue by
  frustrating free play is a regression, not a win.

**Verification checklist:**
- [ ] The wait/energy mechanic has a stated design purpose beyond selling
      the skip.
- [ ] No competitive/leaderboard advantage is purchasable that a
      non-paying player cannot reach through ordinary play.
- [ ] Cosmetic-only vs gameplay-affecting status is disclosed at the point
      of purchase.
- [ ] Pacing has not been tightened post-launch specifically to increase
      skip-purchase pressure.

**Related:** [#battle-passes-and-season-passes](#battle-passes-and-season-passes),
[02-cognitive-foundations.md](02-cognitive-foundations.md#cognitive-biases-in-ux).

**Sources:** [18-patterns-antipatterns.md](18-patterns-antipatterns.md#the-deceptive-neutral-persuasive-spectrum)
(the disclosure/regret test applied here).

---

## Ethical in-game storefront

**Definition:** The baseline UX for a game's real-money storefront done
honestly: clear real-money pricing, unambiguous purchase confirmation,
durable receipts, an accessible refund path, and visible spend history —
the game-commerce application of general e-commerce and destructive-action
UX doctrine.

**Reasoning / Evidence:** Most of the harm catalogued above is a *deception*
or *pressure* problem, not an inherent objection to games charging money;
a storefront that discloses honestly and confirms deliberately removes the
deception layer even where some tension (real-money spend on virtual goods)
remains a legitimate design choice. This mirrors general checkout and
destructive-action doctrine
([19-domain-ecommerce-saas.md](19-domain-ecommerce-saas.md#conversion-ethics),
[07-forms-and-input.md](07-forms-and-input.md#destructive-action-confirmation))
applied to a context (a game, often played by or with minors, often on a
device with saved payment credentials) where the cost of a mistaken purchase
is proportionally higher.

**When to use:** Every real-money and premium-currency purchase flow,
without exception.

**When NOT to use / exceptions:** None — this is a baseline correctness
requirement, not a judgment call.

**Pros:** Fewer disputed/charged-back transactions, fewer platform
policy strikes (Apple/Google both police deceptive purchase flows), higher
long-term trust and retention than a storefront optimized purely for
short-term conversion.

**Cons:** A genuine confirmation step adds friction to impulse purchases —
by design; this is the point, not a flaw to optimize away.

**Implementation guidance:**
- State the real-money price (or clear real-money equivalent) on every
  purchasable item, at the point of decision, not after currency conversion.
- Require an explicit confirmation step for every real-money transaction,
  separated from browsing/preview interactions by both space and a distinct
  control — the FTC's Epic/Fortnite settlement (December 2022, $245M
  consumer-refund order alongside a $275M COPPA penalty) specifically found
  that placing a preview button close to a purchase button, with no
  confirmation step, caused unintended real-money charges from a single
  misplaced tap.
- Issue a durable receipt (in-app purchase history plus platform receipt)
  for every transaction, and make purchase history reachable from the
  settings/account menu, not only from the platform store app.
- Provide an accessible, working refund request path in-game — don't rely
  solely on the platform's refund flow, which players may not know applies.
- Never lock a disputing player out of already-owned content while a
  chargeback is investigated as a matter of course; the same FTC action
  found this practice (locking accounts after a disputed charge) itself
  harmful.

**Verification checklist:**
- [ ] Real-money price is visible before currency conversion is required.
- [ ] A distinct confirmation step exists for every real-money purchase,
      separated from preview/browse controls.
- [ ] Purchase history and receipts are reachable in-game.
- [ ] A refund request path exists in-game, independent of the platform
      store.
- [ ] Disputed charges do not trigger automatic loss of access to
      previously-owned content.

**Related:** [19-domain-ecommerce-saas.md](19-domain-ecommerce-saas.md#conversion-ethics),
[07-forms-and-input.md](07-forms-and-input.md#destructive-action-confirmation),
[18-patterns-antipatterns.md](18-patterns-antipatterns.md#hard-to-cancel-roach-motel).

**Sources:** [FTC: $245 million Fortnite settlement over dark patterns](https://www.ftc.gov/business-guidance/blog/2022/12/245-million-ftc-settlement-alleges-fortnite-owner-epic-games-used-digital-dark-patterns-charge),
[FTC: Epic Games COPPA and dark-patterns press release](https://www.ftc.gov/news-events/news/press-releases/2022/12/fortnite-video-game-maker-pay-more-half-billion-dollars-over-ftc-allegations).

---

## Minors and spend protection

**Definition:** Design and platform-policy measures that keep real-money
spend decisions with a parent/guardian where the player is a minor:
purchase gating behind parental consent, spend limits, purchase
notifications to a parent account, and age-appropriate storefront design.

**Reasoning / Evidence:** Games are disproportionately played by minors
relative to most other software categories, and unattended real-money
purchase flows on a parent's saved payment method are a well-documented
harm pattern (large, well-publicized accidental child-spend cases predate
current platform protections). This is also directly regulated: in the US,
COPPA governs data collection from under-13 users (the Epic Games
settlement's $275M component was a COPPA enforcement action, separate from
its dark-patterns component above), and the UK's 2023 industry loot-box
principles explicitly target keeping loot-box purchases unavailable to
children/young people absent parental enablement (see
[Loot boxes and gacha § Legal status](#loot-boxes-and-gacha)).

**When to use:** Any game whose install base plausibly includes minors —
which, absent an enforced adult-only distribution channel, is most
consumer games.

**When NOT to use / exceptions:** Verified adult-only distribution contexts
(age-verified platforms/storefronts) reduce but rarely eliminate the need —
shared family devices and accounts remain common even on adult-rated
platforms.

**Pros:** Reduces real financial harm to families and the reputational/
regulatory risk that follows publicized child-spend incidents; parental
controls are increasingly a storefront-listing requirement (Apple/Google
family-management policies), not just good practice.

**Cons:** Adds friction and account-management surface (family linking,
parental dashboards) that must itself be usable, not merely present.

**Implementation guidance:**
- Gate real-money purchases behind a parent/guardian-controlled setting for
  accounts flagged or detected as belonging to a minor, consistent with
  platform family-management APIs (Apple Ask to Buy / Family Sharing,
  Google Family Link) rather than a bespoke, easily-bypassed in-game gate.
- Offer account-level spend limits and purchase notifications to a linked
  parent account, not only a one-time consent at signup.
- Avoid storefront design that specifically targets child psychology (loud,
  countdown-heavy, character-endorsed prompts) for real-money purchases —
  the same honesty standard as the [ethical storefront](#ethical-in-game-storefront)
  above, applied with extra weight given the audience.
- Follow platform policy on marketing/soliciting purchases to child-directed
  apps (both major app-store policies restrict this) as a compliance floor,
  not a ceiling.

**Verification checklist:**
- [ ] Real-money purchases are gated by parental consent/controls where the
      account is a minor's or the app is child-directed.
- [ ] Spend limits and purchase notifications are available to a linked
      parent/guardian account.
- [ ] Storefront design for real-money purchases does not specifically
      exploit child-directed psychological pressure.
- [ ] Platform family-management APIs are used rather than a custom,
      bypassable gate.

**Related:** [#loot-boxes-and-gacha](#loot-boxes-and-gacha),
[#ethical-in-game-storefront](#ethical-in-game-storefront),
[23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md#game-settings-baseline).

**Sources:** [FTC: Epic Games COPPA settlement](https://www.ftc.gov/news-events/news/press-releases/2022/12/fortnite-video-game-maker-pay-more-half-billion-dollars-over-ftc-allegations),
[GOV.UK: Loot boxes — industry-led protections update](https://www.gov.uk/guidance/loot-boxes-in-video-games-update-on-improvements-to-industry-led-protections).

---

## Quick reference

| Topic | One-line guidance | Key numbers |
|---|---|---|
| Loot boxes / gacha | Disclose exact odds; cap and disclose pity counters; prefer direct purchase | BE: gambling since 2018; NL: overturned 2022; CN: odds disclosure since 2017; JP: kompu gacha banned 2012 |
| Premium currency | Show real-money price; size bundles to avoid stranded balances | — |
| Battle/season passes | Track completable by average play; bank unclaimed progress | Cosmetic-only reduces stakes most |
| Storefront scarcity | Real timers only; "limited" reserved for items that never return | Same law as general fake urgency |
| Energy gates / pay-to-skip | Convenience only, never a competitive-advantage purchase | — |
| Ethical storefront | Real-money price + explicit confirm step + receipt + refund path | FTC v. Epic: $245M (dark patterns) + $275M (COPPA), 2022 |
| Minors' spend protection | Platform family-management APIs; spend limits; parent notifications | UK industry principles, 2023 |
