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
* **Memory-Mapped I/O (MMIO):** Manually mapped the RCC (Base: `0x40023800`) and GPIOD (Base: `0x40020C00`) to control hardware clocking and pin states.
* **SIL Validation (Renode):** Automated hardware verification using Renode to validate register-level behavior via memory-access tracing. This allows for cycle-accurate auditing of the firmware's execution without physical hardware.

### 🔍 Simulation & Hardware Verification

The project is validated using **Renode** to trace memory-mapped I/O (MMIO) interactions. This ensures the firmware logic correctly targets the ARM Cortex-M4 register map before deployment to physical silicon.

{{< terminal command="renode --plain --hide-monitor -e 'include @Simulation/stm32_blink.resc'" >}}
19:43:38.5796 [INFO] System bus created.
19:43:39.3288 [INFO] sysbus: Loaded SVD: STM32F40x.
19:43:39.4383 [INFO] gpioPortD: [cpu: 0x80001EE] WriteUInt32 to 0x0 (Mode), value 0x1000000
19:43:39.4390 [INFO] gpioPortD: [cpu: 0x80001F8] WriteUInt32 to 0x14 (OutputData), value 0x1000  <-- LED ON
19:43:39.4457 [INFO] gpioPortD: [cpu: 0x8000216] WriteUInt32 to 0x14 (OutputData), value 0x0     <-- LED OFF
... [Simulation continues] ...
{{< /terminal >}}

#### 📑 Full Verification Log Audit (`verification.log`)
The following excerpt from the automated log audit validates the peripheral clock gating and the recursive toggling logic:

```text
19:43:39.3542 [INFO] sysbus: Loading block of 764 bytes length at 0x8000000.
19:43:39.4383 [INFO] gpioPortD: [cpu: 0x80001E6] ReadUInt32 from 0x0 (Mode), returned 0x0
19:43:39.4383 [INFO] gpioPortD: [cpu: 0x80001EE] WriteUInt32 to 0x0 (Mode), value 0x1000000
19:43:39.4390 [INFO] gpioPortD: [cpu: 0x80001F2] ReadUInt32 from 0x14 (OutputData), returned 0x0
19:43:39.4390 [INFO] gpioPortD: [cpu: 0x80001F8] WriteUInt32 to 0x14 (OutputData), value 0x1000
19:43:39.4457 [INFO] gpioPortD: [cpu: 0x8000216] WriteUInt32 to 0x14 (OutputData), value 0x0
19:43:39.4522 [INFO] gpioPortD: [cpu: 0x80001F8] WriteUInt32 to 0x14 (OutputData), value 0x1000
19:43:39.4733 [INFO] gpioPortD: [cpu: 0x8000216] WriteUInt32 to 0x14 (OutputData), value 0x0
```

### Hardware Audit
* **Mode Config (Offset 0x0):** The value `0x1000000` sets Bits 25:24 to `01`, successfully configuring Pin 12 as a General Purpose Output.
* **Output Toggle (Offset 0x14):** Toggling between `0x1000` (Bit 12 set) and `0x0` verifies cycle-accurate pin control.
* **Watchpoint Tracing:** Renode captures the exact Program Counter (e.g., `0x80001EE`) for every write, providing a verifiable audit trail.

### Key Learning Outcomes
* **Code Maintainability:** Improved readability for engineering teams by replacing "magic number" masking with structured mapping.
* **Hardware-Software Verification:** Mastered the use of **Watchpoint Hooks** on memory address `0x40020C14` to capture value writes during execution.

<a href="https://github.com/VamsiKiran277/-EMBEDDED-small-projects/tree/main/LED_TOGGLE_BITFIELD" target="_blank" rel="noopener noreferrer">View Source on GitHub</a>