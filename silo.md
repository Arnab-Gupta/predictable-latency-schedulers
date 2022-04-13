## Silo

1. Silo leverages the tight coupling between bandwidth and delay: controlling tenant bandwidth leads to determinstic bounds on network queuing delay.

1. uses a novel hypervisor-based policing mechanishm that achieves packet pacing at sub-microsecond granularity, ensuring tenants do not exceed their allowances.

1. Silo provides latency guarantees by leveragin admission control and relying on deterministic network calculus (DNC).

### 3 key requirements

1. guaranteed bandwidth
1. guaranteed packet delay - through fine-grained rate control at end hosts.
1. guaranteed burst allowance



## QJUMP
1. Relies on information about application perfformance requirements, related to latency, rate and packet size, at network initialization time.
1. maximum latency of $2nP/R + \epsilon$.


## Chameleon

1. Chameleon employs source routing on the queue-level topology, a network abstraction that accounts for the current states of the network queues, and hence, the different delays of different paths.
1. "queue-level" topology rather than just a "switch-level" topology
1. combines *

### Re-reouting
1. At first, the routing procedure tries to find a path for the flow request using a least-delay search. If it fails, the procedure tries to reroute alredy embedded flows to make space for the new one.
        
    - It selects all flows traversing at least one edge of any of the equal-length shortest paths in the physical topology from the source server to the destination server of the new flow to embed. 
    - it sorts the flows according to the number of shared physical links with the shortest paths, and this list is truncated to limit the maximum number of re-routing retries, which mitigates the [runtime increase caused by re-reouting](/pred-lat-sch/silo.md##Silo) .


### Drawback(s)
1. 
 
- Provides:
    1. predictable latency
    1. high utilization


<!--- ElasticSwitch -->

Static allocations can lead to unnecessary request rejections. By not optimizing nor accounting for the routes where flows are embdedded, such approaches are not demand-aware, as the network state and performance characteristics depend on the specific route taken.


