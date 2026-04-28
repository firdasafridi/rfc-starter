# rfc-starter

A skill for AI coding assistants that **drafts agent-execution-ready RFCs**:
self-contained packages an autonomous coding agent can read, understand the
existing codebase from, and then execute against — without follow-up
clarification meetings.

## What it does

You give it a PRD or problem statement (and usually some repository context).
It walks a fixed authoring sequence and produces a Markdown RFC built around
three pillars:

1. **Repo as documentation** — the RFC's §2 Detail 2.0 Repo Reading Guide
   anchors every design decision in real files: a mermaid Repo Map, an
   Existing Code Anchors table, patterns to follow with reference file paths,
   and a numbered Reading Order the agent walks before writing code.
2. **Mermaid-first diagrams** — architecture (`flowchart`), sequence
   (`sequenceDiagram`), data model (`erDiagram`), status lifecycle
   (`stateDiagram-v2`), and branch/skip flows are all mermaid blocks the agent
   can read and edit in-place. No draw.io / Lucidchart / Excalidraw exports as
   the primary spec.
3. **Executable call-to-action** — the §4 Agent Execution Plan emits ordered
   chunks with exact files, exact commands, and verifiable acceptance criteria,
   plus a §4 Verification & Rollback Recipe (pre-merge commands, post-deploy
   signals, ordered rollback steps).

The mandatory Confluence shell (metadata table + sections **1. Overview**,
**2. Technical Design**, **3. High-Availability & Security**, **4. Backwards
Compatibility and Rollout Plan**, **5. Concern, Questions, or Known
Limitations**, **6. Comment logs**) is preserved. Agent-execution content lives
inside those sections, plus a **§7 Ready for agent execution** gate.

It does **not** replace human judgment; it front-loads structure so the
executing agent operates on facts, not guesses.

## Supported RFC types

Pick **one** template.

| Type        | Sub-types (in template)                         | Template focus                                                            |
|-------------|--------------------------------------------------|---------------------------------------------------------------------------|
| Frontend    | new-feature, enhancement, performance            | UI contracts, data-fetching, states, mermaid component+state, exec plan   |
| Backend     | new-feature, enhancement, tech-improvement       | DDL, APIs, integrity, jobs, mermaid component+ER+state+sequence, exec plan |
| Full-stack  | FE + BE sub-types as in template                 | Cross-layer contract checks + deploy-order matrix + exec plan per layer    |

Each type loads a different skeleton from `templates.md`.

## Inputs

- **PRD or problem statement:** path (`.md`, `.txt`, `.docx`, `.pdf`), URL, or pasted text
- **Repository context:** paths, snippets, or "read these modules" guidance so
  decisions can name real files
- **Optional:** RFC type hint — `frontend`, `backend`, or `full-stack`
  (otherwise inferred from scope)
- **Optional:** extra links — related RFCs, wiki/Confluence pages, ADRs,
  `THEORY.MD`-style narratives (see `workflow.md` Phase 1); skip when not needed

## Output

Default write path (unless the user asks otherwise):

- `./rfc-draft.md` — written directly into the **current working directory**,
  no `output/` subfolder

Do not overwrite an existing draft without explicit confirmation.

## Installation

There is no separate published package in this tree — treat **`rfc-starter/`**
as the skill folder. Copy or symlink it wherever your tool loads skills from.

Replace `REPO` with the path to your clone of the repository that contains
`rfc-starter/`.

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

Paste or load `SKILL.md` as the instruction source. Keep `workflow.md`,
`templates.md`, and `checklist.md` on the workspace path (or paste their paths
into the prompt) so the agent can open them.

## Usage

### Claude Code / OpenCode

Invoke the skill your environment uses (for example a slash-command or skill
name configured for `rfc-starter`), passing PRD location and optional type:

```
<rfc-starter> [prd-path-or-url] [frontend|backend|full-stack]
```

### Cursor / Copilot / chat UIs

```
Draft an agent-execution-ready RFC using the rfc-starter skill from this PRD: <path-or-url>
RFC type (if known): <frontend|backend|full-stack>
```

### After drafting

The RFC closes itself with **§7 Ready for agent execution: yes/no**. If `yes`,
hand it directly to your executing agent.

Optional second pass for an external review score:

```
Review the RFC at ./rfc-draft.md using rfc-reviewer
```

## Project structure

```
rfc-starter/
├── SKILL.md        # Skill entry: inputs, output path, sequence, quality bar
├── workflow.md     # Phases: PRD → repo-as-docs → decisions → mermaid+exec draft → hardening
├── templates.md    # Frontend / backend / full-stack templates (mermaid-first, exec-plan)
├── checklist.md    # Pre-handoff agent-execution readiness gate
└── README.md       # This file
```

The drafted RFC is written to the **current working directory** (the folder
where you invoke the skill) as `rfc-draft.md`. The skill does not create or
rely on an `output/` subfolder.

## How it works

Authoring follows `workflow.md` (and the shorter numbered sequence in
`SKILL.md`):

1. **Intake** — Read PRD/problem; optional external context when it matters.
2. **Repo grounding (treat the codebase as documentation)** — Anchor design in
   real modules, contracts, and patterns. Build the Repo Map (mermaid),
   Existing Code Anchors table, Patterns to Follow table, Reading Order, and
   Existing Contracts (reuse / extend / new-with-justification).
3. **Decision closure** — Chosen options, alternatives, rejection rationale
   (no dangling "TBD").
4. **Draft from template (mermaid-first, agent-executable)** — Fill the
   type-specific skeleton in `templates.md`; replace every `[REQUIRED: …]`;
   render every diagram as a mermaid block; produce an Agent Execution Plan
   with files + commands + verifiable acceptance criteria; produce a
   Verification & Rollback Recipe.
5. **Hardening** — Run `checklist.md`; fix or explicitly `n/a` with one-line
   reason; set **Ready for agent execution**.

After step 5, optionally hand off to **`rfc-reviewer`** for a second-pass score.

## Design choices

- **Repo as the source of truth** — design decisions name real files. The
  Existing Code Anchors table is the agent's pre-flight reading list.
- **Mermaid-first** — text-native diagrams the agent can render, edit, and
  diff. Fenced ` ```mermaid ` blocks are the spec, not screenshots.
- **Executable plan** — every chunk has files, commands, and verifiable
  acceptance criteria. No vague "implement the feature" rows.
- **Strict placeholders** — `TBD` is forbidden; use `[REQUIRED: …]` prompts and
  close them before claiming ready.
- **Reviewer is supplementary** — the §7 gate is the agent-execution contract.
  `rfc-reviewer` is a useful second pass, not a precondition.

## Suggested loop

1. Draft with `rfc-starter` → `./rfc-draft.md` (in the current working directory)
2. Self-check via `checklist.md`; close blockers
3. Mark **Ready for agent execution: yes** when all gates pass
4. Hand to your executing agent — or, optionally, run `rfc-reviewer` for a
   second-pass score before execution

For reviewer install, scoring bands, and report shape, see [`rfc-reviewer/README.md`](https://github.com/firdasafridi/rfc-reviewer).
