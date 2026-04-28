---
title: "Smart Asset Tracker & Telemetry Dashboard"
date: 2026-04-13T10:00:00Z
categories: ["Major Projects"]
weight: 1
tags: ["IoT", "C++", "Qt 6", "MQTT", "Raspberry Pi", "Cybersecurity", "Embedded Linux"]
# --- UPDATE THIS SECTION ---
cover:
    image: "images/dashboard_screenshot.png" # Path relative to the 'static' folder
    alt: "Qt Dashboard"
    caption: "Real-time Telemetry Dashboard"
    relative: false
    hidden: true
# ---------------------------
ShowShareButtons: false
---
### 🛠 Technology Stack
* **Target Hardware:** Raspberry Pi 4 Model B
* **Sensing Hardware:** ADXL345 (3-Axis Digital Accelerometer)
* **Languages:** C++ (Qt 6), Embedded C (Paho MQTT), Bash
* **Protocols:** MQTT over SSL/TLS (Port 8883), I2C, JSON
* **Cloud Infrastructure:** HiveMQ Cloud Broker
* **Networking:** Tailscale Mesh VPN, Pi-hole (DNS Security)

---

### The Project Overview
Engineered a secure, end-to-end telemetry pipeline designed to monitor and synchronize high-G impact data from edge devices to a centralized monitoring station. This project bridges the gap between register-level sensor interfacing on Linux and high-level data visualization using the Qt framework. The system is hardened with industry-standard encryption to ensure data integrity across public networks.

---

### System Logic & Implementation
The architecture is divided into a high-performance **Edge Firmware** and a reactive **Telemetry Dashboard**:

#### 1. Edge Firmware (Impact Detection)
The Raspberry Pi interacts with the **ADXL345** via a custom I2C driver. To optimize performance, the system utilizes a "High-G" threshold interrupt. 
* **Data Processing:** Raw 16-bit acceleration data is converted using **Two’s Complement** sign-extension and scaled to G-force units.
* **Cloud Synchronization:** When an impact exceeds the 1.5G threshold, the firmware packages the event into a JSON payload and publishes it to a secure HiveMQ cluster.

#### 2. Qt 6 Dashboard (Asynchronous Visualization)
The monitoring suite is built in **C++ (Qt 6)** and operates on a non-blocking event loop:
* **Secure Handshake:** Implements a manual **SSL/TLS handshake** (TLS v1.2) utilizing Server Name Indication (SNI) and ALPN protocols to bypass strict cloud firewalls.
* **Live Telemetry:** Features a dynamic scrolling chart that renders X, Y, and Z axes simultaneously.
* **JSON Pipeline:** An asynchronous parser decrypts and flattens incoming MQTT messages into UI-ready data structures without locking the main thread.

---

### Network & Security Hardening
A significant portion of development was dedicated to ensuring the telemetry pipeline could operate within a secured home/enterprise network:

* **DNS Whitelisting:** Configured **Pi-hole** regex rules to allow encrypted telemetry traffic while blocking standard ad-trackers.
* **Proxy Bypass:** Since the system often operates behind a **Tailscale VPN**, the Qt application was programmed to bypass virtual network proxies using `QNetworkProxy::NoProxy` to ensure direct, low-latency socket communication.
* **Port 8883 Enforcement:** All traffic is strictly routed through Port 8883 (MQTTS), preventing packet sniffing and man-in-the-middle attacks.

---

### Live Execution Logs

**1. Edge Firmware Output (Raspberry Pi):**
{{< terminal command="./firmware_app" >}}
Connecting to ssl://40db01641ba149dc91182576560ea139.s1.eu.hivemq.cloud:8883...
Connected to the broker. RTC Time has been synced!
Logger active: Waiting for Vibration...

[LOGGED] IMPACT DETECTED! X:0.25g Y:0.74g Z:1.09g
--- Attempting Cloud Sync... ---
[LOGGED] IMPACT DETECTED! X:0.12g Y:-0.14g Z:1.93g
--- Attempting Cloud Sync... ---
[LOGGED] IMPACT DETECTED! X:-0.05g Y:-0.82g Z:0.58g
{{< /terminal >}}

**2. Dashboard Output (Linux Monitoring Station):**
{{< terminal command="./AssetTrackerDashboard" >}}
>>> Opening TCP Socket to Port 8883...
>>> TCP Connected. Starting SSL Handshake...
>>> SUCCESS: Tunnel Encrypted! Sending MQTT Login...
MQTT Login Successful!
Subscribed to topic: asset_tracker/#
Incoming Data: {"x":0.25, "y":0.74, "z":1.09} -> Rendering Frame.
{{< /terminal >}}

---
![Dashboard Live View](/images/dashboard_screenshot.png)

### Key Learnings

* **SSL/TLS Protocol Nuances:** Gained deep experience in debugging handshake failures, specifically managing **SNI** (Server Name Indication) and **ALPN** tags required by AWS-backed load balancers.
* **Embedded Data Integrity:** Learned to handle I2C register-level timing and sign-extension for MEMS sensors, ensuring that physical motion translates accurately to digital telemetry.
* **Network Interoperability:** Solved complex routing issues involving **Pi-hole DNS sinkholes** and **Tailscale VPN** interference, a critical skill for deploying IoT devices in real-world environments.
* **Qt UI Performance:** Mastered the use of **QtCharts** and signal-slot mechanisms to handle high-frequency data bursts without causing UI latency.

<br>

<a href="https://github.com/VamsiKiran277/Smart-Asset-Tracker" target="_blank" rel="noopener noreferrer" style="padding: 10px 20px; background-color: #24292e; color: #00d2ff; text-decoration: none; border-radius: 5px; border: 1px solid #00d2ff; font-weight: 600;">View Source on GitHub</a>