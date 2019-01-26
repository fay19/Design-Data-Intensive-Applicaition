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
   -- load parameters
        -- request per second to a web server
        -- ratio of reads to writes in a database
        -- numbers of simultaneoursly active users in a chat room
        -- hit rate on a cache
        -- etc

#### Twitter's fan-out problem: each user follows many people, each user is followed by many people


### Maintainability
Over time, many different people will work on the system (engineering and operations, both maintaining current behavior and adapting the system to new use cases), and they should all be able to work on it productively.
