---
layout: default
title: FPGA Snake Game – Basys 3 Edition
---

# 🎀💗 FPGA Snake Game – Basys 3 Edition 💗🎀

[← Back to University Projects](../university-projects.md)
## Project Overview
A complete hardware implementation of the classic Snake game running on a Basys 3 FPGA board. This project demonstrates advanced digital design concepts using pure Verilog without any CPU, featuring real-time VGA graphics and interactive gameplay.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b8562e72-e7ed-45bb-83af-8c723c2a7584" width="600" alt="FPGA Snake Game Banner">
</p>

## Features 

- **Full hardware implementation** — no CPU, just pure digital logic magic!
- **VGA display** (640×480) with smooth, colorful graphics
- **Snake movement, growth, & food spawning**
- **Random food** using an LFSR
- **Sweet modular Verilog design**
- **Button controls** using the Basys 3 push-buttons

##  Project Structure 
- **`vga_controller.v`** → Handles VGA timing 
- **`snake_logic.v`** → The heart of the game (snake brain )
- **`lfsr_random.v`** → Pseudo-random 
- **`input_controller.v`** → Button debouncing so inputs behave nicely 
- **`score_count.v`** → Keeps track of your score 
- **`seg_7_disp.v`** → 7-seg score display driver 
- **`top.v / topwrapper.v`** → Everything comes together 


##  How to Play 

Connect your Basys 3 to a VGA monitor, program the bitstream, and you're ready! Use the buttons to move your little snake:

- ⬆️ **BTNU** – Up
- ⬇️ **BTND** – Down  
- ⬅️ **BTNL** – Left
- ➡️ **BTNR** – Right

##  Setup Instructions 

1. **Open** the project in Vivado
2. **Synthesize** 
3. **Generate** the bitstream 
4. **Program** the Basys 3 board 
5. **Plug in** a VGA display 

##  Technical Highlights
- **Full hardware implementation** - No microprocessor, pure digital logic
- **VGA display controller** - 640×480 resolution with real-time graphics rendering
- **Modular Verilog design** - Hierarchical state machines and clean architecture
- **LFSR random number generator** - For unpredictable food spawning
- **7-segment score display** - Real-time scoring system with visual feedback
- **Button debouncing** - Reliable input handling for smooth gameplay

##  Technologies & Tools
- **Hardware**: Xilinx Basys 3 FPGA (Artix-7)
- **Language**: Verilog/SystemVerilog
- **Development**: Vivado Design Suite 2022.x
- **Protocols**: VGA, GPIO, custom state machines
- **Simulation**: Vivado Simulator for verification

## Learning Outcomes & Skills
- Advanced FPGA design principles and best practices
- VGA timing generation and video signal protocols
- Complex state machine design and optimization techniques
- Hardware-software co-design and system integration
- Real-time system constraints and performance optimization
- Digital logic verification and testing methodologies

##  Key Features
- Real-time 60 FPS VGA output at 640x480 resolution
- Smooth snake movement with responsive controls
- Random food generation using 8-bit LFSR
- Collision detection for walls and self-intersection
- Progressive difficulty with increasing speed
- Visual and numerical score display

[View on GitHub](https://github.com/AnnabelleJohnstone/FPGA--Snake-game-for-basys-3-board-) | [← Back to University Projects](../university-projects.md)

