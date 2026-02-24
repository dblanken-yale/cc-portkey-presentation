# Bonus: More Claude Code in Practice

---

## Weekly Summary Skill

**What it does:** Reads Claude Code session history and generates a plain-language summary of the week's work — written for a non-technical audience.

**Why it matters:** Session history is stored locally. No manual tracking. No journal. Just ask.

```
Me: /weekly-summary
Claude: Reads ~/.claude/projects/ session logs
        Generates summary grouped by project and theme
```

**Example output (paraphrased):**
> "This week focused on improving the AI chat widget's mobile experience. You worked through the configuration interface, adjusted visual sizing, and resolved a layout issue on small screens. You also reviewed a pull request related to accessibility compliance."

**Key detail:** It doesn't expose code or technical jargon — useful for status updates, standups, or personal logs.

---

## Brainstorming Before Building

**What it does:** Before writing any code, the brainstorming skill explores intent, surfaces hidden requirements, and considers tradeoffs — so implementation decisions are intentional, not accidental.

**Trigger:** Automatically invoked before creative or architectural work.

```
Me: "Add a dark mode toggle to the site header"
Claude: [brainstorming skill activates]
        - Where does the toggle live? Header, footer, user menu?
        - Stored in localStorage or user profile?
        - Does it respect OS preference (prefers-color-scheme)?
        - What happens on first visit with no preference set?
        - Accessibility: keyboard accessible, ARIA state?
```

**Reality:** Most of these questions have obvious answers once you see them. The skill surfaces them *before* you're deep in code.

---

## Systematic Debugging

**What it does:** When something breaks, this skill enforces a methodical process rather than letting Claude (or you) guess-and-check.

```
Me: "The chat button doesn't appear on mobile"
Claude: [systematic-debugging skill activates]
        Step 1: Reproduce — what viewport, what browser?
        Step 2: Isolate — CSS display:none below 993px, confirmed
        Step 3: Hypothesize — is the media query too aggressive?
        Step 4: Test one change — not a cascade of edits
        Step 5: Verify — confirm before moving on
```

**Why it matters:** Without structure, debugging becomes: change something, refresh, change something else. This forces the right order.

---

## CLAUDE.md: Project Memory

**What it is:** A markdown file in the repo root that Claude reads at the start of every session. It's how you teach Claude your project's conventions, architecture, and preferences — once, not every session.

**`/init` command:** Analyzes the codebase and generates an initial CLAUDE.md automatically.

```
Me: /init
Claude: Reads file structure, existing docs, config files
        Generates CLAUDE.md with:
          - Build/test/lint commands
          - Architecture overview
          - Project-specific conventions
```

**`/revise-claude-md` skill:** After a working session, updates CLAUDE.md with anything new learned — patterns discovered, decisions made, gotchas encountered.

**The result:** Each session starts with full context. No re-explaining the project. No "as I mentioned earlier."

---

## Automation Recommender

**What it does:** Analyzes a codebase and recommends Claude Code automations — hooks, subagents, skills, MCP servers — tailored to how that project actually works.

```
Me: /claude-automation-recommender
Claude: Reads project structure, existing workflows, tech stack
        Recommends:
          - Pre-commit hook: run linting before every commit
          - Session start hook: load project context automatically
          - Custom skill: project-specific PR template
          - MCP server: connect to internal design system docs
```

**Good for:** Onboarding a new project, or auditing an existing setup to find gaps.

---

## Custom Status Line

**What it is:** A shell script that feeds live session data into a two-line status display at the bottom of every Claude Code session.

**What it shows:**
```
[Claude Sonnet 4.6]  📁 ai_engine  |  🌿 feature/1078-chat-button ●●
██████████░ 62%  |  S:$0.18  D:$1.43  W:$4.92  |  +47 -12  |  ⏱  38m 22s
```

| Element | Meaning |
|---------|---------|
| `[Claude Sonnet 4.6]` | Active model |
| `📁 ai_engine` | Project name (basename of working dir) |
| `🌿 feature/1078-...` | Git branch |
| `●●` | Git status: green=staged, red=unstaged, blue=untracked |
| `██████████░ 62%` | Context window remaining (drains as conversation grows) |
| `S:$0.18` | Session cost |
| `D:$1.43` | Daily cost (persists across sessions) |
| `W:$4.92` | Rolling 7-day cost |
| `+47 -12` | Lines added/removed this session |
| `⏱ 38m 22s` | Session duration |

**How it works:** Claude Code exposes a `statusline` hook — a JSON payload with session metadata delivered to any shell script. The script formats and prints two lines of output.

**Bonus:** Cost data is tracked per-session in `~/.claude/cost-tracking/YYYY-MM-DD/` and summed across sessions for daily and weekly totals. No external service — just flat files.

**Lando integration:** When a `.lando.local.yml` is present, the local site URL is detected and rendered as a clickable terminal hyperlink on line 1.

---

## Parallel Agents

**What they are:** Subprocesses that Claude spawns to work on independent tasks simultaneously, each with its own context window and tool access.

**When they help:** Any time a task has parts that don't depend on each other.

```
Me: "Audit the CSS, review the Twig template, and check the PHP form class
     for accessibility issues"

Claude: [dispatching-parallel-agents skill activates]
        → Agent 1: reads chat_button.css
        → Agent 2: reads ai-engine-chat-button.html.twig
        → Agent 3: reads AiEngineChatAdmin.php
        All three run simultaneously
        Claude synthesizes findings into one report
```

**Other use cases:**
- Run tests in one agent while reading docs in another
- Explore multiple possible root causes at once during debugging
- Generate several implementations to compare before choosing one

**Types of agents available:**

| Agent | Purpose |
|-------|---------|
| `Explore` | Fast codebase search and analysis |
| `Plan` | Architecture and implementation planning |
| `Bash` | Terminal operations and git tasks |
| `feature-dev:code-reviewer` | Code review against plan and standards |
| `security-auditor` | Comprehensive security audit |
| `prd-writer` | Generate structured PRDs |

**Key detail:** Each agent gets its own context window. Spawning agents protects the main conversation from getting saturated with file contents or search results.

---

## The Bigger Picture

These aren't features you use once. They're habits that compound.

| Habit | Tool |
|-------|------|
| Start every project right | `/init` → CLAUDE.md |
| Think before building | brainstorming skill |
| Debug without guessing | systematic-debugging skill |
| Commit with intention | `/commit-conventional` |
| Update project memory | `/revise-claude-md` |
| Reflect on your week | `/weekly-summary` |
| Stay aware mid-session | custom status line |
| Parallelize independent work | agents |

**The workflow is the product.** Claude writes the code. You own the decisions.

---

## Plugins

Claude Code has a plugin marketplace. Most of these are one command to install and they just work — no configuration required.

---

### Superpowers
**Author:** Jesse Squires (open source)

The workflow backbone already referenced throughout this talk. Automatically activates brainstorming, planning, TDD, and review skills at the right moments. Runs subagents to work through implementation plans autonomously.

**Install:** `/plugin marketplace add obra/superpowers-marketplace`

---

### Ralph Loop
**Author:** Community

Named after the Ralph Wiggum coding technique. Runs Claude in a `while true` loop — you give it a task and a completion condition, and it keeps working, self-correcting, and retrying until the condition is met. Useful for long-running autonomous tasks.

```
/ralph-loop "Write tests for the chat button module" --completion-promise "All tests passing"
# Claude works until tests pass, restarting if it exits early
```

---

### Playwright (Microsoft MCP)
**Author:** Microsoft

Gives Claude a real browser. It can navigate pages, click elements, fill forms, take screenshots, and run E2E tests — all within the conversation. No separate test runner setup.

```
Me: "Open the staging site, navigate to the chat widget, and verify the
    icon changed to sparkles on mobile viewport"
Claude: [opens browser, navigates, resizes viewport, takes screenshot,
         reports what it sees]
```

---

### Context7 (Upstash MCP)
**Author:** Upstash

Live documentation lookup. Instead of Claude working from training data that might be outdated, it fetches version-specific docs and code examples directly from source repositories. Especially useful when working with APIs or frameworks that change frequently.

```
Me: "How do I implement a configuration schema in Drupal 10?"
Claude: [fetches current Drupal 10 docs]
        [answers based on actual current API, not training data]
```

---

### Security Guidance
**Author:** Anthropic

A passive hook — runs in the background whenever Claude edits files. Flags potential security issues automatically: command injection, XSS, unsafe patterns. Acts as a safety net rather than something you invoke.

---

### Frontend Design
**Author:** Community

Activates automatically for frontend work. Pushes Claude toward distinctive aesthetic choices — typography, color, motion — instead of the generic "AI default" look. Useful when you want production-quality UI, not a gray form on a white background.

---

## What Could You Build?

These are practical tools your team could create with Claude Code skills or hooks — most in an afternoon.

---

### GitHub Issue from a Vague Request

**The problem:** Someone pastes a Slack message into a ticket. No acceptance criteria. No context. No labels.

**The tool:** A skill that takes a raw description and produces a structured GitHub issue — using your team's actual template — with acceptance criteria, suggested labels, and open questions called out.

```
Me: /create-issue "The chat button looks weird on iPad"
Claude: Reads issue template from .github/ISSUE_TEMPLATE/
        Asks clarifying questions or makes reasonable assumptions
        Outputs: title, description, reproduction steps,
                 acceptance criteria, suggested labels
```

---

### Standup Notes from Git Log

**The problem:** "What did you do yesterday?" Nobody tracks this in real time.

**The tool:** A hook or skill that reads the previous day's git commits and Claude Code session history, then drafts a standup entry — grouped by project, written in plain language, ready to paste.

```
Me: /standup
Claude: Reads git log --since=yesterday across configured repos
        Reads session summaries from ~/.claude/projects/
        Outputs: "Yesterday: implemented mobile breakpoint for chat
                  button, reviewed accessibility PR, fixed staging deploy"
```

---

### Meeting Notes → Tickets

**The problem:** Good discussions happen in meetings. Actionable work gets lost.

**The tool:** Paste raw meeting notes. Get back a set of GitHub issues, each with a title, description, and acceptance criteria — ready to review and file.

```
Me: /notes-to-tickets [paste meeting notes]
Claude: Identifies distinct action items
        Groups related items
        Formats each as a GitHub issue draft
        Flags anything that needs more information before ticketing
```

---

### Onboarding Doc Generator

**The problem:** A new developer joins. The README is outdated. Tribal knowledge lives in Slack.

**The tool:** Point Claude at a repo. Get a "how this codebase actually works" document — architecture, key files, local setup, gotchas — generated from the code itself, not from memory.

```
Me: /onboard
Claude: Reads file structure, CLAUDE.md, config files, key source files
        Generates: architecture overview, local setup steps,
                   where to find things, known quirks
```

---

### Dependency Upgrade Advisor

**The problem:** `npm outdated` gives you a list. It doesn't tell you what breaks, what to prioritize, or what the migration path looks like.

**The tool:** A skill that reads your package files, checks for outdated or vulnerable dependencies, and produces a prioritized upgrade plan with links to changelogs and notes on breaking changes.

```
Me: /dep-audit
Claude: Reads package.json / composer.json
        Cross-references known breaking changes
        Outputs: prioritized list, risk level per package,
                 migration notes where relevant
```

---

### Accessibility Spot-Check

**The problem:** Full accessibility audits are slow. Most issues are caught late, in review or QA.

**The tool:** A skill that scans templates and components for common WCAG violations — missing alt text, unlabeled form fields, insufficient color contrast markup, missing ARIA roles — before a line of code goes to review.

```
Me: /a11y-check src/templates/
Claude: Reads Twig/HTML/JSX files
        Flags: missing aria-label, aria-hidden misuse,
               form inputs without labels, icon-only buttons
               without accessible text
        Outputs: file, line, issue, suggested fix
```

---

## The Common Thread

None of these require a software engineering background to commission. They require knowing:

1. What repetitive friction exists on your team
2. What the input looks like
3. What good output looks like

Claude handles the in-between.
