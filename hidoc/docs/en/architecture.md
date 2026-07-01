# Architecture

HiDoc uses a 4-layer architecture that separates regulatory source material, atomic obligation units, output mappings, and generated document definitions. Each layer has a single responsibility.

<br>

## Overview

```
Layer 1 — Regulatory     regulations table
               ↓
Layer 2 — Atom           atom_generation table
               ↓
Layer 3 — Mapping        atom_output_mapping table
               ↓
Layer 4 — Output         compliance_outputs table
```

Layers only communicate downward. The regulatory layer knows nothing about outputs. The output layer knows nothing about source clauses. This separation means adding a enw regulation requires changes only to Layers 1 and 2 — not to the generation engine.

<br>

## Layer 1 — Regulatory

**Table:** `regulations`

Stores metadata for each supported regulation: name, jurisdiction, version, and whether AI use is permitted for clause analysis.

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

Each row is a single atom — one indivisible regulatory obligation extracted from a source clause.

**Atom ID format:** `{REGULATION}_{YEAR}_ART{ARTICLE}_{SEQUENCE}`

Example: `GDPR_2016_ART13_001` — first atom extracted from GDPR Art.13.

**Key columns:**

| Column | Purpose |
|---|---|
| `id` | Unique atom identifier |
| `regulation_id` | References `regulations` table |
| `source_clause` | Original regulatory clause text |
| `obligation_type` | What the atom requires (`DISCLOSE`, `IMPLEMENT`, `MAINTAIN`, `PERFORM`, `VERIFY`, `REPORT`, `CONTRACT`) |
| `principle_context` | Design rationale — why this atom was extracted this way |
| `condition` | When this atom applies (`NULL` = always, or conditional expression) |

**Condition examples:**

- `NULL` — applies to all companies
- `if_special_category_data` — applies only if the company processes health, biometric, or other special category data
- `if_uses_consent` — applies only if the company relies on consent as a legal basis

**Current coverage:** 79 GDPR atoms (Art.5-Art.33) + 5 Korean statustory retention atoms.

<br>

## Layer 3 — Mapping

**Table:** `atom_output_mapping`

Each row maps one atom to one output, specifying the role and obligation type of that contribution.

**Row ID format:** `{ATOM_ID}__{OUTPUT_ID}`

Example: `GDPR_2016_ART33_001__DOC_IR_NOTIFY_AUTH`

**Key columns:**

| Column | Purpose |
|---|---|
| `atom_id` | References `atom_generation` |
| `output_id` | References `compliance_outputs` |
| `role` | `PRIMARY` — atom is the core source · `SUPPORTING` — atom supplements |
| `obligation_type` | `DISCLOSE` · `IMPLEMENT` · `MAINTAIN` · `PERFORM` · `VERIFY` · `REPORT` · `CONTRACT` |
| `condition` | Inherits or overrides the atom's condition |
| `injection_order` | Order in which atoms are injected within a `DOC_` output — lower values inject first |

**Current mapping count:** 147 rows.

<br>

## Layer 4 — Output

**Table:** `compliance_outputs`

Each row defines one document, artifact, procedure, control, or policy that the system can generate.

**Output ID format:** `{TYPE}_{DOMAIN}_{PPRINCIPLE}`

**Output types:**

| Prefix | Type | Examples |
|---|---|---|
| `DOC_` | Document Section | `DOC_PP_RETENTION`, `DOC_DPA_PROC_OBLIG` |
| `EVD_` | Evidence Artifact | `EVD_TRN_SECURITY`, `EVD_INC_LOG` |
| `OPR_` | Operational Procedure | `OPR_INC_NOTIFY`, `OPR_DEL_EXECUTE` |
| `TCH_` | Technical Control | `TCH_ENC_AT_REST`, `TCH_MFA_USER` |
| `CTR_` | Contractual Artifact | `CTR_DPA_STANDARD`, `CTR_SUB_PROC` |
| `POL_` | Policy Statement | `POL_SEC_ENCRYPTION`, `POL_AI_PRINCIPLES` |

**Current output count:** 97 rows.

**Design principle:** Output IDs are named by regulatory principle, not by article number. `DOC_PP_RETENTION` means "Privacy Policy section covering retention obligations" — not "Privacy Policy section from Art.X." This makes outputs reusable across regulations that share the same principle.

<br>

## Key Design Decisions

**Why atoms, not sections?**

Early versions mapped regulatory clauses directly to document sections. This broke when a single clause needed to contribute to multiple outputs (e.g., Art.5(1)(f) on security contributes to both `DOC_PP_RIGHTS` and `TCH_ENC_AT_REST`). Atoms solve this: one atom, multiple mappings.

**Why separate `compliance_outputs` from `atom_output_mapping`?**

Outputs define *what* the system can generate. Mappings define *which atoms* contribute to each output. Separating them allows outputs to be regulation-agnostic — the same `DOC_PP_RETENTION` output can receive atoms from GDPR, ISMS-P, and Korean law simultaneously.

**Why `injection_order`?**

Within a `DOC_` output, atoms inject in a specific sequence to produce coherent prose. The `injection_order` column controls this sequence without hardcoding it in the generation engine.

<br>

*← [Back to README](../../README.md)*

<br>
<br>


