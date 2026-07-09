# Domain shape patterns

The `domain` Element kind (canon Spec §6) accepts more than one
internal shape. This document describes the patterns seen in
practice + how to choose.

When you fork `rasa.domain.core` to author a new domain, decide
upfront which pattern fits — or commit to a hybrid. Then delete
this `SHAPE.md` from your fork and replace it with your domain's
own `content/README.md` describing what's actually shipped.

---

## Pattern 1 — Toolkit domain

**Examples in canon:** `rasa.domain.code` (engineering toolkit).

**Shape:** `content/` contains capability-shaped subdirectories:

```
content/
├── skills/           # one folder per skill (each a markdown SKILL.md + scripts)
│   ├── audit/
│   ├── handoff/
│   └── save/
├── rules/            # rule files; project-specific gates + conventions
│   ├── task-rules.md
│   ├── git-flow-rules.md
│   └── ...
├── agents/           # Claude subagent definitions
│   ├── code-reviewer.md
│   └── doc-scanner.md
├── modes/            # operating modes (drive prose)
├── build/            # build pipeline scaffolding
└── tests/            # test infrastructure
```

**When to use it:** the domain ships a workflow toolkit — skills
that Claude invokes, rules that gate work, agents that handle
sub-tasks. Engineering, DevOps, design-system work, technical
writing, etc.

**`capabilities[]` shape:** `<domain>.<verb-or-noun>` enumerating
what the toolkit makes possible. Example: `engineering.task-orchestration`,
`engineering.deploy-pipeline`, `engineering.git-discipline`.

**`seed/`:** often heavy — many project-side templates the install
seeds (`CLAUDE.md.template`, `AUDIT.md.template`, `RELEASES.md.template`,
`ROADMAP.md.template`, `ENV.md.template`, runtime templates, test
templates, etc.).

---

## Pattern 2 — Structural domain

**Examples in canon:** `rasa.domain.legal` (law-firm structural foundation).

**Shape:** `content/` contains domain-organization-shaped subdirectories:

```
content/
├── <concept-1>/      # e.g., firm/ — firm registry + conduct + templates
├── <concept-2>/      # e.g., drives/ — drive registry
└── <concept-3>/      # e.g., members/ — member-workspace template
```

**When to use it:** the domain ships organizational structure —
templates for how data is shaped, how members are registered, how
the user's workspace gets laid out. Less about skill workflow,
more about substrate shape.

**`capabilities[]` shape:** `<domain>.<concept>` enumerating the
structural primitives. Example: `legal.conduct-rules`,
`legal.matter-intake`, `legal.member-workspace`.

**`seed/`:** typically lighter — the structure ships in `content/`;
`seed/` carries just per-project bootstrap (`CLAUDE.md.template`,
the canonical lockfile template). The domain may add a few extras
like `FIRM.md.template`.

**`bin/`:** may include domain-specific operational scripts —
e.g., `register-user` for identity binding, `sync-report` for
reporting.

**Cross-platform:** structural domains often serve broader user
bases (lawyers on Windows, clinicians on Macs). Consider shipping
`.cmd` variants alongside bash scripts. (`rasa.domain.legal` does
this; the canonical `bin/check-manifest` shipped in v1.0+ of
`domain-core` is pure-Python for that reason.)

---

## Pattern 3 — Hybrid

**Examples in canon:** none yet, but a likely-future shape (e.g.,
`rasa.domain.health` might ship both clinical-workflow skills AND
patient-record structural templates).

**Shape:** mix of toolkit subdirs (`skills/`, `rules/`, `agents/`)
plus domain-organization subdirs (e.g., `patients/`, `prescriptions/`).
Allowed; no contract violation. Document clearly in the fork's
`content/README.md` which is which.

---

## Which pattern does `rasa.domain.core` v1.0.0 commit to?

**None.** As of v1.1.0 the template ships a small, domain-agnostic
**opt-in starter layer** under `content/skills/`, `content/rules/`,
`content/agents/` (see `content/README.md`) — meta-skills, universal
rules, and authoring conventions that reference only the Element
contract, never a subject. It's a running start, not a commitment to a
pattern: a toolkit fork keeps most of it, a structural fork keeps the
Element-mechanics skills (`sync`, `codify`) and deletes the rest. All
opt-in — nothing installs into a consumer until a fork flips the
policy. Fork-time, delete this `SHAPE.md` and `content/README.md` and
replace with your domain's own `content/README.md`.

What the template enforces is the **Element primitive** (`rasa.json`
+ `content/` + `seed/` + `bin/init`) per canon, not the internal shape
of `content/`.

---

## See also

- Canon Spec §6 — the `domain` kind definition
- Canon ELEMENT_CONTRACT.md §2 — kinds table
- Canon ELEMENT_CONTRACT.md §7 — install policies (apply equally to both shapes)
- The `rasa.domain.code` Element's `content/` (`RasaOS/domain-code`) — Pattern 1 reference (toolkit)
- The `rasa.domain.legal` Element's `content/` (`RasaOS/domain-legal`) — Pattern 2 reference (structural)
