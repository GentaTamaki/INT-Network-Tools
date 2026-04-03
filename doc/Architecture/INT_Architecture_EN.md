

# INT Platform Architecture

## 1. Overview

INT Platform is a network-based timecode distribution system designed to bridge
modern software production environments and traditional synchronization
infrastructure such as LTC-based devices.

The platform separates responsibilities into three layers:

- Transport protocol (INTTC)
- System timing architecture
- Device implementations

This document describes the high-level architecture of the INT Platform and
how the different components interact.

---

## 2. System Roles

INT Platform defines several logical roles.

### Sender

A Sender generates INTTC packets that represent the authoritative timeline
state of a system such as an NLE.

Typical Sender examples:

- DaVinci Resolve plugin
- playback control software
- timeline-based production tools

The Sender publishes time information to the network.

### Receiver

A Receiver consumes INTTC packets and reconstructs the timeline state using a
local timing model.

Receivers may be implemented in:

- monitoring applications
- stage displays
- synchronization tools
- diagnostic utilities

### Bridge

A Bridge converts network time information into physical synchronization
signals.

Typical outputs:

- LTC
- audio LTC
- reference-aligned timing signals

Bridge devices allow legacy systems to participate in an INT-based workflow.

---

## 3. Data Flow

A typical INT Platform signal flow looks like this:

```
Timeline Software
      ↓
INTTC Sender
      ↓
Network (UDP)
      ↓
Receiver
      ↓
Bridge (optional)
      ↓
LTC / Display / Monitoring
```

The Sender publishes timeline state over the network using the INTTC
protocol. Receivers interpret this information and may generate display,
monitoring, or synchronization outputs.

---

## 4. Timing Model

INT Platform separates time handling into three conceptual layers.

```
Sender Timeline
      ↓
Packet Time (INTTC transport)
      ↓
Resolved Time (receiver interpretation)
      ↓
Output Time (signal generation)
```

This layered design allows Receivers to maintain stable output timing even
when network packet timing varies.

---

## 5. Relationship to INTTC

INTTC is the transport protocol used by the INT Platform.

```
INTTC Protocol
      ↓
INT Platform Architecture
      ↓
Device Implementations
```

INTTC defines how timing information is encoded and transmitted.

The INT Platform System Specification defines how systems interpret and
process that information.

---

## 6. Implementation Examples

Example INT Platform deployments may include:

- NLE plugin acting as a Sender
- network Receiver displaying timeline timecode
- hardware Bridge generating LTC

These components can be combined to support workflows in:

- post-production
- broadcast environments
- stage systems
- virtual production

---

## Status

Draft architecture description.

The INT Platform architecture may evolve as additional implementations are
developed.