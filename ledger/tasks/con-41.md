---
task: CON.41
status: complete
recorded: 2026-10-11
claimant: w-deku-20261011-con-41
epoch: 1
---

# Publish the public operation metadata in the PublicApi package

## Evidence

Each statement is marked by its source. **Observed** means public GitHub state that this author read on 2026-10-11 with `gh pr view`, `gh api`, `gh run view --json jobs` and greps of `gh run view --job <id> --log`. Release metadata was not needed, and no artifact or package was downloaded. **Claimant-reported** means it comes from the PR body or the claimant. **Reviewer-reported** means it comes from the independent review comment. **Coordinator-reported** means it comes from the coordinator.

- **Planning authority.** CON.41 is a new task from planning repair fix8: Design #350 (`f4b2299c`) and Plan #479 (`f7cb511a`), under coordinator ruling S57(12) (coordinator-reported; this author did not re-read those PRs). The fix8 delta S59(7) adds PLT.66's contract start edge on this task (S62(1): "PLT.58" in that ruling means PLT.66). `python -I rec.py CON.41` lists `complete: []`, so this task has no completion edges.
- **Start prerequisites** (read in the Plan ledger at `641b4d8f`):
  - CON.18: `complete`. It provides the operation-metadata.v1 export schema, the eng/operations exports and `eng/check_operation_scope.py`.
  - CON.07: `complete`. It provides the 49 public IdentityService, WorkspaceService and DeviceService rows.
  - CON.40: `delivered`. Its artifact (the C#-only packaging pipeline) is merged at Contracts main `56158219`, the base of this task's PR.
- **Claim** (observed on Plan branch `claims/con-41`): CON.41 epoch 1, claimant `w-deku-20261011-con-41`.
  - It was claimed at 2026-10-11T01:59:50Z (`074d8cad`) and renewed at `7f5a4f8e` and `a8daa6e8`.
  - At `a8daa6e8` the handoff names PR #100, `head` and `reviewed` both `6857e9f2bb88426f387842ef2283f4582fbf6ce8`, and `merges` `bbe12a0420a8714bb1dfb8c6db888d6098dfc167`.
  - The claimant is a local-only agent, and the coordinator holds the record for it (coordinator-reported).

- **Implementation** (observed): Contracts [PR #100](https://github.com/ArcForges/Contracts/pull/100), "[CON.41] Publish the public operation metadata in the PublicApi package".
  - Head branch `task/con-41`, base `main` at `5615821917922c7c262b8f43bba1285fa9791444`. It was opened at 2026-10-11T09:33:00Z by `deku2026`.
  - It has 3 commits, 8 files, 1039 insertions and 0 deletions. The head is 3 commits ahead of the base and 0 behind.

  | Unit | Commit | What it delivered (PR body, claimant-reported; file lists observed) |
  |---|---|---|
  | U1 | `75eaf8e64f98329b8860db6a14b9a73f8634a0d6` | `eng/packaging_tools.py` stages and verifies the PublicApi operation rows, with `tests/tooling/test_operation_metadata_package.py` (new) and the `eng/provenance/files.json` rows. Rows are copied verbatim from the gated exports, in ordinal operationId order, as LF UTF-8 bytes. `source.json` binds the SHA-256 of every contributing export (`operationSources`). `verify_artifacts` refuses a missing, extra or changed row; a missing, extra or changed digest; the file or `operationSources` in any other package; and any non-deterministic byte layout. |
  | U2 | `04e7d5555a18d4bd9341c65b9452254e5ca5be29` | `tests/StructureTests/Con41OperationMetadataCases.cs` (new), the C# `--con-41-staged` case over the staged rows, registered by one line in `tests/StructureTests/Program.cs`. `pack()` runs it on the staged file before `dotnet pack` (observed at `eng/packaging_tools.py:362` on main). |
  | U3 | `6857e9f2bb88426f387842ef2283f4582fbf6ce8` | Documentation: `src/public/dotnet/ArcForges.Contracts.PublicApi/README.md`, `docs/releasing.md` and `docs/consuming.md`. |

  - **Files** (observed): `docs/consuming.md`, `docs/releasing.md`, `eng/packaging_tools.py`, `eng/provenance/files.json`, `src/public/dotnet/ArcForges.Contracts.PublicApi/README.md`, `tests/StructureTests/Con41OperationMetadataCases.cs`, `tests/StructureTests/Program.cs` and `tests/tooling/test_operation_metadata_package.py`.
    - All eight are inside the CON.41 writes. There is no ADP-07 supporting file.
    - No `eng/operations/**` file, no `eng/build_identity.py`, no `eng/policy/**` file and no PublicApi project file changed. So no row or authorization value was authored or changed here.
    - No successor receipt was needed: `dependency_admission` and `check_provenance` pass without one (claimant-reported; both passed in the hosted runs below).
  - **DCO** (observed): each of the three commits carries `Signed-off-by: sammiller <dekueon@gmail.com>`, which matches its author and committer.

- **Independent review.** The PR has one comment and no GitHub review events (observed). Comment [6107622484](https://github.com/ArcForges/Contracts/pull/100#issuecomment-6107622484) was posted at 2026-10-11T09:33:03Z by `deku2026`. It reads "Reviewed 6857e9f2bb88426f387842ef2283f4582fbf6ce8 for [CON.41] epoch 1: approved".
  - The reviewer is `w-deku-20261011-rev-con-41` (Opus 5.5), an independent session that authored nothing.
  - The reviewer reports:
    - the outcome is fully implemented, with no stubs, and hostile and failure cases are tested both ways with positive controls;
    - all 8 paths are inside the bound writes, with no rule, identity, channel or gate change;
    - the commits are DCO-signed;
    - the gates were re-run under CI conditions on Windows and in WSL2, and gitleaks found 0.
  - Non-blocking items: the seven Events and ExecutionService rows (see Scope); the pre-existing actionlint `queue` key; and the publication receipt, which is recorded below.
  - The PR author, the comment and the claimant all run under the one GitHub account `deku2026`, so independence rests on the separate session only.

- **Validation actually performed.**
  - **Hosted, observed in the main-push Build candidate job** (114441630409, at `bbe12a04`). The PR Build candidate job (114438549118, at `6857e9f2`, version `1.0.0-ci.367.1`) shows the same CON.41 lines, the same row counts and the same SHA-256:
    - `python -m unittest discover -s tests/tooling -v`: "Ran 468 tests", then "OK". This includes 40 `test_operation_metadata_package` cases, each "... ok". They cover a removed row (missing), an added cf-internal row (extra), an altered risk and reordered keys (changed), a duplicated row, reordered rows and a CRLF layout (non-deterministic), a deleted operations file, an extra file under `operations/`, the file or `operationSources` injected into another package, a changed, missing or extra source digest, a wrong document shape, the `source.json` binding in PublicApi only, deterministic staging under reversed filesystem order, and a positive control.
    - `eng/contracts.py generate` in check mode: "Generated bindings match the authored proto." (the check-mode message at `eng/contracts.py:286`).
    - `check_provenance --owner Contracts`: passed (1000 files, 139 records). `dependency_admission`: "result": "passed" (42 dependencies, 214 inputs).
    - `check_licences`, `check_contract_access.mjs` (WP03.00, commit `bbe12a04`, not dirty), `check_foundation --generated --self-test` (14 negative cases), `check_compatibility --window` and `check_serialization`: passed.
    - `check_operation_scope.py`: "Operation scope passed: 335 registered, 0 pending, 7 reserved".
    - Release build: "0 Warning(s)", "0 Error(s)". ArchitectureTests (local fixtures): passed.
    - StructureTests (the full case set): passed.
    - The linux-x64 Native AOT serialization probe: "31 binary, 51 JSON, 1174 messages, 244 bound methods".
    - StructureTests `--con-41-staged` on the staged `ArcForges.Contracts.PublicApi/operations/operation-metadata.v1.json`: "200 rows, one per generated PublicApi method across 28 services (migration example arcforges.hello.v1.HelloService/SayHello has no row), each deep-equal to its eng/operations source row".
    - Pack, then `verify_artifacts`: "Verified candidate contents: 1.0.0-ci.368.1" and "Verified candidate handoff identity/integrity".
  - **Hosted, observed in the Security Secret scan jobs** (PR 114438549218 and main 114441630161):
    - gitleaks v8.30.1 (image digest `sha256:c00b6bd0…abbb7f`) over the full history: "333 commits scanned", "no leaks found", in both jobs.
    - `ArcForges.Contracts.ArchitectureTests.dll --hosted`: "Contracts architecture policy passed for the exact hosted source revision", in both jobs.
  - **Local, claimant- and reviewer-reported** (not re-run by this author), under CI conditions on Windows:
    - the full candidate pack, with every gate listed above;
    - the tooling suite, 468 tests;
    - `contract_access.test.mjs` 24/24, with format:check and npm audit clean.
  - **WSL2 Debian leg, claimant- and reviewer-reported** (a fresh full-history clone): locked restore, build, naming and hosted ArchitectureTests passed, and gitleaks with the pinned image found 0 across 333 commits.
  - **Local toolchain gaps** (S47(7)): Node 24.20 with npm 12, and Python 3.13 in WSL. Hosted CI, with Node 24.21.0 and Python 3.14.7, is authoritative.

- **Obligations satisfied:** WP-03.05, CON.41 part: the packaged export of every public method's eight authorization fields, scope, idempotency class and exact source rule. The public operation-metadata.v1 rows of eng/operations ship inside the published ArcForges.Contracts.PublicApi NuGet candidate `1.0.0-ci.368.1`. The rows themselves stay with the domain tasks that authored them, and the operation-scope gate stays with CON.18.
- **Substitutes still in use and their removing tasks:** none.

## Merge and publication

- **PR CI at the reviewed head `6857e9f2bb88426f387842ef2283f4582fbf6ce8`** (observed; pull_request event):
  - CI run [38128769932](https://github.com/ArcForges/Contracts/actions/runs/38128769932) (#367): success.
    - Build candidate (job 114438549118): success.
    - **Verify** (required; job 114440064485): success.
    - Publish NuGet and Publish npm: skipped, as expected on a pull request.
  - Security run [38128769992](https://github.com/ArcForges/Contracts/actions/runs/38128769992) (#372): success.
    - **Secret scan** (required): success (job 114438549218, 09:33:06Z to 09:49:18Z).
    - **Dependency review** (required): success (job 114438549404).
    - **CodeQL (csharp)** (required): success (job 114438549374).
    - **CodeQL (javascript-typescript)** (required): success (job 114438549417).
    - **CodeQL (python)** (required): success (job 114438549496).
  - **CodeQL** (required; the code-scanning results check from `github-advanced-security`, check run 114438704644): conclusion `neutral`. `gh pr checks` shows this as "skipping". GitHub counts a neutral conclusion as passing a required check.
  - The required contexts on Contracts main are Verify, Dependency review, Secret scan, CodeQL (csharp), CodeQL (javascript-typescript), CodeQL (python) and CodeQL, with strict true (observed with `gh api .../branches/main/protection/required_status_checks`).

- **Merge** (observed): merge commit `bbe12a0420a8714bb1dfb8c6db888d6098dfc167`, at 2026-10-11T09:50:42Z by `deku2026`.
  - Its parents are `5615821917922c7c262b8f43bba1285fa9791444` (main before the merge) and `6857e9f2bb88426f387842ef2283f4582fbf6ce8` (the reviewed head).
  - Its tree, `22cb51de6c4c725e38030c85307fc43015b4f8b7`, is identical to the reviewed head's tree.
  - The message is "[CON.41] Publish the public operation metadata in the PublicApi package (#100)". It is a merge commit, not a squash; the PR body asks for one because the tests read history.
  - The command was `gh pr merge 100 --merge --match-head-commit 6857e9f2` (coordinator-reported). It ran under `integration:Contracts` epoch 2, held by `w-deku-20261010-coord`. That role holding is observed on Plan branch `roles/integration-contracts` at `4374d7ba`, "Reclaim integration:Contracts epoch 2 by w-deku-20261010-coord", 2026-10-11T09:50:33Z.

- **Main-push runs at `bbe12a0420a8714bb1dfb8c6db888d6098dfc167`** (observed; push event):
  - CI run [38129817154](https://github.com/ArcForges/Contracts/actions/runs/38129817154) (#368): success, 09:50:44Z to 09:58:13Z.
    - Build candidate (job 114441630409): success. Its `PACKAGE_VERSION` is `1.0.0-ci.368.1`.
    - Verify (job 114442748834): success.
    - Publish NuGet (job 114442768610): success.
    - Publish npm (job 114442768888): success.
  - Security run [38129817147](https://github.com/ArcForges/Contracts/actions/runs/38129817147) (#373): final conclusion success (completed at 10:00:14Z).
    - Secret scan (job 114441630161): success, finishing at 10:00:13Z.
    - CodeQL (csharp), CodeQL (javascript-typescript) and CodeQL (python): success.
    - Dependency review: skipped, because it runs on pull requests only.

- **Publication receipt for the CON.41 evidence** (observed in the main-push Build candidate log, job 114441630409, at 09:54:21Z):
  - The log line is "Operation metadata staged in ArcForges.Contracts.PublicApi at operations/operation-metadata.v1.json: 200 rows across 28 services".
  - **SHA-256** of `operations/operation-metadata.v1.json`: `4123d235afa7048940ef050b8e2612a8a12e1d71245f60cdaa44cfd910910e1d`. The claimant reports the same digest on Windows and Linux, and the PR candidate at `6857e9f2` shows the same digest.
  - **Row count by service** (the sum is 200):

    | Service | Rows | Service | Rows |
    |---|---|---|---|
    | arcforges.catalog.v1.CatalogService | 7 | arcforges.publicapi.v1.IdentityService | 32 |
    | arcforges.publicapi.v1.AgentService | 7 | arcforges.publicapi.v1.NotificationService | 4 |
    | arcforges.publicapi.v1.ApplicationService | 3 | arcforges.publicapi.v1.PolicyService | 1 |
    | arcforges.publicapi.v1.ApprovalService | 2 | arcforges.publicapi.v1.PreferenceService | 1 |
    | arcforges.publicapi.v1.AutomationService | 9 | arcforges.publicapi.v1.ResourceService | 7 |
    | arcforges.publicapi.v1.BridgeService | 3 | arcforges.publicapi.v1.ScopeService | 3 |
    | arcforges.publicapi.v1.ChatService | 21 | arcforges.publicapi.v1.SearchService | 1 |
    | arcforges.publicapi.v1.CommerceService | 14 | arcforges.publicapi.v1.SourceService | 5 |
    | arcforges.publicapi.v1.ConnectorService | 6 | arcforges.publicapi.v1.SupportService | 4 |
    | arcforges.publicapi.v1.DataService | 2 | arcforges.publicapi.v1.SyncService | 10 |
    | arcforges.publicapi.v1.DeviceService | 10 | arcforges.publicapi.v1.TaskService | 9 |
    | arcforges.publicapi.v1.EntitlementService | 6 | arcforges.publicapi.v1.TransferService | 6 |
    | arcforges.publicapi.v1.ExportService | 3 | arcforges.publicapi.v1.WorkspaceService | 7 |
    | arcforges.publicapi.v1.HistoryService | 4 | arcforges.simulation.v1.SimulationService | 13 |

    IdentityService (32), DeviceService (10) and WorkspaceService (7) total 49, which matches the CON.07 export.
  - `operationSources` covers 10 of the 16 export files under `eng/operations/` at `bbe12a04` (the count is claimant-reported; the 16 files are observed in the merge tree).
  - **Candidate version:** `1.0.0-ci.368.1`. The log shows "Successfully created package '.../ArcForges.Contracts.PublicApi.1.0.0-ci.368.1.nupkg'", then "Verified candidate contents: 1.0.0-ci.368.1".
  - **NuGet** (Publish NuGet log): 12 pushes to `https://www.nuget.org/api/v2/package` at `1.0.0-ci.368.1`, each followed by "Your package was pushed.":
    - `ArcForges.Contracts.Foundation`, `ArcForges.Contracts.PublicApi` (pushed at 09:57:57Z) and `ArcForges.Contracts.Events`;
    - `ArcForges.Sdk.Contracts`, `ArcForges.Contracts.Validation`, `ArcForges.Sdk.Client` and `ArcForges.Cli`;
    - `ArcForges.Contracts.LocalRpc.Platform`, `.LocalRpc.Sandbox`, `.LocalRpc.Chat` and `.LocalRpc.Scope`;
    - `ArcForges.Contracts.CloudInternal`.
  - **npm** (Publish npm log): one `npm publish .../arcforges-ai-internal-1.0.0-ci.368.1.tgz --access public --tag latest --ignore-scripts`, then "+ @arcforges/ai-internal@1.0.0-ci.368.1". A provenance statement was published to the sigstore transparency log (logIndex 3205048532).
  - No package or registry index was downloaded or queried. That the packages are resolvable rests on the push and publish log lines.

## Scope (2026-10-11)

- **Delivered outcome.** From the merge on, every published ArcForges.Contracts.PublicApi candidate carries `operations/operation-metadata.v1.json`, the first being `1.0.0-ci.368.1`.
  - The file holds exactly the public rows that bind a method of the package's descriptor closure: 200 rows, one per generated PublicApi method except the Hello migration example.
  - Each row is deep-equal to its merged source row, in a stable order.
  - `source.json` binds the contributing export digests, and `verify_artifacts` refuses a missing, extra or changed row.
  - No row, field, operation, authorization value, package identity, channel or candidate format changed.
- **Status decision: `complete`.** The outcome, validation and evidence are met at the merge head and in the main-push publication. The graph lists no completion edges.
- **Scope note (reviewer-reported, non-blocking; not a CON.41 gap).** Seven operation rows belong to ArcForges.Contracts.Events, outside the PublicApi descriptor closure: Poll, Watch, StartTransientTurn, ReadOutput, WatchOutput, AcknowledgeOutput and PurgeTransient of Events/ExecutionService.
  - Under the outcome text ("whose binding names a method of the package's descriptor closure") they are correctly not packaged here.
  - Their packaging is a coordinator decision for CLOUD.29, before CLOUD.21 and CLOUD.29 need them.
- **Consumers (not part of this task's acceptance).** Cloud moves its ArcForges.Contracts.PublicApi, CloudInternal and Validation pins, and its `@arcforges/ai-internal` pin, to the first Contracts main candidate that contains this task, which is `1.0.0-ci.368.1`, or a later one. It does so through its own consumer tasks (CLOUD.15, CLOUD.16 and CLOUD.21), never to `1.0.0-ci.350.1`.
  - PLT.66 and APP.03 adopt the first Contracts main candidate that contains both CON.41 and CON.43 (S59(7), S62(1)).
  - No consumer run was performed or is claimed here.
- **Recorded non-blocking items:** the pre-existing actionlint `queue` key in `ci.yml` (S50(4) item 6), which is untouched.
- **Remaining completion prerequisites and next action:** none. *Next action:* merge this ledger record, then the claimant sets `claims/con-41` to `complete`.

## Untested coverage (stated expressly)

- **Registry resolution:** nobody has resolved `ArcForges.Contracts.PublicApi 1.0.0-ci.368.1` from nuget.org or read the packaged file from the downloaded `.nupkg`. This is blocked on a consumer restore (the Cloud consumer tasks), not proven. The packaged contents rest on the hosted `verify_artifacts` and `--con-41-staged` lines over the staged candidate.
- **Local gates:** claimant- and reviewer-reported; this author re-ran none of them. Hosted CI is authoritative.
