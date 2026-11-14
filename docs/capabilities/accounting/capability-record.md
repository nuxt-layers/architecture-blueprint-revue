# Capability Record — Accounting

**Status:** Proposed conceptual domain capability; not an implementation specification or evidence of an existing package.  
**Review sequence:** First directory alphabetically under `docs/capabilities/`, reviewed 14 November 2026.  
**Governing authority:** `nuxt-layers/architecture-rules/docs/catalogue.md`, consulted on `master` (governing definition: Accounting owns ledger, journal, periods and reconciliations, not originating sales or warehouse transactions).  
**Related blueprint evidence:** [Multi-channel commerce and warehouse review](../../../reviews/multichannel-commerce-warehouse.md), especially the separate financial lifecycles and outstanding CW-10. The `nuxt-layers/integrations` repository exists, but its root `CONTRACT.md` was not retrievable in this inspection, so the connector contract and implementation remain **unverified**.  
**Evidence boundary:** An inspection of the `nuxt-layers` repository list found **no `nuxt-layers/accounting` repository**. A direct repository lookup returned 404; consequently no Accounting `CONTRACT.md`, package exports, manifests, tests or implementation were inspected. This is a finding about the checked repository, not a claim that no accounting solution or design exists elsewhere.

## Capability Name

**Accounting** — proposed identifier `accounting`. The public package name and interfaces are **not approved**.

## Status

- **Architecture:** Domain candidate in the governing catalogue.
- **Conceptual review:** Draft for discussion; no decision to create a capability repository.
- **Availability / Installed / Configured / Operational / Enabled:** **Not established**; no corresponding repository or application-specific evidence was found.
- **Authorised:** Not a global capability status. Any future accounting action would require a decision for the specific actor, group, resource and operation.
- **Implemented / completed / in progress:** **Not verifiable from a public Accounting contract**, because no `nuxt-layers/accounting` repository was found.

Do not treat a catalogue entry as completed software or a merged design review as implementation evidence.

## Purpose

Record and maintain a trustworthy financial account of an organisation's economic transactions over time: **what value was recognised, where it was allocated, in which accounting period, and how balances can be explained and reconciled**.

Accounting provides a stable financial capability regardless of which accounting system is authoritative. It must support both its own accounting books **and** an authorised externally authoritative accounting package connected through Integrations. This is a provider-selection and authority arrangement, not two mutually exclusive products. Accounting remains the platform-facing financial boundary and is distinct from the commercial and operational activity that generates underlying facts.

## Classification

**Domain candidate.** It has a credible independent business lifecycle, distinct from Order, Billing, Invoice, Payment Gateway and Inventory. Whether it becomes a standalone capability repository remains subject to the governing capability-start criteria and this record's open questions.

## Conceptual ownership

Accounting would be responsible for:

1. **Books and accounting entities** — a clearly identified set of books, reporting context and financial period structure. Establish explicitly how an accounting entity relates to legal entities and Identity-owned groups; they need not be identical.
2. **Chart of accounts** — account definitions, classifications, lifecycle and permitted use.
3. **Journal entries or provider mappings** — dated, attributable financial postings and a balancing invariant for locally authoritative books; when an external system is authoritative, validated mappings, source references and reconciliation evidence without claiming local postings are the authoritative ledger.
4. **Posting and correction lifecycle** — draft/validated/posted/voided or reversed as appropriate, preserving the historical record rather than silently rewriting posted facts.
5. **Accounting periods** — boundaries, opening/closing, adjustment restrictions and close controls.
6. **Reconciliation** — explaining differences between the selected authoritative books, local projections/pending actions and source records or statements, including documented resolution.
7. **Financial balances and reports** — trial balances and other authorised financial views derived from owned journals, with traceable source and period context.
8. **Financial control evidence** — approval requirements, posting restrictions, authority-mode changes and accountable actions, without substituting for a platform-wide Audit capability.
9. **Authority selection and continuity** — determine the accounting authority for each approved accounting context, use local books when no external accounting system is configured, and delegate accounting actions to a configured authoritative external system through Integrations. Do not run competing authoritative ledgers.

These are **conceptual candidate responsibilities**, not assertions about implemented schemas or API methods.

## Exclusions and boundaries

| Other responsibility | Owning concept | Accounting relationship |
|---|---|---|
| Sales commitments, quantities and order status | **Order** | Records financial consequences based on validated source facts; does not re-own orders |
| Charges and billing periods | **Billing** | Uses accepted charge/accounting facts as appropriate; does not determine the commercial charge lifecycle |
| Legally issued invoices, credit notes and their lifecycle | **Invoice** | Records financial postings attributable to issued documents; does not own invoice numbering/issuance |
| Provider payment authorisation, capture and refund operations | **Payment Gateway** | Records settlement consequences from verified payment/financial evidence; does not initiate payment-provider actions |
| Stock movements and reservation quantities | **Inventory** | May value stock under approved accounting policy; Inventory remains stock source of truth |
| Physical warehouse work | **Warehouse** | Does not own picking, receiving or dispatch operations |
| Prices, offers and promotions | **Pricing** | Does not compute commercial quotations |
| Legal-entity registration, statutory compliance rules and taxation policy | Applicable domain and **Legal Compliance** | Accounting applies approved relevant financial treatments, not an invented universal legal rule |
| Identity groups and permissions | **Identity / Authorisation** | Books must have explicit accountable ownership and server-authorised operations, with no implicit group membership grants |
| Infrastructure logs and generic audit infrastructure | **Logging / Audit**, if applicable | Financial journals are business records, not substitutes for unrelated security/audit logs |

Do not create a second financial source of truth in Integrations, Reporting, Order or Payment Gateway.

## Conceptual lifecycle

**Accounting books and periods:** establish accounting context and approved authority → accept/validate source evidence → prepare or delegate the authorised action → approve where required → post locally or obtain verified provider confirmation → reconcile → adjust/reverse through the authoritative system → close period → preserve/export financial evidence under applicable retention rules.

A finalised journal should not be silently edited or deleted. Corrections are explicit and auditable. This applies both to locally owned financial journals and the platform's records of externally confirmed actions. Whether all locally posted journals require double entry, how multiple currencies are represented, and what constitutes a legally closed period require separate accounting-policy decisions.

### Accounting authority modes

| State | Authority for accounting actions | Expected behaviour |
|---|---|---|
| **Internal accounting** | Accounting's own approved books | Default when **no authoritative external accounting connection is configured** for the accounting context. Accounting validates, posts, corrects and reconciles under its own policies. |
| **External accounting** | Selected external accounting package, such as Xero or QuickBooks | A properly configured, authorised connector through **Integrations** routes supported accounting actions to that system; local state is a mapped projection, evidence, or pending action, **not a competing authoritative ledger**. |
| **External configured but unavailable** | External system **retains authority** | Do not automatically post to local books. Mark requests pending, queue only if designed and safe, or refuse them. Expose failures and reconcile on recovery. |
| **Authority changing** | Explicitly governed transition | Activation, replacement and removal require an authorised cut-over, opening balances, mapping, outstanding-work reconciliation and provenance; an absent connector after previously being authoritative does not automatically make local books primary. |

**Authority is scoped** to an approved accounting entity/book and context, not inferred globally from the mere presence of an Integrations package. The platform must not permit two simultaneous writers to claim the same authoritative books. The public Accounting contract, if developed, should provide consistent business semantics in both modes without exposing Xero/QuickBooks SDK types or treating Integrations as the owner of financial records.

## Dependencies

**No package-level mandatory `requires` relationships are approved by this record.**

Conceptual obligations for any future implementation:

- Verify identity, tenancy/group context and **server-side Authorisation** for protected accounting operations through published composition contracts.
- Maintain trustworthy time/period semantics, potentially using **Clock** through its public contract; do not invent a direct package dependency.
- Receive verified, idempotently correlated financial source facts from relevant domains through documented public contracts and/or owner-published business events; avoid direct reads from other capabilities' persistence.
- Preserve accounting-owned data through its own persistence contract and schema; host provides infrastructure, never the accounting rules.
- Ensure a legal/accounting-entity mapping is explicitly defined rather than equating Identity group IDs to legal persons or books.

Required versus optional service ports, failure modes, event schemas and dependency DAG edges need design and owner-contract validation before any implementation specification.

## Optional integrations

Subject to independent design: invoicing, billing, payments, sales/orders, stock valuation, banking feeds, external accounting systems, reporting, audit/evidence and export. **Integrations** owns provider-connection configuration, transport and adapter concerns under its eventual verified public contract. A selected Xero, QuickBooks or other provider may be the authoritative accounting system for an approved accounting context; **Accounting** keeps responsibility for the platform-facing accounting semantics, authority selection, validation, reconciliation and protection against dual posting. The provider is not a second platform `accounting` capability, and Integrations must not become the owner of accounting business rules.

## Version

**Conceptual record:** initial draft. **Implementation/package version:** not applicable / not verified.  
Once defined, accounting policies, chart structures, source event mappings and reporting schemes may need independently versioned representations; no concrete versioning schema is prescribed here.

## Availability

No `nuxt-layers/accounting` repository or root `CONTRACT.md` was found in the inspected organisation. The catalogue is a **candidate**, not software availability. Application installation, activation and operational health have **not been checked**.

## Capabilities offered (conceptual)

- Maintain accounting books and charts of accounts.
- Accept validated postings with source attribution and balancing controls.
- Enforce journal posting, reversal and period-closing rules.
- Reconcile balances against separately owned evidence and statements.
- Produce authorised, traceable ledger and period views.
- Support financial corrections and retention without erasing historical provenance.

These are possible future business functions, **not documented exported functions or endpoints**.

## Configuration (conceptual)

Potential organisational/accounting policy decisions include **authority mode per accounting context**, authorised external connection identifier, migration/cut-over state, accounting basis, fiscal year and periods, functional and transaction currencies, rounding, chart of accounts, approval thresholds, retention, reporting policies, and source-system mappings. A configured connector must be explicitly activated as an accounting authority; connection existence alone is not activation. **Configuration's** responsibility and any host-supplied policy declarations must be reviewed: business accounting policy cannot become unauthorised generic settings that override ledger invariants.

No keys, values, APIs, database type, infrastructure provider or user interface are specified here.

## Relationships to other conceptual records

- **Invoice / Billing / Payment Gateway / Subscription:** financial source evidence and separate business lifecycles.
- **Order / Inventory / Warehouse:** commercial/operational source facts without double ownership.
- **Integrations / external accounting providers:** optional configured Xero, QuickBooks or comparable connectors, with explicit authority selection and governed transition. Provider capabilities and API limits must be verified before implementation.
- **Reporting:** derived analytics and financial views should not supplant whichever ledger is authoritative for the accounting context.
- **Groups / Organisation:** groups can own and control accounting resources through authorised relationships, but a legal/accounting entity may span or differ from an Identity group.
- **Workflow / Scheduler / Message Queue:** potential approval, period-end or event-delivery mechanisms, not accounting-state owners.

## Open architectural decisions

1. **Dual-mode support is the proposed requirement:** internal books are primary when no external authority has been configured; a selected external accounting package becomes authoritative upon approved activation. Define the precise accounting-context scope, supported functions and non-overlap guarantees.
2. How do group ownership, tenant isolation, legal entities, multiple books, branches and consolidated accounts relate?
3. Which accounting basis and applicable jurisdictions are in scope? Which validations are invariants versus configurable policies?
4. Who determines and approves invoice-to-journal, refund, payout, exchange-rate, tax and stock-valuation treatments?
5. Which source facts are authoritative and how are duplicate, delayed, corrected or missing transactions reconciled?
6. Which posting and period-close operations require separation of duties, reauthentication and immutable evidence?
7. What minimum viable accounting lifecycle would establish an independent owner without prematurely building invoicing, banking or general financial ERP functionality?
8. Which actions and financial objects are covered by external authority, including unsupported provider features, and how is consistent accounting semantics preserved across connectors?
9. How is internal-to-external and external-to-internal cut-over performed: mapping, opening balances, unreconciled transactions, posted history, duplicates and rollback?
10. What happens when the external connection is degraded, revoked, paused or removed, without silent local fallback or loss of accounting evidence?

## Conceptual acceptance scenarios (not implementation tests)

- Two orders from different channels produce valid financial source evidence; Accounting creates **no duplicate journals** when events are delivered twice.
- An invoice is corrected after posting; the financial history shows an explicit compensating/reversal entry rather than a silent mutation.
- A closed period rejects or routes late changes according to an explicitly approved adjustment policy.
- A person who belongs to two groups sees and operates only on appropriately authorised books; loss of membership removes authority without erasing lawful records.
- A payment-provider payout differs from an invoice or bank statement; reconciliation exposes and resolves the difference without rewriting Order or Payment Gateway records.
- A newly configured Xero/QuickBooks-like connector is explicitly approved as authoritative; Accounting routes supported actions through Integrations and does not double-post locally.
- With **no external authority ever configured**, Accounting operates its own authoritative books.
- An authoritative external connection becomes unavailable; Accounting does **not** silently become the primary ledger, and pending/failed actions remain visible and reconcilable.
- A group deliberately migrates back from external to local authority; the cut-over reconciles balances and outstanding work before internal posting is enabled, preserving provenance and historic source references.

## Next decision gate

Review and approve the **dual-mode accounting authority model** and the scope of authority per accounting context. Then evaluate how Integrations supplies provider connections, how cut-over and outages work, and which accounting behaviours belong to the common business contract. Only then derive interfaces, persistence and operational design. This record does not amend the governing catalogue or any ADR.
