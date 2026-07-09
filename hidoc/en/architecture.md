# Architecture: How HiDoc Turns Regulations Into Documents

<br>

## What you'll get from this

A single regulatory obligation — say, "you must disclose how long you retain user data" — often needs to appear in more than one place: a privacy policy, an internal security control, a contract clause. Most systems handle this by treating each output as its own separate writing task. The same obligation gets interpreted again every time it's used, and each interpretation introduces a chance for the wording to drift.

HiDoc avoids this by never writing directly from a regulation to a document. Instead, it breaks every regulation into small, reusable pieces first, then decides where each piece belongs. This page explains that structure: four layers, each with one job, so that a single piece of regulatory text can feed multiple outputs without being duplicated or reinterpreted.

<br>

## The core idea: break it down once, reuse everywhere

Take one sentence from the GDPR: "You must disclose how long you keep personal data." This requirement needs to appear in a privacy policy (to tell users), in an internal security document (to prove compliance to an auditor), and possibly in a data processing agreement (to tell a business partner).

Writing directly from that regulation to each of those three documents means writing the same requirement three separate times. Each time introduces a chance for the wording to drift — or for someone to miss one of the three.

HiDoc's solution: extract that requirement once as its own standalone unit, then connect that one unit to all three outputs. Change the requirement once, and every connected output reflects the change. This is the reasoning behind the four-layer structure below.

```
Layer 1 — Regulatory     regulations table
↓
Layer 2 — Atom           atom_generation table
↓
Layer 3 — Mapping        atom_output_mapping table
↓
Layer 4 — Output         compliance_outputs table
```

Each layer only knows about the layer directly below it. The regulatory layer has no idea what documents exist. The output layer has no idea which article of which law produced its content. This isolation is deliberate: adding a new regulation later only requires changes to Layers 1 and 2. The generation engine never has to change.

<br>

## Layer 1 — Regulatory

**Table:** `regulations`

A list of every regulation HiDoc knows about, along with basic facts about each one: its name, which jurisdiction it applies in, its version, and whether AI-assisted analysis is permitted for that regulation's clauses.

**Current regulations:**

| ID | Regulation | Jurisdiction |
|---|---|---|
| GDPR_2016 | General Data Protection Regulation | EU |
| ISMS_P_2023 | Information Security Management System — Personal | Korea |
| SOC2_2017 | Service Organization Control 2 | USA |
| EU_AI_ACT_2024 | EU Artificial Intelligence Act | EU |
| KR_ECOMMERCE | Electronic Commerce Act Art.6 | Korea |
| KR_TAX | Framework Act on National Taxes Art.85-3 | Korea |

<br>

## Layer 2 — Atom

**Table:** `atom_generation`

This is where "break it down once" actually happens. An **atom** is the smallest unit HiDoc works with — one single, indivisible obligation pulled directly from one regulatory clause. The GDPR retention example above becomes exactly one atom.

**Atom ID format:** `{REGULATION}_{YEAR}_ART{ARTICLE}_{SEQUENCE}`

Example: `GDPR_2016_ART13_001` — the first atom extracted from Article 13 of the 2016 GDPR.

**Key columns:**

| Column | What it means |
|---|---|
| `id` | This atom's unique identifier |
| `regulation_id` | Which row in `regulations` this atom came from |
| `source_clause` | The original regulatory text, word for word |
| `obligation_type` | What kind of action this atom requires — see the seven types below |
| `principle_context` | A short note explaining why this atom was extracted this way — useful later, when someone needs to understand the reasoning without re-reading the original law |
| `condition` | When this atom applies. `NULL` means always. Otherwise, a specific condition — see examples below |

An atom's `obligation_type` is always one of seven kinds:

- `DISCLOSE` — must tell someone something
- `IMPLEMENT` — must build or configure something
- `MAINTAIN` — must keep something in place over time
- `PERFORM` — must carry out an action
- `VERIFY` — must confirm or check something
- `REPORT` — must formally notify a party
- `CONTRACT` — must have a signed agreement in place

**Condition examples** — not every atom applies to every company:

- `NULL` — applies to every company, no exceptions
- `if_special_category_data` — only applies if the company processes health data, biometric data, or other legally "special category" personal data
- `if_uses_consent` — only applies if the company relies on user consent to process personal data

**Current coverage:** 79 GDPR atoms (Art.5–Art.33) + 5 Korean statutory retention atoms.

<br>

## Layer 3 — Mapping

**Table:** `atom_output_mapping`

Once an atom exists as its own unit, it needs to connect to the document(s) it should appear in. Each row represents exactly one connection: one atom pointing to one output.

**Row ID format:** `{ATOM_ID}__{OUTPUT_ID}`

Example: `GDPR_2016_ART33_001__DOC_IR_NOTIFY_AUTH` — this atom contributes to this specific output.

**Key columns:**

| Column | What it means |
|---|---|
| `atom_id` | Which atom this row connects |
| `output_id` | Which output this row connects it to |
| `role` | `PRIMARY` — this atom is the main source · `SUPPORTING` — this atom adds supplementary detail |
| `obligation_type` | Same seven types as Layer 2 — stored here so the mapping layer doesn't need to look back up to Layer 2 |
| `condition` | Usually the same as the atom's condition, but can be overridden for a specific output |
| `injection_order` | When multiple atoms feed the same output, this number decides which one comes first — lower numbers come first |

**Current mapping count:** 147 rows.

The same atom can have multiple rows here — one connecting it to a privacy policy, one to an internal security document, one to a contract clause. One atom, several outputs, zero duplication.

<br>

## Layer 4 — Output

**Table:** `compliance_outputs`

Every row is one concrete thing HiDoc can generate — a document section, a piece of evidence, an internal procedure, a technical control, a contract, or a policy statement.

**Output ID format:** `{TYPE}_{DOMAIN}_{PRINCIPLE}`

**Output types:**

| Prefix | Type | What it actually is | Examples |
|---|---|---|---|
| `DOC_` | Document Section | A section of a customer-facing or internal document | `DOC_PP_RETENTION`, `DOC_DPA_PROC_OBLIG` |
| `EVD_` | Evidence Artifact | A record kept to show an auditor | `EVD_TRN_SECURITY`, `EVD_INC_LOG` |
| `OPR_` | Operational Procedure | A process the company must carry out | `OPR_INC_NOTIFY`, `OPR_DEL_EXECUTE` |
| `TCH_` | Technical Control | A technical requirement that must be implemented | `TCH_ENC_AT_REST`, `TCH_MFA_USER` |
| `CTR_` | Contractual Artifact | A clause or agreement that must be signed | `CTR_DPA_STANDARD`, `CTR_SUB_PROC` |
| `POL_` | Policy Statement | A formal internal policy declaration | `POL_SEC_ENCRYPTION`, `POL_AI_PRINCIPLES` |

**Current output count:** 97 rows.

**Why outputs are named by principle, not by article number:** `DOC_PP_RETENTION` means "the privacy policy section covering retention obligations" — not "the section derived from Article X." The same output can receive atoms from GDPR, ISMS-P, and Korean statutory law simultaneously, without needing three near-identical outputs that all cover the same ground.

<br>

## Why the System Is Built This Way

**Why atoms instead of mapping clauses straight to document sections?**

An earlier version mapped regulatory clauses directly to document sections. It broke the first time a single clause needed to feed more than one output. Art.5(1)(f) on security needs to inform both a privacy policy section (`DOC_PP_RIGHTS`) and a technical control (`TCH_ENC_AT_REST`). A direct clause-to-section mapping can't represent "one clause, two destinations" without duplicating the clause itself. Atoms solve this cleanly: one atom, as many mappings as needed.

**Why keep `compliance_outputs` and `atom_output_mapping` as two separate tables?**

`compliance_outputs` defines what HiDoc is capable of generating — independent of any single regulation. `atom_output_mapping` defines which specific atoms feed into which specific output. Keeping them separate makes outputs regulation-agnostic: `DOC_PP_RETENTION` isn't "the GDPR retention section" — it's just "the retention section," and it receives atoms from GDPR, ISMS-P, and Korean law simultaneously, without needing to know which regulation any given atom came from.

**Why does `injection_order` exist?**

When several atoms feed the same `DOC_` output, they need to appear in a sequence that reads as coherent prose — not a list of facts in whatever order they happened to be queried. `injection_order` controls that sequence directly as a value on each mapping row, rather than hardcoded logic inside the generation engine. That keeps ordering easy to adjust without touching the engine's code.

<br>
<br>

*← [Back to README](../../README.md)*

<br>
<br>
