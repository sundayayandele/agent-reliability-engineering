# Design for the Crash: A Failure-First Blueprint for Production AI Agents

### Most agent architecture is designed forward, from a feature. The agents that survive production are designed backward, from a failure.

---

## The demo that worked, and the agent that didn't

Here is a pattern that has played out at enough companies, in enough variations, that it barely needs a specific name attached to it anymore.

A team builds an agent. It plans, it calls tools, it answers questions, it takes actions on a live system. In the demo, it's genuinely impressive — reasoning traces that look almost thoughtful, tool calls that chain together cleanly, a UI that streams tokens like it's already a product. Everyone in the room agrees: this is close to shippable.

Three weeks after it ships, something goes quietly wrong. Not a crash — a crash would have been easier. The agent starts producing answers that are subtly, confidently incorrect. It calls a tool with a malformed argument and doesn't notice. It retries the same failing action eleven times before anyone sees a spike anywhere. By the time a human traces the behavior back to its root cause, days have passed, and the honest answer to "why didn't we catch this sooner" is that nothing in the system was built to catch it. The agent was built to *work*. Nothing was built to watch it, question it, or recover when it stopped working.

This is not a story about a bad agent. The reasoning was sound. The demo was real. The gap wasn't in what the agent could do — it was in everything the team never built *around* it.

## The inversion

Most agent tutorials, courses, and framework documentation teach architecture forward: start with a use case, pick a framework, wire up some tools, get it answering questions, ship it. That sequence optimizes for one question — *does it work* — and treats everything else as a later concern, something you'll "add observability for" once you have traction.

The architecture that actually survives contact with production asks a different first question: **when this breaks — and it will — how will we know, and what happens next?** Everything else gets designed backward from that answer. Not because failure is more important than function, but because in a system built on a non-deterministic reasoning engine calling external tools against live data, failure isn't an edge case you handle later. It's the default state you're constantly one bad retrieval, one malformed tool call, or one stale document away from landing in.

Call it failure-first architecture. It isn't a framework — you can build it with LangGraph or Google's Agent Development Kit or Pydantic AI or the Claude Agent SDK, and the specific tools genuinely don't matter as much as the discipline does. It's a claim about *order*: build the parts that catch failure at the same time you build the parts that do the work, not after.

## Six layers, three phases

The clearest way to make this concrete is as a stack, six layers deep, organized into three phases that map almost exactly onto the classic build–operate–improve loop every mature engineering discipline eventually reinvents for itself.

**BUILD** *(learn and test)*

**Layer 1 — Foundation Stack.** The actual agent: the framework orchestrating reasoning and tool calls, the protocol connecting it to the outside world. In 2026 this typically means something like LangGraph for explicit, stateful multi-actor orchestration; Google's Agent Development Kit if you're Gemini/Vertex-native and want a code-first, tree-structured agent hierarchy; Pydantic AI if your priority is type-safe, validated agent logic with minimal ceremony around a single agent or small agent system; or the Claude Agent SDK, which packages the same "deep agent" harness pattern that powers Claude Code itself. Underneath most of these now sits the Model Context Protocol — an open standard, donated to the Linux Foundation's Agentic AI Foundation in December 2025, that by early 2026 had grown to more than ten thousand active servers and on the order of 97 million monthly SDK downloads. MCP's actual contribution isn't magic — it's structure: a consistent way for an agent to discover and call a tool instead of every integration being bespoke glue code, which matters enormously once Layer 6 needs to reason about *which* tool call failed and why.

**Layer 2 — Evaluation.** The layer most teams build *some* version of, and almost always underbuild. Test cases are the floor — a fixed set of inputs with known-good properties. Semantic evals go further, scoring output quality against a rubric rather than checking for one exact right answer, often via embedding-similarity scorers or LLM-as-judge. Behavior evals are the ones teams skip most often and need most: does the agent refuse what it should refuse, stay inside its tool-use boundaries, and avoid taking an action it wasn't authorized to take — tested deliberately, not hoped for. Wire the whole thing into CI so a change that degrades behavior fails a check before it ships, and keep a regression suite that only grows, never shrinks, because every incident Layer 6 ever resolves should leave a permanent trace here.

**OBSERVE** *(trace and ground)*

**Layer 3 — Tracing.** You cannot fix what you cannot see, and an agent's failures overwhelmingly hide in an intermediate step, not the final answer — a tool call with the wrong arguments, a reasoning loop that repeats itself four times before giving up, a retrieval that returned nothing useful. Modern agent observability platforms — Langfuse, LangSmith, and a growing field of specialists like Braintrust for eval-heavy teams — capture every LLM call, every tool invocation, latency and cost per step, and reconstruct the full multi-agent run as one connected trace tree rather than a pile of disconnected logs. The single most useful capability, once you've adopted one, is *run replay*: the ability to re-execute exactly what happened, step by step, against the same inputs, instead of trying to reason about a failure from a log line after the fact.

**Layer 4 — Data Foundation.** Every agent grounded in retrieval is only as reliable as the data underneath it, and this is the layer where "it worked in testing" and "it's still correct eight months later" quietly diverge. Source documents need real version control, not an index built once and never revisited. Metadata needs to travel with every chunk — including, critically, whether the document it came from has since been retired or superseded. A versioned vector store with an atomic cutover between versions means a bad reindex never takes production down mid-update. And freshness checks — an automated answer to "is anything in this index now stale" — catch the single most common, most avoidable failure mode in production RAG: an agent confidently citing a policy, a price, or a fact that stopped being true months ago, because nothing ever told the retrieval layer it had changed.

**RECOVER** *(control and improve)*

**Layer 5 — Orchestration.** Once an agent's job outgrows a single reasoning loop, this layer decides how the pieces work together and — just as importantly — what happens when one piece doesn't. A state graph gives you an explicit, inspectable model of what the system is doing at any point, instead of an implicit one buried in prompt history. Routing sends a task to the right specialist agent or tool rather than making one generalist agent do everything adequately. Handoffs let one agent cleanly pass context to another instead of losing it at the boundary. Human review gates sit in front of anything consequential enough that a wrong autonomous action would be expensive to undo. And fallback paths mean a single failed sub-agent degrades the system instead of taking the whole thing down — the same graceful-degradation instinct that's kept web services resilient for two decades, applied to a system that reasons instead of just responding.

**Layer 6 — Failure Recovery.** The layer that turns "the agent broke" from an incident into a process. Detection means the system notices something is wrong without a human having to stumble onto it first — Layer 3's tracing is what makes this possible at all. Diagnosis means classifying *what kind* of failure this actually was: a malformed tool argument is a different problem from a stale retrieval, which is a different problem from a genuine repeated reasoning loop, and each deserves a different fix, not a blanket retry. Resume means recovering from a checkpoint — picking back up from the last known-good state — rather than discarding all completed work and starting over, which is slower, more expensive, and often loses context that mattered. Add test closes the loop that makes this whole architecture compound instead of just cope: every real failure, once understood, becomes a permanent addition to Layer 2's regression suite, so the exact same failure mode can never silently recur. And redeploy is the final step done deliberately rather than heroically — a controlled release of the fix, not a 2 a.m. hotfix pushed straight to production because everyone's too tired to do it properly.

This six-layer shape isn't arbitrary, and it isn't new in spirit — it's a close cousin of MAPE-K, the decades-old autonomic-computing control loop (Monitor, Analyze, Plan, Execute, over a shared Knowledge base) that self-managing systems have used since long before anyone called them "agents." What's new is applying that same discipline to a system whose core reasoning step is non-deterministic, and whose failure modes — a hallucinated tool argument, a subtly wrong retrieval, a reasoning loop that looks purposeful right up until it isn't — don't look anything like the failure modes a traditional monitoring stack was built to catch.

## Why the order matters

It's tempting to read those six layers as a checklist, buildable in any sequence once you eventually get to all of them. In practice, the order is load-bearing.

Layer 2 has to exist before Layer 6 can close its loop — "add test" is meaningless without a regression suite already there to add to. Layer 3 has to exist before Layer 6 can detect anything — you cannot diagnose a failure you never observed. Layer 4's freshness checks are what make Layer 5's fallback paths trustworthy — a fallback that silently serves stale data is not meaningfully safer than the failure it was meant to catch. Skip a layer, and every layer above it inherits a blind spot it can never fully see past.

This is also, not coincidentally, why so many agent projects plateau exactly where they do. Layer 1 is the fun part, the part every framework's quickstart optimizes for, and it's genuinely gotten easier — Google ADK, LangGraph, and Pydantic AI have all matured fast, and MCP has made tool integration something you configure rather than something you hand-build from scratch. Layer 2 gets built because someone insists on tests before shipping. Layers 3 through 6 get built by teams that have already been paged at 2 a.m. by an agent nobody could explain, and decided once was enough.

The honest pitch of failure-first architecture is that you don't have to wait for that page. You can build the discipline in from layer one, the same way you'd build in structured logging and health checks for any other production service — because that is, in the end, exactly what a production AI agent is: a service, with an unusually creative and occasionally unreliable core component, that deserves the same operational rigor as anything else you'd trust with real users and real consequences.

## Building it

This architecture is more useful built than read about, which is the actual point of the repository this article ships alongside: seven hands-on projects, one per layer plus a capstone, each with its own architecture diagram, each building directly on the project before it — so that by the seventh project, "add test" in Layer 6 genuinely does feed back into a regression suite you built with your own hands in Layer 2, three projects earlier, on the same agent, still running.

Start wherever your own agent currently stops. If it's a Layer 1 prototype, that's the honest, unglamorous, correct place to begin.

---

*This article accompanies the [Agent Reliability Engineering](https://github.com/sundayayandele/agent-reliability-engineering) repository — the full six-layer architecture, seven progressive hands-on projects with diagrams, and a structured learning guide.*
