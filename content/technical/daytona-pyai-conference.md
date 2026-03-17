+++
title = "Daytona Compute + PyAI Conference"
date = 2026-03-12
draft = false
tags = ["ai", "agents", "infrastructure", "sandboxes", "mcp", "browser-tools", "conferences"]
description = "Sandbox architectures converging, CPUs becoming bottlenecks, and tool discovery evolving. Patterns from March 2026 conferences."
+++

Last week at Daytona Compute and PyAI conferences, the same architectural pattern dominated: agent-in-sandbox. These agents run in full OS environments with forking, checkpointing, and multi-day execution. [Daytona](https://www.daytona.io/) demonstrated 50,000 concurrent sandboxes in 2 minutes 13 seconds. Multiple companies showed agents running for days.

Five patterns from the floor:

- **Sandbox architecture choices**: agent-in-sandbox versus sandbox-as-tool patterns
- **Progressive tool disclosure**: using knowledge graphs to manage complexity, not just for skills but for code understanding
- **Browser vs computer use**: when each approach makes sense
- **CPU bottleneck**: why there's a high spike in CPU demand again
- **Parallel execution**: multiple long-running agents per user

## Sandbox Patterns: Agent-In vs Tool

Two distinct patterns emerged. In [agent-in-sandbox](https://blog.langchain.com/the-two-patterns-by-which-agents-connect-sandboxes/), the agent runs inside a full OS environment with persistent state, installing packages and maintaining processes. The sandbox becomes the workspace. In sandbox-as-tool, the agent operates outside and calls the sandbox for specific stateless execution via APIs from providers like E2B, Modal, Daytona, or Runloop.

```python
# Agent-in-sandbox: stateful, persistent
sandbox = Sandbox.create(os="ubuntu:22.04")
agent = Agent(environment=sandbox)
agent.execute("git clone repo && npm install")
checkpoint = sandbox.fork()  # Save and explore variants

# Sandbox-as-tool: stateless, isolated
agent.call_tool("sandbox.execute", {
    "command": "pytest tests/",
    "environment": "python:3.11"
})
```

The trade-offs are real. Agent-in-sandbox mirrors local development closely with direct filesystem access, but requires API credentials inside the sandbox, creating security vulnerabilities. Container rebuilds slow iteration. Sandbox-as-tool keeps credentials secure outside, enables rapid agent iteration without rebuilds, and supports parallel execution across multiple sandboxes. The cost is network latency on each remote execution call.

A middle-ground approach emerged at PyAI with [Monty](https://github.com/pydantic/monty), a Python interpreter written in Rust for executing model-generated code. Instead of choosing between full OS sandboxes or stateless execution, Monty provides a controlled Python runtime with explicit boundaries to host functions. This gives more flexibility than strict tool-calling while staying more controlled than open-ended sandbox compute. The approach addresses security concerns by limiting execution scope while maintaining enough capability for typical agent code generation tasks.

The key innovation is treating forking as an execution primitive rather than a backup mechanism. Fork the sandbox to explore two implementation approaches in parallel. Test suites fork before each run for clean state without restart overhead. Agents checkpoint before risky operations and roll back instantly if something fails.

The forking uses [copy-on-write](https://en.wikipedia.org/wiki/Copy-on-write) from a base image. Two forked paths share the base state and only pay for divergent modifications. This gives sub-second sandbox creation at scale and changes the economics of parallel exploration.

OS diversity was notable: Windows for desktop testing, Android for mobile workflows, Ubuntu variations for different toolchains. Docker-in-docker support appeared everywhere. The sandbox is becoming a meta-environment hosting whatever stack you need.

Long-running agents dominated the talks. These agents persist for days or weeks, monitoring codebases and running continuous tests. An agent running for 30 days gets optimized very differently than one running for 30 seconds. [Terminal bench](https://github.com/comprehensiveai/terminal-bench) runs evaluation tasks inside sandboxes, using the same infrastructure that hosts production agents.

## Progressive Tool Disclosure

Tool discovery is becoming a real problem. An agent with access to 50+ [MCP](https://modelcontextprotocol.io/) tools faces decision paralysis. Progressive disclosure emerged as the solution. Show simple tools first and expand complexity on demand.

[Lat.md](https://github.com/1st1/lat.md) uses knowledge graphs for cross-cutting concerns. When an agent modifies authentication logic, the knowledge graph shows all 47 affected files across the codebase. The system organizes documentation as interconnected markdown files using wiki-link syntax like `[[file#Section#Subsection]]`. Developers bind code to docs with `// @lat: [[section-id]]` comments in source files. The `lat check` command validates that all links resolve and documented code references exist.

The maintenance cost is real. Knowledge graphs need updates when code structure changes. But for large codebases with complex dependencies, agents that understand cross-cutting impact make better decisions. Lat.md prevents hallucination by providing structured, validated context that can be reliably looked up rather than guessed.

Static tool registration is being replaced by dynamic discovery. MCP servers expose capabilities based on context. Database tools appear when the agent queries user records, not before. This context-driven exposure reduces cognitive load. The pattern is to match tool complexity to task sophistication. Simple tasks get simple tools, complex tasks unlock advanced tooling. The challenge is determining task sophistication automatically.

## Browser vs Computer Use

Browser automation dominates current agent workflows because structured [DOM](https://en.wikipedia.org/wiki/Document_Object_Model) provides a reliable action space with deterministic selectors. Computer use with full OS control kept appearing in demos though. One presenter put it well: "Browser vs computer use is because coding use case is strong. Once vision models click in, it could change the game."

Coding workflows benefit from structure. IDEs and terminals have clear interaction patterns that don't require visual interpretation, so browser automation suffices. Desktop applications, visual design tools, and multi-app workflows require computer use with vision capabilities.

The constraint is reliability. Vision models must interpret visual state correctly, locate UI elements accurately, and understand interaction affordances from visual cues alone. Computer use becomes viable when vision model error rates drop below browser automation brittleness rates.

## The CPU Bottleneck

The most surprising reveal across talks was about CPUs rather than GPUs. After two years of obsessing about GPU shortages for training workloads, agent execution is revealing CPU as the actual constraint.

The primary driver is [reinforcement learning and agentic AI](https://newsletter.semianalysis.com/p/cpus-are-back-the-datacenter-cpu). The RL environment must execute actions generated by the model and calculate rewards, requiring parallel CPU clusters for code compilation, verification, and tool use. Microsoft's Fairwater datacenter for OpenAI exemplifies this shift: a 48MW CPU facility with tens of thousands of CPUs supporting the main GPU cluster for data processing that wouldn't exist without AI workloads.

Agent workloads turn out to be I/O bound rather than compute bound. File system access, network calls, state management, and process coordination dominate execution time. Running 50,000 concurrent sandboxes requires 50,000 isolated CPU environments with independent file systems and network stacks. GPU clusters optimized for dense matrix multiplication don't match this resource profile.

Training infrastructure optimizes for compute density, packing maximum FLOPs into minimum hardware cost. Agent execution infrastructure optimizes for isolation density, packing maximum concurrent environments into minimum infrastructure cost while maintaining isolation guarantees. These are fundamentally different workloads with different economics.

[OpenAI](https://openai.com/) is moving from x86-only to [ARM](https://en.wikipedia.org/wiki/ARM_architecture_family) with Amazon, signaling serious cost pressure. ARM processors provide better performance per watt for I/O-bound workloads. When you're running thousands of concurrent agent sandboxes, power efficiency translates directly to operating cost. Amazon added 3x vCPU capacity in the last year, showing demand is exceeding supply.

The architectural response is chiplet designs with disaggregated I/O. AMD's Venice uses mesh networks within compute chiplets. Intel's Diamond Rapids puts four compute blocks around central memory hubs. Intel increased 2026 capex guidance and shifted wafer allocation from PCs to servers. AMD projects strong double-digit CPU growth in 2026. Frontier AI labs now compete directly with cloud providers for commodity x86 CPU capacity. The CPU shortage appears structural to how agent execution workloads scale.

## Parallel Execution Patterns

The search paradigm is evolving through three stages. First was one user searching manually, then one agent searching on behalf of a user, and now multiple agents per user searching continuously. [Parallel](https://www.parallel.so/) framed it well: we're "not bounded by human count and time awake." Workers spend roughly 30% of their day searching for information. With multiple agents searching continuously, that constraint disappears. You might have five agents running concurrently. One monitors code changes, another runs tests on commits, a third updates documentation, a fourth does security scanning, and a fifth analyzes performance. Each one is long-running with an independent sandbox environment.

This multiplies resource requirements linearly. Five agents per user means five concurrent sandbox environments. Which agent runs when? How do agents share state? What happens when agents conflict? These orchestration questions become the product differentiation layer rather than sandbox implementation details.

[Bauplan](https://www.bauplan.dev/) addresses these state management questions with a transactional pipeline model. Their approach maps software development primitives like branches, commits, and transactions to data workflows. Run the pipeline in an isolated branch, merge on success, keep main untouched on failure. This aligns well with agentic workflows where reproducibility, rollback, and auditability matter. The model treats data operations with the same version control guarantees that git provides for code.

Sandbox forking enables speculative execution at the agent level. An agent exploring two different implementation approaches can fork the sandbox, run both paths independently, evaluate the results, and merge the successful path.

[Recursive language models](https://alexzhang13.github.io/blog/2025/rlm/) push this pattern to a lower layer. Instead of agents calling sub-agents, the model calls another model instance to decompose problems as a tool. The root model receives only the query and accesses context stored in a Python REPL environment, then spawns recursive calls to partition, grep, and summarize data. This shifts decomposition decisions from human-designed workflows to the model itself. Sandboxing and forking become critical here. Each recursive model call needs an isolated execution environment with the REPL context, and forking enables spawning multiple instances without copying the entire context state.

The sandbox-as-evaluation-harness pattern appeared in multiple contexts. Terminal bench running in sandboxes. RL training environments for agent skill development. The agent logic sits outside the sandbox, orchestrating execution and collecting results. This creates clean separation between the agent (the learner) and the environment (the simulation). The same forking mechanism used for parallel exploration can run evaluation suites.

[FastAPI](https://fastapi.tiangolo.com/) is evolving to become AI-native with improved streaming patterns for real-time LLM responses. The framework now supports [Server-Sent Events](https://en.wikipedia.org/wiki/Server-sent_events), JSON Lines, and bytes streaming natively, with pyproject.toml-based setup and VS Code integration. These production-ready patterns make FastAPI feel optimized for modern AI backend infrastructure.

## Maintainer Overhead and Quality Signals

A recurring concern at PyAI was maintainer overhead from AI-generated contributions. Low-signal AI PRs, issues, and reviews are increasing triage load for open source maintainers. The discussions centered on creating reputation management systems similar to credit scores for contributors.

This pressure extends beyond conference attendees. Pete Steinberger of [OpenClaw](https://openclaw.com/) and Daniel Stenberg of [curl](https://curl.se/) have documented the same patterns. Stenberg describes the phenomenon as "death by a thousand slops" and recently ended curl's bug bounty program due to AI-generated noise overwhelming legitimate reports. The cost of triaging automated contributions now exceeds the value they provide.

The proposed solution involves persistent contributor identity with quality scoring. High-quality contributions increase reputation, enabling faster review paths. Low-quality patterns decrease reputation, triggering additional scrutiny or automatic filtering. This shifts the cost from maintainers doing manual triage to systems tracking contribution patterns over time. The challenge is building these reputation systems without creating barriers for legitimate new contributors while filtering automated noise.

## Conclusion

The infrastructure layer is solidifying:

- Agent-in-sandbox architectures with forking primitives are becoming standard
- CPU bottlenecks are replacing GPU concerns as the primary scaling constraint
- Progressive tool disclosure and dynamic discovery are solving the tool complexity problem
- Long-running parallel execution requires orchestration as the key differentiation layer
- Browser automation dominates today while computer use emerges as vision models improve
- The execution foundation appears settled, product decisions now happen at orchestration and interaction layers

## References

- [The Two Patterns by Which Agents Connect to Sandboxes](https://blog.langchain.com/the-two-patterns-by-which-agents-connect-sandboxes/) - LangChain Blog
- [Monty: Python interpreter in Rust](https://github.com/pydantic/monty) - Pydantic
- [lat.md: Knowledge graphs for codebases](https://github.com/1st1/lat.md) - GitHub
- [Bauplan: Git for Data](https://www.bauplan.dev/) - Bauplan
- [FastAPI Documentation](https://fastapi.tiangolo.com/) - FastAPI
- [Death by a thousand slops](https://daniel.haxx.se/blog/2025/01/07/death-by-a-thousand-slops/) - Daniel Stenberg
- [The end of the curl bug bounty](https://daniel.haxx.se/blog/2024/08/26/the-end-of-the-curl-bug-bounty/) - Daniel Stenberg
- [CPUs Are Back: The Datacenter CPU Shortage](https://newsletter.semianalysis.com/p/cpus-are-back-the-datacenter-cpu) - SemiAnalysis
- [Recursive Language Models](https://alexzhang13.github.io/blog/2025/rlm/) - Alex Zhang
