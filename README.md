# INT Platform
Network-based synchronization infrastructure for creative production systems.

---

## Quick Architecture

```
Creative Software Timeline
        ↓
INT Sender
        ↓
INT Distributor
        ↓
Receivers / Bridges / Monitoring Systems
```

INT Platform separates **timeline generation**, **network distribution**, and **hardware synchronization** into independent roles.

This architecture allows creative software to stay lightweight while enabling a scalable synchronization infrastructure across production environments.

---

INT Platform is a **production synchronization infrastructure for creative workflows**.

It is designed to transport, distribute, and resolve timeline timing information across networked production environments while maintaining compatibility with traditional **LTC‑based synchronization systems**.

The platform connects modern software‑driven timelines with legacy hardware timing infrastructure used in post‑production, broadcast, stage systems, and virtual production.

---

# Core Idea

INT Platform separates **timeline state generation**, **network distribution**, and **hardware synchronization** into clearly defined roles.

The goal is to decouple creative software from synchronization infrastructure while keeping full compatibility with traditional broadcast and production timing systems.

---

# Platform Architecture

```
INT Platform
│
├ Protocol
│   └ INTTC
│
├ Senders
│   ├ Resolve Sender
│   │   └ INT TimeCode Tool
│   ├ Media Composer Sender
│   ├ Premiere Sender
│   └ Pro Tools Sender
│
├ Distributor
│   └ INT Distributor
│
└ Receivers
    ├ INT Viewer
    ├ INT Monitor
    └ INT Bridge
```

### Sender
Extracts timeline state from creative software such as NLEs or DAWs.

### Distributor
Acts as the central network distribution point.  
All senders publish timing information to the distributor, and all receivers subscribe to it.

### Receiver
Displays, monitors, or converts timecode for external systems.

---

# Three-Layer Model

INT Platform can also be understood as a layered synchronization model:

```
Software Timeline Layer
        ↓
Sender Layer
        ↓
Distribution Layer
        ↓
Receiver / Hardware Layer
```

In practical terms:

```
Editing / Playback Software
        ↓
Sender
        ↓
INT Distributor
        ↓
Receiver / Bridge
        ↓
LTC / Legacy / External Sync Systems
```

This layered model clarifies where different responsibilities exist within the platform:

- where timeline state is generated (software timeline layer)
- where timeline state is published to the network (sender layer)
- where distribution and routing occur (distribution layer)
- where timecode is resolved, displayed, or converted for external systems (receiver / hardware layer)

---

# Design Goals

INT Platform aims to:

- enable **network‑native timecode distribution**
- maintain compatibility with **existing LTC hardware ecosystems**
- support both **editorial workflows** and **synchronization‑critical environments**
- provide a foundation for future synchronization systems such as **PTP, GPS, and distributed timing**
- separate **timeline state**, **transport**, and **output synchronization**
- support **distributed production environments**
- allow hardware bridges to integrate legacy synchronization systems

---

# Project Status

Early architecture and specification stage.

Core components currently under development:

- INTTC Protocol
- Resolve Sender
- INT TimeCode Bridge
- INT Viewer

---

# Author

INT Platform DEV