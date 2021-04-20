# Caching
A way to ensure low latency and high throughput by using a high-speed data storage layer which stores a subset of data, typically transient in nature, so that future requests for that data are served up faster than is possible by accessing the data’s primary storage location.  
Caching allows you to efficiently reuse previously retrieved or computed data.  

A cache's primary purpose is to increase data retrieval performance by reducing the need to access the underlying slower storage layer, and the tradeoff is usually capacity for speed.  

Caching can be implemented at any layer: Browser cache, [HTTP caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching), Database caching, DNS caching, etc  

> Cache Invalidation: The process where data is purged from the cache and is replaced by newly updated data after the TTL of the old data is reached.  
> Usually handled by TTLs.  

Cache Hit: Data requested by the client is present in the cache, and is served.  
Cache Miss: Data requested by the client is not in the cache, and needs to hit the DB to be served.  

<img src="https://user-images.githubusercontent.com/45961072/115349362-620a2180-a1d1-11eb-90a2-6a629c07d9bb.png" height="300px" width="600px"></img>  

## Caching strategies
### Lazy loading (Cache aside)
> Client query -> Cache -> hasData? (return : query DB, update cache, return)   

Most common; when the user sends a request for particular data, the system first looks for it in the cache.  
If present, then it is simply returned from it.  
If not, the data is fetched from the database, the cache is updated and is returned to the user.

Works best with read-heavy workloads.

**Advantages**:
- Only requested data is cached. Avoids filling up the cache with data that isn't requested.  
- Node failures aren't fatal. When a node fails and is replaced by a new empty node, the application continues to function, though with increased latency (since it hits the DB for the first time). 

**Disadvantages**:
- There is a cache miss penalty. 3 trips need to be taken: 
  - Initial request to the cache
  - Query to DB
  - Writing data to cache
- Stale data: Data in the cache will not have the latest updates in the DB, and can return outdated data.  

### Write-through
> Client write -> Cache -> update DB -> update Cache   

Each piece of information written to the database goes through the cache. Before the data is written to the DB, the cache is updated with it.  
This maintains high consistency between the cache and the database but adds a little latency during the write operations as data is to be updated in the cache additionally.  

Works best with write-heavy workloads, such as MMORPGs.  

**Advantages**:
- Data is never stale  
- Data retrieval is faster - there's no cache miss penalty

**Disadvantages**:
- Adds latency due to the Write penalty: Every write needs to be written both to the DB and cache
- Node failures result in missing data. Lazy loading needs to be implemented in that case.
- Cache churn: Most data is never read, which is a waste of resources. Can leverage TTL.

> End users are generally more tolerant of latency when updating data than when retrieving data, so this works well.

### Write-Behind Caching
> Client write -> update Cache ...(aggregates a batch of writes)... -> update DB  
 
The data is directly written to the cache instead of the database.  
The cache after some delay writes data to the database, reducing overall DB writes.  
Needs to handle complex cases, such as transaction failures/out of order updates, etc.

**Advantages**:
- Drastically reduced database load: Here, the DB hits for both reads and writes are reduced.
- Improves performance, because the client does not have to wait for data to be written to the underlying data source.  
- Insulated from DB failures to an extent; write-to-DB failures can be requeued.

**Disadvantages**:
- If caching node goes down, some data might be lost
- This method implies that the database transactions must never fail; if this cannot be guaranteed, then rollbacks must be accommodated.  
- This may re-order database updates, and so referential integrity constraints (FK relations) must allow out-of-order updates.

---

Further read: [Oracle: Selecting a caching strategy](https://docs.oracle.com/cd/E15357_01/coh.360/e15723/cache_rtwtwbra.htm#COHDG200)
