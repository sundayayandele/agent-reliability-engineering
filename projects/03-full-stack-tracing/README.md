# Project 3 — Full-Stack Tracing

**Layer:** 3 — Tracing | **Phase:** OBSERVE | **Level:** Intermediate

## Scenario

Project 2's CI gate just failed on a change nobody expected to be risky — a "small" prompt tweak that was supposed to make Aegis's `suggested_first_action` more concise. The semantic-eval score dropped. Nobody knows why yet, because right now the only evidence you have is a number that got worse. Project 3 builds the thing that turns "the score dropped" into "here's the exact step where it went wrong."

## Learning Objectives

- Understand why agent failures overwhelmingly hide in an intermediate step, not the final answer
- Instrument every LLM call and every tool call as a structured trace, not a scattered print statement
- Capture the specific signals that matter for an agent (not just a web request): latency and cost *per step*, retry/loop counts, and full tool-call arguments
- Use run replay to re-investigate a specific failure without re-triggering the whole system from scratch

## Tasks

1. **Pick a tracing approach and set it up end to end.** A self-hosted option like Langfuse, a managed option like LangSmith, or raw OpenTelemetry with the generative-AI semantic conventions are all legitimate — the requirement is that every LLM call and every MCP tool call from Project 1's Aegis produces one connected trace, not disconnected log lines.
2. **Capture, at minimum, per step:** the exact prompt and completion, token counts, latency, estimated cost, and — for tool calls — the tool name, the exact arguments passed, and the raw result returned.
3. **Add loop and retry detection.** Instrument a counter that flags when Aegis calls the same tool with the same (or near-identical) arguments more than once in a single run — this is the single earliest warning sign of the failure Project 6 exists to fix, and you want visibility into it long before it becomes a real incident.
4. **Re-run the "small prompt tweak" scenario from the top of this README** (or invent an equivalent regression) and use your trace data — not guesswork — to identify exactly which step's output changed and why the semantic eval score moved.
5. **Implement run replay**: given a trace ID, re-execute that exact run's tool calls against the same recorded arguments and compare the new output to the original. This is what makes a trace something you can *investigate*, not just something you can *read*.
6. **Add cost and latency dashboards** aggregating across all traced runs — not because Aegis is expensive yet, but because the instrumentation that catches a cost blowup later has to exist before the blowup happens, not after.

## Deliverable

A fully traced Aegis, with loop/retry detection, a working run-replay capability demonstrated against a real recorded trace, and a written root-cause finding for the "prompt tweak" regression — showing the specific trace evidence that explains it, not just the eval score that flagged it.

## Why This Comes Before Data Grounding

It would be reasonable to assume Project 4 (grounding Aegis in real incident/runbook data) should come first, since a better-grounded agent seems like a bigger win than better observability. But Project 4 will introduce its own new failure mode — a stale or missing document — and without this project's tracing already in place, that failure mode is invisible in exactly the same way the prompt-tweak regression was invisible a moment ago. Observability has to exist before you add the next thing that needs observing.

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd), [`diagram.drawio`](architecture/diagram.drawio), [`diagram.png`](architecture/diagram.png).

## Next

[Project 4 — The Versioned Data Foundation](../04-the-versioned-data-foundation/) grounds Aegis in real incident history and runbooks — and because this project's tracing already exists, you'll be able to *see* the difference grounding makes, step by step, instead of just hoping it helped.
