# Review — External Application Integrations

**Status:** Blueprint proposal awaiting review; not an accepted ADR, public capability contract or implementation plan.  
**Reviewed:** 14 November 2026.  
**Authority:** `nuxt-layers/architecture-rules` `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`; Capability Catalogue, Capability Standard, Architecture, Security, Privacy and ADR-0012, ADR-0015, ADR-0016.  
**Contracts inspected:** root `CONTRACT.md` for Identity, Configuration and Authorisation. Root `CONTRACT.md` for `integrations`, `message-queue` and `workflow` was not retrievable; do not infer their implementation status from that alone. No implementation repositories or non-root files inspected.

## 1. Goal

Enable a person or authorised organisation to connect, administer and revoke external-service accounts, discover permitted external operations, and exchange information with capability owners through documented interfaces. The system must be provider-neutral where useful, tenant/group isolated, resilient to unreliable providers, and compatible with organisations operating in different jurisdictions.

Examples: own web shop with Amazon and eBay sales channels; Microsoft 365 or Google Docs; external social feeds and reputation providers. A connector must not take ownership of Product, Inventory, Order, Profile, Identity or other originating records.

## 2. Ownership and classification

| Responsibility | Proposed authority |
|---|---|
| Tenants, groups, people and memberships | Identity; integrations reference verified public IDs |
| Permissions, approval and step-up | Authorisation; no grant inherited from merely having an account or group membership |
| Organisational settings and allowable integration policy declarations | Configuration, within its published generic scope; not arbitrary provider business data |
| **Connection lifecycle** | Platform `integrations` capability *candidate owned state*: provider ID, remote account identity, tenant/group controlling context, scopes granted, authorised purposes, status, consent/authorisation provenance, expiration/rotation metadata, credential reference, revocation and audit |
| Credential material | Deployment or runtime secret management and credential-owning trusted component, never source code, client runtime, logs or a second plaintext registry |
| External API adaptation | Provider-specific, optional connector behind a stable public contract; not automatically a standalone Nuxt layer |
| Domain record authority | Product, Inventory, Order, Reviews, documents etc; connector records only opaque references, external identifiers, checkpoints and permitted mappings |
| Business event production | Source domain's transactional outbox, never a connector-owned substitute |
| Delivery, scheduling and workflows | Their respective platform responsibilities, once contracts are independently verified; `integrations` owns its own synchronisation policies and checkpoints if justified |
| Privacy, compliance and territorial constraints | Applicable domain/app policies and Privacy/Legal Compliance responsibilities; no automatic inference of all applicable law from group membership |

**Important distinction:** an `integrations` *platform capability* that owns connection and sync lifecycle differs from an ADR-0015 *infrastructure integration library* (for example Temporal SDK adaptation), which must not own business persistence. Avoid using one term to conflate them.

## 3. Connection lifecycle

Proposal: `proposed → awaiting-authorisation → connected → degraded/expired → reauthorisation-required → revoked/disconnected`, with explicitly designed transitions and refusal behaviour. A permanently failed account may be archived according to policy.

- Connections are scoped to the controlling tenant/group, service account and authorised actor. An individual account may be linked to a personal group, but must never silently become an organisational connection.
- Authorise connection creation, scope expansion, token rotation, account reassignment, synchronisation changes and revocation as distinct actions. Sensitive transitions may require step-up and approval.
- Where a provider offers OAuth, use reviewed provider-supported server-side flows, state binding and CSRF protection, PKCE where applicable, smallest viable scopes, callback validation, safe refresh/revocation handling and no token disclosure. Some providers require other credential modes; these need separate policy and threat modelling.
- Secrets and tokens are never ordinary Configuration settings or visible to untrusted browsers. Connection records contain references, not reusable credentials. Token material is encrypted/secured at its rightful owner, with rotation and account unlink support.
- Distinguish `connector installed`, `connection configured`, `credentials valid`, `provider reachable`, `sync enabled`, `operation authorised` and `last sync successful`. No false 'connected' status inferred from package presence.
- Revocation must stop new deliveries, invalidate worker credentials safely, expire queued actions subject to policy and log the transition without revealing secrets. External provider revocation may fail; show pending/failed remotely while local use is denied.

## 4. Provider-neutral public contract

Define interfaces only after confirming genuine repeated needs. Candidate operations (illustrative, **not existing APIs**):

- `describeConnector()`: supported providers, capability domains, operations, supported account types, scopes, provider/API compatibility and rate-limit guidance;
- `connect()` / `reauthorise()` / `disconnect()`: secure, audited lifecycle under Authorisation;
- `checkConnection()`: bounded readiness, credential and permission diagnostics without returning secrets;
- `mapExternalReference()`: controlled source/remote ID and version associations with provenance;
- `runSync()` / `handleWebhook()`: delegated operations tied to connection, domain owner, correlation and idempotency context;
- `getSyncStatus()`: checkpoints, last completion, error classification, recovery requirements.

Provider SDK types and arbitrary remote payloads must not be forced into shared public capability interfaces. Domain owner must validate inbound data and authorise intended mutations. A connector may translate provider DTOs to owner-approved commands; it must not write directly into the owner's tables.

## 5. Synchronisation, webhook and recovery

- A connection may support outbound publication, inbound discovery, periodic reconciliation, inbound webhooks, polling or manual import. Modes and capabilities are provider-dependent.
- Outbound work starts from authoritative changes (normally owned transactional outbox) and can be delivered at least once. Avoid inferring distributed exactly-once delivery. Use persistent idempotency keys, remote IDs, version/source checkpoints, deduplication and safe retry.
- An incoming webhook has an untrusted payload: verify provider signatures/secrets and timestamps where available, tolerate retries and replay, enforce payload limits, associate it to a valid connection and tenant before processing, and send validated commands to the domain owner.
- Avoid echo loops where provider updates re-trigger the same change indefinitely; reconcile provenance and origin. Conflict resolution (local authoritative, external authoritative, manual approval) is a **domain and organisation policy**, not a universal connector decision.
- Define rate limiting, quotas, pagination, token expiry, retries with backoff/jitter, dead-letter or manual repair, provider outages, schema/API evolution and deactivation. Never promise that remote systems immediately reflect local revocation.
- Provider failures must not corrupt the source-of-truth domain; stale external status must be visible, with last-success and pending-work evidence. Do not hold database transactions across remote API calls.
- Changes to remote records may need operational approval; integrations must never bypass domain invariants for the sake of sync throughput.

## 6. Organisation, regional and publishing context

A group's selected policy provides its intended integration constraints; the affected controller/legal entity, data location, recipient, processing purpose and remote provider country may each create additional law/policy obligations. Organisational assertion does not automatically mean legal validity.

For documents, themes, templates and social resources, distinguish permission to **publish locally**, permission to **send to a specific external provider**, and permission/licence for **world-public reuse**. A connected social account does not give every member unrestricted posting rights. Domain ownership, licence, external content terms and privacy notice/consent requirements remain explicit.

Treat external service registration, data export and access through overseas operators as processing/transfer surfaces to evaluate before enabling, not as a generic checkbox that every connector satisfies. Honour applicable data-region constraints for persistent data and derived copies, including logs, queues and backups.

## 7. Multi-channel commerce acceptance case

A shop manages Product, Inventory, Pricing and Order as authoritative domain capabilities, with configured outbound channels for its website, Amazon and eBay.

| Event or action | Authoritative responsibility | Integration's responsibility |
|---|---|---|
| Product title/stock change | Product/Inventory approves source change | Map and publish permitted changes per channel, record remote acknowledgement |
| Sale received from eBay | Order validates and records order; Inventory reserves/allocates stock | Verify remote event/account, deduplicate and translate incoming order data |
| Competing orders across channels | Inventory/Order arbitrate quantities and consistency | Surface provider lag and reconcile acknowledgements without minting stock |
| Provider account token expires | Connection lifecycle records unusable state | Halt protected operations, request reauthorisation and resume safely |
| One group's account revoked | Group-owned connection invalidated | No bleed into other tenant/group channels; preserve lawful audit and remote reconciliation evidence |
| Marketplace changes product fields | Product/Publishing owner validates new mappings | Connector announces compatibility/mapping error and prevents invalid send |

Acceptance must include full re-sync after outages; duplicate and out-of-order webhooks; insufficient permission; expired tokens; multi-account provider connections; per-channel publication rights; partial remote failure; and disconnection while work is queued.

## 8. Security and operations

- Enforce OWASP ASVS Level 2 minimum and relevant Level 3 for secrets, privileged connection administration and cross-tenant processing.
- Public callbacks are narrow ingress points, not general data access; ensure signature/nonce checks, protected parsing, SSRF-safe destinations, no arbitrary user-controlled external URLs and least-privilege provider scopes.
- Keep credentials, PII and provider payload secrets out of event buses, error surfaces, diagnostics and audit logs. Retain correlation IDs and minimal approved provenance.
- Use capability-owned persistence for actual connection/checkpoint state if and when specified, with `STORAGE-REGISTER.md` and independent lifecycle/retention. No generic shared persistence service.
- Deployed application uses host-supplied secrets and resources (Hetzner VPS + Coolify baseline). Hetzner S3-compatible object storage remains for application images/files, not integration credential storage or infrastructure persistence.
- One-call host composition and 50-line budget remain applicable to a Nuxt capability; new connector packages should not multiply host boilerplate or require universal installation.

## 9. Decision register and questions

| ID | Proposed direction / question | Status |
|---|---|---|
| EI-01 | Keep `integrations` as the catalogue's Platform responsibility, with connection/sync coordination as a **candidate** independent owned lifecycle | Proposed; root contract not retrievable |
| EI-02 | Provider-specific connectors are optional adapters; no per-provider Nuxt layer by default | Proposed, aligned with Capability Standard |
| EI-03 | Identity/Authorisation retain group context and rights; domain owners retain business source of truth | Verified governing ownership; proposed integration application |
| EI-04 | Store credential references and scoped connection metadata, not secrets as ordinary Configuration values | Proposed; design the actual credential-owning boundary before implementation |
| EI-05 | Treat installed, connected, authorised, operational and synchronised as different states | Proposed, follows discovery review |
| EI-06 | Outbound owner outbox and inbound validated/idempotent commands, with checkpointed reconciliation | Proposed integration pattern |
| EI-07 | Group policy and law constrain third-party transfers and publication without claiming to decide legal validity | Proposed |
| EI-08 | Does `integrations` itself own sync checkpoints/mappings or are some domain-owned? | **Open**: distinguish connection transport mappings from domain-specific business semantics |
| EI-09 | Which small provider-neutral contract, approval flows and recovery statuses should be standardised? | **Open**; needs separate contract/design and example test |
| EI-10 | Which OSS libraries support secure OAuth, webhook, request retry, secret references and provider clients? | **Open evaluation**; compare licences, maintenance and actual provider support before selection |

## 10. Next steps and boundary

This is a review of principles and ownership. Before formalising a contract, confirm target application flows (retail marketplace, document collaboration, social publication), provider authentication details, Privacy/Legal Compliance interfaces and the distinction between platform `integrations` and infrastructure libraries.

**Follow-on architectural workstream:** Workflow / Process / Task / Scheduler / Message Queue and Temporal Gateway responsibilities, so synchronisation orchestration does not create a competing workflow engine.

No new repository, provider dependency, ADR amendment, implementation or production availability claim is authorised by this document.
