---
name: rfc-starter
description: Draft engineering RFCs from scratch using PRD intent and repository context, then harden them for agentic implementation. Use when the user asks to write, groom, or structure an RFC from a PRD, feature request, or problem statement.
argument-hint: [prd-path-or-url] [optional-rfc-type]
---

# RFC Starter

Use this skill to produce an implementation-ready RFC draft before review.

This skill is the authoring companion to `rfc-reviewer`:
- `rfc-starter` creates the RFC.
- `rfc-reviewer` scores and challenges the RFC.

## Inputs

Accept one of:
- PRD path (`.md`, `.txt`, `.docx`, `.pdf`)
- URL containing PRD/product requirements
- pasted requirement text

Optional:
- RFC type hint: `frontend`, `backend`, or `full-stack`

If type is not provided, infer it from scope and touched systems.

## Output

Write the draft RFC to:
- `rfc-starter/output/rfc-draft.md` by default
- or a path explicitly requested by the user

Never overwrite an existing RFC without user confirmation.

## Workflow

Follow this sequence strictly:

1. **Intake**
   - Read the PRD/problem statement fully.
   - Extract goals, constraints, acceptance criteria, and non-goals.
   - Infer RFC type and sub-type.

2. **Repo grounding**
   - Identify existing modules/services/components this change should follow.
   - Capture concrete file references to anchor design decisions.
   - Note existing patterns to preserve.

3. **Decision closure before drafting**
   - Resolve key architecture decisions with explicit chosen options.
   - For each major decision, list rejected alternatives and why.
   - Eliminate "TBD", "maybe", and dangling "X or Y" wording.

4. **Draft RFC using type template**
   - Use the matching template sections from `templates.md`.
   - Fill every required contract field; if unknown, ask targeted questions.
   - Include rollout, rollback, observability, and acceptance criteria.

5. **Run readiness checklist**
   - Validate draft against `checklist.md`.
   - If blockers remain, list them in the RFC under "Open Questions".

6. **Hand-off note**
   - Add a final section: "Ready for rfc-reviewer: yes/no".
   - If no, list exactly what is missing.

## Quality bar

- Prefer explicit schemas/contracts over prose summaries.
- Every external interaction must define failure behavior.
- Every implementation chunk must have verifiable acceptance criteria.
- Keep alignment with current codebase patterns unless deviation is justified.

## Additional Resources

- Authoring flow and section requirements: [workflow.md](workflow.md)
- RFC templates by type: [templates.md](templates.md)
- Final pre-review gate: [checklist.md](checklist.md)
