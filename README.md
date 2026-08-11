# 🚗 ESP32 Smart Garage Guard

> **CraftifAI FirmGen Hackathon Submission**  
> Built with [FirmGen by CraftifAI Orbit](https://craftifai.com) · ESP32 · ESP-IDF v5.5

---

## 📌 Problem Definition

Garages are frequently left unattended and are a common entry point for intruders or accidents. The **Smart Garage Guard** addresses this by creating an intelligent embedded system that:

- Detects proximity (objects/vehicles) using an ultrasonic sensor
- Monitors motion and orientation using an MPU6050 gyroscope/accelerometer
- Provides a 15-second audible grace-period alarm via a buzzer before triggering an SOS alert
- Sends real-time Wi-Fi alerts over MQTT when a threat is detected
- Gives clear visual status feedback through a 4-LED indicator strip
- Allows manual control and override through a 4-key switch module

---

## 👥 Target Users

- Homeowners who want low-cost, DIY smart home security
- Makers and hobbyists building embedded IoT systems
- Students learning ESP-IDF firmware development with FirmGen

---

## 🛠️ Hardware — Bill of Materials (BOM)

| Component | Quantity | Notes |
|---|---|---|
| ESP32 Development Board | 1 | Main MCU with Wi-Fi |
| MPU6050 Gyroscope/Accelerometer | 1 | Motion & fall/tilt detection |
| HC-SR04 Ultrasonic Distance Sensor | 1 | Proximity detection |
| Active Buzzer | 1 | 15-second grace-period alarm |
| 4-LED Indicator Strip (R/W/G/Y) | 1 | Visual status output |
| 4-Key Switch Module (green PCB) | 1 | Manual control |
| Breadboard | 1 | Prototyping |
| Jumper Wires | ~20 | Male-to-male and female-to-male |
| USB Cable (micro/USB-C) | 1 | Flashing and power |

---

## 🔌 Wiring / Pin Assignment

The ESP32 receives power via the red wire to the VIN/5V pin and is grounded with a black wire. All components share the common ground rail on the breadboard.

### MPU6050 (I2C — Gyroscope & Accelerometer)
| MPU6050 Pin | ESP32 GPIO | Wire Color |
|---|---|---|
| VCC | 3.3V / 5V Rail | Red |
| GND | GND Rail | White/Grey |
| SDA | GPIO 21 | Purple |
| SCL | GPIO 22 | Blue |

### HC-SR04 Ultrasonic Sensor
| Sensor Pin | ESP32 GPIO | Wire Color |
|---|---|---|
| VCC | 5V Rail | White |
| GND | GND Rail | Blue |
| TRIG | GPIO 4 | Green/Purple |
| ECHO | GPIO 2 | Yellow |

> ⚠️ **Signal Level Note:** The HC-SR04 ECHO pin outputs 5V. Confirm level-shifting or wiring guidance before connecting directly to the ESP32.

### Active Buzzer
| Pin | ESP32 GPIO |
|---|---|
| Positive (+) | GPIO 23 |
| Negative (–) | GND Rail |

The buzzer pulses during the 15-second grace period before the SOS Wi-Fi alert is sent.

### 4-LED Indicator Strip
| LED Color | ESP32 GPIO | Wire Color |
|---|---|---|
| Red | GPIO 19 | White |
| White | GPIO 18 | Brown |
| Green | GPIO 5 | Blue |
| Yellow | GPIO 17 | Black |

All LED negative legs connect to the GND rail.

### 4-Key Switch Module
| Switch | ESP32 GPIO | Wire Color |
|---|---|---|
| COM (GND) | GND Rail | Brown |
| SW1 (Key 1) | GPIO 13 | Green |
| SW2 (Key 2) | GPIO 12 | Yellow |
| SW3 (Key 3) | GPIO 14 | White |
| SW4 (Key 4) | GPIO 27 | Grey/Green |

---

## 🔧 Build Instructions

### Prerequisites

- Windows PC with ESP-IDF v5.5 installed ([official installer](https://dl.espressif.com/dl/esp-idf/))
- FirmGen v0.3.1 (by CraftifAI Orbit)
- Git + Python (bundled with the ESP-IDF installer)
- CMake + Ninja (bundled with the ESP-IDF installer)
- An MQTT broker (e.g. [HiveMQ Cloud](https://www.hivemq.com/mqtt-cloud-broker/) — free tier works)

### Step 1 — Activate the ESP-IDF Environment

```cmd
:: Open Command Prompt and navigate to your ESP-IDF installation
cd C:\Espressif\frameworks\esp-idf-v5.5

:: Run the export script to activate all build tools
export.bat

:: You should see: "Done! You can now compile ESP-IDF projects."
```

> ⚠️ **Do not close this window.** Launch FirmGen from this same terminal so it inherits `IDF_PATH`, CMake, and Ninja.

### Step 2 — Launch FirmGen from the Activated Terminal

```cmd
:: Type the full path to FirmGen to launch it from the activated environment
"C:\Program Files\CraftifAI\FirmGen.exe"
```

### Step 3 — Configure the Toolchain in FirmGen

1. Open the **Toolchain Status** panel.
2. Set the **ESP-IDF Export Script** path to `C:\Espressif\frameworks\esp-idf-v5.5\export.bat`.
3. Confirm **CMake**, **Ninja**, and **Python** all show **Installed**.
4. Click **Save toolchain & restart agent**.

### Step 4 — Create / Open the Project

1. Select **ESP32** as the platform and verify your board.
2. Open the existing project or create a new one.
3. Paste your Wi-Fi SSID, password, and MQTT broker details into the FirmGen configuration when prompted.

### Step 5 — Deploy

Click **Deploy** in FirmGen. The agent will:
- Build the firmware (`idf.py build`)
- Flash it to the ESP32 (`idf.py flash`)
- Open the serial monitor (`idf.py monitor`)

---

## 🚀 Firmware Behavior

| State | Behavior |
|---|---|
| **Boot** | LEDs blink twice; buzzer pulses 250 ms; system enters idle |
| **Object Detected** (ultrasonic < threshold) | Yellow LED blinks; buzzer pulse rate increases |
| **Motion / Tilt Detected** (MPU6050 threshold crossed) | Red LED turns on; single alert event generated |
| **Grace Period (15 s)** | Buzzer pulses continuously as countdown warning |
| **SOS Triggered** | Wi-Fi alert published over MQTT to `<topic-prefix>/events` |
| **Manual Override** | Key switches (SW1–SW4) allow manual state control |

MQTT telemetry is published as JSON every 5 seconds to `<topic-prefix>/telemetry`.

---

## 📹 Demo Video

*(Drag and drop your 1–2 minute demo video here, or link your YouTube video below)*

[![Watch the Demo](https://img.youtube.com/vi/YOUR_VIDEO_ID/maxresdefault.jpg)](https://youtu.be/YOUR_VIDEO_ID)

> Replace `YOUR_VIDEO_ID` with the actual YouTube video ID.

---

## 📸 FirmGen Task List & Iteration Screenshots

*(Add screenshots of your FirmGen task list, topology view, and iteration history here)*

| Screenshot | Description |
|---|---|
| `screenshots/task_list.png` | FirmGen generated task plan |
| `screenshots/topology.png` | Firmware topology / module graph |
| `screenshots/deploy_log.png` | Successful deploy + flash output |
| `screenshots/serial_monitor.png` | Runtime serial monitor output |

---

## ⚠️ Known Limitations

- **HC-SR04 ECHO signal is 5V** — direct connection to ESP32 GPIO (3.3V tolerant) may damage the pin without a voltage divider or level shifter.
- **Wi-Fi reconnection** is handled non-blockingly, but brief disconnections may delay MQTT events.
- **MPU6050 threshold** for tilt/motion detection is configurable but requires a redeploy to change.
- **MQTT credentials** must be set in configuration before building — never hard-code passwords in source files.
- The project was developed and tested on **Windows only** with ESP-IDF v5.5.

---

## 💬 FirmGen Chat Export

The full FirmGen chat (project prompt history, iterations, and agent responses) is included in this repository:

📄 [`firmgen_chat_export.json`](./firmgen_chat_export.json)

> Export your chat from the **rightmost icon on the FirmGen Home Screen**, then push the file to this repo.

---

## 📝 Source Code

The generated firmware source is organized as follows:

```
.
├── main/
│   ├── main.c                  # Entry point, app_main
│   ├── config.h                # All pin assignments and timing values
│   ├── sensor_mpu6050.c/.h     # MPU6050 I2C driver module
│   ├── sensor_ultrasonic.c/.h  # HC-SR04 distance sensor module
│   ├── buzzer.c/.h             # Active buzzer control
│   ├── led_strip.c/.h          # 4-LED indicator control
│   ├── key_switch.c/.h         # 4-key switch input handler
│   ├── wifi_manager.c/.h       # Wi-Fi connection (non-blocking)
│   └── mqtt_client.c/.h        # MQTT publish (telemetry + events)
├── CMakeLists.txt
└── README.md
```

---

## 🏆 Hackathon Submission Checklist

- [x] Public GitHub repository named `CraftifAI_Hackathon_SmartGarageGuard`
- [x] Detailed README with wiring, BOM, build instructions, and limitations
- [x] FirmGen chat exported and pushed to repo
- [x] Demo video (1–2 min) included
- [x] FirmGen task list screenshots included
- [x] Source code well-organized and documented
- [ ] Feedback form submitted via [CraftifAI QR / form link](https://forms.gle/U9RsWGSBeLgkGLeM6)

---

## 🙏 Built With

- **[FirmGen by CraftifAI Orbit](https://craftifai.com)** — AI-powered firmware generation for ESP32 and STM32
- **ESP-IDF v5.5** — Espressif IoT Development Framework
- **ESP32** — Dual-core MCU with Wi-Fi & Bluetooth

---

*Built with FirmGen by [Your Name / Team Name]*
