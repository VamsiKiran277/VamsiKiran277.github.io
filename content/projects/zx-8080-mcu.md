---
title: "ZX-8080 MCU: Bare-Metal Peripheral Control"
date: 2026-04-12T10:00:00Z
categories: ["Lab & Micro-Projects"]
weight: 100
tags: ["C", "Bare-Metal", "RISC-V", "ADC", "Timers"]
ShowShareButtons: false
---

### 🛠 Technology Stack
* **Target Hardware:** ZX-8080 (32-bit RISC-V Architecture)
* **Clock Speed:** 80 MHz System Frequency
* **Language:** Bare-Metal C
* **Toolchain:** RISC-V GCC / Memory-Mapped I/O (MMIO)
* **Key Peripherals:** 12-bit SAR ADC, General Purpose Timer (TIM1), GPIO

---

### Project Overview
This repository contains a bare-metal C implementation for the **ZX-8080**. The project demonstrates low-level hardware abstraction by interfacing with an onboard 12-bit SAR ADC for thermal monitoring and utilizing a General Purpose Timer for precise LED toggling. By interfacing directly with hardware registers, I bypassed abstraction layers to optimize performance and memory footprint.

---

### System Logic & Implementation
The application performs two concurrent tasks within a robust super-loop:

#### 1. Precision Timing (LED Toggle)
To achieve a human-readable **0.5s blink rate** from an **80 MHz** system clock, I implemented a two-stage clock division:

* **Prescaler (PSC):** Set to **7999**. This reduces the timer clock to **10 kHz**.
  * Formula: 80,000,000 / (7999 + 1) = 10,000 Hz
* **Auto-Reload Register (ARR):** Set to **4999**. The timer counts 5000 ticks before setting the Update Interrupt Flag (UIF).
  * Calculation: (8000 * 5000) / 80,000,000 = **0.5s Delay**

#### 2. Thermal Monitoring (ADC Polling)
The code interacts with a **12-bit SAR ADC** (Scale: 0–4095) for real-time environmental monitoring.
* **Trigger:** Hardware conversion is initiated directly via software register writes.
* **Synchronization:** The CPU polls the `START_CONV` bit, waiting for hardware to clear it upon completion to ensure data integrity.
* **Hysteresis Logic:** * **High Threshold (> 3000):** Activate Alarm (GPIO Pin 0 HIGH).
    * **Low Threshold (< 1500):** Deactivate Alarm (GPIO Pin 0 LOW).

---

### Key Learnings

#### Hardware Abstraction via Structs
Instead of using error-prone pointer arithmetic, I utilized **Memory-Mapped Structures**. This approach maps a C `struct` directly to the peripheral's register offsets, ensuring type-safe access and production-grade code readability.

#### Clock Tree Management
Mastered the relationship between system oscillators and peripheral clock domains, specifically how to manipulate **Prescalers** and **Auto-Reload** values to derive accurate timing intervals.

#### Register-Level Synchronization
Practiced robust peripheral handshaking by implementing polling methods—checking hardware status flags and manually clearing them to acknowledge events.

---

### 🔗 Project Links
<a href="https://github.com/VamsiKiran277/-EMBEDDED-small-projects/tree/main/ZX_8080_MCU" target="_blank" rel="noopener noreferrer" style="padding: 10px 20px; background-color: #24292e; color: white; text-decoration: none; border-radius: 5px;">View Source on GitHub</a>