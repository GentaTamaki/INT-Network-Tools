

# INT Network Tools
## Philosophy

INT Network Tools is designed to bridge modern software-driven production environments and traditional hardware-based timing systems.

Professional media workflows rely heavily on **timecode synchronization**, yet modern creative tools and legacy broadcast hardware often operate in different timing domains.

INT Network Tools provides a **network-native timecode distribution layer** that connects these environments.

---

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

INT Network Tools connects these ecosystems by transporting timecode over IP networks while preserving compatibility with LTC hardware.

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

Instead of replacing existing synchronization infrastructure, INT Network Tools introduces a **network distribution layer for timecode**.

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

To support both scenarios, INT Network Tools defines two output policies:

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

INT Network Tools treats time distribution as a **software-defined system**.

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

A core design principle of INT Network Tools is:

> Compatibility with existing workflows must never be sacrificed.

For this reason, **LTC output is treated as a first-class interface**.

LTC remains the most universal synchronization signal used across broadcast, stage, and media playback systems.

---

# 6. Foundation for Future Synchronization Systems

INT Network Tools is designed to evolve.

Future extensions may include:

- PTP-based synchronization
- GPS disciplined timing
- multi-sender arbitration
- distributed synchronization architectures

By separating protocol transport, time resolution, and signal generation, the system provides a flexible foundation for future timing technologies.

---

# Status

Draft document.
The philosophy and architecture may evolve as implementations develop.