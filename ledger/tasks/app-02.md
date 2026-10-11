---
task: APP.02
status: complete
recorded: 2026-10-11
claimant: w-deku-20261010-app-02
epoch: 1
---

# Minimal ArcScope application services: read, create and append annotations

## Evidence

- **Task definition used.** This record uses the graph record as re-scoped by planning repair fix8 and its delta (DLV-34; coordinator rulings S58 and S59(5)). Design PR [#350](https://github.com/ArcForges/ArcForges-Design/pull/350) was merged as `f4b2299c1b57fb8a9905b9463680cc7bc4686687` at 2026-10-11T01:56:16Z (head `8d961a09fa819991f064a9600f4759acd70d078e`). Plan PR [#479](https://github.com/ArcForges/Plan/pull/479) was merged as `f7cb511a4b58f8d93aec34cd06a19c2e2317e2da` at 2026-10-11T01:56:22Z (head `fa0943b6442648fb2ab716be806a51087d4b2f7e`). Both were read with `gh pr view`. `rec.py APP.02` on the Design primary at current main (`6417361`) shows:
  - **outcome:** real read, create and append annotation commands through typed application handlers and local persistence, with command input validation and one write path built for reuse by the own-app capability binding that APP.03 composes;
  - **validation:** offline unit tests of the handlers, the store and the single write path; no live Cloud in CI;
  - **evidence:** "Source commit, command receipt samples, command input validation failure cases.";
  - **completion prerequisites:** none (`complete` is empty).

  The start edges are on APP.01 (`complete`), PLT.24 (`delivered`) and PLT.38 (`complete`). The graph states the PLT.24 and PLT.38 edges as satisfied history, with no ordering effect. Under S59(5), APP.03 now owns the capability route, the generated DP-03 decoder (CON.43), the durable SQLite `IInvocationRecordStore` on the S58 restore factory, and the cross-route proof. Part B of S53(1) is therefore no longer APP.02 work. Brief 0f says APP.02 "closes on its committed part A, plus factual docs, under S59(5)".
- **Claim** (observed: Plan `claims/app-02`): epoch 1, held by `w-deku-20261010-app-02`, claimed 2026-10-10T20:02:31Z (resume run, brief 0d). The claim is coordinator-held; the claimant is a local-only agent. At this recording the record is still `claimed`, with `reviewed` and `head` `728257ca21bd0e22db371d67f0ad8acace999090` and `merges` `485c8d6d9a84825eb5bc737c9808b8bc4b616643`. Its `next` entry ("part B after PLT.46 and PLT.58 publish") predates S59(5) and is stale. The coordinator updates and releases the record (brief 0f).

- **Implementation, in one ArcScope pull request** (observed). ArcScope [PR #27](https://github.com/ArcForges/ArcScope/pull/27), "[APP.02] Minimal ArcScope annotation services (part A, S59(5))":
  - head branch `task/app-02`, opened 2026-10-11T15:09:04Z by `deku2026` (the coordinator, publishing for the local-only agent);
  - 4 commits, 50 files, 5346 insertions and 56 deletions;
  - merged at head `728257ca21bd0e22db371d67f0ad8acace999090` as `485c8d6d9a84825eb5bc737c9808b8bc4b616643` at 2026-10-11T15:39:15Z by `deku2026`. The merge commit has two parents: `b0ff5cd18fb0ffbbaf1bd8b7b67cb7304e6ba794` (the previous main, the GOV.07 follow-up #24) and `728257ca`. This is consistent with the `--merge` method (verified from the commit).
  - The `integration:ArcScope` role is held by `w-deku-20261010-coord` at epoch 1 (observed: Plan `roles/integration-arcscope` at `73f28a7b`, claimed 2026-10-11T15:09:19Z, before the merge). The `--match-head-commit 728257ca` flag is coordinator-reported; it is not visible in the PR text.
- **Units, with full commit SHAs** (observed: the PR commit list and the merged diff `b0ff5cd1..485c8d6d`):
  - `807e9e5318c1e1e35834fe44124d7f7549186dff` admits the part-A closure (S53(2) as amended by S56(2)). It adds one `MSBuildProjectName` selector `ItemGroup` in `Directory.Packages.props` for `ArcForges.ArcScope.Application`, `.Infrastructure` and `.Tests` only:
    - DesktopPlatform `ArcForges.Foundation` and `Application.Abstractions` (Update rows), and `Assistant.Abstractions`, `Capabilities` and `Persistence.Sqlite` (Include rows), all at `1.0.0-ci.130.1`;
    - `ArcForges.Sdk.Contracts` (Include), and `Contracts.Foundation` and `Contracts.PublicApi` (Update rows), at `1.0.0-ci.216.1`. The DesktopPlatform 130.1 packages pin Contracts exactly at 216.1, which caused NU1107 (S56(1));
    - `Microsoft.Data.Sqlite` and `.Core` `10.0.12`, and `SQLitePCLRaw.bundle_e_sqlite3`, `core`, `lib.e_sqlite3` and `provider.e_sqlite3` `2.1.12`.

    The executable and Core closures are unchanged. No Security package, no `LocalRpc.Scope` and no rolled-back version is admitted. The publishing commits are ancestors of their mains (observed by this author with `gh api …/compare/<sha>...main`): DesktopPlatform `c8280488a1203a5c0160bebeba48ae5f20443f9c` (main 16 ahead, 0 behind) and Contracts `eb650e5b9ef7fc06f0eb79ee167b896e4252b50e` (main 181 ahead, 0 behind). `eng/ArcForges.Repository/DependencyPolicy.cs` reads `Include ?? Update`, so a selector Update row is validated like an Include row (authorised rule change, S56(3)). It is tested both ways by `DependencyPolicyTests.AdmittedSelectorUpdateRowsPass`, `RefusesFloatingOrUnadmittedSelectorUpdateRows` (theory) and `SelectorUpdateRowCannotPinAnotherAdmittedVersionOfADifferentPackage`.
  - `3f021d9106c183e5500644a5f55c3fbaf29caa56` adds the Application layer (`src/ArcForges.ArcScope.Application/Annotations`):
    - the annotation model (`Annotation`, `AnnotationEntry`, `AnnotationText`, `AnnotationActor`, `SampleRange` and the identities);
    - the typed commands `CreateAnnotation` and `AppendAnnotation`, and the queries `ReadAnnotation` and `ReadSessionAnnotations`;
    - `AnnotationQueries` and the untyped UI entry point `AnnotationCommandSurface`;
    - the canonical SHA-256 command fingerprint;
    - the closed failure set `AnnotationFailures`;
    - the port `IAnnotationStore`, whose one mutation is `CommitAsync(AnnotationCommit)`;
    - the single `AnnotationWritePath`, which serializes writes per annotation with `KeyedAsyncLock`.
  - `e94d3137c106285d36bde2821b6df551a3a9a53a` adds the Infrastructure layer (`src/ArcForges.ArcScope.Infrastructure`):
    - `ArcScopeStore` and `ProductDatabase` over the shared PLT.01 and PLT.04 SQLite mechanism;
    - `ArcScopeMigrations` with ordinal migration ordering, and `Storage/Migrations/0001_annotation_store.sql` (the grow-only `arcscope_session_target` table, the derived `arcscope_annotation_index` and its catch-up marker);
    - `SqliteAnnotationStore` with the canonical `AnnotationPayload` codec and content origin;
    - `SqliteSessionTargetCatalog`, the S53(4) `ISessionTargetCatalog` with a `Register` entry point for the future session owner. No command creates sessions, and an unregistered session refuses with `resource.unavailable`.
  - `728257ca21bd0e22db371d67f0ad8acace999090` adds the factual docs and records:
    - factual lines in `docs/development.md`, `docs/dependency-policy.md` and `docs/provenance.md`;
    - the `eng/policy/dependency-review.json` reseal, with `reviewer` `w-deku-20261010-rev-app-02` and `reviewedOn` 2026-10-11;
    - the provenance successor `eng/provenance/records/app-02-provenance-tools-r1.json`, which `supersedes` `gov-07-provenance-tools-r2`. Its `review` block names reviewer `w-deku-20261010-rev-app-02`, decision `approved`, reviewedOn 2026-10-11 and baselineCommit `b0ff5cd1` (S25, S56(4); read by this author at `485c8d6d`).
- **ADP-07 supporting files** (outside the writes `src/ArcForges.ArcScope.Application/**`, `src/ArcForges.ArcScope.Infrastructure/**` and `tests/**`; under S53(5), S56(3) and S56(4)): `ArcScope.slnx` (two Project lines), `Directory.Packages.props`, `eng/policy/dependency-policy.json`, `dependency-review.json` and `licence-boundary.json`, `eng/ArcForges.Repository/DependencyPolicy.cs` and `LicencePolicy.cs`, `eng/provenance/files.json`, `NOTICE.txt` and the new provenance record, and the three docs. These are the merged diff's paths outside the writes, checked with `git diff --stat b0ff5cd1 485c8d6d`. `tests/ArchitectureTests/HostedPolicyGate.cs` is inside `tests/**`. The reviewer reports that every path outside the writes is an S53(5), S56(3) or S56(4) binding, and that no record already on main is rewritten.

- **Review trail (ArcScope PR #27)** (observed). The PR has one comment and no GitHub review events. Comment [6110420133](https://github.com/ArcForges/ArcScope/pull/27#issuecomment-6110420133) was posted 2026-10-11T15:09:07Z by `deku2026` for the independent session `w-deku-20261010-rev-app-02` (Opus 5.5, which authored nothing). It reads: "Reviewed 728257ca21bd0e22db371d67f0ad8acace999090 for [APP.02] epoch 1 (the whole change, `git diff origin/main..HEAD`, 4 commits): approved."
  - Reviewer-reported:
    - the handlers, input validation, the single write path and the SQLite store are real, with no stubs, and the capability route is absent (S59(5));
    - the nuspec SHA-256 and licence of all 14 new coordinates match a fresh cache, and every lock `contentHash` validated;
    - the gates were re-run in a fresh clone under CI conditions;
    - in WSL2 the offline tests were 214/216, and "the 2 failures come only from the scratch SDK pin".
  - Non-blocking items (each is carried below): the ledger must quote the receipt samples and the failure catalogue (done in this record); the verify-assemblies list; the `SqliteAnnotationStore.Commit` mapping; the orphan index row; the AOT linking probe; the `docs/development.md` tests bullet.
  - The approval was posted before the PR CI run completed. The run was created at 15:09:08Z and succeeded at 15:13:00Z, and the merge followed at 15:39:15Z. The PR author, the review comment and the merge are under the one GitHub account `deku2026`, so independence rests on the separate session only.

- **Hosted runs** (observed: `gh run view --json` for job conclusions, and `gh run view --log` greps for specific lines; no artifact was downloaded):
  - **PR run [38150079626](https://github.com/ArcForges/ArcScope/actions/runs/38150079626)** (CI, pull_request, head `728257ca`, 15:09:08Z to 15:13:00Z): success.
    - Succeeded: Dependency review, Native (win-x64), Native (win-arm64), Native (linux-x64), CodeQL (actions), CodeQL (csharp), Workflow and secret checks, and Verify. The PR check rollup also lists a `CodeQL` analysis check as SUCCESS.
    - Skipped, because they run only on main pushes: Publish portable release and Verify publication.
    - Native (linux-x64) logged:
      - `dotnet format ArcScope.slnx --verify-no-changes --no-restore` (the step passed);
      - `Verified actual PE build identity of all four owned assemblies.`;
      - `Provenance passed: 156 files, 29 reused files, 20 immutable records.`, `Dependency admission, immutable inputs and upgrade review passed.` and `Verified 8 project licence declarations and references.`;
      - for `dotnet test --project tests/ArcForges.ArcScope.Tests/... --filter-not-class '*.TransportTests'`: `Test run summary: Passed!`, `total: 216`, `failed: 0`, `succeeded: 216`, `skipped: 0`.
    - Workflow and secret checks logged actionlint 1.7.12 (sha256-checked download), gitleaks at the pinned digest `zricethezav/gitleaks@sha256:c00b6bd0…` with `63 commits scanned.` and `no leaks found`, and `ArcScope architecture policy passed for the exact hosted source revision.`
  - **Main-push run [38152177188](https://github.com/ArcForges/ArcScope/actions/runs/38152177188)** (CI, push, head `485c8d6d`, 15:39:17Z to 15:43:00Z): success.
    - Every job succeeded, including Publish portable release and Verify publication, except Dependency review, which is skipped on a push. The Verify step requires `skipped` for a push.
    - The same lines as the PR run were logged: 216/216 offline tests, 63 commits scanned with no leaks, and the architecture policy passed.
    - The three native jobs logged the immutable version `0.1.0-ci.68.1`.
  - **Publication** (observed: `gh release view` metadata only; nothing downloaded). Pre-release `ArcScope 0.1.0-ci.68.1`, tag `v0.1.0-ci.68.1`:
    - target `485c8d6d9a84825eb5bc737c9808b8bc4b616643`; `git ls-remote` shows the tag at the same commit;
    - published 2026-10-11T15:42:52Z, `isPrerelease` true, `isDraft` false;
    - assets: `arcscope-0.1.0-ci.68.1-linux-x64.tar.gz` (34385353 bytes), `-win-arm64.zip` (63942303), `-win-x64.zip` (71281847), their three `.sha256` files, and `arcscope-verification.tar.gz` (560).

    The executable does not reference the new projects yet, so the release does not exercise the annotation services at runtime.

- **Evidence clause: source commit.** The merge is `485c8d6d9a84825eb5bc737c9808b8bc4b616643`, at reviewed head `728257ca21bd0e22db371d67f0ad8acace999090`.
- **Evidence clause: command receipt samples** (observed in merged source and tests at `485c8d6d`, read by this author; executed in the hosted 216-test runs above).
  - **The receipt types:**
    - `AnnotationWriteReceipt(CommandId Command, AnnotationId Annotation, NativeRevision Revision, bool Replayed)` in `AnnotationCommands.cs`, returned by the write path;
    - `AnnotationCommitReceipt(CommandId Command, AnnotationId Annotation, NativeRevision Revision, bool Replayed)` in `IAnnotationStore.cs`, the store's durable command record.
  - **Fresh:**
    - `AnnotationWritePathTests.ACreateCommitsOnceThroughTheStoreAndRecordsTheEntry` asserts `new AnnotationWriteReceipt(command.Command, command.Annotation, new NativeRevision(1), Replayed: false)`, with exactly one store commit (operation `arcscope.annotation.create`, expected revision 0, fingerprint equal to the command's).
    - `SqliteAnnotationStoreTests.CreateAppendAndReadRoundTripThroughTheRealStoreAndSurviveARestart`, on the real SQLite store, asserts `new AnnotationWriteReceipt(append.Command, create.Annotation, new NativeRevision(2), Replayed: false)` for an append. After a reopen it reads byte-identical canonical payloads, and the journal holds the create and append command ids with their operations.
    - `AnAppendAddsOneEntryAndNeverRewritesHistory` asserts revision 2 with `Replayed` false, and that the earlier entry is the same object.
  - **Replayed:**
    - `RetryingTheSameCreateReplaysTheReceiptWithoutASecondEffect`: `Replayed` true, revision 1, still one commit, and the original `CommittedAt` is kept after the clock advanced 5 minutes.
    - `RetryingAnAppendReplaysItEvenAfterLaterAppends`: `Replayed` true at revision 2 after revision 3 exists, and the commit count stays 3.
    - `SqliteAnnotationStoreTests.RetryingACommittedCommandAfterARestartReplaysWithoutASecondEffect`: after a reopen, the write-path receipt and a direct store `AnnotationCommitReceipt` both have `Replayed` true and revision 1, and the journal count is unchanged.
    - `StoreFailuresMapToClosedCodesWithTheProvableEffect`: a store receipt `(command, annotation, revision 2, Replayed: true)`, as when another process committed the same command first, is accepted as a replay.
    - `AnnotationQueryAndSurfaceTests.TheUserInterfaceSurfaceRunsTheSameWritePathAsATypedCommand`: the UI surface create and the equivalent typed `CreateAnnotation` have equal fingerprints. The typed retry and a surface retry both replay, with one commit.
  - **Refused:** the refusals are the cases in the catalogue below. Each test asserts the exact code and, where it applies, that no commit happened (`Store.Commits` empty or `CommitCalls` unchanged).
- **Evidence clause: command input validation failure cases** (observed in merged source and tests at `485c8d6d`). `AnnotationFailures` holds the closed set. `AnnotationFailures.All` has nine codes, and `CommitRefusals` holds the four that a store commit may report, each before any effect: `conflict.revision_mismatch`, `conflict.duplicate_identifier`, `command.reused_identifier` and `resource.integrity_failed`.

  | Code | Cases, with the tests that assert them |
  |---|---|
  | `validation.invalid_request` | `TheUserInterfaceSurfaceRefusesInvalidInputBeforeAnyStoreAccess`, with 0 store reads, 0 session reads and no commits: 13 write refusals (empty command, annotation or session id; count 0; a range starting at `ulong.MaxValue`; blank, null, NUL-containing and lone-surrogate text; append expected revision 0; empty ids on append; text over `AnnotationText.MaxUtf8Bytes`) and 5 read refusals (empty id, empty session, limit 0, limit over 200, a cursor with count 0). `ACreateOnAnUnregisteredSessionOrOutsideTheRecordedSamplesRefuses`: ranges 990+11 and 1000+1 on a 1000-sample session. `AnAppendBeyondTheHistoryOrTextBoundRefuses`: the `Annotation.MaxEntries` bound and the total text-byte bound. The type-level guards are in `AnnotationModelTests.TextIsPreservedExactlyAndRefusesBlankNulMalformedOrOversizedInput`, `SampleRangesAreHalfOpenNonEmptyAndOverflowChecked` and `CommandsAndQueriesRefuseUninitializedOrOutOfBoundsFields` |
  | `conflict.revision_mismatch` | `AStaleOrMissingAppendRefusesBeforeAnyCommit` (expected 1 or 3 when the annotation is at 2). `ConcurrentAppendsWithTheSameExpectedRevisionCommitExactlyOnce` (16 writers: 1 success and 15 mismatches; at most one commit in flight per annotation). On the real store: `ConcurrentCommandsCommitIndependentlyAndOneStaleRaceWinsExactlyOnce` (8 racing appends) and `TheStoreItselfRefusesDuplicatesStaleRevisionsReuseAndMissingAnnotations` (a stale append; at the store level, an append to a missing annotation). Through the UI surface: the "stale view" append in `TheUserInterfaceSurfaceRunsTheSameWritePathAsATypedCommand` |
  | `conflict.duplicate_identifier` | `ASecondCreateOfAnExistingAnnotationRefusesAsADuplicate`; on the real store, `TheStoreItselfRefuses…` |
  | `command.reused_identifier` | `ReusingACommandIdentityForDifferentContentRefuses`: the same annotation, and another annotation that reaches the store's durable command log. `RetryingAnAppendReplaysItEvenAfterLaterAppends`: reworded content, and a create command id reused as an append. On the real store: `TheStoreItselfRefuses…`, and `ACommandIdentityRecordedForAnotherAnnotationRefusesAndLeavesNoVisibleAnnotation` (the orphan index row is invisible and is removed on reopen, `OrphanIndexRowsRemoved` 1) |
  | `state.not_found` | `AStaleOrMissingAppendRefusesBeforeAnyCommit` (an append to an unknown annotation) and `ReadingReturnsTheFullHistoryOrRefusesAMissingAnnotation` |
  | `state.invalid_transition` (in the code set, though not named in the coordinator's facts) | A session target never shrinks: `ACreateOnAnUnregisteredSessionOrOutsideTheRecordedSamplesRefuses` (register 1999 after 2000) and `ArcScopeStoreTests.SessionTargetsRegisterGrowAndSurviveARestartButNeverShrink`. The table triggers also refuse a shrink, an identity change or a delete from any writer (`TheSessionTableRefusesAShrinkAnIdentityChangeOrADeleteFromAnyWriter`) |
  | `resource.unavailable` | An unregistered session (S53(4)): `ACreateOnAnUnregisteredSessionOrOutsideTheRecordedSamplesRefuses` (no commit), `ASessionReadRefusesAnUnregisteredSessionAndAnUntrustworthyPage` (no store read) and the "no session" surface create in `TheUserInterfaceSurfaceRunsTheSameWritePathAsATypedCommand` |
  | `resource.integrity_failed` | Untrustworthy stored state fails closed. `UntrustworthyStoredStateRefusesWithoutACommit` (`InvalidDataException` on read). `AStoreAnswerForAnotherAnnotationIsAnIntegrityFailure`. `ASessionReadRefuses…UntrustworthyPage` (out-of-order, duplicate, foreign-session, over-limit and before-cursor pages). On the real store: `ATamperedOrNonCanonicalPayloadFailsClosedForReadsAndWrites` (altered text, insignificant whitespace), `AnUnsupportedOriginProfileLeavesTheAnnotationReadableButReadOnly`, `AnIndexRowThatDisagreesFailsClosedAndTheNextOpenRepairsItFromTheCanonicalAnnotation`, and `ASnapshotRestoreRecreatesTheIndexRowsOfTheReplayedCommitsAndOpensReadFirst` (commits refuse until the recovery evidence is resolved) |
  | `internal.unexpected`, with its effect certainty | **`Unknown` after dispatch.** `StoreFailuresMapToClosedCodesWithTheProvableEffect` covers an `IOException` during commit, an unregistered store code (`perm.capability_denied`), and receipts with the wrong revision (7), annotation or command. `ACreateInterruptedBeforeItsCommitIsInvisibleRetryableAndCleanedUpOnReopen` and `AFailedCanonicalCommitRollsBackEveryPartAndTheSameCommandCanBeRetried` also give `Unknown`; in the second, every part rolls back and the retry commits revision 1. **`DidNotHappen` before dispatch.** `UntrustworthyStoredStateRefusesWithoutACommit` (an `IOException` on read; a session catalog fault), `ReadingReturnsTheFullHistoryOrRefusesAMissingAnnotation` (an `IOException` on read) and `ArcScopeStoreTests.ACorruptSessionRowFailsClosed` |

  Cancellation is reported as `Cancelled`, not as a failure code: `CancellationBeforeDispatchProvesNoEffect` and `CancellingAWaiterLeavesNoLockAndNoEffect` give `DidNotHappen`, with no store read or commit and no leaked lock. `CancellationDuringTheCommitLeavesTheEffectUnknown` gives `Unknown`. The write path never retries.
- **One write path** (observed in merged tests):
  - `AnnotationWritePathTests.OnlyTheWritePathCanBuildACommitAndTheStoreHasOneMutation` asserts four things: `AnnotationCommit` has no public constructor and only an assembly-internal one; `IAnnotationStore` has exactly `CommitAsync`, `ReadAsync` and `ReadSessionAsync`; exactly one method takes an `AnnotationCommit`; and no other public method in the Application assembly returns one.
  - `TheUserInterfaceSurfaceRunsTheSameWritePathAsATypedCommand` and `SqliteAnnotationStoreTests.TheUserInterfaceSurfaceWritesThroughTheSameStore` show that the UI surface and the typed commands share the write path and the store.
  - The capability route is not part of this task. The proof that the capability route and the UI share this one write path is APP.03's (S59(5)).
- **New test methods at the merge** (counted by this author as `[Fact]`/`[Theory]` attributes; a theory can expand to several cases): `AnnotationModelTests` 12, `AnnotationQueryAndSurfaceTests` 5, `AnnotationWritePathTests` 19 and `SqliteAnnotationStoreTests` 18 (under `Annotations/`), and `ArcScopeStoreTests` 11 and `ArcScopeMigrationsTests` 7 (under `Storage/`). That is 72 methods, plus 3 new `DependencyPolicyTests` methods. The write-path and query tests run against the test-only `InMemoryAnnotationStore` and session-catalog doubles in `AnnotationTestDoubles.cs`. The `SqliteAnnotationStoreTests` and `Storage/` suites run against the real `ArcScopeStore` through `SqliteStoreFixture` on a temporary file. The migration-ordering tests (`ThePlanDoesNotDependOnTheOrderTheFilesAreGiven` and `FileSystemEnumerationOrderDoesNotChangeThePlan`) guard the ordinal ordering. Its absence caused the CLOUD.84 Linux failure.

- **Validation actually performed:**
  - **Hosted, observed:** the PR and main-push runs above. Offline unit tests 216/216 ran in Native (linux-x64), the only job that runs the test step (filter `--filter-not-class '*.TransportTests'`). Format, verify-assemblies, check (provenance, dependency admission, licence), the hosted architecture policy, actionlint, gitleaks (63 commits), CodeQL for csharp and actions, Dependency review on the PR, and the win-x64, win-arm64 and linux-x64 Native AOT compile, stage and pack all ran. The win-x64 and win-arm64 jobs do not run the unit tests.
  - **Local, claimant- and reviewer-reported** (fresh clone of `728257ca` under CI conditions, SDK 10.0.401, fresh package folder; this author re-ran none of them): locked restore; format; Release build with 0 warnings; verify-assemblies, check and version; offline tests 216/216; naming evidence 0 findings; hosted architecture tests; win-x64 Native AOT publish, prepare and pack; win-arm64 cross-compile, prepare and pack; the CodeQL build; actionlint 1.7.12 exit 0; gitleaks at the pinned digest (S31 method), 60 commits with no leaks. The hosted scan counted 63 commits on the PR merge ref and on main.
  - **WSL2 Debian, claimant- and reviewer-reported:** only SDK 10.0.400 is installed, so the locked restore fails NU1004 on the SDK band (recorded). The PR body says the annotation, SQLite store and ext4 migration-ordering tests all pass there. The reviewer reports 214/216, with the 2 failures attributed only to the scratch SDK pin. Hosted linux-x64 under 10.0.401 is authoritative (216/216).
- **Obligations satisfied:** WP-14.01 for the part the graph now assigns to APP.02: real read, create and append annotation commands through typed handlers and local persistence, with command input validation and the one write path built for reuse by the own-app capability binding. The own-app capability half of WP-14.01 is APP.03's (S59(5)).
- **Substitutes still in use and removing tasks:** none in any composition. The in-memory store and session-catalog doubles are test-only. They are registered nowhere, and the real SQLite store is tested separately. No ArcScope-owned stand-in risk check exists (S53(1)).

## Scope and status (2026-10-11)

- **Status decision: `complete`.** Every condition holds:
  - **Outcome:** merged in `485c8d6d` and published in the main-push run, release `0.1.0-ci.68.1`.
  - **Validation:** hosted offline tests of the handlers, the store and the single write path, 216/216 in both runs.
  - **Evidence:** the source commit, the receipt samples and the failure cases are recorded above from merged source and tests.
  - **Completion prerequisites:** the graph's list is empty.

  The open items below are outside APP.02's re-scoped acceptance, and each names its owner.
- **Open items, honestly stated (not APP.02 acceptance):**
  - **UI flow on a real capture session:** blocked on SCOPE.06, not proven (S53(4)). No command creates sessions; the session owner will call `Register`.
  - **Exposing read and append as capabilities:** blocked on a Contracts operation, not proven (S53(3)). Only `arcscope.annotation.create` will be an own-app capability, bound by APP.03.
  - **Part B of S53(1)**, including the capability route through the real `CapabilityInvocationPipeline` and `CapabilityEnforcementGate`, the generated decoder (CON.43), the durable `IInvocationRecordStore` (S58) and the cross-route one-write-path proof: blocked on APP.03, not proven here (S59(5)).
  - **Native AOT linking of the Infrastructure SQLite closure:** blocked on APP.03, not proven in CI. It is shown only by the author's scratch probe at `e94d313`, which is claimant- and reviewer-reported and not committed. The executable does not reference the new projects, so the hosted Native AOT jobs do not compile them.
  - **verify-assemblies coverage:** it checks a fixed list ("all four owned assemblies", observed in the hosted log) that does not yet include Application and Infrastructure. This predates the change; APP.03 extends the list (reviewer, non-blocking).
- **Recorded non-blocking items (reviewer):**
  - `SqliteAnnotationStore.Commit` maps a plain `InvalidOperationException` from the shared mechanism to `command.reused_identifier` after a reload confirms that the state did not move (lines 132 to 140 at `485c8d6d`). This is correct today, but fragile once a `Committed` subscriber exists. It is carried to APP.03.
  - The orphan session-index row left by an interrupted create is documented and tested (`ACreateInterruptedBeforeItsCommitIsInvisibleRetryableAndCleanedUpOnReopen`, and `ACommandIdentityRecordedForAnotherAnnotationRefusesAndLeavesNoVisibleAnnotation`).
  - The `docs/development.md` tests bullet does not list the new `Annotations/` and `Storage/` suites. This is a doc gap for a later docs touch.
  - Part A's test project still references `ArcForges.Assistant.Abstractions` and `ArcForges.Capabilities`, which no APP.02 code uses. The dependency review records why they stay: they are admitted roots for APP.03's capability binding.
- **Remaining completion prerequisites and next action:** none for APP.02. The coordinator updates the claim to `complete` and releases it with the integration role (brief 0f). APP.03 is not started (brief 0f).

## Untested coverage (stated expressly)

- **Runtime in the product:** no executable or UI path opens the store yet. The annotation services are exercised only by the offline unit tests. No receipt was captured from a running application. The receipt samples are test assertions in merged code, executed in hosted CI.
- **Platforms:** hosted unit tests ran on linux-x64 only. Windows unit-test runs are claimant- and reviewer-reported (local, SDK 10.0.401). The WSL2 run used SDK 10.0.400 with a scratch pin. Native AOT of the new libraries is not compiled in CI (see above).
- **Concurrency across processes:** the per-annotation lock is in-process. Cross-process exclusion rests on the shared store's owner lock (`ASecondOwnerOfTheSameFileIsRefusedUntilTheFirstCloses`) and the store-level revision and command-log checks. No multi-process stress was run.
- **Hosted CI, merge and publication:** observed through `gh` only. No artifact or release asset was downloaded.
- **Independence:** the PR, the review comment and the merge are under one GitHub account (`deku2026`). Independence rests on the separate session `w-deku-20261010-rev-app-02`.
- **Coordinator-reported facts:** the `--match-head-commit` merge flag.
