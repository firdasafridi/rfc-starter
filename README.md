# rfc-starter

A skill for AI coding assistants that **drafts** engineering RFCs before formal review. It turns PRD intent (and optional repo and document context) into a structured draft aligned with what `rfc-reviewer` scores—so the first review pass is about substance, not missing sections or unnamed contracts.

## What it does

You give it a PRD or problem statement (and usually some repository context). It walks a fixed authoring sequence and produces a Markdown RFC that includes:

- Mandatory RFC shell (metadata, sections 1–6) plus **Detail** sub-sections tagged to reviewer rubric codes
- Explicit contracts (APIs, schemas, UI props/state, rollout flags) where the template requires them
- Decision tables (chosen option, rejected alternatives, rationale)
- A **Ready for rfc-reviewer: yes | no** hand-off with gaps listed when `no`

It does **not** replace human or `rfc-reviewer` judgment; it front-loads structure so the reviewer can score evidence instead of guessing intent.

## Supported RFC types

Pick **one** template. Types match `rfc-reviewer` so the same draft can flow straight into review.

| Type        | Sub-types (in template)                         | Template focus                                      |
|-------------|--------------------------------------------------|-----------------------------------------------------|
| Frontend    | new-feature, enhancement, performance            | UI contracts, data-fetching, states, rollout (ROL)  |
| Backend     | new-feature, enhancement, tech-improvement     | DDL, APIs, integrity, jobs, migration (MRP)           |
| Full-stack  | FE + BE sub-types as in template                | Cross-layer contract checks and deploy-order matrix |

Each type loads a different skeleton from `templates.md`; rubrics live in `rfc-reviewer` (`rubric-*.md`), not duplicated here.

## Inputs

- **PRD or problem statement:** path (`.md`, `.txt`, `.docx`, `.pdf`), URL, or pasted text
- **Repository context:** paths, snippets, or “read these modules” guidance so decisions can name real files
- **Optional:** RFC type hint — `frontend`, `backend`, or `full-stack` (otherwise inferred from scope)
- **Optional:** extra links — related RFCs, wiki/Confluence pages, ADRs, `THEORY.MD`-style narratives (see `workflow.md` Phase 1); skip when not needed

## Output

Default write path (unless the user asks otherwise):

- `./rfc-draft.md` — written directly into the **current working directory**, no `output/` subfolder

Do not overwrite an existing draft without explicit confirmation.

## Installation

There is no separate published package in this tree—treat **`rfc-starter/`** as the skill folder. Copy or symlink it wherever your tool loads skills from.

Replace `REPO` with the path to your clone of the repository that contains `rfc-starter/`.

### Claude Code / OpenCode

```bash
mkdir -p ~/.claude/skills
cp -R REPO/rfc-starter ~/.claude/skills/rfc-starter
# or: ln -s REPO/rfc-starter ~/.claude/skills/rfc-starter
```

OpenCode often also reads `~/.claude/skills/`; one copy there can cover both.

### Cursor (project-level)

```bash
mkdir -p .cursor/rules
cp REPO/rfc-starter/SKILL.md .cursor/rules/rfc-starter.mdc
cp REPO/rfc-starter/workflow.md REPO/rfc-starter/templates.md REPO/rfc-starter/checklist.md .cursor/rules/
```

### GitHub Copilot (project-level)

```bash
mkdir -p .github
cp REPO/rfc-starter/SKILL.md .github/copilot-instructions.md
# Optionally also copy workflow.md, templates.md, checklist.md into .github/ for context.
```

### Other assistants

Paste or load `SKILL.md` as the instruction source. Keep `workflow.md`, `templates.md`, and `checklist.md` on the workspace path (or paste their paths into the prompt) so the agent can open them.

## Usage

### Claude Code / OpenCode

Invoke the skill your environment uses (for example a slash-command or skill name configured for `rfc-starter`), passing PRD location and optional type:

```
<rfc-starter> [prd-path-or-url] [frontend|backend|full-stack]
```

### Cursor / Copilot / chat UIs

```
Draft an RFC using the rfc-starter skill from this PRD: <path-or-url>
RFC type (if known): <frontend|backend|full-stack>
```

### After drafting

```
Review the RFC at ./rfc-draft.md using rfc-reviewer
```

## Project structure

```
rfc-starter/
├── SKILL.md        # Skill entry: inputs, output path, sequence, quality bar
├── workflow.md     # Phases: PRD → repo → decisions → draft → hardening
├── templates.md    # Frontend / backend / full-stack Markdown templates
├── checklist.md    # Pre-review gate before calling rfc-reviewer
└── README.md       # This file
```

The drafted RFC is written to the **current working directory** (the folder where
you invoke the skill) as `rfc-draft.md`. The skill does not create or rely on an
`output/` subfolder.

## How it works

Authoring follows `workflow.md` (and the shorter numbered sequence in `SKILL.md`):

1. **Intake** — Read PRD/problem; optional external context when it matters.
2. **Repo grounding** — Anchor design in real modules, contracts, and patterns.
3. **Decision closure** — Chosen options, alternatives, rejection rationale (no dangling “TBD”).
4. **Draft from template** — Fill the type-specific skeleton in `templates.md`; every `[REQUIRED: …]` must be satisfied or asked about.
5. **Hardening** — Run `checklist.md`; fix or explicitly `N/A` with one-line reason; set **Ready for rfc-reviewer**.

Then hand off to **`rfc-reviewer`** for scoring, traceability checks, and verdict (PROCEED / HOLD / BLOCKED).

## Design choices

- **Templates mirror the reviewer’s categories** via `_(satisfies: …)_` tags on Detail sections so authors know what evidence the reviewer will look for.
- **Strict placeholders** — `TBD` is forbidden; use `[REQUIRED: …]` prompts and close them before claiming ready.
- **Starter vs reviewer** — Starter optimizes for *complete draft structure*; reviewer optimizes for *scored implementation readiness*. Run both; iterate until PROCEED (or acceptable HOLD with a short fix list).

## Suggested loop

1. Draft with `rfc-starter` → `./rfc-draft.md` (in the current working directory)
2. Review with `rfc-reviewer`
3. Fix gaps in the RFC (and re-run checklist)
4. Re-run review until **PROCEED** (or a documented HOLD you accept)

For reviewer install, scoring bands, and report shape, see [`rfc-reviewer/README.md`]([../rfc-reviewer/README.md](https://github.com/firdasafridi/rfc-reviewer)).
