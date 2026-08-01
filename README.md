# 🚗 ARM based Advanced Ultrasonic Obstacle Detection & Intelligent Reverse Parking Assistance System

An embedded system using ARM7 - LPC2129 and CAN Communication protocol to implement a real-time reverse parking alert mechanism with buzzer and sensor-based feedback.

---------------------------------------------------------------------------------
## 🧠 Overview

This project implements a reverse parking assistance system using two independent embedded nodes communicating over CAN protocol:

- Node A (Control Node)
Sends remote frames and controls the buzzer based on received distance data.

- Node B (Sensor Node)
Measures distance using an ultrasonic sensor and responds via CAN.

The system is designed using an interrupt-driven approach for effective communication and responsiveness.

## System Overview

```
  NODE A (Driver Side)               NODE B (Sensor Side)
  ┌─────────────────────┐            ┌─────────────────────┐
  │  LPC2129            │            │  LPC2129            │
  │                     │            │                     │
  │  SW (P0.16/EXT0) ──►│            │◄── HC-SR04 TRIG P0.3│
  │  Buzzer (P0.21)  ◄──│            │    HC-SR04 ECHO P0.4│
  │                     │            │                     │
  └──────────┬──────────┘            └──────────┬──────────┘
             │  CANH                             │
             ├───────────────────────────────────┤
             │  CANL                             │
           120Ω                               120Ω
        (termination)                     (termination)
```

- Node A sends an **RTR frame** when reverse gear is engaged
- Node B responds with **distance data** every 60 ms
- Node A drives the buzzer at a speed proportional to the measured distance
- Node B sends a stop response when reverse is disengaged

---------------------------------------------------------------------------------

## ⚙️ System Architecture

 ![Img1](https://github.com/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-sing-CAN-Protocol/blob/main/docs/BlockDiagram.jpg)

---------------------------------------------------------------------------------

## 🔧 Hardware Reqired

| Component | Details |
|---|---|
| Microcontroller | NXP LPC2129 (ARM7TDMI-S, 60 MHz) |
| CAN Transceiver | MCP2551 (on both nodes) |
| Ultrasonic Sensor | HC-SR04 (range: 2 cm – 400 cm) |
| Buzzer | Active buzzer, 5V |
| Reverse Switch | Normally open, active-LOW |
| Termination | 120Ω resistors at both ends of CAN bus |
| IDE | Keil µVision 5 |

## Pin Configuration

### Node A

| Pin | Direction | Function |
|---|---|---|
| P0.0 | Output | UART0 TX |
| P0.1 | Input | UART0 RX |
| P0.16 | Input | EXT0 — Reverse gear switch (active-LOW) |
| P0.21 | Output | Buzzer |
| P0.25 | Output | CAN2 TD2 |
| P0.26 | Input | CAN2 RD2 |

### Node B

| Pin | Direction | Function |
|---|---|---|
| P0.0 | Output | UART0 TX |
| P0.1 | Input | UART0 RX |
| P0.9 | Output | HC-SR04 TRIG |
| P0.10 | Input | HC-SR04 ECHO |
| P0.25 | Output | CAN2 TD2 |
| P0.26 | Input | CAN2 RD2 |

## CAN Communication Protocol

| Parameter | Value |
|---|---|
| CAN Standard | CAN 2.0B |
| Baud Rate | 125 Kbps |
| Frame ID | 0x501 |
| Frame Format | Standard (11-bit ID) |
| PCLK | 60 MHz (VPBDIV = 1) |
| C2BTR | 0x001C001D |

### Frame Types

**RTR Frame — Node A → Node B (Reverse Engaged)**
| Field | Value |
|---|---|
| ID | 0x501 |
| RTR | 1 |
| DLC | 0 |
| Data | None |

**Data Frame — Node B → Node A (Distance Response)**
| Field | Value |
|---|---|
| ID | 0x501 |
| RTR | 0 |
| DLC | 4 |
| Data | Distance in cm (byteA) |

**Stop Frame — Node A → Node B (Reverse Disengaged)**
| Field | Value |
|---|---|
| ID | 0x501 |
| RTR | 0 |
| DLC | 4 |
| Data | 0x1 (stop command) |

## Firmware Architecture

### Structure

```
ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol/
├── NodeA/
│   ├── header.h
│   ├── maina.c
│   ├── candriver.c
│   ├── canintr.c
│   ├── uartdriver.c
│   └── delay.c
├── NodeB/
│   ├── header.h
│   ├── mainb.c
│   ├── candriver.c
│   ├── canintr.c
│   ├── uartdriver.c
│   ├── ultrasonic.c
│   └── delay_us.c
├── Docs/
├── License
└── README.md
```

### Node A

| File | Description |
|---|---|
| `maina.c` | Main loop — handles gear switch flag, buzzer control |
| `candriver.c` | CAN2 init, TX function |
| `canintr.c` | CAN2 RX ISR, EXT0 ISR, VIC configuration |
| `uartdriver.c` | UART0 init, TX, RX, string and integer print |
| `delay.c` | Blocking ms delay using Timer0 |
| `header.h` | Struct definition, typedefs, function declarations |

### Node B

| File | Description |
|---|---|
| `mainb.c` | Main loop — handles CAN RX flag, triggers measurement |
| `candriver.c` | CAN2 init, TX function |
| `canintr.c` | CAN2 RX ISR, Timer0 ISR, VIC configuration |
| `ultrasonic.c` | HC-SR04 trigger, echo measurement, distance calculation |
| `delay_us.c` | Blocking µs delay using Timer1 |
| `uartdriver.c` | UART0 init, TX, RX, string and integer print |
| `header.h` | Struct definition, typedefs, function declarations |

### Interrupt / Timer Usage

| Timer | Node | Purpose |
|---|---|---|
| Timer0 | Node A | Blocking ms delay (buzzer) |
| Timer0 | Node B | 60 ms measurement interval |
| Timer1 | Node B | Echo pulse width measurement + µs delay |

### VIC Slot Assignments

**Node A**
| Slot | Channel | ISR |
|---|---|---|
| 0 | 27 | CAN2 RX |
| 1 | 14 | EXT0 (gear switch) |

**Node B**
| Slot | Channel | ISR |
|---|---|---|
| 0 | 27 | CAN2 RX |
| 1 | 4 | Timer0 |

---
![Img2](https://github.com/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-sing-CAN-Protocol/blob/main/docs/HardwareSetup.jpeg)

---------------------------------------------------------------------------------

## 📌 Key Features

- Multi-node Interrupt-driven CAN communication
- Real-time distance measurement using ultrasonic sensor
- Distance-based buzzer control & sensor fault detection

---------------------------------------------------------------------------------

## Build Instructions

- Open Keil µVision 5
- Create a new project, select **NXP LPC2129** as target device
- Set compiler to **ARM Compiler v5**
- Add all `.c` files from the respective node folder to the project
- In Target Options → Target tab:
   - IRAM1: 0x40000000, size 0x4000
   - IROM1: 0x00000000, size 0x40000
- In Target Options → C/C++ tab, add include path to the folder containing `header.h`
- Build and flash using ULINK or J-Link debugger

> Flash Node B first, then Node A. Node B must be ready on the CAN bus before Node A starts sending RTR frames.

---------------------------------------------------------------------------------

## Working Principle

1. Node A initializes CAN, UART, EXT0 interrupt
2. Driver engages reverse gear → switch pulls P0.16 LOW → EXT0 ISR fires → flag set
3. Node A main loop detects flag → transmits **RTR frame** (ID 0x501) to Node B
4. Node B CAN RX ISR receives RTR → sets flag → main loop starts Timer0
5. Timer0 fires every 60 ms → Node B triggers HC-SR04 on P0.9 (10µs pulse)
6. HC-SR04 echo pulse on P0.10 → Timer1 measures pulse width → distance calculated
7. Node B transmits distance as **data frame** back to Node A
8. Node A CAN RX ISR receives distance → sets flag → main loop drives buzzer
9. Buzzer frequency increases as obstacle gets closer:

### 🔔 Buzzer Output
<div align = "center">
 
| Condition	| Buzzer Behavior |
| :----------- | :-------------- |
| Distance < 10 cm | Continuous ON |
| 10 cm < Distance < 100 cm | Fast Beep |
| 101 cm < Distance < 299 cm | Faster Beep |
| 300 cm < Distance < 400 cm | Slow Beep |
| Distance > 400 cm | OFF |
| Sensor Fails (>400) | OFF |

</div>
10. Driver disengages reverse → Node A sends **stop data frame** → Node B stops Timer0

---------------------------------------------------------------------------------
## 🛠️ Tech Stack

- Microcontroller: LPC2129 (ARM7)
- Language: Embedded C
- Protocols: CAN, UART
- Peripherals: Timer, GPIO, LED, Buzzer
- Sensor: Ultrasonic (HC-SR04)

---------------------------------------------------------------------------------
## 🧠 What I Learned

- Practical implementation of CAN protocol
- Difference between polling and interrupt-driven design
- Handling real-world sensor failures
- Building a multi-node embedded system

---------------------------------------------------------------------------------
## 📜 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## 🤝 Let's Connect

I'm always open to collaborating on **Embedded Systems** or **Firmware**. Feel free to reach out!

⭐ If you found this interesting, consider giving it a star!
<div align="center">

[![LinkedIn](https://img.shields.io/badge/Connect_on_LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/ijaidevpandya)
[![Email](https://img.shields.io/badge/Send_an_Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:pandya99jaidev@gmail.com)
</div>

<p align="center">

![Release](https://img.shields.io/github/v/release/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol?include_prereleases)
![License](https://img.shields.io/github/license/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)
![GitHub Stars](https://img.shields.io/github/stars/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)
[![Forks](https://img.shields.io/github/forks/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)](https://github.com/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol/network/members)
![Issues](https://img.shields.io/github/issues/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)
![Pull Requests](https://img.shields.io/github/issues-pr/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)
![Last Commit](https://img.shields.io/github/last-commit/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)
![Repo Size](https://img.shields.io/github/repo-size/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)
![Language](https://img.shields.io/github/languages/top/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-using-CAN-Protocol)

</p>

---------------------------------------------------------------------------------
## 👨🏽‍🚀Author

jaidev-11
