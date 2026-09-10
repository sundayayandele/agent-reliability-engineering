# Project 5 — Multi-Agent Orchestration

**Layer:** 5 — Orchestration | **Phase:** RECOVER | **Level:** Advanced

## Scenario

One agent doing triage, runbook lookup, and drafting customer communications is starting to strain — the prompt is getting long, the tool list is getting crowded, and it's genuinely three different jobs wearing one agent's clothes. Worse, one of those jobs (drafting anything that might reach a customer) is exactly the kind of consequential action that Layer 5 exists to put a human in front of, and a single monolithic agent makes that much harder to enforce consistently.

## Learning Objectives

- Recognize when a single agent has actually outgrown itself, versus when multi-agent complexity is being added for its own sake
- Build an explicit state graph instead of letting control flow live implicitly in prompt history
- Implement routing, handoffs, a human-review gate, and fallback paths as first-class, inspectable parts of the system
- Understand why a fallback path is only as trustworthy as the data foundation and tracing underneath it

## Tasks

1. **Split Aegis into three specialist agents**: a **Triage Agent** (Project 1's original job), a **Runbook Agent** (owns Project 4's retrieval, answers "what's the documented fix for this"), and a **Comms Agent** (drafts a customer- or stakeholder-facing status update — never sends one).
2. **Build an explicit state graph** coordinating them: Triage runs first and classifies severity; on high severity, route to Runbook for a documented fix; Runbook's findings feed Comms for a draft update. Make the current state inspectable at every step — not implicit in a growing message history.
3. **Implement a human-review gate in front of the Comms Agent's output specifically.** No draft reaches anywhere real without an explicit human approval step — this is the single most consequential action in the whole system, and it's the one place automation should not be allowed to run fully autonomously yet.
4. **Implement a fallback path**: if the Runbook Agent's retrieval comes back empty or low-confidence (using Project 4's freshness/versioning signals), the state graph should route to a "no documented fix, escalate to human" path instead of letting Comms draft a confident-sounding update based on nothing.
5. **Add handoff logging**: every transition between agents should be visible in Project 3's tracing as its own step, with what was handed off and why — an orchestration layer you can't trace is exactly as opaque as the single-agent version it replaced.
6. **Run the full pipeline against 10 end-to-end scenarios**, including at least 2 designed specifically to trigger the fallback path, and confirm human review is never skipped for a Comms output.

## Deliverable

A working three-agent Aegis with an explicit, traced state graph, a human-review gate that cannot be bypassed, and a demonstrated fallback path for the no-documented-fix case.

## Why This Comes Before Failure Recovery

Project 6 builds the loop that detects, diagnoses, and resumes from a failure — but "resume" only means something if there's an explicit state to resume *to*. A monolithic agent with no inspectable state graph has nothing for Project 6 to checkpoint against; this project's explicit orchestration is what makes recovery-by-resuming possible at all, rather than recovery-by-starting-over.

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd), [`diagram.drawio`](architecture/diagram.drawio), [`diagram.png`](architecture/diagram.png).

## Next

[Project 6 — The Self-Healing Loop](../06-the-self-healing-loop/) turns this multi-agent Aegis into a system that watches *itself* — detecting when one of its own agents fails, diagnosing why, and recovering without starting the whole pipeline over.
