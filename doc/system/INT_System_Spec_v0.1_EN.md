

# INT Platform
## System Specification
### Version 0.1 (Draft)

INT Platform is a network-based timecode distribution system designed to bridge modern software production workflows and traditional LTC‑based synchronization environments.

The system provides a lightweight network protocol (INTTC) and a set of device roles that allow timecode to be transmitted, resolved, and output across heterogeneous production systems.

---

# 1. Goals

INT Platform is designed with the following goals:

- Provide **network-native timecode distribution**
- Maintain compatibility with **existing LTC infrastructure**
- Support both **editorial workflows** and **frame‑critical synchronization systems**
- Enable flexible integration with software environments such as NLEs and real‑time engines
- Provide a foundation for future synchronization technologies

---

# 2. System Overview

INT Platform consists of three primary device classes.

```
Sender
   ↓
INTTC Network
   ↓
Receiver / Bridge
```

Example system topology:

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

---

# 3. Device Roles

## 3.1 Sender

Senders generate INTTC packets containing timecode information.

Typical implementations:

- DaVinci Resolve plugin
- media playback systems
- software timeline systems

Example:

```
INT TimeCode Tool
(DaVinci Resolve Workflow Integration Plugin)
```

### Sender Transmission Timing

Senders should transmit INTTC packets at a fixed rate independent of project frame rate.

Default sender mode:

- Packet rate: 60 Hz
- Expected interval: approximately 16.67 ms (60 Hz)

Each transmitted packet represents a snapshot of the sender's current logical timeline state at the sender transmission tick.

The packet transmission schedule determines **when packets are sent**, but does not redefine the true timecode value itself.

Receivers must treat the **packet content** as the authoritative timing state rather than relying on packet arrival time.

Senders may also emit immediate packets when significant state changes occur, including:

- play / stop transitions
- source selection changes
- timecode discontinuities
- frame‑rate related configuration changes
```

---

## 3.2 Receiver

Receivers consume INTTC packets and resolve timecode information for display or software use.

Receiver responsibilities:

- packet reception
- sequence tracking
- timecode resolution
- state interpretation

Typical use cases:

- monitoring applications
- timecode displays
- software integrations

Example:

```
SoftReceiver
```

---

## 3.3 Bridge

Bridge devices convert network timecode into hardware signals such as LTC.

Bridge responsibilities:

- receive INTTC packets
- resolve timecode
- maintain output timing
- generate LTC output

Example implementation:

```
INT TimeCode Bridge
RP2040 (Pico2) + W5500
```

---

# 4. Timing Architecture

INT Platform separates time handling into three layers.

```
Packet Time
Resolved Time
Output Time
```

---

## 4.1 Packet Time

Packet Time is the time value transmitted within an INTTC packet.

Characteristics:

- reflects sender timeline state
- subject to network latency and jitter
- may arrive out of order

---

## 4.2 Resolved Time

Resolved Time is the time value selected by the receiver after evaluating packet state and source selection.

Receiver logic may include:

- packet ordering using SEQ
- source selection using ACTIVE
- freeze detection
- stale detection

Resolved Time represents the **logical timecode state**.

---

## 4.3 Output Time

Output Time is the time value used for actual signal generation or display.

Output Time may differ from Resolved Time due to:

- holdover behavior
- reference discipline
- output offset
- reacquire logic

---

# 5. Output Policy

INT Platform defines two operational policies.

```
Loose
Tight
```

---

## 5.1 Loose Policy

Loose policy is intended for editorial workflows.

Characteristics:

- packet-driven updates
- small jitter tolerated
- fast reacquire
- minimal timing discipline

Typical uses:

- client monitors
- editorial review
- software displays

---

## 5.2 Tight Policy

Tight policy is intended for synchronization‑critical environments.

Characteristics:

- continuous frame progression
- stable frame boundaries
- disciplined timing
- controlled reacquisition

Typical uses:

- Unreal Engine synchronization
- LED volume playback
- stage lighting systems
- media servers

---

# 6. Reference Source

Receivers and bridge devices may use reference sources to discipline the local timing model.

Possible sources:

```
Internal
Video Reference
PTP
GPS
LTC In
```

Reference sources regulate the stability of the **Frame Clock** but do not directly determine the target time value.

---

# 6.1 Input Modes

Bridge devices may support multiple input signal types through a shared BNC input connector.

The BNC input is designed to support mutually exclusive input modes:

```
LTC In
Sync In (future implementation)
```

Only one input mode may be active at a time.

In Bridge v1 implementations, LTC In may be supported first while Sync In remains reserved for future implementation.

Hardware and board-level design should preserve sufficient flexibility to enable future Sync In support on the same BNC input path.

Sync In is intended to act as a timing discipline or reference signal for output generation rather than a direct source of target timecode.

---


# 6.2 Status Indicators (LED)

Bridge devices may include minimal hardware status indicators to allow basic operational state inspection without external monitoring tools.

A recommended minimal configuration consists of two LEDs:

```
Green LED
Red LED
```

Typical semantics:

Green LED (normal operation):

- Boot / initialization: slow blink
- Active packet reception and output generation: steady on or activity blink

Red LED (warning or fault conditions):

- Stale packet condition: blinking
- Offline / fault state: steady on

Exact LED behavior patterns may vary between implementations but should reflect the internal clock or network state machine where possible.

---

# 6.3 Bridge Hardware Overview

A typical Bridge implementation consists of a lightweight network receiver and LTC generation pipeline implemented in embedded hardware.

Example reference implementation:

```
MCU: RP2040 (Pico2)
Network interface: W5500 Ethernet controller
Shared BNC input: LTC In / Sync In (exclusive mode)
Status indicators: Green LED, Red LED
```

Hardware implementations should remain flexible to allow future expansion including additional reference inputs or synchronization features.

---

# 7. Frame Clock

The Frame Clock is a receiver‑local timing engine responsible for advancing output time.

Key state variables:

```
target_frame_time
current_frame_time
frame_rate
drop_frame
clock_state
reference_source
```

The Frame Clock operates independently of packet arrival timing.

---

# 8. Frame Boundary Generator

The Frame Boundary Generator produces events representing transitions to the next frame.

Each frame boundary event:

- advances the current frame time
- applies reacquire behavior
- processes holdover logic
- triggers LTC encoding

Frame boundaries define the cadence of output generation.

---

# 9. Holdover Policy

Holdover determines how output continues when packet updates are lost.

```
None
Freeze
Run
```

---

### None

Output stops or becomes invalid when updates are lost.

---

### Freeze

The last resolved timecode value is held.

---

### Run

Output continues using a local timing model.

Typical for Tight synchronization systems.

---

# 10. Reacquire Behavior

Defines how receivers respond when valid packet updates resume.

```
Snap
Smooth
```

---

### Snap

Immediate alignment to target time.

Suitable for editorial workflows.

---

### Smooth

Gradual convergence toward the target time.

Used in synchronization‑critical environments.

---

# 11. Output Offset

Output Offset is a signed time adjustment applied during output generation.

Purpose:

- compensate downstream device latency
- align systems with different processing delays
- match external synchronization environments

Offset examples:

```
+1 frame
-1 frame
+8 ms
-12 ms
```

Offset is applied during output generation rather than protocol resolution.

---

# 12. LTC Output Stage

Bridge devices generate LTC using the following pipeline.

```
Resolved Time
   ↓
Output Policy Controller
   ↓
Timing Discipline
   ↓
Frame Boundary Generator
   ↓
Output Offset Apply
   ↓
LTC Frame Encoder
   ↓
Physical Signal Driver
```

Under Tight policy, LTC generation is driven by the local Frame Clock rather than packet arrival timing.

---

# 13. Clock States

Receivers may maintain clock state information.

```
FreeRun
Locked
Holdover
Converging
Fault
```

---

# 14. Relationship to INTTC Protocol

INTTC provides the **transport layer** for timecode distribution.

The System Specification defines how receivers interpret protocol data and generate output.

```
INTTC Protocol
      ↓
Receiver Timing Model
      ↓
Output Generation
```

---

# 15. Status

Draft specification.

The system architecture may evolve as implementations develop.

Future versions may include:

- PTP timing discipline
- redundancy mechanisms
- multi-sender arbitration
- extended metadata