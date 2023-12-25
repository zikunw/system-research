# Realtime Data Processing at Facebook

Use multi-system to create streaming system that targets seconds of latency and fast enough for all of the use cases and allows using a persistent message bus for data transport.
- Decoupling data transport from the processing.
![[Screenshot 2023-12-24 at 20.11.12.png]]

System overview:
- Scribe: a persistent, distributed messaging system for collecting, aggregating and delivering high volumes of log data with a few seconds of latency and high throughput.
	- Data is organized by category, each category is a distinct stream of data. 
	- A Scribe bucket is the basic processing unit for stream processing system: applications are parallelized by sending different Scribe buckets to different processes
 - Puma: a stream processing system whose applications are written in a SQL-like language with UDFs (user defined functions) written in Java.
	 - Provides pre-computed query results for simple aggregation queries.
	 - Puma provides filtering and processing of Scribe streams (with a few seconds delay).
	 - Puma is optimized for compiled queries, not for ad-hoc analysis. 
	 - Puma aggregation apps store state in a shared HBase cluster
- Swift: a basic stream processing engine which provides checkpointing functionalities for Scribe. (write client apps in scripting languages like Python)
	- Mostly useful for low throughput, stateless processing.
- Stylus: a low-level stream processing framework written in C++. The basic component is a stream processor.
	- Can be both stateless or stateful.
	- Must handle imperfect ordering in its input streams. (need the writer to identify the event time data)
- Laser: a high query throughput, low latency, kv storage service built on top of RocksDB.
	- Laser can make the output Scribe stream of a Puma or Stylus app available to Facebook products. 
	- Laser can also make the result of a complex Hive query or a Scribe stream available to a Puma or Stylus app.
- Scuba: Fast slice-and-dice analysis data store. Mostly used for trouble-shooting of problems as they happen.
- Hive data warehouse: Facebook's exabyte-scale data warehouse.
	- Most event tables in Hive are partitioned by day.

Conclusion:
1. Multiple system let us "move fast":
	- There is no single stream processing system that is right for all use cases.
	- Writing a simple application lets users deploy something quickly and prove its value first, then invest time to build something more complex and robust.
2. Ease of debugging:
	1.  When working with stream processing systems, it is harder to iterate during development because the data is not stored. 
	2. With persistent Scribe streams, we can replay a stream from a given time period, which makes debugging much easier.
3. Ease of deployment:
	1. Laser and Puma apps are deployed as a service.
	2. Stylus apps are owned by the individual teams who write them.
4. Ease of monitoring and operation:
	1. Important question: is the correct amount of parallelism used?
	2. Alert for "processing lag" (when an app is processing data slower than the rate of input).
5. Streaming v.s. batch processing:
	1. Using a mix of streaming and batch processing can speed up long pipelines by hours
	2. Streaming only system can be authoritative.
