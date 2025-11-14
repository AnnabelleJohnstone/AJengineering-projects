# 🎮 FPGA Snake Game

[← Back to University Projects](/university-projects)

## Project Overview
A complete hardware implementation of the classic Snake game running on a Basys 3 FPGA board. This project demonstrates advanced digital design concepts using pure Verilog without any CPU, featuring real-time VGA graphics and interactive gameplay.

![FPGA Snake Game Banner](/assets/images/fpga-snake-banner.png)

### 🔧 Technical Highlights
- **Full hardware implementation** - No microprocessor, pure digital logic
- **VGA display controller** - 640×480 resolution with real-time graphics rendering
- **Modular Verilog design** - Hierarchical state machines and clean architecture
- **LFSR random number generator** - For unpredictable food spawning
- **7-segment score display** - Real-time scoring system with visual feedback
- **Button debouncing** - Reliable input handling for smooth gameplay

### 🛠️ Technologies & Tools
- **Hardware**: Xilinx Basys 3 FPGA (Artix-7)
- **Language**: Verilog/SystemVerilog
- **Development**: Vivado Design Suite 2022.x
- **Protocols**: VGA, GPIO, custom state machines
- **Simulation**: Vivado Simulator for verification

### 🎯 Learning Outcomes & Skills
- Advanced FPGA design principles and best practices
- VGA timing generation and video signal protocols
- Complex state machine design and optimization techniques
- Hardware-software co-design and system integration
- Real-time system constraints and performance optimization
- Digital logic verification and testing methodologies

### 📊 Key Features
- Real-time 60 FPS VGA output at 640x480 resolution
- Smooth snake movement with responsive controls
- Random food generation using 8-bit LFSR
- Collision detection for walls and self-intersection
- Progressive difficulty with increasing speed
- Visual and numerical score display

[View on GitHub](https://github.com/yourusername/fpga-snake-game) | [← Back to University Projects](/university-projects)
