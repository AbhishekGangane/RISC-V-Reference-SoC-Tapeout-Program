# BabySoC Fundamentals & Functional Modelling (LAB)

## Step by step modeling walkthrough
To implement the functional modeling using BabySoC Follow the following steps: as reference https://github.com/manili/VSDBabySoC.git

In this section we will walk through the whole process of modeling the VSDBabySoC in details. We will increase/decrease the digital output value and feed it to the DAC model so we can watch the changes on the SoC output.

  1. First we need to install some important packages:

  ```
  $ sudo apt install make python python3 python3-pip git iverilog gtkwave docker.io
  $ sudo chmod 666 /var/run/docker.sock
  $ cd ~
  $ pip3 install pyyaml click sandpiper-saas
  ```
  above commands throw errors for my pc, so write following flow
  ```
  $ sudo apt install python3-venv
  $ python3 -m venv ~/py_envs
  $ source ~/py_envs/bin/activate
  $ python3 -m pip install pyyaml click sandpiper-saas

  ```

  2. Now we can clone this repository in an arbitrary directory (we'll choose home directory here):

  ```
  $ cd ~
  $ git clone https://github.com/manili/VSDBabySoC.git
  ```

  3. It's time to make the `pre_synth_sim.vcd`:

  ```
  $ cd VSDBabySoC
  $ make pre_synth_sim
  ```
  
  The result of the simulation (i.e. `pre_synth_sim.vcd`) will be stored in the `output/pre_synth_sim` directory.

  4. We can see the waveforms by following command:

  ```
  $ gtkwave output/pre_synth_sim/pre_synth_sim.vcd
  ```
  
  Two most important signals are `CLK` and `OUT`. The `CLK` signal is provided by the PLL and the `OUT` is the output of the DAC model. Here is the final result of the modeling process:
  <img width="1638" height="1042" alt="pre_synth_sim_1" src="https://github.com/user-attachments/assets/47da60c6-f512-4e0e-a618-7cbc03203ae1" />
  <img width="1456" height="770" alt="pre_synth_sim_2" src="https://github.com/user-attachments/assets/5940d883-1013-47c5-8f12-2964e5673ef3" />


In this picture we can see the following signals:

  * **CLK:** This is the `input CLK` signal of the `RVMYTH` core. This signal comes from the PLL, originally.
  * **reset:** This is the `input reset` signal of the `RVMYTH` core. This signal comes from an external source, originally.
  * **OUT:** This is the `output OUT` signal of the `VSDBabySoC` module. This signal comes from the DAC (due to simulation restrictions it behaves like a digital signal which is incorrect), originally.
  * **RV_TO_DAC[9:0]:** This is the 10-bit `output [9:0] OUT` port of the `RVMYTH` core. This port comes from the RVMYTH register #17, originally.
  * **OUT:** This is a `real` datatype wire which can simulate analog values. It is the `output wire real OUT` signal of the `DAC` module. This signal comes from the DAC, originally.

**PLEASE NOTE** that the sythesis process does not support `real` variables, so we must use the simple `wire` datatype for the `\vsdbabysoc.OUT` instead. The `iverilog` simulator always behaves `wire` as a digital signal. As a result we can not see the analog output via `\vsdbabysoc.OUT` port and we need to use `\dac.OUT` (which is a `real` datatype) instead.

**Simulation Logs**
```
abhishek@Abhishek:~$ sudo apt install python3-venv
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following package was automatically installed and is no longer required:
  libllvm19
Use 'sudo apt autoremove' to remove it.
The following additional packages will be installed:
  python3-pip-whl python3-setuptools-whl python3.12-venv
The following NEW packages will be installed:
  python3-pip-whl python3-setuptools-whl python3-venv python3.12-venv
0 upgraded, 4 newly installed, 0 to remove and 0 not upgraded.
Need to get 2,430 kB of archives.
After this operation, 2,783 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 python3-pip-whl all 24.0+dfsg-1ubuntu1.3 [1,707 kB]
Get:2 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 python3-setuptools-whl all 68.1.2-2ubuntu1.2 [716 kB]
Get:3 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 python3.12-venv amd64 3.12.3-1ubuntu0.8 [5,678 B]
Get:4 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 python3-venv amd64 3.12.3-0ubuntu2 [1,034 B]
Fetched 2,430 kB in 3s (899 kB/s)
Selecting previously unselected package python3-pip-whl.
(Reading database ... 223060 files and directories currently installed.)
Preparing to unpack .../python3-pip-whl_24.0+dfsg-1ubuntu1.3_all.deb ...
Unpacking python3-pip-whl (24.0+dfsg-1ubuntu1.3) ...
Selecting previously unselected package python3-setuptools-whl.
Preparing to unpack .../python3-setuptools-whl_68.1.2-2ubuntu1.2_all.deb ...
Unpacking python3-setuptools-whl (68.1.2-2ubuntu1.2) ...
Selecting previously unselected package python3.12-venv.
Preparing to unpack .../python3.12-venv_3.12.3-1ubuntu0.8_amd64.deb ...
Unpacking python3.12-venv (3.12.3-1ubuntu0.8) ...
Selecting previously unselected package python3-venv.
Preparing to unpack .../python3-venv_3.12.3-0ubuntu2_amd64.deb ...
Unpacking python3-venv (3.12.3-0ubuntu2) ...
Setting up python3-setuptools-whl (68.1.2-2ubuntu1.2) ...
Setting up python3-pip-whl (24.0+dfsg-1ubuntu1.3) ...
Setting up python3.12-venv (3.12.3-1ubuntu0.8) ...
Setting up python3-venv (3.12.3-0ubuntu2) ...
abhishek@Abhishek:~$ python3 -m venv ~/py_envs
abhishek@Abhishek:~$ source ~/py_envs/bin/activate
(py_envs) abhishek@Abhishek:~$ python3 -m pip install pyyaml click sandpiper-saas
Collecting pyyaml
  Downloading pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl.metadata (2.4 kB)
Collecting click
  Downloading click-8.3.0-py3-none-any.whl.metadata (2.6 kB)
Collecting sandpiper-saas
  Downloading sandpiper_saas-1.1.0-py3-none-any.whl.metadata (1.7 kB)
Collecting Path (from sandpiper-saas)
  Downloading path-17.1.1-py3-none-any.whl.metadata (6.5 kB)
Collecting argparse (from sandpiper-saas)
  Downloading argparse-1.4.0-py2.py3-none-any.whl.metadata (2.8 kB)
Collecting requests (from sandpiper-saas)
  Downloading requests-2.32.5-py3-none-any.whl.metadata (4.9 kB)
Collecting charset_normalizer<4,>=2 (from requests->sandpiper-saas)
  Downloading charset_normalizer-3.4.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl.metadata (36 kB)
Collecting idna<4,>=2.5 (from requests->sandpiper-saas)
  Downloading idna-3.10-py3-none-any.whl.metadata (10 kB)
Collecting urllib3<3,>=1.21.1 (from requests->sandpiper-saas)
  Downloading urllib3-2.5.0-py3-none-any.whl.metadata (6.5 kB)
Collecting certifi>=2017.4.17 (from requests->sandpiper-saas)
  Downloading certifi-2025.8.3-py3-none-any.whl.metadata (2.4 kB)
Downloading pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl (807 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 807.9/807.9 kB 1.6 MB/s eta 0:00:00
Downloading click-8.3.0-py3-none-any.whl (107 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 107.3/107.3 kB 916.6 kB/s eta 0:00:00
Downloading sandpiper_saas-1.1.0-py3-none-any.whl (5.3 kB)
Downloading argparse-1.4.0-py2.py3-none-any.whl (23 kB)
Downloading path-17.1.1-py3-none-any.whl (23 kB)
Downloading requests-2.32.5-py3-none-any.whl (64 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 64.7/64.7 kB 504.9 kB/s eta 0:00:00
Downloading certifi-2025.8.3-py3-none-any.whl (161 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 161.2/161.2 kB 1.0 MB/s eta 0:00:00
Downloading charset_normalizer-3.4.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl (151 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 151.8/151.8 kB 929.0 kB/s eta 0:00:00
Downloading idna-3.10-py3-none-any.whl (70 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 70.4/70.4 kB 516.3 kB/s eta 0:00:00
Downloading urllib3-2.5.0-py3-none-any.whl (129 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 129.8/129.8 kB 772.4 kB/s eta 0:00:00
Installing collected packages: argparse, urllib3, pyyaml, Path, idna, click, charset_normalizer, certifi, requests, sandpiper-saas
Successfully installed Path-17.1.1 argparse-1.4.0 certifi-2025.8.3 charset_normalizer-3.4.3 click-8.3.0 idna-3.10 pyyaml-6.0.3 requests-2.32.5 sandpiper-saas-1.1.0 urllib3-2.5.0
(py_envs) abhishek@Abhishek:~$ cd ls
bash: cd: ls: No such file or directory
(py_envs) abhishek@Abhishek:~$ cd ~
(py_envs) abhishek@Abhishek:~$ cd ~
(py_envs) abhishek@Abhishek:~$ cd VSDBabySoC
(py_envs) abhishek@Abhishek:~/VSDBabySoC$ make pre_synth_sim
sandpiper-saas -i src/module/rvmyth.tlv -o rvmyth.v \
	--bestsv --noline -p verilog --outdir output/compiled_tlv
Please review our Terms of Service: https://makerchip.com/terms/.
Have you read and do you accept these Terms of Service? [y/N]: y
You have agreed to our Terms of Service here: https://makerchip.com/terms.
INFORM(0) (PROD_INFO):
	SandPiper(TM) 1.14-2022/10/10-beta-Pro from Redwood EDA, LLC
	(DEV) Run as: "java -jar sandpiper.jar --bestsv --noline -p verilog --outdir=out --nopath -i ./rvmyth.m4out.tlv -o rvmyth.v
	For help, including product info, run with -h.

INFORM(0) (LICENSE):
	Licensed to "Redwood EDA, LLC" as: Full Edition.

INFORM(0) (FILES):
	Reading "./rvmyth.m4out.tlv"
	to produce:
		Translated HDL File: "out/rvmyth.v"
		Generated HDL File: "out/rvmyth_gen.v"

if [ ! -f "output/pre_synth_sim/pre_synth_sim.vcd" ]; then \
	mkdir -p output/pre_synth_sim; \
	iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM \
		src/module/testbench.v \
		-I src/include -I src/module -I output/compiled_tlv; \
	cd output/pre_synth_sim; ./pre_synth_sim.out; \
fi
VCD info: dumpfile pre_synth_sim.vcd opened for output.
src/module/testbench.v:63: $finish called at 84999000 (1ps)

```
