# Implementation - Agentic Approach

- [Overview & Reference](#overview--reference)
- [How It Works: The 3 Moving Parts](#how-it-works-the-3-moving-parts)
  - [1. Trigger — command delegates to agent](#1-trigger--command-delegates-to-agent)
  - [2. Agent — owns the recipe and loads knowledge](#2-agent--owns-the-recipe-and-loads-knowledge)
  - [3. Knowledge — captured in skills](#3-knowledge--captured-in-skills)
- [Directory Structure](#directory-structure)
- [Key Principle](#key-principle)
  - [Explicit Trigger & Implicit Knowledge](#explicit-trigger--implicit-knowledge)
  - [Stock Extensible Mechanisms](#stock-extensible-mechanisms)
  - [Agent Skill Frontmatter](#agent-skill-frontmatter)

## Overview & Reference

This implementation guide is based on the boilerplate published by shinpr:
[github.com/shinpr/ai-coding-project-boilerplate](https://github.com/shinpr/ai-coding-project-boilerplate)

That repo provides a working example of recipe and knowledge skill separation in practice, using stock Claude Code extensible mechanisms. The structure and conventions described below are derived from that reference implementation.



## How It Works: The 3 Moving Parts

### 1. Trigger — command delegates to agent

A process is started by an explicit slash command. This is not semantic matching — the user intentionally invokes the command when they are ready to begin. The command delegates immediately to a dedicated agent, which takes ownership of the workflow from that point.

```
/implement <feature>
```

The command is the entry point. It does not do the work itself — it hands off to the agent.

**In the boilerplate:**
Commands live in `.claude/commands/`. Each command file defines what agent to delegate to and any initial instructions.



### 2. Agent — owns the recipe and loads knowledge

The agent plays two distinct roles in this system.

**Role 1: The recipe**
The agent file *is* the recipe. It defines the workflow — the stages, the sequence, the conditions, and the completion criteria. This is where the orchestration lives. In shinpr's approach there is no separate recipe file — the agent owns and expresses the end-to-end process. Naming your agents clearly reflects this: `implement-agent.md`, `review-agent.md`, `diagnose-agent.md`.

**Role 2: Knowledge loader**
As the agent executes its workflow, it automatically loads knowledge skills whose frontmatter description matches the current context. This is not explicit — the agent does not call knowledge skills by name. Claude reads the frontmatter descriptions of available skills and loads those that are relevant.

These two roles together are what make the pattern work: the agent knows *what* to do and *in what order*, and the knowledge skills inform *how* to do it well.

**In the boilerplate:**
Agents live in `.claude/agents/`. Each agent is a markdown file that defines the full workflow for its scope. Knowledge skills load in the background based on context — the agent does not need to reference them directly.



### 3. Knowledge — captured in skills

Knowledge skills carry the conventions, standards, and best practices that inform execution. They do not run steps — they provide context. Their frontmatter description is what determines when they load.

A knowledge skill might carry:
- Coding standards for a specific language or framework
- Naming conventions for the project
- Testing principles and quality criteria
- Domain-specific rules or constraints

Because knowledge is captured in skills and not embedded in the agent, it is swappable. A different technology or team context means swapping the knowledge skills — the agent and command stay the same.

**In the boilerplate:**
Knowledge skills live in `.claude/skills/`. Each skill is a markdown file with YAML frontmatter. The `description` field is critical — it determines when Claude loads the skill.



## Directory Structure

```
.claude/
  commands/         ← slash commands — trigger & delegate
  agents/           ← agents — own and run the workflow
  skills/           ← knowledge skills — context & conventions
CLAUDE.md           ← project-level orchestration & rules
```



## Key Principle

### Explicit Trigger & Implicit Knowledge

The trigger is explicit. The knowledge loading is implicit. The agent connects the two.

> User invokes command → command delegates to agent → agent executes workflow → knowledge skills load automatically based on context



### Stock Extensible Mechanisms

This implementation uses Claude Code's out-of-the-box extensible mechanisms — no custom tooling or middleware required. Claude Code provides a set of built-in mechanisms that can be composed to build any system:

- **Commands** — slash commands that trigger workflows
- **Agents** — sub-agents that own and execute a defined scope of work
- **Skills** — markdown files that provide context, knowledge, or instructions
- **Hooks** — event-driven intercepts that enforce governance at key points
- **Rules** — always-on constraints applied across the session
- **CLAUDE.md** — project-level configuration read at the start of every session

RKSS is built entirely on these mechanisms. There is nothing to install beyond what Claude Code already provides.



### Agent Skill Frontmatter

The frontmatter `description` field in each skill file is the mechanism that enables implicit knowledge loading. When Claude is operating in a session, it reads the description of every available skill and loads those whose description matches the current context.

This means the quality of your description directly determines whether a knowledge skill loads when it should. A well-written description is specific, contextual, and uses language that naturally appears in the workflows it should inform.

```yaml
---
description: Use when implementing or reviewing TypeScript code. Covers naming conventions, type safety rules, and project coding standards.
---
```

The agent never explicitly calls a knowledge skill. The frontmatter does the work.



