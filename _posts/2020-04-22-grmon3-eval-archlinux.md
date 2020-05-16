---
title: "Instalar grmon3-eval en ArchLinux"
date: 2020-04-21
categories:
  - blog
tags:
  - i3wm
  - arch
  - sysadmin
  - grlib
---

## Prerequisitos 
`sudo pacman -S libusb libusb-compat`
`sudo pacman -S libftdi  libftdi-compat-0.20-7`

## Ejecutable
`wget https://www.gaisler.com/anonftp/grmon/grmon-eval-64-3.2.2.tar.gz`
--2020-04-21 13:32:46--  https://www.gaisler.com/anonftp/grmon/grmon-eval-64-3.2.2.tar.gz
`tar xvf grmon-eval-64-3.2.2.tar.gz`

## Ejemplo de uso:
` sudo /home/jmgomez/Workspace/fpga/grmon-eval-3.2.2/linux/bin64/grmon -u -uart /dev/ttyUSB1 -baud 230400 `
  GRMON debug monitor v3.2.2 64-bit eval version
  
  using port /dev/ttyUSB1 @ 115200 baud
  GRLIB build version: 4246
  Detected frequency:  74.0 MHz
  
  Component                            Vendor
  LEON3 SPARC V8 Processor             Cobham Gaisler
  AHB Debug UART                       Cobham Gaisler
  AHB/APB Bridge                       Cobham Gaisler
  LEON3 Debug Support Unit             Cobham Gaisler
  LEON2 Memory Controller              European Space Agency
  SPI Memory Controller                Cobham Gaisler
  Generic UART                         Cobham Gaisler
  Multi-processor Interrupt Ctrl.      Cobham Gaisler
  Modular Timer Unit                   Cobham Gaisler
  
  Use command 'info sys' to print a detailed report of attached cores

grmon3> info sys

>   cpu0      Cobham Gaisler  LEON3 SPARC V8 Processor    
>             AHB Master 0
>   ahbuart0  Cobham Gaisler  AHB Debug UART    
>             AHB Master 1
>             APB: 80000700 - 80000800
>             Baudrate 115200, AHB frequency 74.00 MHz
>   apbmst0   Cobham Gaisler  AHB/APB Bridge    
>             AHB: 80000000 - 80100000
>   dsu0      Cobham Gaisler  LEON3 Debug Support Unit    
>             AHB: 90000000 - a0000000
>             AHB trace: 128 lines, 32-bit bus
>             CPU0:  win 8, itrace 128, V8 mul/div, lddel 1
>                    stack pointer 0x4007fff0
>                    icache 2 * 8 kB, 16 B/line
>                    dcache 2 * 4 kB, 16 B/line
>   mctrl0    European Space Agency  LEON2 Memory Controller    
>             AHB: 40000000 - 80000000
>             APB: 80000000 - 80000100
>             8-bit static ram: 1 * 512 kbyte @ 0x40000000
>   spim0     Cobham Gaisler  SPI Memory Controller    
>             AHB: fff70000 - fff70100
>             AHB: 00000000 - 00400000
>             IRQ: 7
>             SPI memory device read command: 0x0b
>   uart0     Cobham Gaisler  Generic UART    
>             APB: 80000100 - 80000200
>             IRQ: 2
>             Baudrate 38381, FIFO debug mode available
>   irqmp0    Cobham Gaisler  Multi-processor Interrupt Ctrl.    
>             APB: 80000200 - 80000300
>   gptimer0  Cobham Gaisler  Modular Timer Unit    
>             APB: 80000300 - 80000400
>             IRQ: 8
>             8-bit scalar, 2 * 32-bit timers, divisor 74

  
grmon3> 

