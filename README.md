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

---------------------------------------------------------------------------------
## ⚙️ System Architecture

 ![Img1](https://github.com/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-sing-CAN-Protocol/blob/main/docs/BlockDiagram.jpg)

---------------------------------------------------------------------------------

## 🔧 Hardware Reqired

![Img2](https://github.com/jaidev-11/ARM-based-Reverse-Parking-Assistance-System-sing-CAN-Protocol/blob/main/docs/HardwareSetup.jpeg)

---------------------------------------------------------------------------------

## 📌 Key Features

- Multi-node Interrupt-driven CAN communication
- Real-time distance measurement using ultrasonic sensor
- Distance-based buzzer control & sensor fault detection

---------------------------------------------------------------------------------

## 🔔 Buzzer Output
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
## 🤝 Let's Connect

I'm always open to collaborating on **Embedded Systems** or **Firmware**. Feel free to reach out!
# ⭐ If you found this interesting, consider giving it a star!
<div align="center">

[![LinkedIn](https://img.shields.io/badge/Connect_on_LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/ijaidevpandya)
[![Email](https://img.shields.io/badge/Send_an_Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:pandya99jaidev@gmail.com)
</div>

---------------------------------------------------------------------------------
## 👨🏽‍🚀Author

jaidev-11
