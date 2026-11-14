# Platform Blueprint Review — Decision Register

**Status:** Working review record; not an ADR register. **Recorded:** 13 November 2026.

## Meaning of status

- **Verified:** requirement or fact evidenced by authoritative documents or published contracts.
- **Finding:** supported review observation, not an architectural decision.
- **Proposed:** recommended direction awaiting a distinct design decision.
- **Review approved:** agreed scope or direction for investigation, not an ADR amendment.
- **Adopted:** incorporated through the applicable governing process; record the accepted reference when this occurs.

## Approved review directions

| ID | Direction | Status | Consequence |
|---|---|---|---|
| R-01 | Reuse existing capabilities and maintain independent bounded ownership | Review approved | No theoretical rewrites or extra repositories |
| R-02 | Resolve declarative configuration, organisation and IAM boundaries through public contracts | Review approved | Begin targeted review of Configuration, Identity, Authorisation and Profile |
| R-03 | Treat libraries, presentation, integration packages and compositions as distinct implementation classifications | Review approved | Avoid making every catalogue entry a Nuxt Layer |
| R-04 | Make External Application Integrations a named workstream | Review approved | Assess configurable connections, actions, mappings, webhook and synchronisation lifecycle; no individual connectors designed yet |
| R-05 | Use a multi-channel retailer as a cross-capability reference scenario | Review approved | Test Product, Inventory, Order, Scheduler, Workflow, MQ, Integrations and reporting boundaries conceptually |
| R-06 | Keep the review in the documentation-only `nuxt-layers/architecture-blueprint-revue` workspace | Review approved | Governing `architecture-rules` stays unchanged until formal acceptance |

## Unresolved architectural decisions

| ID | Question | Preliminary direction | Governing authority / action |
|---|---|---|---|
| D-01 | Who owns runtime business definitions? | Generic configuration mechanism shared; definitions and enforcement remain domain-owned | Review Configuration/Profile/IAM contracts; ADR-0001, 0002, 0012 |
| D-02 | Is `organisation-model` an independent capability? | Prefer archetypes unless an independently governed lifecycle is demonstrated | Preserve Identity and Authorisation ownership; ADR-0016 |
| D-03 | Where do Process, Task and Workflow end? | Separate human work, business outcomes and technical execution | Review public Workflow/Scheduler/MQ contracts; ADR-0012, 0015 |
| D-04 | What does Integrations own? | Connection records, mapping and synchronisation coordination; domain data stays with its owner | Review any published Integrations contract and propose portable interfaces |
| D-05 | Who handles external secrets? | Host secret management stores secrets; Integrations handles scoped references and lifecycle | Security and operational design review required |
| D-06 | Should 50 lines remain a strict composition budget? | Do not relax without evidence and formal amendment | ADR-0012 currently mandates the ceiling |
| D-07 | How should machine-readable discovery work? | Derive from public contracts without creating competing authority | Capability Standard §4 requires separate schema approval |
| D-08 | Which open-source components should be selected? | Evaluate before bespoke implementation | Verify exact licences, commercial rights, maintenance and portability |
| D-09 | Which candidates need their own repositories? | Decide only after ownership, interface, reuse and operational justification | ADR-0001, ADR-0016 |

## Change control

Review approval does not authorise changing implementations, accepted ADRs, standards or capability contracts. Record formally adopted changes here only after their governing PRs are accepted. No new connector, capability or infrastructure implementation is authorised by this register.

## Task 2 — Review findings awaiting architectural decisions (13 November 2026)

| ID | Recommendation or question | Status | Required next action |
|---|---|---|---|
| D-01a | Use capability-owned schemas and enforcement; reuse Configuration's scoped, governed-value machinery where its public contract fits | Proposed; evidence-backed, not formally adopted | Specify schema registration, storage authority, versioning and runtime activation before approval |
| D-01b | Keep Profile's `locale` and `zoneinfo` authoritative; do not create competing personal preferences in Configuration | Verified existing ownership; proposed integration convention | Examine `iam-integration/CONTRACT.md` for current resolution behaviour |
| D-02a | Prefer organisational archetypes over an independent `organisation-model` capability unless distinct owned state and lifecycle are demonstrated | Proposed | Define candidate records and lifecycle; check against Identity's groups and Authorisation's permissions |
| D-02b | Reporting relationships do not confer permissions, groups or resource ownership | Verified governing boundary | Preserve in any future archetype design |
| D-01c | Resolve Configuration/Identity/Authorisation ADR-0012 composition gaps as targeted integration work, not broad rewrites | Finding | Separate design and measured acceptance review; no implementation now |

Review approval of this document does not amend the governing architecture.

## Task 3 — Requested architectural direction (13 November 2026)

| ID | Proposed decision | Status | Condition / outstanding point |
|---|---|---|---|
| D-01d | Profile remains canonical for a person's `locale`/`zoneinfo`; Configuration provides organisational display defaults and Clock validates/formats | Proposed, grounded in existing contracts | No duplicate authoritative account values |
| D-01e | Effective *ordinary, non-locked* display preference: authorised Profile value if present, otherwise Configuration group/ancestor/tenant/platform/deployment/default hierarchy | Proposed | Define absence versus provider failure; preserve authorised group/tenant context |
| D-01f | Resolve once in request-scoped server context where practical; hydrate with the same values and clear on viewer/group/preference changes | Proposed; aligns with performance review | No public cache or exposure of other viewers' preferences |
| D-01g | Interaction between locked deployment settings and personal Profile preferences | **Open** | Specify policy before committing to a universal precedence rule |
| D-01h | Public resolver location and interface | **Open** | Prefer existing application/IAM composition ports; require contract reconciliation before choosing |
| D-02c | Defer a standalone `organisation-model` capability unless independent state, governance and lifecycle are demonstrated | Proposed | Test archetypes and typed reporting against company, club and retailer cases; no new repository authorised |
| D-02d | Identity groups/memberships and Authorisation decisions cannot be derived as effective rights from reporting relationships | Verified governing invariant | Preserve in archetype acceptance criteria |

These entries record design proposals for review. They do not amend accepted ADRs, issue implementation instructions or make the proposed resolver an existing public API.

## Consolidated personal, membership, legal-governance and publishing direction (13 November 2026)

The synthesis and proposed acceptance cases are in [`proposals/membership-governance-publication.md`](proposals/membership-governance-publication.md). These are **review-agreed design directions**, not yet accepted normative requirements or verified implementation contracts.

| ID | Direction | Status / qualification |
|---|---|---|
| G-01 | Personal groups remain private; explicit resource-level sharing never adds other members to the person's system-managed group | Review agreed; preserves existing Identity invariant |
| G-02 | Membership is the Identity-owned person/group relationship; other owners may use its identifier as context, not duplicate it | Review agreed; preferences by membership require a Profile contract extension |
| G-03 | Business records created on behalf of a group ordinarily remain under that group's designated business control after departure | Proposed default, subject to domain rules, contracts, intellectual-property and applicable law |
| G-04 | Organisation-supplied legal/policy documents and agreements can be recorded with versions, sources, evidence, jurisdiction/activity context and withdrawal | Review agreed direction; validity/applicability cannot be asserted automatically |
| G-05 | Privacy and legal obligations can depend on a membership but also apply independently to customers/visitors and cross-border activities | Review agreed boundary; Privacy/Legal Compliance public contracts not verified |
| G-06 | Optional application-wide community group for eligible registered users; public audiences are separate: application public and world public | Review agreed; anonymous world audience is not an Identity group |
| G-07 | Visibility, licence, editorial authority, group/resource ownership and creator attribution are separate | Review agreed; publishing does not itself transfer ownership |
| G-08 | Reusable publication supports authorisation, reviewed contributions, immutable revisions, pinned reuse, withdrawal and lawful attribution/deletion | Proposed capability integration; Publisher contract review required |
| G-09 | Group-context preference order includes member override then organisational default then general Profile fallback, with locked/mandatory policies evaluated separately | Proposed; supersedes simpler Profile-first proposal in group context |
| G-10 | Do not create new community/public ownership layers, membership databases or automatic legal-rule engines without proven independent authority | Review agreed constraint |

**Outstanding before ADR adoption:** decide member preference persistence and lifecycle, the legal-context representation, Publisher's actual public interfaces, licensing/rights enforcement, publication dependency handling, and cross-jurisdiction legal policy provenance.

## Task 4 — Capability Discovery and Registry decisions (14 November 2026)

Full review: [`reviews/capability-discovery-registry.md`](reviews/capability-discovery-registry.md).

| ID | Direction | Status |
|---|---|---|
| CD-01 | Do not create a new standalone registry capability or persistent database on present evidence | Proposed for approval |
| CD-02 | Keep `capability.json` for Nuxt layers, and `CONTRACT.md`/exports for other implementation forms; no universal manifest without approval | Existing governing rule; proposed continuation |
| CD-03 | Generate descriptive application-level inventory from selected, verified metadata at build/composition time | Proposed; not an existing platform API |
| CD-04 | Separate package installation, composed providers, runtime operational state, enablement, user permission and public availability | Proposed acceptance model |
| CD-05 | Track contract dependencies and Nuxt extension edges separately; validate one combined acyclic graph | Verified governing rule (ADR-0016) |
| CD-06 | A discovery record never grants permissions, installs executable code, or overrides owner contracts | Proposed enforcement safeguard |
| CD-07 | Runtime feature lists, registered document types and permissions stay with the owning capability and Authorisation | Proposed application integration discipline |
| CD-08 | Approve only the minimal additional metadata demonstrated necessary by consumer tests | Open; needs follow-on specification if required |

The next review workstream is External Application Integrations. These discovery entries do not alter `architecture-rules` or mandate new packages.

## Task 5 — External Application Integrations (14 November 2026)

Full proposal: [`reviews/external-application-integrations.md`](reviews/external-application-integrations.md). These entries are proposed design directions, **not accepted ADRs, APIs or implementation evidence**.

| ID | Direction / unresolved choice | Status |
|---|---|---|
| EI-01 | `integrations` is the existing Platform classification; consider connection records and sync coordination as independently owned lifecycle | Proposed; root contract unavailable |
| EI-02 | Keep each external provider as an optional adapter behind documented public interfaces, not a compulsory standalone Nuxt layer | Proposed |
| EI-03 | Identity owns membership, Authorisation decides access, domains own business data; connectors never mutate another capability's persistence directly | Governing boundary; integration proposal |
| EI-04 | Store connection metadata and opaque credential references; decide secure credential-owning infrastructure and token revocation mechanisms separately | Proposed; detailed security review required |
| EI-05 | Separate installed, configured, connected, authorised, operational and in-sync status | Proposed |
| EI-06 | Use authoritative owner outboxes and validated idempotent inbound commands; preserve retry, provenance and reconciliation | Proposed |
| EI-07 | Respect organisation-specific policy, privacy, provider terms and all applicable jurisdictional constraints, without asserting automatic legal validity | Proposed |
| EI-08 | Define boundary between connection-level external-ID/checkpoint state and domain-owned mapping/business meaning | **Open** |
| EI-09 | Specify smallest portable connection/provider contract, security assurance and operational failure model | **Open** |
| EI-10 | Evaluate OSS libraries and provider client licences, maintenance and suitability before dependency selection | **Open** |

Next review: **Workflow / Process / Task / Scheduler / Message Queue / Temporal Gateway separation**, including the role of integration synchronisation and process orchestration.

## Task 6 — Workflow, Process, Task and Temporal Gateway (14 November 2026)

Complete review: [`reviews/workflow-process-task-execution.md`](reviews/workflow-process-task-execution.md). No normative or implementation change is authorised.

| ID | Direction / decision | Status |
|---|---|---|
| WE-01 | Preserve existing catalogue ownership for Scheduler, Message Queue, Workflow and ADR-0015 Temporal Gateway | Verified classification; proposed continuation |
| WE-02 | Distinguish human Task, Scheduler handler and Temporal activity | Proposed; prevents duplicate task registries |
| WE-03 | Process needs demonstrable independent versioned definitions and outcomes to justify a new capability | Open candidate classification |
| WE-04 | Workflow's existing catalogue remit is approval, review and sign-off; broader orchestration is not assumed | Verified starting remit; extension requires decision |
| WE-05 | Gateway centralises shared Temporal SDK/worker connectivity, never business execution decisions or persistence | ADR-0015 accepted constraint |
| WE-06 | MQ carries owners' published outbox events; Temporal signals do not silently substitute for cross-capability event transport | Proposed integration rule |
| WE-07 | Domain owners remain authoritative for outcome, authorisation, approval validation and compensating changes | Governing responsibility applied to scenario |
| WE-08 | Explicit schedule, worker retry, idempotency, replay, cancellation and failure semantics | Open contract design |
| WE-09 | Reconcile ADR-0012 scheduled handler shape with Scheduler/Temporal without double scheduling | Open; root contracts unavailable |
| WE-10 | Defer new `process` and `task` repositories until distinct ownership and human-work lifecycle are proven | Proposed |
| WE-11 | Preserve binding of approvals to action, resource version/digest and current policy; confirm evidence ownership | Open public-contract review |
| WE-12 | Determine Temporal operational persistence, recovery and deployment independently of business data and image/file object storage | Open operational design |

Follow-on review: Multi-channel commerce and warehouse domain ownership (Product, Inventory, Pricing, Order, Warehouse) under the execution and integrations constraints.

## Task 7 — Organisation candidate reduction (14 November 2026)

Full proposal: [`reviews/organisation-candidate-consolidation.md`](reviews/organisation-candidate-consolidation.md).

| ID | Recommendation / question | Status |
|---|---|---|
| OC-01 | Default `organisation`, `business`, `company`, `club`, `committee`, `department`, `office` to archetype/composition examples rather than independent capability candidates | Proposed for review approval |
| OC-02 | Keep one Identity group/hierarchy/membership model; archetypes only reference its public identifiers | Verified governing boundary |
| OC-03 | Keep specific independently governed records (company filings, committee resolutions, club subscriptions, facilities) with the appropriate demonstrated domain owner | Proposed separation; individual domains not approved |
| OC-04 | Treat scheme/paid `membership` as potentially distinct from Identity membership, not another identity relationship | Verified catalogue distinction; independent lifecycle assessment pending |
| OC-05 | Do not promote `organisation-model` to a new repository without evidence for versioned archetypes and governance lifecycle | Proposed continuation of D-02a/D-02c |
| OC-06 | On approval, submit a separate narrowly scoped governing catalogue clarification PR, retaining valid terminology and excluding changes to Identity/Authorisation contracts | **Pending explicit approval** |

Next blueprint workstream remains Multi-channel Commerce and Warehouse Operations.
