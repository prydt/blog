---
title: "Paper Review: CRAQ"
date: 2024-06-26T14:18:26-05:00
---

Hey everyone, today I'll be reviewing a paper I love: [Object Storage on CRAQ](https://www.usenix.org/legacy/event/usenix09/tech/full_papers/terrace/terrace.pdf). 


## Summary

Remember [chain replication (2004)](https://www.usenix.org/legacy/publications/library/proceedings/osdi04/tech/full_papers/renesse/renesse.pdf)? Well if not, here's the main ideas:
* Chain Replication is a high throughput, strongly consistent object storage architecture.
* Nodes are organized in a linked list fashion.
* The head receives all writes and passing them down the chain until they arrive at the tail. Once a write is at the tail it is *committed*.
* Tail receives and executes all reads.
* Decouples the fault-tolerance layer with a fast datapath. Fault-tolerance is achieved using [ZooKeeper](https://www.usenix.org/legacy/event/atc10/tech/full_papers/Hunt.pdf) to manage chain reconfiguration in the event of node failures. 

An immediately obvious limitation of chain replication is that reads are fully limited by the capacity of the tail node. CRAQ or Chain Replication with Apportioned Queries is a clever solution to this problem.

CRAQ key ideas:
* Every node (except the tail) now can store multiple versions of a key-value pair (object).
* Every object is either marked clean or dirty. Every object is initially marked clean.
    * dirty = potentially not committed yet
    * clean = committed
* Once any node besides the tail receives a write, this version of the object is stored and marked dirty. Once writes reach the tail and are committed, the tail sends back acks through the chain which allow each node to mark the object version as clean (and garbage collect old versions).
* Now clients with read requests can send their requests to any of the nodes in the chain. There are two possibilities here:
    * **[clean read]** if the object requested is marked clean, the node just returns the object.
    * **[dirty read]** if the object's latest version is marked dirty, then the node sends a *version query* to the tail, asking what version is the latest committed. Once the node gets back the current committed object version, it sends the object back. (Any node which is not the tail is guaranteed to have all the relevant versions of an object since there is no way for the tail node to receive a version of an object without it first passing through all of the nodes in the chain.)
* Additionally CRAQ allows for the client to be able to request eventually consistent reads which just allows nodes to return objects marked dirty. (This can satisfy *monotonic read consistency* if the client is always querying the same node.)
* Read throughput scales linearly with the number of nodes in the chain.


## Novelty
CRAQ is a paper which takes a good idea (chain replication) and really elevates it to being great for the very common case of read-mostly workloads. Ideas like storing multiple versions of keys are of course much older (MVCC, etc.) but I do think that this is a novel application of those ideas to the chain replication case.

## Significance
CRAQ is significant for being a very interesting point on the design space of linearizable storage. Compared to Paxos, Raft, ZAB, and Viewstamped Replication, CRAQ has the capacity for much higher write and read throughput. Additionally compared to earlier work they cite, like Cassandra and the Google File System, CRAQ has high throughput but without compromising on consistency. 

## Questions / Future work
* I haven't looked for it yet, but I am interested in reading about schemes which use multiple chains.

* How much can networking support like multicast help chain replication schemes?

* Facebook's [Delta](https://engineering.fb.com/2022/05/04/data-infrastructure/delta/) is a in-production system based on CRAQ with further optimizations.

* Where else is this used in production?


## Further reading
* [IONIA: High-Performance Replication for Modern Disk-based KV Stores](https://www.usenix.org/system/files/fast24-xu.pdf) - A replication scheme optimized for LSM-tree storage and SSDs.
* [CORFU: A Shared Log Design for Flash Clusters](https://www.usenix.org/system/files/conference/nsdi12/nsdi12-final30.pdf) - A fast, strongly consistent shared SSD log.