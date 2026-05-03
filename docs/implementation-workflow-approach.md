# Implementation - Workflow Approach

- [Overview](#overview)
- [How It Works: The 4 Moving Parts](#how-it-works-the-4-moving-parts)
  - [1. Trigger — explicit or semantic](#1-trigger--explicit-or-semantic)
  - [2. Orchestrator skill — facilitates the workflow](#2-orchestrator-skill--facilitates-the-workflow)
  - [3. Workflow skill and stage skills](#3-workflow-skill-and-stage-skills)
  - [4. Knowledge skills — semantic matching](#4-knowledge-skills--semantic-matching)
- [Skill Hierarchy](#skill-hierarchy)
- [Directory Structure](#directory-structure)
- [Key Principles](#key-principles)

## Overview

This is an alternative implementation approach to the agentic model. Rather than delegating to sub-agents in isolated context windows, the entire workflow runs within a single main session. There are no sub-agents. The orchestrator, workflow, and all stages execute in the same context.

Knowledge skills are the same as in the agentic approach. The key differences are in how the workflow is triggered, how it is structured, and how knowledge skills load.



## How It Works: The 4 Moving Parts

### 1. Trigger — explicit or semantic

Unlike the agentic approach where a command explicitly delegates to an agent, this implementation supports both explicit and semantic triggers.

A **semantic trigger** means Claude recognises the intent from natural language and starts the workflow without a slash command. The orchestrator skill's description is written to match the language a user would naturally use when they want to begin that process.

An **explicit trigger** works the same way as the agentic approach — a slash command starts the workflow directly.

Either way, the trigger lands at the orchestrator skill, which takes ownership from that point.



### 2. Orchestrator skill — facilitates the workflow

The orchestrator is a skill that launches and facilitates the workflow. It does not define the detail of what happens at each stage — that lives in the workflow skill. Its job is to:

- Receive the trigger and confirm the workflow is starting
- Load and hand off to the workflow skill
- Announce each stage clearly as the workflow progresses
- Monitor stage exit criteria before moving to the next stage

The orchestrator's explicit announcements are important — they are what allows knowledge skills to load at the right moment via semantic matching.

**Trigger:** explicit slash command or semantic match on user intent.



### 3. Workflow skill and stage skills

The workflow is defined across a set of skills with a clear hierarchy.

**Workflow skill**
Outlines the intent of the overall workflow and the stages it contains. This is the recipe — it defines what happens, in what order, and what the workflow is trying to achieve. The orchestrator loads this skill and follows it.

**Trigger:** explicit — called by the orchestrator at workflow start.

**Stage skills**
Each stage in the workflow has its own skill. A stage skill defines what happens within that stage, the actions to take, and — critically — the exit criteria that must be met before the orchestrator can advance to the next stage. Clear exit criteria keep the workflow governed and prevent drift.

A stage skill can delegate to action skills to execute specific tasks within that stage.

**Trigger:** explicit — defined by the workflow skill, triggered by the orchestrator announcing that stage.



### 4. Knowledge skills — semantic matching

Knowledge skills carry the same conventions, standards, and best practices as in the agentic approach. The difference is how they load.

In this implementation there is no agent frontmatter to drive implicit loading. Instead, knowledge skills load through **semantic matching** — Claude recognises language in the current context that matches the knowledge skill's description and loads it.

To keep this reliable and predictable, knowledge loading is coupled to workflow events. Because the orchestrator explicitly announces each stage ("Starting stage 2: Implementation"), that announcement provides consistent, recognisable language for knowledge skills to match against. Writing knowledge skill descriptions that reference known workflow language makes loading more deterministic and less reliant on arbitrary context.

**Trigger:** semantic match — description references language used by the orchestrator at workflow and stage boundaries.



## Skill Hierarchy

```
Orchestrator skill       ← launches and facilitates the workflow
  └── Workflow skill     ← defines stages and overall intent
        ├── Stage skill  ← defines what happens, exit criteria
        │     └── Action skill  ← executes a specific task
        └── Knowledge skill     ← loads via semantic match at stage boundaries
```



## Directory Structure

```
.claude/
  commands/              ← optional explicit trigger
  skills/
    orchestrator/        ← workflow orchestrator
    workflows/           ← workflow skill(s)
    stages/              ← one skill per stage
    actions/             ← action skills called by stages
    knowledge/           ← knowledge skills — conventions & standards
  hooks/                 ← guardrails
CLAUDE.md                ← project-level configuration
```



## Key Principles

**No agent frontmatter for implicit loading**
This approach does not use agent sub-contexts or frontmatter-driven implicit loading. Everything runs in the main session. Knowledge loading relies on semantic matching, not frontmatter.

**Knowledge pairing to workflow**
To keep knowledge loading predictable, couple knowledge skills to workflow and stage events. The orchestrator announces each stage clearly and consistently. Knowledge skill descriptions reference that same language so they match reliably — this reduces ambiguity without needing explicit calls.

**Exit criteria govern stage progression**
Each stage skill defines clear exit criteria. The orchestrator checks these before advancing. This keeps the workflow disciplined and prevents Claude from drifting past incomplete stages.

**Trigger summary**

| Skill | Trigger type | Triggered by |
|---|---|---|
| Orchestrator | Explicit or semantic | User |
| Workflow | Explicit | Orchestrator |
| Stage | Explicit | Orchestrator (per workflow) |
| Knowledge | Semantic match | Orchestrator stage announcements |



