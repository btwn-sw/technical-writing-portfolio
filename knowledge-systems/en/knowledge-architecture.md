# Building the Foundation of Knowledge Architecture: One Trusted Source

<br>
<br>

## What you'll get from this

The five failures in the [Problem Diagnosis](../en/problem-diagnosis.md) all trace back to one missing thing: no single place is designated as the trustworthy version of any given piece of knowledge. This page explains the fix — not a new tool, but a decision about which existing tool becomes the trustworthy one, and a structure that keeps it that way.

By the end, you'll understand how to organize knowledge by type, how to turn every other tool into a mirror of that one source instead of a competitor to it, how decisions get captured before they vanish, and how conflicting terminology gets resolved without freezing forever in a compromise.

<br>
<br>

## Start with one decision, not one new tool

Before building any automation, one question has to be answered first: for each type of knowledge — policies, decisions, glossary terms, product specs — which single location is the trustworthy one?

This trustworthy location is often called a **Single Source of Truth**, or **SSOT** for short. It is not a new piece of software. It's an existing tool — a wiki, a shared drive, whatever the team already uses — that gets formally designated as "the real one," combined with a commitment to keep it current and to route every decision into it.

Every structural failure described in the diagnosis comes from skipping this step: automating or connecting tools before deciding which one is actually the source of truth just produces faster, more confident inconsistency.

<br>
<br>

## Organize by type of knowledge, not by team

The trustworthy source is organized by *what kind* of knowledge something is — not by which team happens to own it. A policy that affects five different teams should live in one place, not be copied into five team-specific folders.

```
Single Source of Truth(SSOT)
│
├── decisions/     ← Formal record of confirmed decisions
├── policies/      ← Internal operating rules
├── glossary/      ← Company-wide term definitions
├── specs/         ← Product and technical specifications
├── experiments/   ← Test results and findings
├── guides/        ← Documentation for people outside the team
└── onboarding/    ← Documentation for new team members
```

Each folder has one owning team, plus a documentation owner responsible for keeping its structure and quality consistent.

<br>
<br>

## Make every other tool a mirror, not a rival

Every other place knowledge might show up — a wiki page, an internal AI assistant's answers, an onboarding checklist — becomes a *derived copy* of the source of truth, not a second version competing with it.

| Where it shows up | How it stays in sync |
|---|---|
| Company wiki | Updates automatically when the source changes |
| Product/spec reference | Regenerated automatically when the spec file changes |
| Internal AI assistant | Its search index rebuilds automatically whenever a source document updates |
| Onboarding materials | Synced automatically through the same update process |

The rule that makes this work: **when a derived copy disagrees with the source of truth, the source of truth is always right.** This isn't a technical detail — it's a policy the whole organization needs to know and trust.

<br>
<br>

## Label documents so people and AI can both tell what's current

Every document in the source of truth carries a small set of labels that tell a reader — human or AI — whether to trust it.

```yaml
status: current | outdated | draft
version: 2.1
owner: "@team-name"
last_reviewed: 2026-06-01
replaces: "policy-v1"
source_decision: "#DECISION-089"
searchable_by_ai: true | false
```

The combination of `status: outdated` and `searchable_by_ai: false` removes a document from the AI assistant's index automatically. This is what prevents Failure 5 from the diagnosis — an AI assistant confidently repeating an answer from a document that's no longer true.

<br>
<br>

## Capture decisions before they vanish

The most common and costly kind of knowledge loss isn't a deleted file — it's a decision made in a conversation that never became a document at all.

**The fix:** when a real decision is confirmed in a conversation, someone submits it into the `decisions/` folder in a consistent format, either by asking the internal AI assistant to log it, or by filling out a short guided form.

```
type: policy change | product decision | process change | deprecation
summary: [one sentence — what was decided]
reasoning: [one sentence — why]
affected documents: [what this touches]
related item: #DECISION-XXX
decided by: @name
date: YYYY-MM-DD
```

A consistent format matters because a free-form note is hard to search, hard to link to, and hard for an AI assistant to use reliably. A structured one is easy to find, reference, and act on later.

<br>
<br>

## What the internal AI assistant does — and deliberately doesn't do

The internal AI assistant is the main way people ask the source of truth a question, across whatever tools the team already uses, answering only from what's actually in the source of truth.

**What it does:**
- Answers questions about policies, terms, and processes
- Accepts decision submissions and files them into `decisions/`
- Flags when a new decision seems to affect an existing document, for a person to review
- Points out when someone uses an outdated term

**What it deliberately does not do:**
- Generate an answer that isn't grounded in the source of truth
- Decide on its own what content needs updating
- Approve or publish any documentation change

It works from a pre-built, searchable index of the source of truth rather than reading documents live — and that index rebuilds automatically the moment a document changes, so the assistant's knowledge and the source of truth never drift apart from each other.

<br>
<br>

## Resolve conflicting terms without freezing the compromise forever

Term conflicts happen naturally: a term that took hold inside the company diverges from the term everyone outside the company actually uses. The goal isn't to maintain both terms forever — that just keeps the original confusion alive in a different form. The goal is a **staged transition** to one term.

**How the right term gets chosen:** three things are weighed together — what outside partners and customers actually call it, where the broader industry is trending, and whether a regulatory or legal term already exists and takes precedence.

**How the transition happens:**

```
Conflict noticed (by a team member, the AI assistant, or a documentation review)
↓
Documentation owner reviews outside usage, industry trend, and any legal requirement
↓
Relevant team discusses and confirms the standard term
↓
Glossary updated (status: proposed → current)
↓
AI assistant scans the source of truth for every place the old term appears
↓
Documentation owner and team agree on a transition timeline
↓
Old term is marked outdated; new documents use the new term going forward
```

During the transition, documents meant for people outside the company use the new term with the old one noted in parentheses, so nobody is confused mid-transition. Internal documents can keep the old term until the transition is complete.

<br>
<br>

→ [Automation Pipeline](../en/automation-pipeline.md)

<br>
<br>