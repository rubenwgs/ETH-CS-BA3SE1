---
title: "Computer Systems - Notes Week 8"
author: Ruben Schenk, ruben.schenk@inf.ethz.ch
date: January 6, 2022
geometry: margin=2cm
output: pdf_document
---

# Chapter 14: Introduction To Distributed Systems

## 14.1 Why Distributed Systems?

Today's computing and information systems are inherently _distributed._ Many companies are operating on a global scale, with thousands or even millions of machines on all the continents. Data is stored in various data centers, computing tasks are performed on multiple machines. In summary, today almost all computer systems are distributed, for different reasons:

- Geography: Large organizations and companies are inherently geographically distributed, and a computer system needs to deal with this issue anyway.
- Parallelism: To speed up computation, we employ multicore processors or computing clusters.
- Reliability: Data is replicated on different machines to prevent data loss.
- Availability: Data is replicated on different machines to allow for access at any time, without bottlenecks, minimizing latency.

## 14.2 Distributed Systems Overview

We introduce some basic techniques to building distributed systems, with a focus on fault-tolerance. We will study different protocols and algorithms that allow for fault-tolerant operation, and we will discuss practical systems that implement these techniques. Furthermore, we will see different models that can be studies. The focus is on protocols and systems that matter in practice.

# Chapter 15: Fault-Tolerance & Paxos

How do you create a fault-tolerant distributed system? In this chapter we start out with simple questions, and, step by step, improve our solutions until we arrive at a system that works even under adverse circumstances: Paxos.

## 15.1 Client/Server

We call a single actor in the system **node.** In a computer network the computers are the nodes, in the classical client-server model both the server and the client are nodes, and so on. If not stated otherwise, the total number of nodes in the system is $n$.

**_Model 15.2:_** In the **message passing model** we study distributed systems that consist of a set of nodes. Each node can perform local computations, and can send messages to every other node.

```pseudo
# Algorithm 15.3: Naive Client-Server Algorithm
1:  Client sends commands one at a time to the server
```

**_Model 15.4:_** In the message passing model with **message loss,** for any specific message, it is not guaranteed that it will arrive safely at the receiver.
Algorithm 15.3 does not work correctly if there is message loss, so we need a little improvement.

```pseudo
# Algorithm 15.5: Client-Server Algorithm with Acknowledgments
1:  Client sends commands one at a time to the server
2:  Server acknowledges every command
3:  If the client does not receive an acknowledgment within a reasonable time, the client resends the command
```

_Remark:_ Since not only messages sent by the client can be lost, but also acknowledgments, the client might resend a message that was already received and executed on the server. To prevent multiple executions of the same command, one can add a _sequence number_ to each message, allowing the receiver to identify duplicates.

**_Model 15.6:_** In practice, messages might experience different transmission times, even if they are being sent between the same two nodes.

_Remark._ Throughout this chapter, we assume the variable message delay model.

> **_Theorem 15.7:_** If Algorithm 15.5 us used with multiple clients and multiple servers, the servers might see the commands in different order, leading to an inconsistent state.

A set of nodes achieves **state replication** if all nodes execute a potentially infinite sequence of commands $c_1, \, c_2, \, c_3,...$ in the same order.
State replication is a fundamental property for distributed systems! Since state replication is trivial with a single server, we can designate a single server as a _serializer._ By letting the serializer distribute the commands, we automatically order the requests and achieve state replication.

```pseudo
# Algorithm 15.9: State Replication with a Serializer
1:  Clients send commands one at a time to the serializer
2:  Serializer forwards commands one at a time to all other servers
3:  Once the serializer received all acknowledgments, it notifies the client about the success
```

Can we have a more distributed approach of solving state replication? Instead of directly establishing a consistent order of commands, we can use a different approach: We make sure that there is always at most one client sending a command, i.e. we use _mutual exclusion,_ respectively _locking._

```pseudo
# Algorithm 15.10: Two-Phase Protocol
    # Phase 1
1:  Client asks all servers for the lock
    # Phase 2
2:  if client receives lock from every server then:
3:      Client sends command reliably to each other server, and gives the lock back
4:  else:
5:      Clients gives the received locks back
6:      Client waits, and then starts with Phase 1 again
7:
```

## 15.2 Paxos

A **ticket** is a weaker form of a lock, with the following properties:

- _Reissuable:_ A server can issue a ticket, even if previously issued tickets have not yet been returned.
- _Ticket expiration:_ If a client sends a message to a server using a previously acquired ticket $t$, the server will only accept $t$, if $t$ is the most recently issued ticket.

_Remarks:_
 
  - There is no problem with crashes: If a client crashes while holding a ticket, the remaining clients are not affected, as servers can simply issue a new ticket.
  - Tickets can be implemented with a counter: Each time a ticket is requested, the counter is increased. When a client tries to use a ticket, the server can determine if the ticket is expired.

```pseudo
# Algorithm 15.12: Naive Ticket Protocol
    # Phase 1
1:  Client asks all servers for a ticket
    # Phase 2
2:  if a majority of the servers replied then:
3:      Client sends command together with ticket to each server
4:      Server stores command only if ticket is still valid, and replies to client
5:  else
6:      Client waits, and then starts with Phase 1 again
7:  end if
    # Phase 3
8:  if client hears a positive answer from a majority of the servers then:
9:      Client tells servers to execute the stored command
10: else
11:     Client waits, and then starts with Phase 1 again
12: end if
```

_Remarks:_

- There are problems with this algorithm: Let $u_1$ be the first client that successfully stores its command $c_1$ on a majority of the servers. Assume that $u_1$ becomes very slow just before it can notify the servers (line 9), and a client $u_2$ updates the stored command in some servers to $c_2$. Afterwards, $u_1$ tells the servers to execute the command. Now some servers will execute $c_1$ and other $c_2$!
- We know that every client $u_2$ that updates the stored command after $u_1$ must have used a newer ticket than $u_1$. As $u_1$'s ticket was accepted in Phase 2, it follows that $u_2$ must have acquired its ticket after $u_1$ already stored its value in the respective server.
- Idea: What if a server, instead of only handing out tickets in Phase 1, also notifies clients about its currently stored command? Then, $u_2$ learns that $u_1$ already stored $c_1$ and instead of trying to store $c_2$, $u_2$ could support $u_1$ by also storing $c_1$. As both clients try to store and execute the same command, the order in which they proceed is no longer a problem.

```pseudo
# Algorithm 15.13: Paxos
Client (Proposer)                           Server (Acceptor)
    # Initialization
    c <- command to execute                     T_max = 0 <- largest issued ticket
    t = 0 <- ticket number to try
                                                C = bot <- stored command
                                                T_store = 0 <- ticket used to store C
   # Phase 1
1:  t = t + 1
2:  Ask all servers for ticket t
                                            3:  if t > T_max then:
                                            4:      T_max = t
                                            5:      Answer with ok(T_store, C)
                                            6:  end if
    # Phase 2
7:  if a majority answer with ok then:
8:      Pick(T_store, C) with largest T_store
9:      if T_store > 0 then
10:         c = C
11:     end if
12:     Send propose(t, c) to same majority
13: end if
                                            14: if t = T_max then:
                                            15:     C = c
                                            16:     T_store = t
                                            17:     Answer success
                                            18: end if
    # Phase 3
19: if a majority answers success then:
20:     Send execute(c) to every server
21: end if
```

Unlike previously mentioned algorithms, there is no step where a client explicitly decides to start a new attempt and jumps back to Phase 1. This has the advantage that we do not need to be careful about selecting "good" values for timeouts, as correctness is independent of the decisions when to start new attempts.

> **_Lemma 15.14:_** We call a message `propose(r, c)` sent by clients on line 12 a **proposal for (t, c).** A proposal for (t, c) is **chosen,** if it is stored by a majority of servers (line 15). For every issued `propose(t', c')` with $t' > t$, it holds that $c' = c$, if there was a chosen `propose(t, c)`.

> **_Theorem 15.15:_** If a command $c$ is executed by some servers, all servers eventually execute $c$.

If the client with the first successful proposal does not crash, it will directly tell every server to execute $c$. However, if the client crashes before notifying any of the servers, the servers will execute the command only once the next client is successful. Note that Paxos cannot make progress if half (or more) of the servers crash, as clients cannot achieve a majority anymore.
