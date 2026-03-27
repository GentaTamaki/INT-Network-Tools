

# INT Network Tools

**Network Timecode Infrastructure for Creative Workflows**

INT Network Tools is an experimental system architecture designed to transport, resolve, and distribute timecode across networked environments while maintaining compatibility with traditional **LTC-based workflows**.

The project focuses on bridging modern software-driven production systems with legacy professional timing infrastructure used in broadcast, stage, and virtual production environments.

---

## Project Goals

INT Network Tools aims to:

- Enable **network-native timecode distribution**
- Maintain compatibility with **existing LTC hardware ecosystems**
- Support both **editorial workflows** and **frame-critical synchronization environments**
- Provide a foundation for **future timing systems (PTP, GPS, distributed sync)**

---

## Core Concepts

### Network Timecode Layer

Instead of replacing existing synchronization systems, INT introduces a **network distribution layer for timecode**.

Software systems can generate and distribute time information while remaining compatible with hardware infrastructure via LTC output.

---

### Output Policy

The system supports two operational timing models.

#### Loose

Designed for editorial workflows.

Characteristics:

- Small timing jitter tolerated
- Packet-driven updates acceptable
- Fast reacquisition preferred

Typical uses:

- Client monitoring
- Review environments
- Post-production editing

#### Tight

Designed for synchronization-critical environments.

Characteristics:

- Continuous frame progression
- Stable frame boundaries
- Clock-disciplined output

Typical uses:

- Unreal Engine
- LED wall / virtual production
- Stage lighting systems
- Media servers

---

## Architecture

INT Network Tools consists of three primary device classes.

```
Sender
   ↓
INTTC Network
   ↓
Receiver / Bridge
```

### Sender

Generates network timecode packets.

Example:

- **INT TimeCode Tool**
- DaVinci Resolve Workflow Integration Plugin

---

### Receiver

Software receiver that resolves and displays timecode.

Example:

- **SoftReceiver**

---

### Bridge

Hardware receiver that converts network timecode into **LTC output**.

Example implementation:

- **RP2040 (Pico2) + W5500**
- INT TimeCode Bridge

---

## Core Technology

### INTTC Protocol

INT Network Tools uses the **INTTC protocol** for network timecode transport.

Protocol features include:

- Timecode distribution over UDP
- Source identification
- Frame rate metadata
- Drop-frame support
- Multiple time sources

Default transport:

```
UDP Port 8001
```

---

## Design Principles

### Compatibility First

INT Network Tools always preserves compatibility with existing workflows.

**LTC output remains a first-class interface.**

---

### Software‑Defined Timing

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

## Example Applications

### Post Production

- editorial review
- client monitors

### Broadcast

- LTC distribution
- timing displays

### Virtual Production

- Unreal Engine synchronization
- LED wall playback

### Stage Systems

- DMX lighting
- media server synchronization

---

## Repository Structure

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

## Project Status

Early architecture and specification stage.

Core components currently under development:

- INTTC Protocol
- Resolve Sender
- INT TimeCode Bridge
- SoftReceiver

---

## License

TBD

---

## Author

INT Network Tools DEV