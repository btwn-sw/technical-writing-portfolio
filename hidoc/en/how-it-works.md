# How It Works: From a Selection to a Streamed Document

## What you'll get from this

The [Architecture](../en/architecture.md) page explains how HiDoc structures regulatory knowledge into four layers. This page explains what actually happens when a user runs the system — the six-step path from "I selected GDPR and filled in my company profile" to "a finished document is appearing on my screen, section by section."

By the end, you'll understand exactly what each step does, why the engine refuses to guess when information is missing, and how the streaming behavior works.

<br>

## The six-step flow, at a glance

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

Before generation starts, the user provides two things:

**Regulations:** one or more regulations the user wants to comply with — for example, GDPR plus ISMS-P together. Selecting a regulation activates every atom (recall from the [Architecture](../en/architecture.md) page: an atom is one indivisible regulatory obligation) that's mapped to it.

**Company profile:** a set of structured fields describing what the company's product actually does and how it handles data. This profile is what decides which *conditional* atoms — the ones with a `condition` other than `NULL` — actually apply to this specific company.

**Profile fields that affect what gets generated:**

| Field | Effect |
|---|---|
| `processes_special_category_data` | Activates atoms with the `if_special_category_data` condition |
| `uses_consent_as_legal_basis` | Activates atoms with the `if_uses_consent` condition |
| `has_data_processor_agreements` | Activates outputs related to data processing agreements |
| `operates_in_eu` | Activates GDPR atoms about cross-border data transfer |
| `company_name`, `dpo_contact` | Not conditions — these get inserted directly as actual values into the generated text |

<br>

## Step 2 — Atom Query

The engine looks up every atom-to-output mapping that belongs to whichever regulations the user selected in Step 1.

```sql
SELECT aom.*, ag.source_clause, ag.principle_context
FROM atom_output_mapping aom
JOIN atom_generation ag ON aom.atom_id = ag.id
WHERE ag.regulation_id IN (selected_regulation_ids)
ORDER BY aom.output_id, aom.injection_order
```

In plain terms: this pulls every atom relevant to the user's selected regulations, already grouped by which output each one belongs to, and already sorted into the order they should appear within that output.

<br>

## Step 3 — Condition Filtering

Not every atom applies to every company — that's what the `condition` column (introduced in [Architecture](../en/architecture.md) is for. This step checks each atom's condition against the company profile from Step 1.

```
atom.condition = NULL              → always inject
atom.condition = 'if_uses_consent' → inject only if profile.uses_consent = true
atom.condition = 'if_special_category_data' → inject only if profile.processes_special_category_data = true
```

Atoms whose condition doesn't match get excluded here, before generation ever happens. This is what keeps the final document specific to how *this* company actually operates, instead of reading like a generic template that happens to mention every possible obligation.

<br>

## Step 4 — Section Assembly

For each output the engine needs to generate, it gathers all the atoms that contribute to it, arranged in `injection_order` sequence — the same ordering explained in the [Architecture](../en/architecture.md) page's Layer 3 section.

Each assembled section becomes one prompt sent to the generation model:

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

Every single generation call — no exceptions — is bound by four principles. These exist specifically to prevent the model from doing anything HiDoc's whole design is meant to avoid: inventing content that isn't grounded in an actual regulatory source.

**Information Discipline**
The engine injects only what the atom mapping explicitly authorizes. If there's no atom content for something, nothing gets generated for it — the model has no room to fill a gap with a plausible-sounding guess.

**Mapping Completeness**
If an output's source atoms aren't fully mapped, that section simply doesn't generate. The system produces an explicit warning instead of a best-effort guess, because a confident-sounding but incomplete compliance document is more dangerous than an obviously missing one.

**Generation Traceability**
Every generated section records exactly which atoms contributed to it. This means anyone can trace a sentence in a finished document all the way back to the specific regulatory clause it came from.

**The `[INFORMATION NEEDED]` Protocol**
If a company profile value the generation needs is missing, the engine inserts a literal `[INFORMATION NEEDED: field_name]` placeholder into the document, rather than guessing or silently leaving it out. The document stays structurally complete — the user just needs to fill in that one blank.

<br>

## Step 6 — Streaming Output

Each section gets sent to the Claude API (model: claude-sonnet-4-6), and the response streams back to the client using Server-Sent Events (SSE) — a way of sending a response to a browser piece by piece, rather than all at once when it's fully ready.

Sections stream in the same order they appear in the final document. This means the client can render each section the moment it arrives, so the user watches the document build in real time instead of staring at a loading spinner until everything is done.

<br>

## View Modes

The same generated document can be shown in three different modes, depending on who's reading it.

| Mode | Who it's for | What's shown |
|---|---|---|
| `PUBLIC_VIEW` | End users, customers | Just the privacy policy and consumer-facing disclosures — nothing internal |
| `INTERNAL_REVIEW` | Legal and compliance staff | The full document, plus annotations showing which source clause backs each part |
| `AUDIT_SUBMISSION` | Regulators, auditors | The document with explicit obligation-traceability tags, formatted for external review |

<br>

## Tone Settings

The same content can also be generated in four different tones, depending on the intended audience — the underlying obligations don't change, only how they're worded.

| Tone | Who it's for | What it sounds like |
|---|---|---|
| `FORMAL_REGULATORY` | Regulators, legal teams, enterprise readers | Passive voice, inline clause references, legal terminology kept intact |
| `PROFESSIONAL_ACCESSIBLE` ★ default | B2B SaaS companies, startups | Active voice, minimal legal jargon, reads like professional business writing |
| `FRIENDLY_PLAIN` | Consumer apps, B2C companies | Speaks directly to "you," short sentences |
| `INDUSTRY_TECHNICAL` | Developer-facing B2B, API products | Technical terminology, specific values, written from an implementation perspective |

<br>
<br>

*← [Back to README](../README.md) · [Architecture](../en/architecture.md)*

<br>
<br>