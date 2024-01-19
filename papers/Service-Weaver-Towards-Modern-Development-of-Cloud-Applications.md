# Service Weaver - Towards Modern Development of Cloud Applications

> Proposed an distributed system methodology to write applications in logical monolith, and offload the decisions of how to distribute and run applications to an automated runtime, and deploy applications atomically.

Problem with writing applications in microservices:
1. C1: Hurts performance
2. C2: Hurts Correctness
3. C3: Hard to manage
4. C4: Freezes APIs
5. C5 Slows down application development

Proposed programming methodology:
1. Write monolithic applications that are modularized into logically distinct components
2. Leverage a runtime to dynamically and automatically assign logical components to physical processes based on execution characteristics
3. Deploy applications atomically, preventing different versions of an application from interacting.

Part 1 - Programming model:
1. Components: a long-lived, replicated computational agent. (can live based on different process or different machines)
2. API: represents as a Go interface

Part 2 - Runtime:
1. Code generation: Generate different code for RPC call and compile it along the original source code.
2. Application-runtime interaction:
	1. Each caller of the API is a proclet, which is run/start/stop/restart by the runtime. (interact over UNIX pipeline)
	2. The runtime decides how and where proclets should run.
	<img width="738" alt="Screenshot 2024-01-19 at 01 29 16" src="https://github.com/zikunw/system-research/assets/68682076/94b11c57-8c9d-40de-83ea-2c9d09916a10">
3. Atomic rollouts: ensures applications versions are rolled out atomically. (only communicates over the same version) e.g. blue/green deployments.

Advantages:
1. More fine grained call graph (for performance profiling purpose)
2. Improve performance when requests are routed with affinity.
3. Automated testing (decomposed e2e testing to simple unit tests)
4. Stateful rollouts:
	1. Not solved: two versions may both have access to a database.
