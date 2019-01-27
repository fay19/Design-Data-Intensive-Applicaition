# Replication
Keeps a copy of the same data on multiple machines that are connected via a network
- To keep data geographically close to your users - reduce latency
- To allow the system to continue working even if some of its parts have failed - increase availability
- To scale out the number of machines that can serve read queries - increase read throughput

## Leaders and Followers
How to ensure that all the data ends up on all the replicas?

### Leader-based replication(active/passive or master-slave replication)
- write on leader only
- PostgreSQL, MySQL, Oracle Data Guard, SQL server's AlwaysOn Availability Groups, MongoDB, RethinkDB, Espresson, Kafka, RabbitMQ

#### Synchronous V.S. Asynchronous Replication
- sync: leader waits for all followers' reponse before reporting success to user
  - gurantee up-to-date copy on all replicas, if leader fails, data is still available on followers
  - write cannot be processed if any follower doesnt respond
  - in reality, sync of means one of the follower is sync, all other followers are async - semi-sync
- Async: leader sends message to follower but doesnt wait for response
  - write hasnt replicated to followers are lost if leader fails (write is not guranteed to be durable, even if it has been confirmed to the client)
  - leader can continue to process writes even if all of its followers fail
- research like chain replication is Azure are investigation of replication methods that dont lose data but still have good performance and availability

#### Setting Up new Followers
Replace failed nodes or increase number of replicas. How to ensure new followers has an accurate copy of leader's data
- take a consistent snapshot of leader's database at some point of time - if possible, without taking a lock on the entire database
- copy snapshot to follower
- follower connects to leader and requests all the data that happened since snapshot
- caught up(processed the backlog of data changes)

#### Handling Node Outrages

##### Follower Failure
Caught up by reading its log after wake up and request from leader of all data changes after it got disconnected

##### Leader Failure : Failover
- Automatic failover Process
    - Determined that leader has failed - timeout
    - Election - choose a new leader
    - Reconfigure system to use the new leader
- Things can go wrong
  - Asyn replication - conflict after old leader rejoins if there is unreplicated data when it failed
     - this data usually get discarded
  - Discarding writes is especially dangerous if other storage system outside the database need to be cooridnated with database contents
    - For example, in one incident at GitHub [13], an out-of-date MySQL follower was promoted to leader. The database used an autoincrementing counter to assign primary keys to new rows, but because the new leader’s counter lagged behind the old leader’s, it reused some primary keys that were previously assigned by the old leader. These primary keys were also used in a Redis store, so the reuse of primary keys resulted in inconsistency between MySQL and Redis, which caused some private data to be disclosed to the wrong users.
   - a scenario could happen that two node both believe they are the leader (split-brain)
   - what is the right timeout before declare the leader is dead?
   
 #### Implementation of Replication Logs
 
 ##### Statement-based Replication
 ##### Write-Ahead Log(WAL) Shipping
  - a WAL contains details of which bytes were changed in which disk blocks
  - make it very related to storage enginer, if follower has different engine with leader, it is not easy to read
 ##### Logical(row-based) Log Replication
  - inserted row - log contains the values of all columns
  - delete row - log contains enough information to uniquely identify the row was deleted
  - updated row - log contains enought information to uniquely identifu the updated row, and the new values of all columns
  - makes it decoupled from storage engine
##### Trigger-based Replication
- register custom application code that is automatically execuated when a data change


#### Problems with Replication Lag
 
