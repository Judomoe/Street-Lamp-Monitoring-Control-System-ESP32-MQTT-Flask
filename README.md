# Street Lamp Monitoring & Control System 💡

An IoT project that detects street lamp failures in real time using an **ESP32** 
microcontroller and displays live status on a **Flask web dashboard** via MQTT.

**By:** Mohamed Mostafa Kammel, Mazen Wael, Mohamed Ashraf Mohamed Mahmoud

---

## System Architecture
[ESP32 + LDR Sensors]
|
MQTT Broker (102.46.132.3:1883)
|
[Flask Dashboard] ← Browser (admin login)

---

## Components

### 🔌 ESP32 Firmware (Arduino C++)
- Connects to Wi-Fi and an MQTT broker
- Reads an **analog LDR** (pin 34) to control brightness of 3 LEDs via PWM
- Monitors 3 **digital LDR bulb-check sensors** (pins 27, 14, 13) every 5 seconds
- Detects a broken lamp when brightness is high but no light is detected
- Publishes lamp status to `esp32/bruh/status`

**Pin Map:**
| Pin | Role |
|-----|------|
| 34  | Analog LDR (ambient light) |
| 2, 5, 4 | LED PWM outputs (lamps 1–3) |
| 27, 14, 13 | Digital LDR bulb checks (lamps 1–3) |

---

### 🌐 Flask Web Dashboard (Python)
- Subscribes to `esp32/bruh/status` via `paho-mqtt`
- Displays live status of each lamp (🔵 OK / 🔴 Broken)
- Timestamped message log (last 10 messages)
- Auto-refreshes every 5 seconds
- **Reset** button to manually clear a lamp's error state
- HTTP Basic Authentication (admin access only)

---

## How to Run

### ESP32
1. Flash `main.ino` using Arduino IDE or PlatformIO
2. Update Wi-Fi credentials in the sketch if needed
3. Make sure the MQTT broker is reachable

### Flask Dashboard
```bash
pip install flask paho-mqtt flask-httpauth werkzeug
python app.py
```
Then open: `http://localhost:5000`  
Login: `admin` / `ChangeThisPassword123!`

---

## Tech Stack
- **ESP32** (Arduino Core, PubSubClient)
- **Python** (Flask, paho-mqtt, Flask-HTTPAuth)
- **MQTT** protocol for IoT messaging

---

## Recommended Improvements
- Use SSL/TLS for MQTT and HTTPS for Flask
- Store Wi-Fi and MQTT credentials in secure config (not hardcoded)
- Add MQTT QoS levels and Last Will & Testament (LWT) for disconnect detection
- Send MQTT control message to ESP32 when Reset is clicked (bi-directional)
- Replace `delay(50)` with `millis()`-based non-blocking timing
