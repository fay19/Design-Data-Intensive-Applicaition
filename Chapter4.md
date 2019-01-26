# Encoding and Evolution

### Evolvability
  - We should aim to build systems that make it easy to adapt to change

### Backward compatibility
  - Newer code can read data that was written by older code.

### Forward compatibility 
  - Older code can read data that was written by newer code.


## Formats of Encoding Data
- In-memory data structrues, often are optimized for efficient access and manipulation by the CPU(typically using pointer)
- Write data to a file and send it over the network: encode it as some kind of self-contained sequence of bytes
- translation:
  - from in-memory representations to a byte sequence is called encoding(serialization or marshalling)
  - from byte sequence to in-memory representations is decoding(parsing, deserialization, unmarshalling)
  
- Comman Formats
  - Json
    - not distinguish between int and float, integers larger than 2^53 cannot be exactly represented in float
    - good support of unicode, but no support of binary strings 
    - optional schema support
  - XML
    - verbose, not distinguish between a number and a string that happends to consist of digits(without external schema)
    - good support of unicode, but no support of binary strings
    - optional schema support
  - CSV
    - no schema, application define the meaning of each row and column  
    - escaping is not well supported in a lot of parsers
  - Binary Encoding
    - more compact and faster to parse
    - MessagePack
    - Thrift and Protocol Buffers
      - thrift
         - original developed in FB. Two binary format: BinaryProtocol & CompactProtocol(sort of like message pack, combine more than one piece of information into one byte)
         - has a delicated list datatype, which gives it the advantage to support nested list
      - protobuf
         - original develoed in Google.
         - doesnt have list or array datatype, instead it has repeated groups data type(can repeat zero to more times)
      - Field marked as optional doesnt make a difference to how the field is encoded. A runtime check will fail if a required field is not set
      - Schema evolution to keep foward and backward compatibility
        - field tag is immutable
        - forward compatibility 
          - if a new field is added, when old code reading new encoded data, if it reads some field tage it doesnt recoginize, it simply ignore it and the datatype annotation allows the parser to determine how many bytes it needs to skip
          - cannot remove required field
        - backward compatibility
          - new field cannot be required
          - cannot reuse tag number
    - Avro
      - requires encoders and decoders use exact the same schema, since it doesnt encode field tag information and datatype, decoders need to go throught the field in the order they appear in schema and use the schema to tell you the datatype of each field
      - backward and forward compatibility
        - writer’s schema and the reader’s schema don’t have to be the same - they oonly need to be compatible. when decoding, avro will resolve the difference by looking at writer's schema and reader's schema side by side and translate the data from writer's schema to reader's schema
      - dynamically generate schema
        
       
     
        
      
    
  
