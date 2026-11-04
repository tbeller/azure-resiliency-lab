# Lab 1 — Trust from zero (identity & OIDC)

**Mission:** build the deployment identity the entire lab will run on — app registration, scoped RBAC, and a GitHub-Actions OIDC federated credential, by hand, and prove the exchange works with a call that touches Azure.
**Cost:** $0 (identity objects are free). **Time:** 60–90 min.
**Why first:** every future lab deploys through this chain. Every resiliency claim you make later inherits from "who was allowed to deploy this, and how did Azure know it was them?" You review customers' identity posture at work; today you build the posture you'd tell them to have.

## The chain (draw it in the session log from memory, then verify your drawing)
```
GitHub Actions (this repo, ref main)
  → requests a GitHub-issued OIDC token (audience api://AzureADTokenExchange)
  → redeems at Entra: app registration + FEDERATED CREDENTIAL must accept
       issuer: https://token.actions.githubusercontent.com
       subject: repo:tbeller/azure-resiliency-lab:ref:refs/heads/main
  → service principal (the identity) acts with its RBAC role
       Contributor on rg-reslab ONLY
```

## Missions
1. **Resource group:** `rg-reslab` (mexicocentral), tags `project=reslab, env=lab, owner=tom`. Verify via `az group list`.
2. **App registration + SP:** `az ad app create` (name: `gh-reslab`). Create the SP.
3. **Scope the leash:** Contributor on `rg-reslab` **only**. Write in the session log: what the v1 CI identity was allowed to do (subscription Owner — read `az role assignment list` history lesson in your head), and name two concrete attack paths from Owner-on-CI, one from RG-scoped Contributor.
4. **Federated credential:** create on the SP (issuer/subject/audience above). Answer in the log: why `ref:refs/heads/main` and not `repo:tbeller/azure-resiliency-lab:*`? What does a rogue feature branch get with the wild subject?
5. **Wire GitHub:** repo secrets `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` (app id), `AZURE_SUBSCRIPTION_ID`. (Values from `az account show` — you type them; they're not secrets-leakable, client/tenant ids are public identifiers.)
6. **Prove it:** minimal workflow `proof-of-trust.yml` (workflow_dispatch): `azure/login@v2` with the three ids, then `az group list -o table` and `az ad signed-in-user show` equivalent. Run it. **The login must be performed by the SP, not a user.**
7. **Negative test (the good part):** dispatch the workflow from a branch other than `main` (add `on: workflow_dispatch` on any branch push, or temporarily change the subject) → login must **fail** with a federated-credential mismatch. Read the error, paste it in the session log, then fix to green.

## Exit criteria
- [ ] Positive: workflow dispatch on main → login as SP + resource listing works
- [ ] Negative: off-main subject rejected, error read and logged
- [ ] Session log: chain diagram from memory, RBAC scoping write-up, negative-test error + explanation
- [ ] Nothing billable exists (`az group list` = `rg-reslab` empty)

## Oral exam
1. The four hops: GitHub JWT → Entra token → SP → ARM call. Where does the audience value get checked, and by whom?
2. What exactly ties "this repo's main branch" to a GitHub-issued token? (Answer: subject prefix matching on the OIDC claim — say what an attacker who controls a PR to main *can't* forge.)
3. Contributor on one RG: list two actions it permits and two it must not (identity writes? role assignments? policy bypass?). Where does Contributor's data-plane reach on a storage account worry you?
4. You'll later add a *second* env (dr). What's the smallest identity change?
