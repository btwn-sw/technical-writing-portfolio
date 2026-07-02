# Automation Pipeline: Keeping Documentation Current Without Relying on Memory Alone

<br>

## What you'll get from this

A fast-moving organization changes faster than any single person can manually track. This page describes a seven-stage pipeline that moves every knowledge update — a decision, a policy change, a spec update — through the same consistent path, using AI to handle the parts that don't need human judgment, so a human's attention goes only to the parts that do.

By the end, you'll see exactly where a person must act, where AI can act alone, and how the two stay reading the same current knowledge at every step — never one ahead of the other.

<br>

## Structure comes first, automation comes second

Automating an update process on top of a knowledge base that has no clear structure just produces disorganized results faster. This pipeline assumes the foundation from [Building the Foundation](../en/knowledge-architecture.md) — one designated source of truth — is already in place. Without it, there's nothing reliable for the automation to check against.

<br>

## The seven stages, at a glance

Every kind of update — a team decision, a policy change, a spec revision, a test result — moves through the same seven stages.

```
A change happens
(a decision, a policy update, a spec revision, a test result)
↓

Analysis (AI)
↓
Planning (AI + Technical Writer)     ← first required human decision
↓
Draft generation (AI)
↓
Review and refinement (Technical Writer)
↓
Quality check (AI + Technical Writer)
↓
Publishing (Technical Writer approves → system executes)
↓
Monitoring (AI + Technical Writer)
↓
If something's off → loop back to Stage 2 or Stage 6
```

<br>

## Stage 1 — Analysis (AI)

When a change happens, the AI assistant analyzes it and sends a short structured summary to the Technical Writer's queue.

```
type: policy change | spec update | test result | decision
summary: [one sentence — what changed]
reasoning: [one sentence — why]
affected documents: [which source-of-truth documents reference this]
related item: #DECISION-XXX
confidence: high | medium | low
※ low-confidence items need a person's judgment before moving forward
```

The AI assistant checks the change against the source of truth's index to find every document it affects. Anything it's not confident about gets set aside for a person to look at before the pipeline continues — the system doesn't guess when it isn't sure.

<br>

## Stage 2 — Planning (AI + Technical Writer)

The AI assistant proposes a work plan based on the Stage 1 analysis.

```
purpose: [what this update needs to say, and to whom]
audience: internal team | people outside the team | the AI assistant itself
work type: new document | update existing | full revision
affected documents: [in priority order]
urgency: high | medium | low
```

**The person's role here:** the Technical Writer reviews the AI's proposed plan and confirms or adjusts the scope, the document list, and the priority. This is the pipeline's first required human decision — the only point where the process cannot continue until a person acts.

<br>

## Stage 3 — Draft generation (AI)

The AI assistant writes a draft, referencing the current source of truth in real time rather than a fixed snapshot.

```
Always the same:     the AI's role definition, standard document structure,
the writing framework, the evaluation checklist
Pulled fresh each time: the current style guide
the current glossary
the current version of related documents
```

Because the second group is pulled fresh every time, the draft always reflects today's terminology and structure — not whatever was true when the AI's instructions were first written.

**A few example rules the draft must follow:**
- Flag any unfamiliar term with a `[NEEDS REVIEW]` tag instead of guessing at it
- Never invent content that isn't in the source material
- Replace any outdated term with the current glossary term, and note the substitution

<br>

## Stage 4 — Review and refinement (Technical Writer)

This stage belongs entirely to the Technical Writer.

**What gets checked:**
- Is it factually accurate against the source material?
- Does it use the approved glossary terms?
- Does it follow the style guide?
- Does this need a wider team review before it goes further?
- Are all `[NEEDS REVIEW]` tags resolved?

The AI assistant can suggest fixes in real time as the Technical Writer edits — flagging a term or an inconsistency — but it doesn't make changes on its own at this stage.

<br>

## Stage 5 — Quality check (AI + Technical Writer)

The AI assistant runs the draft against a standing evaluation checklist. The Technical Writer reviews the result and gives final approval.

**What the checklist covers:**
- Required labels are present: status, version, owner, related decision
- How well it follows the glossary
- Any style guide violations
- Whether it's structurally consistent with related documents

**Outcome:**
- Passes → Stage 6
- Doesn't pass → flagged items go back to Stage 3

Publishing never happens without the Technical Writer's approval, regardless of what the automated check says.

<br>

## Stage 6 — Publishing (Technical Writer approves → system executes)

The moment the Technical Writer approves, several things happen at once:

```
Source of truth updated
↓ (all at once)
├── AI assistant's search index rebuilds
├── Company wiki syncs
├── Onboarding materials sync
├── Product/spec reference syncs
└── Team notification sent:
"[Document name] updated to vX.X. What changed: ..."
```

The moment a person sees the notification and the moment the AI assistant can reference the new document are the same moment. Neither one is ever ahead of the other.

<br>

## Stage 7 — Monitoring (AI + Technical Writer)

**In the first two weeks after publishing:**
- Are people still asking the same question in team chat that this update was supposed to answer?
- Is anyone actually reading the document? For how long?
- Is the AI assistant citing the updated version?

**Ongoing:**
- Documents that have gone past their review date
- Outdated terms still appearing anywhere in the source of truth
- Cases where the AI assistant cited the wrong document

**When something's off, here's where it goes:**

```
Same questions keep coming up after the update → the content or its visibility is the problem → back to Stage 2
AI assistant citing the wrong document → the search index is the problem → back to Stage 6
Outdated content discovered → needs a full revision → back to Stage 2
```

<br>

## What to automate first

Three kinds of work are strong automation candidates. They're ordered by how urgent the problem is — not by how hard they are to build.

| Priority | What | Which stages | Why this order |
|---|---|---|---|
| 1 | Turning confirmed decisions into documents and archiving them | 1 · 2 · 3 | This is the most common and most damaging failure — solve it first. |
| 2 | Catching outdated or conflicting terms across documents | 7 | Prevents Failure 5 (the AI repeating stale answers) from recurring once the source of truth exists. |
| 3 | Recurring document reviews | 4 · 5 | An efficiency gain worth adding once the first two are stable. |

<br>

## Built for two readers at once: people and AI

Every stage of this pipeline is designed with two readers in mind — a person, and the AI assistant.

**Simultaneous access (Stage 6):** publishing notifies a person and updates the AI's index in the same moment. There's no window where one knows something the other doesn't.

**Dual-reader quality check (Stage 5):** a document only passes if it reads well for a person *and* is structured in a way the AI assistant can reliably index. Something that reads well but can't be indexed reliably doesn't go out.

**Built-in staleness prevention:** the `status: outdated` + `searchable_by_ai: false` label combination removes a retired document from the AI assistant's index automatically. There's no separate step anyone has to remember to do.

<br>

→ [Adoption & Measurement](../en/adoption-measurement.md)

<br>
<br>

