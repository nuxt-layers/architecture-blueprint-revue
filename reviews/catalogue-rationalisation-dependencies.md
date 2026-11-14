# Review — Capability Catalogue Rationalisation and Dependency Gates

**Status:** Blueprint proposal; no change to the governing Capability Catalogue or implementation.  
**Reviewed:** 14 November 2026.  
**Governing revision:** `nuxt-layers/architecture-rules` `master` `2eea557fa3acbf9412fea065d2a3cdda42a764fc` (includes organisation-archetype PR #34).  
**Review revision:** blueprint `master` `6349502af62a9d64c7041fca7913326c36cf79fb` (includes PR #9).  
**Authority:** Capability Catalogue, Capability Standard, Architecture, ADR-0001, ADR-0002, ADR-0008, ADR-0012, ADR-0015, ADR-0016, plus earlier blueprint Tasks 2–8. This is a classification and *candidate dependency* review; it is **not** verification of actual `capability.json` or `CONTRACT.md` compatibility for candidate packages.

## 1. Classification method

Distinguish four questions: (1) **Responsibility:** who owns durable records, lifecycle and invariants? (2) **Implementation kind:** capability, stateless library, infrastructure integration library, external connector, archetype, presentation or application composition? (3) **Dependency type:** required public contract, optional contract, runtime event, declarative specialisation or direct Nuxt `extends`? (4) **Delivery state:** documented/implemented/composed/operational/authorised, independently verified.

**Rule for promoting a candidate:** a distinct resource and business lifecycle must justify ownership, supported by a complete public consumer contract, failure model and independent acceptance example. Being named in a list, needing an admin screen, or having external SDK calls is insufficient.

**Disposition vocabulary:** **Consolidate** = existing owner/composition already covers the generic requirement; **Retain candidate** = credible independent responsibility, not repository approval; **Conditional** = first prove independent state/lifecycle; **Library** = non-domain reusable function with no independent business records; **Composition/presentation** = assembled UI or capabilities with no domain persistence.

## 2. Foundation, platform, shared and presentation candidates

| Catalogue entry | Recommended disposition | Boundary and reason |
|---|---|---|
| `architecture-rules` | Retain governance classification | Normative source, not runtime capability; no duplication with an application registry |
| `infrastructure` | **Composition/operations** by default; conditional platform only with separately justified owned control state | Hetzner/Coolify resources, deployment and provider lifecycles belong to operators/host; avoid a universal app-level persistence or privileged control plane |
| `reviews` | **Retain candidate** | Independent review, verification, moderation and reputation state; Trustpilot is optional Integrations adapter |
| `template-engine` | **Library candidate; confirmed catalogue classification** | Secure deterministic parsing, binding and rendering from explicitly supplied data; distinct from reusable model templates and their owning model/domain definitions. No arbitrary execution, model persistence, document lifecycle or business-data fetching |
| `organisation-model` | **Conditional archetype management**, not a default capability | Actual versioned archetype definitions, activation and provenance need evidence; Identity/Authorisation remain authoritative |
| `audit` | **Retain candidate** | Tamper-evident, accountable business/security evidence and retention, separate from operational logs |
| `caching` | **Conditional foundation utility/composition** | Reuse host/provider caching and owner invalidation first; only independent cache policies/ports justify package |
| `logging` | **Infrastructure/service integration by default** | Structured technical logging and sinks; not business audit records |
| `observability` | **Infrastructure/service integration by default** | Telemetry collection, metrics, traces and health; possible composed operational UI, no domain ownership by default |
| `uuidv7-generator` | **Library or runtime primitive**; do not create capability | Identifier generation is stateless; avoid another authority for IDs |
| `api-gateway` | **Host/infrastructure composition by default** | Reverse proxy and boundary routing are not a replacement for server-side Authorisation; only separately governed API product lifecycle could justify stateful domain |
| `application-compositions` | **Composition artefacts** | Templates select and wire public capability contracts; no new core domain owner |
| `company-console` | **Presentation** | Authorised admin view over public contracts, no company records |
| `user` | **Presentation; consolidate** | Identity, Profile, Authentication and Authorisation own the underlying state |
| `document` | **Consolidate generic case into Publisher** | Publisher manages document lifecycles where its contract supports them. A structured model is not inherently a document; documents may embed/refer to independently governed model revisions |
| `search` | **Retain candidate** | Authorised cross-domain index/query/relevance with stale-projection and erasure safeguards, never source facts |
| `location` | **Conditional platform or library**, subject to actual geospatial lifecycle | Coordinates/parsing/geocoding alone may be utilities; owned named places, zones or routes may justify durable domain state |
| `reporting` | **Retain candidate** | Report definitions, scheduled outputs and authorised aggregates; never source commercial transactions |
| `process` | **Retain conceptual domain candidate; ownership open** | Business activities, objectives and outcomes; distinguish process definitions from procedure methods and Workflow execution. Require independent lifecycle evidence before repository creation |
| `project` | **Retain domain candidate** | Group-owned undertaking with goals, milestones, resource references, budgets, accountable roles, progress and outcomes; no duplicate ownership of referenced resources or Identity memberships |
| `task` | **Retain domain candidate** | Human/organisational work item, assignment, progress, deadlines, evidence and outcomes; distinct from occurred business events, Scheduler handlers and Temporal activities |
| `booking` | **Retain domain candidate** | Reservation, capacity and allocation invariants, not Calendar/Clock/Scheduler |
 
## 3. Organisational and activity candidates

**Already resolved by governing PR #34:** `organisation`, `business`, `company`, `club`, `committee`, `department`, `office` are **archetypes/compositions by default**, not seven independent capability candidates. Their specialist domains still require separate evidence.

| Candidate | Recommended disposition | Independent records / exclusion |
|---|---|---|
| `membership` | **Conditional domain** | Paid/scheme contracts, terms, renewals or benefits *only*; Identity owns ordinary person-to-group memberships |
| `events` | **Retain domain candidate** | One-off and recurring planned activities (concerts, classes, voyages), versioned definitions and independently identified occurrences with exceptions/cancellations; not messages, domain facts, bookings or resource ownership |
| `course` | **Retain domain candidate** | Curriculum/offering versions and learning outcomes; not a presentation page |
| `enrolment` | **Conditional domain** | Learner allocation, progression and participation if its lifecycle cannot belong to Course/Training |
| `training` | **Conditional domain** | Completion, competency assessment and certification evidence where independently regulated; otherwise Course composition |
| `gaming` | **Conditional domain** | Specific game session, ranking and participation state; not universal account/session state |
| `workshop` | **Consolidate by default** | Event/Course specialised type; independent materials/participation rules must be demonstrated |
| `blog` | **Consolidate** | Publisher document types, listings, feeds and optional presentation unless materially different business lifecycle |
| `job` | **Disambiguate** | Job vacancy/position can be an employment domain; technical background job belongs to Scheduler, not a competing `job` capability |

## 4. Commerce and operations candidates

| Candidate | Recommended disposition | Independent source of truth |
|---|---|---|
| `product` | **Retain domain candidate** | Product and variant master |
| `pricing` | **Retain domain candidate** | Price lists, promotional rules and quotations; an agreed order-price snapshot belongs to Order |
| `inventory` | **Retain domain candidate** | Stock movements, reservations, availability and ownership |
| `asset` | **Retain domain candidate** | Fixed/controlled assets, assignment and lifecycle, not saleable inventory |
| `order` | **Retain domain candidate** | Sales order commitments, state and fulfilment obligations |
| `billing` | **Conditional domain** | Charges, accrual periods and billable obligations independent of Order/Invoice |
| `invoice` | **Retain domain candidate** | Issued legal/financial document, numbering, correction and status |
| `payment-gateway` | **Retain integration/domain candidate** | Payment intent/execution, capture/refund status; actual processor adapters optional |
| `accounting` | **Retain domain candidate** | Ledger, immutable journals, periods and reconciliation |
| `subscription` | **Retain domain candidate** | Recurring plan agreements, renewals and entitlement lifecycle |
| `shop` | **Application composition by default** | Retail experience combining Product, Pricing, Inventory, Order etc.; prove a distinct state lifecycle before new domain |
| `marketplace` | **Conditional domain** | True multi-seller management, onboarding and marketplace commerce; external Amazon/eBay *seller* accounts belong to Integrations |
| `warehouse` | **Retain domain candidate** | Physical receiving, bin placement, picking, packing and work evidence, distinct from Inventory stock ledger |
| `logistics` | **Retain domain candidate** | Shipment, carrier, dispatch and delivery tracking |

**Financial caution:** `billing`, `invoice`, `accounting`, `subscription` and `payment-gateway` have adjacent but not interchangeable lifecycles. Do **not** collapse them into Order or a general `commerce` capability merely because a simple application initially co-locates them.

## 5. Names already consolidated in the governing catalogue

Existing authoritative consolidations remain effective; no reopen without a distinct lifecycle: `image` → Media, `storage-service` → Storage, `user-service`/`group-service` → Identity, `tenancy` → Identity, `blog-service` → Publisher, `integration-adapters` → Integrations, `policy-map` → Legal Compliance, `doc-search` → Search candidate, `scheduling-service`/background `job-service` → Scheduler, `workflow-service` → Workflow, `event-bus-service`/`events-queue` → Message Queue, `notification-service` → Notifications. `todo-manager` is a potential human Task use case, not a second task platform.

**Do not change the established Capability table in this review.** An established capability's public contract has stronger authority than a new classification suggestion.

## 6. Dependency map — candidate *conceptual* requirements, not published `requires` edges

The following rows state logical services or integration relationships to validate **after** the relevant root contracts exist. They must not be copied as mandatory manifest dependencies without proving necessity and absence/failure behaviour.

| Consumer / scenario | Potential required owner contract | Optional integration / event / composition | Exclusions and acceptance gate |
|---|---|---|---|
| Business resource actions | Authorisation / verified Identity context via host | Organisational Profile/Configuration policies as appropriate | No generic direct runtime dependence on all IAM layers; preserve assured caller and tenant checks |
| Publisher content and approval | Publisher owns documents and outbox | Workflow approval, Scheduler task executor, MQ transport, Notifications | Publisher works in its declared absence modes, with owned state intact |
| Product sales application | Product, Pricing, Order and appropriate Inventory operations *as selected* | Shop presentation, Integrations, Publisher/Media for listing content | App composition selects dependencies; do not force all providers on Product |
| Stock reservation | Inventory's public reservation contract | Order requests and consumes outcomes; MQ relays owned events | Inventory never imports Order internals; no cross-database transaction or duplicate stock ledger |
| Physical picking | Warehouse work operations and validated Inventory interactions where required | Human Task, Scheduler/Workflow approval and Notifications | Warehouse owns pick evidence, not canonical Inventory balances; absence policies explicit |
| External marketplace connection | Integrations' scoped connection/provider ports | Product, Inventory, Order domain commands/events; provider connector | Third-party connection is not a Marketplace domain; owner records never in connector schema |
| Payment capture | Payment Gateway's public port and Authorisation | Order domain state and external processor adapter | Accounting owns ledger, Invoice owns issued document; settlement not an Order fact |
| Business review/analytics | Owner's authorised export/event/query contract | Reporting, Search and Audit as selected | Derived indexes/projections, policy-aware erasure, no data-master drift |
| Human work / project coordination | Task only if approved as independent domain | Project/Process compositions and Workflow approval | Avoid cycles or turning Scheduler jobs into human work |
| Timer / durable execution | Scheduler/Workflow owning public operation | Temporal Gateway infrastructure, Clock time semantics | One scheduler; no second orchestration authority or leaked Temporal SDK |
| Cross-capability event transport | Each owner outbox and Message Queue public contract | Consumers' idempotent handlers | Message Queue owns delivery, not source event facts |
| Public review/feedback | Reviews domain (if approved), Authorisation decisions | Identity and Orders for verified purchase evidence, Integrations/Trustpilot optional | Reviewer identity must not be inferred; evidence and privacy separately governed |

**Graph validation requirement:** a proposed `requires` arrow is directed from consumer to provider, optional arrows record a valid absence mode, and direct Nuxt `extends` edges are separately recorded. Check the **combined** graph for cycles under ADR-0016. Events alone do not create reverse code dependencies; the host connects subscribers through published contracts. The generated application discovery catalogue is descriptive and never a new dependency root or permission authority.

## 7. Foundation readiness — avoid equating reuse with mandatory dependency

**Established and foundational for protected multi-tenant applications:** Identity, Authentication, Authorisation, Profile (where personal data is used), Configuration (where declared settings are used), Clock (where time semantics are needed), and Privacy/Legal Compliance obligations where applicable. These do **not** become compulsory direct dependencies for every isolated library or capability.

**Platform execution interfaces:** Scheduler, Message Queue and Workflow already have ownership boundaries in the catalogue, but a candidate's requirement for any of them must be justified; Temporal Gateway is an ADR-0015 infrastructure library, not a business foundation and not automatically required.

**Operational cross-cutting needs:** Logging, observability, caching, deployment control, secrets, audit and health matter operationally but do not imply standalone Nuxt capabilities. Use host/infra composition and capability-owned factual records first. Audit may require independent tamper-evident persistence when its domain rules are justified.

**Domain development sequencing (proposal):** prove one thin vertical slice, for example Product → Inventory reserve → Order accepted/pending state → owned outbox → authorised Warehouse pick → integration replay. Expand to pricing promotions, multiple sites, multi-channel inventory, invoices and financial settlement only after contracts and failure tests establish the boundaries.

**No central universal runtime service or mandatory `organisation-model` is necessary merely to represent the application's organisation type.** Retain group resource ownership and membership-based authorisation.

## 8. Specific outstanding decisions

| ID | Decision | Proposed disposition |
|---|---|---|
| CR-01 | How aggressively should the catalogue candidate table shrink? | Retain vocabulary; mark consolidated or conditional entries clearly instead of deleting concept names |
| CR-02 | Should `document`, `blog`, `workshop`, `shop` and `user` get independent repositories? | No by default; require evidence of new owned lifecycle |
| CR-03 | Is generic `organisation-model` needed? | Defer pending versioned definition/activation ownership proof |
| CR-04 | Should logging, observability, caching, API gateway and infrastructure each be Nuxt capabilities? | No default: classify as host/infra integration or utility unless justified |
| CR-05 | Which candidates retain credible independent domains? | Review, Audit, Search, Reporting, Booking, Events, major commerce domains; conditional others remain separately scoped |
| CR-06 | Are Process, Task and Project distinct from Workflow? | Yes conceptually: business outcomes, assigned work and group-owned undertakings versus coordination; each domain repository still needs lifecycle/contract evidence |
| CR-07 | Is `marketplace` needed for Amazon/eBay sellers? | No, only for distinct multi-seller platform responsibilities |
| CR-08 | Can all commerce financial candidates be merged? | No automatic merge; preserve distinct legal/accounting/settlement invariants |
| CR-09 | Is a universal dependency graph ready to publish as mandatory? | No; conceptual edges need owner root contracts, compatibility and optionality checks |
| CR-10 | What is the next governing change? | A narrowly scoped catalogue classification clarification *after approval*, not sweeping edits to existing packages |
| CR-11 | Which decisions need ADR rather than catalogue editing? | Changes to ownership invariants, dependency model, general contract rules and security/platform-wide precedence; assess in consolidated ADR-impact pass |

## 9. Model, definition, instance and lifecycle review — additional findings

The following distinctions refine this review based on the discussion of Projects, Tasks, Processes, Procedures, scheduled Events and specialised Modellers. They are **proposed design vocabulary**, not new implemented capabilities or amendments to the governing catalogue.

### Definitions, instances and outcomes

| Concept | Definition or template | Instance / occurrence | Outcome and authoritative owner |
|---|---|---|---|
| Project | Reusable project type or plan where required | Group-owned project with goals, milestones, budgets and resource references | Project records progress/outcomes; specialist resources retain their own state |
| Task | Reusable work instruction or task pattern | Assigned work item with accountable person/group, due date, progress and version | Task owns work completion evidence, not every underlying business result |
| Process | Business activity model and defined intended outcome | A particular process undertaking with its own business context | Domain business owner records the authoritative result |
| Procedure | Approved, versioned repeatable method, steps, required roles/resources and acceptance measures | Execution of a particular procedure revision | Responsible domain records results/evidence; coordination is not ownership |
| Scheduled Event | One-off event definition or recurring series and recurrence rule | Individually identified class, concert or ferry sailing with rescheduling/cancellation overrides | Event owns planned occurrence/status; specialist domains own voyage, course, ticket, attendance or transport facts as applicable |
| Structured model | Typed model schema/metamodel, model definition and semantic revision | Particular model snapshot, view or deployment bound to a use case | Owning domain validates meaning and status; a rendered diagram is not the authoritative fact |

**Events have at least three distinct senses:** (a) scheduled real-world activities and occurrences (the `events` candidate), (b) business facts published by an owner through an outbox and Message Queue, and (c) technical execution/telemetry occurrences. A scheduled event is not a message, nor is a Task an abstraction of an event: tasks record intended work and emit factual events during their lifecycle.

**Time and recurrence:** Event determines its planned occurrences and approved exceptions, Clock supplies time-zone/calendar semantics through public contracts, and Scheduler runs technical handlers when required. A recurring series is not identical to its individual occurrences. Changes to the series must not silently rewrite completed or specifically overridden occurrences.

### Modelling, models, modellers and documents

- **Model:** typed, validated semantic representation with element relationships, immutable revisions and explicit owner. Not necessarily a document and never automatically executable.
- **Metamodel:** rules for the allowed model elements, properties, relationship types and constraints. Different standards may require different compatible model schemas.
- **Modeller:** specialised editor/tool (BPMN, DMN, CMMN, flowchart, organisation chart, data or procedure modeller) consuming the appropriate public contracts. Presentation does not automatically own the model.
- **Modelling:** an authoring/review/analysis activity that may be described as a Task or Procedure, without implying a universal Modelling process engine.
- **Document:** composed material that may include narrative, tables and embedded/referenced models. Publisher owns its documented document revisions/publication/approval; reusable models may retain separate domain ownership and revision pins.
- **Template Engine:** existing **library candidate** in the governing catalogue. Deterministic parse/bind/render of supplied data only, not template authoring governance, persistence, model validation, data fetching or arbitrary code execution.
- **Document template versus model template:** presentation layout and authorised data bindings differ from reusable semantic model structures. Their creation/revision ownership must be assigned explicitly; Template Engine is not presumed to store either.
- **Model views:** textual, diagrammatic or tabular representations may be generated from one semantic revision, without creating competing semantic masters. Export/import must respect standard-specific semantics and loss reporting.

BPMN process models, DMN decision models and CMMN case models are different notations with different semantics. A general flowchart need not be executable; a valid BPMN diagram is not automatically deployment-authorised. An execution binding must specify approved handlers, resources, policy, version and failure/compensation behaviour before Workflow or another responsible executor can run it. A **Case** can represent adaptive evidence-led work, but does not yet justify a separate capability.

### Relationships, resources and measurable outcomes

- Use **typed, authorised references** across owners: *uses*, *depends on*, *instantiates*, *derived from*, *evidenced by*, *assigned to*, *scheduled occurrence of*, *approved revision of*. Do not create a universal graph database or assume any cross-schema joins.
- **Resources** can be people, facilities, assets, inventory, budgets or compute. Each retains its authoritative owner. Booking/Inventory/Asset/other appropriate owners enforce reservation and capacity; a Project/Procedure/Event reference does not allocate a resource by itself.
- **Goals** specify expected outcomes and measurable indicators. Reported results derive from authoritative owner records and approved measurement definitions, not just Workflow/Temporal activity completion.
- Version and provenance links must support impact analysis: identify active procedure executions, training documents, project references and other consumers of a superseded model revision. Revisions require pinned dependencies and explicit change/withdrawal notice semantics.
- Group-owned Projects, Procedures, Models and Events follow explicit resource ownership and Authorisation; Identity membership alone grants no blanket rights. Private personal projects may be held under a personal group subject to the applicable domain contract.
- Plan for review/approval, deprecation, retention, lawful withdrawal and migration of active instances across definition versions; do not rewrite historical evidence.

### Proposed ownership decision and acceptance examples

**Do not introduce a `modelling` repository by default.** First test whether Publisher's host-registered document lifecycle, specialised model validation libraries, public model/domain contracts and editor presentation can support reusable semantic models. An independent Model-owning capability is justified only if shared semantic model lifecycle, cross-document relationships, revision invariants and validation cannot be appropriately represented by existing owners.

Use as initial acceptance cases: (1) a recurring educational class with one cancelled occurrence and separate bookings; (2) a ferry sailing with scheduled/actual times and a transport-domain owner; (3) a group-owned construction project with resources belonging to other domain owners; (4) monthly stocktake procedure version 2, with historical executions pinned to version 1; (5) one reusable BPMN model embedded in a procedure and training document without silent update; (6) a non-executable flowchart and a separately approved executable workflow binding; (7) adaptive case work that cannot follow a fixed BPMN sequence.

### Additional decisions for the next ADR-impact review

| ID | Recommendation / question | Status |
|---|---|---|
| MD-01 | Keep definition, instance/occurrence, outcome, model revision, view and document revision distinct | Proposed conceptual rule |
| MD-02 | Retain Project, Task and scheduled Event as serious domain candidates; require independent consumer/lifecycle proof before repositories | Proposed |
| MD-03 | Distinguish Process (business activity/outcomes), Procedure (versioned prescribed method), Workflow (authorised coordination) and Scheduler (technical timing) | Proposed |
| MD-04 | Model is structured meaning, modeller is an editor, modelling is an activity, and document is a possible container/reference | Proposed |
| MD-05 | Keep Template Engine as a deterministic library, not a new model/document owner | Grounded in current governing catalogue |
| MD-06 | Treat BPMN/DMN/CMMN as typed notations; importing or rendering a diagram never grants executable authority | Proposed security/contract boundary |
| MD-07 | Preserve resource-authoritative capacity, group ownership, typed references and version-pinned dependencies | Proposed application of governing invariants |
| MD-08 | Determine whether shared Model/Procedure/Case governance requires distinct owners after testing Publisher and domain contracts | **Open**, not repository approval |

## 10. Next step and acceptance conditions

1. Review candidate dispositions and preserve exceptions for demonstrable independent domains.
2. If accepted, propose a **separate targeted governing catalogue PR** that clearly distinguishes candidate capabilities from composition, presentation, libraries and consolidation. Do not remove terms merely to lower the count.
3. Perform a **blueprint-wide decision and ADR-impact review**: reconcile previous proposed precedence rules, personal membership scopes, ownership/publication, integration, execution and commerce, identifying what is already governed and what requires a new ADR.
4. Publish an initial **dependency-validation checklist** for candidate designs, using their eventual root `CONTRACT.md`; no invented manifest edges or implementation-readiness claims.

No implementation, repo creation, runtime registry, new global hierarchy or accepted ADR amendment is authorised by this review.
