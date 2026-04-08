# AI-Augmented Engineering Workshop

> **Proposal Draft v1** — For review before slide conversion

---

## Workshop Overview

| Field | Details |
|-------|---------|
| **Goal** | Shift team leads from "AI as autocomplete" to "AI as delegatable specialist" — and equip them to bring this mindset and toolkit to their teams |
| **Audience** | Engineering team leads (technical, varying AI tooling experience) |
| **Format** | On-site, hands-on workshop |
| **Duration** | ~2.5 hours |
| **Prerequisites** | GitHub Copilot subscription active, Copilot CLI installed and authenticated (pre-work sent 1 week before) |

### Outcomes

By the end of this workshop, every attendee will have:

1. **Understood** the 6 pillars of agentic AI tooling and how they compose into a workflow
2. **Experienced** each pillar hands-on with guided exercises
3. **Built** a configured repo with instruction files, a custom agent, and a custom skill — artifacts they can take back to their real projects
4. **Delegated** a task to an AI agent and reviewed the output — practicing the lead's role as conductor, not performer
5. **Received** a playbook for rolling out AI-augmented workflows to their engineering teams

---

## The Mindset Shift

### From This...

```
Developer writes code → AI suggests autocomplete → Developer accepts/rejects
```

### To This...

```
Lead sets up the system (instructions, agents, skills, integrations)
    ↓
Engineer describes the problem
    ↓
AI researches, plans, and executes with embedded team standards
    ↓
Engineer reviews and approves
    ↓
AI ships (PR, commit, deploy pipeline)
```

**The key insight:** AI is not a typing assistant. It is a junior team member that follows your standards, uses your tools, and gets better the more structure you give it.

---

## The 6 Pillars

| # | Pillar | One-Line | Team Lead Relevance |
|---|--------|----------|---------------------|
| 1 | Context & Commands | How you talk to AI and direct it | Foundation — everything else builds on this |
| 2 | Instruction Files | How you set the rules for every session | **The multiplier** — set up once, every engineer benefits |
| 3 | Agents | How you create on-demand specialists | Encode expertise that's currently in people's heads |
| 4 | Skills | How you encode repeatable tasks | Team consistency without policing |
| 5 | MCP | How AI reaches external systems | Eliminates context-switching, connects to real data |
| 6 | Automation | How AI runs without you | The bridge from "tool" to "pipeline" |

---

## Agenda

| Block | Duration | Content | Format |
|-------|----------|---------|--------|
| Opening | 10 min | The AI-augmented pipeline vision + live demo | Presentation + demo |
| Pillar 1 | 15 min | Context & Commands | Concept (3 min) + hands-on (12 min) |
| Pillar 2 | 10 min | Instruction Files | Concept (3 min) + hands-on (7 min) |
| Pillar 3 | 12 min | Agents | Concept (3 min) + hands-on (9 min) |
| Pillar 4 | 12 min | Skills | Concept (3 min) + hands-on (9 min) |
| Pillar 5 | 12 min | MCP | Concept (3 min) + hands-on (9 min) |
| Pillar 6 | 10 min | Automation | Concept (3 min) + hands-on (7 min) |
| Break | 10 min | | |
| Capstone | 30 min | "Set Up Your Team's AI Workflow" | Guided exercise |
| Closing | 10 min | Rollout playbook + Q&A | Discussion |
| **Total** | **~2.5 hrs** | | |

---

## Opening: The Vision (10 min)

### What the Audience Sees

A live demo: **user story to merged PR in one terminal session**, no context-switching.

### Demo Script (abbreviated)

```
1. Pull a GitHub issue using MCP          → "List open issues in this repo"
2. Plan the approach                       → /plan <feature from the issue>
3. Switch to a specialist agent            → /agent python-reviewer
4. Implement (skill auto-triggers standards)
5. Review                                  → /review
6. Ship                                    → /pr create
```

### Key Message

> "Everything you just saw — the standards it followed, the agent it used, the checks it ran — was configured in files checked into the repo. Your engineers don't need to learn prompts. They need you to set up the system."

---

## Pillar 1: Context & Commands (15 min)

### Concept (3 min)

- The `@` syntax is how you give AI real understanding — `@file.py`, `@folder/`, multiple files at once
- Without `@`, filenames are just text. With `@`, they're loaded into context
- Commands direct the AI: `/plan` (think before coding), `/review` (check before committing), `/research` (understand before building)
- Sessions auto-save — resume with `--continue` or `--resume`

### Hands-On Exercise (12 min)

**Scenario:** A bug has been reported — "Finding books by author name doesn't work for partial names"

```
1. Start a session:                copilot
2. Reference the file:             > @samples/book-app-project/books.py What does find_by_author do?
3. Describe the symptom:           > Users report partial author names return no results. Debug why.
4. Plan the fix:                   > /plan Fix find_by_author to support partial, case-insensitive matching
5. Check context usage:            > /context
```

**Takeaway:** Specific context + specific symptoms → specific answers. The AI is only as good as the context you give it.

---

## Pillar 2: Instruction Files (10 min)

### Concept (3 min)

- `AGENTS.md` at repo root = always-on instructions for every session, every engineer
- No one needs to invoke anything — it's automatic
- `/init` generates a starter file
- This is how you encode team standards without policing

**The team lead pitch:**
> "You write your coding standards once in AGENTS.md. From that moment, every Copilot session in your repo follows them — for new hires, contractors, and senior engineers alike."

### Hands-On Exercise (7 min)

```
1. Generate a starter:             > /init
2. Open and edit AGENTS.md to add your team's rules, e.g.:
   - "Always use type hints in Python"
   - "Follow conventional commits"
   - "Never commit secrets or .env files"
   - "All public functions must have error handling"
3. Test it:                        > @books.py Review this file
4. Compare:                        The review now enforces YOUR standards, not generic ones
```

**Takeaway:** Instruction files are the highest-leverage thing a team lead can set up. 5 minutes of writing, permanent impact.

---

## Pillar 3: Agents (12 min)

### Concept (3 min)

- Agents = on-demand specialists (vs instruction files = always-on baseline)
- A `.agent.md` file: YAML frontmatter + markdown instructions
- Stored in `.github/agents/` (team) or `~/.copilot/agents/` (personal)
- Invoke with `/agent <name>` or `copilot --agent <name>`
- Think of it as: **Instruction files set the floor. Agents raise the ceiling.**

### Hands-On Exercise (9 min)

**Before/After Demo (4 min):**

```
1. Generic prompt:                 > Review @books.py for quality
2. Note the output — broad, generic suggestions
3. Switch agent:                   > /agent python-reviewer
4. Same prompt:                    > Review @books.py for quality
5. Note the difference — specific, standards-aware, actionable
```

**Build Your Own (5 min):**

```
Create .github/agents/security-reviewer.agent.md:

---
description: "Reviews code for security vulnerabilities and OWASP top 10 issues"
---

You are a security-focused code reviewer. For every file reviewed:
1. Check for injection vulnerabilities (SQL, command, path traversal)
2. Check for authentication/authorization gaps
3. Check for sensitive data exposure (hardcoded secrets, logged PII)
4. Check for input validation on all external inputs
5. Rate each finding: Critical / High / Medium / Low
6. Suggest specific fixes, not just warnings
```

**Takeaway:** You just encoded a security expert's checklist into a reusable, shareable agent. Any engineer on your team can now invoke `/agent security-reviewer`.

---

## Pillar 4: Skills (12 min)

### Concept (3 min)

- Skills = task-specific instructions that auto-trigger based on what you ask
- You don't invoke them — Copilot matches your prompt to the skill's `description` and loads it silently
- A folder + `SKILL.md`: YAML frontmatter (`name`, `description`) + step-by-step instructions
- Stored in `.github/skills/<name>/` (team) or `~/.copilot/skills/<name>/` (personal)
- **Agents change who the AI is. Skills change how it does a specific task.**

### Hands-On Exercise (9 min)

**Build a PR Review Skill (9 min):**

```
Create .github/skills/pr-review/SKILL.md:

---
name: "pr-review"
description: "Review a pull request for code quality, security, and completeness"
---

When reviewing a pull request:

## Checklist
1. Does the PR have a clear title and description?
2. Are all changes covered by tests?
3. Are there any security concerns? (input validation, auth, secrets)
4. Does the code follow the project's style conventions?
5. Are there any performance concerns? (N+1 queries, large loops, memory)
6. Is error handling appropriate?
7. Are edge cases considered?

## Output Format
- Use a markdown checklist with PASS / FAIL / NEEDS DISCUSSION
- For each FAIL, provide the file, line, and suggested fix
- End with an overall verdict: APPROVE / REQUEST CHANGES
```

**Test it:**

```
> Review the changes in this project for a pull request
(The skill auto-triggers — notice you didn't invoke it by name)
```

**Takeaway:** Every PR review on your team now follows the same 7-point checklist — without anyone having to remember the checklist.

---

## Pillar 5: MCP (12 min)

### Concept (3 min)

- MCP = Model Context Protocol — lets AI reach out to external services
- Think of it as: **browser extensions for Copilot**
- Without MCP: you copy-paste data from GitHub/Jira/docs into the terminal
- With MCP: Copilot fetches it directly

| Server | What It Accesses | Setup Required |
|--------|-----------------|----------------|
| GitHub | Issues, PRs, commits, repos | Built-in — just `/login` |
| Filesystem | Local files beyond `@` references | Config file |
| Context7 | Live library documentation | Config file |

### Hands-On Exercise (9 min)

```
1. Try GitHub MCP immediately:     > List the 5 most recent commits in this repository
2. Fetch issue context:            > Show me the open issues in this repository
3. Combine with planning:          > Based on issue #X, create a technical plan for implementation
4. Combine with agents:            > /agent security-reviewer
                                   > Review the changes in the most recent PR
```

**Takeaway:** The AI just pulled live data from GitHub, planned work based on it, and reviewed it through a security lens — all without you leaving the terminal or copy-pasting anything.

---

## Pillar 6: Automation (10 min)

### Concept (3 min)

- Programmatic mode: `copilot -p "prompt"` — one-shot, no interaction, scriptable
- This is how AI goes from "tool I use" to "step in my pipeline"
- Use cases:
  - Commit messages: `copilot -p "Generate a conventional commit message for: $(git diff --staged)"`
  - PR descriptions: `copilot -p "Generate a PR description for: $(git log main..HEAD)"`
  - Pre-commit hooks: run security checks on every commit automatically
  - CI integration: embed AI checks in your pipeline

### Hands-On Exercise (7 min)

```
1. Generate a commit message:      copilot -p "Generate a conventional commit message for: $(git diff --staged)"
2. Generate a PR description:      copilot -p "Summarize these changes for a PR description: $(git log --oneline -5)"
3. Discuss: where in YOUR team's pipeline would you embed this?
```

**Takeaway:** Everything you've learned today can run without a human in the loop. That's the path from "developer tool" to "engineering platform."

---

## Break (10 min)

---

## Capstone Exercise: "Set Up Your Team's AI Workflow" (30 min)

### The Scenario

> You are the tech lead for a team that maintains a Python web application. A new user story has just landed:
>
> **"As a library user, I want to search for books by publication year range, so I can find books from a specific era."**
>
> Your job is not to implement this. Your job is to **set up the system** so that when your engineer picks this up tomorrow morning, the AI-augmented workflow is ready to go.

### What You'll Build

By the end of this exercise, your repo will contain:

```
.github/
├── agents/
│   └── python-reviewer.agent.md      ← Your team's code quality specialist
├── skills/
│   └── pr-review/
│       └── SKILL.md                   ← Your team's PR review checklist
AGENTS.md                              ← Always-on team standards
```

And you'll have demonstrated:
- Researching the problem using MCP
- Planning the implementation using `/plan`
- Delegating (or briefing) the work
- Reviewing the output

### Step-by-Step Guide

#### Phase 1: Research & Plan (10 min)

```
1. Start a session:                copilot
2. Name it:                        > /rename capstone-workshop
3. Pull context via MCP:           > What are the open issues in this repository?
4. Understand the codebase:        > @samples/book-app-project/ Give me an overview of this project's
                                     architecture and how book search currently works
5. Plan the feature:               > /plan Add a search_by_year_range function that accepts a start_year
                                     and end_year, returns all books published within that range inclusive,
                                     handles edge cases like reversed ranges and missing years
6. Review the plan — would you approve this if an engineer brought it to you?
```

#### Phase 2: Configure the Repo (10 min)

Now set up the guardrails your engineer will work within:

```
7. Set up instruction file:        > /init
8. Edit AGENTS.md with your team's standards:
   - Type hints required on all functions
   - Docstrings on all public functions
   - Input validation on user-facing parameters
   - Conventional commit messages
   - Tests required for all new functions

9. Create your agent:              Create .github/agents/python-reviewer.agent.md
   (Use the one from the Agents pillar exercise, or customize it)

10. Create your skill:             Create .github/skills/pr-review/SKILL.md
    (Use the one from the Skills pillar exercise, or customize it)
```

#### Phase 3: Delegate & Review (10 min)

Hand off the work and review what comes back:

```
11. Delegate the task:             > /delegate Implement the search_by_year_range feature as described
                                     in the plan. Include comprehensive tests. Follow the project's
                                     existing patterns in books.py.

    OR simulate delegation:        > Implement the search_by_year_range function following the plan
                                     and the standards in AGENTS.md

12. Review the output:             > /review
13. Check with your agent:         > /agent python-reviewer
                                   > Review the implementation for quality and completeness
14. Generate the commit:           copilot -p "Generate a conventional commit message for: $(git diff --staged)"
```

### Reflection Questions

After completing the exercise, consider:

1. **What standards would you add to AGENTS.md for your real project?**
2. **What agents would your team need?** (e.g., `database-reviewer`, `api-designer`, `test-architect`)
3. **What skills would save your team the most time?** (e.g., `incident-response`, `migration-checklist`, `release-notes`)
4. **Where in your current pipeline would programmatic mode (`copilot -p`) add the most value?**

---

## Closing: Your Rollout Playbook (10 min)

### Week 1: Foundation

- [ ] Add `AGENTS.md` to your team's main repos with your coding standards
- [ ] Run `/init` to generate starter config
- [ ] Share Copilot CLI install instructions with your team
- [ ] Ensure all team members have active Copilot subscriptions

### Week 2: First Agents & Skills

- [ ] Create 1-2 agents your team would use daily (e.g., `code-reviewer`, `security-reviewer`)
- [ ] Create 1 skill for your most common task (e.g., `pr-review`)
- [ ] Store in `.github/` so they're shared via git

### Week 3: Team Adoption

- [ ] Run a 1-hour hands-on session with your engineers (Pillars 1-4 only)
- [ ] Establish the habit: `/review` before every commit
- [ ] Set up programmatic mode for commit messages and PR descriptions

### Week 4: Measure & Iterate

- [ ] Collect feedback: what agents/skills are being used? What's missing?
- [ ] Iterate on AGENTS.md based on the code quality you're seeing
- [ ] Consider: MCP integrations, pre-commit hooks, CI integration

### 3 Starter Templates Every Team Should Have

1. **`AGENTS.md`** — Your team's coding standards (always-on)
2. **`.github/agents/code-reviewer.agent.md`** — Quality-focused review agent
3. **`.github/skills/pr-review/SKILL.md`** — Standardized PR review checklist

---

## Appendix: Pre-Work Email Template

> **Subject: AI-Augmented Engineering Workshop — Setup Required**
>
> Hi team,
>
> Please complete the following before the workshop on [DATE]:
>
> 1. **Verify your Copilot subscription** is active: [github.com/settings/copilot](https://github.com/settings/copilot)
> 2. **Install Copilot CLI:**
>    - macOS: `brew install gh-copilot` (or via npm: `npm install -g @githubnext/github-copilot-cli`)
>    - Windows: `winget install GitHub.CopilotCLI`
> 3. **Authenticate:** Run `copilot` then `/login`
> 4. **Verify:** Run `copilot -p "Say hello"` — you should get a response
>
> If you hit any issues, reply to this email. We'll also have 5 min at the start for troubleshooting.
>
> Alternatively, we'll have GitHub Codespaces available as a fallback — everything is pre-installed there.

---

## Appendix: Facilitator Notes

### Environment Options

| Option | Pros | Cons |
|--------|------|------|
| **GitHub Codespaces** (recommended) | Zero setup, consistent environment, pre-installed tools | Requires internet, may need org approval |
| **Local install** | No dependency on cloud, faster for experienced devs | Setup variance, troubleshooting time |

### Common Issues

- "Copilot not responding" → Check subscription status and `/login`
- "Agent not loading" → Verify file is in `.github/agents/` with `.agent.md` extension
- "Skill not triggering" → Check that `description` field matches natural language; run `/skills reload`
- "MCP not working" → Run `/mcp show` to verify server status

### Timing Adjustments

- If running short: cut Pillar 6 (Automation) to 5 min concept-only, no exercise
- If running long: extend capstone Phase 1 (Research & Plan) — that's where the best discussions happen
- If audience is very technical: shorten concepts to 1-2 min each, extend hands-on time
- If audience is less technical: extend concepts, pair up for exercises
