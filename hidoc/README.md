# HiDoc — Hybrid Documentation

> AI generates compliance document drafts. Humans reveiw, edit, and approve.

[한국어 →](/hidoc/docs/ko/README.md)

<br>

## The Problem

Compliance documentation is broken in two ways.

First, the same obligation appears in multiple regulations. GDPR Art.28, ISMS-P, and SOC 2 all require data processor agreements — but most tools generate separate documents per regulation, forcing teams to maintain redundant, often conflicting content.

Second, compliance documents require legal precision, but drafting them from scratch demands expertise most teams don't have. THe result: documents that are either dangerously generic or prohibitively expensive to produce.

<br>

## What HiDoc Does

HiDoc takes a company's product profile and selected regulations as input, then generates a single integrated compliance document where every section is traceable to the specific regulatory obligations it satisfies.

```
Input: Company profile + selected regualtions (e.g. GDPR + ISMS-P)
Engine: Atom-based compliance mapping
Output: One integrated document — each section tagged to its source obligations
```

When a section satisfies GDPR Art.13 *and* ISMS-P simultaneously, it's generated once — not duplicated across two separate documents.

<br>

## How It Works

### The Atom Model

HiDoc decomposes each regulatory obligation into an atom — the smallest indivisible compliance requirement. Each atom carries:

- The specific regulatory clause it comes from
- The obligation type it represents (`DISCLOSE`, `IMPLEMENT`, `MAINTAIN`, `PERFORM`, `VERIFY`, `REPORT`, `CONTRACT`)
- The condition under which it applies (always, or conditionally based on company profile)
- The output it contributes to

**Current atom coverage:** 79 GDPR atoms (Art.5-Art.33) + 5 Korean statustory retention atoms.

### Output Types

Atoms map to six output types, each serving a different compliance function:

| Prefix | Type | Purpose |
|---|---|---|
| `DOC_` | Document Section | Information disclosure obligations |
| `EVD_` | Evidence Artifact | Audit-submittable records |
| `OPR_` | Operational Procedure | Required processes |
| `TCH_` | Technical Control | Implementation requirements |
| `CTR_` | Contractual Artifact | Required agreements |
| `POL_` | Policy Statement | Formal policy declarations |

### Generation Engine

The generation engine queries atoms mapped to the selected regulations, applies company profile conditions, orders sections by injection priority, and streams output to the client via Server-Sent Events (SSE).

**Generation control principles:**
- **Information Discipline** — only inject what the atom mapping explicitly authorizes
- **Mapping Completeness** — no section generates unless its source atoms are fully mapped
- **Generation Traceability** — every generated section links back to its source atoms
- **`[INFORMATION NEEDED]` protocol** — missing company profile data surfaces as explicit placeholders, never as assumptions

→ [Architecture details](docs/en/architecture.md) · [Generation flow](docs/en/how-it-works.md)

<br>

## Current Status

Phase 1 MVP complete.

- GDPR Art.5-Art.33: 79 atoms fully mapped
- `compliance_outputs` table: 97 output definitions
- `atom_output_mapping` table: 147 atom-to-output mappings
- Generation engine implemented with SSE streaming
- Privacy Policy (9 sections, 47 atoms): first full generation verified

**Next:** EU AI Act atom build · DPA generation engine · Company Profile input UI

<br>

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Database | Supabase (PostgreSQL) |
| Generation | Anthropic API (claude-sonnet-4-6) · SSE streaming |
| Architecture | 4-layer hybrid (regulatory → atom → mapping → output) |

<br>

## My Role

**Founder — Product design, architecture, and AI-assisted development**

HiDoc came from 8 years of compliance documentation work across 30+ countries in the medical device industry. The atom model, the 4-layer architecture, the output taxonomy, and the generation control principles are entirely my design. I'm not a programmer — implementation was AI-assisted throughout, using Claude as a development partner.

The goal was to build the tool I always needed but never had:
one that understands regulatory structure deeply enough to generate documents that are integrated, traceable, and auditable by design.

<br>

## Regulation Coverage Roadmap

| Regulation | Status |
|---|---|
| GDPR (EU) | ✅ Phase 1 complete — 79 atoms |
| Korean statutory retention (전자상거래법, 국세기본법, 근로기준법) | ✅ 5 atoms |
| EU AI Act | → Next |
| ISMS-P | Planned |
| SOC 2 | Planned |
| ISO 27001 / 42001 | Planned |

<br>

*Source code is maintained in a private repository. This document reflects the product architecture and current build status as of May 2026.*

<br>
<br>
