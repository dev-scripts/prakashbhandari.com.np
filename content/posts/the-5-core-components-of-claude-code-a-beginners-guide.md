---
title: "The 5 Core Components of Claude Code: A Beginner's Guide"
description: "A friendly walkthrough of the 5 files that power every Claude Code project — CLAUDE.md, settings.json, skills, agents, and MCP. Learn what each one does and why it matters."
date: 2026-05-04T10:00:00+05:30
lastmod: 2026-05-04T10:00:00+05:30
draft: false
slug: "claude-code-5-core-components"
categories: ["AI", "Claude","Developer Tools"]
tags: ["claude-code", "AI", "anthropic", "developer-experience", "tutorial", "mcp", "Agentic AI"]
author: "Prakash Bhandari"
toc: true
showReadingTime: true
cover:
  image: "images/posts/claude-code-5-core-components/claude-code-5-core-components.png" 
  alt: "Diagram showing the 5 core components of a Claude Code project"
  caption: "Diagram showing the 5 core components of a Claude Code project"
  relative: false
  author: "Prakash Bhandari"
  description: "As a beginner, understanding a Kubernetes (k8s) cluster might be a little bit complicated."
---

If you've heard the buzz about Claude Code but feel a little lost about how it actually works, you're in the right place. Claude Code isn't just another AI chatbot stitched into your terminal — it's a fully configurable development environment where you, the developer, decide how the AI behaves inside your project.

But to use it well, you need to understand its anatomy. Don't worry, it's simpler than it looks.

In this post, we'll walk through the **5 core components** that make up every Claude Code project. By the end, you'll know exactly which file does what, why it exists, and how they all work together. Let's dive in.


## A Quick Mental Model First

Before we look at the components individually, here's the big picture: Claude Code reads files in your project to figure out how to behave. Some files tell it about your project. Some give it special skills. Some let it talk to outside services like GitHub or your database.

Think of it like onboarding a new developer. You'd give them:

- A README explaining the project
- Access permissions to certain tools
- Documentation on how the team does things
- Maybe a buddy to help with specific tasks
- Logins to shared services

Claude Code's components map almost one-to-one with that list. Cool, right? Let's look at each one.

## 1. `CLAUDE.md` — The Project Brain

This is **the most important file** in your Claude Code setup. Every time you start a session, Claude automatically reads this file to learn about your project.

### What goes inside

A typical `CLAUDE.md` covers:

- **Architecture**: What pattern does your project follow? (MVC, microservices, monolith?)
- **Tech stack**: What languages, frameworks, and databases are in use?
- **Conventions**: How do you name files? Do you use camelCase or snake_case? Tabs or spaces?
- **Workflow rules**: How do you run tests? Deploy? Run migrations?

### Example

```markdown
# My Node.js API — Project Brain

## Architecture
- Pattern: MVC (Model → Service → Controller → Route)
- Runtime: Node.js 22 with ES Modules
- Database: MySQL via mysql2/promise

## Conventions
- All async/await — no callbacks
- Errors use status codes attached to Error objects
- API versioned under /api/v1/

## Workflow
- Run `npm run dev` to start
- Run `npm test` for Jest tests
- Migrations auto-run on startup
```

### Why it matters

Without `CLAUDE.md`, Claude has to guess at your conventions. With it, every response stays consistent with how your project actually works. It's the difference between an intern who reads the docs first and one who just starts typing.

This file should be committed to git. The whole team benefits from a shared brain.


## 2. `.claude/settings.json` — Permissions, Hooks, and Environment 

If `CLAUDE.md` tells Claude *what* the project is, `settings.json` tells it *what it's allowed to do* and *how it should behave*.

### What goes inside

```json
{
  "permissions": {
    "allow": ["read", "write", "execute"],
    "deny": ["delete_db", "drop_table"]
  },
  "hooks": {
    "preToolUse": ".claude/hooks/pre-tool.js",
    "postToolUse": ".claude/hooks/post-tool.js"
  },
  "env": {
    "NODE_ENV": "development",
    "LOG_LEVEL": "info"
  },
  "allowedCommands": ["node", "npm", "npx"]
}
```

### Three things it controls

**Permissions** tell Claude which tools it can use. Want Claude to never delete files? Add `delete` to the deny list. Want it to be able to read but not write? Allow `read` only.

**Hooks** are scripts that run automatically at certain moments. Think of them as "intercept this action and run my code first."

**Environment variables** set up consistent context — things like which environment Claude is operating in (`development` vs `production`).

### Why it matters

This is your safety net. By default, Claude will try to be helpful — but "helpful" can mean accidentally running a destructive command. Settings let you put guardrails in place. The file is committed to git so the whole team gets the same protections.

## 3. `.claude/skills/` — Reusable Expertise

Skills are bite-sized instruction packets that teach Claude how to do specific things in your project.

Imagine you've got a new dev on the team and you keep explaining "when we add a new feature, we follow these 7 steps." Wouldn't it be nice to just write that down once? That's exactly what a skill is.

### How they're structured

Skills live in `.claude/skills/<skill-name>/SKILL.md`. Each one starts with a YAML frontmatter block describing when to use it:

```markdown
---
name: add-feature
description: "Auto-invoked when user asks to add a new feature or module."
slash_command: /add-feature
---

# Add Feature Skill

## Steps
1. Add the table to migrations
2. Create the model file
3. Create the validator
4. Create the service
5. Create the controller
6. Create the routes
7. Register the route in the main router
```

### Two ways to invoke them

**Auto-invoke**: When you say something like "add a products feature," Claude notices the description matches and applies the skill automatically. You don't have to remember to call it.

**Slash command**: You can also explicitly trigger it with `/add-feature products`. Useful when you want to be precise.

### Why it matters

Skills capture institutional knowledge. The first time someone adds a feature, it's a learning curve. With a skill, the *thousandth* time looks exactly like the first — same files, same naming, same patterns. Your codebase stays consistent without anyone having to police it.

This is huge for big teams or growing codebases.

## 4. `.claude/agents/` — Subagents 

Subagents are like cloning Claude into a specialized version of itself for a specific task.

The default Claude session has one big context window where it tracks the whole conversation. But sometimes you want a smaller, focused worker that doesn't pollute that context with unrelated stuff. That's a subagent.

### How they're defined

```markdown
---
name: code-reviewer
description: "Reviews source files for code quality issues."
tools: [read_file, list_files]
model: haiku
isolation: worktree
---

# Code Reviewer Subagent

## Role
Read-only subagent that scans src/ and reports quality issues.
Does not modify files.

## Checks
- Missing error handling
- Hardcoded secrets
- Business logic in controllers
```

### Key features

- **Isolated context**: Each subagent has its own conversation window. The main Claude doesn't see the noise.
- **Configurable model**: Use a smaller model like Haiku for cheap, fast tasks; use Sonnet for harder ones.
- **Restricted tools**: Give a subagent only the tools it needs. A reviewer doesn't need write access.
- **Worktree isolation**: For agents that *do* write, they can work in their own git branch + filesystem so they don't conflict with you.

### Why it matters

Subagents let you parallelize work. While you're still chatting with the main Claude about feature design, a code-reviewer subagent can be checking last week's commits in the background. A db-migrator subagent can be drafting your next schema change. It's like having a small team of specialists.

There are also three built-in subagents: **Explore** (Haiku-powered, fast file scanning), **Plan**, and **General**.

## 5. `.mcp.json` — External Tool Connections

The last core piece. MCP stands for **Model Context Protocol**, and this file is how Claude connects to the outside world.

Out of the box, Claude can read and write files in your project. But what if you want it to:

- Query your MySQL database directly?
- Open a pull request on GitHub?
- Post a message to Slack?
- Pull tickets from JIRA?

That's what MCP servers are for. Each one is a little bridge between Claude and an external service.

### What it looks like

```json
{
  "mcpServers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    },
    "mysql": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@benborla29/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_USER": "root"
      }
    }
  }
}
```

### Why it matters

This is what turns Claude from a code assistant into something more like a teammate. Instead of saying "here's the SQL, run it yourself," Claude can actually run the query, see the results, and act on them.

This file is **committed to git**, so the whole team uses the same connections. No one needs to set up GitHub access individually — it's part of the project.

## How They Work Together

Let's trace what happens when you type a prompt like "add a products feature":

1. **`CLAUDE.md`** is already loaded — Claude knows you use MVC, MySQL, Zod, JWT.
2. The hook in **`settings.json`** checks if the request is allowed. 
3. Claude scans **`.claude/skills/`** and finds `add-feature` matches — it auto-invokes the skill.
4. The skill says "follow these 7 steps." Claude does, generating files.
5. If the skill needs a code review, it forks a **subagent** from `.claude/agents/`.
6. The subagent uses **`.mcp.json`** to query GitHub and check if a similar feature exists in another branch.
7. Done. You get a clean, consistent feature with all the right files.

Notice how each component plays its role: brain → guardrails → expertise → specialists → external reach.

## How to Get Started

If you're setting up your first Claude Code project, here's the minimum viable setup:

1. **Create `CLAUDE.md`** at the root with your tech stack and conventions. (You can run `/init` and Claude will help draft it.)
2. **Create `.claude/settings.json`** with sensible permissions for your team.
3. **Skip skills and agents at first.** Add them as you find yourself repeating instructions.
4. **Add `.mcp.json`** when you want Claude to talk to a database, GitHub, etc.

You don't need all 5 components from day one. Start with `CLAUDE.md` and grow from there.

## Wrapping Up

The 5 core components of Claude Code aren't magic — they're just files in your repo that shape Claude's behavior. Once you understand each one, you can build a setup that fits your team perfectly:

| Component | Role |
|---|---|
| `CLAUDE.md` | Project brain — what is this project? |
| `.claude/settings.json` | Guardrails — what can Claude do? |
| `.claude/skills/` | Expertise — how does our team work? |
| `.claude/agents/` | Specialists — who handles focused tasks? |
| `.mcp.json` | Connections — what's outside the project? |

The beauty of this design is that **Claude's behavior lives in version control**. When a new dev joins, they `git clone` the repo and get the exact same Claude setup as everyone else. No config drift, no "works on my machine," no slack threads explaining how to set things up.

That's a pretty powerful idea — and it's the foundation of treating AI not as a chat window, but as a teammate with a real seat in your project.

Happy building! 

