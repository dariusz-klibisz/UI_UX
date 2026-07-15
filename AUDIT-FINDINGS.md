# Correctness Audit — Findings

Audit date: 2026-07-15. Scope: all 25 content files (`00`–`24`) plus `README.md`
and `AGENTS.md`. This log records what was checked, how, and what was fixed.
It is a point-in-time record, not a maintained document — standards and legal
status drift, so treat entries as valid as of the audit date and re-verify
before relying on them long after.

## Method

Per the priority order in the governing plan: (1) structural link/anchor
integrity, full corpus, automated; (2) cheat-sheet numbers and legal/standard
citations, high-risk subset, verified against primary sources; (3)
version-dated claims (engine/spec versions, revision numbers), verified;
(4) remaining inline numbers and prose, spot-checked rather than exhaustively
re-derived — the corpus's existing citation discipline (AGENTS.md: no invented
citations/numbers) was already evident throughout files read in depth this
session, and nothing contradicting that discipline was found.

## 1. Link/anchor integrity — full corpus, automated

Extracted every `](NN-file.md#anchor)` and `](#anchor)` reference across all
25 files (3,317 total links) and verified each anchor exists as a real heading
slug (GitHub kebab-case algorithm, including its non-collapsing treatment of
consecutive spaces left by stripped punctuation like `/` and `&`).

**Result: 0 dangling links out of 3,317.** No fix needed.

## 2. WCAG success-criterion citations — full corpus

Enumerated all unique WCAG SC references (34 distinct criteria, e.g. 1.4.10,
2.4.11, 2.5.8, 3.2.6, 3.3.8/3.3.9, 4.1.3) across every file that cites one.
Cross-checked each number against its correct WCAG 2.2 name and level.

**Result: all 34 correctly numbered and named.** Also verified: WCAG 2.2
became a W3C Recommendation **October 5, 2023**, with **86 total success
criteria** (9 added since 2.1, 4.1.1 Parsing removed) — matches
[05-accessibility.md](05-accessibility.md#wcag-22-structure-and-conformance-levels)
exactly. No fix needed.

## 3. Cheat-sheet and statistic spot-checks

- **Deque automated-detection figure** (`~57%`, [00-index.md](00-index.md),
  [05-accessibility.md](05-accessibility.md#testing-methodology)): verified
  against Deque's Automated Accessibility Coverage Report — **57.38%** of
  issues from first-audit customers, exact match. No fix needed.
- **Core Web Vitals thresholds** ([13-platform-web.md](13-platform-web.md#core-web-vitals)):
  LCP ≤2.5s, **INP ≤200ms**, CLS ≤0.1 at the 75th percentile — verified
  current. Confirmed the corpus already reflects INP's March 2024 replacement
  of FID with no stale FID references remaining. No fix needed.
- **EAA compliance deadline** (June 28, 2025): verified correct and consistent
  across all three mentions ([00-index.md](00-index.md),
  [05-accessibility.md](05-accessibility.md) ×2). No fix needed.
- **Godot version-dated claims** ([23-game-feel-input-and-onboarding.md](23-game-feel-input-and-onboarding.md)):
  4.5 AccessKit screen-reader support (Sept 2025), 4.6 CSV `?plural`/`?context`
  columns + OpenXR 1.1 (Jan 2026), 4.7 `offset_transform_*` + landmark
  navigation (June 2026) — all verified against release notes/coverage, all
  real and correctly attributed to their release. No fix needed.
- **Air Canada chatbot liability case** ([20-ai-product-ux.md](20-ai-product-ux.md#ai-chatbots-as-primary-support)):
  *Moffatt v. Air Canada* (BC Civil Resolution Tribunal, 2024) — verified
  accurate. No fix needed.
- **Game-monetization legal claims** (new [24-game-monetization-live-service.md](24-game-monetization-live-service.md)):
  Belgium 2018 ruling, the Netherlands' 2022 Council of State reversal, China's
  May 2017 disclosure rule, Japan's July 2012 kompu-gacha ban, ESRB/PEGI April
  2020 labels, UK DCMS July 2022 self-regulation response, FTC's Epic/Fortnite
  December 2022 settlement ($275M COPPA + $245M dark-patterns) — all verified
  against primary/authoritative sources during authoring; see that file's own
  citations.
- **New ISO/IEC/EN standards** (added this session — ISO 9241-11/-210,
  ISO/IEC 25010/25065/25066, ISO 24495-1, ISO 21801-1, IEC 62366-1, EN 17161):
  each verified against its ISO/IEC/CEN catalog page before being added; see
  the commit that introduced them.

## 4. Fixed: stale NIST SP 800-63B citation

**Finding:** NIST published **Revision 4** of SP 800-63B in **July 2025**. The
corpus's three citations ([00-index.md](00-index.md),
[07-forms-and-input.md](07-forms-and-input.md#password-and-auth-ux) ×2) all
linked to the Revision 3 URL (`pages.nist.gov/800-63-3/...`), which still
resolves (HTTP 200) but is superseded. Content-wise the guidance was still
accurate — Rev. 4 reinforces the same length-over-composition,
no-mandatory-rotation direction with explicit "SHALL NOT" language — so this
was a citation-currency issue, not a factual error.

**Fix applied:** updated all three citations to the Rev. 4 URL
(`pages.nist.gov/800-63-4/sp800-63b.html`) and noted the revision/date;
added a sentence in [07-forms-and-input.md](07-forms-and-input.md#password-and-auth-ux)'s
Reasoning/Evidence noting Rev. 4's explicit language. **Outcome: fixed.**

## 5. Not exhaustively re-derived

Given the corpus's scale (~17,000 lines, several hundred implementation-
guidance bullets and Pros/Cons statements), this pass did not re-verify every
individual inline claim line-by-line — most of that content is qualitative
UX reasoning rather than an externally-checkable fact, and the corpus already
self-flags genuinely unverifiable claims as such (e.g.
[10-design-systems.md](10-design-systems.md) explicitly labels the "30–50%
faster UI build time" figure as "practitioner folklore / vendor-published
estimates without independent verification"). Categories checked at the
"is this the kind of number that's usually wrong" risk tier above were
prioritized per the plan; no errors surfaced there beyond the NIST citation.

## Summary

| Category | Checked | Errors found | Fixed |
|---|---|---|---|
| Link/anchor integrity | 3,317 links, 100% | 0 | — |
| WCAG SC citations | 34 unique, 100% | 0 | — |
| Cheat-sheet statistics | 6 high-risk figures | 0 | — |
| Version-dated claims | Godot ×3, WCAG 2.2, NIST | 1 (NIST URL stale) | Yes |
| Legal citations (new file 24) | 7 jurisdictions/cases | 0 | — |
| New standards citations | 8 ISO/IEC/EN standards | 0 | — |
