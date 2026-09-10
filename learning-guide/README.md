# Learning Guide — Agent Reliability Engineering

This guide ties the article, the six-layer architecture, and the seven projects together into one learning path — what to know before you start, how to pace it, and what you should genuinely be able to do at the end of each stage. It assumes you've read [`/article/design-for-the-crash.md`](../article/design-for-the-crash.md) first; if you haven't, start there — this guide is the "now go build it" companion to that article's "here's why," not a replacement for it.

## Prerequisite Knowledge

Before Project 1, you should already be able to:

- Write a working Python (or TypeScript) agent using *some* framework — this repository does not teach what an agent is
- Make an LLM API call, handle a streaming response, and parse a structured output
- Read and write basic YAML/JSON configuration
- Use Git and GitHub comfortably enough to track your own work across 7 projects without losing history

You do **not** need prior experience with: MCP, vector databases, distributed tracing, multi-agent orchestration, or any specific observability platform. Every project introduces exactly one new discipline; you're expected to learn *that* discipline hands-on, not arrive already knowing it.

## Suggested Pace

| Stage | Projects | Realistic Timeframe | Why This Pace |
|---|---|---|---|
| Foundation | 1–2 | 1–2 weeks | Project 1 is genuinely quick if your framework choice is comfortable; Project 2's evaluation discipline is the one most people underestimate — budget real time for writing adversarial behavior evals well, not just checking a box. |
| Observability | 3–4 | 2–3 weeks | Project 3's tracing setup has real infrastructure overhead (standing up Langfuse or equivalent). Project 4 is the single most time-consuming project in the set — a real 15+ document corpus with deliberate staleness takes longer to build well than it looks like it should. |
| Reliability | 5–6 | 2–3 weeks | Project 5's state graph redesign is a genuine architectural rewrite, not an addition — don't rush it. Project 6 is the conceptual peak of the whole repository; if you only have time to do one project *slowly and well*, make it this one. |
| Capstone | 7 | 1–2 weeks | Mostly integration work if 1–6 were built cleanly, mostly rework if they weren't — which is itself useful information about where your own work needs to go back and get more solid. |

Total: roughly 8–12 weeks at a sustainable, part-time pace. This is not a weekend project, and treating it like one defeats the point — the entire argument of this repository is that the slow, unglamorous layers are where the real engineering is.

## Layer-by-Layer: What You Should Walk Away Knowing

### Layer 1 — Foundation Stack (Project 1)
**You should be able to:** justify a framework choice against actual requirements instead of defaulting to whichever one you've heard of most; design a typed output schema *before* writing a prompt; distinguish "the agent has a tool" from "the agent has a real, structured MCP tool call."
**Common mistake to watch for:** treating Layer 1 as the whole project instead of the entry point. If you find yourself polishing Aegis v0's prompt engineering for a second week, you've mistaken the foundation for the building.

### Layer 2 — Evaluation (Project 2)
**You should be able to:** explain the difference between a test case, a semantic eval, and a behavior eval without hesitating; write an adversarial eval that's actually adversarial, not a softened version of one; prove your evaluation suite works by using it to catch a bug you planted yourself.
**Common mistake to watch for:** writing evals that only check the happy path. A suite that never fails during development almost certainly isn't testing anything hard enough to matter in production.

### Layer 3 — Tracing (Project 3)
**You should be able to:** read a trace tree and identify which specific step of a multi-step run produced a bad output, without guessing; explain why "it worked when I tested it manually" and "the trace shows it working" are different claims with different evidentiary weight.
**Common mistake to watch for:** instrumenting only the LLM calls and skipping tool-call tracing, because tool calls feel like "just plumbing." Tool-call failures are one of the most common real-world agent failure modes — undertraced plumbing is exactly where problems hide.

### Layer 4 — Data Foundation (Project 4)
**You should be able to:** reproduce a stale-retrieval bug on demand and then fix it with a versioned, atomic-cutover index; explain why metadata has to travel with a chunk instead of living only in the source document.
**Common mistake to watch for:** building the corpus too small or too clean. If every document in your test corpus is obviously current and well-formed, you haven't actually built the failure mode this project exists to teach — go back and add the messy, contradictory, or genuinely outdated material a real organization would actually have.

### Layer 5 — Orchestration (Project 5)
**You should be able to:** point to the exact moment in your state graph where a human-review gate sits, and explain why it's there and what specifically it prevents; demonstrate a fallback path actually triggering, not just existing in the code unexercised.
**Common mistake to watch for:** adding multi-agent complexity that doesn't earn its keep. If your three agents could genuinely be one agent with three tools, splitting them was complexity for its own sake — Layer 5 exists to justify coordination overhead, not to add it reflexively.

### Layer 6 — Failure Recovery (Project 6)
**You should be able to:** name your failure taxonomy's categories from memory and explain what's different about diagnosing each one; show a measured recovery time for a resumed-from-checkpoint recovery versus a restarted-from-scratch one, and explain why the difference matters at scale.
**Common mistake to watch for:** building "detect and alert a human" and calling it done. That's monitoring, not self-healing — the bar this layer sets is resume, add test, and redeploy, not just noticing.

### Capstone — Integration (Project 7)
**You should be able to:** hand your onboarding doc to someone else and watch them actually succeed at the first-day task without your help; describe, from your chaos-day report, at least one failure that Layer 6 caught that you're confident would have gone unnoticed for hours or days under your Project-1-era Aegis.
**Common mistake to watch for:** treating the capstone as a demo day instead of a stress test. The chaos day is supposed to be uncomfortable — if nothing breaks in ways you didn't fully expect, you likely didn't push hard enough.

## How to Use This If You're Teaching, Not Just Learning

Every project's README states its own prerequisites in its "Why this comes before/after" section — that's deliberately written so an instructor can assign projects individually and still have students understand *why* the sequence is what it is, not just follow it obediently. The chaos-day exercise in Project 7 works well as a cohort exercise: have pairs or small groups inject failures into each other's Aegis builds rather than their own, since a system's own builder is often the worst-positioned person to find its blind spots.

## After the Capstone

See the main repository README's closing section for suggested directions this guided path deliberately left out of scope — this repository teaches the six-layer discipline on one worked example; applying it to your own real system, at your own real scale, with your own organization's real constraints, is the next project, and it's yours to define.
