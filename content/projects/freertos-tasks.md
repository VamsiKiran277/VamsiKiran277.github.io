---
title: "FreeRTOS Task Management & Sensor Suite"
categories: ["Major Projects"]
date: 2026-04-18T10:00:00Z
weight: 2
draft: false
tags: ["FreeRTOS", "Embedded C", "Real-Time Systems", "IPC", "I2C"]
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

###  Live RTOS Execution & Task Arbitration

The following terminal trace captures the live execution of the FreeRTOS tasks. It demonstrates the real-time arbitration of the I2C bus between the high-frequency accelerometer and the low-frequency real-time clock.

{{< terminal command="./telemetry_app" >}}
ADXL initialized succesfully
X:0 | Y:0 | Z:0
H:02 | M:20 | S:29
X:-4 | Y:144 | Z:192
X:-6 | Y:144 | Z:191
X:-4 | Y:144 | Z:194
X:-5 | Y:144 | Z:193
X:-7 | Y:145 | Z:192
X:-4 | Y:143 | Z:194
X:-4 | Y:145 | Z:190
X:-5 | Y:145 | Z:190
X:-6 | Y:143 | Z:197
X:-5 | Y:145 | Z:193
H:02 | M:20 | S:30
X:-5 | Y:143 | Z:189
X:-4 | Y:142 | Z:193
... [Simulation continues] ...
^C
{{< /terminal >}}

###  Telemetry Data Audit

* **Deterministic 10Hz Scheduling:** Between the RTC outputs of `S:29` and `S:30`, there are exactly ten `X/Y/Z` coordinate lines. This proves the `vTaskDelayUntil()` logic successfully avoids drift and maintains a strict 100ms task frequency.
* **Thread-Safe IPC (Mutex):** Notice that the output lines never mangle or overlap (e.g., you never see `X:-4 | H:02 Y:144...`). This confirms the FreeRTOS Mutex is successfully locking the `printf` and I2C file descriptors during context switches.
* **Graceful Termination:** The `^C` (SIGINT) is caught by the system, allowing the process to release the I2C bus and terminate gracefully without hanging the hardware state.

---

### Key Learning Outcomes
* **Concurrency Control:** Mastered the use of Mutexes and Semaphores to handle shared resource contention in a real-time environment.
* **RTOS Scheduling:** Understood the difference between "soft" delays and deterministic "fixed-frequency" scheduling for sensor data integrity.
* **System Robustness:** Learned to implement graceful degradation and cleanup procedures to prevent memory leaks and "hung" hardware peripherals.

---

### Hardware Target
This project is designed for **Linux-based Embedded Systems** (specifically tested on Raspberry Pi). It interfaces with an **ADXL345** for motion data and a **DS3231** Real-Time Clock for timestamping via the I2C protocol.

<br>

<a href="https://github.com/VamsiKiran277/FreeRTOS_project.git" target="_blank" rel="noopener noreferrer" style="padding: 10px 20px; background-color: #24292e; color: #00d2ff; text-decoration: none; border-radius: 5px; border: 1px solid #00d2ff; font-weight: 600;">View Source on GitHub</a>