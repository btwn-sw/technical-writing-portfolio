# Developer Portal — Information Architecture Design

A case study in designing an information architecture for an Ads API developer portal. The context: a commerce platform opening its advertising API to external developers for the first time.

[한국어 →](../ia-design/ko/README.md)

<br>

## Context

Commerce platforms hold a category of data that pure ad platforms don't: real transaction and behavioral signals generated within their own ecosystem. When a commerce platform opens an advertising API to external developers, this data becomes the primary differentiator — but only if the developer portal is designed to surface it.

This case study covers the IA design for a commerce platform's first external-facing advertising API developer portal. The platform was exposing its ad infrastructure to agencies, advertisers, and measurement partners for the first time.

The design challenge was about building an IA that would scale cleanly as the portal grew, while ensuring the platform's data advantage was visible in the structure from day one — not added as an afterthought.

<br>

## Design Rationale

### Audience Analysis

The platform's existing developer documentation was designed for internal developers already familiar with the ecosystem — objects and schemas first, with code blocks appearing narrow and secondary. This structure works for developers who already know the domain.

External advertising developers work differently. They arrive with a specific integration goal and need to validate their understanding through running code as quickly as possible. Scrolling through object definitions before reaching executable examples increases time to first success.

### Structure Decision

After reviewing Payabli, Stripe, and Eventbrite developer portals, the API playground format (Payabli) best matches this audience's needs: a single viewport where parameters, documentation, and live execution coexist.

| Portal | Strength adopted | Reason |
|---|---|---|
| Payabli | API playground layout | Single viewport: docs + params + live execution |
| Stripe | Toggle-style object pattern | Collapsible nested objects reduce cognitive load as content scales |
| Eventbrite | Section-based IA | Clear separation of tutorial, how-to, reference, explanation |

The decision: adopt a **code-first, single-viewport layout** — because it removes the step between "I read the parameter" and "I tested the parameter."

<br>

## Information Architecture

The sitemap is designed around two principles: the external developer's journey from discovery to production, and expandability as new APIs and SDKs are added.

```
Ads API Developer Portal
│
├── Get Started                  # Authentication · Quick Start · Integration overview
├── Concept Guides               # Ad object hierarchy · Campaign types
│                                # Conversion tracking · Attribution models · Ad policies
├── Integration Guides           # Account setup · Ad operations
│                                # Performance management · Conversion tracking
├── API Reference                # Account · Campaign · Performance Reports
│                                # Conversion Events
├── SDK                          # JavaScript/Node.js · Python · Java · PHP · Ruby
├── Advanced                     # Audiences · Platform-native data integration
│                                # Creative · Optimization · Experiments
├── Common Reference             # Error codes · Metric definitions
│                                # Rate limiting · Pagination
└── Changelog                    # RSS · Breaking change notices
```

**Key structural decisions:**

- **Error codes and metric definitions live in Common Reference, not in individual API pages:** Repeating definitions per endpoint creates a maintenance problem: when a definition changes, every page that contains it must be updated. A single source of truth for shared reference material is essential at any scale.

- **Concept Guides precede Integration Guides:** External developers unfamiliar with ad platform concepts (attribution models, conversion windows, ad object hierarchy) cannot complete an integration guide without this foundation. Skipping concept documentation reduces time-to-first-integration for experienced developers, but increases support burden for everyone else.

- **Changelog is a top-level section:** API changes are not footnotes. External developers who have already integrated need a reliable, subscribable signal when the API changes. RSS support and explicit Breaking Change notices are required from day one, not added later.

- **Advanced section is expandable by design:** Platform-native data integration, creative tools, and optimization features are isolated in one section so the core integration path (Get Started → Integration Guides → API Reference) stays clean as the portal grows.

<br>

## Feature Priorities

| Priority | Feature | Reason |
|---|---|---|
| 1 | **API Playground** | Reduces time to first successful integration. Developer can confirm parameters → execute → verify response without leaving the portal or setting up Postman. |
| 2 | **Platform-native data integration** | The platform's proprietary user data enables targeting that external ad platforms cannot replicate. This is the portal's primary differentiation — it belongs in the IA from day one, not added as an afterthought. |
| 3 | **Toggle-style object pattern** | Nested objects collapsed by default. Cognitive load stays manageable as the API surface grows. Adopted from Stripe's pattern, which handles complex schemas without burying the code. |
| 4 | **Experiment management** | Agencies running A/B campaigns need structured measurement. Meta and Google both treat this as a top-level section — not a buried how-to. |

**What was deprioritized and why:**

Search and versioning were considered but not prioritized for the initial portal. Search becomes essential once the portal has more than ~15 pages. Versioning is required once a breaking change is made. Both can be added incrementally without restructuring the IA.

<br>

## Documentation Update Process

`openapi.yaml` is the single source of truth for the API specification. When the spec file updates, documentation syncs automatically. This eliminates an entire class of documentation staleness.

But the goal is not just keeping documentation current. The goal is ensuring that from the moment a spec changes to the moment an external developer successfully adapts, all three parties stay aligned:

```
Engineer (technical intent)  →  TW (impact judgment · translation)  →  External developer (integration goal)
```

**The process:**

```
Spec change
  → TW: PR review · Breaking Change assessment · Migration guide authoring
  → Internal pre-distribution (customer success · support teams)
  → External notice (Changelog · email · Breaking Change dedicated notice)
  → Deploy
  → Monitor: error logs · support tickets · documentation exit rate
  → If failure signal detected → revise documentation → update quality criteria
```

**Why TW reviews the PR, not just the merged spec.** 

The PR is where intent is visible. After merge, the diff shows what changed — but not why, and not what it means for developers who have already integrated. TW involvement at PR stage captures that context before it disappears into commit history.

**Documentation is not complete at deploy time.** 

External developer responses after release are the signal for what the documentation missed. The monitoring step is not optional — it connects developer responses back to documentation improvement, completing the cycle.

<br>

## Reference Portals

| Portal | URL | What was analyzed |
|---|---|---|
| Payabli | docs.payabli.com | API playground layout · single-viewport execution |
| Stripe | docs.stripe.com | Toggle-style nested object pattern |
| Eventbrite | eventbrite.com/platform/api | Section-based IA · audience-type navigation |
| Google Ads API | developers.google.com/google-ads | Experiment management as top-level section |
| Meta Marketing API | developers.facebook.com/docs/marketing-apis | Campaign hierarchy · breaking change notices |

<br>
<br>

*This is a portfolio case study. The API specification used as context was a fictional spec provided as part of a technical writing assessment. All design decisions are the author's own.*

<br>
<br>
