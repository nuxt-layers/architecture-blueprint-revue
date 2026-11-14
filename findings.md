# Platform Blueprint Review — Findings

**Review baseline:** 13 November 2026. **Status:** Working evidence and preliminary conclusions, not normative architecture.

## Authority and evidence

- Governing repository: `nuxt-layers/architecture-rules`, `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`.
- Governing documents: `docs/platform-charter.md`, `docs/capability-standard.md`, `docs/architecture.md`, `docs/catalogue.md`.
- Accepted ADRs reviewed: ADR-0001, 0002, 0004, 0008, 0009, 0010, 0011, 0012, 0015, 0016. Recent PRs #32 and #33 are merged.
- External public contracts inspected: `configuration/CONTRACT.md`, `identity/CONTRACT.md`, `authorisation/CONTRACT.md`, `clock/CONTRACT.md` on `master`. `integrations/CONTRACT.md` was not retrievable; absence of a retrieved contract does not demonstrate absence of a design or implementation.

## Catalogue classification

A catalogue entry defines an established architectural responsibility or a provisional candidate; it does not prove delivered functionality or justify an independent repository.

| Classification | Entries / initial disposition |
|---|---|
| Established Foundation | `theme-manager`, `clock`, `configuration`, `identity`, `authentication`, `authorisation`, `profile`, `privacy`, `legal-compliance`; preserve distinct authoritative boundaries |
| Suite composition | `iam-integration`; orchestrates IAM integration but owns no separate domain data |
| Established Platform | `publisher`, `media`, `storage`, `scheduler`, `message-queue`, `workflow`, `content-security`, `notifications`, `comments-engine`, `metadata`, `translate`, `seo`, `site-hosting`, `integrations`; distinctions require contract review, not assumed delivery status |
| Stateless libraries | `rich-text`, `export`; proposed `template-engine` and `uuidv7-generator` should be assessed as libraries before any capability proposal |
| Infrastructure integration | `temporal-gateway`; ADR-0015 applies; does not own scheduling, workflow or message delivery rules |
| Presentation | `ui`, `content-editor`, `gallery`; proposed `company-console`, `user`; no independent business-state authority |
| Composition and archetypes | `application-compositions`, `shop`, `business`, `organisation`, `department`, `office`, potentially `club` and `workshop`; business-specific lifecycle may justify a domain exception |
| Candidate Platform/shared mechanisms | `audit`, `caching`, `logging`, `observability`, `api-gateway`, `search`, `location`, `reporting`, `process`, `project`, `task`, `document`, `reviews`, `infrastructure`, `organisation-model`; status and ownership remain provisional |
| Candidate activity domains | `company`, `committee`, `membership`, `events`, `course`, `enrolment`, `training`, `gaming`, `job`, `booking`; assess independently owned lifecycle rather than labels |
| Candidate commerce domains | `product`, `pricing`, `inventory`, `asset`, `order`, `billing`, `invoice`, `payment-gateway`, `accounting`, `subscription`, `marketplace`, `warehouse`, `logistics`; maintain clear sources of truth |
| Held for consolidation | `image`, `integration-adapters`, `tenancy`, generic `document`, `blog`, infrastructure `job`, and duplicate `*-service` names; follow catalogue consolidation statements |

`architecture-rules` is governance rather than a runtime capability. No separate repository is implied by any classification here.

## Principal overlaps and gaps

1. **Configuration versus business definitions:** separate generic setting validation, scope, precedence and change governance from domain-owned policies and data.
2. **Workflow / Process / Task / Scheduler:** distinguish business outcomes, assigned human work, durable orchestration and scheduled technical execution.
3. **Integrations:** established catalogue description mentions external adapters but does not yet specify a configurable connection-management platform. Connection records, credential references, discovery, mappings, synchronisation state, revocation, provenance and provider compatibility need ownership review.
4. **Organisation Model:** Identity already owns tenants, group hierarchy and memberships; Authorisation owns access decisions. Archetypes and reporting structures cannot become competing authorities.
5. **UI and templates:** shared schema-driven editors and rendering libraries should not own saved domain definitions or bypass domain validation.
6. **Reporting / Search / Audit:** reusable indexes and aggregations must not take ownership of source records or weaken tenant access controls.
7. **Documentation:** the catalogue's reference to ADR-0016 as proposed is outdated; the decision is accepted. This is a later documentation correction, not an approved change.

## Public-contract evidence

- **Configuration:** declared, validated settings; platform/tenant/group/account scopes, precedence and provenance; governed changes. Its contract explicitly states that one-call ADR-0012 composition is not yet met. Account locale/time-zone ownership must not conflict with Profile.
- **Identity:** owns tenants, groups, membership, hierarchy and governance; a `composeIdentity` entry point exists, but shared caller/access port migration is unfinished according to its contract.
- **Authorisation:** independently owns server-side resource-aware permissions and access decisions; current contract documents explicit provider wiring. No measured composition-compliance verdict was established.
- **Clock:** public time parsing, formatting and calendar functions, including framework-independent exports; should be reused by consumers.
- **Integrations:** no root contract retrieved from `master`; proposed broader platform boundaries are therefore unverified, not assumed implemented.

## Workstreams and reference case

1. Capability catalogue and boundaries.
2. Declarative runtime configuration.
3. Contract-derived discovery and capability metadata.
4. External Application Integrations as a named workstream.
5. Workflow, scheduling and execution boundaries.
6. UI, forms, visual editors and templates.
7. Open-source reuse, including commercial self-hosting rights, maintenance, compatibility, security and portability.
8. Multi-channel retailer architectural acceptance case: one application coordinating its own website, eBay and Amazon while Product, Inventory and Order remain authoritative for their business records.

## Next evidence required

Review the contracts relevant to unresolved boundaries, particularly `profile`, `scheduler`, `message-queue`, `workflow`, `publisher`, `ui`, `temporal-gateway`, and `integrations` if published. Do not inspect other files outside `architecture-rules` without approval.

## Task 2 — Declarative configuration and organisational ownership (13 November 2026)

**Evidence:** `architecture-rules/docs/identity.md` at governing SHA `94210cb938f7e3945d933b903cc7a9d71e3a18e7`; published `CONTRACT.md` on `master` for Configuration, Identity, Authorisation, Profile and Clock. This review does not verify implementation beyond contract claims.

| Owner | Verified boundary | Implication |
|---|---|---|
| Configuration | Declared setting schemas, scope and precedence, effective values with provenance, and governed change requests | Reuse the existing management mechanisms; do not assume ownership of domain policy meaning |
| Identity | Opaque identities, tenants, single-parent group hierarchy and membership lifecycle | Organisational archetypes must not reown groups, hierarchy or membership |
| Authorisation | Roles, permissions, grants and resource-aware server decisions | Archetypes may request role profiles but cannot grant access themselves |
| Profile | Canonical personal attributes, preferences including `locale` and `zoneinfo`, and disclosure controls | Configuration account scope must not create a second authoritative personal-preference store |
| Clock | Time representation, parsing, formatting and time-zone rules | Owner of time semantics, not preference persistence or business scheduling |

**Proposed reusable pattern (not an implemented contract):** the domain owner publishes a validated schema and controls its business meaning; a suitable management mechanism handles scoped values, revisions, provenance and approvals; the domain owner validates and enforces effective rules. Whether Configuration can provide all these management functions for complex structured definitions remains to be demonstrated.

**Organisation Model:** the governing Identity and Access document distinguishes Identity groups from the domain entities they represent. Organisational archetypes may describe positions, reporting relationships, organisation types and requested role profiles. A separate owning capability requires evidence of an independent lifecycle; otherwise prefer declarative specialisation under ADR-0016. Reporting relationships must not imply access inheritance.

**Contract and integration constraints:** Configuration's published contract acknowledges incomplete ADR-0012 single-call composition. Identity's shared caller/access migration remains unfinished, and Authorisation has explicit provider wiring; no broad rewrite or compliance claim is warranted. Preserve authentication recency, assurance, tenant boundaries, approvals and fail-closed behaviour when composition is eventually reconciled.

**Open questions:** how domain schemas are registered and versioned; whether complex configuration should be stored with its domain or in Configuration; migration/rollback of definitions; precedence between organisational defaults and personal preferences; runtime activation and audit; how the organisation archetype is tied to Identity's authoritative group identifiers; whether a dedicated Organisation Model lifecycle can be justified.

**Recommended next evidence:** inspect `iam-integration/CONTRACT.md` for existing cross-capability preference resolution before proposing a new mechanism; then prepare a small ownership and definition-lifecycle decision for explicit approval. No architecture-rule or implementation change is authorised.

## Task 3 — Preference resolution and Organisation Model disposition (13 November 2026)

**Governing baseline:** `architecture-rules` `94210cb938f7e3945d933b903cc7a9d71e3a18e7`. Published contracts reviewed: Configuration, Profile, Clock and IAM Integration.

- **Preference ownership:** Profile is canonical for each person's `locale` and `zoneinfo`. Configuration already owns ordinary platform/tenant/group scoped values, inheritance, provenance and approvals. Clock validates and formats, not stores. A composite effective-viewer preference response is a **proposed** application/suite integration, not an existing Configuration feature.
- **Proposed non-locked display fallback:** Profile preference if present; otherwise Configuration's authorised group → permitted ancestor → tenant → platform → deployment → declared default. A failed read is not the same as an absent preference. Locked settings and their interaction with personal preferences require a separate explicit policy decision.
- **Performance and privacy:** resolve request-scoped on the server, reuse for SSR and hydration, avoid duplicate fetches, and invalidate on context/identity/preference changes. No shared personal cache or client-supplied tenant authority.
- **Organisation Model:** current authority already allows versioned archetype specialisation under ADR-0016 without creating a capability. Candidate fields include organisation type, requested role profiles, positions, capability selections and non-authoritative reporting relationships. They cannot be second Identity memberships/hierarchies or Authorisation decisions.
- **Independent capability criterion:** require separately owned, durable state and meaningful lifecycle (e.g. governed archetype publication/versioning, assignment, transition and retirement with compatibility guarantees) not satisfactorily provided by existing domain contracts or validated application configuration. That lifecycle has **not been evidenced** by the public contracts reviewed.
- **Provisional disposition:** no new `organisation-model` repository or capability yet; retain classification as unresolved. Do not infer that organisational archetypes may be persisted as generic Configuration keys without validating schema limits, revision semantics, and business invariants.

**Further work:** decide exactly where a resolver adapter belongs and how locked values interact with personal preferences; evaluate archetype lifecycle with a few concrete company, club and retail cases; reconcile `iam-integration/CONTRACT.md` separately before treating it as a reliable consumer handover.

## Task 4 — Capability Discovery and Registry (14 November 2026)

Full evidence and proposed model: [`reviews/capability-discovery-registry.md`](reviews/capability-discovery-registry.md).

The Capability Standard §4 already mandates Nuxt-layer `capability.json` metadata with provided and required contracts; non-Nuxt packages use `CONTRACT.md` and public exports without a universal manifest. ADR-0016 separately declares direct Nuxt extension edges alongside contract dependencies and requires a jointly acyclic graph. These metadata declarations do not replace the owner contract.

Reviewed public contracts show that Identity's ability to operate securely depends on required providers, Publisher's installed package does not reveal its host-registered document types or a viewer's permissions, and Configuration's installed package does not reveal its host-declared setting keys or effective viewer values. Consequently, **installed**, **composed**, **operational**, **enabled**, **permitted** and **public** must not be conflated.

**Recommendation, not normative:** no new persistent registry capability. Generate an application-owned read-only inventory from selected package metadata and explicit composition declarations at build/deployment; validate compatibility, missing required dependencies and combined service/extension cycles. Runtime availability and permissions must remain owner-provided queries and server-side decisions. A universal cross-package manifest needs separate schema approval under the existing standard. No actual capability manifests were inspected or implementation availability proven in this review.

## Task 5 — External Application Integrations (14 November 2026)

See [`reviews/external-application-integrations.md`](reviews/external-application-integrations.md) for the complete ownership and lifecycle review.

- The governing Capability Catalogue lists `integrations` as a Platform capability for external service adapters; the Capability Standard distinguishes provider-specific connectors from business capabilities and ADR-0015 infrastructure integration libraries. The proposed connection-management responsibility is **not yet established as an implemented public contract**.
- Root `CONTRACT.md` could not be retrieved for `nuxt-layers/integrations`, `message-queue` or `workflow` on `master`; this is an evidence gap, not proof of non-existence or a licence to inspect private implementation.
- Proposed shared concerns: scoped connection lifecycle and credential references, explicit provider authorisation, read-only provider/operation discovery, external-ID mapping and synchronisation checkpoints, audit provenance, webhook verification, idempotency and reconciliation. Business rules and authoritative records remain with originating domain capabilities.
- Crucial distinction: installed connector, configured account, valid credentials, authorised operation, reachable provider and completed synchronisation are different states; no proxy of permissions, group membership or publication visibility may substitute for server-side Authorisation.
- Multi-channel retail scenario (owned website, Amazon, eBay) is an architecture acceptance test, not proof of connector availability. Jurisdiction, privacy, external publication licences and organisational policy remain independently relevant.

## Task 6 — Workflow, Process, Task and Temporal execution boundaries (14 November 2026)

Full review and acceptance cases: [`reviews/workflow-process-task-execution.md`](reviews/workflow-process-task-execution.md).

The authoritative catalogue establishes `scheduler` (runs owners' scheduled handlers), `message-queue` (delivers owners' outbox events) and `workflow` (review, sign-off and approval routes), while `temporal-gateway` is an ADR-0015 framework-independent infrastructure integration library that **does not** own business scheduling, orchestration, event delivery or persistence. `process` (business process definitions/outcomes) and `task` (assigned human work) remain candidates, not approved additional repositories.

A key ambiguity is the overloaded word *task*: a human assignment is not an ADR-0012 `{name, every, run}` scheduled handler and neither is a Temporal activity. Identity's published `getIdentityTasks()` returns maintenance and outbox handlers; Publisher's `composePublisher()` returns scheduled maintenance/relay and uses a digest-bound approval port. Clock owns temporal semantics, not scheduling. These are verified public-contract examples, **not evidence that Workflow, Scheduler or MQ have interoperable runtime contracts**.

Root `CONTRACT.md` was not retrievable for Scheduler, Workflow, Message Queue, Temporal Gateway, Task or Notifications at review time; distinguish missing evidence from implementation status. The proposed design maintains domain-owned records, durable outboxes, explicit approved actions, tenant isolation, replay/idempotency, workflow versioning, schedule time semantics and provider failure recovery. No Temporal persistence or queue state belongs in Hetzner S3-compatible application object storage.

## Task 7 — Organisational candidate consolidation (14 November 2026)

See [`reviews/organisation-candidate-consolidation.md`](reviews/organisation-candidate-consolidation.md) for a focused classification review against `architecture-rules/docs/catalogue.md`, `docs/identity.md` and ADR-0016.

The seven labels `organisation`, `business`, `company`, `club`, `committee`, `department` and `office` already fit the Identity-owned group context and schema-validated organisational archetype model. None should be presumed an independent Nuxt capability from its label alone. Recommend treating each as an archetype or application composition **by default**, while preserving genuinely independent business lifecycles such as statutory entity records, formal committee votes, club subscriptions, facility records and domain operations as separately justified candidate owners.

Identity remains the only authority for groups, hierarchy and ordinary memberships; Authorisation for access; Profile for personal data. An archetype never grants permissions or owns a second membership tree. The `membership` commercial candidate remains distinct from Identity membership only where it owns paid/scheme rights, fees and renewals. `organisation-model` stays unapproved pending demonstrable independent archetype governance state/lifecycle. Proposal requires a subsequent separately approved documentation PR in governing `architecture-rules`; no catalogue or implementation change is made here.

## Task 8 — Multi-channel Commerce and Warehouse boundaries (14 November 2026)

Full design review and acceptance cases: [`reviews/multichannel-commerce-warehouse.md`](reviews/multichannel-commerce-warehouse.md).

The governing catalogue already distinguishes the candidate domains `product`, `pricing`, `inventory`, `order`, `warehouse`, `logistics`, `billing`, `invoice`, `payment-gateway` and `accounting`. A retail `shop` can primarily be an application composition. Selling on Amazon/eBay through external connectors does **not** automatically justify a new multi-seller `marketplace` capability. Warehouse physical work must not duplicate Inventory's canonical movements/balances, while Order preserves canonical commercial commitments and accepted price snapshots.

The principal consistency risk is simultaneously publishing stock availability to several eventually consistent channels. Inventory must own reservations and available-to-promise, while connectors publish bounded projections and report delays, failures and reconciliation state. Cross-capability transactions cannot be treated as atomic; use public commands, durable owner outboxes, idempotency and explicit failure/compensation handling. Staff membership and group/site context determine authorised access, not domain ownership or a new group hierarchy.

Root `CONTRACT.md` was not retrievable for the six candidate repositories Product, Pricing, Inventory, Order, Warehouse and Marketplace. This limits the review to authoritative catalogue boundaries and proposals; no implementation-readiness claims are made. The organisational archetype clarification in governing PR #34 remains **proposed** pending its merge.
