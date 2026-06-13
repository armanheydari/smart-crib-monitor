# Smart Crib Monitor

> An IoT baby monitoring system that combines an ESP32-CAM web server with a Python alert client — streaming live video and notifying parents when temperature, humidity, sound, or motion thresholds are crossed.

[![Arduino](https://img.shields.io/badge/Arduino-ESP32--CAM-teal.svg)](https://www.arduino.cc/)
[![Python Version](https://img.shields.io/badge/python-3.x%2B-blue.svg)](https://www.python.org/)

## 📌 Overview

Smart Crib Monitor is an embedded IoT system designed for infant monitoring. An ESP32-CAM module runs an HTTP web server that continuously exposes live camera frames and real-time sensor readings — humidity, temperature (DHT sensor), sound presence, and motion detection — over a local WiFi network. A Python client polls this server every 10 seconds, compares readings against prior values, and alerts the parent whenever a significant change is detected.

The system bridges two worlds: the Arduino firmware handles low-level sensor integration and real-time image capture on constrained hardware, while the Python layer provides the monitoring intelligence and user interaction. When an alert fires, the parent can immediately capture a timestamped JPEG snapshot or open a live MJPEG stream — all without a dedicated mobile app or cloud dependency.

## ✨ Key Features

* **Multi-sensor alert engine:** Monitors temperature (triggers on a rise > 1°C), humidity (triggers on a rise > 2%), sound detection, and motion detection simultaneously — printing a specific reason for each alert.
* **On-demand camera capture:** Fetches a JPEG frame from the ESP32-CAM's `/capture` endpoint via `urllib`, decodes it with NumPy and OpenCV, and saves it as a timestamped file (`YYYY-MM-DD_HH-MM-SS.jpg`).
* **Live MJPEG streaming:** Opens a real-time OpenCV window (`live transmission`) that continuously polls `/capture` and refreshes at ~10fps until the user presses `q`.
* **MQTT publish support:** Includes an `run_mqtt()` function that connects to a broker and publishes sensor data to a topic using `paho-mqtt` — ready to integrate with any MQTT-based dashboard.
* **ESP32-CAM HTTP server:** The Arduino firmware (C/C++) serves sensor readings at `/mode` as plain text and camera frames at `/capture`, acting as a lightweight embedded REST API.

## 🛠️ Tech Stack

* **Core Languages:** C, C++, Python
* **Microcontroller:** ESP32-CAM (AI-Thinker) with OV2640 camera module
* **Firmware Framework:** Arduino IDE with ESP32 board support (`esp_camera.h`, `WiFi.h`)
* **Sensors:** DHT temperature/humidity sensor, sound sensor, PIR motion sensor
* **Python Libraries:** `paho-mqtt`, `opencv-python`, `numpy`, `urllib`
* **Protocol:** HTTP (firmware → Python), MQTT (Python → broker)

## 🚀 Getting Started

### Prerequisites

* Arduino IDE with [ESP32 board support](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)
* Python 3.x
* ESP32-CAM module (AI-Thinker) with DHT sensor, sound sensor, and PIR sensor wired up

### Installation

```bash
git clone https://github.com/armanheydari/Smart-crib_Arduino.git
cd Smart-crib_Arduino
```

**Firmware (Arduino):**

1. Open `CameraWebServer/CameraWebServer.ino` in Arduino IDE.
2. Select board: **AI Thinker ESP32-CAM**.
3. Set your WiFi credentials in the sketch.
4. Flash to the board.

**Python client:**

```bash
pip install paho-mqtt opencv-python numpy
```

Then update the IP address in `MQTT.py` to match your ESP32-CAM's local IP:

```python
url = 'http://<your-esp32-cam-ip>'
```

## 💻 Usage

Run the monitoring loop:

```bash
python MQTT.py
```

The script polls the crib sensors every 10 seconds. When a threshold is crossed, it prints the alert reason and prompts:

```
you can check your baby due to high Temperature, Sound,
enter 0 to capture a photo or 1 to watch him/her live:
```

Enter `0` to save a timestamped snapshot, or `1` to open a live video window.

## 📁 Project Structure

```
Smart-crib_Arduino/
├── CameraWebServer/        # ESP32-CAM Arduino firmware (HTTP server, sensor reads, MJPEG capture)
│   ├── CameraWebServer.ino # Main sketch: WiFi setup, camera init, HTTP endpoint routing
│   ├── app_httpd.cpp       # HTTP request handlers for /capture and /mode
│   └── camera_pins.h       # Pin definitions for AI-Thinker ESP32-CAM board
└── MQTT.py                 # Python client: sensor polling, alert logic, snapshot/stream on demand
```
