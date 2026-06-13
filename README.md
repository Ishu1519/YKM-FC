[README (2).md](https://github.com/user-attachments/files/28266858/README.2.md)
<div align="center">

<!-- ANIMATED HEADER -->
<img src="https://capsule-render.vercel.app/api?type=venom&height=200&text=YKM-FC&fontSize=80&color=0:0a0a0a,100:1a1a2e&stroke=00ff88&strokeWidth=2&fontColor=00ff88&animation=fadeIn" width="100%"/>

```
                        ╔════════════════════════════════════════════════════╗
                        ║  ██╗   ██╗██╗  ██╗███╗   ███╗    ███████╗ ██████╗  ║
                        ║  ╚██╗ ██╔╝██║ ██╔╝████╗ ████║    ██╔════╝██╔════╝  ║
                        ║   ╚████╔╝ █████╔╝ ██╔████╔██║    █████╗  ██║       ║
                        ║    ╚██╔╝  ██╔═██╗ ██║╚██╔╝██║    ██╔══╝  ██║       ║
                        ║     ██║   ██║  ██╗██║ ╚═╝ ██║    ██║     ╚██████╗  ║
                        ║     ╚═╝   ╚═╝  ╚═╝╚═╝     ╚═╝    ╚═╝      ╚═════╝  ║
                        ║                                                    ║
                        ║          ESP32-C3 · MICRO DRONE FLIGHT CONTROLLER  ║
                        ╚════════════════════════════════════════════════════╝
```

<img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=700&size=18&pause=1000&color=00FF88&center=true&vCenter=true&width=600&lines=First+ESP32-C3+Betaflight+Flight+Controller;Custom+Discrete+MOSFET+Motor+Driver;816+Coreless+%7C+WiFi+Autonomous+Hover;Built+from+scratch.+No+shortcuts." alt="Typing SVG" />

<br/>

![ESP32-C3](https://img.shields.io/badge/MCU-ESP32--C3-ff4444?style=for-the-badge&logo=espressif&logoColor=white)
![Betaflight](https://img.shields.io/badge/Firmware-Betaflight_4.4.0-00ff88?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-IN_DEVELOPMENT-ffaa00?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

</div>

---

## ⚡ What is YKM-FC?

> **The world's first documented ESP32-C3 micro drone flight controller running Betaflight.**

Most people said it couldn't be done. ESP32-C3 is flagged *experimental* in ESP-FC. No pre-built binary exists. No community reference. Built it anyway.

This is a **fully custom micro quadcopter** — from discrete MOSFET motor drivers to compiled-from-source Betaflight firmware — designed to autonomously stabilize and hover on a 1S LiPo, controlled over WiFi.

---

## 🔧 Hardware Stack

```
┌─────────────────────────────────────────────────────┐
│                   YKM-FC v0.1                       │
│                                                     │
│   [ESP32-C3] ──I2C──► [MPU6050]                     │
│       │                                             │
│    GPIO 1,2,3,4                                     │
│       │                                             │
│   [SI2306A] ×4  ◄── MOSFET Motor Driver             │
│   [SS14]    ×4  ◄── Flyback Protection              │
│       │                                             │
│   [816 Motors] ×4  (2CW + 2CCW)                     │
│       │                                             │
│   [300mAh 1S LiPo] ◄── SS14 diode → 3.4V ESP32      │
└─────────────────────────────────────────────────────┘
```

| Component | Choice | Why |
|-----------|--------|-----|
| **MCU** | ESP32-C3 | 160MHz, WiFi, 400KB RAM |
| **IMU** | MPU6050 | I2C, cheap, proven |
| **Motors** | 816 Coreless | 4×~28g thrust, 1S compatible |
| **Driver** | SI2306A MOSFET | 5.8A, low Rds, SOT-23 |
| **Protection** | SS14 Schottky | Flyback + power rail |
| **Battery** | 300mAh 1S LiPo | From salvaged TWS earphones |
| **Firmware** | ESP-FC (ESP-FC compiled for C3) | Betaflight compatible |

---

## 🏗️ Motor Layout

```
        FRONT
    
  M1(CW) ──┬── M2(CCW)
     ╲     │     ╱
      ╲    YKM  ╱
      ╱    │    ╲
     ╱     │     ╲
  M4(CCW)──┴── M3(CW)

         BACK

GPIO Map:  M1→GPIO3  M2→GPIO1  M3→GPIO2  M4→GPIO4
PWM:       20kHz  ·  8-bit  ·  No ESC
```

---

## ⚙️ Motor Driver Circuit (Per Motor × 4)

```
LiPo+ ──────────────────── Motor(+)
                                 │
                            [816 Motor]
                                 │
                          Motor(-) ──── DRAIN [SI2306A]
                          SOURCE ──────────────── GND
                          GATE ──── 100Ω ──── ESP32 GPIO
                          GATE ──── 10kΩ ──── GND

SS14: Cathode→LiPo+  Anode→DRAIN  [Flyback Protection]
100nF cap across motor terminals  [Decoupling]
```

**No ESCs. No integrated driver ICs. Pure discrete design.**
Cost per channel: ~₹15. Total driver cost: ~₹60.

---

## Real model look

<img width="1500" height="1500" alt="WhatsApp Image 2026-06-13 at 3 52 01 AM" src="https://github.com/user-attachments/assets/aa4fab95-bdf8-4e63-b461-b7aab244039d" />


## 🚀 Firmware

Built from source using **ESP-FC** by [@rtlopez](https://github.com/rtlopez/esp-fc) — compiled specifically for ESP32-C3 via PlatformIO.

```bash
# Clone and build
git clone https://github.com/rtlopez/esp-fc
cd esp-fc
pio run -e esp32c3 -t upload
```

> Pre-built binary for ESP32-C3 doesn't exist in official releases.  
> This repo provides the first compiled `firmware.bin` for C3.

### Flash via Web Flasher
1. Go to [espressif.github.io/esptool-js](https://espressif.github.io/esptool-js)
2. Upload `firmware_0x00.bin`
3. Set address to `0x0`
4. Flash

---

## 📡 Betaflight CLI Config

```bash
# Motor pin mapping
resource MOTOR 1 3
resource MOTOR 2 1
resource MOTOR 3 2
resource MOTOR 4 4

# PWM settings for brushed motors
set motor_pwm_protocol = PWM
set motor_pwm_rate = 20000

# Frame and gyro
set mixer = QUADX
set gyro_to_use = FIRST
set align_gyro = CW0
set align_acc = CW0

save
```

---

## 📊 Specifications

| Spec | Value |
|------|-------|
| **All-up Weight** | ~35g (target) |
| **Thrust (total)** | ~112g max |
| **Thrust/Weight** | ~3.2:1 |
| **Flight Time** | ~8 min (estimated) |
| **Control** | WiFi / Autonomous |
| **Frame Size** | ~50mm |
| **Battery** | 3.7V 300mAh 1S |
| **Hover Throttle** | ~50-60% |

---

## 🗺️ Roadmap

- [x] Firmware compiled for ESP32-C3
- [x] Betaflight configurator connected
- [x] Motor GPIO mapping defined
- [x] MPU6050 integration verified
- [x] Motor driver PCB assembled
- [x] First armed spin test
- [ ] PID tuning on test rig
- [ ] First autonomous hover
- [ ] WiFi telemetry stream
- [ ] Conference/paper submission

---

## 🧠 Why This Matters

ESP32-C3 has no hardware FPU, single core, and was flagged *experimental* by ESP-FC maintainers. No binary existed. No one had publicly documented running Betaflight on C3.

This project proves it's possible — and documents every step so others don't start from zero.

---

## 👤 Author

**Ishant Jaiswal**  
B.Tech Robotics & Automation · India  
Building robots from scratch since before it was documented.


---

<div align="center">

**If this helped you — star it. If you improved it — PR it.**

![Footer](https://capsule-render.vercel.app/api?type=waving&color=0:0a0a0a,100:00ff88&height=80&section=footer)

</div>
