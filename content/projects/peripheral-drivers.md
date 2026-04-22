---
title: "Peripheral Driver Suite: GPIO, I2C, SPI & UART"
categories: ["Major Projects"]
date: 2026-04-20T10:00:00Z
weight: 4
draft: false
tags: ["C++", "Raspberry Pi 4", "Bare-Metal", "Driver Development"]
# These flags kill the social sharing bar at the bottom
ShowShareButtons: false
showShareButtons: false
---

### 🛠 Technology Stack
* **Languages:** C++ (OOP Approach), Makefile
* **Protocols:** I2C, SPI, UART (Mini-UART), GPIO (Alternate Functions)
* **Hardware:** Raspberry Pi 4 (BCM2711 / ARM Cortex-A72)
* **Tools:** BCM2711 ARM Peripherals Datasheet, GCC Toolchain, GDB

---

### The Project Overview
Developed a comprehensive, register-level peripheral driver suite for the Raspberry Pi 4 using an Object-Oriented approach in C++. This project demonstrates how to translate complex technical datasheets into a modular, reusable Hardware Abstraction Layer (HAL).

### Technical Highlights
* **Register-Level Control:** Directly manipulated SoC registers using memory-mapped I/O (MMIO) to configure GPIO pins, Alternate Functions, and peripheral clock rates.
* **OOP Driver Design:** Implemented drivers as C++ classes, encapsulating peripheral state and methods. This ensures type safety and allows for easy instantiation of multiple communication buses.
* **Protocol Implementation:**
    * **I2C:** Developed a robust master-mode driver with support for 7-bit addressing and data stream management.
    * **SPI:** Engineered a high-speed Serial Peripheral Interface driver, managing Clock Polarity (CPOL) and Phase (CPHA).
    * **UART:** Configured the Mini-UART peripheral for asynchronous serial communication, including precise baud rate generation math.
* **Custom Build Pipeline:** Created a modular **Makefile** system to manage dependencies, object files, and cross-compilation for the ARM architecture.

---

### Key Learning Outcomes
* **Datasheet Interpretation:** Mastered the **BCM2711 Reference Manual**, learning to map physical addresses to virtual memory and understanding peripheral-specific control registers.
* **Communication Protocols:**
    * **SPI:** Learned the timing diagrams and shift-register logic required for high-speed full-duplex communication.
    * **UART:** Understood start/stop bit framing, parity, and the impact of system clock frequencies on baud accuracy.
    * **I2C:** Gained experience with the "Start-Address-Data-Stop" sequence and handling Acknowledgement (ACK/NACK) bits.
* **C++ for Embedded:** Leveraged C++ features like classes and private member variables to enforce data hiding and prevent illegal register access from the application layer.

---

### Hardware Target
Designed specifically for the **Raspberry Pi 4 Model B**. The drivers interface directly with the **Broadcom BCM2711 SoC**, providing a bare-metal feel while running within a Linux user-space environment via `/dev/mem` access.

<a href="https://github.com/VamsiKiran277/GPIO_DRIVER.git" target="_blank" rel="noopener noreferrer">View Source on GitHub</a>