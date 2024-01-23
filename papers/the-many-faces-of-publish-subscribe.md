# The Many Faces of Publish-Subscribe

> This paper introduces the notion of using pub/sub service to provide more flexible asynchronous communication between different components in a system. The key is to use event based interaction to decoupling space, time, and synchronization. 
> *This paper is from 2003 and so it is kinda old*

Three main decoupling:
1. Space: The interacting parties do not need to know each other
2. Time: The interacting parties do not need to be actively participating in the interaction at the same time.
3. Synchronization: Publishers are not blocked while producing events, and subscribers can get asynchronously notified through callbacks while performing some concurrent activity.

Alternatives: Message passing / RPC (future) / notifications (observer) / shared spaces / message queuing.

Pub/Sub variations:
1. Topic-based: a topic can be like a domain name that is expressed in a hierarchy. `/stock/stock_price/company_name`.
2. Content-based: based on contents or a logical constrain on the content.
(topic based is easier to optimize, content based is more expressive)

Implementation issues:
1. Events:
	1. Messages: header with meta data + payload data that contains user-specific information.
	2. Invocations: Directed to a specific type of object and has well-defined semantics.
2. The media:
	1. Centralized architecture (high reliability + consistency / low throughput)
	2. Distributed architecture (high throughput)
	3. Or more intermediate approach, using dedicated servers to execute the complex protocols required for persistence, reliability or high availability. (event dispatcher / message broker)
3. Dissemination:
	1. pointer to point
	2. multicast (hard to make it efficient)
4. QoS:
	1. Persistence: more common in centralized architecture
	2. Priorities: mostly best effort based
	3. Transactions: most pub/sub support transactions since it is built-in for most databases.
5. Reliability:
	1. Centralized architecture use reliable p2p channels to communicate, and keep copied in storage.
	2. Distributed architecture use reliable protocols to propagate messages (e.g. group communication / reliable application-layer multicast)
