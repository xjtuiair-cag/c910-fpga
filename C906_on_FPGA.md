# C906 on FPGA

## Prepare



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

  



