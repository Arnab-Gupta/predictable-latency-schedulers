
## QJUMP

(2015)
1. QJUMP relies on information about application performance requirements, related to latency, rate and packet size, at network initialization time, using them to compute the maximum latency. 

1. It ensures a maximum latency of $2nP/R + \epsilon$, which is the **network epoch**, where $n$ is the number of hosts, $P$ is the max packet size (in bits), $R$ is the rate of the slowest link (in bits/sec) and $\epsilon$ is the cumulative processing delay introduced by switch hops.

1. The **idea** is to place a low, finite bound on queueing to fully control network interference.

1. <a name="guar"> It computes latency guarantees by ensuring that each flow has at least one packet transitting the network at any given time. </a> This results in our second drawback.

1. QJUMP utilizes a concept of epochs, with a mesosynchronous network (same frequency but possibly phase-shifted) to give the following key property: **rate-limiting all hosts so that they only issue one packet every network epoch, causing no packet to take more than one network epoch to be delivered in the worst-case**.

1. An application is assigned a latency sensitivity level. Packets from higher levels are rate-limited in the end host, but once allowed into the network can _jump the queue_ over packets from lower levels.
    - A **throughput factor** ($f$) is introduced, which quantifies the latency variance vs throughput tradeoff. Multiple values of $f$ are used so that different applications can benefit from this tradeoff.
    - The network is then partitioned such that latency-sensitive applications can "jump the queue" over traffic from throughput intensive applications. Higher priorities receive smaller values of $f$.
    - Therefore, QJUMP users must choose between low latency variance at low throughput (high priority) and high latency variance at high throughput (low priority).


### Features:
1. Resolves network interference for latency sensitive applications without sacificing utilization for throughput-intensive applications.
1. Offers bounded latency to applications requiring low-rate, latency-sensitive messaging.


### Drawback(s):
1. It only provides a latency guaratee for very low bandwidth traffic, while higher bandwidth traffic can incur variable message latency.
1. To ensure [this (Pt. 4)](#guar), QJUMP prevents applications from sending bursts of data, a common requirement.

## Silo
(2015)

1. Silo leverages the tight coupling between bandwidth and delay: controlling tenant bandwidth leads to determinstic bounds on network queuing delay.

1. By using a novel hypervisor-based policing mechanism, it achieves packet pacing at sub-microsecond granularity, ensuring tenants do not exceed their allowances.

1. Silo provides latency guarantees by leveraging admission control and relying on deterministic network calculus (DNC).

1. A key insight from the paper is: end-to-end packet delay can be bounded throught fine-grained rate control at end hosts.

1. Makes use of "void" packets for _Paced IO Batching_ that are forwarded by NIC but dropped by the first hop switch, simulating a spaced out flow of actual data packets. Rate-limiting the senders in a network ensures a deterministic upper bound for network queuing.e
1. Silo also encompasses a **VM placement manager** along with a **hypervisor-based packet pacer**.


### Provides -

1. Guaranteed bandwidth
1. Guaranteed packet delay - through fine-grained rate control at end hosts.
1. Guaranteed burst allowance
1. And therefore, **guaranteed packet latency**.


### Drawback(s):
1. Giving delay and bandwidth guarantees to tenants can result in reduction of network utilization.
1. There are fewer accepted tenants compared to giving only bandwidth guarantees.




## Chameleon
(2020)

1. Chameleon employs source routing on the queue-level topology, a network abstraction that accounts for the current states of the network queues, and hence, the different delays of different paths.
1. This builds on the concept of Silo in terms of _resource allocation_, _access control_ and _resource reservation_.
1. The authors have introduced priority queuing to _increase the delay diversity_, offering different service levels.
1. Due to priority queues, the path finding problem changes to selecting the physical path as well as the priority queue, introducing a "queue-level" topology. Queues reveal useful information about the current demand and network state.
1. **Reconfiguration**: At first, the routing procedure tries to find a path for the flow request using a least-delay search. If it fails, the procedure tries to reroute already embedded flows to make space for the new one.
        
    - It selects all flows traversing at least one edge of any of the equal-length shortest paths in the physical topology from the source server to the destination server of the new flow to embed. 
    - It sorts the flows according to the number of shared physical links with the shortest paths, and this list is truncated to limit the maximum number of re-routing retries, which mitigates the [runtime increase caused by re-reouting](#runt).
    - Depending on the size of the network and number of queues, Chameleon may even be able to double the number of flows it can accommodate in the network using reconfigurations.


### Drawback(s)
1. <a name="runt">Adding reconfigurations leads to an increased tail for the runtime. </a> A fix to this would be limiting the number of reconfigurations allowed.
1. Greater complexity in Chameleon's logic. 
1. Chameleon's runtime for processing requests makes it suitable for long-lived flows only.



## Concluding notes
1. None of the aforementioned approaches are work-conserving.
1. Chameleon accepts between 2x and 10x more flow requests and has a higher network utilization compared to QJUMP and Silo.
1. QJUMP has a much lower runtime than Chameleon and Silo due to the pre-assignment of all its decision parameters.
1. Static allocations can lead to unnecessary request rejections. By not optimizing nor accounting for the routes where flows are embdedded, such approaches are not demand-aware, as the network state and performance characteristics depend on the specific route taken.