# Capability Record — Accounting

**Status:** Proposed conceptual domain capability; not an implementation specification or evidence of an existing package.  
**Review sequence:** First directory alphabetically under `docs/capabilities/`, reviewed 14 November 2026.  
**Governing authority:** `nuxt-layers/architecture-rules/docs/catalogue.md`, consulted on `master` (governing definition: Accounting owns ledger, journal, periods and reconciliations, not originating sales or warehouse transactions).  
**Related blueprint evidence:** [Multi-channel commerce and warehouse review](../../../reviews/multichannel-commerce-warehouse.md), especially the separate financial lifecycles and outstanding CW-10.  
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

Accounting provides financial books and evidence suitable for reporting and control. It is distinct from the commercial and operational activity that generates the underlying facts.

## Classification

**Domain candidate.** It has a credible independent business lifecycle, distinct from Order, Billing, Invoice, Payment Gateway and Inventory. Whether it becomes a standalone capability repository remains subject to the governing capability-start criteria and this record's open questions.

## Conceptual ownership

Accounting would be responsible for:

1. **Books and accounting entities** — a clearly identified set of books, reporting context and financial period structure. Establish explicitly how an accounting entity relates to legal entities and Identity-owned groups; they need not be identical.
2. **Chart of accounts** — account definitions, classifications, lifecycle and permitted use.
3. **Journal entries** — dated, attributable financial postings, with a defined balancing invariant (for example, double-entry debit/credit balance for a supported accounting basis) and recorded source provenance.
4. **Posting and correction lifecycle** — draft/validated/posted/voided or reversed as appropriate, preserving the historical record rather than silently rewriting posted facts.
5. **Accounting periods** — boundaries, opening/closing, adjustment restrictions and close controls.
6. **Reconciliation** — explaining differences between internal ledger balances and external statements or authoritative source records, including documented resolution.
7. **Financial balances and reports** — trial balances and other authorised financial views derived from owned journals, with traceable source and period context.
8. **Financial control evidence** — approval requirements, posting restrictions and accountable changes, without substituting for a platform-wide Audit capability.

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

**Accounting books and periods:** establish approved chart of accounts and book context → accept/validate source evidence → prepare journal → approve where required → post immutably → reconcile → adjust/reverse through recorded entries → close period → preserve/export financial evidence under applicable retention rules.

A finalised journal should not be silently edited or deleted. Corrections are explicit and auditable. Whether all journals require double entry, how multiple currencies are represented, and what constitutes a legally closed period require separate accounting-policy decisions.

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

Subject to independent design: invoicing, billing, payments, sales/orders, stock valuation, banking feeds, external accounting systems, reporting, audit/evidence and export. An external accounting provider would be an **integration adapter**, not silently a replacement owner of platform journal records. The choice between internally owned books and externally authoritative accounts remains open.

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

Potential organisational/accounting policy decisions include accounting basis, fiscal year and periods, functional and transaction currencies, rounding, chart of accounts, approval thresholds, retention, reporting policies, and source-system mappings. **Configuration's** responsibility and any host-supplied policy declarations must be reviewed: business accounting policy cannot become unauthorised generic settings that override ledger invariants.

No keys, values, APIs, database type, infrastructure provider or user interface are specified here.

## Relationships to other conceptual records

- **Invoice / Billing / Payment Gateway / Subscription:** financial source evidence and separate business lifecycles.
- **Order / Inventory / Warehouse:** commercial/operational source facts without double ownership.
- **Reporting:** derived analytics and financial views should not supplant the authoritative accounting journal.
- **Groups / Organisation:** groups can own and control accounting resources through authorised relationships, but a legal/accounting entity may span or differ from an Identity group.
- **Workflow / Scheduler / Message Queue:** potential approval, period-end or event-delivery mechanisms, not accounting-state owners.

## Open architectural decisions

1. Is Accounting intended to own a full internal ledger, or is the platform initially an integration with an external accounting system? The ownership model changes substantially.
2. How do group ownership, tenant isolation, legal entities, multiple books, branches and consolidated accounts relate?
3. Which accounting basis and applicable jurisdictions are in scope? Which validations are invariants versus configurable policies?
4. Who determines and approves invoice-to-journal, refund, payout, exchange-rate, tax and stock-valuation treatments?
5. Which source facts are authoritative and how are duplicate, delayed, corrected or missing transactions reconciled?
6. Which posting and period-close operations require separation of duties, reauthentication and immutable evidence?
7. What minimum viable accounting lifecycle would establish an independent owner without prematurely building invoicing, banking or general financial ERP functionality?

## Conceptual acceptance scenarios (not implementation tests)

- Two orders from different channels produce valid financial source evidence; Accounting creates **no duplicate journals** when events are delivered twice.
- An invoice is corrected after posting; the financial history shows an explicit compensating/reversal entry rather than a silent mutation.
- A closed period rejects or routes late changes according to an explicitly approved adjustment policy.
- A person who belongs to two groups sees and operates only on appropriately authorised books; loss of membership removes authority without erasing lawful records.
- A payment-provider payout differs from an invoice or bank statement; reconciliation exposes and resolves the difference without rewriting Order or Payment Gateway records.
- An external ledger integration is unavailable; no unverified success or missing postings are concealed.

## Next decision gate

Review and approve the **conceptual ownership** and **scope** first, especially whether Accounting is the internal ledger owner or merely an adapter to an external finance system. Only then derive eventual owner contracts, required interfaces, storage and operational design. This record does not amend the governing catalogue or any ADR.
