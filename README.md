# **STM32 LED & Button Control — Register Coding Method**

A **bare-metal STM32 project** that demonstrates **LED blink delay control using two push buttons**, implemented using **direct register access** (no HAL GPIO APIs for logic control).

This project focuses on **real-world embedded behavior**: timing control, input handling, and boundary protection — all at the **register level**.

---

## 🎯 Project Objective

The goal of this project is to **dynamically control LED blink speed** using two buttons:

* **LED** on **Pin PA5 (Arduino D13 / Pin 13)**
* **Button 1** on **Pin PA6 (Arduino D12 / Pin 12)** → *Increase delay*
* **Button 2** on **Pin PA7 (Arduino D11 / Pin 11)** → *Decrease delay*

### Functional Behavior

| Action           | Result                                  |
| ---------------- | --------------------------------------- |
| Button-1 pressed | Delay **increases by 100 ms**           |
| Button-2 pressed | Delay **decreases by 100 ms**           |
| Delay range      | **0 ms to 1000 ms**                     |
| LED behavior     | Blinks continuously using current delay |

This mimics a **human-controlled timing system**, a pattern widely used in industrial HMI logic.

---

## 🧠 Why Register-Level Coding?

This project intentionally avoids HAL GPIO logic to help you:

* Understand **how GPIO really works**
* Gain **full control over timing & pin state**
* Eliminate abstraction overhead
* Debug hardware with absolute clarity

Everything is driven by:

* `RCC->AHB1ENR`
* `GPIOx->MODER`
* `GPIOx->IDR`
* `GPIOx->ODR`
* SysTick timing

No magic. No shortcuts.

---

## 🧰 Hardware Mapping

| Component          | STM32 Pin | Arduino Label |
| ------------------ | --------- | ------------- |
| LED                | PA5       | D13           |
| Button – Increment | PA6       | D12           |
| Button – Decrement | PA7       | D11           |

> Buttons should use **pull-down or pull-up logic** (external or internal).

---

## 📦 Repository Structure

```
STM32-LED-BUTTON-CTRL-Register-Coding-Method/
├── Core/
│   ├── Src/
│   │   └── main.c        # Register-level LED & button logic
│   └── Inc/
│       └── main.h
├── Drivers/
│   └── CMSIS/            # Device headers & startup code
├── startup_stm32f446xx.s
├── STM32F446RETX_FLASH.ld
├── .project
├── .cproject
└── README.md
```

All logic lives inside `main.c`.

---

## ⚙️ Core Logic Explained (High Level)

### 1️⃣ Clock Enable

Enable GPIOA and SysTick clocks using RCC.

### 2️⃣ GPIO Configuration

* **PA5** → Output (LED)
* **PA6, PA7** → Input (Buttons)

Configured manually via `MODER`.

---

### 3️⃣ Delay Control Logic

* Delay variable starts at **500 ms** (default)
* Button presses update delay dynamically
* Delay is **clamped** between `0–1000 ms`

```c
if (button_inc_pressed) {
    delay += 100;
}
if (button_dec_pressed) {
    delay -= 100;
}
```

Boundary protection ensures:

```c
if (delay > 1000) delay = 1000;
if (delay < 0) delay = 0;
```

---

### 4️⃣ LED Blink

LED toggles continuously using the current delay value:

```c
GPIOA->ODR ^= (1 << 5);
delay_ms(current_delay);
```

This creates **real-time visual feedback** based on button input.

---

## 🚀 How to Run

### 1️⃣ Clone Repo

```bash
git clone https://github.com/DanielRajChristeen/STM32-LED-BUTTON-CTRL-Register-Coding-Method.git
```

### 2️⃣ Open in STM32CubeIDE

> CubeIDE is used **only as compiler and debugger**

* Import existing project
* Build once

### 3️⃣ Flash & Test

* Connect board via ST-LINK
* Run / Debug
* Press buttons and watch LED speed change live

---

## 📘 What You’ll Learn

By completing this project, you’ll understand:

* GPIO input & output at register level
* Button-driven control systems
* Timing control using SysTick
* Delay clamping & boundary conditions
* Real-time firmware behavior

This is **core embedded thinking**, not just coding.

---

## 🧪 Typical Use-Cases

* Beginner-friendly **embedded timing demo**
* Register-level GPIO practice
* Interview preparation project
* PCB button/LED validation
* Transition step from HAL → Bare-metal

---

## 📜 License — MIT

```
MIT License

Copyright (c) 2025 Daniel Raj Christeen

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.
```

---
