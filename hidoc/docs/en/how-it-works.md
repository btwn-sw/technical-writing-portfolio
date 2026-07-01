# How It Works

This document describes the end-to-end generation flow — from user input to streamed document output.

<br>

## Overview

```
User selects regulations + enters company profile
               ↓
Engine queries atom_output_mapping
               ↓
Engine applies company profile conditions
               ↓
Engine orders atoms by injection_order
               ↓
Engine assembles prompt per output section
               ↓
Claude API generates section content
               ↓
SSE streams output to client
```

<br>

## Step 1 — User Input

The user provides two inputs:

**Regulations:** One or more regulations to comply with (e.g., GDPR + ISMS-P). Each selection activates the atoms mapped to that regulation.

**Company profile:** A structured set of fields describing the company's product and data practices. Profile fields control conditional atom injection.

**Profile fields that affect generation:**

| Field | Effect |
|---|---|
| `processes_special_category_data` | Activates atoms with `if_special_category_data` condition |
| `uses_consent_as_legal_basis` | Activates atoms with `if_uses_consent` condition |
| `has_data_processor_agreements` | Activates DPA-related outputs |
| `operates_in_eu` | Activates GDPR cross-border transfer atoms |
| `company_name`, `dpo_contact` | Injected as values into generated text |

<br>

## Step 2 — Atom Query

The engine queries `atom_output_mapping` for all atoms belonging to the selected regulations.

```sql
SELECT aom.*, ag.source_clause, ag.principle_context
FROM atom_output_mapping aom
JOIN atom_generation ag ON aom.atom_id = ag.id
WHERE ag.regulation_id IN (selected_regulation_ids)
ORDER BY aom.output_id, aom.injection_order
```

This returns all atom-to-output mappings relevant to the user's selected regulations, ordered by output and injection sequence.

<br>

## Step 3 — Condition Filtering

The engine evaluates each atom's condition against the company profile.

```
atom.condition = NULL              → always inject
atom.condition = 'if_uses_consent' → inject only if profile.uses_consent = true
atom.condition = 'if_special_category_data' → inject only if profile.processes_special_category_data = true
```

Atoms whose conditions evaluate to false are excluded from generation. This ensures the generated document reflects the company's actual data practices — not a generic template.

<br>

## Step 4 — Section Assembly

For each `compliance_output`, the engine assembles the atoms that contribute to it, in `injection_order` sequence.

Each assembled section becomes a prompt payload:

```
System: [Generation control principles]
        [Style and tone instructions]
        [Output type context: DOC_ / EVD_ / OPR_ / TCH_ / CTR_ / POL_]

User:   [Atom 1 source clause + principle_context]
        [Atom 2 source clause + principle_context]
        ...
        [Company profile values]
        [Output: generate DOC_PP_RETENTION]
```

<br>

## Step 5 — Generation Control

Every generation call enforces four principles:

**Information Discipline**
The engine injects only what the atom mapping explicitly authorizes. No atom content, no injection. The model cannot generate content beyond what the atoms provide.

**Mapping Completeness**
A section does not generate if its source atoms are not fully mapped. Incomplete mapping produces an explicit warning, not a best-guess output.

**Generation Traceability**
Every generated section records which atoms contributed to it. This creates an audit trail from output back to regulatory clause.

**`[INFORMATION NEEDED]` Protocol**
When a company profile value is missing, the engine inserts a `[INFORMATION NEEDED: field_name]` placeholder rather than inferring or omitting the value. The document remains structurally complete — the user fills in the blanks.

<br>

## Step 6 — Streaming Output

The engine sends each section to the Claude API (claude-sonnet-4-6) and streams the response back to the client via Server-Sent Events (SSE).

Sections stream in document order. The client renders each section as it arrives — the user sees the document being built in real time rather than waiting for full generation to complete.

<br>

## View Modes

The generated document supports three view modes depending on the intended audience:

| Mode | Audience | Content |
|---|---|---|
| `PUBLIC_VIEW` | End users, customers | Privacy policy, consumer-facing disclosures |
| `INTERNAL_REVIEW` | Legal, compliance team | Full document with source clause annotations |
| `AUDIT_SUBMISSION` | Regulators, auditors | Document with obligation traceability tags |

<br>

## Tone Settings

The generation engine supports four tone profiles:

| Tone | Target audience | Style |
|---|---|---|
| `FORMAL_REGULATORY` | Regulators, legal teams, enterprise | Passive voice, inline clause references, legal terminology preserved |
| `PROFESSIONAL_ACCESSIBLE` ★ default | B2B SaaS, startups | Active voice, minimal legal jargon, business professional |
| `FRIENDLY_PLAIN` | Consumer apps, B2C | Second-person address, short sentences |
| `INDUSTRY_TECHNICAL` | Developer-facing B2B, API products | Technical terminology, specific values, implementation perspective |

<br>

*← [Back to README](/hidoc/README.md) · [Architecture](../en/architecture.md)*

<br>
<br>
