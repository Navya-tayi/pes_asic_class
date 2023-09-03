# pes_asic_class
## LAB - WEEK 1
<details><summary>DAY_0</summary>
<details><summary>Installing the riscv64_toolchain:</summary>

* Let's understand the commands :
---
```ruby
# Install Git and Vim packages automatically (without manual confirmation)
sudo apt-get install git vim -y

# Install various development tools and libraries automatically
sudo apt-get install autoconf automake autotools-dev curl libmpc-dev \
libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo \
gperf libtool patchutils bc zlib1g-dev git libexpat1-dev gtkwave -y

# Change to the home directory and store its path in the 'pwd' variable
cd
pwd=$PWD

# Create a directory named 'riscv_toolchain' and change to it
mkdir riscv_toolchain
cd riscv_toolchain

# Download the RISC-V GCC toolchain tarball
wget "https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz"

# Extract the RISC-V GCC toolchain tarball
tar -xvzf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz

# Update the 'PATH' environment variable to include the RISC-V toolchain binaries
export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH

# Install the device tree compiler
sudo apt-get install device-tree-compiler -y

# Clone the RISC-V ISA simulator repository
git clone https://github.com/riscv/riscv-isa-sim.git
cd riscv-isa-sim/

# Create a 'build' directory and change to it
mkdir build
cd build

# Configure the build for the RISC-V ISA simulator
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14

# Compile the RISC-V ISA simulator
make

# Install the RISC-V ISA simulator
sudo make install

# Change back to the 'riscv_toolchain' directory
cd $pwd/riscv_toolchain

# Clone the RISC-V Proxy Kernel repository
git clone https://github.com/riscv/riscv-pk.git
cd riscv-pk/

# Create a 'build' directory and change to it
mkdir build
cd build

# Configure the build for the RISC-V Proxy Kernel
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf

# Compile the RISC-V Proxy Kernel
make

# Install the RISC-V Proxy Kernel
sudo make install

# Update the 'PATH' environment variable again to include the RISC-V Proxy Kernel binaries
export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH

# Change back to the 'riscv_toolchain' directory
cd $pwd/riscv_toolchain

# Clone the Icarus Verilog repository
git clone https://github.com/steveicarus/iverilog.git
cd iverilog/

# Switch to the v10-branch of Icarus Verilog
git checkout --track -b v10-branch origin/v10-branch
git pull

# Change permissions and run autoconf.sh
chmod 777 autoconf.sh
./autoconf.sh

# Configure the Icarus Verilog build
./configure

# Compile Icarus Verilog
make

# Install Icarus Verilog
sudo make install
```
</details>
  <details><summary>Error faced & how I resolved it</summary>
    
  ```
  as: unrecognized option '--64' 
 ``` 
  After you add export PATH to bashrc, and save it,
  you may not be able to run gcc even though riscv64-unknown-elf-gcc is working.
  This can happen when you append the riscv toolchain path before the gcc path.
  Example:**that may give error**

  ```
  export PATH=~/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH
  export PATH=~/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH
  ```
  Instead, to avoid the error, the **correct way** to add to the bashrc file is:
  ```
  export PATH=$PATH:~/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin
  export PATH=$PATH:~/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin
  ```
  notice how the global path ie, the path to bin is at the beginning instead of at the end and the
  riscv64 gcc path is appended to it. So the path first go through the gcc compiler and then the riscv compiler
  and this will help avoid the above error
  
</details>
</details>

<details><summary>DAY_1</summary>
<details><summary>Brief Introduction</summary>
  
  ISA is the language of the computer. It is the way we are going to talk to the computers.
  If you have a C program and it has to be run on a hardware that contains a certain layout, 
  then the information needs to be passed to the hardware in certain terms. 
  It is first compiled in the assembly language, in this case the RISC-V assembly language. 

  This is then converted to machine language which is 1’s and 0’s i.e., logic 0 and logic 1, which is understood by hardware of the computer. 
  These bits are then executed in the layout and then the required output is obtained.
  Another interface that is required is the hardware description language. 
  You need to implement the code’s particular RISC-V specifications using some RTL. 
  Example: picorv32 CPU core. This RTL implements the RISC-V architecture specifications.
  And the it goes from RTL to the layout.
</details>
<details><summary>From Apps to Hardware</summary>
* Apps run on the laptop hardware. How does this happen?
  Application software enters into the system software which converts the application program into binary language. 
  
- Flow: APPLICATION SOFTWARE OR APPS -> SYSTEM SOFTWARE -> HARDWARE
  
- OS -> Handles i/o operations, allocates memory, Low level system functions, It takes an app and converts it into its respective assembly language      program and then to binary language to be understood by the hardware.
  
- COMPILER -> Programming Languages like C, C++, JAVA Etc is taken by the respective compiler and converted into instructions. The syntax/format of      these instructions depends on the hardware used like MIPS or RISC-V or x86. They are an abstract interface (called the ISA) between the application    language and the hardware.

- ASSEMBLER -> Takes these instructions and converts to its binary numbers. I.e., into machine language program, language that the machine       understands. These binary numbers are fed into the hardware.
  Since hardware understands only 1’s and 0’s we need Hardware description language, which is the binary interpretation from the assembler.
  After getting the Hardware description language, it is synthesized into the gate level called the RTL, this gate level is synthesized into the       hardware layout.
</details>

<details>
<summary>Lab Work</summary>
<details><summary>C Program to compute sum from 1 to N</summary>

![sum1ton](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/27258b2f-78cc-4dab-a3e7-927a37763f8a.png)

output:

![gccsum1ton_output](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b9af60b2-c7ae-4ccb-8d4c-510620ce094e.png)


![gcc_sum_is100](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/8bf80fea-2af3-48af-aac7-1b763dbe4e13.png)
</details>

<details><summary>RISC-V gcc compile and disassemble</summary>
Cat output:

![cat_output_sum1ton](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b48d7202-bd91-4924-8c4e-d6cfa84a9a80.png)

Generating file for riscv compiler:

![geenrating_file_for_riscv_compiler](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/f0cf8503-df08-4898-ba2b-3731c2d2bc22.png)

* riscv64-unknown-elf-gcc: This is the command for the RISC-V GCC compiler, which is used to compile C code for RISC-V architectures.

* -O1: This is an optimization level flag. -O1 specifies the first level of optimization. Higher optimization levels (e.g., -O2, -O3) apply more aggressive optimizations but might also increase compilation time.

* -mabi=lp64: This flag sets the ABI (Application Binary Interface) to use the LP64 data model, which means int and pointers are 32 bits, and long and long long are 64 bits.

* -march=rv64i: This flag specifies the target RISC-V architecture and ISA (Instruction Set Architecture). rv64i indicates a 64-bit integer base instruction set architecture.

* -o sum1ton.o: This flag specifies the output file name. In this case, the compiled output will be named sum1ton.o.

* sum1ton.c: This is the source code file that is being compiled.

Disassemble:
Will give the assembly language code

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/7ced1426-69fa-4d4a-b670-7799a6e16cfc.png)

Output: 
* Main section-
Address of main section is 10184. And there are 15 instructions

![main](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/eb4d5927-d460-48fb-bff2-8779b2091388.png)

* To find address of next instruction:

![counting](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1b2a4ad8-3119-4345-8eee-9dbb49fd5ec5.png)

* To find number of instructions: 15

![calc](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/9e78fbb1-b06c-4504-b0fe-9494c46a4356.png)

Compile with Ofast:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/0b4aa67e-50d2-4746-9614-9814f034ac87.png)

- Ofast is a flag specifies an aggressive optimization level, often referred to as "fastest optimization." It enables all `-O3` optimizations and additionally includes optimizations that might sacrifice precision for speed. This can lead to faster code but might not be suitable for all applications.

Output:
* Main section-

![main2](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/bd19b9a8-9fea-4486-bc5c-e341204be741.png)

* To find number of instructions: 12

![12eqns](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a07b1788-36cc-42e2-a9d2-40d4bba05778.png)
</details>


<details><summary>Spike simulation and debug</summary>

* To run the file and get output: Spike Simulation

![spike](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/9f8bfe84-fc09-405b-85e9-8788b1c7ad24.png)

* Debug in Spike:
  
![spike1](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a6b36232-a2c9-4872-849f-0ab544cd5b23.png)

Observing stack pointer:

![apike2](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/4685fc1d-cd3f-4efd-a5c2-e319fc3fc230.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/8d25ec88-52cc-4d4e-9d29-94fa395227e8.png)

</details>

<details><summary>Signed and Unsigned Numbers 64-bit</summary>

* To find highest unsigned:

Code-
  
![2pow10](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1ac90b45-000e-475f-be39-d4a66556832b.png)

Output-

![2pow10_op](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1883e0dd-14ad-4f1f-8235-b7a16277bbad.png)

Code-

![unsignedhighestcode1](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/adc101d6-d40d-4955-9bc4-78c41e214305.png)

Output -

![op1 hn](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/0710c043-4b5e-4a31-867a-8363086f7e08.png)

Code -

![2pow127](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/5c1adf46-ead3-4ac7-86e4-f8b5970dac6e.png)

Output -

![code op2](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/c95f02bb-09d3-4fa3-8902-89e523322a30.png)

In all cases we get the highest unsigned number as the same.

* If you try to find signed with unsigned data type:

code-

![with neg](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/196bdbea-1f99-44c7-9c1e-6ba440814b97.png)

output -

![wiht_neg_output](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/8bc437f5-5bb9-457d-8f0c-ce325403603d.png)

* To find signed number:
  Code -
  
![signed](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/535231e3-1764-47dc-b5dc-d759dd501a21.png)

Output-

![signed_op](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/dea59e07-66b0-460d-a0af-c00aa98e345e.png)

* To find highest and lowest signed 64 - bit:
  Code with BUGS -
  
![signedhighest](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/3a08c3b2-a59b-4ae0-9237-6bbc98b4756b.png)

  Output for code with bugs -

![signedhih_op](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d7078f2b-5067-47b0-ba13-6a2896c4cb32.png)

  DEBUGGED Code -
  
![debugged code](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d89581f2-154a-4e79-ab9f-a2291b5e4ce1.png)

  Output -
  
![debugged_op](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/7233ab3b-4ce5-49a3-86f6-0fb43bfef615.png)

</details>

</details>

</details>



<details><summary>DAY_2</summary>
<details><summary>Introduction</summary>
Application Binary Interface (ABI):
The RISC-V ABI defines the conventions and rules that govern how compiled software components interact with each other at the binary level. It establishes a standardized interface for functions, data, and system calls, ensuring compatibility between different software components.

The RISC-V ABI covers various aspects of binary compatibility, including:

* Calling Conventions: Specifies how function calls are made, how arguments are passed to functions, how return values are handled, and which registers are used for parameter passing.

* Register Usage: Defines which registers are reserved for specific purposes, such as function arguments, return values, and temporary storage.

* Stack Layout: Specifies how the call stack is managed, including how local variables and function call frames are organized in memory.

* Data Representation: Defines how different data types are represented in memory, including integer and floating-point types.

* Exception Handling: Specifies how exceptions, interrupts, and system calls are handled, including the interaction between user-level software and the operating system.

* Memory Layout: Defines how memory is organized, including the layout of code, data, and stack segments.

The RISC-V ABI provides a standardized framework that allows software components, such as compiled programs, libraries, and the operating system, to work together seamlessly. It ensures that software produced by different compilers and tools can interoperate correctly, even if they are developed independently.

Different ABIs are available for RISC-V, each tailored to specific use cases and environments, such as 32-bit or 64-bit systems. Choosing the appropriate ABI is crucial to ensure proper compatibility and efficient execution of software on RISC-V platforms.
</details>
<details><summary>Lab Work</summary>

* Algorithm to re-write C program using ASM Language:

  ![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/9d9c8f72-5490-4e81-8235-60496f81d698.png)

* Code to pass variables through assembly language function:
  
  ![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b9ad3a19-1ef8-4364-a9a9-99cd0660f8f3.png)

* Assembly language function:
  
  ![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/15ff65b1-bd0e-49f0-b765-bc07b30bfed9.png)

* Simulate the above programs:
  
  output -
  
  ![op1](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/f58815a3-a350-45a8-b40a-c867cb21dbc2.png)

* disassembling the code:
  
  ![disassembly](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/cb39430f-5521-4cbe-a200-beabb9cdf637.png)

* Basic Verification flow using iverilog:

  ![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/5fc3ab46-72b1-437c-9cb5-b0372d64aecf.png)

* Set of scrips needed to convert the code into hex file and load it into the memory and then run it on the RISC-V CPU:

```vim rv32im.sh```

  ![rv32](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/3befbcb3-2c08-42b4-bef7-107e77981661.png)

At the end of these scripts we get hex files and then we use a tool called iverilog
which dumps a .vvp file which is the output of the iverilog simulation process. It is the compiled binary that represents the simulation model of the verilog design. 
It can be executed by the Icarus Verilog simulator to simulate the behavior of the digital circuit.

* To run this :

```
chmod 777 rv32im.sh // change permissions
./rv32im.sh   // to run
``` 
output:

![rv32imop](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/9107cb47-1cff-4305-9d80-a6ddddd0c4d5.png)

![op2](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/0093eda8-74b0-445d-a8b9-53e44478c921.png)

This script creates a hex file


* The hex file: The application pattern is converted to a binary pattern
  
```
vim firmware.hex
```

  ![hex](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a280c32b-074a-4322-b6a1-bc80efc2ae7a.png)

* Bit stream file:
  
```
vim firmware32.hex
```

  ![bit stremfile](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/db26ecae-010b-4e38-99fa-1203e124b982.png)

The hex file is loaded into the memory and is being used by (processed by) the picorv32 core:

  ![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/ed478fa7-4521-4c2a-9240-7a59c1483e97.png)

</details>
</details>
</details>

## LAB WEEK -2
<details><summary>Day 1 : Introduction to Verilog RTL design and synthesis</summary>
<details><summary>SKY130RTL DISK1- Introduction to open-source simulator iverilog</summary>
  
* Simulator:
A simulator is a tool for checking the design. RTL design is the implementation of a spec. The intent of a spec is checked by simulating the design. The tool used for this simulation is the simulator.

* Design:
The actual Verilog code or a set of Verilog codes which has the intended functionality to meet with the required specifications.
Testbench is the setup to apply stimulus/test_vectors (to check if design is obeying specifications) to the design to check its functionality.

* How a simulator works:
Simulator looks for changes on the input signals.
Upon every change in the input, the output is evaluated. If there is no change in the input, the simulator is not going to evaluate the output.

Design-> say it has primary inputs, it has one or more primary outputs.
-> Need to generate stimulus at all primary inputs and the stimulus must be observed at all the outputs

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/10c8b73d-490c-4154-9025-f83a6c066d6b.png)

Testbench does not have a primary input or primary output only design has primary input and primary output:
A test bench is a simulation environment used to test digital circuit designs. It doesn't have primary inputs and outputs like the actual design. Instead, it connects to the primary inputs and outputs of the design under test (DUT) and provides stimulus while observing behavior to verify the DUT's functionality. The test bench's purpose is to facilitate testing, not to be a part of the final hardware.

iverilog simulator:
Simulator is going to look for changes in i/p and is going to dump the changes in the output.
The output of a simulator is a vcd file. (vcd stands for value change dump)
Because we are looking for the changes in the values.

We want to view this vcd file. We will use another tool called gtkwave to view the waveform outputs.
![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/45f8db70-a90e-43eb-ba7d-f7a282eb20d3.png)



</details>



</details>

<details><summary>Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles</summary>
  
<details><summary>SKY130RTL D2SK1 - Introduction to timing .libs</summary>
  
## SKY130RTL D2SK1 L1 Lab4 Introduction to dot Lib part1:
What exactly .lib looks like?
It looks like this:
  
![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a7f89ba9-1759-466c-afcc-1a24f49277da.png)
  
To remove the unpleasant color,press shift + colon and type "syn off"

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/db16021c-4693-44f9-80f8-72607962b2a8.png)

DO NOT EDIT THE .lib FILE
first line : Name ofthe library sky130, 130 nm library.
tt - stands for typical. The libraries can be slow, fast or typical
025C - temperature
1v80 - indicates the voltage 1.8 V

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b068f5fc-02fa-484d-9aee-0fa7e91bb1f9)


When you look at a library you should think of P, V, T - process, Voltage, Temperature

There will be variation in process due to variations in fabrication.
Variation in voltage means there will be variations in the working.
We should have the silicon work even when P V and T vary.

We should factor in these variations when designing the circuit.

Our libraries are charecterised to model these variations

## SKY130RTL D2SK1 L2 Lab4 Introduction to dot Lib part2
In the next few lines we can observe:

* Technology : CMOS
* Delay model : Lookup Table
* Units of time, voltage, power, current, resistance and capacitance
* Operating conditions, voltage, process and temperature

There are going to be a lot of cells in our library
"cell" is a keyword in .lib that marks the beginning of a cell defenition
For all input combinations,it is going to have the info

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1bff2d15-62a1-47cf-83a5-7f60542dee36.png)

2 input AND gate and remaining all are OR gate
it is going to add a1 and a2 and or it with b1,c1 and d1

The power value when A1,A2, B1 and C1 are low and D1 is high we can see in "value"
Like this since there are 5 gates, e=s power 5 ie, 32 combinations we can see

To understand the  fucntionality of the cell, we can look at the equivalent verilog model


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d977d06c-e25f-4731-a322-143f2a3e277c.png)

pp stands for power ports. 

Let us check without the power port information

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/04e3b986-edaf-45f7-991f-0f76b5c4acde.png)

behavioral.v file opens:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/5c27767e-67fd-45ca-b485-cfb935de0006.png)


* You can observe the area number and the power port information;

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/4f1af340-9f91-4ba4-9475-b97510a5725f.png)


* Describes each input pin, power, transition, delay associated with each pin:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/4cd20bf2-95f8-4a60-9d0c-9900f3c8a77e.png)

## SKY130RTL D2SK1 L3 Lab4 Introduction to dot Lib part3

* Behavioral model for AND gate:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d8f15863-9f15-441d-a7cd-e5cbc2bf34a3.png)

  
![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/da62849a-077d-45c3-af0d-02011dd6a14f.png)

* AND 2_0, 2_2 and 2_4 are different flavours of the AND cell, we can observe the increase in area as the transistor size gets wider:
We can also observe an increase in power 

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/21664594-2cf0-41cc-bb04-c99e1326987c.png)


</details>

<details><summary>Hierarchial vs Flat Synthesis</summary>

## SKY130RTL D2SK2 L1 Lab05 Hierarchial synthesis flat synthesis part1

File used in this lab:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/706c3e06-4d4c-42f9-ab18-89df1a070f2d.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/37609e80-5b03-4831-8c4a-20cc5c9b10e5.png)


It has 2 sub modules:
* sub module 1 is an AND gate
* sub module 2 is an OR gate
* multiple_modules is instantiating the above 2 modules as u1 and u2 respectively

diagram:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/10fe6848-1f5c-4afb-8022-ebfeefdb3a8e.png)

Understanding synthesis:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/7d7a6a79-dc48-4761-9969-182e9b208398.png)


Observe:

sub module 1 has one AND gate, sub module 2 has one OR gate:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/22071a75-91c9-4be1-9f77-a4814c7e20be.png)

Top module has one instance of sub module 1 and one instance of sub module 2:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/291de92d-98a4-4aa5-93fc-bdd04ff825d9.png)

Link the design to the library:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/7a969b92-22bb-4fa1-ac1b-aa9db5afd797.png)

When you click show, you will expect the above drawn diagram but you get this:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/25873810-842e-4284-97ee-2bb4fbb65efb.png)

It is not showing AND or OR gate it is showing as "u1" and "u2" -> This is called as a hierarchical design since the hierarchies are preserved.

Now, we will write out the netlists:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a5da729c-b826-460e-b3b6-2c63967b079e.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/fe84dd79-a37b-41fa-9597-2c4ec1890490.png)

We can see that the hierarchies are preserved:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/6e5a092e-e654-47da-836e-3201b36a54bb.png)


## SKY130RTL D2SK2 L2 Lab05 Hier synthesis flat synthesis part2:

flatten: Command to write out a flat netlist

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/57e73796-e630-4f23-a736-17bc7a0abfe7.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/c8e72d16-bb00-4e97-8442-3dcf9cd335ca.png)


After flatten, the hierarchy in the netlist is not preserved anymore

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/3e75f00c-6713-4caf-b240-f7e4501c368c.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/08cf97d3-ac91-446c-b397-5c30334095df.png)


We also see the underlying components within u1 and u2 unlike previously:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/ecc3ab66-a7d7-4e5d-bfb2-0d2d9345f8e9.png)



Given multiple modules, lets say you want to synthesize a sub module level: 

We are going to synthesize at sub module 1 although we have read the RTL at top module level:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/c27636a5-d714-42d3-a73a-d2ef0b0a285a.png)


We can see that it has inferred only one AND gate ie, sub_module1 :

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1082c219-65ec-4822-bc46-14515beb79b5.png)


And we can see only sub_module1:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/e37c62ed-575e-4a84-aeb1-ebe796c5c784.png)

We have created only sub module 1 , we have controlled the synthesized module using synth -top

But why do we need this module level synthesis:

Reason 1:
* Say, you have a top module and multiple instantiations of the same component, like example a multiplier, you have 6 instantiations called mul1, mul2, mul3, mul4, mul5, mul6.
* Now, instead of synthesizing the multiplier 6 times, you will synthesize it one time and replicate the netlist 6 times and stitch it together in the top module.
* So, module level synthesis is preferred when we have multiple instances of the same module

Reason 2: DIVIDE & CONQUER
* Say, your design is massive and when you give it to the tool, it is not doing a good job.
* So you give parts of it for synthesis and then stitch it together in the top module.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/e1ad8717-0496-485d-a8f8-5c26f0089e02.png)


  

</details>


<details><summary> Various Flop Coding Styles and Optimisation</summary>

## SKY130RTL D2SK3 L2 Why Flops and Flop coding styles part1

How to code a flop and what are the different coding styles possible?

For any digital design, for example a combinational circuit, when you give them an input, the output is going to change after a propagation delay. 
In the below diagram, when c goes low, it is immediately seen by the OR gate, initially internal node(io) is 0.
After 1 ns of C going low, Y will go low.

And if a,b are 1, it will take some delay for the AND gate's output to reach the internal node. SO for that time when the output of ans gate ha snot yet reached the (io), we will have a 0 at the output and then as soon as it reaches we get a 1. So the part where is was 0, is actually a "glitch".

(A&B)|C => boolean expression

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/2df564d4-2220-45ff-8d33-216bc3c7c240.png)

So what if a glich happens now?
In design, we will have combinational circuits everywhere. More the amount of combinational circuits, more the glitches. The outputs will never settle down. So we want an element to store the value. That element is called as a FLOP.
These flops are like storage elements. We are using flops to restrict the gliches. The output of a flop will change only on the edge of a clock. So even if the input is gliching, the output will be stable. 
Q is shielded from changes in D when there is no clock. 


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d71e31cd-60af-4eee-b0c7-a388268a4e33.png)

We need to initialize the flop with some value, else the combinational logic will take some garbage value.
To initialize the flop we have control pins on the flop like reset or set. An these can be either synchronous or asynchronous.

## SKY130RTL D2SK3 L2 Why Flops and Flop coding styles part2
Code for a D flip flop with asynchronous reset:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/e4883feb-3b69-47d9-9469-5596499f9ab4.png)

always @ posedge clk, signifies a posedge flop, we are also looking at posedge of asynchronous reset.
* The moment you enter the always block, we are first looking for an asynchronous reset
* The always block will get evaluated when there is a change in the clk or rst
* If the reset is on, the output will go to 0
* else, if you had entered the loop cuz of posedge clk, it was not cuz of async_reset and we will look at the else block and only at pos edge of clk, d is at output
* At neg edge, the always block is not evaluated. So this is pos edge sensitive
* It is asynchronous reste because the reset does not depend on the clock, it can be ON at anytime and when it is on, irrespective of clk, reset will happen.
* 
Code for a D flip flop with asynchronous set:
The output will be set to 1, irrespective of clk, when set is ON

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d55f78eb-bbb8-4b86-a3a5-f506b32104d0.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b5ddaa15-c889-4924-9eef-536a195b916c.png)


When something is synchronous, it will turn into D pin of the flop.
You have a MUX, if rst is high, it awaits the pos edge of clk , implemented with a MUX

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/cdf77819-e7f3-4e08-ace2-1c0745fbc2c0.png)

Even if sync_reset is toggled, it will not enter the always block, until posedge of the clock.
Upn posedge of clock, we look for prescence of synchronous reste, if it is there, Q goes to 0, else d.
![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/4e5b2ecc-233d-4825-b2c1-be7dbfb9adc3.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b28ee1f1-198e-482a-bb2a-6dcc04a9a84a.png)


In some situations you may have both synchronous and asynchronous reset.
Although asynchronous reset can happen anytime, you will consider synchronnous reset only if there was posedge of clk.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1fbf6386-9945-4b47-bb06-be0c957660f7.png)

diagram: 
![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/4841ec30-41c9-48ab-9dad-737ebcc5d6c3.png)

## SKY130RTL D2SK3 L3 Lab flop synthesis simulations part1

Asynchronous reset synthesis simulation:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/bc3105e8-f2bf-4647-a442-987520fcddff.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/e32afc71-f3e3-47c0-9cbf-3f27228dfa8f.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/315bf436-c3dc-49e6-a3d3-c571fe335518.png)


Asynchronous set synthesis simulation:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/61e48cae-00ff-4a65-9a74-316d0354028c.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/546fcf84-e9c8-4e0d-8e63-63fc27b62984.png)

Synchronous reset:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b3a10e7c-52f1-4c39-bbb7-1b5bc453ed05.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a1637dc3-4bdd-4301-a844-f7ec71f4e287.png)


## SKY130RTL D2SK3 L4 Lab flop synthesis simulations part2

Lets go to yosys and synthesize the above circuits:

1. Asynchronous reset:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/a9b1493b-ae7d-4c1b-b67d-10c3733304ac.png)

In the flow, the flop library and standard cell library are kept seperately so we need to tell the tool where to pick the d flipflop for the design from. In this case both the libraries are same , so we ask it to look in the same library.
It is looking for the d flipflops.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/3080f3bd-442b-48aa-83d7-26a53d27591b.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/6d3fd191-4567-459d-a2d9-3b77f807085a.png)


After that we do standard synthesis step.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/8a34b9f6-80b4-4c7c-bd9d-22ef9132accd.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d4ad877a-3678-473e-96e8-0ec40ac595e0.png)

the tool inserted an inverter because, we wrote a flop with active high reset but the tool uses active low reset, so it inserted an inverted.

2. Synchronous reset:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/11e6eb82-0fdf-4b73-a37b-64e5eecb53f3.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1c1ab02f-6ea6-4a1a-84a1-6bc9ae46e220.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b61f4890-e57a-4a38-bb42-59598ab9ee9e.png)

3. sync & async reset:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/cba024e2-a0d6-45e3-bac9-275a313b0b91.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/1878b46a-fcba-4be3-82f0-c6dfe5e4be80.png)

We wanted a flop with a synchronous reset, but what we got is reset is on an AND gate with inverted input. A_N is active low input.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/6f1a9970-605f-43a5-8d4f-4072d237648a.png)

## SKY130RTL D2SK3 L5 Interesting optimisations part1
The verilog code is accepting a 3 bit input a and is generating a 4 bit output y.
Observe the truth table:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/8a4e8eaa-5f75-4934-a2a7-b4f012b768f3.png)

The first 3 columns are same. so you can get the output by just appending 0 to a. We dont even need a multiplier, we just have to do shifting. 
Say you do 5 x 4, same as appending 2 zeroes

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/aaa904db-cfc3-4e95-b620-03fdffdb0664.png)

Number of cells are 0.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/d8e5e937-3663-41ef-9d44-14516ccba72d.png)

There is no standard cell required. so it shows the above.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/681b6d52-ad50-4ef9-8108-856015e22d7f.png)

## SKY130RTL D2SK3 L6 Interesting optimisations part2

Suppose you take a 2 bit number a, and a 6 bit number y

We have a relation that a multiplied by 9 is y.
(spl case where a is 3 bit number)
* This can be considered as a x [8 + 1] = a x 8 + a x 1
* a x 8 => append 3 zeros to a plus a x 1

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/c9f0b76d-800b-43e2-a105-270ddfb92eba.png)

hardware not required.

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/64a469ef-48e9-4dbb-ac9b-fd8ba0719c53.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/98cdb84e-f714-42f8-a532-1ec7a6223e6a.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b2dbbdc8-bb0c-4972-a902-db2a3f5608b1.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/08f884a6-fa95-4096-b828-524b7513c498.png)


mult8:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/332b3cce-aca6-461b-81b8-36633fbf9167.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/0954dcc9-23a8-4245-9993-8784bf390866.png)


no. of cells again 0:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/0a288440-f2ed-4acd-9cd6-8d5841c102aa.png)


![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/fcaf1bd8-f355-4e70-a207-a262fcce215a.png)

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/44a532eb-f5d1-4aff-84e3-542f99481221.png)

Netlist:

![image](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/9ba1ec35-3631-4a42-bd9e-8de8e0802547.png)

These are some custom optimisations, very special. Here hardware can just be obtained by rewiring the existing signals. so hardware not required.
</details>
</details>
