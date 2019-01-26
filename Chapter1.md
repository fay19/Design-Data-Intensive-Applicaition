## Reliable, Scalabler and Maintainable Applications

### Reliability
The system should continue to work correctly (performing the correct function at the desired level of performance) even in the face of adversity (hardware or software faults, and even human error).
- The application performs the function that the user expected.
- It can tolerate the user making mistakes or using the software in unexpected ways.
- Its performance is good enough for the required use case, under the expected load and data volume.
- The system prevents any unauthorized access and abuse.

#### Fault
A fault is usually defined as one component of the system deviating from its spec
#### Failure
A failure is when the system as a whole stops providing the required service to the user
#### Fault-tolerance mechanisms : prevent faults from causing failures.

#### Software Errors (system faults)
- A software bug that causes every instance of an application server to crash when given a particular bad input. For example, consider the leap second on June 30, 2012, that caused many applications to hang simultaneously due to a bug in the Linux kernel.
- A runaway process that uses up some shared resource—CPU time, memory, disk space, or network bandwidth.
- A service that the system depends on that slows down, becomes unresponsive, or starts returning corrupted responses.
- Cascading failures, where a small fault in one component triggers a fault in another component, which in turn triggers further faults.

### Scalability
As the system grows (in data volume, traffic volume, or complexity), there should be reasonable ways of dealing with that growth.

#### Describe current load succinctly
   - load parameters
        - request per second to a web server
        - ratio of reads to writes in a database
        - numbers of simultaneoursly active users in a chat room
        - hit rate on a cache
        - etc

#### Twitter's fan-out problem: each user follows many people, each user is followed by many people
- Posting a tweet simply inserts the new tweet into a global collection of tweets. When a user requests their home timeline, look all the people they follow, find all the tweets for each of those users, and merge them(sorted by time).
- Maintain a cache for each user's home timeline - like a mailbox of tweets for each recipient user. When a user posts a tweet, look up all the people who follows that user, and insert the new tweet into each of their homeline caches.
   -- celebrity tweets problem
- the distribution of followers per user (maybe weighted by how often those users tweet) is a key load parameter


#### Describe Performance
- When you increase a load parameter and keep the system resources (CPU, memory, network bandwidth, etc.) unchanged, how is the performance of your system affected?
- When you increase a load parameter, how much do you need to increase the resources if you want to keep performance unchanged?
- Batch processing - throughput, response time - time between sending a request and receive a response
- latency is different with reponse time, latency is the duration that a request is waiting to be handled
- median(a.k.a percentails) is better than average when measuring service response time
- outliers(95th, 99th, 99.9th) percentiles are also important

#### Approaches for Coping With Load
- high percentiles of reponse time - tail latencies
- SLA(service level agreements) & SLO(service level objectives)
- scaling up(veritical scaling, move to a more powerful machine)
- scaling out(horizontal scaling, distributing the load across multiple smaller machines)
- elastic - automatically add computing resources whne a load increase detected
- no one-size-fits-all scalable architecture, it's highly sepefic to the application


### Maintainability
Over time, many different people will work on the system (engineering and operations, both maintaining current behavior and adapting the system to new use cases), and they should all be able to work on it productively.
- Operability
- Simplicity
- Evolvability
