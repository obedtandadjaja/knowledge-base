# System Design Steps

## Requirements Gathering

### Functional
- What do we expect the system to behave
- Core components / features that must be present

### Non-Functional
- Performance and SLA-related stuff
- How the system should deliver

### Extended
- Analytics?
- API accessible to public?

## Constraints

Need to state assumptions before going into detailed constraints:
- Read-heavy? Write-heavy? Ratio of read and write
- Requests coming from different geographic area for the same resource?

### Estimates

- Traffic: # of requests per day
- Storage: record size * # of requests per day
- Bandwidth: request size * # of requests per day
- Cache Memory: 20% of daily storage

## Database Design

Answer the following questions to help you decide:
- How much storage space?
- Any relationship?
- Write-heavy or Read-heavy?
- ACID? BASE?
- CAP Theorem - consistency or availability?
- NoSQL?
  - Key-value store?
  - Document store?
  - Columnar store?
  
### Schema:
- What are the tables? Identify objects.
- PK? FK?
- Constraints of each field

## System Design and Algorithm

Come up with a solution in your brain and **DRAW OUT** the service and architecture in diagrams. Explain in detail what each service does

Things to consider:
- Concurrency
- Hardware / Connection failure handling
- Security
- Where to put cache?
- Where to put load balancers?

## Cache

Type:
- Global cache?
- Distributed cache?

Eviction Policy:
- LRU?
- FIFO?
- MRU? Most Recently Used
- LFU? Least Frequently Used
- RR? Random Replacement

Invalidation:
- Write-through?
- Write-back?
- Write-around?
