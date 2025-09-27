# Day 3 - Combinational and Sequential Optimizations

## Introduction to Optimizations

### Combinational Logic Optimization
It means squeezing the logic to get the most optimized design in terms of area and power. the most commonly used techniques are:
1) Constant propagation using direct optimization
2) Boolean logic optimization using K-map and Quine McKlusky

An example of constant propagation optimization is highlighted below.
<img width="618" alt="1_const_prop" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/c8bd1118-52f7-441b-8cff-254d851cb892">

An example of boolean optimization is highlighted below.

<img width="619" alt="2-boolean-opt" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/aa864102-ef33-4d45-9ec9-929738172cd4">

### Sequential Logic Optimization
The technqiues used are:
1) Basic
   - Sequential constant propagation
2) Advanced (not covered as part of lab)
   - Static optimization
   - Retiming
   - Sequential logic cloning (floorplan aware synthesis)

An example of sequential constant propagation is highlighted below of DFF with asynchronous reset where D input is grounded. To note, the same technique cannot be applied to DFF with the asynchronous set because while `Q=1` when `Set=1`, but `Q=0` at `Set=0` at the next CLK pulse. Q is dependent not only on Set but also on the clock edge.

<img width="629" alt="3-seq-const-prop" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/3e31a212-a0b0-42c3-be92-d0075a9f7d1c">

Retiming is a technique to improve the performance of the circuit.

<img width="600" alt="4-seq-adv" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/23bcc15c-813b-496a-aebf-ebbf5ceba557">

## Combinational Logic Optimizations
Commands for optimization

```
opt_clean -purge
```
### Optimization of opt_check.v
Syntax for opt_check.v
```
module opt_check (input a , input b , output y);
        assign y = a?b:0;
endmodule
```
For opt_check.v the assignment `y = a?b:0` reduces to `y = ab`. The screenshot shown below explains this

<img width="533" alt="5-seq-opt" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/f4b6a999-f665-412f-a705-9496bfdd04c2">

The logic implementation after synthesis for opt_check.v is shown below, showing only AND gate.
<img width="1544" height="1049" alt="yosys_opt_check" src="https://github.com/user-attachments/assets/25e116e4-b120-45b2-99de-8798d9ef4dee" />


### Optimization of opt_check2.v
Syntax for opt_check2.v
```
module opt_check2 (input a , input b , output y);
        assign y = a?1:b;
endmodule
```
For opt_check2.v the assignment `y = a?1:b` reduces to `y = a + b`. 

The logic implementation after synthesis for opt_check2.v is shown below, showing only OR gate.
<img width="1544" height="1049" alt="yosys_opt_check2" src="https://github.com/user-attachments/assets/7aff28c9-65d3-4f25-a687-73357a23f607" />


### Optimization of opt_check3.v
Syntax for opt_check3.v
```
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
For opt_check.v the assignment `y = a?(c?b:0):0` reduces to `y = abc`. The screenshot shown below explains this.

<img width="541" alt="8-opt-check3" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/c9fb59d8-d080-4776-bec6-46e3b48b3d68">

The logic implementation after synthesis for opt_check3.v is shown below, showing 3 input AND gate.

<img width="1544" height="1049" alt="yosys_opt_check3" src="https://github.com/user-attachments/assets/d8f5f96c-9067-4090-8701-766d03e83a44" />
<img width="1544" height="1049" alt="yosys_opt_check4" src="https://github.com/user-attachments/assets/c9f917d6-d4f3-49b1-9e45-9dfc89c96177" />

### Optimization of multiple_module_opt.v

Syntax of multiple_module_opt.v
```
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule

module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule

module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 
endmodule
```
The logic implementation after synthesis for multiple_module_opt.v is shown below.
<!-- <img width="1080" alt="10-multiple-module-opt" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/f2486353-e1f9-44e3-97b1-4b235912b69d"> -->
<img width="1544" height="1049" alt="multiple_module_opt" src="https://github.com/user-attachments/assets/b84ac585-eb00-458e-8ed2-e475663cd43a" />

<img width="1544" height="1049" alt="yosys_multiple_module_opt" src="https://github.com/user-attachments/assets/899d8913-7fbc-49a4-aa66-bf3a7850db9a" />

## Sequential Logic Optimizations

Both the dff_const1.v and dff_const2 are explained below.

<img width="851" alt="12-dff-const1-const2" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/b9bede59-edaa-4f4f-ad90-9036c63aa4da">

### Optimizing dff_const1.v

Syntax for dff_const1.v
```
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end

endmodule
```
For dff_const1.v, `q=0` as long as `reset=1`. However, when `reset=0` `q` doesn't immediately becomes `1` rather at the next rising edge of the `clk` as shown below. ***So the optimization cannot be applied***.

<img width="1589" height="1049" alt="gtkwave_dff_const1" src="https://github.com/user-attachments/assets/3c5e9888-63e5-4d20-9a49-05eadc83e937" />
<img width="1589" height="1049" alt="gtkwave_dff_const2" src="https://github.com/user-attachments/assets/993aa59b-617f-4a7a-9812-036965befb8e" />


The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

The logic implementation after synthesis for dff_const1.v is shown below.

<!-- <img width="1680" alt="13-dff-const1" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/dcde9e56-06b9-4b20-9b7c-4e44477df7ba"> -->
<img width="1626" height="1049" alt="yosys_dff_const1" src="https://github.com/user-attachments/assets/df3eb469-d5bb-4899-a063-bb86d0e6e346" />
<img width="1626" height="1049" alt="yosys_dff_const2" src="https://github.com/user-attachments/assets/11ea9269-df59-4ff3-996c-3fb41b9bda79" />
<!-- <img width="1626" height="1049" alt="yosys_dff_const3" src="https://github.com/user-attachments/assets/e3d32fa7-4fcd-4ce2-9f8e-b769fb017aba" /> -->
<img width="1626" height="1049" alt="yosys_dff_const4" src="https://github.com/user-attachments/assets/5e4dbd0c-8aa7-4aa7-90b6-6f5d848567f5" />
<img width="1626" height="1049" alt="yosys_dff_const5" src="https://github.com/user-attachments/assets/76c3dd37-2bed-4cd5-9efb-4cc80c4ded59" />

***similarly dff_const2,4,5***

### Optimizing dff_const3.v

Syntax for dff_const3.v
```
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```
For dff_const3.v, there are two flops.  `q1=0` as long as `reset=1`. However, when `reset=0` `q1` doesn't immediately becomes `1` rather at the next rising edge of the `clk` with some propagation delay as shown below. `q=1` as long as `reset=1`, acting as `set` rather than `reset`. However, when `reset=0` `q` samples `q1` as `0` as there are some propagation delay for `q1`as shown below. At the next `clk` edge `q` samples `q1` as `1`.
***So the optimization cannot be applied***.

<img width="973" alt="14-dff-const3" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/6bf8a7c4-07f1-4f70-9878-e2773b3eeab5">
The command to run HDL simulation
```
iverilog dff_const3.v tb_dff_const3.v
./a.out
gtkwave tb_dff_const3.vcd
```
The HDL simulation is shown below.

 <img width="1626" height="1049" alt="gtkwave_dff_const3" src="https://github.com/user-attachments/assets/64b9a8d9-1f2e-42b5-b5a7-2978a22050a9" />

The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

The logic implementation after synthesis for dff_const3.v is shown below.

<img width="1626" height="1049" alt="yosys_dff_const3" src="https://github.com/user-attachments/assets/e3d32fa7-4fcd-4ce2-9f8e-b769fb017aba" />
## Sequential Optimzations for Unused Outputs

### Optimization of Case1: 3-bit Up Counter with q[0] used (counter_opt.v)
Example of a counter where bits at the position of [2] and [1] are unused.

```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
The screenshot explains the logic of the counter. Only q[0] is used. ***So the optimization can be applied***.

<img width="1200" alt="17-counter" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/dbdbd8d5-a305-4f31-b8ba-a8c33d53d67a">

The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
We see only one flop after the synthesis and also seen in synthesis report after `synth -top counter_opt.v`

<img width="1626" height="1049" alt="yosys_counter_opt" src="https://github.com/user-attachments/assets/7280218d-765d-4486-abb7-48242316befa" />


### Optimization of Case2: 3-bit Up Counter (counter_opt2.v)

Example of a counter where all the bits are used.
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
We see only 3 flops after the synthesis and also seen in synthesis report after `synth -top counter_opt.v`

<img width="1812" height="1049" alt="yosys_counter_opt1" src="https://github.com/user-attachments/assets/8014ecea-28be-4313-85bd-9001c81bef62" />


<img width="1167" alt="21-counter-opt2" src="https://github.com/sukanyasmeher/sfal-vsd/assets/166566124/11cd582b-4ccd-4b99-82e2-1c68c92db131">
