# Agent Reliability Engineering

**A failure-first blueprint for production AI agents.**

Most AI agent tutorials teach you to build the demo. This repository is about the other 80% of the work — the part that decides whether an agent survives contact with real users, real data drift, and real 3 a.m. failures. It's organized around a six-layer, three-phase architecture: **Build → Observe → Recover**, treating agent reliability as its own discipline, the same way Site Reliability Engineering treats infrastructure reliability as its own discipline separate from "does the feature work."

This repo turns that architecture into something you actually build, not just read about: a Medium-ready article explaining the *why*, seven hands-on projects taking you from a single tool-calling agent to a full self-healing multi-agent platform, and a structured learning guide tying it all together.

## The Architecture

| Phase | Layer | What It Covers |
|---|---|---|
| **BUILD** — learn + test | 1. Foundation Stack | Agent frameworks and protocols: Claude Agent SDK/Claude Code, LangGraph, Pydantic AI, Google ADK, MCP tools |
| | 2. Evaluation | Test cases, semantic evals, behavior evals, CI checks, regression suites |
| **OBSERVE** — trace + ground | 3. Tracing | LLM traces, tool-call traces, latency & cost, retry/loop detection, run replay |
| | 4. Data Foundation | Source docs, metadata, versioning, vector store, freshness checks |
| **RECOVER** — control + improve | 5. Orchestration | State graphs, routing, handoffs, human review, fallback paths |
| | 6. Failure Recovery | Detect, diagnose, resume, add test, redeploy |

The core claim this repo argues, and tries to prove hands-on: **an agent architecture is only as production-ready as its weakest layer**, and most teams build Layer 1 extremely well, build Layer 2 adequately, and never really build Layers 3 through 6 at all — which is why so many agent projects work beautifully in a demo and fail unpredictably three weeks into production.

## What's in This Repository

| Path | What It Is |
|---|---|
| [`/article/`](article/) | A long-form article — written for Medium, ships as clean Markdown here — making the case for failure-first agent architecture. |
| [`/projects/`](projects/) | Seven hands-on projects, foundation to advanced, each building on the last, each with its own architecture diagram in three formats (Mermaid, draw.io, and a rendered PNG). |
| [`/learning-guide/`](learning-guide/) | A structured guide tying every project back to the six-layer architecture, with a suggested pace, prerequisite knowledge, and what to actually walk away knowing after each stage. |
| [`/architecture/`](architecture/) | The overall six-layer/three-phase system diagram (Mermaid + draw.io + PNG), independent of any single project — the reference diagram the whole repo is built around. |

## The Seven Projects, at a Glance

| # | Project | Primary Layer(s) |
|---|---|---|
| 1 | [First, a Production-Shaped Agent](projects/01-first-production-shaped-agent/) | Foundation Stack |
| 2 | [The Evaluation Harness](projects/02-the-evaluation-harness/) | Evaluation |
| 3 | [Full-Stack Tracing](projects/03-full-stack-tracing/) | Tracing |
| 4 | [The Versioned Data Foundation](projects/04-the-versioned-data-foundation/) | Data Foundation |
| 5 | [Multi-Agent Orchestration](projects/05-multi-agent-orchestration/) | Orchestration |
| 6 | [The Self-Healing Loop](projects/06-the-self-healing-loop/) | Failure Recovery |
| 7 | [Capstone: The Full Reliability Stack](projects/07-capstone-full-reliability-stack/) | All six, integrated |

Each project's own README states its real prerequisites, so you can see exactly why the order is what it is — Project 6's "add test" step, for instance, only means something because Project 2's regression suite already exists to add the test *to*.

## Who This Is For

Engineers who can already get an agent working — the demo, the hackathon prototype, the weekend project — and want the discipline to take it further: to a system a team can actually operate, debug at 2 a.m., and trust with real users and real consequences. This is not an introduction to what an AI agent is; Project 1 assumes you can already write one.

## Grounding and Currency

This repository was built with active research into the current (2026) state of the agent-framework, observability, and failure-recovery landscape rather than from static prior knowledge — the field moves fast enough that a stale reference here would actively mislead. Specific tools named throughout (LangGraph, Google ADK, Pydantic AI, Langfuse, LangSmith, MCP, and others) reflect their real current positioning at time of writing; frameworks and their comparative strengths will keep shifting, so treat the *architecture* here as the durable part and the *specific tool choices* as the current best answer, not a permanent one.

## License

MIT — see [LICENSE](LICENSE). Use this, adapt it, teach with it.
