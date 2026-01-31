# 🛣️ EcoCube Project Roadmap

Complete development roadmap from initial research through production deployment.

**Last Updated:** January 31, 2025  
**Current Status:** Phase 2.1 Complete ✅ | Phase 2.2 In Progress 🔄

---

## 📍 Overview Timeline

```
PHASE 1          PHASE 2                      PHASE 3        PHASE 4
Research    Hardware Development         Integration    Production
   │         │  │  │  │  │              │               │
   └─────────┴──┴──┴──┴──┴──────────────┴───────────────┴────────→ Time
       Done      Milestones 1-5         Full System     Real World
                (Jan-Feb 2025)          (Feb-Mar 2025)  (Mar+ 2025)
```

---

## 🔍 Phase 1: Foundational Research ✅ COMPLETE

### Objective
Establish technical foundation and viability of the IoT solution.

### 1.1 Literature Review ✅
- **Task:** Research mmWave radar technology capabilities
- **Deliverable:** `docs/research/mmwave-technology.md`
- **Key Findings:**
  - LD2410 can detect breathing patterns (5mm micro-movements)
  - Frequency: 24GHz FMCW (Frequency Modulated Continuous Wave)
  - Range: 0-6 meters with configurable sensitivity
  - Advantages over PIR: detects stationary presence

### 1.2 Sensor Feasibility Analysis ✅
- **Task:** Evaluate LD2410 specifications for classroom use
- **Deliverable:** LD2410 datasheet analysis
- **Decision:** ✅ APPROVED - Ideal for educational environments
  - Power consumption: ~150mA (acceptable for 5V USB)
  - UART communication: Compatible with ESP32
  - Cost: $15-20 (budget-friendly)
  - Robustness: Immune to light, dust, temperature variations

### 1.3 IR Protocol Research ✅
- **Task:** Map IR codes for different AC brands
- **Deliverable:** `docs/research/ir-protocols.md`
- **Covered Brands:**
  - Samsung air conditioners
  - LG air conditioners
  - Carrier air conditioners
  - Generic 38kHz IR protocols

### 1.4 Energy Consumption Analysis ✅
- **Task:** Quantify energy savings potential
- **Deliverable:** `docs/research/case-study-ufmt.md`
- **Key Results:**
  - Air conditioning: 60-70% of classroom energy
  - Average AC runtime: 8 hours/day, often with empty rooms
  - Potential savings: 15-25% of total electricity cost
  - ROI: < 6 months for single room

### 1.5 Technical Feasibility Assessment ✅
- **Task:** Verify all components integrate properly
- **Deliverable:** Component compatibility matrix
- **Result:** ✅ All components compatible, no conflicts

---

## ⚙️ Phase 2: Hardware Proof of Concept 🚀 IN PROGRESS

### Objective
Build and test each hardware subsystem independently, then integrate.

### Timeline
```
Week 1    Week 2    Week 3    Week 4      Week 5+
│─────────│─────────│─────────│─────────│─────...
M1: LED   M2: Filter M3: IR   M4: Timer  M5: WiFi
✅         🔄       ⏳        ⏳         ⏳
```

---

### 🎯 Milestone 1: Basic LED Control ✅ COMPLETE

**Status:** ✅ COMPLETE (Jan 28, 2025)  
**Duration:** 3 days  
**Code Location:** `firmware/1-led-basic/`

#### Objective
Verify LD2410 sensor communication with ESP32 and confirm detection works.

#### Tasks Completed
- [x] Set up LD2410 sensor with correct UART pins (RX2/TX2)
- [x] Install ld2410 library in Arduino environment
- [x] Write basic detection code
- [x] Integrate LED feedback (GPIO 2)
- [x] Test with hand gestures and movements
- [x] Calibrate sensor sensitivity thresholds

#### Hardware Configuration
```cpp
RX_PIN: GPIO 16 (UART2 RX)
TX_PIN: GPIO 17 (UART2 TX)
LED_PIN: GPIO 2  (On-board LED)
BAUD_RATE: 256000
```

#### Code Architecture
```
setup()
├── Initialize Serial (debug at 115200)
├── Initialize Serial2 (LD2410 at 256000)
├── Configure LED pin as OUTPUT
└── Initialize LD2410 sensor

loop()
├── Read LD2410 data
├── Get distance and energy values
├── Apply basic threshold logic
├── Control LED based on detection
└── Send debug output to Serial
```

#### Test Results
- ✅ Sensor detection: Working
- ✅ LED toggle: Working
- ✅ Serial communication: Stable
- ✅ Response time: ~100ms
- ✅ False positives: Minimal (with basic filtering)

#### Key Code Features
```cpp
// Get detection data
int distancia = radar.movingTargetDistance();
int energiaMov = radar.movingTargetEnergy();
int energiaEst = radar.stationaryTargetEnergy();

// Control logic
if(distance < 60 && totalEnergy > 90) {
    digitalWrite(LED_PIN, HIGH);  // Real presence
} else {
    digitalWrite(LED_PIN, LOW);   // Empty/noise
}
```

#### Deliverables
- ✅ `firmware/1-led-basic/main.cpp` - Working firmware
- ✅ `firmware/1-led-basic/platformio.ini` - Build config
- ✅ `firmware/1-led-basic/README.md` - Phase documentation
- ✅ Test logs and serial output records

---

### 🎯 Milestone 2: Presence Detection with Filtering 🔄 IN PROGRESS

**Status:** 🔄 IN PROGRESS (Started Jan 28, 2025)  
**Estimated Completion:** Feb 1, 2025  
**Duration:** 4-5 days  
**Code Location:** `firmware/2-presence-filtering/`

#### Objective
Implement advanced ghost-filtering algorithm to eliminate false positives from sensor noise, reflections, and environmental interference.

#### Problem Being Solved
The basic detection (Milestone 1) triggers on any energy spike, including:
- Reflections from walls
- Phantom signals from electronic equipment
- Interference from phones, WiFi routers
- False positives from vehicle movement outside windows

**Solution:** Multi-level filtering using energy thresholds and temporal analysis.

#### Tasks in Progress
- [x] Analyze raw sensor data patterns (moving vs stationary targets)
- [x] Establish energy baseline for different scenarios
- [ ] Implement energy threshold filtering (target energy > 40)
- [ ] Add temporal filtering (duration-based confirmation)
- [ ] Implement hysteresis (prevent rapid on/off)
- [ ] Test in different lighting conditions
- [ ] Test with window reflections
- [ ] Test with WiFi and electronic interference
- [ ] Document filtering algorithm

#### Algorithm Design

**Level 1: Energy Filtering**
```cpp
// Distinguish real presence from noise
int movingEnergy = radar.movingTargetEnergy();
int stationaryEnergy = radar.stationaryTargetEnergy();
int totalEnergy = movingEnergy + stationaryEnergy;

if(totalEnergy > ENERGY_THRESHOLD) {
    // Real presence detected
    presence = true;
}
```

**Level 2: Temporal Filtering**
```cpp
// Require sustained presence (not single spike)
if(presence) {
    sensorReadingCount++;
    if(sensorReadingCount >= MIN_READINGS) {
        // Confirmed presence after N consecutive readings
        confirmPresence = true;
        sensorReadingCount = 0;
    }
} else {
    sensorReadingCount = 0;
}
```

**Level 3: Hysteresis**
```cpp
// Prevent rapid on/off oscillation
// Turn on at 80 energy, turn off at 40 energy
if(totalEnergy > 80) {
    confirmed_state = PRESENT;
} else if(totalEnergy < 40) {
    confirmed_state = EMPTY;
}
// In between: maintain previous state
```

#### Expected Improvements
- False positive rate: < 5% (down from 15-20%)
- Response accuracy: > 95% for real presence
- Power consumption: ~150mA (stable)

#### Testing Checklist
- [ ] Test 1: Hand proximity detection
- [ ] Test 2: Window reflections (fail case)
- [ ] Test 3: Stationary person (breathing detection)
- [ ] Test 4: WiFi router nearby
- [ ] Test 5: Phone in room
- [ ] Test 6: Vehicle outside window
- [ ] Test 7: Rapid on/off prevention
- [ ] Test 8: Thermal stability

#### Deliverables (Expected)
- `firmware/2-presence-filtering/main.cpp` - Enhanced firmware
- `firmware/2-presence-filtering/filter.h` - Filter algorithm library
- `firmware/2-presence-filtering/README.md` - Documentation
- `tests/unit/test-filter-logic.cpp` - Unit tests
- Test report with false positive analysis

---

### 🎯 Milestone 3: IR Signal Transmission ⏳ PLANNED

**Status:** ⏳ PLANNED (Start: Feb 1, 2025)  
**Estimated Duration:** 5-6 days  
**Code Location:** `firmware/3-ir-transmission/`

#### Objective
Integrate IR emitter and implement IR control protocol to turn off AC units.

#### Tasks to Complete
- [ ] Wire 38kHz IR LED to GPIO 5
- [ ] Install IRremote or similar library
- [ ] Learn IR signal format (38kHz, NEC protocol)
- [ ] Create IR code library for common AC brands
- [ ] Implement signal transmission function
- [ ] Test with actual AC units (Samsung, LG, Carrier)
- [ ] Measure signal strength and range
- [ ] Test in sunlight conditions
- [ ] Document IR protocol for future brands

#### Hardware Integration
```
GPIO 5 ──[220Ω]──[IR LED]──GND
         (Resistor)
```

#### Code Structure
```cpp
class IRController {
    void sendPowerOff(ACBrand brand);
    void sendPowerOn(ACBrand brand);
    void sendTemperature(int temp);
};

IRController ir;
ir.sendPowerOff(AC_BRAND_SAMSUNG);  // Turn off AC
```

#### AC Brand Support (Priority)
1. **Samsung** - Common in UFMT
2. **LG** - Secondary target
3. **Carrier** - Industrial model
4. **Generic 38kHz** - Fallback

#### Expected Challenges
- Different IR protocols between brands
- Signal strength in sunlight
- Learning correct codes from existing remotes
- Ensuring 100% reliability

#### Testing Scenarios
- [ ] Test from 1 meter (direct line)
- [ ] Test from 3 meters (classroom distance)
- [ ] Test from various angles
- [ ] Test in daylight
- [ ] Test with reflections
- [ ] Test with AC already on
- [ ] Test with AC already off

#### Deliverables
- `firmware/3-ir-transmission/main.cpp`
- `firmware/3-ir-transmission/IRController.cpp/h`
- `hardware/ir-codes/` - Database of IR codes by brand
- IR protocol documentation
- Test results with actual AC units

---

### 🎯 Milestone 4: Time-Based Decision Logic ⏳ PLANNED

**Status:** ⏳ PLANNED (Start: Feb 6, 2025)  
**Estimated Duration:** 4-5 days  
**Code Location:** `firmware/4-time-logic/`

#### Objective
Implement intelligent timer logic to avoid rapid AC cycling and energy waste.

#### Core Logic
```
Room Empty → Timer Starts (t=0)
     ↓
Wait 10 minutes → If still empty: Send AC OFF
     ↓
Room Occupied → Cancel timer, turn AC ON if needed
```

#### Tasks to Complete
- [ ] Implement state machine (EMPTY, TRANSITION, OCCUPIED)
- [ ] Add configurable empty-room timeout (10-30 minutes)
- [ ] Add hysteresis to prevent rapid on/off
- [ ] Log state transitions for debugging
- [ ] Add override capability (manual button)
- [ ] Test state transitions
- [ ] Validate energy savings with logic

#### Timer Configuration
```cpp
#define EMPTY_ROOM_TIMEOUT 600000  // 10 minutes in ms
#define TRANSITION_BUFFER 30000    // 30 seconds buffer
```

#### State Machine
```
┌─────────────┐
│  OCCUPIED   │◄─── Person detected → Cancel timer
└──────┬──────┘
       │
    No movement detected for 10 min
       │
       ▼
┌──────────────┐
│ TRANSITION   │ ◄─── Wait for confirmation
└──────┬───────┘
       │
    10 minutes elapsed
       │
       ▼
┌─────────────┐
│    EMPTY    │ ◄─── Send AC OFF command
└──────┬──────┘
       │
   Person detected
       │
       ▼ (back to OCCUPIED)
```

#### Testing Scenarios
- [ ] Test timeout accuracy
- [ ] Test rapid on/off prevention
- [ ] Test with different timeout values
- [ ] Test manual override
- [ ] Test state logging
- [ ] Simulate room occupancy patterns
- [ ] Energy consumption analysis with timer

#### Deliverables
- `firmware/4-time-logic/main.cpp`
- `firmware/4-time-logic/StateManager.cpp/h`
- State machine diagram
- Timer configuration guide
- Test logs and energy analysis

---

### 🎯 Milestone 5: WiFi Integration & Analytics ⏳ PLANNED

**Status:** ⏳ PLANNED (Start: Feb 10, 2025)  
**Estimated Duration:** 5-7 days  
**Code Location:** `firmware/5-wifi-analytics/`

#### Objective
Connect device to WiFi network and enable cloud-based monitoring and analytics.

#### Key Features
- Real-time occupancy status via web dashboard
- Historical occupancy data
- Energy consumption estimates
- Maintenance alerts
- Remote configuration

#### Tasks to Complete
- [ ] Implement WiFi connection (WPA2)
- [ ] Set up MQTT broker connection
- [ ] Send occupancy data to cloud
- [ ] Create REST API endpoints
- [ ] Build web dashboard (simple HTML)
- [ ] Implement data logging (SD card optional)
- [ ] Test reliability and uptime
- [ ] Implement OTA (Over-The-Air) updates
- [ ] Add security (API tokens)

#### WiFi Configuration
```cpp
const char* WIFI_SSID = "UFMT-WiFi";
const char* WIFI_PASS = "password";
const char* MQTT_BROKER = "192.168.1.100";
```

#### Data Reported
```json
{
  "device_id": "ecocube-001",
  "room": "Classroom A1",
  "timestamp": "2025-02-10T14:30:00Z",
  "occupied": true,
  "presence_duration_sec": 3600,
  "last_ac_command": "OFF",
  "energy_saved_kwh": 2.5,
  "temperature": 22.5,
  "signal_strength": -65
}
```

#### API Endpoints (REST)
```
GET  /api/device/{id}/status
GET  /api/device/{id}/history?days=30
POST /api/device/{id}/config
GET  /api/analytics/rooms
POST /api/maintenance/alert
```

#### Web Dashboard Features
- Real-time room status (Occupied/Empty)
- Occupancy timeline (last 24 hours)
- Energy savings graph
- Alert notifications
- Configuration panel

#### Testing
- [ ] WiFi connection stability
- [ ] Data transmission reliability
- [ ] Dashboard responsiveness
- [ ] API endpoint validation
- [ ] Security testing
- [ ] Load testing (multiple devices)

#### Deliverables
- `firmware/5-wifi-analytics/main.cpp`
- `firmware/5-wifi-analytics/WiFiManager.cpp/h`
- `firmware/5-wifi-analytics/MQTTClient.cpp/h`
- REST API documentation (`docs/api/rest-api.md`)
- Web dashboard code (`frontend/`)
- Security guide

---

## 🧪 Phase 3: Integration & Testing ⏳ PLANNED

### Objective
Combine all components into complete system and validate in real-world scenarios.

### Timeline: February 15 - March 15, 2025

#### 3.1 Full System Integration Test
- [ ] Assemble all components (sensor + IR + WiFi)
- [ ] Run all firmware modules together
- [ ] Test component interactions
- [ ] Verify power consumption
- [ ] Create integration test suite

#### 3.2 Real AC Unit Testing
- [ ] Test with Samsung AC unit
- [ ] Test with LG AC unit
- [ ] Test with Carrier AC unit
- [ ] Measure IR signal strength
- [ ] Document successful codes

#### 3.3 Temperature Monitoring
- [ ] Add temperature sensor (optional)
- [ ] Log temperature before/after AC shutdown
- [ ] Verify AC actually turns off
- [ ] Measure cooling performance

#### 3.4 Energy Monitoring
- [ ] Connect power meter to AC
- [ ] Measure energy consumption with/without EcoCube
- [ ] Calculate actual energy savings
- [ ] Extrapolate annual savings

#### 3.5 Reliability Testing
- [ ] 24-hour continuous operation test
- [ ] Extreme temperature testing
- [ ] Humidity resistance test
- [ ] WiFi reconnection testing
- [ ] MTBF (Mean Time Between Failures) calculation

#### Deliverables
- `tests/integration/` - Full test suite
- Integration test report
- Performance metrics
- Reliability analysis
- Known issues & limitations document

---

## 🏭 Phase 4: Deployment & Production ⏳ PLANNED

### Objective
Prepare device for real-world university deployment.

### Timeline: March 15 - April 30, 2025

#### 4.1 Hardware Refinement
- [ ] Design 3D-printed enclosure
- [ ] Test enclosure thermal properties
- [ ] Add mounting hardware
- [ ] Print prototype enclosures
- [ ] Test in actual classroom environment

#### 4.2 Field Testing (Pilot Program)
- [ ] Select 3-5 pilot classrooms at UFMT
- [ ] Install EcoCube in each
- [ ] Monitor for 2-3 weeks
- [ ] Collect user feedback
- [ ] Log performance data

#### 4.3 Firmware Optimization
- [ ] Optimize power consumption
- [ ] Fine-tune sensor thresholds
- [ ] Improve WiFi reliability
- [ ] Fix any discovered bugs
- [ ] Implement v1.0 release

#### 4.4 Documentation & Training
- [ ] Create installation manual
- [ ] Create troubleshooting guide
- [ ] Train maintenance staff
- [ ] Create video tutorial
- [ ] Build support documentation

#### 4.5 Scaling & Production
- [ ] Finalize BOM (Bill of Materials)
- [ ] Source components in bulk
- [ ] Set up assembly process
- [ ] Create QA checklist
- [ ] Plan rollout schedule

#### 4.6 Maintenance System
- [ ] Create alerts for malfunction
- [ ] Set up remote diagnostics
- [ ] Plan regular calibration
- [ ] Document common issues
- [ ] Build support portal

#### Deliverables
- `hardware/3d-models/ecocube-enclosure.step`
- `docs/deployment/installation-guide.md`
- `docs/deployment/troubleshooting.md`
- `docs/deployment/maintenance-manual.md`
- Field test results
- Lessons learned document
- Production BOM
- Assembly instructions

---

## 📊 Success Metrics

### Phase 1 ✅
- [x] All research questions answered
- [x] No technical blockers identified
- [x] Component compatibility confirmed

### Phase 2 (In Progress)
- [ ] Milestone 1: LED control working ✅
- [ ] Milestone 2: False positives < 5%
- [ ] Milestone 3: AC units respond to IR signals
- [ ] Milestone 4: Timer logic prevents oscillation
- [ ] Milestone 5: Real-time data sent to cloud

### Phase 3
- [ ] System operates 24h without errors
- [ ] Energy savings: 15-25% for pilot rooms
- [ ] Reliability: 99%+ uptime
- [ ] User satisfaction: 4/5 stars

### Phase 4
- [ ] Deployment in 10+ classrooms
- [ ] Annual energy savings: 5,000+ kWh
- [ ] Cost per unit: < $50
- [ ] ROI: < 6 months
- [ ] Portfolio quality: Impressive for graduation

---

## 🔧 Build & Deployment Tools

### Development Environment
- **IDE:** PlatformIO (recommended) or Arduino IDE
- **Language:** C++ (Arduino dialect)
- **Board:** ESP32 Dev Kit v1
- **Compiler:** GCC for Xtensa

### Version Control
- **Repository:** GitHub (public)
- **Branching:** Git Flow (main, develop, features)
- **Commits:** Semantic versioning

### CI/CD (Future)
- GitHub Actions for automated builds
- Firmware version management
- Release automation

### Documentation
- Markdown files in `docs/`
- Code comments (Doxygen compatible)
- README files for each phase

---

## 📈 Risk Management

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|-----------|
| LD2410 false positives | Medium | High | Implement multi-level filtering |
| IR signal unreliability | Low | High | Test with multiple brands, backup protocol |
| WiFi connectivity issues | Medium | Medium | Implement fallback to local operation |
| Component shortage | Low | Medium | Source from multiple suppliers |
| Power consumption too high | Low | Medium | Optimize code, consider sleep modes |
| AC unit incompatibility | Low | Medium | Build IR code database, document |

---

## 🎓 Academic Learning Outcomes

This project demonstrates proficiency in:

1. **Embedded Systems**
   - Microcontroller programming (ESP32)
   - Hardware-software integration
   - Real-time processing

2. **IoT Architecture**
   - Sensor integration
   - Wireless communication (WiFi, MQTT)
   - Cloud connectivity

3. **Electrical Engineering**
   - Circuit design
   - Power management
   - Signal transmission (IR)

4. **Software Engineering**
   - Code organization & architecture
   - State machines
   - Testing & validation

5. **Project Management**
   - Planning & roadmapping
   - Risk management
   - Milestone tracking

6. **Energy Efficiency**
   - Power consumption optimization
   - Energy monitoring
   - Sustainability impact

---

## 📞 Contact & Support

**Project Lead:** [Your Name]  
**Repository:** https://github.com/yourusername/ecocube-smart-energy  
**Issues:** https://github.com/yourusername/ecocube-smart-energy/issues  

---

**Last Updated:** January 31, 2025  
**Next Update:** February 3, 2025 (after Milestone 2 completion)
