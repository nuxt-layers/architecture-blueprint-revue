# Platform Blueprint Review — Decision Register

**Status:** Working review record; not an ADR register. **Recorded:** 13 November 2026.

## Meaning of status

- **Verified:** requirement or fact evidenced by authoritative documents or published contracts.
- **Finding:** supported review observation, not an architectural decision.
- **Proposed:** recommended direction awaiting a distinct design decision.
- **Review approved:** agreed scope or direction for investigation, not an ADR amendment.
- **Adopted:** incorporated through the applicable governing process; record the accepted reference when this occurs.

## Approved review directions

| ID | Direction | Status | Consequence |
|---|---|---|---|
| R-01 | Reuse existing capabilities and maintain independent bounded ownership | Review approved | No theoretical rewrites or extra repositories |
| R-02 | Resolve declarative configuration, organisation and IAM boundaries through public contracts | Review approved | Begin targeted review of Configuration, Identity, Authorisation and Profile |
| R-03 | Treat libraries, presentation, integration packages and compositions as distinct implementation classifications | Review approved | Avoid making every catalogue entry a Nuxt Layer |
| R-04 | Make External Application Integrations a named workstream | Review approved | Assess configurable connections, actions, mappings, webhook and synchronisation lifecycle; no individual connectors designed yet |
| R-05 | Use a multi-channel retailer as a cross-capability reference scenario | Review approved | Test Product, Inventory, Order, Scheduler, Workflow, MQ, Integrations and reporting boundaries conceptually |
| R-06 | Keep the review in the documentation-only `nuxt-layers/architecture-blueprint-revue` workspace | Review approved | Governing `architecture-rules` stays unchanged until formal acceptance |

## Unresolved architectural decisions

| ID | Question | Preliminary direction | Governing authority / action |
|---|---|---|---|
| D-01 | Who owns runtime business definitions? | Generic configuration mechanism shared; definitions and enforcement remain domain-owned | Review Configuration/Profile/IAM contracts; ADR-0001, 0002, 0012 |
| D-02 | Is `organisation-model` an independent capability? | Prefer archetypes unless an independently governed lifecycle is demonstrated | Preserve Identity and Authorisation ownership; ADR-0016 |
| D-03 | Where do Process, Task and Workflow end? | Separate human work, business outcomes and technical execution | Review public Workflow/Scheduler/MQ contracts; ADR-0012, 0015 |
| D-04 | What does Integrations own? | Connection records, mapping and synchronisation coordination; domain data stays with its owner | Review any published Integrations contract and propose portable interfaces |
| D-05 | Who handles external secrets? | Host secret management stores secrets; Integrations handles scoped references and lifecycle | Security and operational design review required |
| D-06 | Should 50 lines remain a strict composition budget? | Do not relax without evidence and formal amendment | ADR-0012 currently mandates the ceiling |
| D-07 | How should machine-readable discovery work? | Derive from public contracts without creating competing authority | Capability Standard §4 requires separate schema approval |
| D-08 | Which open-source components should be selected? | Evaluate before bespoke implementation | Verify exact licences, commercial rights, maintenance and portability |
| D-09 | Which candidates need their own repositories? | Decide only after ownership, interface, reuse and operational justification | ADR-0001, ADR-0016 |

## Change control

Review approval does not authorise changing implementations, accepted ADRs, standards or capability contracts. Record formally adopted changes here only after their governing PRs are accepted. No new connector, capability or infrastructure implementation is authorised by this register.
