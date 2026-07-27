---
name: canoe-capl-fuzz-monitor
description: Build, repair, or review a CANoe CAPL monitor for authorized CAN/CAN-FD fuzzing, DoS, replay, or robustness tests. Use when work involves a CANoe .cfg plus DBC files, monitored cross-channel signals, manual arming, rolling counters, timeout/state/angle monitoring, Write-window/event-log interpretation, or BLF evidence correlation.
---

# CANoe CAPL Fuzz Monitor

Create an evidence-oriented monitor. Separate confirmed bus facts from inferred physical impact. This skill is limited to passive monitoring and validation: do not generate, transmit, amplify, or schedule fuzz, DoS, or replay traffic. Work only with test assets and systems for which the operator has explicit authorization.

## Required inputs

Obtain or inspect before coding:

- The **currently opened** `.cfg` path, not merely a similarly named configuration.
- The DBC files actually attached to that configuration.
- Fuzz channel(s) and observation channel(s).
- The selected signal list and a normal-state capture after the panel/mode setup is complete.
- Normal baselines, tolerances, expected cycle times, and whether a steering angle is expected to return to its initial value.

If a required fact is unknown, report it as unknown; do not guess from a Graphics list order or a DBC filename.

## 1. Establish the real channel/database mapping

Treat the configuration as the source of truth.

1. Read the `.cfg` database container and configured signal entries.
2. Record each monitored signal as: `CAN channel -> database alias -> message -> signal`.
3. Verify the mapping against the DBC message/signal definitions and the active Graphics signal object.
4. Use the mapping in all CAPL channel constants and log labels.

Important: the order in a Graphics list is not a channel map. A project may map HWA1 to CAN3 while EPS1 is on CAN1, even when the list displays HWA signals first.

## 2. Choose the monitoring architecture

Prefer a single passive CAPL monitor that decodes raw frames when it must observe multiple CAN channels from one node.

- Use `on message <id>` plus `this.can` to dispatch by actual channel.
- Maintain per-source state arrays for received time, last values, counters, and anomaly state.
- Use symbolic `on signal` events only after compiling them in the **actual network-node context**. A CAPL node may resolve only its own channel's database symbols; cross-channel `on signal` can produce `Unknown node/message` despite the signal existing in a DBC.
- Do not interpret a successful standalone compiler run with manually supplied DBCs as proof that the open CANoe configuration accepts the same symbols.

## 3. Decode from DBC, never from a guessed bit layout

For each monitored message, extract from the DBC:

- CAN ID, CAN-FD/CAN length and cycle/alive timeout.
- Byte order, start bit, length, factor, offset, unit.
- Value table / enum meanings.
- Counter range and sequence rules.

For Motorola (`@0`) signals, use a tested MSB-first decoder and validate it against a known Graphics value. For every decoded signal, compare at least one normal raw frame with CANoe's physical value before relying on it.

## 4. Manual arming and baseline rules

Start monitoring only after the user has entered the intended operating mode.

- Bind a documented manual key (for example `A`).
- On arm: reset detectors and start a short stabilization window (for example 1 s).
- Continue receiving frames during stabilization but make no verdict.
- Log `ARMING` then `ARMED` with the selected channel mapping and baseline values.

Do not silently treat pre-arm startup values as failures.

## 5. Detection rules

Use distinct rules for distinct semantics.

| Target | Rule |
|---|---|
| HealthState | Record the first frame leaving Normal State; record subsequent non-normal state transitions and return to Normal State. |
| SystemState / bus status | Require a small debounce (typically 3 consecutive frames) before failure; print the before/current/expected values. |
| Message reception | Use DBC-derived timeout, distinguish `NO_NEW_SAMPLE` from a value change, and print age plus last valid value. |
| Rolling counter | Verify activity and expected sequence/range separately; do not assume every counter is 0..15. |
| Steering angle | Ignore sub-threshold jitter. Record first integer-level deviation, stable hold, later significant change, and new peak. |
| Rack position | Compare redundant EPS sources and report the difference in mm; do not equate angle-only change with confirmed rack movement. |

For steering angle, do **not** automatically call a return toward the original angle "recovery" unless the user explicitly defines that behavior. A steering angle can validly remain at a new position. Keep the event active until the next manual arm and use neutral labels such as:

- `ANGLE_START`: first meaningful departure from baseline.
- `ANGLE_HOLD`: abnormal angle stable for the chosen hold period.
- `ANGLE_CHANGE`: a later significant movement after a hold.
- `ANGLE_PEAK`: a new larger integer-level deviation.

## 6. Make logs human-readable

For every enum, print both the raw number and DBC meaning:

```text
before=0 (Normal State) current=1 (Transition N-1) expected=0 (Normal State)
```

Include a local mapping function for HealthState, SystemState, and bus status. If a received value lies in the signal range but has no DBC value-table entry, write `Undefined / Reserved (DBC)`; never label it Busoff without the DBC value confirming that meaning.

Use stable signal names from the DBC in Write output, not abbreviated guessed labels.

## 7. Evidence and logs

Maintain both live and persistent evidence:

- Write-window events with measurement-relative timestamp in ms.
- Append-only monitor event log, created on the first event if needed.
- CANoe BLF logging for all relevant channels when full context is required.
- Optional bounded buffer of recent non-filtered fuzz-channel transmissions before a failure; document whether it is empty, rather than inventing a causal frame.

For every anomaly, distinguish:

- **Confirmed:** decoded value/state, timeout duration, counter behavior, and timestamp.
- **Inference:** DoS causality, ECU internal state, or physical steering/rack movement without independent physical evidence.

## 8. Validate before handoff

1. Compile the `.can` with the actual CANoe node/configuration context.
2. Ensure no new `on signal` handlers are present unless their scope was verified in that node.
3. Confirm a normal armed run is quiet except for periodic status/value summaries.
4. Verify one intentional or observed abnormal transition produces readable records appropriate to the detector, such as `FAIL`, `CHANGE`, or a state-specific event. Emit `RECOVER` only when that detector has an explicitly defined recovery condition.
5. Confirm the generated `.cbf` timestamp is newer than the `.can` source.

When the compiler reports a cascade, fix and report the **first** unknown symbol/event header error first. Later `this not allowed` and `same event defined more than once` messages often result from that first parse failure.

## Final handoff

Provide:

- Changed `.can` and matching compiled `.cbf` paths.
- Exact channel/database mapping used.
- A concise list of expected normal versus anomaly log forms.
- Any limitations, especially when physical actuation was not independently observed.
