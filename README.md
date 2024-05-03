
# STM32F4-FreeRTOS

A demo project of FreeRTOS running on a STM32F4 Discovery board.

## Steps to run this example

### Prerequisite

1. A PC running Linux or Windows with Cygwin(not tested). A Mac is also fine with this example.
2. A STM32F4Discovery board.
3. A FT232RL USB to serial board which is recommended if there's no serial port on your computer.
4. USB Cable, Dupont Line and other tools.

This repository contains FreeRtos porting for STM32F4 board.

-->Refering these link
https://github.com/wangyeee/STM32F4-FreeRTOS

blue board-->stm32f411e (MB1115D)

green board-->stm32f411e (Mb1115B)

##Setting up the Work Bench 
===========================

Toolchain for arm processors
============================

Installing gcc-arm-none-eabi toolchain using, "sudo apt install gcc-arm-none-eabi" -->[ps:don't install it like this GDB debugger is not working] 

-This toolchain provides the GNU Compiler Collection (GCC) targeting the ARM architecture without any embedded operating system (bare-metal)
specifically for ARM Cortex-M and Cortex-R processors.

-This toolchain is widely used in embedded development for ARM microcontrollers, including STM32 devices.

-It produces executable binaries that are compatible with the ARM architecture used in these microcontrollers.

-ARM Cortex-M microcontrollers use a different architecture than typical desktop or server CPUs (e.g., x86). Therefore, you need a specialized toolchain like arm-none-eabi-gcc to generate code that runs on these microcontrollers.

-standard GCC compiler on our system doesn't recognize ARM-specific instructions or peripheral registers,so errors will be produced.

 Follow youtube video "https://www.youtube.com/watch?v=imUiQkO9YHM from 2:21 to 7:55".
 
 -Upgrade and update apt-get packages using "sudo apt-get update" and "sudo apt-get upgrade"
 
 -Install wget using "sudo apt-get wget"
 
 -Go to link "https://developer.arm.com/downloads/-/gnu-rm" and copy link of gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2[Linux
 x86_64 Tarball]
 
 -Run the command "wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/10.3-2021.10/gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2" [remove the part upto "?"] in Downloads folder(any folder would be fine).
 
 -use "tar -xjf gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2"
 
 -Now go to "/opt/" folder and create a folder by any name using "sudo mkdir gcc-arm"
 
 -Now move the extracted folder from downloads to /opt/gcc-arm/ folder using "sudo mv gcc-arm-none-eabi-10.3-2021.10/* /opt/gcc-arm"
 
 -Add the bin floder to PATH variable using "export PATH="/opt/gcc-arm/bin/:$PATH"
 
Even after this the GDB may not work, we may have to do the following process
 
vlab@HYVLAB1:/opt/gcc-arm$ dpkg -l | grep libncurses

ii  libncurses6:amd64                          6.2-0ubuntu2.1                      amd64        shared libraries for terminal handling

ii  libncursesw6:amd64                         6.2-0ubuntu2.1                      amd64        shared libraries for terminal handling (wide character support)

-Eventhough libncurses is there but not exact version so we need to give a symbolic link.

vlab@HYVLAB1:/opt/gcc-arm$ sudo ln -s /lib/x86_64-linux-gnu/libncurses.so.6 /lib/x86_64-linux-gnu/libncurses.so.5

vlab@HYVLAB1:/opt/gcc-arm$ arm-none-eabi-gdb --version

arm-none-eabi-gdb: error while loading shared libraries: libtinfo.so.5: cannot open shared object file: No such file or directory


-If it is still not working ,follow same process for libtinfo

vlab@HYVLAB1:/opt/gcc-arm$ dpkg -l | grep libtinfo

ii  libtinfo6:amd64                            6.2-0ubuntu2.1                      amd64        shared low-level terminfo library for terminal handling

vlab@HYVLAB1:/opt/gcc-arm$ sudo ln -s /lib/x86_64-linux-gnu/libtinfo.so.6 /lib/x86_64-linux-gnu/libtinfo.so.5


vlab@HYVLAB1:/opt/gcc-arm$ arm-none-eabi-gdb --version

GNU gdb (GNU Arm Embedded Toolchain 10.3-2021.10) 10.2.90.20210621-git

Copyright (C) 2021 Free Software Foundation, Inc.

License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software: you are free to change and redistribute it.

There is NO WARRANTY, to the extent permitted by law.

 -To use GDB debugger in every terminal window we have to source it's path --> open .bashrc file using "gedit ~/.bashrc" and add "export PATH="/opt/gcc-arm/bin:$PATH" " at the end of file ,save and close it.
 
 -source the file using "source ~/.bashrc"
 
vlab@HYVLAB1:~/STM32F4-FreeRTOS$ arm-none-eabi-gdb --version

GNU gdb (GNU Arm Embedded Toolchain 10.3-2021.10) 10.2.90.20210621-git

Copyright (C) 2021 Free Software Foundation, Inc.

License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software: you are free to change and redistribute it.

There is NO WARRANTY, to the extent permitted by law.

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ 


 

ST-LINK Utility
===============

stlink utilty installed from terminal "sudo apt install stlink-tools".

vlab@HYVLAB1:~$ sudo st-info --probe

Found 1 stlink programmers

 serial: 343f6a06304d583455350443
 
openocd: "\x34\x3f\x6a\x06\x30\x4d\x58\x34\x55\x35\x04\x43"

  flash: 524288 (pagesize: 16384)
  
   sram: 131072
   
 chipid: 0x0431
 
  descr: F4 device (low power) - stm32f411re

-conlusion for stlink utility --> use the stlink utility downloaded from terminal "sudo apt install stlink-tools"
 and if the board is not connected perfectly with "sudo st-info --probe" then use  the forced option "sudo st-info --probe --connect-under-reset"
 
Compiling STM32F4-FreeRTOS 
==========================

Setting toolchain

-Compiling is possible only if "arm-none-eabi-gcc" tool chain is installed.

-Changed the variable "TOOLCHAIN_ROOT:=/usr/bin/" in Makefile of folder "STM32F4-FreeRTOS" to the folder where tool chain is installed, which was found using "which arm-none-eabi-gcc"-->"/usr/bin/arm-none-eabi-gcc".

-Initially the variable "TOOLCHAIN_PATH:=$(TOOLCHAIN_ROOT)" in Makefile, was like "TOOLCHAIN_PATH:=$(TOOLCHAIN_ROOT)/bin" ,due to error 
"make: /usr/bin//bin/arm-none-eabi-gcc: Command not found",to remove the extra "/bin" we changed it.

-->Compiling

Just run make command

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ make

[CC] stm32f4xx_it.c

[CC] system_stm32f4xx.c 

[CC] main.c 

[CC] syscalls.c 

[CC] port.c 

[CC] list.c 

[CC] queue.c 

[CC] tasks.c 

[CC] event_groups.c 

[CC] timers.c 

[CC] heap_4.c 

[CC] misc.c 

[CC] stm32f4xx_dcmi.c 

[CC] stm32f4xx_rtc.c 

[CC] stm32f4xx_adc.c 

[CC] stm32f4xx_dma.c 

[CC] stm32f4xx_sai.c

[CC] stm32f4xx_can.c 

[CC] stm32f4xx_dma2d.c 

[CC] stm32f4xx_sdio.c 

[CC] stm32f4xx_cec.c 

[CC] stm32f4xx_dsi.c 

[CC] stm32f4xx_i2c.c 

[CC] stm32f4xx_spdifrx.c 

[CC] stm32f4xx_crc.c 

[CC] stm32f4xx_exti.c 

[CC] stm32f4xx_iwdg.c 

[CC] stm32f4xx_spi.c

[CC] stm32f4xx_flash.c 

[CC] stm32f4xx_lptim.c 

[CC] stm32f4xx_syscfg.c

[CC] stm32f4xx_flash_ramfunc.c 

[CC] stm32f4xx_ltdc.c 

[CC] stm32f4xx_tim.c 

[CC] stm32f4xx_pwr.c 

[CC] stm32f4xx_usart.c 

[CC] stm32f4xx_fmpi2c.c 

[CC] stm32f4xx_qspi.c 

[CC] stm32f4xx_wwdg.c 

[CC] stm32f4xx_dac.c 

[CC] stm32f4xx_fsmc.c 

[CC] stm32f4xx_rcc.c 

[CC] stm32f4xx_dbgmcu.c 

[CC] stm32f4xx_gpio.c

[CC] stm32f4xx_rng.c 

[AS] startup_stm32f4xx.s

[LD] FreeRTOS.elf 

[HEX] FreeRTOS.hex 

[BIN] FreeRTOS.bin 

Debugging
=========

-->Flashing

-Flasing using stlink-tools installed from terminal,

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ sudo st-flash write binary/FreeRTOS.bin 0x8000000

st-flash 1.6.0

2024-03-21T11:57:16 INFO common.c: Loading device parameters....

2024-03-21T11:57:16 INFO common.c: Device connected is: F4 device (low power) - stm32f411re, id 0x10006431

2024-03-21T11:57:16 INFO common.c: SRAM size: 0x20000 bytes (128 KiB), Flash: 0x80000 bytes (512 KiB) in pages of 16384 bytes

2024-03-21T11:57:16 INFO common.c: Attempting to write 29796 (0x7464) bytes to stm32 address: 134217728 (0x8000000)

Flash page at addr: 0x08000000 erasedEraseFlash - Sector:0x1 Size:0x40Flash page at addr: 0x08004000 erased

2024-03-21T11:57:17 INFO common.c: Finished erasing 2 pages of 16384 (0x4000) bytes

2024-03-21T11:57:17 INFO common.c: Starting Flash write for F2/F4/L4

2024-03-21T11:57:17 INFO flash_loader.c: Successfully loaded flash loader in sram

enabling 32-bit flash writes

size: 29796

2024-03-21T11:57:17 INFO common.c: Starting verification of write complete

2024-03-21T11:57:18 INFO common.c: Flash written and verified! jolly good!


-->GDB Debugger

-Now to debug and observe the output ,chat gpt recommended to use GDB server to debug and maxicon(?) to observe the output of the written code i.e usart code.

-To connect to GDB server -->"sudo st-util"

-if it says already in use then we have to kill the process -->execute this command "sudo lsof -i :4242".

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ sudo lsof -i :4242

COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME

st-util 15345 root   10u  IPv4 189829      0t0  TCP *:4242 (LISTEN)

Use this PID to kill it -->"kill 15345"

-Make not of port number, which says "Listening at"

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ sudo lsof -i :4242

st-util 1.6.0

2024-05-03T11:22:15 INFO usb.c: -- exit_dfu_mode

2024-05-03T11:22:15 INFO common.c: Loading device parameters....

2024-05-03T11:22:15 INFO common.c: Device connected is: F4 device (low power) - stm32f411re, id 0x10006431

2024-05-03T11:22:15 INFO common.c: SRAM size: 0x20000 bytes (128 KiB), Flash: 0x80000 bytes (512 KiB) in pages of 16384 bytes

2024-05-03T11:22:15 INFO gdb-server.c: Chip ID is 00000431, Core ID is  2ba01477.

2024-05-03T11:22:15 INFO gdb-server.c: Listening at *:4242...

-Using GDB debugger

Open another terminal window and run the below command

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ arm-none-eabi-gdb binary/FreeRTOS.elf

GNU gdb (GNU Arm Embedded Toolchain 10.3-2021.10) 10.2.90.20210621-git

Copyright (C) 2021 Free Software Foundation, Inc.

...........

...........

...........

(gdb) tar ext :4242 (#Extended remote:-With target extended-remote mode-When the debugged program exits or you detach from it, GDB remains
connected to the target, even though no program is running.)

Remote debugging using :4242

0x08002958 in Reset_Handler ()

(gdb) b main (#Breakpoints)

Breakpoint 1 at 0x80007fc: file main.c, line 20.

Note: automatically using hardware breakpoints for read-only addresses.

(gdb) c (#Continue)

Continuing.

Program received signal SIGTRAP, Trace/breakpoint trap.

prvGetRegistersFromStack (pulFaultStackAddress=0x2001ffd8)

    at /home/vlab/STM32F4-FreeRTOS/hardware/stm32f4xx_it.c:87
    
87	  __ASM volatile("BKPT #02");

(gdb) 

*Refer GDB Cheet in DiscoSetup/ for more debugging commands

Observing output
================

Installed minicom using "sudo apt install minicom"

Creating Symlink for the port

-To verify if symlink is there or not -->"dmesg | grep ttyUSB",if it gives empty output then we have to create the symlink manually

-Run the commmand -->"lusb"

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ lsusb

Bus 002 Device 002: ID 8087:8000 Intel Corp. 

Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

Bus 001 Device 002: ID 8087:8008 Intel Corp. 

Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub

Bus 003 Device 002: ID 046d:c31d Logitech, Inc. Media Keyboard K200

Bus 003 Device 003: ID 046d:c077 Logitech, Inc. M105 Optical Mouse

Bus 003 Device 028: ID 0483:3748 STMicroelectronics ST-LINK/V2

Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

-It appears that your STM32F4 board is recognized as Bus 003 Device 028 (These numbers may change): ID 0483:3748 STMicroelectronics ST-LINK/V2 when you run
lsusb.

Since the device is detected, but the /dev/ttyUSB* device is not created automatically, you may need to manually create a symlink for it.

-creating symlink using -->"sudo ln -s /dev/bus/usb/003/028 /dev/ttyUSB0"

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ sudo ln -s /dev/bus/usb/003/028 /dev/ttyUSB0

-Verify if it is created,using-->"ls -l /dev/ttyUSB0"

vlab@HYVLAB1:~/STM32F4-FreeRTOS$ ls -l /dev/ttyUSB0

lrwxrwxrwx 1 root root 20 Mar 22 10:37 /dev/ttyUSB0 -> /dev/bus/usb/003/028



