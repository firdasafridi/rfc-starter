# RFC Authoring Workflow

This workflow converts a PRD (or problem statement) into an RFC that an AI agent can implement without clarification meetings. **Optional:** pull in extra context from linked documentation, related RFCs, or architecture/theory sources when they materially affect the design.

## Phase 1: PRD Decomposition

**(Optional)** Assemble **external context** when it applies—anything outside the single PRD text and the live repo that could still constrain the design:

- product, design, and operational documentation (wiki/Confluence/Google Docs, runbooks, support playbooks)
- related RFCs (same initiative, adjacent services, prior versions of this RFC)
- architecture and decision narrative (ADRs, `THEORY.MD` or equivalent “why we built it this way” docs in this or sibling repositories)
- third-party or vendor documentation when the feature depends on an external API, SDK, or platform contract

If you use this optional pass, minimum output before deeper decomposition:

- a consolidated reference list: **title**, **link or repo path**, and **what the RFC took from it** (or a one-line “reviewed — no material impact” so nothing is cited blindly)
- explicit flags when two sources disagree, or when a document contradicts production behavior (carry forward into Assumptions or Open Questions)

If you skip it, continue straight to PRD extraction below.

Then extract and normalize:
- user outcomes
- business rules
- acceptance criteria
- scope boundaries
- non-goals
- constraints (latency, security, compliance, cost, timeline)
- **UI surfaces and consumer surfaces** — for every page, modal, tab, panel, chatbot
  flow, magic-link view, dashboard, or admin tool the PRD names, list the surface
  and its consumer (web, chatbot, mobile, support tool, internal cron). Each surface
  becomes an input to Phase 2 ("what reads / writes does this surface require?").
- **Roles & actors** — every role the PRD lists, including support / read-only /
  internal roles. Each role becomes an input to Phase 4's authorization matrix.
- **Per-status lifecycle rules** — for every entity with a status enum, capture
  the retention, visibility, and transition rules per status (e.g. cancelled
  hidden after 30 d but row retained 12 mo; archived read-only).
- **Branches & skips** — non-error flow branches that change downstream behavior
  (e.g. opt-out → skip notification). These are not failures; they are policy.

Produce a PRD-to-RFC mapping table before drafting implementation details.

## Phase 1.5: PRD Section Coverage Check

Before moving to Phase 2, walk the PRD's table of contents top to bottom and
confirm every numbered section appears in the Phase 1 output **or** is explicitly
marked `n/a — out of scope (reason)` / `n/a — frontend RFC (reason)`. UI pages,
role tables, and data-lifecycle subsections are the most-skipped sections;
treat any silent omission as a blocker.

## Phase 2: Codebase Context Capture

**(Optional, only if Phase 1 included external references.)** Reconcile those references with the repository: confirm or correct what related RFCs and docs claim about APIs, ownership, and rollout state. When code and an external RFC disagree, record the resolution path (which source wins and why).

Identify concrete anchors:
- existing modules/services/components to extend
- contracts already in production (API/schema/events)
- naming and layering patterns to follow
- relevant operational constraints (flags, rollout patterns, observability stack)

Minimum output from this phase:
- list of target files/modules
- list of interfaces/contracts to preserve or evolve
- list of explicit deviations from existing patterns
- short reconciliation notes for any Phase 1 external reference that was validated, superseded, or contradicted by the codebase (omit this bullet if Phase 1 had no external context pass)
- **Cross-squad responsibility map** when multiple squads are involved: for each
  Behavior or flow in the PRD, list every step in deploy/runtime order and the
  owning squad. Disagreements with the PRD on who-owns-what are blockers,
  not edits — surface them as Open Questions.
- **Existing API check** — before proposing a new endpoint, search the repo and
  related services for an existing contract that already serves the requirement.
  Each newly proposed endpoint must be tagged `reused`, `extended`, or
  `new-with-justification` (with the justification explaining why reuse / extension
  cannot satisfy the requirement without violating semantics, ownership, or
  compatibility).

## Phase 3: Decision Closure

For each major technical decision, write:
1. chosen option
2. alternatives considered
3. rejection rationale
4. implementability risks if wrong
5. reversibility/rollback path

Any unresolved major decision blocks "ready" status.

## Phase 4: RFC Drafting

Use the template that matches RFC type:
- frontend
- backend
- full-stack

Required drafting rules:
- replace vague words with measurable or typed statements
- define failure handling per external interaction
- define rollout and rollback in deploy-order terms
- define observability with named metrics/logs/alerts
- define implementation chunks with acceptance criteria

## Phase 5: Hardening Pass

Before handoff:
- run checklist in `checklist.md` (including **§0 External context inputs** when you cited or relied on anything beyond the PRD and repo—otherwise mark that section `n/a`)
- ensure every required table/section is filled or explicitly marked not applicable with reason
- if the RFC cites external links or paths, ensure each still belongs in the final draft (no stale or orphan references)
- ensure no unresolved "TBD" remains
- ensure every UI surface from Phase 1 has a backing read endpoint listed in
  the API table, OR an explicit one-line note on why none is required
- ensure every role from Phase 1 appears in the §3 Role × Endpoint authorization matrix
- ensure every status enum has explicit per-status lifecycle (retention,
  visibility, transitions) declared in §2 Database Model
- ensure every cross-squad flow has a Responsibility Boundary Matrix row, and
  every PRD-named non-error skip / opt-out / suppress branch has a Branch & Skip
  Catalog row
- ensure every newly proposed endpoint carries the `reused` / `extended` /
  `new-with-justification` tag from Phase 2's Existing API check

If blockers remain, keep them in `Open Questions` and mark:
- `Ready for rfc-reviewer: no`
