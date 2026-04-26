# Example: Invoking rfc-starter with Plan-First and Long Context Window

This file shows a complete example invocation of the `rfc-starter` skill that:

1. Loads the full skill context (long context window from the repository).
2. Emits a plan before any drafting starts (plan-first).
3. Waits for user sign-off before proceeding.

---

## Step 1 — Provide the skill context (long context window)

Before asking the skill to draft anything, attach all four skill files to give the
model its complete authoring framework. In chat UIs you can paste the file contents
directly; in CLI tools use the `@file` or `--context` syntax your tool supports.

```
# Context files to load (in order):
@rfc-starter/SKILL.md
@rfc-starter/workflow.md
@rfc-starter/templates.md
@rfc-starter/checklist.md
```

**Why this matters:** the templates and checklist are several hundred lines each.
Loading them all up-front puts the full authoring framework in one context window
so the model never has to guess what a `[REQUIRED: …]` placeholder means or which
checklist item to evaluate.

---

## Step 2 — Invoke with a plan-first prompt

```
Draft an RFC using the rfc-starter skill.

PRD location: ./docs/prd-notifications-v2.md
RFC type (if known): backend

Before writing anything, follow Phase 0 of workflow.md:
1. Confirm you have loaded SKILL.md, workflow.md, templates.md, and checklist.md.
2. Emit your Execution Plan (RFC type + justification, phases to run, repo files
   to inspect, anticipated decisions to close, open questions from the PRD).
3. Wait for my approval before proceeding to Phase 1 (PRD Decomposition).
```

---

## Step 3 — Example Execution Plan output (what the skill should produce)

The skill should respond with something like:

```
### Execution Plan — rfc-starter

**Skill files loaded:** SKILL.md ✓  workflow.md ✓  templates.md ✓  checklist.md ✓

**RFC type:** backend (new-feature)
Justification: PRD describes server-side notification dispatch, job scheduling, and
a new `notifications` table — no frontend surface changes in scope.

**Phases to execute:**
- Phase 0 (Planning) — complete (this output)
- Phase 1 (PRD Decomposition) — will extract goals, constraints, roles, status enums
- Phase 1.5 (PRD Section Coverage Check) — will walk ToC
- Phase 2 (Codebase Context Capture) — will read:
    - `internal/notifications/` (existing notification handlers)
    - `db/migrations/` (schema patterns to follow)
    - `internal/jobs/` (background job registration pattern)
- Phase 3 (Decision Closure) — will resolve: queue vs. direct-dispatch,
    idempotency key strategy, retry/back-off policy
- Phase 4 (RFC Drafting) — backend template from templates.md
- Phase 5 (Hardening) — full checklist.md pass
- External context pass (Phase 1 optional) — SKIP (no ADRs or related RFCs provided)

**Anticipated decisions to close:**
1. Queue technology — RabbitMQ (existing) vs. new in-process queue
2. Idempotency key — UUID v4 in payload vs. hash of (user_id, event_type, timestamp)
3. Retry policy — exponential back-off cap and dead-letter strategy

**Open questions from PRD:**
- §3.2 mentions "high-priority notifications" but does not define the priority enum
  or SLA. Will assume two tiers (high / normal) unless you clarify.
- §5 rollout: phased by org size or feature flag per tenant?

**Ready to proceed?** Reply "proceed" to start Phase 1, or correct any of the above.
```

---

## Step 4 — User approves the plan

```
proceed
```

The skill then continues through Phases 1–5 and writes `./rfc-draft.md`.

---

## Tips for maximising context quality

| Tip | Detail |
|---|---|
| **Include related RFCs** | If a previous RFC covers adjacent logic, add `@path/to/related-rfc.md` to the context block in Step 1. |
| **Add architecture docs** | ADRs, `THEORY.md`, or runbooks that constrain the design go into the Phase 1 optional external-context pass — list them in your prompt alongside the PRD. |
| **Paste relevant code** | For small but critical files (e.g., an existing API handler you plan to extend), pasting the snippet directly is faster than hoping the model reads the right lines. |
| **Name the modules explicitly** | If you already know which directories are in scope, list them in the prompt ("focus repo grounding on `internal/payments/` and `db/migrations/`") so Phase 2 is targeted rather than exploratory. |
