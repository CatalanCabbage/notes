# Databases
A component used to persist data.

Classification of data:
- **Structured data**: No need to run preparation logic before processing. Eg. Stored in an SQL DB
- **Unstructured data**: No definite structure. Heterogeneous; contains streams, text, blobs, media, etc. Needs preparation.
- **Semi-structured data**: Mix of structured and unstructured data. Usually stored in XML, JSON, etc.

## Relational (SQL) Databases
Modelled based on the normalized relationship between data. ACID compliant.  
Eg. PostgreSQL, MySQL, Amazon Aurora, MariaDB, Google Cloud SQL, MSSQL

Relationships:
- One to one: ID -> Name
- One to many: City -> Many ZIP Codes
- Many to Many: Book -> Many categories, Category -> Many books

Normalization:  
A unique entity occurs only in one place/table, in its simplest and atomic form and is not spread throughout the database.  
This helps uphold Data Consistency.

ACID Transactions:  
- If a transaction occurs, it will be executed as a single operation without affecting other transactions.
- There are only 2 states: Before the transaction and after the transaction. Both states are consistent and durable. No middle state.  

A - Atomicity: A transaction either succeeds, or nothing happens. All or nothing.  
C - Consistency: No integrity constraints are broken.  
I - Isolation: Multiple transactions can happen concurrently without one affecting the other.  
D - Durability: The effects (state) of a completed transaction are never lost.  

**Advantages:**  
- Optimal when relational data needs to be stored.
- Transactions and ACID compliant 

**Disadvantages:**
- Needs to be sharded or replicated to scale


## NoSQL Databases
No...SQL. More like JSON-based databases.  
Built for high frequency reads and writes.  
Eventually consistent.  
Eg. MongoDB, Redis, Cassandra, Neo4J, Google Cloud datastore

**Advantages:**
- Fast reads/writes
- Can scale easily - can add new server nodes on the fly
- Designed to run intelligently on clusters
- No schema, flexible

**Disadvantages:**
- Inconsistent (because it's not normalized). Only eventually consistent
- No ACID Transactions compliance
- No schema, can become dev-unfriendly

> War story: YouTube - Often we see a video with 10 views and 15 likes. How is this even possible?   
> Because it's eventually consistent. The views is inconsistent and takes a while to be updated.  
 

## SQL vs NoSQL
No definite answers, depends on use-cases.  
Performance: Depends on design; SQL with a lot of joins will inevitably take more time.  
SQL with no relationships and joins == NoSQL.  

[Why Quora uses SQL and not NoSQL](https://www.quora.com/Why-does-Quora-use-MySQL-as-the-data-store-instead-of-NoSQLs-such-as-Cassandra-MongoDB-or-CouchDB-Are-they-doing-any-JOINs-over-MySQL-Are-there-plans-to-switch-to-another-DB)

## Polyglot Persistence
Using several different persistence technologies to fulfil different persistence requirements in an application.  

Eg: An FB-esque application:
|Feature|DB|Example|
|:---:|:---:|:---:|
|Friends, details|Relational DB|PostgreSQL|
|Caching, sessions|Key-Value store|Redis, Memcache|
|Run analytics|Wide Column DB|Cassandra, HBase|
|Recommendations|Graph DB|Neo4j|
|Search|Document-oriented data store|Elasticsearch|
