# Lab 0 — Design on one page

**Mission:** decide the architecture before touching Azure. One page, decided trade-offs, merged as a PR. **Timebox: one session, hard stop.** (v1 lesson: plan-growth killed momentum. "Ugly-but-decided" ships.)
**Cost:** $0.

## Deliverable
`docs/architecture/decisions.md` — one page, each decision as:
`**Decision** — choice; alternatives rejected; trade-off accepted; what would make me revisit.`

## Required decisions (at minimum)
1. **Regions:** primary `mexicocentral`? Paired secondary for Phase 6 (westus2? — check mexicocentral's official pair and write it down, don't assume).
2. **Compute targets:** App Service (Linux, B1), Container Apps (Consumption), VMSS (2× B2s, Standard LB) — confirm current SKUs/prices; note which regions each exists in (that's a real deployment gotcha you'll write up).
3. **Data:** Azure SQL **serverless** as default (auto-pause = $0 standing) — record the constraint discovered: geo-replication support on serverless? If not, DR phase runs on a provisioned tier as an event — decide now, write it.
4. **Identity model:** CI = user-assigned managed identity app with Contributor **scoped to `rg-reslab` resource groups only**; never subscription Owner. Data-plane split for app MIs. Who is Owner of the sub and who must never be (the v1 lesson, written down).
5. **Networking:** public ingress + locked data plane (Phase 7 adds private endpoints; design must not preclude them).
6. **Environments:** one environment (`lab`), dev/prod split explicitly rejected — justify.
7. **Cost model:** standing $0; table of per-phase event estimates; $20/mo budget; $10 surprise rule.

## Exit criteria
- [ ] Page merged via PR with ≥ 7 decisions
- [ ] "What would make me revisit" is non-empty for ≥ 4 of them
- [ ] The SQL serverless/geo-replication question answered from docs, cited

## Oral exam
1. Why is auto-pause SQL a trap for latency SLOs — and why is it still the right default here?
2. Name one thing the App Service target can't do that VMSS can, and one that VMSS can't do that CA can. (From your decisions doc, in your words.)
3. Which decisions here does Phase 6 (region DR) force open again? *(Two of them do. Which?)*
