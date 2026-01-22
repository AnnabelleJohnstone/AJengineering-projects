---
layout: default
title: DIY Smart Tracking Robot Car
---

# DIY Smart Tracking Robot Car

[← Back to Personal Projects]({{ '/personal-projects/' | relative_url }})

## Project Overview

A custom-built autonomous line-following robot car with obstacle avoidance capabilities. This project involved calculating electrical specifications, component selection, and circuit design to create a functional robotics platform for experimentation and learning.

![Robot Car Assembly](https://via.placeholder.com/800x400?text=Robot+Car+Assembly)

## Engineering Calculations & Component Selection

### Power System Design

**Motor Specifications:**
- 2× DC Geared Motors (3-6V operating range)
- Stall current: ~800mA per motor
- Normal operating current: ~200mA per motor

**Power Requirements Calculation:**

```
Total Current Draw:
- Motors (2× 200mA): 400mA
- IR Sensors (2× 20mA): 40mA
- Microcontroller: 50mA
- LED Indicators: 20mA
Total: 510mA nominal, 1.8A peak
```

**Battery Selection:**
Selected **4× AA batteries (6V)** based on:
- Voltage requirement: 6V matches motor optimal operating range
- Capacity: 2000mAh provides ~4 hours runtime at nominal load
- Cost-effective and easily replaceable

### Current Limiting Resistor Calculations

**LED Indicators:**
- Forward voltage (Vf): 2.0V (red LED)
- Desired current (If): 10mA
- Supply voltage (Vs): 6V

Using Ohm's Law:
```
R = (Vs - Vf) / If
R = (6V - 2V) / 0.01A
R = 400Ω
```
**Selected: 470Ω resistors** (nearest standard value, provides ~8.5mA)

**IR Sensor Current Limiting:**
- IR LED forward voltage: 1.2V
- Desired current: 20mA for optimal range
```
R = (6V - 1.2V) / 0.02A
R = 240Ω
```
**Selected: 220Ω resistors** (provides ~21.8mA, within acceptable range)

### Motor Driver Circuit

**L298N Motor Driver Selection:**
- Voltage range: 5-35V ✓
- Current rating: 2A per channel ✓
- Logic voltage: 5V (compatible with microcontroller) ✓

**PWM Frequency Calculation:**
For smooth motor control without audible whine:
```
f = 20kHz (above human hearing range)
Duty cycle range: 0-100% for speed control
```

## Circuit Design

### Schematic Overview

```
Power Distribution:
Battery (6V) → Switch → 
  ├─ Motor Driver (5V regulated) → Motors
  ├─ Voltage Regulator (5V) → Microcontroller
  └─ IR Sensors with current limiting resistors

Sensor Input:
IR Sensors → Digital pins (D2, D3)
Ultrasonic Sensor → Echo (D4), Trigger (D5)

Motor Control:
PWM outputs (D9, D10) → Motor Driver → Motors
Direction pins (D6, D7, D8, D11) → Motor Driver
```

## Key Features

- **Autonomous Line Following** - IR sensors detect track boundaries and adjust steering
- **Obstacle Avoidance** - Ultrasonic sensor measures distance and triggers avoidance maneuvers
- **Variable Speed Control** - PWM signals control motor speed for smooth turns
- **Battery Monitoring** - Voltage divider circuit monitors battery level
- **Modular Design** - Easy component swapping for experimentation

## Bill of Materials

| Component | Specification | Quantity | Purpose |
|-----------|--------------|----------|---------|
| DC Motors | 3-6V, 200RPM | 2 | Propulsion |
| L298N Driver | 2A dual H-bridge | 1 | Motor control |
| IR Sensors | Digital output | 2 | Line detection |
| Ultrasonic Sensor | HC-SR04 | 1 | Distance measurement |
| Resistors | 470Ω | 2 | LED current limiting |
| Resistors | 220Ω | 2 | IR sensor current limiting |
| AA Batteries | 1.5V | 4 | Power supply |
| Battery Holder | 4× AA | 1 | Power distribution |
| Wheels | 65mm diameter | 2 | Mobility |
| Chassis PCB | Green | 1 | Structural base |
| Jumper Wires | Various | 20+ | Connections |

## Assembly Process

1. **Power system verification** - Tested voltage rails before connecting components
2. **Sensor calibration** - Adjusted IR sensor height and sensitivity for optimal line detection
3. **Motor mounting** - Secured motors with proper alignment for straight tracking
4. **Wiring organization** - Used color-coded wires for easy troubleshooting
5. **Programming** - Implemented PID control algorithm for smooth line following

## Control Algorithm

**Line Following Logic:**
```
if (left_sensor == BLACK && right_sensor == WHITE):
    turn_left()
elif (left_sensor == WHITE && right_sensor == BLACK):
    turn_right()
elif (both_sensors == BLACK):
    move_forward()
else:
    stop()
```

**Obstacle Avoidance:**
```
if (distance < 15cm):
    stop()
    reverse(500ms)
    turn_right(90°)
    resume_line_following()
```

## Challenges & Solutions

**Challenge 1: Inconsistent line detection**
- *Problem:* IR sensors triggered incorrectly under varying lighting
- *Solution:* Added threshold tuning and calibration routine on startup

**Challenge 2: Motor speed mismatch**
- *Problem:* Car veered to one side despite identical PWM signals
- *Solution:* Implemented software calibration offsets to compensate for motor variations

**Challenge 3: Battery voltage drop**
- *Problem:* Performance degraded as batteries depleted
- *Solution:* Added voltage compensation in motor control algorithm

## Technical Skills Demonstrated

- ⚡ **Circuit Design** - Calculated component values and designed power distribution
- 🔧 **PCB Assembly** - Soldered components and organized wiring
- 💻 **Embedded Programming** - Wrote control algorithms and sensor interfaces
- 📊 **Problem Solving** - Debugged hardware and software issues systematically
- 🔋 **Power Management** - Optimized current draw for extended runtime

## Learning Outcomes

- Practical application of Ohm's Law and electrical engineering principles
- Understanding of motor control using H-bridges and PWM
- Experience with sensor integration and calibration
- Hands-on debugging of electromechanical systems
- Foundation for future robotics projects

## Future Improvements

- 🔄 Add Bluetooth module for remote control capability
- 📡 Implement wireless telemetry for real-time debugging
- 🧠 Upgrade to more sophisticated path planning algorithms
- 🔋 Switch to rechargeable LiPo battery with charging circuit
- 📱 Create mobile app for parameter tuning and monitoring

## Video Demo

*[Video demonstration would go here]*

## Source Files

- Circuit schematic (PDF)
- Control code (Arduino sketch)
- Assembly instructions
- Calibration procedures

---

[← Back to Personal Projects]({{ '/personal-projects/' | relative_url }})
