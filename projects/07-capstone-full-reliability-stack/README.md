# Project 7 — Capstone: The Full Reliability Stack

**Layers:** All 6, integrated | **Phases:** BUILD → OBSERVE → RECOVER | **Level:** Capstone

## Scenario

Six projects, six layers, one agent — but they've been built and tested somewhat independently. This capstone asks the question none of the previous six could answer alone: does it actually work as *one system*? A production platform isn't six components that each pass their own tests; it's one thing a team can deploy, operate, debug at 2 a.m., and trust — and that's a different, harder bar than "every layer works in isolation."

## Learning Objectives

- Integrate all 6 layers into one deployed, coherent Aegis, not 6 adjacent demos
- Write the operational documentation a new team member would actually need to run this system without you
- Design and run a deliberate "chaos day" against your own system, and watch Layer 6 catch what you break
- Produce a portfolio-ready artifact: something a hiring manager, a technical reviewer, or your own future self could pick up cold

## Tasks

1. **Deploy the full stack together.** All three Project 5 agents, Project 4's versioned retrieval, Project 3's tracing wired through every step, Project 2's eval suite gating every change, and Project 6's self-healing loop watching the whole thing continuously — as one running system, not six separate demos you flip between.
2. **Write a real runbook for Aegis itself** (yes — the incident-response agent needs its own incident-response documentation): how to check its health, how to read its dashboards, what to do if the self-healing loop itself stops running, and how to safely roll back a bad deploy.
3. **Write an onboarding doc** aimed at a new engineer joining the team, sufficient for them to ship one small, safe change (e.g., adding a new runbook to Project 4's corpus) within their first day, without you sitting next to them.
4. **Run a chaos day.** Deliberately, and without scripting the exact details in advance, inject at least 5 failures spanning multiple layers in a single session — a malformed tool call, a stale document reintroduced into the index, a severity-routing edge case, a deliberately ambiguous incident report, and at least one failure you invent yourself that isn't covered by this list. Document what Layer 6 caught automatically, what required a human, and how long each took.
5. **Produce a cost and reliability report**: token/API cost per triaged incident, average time-to-detection for an injected failure, average recovery time, and the current size of your regression suite (it should be visibly larger than when Project 2 started).
6. **Package it for a portfolio**: a clean top-level README for the integrated system (separate from this project's own README), an architecture walkthrough referencing every layer, and — if you're comfortable sharing it — a short recorded walkthrough demonstrating one real chaos-day failure being caught and resolved end to end.

## Deliverable

A fully integrated, deployed Aegis; an operational runbook and a new-engineer onboarding doc; a documented chaos-day report covering 5+ injected failures across multiple layers; a cost/reliability report; and a portfolio-ready package.

## The Question This Capstone Actually Tests

Not "can you build an agent" — every one of the previous six projects already answered that. This capstone tests whether the six layers you built *individually* actually compose into something more reliable than the sum of its parts, or whether they're six well-built components sitting next to each other, each blind to the others' failures. The chaos day is where you find out for real, not where you hope the answer is yes.

## Architecture

See [`architecture/`](architecture/) for the diagram in three formats: [`diagram.mmd`](architecture/diagram.mmd), [`diagram.drawio`](architecture/diagram.drawio), [`diagram.png`](architecture/diagram.png) — the full six-layer Aegis, end to end, in one integrated view.

## What Comes After This

This capstone is the end of the guided path, not the end of the discipline. See [`/learning-guide/`](../../learning-guide/) for how to keep extending Aegis, and the main repository README for suggested next directions this project deliberately left out of scope.
