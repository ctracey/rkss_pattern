# Tech Tips

## Table of Contents
- [Plugin Usage](#plugin-usage) - how to package neatly for reuse in specific scopes
- [CLI Tools](#cli-tools) - more efficient LLM token usage
- [MCP Services](#mcp-services) - tools becoming services (next evolution from cli tool)

## Plugin Usage

The RKSS pattern works naturally as a Claude Code plugin. A plugin packages your commands, agents, skills, and hooks into a distributable unit that can be installed into any project. This is how the system becomes reusable across projects and teams rather than rebuilt each time.

**Global plugin**
A global plugin is installed once and available across all projects. This is where your organisation's shared culture lives — coding standards, testing principles, commit conventions, governance rules. Any project that installs the global plugin inherits the baseline way of working without configuration.

Use a global plugin for:
- Shared knowledge skills that apply everywhere
- Organisation-wide guide rails and guardrails
- Common workflow stages that every project follows

**Project-specific plugin**
A project plugin extends or overrides the global baseline for a specific context. It might carry technology-specific conventions, domain knowledge, or bespoke workflows that only apply to that project. Because plugins compose, a project can install both a global plugin and a project-specific one — the knowledge layers stack.

Use a project plugin for:
- Technology-specific conventions (e.g. React, Python, Terraform)
- Domain-specific knowledge and terminology
- Workflows unique to that project or team

**Reference:**
shinpr's `claude-code-workflows` demonstrates this model — global dev skills installed once, frontend or backend workflows layered on top per project.


## CLI Tools

Not every problem needs a language model. When a task is deterministic, repeatable, and well-understood, a CLI tool will execute it faster, cheaper, and more reliably than Claude. The principle here is: **delegate to CLI where LLM is not required**.

A workflow stage or action skill can call a CLI tool directly rather than asking Claude to reason through the task. This keeps token usage efficient and reserves Claude's reasoning for work that actually benefits from it.

Examples of tasks better handled by CLI:
- Running tests, linting, type checking
- Git operations — staging, committing, pushing
- File generation from templates
- Environment setup and dependency installation
- Data transformation with known schemas

The skill or agent simply invokes the CLI tool as a step in the workflow. Claude orchestrates when to run it — the CLI does the work.

**Pattern:** action skill → invokes CLI → returns result to orchestrator


## MCP Services

CLI tools evolve. When a CLI tool matures — when its interface stabilises, when it starts to be used across multiple workflows, when it would benefit from being accessible as a service — it becomes a candidate for an MCP server.

An MCP (Model Context Protocol) service exposes a recognised tool interface that Claude can call directly. Where a CLI tool is invoked as a subprocess, an MCP service is a first-class tool in Claude's context — with structured inputs, outputs, and discoverability.

**The evolution path:**

```
Manual task
  → action skill (Claude reasons through it)
    → CLI tool (deterministic, fast, cheap)
      → MCP service (structured interface, composable, shareable)
```

This progression represents increasing maturity. You don't start at MCP — you start with a skill, identify what can be delegated, build a CLI, and promote it to MCP when the interface has stabilised and the value of sharing it is clear.

**When to consider MCP:**
- The CLI tool is stable and its interface is well-defined
- Multiple projects or workflows would benefit from it
- The tool needs to be discoverable and composable with other tools
- You want structured inputs and outputs rather than raw CLI invocation