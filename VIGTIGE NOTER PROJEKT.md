
### OBS 
`xc7z010clg400-1`
Be wary of pathing(where the project is located) in terms of running the project in hls, ideally do it from magnus/user folder :) .

#### What “simulation” does
Simulation = **checking if your C code works correctly**  
It runs your function in software — no hardware involved.

Example:  
You test if `array_arith()` gives the right numbers.  
If it prints `PASS`, you know your math is correct.

Simulation checks **logic correctness**,  
but it doesn’t create any real hardware.


#### What “synthesis” does
Synthesis = **turning your C code into hardware**  
Vivado HLS reads your code and builds **real circuits** —  
like adders, multipliers, registers — that could run on your FPGA.

Example:  
When you synthesize `array_arith()`,  
Vivado HLS creates an actual **hardware block** that can  
take arrays and compute results **in parallel** on the ZYBO board.

Synthesis creates **hardware** (RTL)  
This is what gets implemented on the FPGA later.

---

#### Why your teacher says synthesis is the important one
Because for your **project**, the goal is to:
- **Design real hardware** from your C code
- **Optimize performance** (speed, area, latency)
- **Export IP** for Vivado
    

And **synthesis** is the step that gives all of that:
- It shows **how many LUTs, DSPs, registers** are used
- It shows **how many clock cycles (latency)** your design takes
- It generates the **RTL code** (VHDL/Verilog) for your FPGA
    

Simulation is just for checking;  
Synthesis proves your C can become **working hardware** 💪

