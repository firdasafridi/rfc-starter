# RFC Agent-Execution Readiness Checklist

Use this checklist before declaring the RFC `Ready for agent execution: yes`.
The gate is whether an autonomous coding agent can pick up the RFC, read the
existing repo as documentation, follow the mermaid diagrams as a mental model,
and execute the Agent Execution Plan to completion — without a clarification
meeting.

Mark each item `yes`, `no`, or `n/a` with a one-line note.

> Optional second pass: after this checklist is green, you may hand the RFC to
> `rfc-reviewer` for a second-pass score. That handoff is supplementary; the
> primary gate is this checklist.

## 0) External context inputs _(optional)_

Skip this whole section with **`n/a` (no sources beyond PRD + repo)** when you
did not use the Phase 1 optional external-context pass. If you did use it, mark
each item `yes`, `no`, or `n/a` with a one-line note.

- Documentation beyond the PRD (wiki/Confluence, design docs, runbooks) is
  listed with stable links or paths where the RFC relied on them.
- Related RFCs and architecture or theory sources (ADRs, `THEORY.MD`,
  sibling-repo narratives) are listed when they informed scope, contracts, or
  rollout.
- For each external item, the RFC states what was taken from it, or explicitly
  notes **reviewed — no material impact** so citations stay honest.
- Conflicts between external sources, or between an external source and the
  repo, are resolved in the body or called out in Assumptions / Open Questions.
- The metadata **Related Documents** and §1 **Related Documents** match this
  inventory (no orphan links, no missing drivers for major decisions).

## A0) Header & metadata _(Qontak RFC Template format + agent-friendly)_

- YAML frontmatter at the top is filled (status, type, sub_type, title,
  owner, authors, reviewers, approvers, dates, target_release,
  related_documents, discussion).
- Visible **Metadata** table is filled with the same values; frontmatter and
  table agree on every shared field.
- Status is one of `IDEA` / `RFC` / `ABANDON` / `AGREED`.
- All dates are ISO-8601 (`YYYY-MM-DD`); `last_updated` matches the most
  recent material edit.
- **Approver(s)** row includes an infosec approver.
- **Related Documents** lists at least one PRD link, or explicitly says
  `no-PRD — reason`.
- **Sections at a Glance** index lists §1–§7 in order with the type-specific
  hints already populated by the template.

## A) Problem and scope

- Problem statement is specific and testable.
- Success criteria are measurable.
- Scope boundaries are explicit.
- Non-goals prevent over-building.

## B) PRD traceability

- Every PRD requirement maps to an RFC section.
- Every major RFC decision has a PRD driver (or explicit self-contained
  justification for no-PRD RFCs).
- Every numbered PRD section appears in Detail 1.A's PRD Section Coverage table
  or is explicitly marked `n/a — reason`.
- Every UI surface in the PRD has a row in Detail 1.A's UI / Consumer Surface
  Coverage table.
- Every role in the PRD has a row in Detail 1.A's Role Coverage table AND in
  §3 Role × Endpoint Authorization matrix.

## B1) Per-Story Change Map _(Detail 1.C — human-readable index)_

The story-centric view that lets a reviewer answer "what does Story M1
require?" in one row, instead of crawling the technical matrices.

- Every PRD-listed user story (PRD §13b or equivalent) has exactly one row
  in Detail 1.C. No silent omission.
- Each row has a **Layer scope** value from the closed list (`FE-only` /
  `BE-only` / `FE + BE` / `Runtime / behavior` / `Config` / `Cross-squad`).
- Each row's **Changes** column lists concrete artifacts (components / hooks
  / endpoints / DDL keys / use cases / async jobs / events / Figma frames /
  env vars) — not prose.
- Each row's **Acceptance criteria** is verifiable (test passes, query
  returns, metric crosses threshold) — not subjective.
- Each row's **RFC anchors** point to the technical sections that contain
  the detail (e.g. `§2.4 row 1 · §4.C chunk 3`).
- Out-of-scope stories are not deleted: they read `n/a — covered in <other
  RFC link>` (FE → BE RFC, or BE → FE RFC), or `deferred — Phase X
  (reason)`.
- Full-stack only: every `FE + BE` row has **both** the FE changes and BE
  changes columns filled. A row with only one half is mis-tagged or has an
  unclosed dependency — surface it in §5 Open Questions.
- Cross-check against §2 — every artifact named in Detail 1.C must appear
  in the matching technical section (§2.3 DDL, §2.4 APIs, §4.C Execution
  Plan, etc.). Detail 1.C is the index, not the source of truth.

## B2) Type-specific design inputs

Mark only the rows for the RFC type you are using; mark the rest `n/a`.

**Frontend or Full-stack (FE half):**

- §1 **Design References** — every PRD-named surface has a direct Figma
  frame link, design system version, and design QA contact. Frames missing
  for any surface → blocker, do not draft chunks against imagined designs.
- Detail 2.0 **Design ↔ Code Mapping** — every Figma frame is mapped to an
  implementing file, with tokens used (color / space / typography) and any
  deviation explicit (or "none — pixel-faithful").
- Detail 2.A **UI Contract** — every component cites its Figma frame URL +
  implementation file path.
- Detail 2.F / 2.J **Asset Inventory** — every new icon / illustration /
  image / lottie / font has a source, format, and repo path.

**Backend or Full-stack (BE half):**

- §1 **PRD-to-Schema Derivation** — every PRD-described entity, attribute,
  and business rule maps to a (table.column), an exposing endpoint/event, an
  enforcement point, and a PRD section reference. **No Figma is required for
  backend RFCs.**
- §2.3 **Database Model** — every DDL row traces back to a §1 PRD-to-Schema
  row; no inferred / "convenient" fields without a PRD anchor.
- §2.4 **APIs** — every endpoint traces back to a §1 PRD-to-Schema row (and,
  for full-stack, a Figma frame in §1 Design References).

**Full-stack only:**

- §2.G **Cross-Layer Contract Verification** — every endpoint satisfies
  *both* a Figma frame and a PRD-to-Schema row; Match? = yes for all rows.

## C) Repo as documentation _(Detail 2.0 Repo Reading Guide)_

- A mermaid `flowchart` Repo Map exists and shows only the slice this RFC
  touches (≤ 12 nodes per diagram).
- The Existing Code Anchors table names real file paths the agent must read
  before writing, with one-line "what to learn" notes.
- The Patterns to Follow table cites a reference file per concern (state
  management / handler shape / repository / error shape / logging / tracing).
  Deviations have an ADR / explicit justification.
- The Reading Order for the Agent is a numbered list (≤ 10 entries) the agent
  reads before opening the first execution chunk.
- Every newly proposed endpoint is tagged `reused`, `extended`, or
  `new-with-justification`. Justification is concrete when `new`.

## C2) Verification & honesty (anti-hallucination)

The biggest hallucination magnets are facts about "existing" repo state. Every
"real" claim must be backed by an opened file, a config read, or a grep — not
inferred from stack heuristics.

- Detail 2.0's **Source Verification** table has one row per Existing Code
  Anchor / Pattern to Follow / Existing Contract, each with concrete evidence
  (function name, exported symbol, line range, or a quoted identifier) — not
  "exists" / "looks correct" / "standard pattern".
- Every Pattern to Follow row points to a reference file the author actually
  opened (not a generic "Redux Toolkit slice" claim against a Zustand repo).
- Every endpoint tagged `reused` or `extended` cites the existing route /
  handler / OpenAPI spec; every `new-with-justification` explains why reuse
  fails.
- Every test or build command in Detail 4.B / 4.C is sourced from the repo's
  config (`package.json` scripts / `Makefile` / `Justfile` /
  `.github/workflows/*` / `Taskfile.yml`) and cites the source file + line —
  not invented.
- Every DDL block in §2.3 matches the project's existing migrator dialect and
  tooling (cite at least one example migration in the repo).
- Every named metric / dashboard / log query references a real or proposed
  artifact (cite a sibling metric for naming convention).
- Decisions Closed (Detail 1.B): rows where no alternative was seriously
  considered say `no alternative considered — [reason]` rather than a
  fabricated rejected option.
- Mermaid diagrams: every node in the Repo Map / Component diagram
  corresponds to a real folder, service, or contract from the Existing Code
  Anchors / Existing Contracts tables (no invented services).
- When a fact cannot be verified, the row is moved to §5 Open Questions
  rather than filled with a plausible-but-unverified value.

## D) Decision closure

- Every major decision has one chosen option.
- Alternatives are documented and rejected with rationale.
- No unresolved "TBD", "maybe", or "X or Y".

## E) Mermaid diagrams

- Component / architecture diagram is a mermaid `flowchart` (or `graph`).
- Every scenario has a mermaid `sequenceDiagram` with at least one failure path.
- Backend / full-stack: data model is a mermaid `erDiagram`.
- Every status enum has a mermaid `stateDiagram-v2`.
- Every PRD-named non-error branch / skip / opt-out has a mermaid `flowchart`.
- No diagram in the RFC depends on draw.io / Lucidchart / Excalidraw exports
  as the primary spec (those may be supplements only).

## F) Contracts and data shapes

- Frontend contracts are typed/specified.
- Backend endpoint contracts include request/response/errors/auth/versioning.
- Inbound webhooks (other services → us) appear in the §2 APIs Inbound table
  with the same fields, not only as prose.
- Every status enum has a per-status lifecycle row (visibility, retention,
  restore, transitions) in §2 Database Model.
- Every UI surface listed in Detail 1.A has a backing read endpoint in §2 APIs
  OR a one-line `n/a — covered by writes` note.
- Every entity that surfaces state to a UI or downstream consumer has a State
  Surface Contract row.
- Schema/data model changes are DDL-level where applicable.
- Cross-layer contracts are aligned for full-stack scope (Detail 2.G shows
  Match? = yes for every endpoint).

## G) Failure and integrity

- Every external call has timeout/retry/failure behavior.
- Every non-error skip / opt-out / suppress branch named in the PRD has a
  Branch & Skip Catalog row with explicit owner.
- Write paths define transaction boundaries and partial-failure handling.
- Idempotency and duplicate handling are defined where needed.
- Concurrency/collision handling is explicit for shared resources.
- For every multi-squad flow, the Responsibility Boundary Matrix exists and
  matches the PRD's allocation.

## H) Rollout and operations

- Rollout stages are defined.
- Rollback trigger and action are explicit.
- Config/flag contract is complete.
- Observability includes named metrics/logs/alerts.
- Full-stack: Cross-Layer Rollout Compatibility Matrix is filled and any "no"
  has a mitigation in the rollout plan.

## I) Agent Execution Plan

- The Execution Plan is an ordered list of chunks (Detail 4.C / 4.D depending
  on template).
- Each chunk lists exact files to create / modify (paths, not vague areas).
- Each chunk lists exact commands to run (copy-pasteable, scoped to the
  feature where possible).
- Each chunk's acceptance criteria are verifiable — assertable by a test, a
  query, a metric threshold, or a build artifact — not subjective.
- Chunk order respects dependencies (e.g. migration before code that depends
  on the new column).

## J) Verification & Rollback Recipe

- Pre-merge verification commands are listed in execution order (lint /
  typecheck / test / build).
- Post-deploy verification signals are named (metric name, log query, or
  dashboard URL with the threshold the agent checks).
- The rollback recipe is a numbered list of concrete steps the agent can
  execute (toggle the named flag, revert the named PR, run the named
  migration-down, confirm the named signal).
- For full-stack: rollback respects deploy order (don't roll back a layer that
  another layer still depends on).

## K) Final gate

- Draft is clear enough for an AI agent to read the existing repo as
  documentation, follow the mermaid diagrams as a mental model, and execute the
  Plan to completion without a clarification meeting.
- Draft is marked `Ready for agent execution: yes`.

If any blocking item is `no`, set `Ready for agent execution: no` and list the
failing gates in §7.
