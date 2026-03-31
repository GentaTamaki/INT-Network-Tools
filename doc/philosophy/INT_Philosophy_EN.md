# INT Platform
## Philosophy

INT Platform began as a practical solution to missing functionality in modern editorial software.

While professional media workflows depend heavily on **timecode synchronization**, many modern creative tools operate entirely inside software environments and lack simple ways to distribute timeline timecode to external systems.

INT Platform introduces a **network‑native timecode distribution layer** that allows software timelines, monitoring tools, and traditional LTC‑based hardware to operate within a shared timing system.

---


# Origins

INT Platform originated from practical needs inside real editorial workflows.

The project began with the development of tools for DaVinci Resolve that provided functionality not available in existing editing software. These tools were initially created to solve day‑to‑day operational problems encountered during professional post‑production work.

As these tools evolved, additional components became necessary:

- a way to transmit timeline timecode outside the editing system
- a bridge device to convert network timecode into LTC
- receiver applications for monitoring and distributed timing

This progression naturally led to the design of a network‑based timecode system.

In other words, INT Platform did not begin as a protocol design project. Instead, the protocol emerged organically from real production tools and workflows.

# 1. Bridging Software and Hardware Worlds

Modern production tools such as:

- NLE systems
- Unreal Engine
- media servers
- virtual production pipelines

operate primarily inside software environments.

At the same time, many professional systems still depend on **LTC-based synchronization**, including:

- broadcast infrastructure
- stage lighting systems (DMX)
- media playback hardware
- timecode display units

INT Platform connects these ecosystems by transporting timecode over IP networks while preserving compatibility with LTC hardware.

```
Software Systems
       ↓
      INTTC
       ↓
    Bridge
       ↓
      LTC
       ↓
Legacy Hardware
```

---

# 2. Network Timecode Distribution

Instead of replacing existing synchronization infrastructure, INT Platform introduces a **network distribution layer for timecode**.

This allows:

- software timelines to generate timecode
- multiple receivers to subscribe to timing information
- bridge devices to convert network timecode into LTC signals

This architecture enables flexible system design while remaining compatible with established workflows.

---

# 3. Supporting Different Timing Environments

Not all production environments require the same level of timing precision.

Editorial environments prioritize:

- responsiveness
- flexibility
- simplicity

Synchronization-critical environments require:

- stable frame timing
- deterministic output
- continuous time progression

To support both scenarios, INT Platform defines two output policies:

```
Loose
Tight
```

### Loose

Designed for editorial and monitoring environments.

Characteristics:

- packet-driven updates
- small timing deviations tolerated
- rapid reacquisition

Typical use cases:

- client monitors
- editorial review
- post-production displays

### Tight

Designed for synchronization-critical environments.

Characteristics:

- continuous frame progression
- clock-disciplined output
- stable frame boundaries

Typical use cases:

- virtual production
- LED wall synchronization
- stage lighting systems
- media server playback

---

# 4. Software-Defined Timing

INT Platform treats time distribution as a **software-defined system**.

Receivers internally separate time into three layers:

```
Packet Time
Resolved Time
Output Time
```

This architecture allows systems to:

- tolerate network jitter
- maintain continuous output
- recover smoothly from packet interruptions
- adapt to multiple synchronization environments

---

# 5. Compatibility First

A core design principle of INT Platform is:

> Compatibility with existing workflows must never be sacrificed.

For this reason, **LTC output is treated as a first-class interface**.

LTC remains the most universal synchronization signal used across broadcast, stage, and media playback systems.

---

# 6. Foundation for Future Synchronization Systems

INT Platform is designed to evolve.

Future extensions may include:

- PTP-based synchronization
- GPS disciplined timing
- multi-sender arbitration
- distributed synchronization architectures

By separating protocol transport, time resolution, and signal generation, the system provides a flexible foundation for future timing technologies.

---

# 7. Open Platform Philosophy

INT Platform follows an open-platform philosophy.

The goal of the project is to make the **concepts, architecture, and protocol** available so that the industry can benefit from a shared understanding of network‑based timecode distribution.

For this reason, the following elements are intended to be openly documented:

- protocol specifications
- system architecture
- timing models
- conceptual design principles

At the same time, individual implementations of INT-compatible tools may remain proprietary.

Commercial products such as timeline tools, bridge devices, or receiver applications may implement the INT concepts while maintaining their own internal designs and optimizations.

This approach allows INT Platform to function both as:

- an open technical foundation for network timecode systems
- a platform for professional tools built on top of that foundation

In short, INT aims to share the **idea and the language of the system**, while leaving room for innovation in how specific tools are implemented.

---

# 8. Design Principles

The development of INT Platform follows a small set of practical design principles:

- **Real workflows first** — tools should solve real production problems before abstract system design.
- **Compatibility over reinvention** — existing infrastructure such as LTC must remain usable.
- **Network-native thinking** — timecode distribution should behave naturally within modern IP-based systems.
- **Open concepts, independent implementations** — the architecture and protocol are shared, while individual tools may innovate in their implementations.

These principles reflect the practical origin of the project and guide the continued evolution of INT Platform.

---

# Status

Draft document.
The philosophy and architecture may evolve as implementations develop.