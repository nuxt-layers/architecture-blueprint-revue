# Review — IAM integration and declarative configuration ownership

**Status:** Evidence and unresolved questions, not approved implementation work.
**Governing baseline:** `nuxt-layers/architecture-rules` `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`.
**Evidence:** `nuxt-layers/iam-integration/CONTRACT.md` retrieved from `master`, compared with the previously retrieved root contracts for Identity, Authorisation, Profile, Configuration and Clock. Only root `CONTRACT.md` was inspected outside the governing repository.

## Verified findings

1. The `iam-integration` contract still uses `@nuxt4-layers/*`, release tag pins and a historical `nuxt4-layers/platform-architecture` URL. This conflicts with the current organisation, ADR-0008 continuous-master policy and the recently accepted capability architecture.
2. The IAM contract expressly states its one-call composition returning `resolveCaller`, `decide` and `tasks` has **not** been built. The contract provides detailed manual adapter wiring. This is documentary evidence of incomplete compliance with ADR-0012, not independently verified implementation state.
3. It describes cross-member adapters for Identity/AuthN/AuthZ/Profile, event handling, legal holds, protected data export, group-based decisions and approved changes. These are relevant integration concerns; no new generic configuration authority is implied.
4. The contract does **not** establish a current shared mechanism for resolving a person's `locale` and `zoneinfo` into Configuration defaults. Profile's own contract establishes canonical personal preferences; Configuration supports scoped defaults and warns against a competing account preference authority.
5. The current Profile and Identity contracts describe newer composition arrangements than those in the IAM suite document. Conflicts must be reconciled through approved public-contract work without guessing private implementations.

## Proposed ownership pattern (pending decision)

- **Profile** owns personal `locale` and `zoneinfo` and their disclosure permissions.
- **Configuration** owns published organisational settings, scope precedence and approval/provenance for settings it actually manages.
- **Clock** validates temporal values and formats user-visible dates/times; it does not store preferences.
- **Application or IAM composition** combines authorised preferences into request-scoped effective presentation values through published contracts. It does not persist a second canonical personal preference. Whether this belongs in `iam-integration` must be assessed against its updated contract, not assumed.
- **Domain capabilities** publish and enforce their own business policies. Generic configuration machinery must not independently grant permissions or change domain state.

## Open decisions and prerequisites

| Question | Proposed next action |
|---|---|
| What is the current valid IAM composition contract? | Reconcile `iam-integration/CONTRACT.md` against accepted ADRs and the current member contracts before relying on it |
| Should IAM composition expose one preference-resolution interface? | Specify required contract, tenancy, disclosure, precedence and SSR isolation before approving an interface |
| Which complex domain definitions can use Configuration's current value store? | Review actual schema/value limits and lifecycle requirements through public contracts; keep owners authoritative |
| How should group defaults and personal `zoneinfo` combine? | Document an explicit, authorised fallback order while preventing duplicate person-level state |
| What happens on sign-out, group switch and revocation? | Require viewer-scoped re-resolution and no shared personalised cache |
| Who manages performance for request-scoped preference resolution? | Include in proposed Performance and Rendering workstream; avoid redundant server/browser fetching |

## Recommendation

Treat the IAM contract's staleness as a **targeted public-contract reconciliation gap**, not justification for refactoring working IAM implementation. Do not introduce a new preference service or modify capability repositories during the blueprint review.

**Next review step:** agree the effective-preference ownership and resolution contract, then assess whether a separate `organisation-model` state/lifecycle remains justified. Submit any implementation or governing changes for separate approval.
