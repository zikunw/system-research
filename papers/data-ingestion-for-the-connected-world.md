# Data Ingestion for the Connected World

Implemented and architected a push-based streaming ETL (extract, transform, load) engine.

**Problem**: getting data into traditional ETL processes is a fundamental roadblock to being able to perform timely analytics or make real-time decisions.
- Old data warehouse design is insensitive to the changes in data.
- However, newer applications like IoT seek to provide an accurate model of the real world in order to accommodate real-time decision making.

>  Therefore, for modern applications in which latency matters, ETL should be conceived as a streaming problem.

Applications:
1. **Internet of Things**: GPS, EGG signals in ER, etc.
2. **Streaming TPC-DI**:
	1. TPC-DI is a benchmark created by the TPC team to measure the performance of various enterprise level ingestion solution. 
	2. This benchmark can be reimagined with a streaming use case.

ETL requirements:
- **Data collection**
	- collecting a large number of heterogeneous data sources that may have different schemas. and originate from a variety of sources.
	- should be able to transform traditional ETL into streaming ETL sources.
- **Bulk loading**
	- The overhead cost of inserting data using SQL is significantly higher than inserting data in bulk.
	- We also can suppress the indexing and materialized view generation of the warehouse until some future time.
- **Heterogenous data type**
	- The data ingestion architectures should provide built-in support for dealing with diverse target storage systems.

Streaming requirements:
- **Out-of-order and missing tuples**
	- We can set a maximum timeout value to wait for the potential missing tuple (increase delay)
	- Or we can use predictive technique.
- **Dataflow orderding**
	- Batches must process the batch in the order in which they arrive.
	- The DAG must execute in the expected order for each batch.
- **Exactly-once processing**
	- To prevent loss or duplication of tuples or batches in recovery.

Infrastructure requirements:
- Local storage: 
	- for data buffering or local query to facilitating real-time analytics at the OLAP backend.
	- Performing looks-ups on a local cache would be more efficient than retrieving them from the backend warehouse every time.
- ACID transaction:
	- Incoming tuples are cleaned and transformed in a user-defined manner, and the output is assumed to be consistent and correct.
	- Maintain atomicity (not influence by the batching mechanism)
	- All state must be fully recoverable in the event of a failure.
- Scalability:
	- Most modern OLAP system scale roughly linearly in order to accommodate the increasing size of datasets. (avoid becoming a bottleneck)
- Data freshness and latency.
	- Improve the end-to-end latency from receiving a data item to storing it in a data warehouse.
	- Refreshing the data frequently in the data warehouse will not help unless new data items can be ingested quickly.


Their architecture:
- Data collection: Each source submits new tuples through a messaging queue.
- Streaming ETL: Provides query functionalities (depends on the use cases)
- OLAP backend: 
	- Can be traditional row-store or column-store or an array database
	- Can also be a delta database (not fully materialized)
- Durable migration: ensure there is no data lost in transit.
	- **Push**: data may be periodically pushed from the ingestion storage to the data warehouse.
	- **Pull**: The data warehouse itself can periodically pull fresh data from the data ingestion storage.

Relational ETL implementation:
- Data collection: Kafka
- Streaming ETL: S-Store
- OLAP backend and migration: Postgres and BigDAWG
