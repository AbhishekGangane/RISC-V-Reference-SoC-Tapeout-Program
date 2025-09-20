# RISC-V-Reference-SoC-Tapeout-Program

 # week 0: Tools installation instructions 
 
# Yosys
```
$ sudo apt-get update

$ git clone https://github.com/YosysHQ/yosys.git (if git is not installed please install using sudo apt install git)

$ cd yosys

$ sudo apt install make (If make is not installed please install it)

$ sudo apt-get install build-essential clang bison flex \
libreadline-dev gawk tcl-dev libffi-dev git \
graphviz xdot pkg-config python3 libboost-system-dev \
libboost-python-dev libboost-filesystem-dev zlib1g-dev

$ make config-gcc

$ make

$ sudo make install
```
<img width="1920" height="1080" alt="yosys" src="https://github.com/user-attachments/assets/3d3e72ee-6fde-4f7d-b728-8b868285656a" />


<img width="1053" height="1026" alt="yosys_complete" src="https://github.com/user-attachments/assets/6b1f8efa-e672-49cb-9aa6-2d85f1fcc1f3" />

# Iverilog
```
$ sudo apt-get update

$ sudo apt-get install iverilog
```
<img width="1053" height="1046" alt="Iverilog_complete_start_gtkwave" src="https://github.com/user-attachments/assets/24dbf974-4022-44c8-b9b0-257f55122338" />

# gtkwave
```
$ sudo apt-get update

$ sudo apt install gtkwave
```
<img width="1053" height="1046" alt="GTKwave" src="https://github.com/user-attachments/assets/6d2f0350-016f-4e31-81fc-22a3d14b4be4" />



