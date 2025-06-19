# 📚 Robotic Arm – Software Repository

This repository contains the source code and computational components of the Robotic Arm with Computer Vision project. It focuses on the software system responsible for gesture recognition, motor control, and integration with Arduino hardware.

[![Licença MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)  
[![Video Demo](https://img.shields.io/badge/YouTube-Demonstração-red)](https://youtu.be/4t1daCFQ1OE)  
[![OpenCV](https://img.shields.io/badge/OpenCV-4.7.0-blue)](https://opencv.org)

---

## 🎯 Objectives

This repository contains the **software system** of the **ATOM Project**, a robotic arm controlled through computer vision and built using 3D printing and open-source hardware. While the overall project integrates mechanics, electronics, and software, this repository focuses specifically on the **computational logic** that enables intelligent control and visual interaction.

The repository includes all source code related to:

- 🧠 **Computer vision and gesture recognition**, powered by OpenCV and CVZone
- 📡 **Communication with Arduino**, for controlling servo motors via serial interface
- ⚙️ **Control algorithms and system logic** written in Python
- 🧪 **Testing scripts and prototypes** of the vision-control pipeline

This software is a core component of the ATOM Project and is continually evolving to support research, experimentation, and educational development in robotics and automation.

---

```
## 📂 Folder Structure
📦 software
├── 📂 .github                 # GitHub integration workflows and configs
├── 📂 software.dsp            # Main computational system source code
│   ├── 📂 arduino             # Embedded code for servo control
│   └── 📂 python              # Python scripts for vision and control
├── 📄 LICENSE                 # Project MIT license
├── 📄 README.md               # Main repository documentation
└── 📄 requirements.txt        # Python dependencies list
```

---

## ⚙️ Installation Guide

### Prerequisites

- Arduino IDE 2.0+
- Python 3.8+
- 3D Printer (recommended configuration: 0.2mm layer height, 20% infill)
- OpenCV 4.7.0
- CVZone 1.5.6

### Setup

```bash

# 1. Clone the repository
git clone https://github.com/ATOM-Arm/software
cd braco-robotico

# 2. Install Python dependencies
pip install -r requirements.txt
# The file includes:
# opencv-python==4.7.0.72
# cvzone==1.5.6
# pyserial==3.5

# 3. Open Arduino IDE and load the code from software.dsp/arduino

# 4. Upload the code to your Arduino board

# 5. Run the Python scripts in software.dsp/python

```
