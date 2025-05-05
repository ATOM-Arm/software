# 🤖 Braço Robótico com Visão Computacional  
**Sistema mecatrônico articulado controlado por gestos humanos via OpenCV e Arduino**  
*(Projeto GIC - Centro Universitário Dom Helder Câmara | 2025)*  

[![Licença MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)  
[![Video Demo](https://img.shields.io/badge/YouTube-Demonstração-red)](https://youtu.be/4t1daCFQ1OE)  
[![OpenCV](https://img.shields.io/badge/OpenCV-4.7.0-blue)](https://opencv.org)  

---

## 📜 Resumo do Projeto
Solução robótica baseada no modelo **articulado** (Groover, 2011) com:
- 👁️ Visão computacional para rastreamento de gestos (CVZone/OpenCV)
- 🦾 Peças personalizadas em impressão 3D (PLA/ABS)
- 🧠 Controle inteligente via Arduino (C++) e Python
- 📚 Documentação acadêmica completa

**Palavras-chave**: Robótica, Visão Computacional, Arduino, OpenCV, Impressão 3D

---

## 🎯 Objetivos
### Geral
Desenvolver um braço robótico que interprete cenários visuais e execute tarefas autônomas (ROSÁRIO, 2010)

### Específicos
- ✅ Integrar impressão 3D na fabricação de peças
- ✅ Implementar módulos mecânicos/eletrônicos
- ✅ Desenvolver algoritmos de rastreamento com CVZone
- ✅ Testar precisão em manipulação de objetos
- ✅ Documentar para replicação acadêmica

---
```
## 📂 Estrutura de Pastas
📦 braco-robotico
├── 📂 docs # Documentação acadêmica e relatórios
│ ├── 📄 especificacoes.pdf # Requisitos do sistema
│ └── 📄 manual_tecnico.md # Guia de implementação
├── 📂 firmware # Código Arduino
│ ├── 📄 braco_robotico.ino # Sketch principal
│ └── 📄 pid_controller.h # Biblioteca de controle
├── 📂 modelos_3d # Arquivos para impressão
│ ├── 📄 base.stl # Peça estrutural
│ ├── 📄 garra.stl # Módulo de preensão
│ └── 📄 articulacoes/ # Conjunto de eixos
├── 📂 src # Código Python
│ ├── 📄 main.py # Script de visão computacional
│ ├── 📄 gesture_tracker.py # Lógica de rastreamento
│ └── 📄 serial_com.py # Comunicação Arduino
├── 📂 testes # Scripts de validação
│ ├── 📄 calibracao.py # Ajuste de servomotores
│ └── 📄 precision_test/ # Dataset de validação
├── 📄 LICENSE # Licença MIT
└── 📄 README.md # Documentação principal
```
---

## 🛠️ Arquitetura do Sistema
### Hardware
| Componente               | Especificações                          |  
|--------------------------|----------------------------------------|  
| **Arduino Uno/Nano**     | Controle PID dos servomotores          |  
| **Servomotores MG996R**  | 10kg/cm torque, 180° rotação          |  
| **Logitech C920**        | 1080p @ 30fps para rastreamento       |  
| **Peças 3D**            | Modelos InMoov (STL disponíveis [aqui](#-apêndice)) |  

### Software
| Camada          | Tecnologias                          |
|-----------------|--------------------------------------|
| **Visão**       | Python 3.8+, OpenCV 4.7, CVZone 1.5 |
| **Controle**    | C++ (Arduino IDE), PlatformIO       |
| **Comunicação** | Protocolo Serial @ 115200 baud      |

---

## ⚙️ Guia de Instalação
### Pré-requisitos
- Arduino IDE 2.0+
- Python 3.8+
- Impressora 3D (configuração recomendada: 0.2mm layer height, 20% infill)

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