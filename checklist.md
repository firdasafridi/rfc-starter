# RFC Readiness Checklist (Pre-Review)

Use this checklist before sending the draft into `rfc-reviewer`.

Mark each item `yes`, `no`, or `n/a` with a one-line note.

## 0) External context inputs _(optional)_

Skip this whole section with **`n/a` (no sources beyond PRD + repo)** when you did not use the Phase 1 optional external-context pass. If you did use it, mark each item `yes`, `no`, or `n/a` with a one-line note.

- Documentation beyond the PRD (wiki/Confluence, design docs, runbooks) is listed with stable links or paths where the RFC relied on them.
- Related RFCs and architecture or theory sources (ADRs, `THEORY.MD`, sibling-repo narratives) are listed when they informed scope, contracts, or rollout.
- For each external item, the RFC states what was taken from it, or explicitly notes **reviewed — no material impact** so citations stay honest.
- Conflicts between external sources, or between an external source and the repo, are resolved in the body or called out in Assumptions / Open Questions.
- The metadata **Related Documents** and §1 **Related Documents** match this inventory (no orphan links, no missing drivers for major decisions).

## A) Problem and scope

- Problem statement is specific and testable.
- Success criteria are measurable.
- Scope boundaries are explicit.
- Non-goals prevent over-building.

## B) PRD traceability

- Every PRD requirement maps to an RFC section.
- Every major RFC decision has a PRD driver (or explicit self-contained justification for no-PRD RFCs).

## C) Decision closure

- Every major decision has one chosen option.
- Alternatives are documented and rejected with rationale.
- No unresolved "TBD", "maybe", or "X or Y".

## D) Contracts and data shapes

- Frontend contracts are typed/specified.
- Backend endpoint contracts include request/response/errors/auth/versioning.
- Schema/data model changes are DDL-level where applicable.
- Cross-layer contracts are aligned for full-stack scope.

## E) Failure and integrity

- Every external call has timeout/retry/failure behavior.
- Write paths define transaction boundaries and partial-failure handling.
- Idempotency and duplicate handling are defined where needed.
- Concurrency/collision handling is explicit for shared resources.

## F) Rollout and operations

- Rollout stages are defined.
- Rollback trigger and action are explicit.
- Config/flag contract is complete.
- Observability includes named metrics/logs/alerts.

## G) Implementation handoff quality

- Implementation chunks are ordered with dependencies.
- Each chunk has verifiable acceptance criteria.
- Files/modules impacted are named.
- Open questions are explicit and scoped.

## H) Final gate

- Draft is clear enough for an AI agent to implement without a clarification meeting.
- Draft is marked `Ready for rfc-reviewer: yes`.

If any blocking item is `no`, set `Ready for rfc-reviewer: no`.
