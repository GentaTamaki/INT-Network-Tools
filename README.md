# INT Network Tools

Network Timecode Infrastructure for Creative Workflows


🇺🇸 English | 🇯🇵 [日本語](README_JA.md)

Protocol: **INTTC v1.2** | Status: **Draft** | Transport: **UDP 8001**


INT Network Tools is an experimental system architecture designed to transport, resolve, and distribute timecode across networked environments while maintaining compatibility with traditional **LTC‑based workflows**.

INTTC is designed as a network-native timecode layer bridging software timelines and LTC-based synchronization systems.

The project focuses on bridging modern software‑driven production systems with legacy professional timing infrastructure used in broadcast, stage, and virtual production environments.

---

# Why INT Network Tools Exists

Modern production environments increasingly rely on software timelines and networked systems, while many professional synchronization workflows still depend on **LTC-based hardware infrastructure**.

This creates a gap between:

- software-driven creative tools
- hardware-based timing systems used in broadcast, stage, and media playback

INT Network Tools is designed to bridge this gap.

It introduces a **network-native timecode layer (INTTC)** that allows software systems to distribute timing information across IP networks while remaining compatible with traditional LTC workflows through bridge devices.

In short:

```
Software timelines → Network timecode → LTC hardware ecosystem
```

This approach enables modern creative tools to integrate with existing professional infrastructure without replacing it.

---

# Documentation

### Philosophy
- English: `docs/philosophy/INT_Philosophy.md`
- 日本語: `docs/philosophy/INT_Philosophy_JA.md`

### System Specification
- English: `docs/system/INT_System_Spec_v0.1.md`
- 日本語: `docs/system/INT_System_Spec_v0.1_JA.md`

### INTTC Protocol
- English: `docs/protocol/INTTC_Protocol_v1.2.md`
- 日本語: `docs/protocol/INTTC_Protocol_v1.2_JA.md`

---

# Project Goals

INT Network Tools aims to:

- Enable **network‑native timecode distribution**
- Maintain compatibility with **existing LTC hardware ecosystems**
- Support both **editorial workflows** and **frame‑critical synchronization environments**
- Provide a foundation for **future timing systems (PTP, GPS, distributed sync)**

---

# Core Concepts

## Network Timecode Layer

Instead of replacing existing synchronization systems, INT introduces a **network distribution layer for timecode**.

Software systems can generate and distribute time information while remaining compatible with hardware infrastructure via LTC output.

---

## Output Policy

The system supports two operational timing models.

### Loose

Designed for editorial workflows.

Characteristics:

- Small timing jitter tolerated  
- Packet‑driven updates acceptable  
- Fast reacquisition preferred  

Typical uses:

- Client monitoring
- Review environments
- Post‑production editing

---

### Tight

Designed for synchronization‑critical environments.

Characteristics:

- Continuous frame progression
- Stable frame boundaries
- Clock‑disciplined output

Typical uses:

- Unreal Engine
- LED wall / virtual production
- Stage lighting systems
- Media servers

---

# Architecture

INT Network Tools consists of three primary device classes.

```
Sender
   ↓
INTTC Network
   ↓
Receiver / Bridge
```

Example topology:

```
Resolve (Sender)
      │
      │ INTTC
      │
 ┌───────────────┬───────────────┐
 │               │               │
Receiver      Receiver        Bridge
(Display)     (Software)     (LTC Generator)
```

Conceptual timing bridge:

```
Software Timeline
        │
        │ INTTC
        │
   Network Layer
        │
 ┌──────┴─────────┐
 │                │
Receiver       Bridge
(Display)      (LTC Output)
                  │
                Legacy
                Devices
```

This diagram illustrates how INTTC acts as a network layer between modern software timelines and legacy LTC‑based hardware systems.
```

---

## Sender

Generates network timecode packets.

Example:

- **INT TimeCode Tool**
- DaVinci Resolve Workflow Integration Plugin

---

## Receiver

Software receiver that resolves and displays timecode.

Example:

- **SoftReceiver**

---

## Bridge

Hardware receiver that converts network timecode into **LTC output**.

Example implementation:

- **RP2040 (Pico2) + W5500**
- INT TimeCode Bridge

---

# Core Technology

## INTTC Protocol

INT Network Tools uses the **INTTC protocol** for network timecode transport.

Protocol features include:

- Timecode distribution over UDP
- Source identification
- Frame rate metadata
- Drop‑frame support
- Multiple time sources

Default transport:

```
UDP Port 8001
```

---

# Design Principles

## Compatibility First

INT Network Tools always preserves compatibility with existing workflows.

**LTC output remains a first‑class interface.**

---

## Software‑Defined Timing

Receivers maintain an internal timing model separating:

```
Packet Time
Resolved Time
Output Time
```

This architecture allows:

- jitter tolerance
- continuous output generation
- controlled reacquisition
- flexible synchronization strategies

---

# Example Applications

## Post Production

- editorial review
- client monitors

## Broadcast

- LTC distribution
- timing displays

## Virtual Production

- Unreal Engine synchronization
- LED wall playback

## Stage Systems

- DMX lighting
- media server synchronization

---

# Repository Structure

```
docs/
    philosophy/
    system/
    protocol/

sender/
    resolve-timecode-tool/

bridge/
    int-timecode-bridge/

receiver/
    softreceiver/
```

---

# Project Status

Early architecture and specification stage.

Core components currently under development:

- INTTC Protocol
- Resolve Sender
- INT TimeCode Bridge
- SoftReceiver

---

# License

TBD

---

# Author

INT Network Tools DEV