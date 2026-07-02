# Adoption & Measurement: Getting People to Actually Use It, and Knowing If It's Working

<br>

## What you'll get from this

A trustworthy source of documentation that nobody uses isn't documentation — it's just storage. This page answers two separate questions: how do you get people to actually use the system day to day, and how do you know if it's working at all?

By the end, you'll see the specific behaviors this design tries to change, the two predictable objections people raise early on, and thirteen concrete measurements that tell you whether the system is actually fixing the five failures from the [Problem Diagnosis](../en/problem-diagnosis.md) — not just whether it exists.

<br>

## Why adoption usually fails

Most documentation systems don't fail because they're poorly built. They fail because they're slower than the alternative. If asking a coworker in team chat takes 30 seconds and searching the documentation takes 3 minutes, people will keep asking the coworker — every time, without exception.

Nobody adopts a system because it was announced in a meeting or written into a policy. People adopt a system because, in the moment they need an answer, it's genuinely the fastest way to get one.

<br>

## Trust has to come before behavior change

Before anyone changes their habits, one condition has to be true first: people need to believe the source of truth is actually accurate. A system that's sometimes wrong, sometimes outdated, or sometimes contradicted by the AI assistant will not be trusted — and something people don't trust, they won't use.

Trust isn't built by announcing the system is ready. It's built by repetition — by people experiencing, over and over, that the source of truth was faster and more accurate than asking a person. The goal for the first month isn't full adoption everywhere. It's giving people enough of those small, repeated experiences to shift their default habit.

<br>

## Three design principles for making it easy

**Simple:** no multi-step process to remember. One consistent way to interact with the AI assistant, the same everywhere it's used.

**Natural:** the system meets people where they already work, instead of asking them to adopt a new tool. If the team already lives in team chat, that's the entry point — not a separate portal.

**Immediate:** when someone submits a decision or asks a question, they see a response right away. A system that responds slowly teaches people not to bother using it.

<br>

## Designing the specific behavior change, role by role

Each role has a specific habit today, and a specific habit this system is trying to build instead. The shift isn't left to chance — each one is paired with something concrete that makes the new habit easier than the old one.

| Role | Habit today | Habit this builds | What makes the shift stick |
|---|---|---|---|
| Product manager | Scrolls chat history looking for a past decision | Searches the `decisions/` folder | The speed difference is obvious the first time they try it |
| Sales / customer-facing | Messages an engineer directly when unsure of current policy | Checks the "last reviewed" date on the wiki page | The date is visible on every single document, without having to ask |
| New hire | Follows onboarding material without checking if it's current | Checks the version and date before relying on it | Version and date are shown prominently, not buried |
| Engineer | Uses whatever term their team happens to use | Checks the glossary | Outdated terms are flagged automatically the moment they're typed |
| Data analyst | Isn't sure whether to trust the AI assistant's answer | Checks the source document and its version | The AI assistant shows its source automatically, with every answer |

<br>

## Two objections, addressed directly

Two predictable objections come up early. Both need a real answer, not a policy statement.

**"This has too many steps to remember."**
Solved by keeping the interaction as simple as possible — one entry point opens a short guided form. Nothing to memorize.

**"I don't believe the documentation will actually get updated."**
Solved by making the first update visible and fast. When someone submits a decision and sees it reflected in the source of truth within hours — not days — the system earns credibility through what people actually see happen, not through a promise.

<br>

## Rolling this out in stages, not all at once

Trying to launch everything at once is a way to lose trust before you've earned any. A staged rollout builds it gradually instead.

| Stage | Timeframe | What's included | Why this order |
|---|---|---|---|
| 1 | Month 1 | Capturing decisions from team chat only | This is the highest-impact failure to fix, and early wins build trust fastest |
| 2 | Months 2–3 | Building out the glossary + notifying other teams | Resolves the terminology problem, and extends trust to more roles |
| 3 | Months 3–6 | Full source of truth + syncing every channel | By now the foundation is trusted, so the full system can be relied on |

<br>

## Measurement: how you know it's actually working

The system is working when the five failures from the [Problem Diagnosis](../en/problem-diagnosis.md) stop happening. Thirteen measurements are grouped into three sets: what got fixed, what got produced, and how efficiently the process ran.

<br>

### Group 1 — Are the five failures actually going away? (M1–M5)

Each measurement maps directly to one of the five failures.

| Metric | Which failure it tracks | How it's measured | How often | Direction we want |
|---|---|---|---|---|
| M1. Decision-to-document rate | Decisions disappearing | % of confirmed decisions recorded within 48 hours | Weekly | ↑ |
| M2. Decision-to-document lag | Documents not updating automatically | Time from a confirmed decision to the source of truth being updated | Weekly | ↓ |
| M3. "What's current?" messages | Nobody knowing which copy to trust | Weekly count of direct messages asking what the current policy is | Ongoing | ↓ |
| M4. New-document term compliance | Inconsistent terminology | % of new documents using the approved glossary term | Monthly | ↑ |
| M5. AI citation accuracy | Stale documents misleading everyone at once | % of AI assistant answers that cite a current document, sampled | Monthly | ↓ in outdated citations |

<br>

### Group 2 — Is knowledge actually building up and getting used? (M6–M9)

| Metric | How it's measured | How often | Direction we want |
|---|---|---|---|
| M6. Source-of-truth growth | New documents added per month | Monthly | Steady increase |
| M7. Decision capture rate | % of real decisions formally recorded | Monthly | ↑ |
| M8. AI assistant usage | How often it's used, by channel | Weekly | ↑ |
| M9. Document views | Page views and time spent, per document | Monthly | ↑ |

<br>

### Group 3 — Is the pipeline itself running efficiently? (M10–M13)

| Metric | How it's measured | How often | Direction we want |
|---|---|---|---|
| M10. How often work loops back, and where | Count of loop-backs, and which stage triggered them | Monthly | ↓ |
| M11. Technical Writer review time | Time from plan confirmation to final approval | Monthly | ↓ |
| M12. Automated vs. manual handling | How much is handled by the pipeline vs. by hand | Monthly | Automated share ↑ |
| M13. How often the AI's instructions themselves change | Version changes to the AI's prompt document | Quarterly | Stable |

<br>

## Reading two metrics together, not one at a time

A single metric can be misleading on its own. Reading two side by side usually reveals what's actually happening.

```
M2 (lag) is improving, but M3 (direct messages) isn't
→ documents are getting updated, but people don't know it's happening
→ this is a notification problem, not a content problem
M4 (term compliance) is low, but M3 (direct messages) is unchanged
→ inconsistent terms aren't actually confusing anyone yet
→ but consistency is still broken, so prioritize the most-visible documents first
```

<br>

## The quarterly review

Once a quarter, the Technical Writer reviews the whole system against all thirteen metrics.

**What gets checked:**
- Is each metric trending up, flat, or down?
- Which stage is causing the most loop-backs?
- Where is usage lowest, and by which role?
- Is the AI's own instruction document still current?
- Are folder owners still the right people?

This review produces a ranked list of what to fix next quarter — closing the loop between measuring the system and actually improving it.

<br>

## Adoption and measurement are the same cycle

These two aren't separate concerns. They feed each other directly.

```
Effort to drive adoption → people's behavior changes
Behavior changes → the metrics move
Metrics moving → tells you what to fix
Fixing it → makes the next round of adoption easier
```

When a metric doesn't move, that's not proof the system is broken. It's a signal that one specific part of the design — a role, a channel, an entry point — isn't working the way it was meant to. Measurement tells you exactly where to look.

<br>
<br>

