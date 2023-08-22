# pes_asic_class
<details><summary>DAY_0</summary>
<details><summary>Installing the riscv64_toolchain:</summary>
  
* Let's understand the commands
---
```ruby
_Install Git and Vim packages automatically (without manual confirmation)_ 
sudo apt-get install git vim -y

_Install various development tools and libraries automatically_
sudo apt-get install autoconf automake autotools-dev curl libmpc-dev \
libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo \
gperf libtool patchutils bc zlib1g-dev git libexpat1-dev gtkwave -y

_Change to the home directory and store its path in the 'pwd' variable_
cd
pwd=$PWD

_Create a directory named 'riscv_toolchain' and change to it_
mkdir riscv_toolchain
cd riscv_toolchain

_Download the RISC-V GCC toolchain tarball_
wget "https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz"

_Extract the RISC-V GCC toolchain tarball_
tar -xvzf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz

_Update the 'PATH' environment variable to include the RISC-V toolchain binaries_
export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH

_Install the device tree compiler_
sudo apt-get install device-tree-compiler -y

_Clone the RISC-V ISA simulator repository_
git clone https://github.com/riscv/riscv-isa-sim.git
cd riscv-isa-sim/

_Create a 'build' directory and change to it_
mkdir build
cd build

_Configure the build for the RISC-V ISA simulator_
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14

_Compile the RISC-V ISA simulator_
make

_Install the RISC-V ISA simulator_
sudo make install

_Change back to the 'riscv_toolchain' directory_
cd $pwd/riscv_toolchain

_Clone the RISC-V Proxy Kernel repository_
git clone https://github.com/riscv/riscv-pk.git
cd riscv-pk/

_Create a 'build' directory and change to it_
mkdir build
cd build

_Configure the build for the RISC-V Proxy Kernel_
../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf

_Compile the RISC-V Proxy Kernel_
make

_Install the RISC-V Proxy Kernel_
sudo make install

_Update the 'PATH' environment variable again to include the RISC-V Proxy Kernel binaries_
export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH

_Change back to the 'riscv_toolchain' directory_
cd $pwd/riscv_toolchain

_Clone the Icarus Verilog repository_
git clone https://github.com/steveicarus/iverilog.git
cd iverilog/

_Switch to the v10-branch of Icarus Verilog_
git checkout --track -b v10-branch origin/v10-branch
git pull

_Change permissions and run autoconf.sh_
chmod 777 autoconf.sh
./autoconf.sh

_Configure the Icarus Verilog build_
./configure

_Compile Icarus Verilog_
make

_Install Icarus Verilog_
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
