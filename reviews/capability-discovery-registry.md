# Review — Capability Discovery and Application Registry

**Status:** Blueprint proposal for review, not a governing decision or implemented API.  
**Reviewed:** 14 November 2026.  
**Governing revision:** `nuxt-layers/architecture-rules` `94210cb938f7e3945d933b903cc7a9d71e3a18e7` (`master`).  
**Authority:** `docs/capability-standard.md` §4; `docs/architecture.md`; ADR-0001, ADR-0008, ADR-0009, ADR-0012, ADR-0015 and ADR-0016.
**Public-contract sample:** `nuxt-layers/identity/CONTRACT.md`, `publisher/CONTRACT.md`, `configuration/CONTRACT.md` on `master`. No implementation files or capability manifests outside `architecture-rules` were inspected in this review.

## 1. What discovery should answer

Distinct questions require distinct sources:

| Question | Source of truth | Suitable result |
|---|---|---|
| What capability packages and versions exist? | Published package exports and development metadata | Available to select; not necessarily installed |
| What does each capability provide and require? | Owner's public `CONTRACT.md`; Nuxt `capability.json` consistent with it | Contract identifiers, compatibility, required/optional dependencies |
| What does this application compose? | Application build/deployment composition | Installed, selected, wired, unavailable or disabled |
| Which runtime features and document types are configured? | Responsible capability's public runtime registry/query | Authoritatively configured offerings, not a global copy |
| What may the current viewer do? | Domain policy and server-side Authorisation, evaluated in current tenant/group/resource context | Allowed action(s) for that request, not static permission assumptions |

**Terminology:** a discovery *catalogue* is generated descriptive metadata. It does not become a new source of domain, identity, configuration or authorisation authority.

## 2. Existing normative requirements

1. Nuxt capability layers retain `capability.json` with `provides`, `requires`, capability identifier, contract version and optionality. It must agree with `CONTRACT.md`, but never overrides it.
2. Non-Nuxt and infrastructure integration libraries are not obliged to adopt that manifest. Their exported contracts and package metadata remain authoritative; a universal manifest schema would need separate approval.
3. Direct Nuxt `extends` edges are **not** the same as service/port dependencies. Both contribute to a combined, directed, acyclic graph (ADR-0016).
4. Optional dependencies must have a documented absence mode; required providers fail closed, and a composed application's requirements must be checked. ADR-0012's one-call and 50-line integration requirements remain unchanged.
5. A capability's installation, enablement and effective user authorisation are different states.

## 3. Contract evidence and practical limits

- **Identity** exports a `./capability` manifest entry point and distinguishes operational provider requirements from optional composition-port defaults. Omitting security providers can produce refusal-only operation; installed does **not** mean operational.
- **Publisher** accepts host-registered document types, supplies allowed type/schema metadata to editors and generates permission definitions for Authorisation. A static catalogue cannot safely infer the registered types or the current user's allowed actions. Publisher's own query and decision boundary must remain authoritative.
- **Configuration** accepts host-defined setting declarations and returns scoped authorised effective values. Merely having Configuration installed does not mean a given host-declared key is present or that the viewer may see it.
- These observations are supported by public contracts; the actual completeness or consistency of particular `capability.json` files has **not** been audited.

## 4. Proposed architecture — generated application catalogue

Use an application-owned discovery **artifact**, generated at build or composition verification time from the application's actual package selection and each owner's published metadata.

Possible process:

1. Read the app's explicitly selected packages and recognised public contract/manifest metadata.
2. Validate names, contract versions, duplicates, dependency compatibility, missing required providers and declared extension edges; reject cycles across both kinds of edge.
3. Record each selected component's implementation class (capability layer, framework-independent capability, stateless library, infrastructure integration library, connector, presentation or application composition).
4. Emit a **read-only, non-authoritative inventory**: provider identity, contract identifier/version, optional and required dependencies, declared contribution surfaces, selection status, and diagnostic provenance (source path/revision).
5. Application composition connects providers according to public contracts and performs required start-up checks; a build-time inventory does not prove that operational dependencies have been successfully supplied.
6. If runtime introspection is needed, use a small authenticated, appropriately redacted diagnostic view. Never expose secrets, private provider configuration, protected routes, internal exports or privileged capabilities just because a package is installed.

**No new universal manifest or persistent registry database is recommended.** Where a framework-independent component has no machine-readable metadata, the host may supply an explicit, checked composition declaration referring back to its root public contract. Such supplementary metadata cannot redefine the owner's API.

## 5. Runtime features and dynamic contributions

A generated inventory is not a runtime business-state store.

- **Publisher:** available document types, their schemas and allowed actions are queried through Publisher's public contract.
- **Configuration:** available setting definitions and effective values remain governed by Configuration, not copied into discovery metadata as a parallel settings authority.
- **Authorisation:** server decisions are made per actor, action, resource and context. A feature catalogue MAY help render navigation, but hidden controls are not security.
- **External Integrations:** a provider connector can be installed while a tenant has no configured account or valid credentials. Discovery must not imply that a connection is operational.
- **Community and publication:** publicly accessible resources are indexed/presented through owning publication surfaces, not made world-readable merely because their provider appears in the inventory.
- **Dynamic organisational archetypes:** schemas, activation and owner-domain policies are validated at the owning capability. A generic catalogue records their **availability for composition**, not their current business state.

Where truly dynamic plug-ins are proposed, distinguish installation (trusted code/deployment change), runtime configuration (authorised data), and activation (validated state and permission). An arbitrary tenant-submitted manifest must never cause executable packages to be imported, routes registered or permissions granted.

## 6. Performance, security and deployment

- Discover once per application build/start-up where possible, not on each page request.
- Avoid repeated network round trips to assemble menus. Request-scoped feature summaries should reuse authorised domain queries and SSR hydration without stale global user state.
- Version or hash generated inventory and invalidate it on deployment changes; runtime feature state follows owner-provided update mechanisms.
- Trust only explicitly selected/verified packages; validate supplied metadata, reject collisions and missing prerequisites, and avoid dynamic execution from registry entries.
- No runtime scan of all GitHub repositories or a vendor-managed discovery service is required. The Hetzner VPS + Coolify deployment can generate the inventory during build/deploy; this recommendation assumes no new hosting infrastructure.
- Support offline, headless and non-Nuxt consumers through public contracts rather than Nuxt-specific registry APIs.

## 7. Example acceptance cases

1. Compose Identity and Publisher, with all required port providers: inventory reports their contracts; actual Publisher types and user permissions are queried from Publisher and Authorisation.
2. Omit Authorisation for a protected operation: feature is not incorrectly reported as operable; the operation still denies access.
3. Install an optional presentation and disable it: core remains available, presentation is reported absent/disabled, without duplicate routes.
4. Add a direct Nuxt extension that creates a cycle through contract dependencies: composition validation fails.
5. Install an eBay connector without a tenant account: the adapter is installed but the connection is unavailable.
6. Switch group, sign out, or revoke permission: visible navigation and actions are re-evaluated; no stale shared cache exposes protected capabilities.
7. Anonymous visitor sees world-public content only through permitted publication queries, never a global list of private features.

## 8. Decisions requested before specification

| ID | Decision | Recommendation |
|---|---|---|
| CD-01 | Independent registry capability/repository? | **No** on current evidence; prefer an app-generated descriptive inventory |
| CD-02 | Universal machine-readable manifest for all implementation classes? | **No** without separate schema approval; retain existing class-specific contract rules |
| CD-03 | Who owns the generated inventory? | Application composition/build tooling; no new persistent domain owner |
| CD-04 | How are runtime feature and permission states found? | Query each owner through its public contract, with authoritative server-side checks |
| CD-05 | Are extension and service dependencies independently tracked? | **Yes**, and validate the combined graph against ADR-0016 |
| CD-06 | Should the catalogue ever dynamically load untrusted code or grant permissions? | **No** |
| CD-07 | Which standardised metadata fields are necessary? | Limit to fields that can be validated from existing contracts; propose a schema change only if genuine evidence requires it |
| CD-08 | How does registry health show in UI? | Separate installed, configured, operational, permitted and public states; no universal availability claim |

## 9. Next workstream

After review, proceed to **External Application Integrations**. Use the same distinctions for *connector installed*, *tenant connection configured*, *provider operational* and *action authorised*. Evaluate public contracts and established OSS before creating new provider-specific repositories.

This document **does not** amend the Capability Standard or create an implementation commitment.
