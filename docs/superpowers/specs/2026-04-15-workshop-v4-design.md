---
title: Workshop v4 — Guided tour of one real plugin
date: 2026-04-15
status: approved-design
supersedes: workshop v3 (commit fd8ad51)
---

# Workshop v4 Design

## Context

The current workshop deck (v3, `WORKSHOP-PRESENTATION.pptx`, 23 slides) teaches six pillars of agentic AI tooling across ~200 minutes. Review of v3 against our actual audience surfaced four problems:

1. **Mixed audience** — the room is team leads + IC engineers, with AI baselines ranging from "Copilot autocomplete users" to "already building agents." v3's framing ("your engineers don't need to learn prompts", "team lead multiplier") alienates ICs.
2. **Pillars rushed, capstone dominates** — v3 spends 50 min on the capstone while Pillars 1, 5, 6 are crammed. The top desired outcome (mental model of how the six pillars compose) lands weakly because the pillars don't get breathing room and no slide makes the composition explicit.
3. **No coherent anchor** — v3 uses synthetic examples (`pr-review`, `csharp-reviewer`) per pillar. They illustrate, but the audience never sees one artifact demonstrating all pillars, so the model composes only by inference.
4. **Pillar 6 is a bolt-on** — CI automation via `copilot -p` sits uncomfortably as a standalone pillar; it's really a deployment concern, not a mental-model pillar.

## Design

**One real plugin threads through the workshop as the guided-tour anchor**: `Copilot-Wiki/Agent-Skills/gep_skills/code-review/` (pinned at Copilot-Wiki commit `9d1b59b`). It is a framework-plus-policy code-review plugin containing one agent (`code-reviewer`), six policy/reporting skills, and an Azure DevOps MCP dependency — every pillar is a real file in this plugin.

Opening demo installs it and runs it live. Pillars 2, 3, 4, 5 each cite a specific file from it. A new "composition" slide shows its `plugin.json` as the literal mental-model payoff. Capstone asks attendees to fork the plugin's structure and ship one of their own skills.

Pillar 1 stays pure baseline (no plugin tie-in) so newcomers in the mixed room are not drowned before they have Copilot CLI fundamentals.

Pillar 6 (CI automation) is demoted from standalone pillar to a "next step" line item in the closing playbook.

### Timing (190 min, 3h 10m)

| Time | Slide(s) in v3 | Segment | Plugin usage |
|---|---|---|---|
| 0–15 | 1 + new | Opening: install `code-review` plugin → run `code review PR #<facilitator-selected>` → watch agent fetch PR via ADO MCP, dispatch sub-agents, post threaded inline comments | Anchor demo (facilitator picks a real PR during workshop prep) |
| 15–25 | 2, 3, 4 (condensed) | Mindset + outcomes + agenda | — |
| 25–40 | 6 | Pillar 1 — Context & Commands (15 min) | None |
| 40–60 | 7, 8 | Pillar 2 — Instruction Files + Indexing (20 min) | `skills/design-patterns/SKILL.md` (index) + `checks/dp-creational.md` (detail) |
| 60–80 | 9, 10 | Pillar 3 — Agents + Sub-agents (20 min) | `agents/code-reviewer.agent.md` frontmatter + README sub-agent orchestration diagram |
| 80–110 | 11, 12, 13, 14 | Pillar 4 — Skills (30 min, 4 parts) | P2: `skills/design-patterns/SKILL.md`. P3: replay opening demo annotated. P4: point at `gep_skills/security/checkmarx-*` as sibling plugin |
| 110–125 | 15 | Pillar 5 — MCP vs CLI Skill (15 min) | `.mcp.json` (6 lines, bundled with plugin) |
| 125–135 | new | Composition — "this IS the mental model" (10 min) | `plugin.json` |
| 135–145 | — | Break | — |
| 145–180 | 17, 18, 19, 20 | Capstone — fork plugin structure, ship 1 skill (35 min) | `plugin.json` + `SKILL.md` template fork |
| 180–190 | 22, 23 | Rollout playbook (absorbs CI automation) + Q&A | — |

### Slide-by-slide change list

| v3 slide | v4 action | Notes |
|---|---|---|
| 1 (cover) | Keep | — |
| 2 (mindset) | Keep | — |
| 3 (walk-away) | Keep, tighten | Condense into 10-min block with 4. |
| 4 (agenda) | Rewrite | New 190-min schedule; remove Pillar 6; add composition slide. |
| 5 (opening narrative) | Replace | Becomes "install plugin → run `code review PR`" demo script. |
| 6 (Pillar 1) | Keep, add 3 min pacing | Stays generic baseline. |
| 7 (Pillar 2 — AGENTS.md) | Keep | — |
| 8 (Pillar 2 — indexing) | Add plugin citation | Show `skills/design-patterns/SKILL.md` + one `checks/*.md` file as the real indexing example. |
| 9 (Pillar 3 — Agents) | Replace example | Swap `csharp-reviewer` sketch for `code-reviewer.agent.md` frontmatter + triggers. |
| 10 (Pillar 3 — Sub-agents) | Replace diagram | Use plugin's actual policy orchestration diagram from `README.md`. |
| 11 (Pillar 4 — Why) | Keep | — |
| 12 (Pillar 4 — What) | Replace example | Swap synthetic `pr-review/SKILL.md` for real `design-patterns/SKILL.md`. |
| 13 (Pillar 4 — Code Review showcase) | Reframe | Annotated replay of opening demo — "this was hims-ads-policy; this was general-best-practices; this was ADO MCP." |
| 14 (Pillar 4 — Checkmarx) | Compress | Brief sibling-plugin callout pointing at `gep_skills/security/checkmarx-*`. Less airtime than v3. |
| 15 (Pillar 5) | Add plugin citation | Show the plugin's `.mcp.json` as the concrete example. |
| 16 (Pillar 6 — Automation) | **Delete** | Content absorbed into playbook (slide 22). |
| **NEW composition slide** | Create | `plugin.json` on screen. Label each field (agents, skills, mcpServers). "This is the mental model." |
| 17 (Capstone setup) | Keep, adjust | Clone instructions point at plugin template fork. |
| 18, 19, 20 (Capstone phases) | Rewrite | Scope reduced to 1 skill inside fork of `plugin.json` template; 35-min budget. |
| 21 (Distribution — plugins) | Integrate into composition slide | No separate slide needed; plugins are now the spine, not a closing topic. |
| 22 (Playbook) | Add CI automation line | One bullet under "Week 4" absorbing old Pillar 6. |
| 23 (Closing) | Keep | — |

### Capstone scope

- **MVP artifact per attendee:** 1 working `SKILL.md` inside a forked plugin wrapper (`plugin.json` + `SKILL.md`), committed to their own repo or a sandbox.
- **Not required:** custom agent file, MCP wiring. Both are taught conceptually; building them is a stretch goal for faster attendees.
- **Topic:** attendee-picked, from the ideation prompt in slide 19 (e.g. `pr-checklist`, `incident-triage`, `api-design`, `onboarding-tour`).
- **Scaffolding provided by facilitator:** a template directory (lives in this repo under `samples/capstone-plugin-template/`, to be created as part of implementation) with `plugin.json` stub + `SKILL.md` stub + short README. Attendees fork this rather than write from scratch.
- **Facilitator pacing:** 10 min pick-and-design, 20 min build, 5 min test-and-share.

### Mixed-audience handling

- **Pillar 1** remains the "everyone aligned" moment — no plugin tie-in, 15 min, assumes nothing.
- **Advanced viewers** get depth from the plugin citations (real inputs, real conditional activation, real severity classification) — these satisfy experienced engineers without slowing the pace for newcomers.
- **Sub-agent orchestration** (Pillar 3) stays as an optional-depth concept; facilitator flags it as "advanced, we'll show it working but you don't need to build this today."
- **Capstone** picks a topic scaled to the attendee — a lead might encode a team convention, an IC might encode a personal workflow. Both end with a committable artifact.

### Reference plugin pin

- **Repo:** `Copilot-Wiki`
- **Path:** `Agent-Skills/gep_skills/code-review/`
- **Pinned commit:** `9d1b59b` (2026-04-15)
- **Why pin:** The plugin is under active development. Slides that cite specific files must survive until workshop day; permalink at this commit hash.
- **Key files referenced by slides:**
  - `plugin.json` — composition slide
  - `.mcp.json` — Pillar 5
  - `README.md` (orchestration diagram section) — Pillar 3
  - `agents/code-reviewer.agent.md` — Pillar 3
  - `skills/design-patterns/SKILL.md` — Pillar 2 + Pillar 4 Part 2
  - `skills/design-patterns/checks/dp-creational.md` — Pillar 2 (indexing detail)

### What v4 does NOT change

- Pillar ordering
- The six-pillar frame itself
- Capstone as a real-artifact deliverable (scope tightened, but still the capstone)
- Rollout playbook structure (content tweaked to absorb CI automation)

## Out of scope

- Building a new reference plugin (we use the existing `code-review` plugin)
- Rewriting any existing slide's visual theme / palette
- Changes to slides 9 and 12 already made in v3 (user-scope vs project-scope annotations stay)
- Any content for a v5 — this spec ends at workshop-delivery readiness

## Next step

Implementation plan covers the pptx edits: which slide XML files to change, what to add to each, order of operations, visual QA pass. That's written separately.
