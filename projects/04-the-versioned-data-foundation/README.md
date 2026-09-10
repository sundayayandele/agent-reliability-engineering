# Project 4 — The Versioned Data Foundation

**Layer:** 4 — Data Foundation | **Phase:** OBSERVE | **Level:** Intermediate

## Scenario

Aegis has been triaging on gut reasoning alone — no memory of past incidents, no access to runbooks, nothing but the prompt and whatever the LLM already knew. That's about to change: you're grounding Aegis in your organization's actual incident history and runbook library. This is also exactly where a new, very real failure mode enters the system — the one this whole repository's originating case study is built around: an agent confidently citing something that used to be true.

## Learning Objectives

- Build a real retrieval pipeline: chunking, embedding, indexing over source documents
- Understand why metadata (especially retirement/supersession status) has to travel with every chunk, not live only in the source document
- Implement a versioned index with an atomic cutover, so a bad reindex never takes production down mid-update
- Build an automated freshness check that catches stale content *before* a user does

## Tasks

1. **Assemble a real corpus**: at least 15 runbooks/postmortems (write realistic ones if you don't have real incident history — each should describe a service, a failure mode, and a resolution). Deliberately include at least one **retired** runbook describing an old process that a newer runbook has since superseded — don't skip this, it's the point of the project.
2. **Build the ingestion pipeline**: chunk by semantic boundary (paragraph/section), not a fixed character count that can cut a runbook step in half. Attach metadata to every chunk: source document, section, and a `status` field (`current` or `retired`).
3. **Embed and index the corpus** in a real vector store (pgvector, Qdrant, or an in-memory equivalent for this project's scale is fine).
4. **Reproduce the failure on purpose**: query Aegis about the retired process and confirm — using Project 3's tracing — that the retired runbook is retrieved and that nothing in the current pipeline knows it's stale. This is your baseline "before."
5. **Fix it**: filter retired content out of retrieval by default, and implement a versioned index with an atomic cutover — build a new index version fully, run a smoke test against a known-good query, and only then flip which version is live, keeping the previous version available for instant rollback.
6. **Build an automated freshness check**: a scheduled job that flags any indexed document past a configurable age threshold, or any document whose source has a newer version, for human review — this is what catches staleness *before* the next Aegis query does, not after.
7. **Re-run the retired-process query from Task 4 and confirm the fix**, using your Project 3 trace to show the retired document no longer appears in the top results.

## Deliverable

A working retrieval pipeline over a 15+ document corpus with versioned, atomic-cutover indexing; a documented before/after trace showing the retired-document bug reproduced and then fixed; and a working freshness-check job.

## Why This Comes Before Orchestration

Multi-agent orchestration (Project 5) is meaningless to build on top of an ungrounded agent — routing a task to "the runbook specialist" only makes sense once there's a real, trustworthy runbook retrieval system for that specialist to use. Grounding has to be solid before you start splitting responsibility across multiple agents that all depend on it.

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd), [`diagram.drawio`](architecture/diagram.drawio), [`diagram.png`](architecture/diagram.png).

## Next

[Project 5 — Multi-Agent Orchestration](../05-multi-agent-orchestration/) splits Aegis into specialists — a triage agent, a runbook-lookup agent, and a comms-drafting agent — coordinated through a state graph with a human-review gate before anything reaches a real user.
