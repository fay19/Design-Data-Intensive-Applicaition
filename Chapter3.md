#Storage and Retrieval

## Log Structured Storage Engine

An index is an additional structure that is derived from the primary data. Index introduce overhead of write operation.
#### HashIndex of Append-only log
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
    
 #### SSTables (sorted string table)
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
#### B-trees
 A very different design philosophy. Log-structured indexs break database down into variable-size segments, typicall several megabytes and more in zie, and always write a segment sequentially. By contrast, B-trees break the database down into fixed-size blocks or pages, traditionally 4KB in size(sometimes bigger), and read or write one page at a time.
- Branching factor: number of child pahes in one page. Usually depends on the amount of space required to store the page references and the range boundaries(typically it is several hundreds)
- (A four-level tree of 4 KB pages with a branching factor of 500 can store up to 256 TB.)
- issues: 
  - think overwrite a page on disk as a hardware operation
  - some operation require several pages to be overwritte, for example, split a page because an insert caused it to be overful. If database crash when doing such operation, you end up with corrupted index(e.g., there may be an orphan page)
    - make it resilient - WAL Log(write ahead log as a redo log)
      - an append only file to which every B-tree modifications must be written before it can be applied to the pages of the tree itself
  - careful concurrency control
    - typically done with latches(lightweight locks)
- Optimizations
  - instead of WAL, use a copy-on-write scheme, a modified page is written to a different location, parent page will point to this new location
  - save space in pages by packing more keys into a page, this results a higher branching factor, and thus fewer levels
  - query requires access to multiple pages is expensive since a page can be positioned in anywhere. Leaf pages can be arraned in sequential order, but it is hard to maintain this as tree grows
    - additional pointers can be added to the tree. For example, sibling pointers
  - B-tree variants such as fractal trees [22] borrow some log-structured ideas to reduce disk seeks (and they have nothing to do with fractals).

 ### Comparing B-trees and LSM-Trees
 - rule of thumb: LSM trees are faster for writes, B-trees are faster for reads
 - benchmarks are often inconclusive and sensitive the details of the workload
 
 #### Advantage of LSM-tress
  - write amplifications: a write in database may cause multiple writes to disk over database's lifetime
    - B-trees must write twice(WAL log and the actual page)
    - Log-structured indexes also rewrite data multiple times due to repeated compaction and merging of SSTables
    - this is is particular concern on SSDs, which can only overwrite blocks a limited time before it wear out
    - in write-heavy applications, write amplifications has a direct performace cost: the more that a storage engine writes to the disk, the fewer writes per second it can handle within the available disk bandwidth
  - LSM-trees are typically able to sustain higher write throughput than B-trees.
    - partially because lower write amplifications(case by case)
    - partially sequentially write compact SSTables rather than overwrite to several pages in tree
    - this difference is particularly important on magnetic hard drives, where sequential writes are much faster than random writes
  - LSM-trees can be compressed better, thus produce smaller files on disk than B-trees. Lower storage overhead
#### Downsides of LSM-trees
  - compaction process sometimes can interfere with performance of write&read
  - if compaction is configured carefully and write throughput is high, it can happen that compaction cannot keep up with rate of writes, and eventually run out of disk space, and read are also slow
  
  
### Fuzzy Indexes
 - trie
 
### In-memory Database
  - they are faster not because they dont need to read from disk(a disk-based storage engine may never need to read from diisk it you have enough memory, because OS cache), rather, they can be faster because they avoid the overheads of encoding in-memory data structures in a form that can be written to disk
  - in-memory database can have data models that are difficult to implement with disk-based indexes. For example, Redis offers a database-like interface to various data structures like PQ and sets.
  - LRU anti-cacheing approach of in memory databse to support datasets that larger than available memory
  
  
### Transaction Processing or Analytics

 
 





  


