# CHANGELOG — `rasa.module.core`

Reverse-chronological. Each entry is a version bump.

---

## 0.1.0 — INITIAL (2026-07-09)

The canonical **MODULE template** every `module` Element forks from.
Scaffolded from `rasa.domain.core` v1.1.0's tree via
`bin/new-element module core`, then trimmed to the module framing.

### What ships

- **Canon-required files (§4):** `rasa.json`, `VERSION`, `README.md`,
  `CLAUDE.md`, `CHANGELOG.md`, `.gitignore` + `LICENSE` (Apache-2.0).
- **`bin/init`** — the kit-aware canonical installer: applies the
  `rasa.json` install policies, stamps `.claude/rasa.lock.json`, and
  clones the Element into the consumer project's `kit/<element>/` (the
  `/kit` stash) that `/sync` + `/promote` read/write. Copied verbatim
  from domain-core — fully generic.
- **`bin/check-manifest`** (inventory gate) + **`bin/check-shape`**
  (structure gate) — both pure-python, cross-platform, release gates.
- **The generic `/sync` + `/promote` skills** — smart-pull /
  smart-push against the `kit/<element>/` clone. Subject-agnostic;
  the load-bearing upstream↔project loop every module inherits.
- **Subject-agnostic opt-in starter layer** under `content/` (all
  `policy: opt-in`): the meta-skills (`new-skill`, `codify`),
  capability skills (`onboard`, `handoff`, `resume`, `update-docs`),
  universal rules (`contract-rules`, `output-rules`, `authoring-rules`),
  and the enforced authoring templates (`content/templates/`).
- **`seed/`** — 2 canonical templates: `CLAUDE.md.template`
  (placeholder-free, project-owned) + `rasa.lock.json.template`
  (SHA-stamped at install).

### Module-kind specifics

- `kind: "module"`, `name: "rasa.module.core"`, `aliases: ["module-core"]`.
- `requires.parent_kind: ["domain", "tenant"]` — the field unique to
  the `module` kind (a module mounts into a parent domain or tenant).
  `module` is the only kind the schema permits to declare it.
- `rasa.role: "module-template"`; `rasa.shape_pattern: "agnostic"`
  (forks pick their own concrete shape).
- `source.repo: https://github.com/RasaOS/module-core`.

### Relationship to neighbors

- Scaffolded from `rasa.domain.core` (the domain-kind template) — same
  generic starter layer + kit-aware installer; differs in `kind` and
  the `requires.parent_kind` contract.
- Distinct from `rasa.core` (kind: `core`, the L1 shared bones).
- The template for concrete modules like `rasa.module.tasks` (the first
  module-kind Element), which fork this shape and add a capability.

### Verification (at author time)

- `bin/check-manifest` → OK (22 tracked files under content/ + seed/,
  all registered).
- `bin/check-shape` → OK (8 skills, 3 rule files, 0 agents).
- `schema/bin/validate` → OK (rasa.json valid against rasa.schema.v1).
- `bin/init` smoke test into a scratch dir → stamps the lockfile +
  clones `kit/module-core/`.

### TODO before first ship (workspace-level)

- Create `RasaOS/module-core` on GitHub.
- Push `main` + tag `v0.1.0`.
- Register in `elements/REGISTRY.md` + `elements/CHANGELOG.md` (track #2).
