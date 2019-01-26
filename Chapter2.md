## Data Models and Query Languages

### NoSQL 
  - Not only SQL
  - one drawback of SQL model: an awkward translation layer is often required between the objects in application code and table in databases
    (sometimes called impedance mismatch)
  - Object-relations mapping frawework: ActiveRecord and Hibernate

### Relational Database and Document Database
  - think about represent resume/Linkedin Profile in a SQL model, there is not a good way to represent it in relational database
  - represent resume/Linkedin Profile in a document model often uses tree structures
  - susually document database doesnt representing many-to-one, many-to-many relationship well since joins of tree structure is often weak. This reponsibility oftens falls to application side
  - Relational Database
    - better support for joins, and many-to-one and many-to-many relationships
    - schema-on-write (similar to static/complie-time type checking)
  - Document Database
    - schema flexibility
    - better performance due to locality
      - locality advantage only applies if you need large parts of the documents at the same time
      - database typicall load the entire document, if only access a small portion of it, which can be wasteful on large documents
      - update document is costly, usually entire document needs to be rewritten(except modifications that dont change encoded size of a document can be perform in place)
      - Some relational database also provides locality property
        - google spanner database
        - Oracle "multi-table index cluster tables"
        - "column family" concept in Bigtable data model(used in Cassandra and HBase)
    - for some applications it is closer to data structures used by the application
    - schema-on-read (similar to dynamic/run-time type checking)
    
### Graph-like Data Models
-Vertex
  - a unique ID
  - a set of outgoing edges
  - a set of incoming edges
  - a collection of properties(key-value pairs) 
- Edge
  - a unique ID
  - tail vertex
  - head vertex
  - a label to describe the kind of relationship between the two vertices
  - a collection of properties(key-value pairs)
-cypher: a declaritive query language for property graph database created in Neo4j graph database
  
  ### Triple-store and SPARQL
  - equivalent to property graph
  

  

