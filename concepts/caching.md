# Caching

- Take advantage of the `locality of reference` principle: recently requested data is likely to be requested again
- Exist at all levels in architecture, but often found at the level nearest to the front end
- Poor eviction policy may lead to slower response time since we are making that extra call to the cache before getting the data from the database

## Application server cache

- Cache placed on a request layer node
- Problem: when request layer is expanded to many nodes (due to load balancer), this increases cache misses since the cache system is per node basis.
  - Solutions:
    - Global cache
    - Distributed cache
    - IP / URL cache if the cache is user specific or request specific

## Global cache

- 

## Preload cache
On a fresh startup of a cluster (e.g. after a new deployment in a blue-green deployment scheme/crash/network failure/etc.) the system may be slow to fill the cache. So you may have a batch job run on startup to fetch some pieces of data from the database and put them in the cache.

## Questions
- How much do you want your cache entries to live?
- How big do you need your cache to be? 
- How should elements expire (cache eviction strategy) – least recently used, least frequently used, first-in-first-out?
