# Hardware-Based Drowsiness Detector

A hardware-only prototype that detects **sustained eye closure** as an indicator of possible drowsiness and triggers an audible alert — with no microcontroller, firmware, or software.

![Prototype](media/prototype.jpg)

> **Project status:** Working breadboard prototype. Validation is qualitative; this is not a safety-certified or clinically validated drowsiness detector.

## Overview

Driver drowsiness can reduce alertness and reaction time. This project explores a simple hardware approach: detect changes in reflected infrared light from the eye, process the signal with discrete logic, measure the duration of the closed-eye condition, and activate a buzzer when the selected threshold is reached.

The design was developed for **Logic Circuit Design** and demonstrates practical use of:

- IR sensing
- Digital logic gates
- 555 timer-based timing
- Decade counting
- Transistor switching
- Breadboard prototyping

## Key Characteristics

- **Hardware-only:** no microcontroller or programmable firmware
- **Detection:** IR-based eye-state sensing
- **Timing:** NE555-generated pulse train
- **Duration measurement:** 74LS90 decade counter
- **Alert:** BC547 transistor-driven buzzer
- **Reset:** manual reset switch
- **Prototype:** breadboard implementation

## System Architecture

```text
IR Sensor
    |
    v
Signal Conditioning / Comparator
    |
    v
74HC04 NOT Gate
    |
    v
74HC08 AND Gate
    |
    v
NE555 Timing Stage
    |
    v
74LS90 Decade Counter
    |
    v
BC547 Transistor Driver
    |
    v
Buzzer Alert

Manual Reset Switch
        |
        v
   Counter Reset
```

The intended signal path is:

1. The IR sensor detects changes in reflected infrared light associated with eye state.
2. A signal-conditioning stage converts the sensor output into a usable logic signal.
3. The **74HC04** inverts the signal so the required eye-closure state can be processed by the logic stage.
4. The **74HC08** gates the detection condition.
5. The **NE555** provides timing pulses.
6. The **74LS90** counts pulses while the eye-closure condition persists.
7. When the selected condition is reached, the **BC547** drives the buzzer.
8. A manual reset returns the circuit to its monitoring state.

> **Schematic note:** `media/circuit-diagram.png` documents the main logic, timing, counter, driver, and buzzer stages. The IR sensor/front-end is visible in the physical prototype but is not fully represented as a separate block in the supplied schematic.

## Hardware

| Component | Role |
|---|---|
| IR sensor module | Detects eye-state changes using reflected infrared light |
| Signal conditioning / comparator stage | Converts the sensor output into a logic-compatible signal |
| 74HC04 | Inverts the conditioned signal |
| 74HC08 | Gates the eye-closure condition |
| NE555 | Generates timing pulses |
| 74LS90 | Counts timing pulses to represent closure duration |
| BC547 | Drives the buzzer stage |
| Piezo buzzer | Audible warning |
| Resistors & capacitors | Timing, biasing, filtering, and signal shaping |
| Voltage regulator | Provides the regulated circuit supply |
| 9V battery | Prototype power source |
| Reset switch | Manually resets the detection cycle |
| Breadboard & jumper wires | Prototype interconnection |

## Detection Threshold

The project documentation uses an **approximately 5-second sustained eye-closure interval as the design target**.

```text
Short eye closure
       |
       v
  No alarm intended

Sustained closure
       |
       v
555 timing pulses
       |
       v
74LS90 counts duration
       |
       v
Threshold reached
       |
       v
Buzzer alert
```

The ~5-second value is a **design target**, not a specification independently validated with logged timing measurements.

## Prototype

The physical implementation was built on a breadboard. During the documented testing, the IR sensor was positioned approximately **2–3 cm from the eye**.

![Circuit Diagram](media/circuit-diagram.png)

![Breadboard Prototype](media/prototype.jpg)

## Testing

The documented prototype testing considered:

- Different eye-closure durations
- Daylight
- Indoor lighting
- Low-light conditions
- Manual reset between detection cycles

The reported observations were qualitative: the sensor provided a usable distinction between open- and closed-eye conditions, the logic/timing stages responded to the detected condition, and the buzzer produced an audible warning when the selected condition was reached.

No statistical accuracy study, logged trial dataset, or independently measured performance percentage is included in the project documentation.

## Limitations

This prototype should be treated as an educational hardware design, not as a production safety system.

Known limitations include:

- IR sensor placement and calibration can affect detection.
- Ambient lighting may influence sensor behavior.
- User-to-user differences can affect sensing.
- Breadboard construction is less mechanically robust than a finished PCB.
- The supplied documentation does not provide a complete statistical validation dataset.
- The exact regulator implementation is inconsistent across the original project materials and should be verified against the physical circuit before construction.
- The complete sensor/comparator front-end is not fully captured in the supplied schematic.
- The behaviour of the second AND-gate input and some reset/inter-blink logic details are not fully documented.

## Project Status

**Implementation:** Breadboard prototype  
**Architecture:** Hardware-only  
**Microcontroller:** None  
**Firmware:** None  
**Primary detection method:** IR-based eye-closure sensing  
**Alert mechanism:** Audible buzzer  
**Validation level:** Qualitative prototype testing  
**Safety certification:** None

## Documentation

The longer engineering report contains additional design details, testing notes, limitations, and known documentation inconsistencies.

- [`PROJECT_REPORT.md`](PROJECT_REPORT.md)

## Repository Structure

```text
hardware-drowsiness-detector/
├── README.md
├── PROJECT_REPORT.md
└── media/
    ├── circuit-diagram.png
    └── prototype.jpg
```

## Future Improvements

Potential next steps include:

- Calibrated IR sensing for different users
- Better rejection of ambient-light variation
- More clearly documented sensor and comparator circuitry
- Quantitative timing and detection experiments
- Logged test data and defined performance metrics
- PCB implementation
- Wireless alerting and data logging
- Environmental compensation

---

**Project type:** Academic hardware prototype  
**Domain:** Digital Electronics / Embedded Hardware Concepts  
**Focus:** Sensor interfacing, logic gates, timing, counting, and hardware alert generation
