---
title: "Smart Asset Tracker & Telemetry Dashboard"
date: 2026-04-22
draft: false
tags: ["IoT", "C"  , "C++", "Qt 6", "MQTT", "Raspberry Pi"]
---

### The Project Overview
Engineered a secure telemetry pipeline to synchronize high-G impact data from a Raspberry Pi edge device to the cloud, complete with a custom real-time visualization dashboard.

### Technical Highlights
* **IoT Cloud Integration:** Configured **MQTT (Paho C)** to sync data to **HiveMQ Cloud**, implementing **SSL/TLS encryption** via port 8883.
* **Custom Qt Dashboard:** Developed a visualization dashboard in **C++ (Qt 6)**, parsing asynchronous **JSON** payloads to visualize 3-axis vibration data on a live-scrolling UI.
* **Asynchronous Event Handling:** Architected an interrupt-driven system using **libgpiod** to monitor GPIO triggers from an **ADXL345** accelerometer, eliminating CPU polling overhead.
* **Hardware Interface:** Developed custom I2C drivers implementing **Two’s Complement** sign-extension for raw data processing.

*(Screenshot coming soon...)*
