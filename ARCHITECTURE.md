# Thread Development Orchestrator Architecture

```mermaid
flowchart TD
  A[User objective, scope, authorizations] --> R[Resolve same execution environment and source]
  R --> INV[Bounded workload/source inventory and source-baseline preflight]
  INV --> SH[Prefer small meaningful disjoint shards for repeatable work]
  SH --> DAG[Initial dependency and ownership checks]
  DAG --> P[Register initial task graph and source contract]
  P --> D{Environment}
  D -->|Local| L[Persist PLAN.md and one complete objective.tasks.json + hashes outside repository]
  D -->|Web| W[Freeze all Web waves/lane definitions and SOURCE_BASE_READY]
  L --> S[One Codex Loop Run with stable task keys, logical pools and depends_on edges]
  S --> CH[Launch K authorized Chats with identical RUN launcher]
  CH --> Q[Global SQLite Ready Queue: fairness and eligible worker filtering]
  Q --> CLAIM[Atomic claim and reuse same Chat task_id/worktree]
  CLAIM -->|Eligible Ready task| SNAP{Reclaimed assignment?}
  CLAIM -->|No eligible Ready task| WAIT[Bounded foreground wait with separate idle-worker heartbeat every 30s]
  WAIT -->|Poll same SQLite queue| CLAIM
  WAIT -->|Run closed / no future work| EXIT[Idle Chat exits]
  SNAP -->|Yes| RESTORE[Materialize dependencies; snapshot old HEAD, index and nonignored task files]
  RESTORE -->|Consistent and conflict-free; symlinks rebased| NAME[Read registered worker name; print Project-workerN]
  RESTORE -->|Unsafe/conflicting| HOLD[Keep task claimed; preserve both worktrees for manual reconciliation]
  SNAP -->|No| NAME
  NAME --> ISOIN[Existing Codex Loop Logical Isolation: new bound context per Slot]
  ISOIN -->|Full Slot instructions; read-only review or writable task| TASK[Execute current Slot; commit result]
  ISOIN -->|Entry failed after SQLite claim| REENTRY[Keep claim intact; retry same Run/task_id]
  REENTRY --> ISOIN
  TASK --> REV[Worker publishes atomic task batch / Pool or revises waiting work]
  REV --> Q
  CLAIM --> LEASE[10 minute observable activity lease]
  LEASE --> TOOL[Scoped MCP call checks inbox and refreshes last_seen at most every 60 seconds]
  TOOL -->|STOP message| HALT[Stop newer Worker on next call; preserve its local output]
  LEASE -->|Expired at next claim| RECLAIM[Revoke generation; preserve old worktree for automatic task-state continuation]
  RECLAIM --> Q
  RECLAIM -. original verified late finish .-> LATE[First accepted verified result wins if successor unfinished]
  LATE --> HALT
  TASK --> FIN[collab finish unlocks dependent tasks]
  FIN --> ISOCLOSE[Auto-close writable Slot; read-only needs isolate-finish; strict semantic needs semantic-work-finish]
  ISOCLOSE -->|finish.continue_now or idempotent retry with complete next_task| ISOIN
  FIN -->|No task; not Integrator; no blocker| WAIT
  FIN --> DONE{All DAG tasks and delegations complete?}
  DONE -->|No| Q
  DONE -->|Yes| INT[Last finisher integrates own task deltas in dependency order]
  INT --> VALID[Stage-appropriate final tests + integration-complete]
  INT -. stale Integrator .-> IR[Reclaim integration owner after clean Git check]
  IR --> INT
  VALID --> TAIL{Prewritten post-integration serial tail?}
  TAIL -->|Yes| SER[Same Chat executes all gates and serial-complete]
  TAIL -->|No| F[Final acceptance/release]
  SER --> F
  W --> WEB[Web lanes and verified Drive handoffs]
  WEB --> WEBINT[Web Integration executes predefined serial stages, activates next frozen physical wave]
  WEBINT -->|Next physical wave| WEB
  WEBINT -->|Final acceptance| F
```

## Authority and data flow

- The coordinator performs a bounded inventory, then **defaults to small useful shards** for repeatable work (documents, generation, revisions, batch QA, independent tests). A shard has a fixed input subset, exclusive output/receipt, meaningful work and independent retry. It avoids giant per-author/project jobs, but also avoids trivial or speculative empty tasks whose handoffs dominate. Source-shard completion should release its own downstream generation/review rather than waiting for unrelated authors or files.
- The coordinator owns the **initial planned graph**: stable task identities, logical Pool names, true prerequisite edges, resource/exclusive-write boundaries, source contracts, exact acceptance/test gates and legitimate rework branches. Calling the Skill for orchestration creates the Run after initial source checks and registration, without a separate approval gate. An already open same-goal Run is inspected first, rather than silently duplicated. A Pool is a fairness grouping, never a hard concurrency reservation or a Wave-wide gate.
- Local initial task definitions and PLAN hashes live on the authorized host outside target Git worktrees; the live SQLite graph can be atomically amended for unclaimed tasks. The complete task file is passed once to Codex Loop `collab create`; SQLite owns current ready/claimed/completed task state and dispatch fairness, Git owns code history. A completed task's verified own commit range can be safely materialized into another worker's isolated checkout before it begins downstream work.
- After its first successful Local claim, the Chat queries the existing host-local SQLite `workers` registrations read-only. Its order within that Run determines `<RepoDirectory>-worker<N>`; the assistant prints this name immediately in user-visible chat text. Worker naming needs no extra registry or individualized launcher; Codex Loop registers stable worker ownership in its existing SQLite store. A Chat keeps the name on later claims and resumption.
- Every active Chat retains its own lifecycle `task_id` and worktree while automatically claiming further Ready tasks. An immediately Ready next task is returned by `collab finish`; otherwise the same Chat uses bounded `collab claim --wait` foreground calls (default check interval 30 seconds; separate idle heartbeat; no claimed Slot or lease renewal). Scoped MCP hooks can observe tool activity and urgent inbox/control messages before each tool call, rate-limiting SQLite writes to once per 60 seconds when installed; otherwise workers report explicit heartbeats. Stale leases can be reclaimed after 600 seconds without observable tool activity, with worker-owned Worktree isolation, recovery evidence and owner-generation fencing; resume from the old worker's verified Git/file state without requiring checkpoints; ignored files and uncertain external effects still need review; closed Chats are not awakened; a pool with Ready work receives priority at the **next active claim** and may borrow all idle capacity without limits.
- Final integration starts only after every required task and tracked delegation is resolved, not after an arbitrary Wave. Commit deltas are integrated in DAG order, avoiding duplicated imported prerequisites. A post-integration serial chain belongs to the elected Integrator in the same Chat until its real gates complete.
- Web lacks shared host-local SQLite and retains frozen physical waves, Drive-backed verified handoffs, and an explicit Integration lane. A Web lane must not claim Local DAG scheduling capabilities. Authorization never transfers between Chats; failure recovery preserves previous worktrees and never blindly replays ambiguous writes.
