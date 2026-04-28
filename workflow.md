# RFC Authoring Workflow

This workflow converts a PRD (or problem statement) into an RFC that an
**AI coding agent can execute end-to-end against the existing repository** —
without follow-up clarification meetings. It is built around three pillars:

1. **Repo as documentation** — anchor every design decision in real files.
2. **Mermaid-first diagrams** — every architecture, sequence, ER, state, and
   branch diagram is an editable mermaid block in the RFC.
3. **Executable call-to-action** — produce ordered chunks with files, commands,
   and verifiable acceptance criteria, plus a verification/rollback recipe.

## Phase 0: Resolve the output destination

Before writing anything, decide where the draft will land. The RFC must end up
inside the **user's project repository**, not the `rfc-starter` skill folder.

1. If the user provided an explicit path, use it.
2. If the project has a conventional RFC folder, prefer it (in this order):
   `./docs/rfcs/`, `./rfcs/`, `./docs/architecture/rfcs/`, `./.rfcs/`. Slug the
   filename from the RFC title.
3. Otherwise, write `./rfc-draft.md` in the project root.

**Hard guard**: if the current working directory resolves to inside the skill
folder (any path containing `/.claude/skills/rfc-starter/` or
`/rfc-starter/`), stop and ask the user for the project path. Never create an
`output/` subfolder. Never overwrite an existing draft without confirmation.

## Phase 1: PRD Decomposition

**(Optional)** Assemble **external context** when it applies — anything outside
the single PRD text and the live repo that could still constrain the design:

- product, design, and operational documentation (wiki/Confluence/Google Docs,
  runbooks, support playbooks)
- related RFCs (same initiative, adjacent services, prior versions of this RFC)
- architecture and decision narrative (ADRs, `THEORY.MD` or equivalent
  "why we built it this way" docs in this or sibling repositories)
- third-party or vendor documentation when the feature depends on an external
  API, SDK, or platform contract

If you use this optional pass, minimum output before deeper decomposition:

- a consolidated reference list: **title**, **link or repo path**, and **what
  the RFC took from it** (or a one-line "reviewed — no material impact" so
  nothing is cited blindly)
- explicit flags when two sources disagree, or when a document contradicts
  production behavior (carry forward into Assumptions or Open Questions)

If you skip it, continue straight to PRD extraction below.

Then extract and normalize:
- user outcomes
- business rules
- acceptance criteria
- scope boundaries
- non-goals
- constraints (latency, security, compliance, cost, timeline)
- **UI surfaces and consumer surfaces** — for every page, modal, tab, panel,
  chatbot flow, magic-link view, dashboard, or admin tool the PRD names, list
  the surface and its consumer (web, chatbot, mobile, support tool, internal
  cron). Each surface becomes an input to Phase 2 ("what reads / writes does
  this surface require?").
- **Roles & actors** — every role the PRD lists, including support / read-only /
  internal roles. Each role becomes an input to Phase 4's authorization matrix.
- **Per-status lifecycle rules** — for every entity with a status enum, capture
  the retention, visibility, and transition rules per status (e.g. cancelled
  hidden after 30 d but row retained 12 mo; archived read-only).
- **Branches & skips** — non-error flow branches that change downstream behavior
  (e.g. opt-out → skip notification). These are not failures; they are policy.
- **User stories** — every PRD §13b (or equivalent) user story must be
  extracted with its ID, title, and acceptance criteria, ready to feed Detail
  1.C Per-Story Change Map.

### Per-story change map (human-readable index)

While extracting the story list, classify each story by **layer scope** —
this is what Detail 1.C in the RFC will record. The classification tells you
immediately whether a story is in scope for the RFC type you're authoring:

- `FE-only`, `BE-only`, `FE + BE`, `Runtime / behavior`, `Config`,
  `Cross-squad`.
- For an FE RFC, treat `BE-only` stories as `n/a — covered in <BE RFC link>`
  rather than dropping them; for a BE RFC, the symmetric rule applies.
- For full-stack RFCs every `FE + BE` story carries both halves of the
  change.

The change map is the entry point a human reader uses to scan "what does
Story M1 require"; the technical matrices in Detail 1.A and §2 are the
detailed view. Both must agree.

### Type-specific design inputs

The three RFC templates take different design inputs. Pick the one matching
RFC type:

- **Frontend**: gather **Design References** — Figma frame link per
  PRD-named surface, design system version, design QA contact. Without these,
  do not draft frontend chunks. Frontend RFCs are design-anchored.
- **Backend**: do **not** require Figma. Instead, derive **PRD-to-Schema** —
  for every PRD-described entity, attribute, and business rule, decide what
  the backend must persist (table.column), expose (endpoint/event), and
  enforce (handler / DB constraint / consumer / job). Backend RFCs are
  schema-anchored, not design-anchored.
- **Full-stack**: gather **both**. The bridge between them is §2.G
  Cross-Layer Contract Verification — every API contract must satisfy both
  the Figma frames and the PRD-to-Schema derivation rows. Mismatches between
  the two are blockers, not implementation details.

Produce a PRD-to-RFC mapping table before drafting implementation details.

## Phase 1.5: PRD Section Coverage Check

Before moving to Phase 2, walk the PRD's table of contents top to bottom and
confirm every numbered section appears in the Phase 1 output **or** is
explicitly marked `n/a — out of scope (reason)` / `n/a — frontend RFC (reason)`.
UI pages, role tables, and data-lifecycle subsections are the most-skipped
sections; treat any silent omission as a blocker.

## Phase 2: Repo-as-Documentation Capture

The agent will read the existing codebase as the source of truth. This phase
produces the inputs for the RFC's **§2 Detail 2.0 Repo Reading Guide**.

**(Optional, only if Phase 1 included external references.)** Reconcile those
references with the repository: confirm or correct what related RFCs and docs
claim about APIs, ownership, and rollout state. When code and an external RFC
disagree, record the resolution path (which source wins and why).

Identify concrete anchors:
- existing modules/services/components to extend
- contracts already in production (API/schema/events)
- naming and layering patterns to follow
- relevant operational constraints (flags, rollout patterns, observability stack)

Minimum output from this phase:
- **Repo Map** — a mermaid `flowchart` showing only the slice this RFC touches
  (folders, services, queues, datastores). Keep ≤ 12 nodes; split into multiple
  diagrams if needed.
- **Existing Code Anchors table** — for each file the agent must read before
  writing: path, why the agent reads it, what pattern it teaches.
- **Patterns to Follow table** — per concern (state mgmt, handler shape,
  repository, error shape, logging, tracing), the reference file in the repo
  and any deviation this RFC makes.
- **Reading Order for the Agent** — a numbered list (≤ 10 entries) the agent
  reads before opening the first execution chunk.
- **Existing Contracts to Reuse, Extend, or Replace** — every endpoint / table
  / event that the RFC reuses, extends, or replaces, with status tag (`reused`
  / `extended` / `new-with-justification`) and the justification when new.
- **Cross-squad responsibility map** when multiple squads are involved: for
  each Behavior or flow in the PRD, list every step in deploy/runtime order
  and the owning squad. Disagreements with the PRD on who-owns-what are
  blockers, not edits — surface them as Open Questions.
- short reconciliation notes for any Phase 1 external reference that was
  validated, superseded, or contradicted by the codebase (omit this bullet if
  Phase 1 had no external context pass)

### Verification (anti-hallucination)

Every Phase 2 output must be backed by an opened file or a directory listing,
not inferred from stack heuristics. Specifically:

- Every Existing Code Anchor path is opened and read at least once.
- Every Pattern to Follow citation points to a reference file the author read.
- Every Reuse / Extend / New tag follows a search of the routes / OpenAPI /
  handler files (e.g. `rg "router\\.(GET\\|POST)" services/`).
- Every test or build command later used in Detail 4.B / 4.C is sourced from
  the repo's actual configuration (`package.json` scripts, `Makefile`,
  `Justfile`, `.github/workflows/*`, `Taskfile.yml`, etc.) — not invented from
  what "a typical project" would have.
- Every DDL block in §2.3 matches the project's migrator dialect and tooling
  (read at least one existing migration to confirm).
- Every metric / log field name introduced cites a sibling metric or log field
  already in the repo for naming convention.

Record the proof in Detail 2.0's **Source Verification** subsection of the RFC
(one row per anchor: path, how it was verified, 1-line evidence quote /
identifier / line range). Treat any row that cannot be verified as a
blocker — surface it in Open Questions, do not fill it with a plausible guess.

If the agent is allowed to fabricate, the entire Repo Reading Guide collapses
to a confident-sounding hallucination. Verification is non-negotiable.

## Phase 3: Decision Closure

For each major technical decision, write:
1. chosen option
2. alternatives considered
3. rejection rationale
4. implementability risks if wrong
5. reversibility/rollback path

Any unresolved major decision blocks "ready" status.

> Honesty escape hatch: if no alternative was seriously considered, write
> `no alternative considered — [reason]` rather than fabricate a rejected
> option to fill the table.

## Phase 4: RFC Drafting (mermaid-first, agent-executable)

Use the template that matches RFC type:
- **frontend** — design-anchored. §1 Design References (Figma per surface) +
  Detail 2.0 Design ↔ Code Mapping + Detail 2.A UI Contract (with Figma
  frame URL per component) + Detail 2.F Asset Inventory.
- **backend** — schema-anchored, no Figma. §1 PRD-to-Schema Derivation drives
  §2.3 DDL and §2.4 APIs. Every DDL row and endpoint traces back to a
  PRD-to-Schema row.
- **full-stack** — both. §1 Design References (FE half) + §1 PRD-to-Schema
  Derivation (BE half). The Cross-Layer Contract Verification table (§2.G)
  is the bridge: every endpoint must satisfy both halves.

Required drafting rules:
- replace vague words with measurable or typed statements
- **every diagram is a mermaid fenced block**:
  - `flowchart` for component / repo map / branch & skip flow
  - `sequenceDiagram` for happy path and at least one failure path per scenario
  - `erDiagram` for the data model (backend / full-stack)
  - `stateDiagram-v2` for every status enum with non-trivial transitions
- define failure handling per external interaction
- define rollout and rollback in deploy-order terms
- define observability with named metrics/logs/alerts
- **define the Agent Execution Plan** — ordered chunks, each with:
  - files to create/modify (exact paths)
  - commands to run (exact, copy-pasteable)
  - acceptance criteria that are verifiable (test passes, query returns,
    metric crosses threshold) — not subjective
- **define the Verification & Rollback Recipe** — pre-merge commands,
  post-deploy signals (named metric / log query / dashboard URL), and concrete
  rollback steps in order

## Phase 5: Hardening Pass (agent-execution readiness)

Before handoff:
- run the checklist in `checklist.md`. The checklist is organised around
  agent-execution gates: repo reading guide, mermaid diagrams, contracts,
  failure handling, rollout, execution plan, verification recipe.
- (Optional, only if Phase 1 used external context) include **§0 External
  context inputs** — otherwise mark that section `n/a`.
- ensure every required table/section is filled or explicitly marked not
  applicable with reason
- if the RFC cites external links or paths, ensure each still belongs in the
  final draft (no stale or orphan references)
- ensure no unresolved "TBD" remains
- ensure every diagram in the draft is a mermaid block (no orphan references
  to draw.io / Lucidchart / Excalidraw exports as primary specs)
- ensure every UI surface from Phase 1 has a backing read endpoint listed in
  the API table, OR an explicit one-line note on why none is required
- ensure every role from Phase 1 appears in the §3 Role × Endpoint authorization
  matrix
- ensure every status enum has explicit per-status lifecycle (retention,
  visibility, transitions) declared in §2 Database Model
- ensure every cross-squad flow has a Responsibility Boundary Matrix row, and
  every PRD-named non-error skip / opt-out / suppress branch has a Branch &
  Skip Catalog row
- ensure every newly proposed endpoint carries the `reused` / `extended` /
  `new-with-justification` tag from Phase 2's Existing API check
- **ensure Detail 2.0's Source Verification table is complete** — one row per
  Existing Code Anchor / Pattern to Follow / Existing Contract, with concrete
  evidence (function name, exported symbol, line range, or quoted identifier).
  No "exists" / "looks correct" / "standard pattern" placeholders.
- ensure every test / build command in Detail 4.B / 4.C cites its source file
  (`package.json` / `Makefile` / `Justfile` / `.github/workflows/*` /
  `Taskfile.yml`) — invented commands are blockers
- ensure the Agent Execution Plan has: order, files, commands, and verifiable
  acceptance criteria for every chunk
- ensure the Verification & Rollback Recipe is concrete (commands runnable,
  signals named, rollback steps ordered)

If blockers remain, keep them in `Open Questions` and mark:
- `Ready for agent execution: no`

> Optional: hand off the finished RFC to `rfc-reviewer` for a second-pass
> score after `Ready for agent execution: yes`. The reviewer is supplementary;
> the §7 gate in the RFC is the agent-execution contract.
