# Capability Register — Working Design

**Status:** Blueprint proposal for collaborative review. **Not normative, not a deployed registry, not an approved manifest schema.**  
**Established:** 14 November 2026.  
**Repository:** `nuxt-layers/architecture-blueprint-revue` (the existing blueprint review repository; the spelling `revue` is intentional).  
**Governing authority at creation:** `nuxt-layers/architecture-rules` `master` at `2eea557fa3acbf9412fea065d2a3cdda42a764fc`.  
**Working catalogue:** [`capability-catalogue.md`](capability-catalogue.md), initially copied **verbatim** from governing `docs/catalogue.md` at that revision. The copied catalogue's relative links originally target the governing repository's `docs/` and `docs/decisions/` layout; while unmodified, they are source-relative and must not be mistaken for local blueprint documents. Use links to the governing repository for authoritative ADRs and standards.
**Existing review:** [Capability Discovery and Application Registry](../reviews/capability-discovery-registry.md), decisions CD-01–CD-08 in [`decisions.md`](../decisions.md).

## 1. Why keep two documents?

| Document | Question it answers | Content and authority |
|---|---|---|
| **Capability Catalogue** (this blueprint's working copy) | What responsibilities or reusable components should exist across the platform? | Architectural vocabulary, established capabilities, proposed candidates, exclusions, ownership and classification. The governing original remains authoritative until a separate approved architecture-rules PR changes it. |
| **Capability Register** (this document) | Which catalogue entries have been specified, implemented, integrated or used, and with what evidence? | A traceable design-time status and relationship register, plus a proposed *application-specific* read-only discovery view. Status here is evidence-based and non-authoritative for runtime operation. |

A catalogue entry can exist without a package. A package can be installed but not configured or operational. Being operational never implies permission to use it.

The two working documents are **not** new business capabilities, runtime persistence services or competing sources of authority. This document is the design for a register; it is not a claim that the register already exists as software.

## 2. Working principles

1. **Respect accepted authority.** Existing ADRs, normative standards and each owner's root `CONTRACT.md` govern. This review may identify needed amendments but cannot override accepted provisions.
2. **Review before changing authority.** Record reasons, alternatives, security/privacy consequences and affected contracts in the blueprint; only an expressly accepted ADR/standard/catalogue PR in `architecture-rules` makes a governance change.
3. **One owner per authoritative fact.** A register references owned records; it does not own capabilities' business data, permissions, secrets, operational status or policy.
4. **Separate concept from implementation.** A capability, library, connector, archetype, composition or presentation may be recorded without implying a standalone repository or Nuxt Layer.
5. **Evidence rather than assumption.** An absent root contract, stale status, missing data or failed probe is **unknown/not verified**, not proof that an implementation does not exist.
6. **Portability.** Nuxt layers use their existing `capability.json` and public `CONTRACT.md`. Framework-independent capabilities and libraries use their public contract and package exports. No new universal manifest is imposed.
7. **Dependencies remain directional and acyclic.** Track public service dependencies separately from Nuxt `extends` and declarative specialisation; verify the combined code/runtime dependency graph where applicable. Events alone are not compile-time dependency edges.
8. **Limit privileges.** A register entry cannot execute arbitrary code, install packages, disclose protected data or grant authorisation. A UI hiding/showing an operation is not an access decision.

## 3. Two related views, not two sources of truth

### A. Platform design register

A reviewed, human-readable inventory of the capabilities and other building blocks in the working catalogue. It records classification proposals, source evidence, decision status and gaps. It can track the development programme across different repositories without becoming their authority.

**Entry fields proposed:**

| Field | Meaning | Authoritative evidence |
|---|---|---|
| `identifier` | Stable name used in catalogue and design discussions | Governing catalogue or expressly labelled candidate |
| `title` / `purpose` | Short statement of responsibility | Catalogue, accepted ADR and owner `CONTRACT.md` |
| `classification` | Foundation, Platform, Domain, Presentation, Library, Infrastructure integration, Connector, Archetype or Composition | Governing rules; proposed values flagged as proposals |
| `ownership` / `exclusions` | Facts/rules owned and explicitly not owned | Catalogue and public contract |
| `governanceStatus` | Established, candidate, proposed change, superseded/consolidated | Accepted governing documents |
| `designStatus` | Not reviewed, review in progress, proposed, review agreed, awaiting governing decision | Blueprint review records |
| `implementationEvidence` | Repository, revision, root public contract, manifest/exports and CI/consumer acceptance evidence | Verified repository documents and checks |
| `relations` | Public required/optional services, extension/specialisation, events and references | Owner contract, published metadata and verified composition |
| `decisions` | Relevant ADRs, review decision IDs, outstanding questions and review date | Linked registers |
| `sourceRevision` / `verifiedAt` | What was checked and when | Exact repository revision and dated inspection |

Do not invent a generic status such as “complete” from a merged review PR; completion of a design review is not implementation readiness. For implementation status, record **unknown** until source evidence is inspected.

### B. Application capability inventory

An application-generated, **read-only descriptive artefact** about its explicitly selected packages, verified contract metadata and build/composition choices.

Proposed discovery pipeline:

1. Application composition identifies the actual selected packages, declared Nuxt `extends` edges and documented, contract-based service providers.
2. The build or composition validator checks names, provided interfaces, versions, duplicates, required providers, compatibility and dependency cycles.
3. The inventory records the evidence and distinguishes installed, selected, configured, initialised, operational and enabled. Runtime readiness comes from authenticated, **owner-provided** health/readiness mechanisms, not inferences based on package presence.
4. Permission for an action is evaluated **at request time** by Authorisation and the domain owner, using the verified actor, tenant/group, resource and assurance context. Do not store one universal “authorised” flag for a capability.
5. Runtime feature types (e.g. Publisher's registered document types or Configuration's declared settings) are queried through their owning public contracts, not copied into a competing global feature database.
6. Optionally expose an appropriately authorised, redacted administration/diagnostic view, without credentials, private routes, personal data or unrestricted package metadata.

This view is an **optional application composition output**. The design does not require a new `nuxt-layers/capability-register` runtime repository or database.

## 4. Precise status vocabulary

These labels describe **different dimensions**, not a single linear “maturity” ladder.

| Dimension | Values or question | Important distinction |
|---|---|---|
| Architecture | Established / candidate / proposed / consolidated | Does the governing catalogue recognise the responsibility? |
| Design | Unreviewed / under review / review-agreed / governing-approved | Has the design direction been reviewed and accepted? |
| Delivery evidence | No evidence / public contract / implementation / independently tested | What can be verified, at which revision? |
| Selection | Available / installed / selected / composed | Is code actually present and included in this application? |
| Configuration | Required providers available / missing / unknown | A selected package may not be usable |
| Operation | Healthy / degraded / unavailable / unknown | Needs an owner-provided probe with time/context |
| Exposure | Enabled / disabled / limited | The application's choice, not an access grant |
| Permission | Allowed / denied / indeterminate **per request** | Authorisation and domain checks, not global metadata |
| Publication | Private / restricted / application-public / world-public where supported | A resource's publication state, never implied by capability installation |

Never collapse **installed**, **operational**, **enabled**, **authorised** and **public** into one “available” badge. Unknown health must not be presented as healthy or treated as a permission grant.

## 5. Relationship types and dependency validation

| Relationship | Meaning | Include in runtime/build dependency DAG? |
|---|---|---|
| `requires` | Consumer needs a provider's published public contract; fails closed if required provider absent | **Yes** |
| `optionallyUses` | Consumer can operate without provider under an explicit documented absence mode | **Yes** where it is a declared runtime/build dependency |
| `extendsNuxt` | Direct documented Nuxt framework contribution extension | **Yes** |
| `specialises` | Declarative schema-validated archetype or model extension | Depends on actual compile/runtime dependency; not automatically an inheritance edge |
| `composes` | Application assembles contracts, presentation and configuration | Represent at the application boundary; avoid inferred provider-to-consumer edges |
| `publishesEvent` / `consumesEvent` | Owner business facts delivered through Message Queue | **No** by itself; any actual imported contract still counts |
| `referencesResource` / `embedsModel` | Typed cross-owner data relationships | **No** by itself; access checks and version semantics remain necessary |

**Validation gate:** a proposed dependency is not automatically an approved manifest `requires` entry. Check the owner's root `CONTRACT.md`, provider availability, versions, optional absence, failure behaviour and combined cycle rules before declaring a machine-readable edge.

## 6. Minimum initial register and review pointers

This table is **a starter index**, not a complete inventory of all entries in the catalogue. Its classifications refer back to the governing catalogue and previous blueprint reviews; no delivery state is asserted.

| Entry | Category / stage | Review focus |
|---|---|---|
| Identity, Authentication, Authorisation, Profile, Configuration | Established Foundations | Public contracts, caller/decision assurance and cross-capability composition |
| Publisher, Media, Storage | Established Platform | Document ownership, model/template reuse and file-byte boundaries |
| Scheduler, Workflow, Message Queue | Established Platform | Business work versus orchestration and event transport |
| Temporal Gateway | Infrastructure integration library | Shared external SDK/worker lifecycle without business state |
| Template Engine | Library candidate | Deterministic parsing, binding and rendering; not model/document persistence |
| Project, Task, Process, Procedure, Case | Candidate / review vocabulary | Definitions, instances, outcomes, group ownership, human assignments and adaptive work; no automatic new repositories |
| Events, Booking, Course | Domain candidates | One-off and recurring scheduled occurrences; capacity and specialist domain responsibilities |
| Modelling core, modellers and model types | Proposed vocabulary; implementation forms unapproved | Semantic ownership, metamodel validation, BPMN/DMN/CMMN/flowcharts, editors and pinned revisions |
| Product, Pricing, Inventory, Order, Warehouse | Domain candidates | Multi-channel stock consistency and source-of-truth boundaries |
| Organisation, Company, Club, Committee, Department, Office | Archetypes/compositions by default | Governing PR #34; no second Identity group model |
| Integrations and external provider connectors | Established Platform + optional adapters | Provider accounts, credentials references, mappings and sync coordination |
| Search, Reporting, Audit, Reviews | Candidates | Authoritative facts versus derived indexes, reports, evidence and moderation |

**Next action:** replace broad starter rows with traceable entries for every catalogue item, linking review decisions and verified public sources. Keep unknown values explicit rather than manufacturing implementation status.

## 7. Working catalogue management

The copied [`capability-catalogue.md`](capability-catalogue.md) is the **editable proposed future catalogue**, initially identical to the governing source. It may diverge deliberately during review. For each proposed change:

- State what is added, removed, renamed, consolidated or reclassified and the bounded ownership reason.
- Record the affected published contracts, dependencies, consumers, security/privacy implications and compatibility impact.
- Explain whether an accepted ADR or standard already permits it, requires clarification, or obstructs it. Distinguish **an incomplete rule** from a **conflicting rule**.
- Do not rewrite or silently delete accepted governing requirements in the blueprint; mark proposed amendments as proposals and state their alternatives.
- Capture proposed ADR/standard amendments in the review decision register before preparing any change in the governing repository.
- When ratified, update the governing catalogue and relevant ADRs/standards through separately reviewed pull requests; refresh and reconcile the working copy rather than overwriting new governing changes blindly.

**Source-copy caveat:** the working catalogue was copied verbatim. Its internal relative links (for example `decisions/ADR-....md` and `identity.md`) point to the source repository's former relative layout; do not interpret their absence from this blueprint directory as missing governing ADRs. We should either convert them to explicit governing-repository links in a deliberate later edit or maintain a visible source mapping.

## 8. Potential ADR/standard impact — issues to assess, not decisions

| Question | Current authority or gap | Proposed review |
|---|---|---|
| Can a separate design-time register exist? | Existing catalogue + ADR-0004 governance and documentation placement | Document as non-authoritative working artefact; if intended as permanent governing material, confirm document placement in ADR-0004 |
| Should all packages have a machine-readable manifest? | Capability Standard §4 currently limits `capability.json` to Nuxt capabilities; no universal schema | Do not introduce one unless independent, non-Nuxt consumer cases justify a new standard/ADR |
| Who is authoritative for runtime health and feature lists? | Owners' public contracts and application composition; CD-04/CD-07 proposals | Define diagnostics contract only where necessary; no global mutable business registry |
| How are dependencies and extension edges checked? | ADR-0016 already covers direction and combined acyclicity | Specify verification evidence, not redundant new authority |
| Do model templates/definitions require a Model capability? | Publisher owns its document lifecycle; Template Engine is a library candidate | Evaluate semantic validation, reuse, versioning and cross-document references first |
| Do Process/Procedure/Project/Task/Events need new owners? | Catalogue and blueprint proposals; some entries are candidates, others conceptual | Prove separately owned lifecycle before capability/ADR amendment |
| Can an app load code based on a register row? | Existing explicit composition and security model | **No**; any dynamic-plug-in design would need an explicit security/architecture decision |

## 9. Acceptance criteria for this blueprint

1. The source catalogue is copied at a recorded commit with content unchanged at the initial checkpoint.
2. Catalogue and register explicitly state their different roles and authority.
3. Every proposed status/relationship carries a verifiable source or is labelled unverified.
4. Existing package/public contract and ADR-0016 dependency rules are respected without an invented universal manifest.
5. No installation, authorisation, secret, domain-record or runtime-ownership privileges are granted by the register.
6. ADR impacts are recorded before any governing PR is created.
7. Later catalogue revisions can be compared against the source baseline and the latest accepted governing revision without silent loss of upstream changes.

**Review checkpoint:** this document is a starting design for discussion, not a completed capability-by-capability audit.
