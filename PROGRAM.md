# The Program (v3)

> Product = hands-on hours + public written artifacts. The infrastructure is the curriculum; the boring app is the lab rat.

**Budget law:** $0 standing · $20/mo Azure budget (set) · events pre-estimated, verified at teardown · $10 surprise = stop everything.
**Cadence:** 3–4 sessions/week, 60–90 min. A session ends at: objective met → PR merged → session log written → cost check.
**Coaching rule:** Tom types every line. Hermes prepares labs, reviews PRs, posts estimates, verifies teardowns, and closes sessions with an oral exam.

## Why this phase order

Each phase adds exactly **one new axis** and ends with a public artifact — progression you can see from outside:

| Phase | New axis added | Ends with (public) |
|---|---|---|
| 0 Design | intent | 1-page architecture decision doc (timeboxed — ugly-but-decided beats beautiful-but-unbuilt) |
| 1 Identity & pipeline | *how anything gets built here* | live walking-skeleton URL (M1) |
| 2 App & data | a workload worth keeping | observable app: logs, KQL, alerts (M2) |
| 3 Platform rotation | breadth: same app, three platforms | platform comparison write-up (M3) |
| 4 Data protection | depth: recovery of the data | restore drills w/ measured RTO/RPO + design doc (M4) |
| 5 Resilience & chaos | depth: recovery of the *service* | chaos report, 3 platforms (M5) |
| 6 Multi-region DR | breadth: geography | **measured region failover — DR test report (M6, headline artifact)** |
| 7 Security & governance | hardening | security scorecard before/after (M7) |
| 8 FinOps & capstone | judgment | cost-per-platform comparison + case study (M8) |

Rationale: identity before deployment (every later drill trusts this chain); one platform before three (comparison needs a reference implementation); data protection before chaos (you break what you can restore); DR last (it composes everything). Phases 6–8 are interview-answer material; 1–5 make them credible.

## Phases & labs

### Phase 0 — Design (Lab 0) · 1 session
Architecture decision doc on one page: regions (primary **mexicocentral**, secondary paired), the three targets, identity model (CI = Contributor scoped to resource groups, never subscription Owner), SQL choice (serverless default; provisioned for DR phase — recorded as decision), state strategy (app stateless, all state in Azure SQL), cost model per phase. Exit: doc merged, decisions listed with trade-offs.

### Phase 1 — Identity & pipeline (Labs 1–2)
- **Lab 1: Trust from zero.** Resource group `rg-reslab`; Entra app registration; **federated credential built by hand** (issuer/subject/audience — what each validates and why ref-scoped subjects beat repo-wide); GitHub secrets; OIDC login proven in Actions. Oral exam: the four hops of an OIDC exchange.
- **Lab 2: Walking skeleton.** Bicep skeleton (RG, App Service plan B1, web app), `pr-validation` (what-if) on PRs, deploy on merge, hello-container from ghcr. **M1: first public URL.** Cold-deploy timed; teardown verified.

### Phase 2 — App & data (Labs 3–5)
- **Lab 3: The app.** Containerized URL shortener meeting [app-contract.md](docs/architecture/app-contract.md) (built with AI assistance — the app is not the curriculum; the *contract* is).
- **Lab 4: State & secrets.** SQL (serverless), Entra-authanced via user-assigned MI, Key Vault references, RBAC to data plane. Decision doc: serverless behavior under load.
- **Lab 5: Observability.** Log Analytics + App Insights, 5 hand-written KQL queries in `docs/kql/`, alert set with severity table. **M2.**

### Phase 3 — Platform rotation (Labs 6–8)
- **Lab 6: Container Apps.** Same image; zone-redundant by default; ingress, scale-to-zero trade-off; MI story across platforms.
- **Lab 7: VM Scale Sets.** Same image via cloud-init/systemd; LB health probes; zone spread; autoscale rules.
- **Lab 8: Comparison write-up #1.** Cost, patch surface, resiliency defaults, blast-radius, operational burden — three platforms, same workload. **M3.** (This artifact has no competition on GitHub.)

### Phase 4 — Data protection (Labs 9–12)
SQL PITR restore after deliberate corruption (measured); soft-delete delete-recovery; Key Vault secret backup/restore; WORM/immutability on a backup storage account (demonstrated: overwrite refused). **Artifact: Backup & Recovery design doc — threat table with a *tested* column. M4.**

### Phase 5 — Resilience & chaos (Labs 13–15)
Kill an instance on each platform; does LB/probe/autoscale heal it? Read the incident timeline from platform logs; graceful degradation + rate-limit drill. **Artifact: chaos report, per-platform healing times. M5.**

### Phase 6 — Multi-region DR (Labs 16–19)
Parameterized-region Bicep (one template, two geographies); Traffic Manager front door; SQL geo-replica (provisioned, **event-only**, ≤ $8 pre-estimated); **measured failover + failback drill** with RTO/RPO vs. pre-committed targets; unannounced DR-surprise drill; runbook a stranger can execute. **Artifact: DR TEST REPORT — the headline. M6.**

### Phase 7 — Security & governance (Labs 20–22)
Azure Policy (deny/audit in Bicep, drift check in CI); RBAC review (CI identity scoped to RGs, data-plane split); private endpoints (SQL + Key Vault + CA), public paths demonstrably refused; Defender for Cloud score before/after. **Artifact: security scorecard. M7.**

### Phase 8 — FinOps & capstone (Labs 23–25)
Cost-per-platform for identical workload (measured, not estimated); elasticity/scale event; retrospective: every drill → interview story (STAR), resume Projects section unfreezes with numbers. **Artifact: capstone case study. M8.**

## Session log format (`docs/sessions/NNN-slug.md`)
Objective · what I built · **what broke** · **what I measured** · lessons · cost delta + teardown verified · oral-exam results.

## Definition of done
25 labs, ~10–12 weeks · ≥ 8 executed drills with numbers · M1–M8 public · commit graph active weekly · capstone case study done · **program outlives job search: it feeds interviews as it runs.**

## Out of scope (scope-freeze)
No Kubernetes beyond what CA teaches. No Terraform until v4. No CI identity at subscription Owner scope, ever. No feature creep in the app — the contract is closed.
