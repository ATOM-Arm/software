# 📚 Robotic Arm – Software Repository

This repository contains the source code and computational components of the Robotic Arm with Computer Vision project. It focuses on the software system responsible for gesture recognition, motor control, and integration with Arduino hardware.

[![Licença MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)  
[![Video Demo](https://img.shields.io/badge/YouTube-Demonstração-red)](https://youtu.be/4t1daCFQ1OE)  
[![OpenCV](https://img.shields.io/badge/OpenCV-4.7.0-blue)](https://opencv.org)

---

## 🎯 Objectives

This repository contains the **hardware** of the **ATOM Project**, a robotic arm controlled through computer vision and built using 3D printing and open-source hardware. While the overall project integrates electronics, and software, this repository focuses specifically on the mechanical and solid parts.

The repository includes all source code related to:

- 🧠 **Forearm, wrist and hand building**, using 3D printed pieces and electronical components.
- 📡 **Connection with Arduino**, we'll use cable connections to communicate with the arduino board.

---

```
## 📂 Folder Structure
📦 software
├── 📂 .github                 # GitHub integration workflows and configs
├── 📂 scripts                 # Needed scripts to the github project organization
├── 📄 LICENSE                 # Project MIT license
├── 📄 README.md               # Main repository documentation
```

---

### Prerequisites
- Git version 2.25 or higher
- Access to a terminal/command line

### Steps

```bash
# 1. Clone the repository without checking out files
git clone --filter=blob:none --no-checkout https://github.com/username/repository.git
cd repository

# 2. Enable sparse checkout
git sparse-checkout init --cone

# 3. Set the folder you want to download
git sparse-checkout set path/to/folder

# 4. Checkout the content
git checkout
