# Top K Heavy Hitters

This question applies to the following:
- Top N videos watched
- Top N exceptions
- Top N most visited
- Top N frequent search queries

## Problems and complexity

Dealing with massive data streams in real-time while having to come up with an accurate and scalable solution. The traditional method to keep an individual counter for each host is very resource-consuming.

## Solution

To provide a near realtime solution, we need to use `lambda architecture` principles in our solution.

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-12-27%20at%202.52.23%20PM.png?raw=true)

Fast path provides a rough estimate of what the solution will be. Slow path provides accurate result at the cost of time (hourly aggregation).

### Fast path

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-12-27%20at%202.53.49%20PM.png?raw=true)

Fast path is pretty simple in that it only needs a set of processor (to reduce bottleneck) incrementing each individual count-min sketch and then aggregating the data together in the Storage.

### Slow path

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-12-27%20at%202.53.39%20PM.png?raw=true)

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-12-27%20at%202.54.32%20PM.png?raw=true)

Slow path is much more complex. It has multiple layers:

- Distributed messaging system: a kafka stream that will stream each individual event from the api-gateway
- Data partitioner: routes and partitions events to another distributed messaging system with a particular topic/channel
- Partition processor: aggregates events every 5 minutes to reduce the amount of data over the network and then saving the data to an HDFS
- MapReduce frequency count: aggregates events for the entire hour
- MapReduce top k: calculates the top k heavy hitter and send it to the Storage

## Data retrieval

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-12-27%20at%202.54.55%20PM.png?raw=true)

Fast path: 1 minute aggregation

Slow path: 1 hour aggregation

Since there is a pretty big aggregation gap between fast and slow path, we should use a combination of both to come up with an acceptable guarantee. On retrieval, aggregate the count of multiple time periods, and recalculate the top k exeption.
