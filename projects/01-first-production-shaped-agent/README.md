# Project 1 — First, a Production-Shaped Agent

**Layer:** 1 — Foundation Stack | **Phase:** BUILD | **Level:** Foundation

## The Running Example: Aegis

Every project in this repository builds one system: **Aegis**, an incident-triage agent. It's a deliberate choice — the system you're building *is itself* a reliability tool, so as Aegis gains each layer of this architecture, it becomes more capable of doing exactly the kind of work this whole repo is about. By Project 7, Aegis is a small, real, deployable on-call support platform. Today, in Project 1, it's a single agent that does one thing.

## Scenario

An engineering team gets paged constantly with vague incident reports — "checkout is slow," "users can't log in," "API returning 500s" — and every page starts with the same five minutes of manual work: figure out which service owns this, guess a severity, decide who to loop in. Aegis v0's job is to automate exactly that first five minutes, no more.

## Learning Objectives

- Build a single-purpose agent using a real 2026-current framework (LangGraph, Pydantic AI, Google ADK, or the Claude Agent SDK — pick one; this project's tasks are framework-agnostic)
- Give the agent one real tool via the Model Context Protocol (MCP), not a hand-rolled function call
- Force typed, structured output instead of freeform text — the single highest-leverage habit for everything Projects 2 through 7 build on top of
- Understand *why* "production-shaped" starts here, before there's anything to evaluate, trace, or recover

## Tasks

1. **Pick a framework and justify it in one paragraph in your own README.** Don't just default to the first one you've heard of — actually compare LangGraph's explicit state-graph model against Pydantic AI's lighter-weight, type-first approach against Google ADK's code-first agent-tree model against the Claude Agent SDK's deep-agent harness, and pick based on what Aegis actually needs (a single agent, one tool, typed output — this is intentionally a case where more than one framework is a perfectly reasonable choice; the point is that you reasoned about it).
2. **Define Aegis's output as a typed schema before writing a single prompt.** At minimum: `severity` (an enum, not a free string), `likely_owning_team`, `suggested_first_action`, and `confidence`. This schema is the contract every later project's evaluation, tracing, and orchestration work will depend on — get it right now.
3. **Build one MCP tool: a service-ownership lookup.** Given a service or component name, it returns the owning team and an escalation contact. A flat JSON file or a tiny SQLite table is a completely legitimate "database" for this — the point is that the agent calls a real, structured tool through MCP, not that the data source is sophisticated.
4. **Wire the agent to call that tool when it needs ownership information, not always.** A well-shaped agent reasons about *when* a tool call is warranted; forcing a tool call on every single input regardless of whether it's needed is a shortcut that Project 2's evaluation work will specifically catch and penalize.
5. **Run it against 10 realistic, messy incident descriptions** (write these yourself — vague, ambiguous, occasionally missing information, the way real pages actually read) and eyeball the output. Don't build an evaluation harness yet — that's Project 2, deliberately kept separate so you feel the difference between "eyeballing output" and "actually knowing it works."

## Deliverable

A working Aegis v0: one agent, one typed output schema, one MCP tool, tested by hand against 10 realistic inputs, with a short README section explaining your framework choice and your schema design decisions.

## Why This Is Where Everything Starts

Nothing in this project is about failure, tracing, or recovery — deliberately. Layer 1 is the one layer every agent tutorial already teaches well, and this project doesn't try to reinvent that. What it insists on, that a typical weekend-project tutorial often doesn't, is the *shape* of the output and the *structure* of the tool call — because Project 2 cannot evaluate an agent that returns freeform prose with no consistent schema, Project 3 cannot usefully trace a tool call that isn't a real, structured MCP call, and Project 5's orchestration cannot route or hand off a result that has no predictable shape. Every later project's difficulty is a direct function of how disciplined this one was.

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd) (Mermaid source), [`diagram.drawio`](architecture/diagram.drawio) (draw.io/mxGraph source), and [`diagram.png`](architecture/diagram.png) (rendered).

## Next

[Project 2 — The Evaluation Harness](../02-the-evaluation-harness/) takes this exact agent and builds the test suite that proves it actually works, instead of just looking like it does.
