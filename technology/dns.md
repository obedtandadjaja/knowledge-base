# DNS

## How DNS works
DNS is a global system for translating IP addresses to human-readable domain names. When a user tried to access a web address like "example.com", their web browser or application performs a **DNS Query** against a DNS server, supplying the hostname. The DNS server takes the hostname and resolves it inot a numeric IP address, which the web browser can connect to.

A component called a **DNS Resolver** is responsible for checking if the hostname is available in local cache, and if not, contacts a series of DNS Name Servers, until eventually it receives the IP of the serve the user is trying to reach, and returns it to the browser of application. This usually takes less than a second.

## 3 DNS Query types

### Recursive Query
In a recursive query, the DNS client provides a hostname and the DNS Resolver **must** return with an answer (either relevant resource record or an error message). The resolver recursively queries from the DNS Root Server until it finds the Authoritative Name Server that holds the IP address and other information about the hostname.

### Iterative Query
In an iterative query, the DNS client provides a hostname and the DNS Resolver returns the best answer it can. If the DNS resolver has the relevant DNS records in its cache, it returns them. If not, it refers the DNS client to the Root Server, or another Authoritative Name Server which is nearest to the required DNS zone. The DNS client must then repeat the query directly against the DNS server it was referred to.

### Non-recursive Query
A non-recursive query is a query in which the DNS Resolver already knows the answer. It either immediately returns a DNS record because it already stores it in local cache, or queries a DNS Name Server which is authoritative for the record, meaning it definitely holds the correct IP for that hostname.

## 3 Types of DNS Servers

### DNS Resolver
A DNS Resolver (recursive resolver) is designed to receive DNS queries and is responsible for tracking the IP address for that hostname

### DNS Root Server
The root server is the first step in the journey from hostname to IP address. The DNS Root Server extracts the Top Level Domain (TLD) from the user's query (e.g. .com from www.example.com and provides detail for .com TLD Name Server).

There are 13 root servers worldwide, indicated by the letters A through M, operated by organizations.

### Authoritative DNS Server
Higher level servers in the DNS hierarchy define which DNS server is the “authoritative” name server for a specific hostname, meaning that it holds the up-to-date information for that hostname.

The Authoritative Name Server is the last stop in the name server query—it takes the hostname and returns the correct IP address to the DNS Resolver (or if it cannot find the domain, returns the message NXDOMAIN).

## 10 DNS Record Types

### Address Mapping record (A Record)
also known as a DNS host record, stores a hostname and its corresponding IPv4 address.

### IP Version 6 Address record (AAAA Record)
stores a hostname and its corresponding IPv6 address.

### Canonical Name record (CNAME Record)
can be used to alias a hostname to another hostname. When a DNS client requests a record that contains a CNAME, which points to another hostname, the DNS resolution process is repeated with the new hostname.

### Mail exchanger record (MX Record)
specifies an SMTP email server for the domain, used to route outgoing emails to an email server.

### Name Server records (NS Record)
specifies that a DNS Zone, such as “example.com” is delegated to a specific Authoritative Name Server, and provides the address of the name server.

### Reverse-lookup Pointer records (PTR Record)
allows a DNS resolver to provide an IP address and receive a hostname (reverse DNS lookup).

### Certificate record (CERT Record)
stores encryption certificates—PKIX, SPKI, PGP, and so on.

### Service Location (SRV Record)
a service location record, like MX but for other communication protocols.

### Text Record (TXT Record)
typically carries machine-readable data such as opportunistic encryption, sender policy framework, DKIM, DMARC, etc.

### Start of Authority (SOA Record)
this record appears at the beginning of a DNS zone file, and indicates the Authoritative Name Server for the current DNS zone, contact details for the domain administrator, domain serial number, and information on how frequently DNS information for this zone should be refreshed.

## DNS Can Do Much More
Advanced DNS solutions can help with the following:

- Global server load balancing (GSLB) - fast routing of connections between globally distributed data centers
- Multi CDN - routing users to the CDN that will provide the best experience
- Geographical routing - identifying the physical location of each user and ensuring they are routed to the nearest possible resource
- Data center and cloud migration - moving traffic in a contrlled manner from on-premise resources to cloud resources
- Internet traffic management - reducing network congestion and ensuring traffic flows to the appropriate resource in an optimal manner
