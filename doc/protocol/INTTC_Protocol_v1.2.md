

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

# 7. Receiver Behavior

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