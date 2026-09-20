# Technical Report — Hardware-Based Drowsiness Detector

This document expands on the README with full design rationale, signal-level detail, and an explicit accounting of what is verified, what is an assumption, and what is unsupported in the source material (original project report, presentation, circuit diagram, and breadboard photo). Where sources conflict, both versions are recorded rather than one being silently chosen.

---

## 1. Project Overview

A circuit that detects prolonged eye closure — treated as an indicator of possible drowsiness — using an IR sensor, discrete logic gates, a 555 timer, a decade counter, and a transistor-driven buzzer. No microcontroller or software is used anywhere in the design. This report documents the circuit as it is described and shown across the source materials, including the points where those materials disagree with each other.

## 2. Problem Statement

Fatigue reduces alertness, reaction time, and decision-making, and is described in the source report as a contributing factor to accidents in transportation and industrial settings. Camera- or software-based drowsiness detection systems address this but typically require a microcontroller or SBC, associated firmware development, and draw more power — a mismatch where simplicity, low part count, or minimal power draw are the priority.

## 3. Objectives

- Detect prolonged eye closure beyond a preset duration, as distinct from routine blinking
- Trigger a buzzer alert on detection
- Build the system entirely from standard digital ICs — no programming
- Target a low-power, low-cost, breadboard-reproducible design

## 4. System Architecture & Signal Flow

```
IR sensor → Signal conditioning/comparator → NOT (74HC04) → AND (74HC08)
                                                                  │
                                          NE555 (astable, free-running clock)
                                                                  │
                                          (gated pulses while AND condition holds)
                                                                  ▼
                                          74LS90 decade counter (accumulates pulses)
                                                                  │
                                                    (counter reaches trip state)
                                                                  ▼
                                          BC547 transistor → Buzzer (alert)

Manual reset switch → clears 74LS90 → silences buzzer → re-arms system
Power: 9V battery → voltage regulator (identity unresolved, see §11) → logic ICs
```

This reflects the design intent as described in the source report: the sensor's analog output is conditioned into a clean logic level, inverted so "eyes closed" reads HIGH, gated to reject short blinks, and accumulated in the counter only while closure is sustained.

**Confirmed from the schematic (`media/circuit-diagram.png`):** the NOT gate, AND gate, 555 astable timer with its R1/C1/C2 network, 74LS90 counter with reset lines, BC547 transistor, buzzer, and a voltage regulator block.

**Not shown in the schematic:** the IR sensor and any discrete comparator stage. These appear only on the physical breadboard (`media/prototype.jpg`). The schematic and the physical build are not fully in sync — treat the schematic as documenting the logic/timing/alert portion only.

## 5. Bill of Materials — Components & Roles

| Component | Designator (schematic) | Role | Status |
|---|---|---|---|
| 9V battery | B1 | Power source | Confirmed (schematic + photo) |
| IR sensor module | — | Senses eye open/closed via reflected IR | Confirmed present in photo; absent from schematic |
| Signal conditioning / comparator | — | Converts sensor output to a digital level | Described in report only — not shown separately in schematic or presentation BOM |
| 74HC04 NOT gate | U2:A | Inverts sensor signal | Confirmed (schematic, report, presentation) |
| 74HC08 AND gate | U3:A | Gates the closed-eye signal | Confirmed component; second input source undocumented |
| NE555 timer (astable) | U4 | Clock source; R1 = 100 kΩ, C1 = 10 µF, C2 = 10 nF | Confirmed (schematic) |
| 74LS90 decade counter | U5 | Counts gated clock pulses | Confirmed (schematic, report, presentation) |
| BC547 transistor | Q1 | Buzzer driver | Confirmed (schematic, presentation) |
| Piezo buzzer | BUZ1 | Audible alert | Confirmed (schematic, presentation, photo) |
| Voltage regulator | U1 | Regulates supply rail | Present in schematic; **identity/output voltage disputed** — see §11 |
| Manual reset switch | — | Clears counter, silences buzzer | Described in report; visible component in photo |
| Power switch | — | Circuit on/off | Described in report ("W switch" in original BOM); visible in photo |
| R2 | R2 | 10 kΩ, in the 555-to-counter clock path | Confirmed (schematic) |
| Resistors / capacitors | R1, C1, C2 | Timing/support | Confirmed (schematic) |

## 6. Circuit Design

The timing stage (U4, NE555) is configured in astable mode using R1 (100 kΩ), C1 (10 µF), and C2 (10 nF), producing a continuous clock pulse train. This feeds, through R2 (10 kΩ), into the clock input of the 74LS90 (U5). The counter's reset lines (R0(1), R0(2), R9(1), R9(2)) tie to the manual reset switch. Counter output Q3 drives the base of Q1 (BC547), which switches the buzzer.

The 555's actual oscillation frequency — and therefore the real time-to-alarm this specific R1/C1/C2 network produces — is not calculated or measured anywhere in the source material. The commonly cited "~5 second" threshold (§8) should be treated as a design target until it's recalculated from these component values and confirmed by a timed test.

## 7. Working Principle & Detection Logic

1. **Sensing** — the IR sensor monitors reflected IR intensity from the eye. Per the source report: higher reflection (eye open) → high output; lower reflection (eye closed) → low output.
2. **Conditioning** — the analog output is converted to a clean digital level.
3. **Inversion** — the NOT gate flips the signal so eye-closed reads logic HIGH.
4. **Gating** — the AND gate combines the closed-eye signal with a second input described in the report only as a "timing enable" signal; what generates that second input is not specified anywhere in the source material.
5. **Clocking** — the 555 free-runs continuously.
6. **Counting** — while the gated condition holds, pulses accumulate in the 74LS90. Whether the counter also resets automatically between separate short blinks (versus only via manual reset after a full alert) is not documented.
7. **Alerting** — reaching the counter's trip state switches on the BC547 transistor, driving the buzzer.
8. **Resetting** — the buzzer and counter remain latched until the manual reset switch is pressed.

## 8. Hardware Implementation

Built on a single breadboard (`media/prototype.jpg`): the 9V battery and IR sensor module are wired in externally, with the logic ICs, timer, counter, and transistor populated on the board, and separate power and reset switches wired off-board. The "~5 second" closure threshold referenced in the source report is a stated design target for this build, not a value confirmed against the assembled timing network.

## 9. Testing and Validation

Per the source report, testing covered:
- Multiple lighting conditions: daylight, indoor, low-light
- IR sensor placement at roughly 2–3 cm from the eye
- Manually simulated eye closures of varying duration
- Manual reset used between trials

This is a functional, qualitative validation exercise. No trial log, sample size, or measurement methodology for a quantified result is present in the source material.

## 10. Results & Observations

Within the qualitative testing described in the report: the IR sensor gave a stable distinction between open and closed eye states; the logic stage responded promptly; the counter appeared to track sustained closure rather than triggering on brief blinks; the buzzer alert and manual reset both functioned as described.

**No accuracy percentage is reported here.** The source academic report's conclusion states "over 95% accuracy in practical conditions," but no trial log, sample size, or defined accuracy metric appears anywhere in that same document to support the figure. It is not carried into this repository's documentation.

## 11. Known Issues & Open Questions

Each item below is a genuine conflict or gap across the circuit diagram, report text, presentation slides, and photo. They're recorded here rather than resolved by choosing one source over another — only physical inspection/testing of the actual hardware can settle most of them.

1. **Voltage regulator identity conflicts.** Schematic and presentation both label the regulator **7812**. The report's component description instead refers to a **7805**, and its testing section states the system "was powered by a regulated 5V supply." A 9V battery cannot supply a 7812 with the headroom (~14V+ typical) needed to regulate to 12V, and 5V also matches the 74LS90's actual rated supply (74LS-series TTL has a low absolute maximum — a 12V rail risks damaging it). Neither the 7812 nor the 7805 identity is confirmed as the actual built hardware from the available material. **Do not state that either part is definitively what was used** — describe it generically as "voltage regulator" until the physical build is inspected and the output rail is measured.
2. **Presentation abstract references components not used elsewhere.** The abstract text mentions "logic gates, flip-flops, and a 74HC174 counter" — no flip-flops appear in any BOM, schematic, or other slide, and every other source (schematic, components slides, report) uses a 74LS90, not a 74HC174 (which is a hex D flip-flop, not a counter). Likely leftover/incorrect boilerplate text.
3. **Unsupported accuracy claim.** "Over 95% accuracy in practical conditions" (report conclusion) has no underlying trial dataset, sample count, or defined metric anywhere in the source material.
4. **Comparator/op-amp stage undocumented in the BOM/schematic.** The report's methodology describes a dedicated comparator (op-amp) stage; no such component appears in the presentation's component-list slides or the schematic. It's possible this function is integrated into the IR sensor module itself (the module in the photo resembles designs with an onboard adjustment potentiometer), but this isn't confirmed anywhere in the source material.
5. **Schematic omits the sensor front-end.** No IR sensor or comparator block appears in the Proteus diagram — it begins at the NOT gate. The physical prototype clearly has a sensor wired in.
6. **AND gate's second input is undocumented.** The report describes it only as a "timing enable" signal without specifying its source; the schematic doesn't label it either.
7. **Inter-blink counter reset behavior is unspecified.** It's not documented whether the 74LS90 resets automatically between individual short blinks (each time the gated condition drops) or only via the manual reset after a full alarm — this affects whether several closely-spaced blinks could accumulate toward a false trigger.
8. **The ~5 second threshold isn't derived or measured.** It's stated as a target value in the report, not calculated from the actual R1/C1/C2 values of the 555 or confirmed by a timed test.

## 12. Limitations

- Ambient lighting sensitivity is noted directly in the source material as an area for improvement
- Sensor calibration may vary by user / eye characteristics
- No wireless alerting, data logging, or environmental compensation in the current build
- Power supply chain unresolved (§11, item 1)
- No quantified accuracy or timing data exists for this build

## 13. Design Decisions & Trade-offs

- **Hardware-only over microcontroller-based**: avoids firmware development and debugging at the cost of losing the flexibility a software-adjustable threshold or per-user calibration routine would offer. The source material states this approach as intended to be lower-power and lower-cost than a microcontroller-based equivalent; that comparison is a stated design goal, not a measurement taken on this build.
- **Pulse-counted duration measurement (555 + 74LS90) over an analog RC timer**: gives a discrete, IC-adjustable threshold, at the cost of an undocumented dependency on the AND gate's second input and undocumented inter-blink reset behavior (§11, items 6–7).
- **IR reflectance sensing over camera-based sensing**: simpler and lower part count, at the cost of higher sensitivity to ambient light and sensor placement, as noted directly in the source material.

## 14. Future Improvements

- Resolve the voltage regulator/supply discrepancy by inspecting and measuring the actual physical build
- Improve sensor calibration across users and eye types
- Reduce ambient-light sensitivity; consider adaptive sensor filtering
- Add wireless alerting and data logging
- Add environmental compensation
- Re-export a complete schematic that includes the IR sensor/comparator front-end and labels the AND gate's second input
- Run a quantified accuracy/timing test with a defined trial count and logged results

## 15. Applications

Vehicles, factory/industrial safety monitoring, standalone safety devices, and electronics/digital-logic education, as described in the source material. Not established here as safety-certified or production-ready.

## 16. Lessons Learned

Building a multi-stage detection and timing system from discrete logic — gating, pulse counting, duration measurement — without a microcontroller required tracing signal states across ICs directly rather than relying on debug output, reinforcing fundamentals of digital logic timing and cross-family IC interfacing (HC/LS/discrete). Auditing this project also surfaced how easily documentation can drift out of sync across a schematic, a report, and a slide deck on the same build — keeping a single, physically-verified source of truth is itself a practical lesson from this project.

## 17. Project Status

Working breadboard prototype, exercised qualitatively across multiple lighting conditions per the source report. Not built to a PCB, not field-tested in a vehicle, and not safety-certified. The items in §11 should be resolved with physical measurement before further hardening or deployment.

## 18. References

- NE555 Timer IC — datasheet reference (astable configuration)
- 74HC04 NOT Gate — Texas Instruments datasheet
- 74HC08 AND Gate — Texas Instruments datasheet
- 74LS90 Decade Counter — Texas Instruments datasheet
- BC547 Transistor — Fairchild Semiconductor datasheet
- Original project report and presentation — see `docs/`
