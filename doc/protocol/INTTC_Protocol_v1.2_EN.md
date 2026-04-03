

# INTTC Protocol
## Version 1.2

INTTC (INT TimeCode) is a lightweight UDP-based protocol designed to distribute
timecode information across networked systems while maintaining compatibility
with traditional LTC-based infrastructure.

The protocol is designed for:

- NLE software integration
- virtual production environments
- stage systems
- broadcast workflows
- LTC bridge hardware

INTTC acts as a **network-native transport layer for timecode**.

---

# 1. Transport

INTTC packets are transmitted using UDP.

Default port:

```
UDP 8001
```

The protocol is designed for **low-latency broadcast on local networks**.

Typical transport modes:

- broadcast
- multicast
- directed UDP

---

# 2. Packet Structure

Each INTTC packet contains a compact set of fields describing the sender
state and the associated timecode.

```
INTTC1
VER
SEQ
SRC_ID
STATE
SRC_TC
ALT_TC
ACTIVE
DISPLAY
OUTPUT
FPS
DF
```

---

# 3. Field Definitions

## VER

Protocol version identifier.

Example:

```
1
```

---

## SEQ

Monotonic packet sequence counter.

Purpose:

- packet ordering
- packet loss detection
- freeze detection

Example:

```
10521
```

---

## SRC_ID

Unique identifier for the sender.

Recommended format:

```
6 characters
[A-Z0-9]
```

Example:

```
RSV001
VPST01
```

---

## STATE

Sender state indicator.

Possible values:

```
RUN
STOP
PAUSE
IDLE
```

This allows receivers to determine the operational state of the sender.

---

## SRC_TC

Primary timecode value.

Format:

```
HH:MM:SS:FF
```

Example:

```
01:12:33:10
```

---

## ALT_TC

Optional alternate timecode.

Used when a secondary reference is available.

Example uses:

- source vs record timecode
- playback vs capture timecode

---

## ACTIVE

Indicates which time source is currently active.

Example values:

```
SRC
ALT
LTCIN
```

---

## DISPLAY

Display preference hint for receivers.

Example values:

```
SRC
ALT
```

Receivers may ignore this field.

---

## OUTPUT

Output preference hint for bridge devices.

Example values:

```
SRC
ALT
```

Bridge devices may select which timecode to convert to LTC.

---

## FPS

Frame rate of the timecode.

Example values:

```
23.976
24
25
29.97
30
59.94
```

---

## DF

Drop-frame indicator.

Values:

```
0 = non-drop-frame
1 = drop-frame
```

---

# 4. Packet Example

Example packet representation:

```
INTTC1
VER=1
SEQ=10452
SRC_ID=RSV001
STATE=RUN
SRC_TC=01:23:45:12
ALT_TC=01:23:45:12
ACTIVE=SRC
DISPLAY=SRC
OUTPUT=SRC
FPS=23.976
DF=0
```

---

# 5. Sender Announcement

Senders may broadcast announcement packets to allow receivers to discover
available sources.

Announcement type:

```
INTTC_SENDER
```

Example fields:

```
SRC_ID
NAME
FPS
STATE
```

---

# 6. Bridge Announcement

Bridge devices may announce their presence.

Announcement type:

```
INTTC_BRIDGE
```

Example fields:

```
BRIDGE_ID
NAME
CAPABILITIES
```

Capabilities may include:

```
LTC_OUT
AUDIO_LTC
REFERENCE_IN
```

---

# 7. Timing Model and Receiver Behavior

Receivers process incoming packets using the following logic:

1. Track packet sequence numbers.
2. Detect packet loss using SEQ.
3. Resolve time source using ACTIVE field.
4. Generate Resolved Time.
5. Produce Output Time according to local Output Policy.

Receiver implementations may implement additional timing models such as:

- holdover
- smooth reacquire
- reference discipline

## 7.1 Sender Transmission Model

INTTC senders transmit packets at a fixed periodic rate independent of the
project frame rate.

Default sender mode:

- Packet rate: 60 Hz
- Expected interval: ~16.67 ms

Each packet represents a **snapshot of the sender's logical timing state** at
the moment the packet is emitted.

The packet includes the current values for:

- primary timecode
- alternate timecode
- sender state
- active source
- frame rate
- drop-frame flag

INTTC packets are **not defined as one-per-frame messages** and must not be
interpreted as scheduling instructions for future frames.

Instead, packets communicate the sender's current timing state. Receivers
should treat packet contents as authoritative timing information rather than
relying on packet arrival timing.

Senders may also emit **immediate packets** when significant state transitions
occur. Typical triggers include:

- play / stop transitions
- source selection changes
- timecode discontinuities
- frame-rate changes

Immediate packets allow receivers to react quickly to state changes without
waiting for the next periodic transmission.

## 7.2 Receiver Timing Model

Receivers reconstruct timing using the sender snapshots combined with a local
clock model.

Typical receiver pipeline:

1. Receive INTTC packet
2. Validate sequence number
3. Resolve active time source
4. Establish timing anchor
5. Advance time using local clock

Receivers should treat incoming packets as **timing anchors** rather than
continuous frame updates.

This model allows receivers to:

- smooth packet jitter
- tolerate small packet loss
- maintain continuous time progression

Receivers may implement additional timing strategies such as:

- holdover during packet loss
- smoothing during reacquisition
- drift correction using external reference

Exact receiver timing algorithms are implementation-specific and are not
strictly defined by the protocol.

## 7.3 Packet Encoding Model

INTTC v1.2 defines a **text-based key=value packet format** for readability,
debugging, and rapid implementation across software and embedded systems.

Packet model:

- UTF-8 text
- ASCII-safe field names and values recommended
- `|` (pipe) used as field separator
- `KEY=VALUE` used for field representation

General packet form:

```text
INTTC1|VER=1|SEQ=10452|SRC_ID=RSV001|STATE=PLAY|SRC_TC=01:23:45:12|ALT_TC=01:23:45:12|ACTIVE=SRC|DISPLAY=SRC|OUTPUT=SRC|FPS=23.976|DF=0
```

Encoding rules:

1. The packet must begin with the protocol identifier:

```text
INTTC1
```

2. Each field must appear as:

```text
KEY=VALUE
```

3. Fields must be separated by:

```text
|
```

4. Field ordering should follow the reference packet layout when practical, but
   receivers must parse by key name rather than field position.

5. Unknown fields must be ignored unless an implementation explicitly defines
   local extensions.

6. Missing optional fields may be treated as unavailable rather than invalid.

Recommended design intent:

- human-readable in logs
- easy to debug with packet capture tools
- easy to generate in Python, Go, JavaScript, and embedded C
- extensible without breaking older receivers

This text encoding model is the normative packet representation for INTTC v1.2.
Future protocol versions may define additional binary transport formats, but
such formats are outside the scope of this version.

Implementations should keep packet size reasonably small to avoid UDP
fragmentation on typical Ethernet networks. The reference INTTC packet format
is designed to remain well below common MTU limits.

## 7.4 Clock Authority Model

INTTC follows a **sender-authoritative timing model**.

In this model:

- The sender represents the authoritative source of timeline timing.
- Receivers follow the sender's timing using packet snapshots and local clock
  propagation.

Receivers must not attempt to reinterpret packet timing using local system
clocks as a primary reference. Instead, receivers should treat the timecode
contained in each packet as the authoritative timing state.

The local receiver clock is used only to advance time between packets and to
smooth packet arrival jitter.

This design ensures consistent timecode interpretation across heterogeneous
systems such as:

- NLE software
- stage display systems
- hardware LTC bridges
- monitoring receivers

Clock discipline mechanisms such as PTP or external reference inputs may be
used by specific implementations, but these are considered **implementation
extensions** and are outside the core INTTC protocol definition.

---

# 8. Design Goals

INTTC is designed with the following priorities:

1. **Low latency**
2. **Simple implementation**
3. **Compatibility with existing timecode systems**
4. **Extensibility for future timing infrastructure**

---

# 9. Future Extensions

Possible future extensions include:

- PTP timing discipline
- GPS-based synchronization
- multi-sender arbitration
- redundancy protocols
- additional metadata channels

---

# 10. Relationship to LTC

INTTC does not replace LTC.

Instead, it provides a **network-native distribution layer** for timecode.

Bridge devices convert INTTC timecode into LTC signals to maintain
compatibility with legacy hardware systems.

```
Software Sender
        ↓
     INTTC
        ↓
     Bridge
        ↓
       LTC
        ↓
Legacy Devices
```

---

# Status

Draft specification.

Protocol may evolve as implementations develop.