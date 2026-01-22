# Get Shit Done (LITE)

**A meta-prompting, context engineering and spec-driven development system for Claude Code [originally by TÂCHES](https://github.com/glittercowboy/get-shit-done) **

THIS is how you vibecode and actually get shit done.

---

# What Makes Get Shit Done Lite (GSDL) Different

1. As the original GSD matured, it got more and more full featured, and outgrew the smaller sized projects we work on. So we forked an earlier version (v1.3.9) that fit the project sizes we typically work on. 
2. Then, we optimized the code to reduce token usage as much as possible without negatively effecting performance (more on this below)
3. Finally, we added an auto frontend review step using Playwright MCP (you need to have it installed beforehand) so that Claude Code can do a full test before reporting that the phase/milestone/whatever is complete

## Token Efficiency Updates

We optimized default context loading to reduce token usage without removing guidance.

What changed:

- Split heavy references into "core" versions used by default.
- Keep full reference docs available for examples and deeper guidance.
- Load provider-specific automation docs only when a phase needs them.
- Require Playwright verification for user-visible UI changes (baked into planning and execution).

Estimated impact (default command context only, excluding project files):

- `/gsd:plan-phase`: ~15.9k tokens -> ~11.1k tokens (~30% reduction).
- `/gsd:execute-plan`: ~10.8k tokens -> ~8.5k tokens (~22% reduction).

Quality is preserved because the full references are still available and loaded when needed.

---

## How It Works (QUICKSTART)

### 1. Start with an idea

```
/gsd:new-project
```

### 2. Create roadmap

```
/gsd:create-roadmap
```

### 3. Plan and execute phases

```
/gsd:plan-phase 1      # System creates atomic task plans
/gsd:execute-plan      # Subagent implements autonomously
```

### 4. Ship and iterate

```
/gsd:complete-milestone   # Archive v1, prep for v2
/gsd:add-phase            # Append new work
/gsd:insert-phase 2       # Slip urgent work between phases
```



---

## Existing Projects (Brownfield)

Already have code? Start here instead.

### 1. Map the codebase

```
/gsd:map-codebase
```

Spawns parallel agents to analyze your code. Creates `.planning/codebase/` with 7 documents:

- **STACK.md** — Languages, frameworks, dependencies
- **ARCHITECTURE.md** — Patterns, layers, data flow
- **STRUCTURE.md** — Directory layout, where things live
- **CONVENTIONS.md** — Code style, naming patterns
- **TESTING.md** — Test framework, patterns
- **INTEGRATIONS.md** — External services, APIs
- **CONCERNS.md** — Tech debt, known issues, fragile areas

### 2. Initialize project

```
/gsd:new-project
```

Same as greenfield, but the system knows your codebase. Questions focus on what you're adding/changing, not starting from scratch.

### 3. Continue as normal

From here, it's the same: `/gsd:create-roadmap` → `/gsd:plan-phase` → `/gsd:execute-plan`

The codebase docs load automatically during planning. Claude knows your patterns, conventions, and where to put things.

---

## Why It Works

### Context Engineering

Claude Code is incredibly powerful _if_ you give it the context it needs. Most people don't.

GSD handles it for you:

| File         | What it does                                           |
| ------------ | ------------------------------------------------------ |
| `PROJECT.md` | Project vision, always loaded                          |
| `ROADMAP.md` | Where you're going, what's done                        |
| `STATE.md`   | Decisions, blockers, position — memory across sessions |
| `PLAN.md`    | Atomic task with XML structure, verification steps     |
| `SUMMARY.md` | What happened, what changed, committed to history      |
| `ISSUES.md`  | Deferred enhancements tracked across sessions          |

Size limits based on where Claude's quality degrades. Stay under, get consistent excellence.

### XML Prompt Formatting

Every plan is structured XML optimized for Claude:

```xml
<task type="auto">
  <name>Create login endpoint</name>
  <files>src/app/api/auth/login/route.ts</files>
  <action>
    Use jose for JWT (not jsonwebtoken - CommonJS issues).
    Validate credentials against users table.
    Return httpOnly cookie on success.
  </action>
  <verify>curl -X POST localhost:3000/api/auth/login returns 200 + Set-Cookie</verify>
  <done>Valid credentials return cookie, invalid return 401</done>
</task>
```

Precise instructions. No guessing. Verification built in.

### Subagent Execution

As Claude fills its context window, quality degrades. You've seen it: "Due to context limits, I'll be more concise now." That "concision" is code for cutting corners.

GSD prevents this. Each plan is maximum 3 tasks. Each plan runs in a fresh subagent — 200k tokens purely for implementation, zero accumulated garbage.

- Task 1: fresh context, full quality
- Task 2: fresh context, full quality
- Task 3: fresh context, full quality

No degradation. Walk away, come back to completed work.

### Clean Git History

Every task: atomic commit, clear message, summary documenting outcomes. Maintainable history you can trace.

### Modular by Design

- Add phases to current milestone
- Insert urgent work between phases
- Complete milestones and start fresh
- Adjust plans without rebuilding everything

You're never locked in. The system adapts.

---

## Token Efficiency Updates

We optimized default context loading to reduce token usage without removing guidance.

What changed:

- Split heavy references into "core" versions used by default.
- Keep full reference docs available for examples and deeper guidance.
- Load provider-specific automation docs only when a phase needs them.
- Require Playwright verification for user-visible UI changes (baked into planning and execution).

Estimated impact (default command context only, excluding project files):

- `/gsd:plan-phase`: ~15.9k tokens -> ~11.1k tokens (~30% reduction).
- `/gsd:execute-plan`: ~10.8k tokens -> ~8.5k tokens (~22% reduction).

Quality is preserved because the full references are still available and loaded when needed.

---

## Commands

| Command                           | What it does                                                  |
| --------------------------------- | ------------------------------------------------------------- |
| `/gsd:new-project`                | Extract your idea through questions, create PROJECT.md        |
| `/gsd:create-roadmap`             | Create roadmap and state tracking                             |
| `/gsd:map-codebase`               | Map existing codebase for brownfield projects                 |
| `/gsd:plan-phase [N]`             | Generate task plans for phase                                 |
| `/gsd:execute-plan`               | Run plan via subagent                                         |
| `/gsd:progress`                   | Where am I? What's next?                                      |
| `/gsd:complete-milestone`         | Ship it, prep next version                                    |
| `/gsd:discuss-milestone`          | Gather context for next milestone                             |
| `/gsd:new-milestone [name]`       | Create new milestone with phases                              |
| `/gsd:add-phase`                  | Append phase to roadmap                                       |
| `/gsd:insert-phase [N]`           | Insert urgent work                                            |
| `/gsd:discuss-phase [N]`          | Gather context before planning                                |
| `/gsd:research-phase [N]`         | Deep ecosystem research for niche domains                     |
| `/gsd:list-phase-assumptions [N]` | See what Claude thinks before you correct it                  |
| `/gsd:pause-work`                 | Create handoff file when stopping mid-phase                   |
| `/gsd:resume-work`                | Restore from last session                                     |
| `/gsd:consider-issues`            | Review deferred issues, close resolved, identify urgent       |
| `/gsd:help`                       | Show all commands and usage guide                             |

---

## Who This Is For

People who want to vibecode and have it actually work.

Anyone who wants to clearly describe what they want, trust the system to build it, and go live their life.

Not for people who enjoy inconsistent and sloppy results.

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

**Claude Code is powerful. GSD gives it the context and the systematic consistency to prove it.**


