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

## Native Accounting — minimum self-sufficient scope

A **naked Accounting capability** means an organisation can use it **without any third-party accounting connection**. It must be useful, internally consistent and capable of maintaining authoritative books, not just a placeholder API waiting for Xero or QuickBooks.

**Minimum native accounting functions (conceptual):**

| Area | Essential native behaviour | Explicit boundary |
|---|---|---|
| Accounting context and books | Create and administer books for an explicitly identified accounting entity, ownership context and financial basis | Identity groups are access/ownership contexts, not automatically legal entities |
| Chart of accounts | Define account codes, types, active/inactive state, permitted posting and controlled changes | No forced universal chart across jurisdictions or different entities |
| Accounting periods | Define fiscal calendar, open/closed periods and governed adjustments | Clock handles time semantics; accounting policy decides eligible periods |
| Journals and double entry | Draft, validate, authorise and post balanced entries with debit/credit sides, account and currency context, source reference, date, identity and provenance | No silent mutation/deletion of posted entries; reversal and correcting entries are distinct facts |
| Balances | Calculate account balances, opening/closing balances and trial balance with a reliable as-of date/period | No unsupported inference that balances equal cleared bank funds |
| Bank and control reconciliation | Import or enter verified statement lines, match financial transactions, track unmatched items and resolve discrepancies | Accounting does not become a payment processor or bank account credential store |
| Corrections and auditability | Controlled reversal, reclassification, adjustment, posting-history evidence, period lock and authorised access | Other capability Audit/Legal Compliance duties remain independent |
| Financial outputs | General ledger, journal listing, trial balance and simple period summaries with traceable lines | Full tax returns, payroll, statutory filings and advanced consolidation are not assumed in the initial scope |
| Data portability | Export a complete supported accounting dataset with schema/semantics and provenance; validate a proposed import before authority transfer | Import is not automatic reconciliation or approval of imported balances |

The native minimum should cover a full small-organisation ledger lifecycle: opening balances → balanced journals → corrections → reconciliation → period close → verifiable reports and export. External providers may expose richer functions, but **absence of an external connector must never leave this native cycle incomplete**.

**Financial correctness principles:** postings must balance within the applicable ledger currency/basis; amounts must have defined decimal precision, currency and rounding policy (no floating-point money); distinguish transaction, posting and effective dates; identify accounting entity and period; prevent double posting on repeated requests; record source document, correction and user/system provenance; apply authorisation, separation of duties and lawful retention. Exchange rates, tax treatment and jurisdictional rules require separately governed policy and cannot be guessed.

## Conceptual public Accounting interfaces

These are **responsibility-level contract surfaces**, not TypeScript names, HTTP routes, SDK calls, an approved `CONTRACT.md` or a new manifest. They should remain recognisable whether the selected authority is native or external.

| Contract surface | Typical operations / information | Who interprets the result |
|---|---|---|
| **Accounting authority** | Identify the approved accounting context/book, authority mode, supported provider, activated version, restrictions, health and cut-over state | Accounting; operating context must be resolved before every financial mutation |
| **Books and accounts** | Create/read book, list/validate chart, propose/change account, identify currencies and fiscal basis | Native books or selected authoritative provider through adapter |
| **Periods** | List/open/close/reopen under controlled policy, determine permitted posting period | Accounting-authorised business action, not Scheduler-owned state |
| **Journal posting** | Propose, validate, approve, post, reverse, adjust, retrieve status and original/compensating references | Selected authority; distinguish accepted request from confirmed posting |
| **Balances and financial reporting** | Get trial balance, account history and period financial views with source, freshness and reconciliation status | Selected authority, optionally cached/read-projected with explicit provenance |
| **Reconciliation** | Present statement/source records, match, explain variance, approve adjustment, record result | Accounting with authorised evidence from external statement/finance owners |
| **Import/export and migration** | Describe portable dataset, validate mappings, preview import, verify reconciliation, commit cut-over, report exceptions | Accounting controls financial meaning; Integrations supplies external transport |
| **Provider capabilities and sync health** | Determine whether a provider supports a requested operation, permissions/scopes, confirmation/read-back, retry/idempotency and last verified sync | Adapter declares provider constraints; Accounting blocks or safely limits unsupported actions |
| **Financial event exchange** | Ingest owner-validated source facts; publish accounting-owned facts such as journal posted, period closed or reconciliation completed | Source owners keep original events; MQ transports events without becoming the ledger |

At a minimum, mutation requests need **accounting context, actor/authority context, source reference, correlation and idempotency key, requested business action, and expected version/concurrency evidence where relevant**. Responses must differentiate `accepted/pending`, `confirmed`, `rejected`, `unavailable`, `conflict`, and `requires reconciliation`; never report a posting as successful merely because an API request was queued or acknowledged.

**Consumer separation:** Order, Invoice, Billing and Payments use published financial operations or emit source facts for Accounting to interpret. They must not contain Xero/QuickBooks-specific branches or write Accounting's tables directly. Accounting owns its conceptual port; Integrations and optional provider adapters implement the transport-facing side under a verified, provider-neutral contract.

### Provider differences must be explicit

Do not assume every provider implements journals, periods, contacts, accounts, reporting, currencies, corrections or bank reconciliation with identical semantics. A connector must expose a **supported-operation and constraint description**, including account scope, API and permission limits, posting model, remote read-back, rate limits and non-reversible operations.

The Accounting boundary may offer a common **portable core**, but must not invent equivalent behaviour for a provider that lacks it. An unsupported operation is refused, explicitly routed to an authorised provider-specific workflow, or supported only by a separately justified local *non-authoritative* aid; never silently posted into local books while external authority is active.

## Converting and synchronising native and external accounting

**This is a governed change of authority, not merely field-format conversion.** A connector translates provider-specific structures; Accounting interprets financial meaning, decides which ledger is authoritative and validates the transition. Conversion must work in **both directions** wherever the supported provider permits usable export/import, with explicit capability limits.

### Canonical meaning and provider mappings

Define a **provider-neutral interchange model** for the agreed portable accounting core, without assuming it can express every provider feature losslessly:

- Identity of accounting entity, book, periods, account classifications, currencies and chart mapping.
- Posted journal header and balanced lines, posting/effective dates, references, corrections/reversals and period state.
- Opening/closing balances and selected reconciliation state, statement references and source provenance.
- Counterparty/customer/supplier references **only as authorised references**; do not absorb Profile, Order or Invoice data.
- Stable internal IDs and remote IDs, mapping versions, original-source IDs, origin direction, sync watermark, date/time interpretation, rounding and currency conversion rules.

**Mapping rules** must specify account-code equivalents, unsupported account types, fiscal period differences, tax-code references, currencies/exchange rates, contact/invoice references and journal correction semantics. Record unsupported or lossy mappings as **blocking exceptions requiring explicit resolution** where financial meaning or auditability would be compromised. Keep original records/source references for traceability; do not silently reinterpret historic postings.

**Where mappings live:** Accounting owns approved financial meanings and conversion policy; Integrations/connector owns connection metadata, provider DTO translations, remote identifiers and transport checkpoints. They may reference each other by documented identifiers, but cannot both become authoritative for the same mapping fact without an approved split. No connector directly modifies internal Accounting persistence.

### Controlled cut-over — internal → external

1. **Authorise and scope:** select accounting entity/book, provider connection, effective cut-over boundary, responsible approver, supported operations and applicable privacy/legal checks.
2. **Discover and preflight:** inspect connector capabilities, remote accounts/periods/currencies, schema versions, access scopes, import/export routes, provider restrictions and outstanding local postings.
3. **Map and dry run:** map chart/accounts, financial categories, source documents, tax and currency treatment; preview a candidate migration dataset and flag incompatible/lossy records.
4. **Establish a consistent boundary:** lock or coordinate financial mutations for the transitioning books, capture a definitive journal/checkpoint snapshot and decide how in-flight source events are held or replayed. No dual authoritative writers.
5. **Transfer:** create/identify target accounts and deliver only approved importable records or opening balances. Record mapping versions, receipts, rejected lines, batch and idempotency/correlation identities. Provider-specific limits determine the available migration depth.
6. **Reconcile and sign off:** independently compare trial balances by account and currency/period, opening balances, reconciled items and unresolved differences; confirm the target's posted/accepted state by provider read-back where possible. Do not treat network success as business success.
7. **Activate authority:** after explicit approval, record an immutable authority transition and enable the external route; preserve local historic books as **read-only evidence** for the pre-cut-over period. Release held new work through the selected authority without double posting.
8. **Monitor and recover:** detect missing, duplicated, late and out-of-order events; reconcile source documents to confirmed postings and track exceptions. A failed migration must keep or restore a single known authority under a documented recovery procedure, not guess that rollback succeeded.

### Controlled cut-over — external → internal or external → another provider

Use the same gates in reverse, but obtain a **provider-verified export or equivalent supported financial evidence** first. Verify period cut-off, mappings and currency treatment; reconstruct supported journals **or** establish approved opening balances with linked immutable historical archives where full ledger transfer is impossible. Load and reconcile locally **before** authorising local posting. A change from one provider to another proceeds through the same neutral validation and reconciliation boundary; it need not perform an uncontrolled double-hop through live local books.

Do not promise reversible migration, exact preservation of remote IDs, historical transaction editing or an identical feature set across providers. Provider export/API permissions and terms may prevent full transfer. In that case present the limitations, preserve accessible evidence and require a documented decision about what can legally and correctly be imported.

### Ongoing synchronisation while external is authoritative

- **Write route:** Accounting validates the business command and delegates it to the selected connector. No parallel authoritative local write.
- **Read route:** authoritative provider read or clearly identified local projection with **last-confirmed timestamp, freshness, source and completeness**, never a projection masquerading as confirmed books.
- **Delivery:** owner-owned outbox/source evidence and connector checkpoints support at-least-once processing; idempotency, remote ID matching and reconciliation prevent duplicate financial effects. Exactly-once distributed execution must not be assumed.
- **Inbound change:** verified webhook/poll response is authenticated, tenant/account scoped, de-duplicated, mapped and passed to Accounting for business validation. Record origin to prevent echo loops.
- **Conflicts:** chosen authority wins for its books, but contradictory evidence is recorded and escalated; do not silently overwrite locally initiated pending transactions.
- **Outages / deactivation:** external authority remains selected until a governed cut-over. Record pending/unknown outcomes; never silently start local posting or claim provider success.
- **History and retention:** store only the minimum permitted local projections, audit evidence and immutable references, observing access, confidentiality, provider terms, lawful retention and erasure/hold rules.

### Conversion acceptance cases

- Native-only organisation creates chart, posts balanced journals, corrects a posting, reconciles a bank statement, closes a period and exports its ledger without Integrations configured.
- Internal → external conversion with compatible chart/journal mappings produces reconciled balances and a single post-cut-over authority.
- Connector supports opening-balance import but not journal history: transition explicitly preserves the old ledger as historical evidence and records the loss of transactional portability.
- A chart mapping changes during migration: preflight and commit detect the version conflict and refuse an unreviewed transfer.
- External provider accepts a request but confirmation is lost: Accounting reports **unknown/pending**, uses idempotency/remote reference checks and reconciliation, not duplicate posting.
- A provider webhook arrives twice or out of order: deduplication and provenance prevent duplicate financial effects.
- External system becomes unavailable: Accounting does not switch to internal books; recovery or an authorised reverse cut-over is required.
- External → native transition reconstructs verified journals or approved opening balances, reconciles and activates internal authority **only after** cut-over sign-off.
- Two accounting entities under one group have different authority modes without cross-book postings or data leakage.

## Version

**Conceptual record:** initial draft. **Implementation/package version:** not applicable / not verified.  
Once defined, accounting policies, chart structures, source event mappings and reporting schemes may need independently versioned representations; no concrete versioning schema is prescribed here.

## Availability

No `nuxt-layers/accounting` repository or root `CONTRACT.md` was found in the inspected organisation. The catalogue is a **candidate**, not software availability. Application installation, activation and operational health have **not been checked**.

## Capabilities offered (conceptual)

The **native core is the baseline**, not a fallback mock or a provider-only shell. The interfaces and accounting results described above should remain meaningful with no third-party connection. An external authority adds delegation and synchronisation, not a second parallel financial truth.

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
