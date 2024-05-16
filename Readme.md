# C910 with Linux on FPGA

## Introduction

>  This project is from CAG lab of Xi'an JiaoTong University(XJTU). 

We terms at ctreate CPU evaluation platform on FPAG, and the CPU we choose should satisfied these key features below.

* The CPU should adapts to high performance  computing scenarios
* The CPU should be open-source which can be customized according to our demands
* The CPU should be compatible with performance statistics tools like Perf

According to these key features, We choose Xuantie-C910 as the basic design. 



## Prepare

### Software

* vivado 2022.2

* debugserver

### Hardware

* FACE-VUP-13B develop board (FPGA: VU13P)

  The board where we implement the CPU.

* Alinx FMC sub board (FL1010) if needed

  Connect this sub board to VU13P FPGA to expand more GPIO.

* CKLink board

  Connect this board to FPGA to download data to DDR.

* UART-USB board

  In order to realize the interaction between PC and CPU, this board should be connected to the FPGA. 

  

## Quick Start

1. Connect FPGA to PC

2. Write Bitstream to FPGA

   >Related files:
   >
   >​	./Start_C910/c910_wrapper.bit 
   >​	./Start_C910/c910_wrapper.ltx

3. Open DebugServerConsole to connect to the CPU

   > Related files:
   >
   > ​	./Start_C910/gdbinit.ice_fpga.txt

4. Use GDB tools to write data to DDR

5. Start the OS



## Steps

### Key Commands

* Create a docker env :

  ````
  docker run -i -t --name xuantie -v /d/Work/002_RISC-V/docker_share:/share -v /dev/bus/usb:/dev/bus/usb 5c16158533e34e9f7f632842a4a9bd66ea63e57476f3949cd6c7dbeaaacfb0cb /bin/bash
  ````

  the **usb sharing** is optional if needed，and Install USBIPD-WIN first.

* Start a docker env :

  ```
  docker start xuantie
  docker exec -it xuantie /bin/bash
  ```

* Update dts

  under docker env

  ```
  ./run.sh 192.168.1.1:1025 ice_fpga 1
  ```

* Start **DebugServerConsole**:

  under dir "**Install-Path**/bin"

  ```
  ./DebugServerConsole.exe -port 1024
  ```

* Restore bin file to DDR

  ```
  riscv64-unknown-elf-gdb -ex "tar remote 172.28.208.1:1024" -x gdbinit.ice_fpga.txt
  ```

* Modify **rootfs**:

  ```
  gzip -d rootfs.cpio.gz
  mkdir rootfs_workspace; cd rootfs_workspace
  cp -r ../rootfs.cpio ./
  cpio -i < rootfs.cpio
  rm -f rootfs.cpio
  ```

  After modifying the files

  ``` 
  find ./* |cpio -H newc -o > ../rootfs_debug.cpio
  cd ../ ; gzip rootfs_debug.cpio > rootfs_debug.cpio.gz
  ```

* Perf Command:

  ```
  perf stat -e L1-dcache-load-misses -e L1-dcache-loads -e L1-dcache-store-misses -e L1-dcache-stores -e L1-icache-load-misses  -e L1-icache-loads -e LLC-load-misses -e LLC-loads -e LLC-store-misses -e LLC-stores -e dTLB-load-misses -e dTLB-loads -e dTLB-store-misses -e dTLB-stores -e iTLB-load-misses -e iTLB-loads ls
  ```

  

