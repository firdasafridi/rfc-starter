# RFC Templates

These templates encode the **Template RFC**
as the **mandatory base format**, and add `Detail` sub-sections that satisfy
`rfc-reviewer` rubric categories so the draft passes the agentic-readiness gate
(PROCEED verdict) without clarification rounds.

## Rules of use

1. Pick exactly one template based on scope: **Frontend**, **Backend**, or **Full-Stack**.
2. **Confluence sections 1–6 + the metadata table are mandatory** — never remove them.
   Mark `N/A` only when truly inapplicable, and explain why in one line.
3. Every `[REQUIRED: …]` placeholder MUST be filled before handing off to `rfc-reviewer`.
   Never leave `TBD`, `to be decided`, or dangling `X or Y` wording.
4. Each `Detail` sub-section is tagged with the rubric code(s) it satisfies
   (e.g., `(satisfies: ACV, FMC)`). Do not delete `Detail` sub-sections — expand them.
5. Status values: `IDEA` | `RFC` | `ABANDON` | `AGREED`.

---

## Frontend RFC Template

```markdown
# RFC: <title>

INSTRUCTIONS
Replace gray italic text with your information. Don’t remove sections, just add N/A if not applicable - unless it’s stated specifically that a section could be removed.

| **Status** | <status_value> | this type IDEA / RFC / ABANDON / AGREED |
| --- | --- | --- |
| **Owner** | <owner_value> | Team Owner of the RFC (e.g. Talenta Growth) |
| **Submitted Date** | <submitted_date> | Author to fill in with RFC discussion submission date |
| **Approver** | <approver_value> | Author to mention relevant tech leaders name, including infosec approver |
| **Related Documents** | <related_docs_value> | Optional. Provide Product Requirement Document (PRD), or any other docs as references. |

**Type:** frontend
**Sub-type:** new-feature | enhancement | performance
**Author:** [REQUIRED]

---

## 1. Overview

[REQUIRED: Problem statement. What does this project do in the grand scheme of the system?
Is this a comprehensive specification or a delta on prior work?]

### Success Criteria
[REQUIRED: prioritized list of high-level outcomes the system must achieve.
Quantify where possible — conversion %, task completion rate, perceived speed delta.]

### Out of Scope
[REQUIRED: explicit non-goals. Anything previously discussed but NOT built this iteration
(e.g., support for legacy app version, exclusion of particular customer segments).]

### Related Documents
[REQUIRED: PRD, design, user research, prior RFCs. For delta specs, link the previous version.]

### Assumptions
[REQUIRED: list assumptions for this iteration.]

### Dependencies
[REQUIRED: external & internal — backend endpoints (with availability status: exists / needs building / blocked),
third-party packages with version + bundle delta, design system components, feature flags, cross-team owners.]

### Detail 1.A — PRD Traceability Matrix _(satisfies: PRT)_
Forward (PRD → RFC):
| PRD requirement | RFC section | Component / file |
|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] |

Reverse (RFC → PRD):
| New component / RFC decision | PRD requirement driving it |
|---|---|
| [REQUIRED] | [REQUIRED] |

> No-PRD exception: tech-debt or perf RFCs may have no PRD. Replace this matrix with a
> self-contained problem + success criteria + non-goals statement (max PRT score: 8.0).

### Detail 1.B — Decisions Closed _(satisfies: TDC)_
| Decision | Chosen option | Alternatives rejected | Why rejected |
|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

---

## 2. Technical Design

### Architecture & Tech Stack
[REQUIRED: system architecture overview with diagram (draw.io / lucidchart / Excalidraw).
Components, communication protocols, framework, state management approach, routing.]

### Sequence
[REQUIRED: UML sequence diagram per scenario, including failure paths
(timeout, 4xx/5xx, race conditions, offline).]

### Database Model
N/A for pure frontend RFC. Document any client-side persistence (localStorage / IndexedDB / cookies):
shape, migration path, eviction policy.

### APIs
[REQUIRED: list every backend endpoint consumed — method, path, availability status
(exists / needs building / blocked), and reference to the contract authority
(backend RFC link, Swagger/OpenAPI spec). Do NOT start dev without an agreed contract.]

### Detail 2.A — UI Contract _(satisfies: CNT)_
For every component introduced or significantly modified:
\`\`\`typescript
interface <ComponentName>Props {
  [REQUIRED: typed prop fields with required/optional + defaults]
}
\`\`\`
- State shape & ownership (which store / context / hook): [REQUIRED]
- Event payloads — analytics + custom events with exact name + properties: [REQUIRED]
- Conditional rendering: example payload per branch: [REQUIRED]

### Detail 2.B — Data-Fetching Strategy _(satisfies: CNT — required for score ≥ 7.0)_
- Library: [REQUIRED: SWR / React Query / RTK Query / Apollo / Pinia / fetch / …]
- Cache key structure: [REQUIRED]
- TTL & refetch triggers (mount / focus / interval / event): [REQUIRED]
- Stale-while-revalidate behavior: [REQUIRED yes/no + behavior]
- Optimistic updates + rollback on failure: [REQUIRED yes/no + mechanism]

### Detail 2.C — UI State Matrix _(satisfies: CNT, FMC)_
For every data-driven surface, define ALL FIVE states.
| Surface | Loading | Empty | Error | Partial | Success |
|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

### Detail 2.D — Scope Boundaries _(satisfies: SCB)_
- Files to create (paths): [REQUIRED]
- Files to modify (paths + reason): [REQUIRED]
- Files explicitly NOT touched (non-goals): [REQUIRED]
- Shared components touched + impact analysis (e.g., Button used in 47 places): [REQUIRED]

### Detail 2.E — Pattern Alignment _(satisfies: CPA)_
- State management pattern + reference file: [REQUIRED]
- Folder convention followed: [REQUIRED]
- Styling pattern (CSS/SCSS/Tailwind/CSS-in-JS): [REQUIRED]
- Error boundary / toast / retry pattern + reference file: [REQUIRED]
- Deviations + ADR reference: [REQUIRED or "none"]

---

## 3. High-Availability & Security

[REQUIRED: HA narrative — graceful degradation when API is slow/down, offline behavior,
recovery from transient failures.]

### Performance Requirement _(satisfies: NFS)_
- LCP target: [REQUIRED]
- INP target: [REQUIRED]
- CLS target: [REQUIRED]
- Bundle size budget + delta vs current: [REQUIRED]
- Code-splitting strategy (route-level / component-level): [REQUIRED]
- Image strategy (WebP/AVIF, lazy-loading, responsive sizing): [REQUIRED]
- Browser support matrix (Chrome/Safari/Firefox/Edge versions, iOS Safari): [REQUIRED]
- Responsive breakpoints + touch targets: [REQUIRED]
- i18n / l10n / RTL: [REQUIRED]

### Monitoring & Alerting _(satisfies: OBS)_
- Analytics events — exact event name + properties + when fires: [REQUIRED]
- Error monitoring (Sentry/Datadog) — severity + sample rate + alert thresholds: [REQUIRED]
- Core Web Vitals tracked + dashboard location: [REQUIRED]
- User-facing success metric tied to PRD (conversion / completion / perceived speed): [REQUIRED]

### Logging _(satisfies: OBS, CDG)_
- Frontend log fields + level: [REQUIRED]
- PII removed from logs (which fields scrubbed): [REQUIRED]


### Security Implications _(satisfies: SAS, CDG)_
- Threat model (XSS, CSRF, third-party script supply chain, clickjacking): [REQUIRED]
- Input sanitization rules per field: [REQUIRED]
- `dangerouslySetInnerHTML` / `v-html` usage + sanitization library: [REQUIRED or "none"]
- Auth token storage (httpOnly cookie / localStorage / memory) + refresh flow: [REQUIRED]
- HTTPS-only resources + CSP additions: [REQUIRED]
- PII handling in client (UU PDP / GDPR / ISO 27701 triggers): [REQUIRED yes/no + classification + retention]
- Secret management (no hard-coded keys; reference to Hashi Vault / config service): [REQUIRED]

### Detail 3.A — Failure Mode Catalog _(satisfies: FMC)_
| API call | 401 | 403 | 404 | 429 | 500 | Timeout (s) | Offline | Retry mechanism |
|---|---|---|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

Plus narrative for: rapid-click race conditions, navigation-during-fetch, third-party script load failure,
WebSocket reconnect with stale state.

### Detail 3.B — Error Message Catalog _(satisfies: FMC — required for score ≥ 7.0)_
| Error code | User-facing message (i18n key) | Surface (toast/inline/banner) | User-facing? |
|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | yes/no |

### Detail 3.C — Accessibility _(satisfies: NFS)_
- WCAG level: AA minimum
- Keyboard navigation flow: [REQUIRED]
- Focus management (modal open/close, route change, dialog dismiss): [REQUIRED]
- ARIA labels on non-semantic interactive elements: [REQUIRED]
- Color contrast ratios verified (tool + result): [REQUIRED]
- `prefers-reduced-motion` handling: [REQUIRED]

---

## 4. Backwards Compatibility and Rollout Plan

### Compatibility _(satisfies: ROL)_
- API contracts changed (request/response shape diff): [REQUIRED or "none"]
- Saved client state (localStorage / cookies / service worker cache) compatibility: [REQUIRED]
- Old bundle / CDN cache invalidation strategy: [REQUIRED]

### Rollout Strategy _(satisfies: ROL)_
- Feature flag name + default state + provisioner + targeting rules: [REQUIRED]
- Rollout stages (internal → 1% → 10% → 100%) with audience + go/no-go evidence: [REQUIRED per stage]
- Stop conditions — specific metric thresholds or error patterns: [REQUIRED]
- Rollback mechanism + behavior for users mid-session: [REQUIRED]
- Blast radius (worst case affected users / surfaces): [REQUIRED]
- PIC + timeline per stage: [REQUIRED]

### Detail 4.A — Configuration Contract _(satisfies: ROL — required)_
| Env var / build config / flag | Type | Default | Required | Provisioner |
|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | yes/no | [REQUIRED] |

### Detail 4.B — Test Plan _(satisfies: TPS)_
- Unit (component / logic + assertions): [REQUIRED]
- Integration (component + store, component + API mock, full user flow): [REQUIRED]
- E2E scenarios — named per user journey + at least one failure path: [REQUIRED]
- Visual regression (snapshot/screenshot): [REQUIRED yes/no]
- Accessibility (axe-core scenarios + keyboard nav): [REQUIRED]
- Performance (Lighthouse CI thresholds, bundle size assertions): [REQUIRED]

### Detail 4.C — Implementation Plan _(satisfies: TDC, SCB)_
| Order | Chunk | Files to modify/create | Acceptance criteria |
|---:|---|---|---|
| 1 | [REQUIRED] | [REQUIRED] | [REQUIRED] |

---

## 5. Concern, Questions, or Known Limitations

[REQUIRED: open questions for reviewer attention; known limitations (rate limit, machine cap);
future scalability plans (e.g., partitioning, sharding, server components migration).]

---

## 6. Comment logs

| **Date** | **Comment(s) From** | **Action Item(s)** |
| --- | --- | --- |
|  |  |  |
|  |  |  |

---

## 7. Ready for rfc-reviewer
- yes | no
- If no, list exactly what is missing per rubric code
  (PRT / TDC / CNT / SCB / DEP / FMC / NFS / TPS / ROL / OBS / CPA):
```

---

## Backend RFC Template

```markdown
# RFC: <title>

INSTRUCTIONS
Replace gray italic text with your information. Don’t remove sections, just add N/A if not applicable - unless it’s stated specifically that a section could be removed.

| **Status** | <status_value> | this type IDEA / RFC / ABANDON / AGREED |
| --- | --- | --- |
| **Owner** | <owner_value> | Team Owner of the RFC (e.g. Talenta Growth) |
| **Submitted Date** | <submitted_date> | Author to fill in with RFC discussion submission date |
| **Approver** | <approver_value> | Author to mention relevant tech leaders name, including infosec approver |
| **Related Documents** | <related_docs_value> | Optional. Provide Product Requirement Document (PRD), or any other docs as references. |

**Type:** backend
**Sub-type:** new-feature | enhancement | tech-improvement
**Author:** [REQUIRED]

---

## 1. Overview

[REQUIRED: problem statement, business / system impact.]

### Success Criteria
[REQUIRED: quantified outcomes (RPS supported, p99 latency, error budget, cost saved, etc.).]

### Out of Scope
[REQUIRED]

### Related Documents
[REQUIRED]

### Assumptions
[REQUIRED]

### Dependencies
[REQUIRED: external (Google API, AWS, SSO, Mekari Payment, …) and internal services
with owning team and confirmed availability.]

### Detail 1.A — PRD Traceability Matrix _(satisfies: PRT)_
Forward (PRD → RFC):
| PRD requirement | Service / endpoint / job | RFC section |
|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] |

Reverse (RFC → PRD):
| New endpoint / table / service / dependency | PRD need it serves |
|---|---|
| [REQUIRED] | [REQUIRED] |

> No-PRD exception: tech-debt / infra RFCs may have no PRD. Replace with self-contained
> problem + success criteria + non-goals (max PRT score: 8.0).

### Detail 1.B — Decisions Closed _(satisfies: TDC)_
| Decision | Chosen option | Alternatives rejected | Why rejected |
|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

---

## 2. Technical Design

### Architecture & Tech Stack _(satisfies: SBC, CPA)_
[REQUIRED: architecture diagram. Communication protocols. Service ownership of the change —
named, not implied. Pattern alignment with existing repository / service / queue conventions.]

- Owning service / module: [REQUIRED]
- Cross-service calls introduced (sync vs async + reasoning): [REQUIRED]
- New coupling created + justification: [REQUIRED or "none"]
- Pattern files mirrored (e.g., `orders/repository.go`, `internal/queue/consumer.go`): [REQUIRED]
- Deviations + ADR reference: [REQUIRED or "none"]

### Sequence _(satisfies: DIC)_
[REQUIRED: UML sequence diagram per scenario including failure cases — timeouts, partial
failure, retry, compensating actions.]

### Database Model _(satisfies: DMS)_
[REQUIRED: full DDL for every new or altered table.]
\`\`\`sql
CREATE TABLE [REQUIRED] (
  [REQUIRED: column TYPE NOT NULL DEFAULT … CHECK (…) REFERENCES …]
);
CREATE INDEX [REQUIRED: name] ON [REQUIRED] ([REQUIRED]);  -- supports query: [REQUIRED]
\`\`\`
- Cardinality estimate + growth projection: [REQUIRED]
- Example rows: [REQUIRED]
- PII classification per column: [REQUIRED]
- Retention policy per table: [REQUIRED]
- Partition / sharding key + rationale: [REQUIRED or "none"]
- NoSQL alternative considered + why rejected: [REQUIRED]

### APIs _(satisfies: ACV)_
[REQUIRED: OpenAPI 3 / Swagger doc link encouraged. Contract MUST be consumer-ready
before development begins. Review with FE / mobile team for UX feasibility.]

| Endpoint | Method | AuthN/AuthZ (middleware + scope) | Request schema | Response schema | Status codes | Idempotency mechanism | Versioning |
|---|---|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

Per endpoint also include:
- Example request / response payloads per scenario
- Rate limits + payload size limits
- Pagination convention (offset vs cursor)
- Backward compatibility (preserved consumers vs deprecation window)

### Detail 2.A — Data Integrity Matrix _(satisfies: DIC)_
For every write path:
| Write path | Transaction scope (atomic with what) | Partial failure behavior | Idempotency key + TTL | Consistency model | Duplicate-event handling | Stale-read handling |
|---|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | strong / eventual | [REQUIRED] | [REQUIRED] |

### Detail 2.B — Concurrency Collision Map _(satisfies: CSS)_
For every shared resource:
| Resource | Writers (actors) | Collision scenario | Resolution mechanism (lock / optimistic / queue) | Behavior when lock fails / optimistic check fails |
|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

### Detail 2.C — Async Job / Event Consumer Spec _(satisfies: FMC, DIC)_
For every background worker / cron / queue consumer / event handler:
| Job/Consumer | Trigger | Input shape | Retry (attempts + backoff) | DLQ name + retention | Concurrency limit | Idempotency key | Per-message timeout | Poison-message handling |
|---|---|---|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

---

## 3. High-Availability & Security

[REQUIRED: HA plan — uptime / APDEX / SLA target; statelessness; horizontal-scale model;
behavior when dependencies are offline; transient-failure recovery; full-restart recovery
of database / queue / dependent services.]

### Performance Requirement _(satisfies: CSS, FMC)_
- Expected RPS sustained, p95, p99, max error rate (e.g., 500 RPS, p99 < 500ms, 0.01% error): [REQUIRED]
- Scalability measures (HPA metric, read replicas, cache strategy with stampede protection): [REQUIRED]
- Load test plan (concurrent users, ramp profile, sustain duration, request mix): [REQUIRED]

### Monitoring & Alerting _(satisfies: OBS)_
- RED metrics (Rate / Errors / Duration) — exact metric names + tags: [REQUIRED]
- USE metrics (Utilization / Saturation / Errors) where relevant: [REQUIRED]
- Trace span names + parent-child relationships: [REQUIRED]
- Alert thresholds + escalation path: [REQUIRED]
- SLO targets (availability, latency, error budget over 30d): [REQUIRED]
- Dashboard location / spec: [REQUIRED]
- "Debug at 3am" runbook for complex paths: [REQUIRED]

### Logging _(satisfies: OBS, CDG)_
- Structured log fields per path + level: [REQUIRED]
- PII removed from logs (which fields scrubbed): [REQUIRED]


### Security Implications _(satisfies: SAS, CDG)_
- Threat model (attacker intent + entry points): [REQUIRED]
- AuthN / AuthZ at every trust boundary (specific middleware + JWT scopes + role checks): [REQUIRED]
- Ownership validation (e.g., `order.user_id = jwt.sub`) + enforcement layer: [REQUIRED]
- Input validation rules per field (length / charset / enum / format / cross-field): [REQUIRED]
- Injection mitigations (SQL via parameterization/ORM, command, SSRF on outbound URLs): [REQUIRED]
- Secrets storage + rotation (Hashi Vault / env / config) + log-leak prevention: [REQUIRED]
- Audit logging (action / fields / sink): [REQUIRED]
- Rate limiting per user-triggered endpoint: [REQUIRED]
- Tenancy isolation enforcement point: [REQUIRED]
- Static analysis tool (Brakeman / phpcs-security-audit / SonarScan / similar): [REQUIRED]
- Public-exposure protections (whitelist domain/IP, DDoS, pen-test plan): [REQUIRED if exposed]
- ISO 27001 / 27701 compliance touchpoints: [REQUIRED]

### Detail 3.A — Failure Mode & Retry Catalog _(satisfies: FMC)_
| External call | Timeout | Retries (count + backoff) | Circuit breaker (threshold + window) | DLQ + retention | Caller behavior on persistent failure |
|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

### Detail 3.B — Error Response Catalog _(satisfies: FMC, ACV — required for FMC ≥ 7.0)_
Consistent error response shape across all endpoints:
\`\`\`json
{ "error": "CODE", "message": "human-readable", "details": {} }
\`\`\`
| Endpoint | Error code | HTTP status | Message | When it occurs | User-facing? |
|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | yes/no |

### Detail 3.C — Compliance & Data Governance _(satisfies: CDG — when triggered)_
Trigger if RFC touches PII, payment, health data, user-generated content retention,
auth logs, audit trails, or cross-border data transfer.
| Field | Classification (PII / sensitive / public) | Legal basis (UU PDP / GDPR clause) | Retention | Encryption (rest + transit) | Access audit | Right-to-delete path |
|---|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] |

> If no trigger, write: `N/A — no compliance trigger; verified no PII / payment / health / audit data touched.`

---

## 4. Backwards Compatibility and Rollout Plan

### Compatibility _(satisfies: MRP, ACV)_
- Existing endpoints / request / response shapes changed: [REQUIRED or "none"]
- Compatibility window (how long old behavior maintained): [REQUIRED]
- Consumer notification plan + timeline: [REQUIRED]
- API version strategy (additive / breaking + deprecation window): [REQUIRED]

### Rollout Strategy _(satisfies: MRP)_
- Migration sequence (e.g., add nullable column → backfill → dual-write → switch reads → drop): [REQUIRED]
- Schema state DURING migration (intermediate state explicit, not just start/end): [REQUIRED]
- Backfill (batch size / rate / ETA / production traffic impact): [REQUIRED]
- Feature flag (name, default state, targeting, kill-switch behavior): [REQUIRED]
- Rollout stages with go/no-go evidence: internal → 1% → 10% → 100% [REQUIRED per stage]
- Rollback trigger (specific metric / error pattern thresholds): [REQUIRED]
- Rollback mechanism + treatment of data written during rollout: [REQUIRED]
- PIC + timeline per stage: [REQUIRED]

### Detail 4.A — Configuration Contract _(satisfies: MRP — required)_
| Env var / config / flag | Type | Default | Required | Provisioner | Secret? |
|---|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | yes/no | [REQUIRED] | yes/no |

### Detail 4.B — Implementation Plan _(satisfies: TDC, SBC)_
| Order | Chunk | Files to modify/create | Acceptance criteria |
|---:|---|---|---|
| 1 | [REQUIRED] | [REQUIRED] | [REQUIRED] |

### Detail 4.C — Resource & Cost Notes _(advisory — not scored)_
- Compute footprint (pods / CPU / memory / autoscale range): [REQUIRED or "no impact"]
- DB load delta + connection delta vs current headroom: [REQUIRED]
- Network egress (cross-AZ / cross-region / external API calls): [REQUIRED]
- Storage growth per table per month + retention impact: [REQUIRED]
- New infra components + estimated cost / licensing: [REQUIRED]

---

## 5. Concern, Questions, or Known Limitations

[REQUIRED: open questions for reviewer; known limits (rate limit, machine cap, queue depth);
future scalability plan (partitioning, sharding) with timeframe.]

---

## 6. Comment logs

| **Date** | **Comment(s) From** | **Action Item(s)** |
| --- | --- | --- |
|  |  |  |
|  |  |  |

---

## 7. Ready for rfc-reviewer
- yes | no
- If no, list exactly what is missing per rubric code
  (PRT / TDC / DMS / ACV / DIC / FMC / CSS / SAS / MRP / OBS / SBC / CPA / CDG):
```

---

## Full-Stack RFC Template

```markdown
# RFC: <title>

INSTRUCTIONS
Replace gray italic text with your information. Don’t remove sections, just add N/A if not applicable - unless it’s stated specifically that a section could be removed.

| **Status** | <status_value> | this type IDEA / RFC / ABANDON / AGREED |
| --- | --- | --- |
| **Owner** | <owner_value> | Team Owner of the RFC (e.g. Talenta Growth) |
| **Submitted Date** | <submitted_date> | Author to fill in with RFC discussion submission date |
| **Approver** | <approver_value> | Author to mention relevant tech leaders name, including infosec approver |
| **Related Documents** | <related_docs_value> | Optional. Provide Product Requirement Document (PRD), or any other docs as references. |

**Type:** full-stack
**Frontend sub-type:** new-feature | enhancement | performance
**Backend sub-type:** new-feature | enhancement | tech-improvement
**Author:** [REQUIRED]

---

## 1. Overview

[REQUIRED: problem statement spanning user + system outcome.]

### Success Criteria
[REQUIRED]

### Out of Scope
[REQUIRED]

### Related Documents
[REQUIRED]

### Assumptions
[REQUIRED]

### Dependencies
[REQUIRED — both layers]

### Detail 1.A — PRD Traceability (cross-layer) _(satisfies: PRT)_
Forward:
| PRD requirement | FE section / component | BE section / endpoint |
|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] |

Reverse:
| New FE component / BE endpoint / dependency | PRD need |
|---|---|
| [REQUIRED] | [REQUIRED] |

> Cross-layer check: flag any PRD requirement where FE and BE RFC sections contradict on
> scope or behavior. These are blockers.

### Detail 1.B — Decisions Closed (cross-layer) _(satisfies: TDC)_
| Decision | Chosen option | Alternatives rejected | Why rejected | Layer |
|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | [REQUIRED] | FE / BE / both |

> Explicitly note any decision where FE and BE could disagree (REST vs gRPC, offset vs cursor
> pagination, casing, error shape) and confirm both layers are aligned.

---

## 2. Technical Design

### Architecture & Tech Stack _(satisfies: SBC, CPA)_
[REQUIRED: end-to-end diagram showing FE ↔ BFF/API gateway ↔ services ↔ DB / queue.
Pattern files cited per layer.]

### Sequence _(satisfies: DIC)_
[REQUIRED: end-to-end sequence per scenario, including failure paths.]

### Database Model _(satisfies: DMS)_
[REQUIRED: same DDL detail as Backend template §2 Database Model.]

### APIs _(satisfies: ACV, CNT)_
[REQUIRED: same endpoint table as Backend template §2 APIs. Reference Swagger / OpenAPI.]

### Detail 2.A — UI Contract _(satisfies: CNT)_
[Same structure as Frontend template Detail 2.A.]

### Detail 2.B — Data-Fetching Strategy _(satisfies: CNT)_
[Same as Frontend template Detail 2.B.]

### Detail 2.C — UI State Matrix _(satisfies: CNT, FMC)_
[Same as Frontend template Detail 2.C.]

### Detail 2.D — Data Integrity Matrix _(satisfies: DIC)_
[Same as Backend template Detail 2.A.]

### Detail 2.E — Concurrency Collision Map _(satisfies: CSS)_
[Same as Backend template Detail 2.B.]

### Detail 2.F — Async Job / Event Consumer Spec _(satisfies: FMC, DIC)_
[Same as Backend template Detail 2.C.]

### Detail 2.G — Cross-Layer Contract Verification _(satisfies: ACV, CNT — full-stack mandatory)_
| Endpoint | BE response schema | FE expected schema | Match? | Gaps (casing / nullability / error shape / pagination / auth) |
|---|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | yes / no / partial | [REQUIRED] |

> Any "no" or "partial" → blocker. Add a transformation layer or align contracts.

### Detail 2.H — End-to-End Data Flow _(satisfies: PRT, TDC — full-stack mandatory)_
For each major user action, trace the full path:
\`User action → FE component → API call (method + path) → BE handler → service → DB write/read → response → FE state update → UI render\`
- Side effects in the flow (events / notifications / audit logs): [REQUIRED]
- Step-by-step ownership (which layer / team owns each step): [REQUIRED]

### Detail 2.I — Pattern Alignment _(satisfies: CPA)_
- FE pattern files mirrored: [REQUIRED]
- BE pattern files mirrored: [REQUIRED]
- Cross-layer naming convention (e.g., snake_case API → camelCase FE) + transformation layer named: [REQUIRED]
- Deviations + ADR reference: [REQUIRED or "none"]

### Detail 2.J — Scope Boundaries _(satisfies: SCB)_
- FE files create / modify / NOT touched: [REQUIRED]
- BE files create / modify / NOT touched: [REQUIRED]
- Shared modules touched + impact analysis: [REQUIRED]

---

## 3. High-Availability & Security

[REQUIRED: HA narrative covering both layers.]

### Performance Requirement _(satisfies: NFS, CSS)_
- Frontend: LCP / INP / CLS / bundle budget / browser support / a11y level — [REQUIRED]
- Backend: RPS / p95 / p99 / error rate / scalability measures / load test plan — [REQUIRED]

### Monitoring & Alerting _(satisfies: OBS)_
- FE: analytics events + error monitoring + Core Web Vitals dashboards: [REQUIRED]
- BE: RED/USE metrics + alerts + SLO + dashboards: [REQUIRED]
- Cross-layer: distributed traces FE → API → BE (trace ID propagation mechanism named): [REQUIRED]

### Logging _(satisfies: OBS, CDG)_
- FE log fields + level: [REQUIRED]
- BE structured log fields + level: [REQUIRED]
- PII removal across both layers: [REQUIRED]


### Security Implications _(satisfies: SAS, CDG)_
- Backend: full Backend §3 Security checklist
- Frontend: input sanitization + auth token handling + CSP
- Cross-layer: token refresh flow + session-invalidation propagation: [REQUIRED]

### Detail 3.A — Failure Mode Catalog (merged) _(satisfies: FMC — full-stack mandatory)_
| Surface | FE behavior on failure | BE response on failure | Code-shape consistency check (FE handles BE's exact codes) |
|---|---|---|---|
| [REQUIRED] | [REQUIRED] | [REQUIRED] | yes/no |

### Detail 3.B — Error Response Catalog (BE) _(satisfies: FMC, ACV)_
[Same as Backend template Detail 3.B.]

### Detail 3.C — Error Message Catalog (FE) _(satisfies: FMC)_
[Same as Frontend template Detail 3.B.]

### Detail 3.D — Compliance & Data Governance _(satisfies: CDG — when triggered)_
[Same as Backend template Detail 3.C.]

### Detail 3.E — Accessibility _(satisfies: NFS)_
[Same as Frontend template Detail 3.C.]

---

## 4. Backwards Compatibility and Rollout Plan

### Compatibility _(satisfies: ROL, MRP, ACV)_
- BE compatibility window + API versioning: [REQUIRED]
- FE saved state / cache compatibility: [REQUIRED]
- Cross-layer compatibility (API contract evolution + transformation rules): [REQUIRED]

### Rollout Strategy _(satisfies: ROL — cross-layer mandatory)_
- **Deploy order** (FE first / BE first / simultaneous) + reasoning: [REQUIRED]
- Feature flag coordination — single flag or two? coupling rules + independent toggling: [REQUIRED]
- Rollback per layer + sequence: [REQUIRED]
- Stop conditions per layer: [REQUIRED]
- PICs + timeline per stage: [REQUIRED]

### Detail 4.A — Cross-Layer Rollout Compatibility Matrix _(satisfies: ROL — full-stack mandatory)_
| Scenario | FE | BE | Works? | Mitigation |
|---|---|---|---|---|
| Pre-deploy | Old | Old | yes | baseline |
| Backend first | Old | New | [REQUIRED] | [REQUIRED] |
| Frontend first | New | Old | [REQUIRED] | [REQUIRED] |
| Both deployed | New | New | yes | target state |
| Backend rollback | New | Old (rolled back) | [REQUIRED] | [REQUIRED] |
| Frontend rollback | Old (rolled back) | New | [REQUIRED] | [REQUIRED] |

> Any "no" in this matrix MUST have a mitigation in the rollout plan or the deploy order
> must avoid that scenario. Score cap: ROL ≤ 6.0 if deploy order or compat matrix is missing.

### Detail 4.B — Configuration Contract _(satisfies: ROL, MRP)_
| Layer | Env var / flag | Type | Default | Required | Provisioner | Secret? |
|---|---|---|---|---|---|---|
| FE / BE | [REQUIRED] | [REQUIRED] | [REQUIRED] | yes/no | [REQUIRED] | yes/no |

### Detail 4.C — Test Plan _(satisfies: TPS — extended for cross-layer)_
- FE: unit + integration + E2E + a11y + perf — [REQUIRED]
- BE: unit + integration with real DB + contract tests — [REQUIRED]
- Cross-layer integration: scenarios crossing the API boundary (named scenarios per user journey): [REQUIRED]

### Detail 4.D — Implementation Plan _(satisfies: TDC, SBC, SCB)_
| Order | Layer | Chunk | Files to modify/create | Acceptance criteria |
|---:|---|---|---|---|
| 1 | FE / BE / both | [REQUIRED] | [REQUIRED] | [REQUIRED] |

### Detail 4.E — Resource & Cost Notes _(advisory — not scored)_
[Same as Backend template Detail 4.C.]

---

## 5. Concern, Questions, or Known Limitations

[REQUIRED]

---

## 6. Comment logs

| **Date** | **Comment(s) From** | **Action Item(s)** |
| --- | --- | --- |
|  |  |  |
|  |  |  |

---

## 7. Ready for rfc-reviewer
- yes | no
- If no, list exactly what is missing per rubric code
  (PRT / TDC / CNT / SCB / DEP / NFS / TPS / DMS / ACV / DIC / FMC / CSS / SAS / ROL / OBS / SBC / CPA / CDG):
```
