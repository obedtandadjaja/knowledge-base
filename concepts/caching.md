# Caching

- Take advantage of the `locality of reference` principle: recently requested data is likely to be requested again
- Exist at all levels in architecture, but often found at the level nearest to the front end

## Application server cache

- Cache placed on a request layer node
- Problem: when request layer is expanded to many nodes (due to load balancer), this increases cache misses since the cache system is per node basis.
  - Solutions:
    - Global cache
    - Distributed cache
    - IP / URL cache if the cache is user specific or request specific

## Global cache (i.e. Redis)

- A server or file store that is faster than original store, and accessible by all request layer nodes
- Two ways:
  - Cache server handles cache miss
    - Most applications use this way
  - Request nodes handle cache miss
    - Have a large percentage of "hot" data set in the cache
    - An architecture where the files stored in the cache are static and shouldn't be evicted
    - The application logic understands the eviction strategy or hot spots better than the cache
- Slower than in-memory-node cache but the data stored is more accurate
- Can scale independently without affecting the request nodes
- Do not take up request node's memory and so each node will have more memory to do processing

## Distributed cache

- Each request layer node owns part of the cached data
- Entire cache is divided up using a consistent hashing function
- Pros:
  - Cache space can be increased easily by adding more nodes to the request pool
  - Faster response time than global cache
  - Simpler implementation
- Cons:
  - A missing node leads to cache loss
  - Data consistency can be an issue
  
## CDN (Content Distributed Network)

- For sites serving large amounts of static media
- Process:
  - A request node asks the CDN for a media
  - CDN checks if media is locally available
  - CDN queries back-end server for media and cache it locally before serving it to the user
- If system is not large enough for a CDN, alternatively we can do the following:
  - Serve static media from a separate subdomain using lightweight HTTP server (e.g. Nginx)
  - Cutover the DNS from this subdomain to a CDN later

## Cache invalidation
- Keep cache coherent with the source of truth. Invalidate cache when source of truth has changed.

### Write-through cache
- Data is written into the cache and permanent storage at the same time. I/O completion is only confirmed once data has been written to both places
- Pros: 
  - Fast retrieval
  - Complete data consistency
  - Robust to system disruptions
- Cons:
  - Higher latency for write operations
  - If cache is not global then some nodes may have stale data in their cache
  
### Write-around cache
- Data is written to permanent storage, not cache
- Pros:
  - Reduce the cache that is not used
- Cons:
  - Query for recently written data creates a cache miss and higher latency

### Write-back cache
- Data is only written to cache
- Write to the permanent storage is done later on
- Pros:
  - Low latency
  - High throughput for write-intensive applications
- Cons:
  - Risk of data loss in case of system disruptions (however, this can be mitigated by duplicating writes to reduce the likelihood of data loss)
  - May not be used for critical/sensitive data like passwords or financials since data can be lost and also data served in other nodes will be stale

## Preload cache
On a fresh startup of a cluster (e.g. after a new deployment in a blue-green deployment scheme/crash/network failure/etc.) the system may be slow to fill the cache. So you may have a batch job run on startup to fetch some pieces of data from the database and put them in the cache.

## Things to be aware of
- Poor eviction policy: leads to slower response time since we are making that extra call to the cache before getting the data from the database
- Thrashing: Replacing values in the cache without ever using it
- Consistency: Database update but the cache is not updated yet

## Questions
- How much do you want your cache entries to live?
- How big do you need your cache to be? 
- How should elements expire (cache eviction strategy) – least recently used, least frequently used, first-in-first-out?
- What kind of data are you caching? Critical/sensitive info or can we afford eventual consistency?
