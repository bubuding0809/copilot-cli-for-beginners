---
title: Pre-Workshop Pulse Survey — Copilot & Agents
date: 2026-04-15
status: draft
purpose: Calibrate workshop v5 content to attendee baseline
audience: Confirmed workshop attendees (pre-session)
length: ~4 min, 8 questions
---

# Pre-Workshop Pulse Survey

## Purpose

Calibrate the next iteration of the Copilot & Agents workshop to where attendees actually are. Results drive three decisions:

1. Which of the six pillars need more airtime vs. compression
2. Whether framing assumes too much (alienating newcomers) or too little (boring power users)
3. Which real workflows and friction points to weave into slide examples and capstone prompts

The survey is **not** for org-wide maturity tracking or leadership reporting. It is a content-tuning instrument for a specific cohort.

## Scope

- **Audience:** confirmed attendees only (sent 3–5 days before the session).
- **Length target:** ≤5 min, ~8 questions.
- **Anonymity:** anonymous by default; optional name field for follow-up pairing.
- **Delivery:** tool-agnostic markdown spec. Transcribe into Microsoft Forms (likely org default) or Google Forms.

## Framing (intro blurb shown to respondents)

> **Copilot & Agents — Pre-Workshop Pulse**
>
> This 4-minute survey helps us tune the upcoming workshop to where you actually are today. There are no wrong answers — "never heard of it" is a perfectly valid response and genuinely helps us pace the session.
>
> Anonymous by default. Optional name field at the end if you'd like us to follow up 1:1 on anything.

---

## The 8 Questions

### Q1. What's your role?

*Single-select.*

- IC engineer (software / data / ML / QA)
- Tech lead
- Engineering manager or above
- Other

**Why:** Determines lead/IC mix. Workshop framing shifts depending on the ratio — a lead-heavy room gets more "team multiplier" content; an IC-heavy room gets more hands-on depth.

---

### Q2. How often do you use GitHub Copilot in a typical week?

*Single-select.*

- Daily
- A few times a week
- Occasionally (a few times a month)
- Rarely or never

**Why:** Baseline usage frequency. If >30% answer "rarely or never," Pillar 1 (Context & Commands) needs full airtime. If most are daily users, we can compress the basics.

---

### Q3. Where do you currently use Copilot? (Select all that apply)

*Multi-select.*

- IDE autocomplete (grey-text suggestions)
- IDE chat (side panel Q&A)
- Copilot CLI (`copilot` command in terminal)
- Custom agents (agents you or your team built)
- MCP connectors (ADO, Jira, databases, etc.)
- I haven't used Copilot yet

**Why:** Surface distribution reveals the maturity curve. Heavy skew toward IDE-only = invest in Pillars 1–3. Meaningful CLI/agent usage = accelerate through basics, spend budget on Pillars 4–6.

---

### Q4. Rate your familiarity with each pillar.

*Matrix question. 4-point scale per row.*

**Scale:** Never heard of it · Heard of it · Used it · Built my own

| Pillar | Scale |
|---|---|
| Context & commands (`/plan`, `/review`, `@file`) | 1–4 |
| AGENTS.md & instruction files | 1–4 |
| Agents & sub-agents | 1–4 |
| Skills (procedural knowledge for agents) | 1–4 |
| MCP servers & connectors (ADO, Jira, etc.) | 1–4 |
| Plugins (bundling agents + skills + MCPs) | 1–4 |

**Why:** The workhorse question. Produces a per-pillar proficiency heatmap that directly drives airtime decisions. Inverse-ranked scores → candidates for expansion; high scores → candidates for compression or advanced-track content.

---

### Q5. Which pillar would you most want the workshop to spend extra time on?

*Single-select.*

- Context & commands
- AGENTS.md & instruction files
- Agents & sub-agents
- Skills
- MCP servers & connectors
- Plugins
- No strong preference

**Why:** Direct audience vote. Acts as a tiebreaker when the Q4 heatmap is mixed, and catches the case where people self-rate high on a pillar but still want more depth (signals genuine interest vs. skill gap).

---

### Q6. What's the biggest friction you've hit using Copilot so far?

*Open text. Optional.*

*Placeholder examples (shown as hint text):*
> "Agent forgets context halfway through a task"
> "Couldn't get the ADO MCP to connect"
> "Don't know when to write a skill vs. just prompt"
> "Suggestions are often wrong for our codebase"

**Why:** Friction themes drive slide examples and de-risking moments. The honest answers here become the "before/after" narrative beats in the deck.

---

### Q7. One team workflow you wish Copilot could run end-to-end?

*Open text. Optional.*

*Placeholder examples:*
> "PR review against our internal checklist"
> "Generating release notes from commits"
> "Onboarding walk-through for new repos"
> "Triaging incident tickets from Jira"

**Why:** Feeds the capstone ideation prompt with real, attendee-specific workflow ideas. Also surfaces org-wide automation appetite — helps the facilitator tailor the closing playbook.

---

### Q8. Anything else the facilitator should know before the session?

*Open text. Optional.*

**Why:** Safety net. Catches accessibility needs, specific deliverable hopes, team-specific context, or anything the 7 structured questions missed.

---

## Closing

- Optional name field (free text, explicitly marked optional): *"Leave your name if you'd like us to follow up 1:1 on anything — otherwise leave blank."*
- Thank-you blurb: *"Thanks — this directly shapes what we cover. See you at the session."*

---

## Analysis Plan

### Quantitative

| Input | Derived signal | Decision it drives |
|---|---|---|
| Q1 | Lead : IC ratio | Framing balance, example choice |
| Q2 | % daily vs. rarely | Pillar 1 airtime budget |
| Q3 | Surface distribution | Where on the maturity curve to start the narrative |
| Q4 | Mean familiarity score per pillar (1–4) | Rank pillars inverse-by-score → airtime candidates |
| Q5 | Top-voted pillar | Tiebreaker + validator for Q4 ranking |

**Heuristics:**
- Any pillar with mean Q4 score < 2.0 → expand or add on-ramp slide
- Any pillar with mean Q4 score > 3.0 → compress or move to advanced-track callout
- Q4 high + Q5 votes for same pillar → signal genuine appetite; keep depth, don't compress
- Q2 "rarely or never" ≥ 30% → keep full Pillar 1, consider a pre-reading note

### Qualitative

- Q6 (friction): cluster responses into themes (context loss / connector setup / prompt strategy / codebase fit / trust). Pick top 3 themes → fold into relevant pillar slides as "this is the thing that usually breaks" moments.
- Q7 (workflows): extract 5–8 concrete workflow ideas → populate the capstone ideation slide with attendee-sourced options instead of generic examples.
- Q8: triage individually; flag anything urgent to the facilitator.

### Reporting artifact

One page back to the team:
- Role mix (pie)
- Usage frequency (bar)
- Pillar heatmap (Q4 matrix visualized)
- Top 3 friction themes (verbatim quotes + count)
- Top 5 workflow asks (verbatim, deduped)
- Proposed v5 timing adjustments per pillar (before/after minutes)

---

## What This Survey Does NOT Ask

Deliberately out of scope to keep under 5 minutes:

- Subscription / licensing state (infra concern, not content concern)
- Detailed model preferences (Sonnet vs Opus vs Haiku)
- IDE-specific tooling preferences (VS Code vs JetBrains)
- Security or policy concerns (separate conversation with platform team)
- Prior training history
- NPS or satisfaction with past sessions (this is pre-, not post-)

If any of these become decision-relevant later, they belong in a post-workshop survey, not this one.

---

## Next Steps

1. Review + approve this spec
2. Transcribe into Microsoft Forms (or team's chosen tool)
3. Send 3–5 days before session to confirmed attendee list
4. Close survey 24h before session
5. Run analysis; adjust v5 slides per the heuristics above
6. Keep results file for post-session comparison with any follow-up pulse
