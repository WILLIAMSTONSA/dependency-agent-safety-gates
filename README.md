# Safety Gates for Agent-Proposed Dependency Updates

A dependency bot or coding agent may propose a change; it must not grant itself approval. Treat every update as a state machine with independent evidence, human ownership, and a tested rollback.

## Safety envelope

- Build in a disposable worker with no production credentials.
- Default-deny network after the explicitly allowlisted package fetch phase.
- Inspect new/changed install, lifecycle, post-install, build, and code-generation scripts before execution; disable them where the ecosystem safely supports that.
- Use lockfiles, registry configuration, exact artifact identity/digests where available, minimum CI token permissions, and minimum privilege for filesystem and package-registry access.
- Never auto-merge because a vulnerability count is zero or a repository score is high.
- Preserve the old artifact/lockfile and verify rollback before rollout.

## Gate state machine

```text
PROPOSED
  → RESOLVED (exact graph + lockfile diff)
  → BUILT_ISOLATED
  → TESTED
  → SCANNED_AND_POLICY_CHECKED
  → HUMAN_APPROVED
  → CANARY
  → RELEASED
  → MONITORED
failure at any stage → HELD or ROLLED_BACK
```

The fillable implementation is [`assets/update-gate.md`](assets/update-gate.md).

## Required evidence by state

| State | Required evidence | Hard stop examples |
|---|---|---|
| PROPOSED | reason, direct/transitive scope, current/target versions, owner | vague “latest is better,” unowned major update |
| RESOLVED | manifest + lockfile diff, resolved source/version/digest, added/removed transitive graph, lifecycle script diff | unexpected registry/source/package, unexplained transitive growth |
| BUILT_ISOLATED | clean disposable build, network/credential policy, artifact digest/SBOM/provenance if available | script seeks credentials, writes outside workspace, undeclared network |
| TESTED | unit/integration/compatibility/migration tests and negative cases for affected surface | migration or compatibility failure, missing required environment |
| SCANNED_AND_POLICY_CHECKED | vulnerability/license/policy results with baseline and exceptions | unresolved policy violation; scanner error reported as zero |
| HUMAN_APPROVED | named accountable reviewer, residual risks, rollback/canary plan | bot/self-approval, score used as authorization |
| CANARY | bounded cohort/time, health metrics, abort threshold | no observability or no rollback path |
| RELEASED/MONITORED | artifact identity, rollout record, post-release checks | released artifact differs from reviewed artifact |

## Tool placement—not tool authority

- [Renovate](https://github.com/renovatebot/renovate) and [Dependabot Core](https://github.com/dependabot/dependabot-core): update proposal/resolution implementations. A proposal is not approval.
- [Syft](https://github.com/anchore/syft): SBOM inventory evidence.
- [OSV-Scanner](https://github.com/google/osv-scanner), [Grype](https://github.com/anchore/grype), and [Trivy](https://github.com/aquasecurity/trivy): known vulnerability/artifact evidence for supported inputs; no result proves a specific package is non-malicious or compatible.
- [Conftest](https://github.com/open-policy-agent/conftest): structured policy checks; policy correctness is still project-owned.
- [OpenSSF Scorecard](https://github.com/ossf/scorecard): upstream repository-practice context; never a release verdict for a version.

These sources support tool roles. The state machine and required evidence are this guide’s authored safety policy and must be tailored to the ecosystem.

## Lockfile and script review

For every update, answer:

- Did package origin, integrity/digest, platform artifact, or registry change?
- Which direct/transitive packages appeared or disappeared?
- Did install/build scripts change or become newly reachable?
- Are native binaries, downloaded executables, or code generators introduced?
- Did licenses or package ownership/maintenance change?
- Does a major/minor update require data/config migration?
- Can the old lockfile/artifact be restored without forward-incompatible data?

Do not run a new dependency’s scripts on a developer machine carrying SSH/cloud/browser credentials merely to inspect it.

## Canary and rollback

Define before approval:

- previous lockfile/artifact digest;
- rollback command and accountable owner;
- data/schema compatibility and backup restore point;
- canary scope/duration;
- abort metrics (errors, latency, resource use, functional invariant);
- maximum decision time and what happens when telemetry is missing.

Missing telemetry is not success. Roll back or hold according to the predeclared policy.

## Boundary with patch review

`ai-patch-review-playbook` reviews one arbitrary patch and its changed invariants. This repository owns the **dependency PR lifecycle**, including resolution, package scripts, canary, and rollback. Shared tools (OSV/Trivy/Scorecard) do not make the policies duplicates.

## Maintenance

Change the gate when an observed incident or ecosystem behavior changes required evidence. Pin tool/policy versions in actual CI. Never weaken a gate automatically to make an update green; exceptions require scope, expiry, and owner.

Sources: [`sources/sources.json`](sources/sources.json).

## License

Original gate state machine and template are available under CC BY 4.0. Linked tools retain their licenses.
