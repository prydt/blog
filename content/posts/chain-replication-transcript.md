---
title: "Chain Replication Video Transcript"
date: 2024-06-18T13:42:01-05:00
---

I've recently released a video explaining chain replication. You can see it here: https://www.youtube.com/watch?v=0ZPNOIMyVCw

Below is my rough draft script for the video. Enjoy!

## [TITLE SLIDE] 
Hi everyone, my name is Pry and I’ll be explaining Chain Replication.

## [PAPER INFO SLIDE] 
The original paper is by Renesse & Schneider presented at OSDI 2004 so it's exactly 20 years old now.

## [MOTIVATION SLIDE]
First, let me motivate the problem that chain replication is trying to solve.

We want a storage system which is both performant and fault tolerant, meaning we can deal with at least an amount of node failures.

What do I mean by storage system? Well it's best to imagine it as a big hashtable which we can read from and write to. Our operations will be simple hashtable reads and writes but really this generalizes to any sort of data structure with read functions and write functions.

A single server node is often performant enough for our needs but what about fault tolerance? A single machine can’t be fault tolerance because by definition it is a single point of failure. So we need some amount of redundancy or replication.

Failure model: fail-stop.


Chain replication is a replication scheme which gives us performance (high throughput) and also a high degree of fault-tolerance. So what does chain replication look like?

## [CHAIN REP SLIDE] 
We arrange our storage nodes in a linked list formation. The first node being the head, the last being the tail. All writes get sent to the head and are then passed down the chain. Once the write reaches the tail, it is “fully committed” and the tail can send an acknowledgement (ack) to the client.

All reads are sent to the tail where they can be immediately executed and returned to the clients.

This is a pretty simple set up… almost too simple… What happens when we break the chain? [FLEETWOOD MAC joke]





## [enter ZOOKEEPER, SEXIEST DISTRIBUTED SYSTEM MAN]

Chain replication relies on a configuration manager which it assumes never goes down. [PAPER SCREENSHOT “we assume the master is a single process that never fails”]. This is obviously unrealistic. But its only unrealistic if we assume that a single machine never fails. It is a lot more reasonable to assume that out of many machines, most are always up. So we can use a cluster of machines using something like Paxos or Raft for consensus to be the configuration manager for our chain! I’m representing it as ZooKeeper right now because like half the papers I read just end up punting several problems off to ZK including this one.




## [FAILURE RECOVERY SLIDE] 
Well there are 3 cases where nodes in our chain can fail: the head, inner node, or tail can fail, all of which we have to handle slightly differently. 


[HEAD RECOVERY SLIDE] If the head fails, then we make the next node in the line the head. Writes are temporarily not able to be accepted but all reads go through without any disruption.

[TAIL RECOVERY SLIDE] If the tail fails, then we make the prior node in the chain the tail. While recovery is happening, new writes can be accepted but cannot complete until the new tail is functional. Additionally, no reads can happen til the tail is active.

[INNER NODE RECOVERY SLIDE] If an inner node fails, the case is slightly more complicated. 

Every server (except the tail) actually needs to keep track of which writes are currently “in flight.” These are all the writes which the node has received but has not heard the tail ack. This means that the tail also sends acks back through the chain. And once a node heards a respective ack, it deletes the write from its in-flight list.

If the node is an inner node, then there must be both a successor and predecessor nodes. The configuration manager first notifies the successor node to prepare for a state transfer, and then the predecessor node is notified. The predecessor node sends all of the in-flight writes to the successor node and once this is complete, the chain can go back to regular function.

Just to recap, the main problem that can arise when an inner node fails is that it receives a certain write request but it fails before it is able to forward the write requests further along the chain. The in-flight list keeps track of exactly the information needed to recover from this sort of failure.



Alright so how good is chain replication?

## [METRICS SLIDE] 
When measuring distributed systems, there are several different metrics and tradeoffs to consider:
* Consistency
* Availability
* Latency
* Throughput


Consistency - what are the guarantees that when I read something, it is the most up to date version. Chain replication has the strongest consistency that we look for in a distributed system – linearizability. In short, a hashtable implemented with chain replication should behave just like it is running on a single machine with regards to consistency. I’ll leave the full formal definition for another video.

Availability - I take availability as a sort of measure of fault-tolerance. If I have N nodes, how many node failures can I tolerate? In the chain replication case, it's clear that as long as there is one node left, we can handle reads and writes and are thus available. This means we can tolerate N - 1 failures! Of course, if you include the configuration manager, it will probably be using Paxos or some other consensus algorithm which means you will also need a majority of those nodes to be up for your chain to be able to reconfigure and for configuration info to be given to your clients.


Latency - When compared to other distributed algorithms like primary-backup scheme or Paxos consensus, chain replication can have higher latency since it has to go through more network hops.

Throughput - throughput is the amount of requests fulfilled in a given amount of time. While maybe somewhat confusing, a request can have a long latency while the overall system can have a high throughput. This is the case for chain replication. While a single request, start to finish may take a long amount of time (relatively), if we are able to process many many requests all at the same time, throughput can be extremely high. A good way to think about this is a conveyor belt. While one might be able to assemble a machine in a shorter amount of total time for one machine, a conveyor belt has a much much higher throughput which is a measure of how quickly many requests can finish.

[next video? Thank you!] 

