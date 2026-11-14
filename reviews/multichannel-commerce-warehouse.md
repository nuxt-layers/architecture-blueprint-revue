# Review — Multi-channel Commerce and Warehouse Ownership

**Status:** Blueprint proposal only; no accepted ADR, contract, repository or implementation.  
**Reviewed:** 14 November 2026.  
**Governing baseline:** `nuxt-layers/architecture-rules` `master` `94210cb938f7e3945d933b903cc7a9d71e3a18e7`, including Capability Catalogue, Capability Standard, Identity and Access, ADR-0002, ADR-0012, ADR-0015 and ADR-0016. Proposed organisational-archetype catalogue clarification is in governing [PR #34](https://github.com/nuxt-layers/architecture-rules/pull/34) and is **not yet accepted**.
**Evidence:** Previously reviewed blueprint [External Application Integrations](external-application-integrations.md), [Workflow and Execution Boundaries](workflow-process-task-execution.md) and [Organisational Candidate Consolidation](organisation-candidate-consolidation.md). No retrievable root `CONTRACT.md` for `product`, `pricing`, `inventory`, `order`, `warehouse` or `marketplace` on their expected repositories' `master`; no claim of implementation readiness.

## 1. Aim and core model

Use an organisation-controlled shop with three channels (its own website, Amazon and eBay) and one or more warehouses to test precise sources of truth, legal/resource ownership, channel publication, stock consistency, order intake, fulfilment and eventual financial records.

The **business/retailer/warehouse group is an Identity organisational context**, not a separate source for stock or commercial orders. Resource records contain an explicit owning tenant/group and creator provenance, where appropriate. An authorised individual's membership grants context, not unrestricted rights. Cross-channel external accounts are scoped connections, not business authorities.

## 2. Recommended ownership boundaries

| Candidate / platform service | Owns | Excludes |
|---|---|---|
| `product` | Product identity, catalogue definitions, variants, descriptions, bundles and their approved changes | Physical stock; price calculations; marketplace credentials |
| `pricing` | Price lists, currency/channel/customer price rules, promotion eligibility and calculated price quotations | Order lines that record an agreed price; invoice/payment records |
| `inventory` | Stock balances as projections from owned stock movements, reservations/allocations, availability rules, ownership of stock and consistency invariants | Physical warehouse work assignments; product marketing; external marketplace inventory as another source of truth |
| `order` | Sales order identity, customer/party references, accepted line price snapshots, state, fulfilment obligations, cancellation/return commitments and channel provenance | Product master; stock ledger; settlement or carrier operations |
| `warehouse` | Physical receipt, put-away, location/bin movements, picking, packing, dispatch hand-offs, operational work instructions and their recorded evidence | Canonical saleable stock balance and reservations (Inventory), customer order status (Order), carrier delivery state (Logistics) |
| `integrations` | External account connection lifecycle, provider-specific mapping and transport checkpoint/recovery coordination (proposed scope) | Product/stock/order records and their validation rules |
| `shop` | Retail application composition and optional presentation or explicitly justified shop-specific operations | Generic Product, Inventory and Order state duplicated in a shop capability |
| `marketplace` | Distinct multi-seller participation, seller onboarding/fees and marketplace-specific commercial rules *only when independent state exists* | Treating the company's Amazon/eBay **seller-channel integrations** as automatically requiring a marketplace domain |
| `billing`, `invoice`, `payment-gateway`, `accounting` | Their separate charge, invoicing, provider payment and ledger lifecycles as defined in the existing catalogue | Re-owning accepted order state or conflating a payment with an invoice or journal |
| `logistics` | Shipping, carrier coordination, delivery and tracking | Picking or canonical stock ownership |
| `workflow`, `scheduler`, `message-queue`, Temporal Gateway | Existing approved/candidate execution, human approval, timer and transport roles | Any business entity above or its authoritative persistence |

**Boundary caveat:** The above is a proposed decomposition, not a guarantee every candidate needs a Nuxt Layer/repository. Independent persistence, rule complexity and lifecycle must justify each capability before implementation.

## 3. How a sale should cross the boundaries

1. An authorised group manages a Product and its Pricing rules. Channel-specific listings refer to product/variant versions, agreed localisation and marketing payloads; the external channel has its own remote listing identifier.
2. Inventory records stock receipt and confirmed movements and computes available-to-promise quantities using documented reservation, safety-buffer and allocation rules. Define the source balance and adjustments explicitly; **do not take Amazon/eBay stock numbers as authoritative**.
3. A website purchase or marketplace webhook arrives through a verified ingress. The corresponding provider connector identifies the correct tenant/group connection and translates only a validated owner-compatible command.
4. Order deduplicates a channel-specific incoming order identifier, validates its origin/permissions and records authoritative order state and accepted monetary snapshots. Its transaction emits an owned outbox event.
5. Inventory atomically attempts the necessary reservation for that order and returns success or failure using an idempotent public operation. **Do not promise a cross-database transaction** spanning Order and Inventory. Order remains pending, accepted, unavailable or otherwise explicitly specified until the reservation result is known.
6. Message Queue transports owner outbox facts with at-least-once semantics. Scheduler/Workflow may coordinate allowed retry or approval procedures; Temporal Gateway is only technical execution infrastructure. No event delivery grants automatic permission.
7. Warehouse performs picking, packing, adjustments and dispatch hand-off, recording evidence and calling Inventory/Order through public commands to commit physical and commercial consequences. Logistics then tracks carrier-side shipment where applicable.
8. Integrations publishes permitted changes to each marketplace and records remote acknowledgements/checkpoints. It must expose stale, delayed or failed channel state instead of declaring remote inventory synchronised when unverified.
9. Invoices, settlement, refunds and ledger journal entries follow their own domains; do not infer that marketplace payout equals final invoice or bank reconciliation.

**Cross-capability state transitions:** model explicit state machines, idempotency keys, business invariants, asynchronous success/failure outcomes and compensation. A workflow/activity completion is not an authoritative Order or Inventory fact.

## 4. Critical decision — available stock across channels

One pool of stock may serve three channels. Publishing the same quantity to each creates overselling risk when orders are near-simultaneous or provider acknowledgements are delayed.

- Inventory must distinguish on-hand, reserved/allocated, unavailable/quarantined, incoming and **available-to-promise** according to documented rules. Negative stock/backorder permissions are organisational policy evaluated by Inventory, not an adapter default.
- A shared physical pool with channel-specific listing quantities is **one** Inventory source with projections and channel exposure rules, not three stock ledgers.
- Reserve against the canonical pool with concurrency control and idempotent commands. Two competing orders must not both reserve the last unit.
- Remote channel inventory is an eventually consistent projection; delays, API quotas and marketplace restrictions can prevent immediate correction. Use safety stock, publication throttling, discrepancy alerts, reconciliation and explicit channel allocation where warranted; do not promise no oversells based only on local locking.
- Cross-warehouse availability, seller-owned consignment stock and dropshipping require explicit ownership and location rules; warehouse operations must not manufacture a stock balance by independently editing it.

## 5. Product, listing and publication distinctions

- Product is the canonical catalogue record; a website listing, marketplace listing, community-published template or social post may share publication infrastructure **only where their owning contracts support it**.
- Product-specific commercial listings need channel identity, approval, mapping, market/currency/tax context, publication status, remote version, permitted images and failure diagnostics. Whether a distinct catalogue-listing owner is needed remains open; Integrations must not silently become that product-domain owner.
- Publisher's currently documented host-registered document model does not prove it can host Product, Inventory or Order records or marketplace listings. Evaluate public boundaries before reuse; avoid a universal `document` or `shop` store.
- Visibility, authorisation, ownership and licence are separate. A group's authorisation to publish a product listing to Amazon does not authorise other members to edit it or publish customer information publicly.
- Regulatory/tax and product-market obligations depend on actual goods, destination, seller entity, processing activity and jurisdiction; organisation-supplied policies are not a replacement for applicable law.

## 6. Organisation and membership across warehouses and channels

- A retail enterprise can use organisational archetypes such as company, office, department and warehouse-related group contexts. PR #34 is the targeted clarification; it does not turn warehouses into Identity groups by requirement. Physical sites/bin locations belong to the relevant Warehouse/Location domain records, with explicit optional group associations.
- A group can hold several marketplace accounts; credentials and connection administration are independently authorised. An individual's personal channel must not silently become the employer's channel.
- A staff member may have active membership in several warehouses or selling organisations; every requested operation must resolve the correct authorised tenant, group, account and resource.
- On departure, terminate membership-derived account, publishing, stock-adjustment and warehouse permissions; do not delete order transactions, stock history or lawful evidence. Reassign active picking work or approvals through their owning domain rules.
- Cross-group collaboration, sale/transfer of stock between legal entities and consignment cannot be represented as accidental inheritance of group ownership. Model explicit transfers with appropriate legal, financial and stock-movement outcomes.

## 7. Failure, recovery and isolation

| Incident | Required behaviour / evidence |
|---|---|
| Two channel orders for the final unit | At most one successful canonical reservation absent authorised oversell; other order receives explicit unavailable/backorder outcome |
| Duplicate or replayed eBay webhook | Same external order reference must not create two canonical orders; replay safely acknowledged after the first committed effect |
| Provider loses connection or rate-limits | No false synchronisation; retain owner outbox, permitted work checkpoints, backoff/retry and reconciliation visibility |
| Marketplace and local records diverge | Detection and provenance recorded; resolving differences invokes domain-approved reconciliation, not blind remote overwrite |
| Warehouse pick fails after reservation | Inventory and Order receive idempotent authorised correction/compensation through their own contracts |
| Customer cancels after dispatch | Order and Logistics coordinate an explicit return/refund process; cannot unwind physical events by deleting historical entries |
| Account reassigned or employee leaves | Revoked access to account/channel/warehouse; other organisations' connections and data remain isolated |
| Region-specific product restrictions or tax treatment | Deny/hold unvalidated sales where applicable under owner-supplied verified policy; preserve audit context |
| Temporal/MQ outage | No invented order completion; owner writes and outboxes remain recoverable; queue is not authoritative business storage |
| Product listing revoked or withdrawn | Remote delisting is an authorised asynchronous request with acknowledgement/failure; local public visibility is not remote removal |

## 8. Proposed decisions and remaining evidence

| ID | Direction / decision | Status |
|---|---|---|
| CW-01 | Preserve distinct Product, Pricing, Inventory, Order and Warehouse ownership as outlined in the governing candidate catalogue | Proposed decomposition; no approved repositories |
| CW-02 | Shop is primarily a retail application composition; no duplicate Product/Order/Inventory persistence | Proposed |
| CW-03 | Selling through Amazon/eBay is an Integrations use case, **not** itself justification for `marketplace` domain | Proposed |
| CW-04 | Inventory is authoritative for physical stock movements, reservations and available-to-promise; remote per-channel quantities are projections | Proposed with consistency acceptance test |
| CW-05 | Orders own canonical source/channel references, accepted price snapshots and commercial obligations; no global distributed transaction assumption | Proposed |
| CW-06 | Warehouse owns physical work records, not a second stock ledger | Proposed |
| CW-07 | Remote product listing lifecycle needs an explicit owner and mapping/version policy; do not silently assign it to Publisher or Integrations | **Open** |
| CW-08 | Decide precise boundaries for transfers, returns, backorders, multi-warehouse allocation, consignment and shipments | **Open** |
| CW-09 | Inbound/outbound commerce integration requires secure group-scoped connections, event idempotency, resilience and provider-lag evidence | Proposed from EI/WE reviews |
| CW-10 | Pricing, taxes, currency, invoices, payments and settlement need separate financial invariant and jurisdiction review | **Open** |
| CW-11 | Do not infer independent capability readiness from catalogue entries or missing root contracts; require a bounded state/lifecycle and public consumer contract before development | Verified governance |
| CW-12 | Keep catalogue archetype clarification PR #34 independent of this blueprint PR until merged | Change-control condition |

## 9. Architectural decision gates before implementation

1. Select a narrowly scoped commerce MVP: one legal seller, one physical pool, one SKU, own website, one external channel; demonstrate failure and replay before generalising.
2. Resolve who owns **channel listing** and **remote order mapping** data; distinguish integration transport references from business mapping.
3. Define consistency and reservation strategy across Orders, Inventory and Warehouse without cross-capability table access.
4. Specify immutable financial snapshots and ownership boundaries for Pricing, Invoice and Accounting, including tax/legal applicability.
5. Obtain and review any new root `CONTRACT.md` when owners are created; avoid migrating previous independent layers merely to satisfy a theoretical future commerce design.

**Next review:** refine remaining candidate reductions and foundational dependencies, or progress toward a consolidated blueprint decision/ADR-impact pass after commerce classification approval. No implementation or change to governing catalogue is made here.
