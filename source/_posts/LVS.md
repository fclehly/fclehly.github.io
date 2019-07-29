---
title: LVS
date: 2019-07-28 20:26:16
tags:
- lvs
- loadbalance
category:
- server 
---


## 基于LB的集群
###  简单拓扑

![ip-topo.001.png](ip-topo.001.png)

* LB: 负载均衡器；IP；转发，不做三次握手；保证三次握手到四次分手不可分割；
* CIP: Client IP;
* VIP: Vitual IP;
* DIP: Director's IP;
* RIP: Real IP;

#### NAT
### S_NAT
数据包过路由器时，修改源的IP地址。

### D_NAT
数据包过路由器时，修改目标的IP地址。


```mermaid
sequenceDiagram

participant clients
participant director
participant servers

clients ->> director: CIP=>VIP
director ->> servers: DIP=>RIP
servers -->> director: DIP<=RIP
director -->> clients: CIP<=VIP
```

client 请求server的过程为DNAT，server回复client的过程为SNAT。