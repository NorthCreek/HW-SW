
What are we building?
An alarm system: reads two sensors (S1,S2) and a tiny 9×9 “camera” grid C, decides **intrusion** then handles **PIN** and **alarm** states.

## Rules for system

|Thing|Simple rule|
|---|---|
|States|Inactive → Active → Alarmed → (back to Active)|
|PIN|Valid if **even** and **< 10**|
|Sensor timing|S1,S2 every **60 ms**|
|Camera timing|C every **120 ms**|
|Intrusion|Compute `(S1+S2)*C[i,j]` for all 81 cells; if **sum ≥ 1900** ⇒ Alarm|
|Leave Alarmed|Enter valid PIN **or** wait **10 s**|
|Performance|Hardware block must finish in **≤ 92 clock cycles**|

## Todolist 

|Step|Goal|What you do|Output you keep|
|---|---|---|---|
|1|Freeze the spec|Copy the **Rules** table above into your notes|1-page spec|
|2|Draw behavior|Draw the **state boxes** (Inactive/Active/Alarmed) and arrows|Simple diagram|
|3|Make a tiny simulator|Write a small C/C++ program that updates states using the rules|Console log that proves logic|
|4|Pick what runs in HW|Put the **81-cell sum** in hardware; keep the buttons/PIN on CPU|Short paragraph: “why this split”|
|5|Create HW block (HLS)|In HLS: implement the 81-cell sum; add a start button and result register|HLS reports with **latency**|
|6|Two versions|**A:** small & pipelined. **B:** faster with more parallel work|Two HLS solutions (A,B)|
|7|Pack as IP|Export each HLS design as an **IP core**|Two IPs in your IP catalog|
|8|Build the board design|In Vivado: Zynq PS + AXI4-Lite to the IP. No fancy memory needed|Block diagram + bitstream|
|9|Write test app|On CPU: send S1,S2 and the 9×9 C, read result, toggle LEDs/print UART|Runs on board; saves CSV|
|10|Measure & compare|Fill the **Results** table (below) for A vs B|Table + short conclusion|
|11|Report|10–15 pages max (outline below)|Final PDF + project ZIP|

## How to do core parts

|Task|Tiny how-to|
|---|---|
|HLS block|Make function that takes `S1,S2` and 81 numbers of `C`. Inside: loop 81 times, add `(S1+S2)*C[k]`. Use **pipeline** pragma so it starts a new iteration every clock. Check **Latency ≤ 92** in report.|
|IP registers (AXI4-Lite)|Map inputs/outputs to registers: write S1, S2, C[0..80]; write START; read DONE + SUM.|
|Vivado wiring|Zynq PS → AXI Interconnect → your IP’s **S_AXI** port. Clock/reset from PS. Generate bitstream.|
|CPU app flow|For each test: write S1,S2,C[], start IP, wait DONE, read SUM, decide intrusion, update state, log to CSV.|

## What to measure

|Metric|What it means|How you get it|
|---|---|---|
|Latency (cycles)|How many clocks the HW block needs for one result|HLS “Latency” in C-synth report|
|Initiation Interval (II)|Clocks between starting two loop iterations|HLS report (lower is better; aim **II=1**)|
|Resources|LUT/FF/BRAM/DSP used on FPGA|Vivado utilization report|
|Time (µs)|Real time for one result|Convert cycles using design clock (from timing)|
|Energy (rough)|Work done × time|Power estimate × Time (Vivado Power)|

## Report outline
| Section                      | What you put in                                      |
| ---------------------------- | ---------------------------------------------------- |
| 1. Problem (½ page)          | The **Rules** table + goal “≤92 cycles”              |
| 2. How it behaves (1 page)   | State diagram + sequence of a full alarm             |
| 3. Design split (1 page)     | Why sum is HW and the rest is CPU                    |
| 4. Hardware block (2 pages)  | What the loop does, your pragmas, two versions (A,B) |
| 5. System build (1 page)     | One block-diagram screenshot + wiring sentence       |
| 6. Tests (1 page)            | What inputs you tried; how you logged                |
| 7. Results (2 pages)         | Filled **Results** table + one simple bar chart      |
| 8. Discussion (1 page)       | Which version you’d ship and why                     |
| 9. Short conclusion (¼ page) | One paragraph                                        |
| Appendix                     | Screenshots, exact settings, command lists           |

## Ordbog

|Term|Kid-friendly meaning|
|---|---|
|**HLS**|A tool that turns C/C++ into hardware you can put on the chip|
|**IP core**|A lego block of hardware you can reuse and wire up|
|**AXI4-Lite**|A simple road where the CPU drives data to the hardware block|
|**Latency**|How long one job takes inside the hardware (counted in clock ticks)|
|**II (Initiation Interval)**|How often the loop can start a new step (every tick if II=1)|