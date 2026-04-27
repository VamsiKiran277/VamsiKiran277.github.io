---
title: "ZX-8080 MCU: Bare-Metal Peripheral Control"
date: 2026-04-12T10:00:00Z
categories: ["Lab & Micro-Projects"]
weight: 100
tags: ["C", "Bare-Metal", "Computer Architecture", "ADC"]
ShowShareButtons: false
---

### 🛠 Technology Stack
* **Language:** Bare-Metal C (Register-level)
* **Hardware:** Simulated ZX-8080 MCU (based on technical datasheet)
* **Key Concepts:** Memory-Mapped I/O (MMIO), Polling-based State Machines

---

### The Project Overview
This project serves as a deep-dive sandbox for mastering fundamental Firmware Engineering. I simulated the firmware for a custom microcontroller (ZX-8080) to manage ADC conversions and hardware timers based strictly on datasheet specifications.

### Technical Highlights
* **Memory-Mapped I/O (MMIO):** Manually defined peripheral base addresses and offsets to interface with system control, GPIO, ADC, and Timer registers.
* **Timer Configuration:** Calculated and configured **Prescaler (PSC)** and **Auto-Reload Register (ARR)** values to derive precise 0.5s hardware delays from an **80MHz** system clock.
* **ADC Data Acquisition:** Developed a polling-based state machine to trigger ADC conversions, processing thermal sensor data for threshold-based thermal management logic.
* **Bit-Level Optimization:** Utilized **XOR bitwise operations** for efficient GPIO toggling and manual flag clearing in the Timer Status registers.

---

### Key Learning Outcomes
* **Datasheet Implementation:** Learned to translate raw hexadecimal address maps into functional C drivers.
* **Clock Trees:** Gained experience in frequency scaling and deriving timing intervals from system oscillators.
* **Polling Logic:** Mastered peripheral status flag management without the overhead of an OS.

<a href="https://github.com/VamsiKiran277/-EMBEDDED-small-projects/tree/main/ZX_8080_MCU" target="_blank" rel="noopener noreferrer">View Source on GitHub</a>