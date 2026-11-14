# Nuxt Layers — Architecture Blueprint Review

**Status:** Non-normative architectural design and review workspace.

The repository develops proposed capability descriptions, registers findings and decisions, and preserves evidence for changes that may later be proposed to the governing architecture. It is not an implementation repository or a source of normative architectural authority.

**Governing authority:** [`nuxt-layers/architecture-rules`](https://github.com/nuxt-layers/architecture-rules), its accepted ADRs and normative standards. Verify its current `master` before making or promoting a proposal.

## Repository structure

| Directory | Responsibility | Documents |
|---|---|---|
| [`docs/`](docs/) | Working architectural documents being developed for review | [Capability Catalogue](docs/capability-catalogue.md) and [Capability Register](docs/capability-register.md) |
| [`proposals/`](proposals/) | Unaccepted design specifications and proposed architecture changes | [Register design](proposals/capability-register-design.md), membership governance and performance/rendering |
| [`reviews/`](reviews/) | Investigations, sources, comparisons, alternatives and acceptance scenarios | Capability discovery, IAM, integrations, execution, commerce and rationalisation reviews |
| [`records/`](records/) | Consolidated review history and traceable decisions | [Decisions](records/decisions.md) and [Findings](records/findings.md) |

The **working catalogue** describes what capabilities and other architectural building blocks could or should exist. It began as a copy of the governing catalogue and is expressly **not** a replacement for it.

The **working register** is an index of those catalogue entries, their classifications and evidence status. It does not infer implementation readiness from an architectural entry or create a runtime registry. Its proposed design and application discovery principles belong in [`proposals/capability-register-design.md`](proposals/capability-register-design.md).

The historical [decisions](records/decisions.md) and [findings](records/findings.md) records are deliberately retained as accumulated review evidence, not accepted ADRs. Detailed reasoning belongs with its [review](reviews/) or [proposal](proposals/).

## Review and adoption process

1. Confirm the current governing revision and the accepted ADRs and normative standards.
2. Consult other repositories **only through their public root `CONTRACT.md`**, unless wider inspection is expressly authorised.
3. Record verified evidence in a review; distinguish findings, alternatives, proposals, approval for further review and formally adopted decisions.
4. Develop working architectural changes under `docs/` and record consequences, dependencies, security/privacy effects, affected consumers and any proposed ADR/standard amendments.
5. Treat a blueprint PR as **review approval only**. A proposed change becomes normative only through the appropriate independently reviewed and accepted PR in `architecture-rules`.
6. After governing adoption, reconcile both the working documents and review records against the latest accepted source without silently discarding upstream changes.

**Existing governing requirements remain in force throughout review**, including ADR-0012's single-call composition/50-line host budget and ADR-0016's directional, jointly acyclic contract and Nuxt extension dependencies, unless properly amended.

## Documentation maintenance

Prefer small, purposeful changes. Preserve source provenance and historical review evidence, repair relative links on moves, and avoid introducing a new repository, capability, manifest or runtime service just to organise documentation.
