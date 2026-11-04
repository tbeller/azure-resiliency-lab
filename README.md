# azure-resiliency-lab

A hands-on Azure engineering lab built around one thesis: **resiliency is a property of the whole stack, and you earn the right to talk about it by measuring it.**

A deliberately boring application (a URL shortener) is deployed on **three platforms — App Service, Container Apps, and VM Scale Sets** — using the same container image and the same contract, so every resiliency, security, and cost claim can be compared apples-to-apples. Everything is Bicep. Nothing runs when I'm not working. Every drill produces a number.

**Status:** Phase 0 (design) — lab starts now.

## Milestones (public progress tracker)

- [ ] **M1** — Walking skeleton live: pipeline + identity built by hand, hello-container on App Service
- [ ] **M2** — Real app + SQL + Key Vault, observable (KQL queries, alerts, dashboard)
- [ ] **M3** — Platform rotation: same image on Container Apps and VMSS; comparison write-up #1
- [ ] **M4** — Backup & recovery: restore drills executed with measured RTO/RPO; design doc with a *tested* column
- [ ] **M5** — Chaos report: instance-kill drills on all three platforms; incident timelines from platform logs
- [ ] **M6** — **Region failover drill: measured RTO/RPO vs. targets, published DR test report**
- [ ] **M7** — Security scorecard: before/after Defender for Cloud + policy + private endpoints
- [ ] **M8** — Capstone: cost-per-platform comparison + full case study

## Documents

| Doc | What |
|---|---|
| [PROGRAM.md](PROGRAM.md) | The program: phases, rules, cost governance, definition of done |
| [docs/architecture/app-contract.md](docs/architecture/app-contract.md) | The application contract — the platform-agnostic rules the app must satisfy on every target |
| [docs/labs/](docs/labs/) | Lab sheets: objectives, missions, exit criteria, oral exam |
| [docs/sessions/](docs/sessions/) | Session logs — what was built, **what broke, what was measured**, cost |

## Ground rules

1. **IaC or it didn't happen** — Bicep for every resource; teardown path ships in the same PR.
2. **Standing cost $0** — the environment exists only during a session; a $20/mo Azure budget is the cap.
3. **Drills over diagrams** — no resiliency claim ships without a measured number.
4. **Written artifacts are the product** — session logs, design docs, drill reports; the infra is scenery.
5. **I type every line** — coaching, reviews, and oral exams on the other side.

*This is a from-scratch rebuild (Nov 2026) of an August program — new design, new stack, new rules. Nothing carried over.*
