Noria: dynamic, partially-stateful data-flow for high-performance web applications
Main Author: Jon Gjengset

Introduces an implementation of partially-stateful data-flow in web application, which provides a SQL-like relational schema for parameterized queries.

Problem with existing approaches:
1. **Caching in separate key value storage:** Caching invalidation increase the application's complexity with each updates to the data.
2. **Windowed state:** Cannot perform fine-grained incremental updates to states. 

The partially-stateful data-flow addresses both of the problem above. It is able to perform fine grained queries without iterating over all the data.

Key ideas:
1. Operators like 'join' performs up-queries instead of maintain the state all the time.
2. Eviction notice to state that are no longer used.

Limitations:
1. Only eventual consistency (which is common in read heavy web applications)
2. Eviction from partial state is randomized
3. Inefficient for sharded queries that require shuffles in the data flow (A total order is required to avoid monitoring over the entire system)
4. Lacks support for some SQL keywords.

