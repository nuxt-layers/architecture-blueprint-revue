# Review — Workflow, Process, Task, Scheduler, Message Queue and Temporal Gateway

**Status:** Blueprint proposal, not a normative amendment or an implemented design.  
**Reviewed:** 14 November 2026.  
**Governing revision:** `nuxt-layers/architecture-rules` `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`.  
**Authority:** `docs/catalogue.md`, `docs/architecture.md`, `docs/capability-standard.md`, ADR-0012, ADR-0015, ADR-0016, ADR-0002 and ADR-0008.  
**External root contracts consulted:** Identity, Publisher and Clock. Root `CONTRACT.md` could not be retrieved for `scheduler`, `workflow`, `message-queue`, `temporal-gateway`, `tasks` or `notification`. This indicates an evidence gap, not proof that these projects are unimplemented. No private implementation inspected.

## 1. Purpose

Clarify which component owns *business state*, *assigned human work*, *approval processes*, *scheduled execution*, *event delivery* and *Temporal infrastructure* before specifying or building overlapping APIs. Test the model against organisational membership departure, publisher approvals, integration reconciliation, multi-channel order fulfilment and warehouse work.

## 2. Governing classification versus candidates

| Name | Established / candidate | Owned responsibility / exclusion |
|---|---|---|
| `scheduler` | Established Platform | Run capabilities' declared scheduled task handlers on time, for the host; not ownership of their business meaning |
| `message-queue` | Established Platform | Transport events from each owner capability's durable outbox to consumers; not source business events or calendar events |
| `workflow` | Established Platform | User-defined review, sign-off and approval routes by type and site; fills published approval ports; *not automatically every business process* |
| `temporal-gateway` | ADR-0015 infrastructure integration library | Centralises Temporal SDK/client/worker/schedule connectivity and lifecycle; no business workflows, schedules, event transport or domain persistence |
| `process` | Candidate Platform | Versioned business process definitions and outcomes, if independent owned lifecycle justifies it; execution may use Workflow/Scheduler |
| `task` | Candidate Platform | Individual assigned work, progress and due dates, not technical scheduler tasks |
| `notifications` | Established Platform | Informs people through selected channels; delivery may respond to events or workflow requests |
| `clock` | Established Foundation | Date/time representation, time zones, arithmetic and validation, not business scheduling or workflow state |
| Domain capability, e.g. Order/Warehouse | Independent domain owner | Business facts, inventory, fulfilment decisions, and the authoritative outcome. May consume execution and approval services |

The catalogue also consolidates infrastructure `scheduling-service` and background `job-service` into Scheduler, `workflow-service` into Workflow, `event-bus-service` into Message Queue, and `todo-manager` as a potential Task use case. A named candidate does not authorise a separate repository.

## 3. Three different meanings of task

Do not reuse one ambiguous `Task` identity for:

1. **Assigned work item:** an employee's picking task, member's review assignment or due date; potential `task` business capability with ownership, assignee and progress.
2. **Scheduled handler:** an owner-supplied maintenance/outbox function, using ADR-0012 `{ name, every, run }`; Scheduler decides **when to invoke it**, not its business semantics.
3. **Temporal activity/worker job:** a technical unit of execution owned by the gateway/provider's infrastructure; not a domain Task or a new `tasks` platform capability.

These three forms may refer to the same overall business operation, but must retain different identifiers, lifecycles, access rules and audit records. A `scheduler` task may run without any human Task capability installed.

## 4. Proposed reference interaction

Example: fulfil an online order:

1. Order validates an incoming order, commits its own state and transactional outbox event.
2. Message Queue delivers the event to interested consumers at least once; it never becomes the order source of truth.
3. A domain-owned fulfilment operation determines stock reservation and permitted next steps. If process definitions are separately required, Process may supply a *versioned definition*, never silently re-own Order state.
4. Task may create and track an authorised picking assignment, tied to the group's active membership and the owning warehouse record.
5. Workflow may route a human sign-off/exception approval. The domain owner checks the returned decision and its binding to the *specific operation/version*, not merely a generic success flag.
6. Scheduler may arrange a deadline/reminder or dispatch recurring retries from an owner-published handler.
7. Scheduler/Workflow may use Temporal Gateway for durable timers, execution, retries and worker lifecycle behind their own public contracts.
8. Notifications informs the assignee/approver; it does not create the work or decide authorisation.
9. The domain owners record completed fulfilment, inventory movement and audit outcome.

**Do not interpret this example as proof of any current cross-repository integration**; these are proposed compositional responsibilities.

## 5. Temporal boundaries and portability

ADR-0015 already forbids the Gateway from controlling business persistence, workflow business policy, message transport or scheduled-task business ownership.

- **Gateway exposes:** validated Temporal connection/configuration, client and worker lifecycle, scheduling/execution SDK adaptation, technical health and diagnostics within its independent Node contract, if implemented.
- **Scheduler exposes:** provider-neutral registration/execution of scheduled owner handlers, schedule policy, missed-run/duplicate-run handling and status; it may adapt to Temporal or another supported executor.
- **Workflow exposes:** approved process route definitions, instances, approval evidence, current state, actor/permission constraints, deadlines and cancellation for its *established review/sign-off scope*. It may use durable Temporal execution.
- **Message Queue exposes:** transport of facts emitted by capability owners through their durable outboxes, with delivery contracts, acknowledgements, redelivery, ordering limitations and consumer checkpoints. Temporal signals must not silently be treated as a substitute for cross-capability message transport.
- **Domain owner exposes:** authoritative events and command handlers; decides transitions, compensation and transaction boundaries.
- A common technical gateway must not force a particular Temporal SDK type into consumer public contracts. Consuming capabilities retain their own error/status vocabulary and provider-neutral interfaces.
- Decide separately whether Scheduler and Workflow share a technical gateway package or merely an external Temporal deployment. A shared dependency does not create a shared business registry or shared persistence.
- A workflow execution history in Temporal is operational evidence, **not** a replacement for authoritative approvals, membership state, order records or data retention decisions.

**Hosting:** Hetzner VPS + Coolify is the deployment baseline. Temporal persistence, workers and any queue persistence require their own appropriate operational storage and recovery strategy; Hetzner S3-compatible Object Storage is reserved for application images/files, not infrastructure persistence or backups.

## 6. Existing contract evidence

- **ADR-0012:** Nuxt capabilities expose `tasks: { name, every, run }[]`; host has one scheduler. Owners supply handlers and durable event outboxes; transport may run them/deliver events but cannot re-own source logic.
- **Identity `CONTRACT.md`:** `getIdentityTasks()` already returns `identity:maintenance` every five minutes and `identity:outbox` every minute, with no automatic schedule on import; missing event publisher refuses delivery rather than acknowledging it.
- **Publisher `CONTRACT.md`:** `composePublisher()` returns maintenance/relay tasks, and uses an optional `approval` port for type-specific publisher transitions; approvals bind to a request digest. Publisher's own lifecycle retains ownership of published documents.
- **Clock `CONTRACT.md`:** owns canonical temporal semantics, with no scheduler job registry, persistence, HTTP endpoints or database schema.
- No Scheduler/Workflow/Message Queue/Temporal Gateway root contract was retrievable, so implementations, API shape, delivery guarantees and mutual compatibility remain **unverified**.

## 7. Strong consistency, failure and security requirements

- Owner data and its outbox must commit atomically. Transport is normally at least once, so handlers need idempotency; ordering and deduplication must be explicit per stream/key.
- Never hold a domain transaction open across a Temporal/queue remote invocation. If the execution backend is unavailable, retain recoverable intent in the owner/outbox or the responsible capability's state as appropriate.
- A timer trigger, event delivery, task assignment or completed workflow activity cannot bypass Authorisation, tenant/group separation, approval assurance or domain invariants.
- Approval must be bound to the action, target resource/version, requester and current policy context. Revalidate critical preconditions at execution time.
- Human work assignments must be reassigned or invalidated when membership/authorisation ends, according to their owner; completion of a worker activity is not proof of an authorised business outcome.
- Long-running workflows require versioned definitions and safe upgrade/cancellation/compensation policy. Business compensations remain domain-owned; avoid pretending a distributed transaction is atomic.
- Scheduler must state time-zone and DST semantics using Clock's public time service where appropriate. No ambiguous silent local-time interpretation.
- Execution history, messages and notifications must not disclose personal information or credentials beyond approved bounded purposes; comply with Privacy, retention and data-region requirements.
- Missing mandatory backend/ports fail closed; optional integrations have explicit absence behaviour. Avoid double-triggering when both host and Temporal support schedules.

## 8. Acceptance scenarios

| Scenario | Expected proof |
|---|---|
| Identity's maintenance tasks register once | One host scheduler, no duplicate work from Nuxt imports or competing Temporal schedules |
| Publisher publication at a future time | Scheduler triggers owner handler; Publisher remains authoritative over document lifecycle and permissions |
| Publisher high-risk approval | Workflow binds approval to document digest/version, retains evidence; Publisher independently checks before publishing |
| Warehouse picking assignment | A human Task has authorised assignee, progress, due date and membership lifecycle; it is distinct from Temporal activity |
| Membership departure during work | Assignment/review access revoked or reassigned; business record and lawful audit evidence survive according to owner policy |
| Outbox consumer outage and replay | Message redelivered, source write is not lost, duplicate effects prevented, recovery and lag visible |
| Temporal unavailable | Execution surfaces unavailable, no false completion, recoverable intents preserved, non-Temporal capabilities can follow documented absence modes |
| Order cancellation after dispatch | Domain determines compensation; Workflow orchestrates only approved steps |
| Integration sync schedule | Integrations owns its checkpoint and mapping responsibilities; Scheduler/Temporal run technical work, MQ transports owner events |
| Multi-region deadline | Time-zone/DST and retention semantics explicit, without confusing display preferences with legal deadlines |

## 9. Decisions and separate contract review needed

| ID | Proposed decision / question | Status |
|---|---|---|
| WE-01 | Keep Scheduler, MQ, Workflow and Gateway in their existing established classifications, no automatic reclassification due to Temporal | Aligned with governing catalogue/ADR-0015 |
| WE-02 | Reserve `Task` for human-assigned work; distinguish scheduler handlers and Temporal activities | Proposed naming boundary; `task` remains candidate |
| WE-03 | Treat `Process` as versioned business process definitions/outcomes only if distinct state and lifecycle are demonstrated | Proposed; candidate, no repository approval |
| WE-04 | Keep Workflow's established approval/review/sign-off scope; expansion to general process orchestration needs separate justification | Proposed scope discipline |
| WE-05 | Gateway is a pure Node infrastructure adapter for Temporal, with no business state/policy; avoid duplicate SDK adapters in Scheduler/Workflow/MQ | Existing ADR-0015 boundary plus proposed integration convention |
| WE-06 | MQ transports owned outbox events; Temporal signals/queues do not automatically replace public event delivery | Proposed interface constraint; MQ contract missing |
| WE-07 | Domain owner retains business outcomes, compensated transitions, and checks permissions/approvals at execution | Governing ownership applied to this scenario |
| WE-08 | Ensure schedule/run identities, dedupe, retries, time semantics, cancellation and provider-failure contracts are explicit before implementation | Open for per-capability design |
| WE-09 | Confirm the exact integration of ADR-0012 `tasks` with a single Scheduler and Temporal schedules; do not run the same handler twice | Open; requires actual Scheduler and Gateway contracts |
| WE-10 | Decide whether `Process` and `Task` warrant independent persistence and public contract only after representative human-work cases | Open; avoid premature repositories |
| WE-11 | Determine Workflow approvals versus domain approvals and the audit evidence owner, respecting assurance and binding | Open, subject to published contract review |
| WE-12 | Document operational recovery, Temporal persistence/worker deploy, version compatibility and independent test evidence | Open operational design; not a Blueprint implementation mandate |

## 10. Recommendation and next workstream

**Decision proposed:** preserve the present ownership classifications, prohibit competing Temporal-backed execution APIs, and defer standalone Process/Task development until independent state/lifecycles are demonstrated. Avoid claiming Workflow, MQ or Scheduler are implementation-ready without a published root `CONTRACT.md`.

**Next:** assess the multi-channel commerce reference case (Product, Inventory, Order, Pricing and Warehouse) or follow the review plan's next listed domain workstream, using these execution boundaries as constraints. Any normative amendment or code change requires a separate explicitly approved change.
