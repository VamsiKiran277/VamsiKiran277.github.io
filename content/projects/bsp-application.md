---
title: "BSP-Backed Embedded Application"
date: 2026-04-15
draft: false
tags: ["Embedded C", "STM32", "Bare-Metal", "Firmware Architecture"]
# These flags kill the social sharing bar at the bottom
ShowShareButtons: false
showShareButtons: false
---

### 🛠 Technology Stack
* **Languages:** Bare-Metal C (C11)
* **Protocols:** UART/USART, SysTick Timer logic
* **Hardware:** STM32F407 (Discovery Board) - ARM Cortex-M4
* **Tools:** STM32CubeIDE, GDB, Real-term/Putty (Serial Debugging)

---

### The Project Overview
A modular, power-conscious firmware application that demonstrates a clean separation between hardware-specific drivers (BSP) and high-level application logic. The project focuses on non-blocking execution and architectural maintainability.

### Technical Highlights
* **Layered Architecture (BSP):** All register-level operations (RCC, GPIO, UART) are encapsulated within a dedicated Board Support Package. The application layer contains zero raw register addresses.
* **Non-Blocking Logic:** Replaced "busy-wait" `delay()` loops with a **SysTick-based timer flag system**, allowing the CPU to process multiple tasks (LED Heartbeat & Status Reporting) concurrently.
* **Clock Configuration:** Calculated and implemented the **42 MHz APB1** clock math to ensure a precise **115200 baud rate** for serial communication.
* **Hardware Abstraction:** Developed a suite of APIs (e.g., `BSP_LED_Toggle`, `BSP_UART_Write`) to ensure high-level logic remains portable across different MCU architectures.

---

### Key Learning Outcomes
* **Register Mapping:** Mastered the translation of technical datasheets into functional C code using structures and bit-fields.
* **Software Design Patterns:** Implemented the "Separation of Concerns" principle to reduce coupling between hardware and application code.
* **Asynchronous Timing:** Learned to manage time-based events in a super-loop environment without blocking the main execution thread.

---

### Hardware Target
This project was designed and tested on the **STM32F407 Discovery Board**. It utilizes the onboard **PD12 (Green LED)** for heartbeats and **USART2 (PD5/PD6)** for system status reporting.

[View Source on GitHub](https://github.com/VamsiKiran277/BSP-Backed-Embedded-Application-GPIO-Timer-UART.git)