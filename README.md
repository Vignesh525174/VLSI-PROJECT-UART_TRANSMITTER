# UART Transmitter — Verilog Implementation

## 📌 Project Overview

A **UART (Universal Asynchronous Receiver Transmitter) Transmitter** is a serial communication circuit that transmits 8-bit data bit by bit using a standard UART protocol format — Start bit, 8 Data bits, and Stop bit.

This project implements a UART Transmitter using **Verilog HDL** and simulates it using **ModelSim - Intel FPGA Starter Edition 2020.1**.

---

## 🔧 Tools Used

| Tool | Purpose |
|------|---------|
| ModelSim 2020.1 | HDL Simulation |
| Intel FPGA Starter Edition | EDA Environment |
| Verilog (SystemVerilog) | Hardware Description Language |

---

## 📐 UART Protocol Format

```
IDLE  | START | D0 | D1 | D2 | D3 | D4 | D5 | D6 | D7 | STOP
  1       0    LSB                                   MSB    1
```

- **IDLE** — Line stays HIGH
- **START bit** — Line goes LOW (1 bit)
- **DATA bits** — 8 bits sent LSB first
- **STOP bit** — Line goes HIGH (1 bit)

---

## 🔄 FSM State Diagram

```
        ┌─────────┐
        │  IDLE   │ ◄─────────────────────┐
        └────┬────┘                       │
             │ start=1                    │
             ▼                            │
        ┌─────────┐                       │
        │  START  │                       │
        └────┬────┘                       │
             │                            │
             ▼                            │
        ┌─────────┐                       │
        │  DATA   │ (8 bits, LSB first)   │
        └────┬────┘                       │
             │ bit_count=7                │
             ▼                            │
        ┌─────────┐                       │
        │  STOP   │ ──── done=1 ──────────┘
        └─────────┘
```

### States

| State | tx Output | Description |
|-------|-----------|-------------|
| IDLE  | 1 | Waiting for start signal |
| START | 0 | Sending start bit |
| DATA  | data[bit] | Sending 8 data bits LSB first |
| STOP  | 1 | Sending stop bit, done=1 |

---

## 📁 File Structure

```
UART_Transmitter/
├── uart_tx.sv        # UART Transmitter design module
├── uart_tx_tb.sv     # Testbench for simulation
└── README.md         # Project documentation
└── UART_TRANSMITTER  # UART Transmitter OUTPUT WAVEFORM
```

---


## ✅ Simulation Output

### Console Log
```
Time=0   | clk=0 rst=1 start=0 data=00 tx=1 done=0
Time=20  | clk=0 rst=0 start=0 data=00 tx=1 done=0
Time=30  | clk=0 rst=0 start=1 data=41 tx=1 done=0
Time=40  | clk=0 rst=0 start=0 data=41 tx=0 done=0
...
UART Transmission Complete!
```

### Waveform Output
```
clk  : ‾|_|‾|_|‾|_|‾|_|‾|_|‾|_|‾|_|‾|_
rst  : ‾‾‾‾‾|_______________________________
start: __|‾‾|________________________________
data : xxxxxxx|01000001|xxxxxxx|01010101|xxx
tx   : ‾‾‾‾‾‾|_|d|d|d|d|d|d|d|d|‾‾‾‾‾‾‾‾‾
done : _________________________|‾‾|_______
```

---

## 🚀 How to Run in ModelSim

**Step 1** — Open ModelSim as Administrator

**Step 2** — Create new project and add both `.sv` files

**Step 3** — Compile all files:
```
Compile → Compile All
```

**Step 4** — In VSIM console type:
```tcl
vlib work
vmap work work
vsim work.uart_tx_tb
add wave *

```

**Step 5** — View waveform:
```tcl
view wave
```

---

## 📚 Concepts Learned

| Concept | Description |
|---------|-------------|
| FSM Design | 4-state machine (IDLE, START, DATA, STOP) |
| Serial Communication | Transmitting bits one at a time |
| Shift Register | Shifting data LSB first |
| Testbench Writing | Clock generation, wait statements |
| Waveform Analysis | Reading timing diagrams in ModelSim |
| UART Protocol | Industry standard serial communication |

---

## 🔗 Related Projects

| # | Project | Status |
|---|---------|--------|
| 1 | Half Adder | ✅ Completed |
| 2 | UART Transmitter | ✅ Completed |

---

## 👤 Author

**Vignesh R**
- LinkedIn: [linkedin.com/in/vignesh-r-906157206](https://www.linkedin.com/in/vignesh-r-906157206)
- GitHub: https://github.com/Vignesh525174

---

## 📅 Date

June 05, 2026

---

*This is Project 2 of my VLSI Design Engineer learning journey. 🚀*
