# Phase 2: Sensor Experimentation 🚀

**Current Phase - LED Blink Testing with LD2410C**

---

## 📌 Phase Overview

Phase 2 focuses on practical experimentation with the **HLK-LD2410C mmWave radar sensor**. The goal is to:
1. Successfully integrate the sensor with ESP32
2. Validate basic presence detection
3. Test the internal LED control
4. Understand sensor sensitivity and calibration
5. Document all findings for future phases

---

## ✅ Completed Milestones

### Milestone 1: Hardware Acquisition
- ✅ Purchased generic LD2410C sensor from AliExpress
- ✅ Received sensor without issues
- ✅ Verified pinout against datasheet

### Milestone 2: Initial Setup
- ✅ Connected LD2410C to ESP32 via UART
- ✅ Installed required libraries
- ✅ Uploaded first test code

### Milestone 3: LED Control Success
- ✅ **Successfully lit internal ESP32 LED when hand detected**
- ✅ Sensor responds to presence within ~1-2 meters
- ✅ Basic detection logic working

---

## 📊 Current Test Results

### Test 1: Basic Hand Detection
```
Date: January 2025
Condition: Controlled environment, indoor
Distance: ~50cm from sensor
Result: ✅ SUCCESS
- LED turned on when hand approached
- LED turned off when hand moved away
- Response time: ~500ms
- No false positives observed in 5-minute test
```

### Test 2: Breathing Detection
```
Status: PENDING
Expected: Detect micro-movements (breathing) at 1+ meters
Purpose: Validate core EcoCube functionality
```

### Test 3: Sensitivity Calibration
```
Status: PENDING
Purpose: Find optimal sensitivity for classroom environment
Variables to test:
  - Distance thresholds (1m, 2m, 3m)
  - Multiple person detection
  - Lighting conditions (dark, normal, bright)
  - Obstructions (furniture, whiteboards)
```

---

## 🔧 Hardware Setup

### Wiring Diagram
```
ESP32 Pin Layout:
┌─────────────────────────────────────┐
│         ESP32 DevKit (30-pin)       │
├─────────────────────────────────────┤
│ GPIO 16 (RX) ← LD2410C TX          │
│ GPIO 17 (TX) → LD2410C RX          │
│ GND         → LD2410C GND          │
│ 5V          ← LD2410C 5V (USB)     │
│ GPIO 2      ← Internal LED         │
└─────────────────────────────────────┘

LD2410C Pins:
1. 5V   (Red)   → ESP32 5V
2. GND  (Black) → ESP32 GND
3. RX   (Blue)  → ESP32 GPIO 17
4. TX   (Green) → ESP32 GPIO 16
```

### Components Checklist
- [x] ESP32 microcontroller
- [x] LD2410C mmWave radar sensor
- [x] USB cable (for programming and power)
- [x] Jumper wires (male-to-female)
- [ ] Breadboard (optional, for cleaner connections)
- [ ] Heatshrink tubing (optional, for protection)

---

## 💻 Code Overview

### Main File: `esp32_ld2410_led_test.ino`

**Purpose:** Test basic presence detection and LED control

**Key Functions:**
```cpp
void setup()
  - Initialize serial communication (115200 baud)
  - Initialize LD2410C sensor via UART (9600 baud)
  - Configure GPIO 2 as output (internal LED)

void loop()
  - Read presence data from sensor
  - Check if motion detected (motion == 1)
  - Turn on LED if motion detected
  - Turn off LED if no motion
  - Print debug information to serial
```

**Libraries Used:**
- `HardwareSerial` (built-in ESP32 serial library)
- Custom LD2410C protocol handler (see `ld2410_protocol.h`)

---

## 📈 Sensor Specifications (LD2410C)

| Parameter | Value |
|-----------|-------|
| **Frequency** | 24 GHz (mmWave) |
| **Detection Range** | 0.5 - 5 meters (adjustable) |
| **Detection Type** | Presence + Micro-movements |
| **Response Time** | ~200ms |
| **Power Consumption** | ~100mA (5V) |
| **Output** | UART (9600 baud) |
| **Dimensions** | ~35mm × 35mm × 10mm |
| **Operating Temp** | -10°C to +60°C |

### Advantages Over PIR Sensors
- ✅ Detects breathing (5mm micro-movements)
- ✅ Works through light and some barriers
- ✅ No warm-up time needed
- ✅ Immune to dust and insects
- ✅ 360° detection possible with antenna design

---

## 🔍 Next Steps (Phase 2 Continuation)

### Task 1: Multi-Distance Testing
```
Goal: Determine maximum reliable detection distance
Method:
  1. Position sensor in controlled environment
  2. Move hand from 10cm to 3m distance
  3. Record response at each 25cm increment
  4. Document minimum detection distance
Expected: Should detect up to ~2-3 meters
```

### Task 2: Breathing Detection Validation
```
Goal: Confirm sensor detects stationary human presence
Method:
  1. Sit still 1 meter away from sensor
  2. Monitor serial output for presence flag
  3. Test with different body positions (sitting, standing)
  4. Record time to detection
Expected: Detect breathing within 30 seconds
```

### Task 3: Multiple Person Detection
```
Goal: Validate sensor in realistic classroom scenario
Method:
  1. Have 2-3 people in room
  2. One person stays still, others move
  3. Verify presence detected continuously
  4. Document any gaps in detection
Expected: Continuous presence while any person in room
```

### Task 4: Environmental Testing
```
Goal: Test in various lighting and obstruction conditions
Variables:
  - Lights on/off
  - Sunlight exposure
  - Behind furniture/desks
  - Across whiteboard
Expected: Consistent detection regardless of conditions
```

---

## 📝 Test Log Template

Use this template to document all experiments:

```markdown
## Test: [Test Name]
**Date:** YYYY-MM-DD
**Time:** HH:MM - HH:MM
**Duration:** X minutes

### Setup
- Distance from sensor: ___ meters
- Lighting: [dark/normal/bright]
- Obstructions: [none/list items]
- Room temperature: ___ °C

### Procedure
[Describe what you did]

### Results
- Detection: [yes/no/partial]
- Response time: ___ ms
- False positives: ___
- False negatives: ___

### Notes
[Any observations, issues, or improvements]

### Next Actions
[What to test next based on results]
```

---

## 🐛 Troubleshooting

### Issue: No Serial Output
**Solution:**
- Check baud rate (should be 115200 for ESP32, 9600 for sensor)
- Verify USB cable is functioning
- Try different USB port
- Check GPIO 16/17 are not conflicting with other functions

### Issue: Sensor Not Responding
**Solution:**
- Verify 5V power connection (voltage should be 4.7-5.3V)
- Check UART connections are correct
- Try disconnecting/reconnecting sensor
- Check for loose jumper wires

### Issue: LED Not Lighting
**Solution:**
- Verify GPIO 2 is configured as output
- Check LED polarity (built-in LED should light on HIGH)
- Test with simple blink code first
- Check digitalWrite() is being called correctly

### Issue: False Positives/Negatives
**Solution:**
- Adjust sensitivity parameter in code
- Change detection distance thresholds
- Check for EMI interference (WiFi, other RF devices)
- Verify sensor is not too close to walls/obstacles

---

## 📚 Resources

### Documentation
- [LD2410C Datasheet](../datasheets/LD2410C_datasheet.pdf)
- [ESP32 Pin Reference](https://randomnerdtutorials.com/esp32-pinout-reference-gpios/)
- [Arduino UART Tutorial](https://www.arduino.cc/reference/en/language/functions/communication/serial/)

### References
- mmWave Radar Technology: [See main references.md](../resources/references.md)
- Similar Projects: See `resources/tutorials.md`

---

## 📊 Progress Tracker

```
Phase 2: Sensor Experimentation
├── ✅ Hardware acquisition (Jan 2025)
├── ✅ Initial integration (Jan 2025)
├── ✅ LED control test (Jan 2025)
├── ⏳ Distance calibration (In Progress)
├── ⏳ Breathing detection (Pending)
├── ⏳ Multi-person testing (Pending)
├── ⏳ Environmental testing (Pending)
└── ⏳ Phase completion & report (Pending)
```

---

## 🎯 Phase 2 Success Criteria

✅ = Complete | ⏳ = In Progress | ⚠️ = Need Work

| Criterion | Status | Evidence |
|-----------|--------|----------|
| Sensor responds to motion | ✅ | LED blinks on hand detection |
| Reliable distance detection | ⏳ | Testing in progress |
| Breathing detection works | ⏳ | To be tested |
| Multiple person handling | ⏳ | To be tested |
| Clean code & documentation | ⏳ | In progress |
| Phase report complete | ⏳ | To be written |

---

## 📢 Notes for Future Phases

**For Phase 3 (IR Control):**
- This phase validates the presence detection core
- Results will inform detection thresholds for Phase 3
- Current code structure can be extended for IR module

**For Phase 4 (Integration):**
- Sensor reliability metrics from this phase are critical
- Will need to combine with IR control logic
- Time-based thresholds will depend on test results

---

**Last Updated:** January 31, 2025
**Phase Status:** 🚀 Active - Initial Success, Continuing Validation
**Next Review:** After completing Task 4 environmental testing
