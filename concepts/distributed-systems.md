# Distributed Systems

## Key characteristics

### Scalability
Scalable: A system that can continuously evolve to support growing amount of work

- The capability of a system to grow and increased demand
- Horizontal scaling: Adding more servers into the pool of resources
- Vertical scaling: Adding more resource (CPU, RAM, storage, etc.) to an existing server. This aproach comes with downtime and an upper limit

### Reliability
Reliability: Probability that a system will fail in a given period

- Reliable if it keeps delivering service even when one or multiple components fail
- Achieved through redundancy of components and data (remove every single point of failure)

### Availability
Availability: The time a system remains operational to perform its required function in a specific period

- Measured by the percentage of time that a system remains operational under normal conditions
- A reliable system is available
- An available system is not necessarily reliable: a system can be available but not delivering service correctly

### Efficiency
- Latency: response time, the delay to obtain the first piece of data
- Bandwidth: throughput, amount of data delivered in a given time

### Serviceability / Manageability
Easiness to operate and maintain the system. Only need to fix one part of the system at a time instead of a monolith whole

## Key Challenges

### Complexity
Difficult to deploy, maintain, and troubleshoot/debug than their centralized counterparts. However, this complexity is heavily mitigated by new distributed systems software and cluster management systems like Kubernetes.

### Higher initial cost
The deployment cost of a distribution is higher than a single system. Increased processing overhead due to additional computation and exchange of information adds up cost.

### Security Concerns

- Network needs to be secured between the microservices
- Sharing risks (should microservices doubt each other?)
