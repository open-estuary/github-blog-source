title: Hardware Boards
date: 2015-08-10 10:13:06
tags: Hardware Boards
---

Currently, we totally support <a href="/tags/D01/">D01</a> and <a href="/tags/D02/">D02</a> hardware boards, and more other boards will be expected to enable in future, for the detail difference between them, please see following information.

##Overview

* [D01 Board Hardware Featuers](#d01)
* [The D01's Architecutre](#d01arch)
* [D02 Board Hardwawre Feature](#d02)
* [The D02's Architecture](#d02arch)

<a name="d01"/>
##D01 Board Hardware Featuers

Major hardware features of D01 are listed here.                               
                                                                              
	+----------+---------------------------------------------------------+
	|Features  |Description                                              |
	+----------+---------------------------------------------------------+
	|Processors|Integrated 16 x ARM Cortex-A15 CPU Core @ max. 1.5GHz    |
	|          |Support for up to 84000 DMIPS                            |
	|          |Support for CPU configuration as AMP/SMP                 |
	|          |Configurable Big or Littile endian,default Little endian |
	|          |Support ARMv7-A instruction set                          |
	|          |Support float VFPv4 instruction set                      |
	+----------+---------------------------------------------------------+
	|Memory    |Two 64bit DDR3 DRAM Dual Inline                          |
	|          |Memory Module(DIMM) sockets:(2)&(3)                      |
	|          |Maximum frequency of 1600 MHz                            |
	|          |Maximum capacity of 64GB                                 |
	|          |preassembled capacity:16GB                               |
	|          |Built-in two 1Gb NOR Flash;                              |
	|          |only one NOR flash could use by software:(29)            |
	|          |Built-in two 512MB NAND Flash:(30)                       |
	+----------+---------------------------------------------------------+
	|GPU       |None                                                     |
	+----------+---------------------------------------------------------+
	|Peripheral|TWO USB2.0 Host port:(11)                                |
	|Interfaces|Two UART interfaces:(12)&(13)                            |
	|          |Four I2C interfaces                                      |
	|          |One SPI interface, supporting four CSs                   |
	|          |One SD card interface:(10)                               |
	|          |GPIO: eight LED interfaces:(26);eight switchs:(25)       |
	|          |Three SATA interfaces(2.5in SATA 3.0 6Gbps):(4)&(5)&(6)  |
	|          |Tracer Connector x2:(19)&(20)                            |
	|          |One JTAG interfaces(5x2 pin, ARM Connector):(24)         |
	+----------+---------------------------------------------------------+
	|BIOS      |BIOS resident in NOR Flash                               |
	|          |Support for update with FTP                              |
	+----------+---------------------------------------------------------+
	|LAN       |Two 10/100/1000Mbit/s Gigabit Ethernet ports:(15)&(16)   |
	|          |One 10/100Mbit/s FE port:(14)                            |
	+----------+---------------------------------------------------------+
	|Hardware  |Power consumption sense                                  |
	|Monitor   |                                                         |
	+----------+---------------------------------------------------------+
	|Input     |None                                                     |
	|Devices   |                                                         |
	+----------+---------------------------------------------------------+
	|Other     |One hardware button for power-off:(8)                    |
	|          |One hardware button for reset:(9)                        |
	+----------+---------------------------------------------------------+
                                                                              
<a name="pic"/>
Refer to the following picture to know  
where each component below is physically located.    

<img src="http://7vilkn.com1.z0.glb.clouddn.com/d01-portrait.png" style="width:700px;height:500px;margin-left:30px"/> 

<a name="d01arch"/> 
##The D01's architecture 

The software architecture on D01 is consisted of [UEFI], [BootWrapper]  
, [GRUB], [EFI Stub], [Kernel], [Distributions] and   
[Toolchain]. The big picture is like following:


          +--------------------------+ +-----+
          |  NANDRootfs Ubuntu       | |  T  |
          |  OpenSuse Debian Fedora  | |  o  |
          +--------------------------+ |  o  |
          |         Kernel           | |  l  |
          +-----------+              | |  C  |
          |    GRUB   |   EFI-STUB   | |  h  |
          +----+------+--------+-----+ |  a  |
          |    |  BootWrapper  |     | |  i  |
          |    +---------------+     | |  n  |
          |           UEFI           | |     |
          | +-------+-------+-----+  | |     |
          | | NANDC | SATAC | PXE |  | |     |
          | +-------+-------+-----+  | |     |
          +--------------------------+ +-----+

More detail about software solution, please refer to <a href="/tags/Overview-Introduction/">Overview Introduction</a>

##<span id="d02">D02 Board Hardware Features</span>

You should check the type of dimm and disk on D02 board firstly. Currently, for disks, it must satisfy the specification of **SATA3**; for dimms, it should satisfy the specification of **UDIMM 2Rank 8Gbytes** and **RDIMM 2Rank 8Gbytes**.

Major hardware features of D02 are listed as follow:

<img src="http://7vilkn.com1.z0.glb.clouddn.com/d02_structure.jpg" style="width:700px;height:500px;margin-left:30px"/> 

The structure picture of D02 is as follow:

<img src="http://7vilkn.com1.z0.glb.clouddn.com/hardware.png" style="width:700px;height:500px;margin-left:30px"/> 

### <span id="d02arch">The D02's Architecture</span>

The software architecture on D02 is consistented of UEFI,GRUB,Kernel,Distribution and Toolchain.The architecture of D02 is as follow:

	+-----------------------++------+
	| NANDRootfs  ubuntu    ||  T   |
	| OpenSuse Debian Fedora||  o   |
	+-----------------------+|  o   |
	|        Kernel         ||  l   |
	+--------+              ||  c   |
	|  GRUB  |              ||  h   |
	+--------+--------------+|  a   |
	|        UEFI           ||  i   |
	| +------+------+------+||  n   |
	| |NANDC | SATAC| PXE  |||      |
	| +------+------+------+||      |
	+-----------------------++------+

More detail about software solution, please refer to <a href="/tags/Overview-Introduction/">Overview Introduction</a>
