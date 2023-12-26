# Watermarks in Stream Processing System

Main problem: how does one reason about the completeness of a stream that never ends?

Problems with eventual consistent streaming system:
1. Computing a single correct answer
2. Reasoning about a lack of data, e.g. dip detection
3. Safely and punctually garbage collecting obsolete inputs and intermediate state
4. Surfacing a reliable signal of overall pipeline.

**Watermarks**: quantitative markers associated with a stream that indicate no future events within a stream will have a timestamp earlier than their timestamp.

Formal def: $I$ is the sequence of input elements. The arriving time is $p:I\rightarrow T$ where the event time stamp is $e:I\rightarrow T$. A watermark for $N$ is a function $w:T\rightarrow T$ satisfying the following properties:
1. Monotonicity: $w$ is monotone. It must never "move backwards"
2. Conformance: for all $i \in I$, $w(p(i)) < e(i)$. A conformant watermark must bound event times from below.
3. Liveness: $w(t)$ has no upper bound.

Key uses of completeness:
1. Readiness (having all necessary inputs available for producing some output).
2. Obsolescence (ensuring that input is not forgotten until all outputs dependent upon it have been computed)
3. Health monitoring. 

Other ways to reason about completeness:
1. Order-agnostic processing
2. Ordered processing
3. Timestamp frontiers (Timely Dataflow: a generalization of watermarks that allows multiple dimensions of time to advance independently)
4. Punctuations: A general technique for indicating that no element satisfying a given predicate will appear on a data stream (harder to implement generally and scale).
	1. Hard: every operator needs to implement a complex set of functions for updating and propagating the punctuations through the pipeline.

Advantages of watermark compare to other approach:
1. Allows system to react dynamically to changing input disorder
2. Allow incremental processing to proceed even as the system awaits a signal that inputs are complete.
3. Adaptive to different use cases (specialized input source)
4. Stragglers may often mitigated by relaxing the completeness constrains.
5. Complexity burden falls on the implementation of the framework instead of the user.

Two ways of generating watermark:
1. Generate when a node introduces elements from an external event source.
2. Generate conformant watermarks within the streaming system itself. (for usage such as garbage collection and health monitoring)

Ways for non-conformant watermark: (using different heuristics)
1. Bounded disorder: advance watermark to $t - \Delta$ where $\Delta$ is the timeout.
2. Statistically model: more dynamic but harder.

Watermarks propagation (several common patterns)
1. Stateless node: sending messages with unchanged timestamps.
2. Stateful node: 
	- Watermarks must be held back by applying the timestamp aggregating function to the stored data and the node's watermark.
	- Usually the final aggregate has an event time that is a function of the event times of the messages that comprise it (max or min).

Watermark consumption: Trigger callback functions using watermark timers.
- When using non-conformant watermark, we can deal with late data either ignore it or recompute the results.

Different implementation between Flink and Cloud Dataflow:
- Cloud Dataflow:
	- Out-of-band via an external aggregator
	- Tends to have slightly higher latencies (due to the extra network hop and persistent storage writes for checkpointing watermark progress).
- Flink: 
	- In-band with the data stream
	- Flink latency appears to grow super-linearly due to the increased volume of watermark traffic and a hidden inefficiency in the watermark subsystem.
