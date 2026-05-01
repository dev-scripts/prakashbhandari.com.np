---
title: "Claude Code Project Structure Explained: The Complete 2026 Guide to CLAUDE.md, Skills, Subagents, Hooks & MCP"
linkTitle: "Claude Code Project Structure (2026 Guide)"
date: 2026-05-01T10:00:00+10:00
lastmod: 2026-05-01T10:00:00+10:00
draft: false
description: "A complete 2026 walkthrough of the Claude Code project structure — CLAUDE.md, .claude/settings.json, skills, subagents, agent memory, worktrees, MCP, hooks, and agent teams. Learn how each file works and where to start."
summary: "From CLAUDE.md to agent teams: a file-by-file breakdown of how a modern Claude Code project is organized in 2026, and a step-by-step plan for adopting it in your own repo."
keywords: ["claude code", "claude code project structure", "CLAUDE.md", "claude code skills", "claude code subagents", "claude code hooks", "claude code MCP", "claude code settings.json", "claude code agent teams", "claude code tutorial 2026", "anthropic claude code", "agentic coding", "AI coding assistant"]
slug: "claude-code-project-structure-2026"
tags: ["Claude Code", "CLAUDE.md", "Skills", "Subagents", "Hooks", "MCP", "AI Coding", "Anthropic", "Agentic Development", "Developer Tools"]
categories: ["Claude Code", "AI", "Agentic AI"]
series: ["Claude Code Deep Dives"]
author: "Prakash Bhandari"
toc: true
showToc: true
TocOpen: true
cover:
    image: "images/posts/claude-code-project-structure-2026/claude-code-project-structure-2026.png" # image path/url
    alt: "The Complete Anatomy of a Claude Code Project - 2026"
    caption: "The Complete Anatomy of a Claude Code Project - 2026"
    relative: false
---

AI development has quietly crossed an important threshold. What began as prompt engineering and chat-based interactions is now evolving into structured systems that resemble real software environments. Instead of asking a single model to do everything, developers are beginning to design AI-native architectures—systems with rules, roles, memory, and coordination.

One of the most compelling patterns emerging in this space is the Claude Code Project structure

If you opened a Claude Code project in late 2024, you saw a single `CLAUDE.md` file sitting at the root and not much else. Open one in 2026 and the directory tree looks more like the scaffolding of a small operating system: settings, skills, subagents, agent memory, worktrees, MCP wiring, ignore files, and bundled plugins. That shift is not accidental. Claude Code is no longer a chatbot you happen to point at code — it has quietly become an execution environment where multiple agents collaborate, persist knowledge, and react to lifecycle events deterministically.

This post walks through the complete anatomy of a modern Claude Code project, file by file, and explains what each piece is actually for. If you have been getting by with a half-filled `CLAUDE.md` and a vague sense that "skills are a thing now," this is the map.

## The Tree at a Glance

Before going piece by piece, here is the shape we are working with:

```text
my-project/
├── CLAUDE.md               # Project Brain (auto-loaded every session)
├── CLAUDE.local.md         # Personal Overrides (gitignored)
├── .claude/
│   ├── settings.json       # Permissions + Hooks + Env Vars
│   ├── skills/
│   │   └── review/
│   │       └── SKILL.md    # Reusable expertise
│   ├── agents/
│   │   └── code-reviewer.md # Subagent definitions
│   ├── agent-memory/       # Persistent knowledge across sessions
│   └── worktrees/          # Git-level isolation for parallel agents
├── .mcp.json               # External tool connections
├── .claudeignore           # Files Claude should never read
└── plugins/                # Bundled distribution
```

Every entry in that tree solves a specific problem. Let's go through them.

## CLAUDE.md — The Project Brain

`CLAUDE.md` is the file that gets loaded into context at the start of every session, no questions asked. Think of it as the standing brief you would hand a new contractor on day one: what we are building, what stack it runs on, what conventions we follow, and what the workflow rules are.

A good `CLAUDE.md` answers four questions:

1. **Architecture** — what are the major components and how do they talk to each other?
2. **Tech stack** — languages, frameworks, build tools, deployment targets.
3. **Conventions** — naming, formatting, testing patterns, commit style.
4. **Workflow rules** — branch strategy, review requirements, things that are off-limits.

`CLAUDE.md` is hierarchical. The root file applies to the whole repository, but you can drop additional `CLAUDE.md` files into subdirectories — `frontend/CLAUDE.md`, `services/auth/CLAUDE.md` — and they get loaded on demand when Claude is working in that area. This is the right place to put context that only matters locally: the auth service's quirks belong in the auth folder, not in the global brief.

If you are starting from scratch, run `/init` inside your project. Claude will scan the codebase and draft a starter `CLAUDE.md` for you. Treat that draft as a first pass, not a finished artifact. The file you check in should be tight and opinionated — every line should pay rent. Bloated `CLAUDE.md` files are how you end up with a model that knows everything about your project and uses none of it.

## CLAUDE.local.md — Personal Overrides

`CLAUDE.local.md` is the same idea as `CLAUDE.md`, but it is gitignored and meant for you alone. This is where you put:

- Local environment paths (`my Postgres lives at /opt/...`)
- Debugging shortcuts you personally use
- Private preferences (verbosity, comment density, the way you like commit messages written)
- Notes about your machine that the rest of the team does not need

Keeping this file separate is what lets the shared `CLAUDE.md` stay clean. Your teammate's `CLAUDE.local.md` might be radically different from yours, and that is fine — it never enters the repo.

## .claude/settings.json — Permissions, Hooks, and Env Vars

This is the harness configuration. Anything that should be enforced deterministically — not requested politely — lives here.

Three jobs sit in `settings.json`:

- **Tool access**: which tools the agent is allowed to use, and any commands explicitly allowlisted or denied.
- **Hook configurations**: which scripts fire on which lifecycle events.
- **Environment variables**: values Claude Code should pass through to its tools.

The crucial point is that `settings.json` is checked into git and shared across the team, so the rules it encodes are the same for everyone working in the repo. If you want a behavior to be guaranteed, put it here, not in `CLAUDE.md`. A line in `CLAUDE.md` that says "NEVER add Co-Authored-By to commits" is a suggestion the model can forget. The same rule expressed as a permission or attribution setting in `settings.json` simply cannot be violated.

That distinction — `CLAUDE.md` for guidance, `settings.json` for enforcement — is one of the most useful mental models you can carry into a Claude Code project.

## .claude/skills/ — Reusable Expertise

Skills are the unified extensibility model in 2026. Each skill is a directory containing a `SKILL.md` file, and optionally helper scripts, templates, or reference docs. The directory name becomes the skill name.

What makes skills powerful is twofold. First, they are **auto-invoked** when the task context matches the skill's description — Claude reads the YAML frontmatter at startup, and when you ask for something that fits, the skill fires automatically. Second, every skill **doubles as a slash command**: a `/review` skill can also be invoked directly with `/review`. The frontmatter controls whether auto-invocation is allowed, whether the skill appears in the `/` menu, and whether it should run inside a subagent.

A typical `SKILL.md` looks like this:

```markdown
---
name: review
description: Reviews a pull request or diff for quality, security, and adherence to project conventions. Use after the user finishes implementing a feature or before they push.
---

# Code Review Skill

## Instructions
1. Run the test suite first.
2. Check for the conventions documented in CLAUDE.md.
3. Look for ...
```

The `description` field is doing real work — it is what Claude reads to decide whether to invoke the skill. Vague descriptions mean the skill never fires when it should, or fires when it shouldn't. Be specific.

Skills can also fork into a subagent, which is how you keep heavy workflows from polluting your main conversation context. A long code-review skill, for example, can spin up a subagent, do its work in an isolated context window, and report back with only the conclusions.

## .claude/agents/ — Subagents

Subagents are separate Claude instances with their own context windows. They exist because context is finite, and because some tasks are better done in parallel.

Each subagent is defined by a markdown file in `.claude/agents/` with YAML frontmatter that controls its behavior:

```markdown
---
name: code-reviewer
description: Reviews code for quality, security issues, and best practices.
tools: Read, Grep, Bash
model: claude-sonnet-4-7
isolation: worktree
memory: project
---

You are a senior code reviewer. Your job is to ...
```

The frontmatter fields you will use most often:

- `name` and `description` — identity and auto-invocation matching.
- `tools` — which tools this subagent is allowed to use (tighter than the main agent).
- `model` — you can route this subagent to a faster, cheaper model if appropriate.
- `isolation: worktree` — the subagent gets its own git worktree, so it can edit files in parallel without colliding with the main agent.
- `memory` — gives the subagent a persistent directory that survives across sessions.

Three built-in subagents ship out of the box: **Explore** (a fast scout, typically routed to a smaller model like Haiku), **Plan** (used when you enter plan mode), and **General** (a general-purpose worker). You can customize or override these, or define new ones from scratch.

The mental shift here is important. In the old single-agent world, telling Claude "read these fifteen files and summarize" burned fifteen files of context window. With subagents, you say "use the Explore subagent to map the relevant code" and you get back a distilled summary — your main context stays small and focused.

## .claude/agent-memory/ — Persistent Knowledge

`agent-memory/` is where subagents accumulate knowledge over time. A code-reviewer subagent that has been running on your project for three months will have built up a `MEMORY.md` describing the patterns it has seen, the bugs that recur, the conventions that exist but were never written down, and the architectural decisions that explain why certain things look weird.

This is fundamentally different from `CLAUDE.md`. `CLAUDE.md` is what you tell Claude. `agent-memory/` is what Claude has learned. Over a long enough timeline, the latter often becomes more useful than the former.

When a subagent's `MEMORY.md` grows past a threshold (around 200 lines or 25KB), the subagent is instructed to curate it — pruning, consolidating, and reorganizing. The memory does not grow without bound.

## .claude/worktrees/ — Git-Level Isolation

`worktrees/` is the directory where parallel agents do their work. Each agent that is set to `isolation: worktree` gets its own git branch and its own filesystem checkout under `.claude/worktrees/<task-name>/`. They can write files, run tests, and commit without ever stepping on each other.

This is what makes true parallel development possible. You can fire off three subagents to investigate three different bugs simultaneously, and none of them will see another's half-finished edits. When they are done, you review their branches and merge selectively.

Worktrees are also what make agent teams workable, which we will get to at the end.

## .mcp.json — External Tool Connections

The Model Context Protocol (MCP) is how Claude Code talks to systems outside the repository: JIRA, GitHub, Slack, databases, internal APIs, anything that exposes an MCP server. `.mcp.json` declares which servers this project connects to and how to authenticate.

Because `.mcp.json` is committed to git, the whole team gets the same external integrations the moment they clone the repo. New developer joining? They check out the project and immediately have the same JIRA, Slack, and database access as everyone else, scoped through the same channels.

Channels are worth a brief mention. They allow MCP servers to push messages into a live Claude Code session — so when a CI run finishes, or a JIRA ticket gets assigned to you, your active session can react to it without you typing anything. This is what turns Claude Code from a request-response tool into something more like a daemon that participates in your workflow.

## .claudeignore — Context Boundaries

`.claudeignore` works the way `.gitignore` does, but for Claude's reading scope. Files listed here are never read into context, no matter what.

This sounds minor. In a small repo, it is. In a large monorepo, it is the difference between a usable project and an unusable one. Without `.claudeignore`, Claude will happily wander into your `node_modules`, generated migrations, vendored dependencies, and 200MB of fixture data, and burn through context before it gets to anything useful. With it, you draw a tight boundary around the parts of the codebase that actually matter.

## plugins/ — Bundled Distribution

Plugins are packaged bundles of skills, subagents, and slash commands. If your team has built up a useful set of internal workflows — a design-review skill, a release-notes generator, a security-audit subagent — you can bundle them as a plugin and share them across projects with a single install. The install scripts auto-detect the right locations and copy files into place.

This is how organizations standardize. Instead of every project reinventing the same skills, a platform team publishes a plugin and every repo gets the same baseline.

## Hooks — 25 Lifecycle Events

Underneath all of this is the hook system. Hooks are scripts that fire on lifecycle events — deterministically, every time. They cannot hallucinate, because they are not running through the model.

There are roughly 25 events, split into two categories:

- **Blocking hooks**: `PreToolUse`, `UserPromptSubmit`, `PermissionRequest`, `Stop`, `SubagentStop`, `PreCompact`. These run before something happens and can prevent it.
- **Informational hooks**: `SessionStart`, `SessionEnd`, `PostToolUse`, `SubagentStart`, `Notification`, `FileChanged`, and others. These run after the fact and react.

A canonical example is running a formatter after every edit:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          { "type": "command", "command": "./scripts/format.sh", "timeout": 30 }
        ]
      }
    ]
  }
}
```

Every time Claude writes or edits a file, the formatter runs. There is no "I forgot." It happens.

## Agent Teams — Multi-Agent Coordination

The newest layer, still considered experimental in 2026, is **agent teams**: multiple Claude sessions working not as a delegator and a delegate, but as peers. They share a task list. They send each other direct messages. Each runs in its own worktree with a 1M-token context. Structured execution memory keeps them coordinated.

This is the punchline of the whole architecture. Claude Code is not a chatbot anymore. It is an OS for active development — a system where projects carry their own brain (`CLAUDE.md`), their own permissions (`settings.json`), their own reusable expertise (skills), their own specialists (subagents), their own long-term memory (`agent-memory/`), their own external integrations (`.mcp.json`), and their own coordination primitives (worktrees, hooks, agent teams).

## Where to Start

If your existing project has none of this, do not try to bolt it all on at once. The order I would recommend:

1. **Get `CLAUDE.md` right.** This is the highest-leverage file. Spend an afternoon on it.
2. **Add a `.claudeignore`.** Especially if you have a monorepo or heavy generated assets.
3. **Move enforced rules into `settings.json`.** Anything that lives in `CLAUDE.md` as "NEVER do X" is a candidate.
4. **Build one skill** for a workflow you do every day.
5. **Define one subagent** for work that pollutes your main context — research, code review, doc lookup.
6. **Wire up MCP** for the one external tool you reach for most.
7. **Then** look at hooks, plugins, and agent teams.

Each step compounds. By the time you have a `CLAUDE.md`, a `settings.json`, three skills, two subagents, an MCP connection, and a few hooks, you are no longer prompting an assistant. You are running an environment.

That is the shift the 2026 directory tree quietly encodes. The structure is the strategy.