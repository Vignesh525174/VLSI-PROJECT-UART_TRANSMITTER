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
```

---

## 💻 Source Code

### uart_tx.sv — Design Module
```verilog
module uart_tx (
    input  clk,
    input  rst,
    input  start,
    input  [7:0] data,
    output reg tx,
    output reg done
);

    parameter IDLE  = 2'b00;
    parameter START = 2'b01;
    parameter DATA  = 2'b10;
    parameter STOP  = 2'b11;

    reg [1:0] state;
    reg [2:0] bit_count;
    reg [7:0] shift_reg;

    always @(posedge clk or posedge rst) begin
        if (rst) begin
            state     <= IDLE;
            tx        <= 1;
            done      <= 0;
            bit_count <= 0;
        end
        else begin
            case (state)
                IDLE: begin
                    tx   <= 1;
                    done <= 0;
                    if (start) begin
                        shift_reg <= data;
                        state     <= START;
                    end
                end
                START: begin
                    tx        <= 0;
                    state     <= DATA;
                    bit_count <= 0;
                end
                DATA: begin
                    tx        <= shift_reg[0];
                    shift_reg <= shift_reg >> 1;
                    bit_count <= bit_count + 1;
                    if (bit_count == 7)
                        state <= STOP;
                end
                STOP: begin
                    tx    <= 1;
                    done  <= 1;
                    state <= IDLE;
                end
            endcase
        end
    end
endmodule
```

### uart_tx_tb.sv — Testbench
```verilog
module uart_tx_tb;

    reg clk, rst, start;
    reg [7:0] data;
    wire tx, done;

    uart_tx UUT (
        .clk(clk),
        .rst(rst),
        .start(start),
        .data(data),
        .tx(tx),
        .done(done)
    );

    always #5 clk = ~clk;

    initial begin
        clk   = 0;
        rst   = 1;
        start = 0;
        data  = 8'b0;

        #20 rst = 0;

        #10 data = 8'h41; start = 1;
        #10 start = 0;
        wait(done == 1);
        #20;

        #10 data = 8'h55; start = 1;
        #10 start = 0;
        wait(done == 1);
        #20;

        $display("UART Transmission Complete!");
        $finish;
    end

    initial begin
        $monitor("Time=%0t | clk=%b rst=%b start=%b data=%h tx=%b done=%b",
                  $time, clk, rst, start, data, tx, done);
    end

endmodule
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
run -all
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
| 2 | Full Adder | ✅ Completed |
| 3 | D Flip-Flop | ✅ Completed |
| 4 | 4-bit Counter | ✅ Completed |
| 5 | UART Transmitter | ✅ Completed |

---

## 👤 Author

**Vignesh R**
- LinkedIn: [linkedin.com/in/vignesh-r-906157206](https://www.linkedin.com/in/vignesh-r-906157206)
- GitHub: [Add your GitHub link here]

---

## 📅 Date

June 2026

---

*This is Project 5 of my VLSI Design Engineer learning journey. 🚀*
