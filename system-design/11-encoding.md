# Encoding

## Why is encoding needed?
When data is stored in-memory, it's maintained in objects, arrays, hashtables, etc. which are optimized for efficient access mostly by the usage of pointers.  
When that data is written to disk, it looks much different - it needs to be a self-contained sequence of bytes since pointers wouldn't make sense to other applications.  

Thus, a translation is needed between these two forms. Transformation from in-memory to the sequence of bytes is encoding/serialization/marshalling, and vice versa is decoding/deserialization/unmarshalling/parsing.  

## Language-specific formats
Many languages offer their own interface to perform this transformation of data. For example, Java has `java.io.serializable`, Python has `pickle`.  
Although they're convenient, they have some drawbacks:  
- Coupled to the programming language, low interoperability
- Performance concerns: They are generally not built for max performance
- Versioning: Versioning is often an afterthought
- Security concerns: In order to decode the object, arbitary classes need to be instantiated which is a source of issues

## Language-agnostic, human readable formats
The most popular examples are JSON, XML and CSV.  
Although useful for various purposes, they have some disadvantages:  
- Ambiguity around numbers. JSON has limitations wrt the size of numbers. For example, JS doesn't parse 64-bit numbers correctly - this is why the [Twitter API sends Tweet IDs twice](https://stackoverflow.com/a/14083040/12415069) - once as string and once as a number.  
- No hard schema: XML schemas are widespread, but CSV/JSON generally doesn't use schemas. It's upto the application's interpretation.
- No support for binary data - this is handled by base64 encoding it and attaching it. However, that's a waste of space.

However, they're good as general-purpose interchange purposes. As long as people agree on the format, it doesn't matter how pretty or efficient the format is - the difficulty of getting multiple organizations to agree on a single format outweighs these concerns.  

## Language-agnostic, binary
These generally require a schema along with the binary data.  
For example, Thrift/Protocol buffers offer code autogeneration for various languages that take care of interpreting the schema.  

### Single schema with tags - Protobuf, Thrift
The schema definition defines the tag (ID) for each field and the binary format contains data with references to the tags.  
Schema:  
```js
message Person {
    required string name    = 1;
    optional int64 roll_no  = 2;
    repeated string hobbies = 3;
}
```

Binary info:  
```
ff tt ll dd dd dd dd
0f 0t ...

ff: Field number
tt: Datatype
ll: Length
dd dd dd dd: Data
```
Note that the binary data doesn't represent `required`, `optional` or `repeated`. That's handled by the application code.  

#### Backward and Forward compatibility
As long as each field has a unique ID, any code can read any data.  
When a new field is added, it can't be `required` and new fields that are added can't be set to `required`.  

ProtoBuf has `repeated` instead of a `List` or `Array` whereas Thrift has a separate `List` datatype.  
This allows ProtoBuf to switch from `optional` to `repeated` easily since old versions will just check it once and stay compatible.  


### Multiple schema without tags - Apache Avro
Avro takes a slightly different approach: it maintains 2 schemas, a reader's schema and a writer's schema.  
The binary data contains no information about datatype, tags etc.  

Writer's schema:  
```js
record Person {
    string name;
    number roll_no;
    array<string> hobbies;
}
```

The binary data looks like this:
```
ll dd dd dd dd
a2
ll dd dd
ll dd dd

ll: Length
dd: Data
a2: Indicates number of items in array
```

#### Backward and Forward compatibility
Now in case the Reader's schema is an older version of the schema like this:  
```js
record Person {
    string name;
    array<string> hobbies;
}
```

The application takes the diff of the reader and writer schemas and maps the fields correctly. In this case, it would ignore the 2nd piece of data, `roll_no`.

### How are the schemas bundled?
For a large file with millions of records, the writer's schema can be bundled at the start of the file.  
In a database with smaller records, individual bindling is inefficient - we can maintain versions of schema separately and mention the version number with records.  

### Advantages of binary over human-readable formats
- Space: Much more compact
- Schema files serve as documentation. Since schemas are always up-to-date, this documentation never diverges from reality.
- Maintaing multiple versions of the schema enables us to verify the forward and backward compatibility of new data.
- Schemas can be used for code generation in statically-typed languages, enabling type checking at compile time.

