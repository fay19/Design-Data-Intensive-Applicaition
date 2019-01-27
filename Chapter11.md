# Stream Processing
- Event
    - A small, self-contained, immutable object containing the details of something that happened at some point in time.
- Stream Processing  
    - Producer send data to a datastore, datastore notify consumers when a event happens

## Notification Mech

### Messaging Systems
- Two important question of pub/sub systems
  - What happens if the producer send messages faster than the consumer can process them?
    - system can drop messages
    - buffer message in a queue
    - backpressure(flow control) - block sender until consumer catch up
- What happens if nodes crash or temporarily go offline - are any message lost?

#### Direct Message from Producers to Consumers
- UDP multicast 
- Brokerless messaging libs such as ZeroMQ and nanomsg(TCP/IP multicast)
- StatsD and Brubeck (UDP)
- HTTP or RPC request if consumers exposes a service on the network

#### Message Brokers/Message Queues
Essentially a kind of database that is optimized for handling message streams. It runs as a server, with producers and consumers connecting to it as clients. By centralizing data in the broker, these systems can more easily to tolerate clients that come and go(connect, disconnect, and crash), durablity is moved to broker instead. Consumers are generally async.

##### Message Brokers Compared to Databases
- message brokers delete data after it has been successfully delivered to consumers, whereas database is more permanent store
- most message brokers assume their working set is fairly small since they quickly delete messages
- data selecting - database support indexs for searching, while message brokers support some way of subscribing a subset of topic by some pattern matching(different mechanism, but same functionality)
- database support arbitrary queries and often do not notify clients if data get changed, whereas message brokers do(notifying data change when new events become available)

##### Multiple Consumers
- Load Balancing (shared subscription)
  - each message is deliveredd to one of the consumers(when messages are expensive to process)
- Fan-out
  - Each message is delivered to all of the consumers
  
##### Acknowledgement and Delivery
- A client must explicity tell the broker when it has finished processing a message so that the broker can remove it from the queue
- A combination of load-balancing and redelivery can cause message out of order in consumer side

#### Partitioned Logs
Receiving a message is destructive if the acknowledgement causes it to be deleted from the broker, so you cannot run the same consumer and expect to get the same result, this is quite different from database. Database doesnt delete data until it is explicitly told so. A batching processing job can be runned repeatedly without the risk of damaging input.
The combination of durable storage approach of databases with low-latency message queues are called log-based message brokers.

##### Using Logs for Message Storage
- Producer sends a message by appending to the end of the log, consumers receives messages by reading the log sequentially(essentially like tail -f)
- Apache Kafka, Amazon Kinesis Streams, and Twitter’s DistributedLog are log-based message brokers
- Google Cloud Pub/Sub is architecturally similar but exposes a JMS-style API rather than a log abstraction
- write all messages to disk
- achieve throughput: millions of messages per second by partition
- fault tolerance - replicating messages
- trivially supports fan-out, because multiple consumers can indepently read the log without affecting each other - reading a message does not delete it from the log
- partition the log to scale to high throughtput, a topic can be defined as a group of partitions that all carry messages of the same type. Different partition can be hosted on different machines, making each partition a seperate log that can be read and written independently from other partitions
    -[alt text](https://github.com/fay19/Design-Data-Intensive-Applicaition/blob/master/partition_logs.png)

####Event Sourcing
- immutable event log and mutable state
- enable multiple consumers to derive several views from the same distributed event log
- event log makes it easier to evolve application over time




