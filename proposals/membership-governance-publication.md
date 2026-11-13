# Proposal — Personal, Membership and Organisational Governance

**Status:** Consolidated blueprint proposal; not normative and not an accepted ADR.
**Evidence baseline:** `nuxt-layers/architecture-rules` `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`; governing `docs/identity.md`, `docs/privacy.md`, `docs/catalogue.md`, ADR-0002, ADR-0009, ADR-0012, ADR-0016. Reviewed published `CONTRACT.md` for Identity, Configuration, Profile, Authorisation and IAM Integration. Root `CONTRACT.md` was not retrievable for Privacy or Legal Compliance; Publisher and Theme Manager contracts have **not yet been reviewed** for this proposal.

## 1. Intent

Support individuals, organisations and optional application communities using **one established Identity group and membership model**, separately owned business records, configurable information governance and controlled private, community and world publication.

An individual's personal group remains private and independently controlled; an organisation normally controls business records created on its behalf; publication does not automatically transfer ownership; laws and valid rights constrain all decisions. The application supplies mechanisms for recording and administering policies and agreements rather than authoring legislation, asserting legal validity, or providing legal advice.

## 2. Established boundaries

| Owner | Authority |
|---|---|
| Identity | Identity subjects, tenants, groups, one private personal group per human, group hierarchy, membership IDs and lifecycle |
| Profile | Personal data, general preferences and disclosure; membership-scoped preference storage is a **proposal**, not an existing public API |
| Configuration | Platform/tenant/group settings, scope and provenance; not a generic personal-data store |
| Authorisation | Access grants and context-aware decisions; group membership alone conveys no unrestricted access |
| Clock | Date/time interpretation and validation, not preference ownership |
| Domain owners | Authoritative business resources, lifecycle, ownership and creator provenance |
| Privacy | Personal-data governance and consent/retention policy, per existing catalogue; implementation contract unverified |
| Legal Compliance | Framework for representing applicable legal obligations and constraints, per existing catalogue; implementation contract unverified |
| Publisher / publishing interfaces | Candidate for common publication and revision mechanics; detailed public contract and reuse boundaries require verification |

**Terminology:** the **group** is an Identity-owned context, not necessarily a legal person or the domain entity. A responsible legal entity/controller, group resource owner, creator, and copyright/IP rightsholder may differ. No cross-schema joins; reference opaque public IDs through documented contracts.

## 3. Personal groups and memberships

- Every human has exactly one Identity-managed personal group, with that human as its sole permanent member. It is private by default and is not the person's organisation or application-wide community.
- A person may explicitly grant read, comment, collaboration or publish access to individually selected resources, audiences or recipients without adding anyone to their personal group. Sharing never implicitly transfers ownership.
- Identity membership associates a person and an organisation/group, with its own identifier, state and effective lifecycle. **Membership scope** is a context for other owners' data and policies, not a new Identity membership system.
- Proposed Profile extension: personal overrides keyed by a valid membership's public identifier, with server-side verification and defined suspension, exit, retention and erasure behaviour. No membership-preference contract is implemented or approved today.
- Ordinary display preference proposal in an authorised, active group context: membership override (if valid) → organisational/group effective default (and permitted group/tenant lineage) → general Profile preference → deployment/declared fallback. Personal and organisational preferences may be absent independently. For a purely personal page, general Profile preferences lead.
- Configuration's current **locked deployment settings** retain their own precedence. Their interaction with personal overrides must be decided **per setting and purpose**, not by implying every lock governs all display. Operational time zones, deadlines and statutory calculation rules remain domain/policy controlled, never overridden by display preferences.
- Preference resolution should reuse trusted request context during SSR and hydration, with strict isolation and invalidation on identity/group/preference change. No broad shared personalised cache.

## 4. Business ownership and departure

- Business records produced within an authorised organisational context ordinarily remain **under that group's designated business control**, subject to applicable contract, law, intellectual-property rights and domain-specific ownership decisions. This is a proposed default, not automatic legal assignment of copyright or personal-data controllership.
- The owner domain stores explicit group/resource ownership and separate creator provenance, with references to public Identity identifiers and membership context as appropriate.
- Ending or restricting a membership revokes access derived from that membership; it **does not erase or transfer** the group's business records.
- Separation of **content**, **public attribution**, **restricted audit provenance** and **personal data in content** allows different lawful handling. Attribution may remain, be pseudonymised or genuinely anonymised as justified. Pseudonymised data may still be personal data. A lawful hold, legal obligation or established rights may prevent immediate deletion; retention must not become indefinite by default.
- Every exit route should invoke a common policy-governed lifecycle: voluntary departure, removal, expiry, account closure, suspension and organisational disposal may have different consequences. Preserve accurate audit evidence and domain records without unnecessarily retaining personal identifiers.

## 5. Legal context and organisation-supplied policy

- Organisations can record legal entities, operating/processing locations, types of membership (employee, contractor, club member), relevant activities, cited legislative sources, internal policies, agreements, opt-outs, withdrawals and effective dates, with authorship, evidence and versioning.
- Applicable obligations may depend on multiple factors: entity, individual, work location, processing location, activities, time, contracts, data-controller role and other legal connecting factors. **Membership alone never conclusively determines jurisdiction.**
- Model a rule as mandatory, conditional, exception-based or dependent on a validated agreement/opt-out where law permits. The software records the organisation's assertions and supporting evidence; it does not certify that an asserted opt-out or law is legally effective.
- Organisations may create policies within the legal constraints applicable to an activity. Policies and waivers cannot displace non-waivable rights. Individuals retain rights that apply independently of organisational membership.
- Privacy governs lawful purposes, data minimisation, notices, disclosure, consent where appropriate, retention, rights and transfers; privacy obligations can also apply to visitors and customers **without memberships**.
- Relevant domain owners enforce recorded constraints through public interfaces; failures cannot silently turn an uncertain or refused decision into permission. No universal hard-coded legal conclusions or automatic generation of enforceable contracts.
- Clarify responsibility for maintaining law/policy source versions and regional applicability; require human review/validation where necessary.

## 6. Application community and publication

- An application **may** provision an ordinary Identity community group and automatically enrol eligible verified registered accounts. It is **optional** and distinct from each person's private personal group; a group does not itself grant publish permissions.
- Publication audiences are separate from Identity group membership: **private**, **restricted** (specific grants/groups/memberships), **application public** (eligible authenticated accounts), **world public** (including anonymous visitors).
- Registered/verified eligibility and `authenticated` are not equivalent to `authorised to contribute`; publishing, editing, moderation and transfer use explicit resource/operation permissions, approved processes and any applicable legal/privacy restrictions.
- **Publication is not ownership transfer.** The original resource remains under its owning domain and group until an explicit, authorised transfer/contribution occurs. Public visibility does not automatically confer reproduction, redistribution, sublicensing or editing rights; record licensing and derivative/reuse permissions separately.
- Anonymous public visitors are an **audience**, not automatically members of an Identity `public` super-group.
- Resource types (themes, UI templates, document templates, social posts, policies) retain their respective owning-capability semantics. Shared publication mechanisms and audience vocabulary are reusable where their owning contracts justify them; neither Publisher nor a universal content store silently reowns another capability's source records.
- Publication lifecycle candidate: private draft → submission/review → published immutable revision → superseded/withdrawn/tombstoned. Collaborative contributions may be proposed, approved, reverted and attributed. Keep immutable revision identities and durable moderation/audit evidence according to applicable law and retention rules.
- Cross-group reuse is explicit: reference or pinned revision, and separately authorised copy/fork or legally permitted licensed use. A source update should not silently alter a dependent organisation's adopted revision; support change/withdrawal notices.
- Withdrawal and deletion require checking lawful obligations, published licences, external copies and dependencies. **Public does not mean permanently undeletable.** Public URLs can return an appropriate removal status/tombstone where lawful, without leaking restricted details.
- On contributor departure, retain appropriate revision history and content; handle visible attribution and identifying audit data independently under relevant policy.

## 7. Suggested end-to-end acceptance scenarios

1. **Private theme:** creator stores a theme in their personal group, grants one colleague review access, then publishes a selected revision to application public without losing ownership.
2. **Community template:** an eligible registered user contributes a template, reviewers approve it, another group pins version 2, later updates or withdrawal do not silently overwrite that dependency.
3. **World article/social post:** anonymous users can view published content, but only explicitly authorised accounts can edit or moderate; publication licence is stated separately from visibility.
4. **Organisation departure:** an employee produces a warehouse report, then leaves; derived access ends, the report remains under the group's business control, and public name/audit evidence follow separate lawful retention and attribution rules.
5. **Multi-region worker:** one person has two active memberships in different regions; different organisational display defaults and lawful local policies may apply. An opt-out is recorded with consent/evidence/effective dates, not automatically deemed legally valid.
6. **Rights request:** one person's erasure/withdrawal request affects private, group and public records; responsible owners evaluate applicable exceptions, third-party rights, published licences and audit retention; do not guarantee deletion from external copies.

## 8. Open design questions and decision gates

1. **Membership scoped personal preferences:** Profile public extension, applicability to ended/suspended memberships and lawful retention.
2. **Organisation legal context:** legal entities/controller identities, region/activity applicability and verification of asserted exceptions, including opt-outs.
3. **Publication core:** whether Publisher's public contract supplies suitable versioning, status transitions, moderation hooks, audience and reuse controls; verify its root contract before ownership decisions.
4. **Cross-capability publication:** how Theme Manager, UI templates and other domain records expose safe published snapshots or revision references without duplicated authority.
5. **Ownership transfer and licensing:** evidence, acceptance, revocation, derived works and irrevocable rights; avoid treating visibility as a licence.
6. **Departure and deletion:** consistent effects, privacy/audit/legal-hold precedence, deletion/withdrawal notices and external reuse.
7. **Application community provisioning:** eligibility, automatic enrolment, termination and moderation; no mandatory community for private applications.
8. **Operational performance:** public SSR/indexability, fast feeds and caching without exposing membership-restricted material.

**Next step:** obtain only the available root `CONTRACT.md` for Publisher and relevant resource owners, then review discoverability and publishing integration against the Capability Standard. Prepare governing ADR revisions only after explicit decisions; no implementation or normative changes are authorised by this review.
