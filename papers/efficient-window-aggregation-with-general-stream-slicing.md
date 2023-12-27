> This paper introduces the definition of stream slicing in window aggregation, the differences and advantages over other existing methods, and how to implement it by adjusting to different types of workload.

Problem: large computation overlaps caused by sliding windows and multiple concurrent queries lead to redundant computations and inefficiency.

Stream slicing is applicable to all data flow systems which adopt a tuple at a time processing model.
- Key: reuse partial aggregates.

Different window aggregation methods:

1. **Tuple Buffer**: High throughput as long as there are no concurrent windows and there are few or no out of order tuples.
2. **Aggregate Trees**: Store partial aggregates in a tree structure and share them among overlapping windows. e.g. FlatFAT and B-INT.
3. **Buckets**: A bucket-per-window. Each window is represented by an independent bucket. A system assigns tuples to buckets based on event-times.(bottleneck is overlapping concurrent windows)
4. **Stream Slicing**: divides a data stream into non-over-lapping chunks of data. The system computes a partial aggregate for each slice. When windows end, the system computes window aggregates from slices.

Workload characterization:
1. Stream Order: In-order AND out-of-order
2. Aggregation functions:
	1. **Distributive**: `sum`, `min`, `max`.
	2. **Algebraic**: if its partial aggregates can be summarized in an intermediate result of fixed size
	3. **Holistic**: have an unbounded size of partial aggregates.
3. Windowing measure:
	1. **Time-based measure**: can based on either event-time or processing-time.
	2. **Arbitrary advancing measures**: a generalization of event times, like kilometers driven by a car.
	3. **Count-based measures**: based on a tuple counter.
4. Window type:
	1. **Context free (CF)**: one can tell all start and end timestamps of windows without processing any tuples. (sliding and tumbling windows)
	2. **Forward context free (FCF)**: if one can tell all start and end timestamps of windows up to any timestamp $t$, once all tuples up to this timestamp $t$ have been processed. (punctuation-based windows)
	3. **Forward context aware (FCA)**: The remaining window types are forward context aware. Such window types require us to process tuples after a timestamp t in order to know all window start and end timestamps before t. (Multi-Measure windows)

Storing tuples v.s. Partial aggregates:
- In-order streams: For in-order streams, we drop tuples for all context free and forward context free windows but must keep tuples if we process forward context aware windows.
- Out-of-order streams: we keep tuples if at least one of the following is true:
	1. The aggregation function is non-commutative
	2. The window is neither context free nor a session window
	3. The query uses a count-based window measure

Slice Metadata: A lice stores its start timestamp, its end timestamp and the timestamp of the first of the last tuple it contains.

Operations on stream slices:
1. Merge: merging two slices.
2. Split: Splitting a slice.
3. Update: Can involve adding in-order tuples, adding out-of-order tuples, removing tuples, or changing metadata.

Process input tuples:
1. The stream slicer: In an in-order stream, it is sufficient to start slices when windows start. In an out-of-order stream, we also need to start slices when windows end to allow for updating the last slice of windows later on with out-of-order tuples. It does not process out of order tuples but forwards them to slice manager.
2. The slice manager: The Slice Manager is responsible for triggering all split, merge, and update operations on slices.
3. The window manager: The Window Manager computes the final aggregates for windows from slice aggregates.

User-defined windows and aggregations: Each aggregation type consists of three functions:
1. Lift: transforms a tuple to a partial aggregate
2. Combine: Computes the combined aggregate from partial aggregates.
3. Lower: transforms a partial aggregate to a final aggregate.
