##Storage and Retrieval

### Log Structured Storage Engine
- Many database internally use a log, which is an append-only data file
  - An index is an additional structure that is derived from the primary data. Index introduce overhead of write operation.
  - HashIndex
    - simple example of log hash index: keep an in-memory hashmap where every key is mapped t a byte offset in the data file
      - essentiallu what Bitcask(the default storage engine in Riak) does
      - Bitcask offers high-performace reads and writes, subject to the requirement that all the keys fits in the available RAM since the hashmap keeps completely in memory
      - suites for workload that doesnt have much distinct keys with a lot of writes(key is url of a video, value is numbers of times a video has been played)
    - what to do when evetually running out of disk?
      - break the log into segments of a certain size by closing a segment file when it reaches a certain size, then compact old segments
    - issues in real implementations
      - file format
        - csv is not a good format, better to use a binary format that first encodes length of a string in bytes, followed by the raw string(without need for escaping)
      - deleting records
        - delete a record needs a special deletion record to the data file(somestimes called a tombstone)
      - crash recovery
        - database restart is painful since index are in memory, Bitcast speeds up recovery by storing a snapshot of each segments' hashmap on disk
      - partially written records
        - crash happens when writing. Bitcast files include checksums, allowing such corrupted parts of the log to be deleted and ignored.
      - concurrency control
        - read by multiple threads but only write by one thread
    - why append only not just updating old value
      - append and merge segments are sequential write operations which are generally faster than random writes, especially on magnetic spinning-disk hard drives
      - concurrency and crash recovery are much simpler if segment files are append-only or immutatble
      - Merging old segments avoids the problem of data files getting fragmented over time? 
    - limitations
      - size of index map must fit in memory. On disk hashmap hardly perform well and has collisions require fiddly logic
  
      
     
      
   


### Page Structured Storage Engine
