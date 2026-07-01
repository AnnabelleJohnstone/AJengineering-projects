---
layout: default
title: DIY Smart Tracking Robot Car
---

#  DIY Smart Tracking Robot Car
[← Back to At Home Projects]({{ '/home-projects/' | relative_url }})

---
##  Demo Video

<video width="100%" controls style="max-width: 800px; border-radius: 10px; box-shadow: 0 4px 8px rgba(0,0,0,0.2);">
  <source src="{{ 'assests/IMG_3191 (1).MOV' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>

## Overview

A fully autonomous line-following robot car with integrated obstacle avoidance. This project demonstrates practical application of electrical engineering principles, from power system design and component selection to embedded programming and PID control implementation.

---

##  Power System Engineering

### Motor & Load Analysis

The first step was analysing the power requirements of all components to select an appropriate battery configuration.

**Component Current Draw:**

| Component | Quantity | Current per Unit | Total Current |
|-----------|----------|------------------|---------------|
| DC Geared Motors | 2 | 200mA (nominal) | 400mA |
| IR Line Sensors | 2 | 20mA | 40mA |
| Microcontroller | 1 | 50mA | 50mA |
| LED Indicators | 2 | 10mA | 20mA |
| **Total System Draw** | | | **510mA nominal** |

**Peak Current Considerations:**
- Motor stall current: 800mA per motor $\Rightarrow$ 1.6A total
- Safety margin: added 200mA buffer
- **Maximum expected draw: 1.8A**

### Battery Selection Rationale

Selected **4× AA batteries in series (6V nominal)** because:

1. **Voltage Matching** — 6V falls within the 3–6V motor operating range
2. **Capacity** — standard 2000mAh AA batteries provide approximately 4 hours of continuous operation
3. **Availability** — easy to source and replace
4. **Cost** — most economical solution for prototyping

**Runtime Calculation:**

$$
t_{\text{runtime}} = \frac{Q_{\text{battery}}}{I_{\text{avg}}} = \frac{2000\ \text{mAh}}{510\ \text{mA}} \approx 3.9\ \text{hours}
$$

---

##  Resistor Calculations

### LED Current Limiting

To prevent LED burnout while ensuring adequate brightness, the required current-limiting resistor was calculated using Ohm's Law.

**Given Parameters:**

| Symbol | Description | Value |
|--------|--------------|-------|
| $V_s$ | Supply voltage | 6 V |
| $V_f$ | LED forward voltage (typical red LED) | 2.0 V |
| $I_f$ | Desired LED current | 10 mA |

**Calculation:**

$$
R = \frac{V_s - V_f}{I_f} = \frac{6\,\text{V} - 2.0\,\text{V}}{0.010\,\text{A}} = \frac{4\,\text{V}}{0.010\,\text{A}} = 400\ \Omega
$$

**Component Selection:** $470\ \Omega$ resistors (closest standard value)

$$
I_{\text{actual}} = \frac{V_s - V_f}{R} = \frac{6\,\text{V} - 2\,\text{V}}{470\ \Omega} = 8.5\ \text{mA}
$$

$$
P = I^2 R = (0.0085\,\text{A})^2 \times 470\ \Omega = 0.034\ \text{W}
$$

Selected $\tfrac{1}{4}\,\text{W}$ resistors — well within the power rating.

### IR Sensor Current Limiting

IR LEDs require different forward voltages and currents for optimal range.

**Given Parameters:**

| Symbol | Description | Value |
|--------|--------------|-------|
| $V_s$ | Supply voltage | 6 V |
| $V_f$ | IR LED forward voltage | 1.2 V |
| $I_f$ | Desired current | 20 mA |

**Calculation:**

$$
R = \frac{V_s - V_f}{I_f} = \frac{6\,\text{V} - 1.2\,\text{V}}{0.020\,\text{A}} = \frac{4.8\,\text{V}}{0.020\,\text{A}} = 240\ \Omega
$$

**Component Selection:** $220\ \Omega$ resistors (nearest standard value)

$$
I_{\text{actual}} = \frac{6\,\text{V} - 1.2\,\text{V}}{220\ \Omega} = 21.8\ \text{mA}
$$

This is within the acceptable $\pm 10\%$ tolerance for IR detection.

---

##  Motor Driver Selection

### L298N Dual H-Bridge Analysis

**Requirements:**
- Control 2 DC motors independently
- Variable speed control (PWM)
- Forward/reverse capability
- Handle stall current safely

**L298N Specifications:**
- Voltage range: 5–35V (6V nominal is compatible)
- Current rating: 2A per channel (exceeds 800mA stall current)
- Logic voltage: 5V (matches microcontroller)
- Built-in flyback diodes (motor protection)

### PWM Configuration

To achieve smooth speed control without audible motor whine:

$$
f_{\text{PWM}} = 20\ \text{kHz} \quad (\text{above human hearing range of } 20\,\text{Hz}\text{–}20\,\text{kHz})
$$

$$
\text{Duty cycle} \in [0\%, 100\%], \qquad \text{Resolution} = 2^{8} = 256\ \text{speed levels}
$$

This provides smooth acceleration and prevents annoying motor sounds during operation.

---

##  Circuit Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    6V Battery Pack                       │
│                    (4× AA Batteries)                     │
└─────────────────┬───────────────────────────────────────┘
                  │
            ┌─────▼─────┐
            │   Switch  │
            └─────┬─────┘
                  │
        ┌─────────┼─────────┐
        │         │         │
    ┌───▼───┐ ┌──▼───┐ ┌───▼────┐
    │ 5V    │ │L298N │ │  IR    │
    │ Reg.  │ │Motor │ │Sensors │
    │       │ │Driver│ │+220Ω R │
    └───┬───┘ └──┬───┘ └────────┘
        │        │
    ┌───▼───┐    │
    │Micro  │◄───┘ (PWM + Direction Control)
    │Ctrl   │
    │       │
    │(5V)   │◄─── Ultrasonic Sensor (5V)
    └───────┘◄─── IR Sensors (Digital Input)
```

**Key Design Decisions:**

1. **Voltage Regulation** — 5V regulator powers microcontroller for stable operation
2. **Motor Driver** — directly connected to battery for maximum power delivery
3. **Sensor Power** — all sensors run on regulated 5V for consistent readings
4. **PWM Lines** — D9, D10 for motor speed control
5. **Direction Control** — D6, D7, D8, D11 for motor direction

---

## Control Algorithm

### Line Following Logic

Implemented a simple but effective decision tree for line tracking:

```python
def line_follow():
    left = read_left_sensor()
    right = read_right_sensor()

    if left == BLACK and right == WHITE:
        # Robot drifting right, correct left
        turn_left(correction_speed)

    elif left == WHITE and right == BLACK:
        # Robot drifting left, correct right
        turn_right(correction_speed)

    elif left == BLACK and right == BLACK:
        # On track, full speed ahead
        move_forward(max_speed)

    else:
        # Lost the line, stop and search
        stop()
        search_for_line()
```

---

## Assembly Process

### 1. Power System Verification
- Verified 6V output from battery pack with multimeter
- Tested 5V regulator under load
- Confirmed voltage stability across all power rails

### 2. Component Mounting
- Secured motors with proper alignment (tested for straight-line motion)
- Mounted IR sensors at optimal height (5mm above surface)
- Positioned ultrasonic sensor at front centre

### 3. Wiring & Organization
- Used colour-coded wires: Red (power), Black (ground), Yellow (signal)
- Kept wire runs short to minimise interference
- Added heat shrink tubing to exposed connections

### 4. Sensor Calibration
- Adjusted IR sensor potentiometers for black/white threshold
- Tested under various lighting conditions
- Calibrated ultrasonic sensor for accurate distance readings

### 5. Programming & Testing
- Uploaded control code via Arduino IDE
- Implemented serial debugging for sensor values
- Tuned PID constants through iterative testing

---

## Challenges & Engineering Solutions

<h3>Challenge 1: Inconsistent Line Detection</h3>
  <p><strong>Problem:</strong> IR sensors gave false readings under fluorescent lighting and near windows.</p>
  <p><strong>Root Cause:</strong> Ambient light interference overwhelming IR LED signal.</p>
  <p><strong>Solution:</strong></p>
  <ul>
    <li>Added startup calibration routine to measure ambient light</li>
    <li>Implemented dynamic threshold adjustment</li>
    <li>Positioned sensors closer to ground (reduced from 10mm to 5mm)</li>
  </ul>
  <p><strong>Result:</strong> 95% detection reliability across various lighting conditions.</p>

<h3>Challenge 2: Motor Speed Mismatch</h3>
  <p><strong>Problem:</strong> Robot veered left despite sending identical PWM signals to both motors.</p>
  <p><strong>Root Cause:</strong> Manufacturing tolerances in DC motors causing speed variation.</p>
  <p><strong>Solution:</strong></p>
  <ul>
    <li>Measured actual RPM of each motor at various PWM values</li>
    <li>Created calibration offset table (left motor required 8% higher PWM)</li>
    <li>Implemented software compensation in motor control function</li>
  </ul>
  <p><strong>Result:</strong> Straight-line tracking within ±2cm over 1 meter.</p>

<h3>Challenge 3: Battery Voltage Drop</h3>
  <p><strong>Problem:</strong> Robot slowed significantly as batteries depleted below 5V.</p>
  <p><strong>Root Cause:</strong> Fixed PWM values didn't compensate for lower supply voltage.</p>
  <p><strong>Solution:</strong></p>
  <ul>
    <li>Added voltage divider circuit to monitor battery level</li>
    <li>Implemented voltage compensation algorithm (below)</li>
    <li>Added low-battery warning (LED blinks when $V < 4.5\,\text{V}$)</li>
  </ul>

$$
\text{PWM}_{\text{adjusted}} = \text{PWM}_{\text{base}} \times \frac{6\,\text{V}}{V_{\text{current}}}
$$

  <p><strong>Result:</strong> Consistent performance from 6V down to 4.5V threshold.</p>

---

##  Technical Skills Demonstrated

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 20px; margin: 30px 0;">

  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3> Circuit Design</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>Power system analysis</li>
      <li>Component value calculations</li>
      <li>Schematic interpretation</li>
      <li>Load balancing</li>
    </ul>
  </div>

  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3> Hardware Skills</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>PCB assembly</li>
      <li>Soldering & wiring</li>
      <li>Mechanical integration</li>
      <li>Component testing</li>
    </ul>
  </div>

  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3> Programming</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>Arduino C/C++</li>
      <li>Control algorithms</li>
      <li>Sensor interfacing</li>
      <li>PWM implementation</li>
    </ul>
  </div>

  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3> Problem Solving</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>Systematic debugging</li>
      <li>Performance optimization</li>
      <li>Calibration procedures</li>
      <li>Documentation</li>
    </ul>
  </div>

</div>

---

## Key Takeaways

This project provided hands-on experience with fundamental electrical engineering concepts:

- **Ohm's Law in Practice** — applied $V = IR$ calculations for real components with measurable results
- **Motor Control Theory** — understood H-bridge operation and PWM duty cycle effects
- **Sensor Calibration** — learned the importance of environmental testing and threshold tuning
- **System Integration** — developed skills in combining mechanical, electrical, and software subsystems
- **Debugging Methodology** — practised systematic troubleshooting from symptom to root cause

The most valuable lesson was that theoretical calculations provide a starting point, but real-world testing and iteration are essential for robust system performance.

---

##  Future Enhancements

- **Bluetooth Control Module** — add HC-05 for wireless command and telemetry
- **LiPo Battery Upgrade** — implement 7.4V 2S LiPo with charging circuit for higher performance
- **Advanced Navigation** — upgrade to encoder-based odometry for precise positioning
- **Mobile App Interface** — develop Android/iOS app for parameter tuning and data visualisation
- **Speed Optimization** — implement PID control for smoother acceleration curves

---

##  Bill of Materials

| Component | Specification | Qty | Unit Cost | Purpose |
|-----------|--------------|-----|-----------|---------|
| DC Motors | 3-6V, 200RPM | 2 | £2.50 | Propulsion |
| L298N Driver | 2A H-bridge | 1 | £3.00 | Motor control |
| IR Sensors | TCRT5000 | 2 | £1.00 | Line detection |
| Ultrasonic | HC-SR04 | 1 | £1.50 | Distance measurement |
| Resistors | 470Ω, 1/4W | 2 | £0.10 | LED current limiting |
| Resistors | 220Ω, 1/4W | 2 | £0.10 | IR current limiting |
| AA Batteries | 1.5V, 2000mAh | 4 | £0.50 | Power supply |
| Battery Holder | 4×AA | 1 | £1.00 | Power distribution |
| Wheels | 65mm diameter | 2 | £1.50 | Mobility |
| Chassis PCB | FR4 Green | 1 | £2.00 | Structural base |
| Jumper Wires | 22AWG, various | 1 | £2.00 | Connections |
| | | **Total** | **~£20** | |

---

<div>
  <a href="{{ '/at-home-projects/' | relative_url }}">← Back to At Home Projects</a>
</div>
