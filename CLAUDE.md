# CLAUDE.md — `rasa.module.core`

Per-repo working contract for Claude sessions opened inside this
folder. Extends the user-level Claude contract and the workspace
contract of the `rasa.tenant.rasaos` tenant; does not override them.
(Referenced by identity, not filesystem path — this template pins no
absolute paths.)

## What you are when you're in this folder

You are working on **`rasa.module.core`** — the lean canonical template
every `module` Element forks from. The Element ships **zero subject
knowledge**; it's the SHAPE every module shares. It also ships a small,
**subject-agnostic opt-in starter layer** under `content/` (the generic
`/sync` + `/promote` skills, meta/capability skills, universal rules,
authoring templates) that references only the Element contract — still
zero subject knowledge. See `README.md` + `content/README.md`.

A **module** is an L1 mountable capability (canon Spec §6): a parent
`domain` or `tenant` pulls it in and mounts it. That's why this
template's `rasa.json` declares `requires.parent_kind: ["domain",
"tenant"]` — the one contract field unique to the `module` kind.

Two distinct concerns:
- **The template** (this folder) — the canonical module shape. The
  `module-core` Element in the `rasa.tenant.rasaos` workspace; destined
  for `RasaOS/module-core`.
- **The forks** (e.g., `module-tasks`, `module-releases`,
  `module-pipelines`) — concrete modules that adopt this shape and
  populate it with a specific capability.

When the template's shape changes, forked modules may need to absorb the
change. Versioning matters: `module-core v0.x → v1.0` will be the
lock-down moment (as it was for `domain-core`).

## Lineage — scaffolded from domain-core

`module-core` was scaffolded from `rasa.domain.core` v1.1.0's tree via
`bin/new-element module core`. That tree already carried the two things
a module template most needs: the **kit-aware `bin/init`** (clones the
Element into the consumer's `kit/<element>/` at install) and the generic
**`/sync` + `/promote`** skills that read/write that clone. Both are
entirely subject-agnostic — they were copied verbatim, not re-authored.
What changed from domain-core: `kind: module`, the `requires.parent_kind`
contract, and the module framing in the docs. The shared starter layer
(skills, rules, templates) is identical by design.

## The generic sync pair — `/sync` + `/promote`

These are the load-bearing skills every module inherits. Keep them in
any fork; they are generic.

- **`/sync`** — smart-**pull**. Reads `.claude/rasa.lock.json`, fetches
  the project's `kit/<element>/` clone, three-way diffs every
  Element-managed file (pinned vs upstream HEAD vs local), classifies
  drift, and proposes per-file pulls. Never auto-commits; never
  overwrites a recorded override or a project-owned file. Advances
  `pinned_sha` only after the diff is applied.
- **`/promote`** — smart-**push** (the reverse). Detects local drift in
  Element-managed files, classifies each (portable / project-override /
  mistake / both-diverged), stages the portable ones into the
  `kit/<element>/` clone on a branch, and opens a PR upstream. Never
  pushes without confirmation.

Both stage everything through `kit/<element>/` — never the consumer
project's `.claude/` directly.

## Source of truth

- **The workspace canon** (the `canon/` tree in the `rasa.tenant.rasaos`
  workspace) — authoritative for everything architectural. Spec §6
  defines the `module` kind + `requires.parent_kind`; ELEMENT_CONTRACT.md
  is the contract module-core conforms to.
- **The workspace `CLAUDE.md`** (the `rasa.tenant.rasaos` tenant's
  working contract) — workspace orientation; the role-split is there.
- **This folder's `README.md`** — what this Element is, how to fork it,
  what's deliberately stripped.
- **This folder's `rasa.json`** — formal declaration.

## Don'ts

- **Don't hardcode filesystem paths — ever.** No absolute or
  home-anchored paths (`~/…`, `/Users/…`, a workspace-specific tree) in
  this template's base-level files or a fork's. Reference other Elements
  by name/repo slug, the canon and workspace by role, and files inside
  the Element by paths relative to its root. A reference must survive a
  `git clone` anywhere. (Codified as a universal rule in
  `content/rules/contract-rules.md` so every fork inherits it.)
- **Don't add subject-specific content here.** This is the template;
  a module's actual capability goes in a fork (`module-tasks`,
  `module-releases`, etc.). If a piece of content is universal to ALL
  modules, it MAY land here — but that's a deliberate template decision,
  not casual authoring.
- **Don't bin/init this Element into itself.** Like every Element source
  repo: `module-core/.claude/` is for sessions; `module-core/content/`
  is the source. They don't duplicate.
- **Don't conflate with `rasa.core`** (kind: `core`). Different Element.
  `rasa.core` is the L1 shared bones every Element depends on
  (vocabulary, output styles, stamp definitions). `rasa.module.core` is
  the template for the `module` KIND. They share a word, not a concern.
- **Don't strip the `/sync` + `/promote` skills from a fork.** They are
  the generic upstream↔project loop every module relies on. A fork keeps
  them; only subject-specific content is fork-authored.
- **Don't bump the major version casually.** A major (→ 1.0.0 lock-down,
  or a later → 2.0.0) is a shape change that forces every fork to
  migrate — only with explicit user direction. Minors stay additive.

## How a version bump works

- **Patch (0.1.0 → 0.1.1)** — typo fix, README/doc clarification,
  minimal bin/init or check-* bug fix. No structural change.
- **Minor (0.1.x → 0.2.0)** — additive: a new generic starter skill or
  rule, a new canonical bin tool, a new universal seed template. Forks
  may adopt opportunistically; not adopting is fine.
- **Major (→ 1.0.0 and beyond)** — the shape lock-down or a breaking
  shape change. Forks REQUIRED to migrate. Avoid without explicit user
  direction.

Each bump: edit `VERSION`, update `rasa.json#version`, write a
CHANGELOG.md entry. Commit + tag + push. Add a row to the workspace's
aggregated elements `CHANGELOG.md` (track #2). Update the workspace
elements `REGISTRY.md` if the row's values change.

## Release gates (run before tagging)

Both are pure-python, cross-platform, and must exit 0:

- **`bin/check-manifest`** — the INVENTORY gate: every git-tracked file
  under `content/` + `seed/` is registered in `rasa.json`, and every
  `rasa.json` `from` path exists.
- **`bin/check-shape`** — the STRUCTURE gate: every authored skill / rule
  / agent under `content/` follows the templated shape (required
  sections, frontmatter, naming). `content/templates/` is exempt.

Plus schema validation of `rasa.json` against the workspace `schema/`
(`bin/validate`).

## What success looks like for this Element

- A new module (e.g., `rasa.module.notes`) can be authored by forking
  this Element + populating `content/`/`seed/` with its capability — no
  other scaffolding needed, and `/sync` + `/promote` work out of the box.
- The shape decisions are documented (README + this CLAUDE.md +
  CHANGELOG) so forks understand what they're conforming to.
- When the shape evolves, forks have a clear migration path (CHANGELOG
  calls out what changed + how to absorb).
- `module-tasks` and later modules align with this shape — proof the
  template is real, not aspirational.
