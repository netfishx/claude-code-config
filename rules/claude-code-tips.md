# Claude Code Workflow

---

## TL;DR - The 5 Tips That Actually Matter

| Tip | Why It's a Game-Changer |
|-----|------------------------|
| **Run 3-5 Claudes in parallel** | This is the #1 productivity unlock. Use git worktrees, each running its own Claude session. |
| **Start complex tasks in Plan mode** | Pour energy into the plan → Claude 1-shots the implementation. Shift+Tab twice. |
| **Invest in your CLAUDE.md** | After every correction: "Update your CLAUDE.md so you don't make that mistake again." |
| **Give Claude a way to verify its work** | Feedback loops 2-3x the quality. Tests, browser checks, bash commands - whatever fits your domain. |
| **Use subagents extensively** | Append "use subagents" to throw more compute at problems. Keep main context clean. |

---

## Quick Setup Checklist

Get running in 10 minutes:

```
.claude/
├── CLAUDE.md              # Project-specific instructions
├── settings.json          # Permission allowlists
├── commands/              # Custom slash commands
│   └── commit-push-pr.md
└── agents/                # Subagent definitions
    ├── code-simplifier.md
    └── verify-app.md
```

**Immediate actions:**
- [ ] Set up 3-5 git worktrees for parallel Claude sessions
- [ ] Create or enhance your `CLAUDE.md` with project-specific rules
- [ ] Run `/permissions` to pre-allow safe commands
- [ ] Run `/statusline` to show context usage and git branch
- [ ] Optional: Install [Ghostty](https://ghostty.org/) terminal

---

## Quick Reference Table

| Category | Command/Action | When to Use |
|----------|---------------|-------------|
| **Parallel work** | `git worktree add .claude/worktrees/my-worktree origin/main` | Starting a new parallel task |
| **Plan mode** | `Shift+Tab` (twice) | Beginning any complex task |
| **Subagents** | Append "use subagents" to prompt | Need more compute on a problem |
| **Permissions** | `/permissions` | Pre-allow safe bash commands |
| **Status bar** | `/statusline` | Customize context/branch display |
| **Learning mode** | `/config` → Explanatory output | Want Claude to explain the "why" |
| **Verify work** | "Prove to me this works" | Before finalizing any PR |
| **After mediocre fix** | "Knowing everything you know now, scrap this and implement the elegant solution" | Claude's first attempt is messy |
| **Code review** | "Grill me on these changes and don't make a PR until I pass your test" | Want Claude as reviewer |

---

## The Full Playbook

### 1. Parallel Execution - The #1 Productivity Unlock

**This is the single biggest productivity unlock and the top tip from the team.**

#### Git Worktrees (Team Preferred)

```bash
# Add a new worktree
git worktree add .claude/worktrees/my-worktree origin/main

# Navigate to it
cd .claude/worktrees/my-worktree && claude
```

Most of the Claude Code team prefers worktrees - it's why @amorriscode built native support for them into the Claude Desktop app.

#### Alternative Approaches
- **Multiple git checkouts** with shell aliases (za, zb, zc) to hop between them in one keystroke
- **Dedicated "analysis" worktree** only for reading logs and running BigQuery queries

#### Boris's Personal Setup
- 5 Claudes in terminal (tabs numbered 1-5) with system notifications for when Claude needs input
- 5-10 additional Claudes on [claude.ai/code](https://claude.ai/code) running in parallel
- Hand off local sessions to web using `&`, or manually kick off sessions in Chrome
- Use `--teleport` to go back and forth between local and web
- Start sessions from phone (Claude iOS app) every morning and check on them later

**Docs:** [code.claude.com/docs/en/common-workflows](https://code.claude.com/docs/en/common-workflows)

---

### 2. Plan Mode for Complex Tasks

**Start every complex task in plan mode.** Pour your energy into the plan so Claude can 1-shot the implementation.

```
> try "refactor cli.tsx"

⊞ plan mode on (shift+Tab to cycle)
```

#### Team Approaches
- One person has Claude write the plan, then spins up a **second Claude to review it as a staff engineer**
- Another switches back to plan mode and re-plans the moment something goes sideways (don't keep pushing!)
- Some explicitly tell Claude to enter plan mode for **verification steps**, not just the build

#### Boris's Approach
> "Most sessions start in Plan mode (shift+tab twice). If my goal is to write a Pull Request, I will use Plan mode, and go back and forth with Claude until I like its plan. From there, I switch into auto-accept edits mode and Claude can usually 1-shot it. A good plan is really important!"

---

### 3. Invest in Your CLAUDE.md

After every correction, end with: **"Update your CLAUDE.md so you don't make that mistake again."**

Claude is eerily good at writing rules for itself. Ruthlessly edit your CLAUDE.md over time. Keep iterating until Claude's mistake rate measurably drops.

#### Team Approach
The Claude Code team shares a single `CLAUDE.md` for their repo. It's checked into git, and the whole team contributes multiple times a week.

```
Memory files  · /memory
├ ~/.claude/CLAUDE.md: 76 tokens
└ CLAUDE.md: 4k tokens
```

#### Pro Tips
- **Notes directory pattern:** Tell Claude to maintain a notes directory for every task/project, updated after every PR. Point CLAUDE.md at it.
- **Code review integration:** Tag `@.claude` on coworkers' PRs to add something to the CLAUDE.md as part of the PR. Use the [Claude Code GitHub action](https://github.com/anthropics/claude-code-action).

#### Example CLAUDE.md Structure

```markdown
# Development Workflow

**Always use `bun`, not `npm`.**

# 1. Make changes

# 2. Typecheck (fast)
bun run typecheck

# 3. Run tests
bun run test -- -t "test name"    # Single suite
bun run test:file -- "glob"       # Specific files

# 4. Lint before committing
bun run lint:file -- "file.ts"    # Specific files
bun run lint                      # All files

# 5. Before creating PR
bun run lint:claude && bun run test
```

---

### 4. Create Custom Skills & Slash Commands

**If you do something more than once a day, turn it into a skill or command.**

Skills live in `.claude/commands/` and are checked into git so Claude can use these workflows too.

#### Ideas from the Team
- `/techdebt` slash command - run at end of every session to find and kill duplicated code
- Slash command that syncs 7 days of Slack, GDrive, Asana, and GitHub into one context dump
- Analytics-engineer-style agents that write dbt models, review code, and test changes in dev

#### Boris's Example
> "Claude and I use a `/commit-push-pr` slash command dozens of times every day. The command uses inline bash to pre-compute git status and a few other pieces of info to make the command run quickly and avoid back-and-forth with the model."

#### Subagents as Automated Workflows

```
.claude/
  └ agents/
      ├ build-validator.md
      ├ code-architect.md
      ├ code-simplifier.md
      ├ oncall-guide.md
      └ verify-app.md
```

**Docs:** [code.claude.com/docs/en/skills](https://code.claude.com/docs/en/skills)

---

### 5. Let Claude Fix Bugs Autonomously

**Claude fixes most bugs by itself.** Here's how:

- **Slack integration:** Enable the Slack MCP, paste a Slack bug thread into Claude, just say "fix." Zero context switching.
- **CI/Tests:** Just say "Go fix the failing CI tests." Don't micromanage how.
- **Docker logs:** Point Claude at docker logs to troubleshoot distributed systems - it's surprisingly capable at this.

---

### 6. Level Up Your Prompting

| Situation | What to Say |
|-----------|-------------|
| Want Claude as reviewer | "Grill me on these changes and don't make a PR until I pass your test" |
| Verify behavior | "Prove to me this works" - have Claude diff behavior between main and your feature branch |
| After a mediocre fix | "Knowing everything you know now, scrap this and implement the elegant solution" |
| Before handing off work | Write detailed specs and reduce ambiguity. **The more specific you are, the better the output.** |

---

### 7. Terminal & Environment Setup

- **Terminal:** The team loves [Ghostty](https://ghostty.org/) - synchronized rendering, 24-bit color, proper unicode support
- **Status line:** Use `/statusline` to customize your status bar to show context usage and current git branch
- **Tab organization:** Color-code and name terminal tabs, sometimes using tmux — one tab per task/worktree
- **Voice dictation:** You speak 3x faster than you type. Hit **fn x2** on macOS to activate.

**Docs:** [code.claude.com/docs/en/terminal-setup](https://code.claude.com/docs/en/terminal-setup)

---

### 8. Use Subagents Extensively

- **Append "use subagents"** to any request where you want Claude to throw more compute at the problem
- **Offload individual tasks** to subagents to keep your main agent's context window clean and focused
- **Route permission requests to Opus 4.5 via a hook** — let it scan for attacks and auto-approve the safe ones

#### Example: Exploring a Codebase

```
> use 5 subagents to explore the codebase

● Running 5 Explore agents...
    Explore entry points and startup
    Explore React components structure
    Explore tools implementation
    Explore state management
    Explore testing infrastructure
```

#### Boris's Regular Subagents
- **code-simplifier**: simplifies code after Claude is done working
- **verify-app**: detailed instructions for testing Claude Code end to end
- **build-validator**: validates builds
- **code-architect**: architectural guidance
- **oncall-guide**: oncall procedures

**Docs:** [code.claude.com/docs/en/sub-agents](https://code.claude.com/docs/en/sub-agents)

---

### 9. Use Claude for Data & Analytics

Ask Claude Code to use the `bq` CLI to pull and analyze metrics on the fly.

> "We have a BigQuery skill checked into the codebase, and everyone on the team uses it for analytics queries directly in Claude Code. Personally, I haven't written a line of SQL in 6+ months."

This works for any database that has a CLI, MCP, or API.

---

### 10. Learning with Claude

| Method | How |
|--------|-----|
| **Explanatory output** | Enable in `/config` to have Claude explain the "why" behind its changes |
| **Visual presentations** | Have Claude generate a visual HTML presentation explaining unfamiliar code |
| **ASCII diagrams** | Ask Claude to draw ASCII diagrams of new protocols and codebases |
| **Spaced-repetition** | Build a skill: you explain your understanding, Claude asks follow-ups to fill gaps, stores the result |

---

### 11. Hooks for Automation

Use a PostToolUse hook to format Claude's code. Handles the last 10% to avoid formatting errors in CI.

```json
{
  "PostToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "command",
          "command": "bun run format || true"
        }
      ]
    }
  ]
}
```

**Docs:** [code.claude.com/docs/en/hooks](https://code.claude.com/docs/en/hooks)

---

### 12. Permission Management

**Don't use `--dangerously-skip-permissions`.** Instead, use `/permissions` to pre-allow common bash commands that you know are safe in your environment.

Most of these are checked into `.claude/settings.json` and shared with the team.

```
Permissions: Allow  Ask  Deny  Workspace

 12. Bash(bq query:*)
 13. Bash(bun run build:*)
 14. Bash(bun run lint:file:*)
 15. Bash(bun run test:*)
 ...
```

---

### 13. MCP Integrations

**Claude Code uses all my tools for me:**
- Searches and posts to **Slack** (via MCP server)
- Runs **BigQuery** queries (using bq CLI)
- Grabs error logs from **Sentry**

```json
// .mcp.json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://slack.mcp.anthropic.com/mcp"
    }
  }
}
```

---

### 14. Long-Running Tasks

For very long-running tasks:

1. **Background agent with verification:** Prompt Claude to verify its work with a background agent when it's done
2. **Agent Stop hook:** Do verification more deterministically
3. **ralph-wiggum plugin:** Originally dreamt up by @GeoffreyHuntley
4. **Sandbox mode:** Use `--permission-mode=dontAsk` or `--dangerously-skip-permissions` in a sandbox

**Docs:** [code.claude.com/docs/en/hooks](https://code.claude.com/docs/en/hooks)

---

### 15. Verification - The Most Important Tip

> "Probably the most important thing to get great results out of Claude Code — give Claude a way to verify its work. If Claude has that feedback loop, it will 2-3x the quality of the final result."

**How Boris does it:** Claude tests every single change he lands to claude.ai/code using the Claude Chrome extension. It opens a browser, tests the UI, and iterates until the code works and the UX feels good.

**Verification by domain:**
- Running a bash command
- Running a test suite
- Testing the app in a browser
- Testing in a phone simulator

**Make sure to invest in making this rock-solid.**

**Docs:** [code.claude.com/docs/en/chrome](https://code.claude.com/docs/en/chrome)

---

## Key Philosophy

1. **Experiment** - There's no single right way
2. **Parallelize** - Run multiple Claudes simultaneously
3. **Plan First** - Complex tasks need good plans
4. **Document Mistakes** - Update CLAUDE.md after every correction
5. **Verify Everything** - Give Claude a feedback loop
6. **Automate Repetition** - Turn frequent actions into skills/commands
7. **Use Opus 4.5** - With thinking, for everything

---
