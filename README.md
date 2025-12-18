# Medicine Box Temperature Monitor (IoT)

A low-cost IoT system that monitors medicine storage temperature (recommended **2°C–8°C**) using a **Raspberry Pi Pico W** + **BMP280** sensor, publishes readings via **MQTT over TLS**, stores them in **InfluxDB**, visualizes them on a **Node-RED Dashboard**, and triggers alerts when temperature goes out of range. :contentReference[oaicite:1]{index=1}

---

## ✨ Key Features

- **Real-time temperature sensing** with BMP280 (I2C)
- **Local processing** on Pico W (threshold checks + formatting)
- **Wi-Fi connectivity** using Pico W built-in Wi-Fi
- **Secure MQTT (TLS)** publishing to **HiveMQ Cloud**
- **Node-RED pipeline** for routing + automation
- **InfluxDB time-series storage** + historical tracking
- **Dashboard visualization** (charts/gauges)
- **Alerting** on unsafe temperatures (SMS/Email pipeline via Node-RED) :contentReference[oaicite:2]{index=2}

---

## 🧠 System Architecture (IoT Layers)

**Sensing Layer → Network Layer → Data Processing Layer → Application Layer**


This matches the report’s layered architecture and Node-RED flow design. :contentReference[oaicite:3]{index=3}

---

## 🔧 Hardware Requirements

- Raspberry Pi **Pico W**
- **BMP280** temperature sensor (I2C)
- Power supply: USB or battery pack
- Optional: 3D-printed enclosure / protective housing :contentReference[oaicite:4]{index=4}

### Wiring (BMP280 → Pico W)

Configured in the project as:
- **SDA → GP4**
- **SCL → GP5**
- Use **3.3V** and **GND** (typical BMP280 modules support 3.3V) :contentReference[oaicite:5]{index=5}

---

## 🧰 Software & Cloud Tools

- **MicroPython** on Pico W
- I2C BMP280 library (MicroPython)
- **HiveMQ Cloud** (MQTT broker, TLS port typically 8883)
- **Node-RED** (MQTT subscribe + logic + routing)
- **InfluxDB** (time-series database)
- Optional alerts: **Vonage** via Node-RED (SMS) :contentReference[oaicite:6]{index=6}

---

## 🚀 Getting Started

### 1) Flash MicroPython to Pico W
- Install MicroPython firmware on Pico W (via official Pico tools/Thonny).

### 2) Upload Firmware to Pico W
Your MicroPython logic should:
- Initialize I2C on `I2C(0)` with `sda=Pin(4)` and `scl=Pin(5)` at `freq=400000`
- Read temperature: `temperature = bmp.temperature`
- Sample periodically (the report uses **10 seconds** in one loop example)
- Connect to Wi-Fi using `network.WLAN(network.STA_IF)`
- Publish to MQTT topic: `Measurement Device/celsius` :contentReference[oaicite:7]{index=7}

**Suggested config constants (don’t hardcode secrets in git):**
- `WIFI_SSID`, `WIFI_PASS`
- `MQTT_HOST`, `MQTT_PORT=8883`
- `MQTT_USER`, `MQTT_PASS`
- `MQTT_TOPIC="Measurement Device/celsius"`

> Note: the report configures MQTT over TLS and (for project-level simplicity) disables certificate verification. For real deployments, enable certificate verification. :contentReference[oaicite:8]{index=8}

### 3) Set Up HiveMQ Cloud
- Create a HiveMQ Cloud cluster
- Create credentials
- Note host + port + username/password

### 4) Import Node-RED Flow
In Node-RED you should have:
- **MQTT In** node subscribed to your topic
- **Database Filtering Function**: discard invalid sensor spikes outside **-30°C to 50°C**
- **InfluxDB Out** node to store valid values
- **Threshold Alert Function**:
  - if `temp < 2` → output `"minusboundary"`
  - if `temp > 8` → output `"plusboundary"`
  - else output nothing (silent during normal range)
- **Delay/Throttle** node to prevent repeated alerts
- **Switch** node to route to the correct alert action (SMS/email) :contentReference[oaicite:9]{index=9}

### 5) Set Up InfluxDB + Dashboard
- Create InfluxDB bucket/database
- Configure Node-RED InfluxDB node
- Use **Node-RED Dashboard** widgets (chart/gauge) for real-time visualization :contentReference[oaicite:10]{index=10}

---

## ✅ Recommended Storage Range

- **Safe range:** **2°C – 8°C**
- Alerts trigger only when temperature leaves this range. :contentReference[oaicite:11]{index=11}

---

## 📊 Evaluation Summary (from report)

- Sensor accuracy: ~**±1°C**
- Device-to-cloud latency: ~**0.8–1.2 seconds**
- Load scenarios:
  - Low load (1 reading / 30s): stable, no packet loss
  - High load (bundled readings): higher latency and minor packet loss under unstable Wi-Fi :contentReference[oaicite:12]{index=12}


---

## 👥 Team

- Prakash Karkee  
- Mohosina Akhter
- Jarkko Ahtiluoma  
- Mayada Ahmed Hassan Mosa  
- Vimukthi Weerakkodi Mohottige Don  

---

