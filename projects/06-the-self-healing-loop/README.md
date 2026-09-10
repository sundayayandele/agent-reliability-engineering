# Project 6 — The Self-Healing Loop

**Layer:** 6 — Failure Recovery | **Phase:** RECOVER | **Level:** Advanced

## Scenario

Aegis now has five layers of maturity and still fails sometimes — every production system does. The difference this project builds is what happens in the thirty seconds after it does: right now, a failure means a human notices something's wrong, manually digs through traces, manually figures out the fix, and manually redeploys. This project automates everything in that sentence except the parts that genuinely need a human.

## Learning Objectives

- Classify agent failures by type, not treat every failure as the same generic "something went wrong"
- Build detection that runs continuously, using Project 3's tracing as its input, not a human noticing
- Implement resume-from-checkpoint recovery instead of restart-from-scratch
- Close the loop for real: every diagnosed failure becomes a permanent addition to Project 2's regression suite

## Tasks

1. **Define your failure taxonomy.** At minimum: malformed tool arguments, stale or contradictory retrieved context (Project 4's territory), a repeated reasoning loop (Project 3 already detects this), and an unsupported final answer (a claim in the output not actually backed by anything retrieved or returned). Each class gets a different fix — this taxonomy is what separates a self-healing system from a blind retry loop.
2. **Build the detector.** Consume Project 3's trace stream in real time (or near-real time) and flag a run as failed the moment it matches one of your failure classes — not after a human files a ticket.
3. **Build the diagnoser.** Given a flagged run, classify *which* failure class it actually is, using the trace evidence: a malformed-argument failure looks different in a trace than a stale-context failure, and your diagnoser should be able to tell them apart programmatically, not just guess.
4. **Implement resume-from-checkpoint.** Using Project 5's explicit state graph, recovery should restart from the last good state (e.g., re-run just the Runbook Agent's retrieval with corrected arguments) rather than discarding the Triage Agent's already-completed, still-valid work and starting the whole pipeline over. Measure and report your recovery time for at least 3 induced failures.
5. **Close the loop: add test.** For every failure your system diagnoses correctly during testing, write the corresponding regression test and add it to Project 2's suite. This is not optional polish — a self-healing system that doesn't do this will happily rediscover the same failure forever.
6. **Build the redeploy step as a deliberate, gated action**, not an automatic hot-patch — a fix for a classified failure should go through the same CI gate (Project 2) as any other change before it ships, even when the system that found the bug is the same system proposing the fix.
7. **Run a controlled failure-injection test**: deliberately induce at least 4 different failures (one per taxonomy class from Task 1) and document, for each, the detection time, the diagnosis, the recovery action taken, and the new regression test it produced.

## Deliverable

A working detect → diagnose → resume → add-test → redeploy loop, a documented failure taxonomy, and a failure-injection test report covering at least 4 distinct failure classes with measured recovery times.

## The MAPE-K Lineage

This loop isn't a novel invention — it's a close relative of MAPE-K (Monitor, Analyze, Plan, Execute, over a shared Knowledge base), a decades-old pattern from autonomic computing for building self-managing systems. What's genuinely new here is applying that discipline to a system whose core reasoning step is non-deterministic, where "the same input" can legitimately produce two different (and both defensible) outputs — which is exactly why diagnosis (Task 3) has to be evidence-based against the trace, not just "it didn't match expected output, retry."

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd), [`diagram.drawio`](architecture/diagram.drawio), [`diagram.png`](architecture/diagram.png).

## Next

[Project 7 — Capstone: The Full Reliability Stack](../07-capstone-full-reliability-stack/) integrates everything built so far into one deployed system, and asks you to run a "chaos day" against your own creation.
