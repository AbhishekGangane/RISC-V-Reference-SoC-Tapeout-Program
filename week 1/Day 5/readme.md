# Day 5 - Optimization in synthesis

## If Case Constructs 
We know the basic logic to implement the if-else and multiple nested elseif conditions are implemented in the verilog coding. On the basis of verilog logic of if else statements hardware logic of flip-flops will be implement as per the conditions.
<img width="1311" height="710" alt="if_case_constructs_Intro" src="https://github.com/user-attachments/assets/0802b07b-9e47-4830-92cc-f3a78c732ac2" />

### Caution using if
Dangers of using if-else called as "Infered latches" which is one of the Bad coding style in verilog as well as in C programming.
Here when we don't declares else condition the undeclared or uninitiated input directly latches with the output that leads in error state.
In combinational circuit we can't have infered latches, so we must avoid this infered coding style.
<img width="1320" height="723" alt="DangersOfusingincomplete_if" src="https://github.com/user-attachments/assets/6660a00f-490a-4e26-80da-b0f3650ce21c" />
But this same will be perfectly fine to use when we are dealing with counters
<img width="1363" height="723" alt="counter" src="https://github.com/user-attachments/assets/b6b5913e-9a94-42b9-9852-47375ec89885" />

### Case Statement
To avoid the multiple nested elseif logic we mostly use case (as in C programming we have Switch-case) 
<img width="1363" height="739" alt="case_statements" src="https://github.com/user-attachments/assets/d4221150-bd7e-446b-bcf6-49911d788ec4" />

#### Caveats with case
But before using Case we must aware of following caveats of case statements:
##### Incomplete case
<img width="1363" height="755" alt="caveats0fcase_1" src="https://github.com/user-attachments/assets/ec29ed93-23ef-46df-b297-54a492f67399" />
Partial assignments in case
<img width="1366" height="761" alt="caveat_case_2" src="https://github.com/user-attachments/assets/e683418d-1133-4df9-a6ff-24f995ffd3ac" />
Overlaping case statement
<img width="1209" height="718" alt="caveat_case_3" src="https://github.com/user-attachments/assets/40dd8744-e796-418d-a0de-abc2ba55c7f4" />

### Lab Incomplete IF 
Lets start with lab we will simulate and synthesize the incomp_if.v incomp_if2.v incomp_case.v comp_case.v partial_case_assign
<img width="1582" height="1037" alt="vim_lab5_if_case_files" src="https://github.com/user-attachments/assets/663b324c-ff4d-4573-841d-81ee75347175" />
For Gtkwave simulation follow given commands with their respective file name
```
$ iverilog incomp_if.v tb_incom_if.v
$ ./a.out
$ gtkwave tb_incomp_if.vcd

```
<img width="1582" height="1037" alt="gtkwave_incomp_if" src="https://github.com/user-attachments/assets/14759675-99c4-4068-bf22-d1748de1e04a" />

<img width="1582" height="1037" alt="gtkwave_incomp_if2 png" src="https://github.com/user-attachments/assets/b499a189-1937-4752-b84c-2fc7ac013cfa" />

<img width="1582" height="1037" alt="gtkwave_incomp_case" src="https://github.com/user-attachments/assets/7e61c1ac-9deb-4064-a224-1ffc103c2115" />

<img width="1666" height="1037" alt="gtkwave_comp_case" src="https://github.com/user-attachments/assets/75b3c8cc-0a52-421d-b1a0-fe28b87d5e38" />

<img width="1666" height="1037" alt="gtkwave_bad_case" src="https://github.com/user-attachments/assets/adfdfb42-c938-4db8-9b2d-27a71bc91b5b" />

Compare logic of each verilog file with above simulation
<img width="1582" height="1037" alt="vim_if_case" src="https://github.com/user-attachments/assets/64c956fc-9205-41a4-8bc5-7fa674e612aa" />

For yosys synthesis follow given commands with their respective file name
```
$ cd verilog_files
$ yosys
$ read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
$ read_verilog incomp_if.v
$ synth -top incomp_if
$ abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
$ show
```
<img width="1582" height="1037" alt="yosys_incomp_if" src="https://github.com/user-attachments/assets/2e410284-4be9-4668-9bf4-afd5fdf67672" />

<img width="1582" height="1037" alt="yosys_incomp_if2 png" src="https://github.com/user-attachments/assets/80cefdac-6187-4c13-b05c-6b1d09672b60" />

<img width="1666" height="1037" alt="yosys_incomp_case" src="https://github.com/user-attachments/assets/3becaeac-c294-48de-aad5-17c95886bfd8" />

<img width="1666" height="1037" alt="yosys_comp_case" src="https://github.com/user-attachments/assets/3d88f17f-2fdc-41fe-8bbd-c4472ed98d0d" />

<img width="1666" height="1037" alt="yosys_partial_case_assign" src="https://github.com/user-attachments/assets/6cc8776c-17f8-43db-b81f-e681916d8c71" />

<img width="1666" height="1037" alt="yosys_bad_case" src="https://github.com/user-attachments/assets/42d39111-72ca-43ea-93c0-7402ee7076b4" />

