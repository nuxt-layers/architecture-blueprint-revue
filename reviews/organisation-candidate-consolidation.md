# Review — Consolidation of Organisational Candidates

**Status:** Blueprint classification proposal, not a governing catalogue amendment or implementation decision.  
**Reviewed:** 14 November 2026.  
**Governing baseline:** `nuxt-layers/architecture-rules` `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`.  
**Authority:** `docs/catalogue.md` (Organisational archetypes versus business domains and Candidates), `docs/identity.md`, Capability Standard, ADR-0001, ADR-0002, ADR-0003, ADR-0016. **Review precedent:** `decisions.md` D-02, D-02a–d and `proposals/membership-governance-publication.md`. No implementation source or unrelated repository was inspected.

## 1. The problem and the classification test

The candidate list contains `organisation`, `business`, `company`, `club`, `committee`, `department` and `office` even though the governing Identity group model already expresses groups for organisations, clubs, committees, departments and other purposes. A label, distinctive interface, custom fields or a particular operating sector is **not enough** to justify a separately owned capability.

Use this test before promoting any candidate to an independent domain: **does it own distinct, durable records, governance rules, transitions, security/privacy requirements and an independently meaningful lifecycle that established capabilities cannot safely represent?** If not, prefer a validated archetype, ordinary group metadata, application composition or presentation.

The goal is to reduce duplicated candidate capabilities, **not** delete the vocabulary or prohibit real business capabilities when their independent records are demonstrated.

## 2. Proposed dispositions

| Name | Preferred disposition | Exception or independent domain test |
|---|---|---|
| `organisation` | General organisational type/archetype referencing Identity's group ID | Independent legal/governance lifecycle must first be evidenced; `organisation-model` stays provisional |
| `business` | Organisational archetype plus business-operations application composition | Specific business domains (Order, Inventory, Accounting) still own their own records |
| `company` | Organisation archetype with optional legal-entity reference/metadata | Jurisdiction-specific registration, officers, filings, statutory records may warrant a separately scoped legal-entity domain; not a replacement Identity group |
| `club` | Organisation archetype, configurable roles and selected capabilities | Competitions, memberships-as-products, subscriptions or club-specific activities may belong to separate *activity* domains |
| `committee` | Organisational/group archetype and meeting/presentation composition | Formal resolutions, recorded votes, quorums, conflicts of interest and minutes may justify distinct decision/meeting governance records; never a second membership database |
| `department` | Organisational unit archetype or typed child group under Identity's existing hierarchy | Independent departmental work belongs to the applicable domain; reporting relationships never grant permissions |
| `office` | Organisational unit/location role, linked to an appropriate location/resource record where available | Facilities, leases, occupancy and physical resources may justify separate Asset/Location/Facilities lifecycles |
| `membership` | Keep Identity membership as the only canonical person-to-group relationship | Paid/scheme membership benefits, fees, renewals or contractual rights can justify a separately owned commercial/activity domain that **references** Identity membership |
| `company-console` / similar UIs | Presentation over existing public contracts | No independently owned business data |

**Proposed tally:** seven organisation-related names resolve to archetypes/compositions by default rather than seven new capability implementations. This is a classification recommendation, not a claim that their existing catalogue rows have already been removed or their business-specific requirements fully discharged.

## 3. Boundary model

- **Identity** owns unique groups, tenants, the single-parent group hierarchy, membership IDs and lifecycle. An archetype associates its definition with public group identifiers; it does not create parallel `organisation`, `club` or `committee` membership relationships.
- **Profile** owns individual personal data; the person's personal group remains private, distinct from organisational and optional application-community groups.
- **Authorisation** owns action/resource decisions; neither a group type, position nor reporting relation automatically grants a permission. Any role proposal must pass authorised permission/approval workflows.
- **Configuration** owns its published declared settings, scopes, defaults and governance. Archetypes may contribute validated settings **only** through appropriate public extension points; avoid using generic key/value storage to re-own regulated business records.
- **Domain owners** retain governance records such as contracts, subscriptions, meeting decisions, business orders and legally required filings where those have genuinely distinct state, validation or lifecycle.
- **Publication and community** can publish selected organisational documents through the appropriate owner/Publisher contracts without transferring group or domain ownership merely by making a page public.

An **organisational archetype** is a typed definition describing permissible metadata schemas, roles requested, relevant feature choices and presentation. It is not necessarily an independently persisted capability or new Nuxt Layer. ADR-0016 permits schema-validated specialisation through existing public boundaries.

## 4. Reference cases

| Case | Model | Key constraint |
|---|---|---|
| Private sports club | Identity group of type `club`; ordinary memberships, role decisions and optional Events/Booking/Subscription domain integrations | Club type does not become a membership store or authorisation grant |
| Company with London and Berlin offices | Company archetype and child office/department groups or other explicit typed relationships | Group hierarchy has only one parent; legal entities and sites are not automatically the same as groups |
| Formal board committee | Committee group, with an independently governed decision/minutes capability only if demonstrably needed | Board reporting and committee hierarchy do not imply access inheritance |
| Member resigns from a club | Identity ends membership; domain-owned paid membership and event registrations process their own contract and retention obligations | Ending Identity membership revokes derived access, does not silently erase domain transactions |
| A shared office or matrix team | Explicit memberships/associations where necessary; never a second Identity parent merely for reporting | Cross-organisational relations are typed domain relationships, not hierarchy edges |
| Shop with warehouse | Shop/business organisation archetype plus Product, Order, Inventory and Warehouse domains | Business label does not reown operations; ready for the commerce review |

## 5. What is *not* being decided here

1. Whether `organisation-model` should ever own separately versioned archetype definitions, position records or non-authoritative reporting relations remains open. Establish real schema management, publishing/governance and lifecycle requirements first.
2. Whether `legal-entity`, `meeting-decisions`, `club-subscription`, `facilities` or other activity domains warrant independent capabilities must be decided **per domain**, not from organisation type labels.
3. The precise persistence location, versioning and activation semantics of archetype definitions remain unverified; Configuration's current root public contract alone does not establish that it can hold all these records.
4. Legal/person-data duties are not inferred from an archetype selection; relevant entity, activity, location, membership and jurisdiction must still be assessed by the responsible owners and organisation-supplied policy records.
5. No new universal hierarchy, `public` super-group, inferred permissions, mandatory organisation capability or automatic changes to existing accepted packages.

## 6. Proposal for governing follow-up

After explicit review approval, propose a **targeted, documentation-only** amendment to `architecture-rules/docs/catalogue.md`:

- Reclassify the seven names above as **archetype/composition examples by default**, rather than apparently competing candidate capabilities.
- Retain distinct candidate business domains where independent data and lifecycle are justified, with clear descriptions and exclusions.
- Preserve the existing authoritative Identity model and ADR-0016 specialisation requirements.
- Avoid deleting valid terms from the catalogue or implying that the archetypes are already implemented.

Such an amendment must have its own governing PR. This blueprint review does **not** change the catalogue.

**Next blueprint workstream:** Multi-channel Commerce and Warehouse domain ownership (Product, Pricing, Inventory, Order, Warehouse), using this reduced organisational candidate vocabulary and the approved/recorded execution and integrations boundaries.
