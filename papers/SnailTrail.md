# SnailTrail: Generalizing Critical Paths for Online Analysis of Distributed Dataflows

SnailTrial generalizes critical path analysis (CPA) to long-running and streaming computations.

Main contribution: Critical participation
- Computation based on time-based snapshots of execution trace.
- Allowing SnailTrail to work online in real time.

Problem with current approaches:
1. Performance information are less helpful in explaining which components in a complex distributed system need improvement to reduce end-to-end latency.
2. Tools offers detailed traces lack of "big picture" for global performance over time.

Previous approach to critical path analysis:
1. Requires a complete execution trace to exist before analysis can start. (does not work in a streaming system)
2. In a continuous computation, there exist many critical paths, which also change over time.
3. Materialize all critical paths is computationally impossible.

SnailTrail aggregates events based on predefined windows, and truncate the events that goes over the window border:

![Screenshot 2023-11-13 at 19 03 54](https://github.com/zikunw/system-group-research/assets/68682076/61b075b6-2864-4001-836b-12d5cbec25be)

Transient critical path: we cannot define a single critical path in a PAG snapshot since there exist multiple longest paths with the same total weight. All paths starting at $t_s$ and ending at $t_e$ are potentially parts of the evolving global critical path.

Critical Participation: Since all transient paths can potentially be part of the evolving global critical path, an activity that appears on many transient paths is more likely to be critical and should be ranked high. (math in paper)

Algorithm to construct a graph snapshot from the streaming events:

![Screenshot 2023-11-13 at 19 28 56](https://github.com/zikunw/system-group-research/assets/68682076/45c04134-b8a3-4db2-b0fb-251de037f018)

Calculate critical participation:

![Screenshot 2023-11-13 at 19 33 15](https://github.com/zikunw/system-group-research/assets/68682076/02496e84-062b-4954-b021-6eb42488b27c)

CP analysis answer the following questions:
1. Which activity type is on the critical path?
2. Is there data skew?
3. Is there computation skew?
4. Is there communication skew?
