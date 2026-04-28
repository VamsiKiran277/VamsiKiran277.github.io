---
title: "BSP-Backed Embedded Application"
categories: ["Major Projects"]
date: 2026-04-15T10:00:00Z
weight: 3
draft: false
tags: ["Embedded C", "STM32", "Bare-Metal", "Firmware Architecture", "Renode"]
ShowShareButtons: false
---

### 🛠 Technology Stack
* **Languages:** Bare-Metal C (C11)
* **Hardware:** STM32F407 (Discovery Board) - ARM Cortex-M4
* **Simulation:** Renode (Antmicro)
* **Protocols:** UART (USART2), SysTick Timer logic
* **Tools:** arm-none-eabi-gcc, GDB, Renode

---

### The Project Overview
A modular firmware application demonstrating a clean separation between hardware-specific drivers (BSP) and high-level application logic. The project focuses on non-blocking execution, architectural maintainability, and full system virtualization using the Renode framework.

### Technical Highlights
* **Layered Architecture (BSP):** All register-level operations (RCC, GPIO, UART) are encapsulated within a dedicated Board Support Package. The application layer interacts only with abstract APIs, containing zero raw register addresses.
* **Non-Blocking Logic:** Replaced "busy-wait" loops with a **SysTick-based interrupt system**, allowing the CPU to process concurrent tasks (LED Heartbeat and Status Reporting) without stalling the execution thread.
* **UART Serial Driver:** Developed a custom driver for USART2, featuring manual Baud Rate calculation for the **16 MHz HSI clock** to ensure stable **115200 baud** communication.
* **Virtual Hardware Validation:** Successfully virtualized the STM32F407 hardware environment in **Renode**, enabling cycle-accurate firmware testing and UART output auditing without physical silicon.

---

### Simulation & Hardware Verification
The project is validated using **Renode** to audit register-level interactions and verify the timing logic of the Board Support Package. By mapping the internal `USART2` peripheral to a virtual analyzer, the firmware execution is audited for cycle-accurate behavior before deployment to physical silicon.

{{< terminal command="renode stm32f4.resc" >}}
16:40:35.7373 [INFO] STM32F4_Discovery/sysbus: Loaded SVD: STM32F40x.
16:40:36.1429 [INFO] STM32F4_Discovery/sysbus: Loading block of 5784 bytes length at 0x8000000.
16:40:36.1430 [INFO] STM32F4_Discovery/sysbus: Loading block of 2352 bytes length at 0x20000548.
16:40:36.1622 [INFO] stm32f4: Machine started.
16:40:36.1629 [INFO] STM32F4_Discovery/cpu: Guessing VectorTableOffset value to be 0x8000000.
16:40:36.1711 [INFO] STM32F4_Discovery/cpu: Setting initial values: PC = 0x80015A5, SP = 0x20020000.
16:40:36.1728 [INFO] STM32F4_Discovery: Machine started.

[USART2 Analyzer]
A -> Char Sent!
A -> Char Sent!
A -> Char Sent!
A -> Char Sent!
A -> Char Sent!
... [Simulation continues at 2.0s intervals] ...
{{< /terminal >}}

#### Execution Audit Logic
* **Vector Table Alignment:** Renode successfully identified the `VectorTableOffset` at `0x8000000`, confirming the linker script correctly placed the code in Flash memory.
* **Stack Pointer (SP) Initialization:** The initial SP was set to `0x20020000`, validating that the startup assembly properly allocated the stack in the upper region of SRAM.
* **Deterministic Peripherals:** The `A -> Char Sent!` output confirms that the manual baud rate calculation for the 16 MHz HSI clock correctly configured the `USART2->BRR` register and that the `TXE` (Transmit Data Register Empty) polling logic is fully functional.

---

### Key Learning Outcomes
* **Register Mapping & MMIO:** Mastered the translation of ARM Cortex-M4 technical datasheets into functional C code using structures and volatile bit-field mapping.
* **Software Design Patterns:** Implemented "Separation of Concerns" to ensure high-level application code remains portable across different MCU architectures.
* **Cross-Compilation & Toolchain:** Transitioned to a Linux-based build pipeline using `arm-none-eabi-gcc` and custom linker scripts (`.ld`) for manual memory mapping.

---

### Hardware Target
This project was designed for the **STM32F407 Discovery Board**. In the virtualized environment, it utilizes **PD12** for the LED heartbeat and **USART2 (PA2/PA3)** for system status reporting at 115200 baud.

<br>

<a href="https://github.com/VamsiKiran277/BSP-Backed-Embedded-Application-GPIO-Timer-UART.git" target="_blank" rel="noopener noreferrer" style="padding: 10px 20px; background-color: #24292e; color: #00d2ff; text-decoration: none; border-radius: 5px; border: 1px solid #00d2ff; font-weight: 600;">View Source on GitHub</a>