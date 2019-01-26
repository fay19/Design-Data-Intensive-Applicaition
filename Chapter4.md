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
       - protobuf
         - original develoed in Google.
       - Field marked as optional doesnt make a difference to how the field is encoded. A runtime check will fail if a required field is not set
     - Schema evolution
      
    
  
