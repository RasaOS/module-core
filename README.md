# RasaOS Module · Core

**Canonical name:** `rasa.module.core`
**Repo / folder:** `module-core`
**Kind:** `module` (canon Spec §6)
**Contract:** Element Contract v1.3.0
**Version:** 0.1.0 (initial — the canonical module template)
**Status:** **v0.1.0 — initial.** The lean, subject-agnostic template every `module` Element forks from. Scaffolded from `rasa.domain.core` v1.1.0's tree (which already carries the generic `/sync` + `/promote` skills and the kit-aware `bin/init`), then trimmed to the module framing.

## What this is

The canonical **MODULE template** every `module` Element forks from. A
module is an **L1 mountable capability** — a portable, opt-in unit that a
parent `domain` or `tenant` pulls in and mounts (canon Spec §6;
`requires.parent_kind` defaults to `["domain", "tenant"]`). `module-tasks`
was the first concrete module; every module after it should fork this
shape.

This Element ships **zero subject knowledge** — it's the SHAPE every
module shares, plus a small **subject-agnostic opt-in starter layer**
under `content/` that references only the rasa Element contract
(lockfile, `pinned_sha`, `overrides[]`, install policies, the `/kit`
stash).

The two load-bearing generic skills it carries:

- **`/sync`** — smart-**pull** upstream Element changes into a consumer
  project, three-way diffing against the project's `kit/<element>/`
  clone. One direction: upstream → here. Never auto-commits.
- **`/promote`** — smart-**push** a local improvement back to the
  Element's upstream repo, staging through the same `kit/<element>/`
  clone and opening a PR. The reverse of `/sync`.

Both operate against the persistent **`kit/<element>/`** git clone that
`bin/init` drops into the consumer project at install time (the `/kit`
stash). They are entirely generic — copy them into any fork verbatim.

## What this isn't

- **Not `rasa.core`** (kind: `core`) — that's the L1 shared bones every
  Element depends on (vocabulary, output styles, stamp definitions).
  Different concerns; both named "core" for canonical positioning.
- **Not a concrete module** — `module-tasks`, `module-releases`,
  `module-pipelines`, etc. are concrete modules that forked a shape and
  populated it with a capability. `module-core` holds no capability; it
  is the template they fork.
- **Not `domain-core`** — that's the template for the `domain` kind.
  `module-core` was scaffolded from domain-core's tree (same generic
  starter layer + kit-aware installer) but declares `kind: module` and
  the module `requires.parent_kind` contract.

## How to fork this as a new module

```sh
# 1. Clone the template
git clone https://github.com/RasaOS/module-core.git module-<yourname>
cd module-<yourname>

# 2. Re-point git remote
git remote set-url origin git@github.com:<your-org>/module-<yourname>.git

# 3. Edit rasa.json
#    - name: rasa.module.<yourname>
#    - version: 0.1.0  (already reset)
#    - description: <what capability this module mounts>
#    - capabilities[]: your module's capability names
#    - aliases[]: your module's alternate names
#    - requires.parent_kind: keep ["domain","tenant"] or narrow it

# 4. Populate content/ with the module's capability (skills / rules /
#    a portable spine + templates). Keep the /sync + /promote skills.

# 5. Add seed/ templates for any project-owned files the module needs
#    (e.g. module-tasks seeds a project-owned done-gate + tasks/ ledger).

# 6. Delete content/README.md + content/SHAPE.md (fork-time guides);
#    replace with a content/README.md describing what YOUR module ships.

# 7. Flip the relevant element.files[] policies from opt-in to
#    directory-mirror / file-replace so the module installs.

# 8. bin/check-manifest && bin/check-shape  (both must exit 0)

# 9. Bump version, commit, tag v0.1.0, push; register in
#    elements/REGISTRY.md + elements/CHANGELOG.md
```

## File layout

```
module-core/
├── rasa.json              # Connection Contract — kind=module, contract_version=1.3.0,
│                          #   requires.parent_kind=["domain","tenant"]
├── VERSION                # semver, currently 0.1.0
├── README.md              # this file
├── CLAUDE.md              # per-repo working contract for sessions on this template
├── CHANGELOG.md           # version history
├── LICENSE                # Apache-2.0
├── .gitignore             # macOS + editor cruft + .claude/rasa.lock.json
├── bin/
│   ├── init               # canonical installer: applies rasa.json policies, stamps the
│   │                      #   lockfile, and clones the Element into kit/<element>/ (the /kit stash)
│   ├── check-manifest     # the INVENTORY gate: rasa.json is a complete inventory of content/+seed/
│   └── check-shape        # the STRUCTURE gate: content/ conforms to the templated shape
├── content/              # subject-agnostic opt-in starter layer — fork keeps/deletes
│   ├── README.md          # guide to the starter layer; fork-time (deleted on fork)
│   ├── SHAPE.md           # shape-pattern guide (toolkit/structural/hybrid); fork-time
│   ├── skills/            # generic starter skills + authoring conventions
│   │   ├── README.md
│   │   ├── sync/          # generic: smart-PULL upstream changes via kit/<element>/
│   │   ├── promote/       # generic: smart-PUSH local improvements upstream via kit/<element>/
│   │   ├── new-skill/     # meta: scaffold a new skill
│   │   ├── codify/        # meta: promote a session convention into a durable rule
│   │   ├── onboard/       # capability: orient a session to the installed module
│   │   ├── handoff/       # capability: durable session checkpoint
│   │   ├── resume/        # capability: read-side of handoff — pick up in-flight work
│   │   └── update-docs/   # capability: reconcile docs against reality
│   ├── rules/             # universal rules + authoring conventions
│   │   ├── README.md
│   │   ├── contract-rules.md   # Element <-> consumer discipline (lockfile is truth)
│   │   ├── output-rules.md     # house communication style
│   │   └── authoring-rules.md  # the enforced authoring lifecycle
│   ├── templates/         # enforced authoring skeletons (skill / rule / agent)
│   └── agents/            # subagent conventions (no starter agents — subject-specific)
│       └── README.md
└── seed/
    ├── CLAUDE.md.template       # per-project Claude session contract (placeholder-free)
    └── rasa.lock.json.template  # canonical lockfile shape (substituted at install)
```

## Required files per canon ELEMENT_CONTRACT §4

All present:

| File | Status |
|---|---|
| `rasa.json` | ✓ (kind=module, requires.parent_kind) |
| `VERSION` | ✓ |
| `README.md` | ✓ |
| `CLAUDE.md` | ✓ |
| `CHANGELOG.md` | ✓ |
| `.gitignore` | ✓ |
| `content/` | ✓ (subject-agnostic opt-in starter layer) |
| `seed/` | ✓ (2 canonical templates) |
| `bin/init` | ✓ (applies policies, stamps lockfile, clones the `/kit` stash) |

## What module-core ships

**Universal (every module Element):**
- Canon-required files (§4): `rasa.json`, `VERSION`, `README.md`,
  `CLAUDE.md`, `CHANGELOG.md`, `.gitignore`
- `LICENSE` (Apache-2.0)
- `bin/init` — canonical installer reading rasa.json policies + cloning
  the `kit/<element>/` stash that `/sync` + `/promote` read/write
- `bin/check-manifest` — the inventory gate (pure-python, cross-platform)
- `bin/check-shape` — the structure gate (pure-python, cross-platform)
- `seed/` with 2 canonical templates: `CLAUDE.md.template` +
  `rasa.lock.json.template`

**Subject-agnostic opt-in starter layer** (`policy: opt-in` — registered
for check-manifest coverage, but not installed until a fork flips the
policy):
- **Generic sync skills** — `content/skills/sync/`, `.../promote/`
  (smart-pull / smart-push against `kit/<element>/`; copy verbatim)
- **Meta-skills** — `content/skills/new-skill/`, `.../codify/`
- **Capability skills** — `content/skills/onboard/`, `.../handoff/`,
  `.../resume/`, `.../update-docs/`
- **Universal rules** — `content/rules/contract-rules.md`,
  `.../output-rules.md`, `.../authoring-rules.md`
- **Enforced authoring templates** — `content/templates/` (skill / rule /
  agent skeletons); `bin/check-shape` is the enforcement and a release gate
- `content/agents/` ships conventions but **no** starter agents —
  subagents are inherently subject-specific

## What module-core deliberately does NOT ship

- **Subject-specific** skills, rules, or agents (every module authors its
  own capability — the starter layer is *subject-agnostic* only, all opt-in)
- Any concrete capability (task lifecycle, release flow, test harness — those
  are what forks like `module-tasks` add on top)
- A committed install (all `content/` entries are `opt-in`; forks flip the policy)

## Versioning intent

- **Patch (0.1.x)** — typo fix, README/doc clarification, bin/init or
  check-* bug fix. No structural change.
- **Minor (0.1.x → 0.2.0)** — additive: a new generic starter skill/rule,
  a new canonical bin tool, a new universal seed template. Forks may adopt
  opportunistically; not adopting is fine.
- **Major (→ 1.0.0 and beyond)** — the shape lock-down (as `domain-core`
  did at its v1.0.0) or a later breaking shape change. A major forces forks
  to migrate — only with explicit user direction.

## See also

- Canon Spec §6 (Element kinds — the `module` definition + `requires.parent_kind`)
- Canon ELEMENT_CONTRACT.md (every Element follows this)
- The `rasa.domain.core` Element / `RasaOS/domain-core` (the domain-kind
  template this was scaffolded from — same generic starter layer)
- The `rasa.module.tasks` Element / `RasaOS/module-tasks` (the first
  concrete module — a worked example of forking a module shape)
- The workspace elements registry (`REGISTRY.md` — live snapshot of every
  Element under workspace management)

## License

Apache 2.0 — see `LICENSE`. Matches the convention used across the RasaOS
Element lineage.
