---
title: "FreeRTOS Task Management & Sensor Suite"
categories: ["Major Projects"]
date: 2026-04-18T10:00:00Z
weight: 2
draft: false
tags: ["FreeRTOS", "Embedded C", "Real-Time Systems", "IPC", "I2C"]
# These flags kill the social sharing bar at the bottom
ShowShareButtons: false
showShareButtons: false
---

### 🛠 Technology Stack
* **Languages:** Embedded C (C11)
* **Operating System:** FreeRTOS (POSIX Port)
* **Protocols:** I2C, Hardware Interrupts (GPIO)
* **Tools:** libgpiod, GDB, GCC, Linux terminal

---

### The Project Overview
The objective was to design a robust, thread-safe sensor acquisition system on a Linux-based RTOS environment. This project demonstrates high-reliability multitasking where multiple sensors share a single communication bus without data corruption or timing jitter.

### Technical Highlights
* **Kernel-Level Multitasking:** Leveraged the **FreeRTOS POSIX Port** to manage three concurrent tasks using fixed-priority preemption, ensuring mission-critical tasks always receive CPU time.
* **Thread-Safe I2C Management:** Implemented a **Mutex Semaphore** to arbitrate access to the I2C bus. This prevents data collisions between the **ADXL345 Accelerometer** and **DS3231 RTC** tasks when accessing shared hardware.
* **Asynchronous Event Handling:** Developed a high-priority hardware listener using **libgpiod** to capture falling-edge interrupts on GPIO 17, triggering a system-wide graceful shutdown.
* **Deterministic Scheduling:** Replaced standard sleep calls with `vTaskDelayUntil()` to ensure a consistent **10Hz sampling frequency**, eliminating timing drift critical for real-time telemetry analysis.
* **Resource Cleanup:** Engineered a custom `shutdown()` sequence ensuring all RTOS tasks are deleted and Linux file descriptors (I2C/GPIO) are properly closed before process termination.

---

### Key Learning Outcomes
* **Concurrency Control:** Mastered the use of Mutexes and Semaphores to handle shared resource contention in a real-time environment.
* **RTOS Scheduling:** Understood the difference between "soft" delays and deterministic "fixed-frequency" scheduling for sensor data integrity.
* **System Robustness:** Learned to implement graceful degradation and cleanup procedures to prevent memory leaks and "hung" hardware peripherals.

---

### Hardware Target
This project is designed for **Linux-based Embedded Systems** (specifically tested on Raspberry Pi). It interfaces with an **ADXL345** for motion data and a **DS3231** Real-Time Clock for timestamping via the I2C protocol.

[View Source on GitHub](https://github.com/VamsiKiran277/FreeRTOS_project.git)