# ESP32-C3 Laser Engraver

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![MCU](https://img.shields.io/badge/MCU-ESP32--C3FH4-green)](https://www.espressif.com/en/products/socs/esp32-c3)
[![Framework](https://img.shields.io/badge/Framework-Arduino-orange)](https://www.arduino.cc/)
[![Website](https://img.shields.io/badge/Website-GitHub%20Pages-brightgreen)](https://carl-debug2025.github.io/ESP32-C3-Laser-Engraver/)

**[中文文档](README.md) | English**

🌐 **Project Website**: [https://carl-debug2025.github.io/ESP32-C3-Laser-Engraver/](https://carl-debug2025.github.io/ESP32-C3-Laser-Engraver/)

> **All-in-One Miniature Laser Engraver Solution**
>
> Compact laser engraver based on ESP32-C3 with integrated web control interface and wireless BLE operation.

---

## 📌 Highlights

- ✅ **All-in-One PCB Design**: No more modular assembly, ready to use
- ✅ **ESP32-C3FH4 Chip**: Built-in Flash, RISC-V single-core 160MHz
- ✅ **Web Control Interface**: No software installation needed, browser-based control
- ✅ **BLE Communication**: Wireless G-code transmission
- ✅ **Real-time Trajectory Preview**: Visual engraving process
- ✅ **Open Source**: GPLv3 licensed

---

## 📸 Product Showcase

![Product Photo](hardware/images/product-front.jpg)

> All-in-one PCB design, ready to use without module assembly

---

## 📖 Acknowledgments

This project is a derivative work of the following open-source projects:

### Firmware

**Based on [Grbl_ESP32](https://github.com/bdring/Grbl_Esp32)**
Original Author: Bart Dring
License: GNU General Public License v3.0

Grbl_ESP32 is an ESP32 port of [Grbl](https://github.com/gnea/grbl) (Original Author: Sungeun K. Jeon). This project includes the following improvements:

- **ESP32-C3 Adaptation**: Modified low-level drivers for ESP32-C3FH4 chip
- **Hardware Configuration Optimization**: Redesigned pin mapping for all-in-one PCB
- **Motion Parameter Tuning**: Adapted for specific stepper motors and lead screws

### Software

**G-code generation algorithm inspired by [LaserGRBL](https://github.com/arkypita/LaserGRBL)**
Original Author: arkypita
License: GNU General Public License v3.0

This project developed a brand new web control interface with core image processing algorithms inspired by LaserGRBL:

- **Smart Skip-White Optimization**: Fast travel over blank areas for improved efficiency
- **Adaptive Speed Control**: Dynamic feed rate adjustment based on path complexity
- **Real-time Status Monitoring**: Bidirectional data flow via BLE communication

### Original Contributions

In compliance with GPLv3 license requirements, this project fully open sources:

1. **Hardware Design**
   - All-in-one PCB design (no modular components)
   - ESP32-C3FH4 minimal system circuit
   - A4988 stepper driver integration solution

2. **Firmware Improvements**
   - `Grbl_Esp32/src/Machines/esp32_c3_write.h`: C3-specific configuration
   - ESP32-C3 RMT/I2S compatibility fixes
   - Optimized Alarm state handling after disabling hard limits

3. **Software Innovation**
   - `G代码生成器.html`: Web control interface based on Web Bluetooth API
   - Real-time trajectory drawing and status monitoring
   - Chinese interface with preset parameter optimization

---

## 📦 Quick Start

### Hardware Requirements

| Component | Model | Qty | Notes |
|-----------|-------|-----|-------|
| Main Board | ESP32-C3FH4 | 1 | Custom PCB design |
| Stepper Driver | A4988 | 2 | 16x microstepping mode |
| Stepper Motor | PL15S-020 (18°) | 2 | Optical drive motors |
| Laser Module | 250-500mW PWM | 1 | 5V TTL control |
| Power Supply | 12V 2A | 1 | USB PD trigger optional |

### Software Compilation

```bash
# 1. Clone repository
git clone https://github.com/Carl-debug2025/ESP32-C3-Laser-Engraver.git
cd ESP32-C3-Laser-Engraver

# 2. Install PlatformIO (VSCode extension)

# 3. Build firmware
pio run -e esp32c3

# 4. Upload firmware
pio run -e esp32c3 -t upload --upload-port COMx
```

### First-time Setup

```bash
# Serial connection (115200 baud rate)
$RST=$        # Reset all settings
$$            # View current parameters
```

---

## 🎮 Usage

### Method 1: Web Control Interface (Recommended)

1. Open `G代码生成器.html` (Chrome browser)
2. Click **🔗 Connect Bluetooth**, select `GRBL_ESP32C3`
3. Click **🔓 Unlock** (required after power-on)
4. Upload image → Adjust parameters → Generate G-code
5. Click **📤 Send to Engrave**

### Method 2: Serial Commands

```bash
$X            # Unlock alarm state
G92 X0 Y0     # Set current position as origin
G0 X10 Y10    # Rapid move
G1 X20 S800   # Engraving move (80% laser power)
M5            # Turn off laser
?             # Query status
```

---

## ⚙️ Core Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| Work Area | 42×42 mm | X/Y axis max travel |
| Positioning Accuracy | ~0.01 mm | Theoretical resolution |
| Max Speed | 3000 mm/min | Recommended 500-1000 |
| Steps/mm | 106.67 | 20 steps/rev × 16 microstepping ÷ 3mm |
| Laser PWM | 5kHz, 12-bit | GPIO3 output |

### GRBL Key Settings

```bash
$100=106.67   # X-axis steps/mm
$101=106.67   # Y-axis steps/mm
$110=3000     # X-axis max rate (mm/min)
$111=3000     # Y-axis max rate (mm/min)
$120=100      # X-axis acceleration (mm/s²)
$121=100      # Y-axis acceleration (mm/s²)
$130=42       # X-axis max travel (mm)
$131=42       # Y-axis max travel (mm)
$3=2          # Y-axis direction invert
$1=255        # Stepper always enabled
```

---

## 🔧 Hardware Connections

### Pin Definitions

| Function | GPIO | Description |
|----------|------|-------------|
| X_STEP | GPIO0 | X-axis step pulse |
| X_DIR | GPIO4 | X-axis direction |
| Y_STEP | GPIO1 | Y-axis step pulse |
| Y_DIR | GPIO5 | Y-axis direction |
| ENABLE | GPIO10 | Motor enable (active low) |
| LASER_PWM | GPIO3 | Laser power control |

### Wiring Diagram

```
ESP32-C3FH4                 A4988 (X-axis)
┌─────────┐                ┌─────────┐
│ GPIO0   ├───────────────►│ STEP    │
│ GPIO4   ├───────────────►│ DIR     │
│ GPIO10  ├────────────────┤ EN      │
│         │                │         │
│         │   A4988 (Y-axis)  VMOT ◄──┼─ 12V
│ GPIO1   ├───────────────►│ STEP    │
│ GPIO5   ├───────────────►│ DIR     │
│ GPIO10  ├────────────────┤ EN      │
│         │                │         │
│ GPIO3   ├───────────────►│ PWM     │ Laser Module
└─────────┘                └─────────┘
```

---

## 📁 Project Structure

```
├── Grbl_Esp32/              # Firmware
│   ├── src/
│   │   ├── Machines/
│   │   │   └── esp32_c3_write.h  # ⭐ C3-specific config
│   │   ├── Grbl.cpp              # System initialization
│   │   ├── GCode.cpp             # G-code parser
│   │   ├── Protocol.cpp          # Communication protocol
│   │   ├── Planner.cpp           # Motion planner
│   │   ├── Stepper.cpp           # Stepper controller
│   │   └── WebUI/
│   │       └── BLEConfig.cpp     # BLE service
│   └── Grbl_Esp32.ino            # Arduino entry point
├── hardware/                  # Hardware design files
│   ├── schematic.pdf          # ⭐ Circuit schematic
│   ├── bom.csv                # Bill of materials
│   ├── gerber/                # Gerber files (for PCB manufacturing)
│   └── images/                # Hardware photos
│       ├── pcb_front.jpg
│       ├── pcb_back.jpg
│       └── product-front.jpg
├── G代码生成器.html           # ⭐ Web control interface
├── LICENSE                    # GPLv3 license
├── README.md                  # Chinese documentation
├── README_EN.md               # English documentation
├── CHANGELOG.md               # Changelog
└── CONTRIBUTING.md            # Contribution guidelines
```

---

## ⚠️ Safety Precautions

### Three Critical Warnings

| ❌ Forbidden Operation | Consequence |
|----------------------|-------------|
| Hot-plugging motors | Burn A4988 driver |
| Long operation without heatsink | Chip overheating |
| Looking directly at laser beam | Permanent eye damage |

### Laser Safety

- ✅ Wear laser safety glasses (appropriate wavelength)
- ✅ Ensure ventilation (engraving produces smoke)
- ✅ Keep fire extinguisher nearby (wood may ignite)
- ✅ Have emergency stop switch ready

---

## 🐛 Troubleshooting

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Alarm state on power-on | Default state after disabling limits | Click "Unlock" (normal behavior) |
| Motors not moving | EN pin logic error | Check $4 setting |
| Y-axis direction reversed | Configuration inverted | This is correct, no need to change |
| Inaccurate positioning | Wrong steps/mm | Recalculate $100/$101 |
| Bluetooth connection failed | ESP32 not restarted | Power cycle |

---

## 🤝 Contributing

Issues and Pull Requests are welcome!

### Development Environment

- VSCode + PlatformIO
- ESP-IDF v4.4+
- Chrome browser (Web Bluetooth support)

### Code Style

- Follow existing Grbl code style
- Add comments for new features
- GPLv3 license requirement: mark changes in modified files

---

## 📜 License

This project is licensed under the **GNU General Public License v3.0**.

### Your Rights

- ✅ Freedom to use, study, and modify this software
- ✅ Freedom to distribute this software and its modifications

### Your Obligations

- ⚠️ **Must preserve original license notices**
- ⚠️ **Must attribute original authors**
- ⚠️ **Modified versions must use the same license**
- ⚠️ **Must provide source code (or means to obtain it)**

### Original Works

- **Grbl** - Copyright © 2011-2016 Sungeun K. Jeon
- **Grbl_ESP32** - Copyright © 2018-2021 Bart Dring
- **LaserGRBL** - Copyright © arkypita

See [LICENSE](./LICENSE) file for details.

---

## 📚 References

- [Grbl Wiki](https://github.com/gnea/grbl/wiki)
- [Grbl_ESP32 Project](https://github.com/bdring/Grbl_Esp32)
- [LaserGRBL Project](https://github.com/arkypita/LaserGRBL)
- [ESP32-C3 Technical Reference Manual](https://www.espressif.com/sites/default/files/documentation/esp32-c3_datasheet_en.pdf)
- [A4988 Datasheet](https://www.pololu.com/file/0J450/A4988.pdf)

---

## 📧 Contact

- Issues: [GitHub Issues](https://github.com/Carl-debug2025/ESP32-C3-Laser-Engraver/issues)
- Pull Requests are welcome!

---

**Happy Engraving! 🎉**
