# Dependency update gate

Package/change: `<fill>`  
Current → target: `<fill>`  
Human owner / rollback owner: `<fill>`

| State | Evidence | Pass / hold / rollback | Owner |
|---|---|---|---|
| PROPOSED — reason/scope |  |  |  |
| RESOLVED — manifest, lockfile, graph, source/digest, scripts |  |  |  |
| BUILT_ISOLATED — clean worker, no production credentials, bounded network |  |  |  |
| TESTED — compatibility, migration, negative cases |  |  |  |
| SCANNED — vulnerability/license/policy with baseline |  |  |  |
| HUMAN_APPROVED — residual risk, exception expiry |  |  |  |
| CANARY — cohort, duration, abort threshold |  |  |  |
| RELEASED — reviewed artifact identity |  |  |  |
| MONITORED — post-release evidence |  |  |  |

## Script and supply-chain review

- [ ] Registry/source/integrity changes understood.
- [ ] Added/removed transitive graph reviewed.
- [ ] Install/lifecycle/build/codegen scripts reviewed before execution.
- [ ] Native/downloaded artifacts identified.
- [ ] SBOM/provenance captured where available.
- [ ] Scanner errors remain errors, not zero findings.
- [ ] Scorecard/scanner output is not used as automatic approval.

## Rollback

Previous lockfile/artifact digest:  
Data/schema restore point:  
Rollback procedure tested:  
Canary abort metrics:  
Action when telemetry is missing:
