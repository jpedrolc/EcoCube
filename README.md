# 🧊 EcoCube - Smart Energy Management for Classrooms

**A IoT retrofit solution for automatic energy management in educational environments using millimeter-wave radar detection and infrared control.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform: ESP32](https://img.shields.io/badge/Platform-ESP32-green.svg)](https://www.espressif.com/)
[![Language: C++](https://img.shields.io/badge/Language-C++-blue.svg)](https://cplusplus.com/)

## 📋 Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Solution Architecture](#solution-architecture)
- [Project Roadmap](#project-roadmap)
- [Hardware Requirements](#hardware-requirements)
- [Getting Started](#getting-started)
- [Development Status](#development-status)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

EcoCube is a compact, plug-and-play IoT device designed to eliminate energy waste in university classrooms. By combining millimeter-wave radar technology with infrared control, the system automatically manages air conditioning units when rooms are unoccupied.

**Key Features:**
- ✅ Non-invasive retrofitting (no wiring modifications)
- ✅ Detects static human presence (breathing micro-movements)
- ✅ Universal AC control via infrared cloning
- ✅ WiFi-enabled occupancy analytics
- ✅ Maintenance alerts & notifications
- ✅ Energy consumption monitoring

## 🔍 Problem Statement

Traditional energy management in university classrooms faces critical issues:

| Challenge | Impact | EcoCube Solution |
|-----------|--------|-----------------|
| **PIR Sensors** | Disable AC when students are stationary (exam/study) | mmWave detects breathing (5mm micro-movements) |
| **Installation Barriers** | Requires electricians, infrastructure changes, high costs | Plug-and-play, USB powered, 5-minute setup |
| **Energy Waste** | AC runs 60-70% of classroom energy costs | Automatic shutdown when room is empty |
| **Portability** | Solution dependent on physical infrastructure | Easily movable between rooms |

## 🏗️ Solution Architecture

```
┌─────────────────────────────────────┐
│     EcoCube Hardware System          │
├─────────────────────────────────────┤
│                                     │
│  ┌────────────────────────────────┐ │
│  │  ESP32 Microcontroller          │ │
│  │  - Logic & Decision Making      │ │
│  │  - WiFi Connectivity            │ │
│  │  - Data Logging                 │ │
│  └────────────────────────────────┘ │
│           ↓         ↓         ↓      │
│  ┌─────────────────────────────────┐ │
│  │  HLK-LD2410 mmWave Radar Sensor │ │
│  │  - Presence Detection            │ │
│  │  - Micro-movement Sensing        │ │
│  │  - Range: 0-6m (configurable)   │ │
│  └─────────────────────────────────┘ │
│                                     │
│  ┌─────────────────────────────────┐ │
│  │  IR Infrared Emitter Module      │ │
│  │  - AC Remote Control Cloning     │ │
│  │  - Brand-agnostic codes          │ │
│  │  - High-power LED                │ │
│  └─────────────────────────────────┘ │
│                                     │
│  ┌─────────────────────────────────┐ │
│  │  5V USB Power Supply             │ │
│  │  - Standard USB charger          │ │
│  │  - Low power consumption         │ │
│  └─────────────────────────────────┘ │
└─────────────────────────────────────┘
         ↓
    3D-Printed Enclosure
    (Mounted on classroom whiteboards)
```

## 🛣️ Project Roadmap

This roadmap documents the journey from research to production-ready device.

### Phase 1️⃣: Foundational Research (✅ Complete)
- [x] Literature review on mmWave radar technology
- [x] Feasibility analysis of LD2410 sensor
- [x] IR control protocol research (Samsung, LG, Carrier)
- [x] Energy consumption analysis (UFMT case study)
- [x] Documentation: `docs/research/`

### Phase 2️⃣: Hardware Proof of Concept (🚀 In Progress)
- [x] **Milestone 1:** Basic sensor-to-LED integration
  - Status: ✅ COMPLETE
  - Device: LD2410 mmWave Sensor
  - Output: Internal ESP32 LED control
  - Code: `firmware/1-led-basic/`
  - Description: Initial test to verify radar detection triggers LED on hand proximity
  
- [ ] **Milestone 2:** Presence Detection Filtering
  - Status: 🔄 IN PROGRESS
  - Task: Implement anti-ghost filtering (energy threshold-based)
  - Code location: `firmware/2-presence-filtering/`
  - Description: Distinguish real presence from sensor noise using energy levels
  
- [ ] **Milestone 3:** IR Signal Transmission
  - Status: ⏳ PLANNED
  - Task: Integrate IR emitter, clone AC remote signals
  - Code location: `firmware/3-ir-transmission/`
  - Description: Transmit IR codes to turn off AC units
  
- [ ] **Milestone 4:** Time-Based Decision Logic
  - Status: ⏳ PLANNED
  - Task: Room empty timer (10-30 min), auto-shutdown
  - Code location: `firmware/4-time-logic/`
  - Description: Add timer logic to prevent rapid on/off cycles
  
- [ ] **Milestone 5:** WiFi Integration & Analytics
  - Status: ⏳ PLANNED
  - Task: MQTT/REST API, occupancy reporting
  - Code location: `firmware/5-wifi-analytics/`
  - Description: Cloud connectivity for monitoring and analytics

### Phase 3️⃣: Integration & Testing (⏳ Planned)
- [ ] Full system integration in test environment
- [ ] Real AC unit testing (multiple brands)
- [ ] Temperature & energy monitoring validation
- [ ] Battery/power efficiency testing
- [ ] Code: `tests/integration/`

### Phase 4️⃣: Deployment & Production (⏳ Planned)
- [ ] 3D enclosure design & printing
- [ ] PCB layout optimization (optional)
- [ ] Field testing in university classrooms
- [ ] Maintenance alert system
- [ ] Production documentation
- [ ] Code: `hardware/3d-models/`, `docs/deployment/`

---

## 🛠️ Hardware Requirements

### Bill of Materials (BoM)

| Component | Model | Qty | Cost (USD) | Notes |
|-----------|-------|-----|-----------|-------|
| Microcontroller | ESP32 DevKit v1 | 1 | $8-12 | UART compatible, WiFi enabled |
| mmWave Sensor | HLK-LD2410 | 1 | $15-20 | 24GHz radar, breathing detection |
| IR Emitter | 38kHz LED + resistor kit | 1 | $3-5 | High-power infrared |
| 3.3V Regulator | AMS1117-3.3 (optional) | 1 | $0.50 | Voltage regulation |
| USB Power | 5V 2A micro-USB | 1 | $3-5 | Standard charger |
| Resistors/Capacitors | Various | 1 set | $2-3 | Support components |
| **Total** | | | **$32-45** | Per unit cost |

### Wiring Diagram

```
ESP32 PIN CONFIGURATION:
┌─────────────────────────────────────┐
│ ESP32 Connections                   │
├─────────────────────────────────────┤
│ RX2 (GPIO 16) → LD2410 TX           │
│ TX2 (GPIO 17) → LD2410 RX           │
│ GPIO 2        → Internal LED (Demo) │
│ GPIO 5        → IR Emitter (Future) │
│ GND           → Common Ground       │
│ 5V            → Power Supply        │
└─────────────────────────────────────┘
```

## 🚀 Getting Started

### Prerequisites

- **Arduino IDE 2.0+** or PlatformIO
- **USB-to-Serial Drivers** (CH340/FTDI)
- **LD2410 Library** (included in `lib/`)
- **ESP32 Board Support** package

### Quick Start

1. **Clone the repository:**
```bash
git clone https://github.com/yourusername/ecocube-smart-energy.git
cd ecocube-smart-energy
```

2. **Install dependencies:**
```bash
# Using PlatformIO (recommended)
pio lib install

# Or manually install via Arduino IDE:
# Sketch → Include Library → Manage Libraries
# Search: "LD2410" by Nacho Postigo
```

3. **Flash firmware (Phase 1):**
```bash
cd firmware/1-led-basic
pio run -t upload
```

4. **Monitor serial output:**
```bash
pio device monitor -b 115200
```

### Expected Output (Phase 1)
```
Starting Radar with Ghost Filter...
OK! Let's filter those phantoms.
Dist: 45cm | Energy: 95
--> REAL PRESENCE! (LED ON)
Dist: 0cm | Energy: 0
... (Ignoring noise)
```

## 📊 Development Status

### Current Phase: Phase 2 - Milestone 1 ✅

| Phase | Milestone | Status | Progress | Deadline |
|-------|-----------|--------|----------|----------|
| 2️⃣ | 1. LED Control | ✅ Complete | 100% | Done |
| 2️⃣ | 2. Presence Filtering | 🔄 In Progress | 60% | This week |
| 2️⃣ | 3. IR Transmission | 📋 Planned | 0% | Next week |
| 2️⃣ | 4. Time Logic | 📋 Planned | 0% | Week 3 |
| 2️⃣ | 5. WiFi Analytics | 📋 Planned | 0% | Week 4 |
| 3️⃣ | Integration Testing | 📋 Planned | 0% | Month 2 |
| 4️⃣ | Production Deploy | 📋 Planned | 0% | Month 3 |

## 📁 Repository Structure

```
ecocube-smart-energy/
├── README.md                          # This file
├── ROADMAP.md                         # Detailed project roadmap
├── LICENSE                            # MIT License
│
├── firmware/                          # Arduino/C++ firmware
│   ├── 1-led-basic/                   # Phase 2.1: Basic LED test
│   │   ├── main.cpp                   # Main firmware code
│   │   ├── platformio.ini             # PlatformIO config
│   │   └── README.md                  # Phase-specific docs
│   ├── 2-presence-filtering/          # Phase 2.2: Ghost filter
│   ├── 3-ir-transmission/             # Phase 2.3: IR control
│   ├── 4-time-logic/                  # Phase 2.4: Timer logic
│   └── 5-wifi-analytics/              # Phase 2.5: Cloud connectivity
│
├── lib/                               # External libraries
│   ├── ld2410/                        # LD2410 mmWave library
│   └── IRremote/                      # IR control library (future)
│
├── hardware/                          # Hardware design files
│   ├── schematics/                    # Circuit diagrams
│   ├── 3d-models/                     # STEP/STL files for printing
│   └── bom/                           # Bill of materials
│
├── tests/                             # Test cases & validation
│   ├── unit/                          # Unit tests
│   └── integration/                   # System integration tests
│
├── docs/                              # Documentation
│   ├── research/                      # Academic research notes
│   │   ├── mmwave-technology.md       # mmWave fundamentals
│   │   ├── ir-protocols.md            # IR control protocols
│   │   └── case-study-ufmt.md         # UFMT energy analysis
│   ├── tutorials/                     # Step-by-step guides
│   │   ├── sensor-setup.md            # LD2410 calibration
│   │   ├── ir-learning.md             # IR signal cloning
│   │   └── wifi-config.md             # WiFi setup guide
│   ├── api/                           # REST/MQTT API docs
│   └── deployment/                    # Installation guides
│
└── data/                              # Logs, test results, analytics
    ├── sensor-logs/                   # Raw sensor data
    ├── test-results/                  # Performance metrics
    └── case-studies/                  # Real-world deployments
```

## 🔬 Technical Specifications

### LD2410 Sensor Specifications
- **Technology:** 24GHz FMCW Radar
- **Detection Range:** 0-6 meters (configurable)
- **Presence Types:** Dynamic (movement), Static (breathing)
- **Response Time:** ~100ms
- **Power Consumption:** 100-150mA @ 5V
- **Communication:** UART (115200 baud, 8N1)
- **Breathing Detection:** Sensitivity to 5mm chest displacement

### ESP32 Specifications
- **Processor:** Dual-core Xtensa 32-bit (240MHz)
- **RAM:** 520KB
- **Flash:** 4MB
- **WiFi:** 802.11b/g/n
- **UART Ports:** 3 (one used for LD2410)
- **GPIO Pins:** 25+ available
- **Power:** 5V USB, 80-160mA typical

## 📚 Documentation

Detailed documentation is available in the `docs/` folder:

- **`docs/research/`** - Academic background and technology research
- **`docs/tutorials/`** - Setup guides and how-to articles
- **`docs/api/`** - API reference for WiFi features
- **`docs/deployment/`** - Installation & production guidelines

## 🧪 Testing

### Running Tests
```bash
# Unit tests
pio test

# Integration tests (on real hardware)
cd tests/integration
./run-tests.sh
```

## 🤝 Contributing

Contributions are welcome! This is an academic portfolio project. If you'd like to contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

Please ensure code follows the Arduino coding style guidelines.

## 📖 Academic Context

This project is part of my **university portfolio** demonstrating:
- IoT embedded systems development
- Hardware integration (sensor + microcontroller)
- Wireless communication protocols
- Energy efficiency optimization
- Full system design & deployment

**Institution:** Universidade Federal de Mato Grosso (UFMT)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📧 Contact & Support

For questions, suggestions, or collaboration inquiries:
- **Email:** [Your Email]
- **GitHub Issues:** [Report bugs or request features](https://github.com/yourusername/ecocube-smart-energy/issues)

## 🙏 Acknowledgments

- **LD2410 Library:** [Nacho Postigo](https://github.com/nachopostigo)
- **ESP32 Community:** Arduino and PlatformIO ecosystem
- **UFMT:** Energy data and research facility
- **mmWave Technology Research:** HLK Electronics

---

**Last Updated:** January 31, 2025  
**Project Status:** 🚀 Active Development (Phase 2.1 Complete, Phase 2.2 In Progress)
