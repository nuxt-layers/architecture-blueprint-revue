# Capability Register — Working Inventory

**Status:** Non-normative, catalogue-derived register. **Implementation and operational status have not been audited.**

**Source:** [Working Capability Catalogue](capability-catalogue.md), originally copied from the governing `nuxt-layers/architecture-rules/docs/catalogue.md` at commit `2eea557fa3acbf9412fea065d2a3cdda42a764fc`. Accepted governing rules remain authoritative.
**Design specification:** [Capability Register Design](../proposals/capability-register-design.md).
**Review records:** [Decisions](../records/decisions.md) · [Findings](../records/findings.md).

## Purpose

This document is the **populated working register**, distinct from its design specification. It indexes every entry in the working catalogue without claiming that a package exists or functions. "Established" means an established architectural responsibility, **not** an implemented or operational package.

## Catalogue-derived entries

### Established capabilities

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `theme-manager` | Foundation | Established | Theme definitions, the semantic token vocabulary, contrast checks, selection and preferences; the only authority for the effective theme |
| `clock` | Foundation | Established | Canonical date/time values, parsing, formatting, temporal arithmetic, time zones and clock services |
| `configuration` | Foundation | Established | The facts a site, tenant or group states about itself, changed through approvals |
| `identity` | Foundation (IAM) | Established | Identities, each person's personal group, groups, the single-parent hierarchy, tenants and memberships with their lifecycle; no personal data beyond opaque identifiers |
| `authentication` | Foundation (IAM) | Established | Credential verification, sign-in identifiers, sessions, rotation, revocation and reauthentication |
| `authorisation` | Foundation (IAM) | Established | Server-side, resource-aware access decisions, with no implicit inheritance through the group hierarchy |
| `profile` | Foundation (IAM) | Established | The personal data describing a person, keyed by an Identity identifier: what each context may see, under the person's control, and their access, correction, export and erasure requests |
| `iam-integration` | Foundation (IAM) | Established | The IAM suite's architecture, cross-capability processes and the adapters connecting its members ([ADR-0005](https://github.com/nuxt-layers/architecture-rules/blob/2eea557fa3acbf9412fea065d2a3cdda42a764fc/docs/decisions/ADR-0005-iam-suite.md)); no data of its own |
| `rich-text` | Library | Established | The one safe formatted-text format, its profiles, checks and safe tree; never renders ([ADR-0011](https://github.com/nuxt-layers/architecture-rules/blob/2eea557fa3acbf9412fea065d2a3cdda42a764fc/docs/decisions/ADR-0011-shared-rich-text-and-configurable-copy.md)) |
| `publisher` | Platform | Established | Documents of host-registered types: drafts, versions, approval, scheduling, addresses, listings and feeds |
| `media` | Platform | Established | Images: web-ready variants, metadata removal, alt text, group ownership and allowances; the files themselves through the Files shape ([ADR-0012](https://github.com/nuxt-layers/architecture-rules/blob/2eea557fa3acbf9412fea065d2a3cdda42a764fc/docs/decisions/ADR-0012-trivial-composition.md)), which the host fills with `storage` |
| `storage` | Platform | Established | Files' bytes in an object store behind one provider-agnostic port: keys under tenant and group, signed addresses, lifecycle, backups and usage figures; no records of its own |
| `ui` | Presentation | Established | Generic, fully styled components on Theme Manager's vocabulary; knows no capability |
| `content-editor` | Presentation | Established | Editing screens for Publisher's documents, saving through Publisher's functions; no data of its own |
| `gallery` | Presentation | Established | Albums and photo of the week as Publisher types over media's images; no data of its own |
| `privacy` | Foundation | Established | Consent and retention policy, which capabilities receive and apply |
| `legal-compliance` | Foundation | Established | The legal duties that apply to each site by jurisdiction, such as UK GDPR and the Online Safety Act, and the constraints they impose beside Authorisation's decisions, such as legal holds that block deletion |
| `export` | Library | Established | Turning a `rich-text` safe tree into files such as PDF and .docx, extensible by format |
| `scheduler` | Platform | Established | Running every capability's `tasks` on time, like cron, for the host |
| `message-queue` | Platform | Established | Carrying messages that something happened, such as "password changed", from capabilities' outboxes to the capabilities that listen; not calendar events, which are the `event-service` candidate |
| `workflow` | Platform | Established | User-defined routes for review, sign-off and approval, per type and site; fills capabilities' approval ports |
| `content-security` | Platform | Established | Scanning what people submit for dangerous content, links and abuse, on top of each layer's own input checks |
| `notifications` | Platform | Established | Telling people something needs them, through their chosen channels; providers' keys come from the host's secret management |
| `comments-engine` | Platform | Established | Comments on content and their moderation |
| `metadata` | Platform | Established | Tags, categories, taxonomies and ontologies attached to content |
| `translate` | Platform | Established | Translation of content and its workflow |
| `seo` | Platform | Established | Redirects, sitemaps and search-engine metadata, driven by Publisher's address events |
| `site-hosting` | Platform | Established | Where each site is served: custom domains, proving their ownership, and finding the site for a request's address; certificates and deployment stay with the host's proxy and provider (ADR-0014) |
| `integrations` | Platform | Established | Adapters to outside services such as Google Docs, Microsoft 365 and social feeds |

### Infrastructure integration libraries

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `temporal-gateway` | Infrastructure integration library | Established | Shared Temporal connection, schedule, execution and worker SDK integration; no business scheduling, workflows, message transport or capability-owned persistence |

### Additional architectural entries

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `architecture-rules` | Architecture governance (not a runtime layer) | Governance | Governs ADRs, normative standards and this catalogue; owns no application runtime data |
| `infrastructure` | Foundation candidate | Under consideration | Host/backend/deployment setup and operational configuration; infrastructure resources and domain data remain with the appropriate hosts/providers |
| `reviews` | Platform candidate | Under consideration | Customer ratings, testimonials, verified purchase/service evidence, moderation, merchant replies and trustworthy reputation aggregates; transaction and identity facts remain owned by source capabilities |
| `template-engine` | Library candidate | Under consideration | Secure deterministic template parsing, data binding and content rendering; no template persistence, document lifecycle, arbitrary code execution or business-data lookups |
| `organisation-model` | Domain / platform boundary to be decided | Under consideration | Versioned, schema-validated organisational archetypes, positions, non-authoritative reporting relationships and capability selection; cannot reown Identity groups/memberships or Authorisation decisions |

### Foundation, platform and library candidates

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `audit` | Platform | Candidate | Tamper-evident accountable business/security audit evidence and review; distinct from operational Logging and Observability |
| `caching` | Foundation | Candidate | Cache provider ports, expiration and invalidation; source capabilities remain authoritative |
| `logging` | Foundation | Candidate | Structured technical logs, correlation and sinks; not the authoritative business audit trail |
| `observability` | Platform | Candidate | Metrics, traces, system health and diagnostics; not logging or business auditing |
| `uuidv7-generator` | Library candidate | Candidate | UUIDv7 identifier generation; not ownership of entities or credentials, and not automatically a Nuxt Layer |
| `api-gateway` | Composition / platform to decide | Candidate | External API routing and exposure; not a replacement for capability APIs or server authorisation |
| `application-compositions` | Composition artefacts | Candidate | Reusable application templates and selected capability wiring; generally not independent stateful services |
| `company-console` | Presentation | Candidate | Cross-capability management screens; no business records |
| `user` | Presentation | Candidate | Group-scoped user administration screens via Identity/Profile/Authorisation; no user records |
| `document` | Platform candidate, unresolved | Candidate | Distinct document-management requirement only if Publisher's document lifecycle is insufficient |
| `search` | Platform | Candidate | Indexing, query and relevance over authorised source records; no source-of-truth storage |
| `location` | Platform | Candidate | Coordinates, geospatial and place references; not all postal addresses or site tenancy |
| `reporting` | Platform | Candidate | Authorised report definitions, aggregation and output; no source transaction records |
| `process` | Platform candidate | Candidate | Business process definitions and outcomes; execution uses Workflow/Scheduler without duplicating their responsibilities |
| `project` | Platform candidate | Candidate | Coordinated bodies of work, milestones, responsibilities and budgets; uses Task/Process contracts where applicable |
| `task` | Platform candidate | Candidate | Individual assigned work, goals, progress and due dates; not Temporal jobs or scheduler triggers |
| `booking` | Domain candidate | Candidate | Reservation lifecycle, availability and capacity for resources; neither a clock nor execution scheduler |

### Organisational archetypes and compositions

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `organisation` | Archetype / composition | Example, not standalone candidate | General organisational context and type; separate legal/governance records require a justified domain owner |
| `business` | Archetype / composition | Example, not standalone candidate | Organisation-oriented business application composition; commerce, accounting and other operations retain their own domain owners |
| `company` | Archetype / composition | Example, not standalone candidate | Organisation type with legal-entity references; statutory registration, officers or filings may warrant a separately reviewed domain |
| `club` | Archetype / composition | Example, not standalone candidate | Group type for a club with selected activity capabilities; paid scheme rights and subscriptions are not Identity membership |
| `committee` | Archetype / composition | Example, not standalone candidate | Group type and meeting composition; formal votes, resolutions and minutes may warrant their own independently governed lifecycle |
| `department` | Archetype / composition | Example, not standalone candidate | Organisational unit type, potentially expressed through Identity's existing single-parent group hierarchy; reporting does not grant access |
| `office` | Archetype / composition | Example, not standalone candidate | Organisational location/unit type; physical facilities, leases and managed assets belong to independently justified domain owners |

### Activity domain candidates

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `membership` | Domain candidate | Candidate | Paid/scheme membership rights, terms, renewals and benefits; NOT Identity's existing group membership |
| `events` | Domain candidate | Candidate | Planned participant activities and event lifecycle; not background scheduled tasks |
| `course` | Domain candidate | Candidate | Structured learning offering and outcomes |
| `enrolment` | Domain candidate | Candidate | Course/learner participation and allocation |
| `training` | Domain candidate | Candidate | Verified training participation, competencies and completion records |
| `gaming` | Domain candidate | Candidate | Game-specific state, rankings and participation; separate from generic Identity accounts |
| `workshop` | Domain candidate | Candidate | Event/Course specialisation unless distinct rules justify a separate domain |
| `blog` | Domain candidate | Candidate | Publisher document type and presentation unless separate domain state demonstrably exists |
| `job` | Domain candidate | Candidate | Ambiguous: employment role/post is possible domain; background execution job belongs to Scheduler |

### Commerce and operations domain candidates

| Name | Classification | Catalogue status | Responsibility / exclusion |
|---|---|---|---|
| `product` | Domain candidate | Candidate | Saleable products, families, bundles and variants; not stock quantities |
| `pricing` | Domain candidate | Candidate | Price rules, lists, promotions and calculations; not order records |
| `inventory` | Domain candidate | Candidate | Stock quantities, movements, reservations and stock ownership; not product definition or warehouse workflows |
| `asset` | Domain candidate | Candidate | Owned controlled assets, lifecycle, assignments and valuation references; not generic sale stock |
| `order` | Domain candidate | Candidate | Commercial orders, lines, states and fulfilment obligations; not payment settlement |
| `billing` | Domain candidate | Candidate | Billable charges and periods; not the owner of the entire ordering lifecycle |
| `invoice` | Domain candidate | Candidate | Invoice issuance, numbers, adjustments and settlement state; not payment provider execution |
| `payment-gateway` | Domain candidate | Candidate | Payment-provider initiation, status, capture and refund coordination; no accounting ledger |
| `accounting` | Domain candidate | Candidate | Ledger, journal, periods and reconciliations; not the originating sales or warehouse transactions |
| `subscription` | Domain candidate | Candidate | Recurring commercial agreements, plans, renewals and entitlements; not payment provider |
| `shop` | Domain candidate | Candidate | Retail/ecommerce composition and shop-specific operations; not replacement of Product/Order/Inventory |
| `marketplace` | Domain candidate | Candidate | Multi-seller participation, listings and marketplace-specific commercial operations |
| `warehouse` | Domain candidate | Candidate | Receiving, put-away, picking, packing and physical warehouse operations; Inventory owns stock facts |
| `logistics` | Domain candidate | Candidate | Shipment and delivery operations, dispatch, carrier coordination and tracking |

## Evidence and status fields

**For every entry above:** implementation evidence **not checked**; independent consumer tests **not checked**; application installation, composition and operational health **not assessed**. These are explicitly unknown, not absent or failed.

| Evidence to add | How it must be verified |
|---|---|
| Root public contract | Repository, path and exact revision of `CONTRACT.md`; or not retrievable |
| Implementation / package | Exact repository revision, public exports and relevant package metadata |
| Independent acceptance | Linked CI build, public-contract tests and minimal consumer evidence |
| Architectural decision | Reviewed decision ID, and governing ADR/catalogue adoption reference if accepted |
| Dependencies | Verified public `requires` and optional contracts; direct Nuxt `extends` separately |
| Application selection | Explicit deployment's installed, selected and composed packages, not platform-wide |
| Health / readiness | Owner-supported health observation with relevant time and scope |
| Action permission | Request-time Authorisation and owning domain decision, never a global register grant |
| Last verified | Date and exact source revision; use unknown where not examined |

## Rules for changes

1. Verify the current governing ADRs, Capability Standard, catalogue and owner root contracts before recording implementation or compatibility facts.
2. Treat catalogue classifications and proposed design changes as distinct from actual availability; maintain review evidence in [`reviews/`](../reviews/) and status/decisions in [`records/`](../records/).
3. An installed package is not necessarily configured, operational, enabled or authorised. An unknown status must not be shown as healthy.
4. Do not impose an undocumented universal manifest. Nuxt capability layers already publish `capability.json`; framework-independent packages use their public contracts and exports.
5. This register is neither a new runtime capability nor an authority for installing code, storing business data or deciding permissions.

**Next work:** assess each entry's root public contract and acceptance evidence, adding verified provenance without presuming implementation from its catalogue entry.
