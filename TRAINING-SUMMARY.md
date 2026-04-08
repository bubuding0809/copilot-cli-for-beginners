# GitHub Copilot CLI — Training Summary

> Internal reference document. Use this to formulate team training on GitHub Copilot CLI.
> Updated as each chapter is completed.

---

## Progress Tracker

| Chapter | Title | Status |
|:-------:|-------|--------|
| 00 | Quick Start | ✅ Complete |
| 01 | First Steps | ✅ Complete |
| 02 | Context and Conversations | ✅ Complete |
| 03 | Development Workflows | ✅ Complete |
| 04 | Create Specialized AI Assistants | ✅ Complete |
| 05 | Automate Repetitive Tasks | ✅ Complete |
| 06 | Connect to GitHub, Databases & APIs | ✅ Complete |
| 07 | Putting It All Together | ✅ Complete |

---

## Chapter 00: Quick Start

**One-line summary:** Get Copilot CLI installed, authenticated, and verified in ~10 minutes.

### Key Takeaways

1. **Multiple installation methods** — Homebrew, WinGet, npm, or install script. Use what fits your system.
2. **GitHub Codespaces = zero setup** — Python, pytest, and Copilot CLI are pre-installed; ideal for quick onboarding.
3. **One-time authentication** — Run `copilot` then `/login`. Token persists across sessions until it expires.
4. **Verify with the sample app** — `cd samples/book-app-project && python book_app.py list` should return 5 books.

### Training Notes

- For team onboarding, Codespaces is the fastest path to a working environment with no local setup required.
- Ensure all team members have an active Copilot subscription before the session ([github.com/settings/copilot](https://github.com/settings/copilot)).

---

## Chapter 01: First Steps

**One-line summary:** Learn the 3 interaction modes and essential slash commands that cover 90% of daily use.

### Key Takeaways

1. **Interactive mode** (`copilot`) — Best for exploration and multi-turn conversations. Context carries forward between prompts, like a conversation with a colleague.
2. **Plan mode** (`/plan <task>`) — Best for complex tasks. Review and approve the step-by-step plan before any code is written.
3. **Programmatic mode** (`copilot -p "..."`) — Best for automation and scripts. One-shot input → output, no interaction needed.
4. **Essential slash commands** cover 90% of daily use:
   - `/help` — Show all commands
   - `/clear` — Fresh start (no history saved)
   - `/plan` — Plan before coding
   - `/model` — Switch AI model
   - `/exit` — End session
5. **Start with Interactive.** Graduate to Plan for complex tasks, Programmatic for automation.

### Training Notes

- The key mental model: **Interactive = conversation, Plan = GPS route, Programmatic = drive-through**.
- Encourage team members to start every unfamiliar task in Interactive mode — it's the lowest friction entry point.
- Plan mode is particularly valuable before large refactors or new feature work where the approach matters.

---

## Chapter 02: Context and Conversations

**One-line summary:** The `@` syntax gives Copilot real understanding of your codebase; sessions auto-save so you never lose your place.

### Key Takeaways

1. **`@` syntax references files and directories** — `@file.py`, `@folder/`, `@file1.py @file2.py`. Without `@`, Copilot treats filenames as plain text.
2. **Cross-file analysis is the real superpower** — Referencing multiple files at once catches bugs, duplicate logic, and architectural issues that single-file review misses entirely.
3. **Sessions auto-save** — Exit normally with `/exit`. Resume with:
   - `copilot --continue` — picks up the most recent session
   - `copilot --resume` — shows an interactive list of past sessions
4. **Context windows have limits** — Manage them with:
   - `/context` — check current token usage
   - `/compact` — summarize history to free space
   - `/clear` — abandon session and start fresh
   - `/rewind` — roll back to any earlier point in the conversation
5. **Be specific with file references** — `@books.py` uses far less context than `@samples/book-app-project/`. Go broad for exploration, specific for focused work.
6. **Name sessions with `/rename`** — Makes sessions findable and resumable across days.

### Training Notes

- Cross-file analysis is the capability that most surprises developers new to Copilot CLI — demonstrate it early in training.
- The session persistence story is compelling for teams: start a review on Monday, resume Wednesday, no re-explaining needed.
- Establish a team convention for session naming (e.g., `feature/auth-review`, `bug/login-fix`) so sessions are easy to share or hand off.

---

## Chapter 03: Development Workflows

**One-line summary:** Copilot CLI plugs into 5 existing workflows — code review, refactoring, debugging, test generation, and git integration.

### Key Takeaways

**Workflow 1 — Code Review:**
1. **Specific prompts get better results** — "Review for input validation, error handling, and edge cases" beats "Review this code"
2. **Use `/review` for git changes** — Built-in agent optimized for high signal-to-noise output on staged/unstaged changes
3. **Ask for structured output** — "Create a markdown checklist categorized by severity" gives you something actionable

**Workflow 2 — Refactoring:**
4. **Generate tests before refactoring** — Tests act as a safety net; if anything breaks after the refactor, you'll know immediately
5. **Cross-file refactoring is possible** — Reference two files with `@` and ask Copilot to move or consolidate logic between them

**Workflow 3 — Debugging:**
6. **Describe the symptom, not "find bugs"** — "Users report X happens when Y" gives Copilot the context to find the root cause
7. **Copilot surfaces related bugs** — While fixing one issue it often spots others nearby in the same file
8. **Paste stack traces directly** — Include the error message and file reference; Copilot maps it to the source

**Workflow 4 — Test Generation:**
9. **Structured prompts produce 15+ tests** — List the scenarios you want covered; Copilot adds edge cases you wouldn't have thought of (unicode, file corruption, empty collections, concurrent access)
10. **Always specify the framework** — Add "using pytest" to avoid getting unittest syntax

**Workflow 5 — Git Integration:**
11. **Commit messages from `git diff`** — `copilot -p "Generate a conventional commit message for: $(git diff --staged)"` is immediately useful from day one
12. **PR descriptions from `git log`** — Pipe branch history into Copilot to auto-generate summary, rationale, testing notes, and breaking changes
13. **`/delegate` for background work** — Hands off a well-defined task to GitHub's cloud agent; it opens a draft PR and works while you focus elsewhere

### Commands Introduced

| Command | Purpose |
|---------|---------|
| `/review` | Built-in code-review agent on staged/unstaged changes |
| `/diff` | Show all changes made in the current session |
| `/pr` | Create, view, or fix a PR for the current branch |
| `/delegate` | Hand off a task to the GitHub Copilot cloud agent |
| `/research` | Deep research using GitHub and web sources before coding |

### The End-to-End Bug-Fix Workflow

The flagship demo for internal training — chains all 5 workflows into one scenario:

```
Bug reported: "Finding books by author name doesn't work for partial names"

1. DIAGNOSE  copilot → @file.py + symptom description → root cause identified
2. FIX       > Fix the find_by_author function to support partial, case-insensitive matching
3. TEST      > Generate pytest tests for full match, partial match, case-insensitive, not found
4. REVIEW    > /review
5. COMMIT    copilot -p "Generate a conventional commit message for: $(git diff --staged)"
```

From bug report to committed fix, entirely in the terminal.

### Training Notes

- The debugging pattern ("describe the symptom") is the single most transferable skill from this chapter — applicable immediately on any codebase.
- The end-to-end bug-fix workflow is the best demo for a training session — it tells a complete story in ~10 minutes.
- `/review` before every commit is a low-effort habit with high payoff; position it as the new pre-commit checklist.
- For teams new to conventional commits, the git integration workflow alone justifies learning Copilot CLI.

---

## Chapter 04: Create Specialized AI Assistants

**One-line summary:** Give Copilot a specific persona with built-in standards — same prompt, dramatically better output.

### Key Takeaways

1. **Agents = specialists, not generalists** — A `python-reviewer` agent automatically applies type hints, PEP 8, docstrings, and error handling to every response, without you asking
2. **Built-in agents you already know** — `/plan` and `/review` are agents. `Explore` and `Task` run automatically behind the scenes
3. **Custom agents are just `.agent.md` files** — YAML frontmatter (`description` required) + markdown instructions. That's it
4. **Two storage locations:**
   - `.github/agents/` — project-scoped, shared with team via git
   - `~/.copilot/agents/` — personal, available in every project
5. **Invoke with `/agent` or `--agent`** — switch agents mid-session anytime
6. **Instruction files are always-on context** — `AGENTS.md` at repo root applies to every session automatically, no `/agent` needed. Run `/init` to generate one
7. **Agents vs slash commands** — commands *do things* (clear, rename, exit); agents *become something* (a Python expert, a security auditor)

### Commands Introduced

| Command | Purpose |
|---------|---------|
| `/agent` | List and select available agents |
| `/init` | Generate project config files (AGENTS.md, instruction files) |
| `copilot --agent <name>` | Launch straight into a session with a specific agent |

### Training Notes

- The before/after demo (generic vs `python-reviewer` agent on the same prompt) is the best way to show agents' value — do this first in training
- Position `AGENTS.md` as a team asset: encode your coding standards once, everyone benefits automatically
- Naming agents clearly matters — `security-reviewer` is discoverable, `my-agent` is not

---

## Chapter 05: Automate Repetitive Tasks (Skills)

**One-line summary:** Skills are task-specific instructions Copilot loads automatically when your prompt matches — encode your team's best practices once, apply them forever.

### Key Takeaways

1. **Skills auto-trigger** — Copilot reads your prompt, matches it against skill descriptions, and loads the relevant skill silently. Just ask naturally
2. **The `description` field is everything** — It's how Copilot decides whether to load your skill. Use the natural words you'd actually say when asking
3. **A skill is just a folder + `SKILL.md`** — YAML frontmatter (`name` and `description` required) + markdown instructions in `.github/skills/<skill-name>/`
4. **Two storage locations:**
   - `.github/skills/` — project-scoped, shared with team via git
   - `~/.copilot/skills/` — personal, available in every project
5. **Direct invocation** — `/skill-name <prompt>` triggers a skill explicitly when you want to be sure it's used
6. **Skills vs Agents vs Slash Commands:**
   - Slash commands → control the session (`/clear`, `/exit`)
   - Agents → change how Copilot thinks (persona + expertise)
   - Skills → provide specific step-by-step task instructions (auto-loaded)
7. **Team consistency is the real value** — a `pr-review` skill means every developer applies the same 10-point checklist, including new hires

### Commands Introduced

| Command | Purpose |
|---------|---------|
| `/skills list` | Show all installed skills |
| `/skills reload` | Pick up changes after editing a SKILL.md |
| `/skills info <name>` | Get details about a specific skill |

### Training Notes

- Lead with the consistency story: "Without skills, everyone writes different review prompts and remembers different things. With a skill, the whole team applies the same standards automatically."
- The `description` field tip is the most common stumbling block — spend time on this in training
- Good starter skills for any team: `pr-review`, `commit-message`, `security-audit`

---

## Chapter 06: Connect to GitHub, Databases & APIs (MCP)

**One-line summary:** MCP connects Copilot to live external services — GitHub issues, file systems, up-to-date docs — so it can fetch data itself instead of waiting for you to paste it.

### Key Takeaways

1. **MCP = browser extensions for Copilot** — without it, Copilot only sees what you `@` reference; with it, Copilot can proactively reach out to GitHub, your filesystem, documentation services
2. **GitHub MCP is built-in and free** — no config needed, just `/login`. Try it immediately: `> List the recent commits in this repository`
3. **Filesystem and Context7 require one config file** — add `~/.copilot/mcp-config.json` with both servers; no API keys required for either
4. **Multi-server sessions are the real power** — combining GitHub + Filesystem + Context7 in one session eliminates context-switching entirely
5. **Check status with `/mcp show`** — see which servers are enabled; use `/mcp add`, `/mcp enable`, `/mcp disable` to manage them
6. **Custom MCP servers** exist for connecting internal APIs, databases, or any service (optional advanced topic)

### The 3 Core Servers

| Server | What It Accesses | Setup |
|--------|-----------------|-------|
| **GitHub** | Repos, issues, PRs, commits, branches | Built-in — no config |
| **Filesystem** | Local project files, directory structure | `~/.copilot/mcp-config.json` |
| **Context7** | Live library documentation | `~/.copilot/mcp-config.json` |

### Commands Introduced

| Command | Purpose |
|---------|---------|
| `/mcp show` | See all configured servers and status |
| `/mcp add` | Add a new server interactively |
| `/mcp enable/disable <name>` | Toggle a server on/off |

### Training Notes

- Start the demo with the GitHub MCP "List recent commits" — it works immediately and is instantly impressive
- The issue-to-PR workflow (read issue → implement fix → run tests → create PR, all in one session) is the best end-to-end MCP demo for a team
- Position the config file as a one-time setup: 5 minutes to configure, permanent productivity gain

---

## Chapter 07: Putting It All Together

**One-line summary:** Everything combines here — go from idea to merged PR in a single terminal session without switching context once.

### Key Takeaways

1. **Context before analysis** — Always gather context first (MCP for GitHub data, `@` for files) before switching to an agent for analysis. Agents without context give generic answers
2. **You are the conductor** — Direct specialists like an architect. Agents handle the details, you handle the vision
3. **One session, one feature** — The full workflow (plan → design → implement → test → review → ship) happens without leaving the terminal
4. **One feature per session** — Use `/rename` to label sessions clearly; keeps history clean and resumable
5. **`/review` before every commit** — Non-negotiable habit; catches issues before they're permanent
6. **Encode workflows in the repo** — Put agents, skills, and instruction files in `.github/` so new team members inherit your workflows automatically
7. **Pre-commit hooks automate quality gates** — Use `copilot -p` in a git hook for automated security checks on every staged file (optional but powerful for teams)

### The Integration Pattern

```
GATHER CONTEXT   →  MCP (GitHub issues, repo history) + @ (files)
ANALYSE & PLAN   →  Agents (specialist expertise on design)
EXECUTE          →  Skills (auto-loaded standards) + implementation
COMPLETE         →  MCP (create PR, link to issue, run tests)
```

### The Full Workflow (Idea to PR)

```
1. PLAN      /plan <feature description>
2. DESIGN    /agent → python-reviewer → @file.py Design the approach
3. TESTS     /agent → pytest-helper  → Design test cases
4. IMPLEMENT > Add [feature] to [file]
5. TEST GEN  > Generate comprehensive tests
6. REVIEW    /review
7. SHIP      /pr create
```

### 3 Scenarios Worth Demoing to Teams

| Scenario | What It Shows |
|----------|--------------|
| Idea → merged PR | The full workflow end-to-end in one session |
| Bug investigation | GitHub issue → research → fix → PR without leaving terminal |
| Onboarding to new codebase | Architecture overview → flow walkthrough → find first issue → start contributing |

### Training Notes

- This chapter is best experienced as a live demo, not a walkthrough — pick one scenario and run it in real-time
- The "idea to PR" workflow is the most compelling for leadership audiences — it shows the full productivity story
- Emphasise the repo-as-workflow-library idea: agents, skills, and instructions in `.github/` mean new hires get your team's best practices for free on day one
- The pre-commit hook is optional but valuable for teams that want automated quality gates without changing their existing workflow

---

## Quick Reference: Core Commands

| Command | Mode | Purpose |
|---------|------|---------|
| `copilot` | Interactive | Start a session |
| `copilot -p "..."` | Programmatic | One-shot prompt |
| `copilot --continue` | — | Resume last session |
| `copilot --resume` | — | Pick a session to resume |
| `copilot --agent <name>` | — | Launch with a specific agent |
| `/plan <task>` | Interactive | Plan before coding |
| `/review` | Interactive | Code-review agent on git changes |
| `/research <topic>` | Interactive | Deep research before coding |
| `/diff` | Interactive | Review session changes |
| `/agent` | Interactive | List and switch agents |
| `/skills list` | Interactive | Show installed skills |
| `/skills reload` | Interactive | Pick up skill file changes |
| `/mcp show` | Interactive | See configured MCP servers |
| `/init` | Interactive | Generate project config files |
| `/rename <name>` | Interactive | Name the current session |
| `/context` | Interactive | Check token usage |
| `/compact` | Interactive | Summarize history to free context |
| `/clear` | Interactive | Fresh start, no history saved |
| `/rewind` | Interactive | Roll back conversation |
| `/exit` | Interactive | End session (auto-saved) |

---

## Key Patterns for Internal Training

### The `@` File Reference Pattern
```
> Review @path/to/file.py for [specific concern]
> @file1.py @file2.py How do these work together?
> @folder/ Give me an overview of this project
```

### The Bug Description Pattern
```
> @relevant-file.py Users report: "[symptom]". Debug why.
```

### The Safe Refactor Pattern
```
> @file.py Generate tests for current behavior
> Now refactor [specific thing]. Preserve all existing behavior.
```

### The Full Bug-Fix Workflow
```
1. copilot → diagnose with @file + symptom description
2. Fix the issue
3. Generate tests for the fix
4. /review to check staged changes
5. copilot -p "Generate a conventional commit message for: $(git diff --staged)"
```

### The Session Persistence Pattern
```
1. copilot → /rename <meaningful-name>
2. Do work → /exit
3. copilot --continue (or --resume to pick session)
4. Ask follow-up without re-explaining context
```

### The Idea-to-PR Pattern
```
1. /plan <feature description>
2. /agent → python-reviewer → design the approach
3. /agent → pytest-helper  → design test cases
4. Implement the feature
5. Generate comprehensive tests
6. /review
7. /pr create
```

---

## Suggested Internal Training Structure

### Session 1: Core Foundations (~90 min)
Covers Chapters 00–03. Suitable for all developers.

| Time | Topic | Demo |
|------|-------|------|
| 15 min | Install + authenticate | Chapter 00 setup |
| 20 min | 3 interaction modes | Interactive vs Plan vs Programmatic |
| 20 min | `@` context + sessions | Cross-file analysis demo |
| 35 min | 5 development workflows | End-to-end bug-fix workflow |

**Outcome:** Attendees leave productive immediately.

---

### Session 2: Power Features (~90 min)
Covers Chapters 04–07. Suitable for developers who completed Session 1.

| Time | Topic | Demo |
|------|-------|------|
| 20 min | Agents | Generic vs `python-reviewer` on the same prompt |
| 20 min | Skills | `code-checklist` or `pr-review` auto-triggering |
| 20 min | MCP | GitHub MCP → "List recent commits" live demo |
| 30 min | Full workflow | Idea → PR in one session |

**Outcome:** Attendees leave with the full toolkit and a repo-based workflow strategy.

---

### Recommended Demo Order for Maximum Impact

1. **Cross-file analysis** (Ch 02) — surprises people immediately
2. **End-to-end bug-fix** (Ch 03) — tells a complete story
3. **Generic vs agent** (Ch 04) — same prompt, dramatically better output
4. **GitHub MCP "list commits"** (Ch 06) — live data, no copy-paste
5. **Idea to PR** (Ch 07) — the full productivity argument
