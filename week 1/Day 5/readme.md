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

<img width="1666" height="1037" alt="gtkwave_bad_case_1" src="https://github.com/user-attachments/assets/fde84eb4-c200-4a87-ae7b-82ed5c5de78f" />


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

<img width="1666" height="1037" alt="yosys_bad_case_" src="https://github.com/user-attachments/assets/19d7468b-a56d-4634-8f39-2a5cc77799bd" />

### Looping Constructs
<img width="1344" height="655" alt="image" src="https://github.com/user-attachments/assets/b7f23af3-4016-4d46-9d09-2a7ea39ac51a" />
Mux
<img width="1344" height="593" alt="looping_contructs_example" src="https://github.com/user-attachments/assets/6f0fec88-876b-4834-9563-899f640fe38c" />
                                                use blocking statements
<img width="1344" height="552" alt="use_blocking_statement" src="https://github.com/user-attachments/assets/91b469bf-df7a-40b3-aaa4-06da72ed11c7" />
                                                        Demux 
<img width="1344" height="639" alt="demux" src="https://github.com/user-attachments/assets/0cb7653d-63e7-4106-8b26-b38a60dfd808" />

#### For Generate 
<img width="1344" height="763" alt="for_generate" src="https://github.com/user-attachments/assets/09078480-3c72-4ce3-8d60-5746b7bb5120" />
                                               Ripple Carry adder
<img width="1322" height="672" alt="RCA" src="https://github.com/user-attachments/assets/95cb51b7-8c90-4be4-a971-b636f7bc598b" />

Basic difference between For and For generator is that For is used inside always block where as For generator is used outide always block statement. First thing first For is used for multiple evalution of verilog circuit where For generator is used for hardware replication.

### Lab on For and For generator
 ##### Mux_generator
<img width="1666" height="1037" alt="mux_generator" src="https://github.com/user-attachments/assets/3e8e91dd-c3e3-4dc4-b1a4-acd5a8126d41" />
<img width="1666" height="1037" alt="yosys_mux_generator" src="https://github.com/user-attachments/assets/4e3c874f-8c9b-47a1-bf44-a75c8d49e735" />
  Demux_case 
<img width="1666" height="1037" alt="gtkwave_demux_case" src="https://github.com/user-attachments/assets/b852ed45-cbe5-4e41-a822-7897a56eef50" />
<img width="1666" height="1037" alt="yosys_demux_case" src="https://github.com/user-attachments/assets/ce78b7fd-e451-438b-aa91-08283a1cec05" />
 Demux_generator 
<img width="1666" height="1037" alt="gtkwave_demux_generator" src="https://github.com/user-attachments/assets/565bc6a6-57c5-4b91-9542-111c60ae3601" />
<img width="1666" height="1037" alt="yosys_demux_generator" src="https://github.com/user-attachments/assets/13dcb808-3e3f-4b5b-9c9d-6ddea97a041e" />
Ripple carry adder 
<img width="1540" height="806" alt="gtkwave_rca" src="https://github.com/user-attachments/assets/816431aa-de8f-4417-b3b9-92821e357923" />
<img width="1666" height="1037" alt="yosys_rca" src="https://github.com/user-attachments/assets/b333c5bc-1a07-442c-af9f-c13d47398e4c" />

