# Project 2 — The Evaluation Harness

**Layer:** 2 — Evaluation | **Phase:** BUILD | **Level:** Foundation+

## Scenario

Aegis v0 works — you eyeballed 10 examples in Project 1 and the output looked reasonable. "Looked reasonable" is not a claim you can make to a team that's about to let this thing triage real pages, and it's not a claim that survives the moment someone tweaks the prompt next month and quietly makes it worse. Project 2 replaces "looked reasonable" with a real, repeatable answer to "does it actually work."

## Learning Objectives

- Build a golden test set with testable properties, not one exact right answer per input
- Distinguish test cases (hard pass/fail) from semantic evals (graded quality) from behavior evals (does it stay inside its boundaries)
- Wire evaluation into CI so a regression is caught before merge, not after a user notices
- Build a regression suite designed to only grow — every future incident (Project 6) permanently adds to it

## Tasks

1. **Build a golden set of 25 incident descriptions** with the correct `severity`, `owning_team`, and a description of what a *reasonable* `suggested_first_action` looks like (not one exact string — a property, e.g., "must mention checking recent deploys").
2. **Write test cases** checking the hard, structural stuff: does the output actually validate against Project 1's schema, is `severity` always one of the defined enum values, does `owning_team` match ground truth exactly.
3. **Write semantic evals** for `suggested_first_action` using an LLM-as-judge or embedding-similarity scorer against your property description — this is graded, not pass/fail, and should produce a score you can track over time.
4. **Write at least 3 behavior evals** that are deliberately adversarial: an incident description that tries to get Aegis to recommend paging an on-call engineer at 3 a.m. for something clearly non-urgent; an ambiguous input with no service name at all (does it correctly say "insufficient information" instead of guessing confidently?); an input containing a prompt-injection-style instruction embedded in the incident text itself (does Aegis follow it, or stay on task?).
5. **Wire the whole set into a CI check** that fails a pull request if the hard test-case pass rate or the semantic-eval average drops below a threshold you define and justify.
6. **Deliberately break Aegis** (e.g., strip the tool-call step, or loosen the schema) and confirm your evaluation suite actually catches it — an evaluation harness that can't catch a deliberately-introduced regression isn't proving anything yet.

## Deliverable

A 25-item golden set, a CI-integrated eval suite (test cases + semantic evals + behavior evals) with a documented pass threshold, and a short writeup of the deliberate-break exercise: what you broke, and whether the suite caught it on the first try.

## Why This Comes Before Tracing

It's tempting to reach for tracing (Project 3) first, since watching an agent run feels more immediately useful than writing test cases against one. But evaluation answers "is this correct" and tracing answers "what happened" — and you need the first answer before the second one is actionable. Without Project 2's golden set, Project 3's traces just show you an agent doing *something*; you have no independent way to know if that something was right.

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd), [`diagram.drawio`](architecture/diagram.drawio), [`diagram.png`](architecture/diagram.png).

## Next

[Project 3 — Full-Stack Tracing](../03-full-stack-tracing/) instruments Aegis so that when the eval suite's pass rate drops, you can actually see *why*, step by step, instead of just knowing *that* it did.
