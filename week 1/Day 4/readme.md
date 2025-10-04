
# Day 4 - GLS, Synthesis-Simulation Mismatch, and Blocking/Non-blocking Statements

### Why is Gate Level Simulation (GLS) necessary?
- Verify the correctness of the design after synthesis
- Ensure the timing of the design is met which is done with delay annotation (timing aware)

  <img width="628" alt="1-gls-iverilog" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/e3bef5db-4722-4561-ad10-0370a924fdc9">
  
### Synthesis Simulation Mismatches

It happens because of the following reasons
- Missing sensitivity list
- Blocking vs non-blocking assignments
- Non-standard verilog coding

#### (1) Missing sensitivity list

As shown in the screenshot below, `always` block is evaluated only when `sel` is changing. So output `y` is not evaluated when `sel` is not changing although `i0` and `i1` are changing. Rather it acts like a latch. The code on the right side represents the correct design coding for `mux`. In this case `always` is evaluated for any signal changes. 

<img width="632" alt="2-missing-sensitivity-list" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/5c494646-600e-4b19-a3ce-7db8c1baa5a7">

#### (2) Blocking vs Non-blocking Assignments

 ##### Blocking Statements
 
 - Represented by `=`
 - Executes the statements in the order it is written inside always block
 - So the first statement is evaluated before the second statement

##### Non-Blocking Statements
- Represented by `<=`
- Executes all the RHS when always block is entered and assigns to LHS
- Parallel execution

   The left side of the screenshot below gives us the correct execution. While the right side can lead to serious issues as `d` is assigned to `q` directly. ***So choosing non-blocking statements is best practice*** (highlighted in the screenshot below).

<img width="612" alt="4-blocking-statement" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/bde53551-9858-4eee-afd2-f37cd0dff762">

 ##### Blocking Statements Leading to Synthesis Simulation Mismatch

In the code shown below, `y` gets the old `q0` value. This will mimic delay or flop. But when you synthesize, there will be no flop. If the order is changed (right side code), latest value of `q0` is assigned to `y`. 

When synthesized, both will lead to the same circuit. However, simulation will result in different behavior. For the left side of the code, `y` gets the old `q0` value and for the right side of the code, `y` gets the latest `q0` value leading to a synthesis simulation mismatch. 

This issue is resolved by using ***non-blocking statements***.

 <img width="615" alt="5-blocking-statement" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/aed8bcbb-ca5e-467d-bfd7-8c679ce91263">

## Labs on GLS and Synthesis-Simulation Mismatch

### Ternary operator MUX (ternary_operator_mux.v)

The Verilog code of ternary_operator_mux.v
```
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```
The command to run HDL simulation
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
HDL Simulation waveform of ternary_operator_mux.v is shown in the screenshot below

<img width="1584" height="913" alt="gtkwave_ternary_operator_mux" src="https://github.com/user-attachments/assets/642b0a63-5b55-4700-9e9b-1e967f7d1884" />

The commands to run the synthesis for ternary_operator_mux.v
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog ternary_operator_mux_net.v
```

<img width="1584" height="1052" alt="yosys_ternary_operator_mux" src="https://github.com/user-attachments/assets/ec0244ae-3d3e-4b6d-8747-3e095c7fedb5" />

The commands to do GLS for ternary_operator_mux.v
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
The GLS output is shown below.

<img width="1678" height="1052" alt="gtkwave_ternary_operator_mux_1" src="https://github.com/user-attachments/assets/091dbd1b-9bdd-4271-8b06-c11cde588b3d" />

### Bad MUX (bad_mux.v)

The `always` block is executed only at `sel` signal. It works like a flop rather than mux.
The Verilog code of bad_mux.v
```
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```

The command to run HDL simulation
```
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
HDL Simulation waveform of bad_mux.v is shown in the screenshot below

<img width="1678" height="1052" alt="gtkwave_bad_mux" src="https://github.com/user-attachments/assets/a9b460ab-eba7-44d4-b2c2-c85775dcd3a5" />

The commands to run the synthesis for bad_mux.v.
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog bad_mux_net.v
```

The synthesis report shows it is still inferring the mux but not the flop.

<img width="1678" height="1052" alt="yosys_bad_mux" src="https://github.com/user-attachments/assets/1ebd674f-562e-4c18-b572-587ba3113cd2" />

The commands to do GLS for bad_mux.v
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
The GLS output is shown below. This shows correct functionality which is different from HDL simulation, leading to ***synthesis simulation mismatch***.

<img width="1678" height="1052" alt="gtkwave_bad_mux_gls" src="https://github.com/user-attachments/assets/d979d951-3390-45e2-a25b-fcf6f17000fb" />

## Labs on Synthesis-Simulation Mismatch for Blocking Statements

### Blocking Caveat (blocking_caveat.v)

The logic to simulate is shown below.

<img width="594" alt="12-blocking-caveat" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/d7e6ea20-93fb-41d4-9f56-9910fe8f9931">

The Verilog code of blocking_caveat.v
```
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

The command to run HDL simulation
```
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
HDL Simulation waveform of blocking_caveat.v is shown in the screenshot below. `d` takes the old value of `x` causing incorrect functionality.

<img width="1678" height="1052" alt="gtkwave_tb_blocking_caveat" src="https://github.com/user-attachments/assets/7b65048c-1326-4b51-9ad9-1b9c43a8f3cf" />


The commands to run the synthesis for bad_mux.v.
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog blocking_caveat_net.v
```

The synthesis report and logic synthesis is shown below.

<img width="1678" height="1052" alt="yosys_blocking_caveat" src="https://github.com/user-attachments/assets/7340b23b-6216-44a2-8857-443dfed520c9" />

The commands to do GLS for bad_mux.v
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
The GLS output is shown below. In this case, `d` takes the current value of `x` causing incorrect functionality.The waveform shows correct functionality which is different from HDL simulation, leading to ***synthesis simulation mismatch***.

<img width="1678" height="1052" alt="gtkwave_blocking_caveat_" src="https://github.com/user-attachments/assets/890fa2d9-2f33-4064-bcab-1c6d62e4581f" />
