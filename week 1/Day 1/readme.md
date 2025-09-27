
<!-- <summary>Day 1 - Introduction to Verilog RTL Design and Synthesis</summary> -->

# Day 1 - Introduction to Verilog RTL Design and Synthesis
## Introduction to open-source simulator Iverilog

Folder structure of the git clone:
- `lib` - will contain sky130 standard cell library
- `my_lib/verilog_models` - will contain standard cell verilog model
- `verilog_files` -contains the lab experiments source files

<img width="762" alt="intro_iverilog" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/ceceb871-47f9-4edc-8ec7-ac273dc5352c">


Example of a design good_mux.v 

```
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
Example of a testbench tb_good_mux.v 

```
`timescale 1ns / 1ps
module tb_good_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```
Command to run the design and testbench
```
iverilog good_mux.v tb_good_mux.v
```
The output of the iverilog is a .vcd file and a.out file is created. By executing a.out iverilog dump the vcd file.

## Introduction to GTKWave
gtkwave will be used to generate the waveforms and display in visual format.

Command to view the vcd file in gtkwave 
```
gtkwave tb_good_mux.vcd
```
The waveform in gtwave is shown below

<img width="1845" height="1019" alt="Day1_Lab2_gtkwave_tb_good_mux_" src="https://github.com/user-attachments/assets/395d128c-55c8-4c45-b55d-d9d19388a0e5" />
<img width="1845" height="1019" alt="Day1_Lab2_gtkwave_tb_good_mux_focus" src="https://github.com/user-attachments/assets/6d825967-53b8-4f64-9abc-b896dd6289e7" />

## Introduction to Yosys

It is the synthesizer used to convert RTL to netlist.
Netlist should be the same as the Design but represented in the form of standard cells.
The same testbench can be used to verify RTL and Synthesized Netlist.

<img width="800" alt="intro_yosys" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/0920be6f-770d-447d-a2cf-eaf73280539e">

## Introduction to Logic Synthesis

<img width="611" alt="intro_logic_synthesis1" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/d01c7771-7bb7-42cd-b7a1-24472ca61226">

## Lab using Yosys and Sky130 PDKs
```
$ cd verilog_files
$ yosys
$ read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
$ read_verilog good_mux.v
$ synth -top good_mux
$ abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
$ show
```
<img width="1434" height="1054" alt="Day1_Lab2_yosys_tb_good_mux" src="https://github.com/user-attachments/assets/33fff7e3-1e7e-4f92-a7c7-1419f6e85ee7" />
<img width="1713" height="1049" alt="Day1_Lab2_yosys_good_mux" src="https://github.com/user-attachments/assets/c842e94f-8e46-4d9c-861c-38dc9d933110" />

