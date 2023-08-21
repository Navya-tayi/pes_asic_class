# pes_asic_class
<details><summary>DAY_0</summary>
<details><summary>Installing the riscv64_toolchain:</summary>
Let's understand the commands

```
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
  as: unrecognized option '--64' 
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
Apps run on the laptop hardware. How does this happen?
Application software enters into the system software which converts the application program into binary language. 
- Flow: APPLICATION SOFTWARE OR APPS -> SYSTEM SOFTWARE -> HARDWARE
OS -> Handles i/o operations, allocates memory, Low level system functions, It takes an app and converts it into its respective assembly language program and then to binary language to be understood by the hardware.
COMPILER -> Programming Languages like C, C++, JAVA Etc is taken by the respective compiler and converted into instructions. The syntax/format of these instructions depends on the hardware used like MIPS or RISC-V or x86. They are an abstract interface (called the ISA) between the application language and the hardware.
ASSEMBLER -> Takes these instructions and converts to its binary numbers. I.e., into machine language program, language that the machine understands. These binary numbers are fed into the hardware.
Since hardware understands only 1’s and 0’s we need Hardware description language, which is the binary interpretation from the assembler.
After getting the Hardware description language, it is synthesized into the gate level called the RTL, this gate level is synthesized into the hardware layout.
</details>
<details>
<summary>Lab Work</summary>
1. Program to compute sum of n numbers:

![sum1ton](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/27258b2f-78cc-4dab-a3e7-927a37763f8a.png)

cat output:

![cat_output_sum1ton](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b48d7202-bd91-4924-8c4e-d6cfa84a9a80.png)

generating file for riscv compiler:
![geenrating_file_for_riscv_compiler](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/f0cf8503-df08-4898-ba2b-3731c2d2bc22.png)

to run the file and get output:

![spike](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/9f8bfe84-fc09-405b-85e9-8788b1c7ad24.png)

output:
![gccsum1ton_output](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/b9af60b2-c7ae-4ccb-8d4c-510620ce094e.png)


![gcc_sum_is100](https://github.com/Navya-tayi/pes_asic_class/assets/79205242/8bf80fea-2af3-48af-aac7-1b763dbe4e13.png)

</details>


</details>



## DAY_2
