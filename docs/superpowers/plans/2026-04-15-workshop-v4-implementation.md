# Workshop v4 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign the workshop slides and create capstone scaffolding per the v4 design spec, delivering a `code-review`-plugin-anchored curriculum in ~190 minutes.

**Architecture:** Edit the existing `WORKSHOP-PRESENTATION.pptx` via unpack/repack using the `example-skills:pptx` skill. Every pillar slide (except Pillar 1) cites a specific file from the pinned `code-review` plugin. Two new slides are added (opening-demo + composition). Slide 16 (Pillar 6 — Automation) is deleted; its one useful idea moves to the playbook. A capstone template is created under `samples/capstone-plugin-template/` for attendees to fork.

**Tech Stack:** Python pptx scripts (`unpack.py`, `clean.py`, `pack.py`, `add_slide.py`) from the `example-skills:pptx` plugin; LibreOffice + pdftoppm for visual QA; git for commits.

**Reference spec:** `docs/superpowers/specs/2026-04-15-workshop-v4-design.md`

**Reference plugin (pinned):** `Copilot-Wiki/Agent-Skills/gep_skills/code-review/` at commit `9d1b59b`. Absolute path on this machine: `/Users/dingruoqian/code/work/Copilot-Wiki/Agent-Skills/gep_skills/code-review/`.

---

## File structure

**Modified:**
- `WORKSHOP-PRESENTATION.pptx` (repo root) — primary artifact

**Created:**
- `samples/capstone-plugin-template/plugin.json`
- `samples/capstone-plugin-template/skills/example-skill/SKILL.md`
- `samples/capstone-plugin-template/README.md`

**Working files (not committed):**
- `/tmp/pptx-unpacked-v4/` — unpack workspace
- `/tmp/pptx-v4-qa/` — visual QA images

**Unchanged v3 slides that stay as-is:** 1, 2, 3, 6, 7, 11, 22 (minor addition only), 23.

---

## Phase 1: Preparation

### Task 1: Unpack the current pptx and snapshot reference plugin content

**Files:**
- Read: `WORKSHOP-PRESENTATION.pptx`
- Create working dir: `/tmp/pptx-unpacked-v4/`

- [ ] **Step 1: Clean any stale unpack dir**

```bash
rm -rf /tmp/pptx-unpacked-v4 /tmp/pptx-v4-qa
mkdir -p /tmp/pptx-v4-qa
```

- [ ] **Step 2: Unpack the pptx**

```bash
cd /Users/dingruoqian/code/work/copilot-cli-for-beginners
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/office/unpack.py WORKSHOP-PRESENTATION.pptx /tmp/pptx-unpacked-v4/
```

Expected: directory created with `ppt/slides/slide1.xml` … `slide23.xml`, `ppt/presentation.xml`, etc.

- [ ] **Step 3: Verify slide count and list**

```bash
ls /tmp/pptx-unpacked-v4/ppt/slides/*.xml | wc -l
```

Expected: `23`.

- [ ] **Step 4: Record plugin-reference content (used as citation snippets across slide-edit tasks)**

Quote these snippets verbatim on slides where cited:

**`plugin.json`** (composition slide):
```json
{
    "name": "code-review",
    "description": "Framework and policy plugins for structured code review ...",
    "mcpServers": ".mcp.json",
    "agents": [
        "./agents/code-reviewer.agent.md"
    ],
    "skills": [
        "./skills/code-review-reporting",
        "./skills/hims-ads-code-review-policy",
        "./skills/general-best-practices",
        "./skills/design-patterns",
        "./skills/performance",
        "./skills/frontend-review-policy"
    ]
}
```

**`.mcp.json`** (Pillar 5):
```json
{
  "mcpServers": {
    "azure-devops": {
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "IHIS-HIP"]
    }
  }
}
```

**`agents/code-reviewer.agent.md` frontmatter** (Pillar 3):
```yaml
name: code-reviewer
description: >
  Policy-driven code review agent. Supports local diff scan and ADO PR
  inline review (fetches PR via MCP, posts threaded comments at file:line).
  Loads swappable policy modules.
  Triggers: "code review", "review PR", "review my changes", "check PR
  for compliance", "security review".
  4-phase pipeline: Intake → Policy Selection → Execute → Output.
```

**`skills/design-patterns/SKILL.md` structure** (Pillar 2 + Pillar 4 Part 2):
```yaml
name: design-patterns
description: >
  Design pattern review policy module for code-reviewer.
  Triggers on: "design pattern review", "architectural review",
  "pattern check", "anti-pattern".
```
Plus tables: `## Inputs` (architecture type, language, focus), `## Conditional Activation` (rules per arch/focus), `## Severity Classification` (CRITICAL/HIGH/MEDIUM/INFO).

**`skills/design-patterns/checks/dp-creational.md`** (Pillar 2 indexing detail):
```markdown
# Creational Design Pattern Checks

## Checks

| Ref | Check | What to look for |
|-----|-------|------------------|
| DP-CREATE-001 | Singleton appropriate use | ... |
| DP-CREATE-002 | Singleton thread safety | ... |
| DP-CREATE-009 | Dependency injection | ... |
```

- [ ] **Step 5: Commit workspace note (no code change yet, snapshot confirmation)**

No commit yet. Proceed to Phase 2.

---

## Phase 2: Structural slide changes (delete, add)

### Task 2: Delete slide 16 (Pillar 6 — Automation)

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/presentation.xml` (remove `<p:sldId>` entry for slide 16)
- Delete (via clean): `/tmp/pptx-unpacked-v4/ppt/slides/slide16.xml`

- [ ] **Step 1: Identify the sldId entry for slide 16 in `presentation.xml`**

Read `/tmp/pptx-unpacked-v4/ppt/presentation.xml`, find `<p:sldIdLst>`. Each `<p:sldId>` has an `r:id` attribute that maps to a slide via `ppt/_rels/presentation.xml.rels`. Find the `<p:sldId>` whose `r:id` resolves to `slides/slide16.xml`.

- [ ] **Step 2: Remove that `<p:sldId>` element**

Use the Edit tool to delete the one-line `<p:sldId id="..." r:id="rIdXX"/>` entry from `<p:sldIdLst>`.

- [ ] **Step 3: Run clean to remove orphaned slide16.xml**

```bash
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/clean.py /tmp/pptx-unpacked-v4/
```

Expected: clean script removes `slide16.xml`, its rels, and any unreferenced media.

- [ ] **Step 4: Verify slide count is now 22**

```bash
ls /tmp/pptx-unpacked-v4/ppt/slides/slide*.xml | wc -l
```

Expected: `22` (but note slide filenames may still be `slide1`…`slide15, slide17…slide23` — that is fine; filename ≠ slide order).

### Task 3: Duplicate slide 5 as the new opening-demo slide

**Files:**
- Create: `/tmp/pptx-unpacked-v4/ppt/slides/slideNN.xml` (new file, via add_slide.py)

The new slide reuses slide 5's layout (title + numbered steps) since the opening is still a step-by-step narrative — just a different narrative. We duplicate slide 5, then edit its content in Phase 3.

- [ ] **Step 1: Run `add_slide.py` to duplicate slide5**

```bash
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/add_slide.py /tmp/pptx-unpacked-v4/ slide5.xml
```

Expected output: script prints a `<p:sldId id="..." r:id="rIdXX"/>` line to add to `<p:sldIdLst>`. Copy this output.

- [ ] **Step 2: Insert the new `<p:sldId>` in `presentation.xml` AFTER slide 5's entry**

We want the new slide to be slide 6 in display order (sitting between current slide 5 "Opening narrative" and current slide 6 "Pillar 1"). Place the printed `<p:sldId>` immediately after the original slide 5's sldId.

Later in Phase 3 we will edit BOTH the original slide 5 and this new copy — the original becomes the install-and-run demo, the copy becomes obsolete; actually revisit this in Task 4: we may only need ONE new slide. See Task 4.

Actually simpler: we do NOT need a net-new opening slide. v3's slide 5 already IS the opening narrative — we rewrite its CONTENT in Phase 3 to be the install-and-run demo. Skip this task.

- [ ] **Step 3: Skip duplication — revert any `add_slide.py` effects**

```bash
# If any stray slide file was added, remove it by editing presentation.xml
# and running clean.py. If not, do nothing.
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/clean.py /tmp/pptx-unpacked-v4/
ls /tmp/pptx-unpacked-v4/ppt/slides/slide*.xml | wc -l
```

Expected: `22`.

**Decision captured:** Task 3 is a no-op. Original slide 5 is rewritten in Task 7 as the install-and-run demo.

### Task 4: Duplicate slide 8 as the new composition slide (for `plugin.json`)

**Files:**
- Create: `/tmp/pptx-unpacked-v4/ppt/slides/slideNN.xml` (new file)

Slide 8 ("Scaling with the Indexing Pattern") has a file-tree + text layout that is structurally perfect for the composition slide (which shows `plugin.json` + labels). We duplicate it, then rewrite its content in Phase 3.

- [ ] **Step 1: Run `add_slide.py` to duplicate slide8**

```bash
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/add_slide.py /tmp/pptx-unpacked-v4/ slide8.xml
```

Expected: prints a `<p:sldId id="..." r:id="rIdXX"/>` entry to stdout. Note the new slide's filename (e.g. `slide24.xml`). Record both.

- [ ] **Step 2: Insert the new `<p:sldId>` after slide 15's entry in `presentation.xml`**

This places the composition slide between Pillar 5 (current slide 15) and the Break (current slide 17 after slide 16 deletion). In `<p:sldIdLst>`, find the `<p:sldId>` that maps to `slide15.xml` and insert the new entry on the line immediately after it.

- [ ] **Step 3: Verify new slide count**

```bash
ls /tmp/pptx-unpacked-v4/ppt/slides/slide*.xml | wc -l
```

Expected: `23` (back to 23 after one deletion + one addition).

Also verify via `python3 -m markitdown /tmp/pptx-unpacked-v4 2>/dev/null` — we'll do a real markitdown check after repack.

---

## Phase 3: Content edits per slide

Each task in this phase edits one slide's XML text content. Use the Edit tool on the slide's `.xml` file. Preserve all `<a:rPr>`, `<a:pPr>`, and other formatting attributes — only change the contents of `<a:t>` elements and (where noted) add/remove `<a:p>` paragraphs copying existing `<a:pPr>` for spacing consistency.

**Pitfalls reminder (from pptx skill):**
- Bold headers with `b="1"` on `<a:rPr>`
- Use XML entities `&#x201C;` `&#x201D;` for smart quotes
- Never concatenate multi-item content — use separate `<a:p>` elements
- Use `xml:space="preserve"` on `<a:t>` when leading/trailing whitespace matters

### Task 5: Rewrite slide 4 (Agenda)

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide4.xml`

**Content change:** The agenda table is replaced with the v4 timing.

- [ ] **Step 1: Read slide4.xml** to find the table/paragraphs holding the v3 agenda rows.

- [ ] **Step 2: Replace rows with v4 schedule**

New rows (label → duration → description):
```
15 min | Opening         | Install plugin → run code review PR live
10 min | Mindset + agenda| Why this mindset shift
15 min | Pillar 1        | Context & Commands — Quick Start
20 min | Pillar 2        | Instruction Files + Indexing Pattern
20 min | Pillar 3        | Agents + Sub-Agent Orchestration
30 min | Pillar 4        | Skills — Why, What, Code Review Showcase
15 min | Pillar 5        | Integration Choices (MCP vs CLI Skill)
10 min | Composition     | How a plugin ties it all together
10 min | Break           |
35 min | Capstone        | Fork the template, ship 1 skill
10 min | Closing         | Rollout playbook + Q&A
Total: ~3h 10m
```

Edit each `<a:t>` in the existing agenda rows to match. If there is one more row in v3 than v4 (v3 had Pillar 6 + Capstone; v4 has Composition + Capstone), the row count is the same — just relabel. If v3 has 10 rows and v4 needs 11, copy the `<a:pPr>`+`<a:r>` block from an existing row to insert one more (preserving formatting).

- [ ] **Step 3: Commit the slide4 edit in place** (no git commit yet — we commit the pptx as a whole after repack)

### Task 6: Tighten slides 2 + 3 pacing notes (optional, minor)

**Files:**
- Modify (optional): `/tmp/pptx-unpacked-v4/ppt/slides/slide2.xml`, `slide3.xml`

- [ ] **Step 1:** No text edit required — these slides stay content-identical. The 10-min combined pacing is a facilitator decision, not a slide change. Skip edits.

### Task 7: Rewrite slide 5 (Opening narrative → install-and-run demo)

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide5.xml`

**Content change:** v3 slide 5 shows a 6-step narrative (Pull work item → Plan → Switch agent → Implement → Review → Ship). v4 replaces it with the install-and-run demo flow.

- [ ] **Step 1: Read slide5.xml** to find the 6 numbered step blocks and the title.

- [ ] **Step 2: Replace title**

Old: `Opening: The Vision` / `Live demo: work item → merged PR in one terminal session`

New: `Opening: See It All At Once` / `Live demo: install a plugin, then run it`

- [ ] **Step 3: Replace the 6 numbered steps**

New steps (keep the 1-6 numbered format; reuse the existing shape layout):

```
1. Open VS Code → install 'code-review' plugin from marketplace
   (bundled: 1 agent, 6 skills, ADO MCP)

2. In terminal: copilot

3. > code review PR #<selected-pr>

4. Watch: agent fetches PR via ADO MCP

5. Watch: 3 sub-agents run in parallel (security · quality · performance)

6. Watch: threaded inline comments post at file:line in ADO
```

- [ ] **Step 4: Replace the footer quote**

Old: `"Your engineers don't need to learn prompts. They need you to set up the system."`

New: `One install → one command → all six pillars in motion. That's what we're teaching.`

### Task 8: Pillar 1 slide 6 — no content change

**Files:** none modified.

- [ ] Skip — Pillar 1 stays intact. Timing rebalance is a facilitator note, not a slide change.

### Task 9: Slide 8 (Pillar 2 — Indexing Pattern) — add plugin citation

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide8.xml`

**Content change:** The current file-tree on this slide shows a synthetic `your-repo/AGENTS.md + docs/*`. Replace with the real `code-review` plugin's index-and-detail pattern.

- [ ] **Step 1:** Replace the `EXAMPLE STRUCTURE` file-tree block text. Old tree:

```
📁 your-repo/
├── AGENTS.md
├── docs/
│   ├── architecture.md
│   ├── csharp-conventions.md
│   ├── typescript-style.md
│   ├── api-design.md
│   └── deployment.md
├── src/
```

New tree:

```
📁 code-review/skills/design-patterns/
├── SKILL.md               ← index (inputs, severity, links)
├── checks/
│   ├── dp-creational.md
│   ├── dp-structural.md
│   ├── dp-behavioral.md
│   ├── dp-architectural.md
│   └── dp-anti-patterns.md
```

- [ ] **Step 2:** Replace the `THE PATTERN` paragraph so it matches the new tree.

Old: "AGENTS.md becomes an index — a short file that points to detailed docs in docs/. AI loads the index always, detail docs only when needed."

New: "SKILL.md is the index — short, always loaded. checks/*.md files are the details — loaded only when the skill runs. This real pattern scales the same way at the repo level with AGENTS.md + docs/."

### Task 10: Slide 9 (Pillar 3 — Agents) — cite `code-reviewer` agent

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide9.xml`

**Content change:** Swap the synthetic `csharp-reviewer` example for the real `code-reviewer` agent frontmatter.

- [ ] **Step 1:** Replace the `BUILD YOUR OWN` code block text. Preserve the scope-annotation work already done in v3 (project-scope green + user-scope amber rows).

Keep the two file-path rows (`.github/agents/...` green and `~/.copilot/agents/...` amber) as they are. Change the agent filename in both from `csharp-reviewer.agent.md` to `code-reviewer.agent.md`.

Replace the three description bullet lines below with:

```
description: "Policy-driven code review — local diff or ADO PR"
Triggers: "code review", "review PR", "review my changes"
Operates as 4-phase pipeline: Intake → Policy → Execute → Output
```

- [ ] **Step 2:** Replace the LEFT column ("GENERIC PROMPT" vs "WITH AGENT") example:

Old right side: `"Review @BooksController.cs" → Nullable checks, async/await, dependency injection patterns`

New right side: `> code review PR #1234 → Phase 1: Intake (target + policies); Phase 2: Load design-patterns + general-best-practices; Phase 3: Execute in parallel; Phase 4: Threaded inline comments`

### Task 11: Slide 10 (Pillar 3 — Sub-agents) — cite plugin's orchestration

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide10.xml`

**Content change:** Current diagram shows generic "Planner → Researcher / Implementer / Reviewer". Replace with plugin's actual orchestration.

- [ ] **Step 1:** Replace the 4-box diagram labels.

Old box labels: `Planner Agent`, `Researcher`, `Implementer`, `Reviewer`.

New labels:
```
MAIN:    code-reviewer (fetches diff, aggregates)
SUB 1:   security policy (hims-ads + compliance)
SUB 2:   quality policy (general-best-practices)
SUB 3:   performance policy (perf checks)
```

Old sub-box descriptions ("Understands codebase", "Writes changes", "Checks output"):

New: `Runs in parallel`, `Isolated context per policy`, `Results aggregated into single PR comment thread`.

- [ ] **Step 2:** Replace the "WHY IT MATTERS" bullets to tie to the plugin:

Old: "Context isolation — each agent sees only what it needs / Specialization / Parallelism"

New: "Context isolation — each policy sees only its own checks/*.md · Specialization — domain expertise per policy · Parallelism — 4+ policies run concurrently (see plugin README: `Subagent orchestration` section)"

### Task 12: Slide 12 (Pillar 4 Part 2 — Skills anatomy) — cite `design-patterns/SKILL.md`

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide12.xml`

**Content change:** v3 shows synthetic `pr-review/SKILL.md`. Replace with real `design-patterns/SKILL.md` — but keep the v3 scope-annotation work (project + user scope rows) already committed in earlier v3 edits.

- [ ] **Step 1:** Keep the two file-path rows (project-scope green and user-scope amber). Change the filename in both from `pr-review/SKILL.md` to `design-patterns/SKILL.md`.

- [ ] **Step 2:** Replace the frontmatter and steps block below the paths:

Old:
```
name: pr-review
description: "Review a PR for quality"
## Steps
1. Check tests cover the changes
2. Verify naming conventions
3. Flag security risks
4. Check error handling
```

New:
```
name: design-patterns
description: "Design pattern review — architectural & anti-pattern"
## Inputs     architecture type, language, review focus
## Conditional Activation     rules per arch (microservices, event-driven, layered)
## Severity Classification     CRITICAL / HIGH / MEDIUM / INFO
## Checks Index     → checks/dp-creational.md, dp-structural.md, dp-behavioral.md …
```

- [ ] **Step 3:** Replace the auto-trigger example prompts list:

Old: `"Review these changes" / "Check the PR for issues" / "Does this look ready to merge?"`

New: `"design pattern review" / "architectural review" / "pattern check" / "anti-pattern"`

### Task 13: Slide 13 (Pillar 4 Part 3 — Code Review showcase) — reframe as opening-demo replay

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide13.xml`

**Content change:** Current slide is a generic 5-step PR review flow. Reframe as an annotated replay of the opening demo.

- [ ] **Step 1:** Change the subtitle.

Old: `End-to-end: PR arrives → policy-aware review → auto-generated summary`

New: `Opening demo, replayed and explained — every step was a pillar`

- [ ] **Step 2:** Replace the 5-step labels to map each step to a pillar:

Old steps: `PR arrives / Pull context / Skill triggers / Policy review / Summary output`

New steps (keep 5-step layout, add pillar tags):
```
1. ADO PR #<n>                 ← Pillar 5 (ADO MCP)
2. code review PR #<n>         ← Pillar 3 (agent trigger)
3. design-patterns auto-loads  ← Pillar 4 (skill)
4. sub-agents run in parallel  ← Pillar 3 (orchestration)
5. Inline comments + summary   ← Pillar 4 (code-review-reporting skill)
```

- [ ] **Step 3:** Update the `KNOWN LIMIT` box.

Old: "az CLI can't post inline PR comments yet — summary goes to terminal, paste into ADO or script via REST."

New: "This plugin uses the ADO MCP (not az CLI) precisely because inline comments need the REST API. The MCP handles it."

### Task 14: Slide 14 (Pillar 4 Part 4 — Checkmarx) — compress to sibling-plugin callout

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide14.xml`

**Content change:** Keep the slide but tighten. Shift framing from "showcase" to "sibling plugin, same pattern".

- [ ] **Step 1:** Change the subtitle.

Old: `Security findings become fixable, not just a long list`

New: `Different domain, same structure — see `gep_skills/security/` for checkmarx-orchestrator + sibling skills`

- [ ] **Step 2:** Keep the 4-step flow (Load report / Prioritize / Propose fixes / Verify) and the "TEAM LEAD ANGLE" box. Only update the closing footer.

Old footer: `Same pattern as pr-review — different domain. Skills compose, don't multiply complexity.`

New footer: `Same pattern as `code-review` — different domain. Skills compose, don't multiply complexity.`

### Task 15: Slide 15 (Pillar 5 — MCP vs CLI) — show plugin's `.mcp.json`

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide15.xml`

**Content change:** Add a concrete `.mcp.json` citation block; keep the CLI-vs-MCP comparison.

- [ ] **Step 1:** Add a new code-block paragraph group under the MCP column showing the plugin's `.mcp.json` content (copy `<a:pPr>` from an adjacent paragraph for formatting consistency):

```
.mcp.json (bundled with plugin):
{
  "mcpServers": {
    "azure-devops": {
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "IHIS-HIP"]
    }
  }
}
6 lines → zero manual MCP setup
```

- [ ] **Step 2:** Update the footer quote.

Old: `Start with CLI Skill. Graduate to MCP only when the tool surface justifies the context cost.`

New: `Plugins bundle their MCP dependency in .mcp.json — engineers install once, MCP wires automatically.`

### Task 16: NEW composition slide (the duplicated slide from Task 4)

**Files:**
- Modify: the newly-added slide XML from Task 4 (e.g. `/tmp/pptx-unpacked-v4/ppt/slides/slide24.xml`)

**Content change:** Rewrite the duplicated indexing slide to be the composition / `plugin.json` payoff.

- [ ] **Step 1:** Replace the title.

Old title: `Pillar 2 (cont.) / Scaling with the Indexing Pattern`

New title: `Composition / This IS the mental model`

New subtitle: `One `plugin.json` — every pillar in one file`

- [ ] **Step 2:** Replace the left-column file-tree with `plugin.json` contents (verbatim from the snippet in Task 1 Step 4), preserving monospace formatting and color.

- [ ] **Step 3:** Replace the right-column "THE PROBLEM / THE PATTERN" text with annotated pillar mappings:

```
"mcpServers" → Pillar 5 — one MCP dependency declared
"agents"     → Pillar 3 — entry-point agent listed
"skills"     → Pillar 4 — six policy/reporting skills
(loaded by AI via) → Pillar 1 — natural-language triggers
(each skill uses)  → Pillar 2 — SKILL.md + checks/ indexing
```

- [ ] **Step 4:** Update the footer.

Old: `Keep AGENTS.md short. Let it point. Detail docs stay discoverable but don't bloat context.`

New: `Install this JSON → engineers get agent + skills + MCP in one action. That is distribution.`

### Task 17: Remove slide 21 (Distribution — Plugins), merge useful line into the playbook

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/presentation.xml` (remove slide 21's `<p:sldId>`)
- Delete: `/tmp/pptx-unpacked-v4/ppt/slides/slide21.xml`

**Rationale:** The composition slide now carries the "plugin bundles everything" message. Slide 21 is redundant.

- [ ] **Step 1:** Find the `<p:sldId>` for slide21 in `presentation.xml`; remove it.

- [ ] **Step 2:** Run clean:

```bash
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/clean.py /tmp/pptx-unpacked-v4/
```

Expected: `slide21.xml` removed.

- [ ] **Step 3:** Verify slide count.

```bash
ls /tmp/pptx-unpacked-v4/ppt/slides/slide*.xml | wc -l
```

Expected: `22`.

---

## Phase 4: Capstone updates

### Task 18: Capstone slides 17, 18, 19, 20 — point at the template

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide17.xml` (setup)
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide18.xml` (goal + phases)
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide19.xml` (ideation)
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide20.xml` (build + test)

- [ ] **Step 1:** On slide 17 (capstone setup), add a line to the SETUP COMMANDS code block:

```
# 5. Fork the capstone template
cp -r samples/capstone-plugin-template .github/plugins/my-plugin
```

- [ ] **Step 2:** On slide 18, update the timing breakdown from `15 / 20 / 15` to `10 / 20 / 5`, matching the 35-min capstone budget.

Old: `15 min` design, `20 min` build, `15 min` test.
New: `10 min` design, `20 min` build, `5 min` test.

- [ ] **Step 3:** Slide 19 (ideation) — no content change required; the six example skills listed are still valid.

- [ ] **Step 4:** On slide 20 (build + test), change the step-by-step block.

Old:
```
mkdir -p .github/skills/pr-review && touch .github/skills/pr-review/SKILL.md
# Add frontmatter (name, description)
# Write the checklist
# Define output format
```

New:
```
# Fork the template
cp -r samples/capstone-plugin-template .github/plugins/my-<skill>
cd .github/plugins/my-<skill>

# Edit skills/example-skill/SKILL.md — rename dir, write your checklist
# plugin.json already wired — no change needed

> copilot → "<your natural-language trigger>"
# Verify the skill auto-triggers. Iterate on description if not.
```

Update the output summary line:
Old: `Output: a committed .github/skills/pr-review/SKILL.md — yours to take to your real repos.`
New: `Output: a forked plugin with one working skill — committable and replicable in your real repos.`

### Task 19: Create the capstone plugin template

**Files:**
- Create: `samples/capstone-plugin-template/plugin.json`
- Create: `samples/capstone-plugin-template/skills/example-skill/SKILL.md`
- Create: `samples/capstone-plugin-template/README.md`

- [ ] **Step 1: Create directory structure**

```bash
cd /Users/dingruoqian/code/work/copilot-cli-for-beginners
mkdir -p samples/capstone-plugin-template/skills/example-skill
```

- [ ] **Step 2: Write `plugin.json`**

File: `samples/capstone-plugin-template/plugin.json`
```json
{
    "name": "my-plugin",
    "description": "Capstone template — rename this and the skill below to match your team's need",
    "keywords": ["capstone", "workshop"],
    "skills": [
        "./skills/example-skill"
    ]
}
```

- [ ] **Step 3: Write `skills/example-skill/SKILL.md`**

File: `samples/capstone-plugin-template/skills/example-skill/SKILL.md`

```markdown
---
name: example-skill
description: >
  REPLACE THIS DESCRIPTION. The AI matches this text against the user's prompt
  to decide whether to auto-trigger the skill. Be specific about when to use it.
  Triggers on: "REPLACE ME", "AND ME", "ALSO ME".
---

# Example Skill

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| scope | Yes | What the skill operates on (e.g. a file path, a PR number) |

## Steps

1. REPLACE — first concrete check or action
2. REPLACE — second
3. REPLACE — third

## Output Format

PASS / FAIL checklist + per-finding file:line + remediation suggestion.
```

- [ ] **Step 4: Write `README.md`**

File: `samples/capstone-plugin-template/README.md`
```markdown
# Capstone Plugin Template

Fork this directory as the starting point for your capstone artifact.

## Fork it

    cp -r samples/capstone-plugin-template .github/plugins/my-<name>

## Customize

1. Rename `skills/example-skill/` to your skill name.
2. Edit `skills/<your-skill>/SKILL.md`:
   - Rewrite `description` — this is what the AI matches against the user's prompt.
   - Replace the three `REPLACE` steps with your team's actual checklist.
   - Adjust Inputs and Output Format.
3. Edit `plugin.json`:
   - Rename `"name"` to your plugin name.
   - Update `"description"` and `"keywords"`.
   - Update the path in `"skills"` to match your renamed directory.

## Test

In `copilot`, use a natural-language prompt that matches your skill's description.
The skill should auto-load and guide the work.

If it does not trigger, iterate on the skill's `description` — that is almost
always the fix.
```

- [ ] **Step 5: Verify file structure**

```bash
tree samples/capstone-plugin-template
```

Expected:
```
samples/capstone-plugin-template
├── README.md
├── plugin.json
└── skills
    └── example-skill
        └── SKILL.md
```

---

## Phase 5: Playbook slide

### Task 20: Slide 22 (Playbook) — absorb CI automation as a Week 4 bullet

**Files:**
- Modify: `/tmp/pptx-unpacked-v4/ppt/slides/slide22.xml`

- [ ] **Step 1:** In the `Week 4 / Measure & Iterate` column, the v3 text ends with `Consider MCP & CI hooks`. Replace that line with two:

Old: `Consider MCP & CI hooks`

New (two bullets):
```
Add CI automation: copilot -p "…" in pipeline
(pre-commit hooks · PR summaries · release notes)
```

- [ ] **Step 2:** The "3 STARTER TEMPLATES" section at the bottom: update item 3.

Old: `3. .github/skills/pr-review/SKILL.md — PR review checklist`

New: `3. Fork samples/capstone-plugin-template — ready-to-customize plugin wrapper`

---

## Phase 6: Repack, QA, commit

### Task 21: Repack the pptx

**Files:**
- Output: `WORKSHOP-PRESENTATION.pptx` (replaced)

- [ ] **Step 1:** Repack.

```bash
cd /Users/dingruoqian/code/work/copilot-cli-for-beginners
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/office/pack.py /tmp/pptx-unpacked-v4/ WORKSHOP-PRESENTATION.pptx --original WORKSHOP-PRESENTATION.pptx
```

Expected: validation passes, smart quotes re-encoded, file written.

- [ ] **Step 2:** Confirm file exists and check size.

```bash
ls -la WORKSHOP-PRESENTATION.pptx
```

Expected: file present, size in the same order of magnitude as before.

- [ ] **Step 3:** Run a quick content scan.

```bash
python3 -m markitdown WORKSHOP-PRESENTATION.pptx | grep -iE "xxxx|lorem|ipsum|replace me|tbd" | head
```

Expected: empty output. If there is output, fix the indicated slide and re-pack.

### Task 22: Visual QA — render and inspect all slides

**Files:**
- Create: `/tmp/pptx-v4-qa/slide-*.jpg`

- [ ] **Step 1:** Convert to PDF, then to images.

```bash
python3 ~/.claude/plugins/cache/anthropic-agent-skills/example-skills/0f7c287eaf0d/skills/pptx/scripts/office/soffice.py --headless --convert-to pdf --outdir /tmp/pptx-v4-qa/ WORKSHOP-PRESENTATION.pptx
pdftoppm -jpeg -r 150 /tmp/pptx-v4-qa/WORKSHOP-PRESENTATION.pdf /tmp/pptx-v4-qa/slide
```

Expected: `slide-01.jpg` through `slide-22.jpg` in `/tmp/pptx-v4-qa/`.

- [ ] **Step 2:** Dispatch a Visual QA subagent.

Use the Agent tool (subagent_type=general-purpose or Explore). Prompt:

> "Visually inspect these slide images. Assume there are issues — find them.
>
> Look for:
> - Overlapping elements (text through shapes, lines through words)
> - Text overflow or cut off at edges/box boundaries
> - Decorative lines positioned for single-line text but title wrapped to two lines
> - Source citations or footers colliding with content above
> - Elements too close (< 0.3" gaps) or nearly touching
> - Uneven gaps (large empty area in one place, cramped in another)
> - Insufficient margin from slide edges (< 0.5")
> - Columns not aligned consistently
> - Low-contrast text or icons
> - Leftover placeholder content like 'XXXX', 'lorem', or 'REPLACE'
>
> Special attention: the following slides were newly edited — inspect first:
> slides 4, 5, 8, 9, 10, 12, 13, 14, 15, composition-slide (last), 17, 18, 20, 22.
>
> For each slide, list issues. Read these images:
> /tmp/pptx-v4-qa/slide-01.jpg through /tmp/pptx-v4-qa/slide-22.jpg.
>
> Report ALL issues found, including minor ones."

- [ ] **Step 3:** For each issue reported, re-edit the corresponding slide XML, repack, re-render just that slide, re-inspect.

```bash
pdftoppm -jpeg -r 150 -f N -l N /tmp/pptx-v4-qa/WORKSHOP-PRESENTATION.pdf /tmp/pptx-v4-qa/slide-fixed
```

Repeat until a full pass reveals no new issues. Do not declare success until at least one fix-and-verify cycle has run.

### Task 23: Commit and push

**Files:**
- Commit: `WORKSHOP-PRESENTATION.pptx`, `samples/capstone-plugin-template/**`, `docs/superpowers/plans/2026-04-15-workshop-v4-implementation.md` (already-committed spec stays)

- [ ] **Step 1:** Stage.

```bash
cd /Users/dingruoqian/code/work/copilot-cli-for-beginners
git add WORKSHOP-PRESENTATION.pptx samples/capstone-plugin-template
git status
```

Expected: two paths staged — pptx and template dir. (The plan and spec are committed separately before implementation starts.)

- [ ] **Step 2:** Commit.

```bash
git commit -m "$(cat <<'EOF'
feat: workshop v4 — guided tour of the code-review plugin

Restructures the 23-slide deck around the code-review plugin as a
running reference. Opening demo installs and runs the plugin. Pillars
2-5 cite its real files (SKILL.md + checks/, agent, .mcp.json, plugin.json).
Adds a new composition slide that shows plugin.json as the mental-model
payoff. Removes Pillar 6 (CI automation) as a standalone segment and
folds it into the playbook. Adds samples/capstone-plugin-template/ for
attendees to fork during the 35-min capstone.

Implements the design in docs/superpowers/specs/2026-04-15-workshop-v4-design.md.

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 3:** Push.

```bash
git push
```

Expected: push to `fork/workshop-proposal` succeeds.

---

## Self-review notes

- Timing: 15 + 10 + 15 + 20 + 20 + 30 + 15 + 10 + 10 + 35 + 10 = 190 min ✓
- Slide count after all changes: 23 − 1 (Pillar 6 deleted) + 1 (composition added) − 1 (distribution slide 21 merged) = 22 ✓
- All placeholder citations from the design spec have a matching task
- Capstone template is created, referenced from slides 17 + 20 + 22
- The plan does not touch CLAUDE.md, .github/agents, or other infrastructure
- Visual QA is mandatory — plan does not declare success without at least one fix-and-verify cycle

## Risks

- **Plugin citations drift.** If `code-review` plugin structure changes between spec-pin (`9d1b59b`) and workshop day, slides may cite files that no longer exist at those paths. Mitigation: slides cite by path inside the plugin (not by line number); structural renames require a re-pin + patch.
- **Slide 8 duplication edge cases.** `add_slide.py` duplicates layout but may carry over decorative shapes we do not want on the composition slide. Visual QA catches this.
- **35-min capstone is tight.** If workshop delivery shows it routinely overruns, post-workshop feedback should drive a v4.1 plan, not a live schedule overrun.
