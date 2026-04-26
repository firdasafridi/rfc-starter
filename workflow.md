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

Produce a PRD-to-RFC mapping table before drafting implementation details.

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

If blockers remain, keep them in `Open Questions` and mark:
- `Ready for rfc-reviewer: no`
