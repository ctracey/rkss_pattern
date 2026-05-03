# Agentic Onboarding with Recipe & Knowledge Skill Separation (RKSS)

This is my attempt to understand and articulate the emerging community recognised "Recipe & Knowledge Skill Separation" pattern.
(Based on my experiments with Claude Code)

> Scalable teams don't carry their entire culture in people's heads — they externalise it into systems, conventions, and processes that anyone can inherit. Onboarding a new team member means handing them the system, in addition to shadowing someone. RKSS applies that same thinking to agents. Instead of prompting Claude differently every time, you build the culture once: the workflows, the conventions, the standards. Claude inherits it — the same way a well-onboarded person would. This is systems thinking applied to agentic work.

## PATTERN: Recipe & Knowledge Skill Separation

A pattern for organising Claude Code skills into two distinct types with different responsibilities and trigger behaviours. Recipe skills define repeatable processes. Knowledge skills carry conventions and context. Keeping them separate allows orchestration to stay stable while the knowledge layer is swapped for different technologies, teams, or domains.

### The Two Skill Types

**Recipe skill** `explicit`
Defines a repeatable process — a workflow, diagnostic, or review cycle. Invoked intentionally by slash command. Orchestrates what happens and in what order.

**Knowledge skill** `implicit`
Carries conventions, best practices, and domain context. Loads automatically when context matches its frontmatter description. Informs how things happen without being directly invoked.

### Why It Matters

The separation keeps orchestration stable while allowing the knowledge layer to be swapped. Same recipe, different conventions — React or Vue, your team's standards or a client's. It also enables agentic onboarding: an agent picks up a way of working the same way a new team member would — here's what to do, here's how to do it right.

## 3 Tier Approach

Three key parts that describe the system process — how a workflow is triggered, how it runs, and what informs it along the way.

**Tier 1 — Orchestrate**
Defines what happens and when. The recipe skill triggers the process, sequences the stages, and coordinates execution. This is the stable layer — it doesn't change when technology or conventions change.

**Tier 2 — Execute**
Does the work. Action skills perform specific steps within a workflow — a commit, a review, a test run. Focused and single-purpose. May be called by the orchestrator or invoked directly.

**Tier 3 — Guide**
Informs how execution happens. Knowledge skills carry conventions, standards, and best practices. Swappable by technology or domain. This is the layer that encodes your culture.

## Supporting Docs
 - [Supporting concepts](docs/supporting-concepts.md)
 - [Tech tips](docs/tech-tips.md)
 - [Implementation - Agentic approach (shinpr)](docs/implementation-agentic-approach.md)
 - [Implementation - Workflow approach](docs/implementation-workflow-approach.md)

