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
