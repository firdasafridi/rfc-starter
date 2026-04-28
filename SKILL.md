---
name: rfc-starter
description: Draft engineering RFCs that an AI coding agent can execute end-to-end against the existing repository — anchored in repo-as-documentation, mermaid diagrams, and a concrete call-to-action execution plan. Use when the user asks to write, groom, or structure an RFC from a PRD, feature request, or problem statement.
argument-hint: [prd-path-or-url] [optional-rfc-type]
---

# RFC Starter

Use this skill to produce an **agent-execution-ready** RFC: a self-contained
package that an autonomous coding agent can read, understand the existing
codebase from, and then execute against — without follow-up clarification
meetings.

The RFC is built around three pillars:

1. **Repo as documentation** — every design decision is anchored in real files
   already in the repository. The agent reads the existing code first (via the
   §2 Repo Reading Guide) and treats it as the source of truth for patterns,
   contracts, and conventions.
2. **Mermaid-first diagrams** — architecture, sequence, ER, state, and branch
   diagrams are mermaid fenced blocks the agent can read and edit in-place. No
   draw.io / Lucidchart / Excalidraw exports as primary specs.
3. **Executable call-to-action** — the §4 Execution Plan emits ordered chunks
   with file paths, commands, and verifiable acceptance criteria, plus a
   Verification & Rollback Recipe the agent can run.

Optional: hand the finished RFC to `rfc-reviewer` for a second-pass score.
That handoff is supplementary; the primary gate is **§7 Ready for agent
execution** in the RFC itself.

## Inputs

Accept one of:
- PRD path (`.md`, `.txt`, `.docx`, `.pdf`)
- URL containing PRD/product requirements
- pasted requirement text

Optional:
- RFC type hint: `frontend`, `backend`, or `full-stack`

If type is not provided, infer it from scope and touched systems.

## Output

Write the draft RFC into the **user's current project repository** — never
into the `rfc-starter` skill's own folder.

Resolution rules (in order):

1. If the user provided an explicit path, use it.
2. If the project has a conventional RFC folder, prefer it (in this order):
   `./docs/rfcs/`, `./rfcs/`, `./docs/architecture/rfcs/`, `./.rfcs/`. Slug the
   filename from the RFC title (e.g. `./docs/rfcs/orders-export.md`).
3. Otherwise, write `./rfc-draft.md` in the project root.

**Hard guard — never write into the skill folder.** If the current working
directory resolves to inside `rfc-starter` (any path containing
`/.claude/skills/rfc-starter/`, `/rfc-starter/`, or the equivalent on this
machine), **stop and ask the user** for the project path. Do not create an
`output/` subfolder anywhere — neither in the skill folder nor in the project.

Never overwrite an existing RFC without user confirmation. If the destination
file already exists, ask the user whether to overwrite, suffix with a version
(e.g. `<name>-v2.md`), or write to a different path.

## Workflow

Follow this sequence strictly:

1. **Intake**
   - Read the PRD/problem statement fully.
   - Extract goals, constraints, acceptance criteria, and non-goals.
   - Extract every UI surface, role, and per-status lifecycle rule.
   - Walk the PRD's table of contents and confirm every numbered section is in the extraction or marked `n/a — reason`.
   - Infer RFC type and sub-type.

2. **Repo grounding (treat the codebase as documentation)**
   - Identify existing modules/services/components this change should follow.
   - Capture concrete file references to anchor design decisions.
   - Note existing patterns to preserve (state mgmt, handler shape, repo, error
     shape, logging, tracing).
   - Compose the **Repo Map** as a mermaid `flowchart` showing only the slice
     this RFC touches.
   - Build the **Existing Code Anchors** table: file paths the agent must read
     before writing, with one-line "what to learn" notes.
   - Set the **Reading Order for the Agent** (≤ 10 numbered files).
   - **Existing API check**: before proposing any new endpoint, search the repo
     and related services for an existing contract. Tag each endpoint as
     `reused`, `extended`, or `new-with-justification`.
   - For multi-squad flows, build the cross-squad responsibility map (per step,
     owner) before drafting.
   - Fill the **Source Verification** subsection of Detail 2.0: one row per
     anchor / pattern / contract, each with concrete evidence (function name,
     exported symbol, line range, or quoted identifier). Any row that cannot
     be filled is a blocker — move it to Open Questions, do not invent.

3. **Decision closure before drafting**
   - Resolve key architecture decisions with explicit chosen options.
   - For each major decision, list rejected alternatives and why.
   - If no alternative was seriously considered, write
     `no alternative considered — [reason]` rather than fabricate a rejected
     option.
   - Eliminate "TBD", "maybe", and dangling "X or Y" wording.

4. **Draft RFC using type template**
   - Use the matching template sections from `templates.md`.
   - **Header format follows Qontak RFC Template governance + agent-friendly
     additions**: fill the YAML frontmatter at the top, the visible Metadata
     table, and the Sections at a Glance index. Frontmatter and table must
     agree on every shared field (status, owner, type, dates). Use ISO-8601
     (`YYYY-MM-DD`) for every date.
   - **Diagrams must be mermaid** — fill the `flowchart` / `sequenceDiagram` /
     `erDiagram` / `stateDiagram-v2` scaffolds in the template. Do not link to
     external diagram tools as the primary spec.
   - Fill every required contract field; if unknown, ask targeted questions.
   - Fill the required matrices: PRD Section Coverage, UI / Consumer Surface
     Coverage, Role Coverage, Per-Status Lifecycle, APIs (Outbound + Inbound),
     Responsibility Boundary, State Surface Contract, Role × Endpoint
     Authorization, Branch & Skip Catalog. Each accepts `n/a — reason` but
     never silent omission.
   - Build the **Agent Execution Plan**: ordered chunks, each with files +
     commands + verifiable acceptance criteria.
   - Build the **Verification & Rollback Recipe**: pre-merge commands,
     post-deploy signals, concrete rollback steps.
   - Include rollout, rollback, observability, and acceptance criteria.

5. **Run agent-execution readiness checklist**
   - Validate draft against `checklist.md`.
   - If blockers remain, list them in the RFC under "Open Questions".

6. **Hand-off note**
   - Set the §7 marker: "Ready for agent execution: yes/no".
   - If no, list exactly what is missing per the §7 gates.
   - (Optional) suggest `rfc-reviewer` for a second-pass score.

## Quality bar

- Prefer explicit schemas/contracts over prose summaries.
- Every diagram is a mermaid fenced block.
- Every external interaction defines failure behavior.
- Every implementation chunk has files, commands, and verifiable acceptance criteria.
- Every chunk's acceptance criteria are assertable (test passes, query returns,
  metric crosses threshold) — not subjective ("looks good").
- Keep alignment with current codebase patterns unless deviation is justified.

## Anti-hallucination guardrails

The biggest risk is fabricating "existing" repository facts — file paths,
pattern names, endpoints, table names, test commands, metric names. The skill
treats those facts as evidence, not vibes. Before drafting any of the
following, the agent must verify against the actual repo:

- **Anchors must be real.** Every path in Detail 2.0's Existing Code Anchors
  table is verified by listing the directory and reading the file. Never guess
  from "what a Go service usually looks like".
- **Patterns must cite an opened example.** Every row in Patterns to Follow
  points to a reference file the author actually read. If no in-repo example
  exists, write `no in-repo pattern — propose one and cite ADR`, do not
  fabricate.
- **Existing contracts must be searched.** Before proposing a new endpoint,
  search the routes / OpenAPI / handler files. Tag honestly: `reused`,
  `extended`, or `new-with-justification` (with the justification explaining
  why reuse cannot satisfy the requirement).
- **Test and build commands come from the repo.** Read `package.json` scripts,
  `Makefile`, `Justfile`, `.github/workflows/*`, or equivalent before writing
  Detail 4.B / 4.C commands. Do not invent `pnpm test --filter X` if the
  project uses `yarn`, `nx`, or `make test`.
- **DDL must match the project's migrator.** Read existing migrations to match
  dialect (Postgres / MySQL / Spanner), naming, and tooling (raw SQL vs ORM
  migrations).
- **Metric / log / alert names follow the project's convention.** Cite at
  least one existing metric or log field before naming new ones.
- **Decisions are honest.** If no alternative was seriously considered, write
  `no alternative considered — [reason]` rather than fabricate a rejected
  option.
- **Diagrams tie back to anchors.** Every node in the Repo Map / Component
  diagram corresponds to a real folder, service, or contract from the
  Existing Code Anchors / Existing Contracts tables.
- **When uncertain, ask.** The skill is allowed — and expected — to surface
  targeted questions before drafting rather than fill `[REQUIRED: ...]` with
  plausible-but-unverified content.

The agent records the proof in the **Source Verification** subsection of
Detail 2.0 (one row per anchor) — see `templates.md`. The hardening pass and
`checklist.md` both gate on it.

## Additional Resources

- Authoring flow and section requirements: [workflow.md](workflow.md)
- RFC templates by type (mermaid-first): [templates.md](templates.md)
- Final agent-execution readiness gate: [checklist.md](checklist.md)
