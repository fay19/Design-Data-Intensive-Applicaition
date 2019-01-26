##Storage and Retrieval

### Log Structured Storage Engine
#### Append-only log
An index is an additional structure that is derived from the primary data. Index introduce overhead of write operation.
HashIndex
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
 SSTables (sorted string table)
    - sort key-value pairs by key
    - each key only appears once within each merged segment file
    - advantages over hash indexes:
      - merge segments is simple and efficient(merge sort)
      - no need to keep an index of all keys in memory(an in memory index still needed, but can be sparse)
      - group records and compress it before writing it to disk
    - constructing and maintaining:
      - red-black trees or AVL trees
      - when a write comes, write to into memtable
      - when memtbale gets bigger than some threshold(typicall a few megabytes) - write it out to disk as and SSTbale file
      - a read request: find the memtable first, then on-disk segment,. then the next-older segment, etc
      - merge and compact segment files in the background from time to time
      - limitations:  
        - when crash, most recent writes (in memory not on disk yet) are lost
        - to avoid this, keep a separate log on disk tp which write is immediately appened(like above append only log), this log can be discarded when this memtable is written out to SSTable
        - this is used in LevelDB and RocksDB
    - LSM-Tree(Log-Structured Merge-Tree)
      - can be slow ehen looking up keys do not exist in the database
        - look memtable and segments files all the back to the oldest
          - an additional bloom-filter and help to avoid this
    - when to compact:  
      - size-tiered compaction: levelDB and RocksDB, Cassandra
      - leveled compaction: HBase, Cassandra


        


  
      
     
      
   


### Page Structured Storage Engine
