---
title: "STM32F4 Bare-Metal LED Toggle (Bit-Field & SIL)"
date: 2026-04-12T10:00:00Z
categories: ["Lab & Micro-Projects"]
weight: 110
tags: ["C", "ARM Cortex-M4", "Bare-Metal", "Renode", "SIL"]
ShowShareButtons: false
---

### 🛠 Technology Stack
* **Language:** C (C11/C17)
* **Hardware:** STM32F407 (ARM Cortex-M4)
* **Tools:** Renode (SIL Simulation), STM32CubeIDE
* **Key Concepts:** C Bit-Fields, MMIO, Software-in-the-Loop (SIL)

---

### The Project Overview
A low-level firmware implementation for the STM32F407 that demonstrates peripheral control via direct register manipulation. This project bypasses standard HAL/LL libraries to interface directly with the **AHB1 bus matrix** using custom C bit-field structures for type-safe hardware access.

### Technical Highlights
* **C Bit-Field Structures:** Utilized custom `struct` definitions to map hardware registers, ensuring type-safe access to individual bits and eliminating manual bitwise masks (`&`, `|`, `<<`).
* **Memory-Mapped I/O (MMIO):** Manually mapped the RCC and GPIOD base addresses (Base: `0x40020C00`) to control hardware clocking and pin states.
* **SIL Validation (Renode):** Automated hardware verification using Renode to validate register-level behavior via memory-access tracing. This allows for cycle-accurate auditing of the firmware's execution without physical hardware.

### Simulation & Verification
The project includes a pre-configured Renode environment. The firmware logic is verified by running:
`renode --plain --hide-monitor --execute "include @Simulation/stm32_blink.resc" 2>&1 | tee verification.log`

**What the `verification.log` proves:**
* **Mode Configuration:** `WriteUInt32` to `0x0` (MODER) with value `0x1000000` (Sets Pin 12 to Output).
* **LED Toggle Logic:** `WriteUInt32` to `0x14` (ODR) showing value `0x1000` (Pin 12 HIGH) and `0x0` (Pin 12 LOW).

### Key Learning Outcomes
* **Code Maintainability:** Improved readability for engineering teams by replacing "magic number" masking with structured mapping.
* **Hardware-Software Verification:** Mastered the use of **Watchpoint Hooks** on memory address `0x40020C14` to capture the Program Counter (PC) and value writes during execution.

<a href="https://github.com/VamsiKiran277/-EMBEDDED-small-projects/tree/main/LED_TOGGLE_BITFIELD" target="_blank" rel="noopener noreferrer">View Source on GitHub</a>