# Hardware-Based Drowsiness Detector

A real-time, hardware-only drowsiness detection system that detects prolonged eye closure and generates an audible alert. The system is built using discrete electronic components and digital logic without a microcontroller or software.

## Overview

Driver drowsiness can reduce alertness, reaction time, and decision-making ability, increasing the risk of accidents.

This project detects prolonged eye closure using an infrared (IR) sensor. The sensor signal is conditioned and processed using logic gates, a timer, and a counter. If the eye remains closed beyond the preset duration, the circuit activates a buzzer to alert the user.

The system is designed as a simple, low-cost, and practical hardware-based safety solution.

## Key Features

- Hardware-only implementation
- No microcontroller or programming
- IR-based eye activity detection
- Prolonged eye-closure detection
- NOT and AND logic processing
- NE555 timer-based timing
- 74LS90 decade counter
- Audible buzzer alert
- Manual reset
- Battery-powered prototype

## System Architecture

IR Sensor
    ↓
Signal Conditioning
    ↓
74HC04 NOT Gate
    ↓
74HC08 AND Gate
    ↓
NE555 Timer
    ↓
74LS90 Counter
    ↓
BC547 Transistor
    ↓
Buzzer Alert

## Working Principle

1. **IR Sensor**  
   Detects reflected infrared light from the eye and produces a signal corresponding to eye status.

2. **Signal Conditioning**  
   The sensor output is converted into a suitable logic signal for digital processing.

3. **74HC04 NOT Gate**  
   Inverts the sensor signal so that the required eye-closure logic level is obtained.

4. **74HC08 AND Gate**  
   Combines the eye-closure signal with the timing condition and helps reject short-duration blinks.

5. **NE555 Timer**  
   Generates timing pulses used to measure the duration of eye closure.

6. **74LS90 Counter**  
   Counts the timing pulses while the eye remains closed. A sustained closure beyond the preset threshold is treated as drowsiness.

7. **BC547 Transistor**  
   Acts as the driver for the alert circuit.

8. **Buzzer**  
   Generates an audible warning when drowsiness is detected.

9. **Manual Reset**  
   Resets the counter and prepares the system for another detection cycle.

## Detection Threshold

The system is designed to distinguish normal blinking from prolonged eye closure.

**Preset eye-closure threshold: approximately 5 seconds**

Short blinks are intended to be ignored, while sustained eye closure triggers the alert.

## Hardware Components

| Component | Function |
|---|---|
| IR Sensor | Eye activity detection |
| Comparator / Signal Conditioning | Converts sensor output to logic signal |
| 74HC04 | NOT gate |
| 74HC08 | AND gate |
| 74LS90 | Decade counter |
| NE555 | Timer and pulse generation |
| BC547 | Buzzer driver |
| Buzzer | Audible alert |
| Resistors | Timing and signal conditioning |
| Capacitors | Timing and filtering |
| Voltage Regulator | Provides regulated supply |
| 9V Battery | Power source |
| Reset Switch | Manual reset |
| Jumper Wires | Circuit connections |

## Circuit Diagram

![Circuit Diagram](Circuit/circuit-diagram.png)

## Hardware Prototype

![Hardware Prototype](Prototype/prototype.jpg)

## Testing

The prototype was tested under different operating conditions.

### Test Conditions

- IR sensor positioned approximately 2–3 cm from the eye
- Daylight conditions
- Indoor lighting
- Low-light conditions
- Different eye-closure durations
- Manual reset after detection

### Observations

- The IR sensor distinguished between open and closed eye conditions.
- Logic gates processed the detection signal.
- The counter tracked the duration of eye closure.
- Short blinks were intended to be filtered out.
- The buzzer produced an audible alert when the drowsiness condition was reached.
- The manual reset returned the system to its monitoring state.

## Advantages

- No programming required
- No microcontroller required
- Low-cost components
- Real-time response
- Simple circuit architecture
- Easy to assemble and maintain
- Suitable for battery-powered operation
- Useful for digital electronics education

## Applications

- Driver safety systems
- Vehicle safety applications
- Industrial safety
- Fatigue monitoring
- Educational electronics projects
- Hardware-based alert systems

## Limitations

The current prototype can be affected by sensor positioning and ambient lighting. Further calibration and improved filtering would be required for more robust operation across different users and environments.

## Future Improvements

- Improve IR sensor calibration for different users
- Reduce sensitivity to ambient light
- Improve signal filtering
- Use adaptive sensing
- Add wireless alerts
- Add data logging
- Add environmental compensation
- Develop a more compact PCB implementation

## Project Information

| Category | Details |
|---|---|
| Course | Logic Circuit Design |
| Domain | Digital Electronics / Hardware Systems |
| Implementation | Hardware-only |
| Microcontroller | None |
| Software / Code | None |
| Detection Method | IR eye-closure sensing |
| Alert | Audible buzzer |
| Prototype | Breadboard |

## Documentation

The detailed project report and presentation will be added to this repository.
