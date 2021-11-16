# Time Synchronization Algorithms

## Calculating round-trip delay time - Cristian's algorithm

![image](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/NTP-Algorithm.svg/440px-NTP-Algorithm.svg.png)

Round-trip delay is calculated by the following:

(t1 - t0) + (t2 - t3)

- t0 = client timestamp of the request packet transmission
- t1 = server timestamp of the request packat reception
- t2 = server timestamp of the response packet transmission
- t3 = client timestamp of the response packet reception

Through this algorithm we can only know the round-trip delay time but we cannot know for sure what the actual difference is between the two clocks. For this reason, the algorithm is usually used when latency is negligible where the error rate is small enough.

Request 1: Client => Server

```
{ current_timestamp: 1000 }
```

Request 2: Server => Client

```
{ received_timestamp: 2000,
  sent_timestamp: 2002 }
```

Request 3: Client => Server - received on 1050

```
{ round_trip_delay: (1000 - 2000) + (2002 - 1050) = 48, mismatch: 2000 - (48/2) - 1000 = 976 }
```

## Network Time Protocol

A distributed and scalable solution to sync time. Uses an external clock to be used as reference time server and is based on multiple time server arranged in levels. Used as an Internet protocol to synchronize the clocks of computers with some time reference.

Uses a hierarchical, semi-layered system of time sources. Each level of the hierarchy is termed a stratum and is assigned a number starting with 0 for the reference clock at the top. The number represents the distance from the reference clock and is used to prevent cyclical dependencies in the hierarchy. Note that stratum is not an indication of quality or reliability.

![image](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c9/Network_Time_Protocol_servers_and_clients.svg/440px-Network_Time_Protocol_servers_and_clients.svg.png)

- 0: high-percision timekeeping devices. Generate very accurate pulse per second to the connected computer.
- 1: computers whose system time is synchronized to within a few microseconds to the attached stratum 0 device.
- 2: computers synchronized over a newtowrk to stratum 1 servers. Queries several stratum 1 servers for more reliability.
- 3: synchronized to stratum 2 servers. Employs the same algorithms for peering and data sampling as stratum and can themselves act as servers for stratum 4 computers and so on.

## Precision Time Protocol

A centralized, master-slave approach where the master is controlled by GPS receiver. 
