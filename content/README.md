# `content/` — the universal starter layer

This directory is what a fork of `rasa.domain.core` inherits. As of
v1.1.0 it ships more than empty scaffold: a small, deliberately
**domain-agnostic** starter set every domain can adopt as-is or
delete.

Everything here is **opt-in** (`policy: opt-in` in `rasa.json`). A
fresh fork installs *nothing* from `content/` into a consumer project
until the fork flips a policy to `directory-mirror`/`file-replace` and
populates the directory with its own material. The starter set exists
so a new domain isn't staring at three empty folders — it's a running
start, not a mandate.

## What "universal" means here

The template ships **zero domain knowledge** (see the repo `CLAUDE.md`
and `SHAPE.md`). Nothing in this starter set knows about code, law,
health, or any subject. What it *does* know is the **rasa Element
contract** — the vocabulary every domain shares regardless of subject:

- the lockfile at `.claude/rasa.lock.json` (`pinned_sha`, `overrides[]`)
- the Element primitive (`rasa.json` + `content/` + `seed/` + `bin/`)
- the install policies (`skip-if-exists`, `directory-mirror`, `opt-in`, …)
- the fork ↔ upstream ↔ consumer-project relationship

A skill or rule that only references *those* is universal. The moment
it references a subject (`contracts/`, iOS, matters, patients), it
belongs in a fork, not here.

## What's in the box

```
content/
├── README.md          ← this file (fork-time guide; delete on fork)
├── SHAPE.md           ← toolkit vs structural vs hybrid guide (delete on fork)
├── skills/            ← domain-agnostic starter skills
│   ├── README.md      ← skill-authoring conventions
│   ├── new-skill/     ← meta: scaffold a new skill in this domain
│   ├── codify/        ← meta: promote a session convention into a durable rule
│   ├── sync/          ← meta: absorb upstream Element changes into a fork/project
│   ├── onboard/       ← capability: orient a fresh session to the installed domain
│   ├── handoff/       ← capability: write a session handoff/checkpoint (+ refresh .claude/HANDOFF.md pointer)
│   ├── resume/        ← capability: read the latest handoff, git-calibrate it, pick up the in-flight work
│   └── update-docs/   ← capability: reconcile docs against reality, propose surgical edits
├── rules/             ← universal rule files
│   ├── README.md      ← how rules load + are authored
│   ├── contract-rules.md   ← Element ↔ consumer discipline (the lockfile is truth)
│   ├── output-rules.md     ← house communication style (blunt, calibrated, altitude)
│   └── authoring-rules.md  ← the enforced authoring lifecycle (template → fill → validate)
├── templates/         ← the enforced authoring skeletons (copy these; don't freehand)
│   ├── README.md
│   ├── SKILL.md.template
│   ├── rule.md.template
│   └── agent.md.template
└── agents/            ← Claude subagent definitions
    └── README.md      ← agent-definition conventions (no starter agents shipped)
```

**Shape is enforced, not just documented.** `bin/check-shape` validates
the authored artifacts under `content/` — skills, rules, agents —
against the templated shape (required sections, frontmatter, naming);
`templates/` itself is exempt, since it holds the TODO skeletons. It is
a release gate alongside `bin/check-manifest`'s inventory check. The lifecycle — template →
fill → validate → register → record — is codified in
`rules/authoring-rules.md`, so every fork inherits the discipline, not
just the shapes.

## Adopting it in a fork

1. **Keep what fits, delete what doesn't.** None of it is required.
   A structural-shape domain (see `SHAPE.md`) may keep `sync`/`codify`
   and delete the rest; a toolkit domain likely keeps all of it and
   adds its own on top.
2. **Flip the policy.** In `rasa.json`, change the `content/skills/`,
   `content/rules/`, `content/agents/` entries from `opt-in` to
   `directory-mirror` (or `file-replace`) once you want them installed
   into consumer projects.
3. **Delete the fork-time guides.** Remove this `README.md` and
   `SHAPE.md`; replace with a `content/README.md` describing what
   *your* domain actually ships.
4. **Re-run `bin/check-manifest`** after any add/remove so `rasa.json`
   stays a complete inventory.

## Absorbing later upstream changes

When `domain-core` ships a new starter skill/rule in a future minor,
your fork can pull it via the `sync` skill (or a plain upstream merge).
Minors are additive and non-breaking by contract — adopting is always
optional.
