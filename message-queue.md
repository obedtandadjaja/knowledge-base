# Distributed, scalable, durable, highly available Message Queue

A task queue decouples components in a distributed system and allows them to communicate in an asynchronous manner. The two communicating parties can then scale separately. In a complex distributed systems, a task queue is essential.

In this particular system design, we are going to create a producer-consumer messaging system. The delivery model we are going to use is the [Competing Consumers](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/dn568101(v=pandp.10)?redirectedfrom=MSDN) pattern.

The Competing Consumers pattern allows concurrent consumers to process messages received on the same messaging channel, instead of having a single consumer to process messages. These consumers must be coordinated to ensure that each message is only delivered to a single consumer.

Pros:
- Scalability
- Reliability - independent of unresponsive/overloaded consumers
- Guaranteed delivery - at least once
- Resiliency - When consumer fails mid-task, message is not lost

Cons:
- Message ordering
- Poison messages - malformed messages will stay in the queue and be processed by consumers again and again

## Requirements

- Durability, tolerance of hardware failures
- Ability to scale the throughput of each queue up and down easily
- Complete support for the competing-consumers pattern
- Language agnostic

## Constraints

- Choose eventual consistency to allow high availability and durability, with the tradeoff of ordering guarantees. For this reason, we eliminate the need for a consistent metadata storage like Zookeeper, which improves availability.
- Choose not to support partitioned consumer pattern. This simplifies consumer worker management and provisioning.

## Key Design Elements

### Failure recovery and replication

To be truly lossless and available, we must replicate each message across different machines so that messages can be reliably read and written despite hardware failures. 

The fault-tolerance comes from leveraging append-only (log-based) property, which means reads and writes can be performed on different nodes. This allows us to embrace a leaderless architecture with high read and write rates.

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue1.gif?raw=true)

We treat each node as conceptual substreams within a queue that independently support appending messages. Each node's messages are replicated to multiple storage layers and each node will hold a metadata detailing its ID and list of storage hosts.

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue2.gif?raw=true)

The node has a WebSocket connection to all relevant replicas and receives acks in the same connection. Note that the node does not wait for an ack before writing the next message to prevent bottleneck. However, only after all storage hosts ack receipt of the same message does the node ack to the producer.

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue3.png?raw=true)

If a single storage host fails, we don't lose messages because the substream is still readable from other replicas. However, writes will stop and we will create a new node to pick up the writes on separate storage layers. A signaling channel will then notify producers to reconnect and publish into the new extent. This ensures that we do not need to sync multiple replicas and catch them up to speed.

### Scaling writes

The nodes do not share anything with each other. A manager node observes the throughput on each node and as write load to a particular queue increases, the manager node will spin additional nodes for that queue automatically. And as write load decreases, the manager node seals some nodes without replacing them with new ones. In this way, we reduce some overhead (memory, network connections, maintenance, load balancing) required to maintain an open node.

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue4.gif?raw=true)

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue5.png?raw=true)

### Consumption handling

Consumers in the same consumer group receive messages from the same queue, but may receive from one or more nodes. When a consumer receives a message and successfully processes it, the consumer replies to the nodes with an ack. If the node does not get an ack after some configured time, it redelivers the message to retry.

How does the system track of ack-ed and unack-ed messages? In each node, we maintain an ack offset, which is a message sequence number below which all messages have been acked. The node reads messages from storage sequentially, keeping them cached in memory. It keeps track of in-flight messages and updates the ack offset when possible. It also keeps track of timing and nacks to redeliver message to consumers when necessary.

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue6.gif?raw=true)

The system is also configured to redeliver each message a limited number of times. If the redelivery limit is reached, the message is publsihed to a dead letter queue and the message is marked as acked so that the ack offset can advance.

### Storage

Messages are durably stored on disks. On the storage hosts, we can use RocksDB as our storage engine for performance and indexing features, and we use a separate RocksDB instance per node with a shared LRU block cache. Messages are stored in the database with an increasing sequence number as the key, and the message itself as the value. Because the keys are always increasing, RocksDB optimizes its compaction so that we don’t suffer from write amplification. When output host reads messages from an extent, it simply seeks to the ack offset for the consumer group it’s serving, and iterates by the sequence number to read further messages.

We construct the key to contain the delivery time in high order bits, and sequence number in low order bits. Since RocksDB provides a sorted iterator, the keys are iterated in order of delivery time, while the sequence number of the lower bits ensures uniqueness of the keys.

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue7.gif?raw=true)

## System Architecture

![img](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/message-queue8.gif?raw=true)
