# Supporting Concepts


## Overview

These are concepts that complement and extend the RKSS pattern. They are not required to get started, but they inform how a mature system evolves — from a project-specific skill set, to a shared plugin, to delegating work to CLI tools and eventually MCP services.

- [Governance: Guide Rails & Guardrails](#governance-guide-rails--guardrails)
- [Context Documentation & Small Scoped Work](#context-documentation--small-scoped-work)
- [Self-Reflection](#self-reflection)
- [Maturity Model](#maturity-model)

## Governance: Guide Rails & Guardrails

Two levels of governance sit across the system. Guide rails nudge towards good behaviour. Guardrails enforce hard boundaries when things have gone too far.

**Guide Rail** `soft`
Soft guidance that outlines what good looks like. A suggestion embedded in a knowledge skill — it informs the agent's behaviour without blocking execution.
> e.g. always confirm the user is ready before committing changes

**Guardrail** `hard`
A hard stop that intercepts when things have gone too far. Implemented as a hook — it blocks execution and requires explicit user action before proceeding.
> e.g. a hook on git commit that enforces the user has confirmed they are ready

## Context Persistence & Small Scoped Work

Two concepts that work together to make the system resilient and repeatable.

**Context persistence**
Claude has no memory between sessions. Left unaddressed, this means progress, decisions, and domain knowledge accumulated in one session are lost when it ends. Context documentation solves this by persisting what matters outside the session — in version-controlled markdown files that live in the repository.

This includes:
- Work item intent — why this change is being made
- Design decisions — what was considered and what was chosen
- Specifications — what done looks like
- Scenarios — the cases that need to work
- Progress state — what has been completed, what is next

When a new session starts, Claude reads the relevant documentation and picks up exactly where the last session left off. The context window starts clean but informed. The knowledge lives in the repo, not in the conversation.

**Small scoped work items with feedback loops**
Large tasks are the enemy of reliable agentic work. The bigger the scope, the more opportunity for drift, the harder it is to validate, and the more painful it is when something goes wrong. Breaking work into small, prioritised items — each with clear intent, specs, and scenarios — keeps sessions focused and outcomes verifiable.

Each small work item has its own feedback loop: implement, validate against scenarios, confirm exit criteria, document outcome. The loop is short enough that failure is cheap and recovery is fast.

**Together: resilient and repeatable**
These two concepts compound. When context is documented and work is small, a failed session is not a setback — it is a restart. Claude re-reads the persisted context, picks up the current work item, and tries again from a known state. Nothing is lost. The system is resilient by design, not by luck.

This also supports quality: because each work item has documented scenarios and exit criteria, success is defined before work begins. Validation is not an afterthought — it is built into the loop.

> Small work items + persisted context = repeatable success regardless of session outcome.



## Self-Reflection

A mature RKSS system doesn't just execute — it observes itself. Self-reflection is the concept of the system paying attention to its own behaviour, efficiency, and outcomes, and using that to improve over time.

This can operate at two levels.

**Reflection within the system**
A workflow can include a wrap-up stage that runs after the main work is complete. Rather than ending abruptly, the orchestrator closes the session by asking: what happened, what worked, what didn't, and what should be captured? This produces structured observations — not just outputs, but learnings about the process itself.

These observations can be written to a persistent document outside the session — a recommendations file, a learnings log, or notes appended to the relevant skill. Over time this creates an evolving record of how the system is actually performing, not just how it was designed to perform.

**The system observing the system**
At a higher level, a dedicated reflection workflow can analyse patterns across sessions. Which stages consistently hit exit criteria? Which knowledge skills load when they shouldn't — or fail to load when they should? Where does Claude drift off-recipe? This kind of systemic observation surfaces inefficiencies that are invisible within a single session.

Outputs from this level of reflection might include:
- Recommended changes to knowledge skill descriptions to improve matching
- Suggested refinements to stage exit criteria
- Identification of tasks that should be delegated to CLI
- Flags for guardrails that are triggering too frequently or not enough

**Self-healing**
The most advanced expression of this concept is a system that acts on its own observations — updating skill descriptions, refining workflow stages, or adding new knowledge based on what it has learned. This is not fully autonomous: changes to the system should be reviewed and confirmed before being applied. But the system can draft the changes, present them for approval, and apply them on instruction. The human stays in control; the system does the thinking.

**Captured recommendations**
At minimum — and as a practical starting point — every workflow can end with a short structured note: what was done, what was hard, what would make it better next time. These accumulate into a living document that informs the next iteration of the system. This is low-cost self-reflection that compounds over time.



## Maturity Model

These concepts map to a natural progression as a system matures:

| Stage | Mechanism | When |
|---|---|---|
| Start | Skills & agents in project | Building and learning |
| Share | Plugin — project or global | Pattern is stable, used across projects |
| Delegate | CLI tool | Task is deterministic, LLM not needed |
| Scale | MCP service | CLI interface has matured, worth sharing |

Start simple. Evolve deliberately.

