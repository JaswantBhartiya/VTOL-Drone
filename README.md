# Autonomous Tactical VTOL Drone System

[![ROS 2](https://img.shields.io/badge/ROS_2-Humble-blue.svg)](https://docs.ros.org/en/humble/)
[![Flight Stack](https://img.shields.io/badge/Autopilot-ArduPilot%20%7C%20PX4-green.svg)](https://ardupilot.org/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Hardware-Y3_Tilt--Rotor_%7C_X8_Coaxial-orange.svg)](#)

A modular, two-stage Vertical Takeoff and Landing (VTOL) UAV platform designed for low-cost aerodynamic flight validation and high-capability autonomous tactical operations.

---

## 📌 Project Architecture & Strategy

This project uses a staged development strategy to isolate aerodynamic risk from expensive payload and autonomy systems:

```
                          VTOL Drone System
                                  │
         ┌────────────────────────┴────────────────────────┐
         │                                                 │
┌────────▼────────────────────────┐       ┌────────────────▼────────────────┐
│   Branch A: Flight Demonstrator │       │   Branch B: Autonomous Tactical │
├─────────────────────────────────┤       ├─────────────────────────────────┤
│ • Y3 Tilt-Rotor Layout          │       │ • Heavy-Lift X8 / Y6 Coaxial    │
│ • Low-Cost 3D Printed (LW-PLA)  │  ──►  │ • Dual-Brain (Jetson + Pixhawk) │
│ • ArduPilot / INAV Control      │       │ • ROS 2 + YOLOv8 + Behavior Tree│
│ • Transition Dynamics Tuning    │       │ • Multi-Sensor UAV Detection    │
└─────────────────────────────────┘       └─────────────────────────────────┘
```

---

## 🚀 Branch Comparison Matrix

| Specification | Branch A (Technology Demonstrator) | Branch B (Tactical Platform) |
| :--- | :--- | :--- |
| **Primary Goal** | Transition tuning & manual envelope testing | Full autonomous mission execution |
| **Airframe Layout** | Y3 Tilt-Rotor (3 Motors / 2 Tilts) | Heavy-Lift X8 or Y6 Coaxial VTOL |
| **Structure** | 3D Printed (LW-PLA/PETG) + 3K Carbon Spars | Carbon Fiber Tubes + Molded Composites |
| **Compute Architecture**| Standalone FC (SpeedyBee F405 / Pixhawk) | Dual-Brain: Jetson Orin Nano + Pixhawk |
| **Payload Capacity** | Low (~1.0 kg – 1.5 kg ballast) | High (5.4 kg – 6.4 kg active mission load) |
| **Sensor Suite** | GPS + FPV HD Feed + Telemetry | Radiometric Thermal + 77 GHz Radar + SDR |
| **Active Mechanics** | Front Motor Vector Tilt Mechanism | Internal Drop Bay + 3 DOF Retractable Claw |
| **Est. Build Cost** | **~₹16,500 – ₹20,500** ($200–$250) | **~₹2.8L – ₹6.5L** ($3,500–$8,000) |

---

## 🛠️ Branch A: Flight Technology Demonstrator

Branch A provides a lightweight, easily repairable Y3 tilt-rotor platform to test transition physics from multirotor hover to fixed-wing glide.

### Hardware Stack
* **Flight Controller:** SpeedyBee F405 WING App (or Pixhawk 2.4.8)
* **Motors:** 3x Sunnysky 2216 (880KV–1100KV) or 2806.5 Brushless Motors
* **ESCs:** 3x 40A BLHeli_S ESCs
* **Servos:** 2x MG996R / DS3218 (Front Tilts) + 2x MG90S (V-Tail Elevons)
* **GPS:** Radiolink / Neo-M8N GPS + Magnetometer
* **Battery:** 4S 3300mAh – 4500mAh LiPo

### Pin Wiring Map (SpeedyBee F405 WING)

```
[Battery 4S] ──► XT60 Main Pads
                  ├── S1 Pad ──► Front Left Motor ESC
                  ├── S2 Pad ──► Front Right Motor ESC
                  ├── S3 Pad ──► Rear Motor ESC
                  ├── S5 Pad ──► Front Left Tilt Servo  (6V Power Rail)
                  ├── S6 Pad ──► Front Right Tilt Servo (6V Power Rail)
                  ├── S7 Pad ──► Left V-Tail Servo     (6V Power Rail)
                  ├── S8 Pad ──► Right V-Tail Servo    (6V Power Rail)
                  ├── UART 1 ──► RC Receiver (ELRS / FlySky)
                  └── UART 2 ──► M8N GPS & External Compass (I2C)
```

---

## ⚡ Branch B: Autonomous Tactical System

Branch B integrates high-level ROS 2 perception and decision-making on an edge computing platform.

### System Architecture
```
                         +-----------------------------------+
                         |      Sensors & Data Stream        |
                         |  (Thermal / 77GHz Radar / SDR)    |
                         +-----------------+-----------------+
                                           |
                                   [Sensor Feeds]
                                           |
+------------------------------------------v------------------------------------------+
| Companion Computer: NVIDIA Jetson Orin Nano 8GB                                      |
|                                                                                     |
|   +-----------------------+     +------------------------+     +----------------+   |
|   | OpenCV / TensorRT     | ──► | Object Tracking        | ──► | Behavior Tree  |   |
|   | (YOLOv8 Detection)    |     | (Extended Kalman Filter) |   | (Nav2 / BT)    |   |
|   +-----------------------+     +------------------------+     +-------+--------+   |
+------------------------------------------------------------------------|------------+
                                                                         |
                                                            [Micro-XRCE-DDS / MAVLink]
                                                                         |
+------------------------------------------------------------------------v------------+
| Real-Time Flight Controller: Pixhawk 6X / PX4 RTOS                                  |
|                                                                                     |
|   • Actuator Outputs (8x Coaxial ESCs)     • Fast PID / IMU Hover Stability         |
|   • Ventral Retractable Claw Actuation     • CoG-Aligned Internal Drop Bay Release  |
+-------------------------------------------------------------------------------------+
```

### Advanced Features
1. **Multi-Spectrum Target Detection:** Fuses thermal imagery, mmWave radar micro-Doppler profiles, and SDR radio sweeps to track airborne targets in all-weather conditions.
2. **Dual-Brain Compute Topology:** Real-time motor loops run on NuttX RTOS (Pixhawk) while heavy neural network inference runs on Linux (Jetson Orin Nano).
3. **Internal Aerodynamic Bay:** Payload drops occur from an internal bay aligned with the aircraft's Center of Gravity (CoG) to maintain flight stability.
4. **Ventral Flush-Retractable Claw:** 2–3 DOF robotic gripper retracts flush into the fuselage during high-speed cruise to eliminate parasitic drag.

---

## 📂 Repository Structure

```
.
├── branch_a_demonstrator/
│   ├── cad_and_3d_prints/       # STL/STEP files for LW-PLA wings & tilt mounts
│   ├── ardupilot_configs/       # Parameter files (.param) for Y3 VTOL transition
│   └── wiring_diagrams/         # Schematics for SpeedyBee & Pixhawk setups
│
├── branch_b_tactical/
│   ├── ros2_workspace/          # ROS 2 Humble packages (perception, BT, tracking)
│   ├── yolo_models/             # TensorRT-optimized target detection weights
│   ├── px4_custom_airframe/     # Custom PX4 mixer matrix for X8 tilt-rotor
│   └── payload_mechanisms/      # CAD models for drop bay & 3 DOF retractable claw
│
└── docs/                        # System architecture specifications & CFD analysis
```

---

## ⚙️ Getting Started (Branch A Setup)

### Prerequisites
* [Mission Planner](https://ardupilot.org/planner/) or [QGroundControl](http://qgroundcontrol.com/)
* [Slic3r / Cura / PrusaSlicer](https://www.prusa3d.com/page/prusaslicer_424/) with LW-PLA profiles
* ArduPilot VTOL Firmware (`ArduVideo` / `ArduPlane 4.x+`)

### ArduPilot Y3 Quick Config Parameters
```ini
Q_ENABLE = 1
Q_FRAME_CLASS = 7      # Tilt-Rotor
Q_FRAME_TYPE = 1       # Y-Shape
Q_TILT_MASK = 3        # Servos on Output 1 & 2 Tilt
Q_TILT_RATE_UP = 45    # Tilt speed in deg/s
```

---

## 📜 License

Distributed under the MIT License. See [`LICENSE`](./LICENSE) for more information.
