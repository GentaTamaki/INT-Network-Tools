

# INT Distributor Specification v0.1

Status: Draft

---

# 1 Overview

The INT Distributor is the central routing component of the INT Platform.

Its role is to receive INTTC packets from one or more Senders and distribute
those packets to multiple Receivers in a stable and scalable manner.

The Distributor is intentionally designed as a **stateless or minimally stateful
routing layer** so that creative software can remain lightweight while network
infrastructure handles distribution and synchronization.

The Distributor does **not generate timecode** and does **not modify timeline
state**. Its responsibility is limited to packet intake, routing, and
connection management.

---

# 2 System Role

Within the INT Platform architecture, the Distributor sits between Senders
and Receivers.

Typical topology:

```
Creative Software Timeline
        ↓
INT Sender
        ↓
INT Distributor
        ↓
Receivers / Bridges / Monitoring Systems
```

Multiple Senders may exist in the network.

Multiple Receivers may subscribe to the same Distributor.

The Distributor allows centralized routing of timing data without requiring
Senders to manage direct peer connections.

---

# 3 Design Goals

The Distributor is designed with the following goals:

- support **multiple senders and multiple receivers**
- allow **centralized network routing** of INTTC packets
- maintain **minimal latency** between sender and receiver
- remain **transport-agnostic** where possible
- avoid modification of timing data
- support scalable monitoring environments

The Distributor must not become a single point of timing authority.

Timing authority always remains with the Sender.

---

# 4 Distributor Responsibilities

The Distributor performs the following tasks:

1. Accept packets from Senders
2. Maintain lightweight sender presence tracking
3. Forward packets to subscribed Receivers
4. Maintain receiver connections
5. Provide optional monitoring endpoints

The Distributor must not:

- rewrite timecode values
- reinterpret timing state
- interpolate timing
- act as a synchronization authority

---

# 5 Sender Registration

Senders transmit INTTC packets using UDP.

When a packet is received, the Distributor records minimal metadata
about the sender.

Example metadata:

- sender_id
- last_packet_time
- source address

This information may be used for monitoring or debugging but must not
influence packet routing behavior.

The Distributor must assume that sender presence is **packet-driven**.

No persistent registration protocol is required.

---

# 6 Receiver Subscription

Receivers connect to the Distributor using one of the following methods:

- WebSocket
- TCP stream
- UDP listener

The subscription model may vary depending on implementation, but the
logical behavior remains the same:

Receivers subscribe to a stream of INTTC packets.

The Distributor forwards incoming packets to all active subscribers.

Receivers are responsible for their own timing reconstruction.

---

# 7 Packet Routing

When the Distributor receives an INTTC packet it performs the following steps:

1. Receive packet from network
2. Validate basic structure
3. Record sender metadata
4. Forward packet to active receivers

The Distributor must not modify packet contents.

Forwarding should occur as quickly as possible to minimize latency.

Recommended behavior:

- non-blocking forwarding
- asynchronous delivery

---

# 8 Multi-Sender Handling

Multiple Senders may operate simultaneously.

Receivers may choose which sender to follow.

The Distributor must therefore allow packets from multiple senders to
coexist in the routing layer.

The Distributor should not enforce sender selection logic.

Receiver software is responsible for choosing the authoritative sender.

---

# 9 Failure Behavior

If a Sender stops transmitting packets, the Distributor takes no special
recovery action.

Sender disappearance is detected by Receivers using packet timeout logic.

The Distributor simply stops forwarding packets from that sender once
transmissions cease.

This design prevents unnecessary complexity inside the routing layer.

---

# 10 Latency Model

The Distributor should introduce **minimal additional latency**.

Typical pipeline:

```
Sender → Distributor → Receiver
```

The Distributor must forward packets immediately without buffering
whenever possible.

Recommended target latency contribution:

< 1 ms

Actual latency depends on network infrastructure.

---

# 11 Monitoring Interfaces

The Distributor may optionally expose monitoring endpoints such as:

- HTTP status endpoints
- Web dashboards
- packet statistics

Example metrics:

- active senders
- active receivers
- packet rate
- last packet time

These monitoring interfaces must remain read-only and must not interfere
with packet routing.

---

# 12 Security Considerations

In production environments, the Distributor may implement optional
network security mechanisms such as:

- access control
- network isolation
- authenticated receiver connections

Security mechanisms must not alter INTTC packet contents.

---

# 13 Relationship to INTTC Protocol

The Distributor is transport infrastructure.

It does not redefine the INTTC protocol.

INTTC packet structure and timing semantics are defined in:

```
INTTC Protocol Specification
```

The Distributor simply routes packets that conform to that protocol.

---

# Status

Draft specification for INT Platform Distributor behavior.

Future revisions may include:

- load balancing
- multi-distributor networks
- redundancy models
- advanced monitoring APIs

This document defines the baseline routing model for INT Platform.