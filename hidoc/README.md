# HiDoc — Hybrid Documentation

> AI generates compliance document drafts. Humans review, edit, and approve.

[한국어 →](../hidoc/ko/README-ko.md)

<br>

## The problem this solves

The same compliance obligation often has to appear in more than one place — a privacy policy, a security control, a contract clause — but most tools generate a completely separate document per regulation. That forces teams to maintain multiple, often conflicting versions of the same requirement. On top of that, compliance documents demand legal precision that most teams don't have in-house, which usually means either dangerously generic boilerplate, or an expensive outside expert.

HiDoc takes a company's product profile and its selected regulations, and generates one integrated document where every section is traceable back to the specific regulatory obligation it satisfies. When a section covers both GDPR Art.13 and ISMS-P at once, it's written once — not duplicated across two separate documents.

<br>

## How it works, in brief

HiDoc breaks every regulation down into **atoms** — the smallest indivisible compliance obligation, extracted from one clause — then maps each atom to whichever output(s) it belongs in: a document section, an evidence artifact, a technical control, a contract, a policy, or an operational procedure.

```
Input:  Company profile + selected regulations (e.g. GDPR + ISMS-P)
Engine: Atom-based compliance mapping
Output: One integrated document — each section tagged to its source obligations
```

→ Full reasoning behind this structure: [Architecture](../hidoc/en/architecture.md)
→ The step-by-step generation flow, from input to streamed output: [How It Works](../hidoc/en/how-it-works.md)

<br>

## What's built so far

**Phase 1 MVP complete:**

- GDPR Art.5–Art.33: 79 atoms fully mapped, plus 5 Korean statutory retention atoms
- `compliance_outputs`: 97 output definitions across all six output types
- `atom_output_mapping`: 147 atom-to-output mappings
- Generation engine live with SSE streaming
- Privacy Policy (9 sections, 47 atoms): first full generation verified end to end

**Next:** EU AI Act atom build · DPA generation engine · Company Profile input UI

<br>

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Next.js |
| Database | Supabase (PostgreSQL) |
| Generation | Anthropic API (claude-sonnet-4-6) · SSE streaming |
| Architecture | 4-layer hybrid (regulatory → atom → mapping → output) |

<br>

## My role

**Founder — product design, architecture, and AI-assisted development**

HiDoc came out of 8 years doing compliance documentation across 30+ countries in the medical device industry. The atom model, the 4-layer architecture, the output taxonomy, and the generation control principles are entirely my own design. I'm not a programmer — implementation was AI-assisted throughout, using Claude as a development partner.

The goal was to build the tool I always needed and never had: one that understands regulatory structure deeply enough to generate documents that are integrated, traceable, and auditable by design.

<br>

## Regulation coverage roadmap

| Regulation | Status |
|---|---|
| GDPR (EU) | ✅ Phase 1 complete — 79 atoms |
| Korean statutory retention (e-commerce, tax, labor law) | ✅ 5 atoms |
| EU AI Act | → Next |
| ISMS-P | Planned |
| SOC 2 | Planned |
| ISO 27001 / 42001 | Planned |

<br>
<br>

*Source code is maintained in a private repository. This document reflects the product architecture and current build status as of May 2026.*

<br>
<br>