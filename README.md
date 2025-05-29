# 🤖 Robotic Arm with Computer Vision  
**Articulated mechatronic system controlled by human gestures via OpenCV and Arduin**  
*(GIC Project - Centro Universitário Dom Helder Câmara | 2025)*  

[![Licença MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)  
[![Video Demo](https://img.shields.io/badge/YouTube-Demonstração-red)](https://youtu.be/4t1daCFQ1OE)  
[![OpenCV](https://img.shields.io/badge/OpenCV-4.7.0-blue)](https://opencv.org)  

---

## 📜 Project Overview
Robotic solution based on the **articulated** model (Groover, 2011) with:
- 👁️ Computer vision for gesture tracking (CVZone/OpenCV)
- 🦾 Custom 3D-printed parts (PLA/ABS)
- 🧠 Intelligent control via Arduino (C++) and Python
- 📚 Complete academic documentation

**Keywords**: Robotics, Computer Vision, Arduino, OpenCV, 3D Printing

---

## 🎯 Objectives
### General
Develop a robotic arm that interprets visual scenarios and performs autonomous tasks (ROSÁRIO, 2010)

### Specific
- ✅ Integrate 3D printing in part fabrication
- ✅ Implement mechanical/electronic modules
- ✅ Develop tracking algorithms with CVZone
- ✅ Test precision in object manipulation
- ✅ Document for academic replication

---
```
## 📂 Folder Structure
📦 software
├── 📂 .github
│ ├── 📂 workflows
|      └── 📄 card-validation.yaml
│ └── 📂 CODEOWNERS
├── 📂 scripts
│    └── 📄 validate_pr.py
├── 📂 software.dsp
│   ├── 📂 arduino
|        └── 📄 pid_config.h
|        └── 📄 servo_control.ino
│   └── 📂 python
|        └── 📄 hand_tracker.py
|        └── 📄 main.py
|        └── 📄 requirements.txt
|        └── 📄 serial_comm.py
├── 📄 LICENSE # Licença MIT
├── 📄 README.md # Documentação principal
└── 📄 requirements.txt
```
---

## 🛠️ System Architecture
### Hardware
| Component                | Specifications                          |  
|--------------------------|----------------------------------------|  
| **Arduino Uno/Nano**     | PID control of servo motors          |  
| **MG996R Servos**  | 10kg/cm torque, 180° rotation          |  
| **Logitech C920**        | 1080p @ 30fps for tracking       |  
| **3D Parts**            | InMoov models (STL available [here](#-apêndice)) |  

### Software
| Layer          | 	Technologies                          |
|-----------------|--------------------------------------|
| **Vision**       | Python 3.8+, OpenCV 4.7, CVZone 1.5 |
| **Control**    | C++ (Arduino IDE), PlatformIO       |
| **Communication** | Serial Protocol @ 115200 baud      |

---

## ⚙️ Installation Guide
### Prerequisites
- Arduino IDE 2.0+
- Python 3.8+
- 3D Printer (recommended configuration: 0.2mm layer height, 20% infill)

### Setup
```bash
# 1. Clonar repositório
git clone https://github.com/domhelder-gic/braco-robotico.git
cd braco-robotico

# 2. Instalar dependências Python
pip install -r requirements.txt
# Arquivo inclui:
# opencv-python==4.7.0.72
# cvzone==1.5.6
# pyserial==3.5

# 3. Programar Arduino
arduino-cli compile --fqbn arduino:avr:nano firmware/braco_robotico.ino
