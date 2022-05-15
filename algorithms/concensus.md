# Concensus algorithms

Concensus algorithms are central to distributed systems and Blockchain. It is the mechanism in which peers reach a common agreement about a present state. A few characteristics of systems that would require a concensus algorithm are as follow:

1. There is no central authority to validate and verify operations
1. Nodes are equal and can die at any time
1. Mandatory participation of all nodes

There are various concensus algorithms most famous are:

1. Raft protocol: Ensures that each node in the cluster agrees upon the same series of state transitions. Leader sends heartbeat messages to followers, afterr 150-300ms of no messages, the followers can elect new leaders. The leader is responsible for managing log replication to the followers. The leader can decide on new entries' placement and establishment of data flow without consulting its followers. Once the leader receives confirmation from the majority of its followers then it will apply the entry to its local state machine. All client requests will go to the leader. In the case that the leader crashes, the new leader will need to consolidate the last known good state and delete additional entries.
1. Paxos protocol: Leaderless quorum-based system. 3 phases: (1) prepare phase - node proposing the value (proposer) contacts all nodes in the cluster (acceptors) and asks them to consider its value. Once a quorum of acceptors return a promise, (2) accept phase begins - the proposer sends out a proposed value, the a quorum of nodes accepts this value then the value is chosen (3) commit phase - the proposer can then commit the chosen value to all nodes in the cluster. Very much time-based, latest timestamp wins.

CockroachDB uses Raft protocol. Cassandra uses Paxos protocol.

In blockchain concensus algorithms are different since they are meant to not trust each other:

1. Proof of Work (PoW): Bitcoin uses this. Used to select a miner for the next block generation. The central idea is to solve a complex mathematical puzzle needing a lot of computational power. The process of verifying the transactions in the block to be added, organizing them in a chronological order and announcing them to the entire network does not take much energy and time. When a miner finally finds the right solution, the node broadcasts it to the whole network at the same time, receing a cryptocurrency prize provided by the protocol. PoW provides security since it makes overtaking the network too resource intensive.
1. Practical Byzantine Fault Tolerance (pBFT): Works efficiently in an asynchronous system. Derived from Byzantine Generals' Problem, it is a safeguard against the system failures by employing collective decision making which aims to reduce the influence of faulty nodes. If we have `3m + 1` correctly working nodes, a concensus can be reached if at most `m` processors are faulty (2/3 must be honest). (1) Client sends request to leader node (2) leader broadcasts to all secondary nodes (3) nodes perform the service requested and send back a reply to client (4) request is successful when the client receives `m + 1` replies from different ndoes in the network.
1. Proof of Stake (PoS): Common alternative to PoS. Ethereum uses this. Instead of investting in expensive hardware to solve a complex math problem, validators put up their coins as stake. Validators will validate blocks by placing a bet on it if they discover a block which they think can be added to the chain. Validators get a reward proportionate to their bets.
1. Proof of Burn (PoB): Validators burn coins by sending them to an address from where theey are irretrivable. Validators will then earn a privilege to mine on the system based on a random selection process. Hence, burning coins here means that validators have a long-term commitment in exchange for their short-term loss. The more coins they burn, the better chance they have at being selected.
1. Proof of Capacity (PoC): Validators are supposed to invest their hard drive space. The more hard drive space validators have, the better are their chances of getting selected.
1. Proof of Elapsed Time (PoET): One of the fairest concensus algorithm. Widely used in permission blockchain networks. All nodes do so by waiting for random amount of time, adding a proof of their wait in the block. The winner is the validator which has least timer value in the proof part.

More algorithms: Proof of Activity, Proof of Weight, Proof of Importance, Leased Proof of Stake, etc.

## Glossary

Avalanche effect: A tiny change to any portion of the original data will result in a totally unrecognizable hash.

51% attack: An attack on a block chain for which such attack by a group of miners controlling more than 50% of the network's mining hash rate or computing power. The attackers would be able to prevent new transactions or halt payments entirely. Reversing transactions are also possible.
