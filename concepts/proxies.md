# Proxies

A Proxy server is an intermediary piece of hardware / software sitting between client and backend server.

A client connects to the proxy server, requesting a resource, and the proxy server evaluates the request as a way to simplify and control its complexity. Proxies were invented to add structure and encapsulation to distributed systems.

## Types

### Gateway / Tunneling Proxy

Passes unmodified requests and responses. It's sole purpose is to link 2 networks together.

### Forward Proxy

Internet-facing proxy used to retrieve data from a wide range of sources (in most cases anywhere on the Internet)

### Reverse Proxy (i.e. Nginx)

Usually an internal-facing proxy used as a front-end to control and protect access to a server on a private network. Commonly also perform tasks such as load-balancing, authentication, decryption, and caching.

Forwards requests to one or more servers to handle the request. Response from the proxy is returned as if it came directly from the original server, leaving the client with no knowledge of the origin servers.

#### Benefits

- Encryption / SSL acceleration: encryption and decryption is done in one spot and thus removing the need to have SSL Server Certificate for each host. However, this means that all the hosts behind the proxy must share a common DNS name or IP address for SSL connections. SSL acceleration hardware can also be installed in this layer to speed up en/decryption.
- Load balancing
- Serve/cache static content
- Compression
- Filter requests
- Log requests
