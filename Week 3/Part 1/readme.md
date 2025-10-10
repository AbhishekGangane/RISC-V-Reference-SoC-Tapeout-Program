# Part 1: Post-Synthesis GLS


### Purpose of GLS:
Gate-Level Simulation is used to verify the functionality of a design after the synthesis process. Unlike behavioral or RTL (Register Transfer Level) simulations, which are performed at a higher level of abstraction, GLS works on the netlist generated post-synthesis. This netlist includes the actual gates and connections used to implement the design.

### Key Aspects of GLS for BabySoC:
1. **Verification with Timing Information:**
   - GLS is performed using Standard Delay Format (SDF) files to ensure timing correctness.
   - This checks if the SoC behaves as expected under real-world timing constraints.

2. **Design Validation Post-Synthesis:**
   - Confirms that the design's logical behavior remains correct after mapping it to the gate-level representation.
   - Ensures that the design is free from issues like metastability or glitches.

3. **Simulation Tools:**
   - Tools like Icarus Verilog or a similar simulator can be used for compiling and running the gate-level netlist.
   - Waveforms are typically analyzed using GTKWave.

4. **Importance for BabySoC:**
   - BabySoC consists of multiple modules like the RISC-V processor, PLL, and DAC. GLS ensures that these modules interact correctly and meet the timing requirements in the synthesized design.


Here is the step-by-step execution plan for running the  commands manually:
---
### **Step 1: Load the Top-Level Design and Supporting Modules**
```bash
yosys
```
<img width="1141" height="407" alt="yosys" src="https://github.com/user-attachments/assets/5b88adeb-28b0-4ae4-968c-86c08364d1b4" />


Inside the Yosys shell, run:
```yosys
read_verilog /home/path/VSDBabySoCC/VSDBabySoC/src/module/vsdbabysoc.v
read_verilog -I /home/path/VSDBabySoCC/VSDBabySoC/src/include /home/path/VSDBabySoCC/src/module/rvmyth.v
read_verilog -I /home/path/VSDBabySoCC/VSDBabySoC/src/include /home/path/VSDBabySoCC/src/module/clk_gate.v

```
<img width="1445" height="740" alt="yosys_run" src="https://github.com/user-attachments/assets/be918eb6-4a39-45e5-9b0d-91c54083b10e" />

---

### **Step 2: Load the Liberty Files for Synthesis**
Inside the same Yosys shell, run:
```yosys
read_liberty -lib /home/path/VSDBabySoCC/VSDBabySoC/src/lib/avsdpll.lib
read_liberty -lib /home/path/VSDBabySoCC/VSDBabySoC/src/lib/avsddac.lib
read_liberty -lib /home/path/VSDBabySoCC/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

<img width="1445" height="894" alt="yosys_load" src="https://github.com/user-attachments/assets/786ba31f-b492-471c-9b42-e79d0c817662" />

---

### **Step 3: Run Synthesis Targeting `vsdbabysoc`**
```yosys
synth -top vsdbabysoc
```

<img width="1445" height="1001" alt="design_hierarchy" src="https://github.com/user-attachments/assets/315b4360-723d-49bf-aa22-fbb890a6076a" />
<img width="1445" height="980" alt="design_hierarchy_run" src="https://github.com/user-attachments/assets/2a1ff3d2-9775-4a34-90a3-69b6ca6b294d" />
<img width="1445" height="1021" alt="design_hierarchy_run_2" src="https://github.com/user-attachments/assets/23276067-1034-4116-bf31-ae31ef737703" />

---

### **Step 4: Map D Flip-Flops to Standard Cells**
```yosys
dfflibmap -liberty /home/path/VSDBabySoCC/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

<img width="1445" height="1021" alt="Map D Flip-Flops" src="https://github.com/user-attachments/assets/90d5ae55-9cc5-44e0-8f2e-7ecfc2e5e677" />

---

### **Step 5: Perform Optimization and Technology Mapping**
```yosys
opt
abc -liberty /home/path/VSDBabySoCC/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```

<img width="1445" height="1036" alt="Perform Optimization_1" src="https://github.com/user-attachments/assets/e32f98d6-0092-4ad6-b8cf-1ca83661b064" />
<img width="1445" height="166" alt="Perform Optimization_2" src="https://github.com/user-attachments/assets/dc5448a6-095a-44b0-8afd-af42825eab51" />

---


### **Step 6: Perform Final Clean-Up and Renaming**
```yosys
flatten
setundef -zero
clean -purge
rename -enumerate
```
<img width="1445" height="237" alt="Perform Final Clean-Up_1" src="https://github.com/user-attachments/assets/7ddd941e-1372-4272-a2bc-4fc804040f9f" />

<img width="1445" height="237" alt="Perform Final Clean-Up_2" src="https://github.com/user-attachments/assets/e1f59942-414f-46e2-ace0-b5dacf079373" />

---

### **Step 7: Check Statistics**
```yosys
stat
```
<img width="1445" height="1043" alt="Check Stat" src="https://github.com/user-attachments/assets/9950978d-7d89-494b-8064-6345bee8eae2" />

---

### **Step 8: Write the Synthesized Netlist**
```yosys
write_verilog -noattr /home/path/VSDBabySoCC/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v
```

<img width="1445" height="191" alt="write_verilog_synth" src="https://github.com/user-attachments/assets/326b03cb-a890-414e-aec0-85eb115d35e5" />

---

## POST_SYNTHESIS SIMULATION AND WAVEFORMS
---

### **Step 1: Compile the Testbench**
Run the following `iverilog` command to compile the testbench:
```bash
iverilog -o /home/path/VSDBabySoCC/VSDBabySoC/output/post_synth_sim/post_synth_sim.out -DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 -I /home/path/VSDBabySoCC/VSDBabySoC/src/include -I /home/path/VSDBabySoCC/VSDBabySoC/src/module /home/path/VSDBabySoCC/VSDBabySoC/src/module/testbench.v
```
---
### **Step 2: Navigate to the Post-Synthesis Simulation Output Directory**
```bash
cd output/post_synth_sim/
```
---
### **Step 3: Run the Simulation**

```bash
./post_synth_sim.out
```
---
### **Step 4: View the Waveforms in GTKWave**

```bash
gtkwave post_synth_sim.vcd
```
---
<img width="1855" height="732" alt="Post-Synthesis_2" src="https://github.com/user-attachments/assets/7a269a96-23ae-42fe-9b8e-88414d0643cd" />

<img width="1456" height="770" alt="pre_synth_sim_2" src="https://github.com/user-attachments/assets/7bfb5673-4b60-4904-aa2d-aa0b087eeb93" />

 Compare both simulated wave pre and post synthesis simulation
