---
name: thread-development-orchestrator
description: "Plan an initial dependency DAG with locally adaptable task pools, worker exclusions, bounded idle-worker heartbeat/wait, 10-minute leases and 30-minute priority recovery for Local collaboration; use frozen physical waves and verified handoffs for Web. Resolve development location first: use Mac/local when explicitly requested, Web when explicitly requested, otherwise inherit the current conversation development environment; keep all lanes on that resolved environment unless the user changes it. Inline bounded shared foundation work, require SOURCE_BASE_READY before repository fan-out, keep dependent serial stages inside one lane with Codex Loop logical isolation, use host-local Codex Loop collaboration for Local lanes, and verified Google Drive handoffs only for Web lanes. Use for parallel coding/refactor/research-development work, environment-aware workspace coordination, cross-thread artifacts, integration, and cleanup."
---

# Thread Development Orchestrator

Plan the **initial known objective**, then create the Run as part of invoking this orchestrator when source and authorization gates are ready; do not introduce a separate plan-approval or launch-confirmation step. Minimize manual relaunches, duplicated setup and idle Chats. The primitive is a **small, independently verifiable task**, not a pre-imposed Wave, an author/project-sized work unit, or a task for every tiny operation. Identify every deliverable, prerequisite, test gate, shared mutable resource, exclusive decision and final acceptance condition before registering the initial plan. For repeatable independent work, **prefer more bounded shards over a few long tasks**, provided each shard has enough substantive work to justify setup, receipt and review. Keep coupled semantic decisions or exclusive shared writes serial. Do bounded shared foundation/T0 work inline when it does not require a separate Chat.

**Local uses one dependency DAG with multiple logical Task Pools.** Every planned task has a stable `key`, a `pool` (e.g. `articles`, `methods`, `regression`), `depends_on` task keys (possibly empty), `title` and complete `instructions`. A Pool is a fairness group, **not** an exclusive phase, fixed quota, permanent hard concurrency limit or integration barrier. Resource-limited pools may have a *learned temporary claim ceiling* following verified resource exhaustion. `collab claim` dynamically selects ready tasks across *all* pools; zero-active ready pools get attention at the next claim, then least-recently served pools. Any pool may use all otherwise idle Chats. A task is unlocked when all its declared predecessors have really completed and their Git deltas are materializable, even if other pools still contain long-running tasks. Each newly claimed Local Slot automatically enters the existing Codex Loop Logical Isolation with a fresh `isolation_id`; retries of that same Slot reuse it. The Slot may be reasoning-only, an independent reviewer, writable research/development, or a strict authoritative semantic stage. Task JSON may define `isolation.role`, `isolation.mutation_policy`, and optional `isolation.semantic_work` (consumer/stage/exact hashes). Default execution tasks are `researcher/workspace_write`, but `review_of` tasks automatically use `reviewer/read_only` unless the task explicitly declares a writable isolation policy; reviewers must finish the actual read-only review. The Slot context contains `collaboration.instructions_chunks` with complete normalized instructions. Logical Isolation is behavioral, not a fresh Chat/model; nested semantic isolations are not supported, so independent semantic stages should be separate dependent Slots. No task needs to wait for an unrelated Wave to finish. When a worker finishes, Codex Loop may return `continue_now=true` and its *next task already claimed*; the **same Chat and Codex Loop task_id** execute it immediately, without a new worker or grant. The runtime cannot wake an ended Chat; preserve useful activity in already-open workers using bounded `collab claim --wait` calls. Each idle planned worker checks Ready every 30 seconds, records a separate waiting-only heartbeat and repeats after a bounded `status=waiting` response. No Slot lease is refreshed; an idle Chat exits on `status=done`.

**Continuous dependent work:** Keep naturally consecutive steps in the same Chat when that reduces re-entry cost. Record separate semantic/review gates inside the task instructions, not as forced new physical workers. The only reasons for a genuine new Run/barrier are verified source snapshot acceptance, strict context isolation, exclusive writable resources, independent decision authority or an unmet prerequisite that cannot safely be materialized inside a worker. An arbitrary stage label is not a barrier. Preserve all safety and domain-specific evidence rules.

## General-purpose decomposition and validation

- **Fine-grained by default for repeated work:** Discover divisible workloads early: document discovery/download/extraction, generated examples, human revision recovery, per-batch QA, conversions, test cases and independent report sections. Unless coordination dominates, prewrite **several small tasks** rather than one author/project-sized task. Bias toward smaller batches when uncertainty is high; an overlarge task can delay downstream work even though it becomes reclaimable after the activity lease expires. This rule applies across *all* relevant Pools, not only AI generation.
- **Minimum useful task and practical sizing:** A shard must have an identifiable, bounded input set; a meaningful amount of work (not a trivial single tool call); one exclusive output surface; a verifiable deliverable; a failure/retry boundary; and a receipt that downstream tasks can consume. Estimate volume/complexity from a brief inventory where possible. Size by expected work, not equal article counts or one author per task. No universal batch count or fixed task/chat ratio: for example, 100–200 short generated passages may be reasonable, while complex multi-page extraction may merit a smaller shard. Merge shards when Git/connector startup, review and handoff would dominate the actual work. Do not precreate many speculative empty buckets.
- **Define initial shards before Run:** Partition known inputs by explicit file/work IDs or stable disjoint ranges/hashes. When the full source inventory is unavailable, do bounded source discovery inline (or one foundation task with a fixed, narrow contract) and predeclare a realistic number of downstream shards, each owning an exclusive subset. Do not hide hundreds of items inside one long task under a label such as 'resumable sub-batches'. Do not require a costly custom estimator or scheduling service.
- **Per-shard dependencies and early flow:** Connect a generated-output shard only to the source shard and shared contracts/holdout rules it actually needs; connect its independent review to that exact generated shard. A first completed source batch should unlock its downstream work while unrelated batches are still running. Reserve global fan-in for real shared identity/deduplication, irreversible split freezing, exclusive resources or final acceptance; do not add an all-sources barrier merely to align stages.
- **Outputs and ownership:** Each unit has one bounded deliverable, acceptance checks, stable input identity, owned paths and a tracked commit/receipt usable by a downstream consumer. For 100 articles, choose small independent batches by retrieval difficulty or actual work volume, not mechanically one author or one item per Slot. Do not let two writers race on the same output.
- **Dependencies:** Declare all edges explicitly in `depends_on`, including cross-Pool edges. Do not put a dependent task in a later Wave solely because its predecessor is running; it becomes ready automatically once predecessors complete. Reject cycles and missing keys. Selectively hold truly incompatible simultaneous writes or testing state until the correct shared baseline is available.
- **Fairness, not hard limits:** Use one global Ready Queue over logical Pools; no fixed per-Pool Chat cap. Do not reserve idle workers for empty Pools. With fewer Chats than active Pools, a ready Pool gets fair opportunity at subsequent task claim boundaries, not guaranteed simultaneous execution or preemption of an already claimed Slot. Do not promise an inactive Chat will be awakened.
- **Testing:** Put independent heavy test shards in their own logical Pool with dependencies on the exact integrated code or shared frozen input they actually require. Give shards isolated writable results and recorded commands/commits. Cross-system/final acceptance gates still aggregate every required result; missing/held/failed shards are not PASS.
- **Pre-registration granularity check:** For each repeatable workload, ask: Can one slow shard stall independent workers or its downstream? Can it be split without concurrent writes? Are any shards empty or so small their handoffs dominate? Check the resulting DAG for early per-shard unlocks, critical-path tail, unnecessary fan-in and shared resource conflicts. Prefer more tasks than simultaneous Chats; dynamic Pool claiming already amortizes many tasks over a few active workers.
- **Timing:** Evaluate critical path, uncertain tool waiting and expected validation cost, not equal task counts. Prewrite recovery branches for likely failures. Unexpected conflicting evidence is a blocker/plan amendment, not a license for the Integrator to redesign other work.

## Initial objective plan and environment-specific evolution

1. Resolve Local vs Web and exact repository/baseline before planning. Explicit environment choice wins, otherwise inherit the current conversation's selected environment. Local never stages prompts/receipts in Google Drive; Web uses the verified temporary Drive handoff protocol below.
2. **Before initial registration**, perform the bounded workload inventory, fine-grained shard sizing and dependency/critical-path review above. The normal invocation completes planning **and creates the Run** as soon as the required source and permission checks pass; no extra user launch approval is required. If an already registered same-goal Run exists, inspect its status before creating another; resume it when it is compatible, and never silently duplicate a running objective or overwrite claimed tasks. A truly changed scope needs an explicit, non-conflicting new objective/Run; leave active predecessors intact.
3. For **Local**, save one outside-repository initial `PLAN.md` and `objective.tasks.json` with all tasks currently justified by evidence and known dependencies. These launch artifacts establish source and initial scope; later permitted task changes live in the Codex Loop SQLite graph. Use the program-consumed keys `key`, `pool`, `depends_on`, `title`, `instructions`. A task can reference future inputs by stable task key and required artifact contract, never by fabricated future SHA. If a true post-fan-in serial chain is required, prewrite one `objective.serial-tail.md` pointing to the frozen PLAN's separate gates. Hash the initial tasks JSON and optional serial tail in PLAN; seal the initial PLAN last and give its SHA-256 in the identical launch prompt. Subsequent Local graph changes are authoritative in SQLite with graph revisions. Do not make circular SHA references.
4. For **Web**, where separate ChatGPT workspaces do not share host-local SQLite, freeze the entire multi-wave stage graph and all future lane definitions **before** Wave 1. Web physical lanes still use verified SOURCE_BASE_READY, Drive manifests and explicit Integration handoffs; do **not** promise Local DAG auto-claim behavior to Web lanes.
5. Keep stable objective plan ID separate from a Run ID. For Local, create **one Run for the initial known DAG** on the verified baseline, then revise that Run when required. Later Runs are exceptional and require an actual integration/isolation barrier. For Web, activate only the current stage's physical lanes; later stages reuse their frozen instructions with newly verified source commits/artifact IDs.
6. **Local:** update the live DAG when new evidence warrants it, using atomic `collab publish` and `collab revise` without overwriting claimed/done work or changing the user-authorized objective. **Web:** retain frozen-wave handoffs and the original plan amendment rules.

## Local: adaptive task graph and lease recovery

- **Local-only mutable DAG:** after initial Run creation, an active worker can publish new tasks/pools and revise unclaimed waiting/ready tasks through Codex Loop's atomic task APIs. Keep stable task keys, prevent cycles, check outputs and exclusive writers, and never rewrite claimed or completed task definitions. The plan is a starting contract, not an immutable prediction of discoveries.
- **Producer-independent review:** a worker creating an independent review task must specify the reviewed producer task(s). Assigning that task to its producing worker is forbidden at the SQLite claim boundary; explicit `exclude_workers` also excludes named stable registered workers. A worker must not claim then reject review work it should never have been offered.
- **Learn resource limits from actual acquisition failures:** By default leave pools uncapped and allow normal claims. On a verified GPU/resource-busy error **before starting task work**, use `collab resource-blocked --run-id RUN --task-id TASK --slot SLOT --lease-generation GEN --resource KEY --reason REASON`; only then immediately `collab claim --wait` from the same Chat and worktree. The Codex Loop backend verifies clean input state and ownership, aborts the Slot isolation, returns the task to Ready, records the concurrently occupied resource capacity and skips full resource groups. If no peers are active, it backs off for 60 seconds before probing again. Other eligible pools are never held behind this resource. Define an explicit shared resource group with `collab resource-cap` when separate pools use the same hardware; otherwise the failed pool learns its own limit. A process already started or any task with side effects **must not** be reported as an unstarted resource failure; reconcile it under ordinary Slot ownership.
- **Idle workers wait without claiming:** for planned Local Runs, use `collab claim --run-id RUN --task-id TASK --worktree-parent PARENT --wait --poll-interval 30 --max-wait-seconds 60`. While waiting, the native process updates only its `waiting_workers` heartbeat. `status=waiting` plus `retry_wait=true` means retry from the same Chat; a normal task result means execute; `status=done` means exit. When My Mac returns a live process PID or My WSL returns a job ID, observe that existing process/job rather than starting another claim. This heartbeat does not prove ChatGPT session liveness and must never be used to shorten the task lease.
- **Pool-fair inactivity recovery:** Codex Loop uses one atomic SQLite claim decision to compare eligible Ready work with assignments idle for at least 600 seconds. After **1800 seconds (30 minutes)**, an eligible stale task is the first takeover priority, oldest first, even if Ready tasks or routine reviews remain. From 600 through 1799 seconds, Ready normally wins; a stale task can outrank it if every unfinished direct prerequisite of a blocked DAG fan-in is stale and that downstream Pool has fewer functioning Workers. Otherwise it is reclaimed only after Ready is exhausted. Apply Worker exclusions and shared resource caps throughout. Expired leases are not functioning Workers; after a resource cap reduction, a stale lease can be replaced if healthy Workers leave capacity, without creating additional active reservations. Revoke only the chosen lease, increment ownership generation and recover task state in a distinct Worktree. Never infer that a stopped heartbeat means an external host process terminated: inspect in-flight effects before replay.
- **Reclaim and late completion:** reassignment requires a fresh ownership generation. The original Worker loses permission to publish/checkpoint/heartbeat but can still submit a *completed clean Git result* with its original `--slot` and `--lease-generation`. If the replacement has not completed and no dependency/delegation conflict exists, a verified original result wins atomically and the replacement receives a mandatory `stop_task` message; otherwise its commit is preserved as an alternative without overriding the accepted output. Preserve the old worktree, never write in it from the claimant, and continue in a distinct authorized worktree. On reclaim, `collab claim` now automatically restores the former task's own commits, staged/unstaged edits and nonignored untracked files after stable-snapshot and conflict checks; a `checkpoint` is optional. `restored_worktree` summarizes recovery; any listed ignored paths, running processes and uncertain external effects require inspection before replay. Internal symlinks are rebased to the new tree; no copied absolute links may reference the old Worker. An authorized coordinator can explicitly force a reclaim with a reason, subject to the same Git and side-effect checks.
- **Adaptive review loop:** a worker encountering a genuine new requirement should write its bounded task JSON object **or task array** (an atomic new Pool with internal dependencies) and call `collab publish --run-id RUN --task-id TASK --lease-generation GENERATION --task-file FILE`. For existing available work, use `collab revise --task-key KEY --expected-revision CURRENT --updates-file FILE`. A dedicated review task uses `review_of` with source task keys and optional `exclude_workers` with registered worker names, ordinals or task IDs; SQLite filters before claim. The current worker can keep working on its own task while a qualified different worker later claims the review.
- **Tool activity and message delivery:** with a deployed updated My Mac/WSL bridge, every scoped tool call carrying the current `task_id` performs a lightweight preflight: check unread worker messages/control each time and renew an active lease only if at least 60 seconds have elapsed. A pending `stop_task` prevents further operations on that worker. Without the updated connector, run `collab heartbeat --run-id RUN --task-id TASK --lease-generation GENERATION` after meaningful tool work and at safe intervals. Integrators use their returned integrator generation. An expired task lease may be reclaimed automatically on the next claim or explicitly with `collab reclaim --task-key KEY --force --reason REASON`. Stalled Integrators use `collab reclaim-integrator` after clean target Git verification. A local SQLite heartbeat is only as fresh as the host's observable tool activity. Long uninstrumented model thought does not register as tool activity; do not claim certain knowledge of a ChatGPT web conversation's liveness. No extra daemon or background Chat launcher is implied.

## Local: one Run, multiple Pools, reusable Chats

1. Verify the actual authorized Git checkout, exact base SHA/tree, worktree parent **outside** the repository and selected same-host My Mac/My WSL connector. Run all lifecycle and Git tools through that pinned connector; obtain exact scoped grants for each new Chat. The first coordinator completes bounded shared foundation before Run creation and verifies SOURCE_BASE_READY.
2. Select `detached` for short-lived workers or `branches` for long-lived independently reviewable work; both create private worktrees. After saving the initial PLAN and task file, execute:

   ```bash
   python3 scripts/codex_loop.py collab create --repo REPO --branch BRANCH \
     --goal-key PLAN_ID --worktree-parent PARENT \
     --tasks-file objective.tasks.json \
     --strategy detached
   ```

   Include `--serial-tail-file objective.serial-tail.md` only if the plan has a real continuous post-global-integration chain. Verify returned Run ID, task count, Pools and recorded source baseline.
3. Choose a **Chat count separately from task count** (e.g. 102 tasks across 3 Pools; 10 simultaneous Chats). Give **one identical one-line launcher**, pasted into each active Chat. Each Chat bootstraps its own Codex Loop lifecycle, claims the same `RUN_ID` with its own `task_id`, and works only inside its owned worktree. After its first successful claim, visibly print the stable worker name defined below. No 10-Chat hard cap or fixed Pool allocation is created in Codex Loop.
4. After completing each task, validate and commit a bounded result/receipt. For a read-only reviewer Slot, first submit the actual review via `isolate-finish`; Codex Loop checks that its worker worktree did not change and rechecks before recording Slot completion; for a strict semantic Slot, submit its **actual** stage result through `semantic-work-finish`. Writable Slots close automatically after verified Git finish; never synthesize review/PASS from Git artifacts. Then call `collab finish --run-id RUN --task-id TASK --slot SLOT_NUMBER --lease-generation GENERATION --commit-sha FULL_HEAD`. If the response has `continue_now=true`, **immediately use `next_task.logical_isolation` as the new bounded context and execute `next_task` in the same Chat** and finish again; no new `task_id` and no new launch prompt. If a dependency cherry-pick is interrupted, or `finish` returns `next_task_blocked`, inspect its actual cause and retry `collab claim` with the same Run/task_id after safe recovery; never blindly replay mutation or release the claimed Slot. A retried `finish` reconstructs a complete `next_task` instead of continuing with only `next_slot`. If no next task is returned by `finish`, this Chat enters the bounded `collab claim --wait` loop using its original task_id; it exits normally only when the Run has no future work or is no longer accepting claims. Expired claims are reclaimable without finding the original Chat.
5. `collab status --run-id RUN` shows each Pool's `ready`, `waiting`, `claimed` and `done` counts, plus currently heartbeating idle workers. A task remains waiting only for its own unsatisfied upstream keys. Do not defer unrelated ready tasks for a whole-Pool/whole-Wave integration checkpoint.
6. The last worker becomes Integrator only when **all DAG tasks** and delegations have completed. It integrates task-specific commit deltas in dependency order, performs the frozen global acceptance checks and executes a registered post-integration serial tail immediately within its same Chat if returned by `integration-complete`. A cross-branch conflict must be resolved against the original plan before release; no automatic Git push without authorization.
7. If tasks are held by inactive Chats, use Codex Loop lease-aware reclaim and automatically continue from copied verified worktree state, without relying on checkpoints. Inspect `restored_worktree`, preserve both isolated worktrees and reconcile ignored task artifacts, Git conflicts and external effects before repeating them; never access or erase another worker worktree. Report the exact tasks and unresolved Git/external effects that prevent safe continuation.

### Stable Local worker names

**Stable Local Chat names after the first successful claim (same launcher for everyone):** Once `collab claim` returns success, immediately derive that Chat's run-local worker ordinal by querying the **existing** Codex Loop `workers` registry **read-only** on the already pinned computer. The first successfully registered worker is `<Project>-worker1`, the second `<Project>-worker2`, etc. `Project` comes from the Run repository directory name, not a task title or a hardcoded project name. Worker registration is serialized by SQLite and retained across subsequent Slot claims. Use the same `CODEX_LOOP_HOME` environment as the actual Codex Loop commands (default `~/.codex-loop/runtime`). Pass the real `RUN_ID` and `TASK_ID` returned by `collab claim` to this host-local Python command; do not replace them with the Slot number:

```bash
python3 - "$RUN_ID" "$TASK_ID" <<'PYWORKER'
import os, re, sqlite3, sys
from pathlib import Path

state = Path(os.environ.get("CODEX_LOOP_HOME", "~/.codex-loop/runtime")).expanduser()
db = state / "local-collaboration.sqlite3"
if not db.is_file():
    raise SystemExit("Codex Loop collaboration database not found; confirm the same CODEX_LOOP_HOME")
with sqlite3.connect(db.resolve().as_uri() + "?mode=ro", uri=True) as conn:
    result = conn.execute("""
        SELECT r.repo, (
            SELECT COUNT(*) FROM workers earlier
            WHERE earlier.run_id = w.run_id AND earlier.rowid <= w.rowid
        )
        FROM workers w JOIN runs r ON r.id = w.run_id
        WHERE w.run_id = ? AND w.task_id = ?
    """, (sys.argv[1], sys.argv[2])).fetchone()
if result is None:
    raise SystemExit("No successful worker registration for this Run/task_id; do not invent a name")
repo, ordinal = result
project = re.sub(r"[^A-Za-z0-9_-]+", "-", Path(repo).name).strip("-") or "Project"
print(f"{project}-worker{ordinal}")
PYWORKER
```

**Immediately print the returned name in the Chat's first user-visible reply after claim**, on its own prominent line (for example, **`ResearchProject-worker3`**), followed by the Run ID, `task_id` and current Slot. Tool stdout alone does not count. The Chat title itself cannot be assumed to change automatically. On `continue_now=true` or an interrupted Chat's resume, use the same registered worker identity and original `task_id`; Slot numbers can change, names do not. Do not allocate names before a successful claim, assign numbers manually, write a separate registry, alter Codex Loop's database or promise Local numbering for Web lanes. If this read-only query fails, report the exact identity lookup failure without guessing a number; the claimed task stays claimed.

**Identical Local launch message** (paste into each Chat, not once per task):

`I authorize this chat through My Mac/My WSL to read/write PROJECT and WORKTREE_PARENT for RUN_ID, create/edit only its claimed worktree, execute scoped tests and local Git commits, and integrate into TARGET_CHECKOUT only if elected; do not push. Initialize/resume Codex Loop on this host, call collab claim --wait --poll-interval 30 --max-wait-seconds 60 for RUN_ID with this chat's task_id and authorized worktree parent; on a normal task result use the returned `logical_isolation` projection (do not call `isolate-enter` again); review automatic `restored_worktree` recovery if reclaimed, reconcile reported ignored artifacts/uncertain external effects, verify inherited dependencies, execute and finish with --slot, --lease-generation and --commit-sha, with the tool activity hook enabled when installed and otherwise explicit collab heartbeat after meaningful tool calls; always handle pending inbox and stop notices before continuing. After the first successful collab claim, print the stable <Project>-worker<N> name using the read-only registered-worker query above. If finish returns continue_now=true, switch to next_task.logical_isolation (fresh Slot scope) and execute next_task with the SAME task_id/worktree; if next_task_blocked is returned, inspect and repair the original claim problem; otherwise if not elected Integrator, resume the bounded wait loop. If wait returns status=waiting, call it again with the SAME task_id; if status=done, exit. Observe an existing My Mac process or My WSL job instead of launching a duplicate wait. Keep repeating. Follow original PLAN_PATH/PLAN_SHA for objective and baseline, but use the live Local task graph revision for any later task amendments. Only the last finisher integrates and runs the post-integration tail.`

## Web: preplanned physical lanes

For Web use the Web-only source, Drive, thread-handle, prompt and integration contracts below. One coordinator still prewrites every stage, but dynamic Local Pool/DAG claiming requires a verified same-host SQLite registry and must never be promised in independent Web workspaces. Split repeatable work into bounded independent producer units, but group contiguous microsteps in one physical lane when a separate Chat and Drive handoff would cost more than the task. Keep known dependent serial stages inside the same Web Integration Chat rather than multiplying manual relaunches; independent physical lanes may start together when their immutable source baseline and ownership permit it.

## Workflow

1. Read current request, final acceptance conditions and repository context; use the corresponding Domain Skill and Codex Loop lifecycle. If an existing frozen plan/Run is explicitly named, resume it instead of replanning.
2. Identify transferable deliverables, risk and effort. For each repeated workload, prefer several small meaningful shards with disjoint inputs/outputs and early per-shard dependency unlocks; merge only if handoff dominates or work is inseparable. Compare critical paths and identify genuinely exclusive resources. Record DAG edges, final test/fan-in gates and exact stage authority **before plan freeze and Run creation**. For Local, prefer one DAG run across multiple logical pools; for Web, freeze all later wave definitions before starting.
3. Verify SOURCE_BASE_READY and run environment-specific permissions. For Local, persist the initial tasks file and plan SHA, create the verified Run and return one identical launcher with Chat copy count. For Web, stage only the current lane prompts/manifests under the verified Drive control plane.
4. Workers preserve source provenance and scoped write ownership, produce their required evidence and commit/hand off. Integrators verify results against the frozen acceptance gates. Never confuse a tracked receipt with a semantic PASS or stage completion.
5. Report exact ready/held tasks and concrete prerequisites. An unexpected blocker invokes only an authorized recovery branch; for Web, change frozen stage definitions only after a real amendment; for Local, use atomic DAG publication/revision.

## SOURCE_BASE_READY pre-fan-out gate

Apply this gate whenever a fresh physical lane will inspect, test, edit, package, or otherwise depend on a Git repository. The coordinator owns the gate. Do **not** delegate source acquisition discovery to T1/T2/T3 and do not generate launch prompts first.

Generate and freeze the orchestration run id before creating any run-scoped source artifact, but prove `SOURCE_BASE_READY` **before** staging worker prompts, HANDOFF-INDEX documents, or producer manifests.

### Required identity

Resolve and record all of the following for each repository/base used by the run:

- canonical repository identity;
- target branch when applicable;
- exact 40-hex base commit;
- exact Git tree for that commit;
- whether the coordinator's current resolved development environment already contains a verified real Git workspace for that exact commit/tree;
- the exact materialization path that every fresh physical lane will use.

Never substitute a branch name, `latest`, a remembered commit, an installed Skill copy, a filename, or a few matching files for exact commit/tree identity.

### Accepted lane materialization paths

A source base is READY only when one of these paths is proven end to end:

1. **Resolved-environment exact workspace fork/worktree/snapshot** — the resolved development environment provides a fresh physical lane with an exact copy/fork/worktree/snapshot of the verified base, and the lane can independently verify exact HEAD commit/tree before implementation. This includes a host-provided Web workspace fork and, when Mac/local is the resolved environment, an explicitly authorized local Git worktree/clone created from the verified local source authority.
2. **Codex Loop verified source acquisition** — an exact GitHub commit-bound source artifact, a receipt-bound published-source artifact, or another current Codex Loop-approved direct acquisition path is already available to fresh lanes with the required artifact identity/integrity/fresh-restore evidence. Reuse Codex Loop's source-acquisition contract rather than inventing a weaker TDO transport.
3. **Coordinator-staged Drive SOURCE-BASE bundle** — when the coordinator already holds a verified repository workspace in the resolved development environment but fresh lanes otherwise lack a direct source path, treat source distribution as bounded inline foundation work:
   - require a real Git repository and exact verified base commit/tree;
   - require complete/non-shallow history sufficient to construct a self-contained Git bundle;
   - create one run-scoped export ref and a self-contained Git bundle for the exact base;
   - fresh-clone that bundle into an empty temporary repository and require restored HEAD commit/tree to equal the recorded base;
   - compute and record bundle byte size and SHA-256;
   - upload it to `ChatGPT Thread Development Temp` with canonical title `<run-id>-SOURCE-BASE-<slug>.bundle`;
   - fresh-search/read back the exact title, parent folder, Drive ID/URL, size/hash when available, and keep the local SHA-256 authoritative when Drive does not expose one;
   - every worker prompt must name this exact artifact and require the lane to materialize a fresh real Git workspace from it, then verify exact HEAD commit/tree before implementation.

A generic GitHub archive, per-file GitHub reconstruction, model-carried source, a stale copied folder, or an implicit environment switch is not a READY path. If the current source authority itself is unverified, do not manufacture a Drive SOURCE-BASE bundle from it.

### SOURCE_BASE_READY receipt

Before fan-out, keep one bounded source-base receipt in the coordinator context and project the necessary fields into every affected full lane prompt:

```text
SOURCE_BASE_READY
run_id: <immutable run id>
repository: <owner/repo or canonical identity>
target_branch: <branch or n/a>
base_commit: <40-hex>
base_tree: <40-hex>
acquisition_kind: <host_fork | codex_loop_direct | drive_source_bundle>
artifact_title: <exact title or n/a>
artifact_id_or_url: <fresh identifier or n/a>
artifact_size: <bytes or n/a>
artifact_sha256: <sha256 or n/a>
fresh_restore: PASS
status: READY
```

`fresh_restore: PASS` means a fresh lane/restoration path has actually been proven to reproduce the exact base commit/tree; it is not a statement that the coordinator merely has access to the repository.

### Fail closed before fan-out

If any required field or materialization proof is missing, report `SOURCE_BASE_NOT_READY` with the exact missing dependency and stop **before** staging launch prompts. Do not:

- emit T1/T2/T3 prompts that will predictably block on source acquisition;
- create placeholder handoff manifests for lanes that cannot start;
- switch away from the resolved development environment merely because another environment is more convenient;
- mutate the target repository merely to manufacture a more convenient downloadable revision;
- treat a source-acquisition blocker discovered by a worker as normal execution progress.

Once the gate is READY, worker source setup should be mechanical. A lane that cannot reproduce the recorded base must report `SOURCE_BASE_MISMATCH` and stop before implementation rather than improvising a new source path.

For Git-backed Mac/local lanes, use host-friendly commit/tree verification commands in generated prompts and launch instructions:

```bash
git show -s --format=%H HEAD
git show -s --format=%T HEAD
```

Compare those outputs to the recorded `base_commit` and `base_tree`. Do not require revision-expression forms such as `HEAD^{tree}` when the two explicit `git show` commands provide the same identity with less host-filter ambiguity.

## Web-only Google Drive staging contract

### Orchestration control plane vs development plane

Keep prompt authoring/staging separate from repository execution:

- **Orchestration control plane (Web only)**: the current ChatGPT coordinator context materializes run-specific lane prompts, metadata, manifests and handoffs from the frozen full-project plan in Drive. Mac/local instead uses the authorized host-local plan and Codex Loop SQLite; it never stages prompts in Drive.
- **Development plane**: the resolved Mac/local or Web workspace owns repository inspection, Git/worktrees, builds, tests, commits, and `SOURCE_BASE_READY` materialization. Use RDC/Desktop tooling only when those development-plane operations actually require the Mac/local machine.
- Never use RDC, a Mac/local shell, or the repository workspace merely to draft, transport, or stage a text prompt into Drive. A repository-local file is not a prerequisite for a Drive prompt.
- If direct Drive staging is temporarily unavailable, retry the Drive operation in a bounded way or report `STAGING_BLOCKED`. Do not reroute prompt authoring through RDC or silently change the resolved development environment.
- A transient file created inside the ChatGPT runtime for connector-compatible import is still orchestration-plane staging; it must not require access to the user's Mac/local repository. Prefer direct native Google Doc creation/write when available.

Use the Google Drive connector. Reuse or create exactly one folder named:

`ChatGPT Thread Development Temp`

Generate exactly one run id at orchestration start using:

`MTD-YYYYMMDD-HHMMSS-<nonce>-<short-slug>`

Use the current user-local timestamp when available, otherwise UTC. Generate `<nonce>` as 4 random uppercase alphanumeric characters. Before staging any run documents, fresh-search `ChatGPT Thread Development Temp` for the exact candidate run id; if any same-run title already exists, regenerate only the nonce and check again. Freeze the run id immediately after this uniqueness check. It is **immutable for the entire run**: never regenerate, shorten, or vary it per thread.

Treat the exact immutable run id as the namespace for every newly created run-scoped identity: physical lane/thread labels, Drive prompt documents, HANDOFF-INDEX, producer manifests, handoff artifacts, SOURCE-BASE artifacts, and any new Git branch or worktree created for the run in the resolved development environment. Use names such as `<run-id>-T1-<slug>` and `<run-id>-INTEGRATION-<slug>`. `T1`, `T2`, and similar thread IDs are unique only inside their run id and must never be used alone to identify cross-thread state.

Treat thread IDs as **physical chat/lane IDs**, not pipeline-stage IDs. Only create a prompt document for a chat the user actually needs to launch. An inline foundation has no T0 prompt document. A T0 document is exceptional and exists only when the T0-inline rule requires a separate fresh lane.

### User-visible thread handle contract

For each user-launched physical lane, assign one stable human-facing handle before its Drive prompt is staged. Default to four uppercase alphanumeric characters, for example `N4K7`, `FN9I`, or `Q2MX`. Check only for collisions inside the current run and regenerate on collision. If the host/workspace has already exposed a stable user-visible thread name for that lane, preserve that exact name instead of inventing a competing alias.

Freeze the handle for the lifetime of the run. Do not silently rename it later because a descriptive purpose changes, a stage advances, or another opaque identifier appears. The handle does **not** replace the immutable run id or physical lane id in Git/Drive identity; it is a recognition layer for the user.

Surface the same handle consistently in all user-facing references to the lane:

- the `Thread identity` section of the full staged prompt;
- the coordinator's execution-order label, preferably as `<handle> · T<n> — <purpose>`;
- the one-line copy-paste launch prompt, which must contain both `<handle>` and `T<n>`;
- the first dependency/handoff reference to that lane in another user-visible prompt or report;
- the lane's final handoff/completion report.

Never return a launch prompt whose only recognizable identity is `T<n>` plus a descriptive title. A user must be able to match a later opaque thread name such as `N4K7` directly back to the original launch prompt without opening the Drive document.

Store one native Google Doc prompt per physical execution lane with deterministic titles. Typical titles are:

`<run-id>-T1-<slug>`
`<run-id>-T2-<slug>`
`...`
`<run-id>-INTEGRATION-<slug>`

Use `<run-id>-T0-<slug>` only for the exceptional separate-T0 case. If one downstream continuation lane also owns final integration, use one prompt document for that lane rather than a chain of stage documents.

Create each document, write the full prompt, move it into the temp folder if needed, then verify exact title and parent folder before returning the short launch prompt.

Treat Google Drive as the **only remote upload/write target** for orchestration prompts and cross-thread handoff artifacts. Do not upload prompts, handoff files, patches, or coordination metadata to GitHub. Do not create or update GitHub branches, pull requests, issues, comments, releases, gists, or other GitHub artifacts, and do not run `git push` as part of orchestration unless the user separately and explicitly requests publication. Git branches, commits, worktrees, shell commands, and repository inspection must stay in the resolved development environment.

### Development environment resolution rule

Resolve the **repository development environment** once at orchestration start and project that same resolved value into every staged lane. Apply this precedence: (1) if the user explicitly requests Mac/local development, use Mac/local; (2) if the user explicitly requests Web development, use ChatGPT Web workspace / Web mode; (3) if the user does not specify a location, inherit the development environment already active in the current conversation. Do not replace an inherited current-conversation environment with a global Web default. Use an unambiguous environment label in prompts (`Web workspace` for Web; the concrete Mac/local workspace label for local execution). When this run creates a Git branch or worktree, namespace its name with the exact immutable run id, for example `<run-id>-T1-<slug>`; do not create generic `T1`, `integration`, or other cross-run-ambiguous names. Preserve the exact run id even when adapting to repository-specific naming conventions. Do not switch repository execution away from the resolved environment merely because another environment is more convenient or because a capability is missing there. If a required repository capability is unavailable in the resolved environment, report the blocker rather than silently rerouting. If the user explicitly changes the development location later in the same objective, re-resolve from that new instruction and treat the change as a routing transition before further environment-sensitive work.

This resolution governs **repository execution only**. It does not move the orchestration control plane: lane-prompt authoring and Google Drive staging remain in the current ChatGPT coordinator context and use the Drive connector directly, regardless of whether repository work runs on Mac/local or Web.

### Mac/local launch authorization contract

A fresh chat does not inherit repository-access or local-mutation authority from the coordinator, a prior lane, RDC connectivity, a known path, or text stored inside a Drive prompt. When the resolved development environment is Mac/local, the **user-visible one-line launch message itself** must explicitly grant the new conversation the local authority that lane actually needs.

- For a read-only repository lane, grant access to the exact worktree named in the Drive prompt plus the repository/process/Git-read operations needed to inspect and validate it.
- For a lane that will create/edit files, stage changes, or commit, additionally grant local source mutation, repository writes, process execution, Git staging, local commit creation, and commit/tree verification, limited to that exact worktree and lane task.
- Do not grant broader filesystem or Git authority than the lane requires.
- Repeat the intended scope in the full Drive prompt for clarity, but never treat the Drive document as a substitute for current-conversation user authorization.
- If a Mac/local launch message was emitted without the required authorization language, fix the launch message before asking the user to start the lane; do not knowingly launch a lane that will fail closed at its first RDC/Git boundary.

Do not put secrets, credentials, private keys, tokens, or unrelated user data into staged prompts or handoff metadata.

## Web-only cross-thread Drive handoff contract

Apply this contract only when a physical execution lane produces a Drive file/package that another physical lane or the final integration lane must consume. Do not manufacture Drive handoffs between serial phases that remain in one chat/lane. The pre-fan-out `SOURCE-BASE` bundle is foundation distribution and follows the SOURCE_BASE_READY contract above; it does not make worker lanes producers before they start.

Drive creation, upload, search, read-back, manifest editing, and cleanup are connector operations performed from the producing/consuming ChatGPT lane. They do not move into the resolved repository development environment merely because that lane edits a Mac/local repository. Use RDC/local filesystem only to create or inspect the **actual payload bytes** when the handoff artifact itself is a repository output; use the Drive connector for the remote handoff operation.

### Canonical artifact identity

Assign each required artifact a stable uppercase artifact key such as `LEARNING-PACKAGE`, `EVAL-PACKAGE`, `MIGRATION-MAP`, or `TEST-REPORT`.

Use the canonical Drive title:

`<run-id>-T<thread-id>-HANDOFF-<artifact-key>-<slug>[.<ext>]`

Do not use ambiguous names such as `latest`, `final-package`, `output`, or `integration-package`. Preserve a meaningful extension for uploaded files. The producing thread must not rename the artifact after verification unless it rewrites and re-verifies its manifest.

### Immutable run handoff index

If any cross-thread Drive artifact is expected, create one native Google Doc before launching threads:

`<run-id>-HANDOFF-INDEX`

Keep this index immutable during parallel execution. Record:

- run id;
- every producer thread id;
- every deterministic producer manifest title;
- expected artifact keys and canonical titles;
- required versus optional status;
- intended consumer thread(s).

Do not make parallel threads edit this shared index.

### Per-thread handoff manifest

For every producer thread with downstream Drive artifacts, create one native Google Doc before launch:

`<run-id>-T<thread-id>-HANDOFF-MANIFEST`

The producer thread exclusively owns updates to its manifest. Initialize it with `status: pending` plus the expected artifact rows. After upload, rewrite each row with:

- run id and producer thread id;
- artifact key;
- exact canonical Drive title;
- fresh Drive file ID and/or URL returned by search/read-back;
- verified parent folder (`ChatGPT Thread Development Temp`);
- file type/MIME when available;
- file size/checksum when available and useful;
- producer branch/worktree in the resolved development environment and exact commit/tree when applicable;
- `status: verified` only after successful read-back;
- any limitations or consumer notes.

Avoid a single mutable manifest shared by parallel producer threads.

### Upload and read-back verification

A producer must not claim a Drive handoff is complete merely because upload/create returned success. For every required artifact:

1. Upload/create it with the exact canonical title in `ChatGPT Thread Development Temp`.
2. Fresh-search the exact title immediately after upload; do not rely only on the creation response URL.
3. Verify exact title, run id, producer thread id, and parent folder.
4. Read/open the artifact or verify sufficient file metadata to prove the Integration chat can access it. For packages/binaries, verify size/checksum when the connector exposes them or inspect the archive after materialization when required by the task.
5. Update only that producer's handoff manifest with the fresh ID/URL and mark the artifact `verified`.
6. Re-read the manifest and confirm the recorded identity matches the artifact actually found.

If any required read-back fails, keep the manifest/artifact for recovery and report the thread as blocked or partially complete. Do not substitute fuzzy search results.

## Web-only robust Drive cleanup protocol

Use this protocol whenever deleting a completed prompt, manifest, index, SOURCE-BASE bundle, or handoff artifact:

1. Do not rely on a stale URL captured at creation time. Fresh-search `ChatGPT Thread Development Temp` using the exact run id and exact title immediately before deletion.
2. Verify exact title and expected parent folder. If identity or parentage is ambiguous, do not delete it.
3. Delete using the freshly discovered Drive URL/ID.
4. If deletion fails, re-search the same exact title once and retry deletion exactly once with the refreshed URL/ID. Do not loop indefinitely.
5. If the second attempt fails, record the exact title plus connector error/reason and leave the item for Integration cleanup.
6. After a reported delete success, optionally re-search the exact title to confirm absence when deletion has been flaky.

Producer threads may delete their completed prompt documents after verified handoff, but must keep any manifest and artifact required downstream. Integration owns deletion of resolved handoff manifests, the handoff index, consumed artifacts, the SOURCE-BASE bundle after all consumers are done, and its own prompt after final verification. Never delete by approximate title, prefix alone, or folder-wide bulk action.

## Full thread prompt contract

Each staged prompt must be self-contained enough to execute in a fresh chat. Include only sections that matter, while preserving these semantics:

- **Thread identity**: exact immutable run id, thread id, frozen user-visible thread handle, and purpose. Put the handle near the top in an explicit field such as `Thread handle: N4K7`. Treat the run id as authoritative and reject same-looking `T<n>` state from any other run.
- **SOURCE_BASE_READY**: for repository lanes, include repository identity, exact base commit/tree, acquisition kind, exact source artifact title/ID/hash/size when applicable, and the required fresh materialization/verification procedure. Require `SOURCE_BASE_MISMATCH` and stop before implementation if the lane cannot reproduce the recorded base.
- **Objective**: the thread's contribution to the user's actual end state.
- **Repository baseline**: repository, base branch/revision, resolved development environment, and any branch/worktree used for this thread. Any branch/worktree newly created for this run must include the exact immutable run id in its name. Never require a remote GitHub branch unless publication is explicitly in scope.
- **Dependencies**: reference the immutable full-objective plan URI/path and SHA, current stage ID and exact prerequisite conditions. Materialize the real predecessor commit or verified workspace state when available; never invent a future Git SHA. Multiple dependent serial stages assigned to this lane stay inside it.
- **In scope**: concrete responsibilities and owned concepts/files/surfaces.
- **Out of scope**: what this thread must not redesign or duplicate.
- **Shared contracts**: schemas, vocabulary, authority rules, interfaces, or invariants frozen by a foundation thread.
- **Required workflow/Skills**: preserve any project-specific lifecycle such as Codex Loop, Epi Lab, or another named workflow from the parent task. When this lane contains dependent serial phases, keep them in this chat and use Codex Loop logical isolation/checkpoints when semantic re-observation or reviewer separation is useful; do not ask the user to launch another chat between those phases.
- **Execution requirements**: materialize and verify the recorded source base first, then inspect current state, implement, test, and review final changes in the resolved development environment. For Git-backed Mac/local lanes, require `git show -s --format=%H HEAD` and `git show -s --format=%T HEAD` (or an equivalently host-safe identity check) against the recorded commit/tree. Keep Git branch/commit/worktree in that environment; do not switch environments unless the user explicitly changes the development location. State the minimum local repository/read-write/process/Git capability scope the lane needs so the coordinator can project the matching explicit authorization into the user-visible launch message. Do not push or write to GitHub unless publication is explicitly in scope.
- **Drive handoff artifacts**: when applicable, list every artifact key, exact canonical title, manifest title, consumer, and required/optional status. Require upload plus read-back verification before `status: verified`.
- **Acceptance criteria**: observable completion conditions for this thread.
- **Handoff**: return the resolved environment, branch/worktree and exact commit/tree, changed surfaces, performed validations, limitations, integration notes and plan stage ID. If this Chat is the elected Integrator, **read and activate only the prewritten next stage** after its real preconditions pass and return a short same-for-every-worker launcher. Web handoffs also report exact titles and freshly verified Drive IDs/URLs.
- **Cleanup**: after verified repository/Drive handoff, clean only this thread's prompt using the robust cleanup protocol. Preserve downstream manifests/artifacts for Integration. If cleanup still fails after the single retry, report the exact leftover title/error and complete the development handoff normally.

Do not copy the entire parent design into every lane. Project only the shared context needed to prevent semantic drift. A lane may contain several serial internal phases; its prompt should make that phase sequence explicit and require the lane to continue through them without user relaunch unless a real blocker occurs.

## Web-only integration prompt contract

The lane that owns Integration is not a mechanical merge lane. By default it must also execute all post-fan-in serial stages in the same Chat, without stopping for another prompt or a new Slot; split only for a prewritten strict isolation or independently required permission boundary. Require it to:

1. Verify that every expected branch/worktree in the resolved development environment, prompt, manifest, index, source-base receipt/artifact, and handoff belongs to the same exact immutable run id; reject artifacts or `T<n>` state from any other run. Then verify every expected handoff commit or equivalent workspace state in that same environment. Do not depend on GitHub remote refs unless publication is explicitly in scope.
2. Require every repository lane to descend from or explicitly reconcile against the exact recorded SOURCE_BASE_READY commit/tree. A lane that started from another base is not silently merged as equivalent.
3. If Drive handoffs exist, fresh-search the exact `<run-id>-HANDOFF-INDEX`, verify its parent folder/run id, and read it before looking for artifacts.
4. For every producer, fresh-search and read the exact `<run-id>-T<thread-id>-HANDOFF-MANIFEST`. Require the manifest run id/thread id to match and every required artifact to be `status: verified`.
5. Resolve each artifact primarily by the fresh Drive ID/URL recorded in the producer manifest. If that identifier is stale or inaccessible, re-search **once** by the exact canonical title in `ChatGPT Thread Development Temp`, verify exact identity/parentage, and use the refreshed result. Never guess by fuzzy filename similarity.
6. Read/open/materialize required artifacts as appropriate before integration. If a required artifact cannot be verified, stop that dependency path and report the exact missing manifest/title/field rather than silently substituting another file.
7. Integrate in dependency order, not arbitrary completion order.
8. Resolve conceptual conflicts against the shared foundation contract and original user objective.
9. Run the cross-thread checks **assigned to this stage by the original plan**; reserve the full regression/adversarial suite for its explicitly planned final gate unless a safety issue requires an earlier targeted test.
10. Check this stage's factual contribution against the saved final objective and do not call early-stage branch-only tests a full acceptance verdict.
11. Report any abandoned or superseded thread explicitly.
12. Immediately complete the entire uninterrupted serial chain **already assigned by the frozen full-objective plan** in this very Integration Chat, preserving the separately specified evidence gates. Do not stop between consecutive stages. Only when the next prewritten stage requires genuine independent physical Chats, activate its definitions with a fresh verified baseline. Do not redo project decomposition during integration or quietly change the next wave. Preserve the user's planned test timing and version gates.
13. After verified integration, run the robust Drive cleanup protocol across all resolved same-run prompt docs, producer manifests, the handoff index, the SOURCE-BASE bundle after all consumers are finished, and consumed temporary handoff artifacts. Delete the final integration-owning prompt last. Treat undeletable leftovers as cleanup residue, not as a reason to invalidate otherwise verified integration.

## Web-only short launch prompts

Keep each launch prompt to one line and directly pasteable into a fresh chat. Create launch prompts only for physical lanes the user must actually start. The launch message is part of the authority boundary: for Mac/local repository work it must carry the explicit current-conversation local authorization described above.

Default Web/non-local worker form:

`[<handle> | T<n> — <purpose>] Execute the Drive temp document "<document-title>" completely in the resolved development environment named in the document; materialize and verify its exact SOURCE_BASE_READY baseline before implementation, continue through all serial phases contained in that lane, verify the handoff, then clean up as instructed.`

The old Mac/local Drive-launch forms are removed. For Local launches, use the one identical `collab claim` launcher under **Mandatory Local / Web split**. For Web Integration, keep the established single Web integration lane and Web Drive document.

Do not emit a launch prompt for inline foundation/T0 work that the coordinator already completed. Do not expose the full staged prompts in chat unless the user asks to inspect them.

## Response format

Keep the coordinator response brief. For **Local**, show the initial DAG summary and current graph revision (logical Pools, **repeatable-work shard sizes** and dependency fan-ins, acceptance gates), total task count, proposed simultaneous Chat count and verified active Run. Give **one identical launcher to paste Chat-count times**. Say explicitly that every active Chat reuses the same `task_id` and worktree when `finish.continue_now` supplies `next_task`, and that Pool fairness has no hard concurrency ceiling. Do **not** demand a second launcher at a purely logical Wave boundary.

For **Web**, show the saved stage order, current SOURCE_BASE_READY evidence, currently runnable physical lanes and their one-line prompts, plus the preplanned integration owner. Later Web stages use their already frozen task definitions; avoid unnecessary manual sync. If SOURCE_BASE_READY is unavailable, return the precise blocker. If the whole objective is serial, prefer one execution Chat rather than artificial parallel work. Never promise completion by a stopped/inactive Chat.

## Cleanup-only invocation

1. Identify the run id from conversation context or the user's supplied run id.
2. Search only `ChatGPT Thread Development Temp` for matching prompt docs, source-base artifacts, handoff manifests, the handoff index, and temporary handoff artifacts.
3. Confirm from current conversation/external handoff evidence that each matching thread is completed, integrated, or explicitly abandoned and that no unresolved downstream consumer still needs the artifact.
4. Delete only resolved matching items using the robust Drive cleanup protocol: fresh exact-title search before deletion, then at most one refreshed retry on failure.
5. Report unresolved documents/artifacts and any still-undeletable resolved leftovers briefly, including exact titles and connector errors when available. Leave them intact.
