---
title: "ZX-8080 MCU: Bare-Metal Peripheral Control"
date: 2026-04-12T10:00:00Z
categories: ["Lab & Micro-Projects"]
weight: 100
tags: ["C", "Bare-Metal", "RISC-V", "ADC", "Timers", "Renode"]
ShowShareButtons: false
---

### 🛠 Technology Stack
* **Target Hardware:** ZX-8080 (32-bit RISC-V Architecture)
* **Clock Speed:** 80 MHz System Frequency
* **Language:** Bare-Metal C
* **Toolchain:** RISC-V GCC (`riscv64-unknown-elf-gcc`)
* **Simulation:** Renode (Antmicro)
* **Key Peripherals:** 12-bit SAR ADC, General Purpose Timer (TIM1), GPIO

---

### The Project Overview
This repository contains a bare-metal C implementation for the **ZX-8080**, a custom 32-bit RISC-V based microcontroller. The project demonstrates low-level hardware abstraction by interfacing with an onboard 12-bit SAR ADC for thermal monitoring and utilizing a General Purpose Timer for precise LED toggling. Furthermore, the entire hardware environment is successfully virtualized and tested using the **Renode** simulation framework.

---

### System Logic & Implementation
The application performs two concurrent tasks within a robust super-loop:

#### 1. Precision Timing (LED Toggle)
To achieve a human-readable **0.5s blink rate** from an **80 MHz** system clock, I implemented a two-stage clock division using Timer 1:

* **Prescaler (PSC):** Set to `7999`. This reduces the timer clock to 10 kHz.
  <br> `f_clk = 80,000,000 / (7999 + 1) = 10,000 Hz`

* **Auto-Reload Register (ARR):** Set to `4999`. The timer counts 5000 ticks before setting the Update Interrupt Flag (UIF).
  <br> `Delay = (PSC + 1) * (ARR + 1) / f_sys = (8000 * 5000) / 80,000,000 = 0.5s`

#### 2. Thermal Monitoring (ADC Polling)
The code interacts with a 12-bit SAR ADC (Scale: 0–4095) for real-time environmental monitoring.
* **Trigger:** Hardware conversion is initiated directly via software register writes.
* **Synchronization:** The CPU polls the `START_CONV` bit, waiting for hardware to clear it upon completion to ensure data integrity.
* **Hysteresis Logic:** * `current_temp > 3000`: Activate Alarm (GPIO Pin 0 HIGH).
    * `current_temp < 1500`: Deactivate Alarm (GPIO Pin 0 LOW).

---

### Bare-Metal Compilation & Linker Script
Because this project runs without an Operating System, the standard C library is excluded using the `-nostdlib` flag. To tell the compiler exactly how to map the code to the ZX-8080's physical memory, a custom linker script (`link.ld`) was authored:

* **Flash Memory (`0x0`):** Mapped for the executable code (`.text` sections) and read-only data (`.rodata`).
* **SRAM (`0x20000000`):** Mapped for initialized variables (`.data`) and uninitialized variables (`.bss`).

The resulting output is a raw `.elf` binary that can be loaded directly onto the silicon.

---

### Virtual Hardware Simulation (Renode)
To test the firmware without physical silicon, the ZX-8080 environment was built from scratch using **Renode**. 

* **Platform Definition (`zx8080.repl`):** Defines the memory map, attaching the CPU, Flash, SRAM, and Memory-Mapped I/O (MMIO) to the system bus at their exact datasheet addresses.
* **Hardware Injection (`gpio.py` & `timer.py`):** Custom Python peripherals act as the virtual silicon. They intercept the CPU's memory reads/writes to inject mock ADC thermal values and assert Timer Interrupt flags in real-time.
* **Execution Script (`zx8080.resc`):** Initializes the machine, loads the `.elf` file, sets the Stack Pointer (`SP`) and Program Counter (`PC`), and logs GPIO outputs.

###  How to Run the Simulation

**1. Compile the Firmware:**
{{< terminal command="riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -nostdlib -T link.ld main.c -o zx8080_project.elf" >}}
{{< /terminal >}}

**2. Launch and Execute in Renode:**
{{< terminal command="renode -e 's @zx8080.resc'" >}}
11:23:13.8888 [INFO] Loaded monitor commands from: scripts/monitor.py
11:23:14.4057 [INFO] Including script(s): zx8080.resc
11:23:14.4293 [INFO] System bus created.
11:23:15.4009 [INFO] sysbus: Loading block of 424 bytes length at 0x0.
11:23:15.4354 [INFO] cpu: Setting PC value to 0x0.
11:23:15.9785 [INFO] ZX-8080: Machine started.
Hardware: GPIO Write -> 0x1L    <-- Thermal Alarm (Pin 0 HIGH)
Hardware: GPIO Write -> 0x20L   <-- LED Toggled
Hardware: GPIO Write -> 0x1L
Hardware: GPIO Write -> 0x20L
... [Simulation continues] ...
{{< /terminal >}}

---

### Key Learnings

* **The `volatile` Keyword is Critical:** A major debugging breakthrough involved compiler optimization. Without the `volatile` keyword on the hardware structs, the RISC-V GCC compiler optimized away the hardware polling loops. Adding `volatile` forced the CPU to fetch fresh data from the memory bus on every cycle, allowing successful hardware synchronization.
* **Hardware Abstraction via Structs:** Instead of using error-prone pointer arithmetic, I utilized Memory-Mapped Structures. This approach maps a C `struct` directly to the peripheral's register offsets, ensuring type-safe access.
* **Register-Level Synchronization:** Practiced robust peripheral handshaking by implementing polling methods—checking hardware status flags (like `UIF` in `TIM_STAT`) and manually clearing them to acknowledge events.
* **Clock Tree Management:** Mastered the relationship between system oscillators and peripheral clock domains to derive accurate timing intervals.

<br>

<a href="https://github.com/VamsiKiran277/-EMBEDDED-small-projects/tree/main/ZX_8080_MCU" target="_blank" rel="noopener noreferrer" style="padding: 10px 20px; background-color: #24292e; color: #00d2ff; text-decoration: none; border-radius: 5px; border: 1px solid #00d2ff; font-weight: 600;">View Source on GitHub</a>