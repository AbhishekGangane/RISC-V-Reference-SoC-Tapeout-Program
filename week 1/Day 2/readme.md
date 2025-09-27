# Day 2 - Timing libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles

## Introduction to timing .libs
Libraries are characterized based on PVT (process, voltage, temperature) \
Process -> Variations due to fabrication \
Voltage -> Variations due to voltage \
Temperature -> Variations due to temperature 

As seen in the screenshot below \
tt stands for typical in the .lib name \
025C stands for temperature of 25 C in the .lib name \
1v80 stands for voltage of 1.8V in the .lib name

<img width="903" alt="libertyfile1" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/4d70123b-e2a7-406b-9d19-9f7bfc958840">

-cell defines the beginning of the cell. Other information of cells mentioned are:
- Leakage power based on the combination of inputs
- Area
- Power ports
- Input capacitance
- Power associated with the pin
- Transition
- Delay

## Hierarchical vs Flat Synthesis

### Hierarchical Synthesis
Report after synthesizing multiple_modules.v. As shown below the sub_modules statistics are printed. For example, sub-module1 has 1 AND gate and sub-module2 has 1 OR gate. This is an example of Hierarchical Synthesis.

Hierarchy is preserved. sub_module1 and sub_module2 are instantiated separately in the synthesized Verilog netlist. Rather than seeing AND or OR gate, we see sub_modules when we run the command 'show' as shown in the screenshot.

<img width="1713" height="1049" alt="Day2_Lab1_vim_multiple_module" src="https://github.com/user-attachments/assets/bc300db0-28ff-42ce-8a69-fd7961894837" />
<img width="1713" height="1049" alt="Day2_Lab1_vim_multiple_module" src="https://github.com/user-attachments/assets/38e8d482-a001-406d-938a-b61ee0f7d617" />


If we look into the sub_module2 in synthesized netlist 'multiple_modules_hier.v', we see that rather than OR gate, the inputs a & b, pass through the inverter and then NAND gate. It is because in CMOS, stacking PMOS, which happens in 'OR' gate is bad as PMOS has lower mobility and always have to be wider to get some meaningful output. The next step is to check .lib file for the answer.

### Flat Synthesis
The design can be flattened by using the command `flatten`.
```
$ cd verilog_files
$ yosys
$ read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
$ read_verilog multiple_modules.v
$ synth -top multiple_modules
$ abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
$ flatten 
$ show
```
Screenshot shows synthesized netlist and the logical diagram.

<img width="1721" height="1049" alt="Day2_Lab1_Yosys_multiple_modules_modified" src="https://github.com/user-attachments/assets/50a764f5-f15d-4e13-82ac-1a7de57f10d4" />

### Sub-module Level Synthesis
RTL (Register Transfer Level) designs are often modular, with various functional blocks or sub-modules. Sub-module level synthesis allows each of these sub-modules to be synthesized independently.

Why is the sub-module level synthesis necessary?
- Optimization and Area Reduction: By synthesizing sub-modules separately, the synthesis tool can optimize each one individually. It performs logic optimization, technology mapping, and area minimization for each sub-module. This leads to more efficient use of resources and reduced overall chip area.
- Resuability: Each submodule can be designed, verified, and optimized independently. They can be reused in a large design multiple times saving time and enhancing efficiency. 
- Parallel Processing: Different sub-modules can be synthesized concurrently, improving efficiency. For large designs, parallel synthesis significantly reduces turnaround time.

The commands to run sub-module synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top sub_module1
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

The screenshot shows that when sub_module1 is synthesized, only AND gate is generated. 

<img width="1628" height="1049" alt="Day2_Lab1_yosys_sub_module" src="https://github.com/user-attachments/assets/f76ff093-86da-488b-be79-46a64a82fcec" />

## Various Flop Coding Styles and Optimization

### Why do we need flops and how do they prevent glitches in the circuit?

Glitches can occur in digital circuits due to various reasons such as signal delays, noise, or timing issues. Flops prevent glitches during the operation in the following ways:
- Synchronization: Flops are edge-triggered devices, meaning they respond only to transitions of the input signal (e.g., rising edge, falling edge). This synchronization ensures that the output changes only at specific points, reducing the likelihood of glitches caused by transient signal variations.
- Timing Control: Flops are typically controlled by a clock signal, ensuring that all circuit operations occur synchronously. This eliminates timing issues that could lead to glitches due to data arriving at different times.

<img width="846" altthe ="flops1" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/32aab966-261a-4e42-9f49-59572586cd0f">

### Different types of flops
To initialize flops, we need to `set` and `reset` which can be synchronous or asynchronous.

<img width="775" alt="syn_async_reset_flop1" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/338b941f-4a51-4cf3-9289-f344afac2922">

The screenshot below shows DFF with asynchronous reset HDL simulation in Iverilog and  waveform display in GTKwave. Irrespective of the clock and d, as soon as async_reset=1, q=0.
<img width="1628" height="1049" alt="Day2_Lab2_gtkwave_dff_Asyncres" src="https://github.com/user-attachments/assets/10f12302-d2d9-4598-8af7-704f77defa45" />

### Synthesizing flops
The command to synthesize ***DFF with asynchronous reset*** as an example
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1628" height="1049" alt="Day2_Lab2_yosys_dff_asyncreas" src="https://github.com/user-attachments/assets/ed2b4c4c-e95a-429a-b418-21d13532e8ff" />


On synthesizing ***DFF with synchronous reset*** we get NOR gate with inverted `d` as shown in the screenshot below. However,on evaluating the boolean expression, we reached the same logic realization. 


<!-- <img width="1211" alt="dff_sync_reset1" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/45f7fc3b-d87f-43bc-94fd-0d9f3870382f"> -->

<img width="1628" height="1049" alt="Day2_Lab2_yosys_dff_asyncreas_modified" src="https://github.com/user-attachments/assets/73a4f483-a9cf-41af-ba95-61ab46fe269d" />

### Synthesizing mult2 (multiply by 2)

 
To implement `y[3:0] = 2*a[2:0]`, we append a `1'b0 `to the `a[2:0]` i.e, `y[3:0] = {a[2:0],0}`. This is also equal to left shift the input bits by 1.
This can be realized by just wiring.
So we expect no hardware which is also seen in the screenshot below, analysis after synthesis and show. The command 'abc' is not required for mapping when there are no cells.

<img width="1628" height="1049" alt="Yosys_mult2" src="https://github.com/user-attachments/assets/88899200-d13b-4609-85d7-a9861a65dd1c" />


### Synthesizing mult9 (multiply by 9 or 8+1)

`y=9*a` can be considered `8*a+1*a`
To implement `y[5:0] = 9*a[2:0]`, we append `000` to `a[2:0]` and then add `a` i.e, `y[5:0] = {a[2:0],000} + a[2:0]`.
This can be realized just by wiring.
So we expect no hardware which is also seen in the screenshot below, analysis after synthesis and show. The command 'abc' is not required for mapping when there are no cells.
<img width="1628" height="1049" alt="Yosys_mult8" src="https://github.com/user-attachments/assets/2afa5001-6d47-4788-b6f7-2677b7313847" />

