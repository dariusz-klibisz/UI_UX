# AGENTS.md — Guidance for AI Coding Agents

This file tells AI coding agents how to read, search, and edit this repository.
Humans are welcome to read it too. For orientation, start with
[`README.md`](README.md) and the deep index at [`00-index.md`](00-index.md).

## What this repository is

- A **UI/UX design knowledge base** — a citation-backed reference library of design
  principles, patterns, platform conventions, and state-of-the-art best practices.
  There is **no application code, build, or test suite** here — only Markdown.
- It is meant to be **referenced from other repositories** while designing,
  implementing, or reviewing user interfaces, not run. The value is in the
  reasoning: every entry states **what** and **why**, with evidence, concrete
  numbers, and **When to use / When NOT to use** conditions.

## How to use it (when working in another repo)

1. **Route via [`00-index.md`](00-index.md) first.** Its decision tree ("I'm working
   on X — what do I read?") and alphabetical topic lookup map a task to the exact
   file and section. **Load only what you need**, not the whole library.
   - Platform work → [`13`](13-platform-web.md)–[`16`](16-platform-cli-tui.md).
   - Component choice / UI review → [`21-agent-checklists.md`](21-agent-checklists.md)
     matrices and acceptance criteria, then the deep-dive file it links to.
   - Hard values (contrast, touch targets, timings) → the key-numbers cheat sheet in
     [`00-index.md`](00-index.md) and each file's closing **Quick reference** table.
2. **Follow the agent operating model** in [`00-index.md`](00-index.md): identify
   goal, platform, input methods, and risk level first; read platform-agnostic
   principles, then platform specifics; use decision questions to pick a pattern;
   verify with the checklists.
3. **Resolve conflicts** in the documented order: accessibility wins over visual
   style; platform convention usually wins over brand; minimalism must not hide
   necessary controls; efficiency and learnability both; user task success and
   reversibility over novelty.
4. **Respect risk levels.** High-risk flows (legal, financial, medical, destructive,
   irreversible) require stronger error prevention, confirmation, review, undo,
   auditability, and accessibility validation.
5. **Apply judgement, not mechanically.** Entries encode applicability conditions in
   *When to use / When NOT to use*; checklists are decision support, not a substitute
   for testing with users. AI-generated review verdicts require human expert review
   ([`17-ux-process-research.md`](17-ux-process-research.md)).

## Structure & conventions

- Files are numbered `00`–`21` (see the File Map in [`README.md`](README.md)). The
  numbering is a stable ordering; **do not renumber or rename files** without
  updating every cross-reference.
- [`00-index.md`](00-index.md) is the canonical index: agent operating model,
  decision trees, topic lookup, key-numbers cheat sheet, glossary, and the annotated
  bibliography.
- Most entries follow the **standard template**:

  > Definition → Reasoning/Evidence → When to use → When NOT to use → Pros → Cons →
  > Implementation guidance → optional Decision questions / Failure modes /
  > Verification checklist → Related → Sources

  Dark-pattern entries in [`18`](18-patterns-antipatterns.md) substitute *Why it's
  harmful / Detection heuristic / Ethical alternative / Legal status*.
- **Every content file ends with a "Quick reference" table** (topic | one-line
  guidance | key numbers) — keep it in sync with the entries above it.
- Cross-links use relative paths with GitHub-style kebab-case anchor fragments,
  e.g. `[04 › Optimistic UI](04-interaction-design.md#optimistic-ui)`.

## Rules for editing / extending

When modifying content in this repo:

1. **Preserve the entry template and heading levels.** New entries follow the same
   field structure so the docs stay scannable and chunk cleanly.
2. **Keep cross-links valid.** There are ~2,000 internal links and anchors. If you
   rename or reword a heading, update every link that targets it — verify with a
   link/anchor checker before committing.
3. **Update the index and README.** When adding, removing, or restructuring content,
   reflect it in [`00-index.md`](00-index.md) (file map, decision trees, topic
   lookup, cheat sheet) and the File Map in [`README.md`](README.md).
4. **Cite real sources.** Keep the standards baseline current (WCAG 2.2, Material 3,
   Apple HIG, Fluent 2, GNOME HIG, clig.dev, NIST SP 800-63B). Do not invent
   citations, statistics, or WCAG success-criterion numbers; verify numbers against
   primary sources before adding them.
5. **State trade-offs.** Every pattern gets *When NOT to use* and *Cons*; if an entry
   presents only upsides, it is incomplete. Hedge practitioner folklore explicitly.
6. **Keep the Quick reference tables in sync** with any entry you add or change.
7. **Match the existing tone.** Neutral, concise, technical. No marketing language,
   no emojis, no first-person voice.

## What not to do

- Do not add code, tooling, dependencies, or a build/test system — this is a
  docs-only repository.
- Do not reproduce copyrighted standards or guidelines wholesale (WCAG text, HIG
  pages, NN/g articles); summarize and link instead.
- Do not break the index or leave dangling links / anchors.
- Do not renumber files, or convert the rationale-driven prose into a context-free
  rulebook — the *When / When-not / Cons* framing is the point.
- Do not soften the accessibility or dark-pattern guidance for convenience;
  accessibility requirements and legal statuses must stay accurate.

## Quick checklist before committing a docs change

- [ ] Entry template and heading levels preserved.
- [ ] All internal links and anchors still resolve (run a link checker).
- [ ] The file's closing Quick reference table reflects the change.
- [ ] [`00-index.md`](00-index.md) and [`README.md`](README.md) updated if structure
      or content changed.
- [ ] New claims backed by real, verifiable sources; numbers checked against
      primary standards.
- [ ] Trade-offs stated; tone neutral, concise, consistent with the rest.
