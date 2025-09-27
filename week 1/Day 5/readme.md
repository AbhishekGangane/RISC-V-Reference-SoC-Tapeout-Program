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
<img width="1363" height="755" alt="caveats0fcase_1" src="https://github.com/user-attachments/assets/ec29ed93-23ef-46df-b297-54a492f67399" />
