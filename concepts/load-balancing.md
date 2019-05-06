# Load Balancing (LB)
Help scale horizontally across an ever-increasing number of servers. Ensures no single server bears too much demand.

Conducts continuous health checks on servers to ensure they can handle requests. It can remove unhealthy servers from the pool until they are restored. Some can even trigger creation of new server to cope with increased demand.

## Why?
- Improves aplication responsiveness
- Improves application availability

## LB Locations
- Between user and web server
- Between web servers and internal platform layer (application servers, cache servers)
- Between internal layer and database

## Algorithms
Load balancing using round robin or random distribution will avoid any `hot spot` problems, i.e. the load distribution to backend remains fair in all situation.

### Least connection
Directs traffic to the server with fewest active connections. LB monitors the number of open connections for each server.

Useful when there are a large number of persistent connections in the traffic.

### Least response time
Directs traffic to the server with fewest active connections and lowest average response time.

### Least bandwidth
Directs traffic to the server currently serving the least amount of traffic, measured in megabits per second (Mbps).

### Round robin
Rotates servers by directing traffic to the first available server and then moves that server to the bottom of the queue.

Useful when servers are of equal specification and there are not many persistent connections. If there are persistent connections then better use least connection.

### Weighted round robin
Just like round robin but some servers get a larger share of the overall traffic

### IP hash
IP address of the client determines which server receives the request.

Useful so that client connection hits the closest server to their geographic location. Client IP address will always go to the same web server. This is good for `session persistence` where changing servers to serve the same session might cause performance issue or transaction failure.

### URL hash
Much like IP hash, except the hashing is done on the URL of the request.

Useful when load balancing in front of proxy caches, as requests for a given object will always go to just one backend cache.

## Implementation
- Smart clients
- Hardward load balancers
- Software load balancers

## Security

### SSL
SSL traffic is often decrypted at the load balancer so that it saves the web servers from having to spend extra CPU cycles required for decryption (`SSL termination`) -> improves performance. However, this can become a security concern, but the risk is lessened when the load balancer is within the same data center as the web servers. The alternative is `SSL pass-through` where decryption happens in web server.

### DDoS
LB can perform off-loading in the case of DDoS attack where it shifts attack traffic to a public cloud provider. 

Hardware defense such as a perimeter wall can also be used but it is costly and require significant maintenance.
