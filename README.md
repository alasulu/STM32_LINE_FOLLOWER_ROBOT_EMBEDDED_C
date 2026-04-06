<div align="center">

# 🤖 STM32 Line Follower Robot for Adaptive Cruise Control
### Real-Time Embedded PID Control with Buzzer Feedback

<p>
  <img alt="Language" src="https://img.shields.io/badge/language-C-blue">
  <img alt="Platform" src="https://img.shields.io/badge/platform-STM32F103RB-success">
  <img alt="Control" src="https://img.shields.io/badge/control-PID-orange">
  <img alt="Sensors" src="https://img.shields.io/badge/sensors-QTR--8RC%20%7C%20HC--SR04-informational">
  <img alt="Driver" src="https://img.shields.io/badge/motor_driver-L298N-critical">
</p>

<p><b>Line tracking + adaptive cruise control + bare-metal embedded implementation</b></p>

</div>

---

## Overview

This project presents a real-time autonomous robotic system that combines:

- **Line following** using an 8-channel reflectance sensor array
- **Adaptive cruise control** using ultrasonic distance feedback
- **Closed-loop PID control** for stable and responsive motion
- **Register-level STM32 programming** without high-level abstraction libraries
- **Audible feedback** through a buzzer for threshold-based user notification

Unlike many educational robot implementations that rely heavily on middleware or high-level libraries, this system emphasizes low-level control, timing precision, and direct peripheral management.

---

## Why this project matters

This repository is not just a line follower.

It is a compact embedded control system that demonstrates how classical control theory can be applied on a microcontroller to solve a multi-objective robotics problem:

1. **Follow a predefined path**
2. **Maintain a safe following distance from a moving target**
3. **React in real time under changing environmental conditions**

This makes the project a strong intersection of:

- Embedded Systems
- Control Theory
- Robotics
- Sensor Integration
- Real-Time Programming

---

## System Objectives

The robot is designed to:

- Track a black line reliably on a predefined course
- Maintain an approximate **60 cm reference distance** from a moving object
- Dynamically adjust motor speed through PID-based control
- Warn the user through a buzzer when distance thresholds are violated
- Operate with stable timing and deterministic response using low-level embedded code

---

## Hardware Stack

| Component | Role |
|---|---|
| **STM32F103RB Nucleo** | Main processing and control unit |
| **QTR-8RC Reflectance Sensor Array** | Detects line position |
| **HC-SR04 Ultrasonic Sensor** | Measures object distance |
| **L298N Motor Driver** | Drives dual DC motors |
| **2x DC Geared Motors** | Locomotion |
| **Passive Buzzer (HW-508)** | Audible warning / system feedback |
| **3x 3.7V Li-ion Batteries** | Power source |

---

## Control Strategy

### 1) Line Tracking

The robot estimates line position using a weighted sensor average and computes the deviation from the center reference.

#### Position estimate

```math
x_{position} = \frac{\sum 1000 \cdot S_n}{\sum S_n}
```

#### Tracking error

```math
e = 4500 - x_{position}
```

Interpretation:

- **Negative error** → robot has deviated left
- **Positive error** → robot has deviated right

The controller compensates by adjusting left and right motor speeds asymmetrically.

---

### 2) Adaptive Cruise Control

The **HC-SR04** continuously measures the distance to a moving object in front of the robot.

The control target is to keep the robot at approximately:

```text
60 cm
```

The robot computes distance error and adjusts forward velocity to maintain the setpoint. This allows the robot to behave like a simple embedded adaptive cruise controller.

---

### 3) PID Controller

The system uses a standard PID controller:

```math
u(t) = K_p e(t) + K_i \int e(t)\,dt + K_d \frac{de(t)}{dt}
```

#### Tuned gains

```c
Kp = 0.015;
Ki = 0.003;
Kd = 10;
```

These values were tuned experimentally to balance:

- fast correction
- minimal oscillation
- improved stability
- reduced steady-state error

---

## Embedded Implementation Philosophy

This project is implemented in a bare-metal style, prioritizing understanding and control of the hardware itself.

### Key characteristics

- No high-level robotics framework
- No external control library
- Direct register configuration
- Timer-based PWM generation
- Microsecond-scale timing for distance sensing
- Deterministic control loop behavior

This makes the repository especially valuable for students and engineers who want to study how embedded robotics actually works at the hardware-interfacing level.

---

## Repository Structure

```text
.
├── EEE3002_PROJECT_REPORT.pdf      # Full project report and documentation
├── main.c                          # Main control logic: sensors, PID, motor control, buzzer
├── delayfunctions.h                # Timing and delay routines
└── system_clock_config.h           # Clock configuration utilities
```

---

## Main Features

- Closed-loop line tracking
- Ultrasonic distance regulation
- PID-based motor speed adjustment
- PWM motor control
- Buzzer-based warning logic
- Register-level STM32 peripheral control
- Modular embedded structure

---

## Engineering Highlights

### Bare-metal motor control

Motors are controlled through PWM outputs generated using timer peripherals, allowing speed modulation and directional corrections.

### Sensor fusion mindset

Although simple in hardware, the system conceptually combines:

- line position sensing
- distance measurement
- controller state
- output feedback

### Real-time responsiveness

The system is designed around continuous sensor acquisition and fast correction loops, which is critical for both line tracking and distance maintenance.

### Practical robustness

The project explicitly considers non-idealities such as:

- surface irregularities
- sensor noise
- battery variation
- dust and friction effects
- unstable environmental conditions

---

## Results

The robot successfully demonstrated:

- stable line-following performance
- maintained following distance near the desired setpoint
- smoother operation after PID tuning
- improved responsiveness under dynamic conditions
- audible feedback for unsafe or undesirable distance conditions

The project also showed how low-level embedded implementation can support reliable autonomous behavior even with modest hardware resources.

---

## Challenges Encountered

Several real-world issues affected performance during development and testing:

- noisy sensor readings
- inconsistent surface reflectance
- friction and dust affecting motion
- battery level variations influencing motor response
- ultrasonic sensor sensitivity to environmental factors

These were addressed through:

- repeated tuning
- timing refinement
- iterative hardware/software debugging
- PID parameter adjustment

---

## Demonstration

### Video

[Project Demo](https://youtu.be/eMMg-Zr_-wU?si=v-y6RqtGeFxpkQkk)

---

## How to Build / Use

You can tailor this section depending on whether you used STM32CubeIDE, Keil, or another development flow.

### Typical workflow

1. Open the project in your STM32 development environment
2. Configure the target for **STM32F103RB**
3. Flash the firmware to the board
4. Connect:
   - QTR-8RC sensor array
   - HC-SR04 ultrasonic sensor
   - L298N motor driver
   - DC motors
   - buzzer
5. Place the robot on a suitable track
6. Test and retune PID parameters if needed for your own mechanical setup

---

## Recommended Improvements

This project can be extended significantly. Some strong next steps:

- Kalman or complementary filtering for cleaner sensing
- Adaptive PID for changing surfaces and battery conditions
- Encoder feedback for better motion estimation
- State machine architecture for cleaner behavioral control
- PCB integration for a more compact system
- SLAM or path planning for higher autonomy
- Wireless telemetry for live monitoring and tuning

---

## Research / Learning Value

This repository is useful for anyone interested in:

- embedded control systems
- STM32 bare-metal development
- robotics fundamentals
- practical PID implementation
- sensor-driven motion control
- educational mechatronics projects

It is especially suitable as a study reference for:

- control courses
- embedded systems courses
- introductory robotics
- microcontroller-based design projects

---

## Report

The full report includes:

- system motivation
- hardware explanation
- circuit and block diagrams
- PID design details
- error formulation
- performance discussion
- implementation notes
- conclusions and references

If you want the full background of the project, start with:

```text
EEE3002_PROJECT_REPORT.pdf
```

---

## Author

**Oğuzhan Alasulu**  
Electrical & Electronics Engineering

- GitHub: [@alasulu](https://github.com/alasulu)
- Email: `oguzhan.alasulu@agu.edu.tr`

---

## Suggested Citation

```bibtex
@misc{alasulu_stm32_line_follower,
  author       = {Oğuzhan Alasulu},
  title        = {STM32 Line Follower Robot for Adaptive Cruise Control with Buzzer Alert},
  year         = {2026},
  howpublished = {\url{https://github.com/alasulu/STM32_LINE_FOLLOWER}}
}
```

---

## Final Note

> This project demonstrates that even a compact educational robot can become a serious embedded systems case study when control theory, sensor integration, and low-level implementation are brought together thoughtfully.
