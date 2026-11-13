# Proposal — Performance, Rendering and Search Discoverability

**Status:** Proposal for review only; **not** an accepted ADR or normative standard.
**Evidence baseline:** `nuxt-layers/architecture-rules` `master` at `94210cb938f7e3945d933b903cc7a9d71e3a18e7`, especially `docs/presentation.md`, `docs/architecture.md`, ADR-0009 and ADR-0012.
**External guidance:** [Google Search Central — Core Web Vitals](https://developers.google.com/search/docs/appearance/core-web-vitals); [web.dev — Core Web Vitals thresholds](https://web.dev/articles/defining-core-web-vitals-thresholds); [Google Search Central — JavaScript SEO](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics). These are evidence, not automatically binding standards.

## Proposed ADR: Performance and rendering as platform requirements

### Summary

User-visible performance, efficient data fetching, smooth transitions and search discoverability are platform-wide acceptance concerns. Every application measures them; capability boundaries and security remain authoritative.

### Why

The current Presentation Standard covers semantic HTML, accessibility and structured data, but establishes no explicit loading, interaction, navigation or rendering performance acceptance model. A capability can appear correct in isolation while causing duplicated fetches, hydration waterfalls, layout shifts or slow composite pages.

### Proposed decision

1. **Measure real experiences.** Applications measure initial rendering, client navigation, interaction latency, layout stability, data request latency and error rates, separating public pages from authenticated journeys and mobile from desktop. No unmeasured claim of speed is sufficient.
2. **Public indexability.** Where a page is intended for indexing, return the main indexable content, meaningful metadata, canonical URL and permitted structured data in initial HTML wherever practical. Prefer SSR or prerendering according to freshness and deployment; do not make critical public content depend solely on browser hydration. Respect robots controls and status codes.
3. **Avoid redundant work.** Reuse request-scoped data, parallelise independent fetches, avoid serial data waterfalls and duplicate calls, and minimise unnecessary browser JavaScript, hydration, route reloads and repainting.
4. **Control freshness.** Cache only where data classification, authorisation, tenant isolation, provenance and revocation permit. Define per-resource freshness, invalidation and fail-closed behaviour. Never share private or personalised responses through public caches; security-sensitive and administration pages retain existing no-store requirements.
5. **Separate responsibilities.** Data owners publish safe query and invalidation contracts; presentation/application compositions choose SSR, prerendering, navigation and loading states; infrastructure provides transport and caching where justified. An optional cache package cannot become a competing source of truth.
6. **Prove composition performance.** Measure realistic composed pages and workflows, not merely component microbenchmarks. Regressions should be reviewed against published budgets with explicit, justified exceptions.
7. **Do not prescribe one rendering method.** Public marketing/product pages, personalised applications and operator consoles have different needs. Preserve accessibility, correct permissions and accurate data.

### Adopting it

Subject to approval, add a focused ADR to the governing architecture and a normative Performance and Rendering Standard with measurable acceptance and evidence requirements; amend the Presentation Standard by reference rather than duplicating requirements. Review impact on ADR-0009/0012 and capabilities before implementation. No governing change is authorised by this proposal.

### What follows

| Gains | Costs |
|---|---|
| Faster meaningful content and better public discoverability | Instrumentation and realistic acceptance scenarios |
| Consistent measurable quality across applications | Some pages need explicit data-freshness policies |
| Reduced unnecessary API, server and browser work | Caching carries privacy and invalidation obligations |

### Guardrails

- No performance optimisation overrides server-side authorisation, tenant isolation, access revocation, consent, accuracy, accessibility or privacy.
- Do not treat Google ranking as guaranteed by Core Web Vitals; these are user-experience metrics, not a search-ranking promise.
- Performance budgets are evaluated in field data where available, and repeatable lab tests for pre-release acceptance.

## Proposed normative standard — initial acceptance criteria

**Status:** Draft targets for review; not binding yet.

| Measure | Proposed initial target | Verification |
|---|---|---|
| Largest Contentful Paint (LCP) | Good threshold: ≤ 2.5 s | Field p75, mobile and desktop separately |
| Interaction to Next Paint (INP) | Good threshold: ≤ 200 ms | Field p75 |
| Cumulative Layout Shift (CLS) | Good threshold: ≤ 0.1 | Field p75 |
| Initial server response (TTFB) | Establish benchmark by route class; no universal numerical limit approved | Field and controlled tests |
| API/data requests | Record p50/p95 latency, request counts, error rates and duplicate/waterfall requests; route-specific budgets to be approved | Instrumented representative journeys |
| Client route navigation | Measure time to useful content and avoid unnecessary full-document reloads | Browser journey tests |
| Hydration/rendering | Detect hydration mismatch, excessive client work, layout shift and long tasks | Browser profiling and acceptance |
| Search discoverability | Indexable pages return meaningful HTML, status, canonicals, metadata and truthful structured data; authenticate/private pages are excluded | HTML response and crawler-oriented checks |
| Caching | Verify tenant/key isolation, authorisation, invalidation, no-store and revocation | Negative tests and representative journey checks |

Define device/network profiles, test percentiles, sample sizes, page classes, regression thresholds, and documented exception rules in the accepted standard. Avoid treating CI synthetic results as proof of field compliance.

**Reference application:** retailer storefront product/category/search pages and administration across own shop, eBay and Amazon. Measure both public and authenticated journeys, including API latency, cross-capability data composition, status/freshness and synchronisation updates.

**Open decisions:** final field/lab budget scheme; whether the standard applies equally to library-only and UI contributions; how to account for infrastructure versus capability latency; monitoring data retention and privacy; the approval and review policy for justified exceptions.
