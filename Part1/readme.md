# Week 3 — Gate-Level Simulation & Verification (GLS)

##  **Table of Contents**

---

- [Prerequisites](#prerequisites)  
- [Key Aspects of Post-Synthesis Simulation](#key-aspects-of-post-synthesis-simulation)  
- [Synthesis of VSDBabySoC](#synthesis-of-vsdbabysoc)   
- [VSDBabySoC Post-Synthesis Simulation Steps](#vsdbabysoc-post-synthesis-simulation-steps)  
- [Functional Verification: Pre-Synthesis vs Post-Synthesis Simulation](#functional-verification-pre-synthesis-vs-post-synthesis-simulation)  
- [Summary](#summary)

---

## Prerequisites
- Synthesized netlist: `output/synthesized/vsdbabysoc.synth.v` (or equivalent).  
- Testbench capable of running post-synthesis (`testbench.rvmyth.post-routing.v` or `testbench.v` with `-DPOST_SYNTH_SIM`).  
- Required header/include files present (e.g. `sp_verilog.vh`, `sandpiper.vh`, `sandpiper_gen.vh`) — copy them to synthesis working dir if needed.  
- Simulator (Icarus `iverilog` for functional GLS or a commercial simulator for accurate SDF timing annotation).  
- GTKWave to view `.vcd` waveforms.

---
