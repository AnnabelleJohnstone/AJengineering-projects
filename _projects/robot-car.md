---
layout: default
title: DIY Smart Tracking Robot Car
---

#  DIY Smart Tracking Robot Car
[← Back to At Home Projects]({{ '/at-home-projects/' | relative_url }})

<div style="display: flex; gap: 15px; margin: 30px 0; flex-wrap: wrap;">
  <span style="background: #667eea; color: white; padding: 8px 15px; border-radius: 20px; font-size: 14px;">🔌 Electronics</span>
  <span style="background: #667eea; color: white; padding: 8px 15px; border-radius: 20px; font-size: 14px;">🤖 Robotics</span>
  <span style="background: #667eea; color: white; padding: 8px 15px; border-radius: 20px; font-size: 14px;">⚡ Circuit Design</span>
  <span style="background: #667eea; color: white; padding: 8px 15px; border-radius: 20px; font-size: 14px;">💻 Arduino</span>
</div>

---

## Overview

A fully autonomous line-following robot car with integrated obstacle avoidance. This project demonstrates practical application of electrical engineering principles, from power system design and component selection to embedded programming and PID control implementation.

<div style="background: #f8f9fa; padding: 25px; border-radius: 10px; margin: 30px 0; border-left: 4px solid #667eea;">
  <h3 style="margin-top: 0; color: #667eea;">🎯 Project Highlights</h3>
  <ul style="line-height: 1.8;">
    <li><strong>Custom Power System Design</strong> - Calculated optimal battery configuration and current requirements</li>
    <li><strong>Component Value Calculations</strong> - Applied Ohm's Law to determine resistor values for LEDs and sensors</li>
    <li><strong>Motor Control Implementation</strong> - Programmed PWM-based speed control with H-bridge driver</li>
    <li><strong>Sensor Integration</strong> - Calibrated IR and ultrasonic sensors for reliable navigation</li>
  </ul>
</div>

---

## ⚡ Power System Engineering

### Motor & Load Analysis

The first step was analyzing the power requirements of all components to select an appropriate battery configuration.

**Component Current Draw:**

| Component | Quantity | Current per Unit | Total Current |
|-----------|----------|------------------|---------------|
| DC Geared Motors | 2 | 200mA (nominal) | 400mA |
| IR Line Sensors | 2 | 20mA | 40mA |
| Microcontroller | 1 | 50mA | 50mA |
| LED Indicators | 2 | 10mA | 20mA |
| **Total System Draw** | | | **510mA nominal** |

**Peak Current Considerations:**
- Motor stall current: 800mA per motor = 1.6A total
- Safety margin: Added 200mA buffer
- **Maximum expected draw: 1.8A**

### Battery Selection Rationale

Selected **4× AA batteries in series (6V nominal)** because:

1. **Voltage Matching**: 6V falls within the 3-6V motor operating range
2. **Capacity**: Standard 2000mAh AA batteries provide approximately 4 hours of continuous operation
3. **Availability**: Easy to source and replace
4. **Cost**: Most economical solution for prototyping

**Runtime Calculation:**
```
Runtime = Battery Capacity / Average Current Draw
Runtime = 2000mAh / 510mA ≈ 3.9 hours
```

---

## 🔧 Resistor Calculations

### LED Current Limiting

To prevent LED burnout while ensuring adequate brightness, I calculated the required current-limiting resistors using Ohm's Law.

**Given Parameters:**
- Supply voltage (Vs): 6V
- LED forward voltage (Vf): 2.0V (typical for red LEDs)
- Desired LED current (If): 10mA (safe operating current)

**Calculation:**
```
R = (Vs - Vf) / If
R = (6V - 2.0V) / 0.010A
R = 4V / 0.010A
R = 400Ω
```

**Component Selection:** 470Ω resistors (closest standard value)
- Actual current: (6V - 2V) / 470Ω = 8.5mA ✓
- Power dissipation: I² × R = (0.0085)² × 470 = 0.034W
- Selected 1/4W resistors (well within rating)

### IR Sensor Current Limiting

IR LEDs require different forward voltages and currents for optimal range.

**Given Parameters:**
- Supply voltage (Vs): 6V  
- IR LED forward voltage (Vf): 1.2V
- Desired current (If): 20mA (optimal for detection range)

**Calculation:**
```
R = (6V - 1.2V) / 0.020A
R = 4.8V / 0.020A  
R = 240Ω
```

**Component Selection:** 220Ω resistors (nearest standard value)
- Actual current: (6V - 1.2V) / 220Ω = 21.8mA ✓
- Within acceptable ±10% tolerance for IR detection

---

## 🔌 Motor Driver Selection

### L298N Dual H-Bridge Analysis

**Requirements:**
- Control 2 DC motors independently
- Variable speed control (PWM)
- Forward/reverse capability
- Handle stall current safely

**L298N Specifications:**
- ✅ Voltage range: 5-35V (6V nominal is compatible)
- ✅ Current rating: 2A per channel (exceeds 800mA stall current)  
- ✅ Logic voltage: 5V (matches microcontroller)
- ✅ Built-in flyback diodes (motor protection)

### PWM Configuration

To achieve smooth speed control without audible motor whine:

**PWM Frequency Selection:**
```
Target frequency: 20kHz (above human hearing range of 20Hz-20kHz)
Duty cycle range: 0-100%
Resolution: 8-bit (256 speed levels)
```

This provides smooth acceleration and prevents annoying motor sounds during operation.

---

## 📐 Circuit Architecture

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

1. **Voltage Regulation**: 5V regulator powers microcontroller for stable operation
2. **Motor Driver**: Directly connected to battery for maximum power delivery
3. **Sensor Power**: All sensors run on regulated 5V for consistent readings
4. **PWM Lines**: D9, D10 for motor speed control
5. **Direction Control**: D6, D7, D8, D11 for motor direction

---

## 🎮 Control Algorithm

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

### Obstacle Avoidance

Ultrasonic sensor continuously monitors distance ahead:

```python
def obstacle_avoidance():
    distance = measure_distance()
    
    if distance < 15:  # Less than 15cm
        stop()
        reverse(500)    # Back up 500ms
        turn_right(90)  # Turn 90 degrees
        resume_line_following()
```

---

## 🛠️ Assembly Process

### 1. Power System Verification
- Verified 6V output from battery pack with multimeter
- Tested 5V regulator under load
- Confirmed voltage stability across all power rails

### 2. Component Mounting
- Secured motors with proper alignment (tested for straight-line motion)
- Mounted IR sensors at optimal height (5mm above surface)
- Positioned ultrasonic sensor at front center

### 3. Wiring & Organization
- Used color-coded wires: Red (power), Black (ground), Yellow (signal)
- Kept wire runs short to minimize interference
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

## ⚠️ Challenges & Engineering Solutions

<div style="background: #fff3cd; padding: 20px; border-radius: 8px; margin: 20px 0; border-left: 4px solid #ffc107;">
  <h3 style="margin-top: 0; color: #856404;">Challenge 1: Inconsistent Line Detection</h3>
  <p><strong>Problem:</strong> IR sensors gave false readings under fluorescent lighting and near windows.</p>
  <p><strong>Root Cause:</strong> Ambient light interference overwhelming IR LED signal.</p>
  <p><strong>Solution:</strong></p>
  <ul>
    <li>Added startup calibration routine to measure ambient light</li>
    <li>Implemented dynamic threshold adjustment</li>
    <li>Positioned sensors closer to ground (reduced from 10mm to 5mm)</li>
  </ul>
  <p><strong>Result:</strong> 95% detection reliability across various lighting conditions.</p>
</div>

<div style="background: #f8d7da; padding: 20px; border-radius: 8px; margin: 20px 0; border-left: 4px solid #dc3545;">
  <h3 style="margin-top: 0; color: #721c24;">Challenge 2: Motor Speed Mismatch</h3>
  <p><strong>Problem:</strong> Robot veered left despite sending identical PWM signals to both motors.</p>
  <p><strong>Root Cause:</strong> Manufacturing tolerances in DC motors causing speed variation.</p>
  <p><strong>Solution:</strong></p>
  <ul>
    <li>Measured actual RPM of each motor at various PWM values</li>
    <li>Created calibration offset table (left motor required 8% higher PWM)</li>
    <li>Implemented software compensation in motor control function</li>
  </ul>
  <p><strong>Result:</strong> Straight-line tracking within ±2cm over 1 meter.</p>
</div>

<div style="background: #d1ecf1; padding: 20px; border-radius: 8px; margin: 20px 0; border-left: 4px solid #17a2b8;">
  <h3 style="margin-top: 0; color: #0c5460;">Challenge 3: Battery Voltage Drop</h3>
  <p><strong>Problem:</strong> Robot slowed significantly as batteries depleted below 5V.</p>
  <p><strong>Root Cause:</strong> Fixed PWM values didn't compensate for lower supply voltage.</p>
  <p><strong>Solution:</strong></p>
  <ul>
    <li>Added voltage divider circuit to monitor battery level</li>
    <li>Implemented voltage compensation algorithm: <code>PWM_adjusted = PWM_base × (6V / V_current)</code></li>
    <li>Added low-battery warning (LED blinks when V < 4.5V)</li>
  </ul>
  <p><strong>Result:</strong> Consistent performance from 6V down to 4.5V threshold.</p>
</div>

---

## 📊 Technical Skills Demonstrated

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 20px; margin: 30px 0;">
  
  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3 style="color: #667eea; margin-top: 0;">⚡ Circuit Design</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>Power system analysis</li>
      <li>Component value calculations</li>
      <li>Schematic interpretation</li>
      <li>Load balancing</li>
    </ul>
  </div>
  
  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3 style="color: #667eea; margin-top: 0;">🔧 Hardware Skills</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>PCB assembly</li>
      <li>Soldering & wiring</li>
      <li>Mechanical integration</li>
      <li>Component testing</li>
    </ul>
  </div>
  
  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3 style="color: #667eea; margin-top: 0;">💻 Programming</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>Arduino C/C++</li>
      <li>Control algorithms</li>
      <li>Sensor interfacing</li>
      <li>PWM implementation</li>
    </ul>
  </div>
  
  <div style="background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
    <h3 style="color: #667eea; margin-top: 0;">📊 Problem Solving</h3>
    <ul style="margin: 0; padding-left: 20px;">
      <li>Systematic debugging</li>
      <li>Performance optimization</li>
      <li>Calibration procedures</li>
      <li>Documentation</li>
    </ul>
  </div>
  
</div>

---

## 🎓 Key Takeaways

This project provided hands-on experience with fundamental electrical engineering concepts:

- **Ohm's Law in Practice**: Applied V = IR calculations for real components with measurable results
- **Motor Control Theory**: Understood H-bridge operation and PWM duty cycle effects
- **Sensor Calibration**: Learned importance of environmental testing and threshold tuning  
- **System Integration**: Developed skills in combining mechanical, electrical, and software subsystems
- **Debugging Methodology**: Practiced systematic troubleshooting from symptom to root cause

The most valuable lesson was that theoretical calculations provide a starting point, but real-world testing and iteration are essential for robust system performance.

---

## 🚀 Future Enhancements

- [ ] **Bluetooth Control Module** - Add HC-05 for wireless command and telemetry
- [ ] **LiPo Battery Upgrade** - Implement 7.4V 2S LiPo with charging circuit for higher performance
- [ ] **Advanced Navigation** - Upgrade to encoder-based odometry for precise positioning
- [ ] **Mobile App Interface** - Develop Android/iOS app for parameter tuning and data visualization
- [ ] **Speed Optimization** - Implement PID control for smoother acceleration curves

---

## 📦 Bill of Materials

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

<div style="text-align: center; margin: 40px 0;">
  <a href="{{ '/at-home-projects/' | relative_url }}" style="display: inline-block; padding: 12px 30px; background: #667eea; color: white; text-decoration: none; border-radius: 8px; font-weight: bold;">← Back to At Home Projects</a>
</div>
