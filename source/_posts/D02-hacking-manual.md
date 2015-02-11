title: D02-hacking-manual
date: 2015-02-10 18:55:25
tags: D02
---
#Hisilicon Opensource Board D02 hacking manual

##Overview
* D02 Board Hardwawre Feature
    * The Architecture of D02
    * The function of each component
       * UEFI
       * GRUB
       * Kernel
       * Distributions
       * Toolchain
* D02 Hacking
    * UEFI hacking
    * Kernel hacking
    * Boot via NAND
    * Boot via SATA
    * Boot via PXE
    * Boot via GRUB
    * Boot via ACPI
 
##D02 Board Hardware Features
Major hardware features of D02 are listed as follow:

	+----------------------+------------------------------------------+    
	|Features              |Description                               |    
	+----------------------+------------------------------------------+    
	|Board                 |SSI-EEB/E-ATX Compatible                  |    
	+----------------------+------------------------------------------+    
	|Processors            |16xARM Cortex-A57@max 2.1GHz              |    
	|                      |Support ARMv8-A instruction set           |    
	+----------------------+------------------------------------------+    
	|Memory                |2x Memory channel 4x DDR3 DIMM            |    
	|                      |2x SPI Flash 158Mb BIOS/UEFI              |    
	|                      |1Gb NorFlash                              |    
	+----------------------+------------------------------------------+    
	|Peripheral interfaces |1x USB2.0 host port                       |    
	|                      |1x UART interface                         |    
	|                      |8x SAS3.0                                 |    
	|                      |1X ARM Tracer connector                   |    
	|                      |1x JTAG interface                         |    
	+----------------------+------------------------------------------+    
	|Expansion Capabilities|2 8x PCI express interfaces               |    
	+----------------------+------------------------------------------+    
	|LAN                   |2x10/100/1000Mbit/s Gigabit Ethernet ports|    
	|                      |2x XGE SEP+                               |    
	+----------------------+------------------------------------------+    
	|Other                 |RTC battery                               |    
	+----------------------+------------------------------------------+   

### The Architecture of D02

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

And you could download the binary from the link:

    https://github.com/hisilicon/d02_binary

### The function of each component

<1>UEFI:responsible for loading and booting Image and dtb file,and you could download binary file from:

	https://github.com/hisilicon/d02_binary/tree/master/UEFI

<2>GRUB:responsible for loading and booting Image and dtb file in another way,and you could download binary file from:

	https://github.com/hisilicon/d02_binary/tree/master/grub

<3>Kernel:the operation system that D02 runs on,and you could get source code from follow website:

    https://github.com/hisilicon/estuary

<4>Linux Distribution:current release distributions that D02 runs on, and you could dowload them from website:
<5>Toolchain:used to compile and debug some above files, and now you could download it from follow website:

    http://releases.linaro.org/14.09/components/toolchain/binaries/gcc-linaro-aarch64-linux-gnu-4.9-2014.09_linux.tar.bz2

***Note:you should export the path of toolchain after you download and decompression it.***

##D02 hacking

###UEFI hacking

FTP protocol is used for downloading between D02 and local network.So before this step, please make sure you have a working FTP server in local network. D02 could get files from network using FTP.
<1>Prepare files about UEFI on local computer
There are three files which should be prepared before hacking UEFI:D02.fd for BIOS, bl1.bin and fip.bin for Trust Fireware.Then put them on the root directory of FTP.
<2>Boot D02 to UEFI SHELL
Follow these steps to UEFI SHELL:

a.Serial port setting:115200/8/N/1

b.Power on and select 1:

c.Press any key to start UEFI Boot Menu;

d.Select 3 EBL

Then D02 enters the UEFI SHELL.
<3>Update UEFI files
a.IP address config

	ifconfig -s eth0 [IP.address] [mask] [gateway]
  	eg. ifconfig -s eth0  192.168.10.4 255.255.255.0 192.168.10.1

b.Burn UEFI file for BIOS

	provision [server.IP] -u [user.name] -p [passwd] -f [UEFI.fd] -a [address]
	eg. provision 192.168.10.102 -u sch -p aaa -f D02.fd -a 100000
 	    spiwfmem 100000 0000000 200000

c.Burn files for Trust Firmware 

	provision [server.IP] -u [user.name] -p [passwd] -f bl1.bin -a [address]
	provision [server.IP] -u [user.name] -p [passwd] -f fip.bin -a [address]
	eg. provision 192.168.10.102 -u sch -p aaa -f bl1.bin -a 100000
	    spiwfmem 100000 200000 10000
            provision 192.168.10.102 -u sch -p aaa -f fip.bin -a 100000
	    spiwfmem 100000 220000 10000

Then D02 must be reset or powered off after this step.

***Note:The first time you boot D02,you have to update UEFI files,and this step is not necessary next time.***

### how to restore the UEFI when UEFI doesn't work
Actually D02 can restore two UEFI in case of failure in the default UEFI.you can resore UEFI in the follow way:
<1>Power off the board and disconnect power supply
<2>Push the dial swift s3 to the side that has silk screen '3'
<3>Power on and enter UEFI SHELL
<4>IP address config

	ifconfig -s eth0 [IP.address] [mask] [gateway]
  	eg. ifconfig -s eth0  192.168.10.4 255.255.255.0 192.168.10.1

<5>Push the dial swift s3 to the other side that has no silk screen 's'
<6>Burn UEFI file for BIOS as above part
Then you have updated your failture BIOS.Then D02 enters the UEFI SHELL.
<3>Update UEFI files
a.IP address config

	ifconfig -s eth0 [IP.address] [mask] [gateway]
  	eg. ifconfig -s eth0  192.168.10.4 255.255.255.0 192.168.10.1

b.Burn UEFI file for BIOS

	provision [server.IP] -u [user.name] -p [passwd] -f [UEFI.fd] -a [address]
	eg. provision 192.168.10.102 -u sch -p aaa -f D02.fd -a 100000
 	    spiwfmem 100000 0000000 200000

c.Burn files for Trust Firmware 

	provision [server.IP] -u [user.name] -p [passwd] -f bl1.bin -a [address]
	provision [server.IP] -u [user.name] -p [passwd] -f fip.bin -a [address]
	eg. provision 192.168.10.102 -u sch -p aaa -f bl1.bin -a 100000
	    spiwfmem 100000 200000 10000
            provision 192.168.10.102 -u sch -p aaa -f fip.bin -a 100000
	    spiwfmem 100000 220000 10000

Then D02 must be reset or powered off after this step.

***Note:The first time you boot D02,you have to update UEFI files,and this step is not necessary next time.***

### Kernel Hacking

<1>Download kernel source code from above section
<2>Setting envirnment from compiling D02 as above
<3>Build steps as follow:
```
   export ARCH=arm64
   export CROSS_COMPILE=aarch64-linux-gnu-
   make hulk_defconfig
   make -j16
   make ./hisilicon/hip05-d02.dtb
```
Then there are two files that have been created:Image in the directory **arch/arm64/boot/**, and hip05-d02.dtb in the directory **arch/arm64/boot/dts/hisilicon**

### Boot via NAND

Boot D02 to UEFI SHELL, and type the follow commands in EBL:
<1>IP address config

    ifconfig -s eth0 [IP.address] [mask] [gateway]
    eg. provision 192.168.10.102 255.255.255.0 102.168.10.1

<2>Download Image binary file from FTP

    provision [server.IP] -u [user.name] -p [passwd] -f [image.file] -a [address]
    eg. provision 192.168.10.102 -u sch -p aaa -f Image -a 0x80000

<3>Download dtb file from FTP

    provision [server.IP] -u [user.name] -p [passwd] -f [dtb.file] -a [address]
    eg. provision 192.168.10.102 -u sch -p aaa -f hip05-d02.dtb -a

<4>Download filesystem file from FTP

    provision [server.IP] -u [user.name] -p [passwd] -f [filesystem] -a [address]
    eg. provision 192.168.10.102 -u sch -p aaa -f filesystem.cpio.gz -a 0x7000000

<5>Reboot D02
    exit ESL and select 5:ESL Start OS

show & change kernel command line in UEFI

### Boot ubuntu on SATA disk
<1>Download ubuntu release for D02 as above section
<2>Boot D02 via NAND as above section
<3>Partition and format SATA disk
    Create one ext4 partition of at least 10G to host the root filesystem.In my case, it is /dev/sda1
<4>Mount /dev/sda1 to D02's directory /dev/sda1

    mkdir /mnt/sda1
    mount -t ext4 /dev/sda1 /mnt/sda1

<5>Exact ubuntu server release to /mnt/sda1
    You could exact this distribution to the directory with FTP

    scp [user.name]@[ip.address]:/U/B/U/N/T/U/P/A/T/H /mnt/sda1

<6>Unmount /dev/sda1
<7>Reboot and enter UEFI EBL SHELL according to UEFI hacking part
<8>Change kernel command line.

    console=ttyS0,115200 root=/dev/sda1 rootfstype=ext4 rw earlyprintk

***Note:you should change /dev/sda1 according to your SATA disk situation***

### Boot via PXE

PXE boot depends on DHCP and TFTP services.So before verifing PXE, you need to setup a working DHCP server and TFTP server on your local network. In this case, my case is on ubuntu.
<1>Setup DHCP server on ubuntu
Refer to https://help.ubuntu.com/community/isc-dhcp-server.For a simplified direction, try these steps:
a.Install DHCP server package

	sudo apt-get install isc-dhcp-server

b.Edit /etc/dhcp/dhcpd.conf to suit your needs and particular configuration.Make sure filename is “grub2.efi”. Here is an example:

     $ cat /etc/dhcp/dhcpd.conf
     # Sample /etc/dhcpd.conf
     # (add your comments here)
     default-lease-time 600;
     max-lease-time 7200;
     option subnet-mask 255.255.255.0;
     option broadcast-address 192.168.0.255;
     option routers 192.168.0.1;
     option domain-name-servers 192.168.0.1;
     option domain-name "mydomain.example";
     subnet 192.168.0.0 netmask 255.255.255.0 {
         range 192.168.0.160 192.168.0.180;
         option subnet-mask 255.255.255.0;
         filename "grub2.efi";
     }
     #

c.Edit /etc/default/isc-dhcp-server to specify the interfaces dhcpd
      should listen to. By default it listens to eth0.
      Assign a static ip to the interface that you will use for dhcp.
d.Use these commands to start or check dhcp service

      sudo service isc-dhcp-server status
      sudo service isc-dhcp-server start

<2>Setup TFTP server on ubuntu
a.Install TFTP server and TFTP client(optional, tftp-hpa is the client package)

      sudo apt-get install tftpd-hpa tftp-hpa

b.Configure the TFTP server, update /etc/default/tftpd-hpa like following:

      TFTP_USERNAME=tftp
      TFTP_ADDRESS=0.0.0.0:69
      TFTP_DIRECTORY=/var/lib/tftpboot
      TFTP_OPTIONS=-l -c -s

c.Set up TFTP server directory

      sudo mkdir /var/lib/tftpboot
      sudo chmod -R 777 /var/lib/tftpboot/

d.Restart TFTP server

      service tftpd-hpa status
      service tftpd-hpa restart
      service tftpd-hpa force-reload

<3>Prepare some files on the TFTP root path
    The files include:grub binary file, grub configure file,Image and dtb file. In my case, they are grubaa64_linux.efi, grub.cfg-01-xx-xx-xx-xx-xx-xx,hip05-d02.dtb and Image.

***Note:The grub configure file grub.cfg-01-xx-xx-xx-xx-xx-xx ends in D02's mac address.***

<4>Reboot and enter UEFI boot menu
<5>Select boot from PXE
After seral seconds,D02 will boot automaticlly.
The content of grub file as follow:

	#
	# Sample GRUB configuration file
	#
	# Boot automatically after 0 secs.
	set timeout=5
	# By default, boot the Euler/Linux
	set default=minilinux
	# For booting GNU/Linux
	menuentry "Mini-Linux" --id minilinux {
	set root=(tftp,192.168.1.110)
	linux /Image_3.16 rdinit=/init console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	initrd /hulk-hip05.cpio.gz
	devicetree /hip05-d02_3.16.dtb
	}

***Note: minilinux is the name of default PXE that will boot automatically.Image_3.16 is the name of kernel that locates on root directory of TFTP.hip05-d02_3.16.dtb is the name of dtb file that locates on root directory of TFTP.hulk-hip05.cpio.gz is the name of filesystem that locates on root diretory of TFTP.And you can change the names and locations.***

### Boot via GRUB
This part will tell you how to boot D02 via GRUB on SATA disk.In my case, SATA disk
will be partitioned into five parts:sda1(EFI part),sda2(ubuntu release),sda3(OpenSUSE release),sda4(miniDistribution),sda5(for user).
<1>Partition and format SATA disk
    Format SATA disk: mkfs -t ext4 /dev/sda
    Partition SATA disk as follow:
    +---------+-----------+--------------+----------+
    | Name    |   Size    |    Type      |   Tag    |
    +---------+-----------+--------------+----------+
    | sda1    |   200M    |  EFI system  |   EFI    |
    +---------+-----------+--------------+----------+
    | sda2    |   10G     |    ext4      |          |
    +---------+-----------+--------------+----------+
    | sda3    |   10G     |    ext4      |          |
    +---------+-----------+--------------+----------+
    | sda4    |   10G     |    ext4      |          |
    +---------+-----------+--------------+----------+
    | sda5    |rest space |    ext4      |   swap   |
    +---------+-----------+--------------+----------+ 
<2>Relative files are placed as follow:
	sda1: -------EFI
	      |       |
          |       ------archaa.efi
          |       |
	      |       ------grub.cfg
          |
	      |-------------Image_3.16
	      |
	      |-------------hip05-d02_3.16
	      |
	      |-------------Image_3.19
	      |
	      |-------------hip05-d02_3.19
	sda2: ubuntu distribution
 	sda3: OpenSUSE distribution
	sda4: miniDistribution
	sda5: left for user

***Note:The names of grub files archaa.efi and grub.cfg can not be modified.***

<3>modify grub config file according to situation
This part we will build 6 different grub config.They are as follow:
	+-----------+-----------+----------------+------------------+
	| boot name |  kernel   |  distribution  |  dtb file        |
	+-----------+-----------+----------------+------------------+
	| ubuntu316 |Image_3.16 |     ubuntu     |hip05-d02_3.16.dtb|
	+-----------+-----------+----------------+------------------+
	| ubuntu319 |Image_3.19 |     ubuntu     |hip05-d02_3.19.dtb|
	+-----------+-----------+----------------+------------------+
	|opensuse316|Image_3.16 |    opensuse    |hip05-d02_3.16.dtb|
	+-----------+-----------+----------------+------------------+
	|opensuse319|Image_3.19 |    opensuse    |hip05-d02_3.19.dtb|
	+-----------+-----------+----------------+------------------+
	| mini316   |Image_3.16 |    mini        |hip05-d02_3.16.dtb|
	+-----------+-----------+----------------+------------------+
	| mini319   |Image_3.19 |    mini        |hip05-d02_3.19.dtb|
	+-----------+-----------+----------------+------------------+

And the context of grub.cfg file is as follow:

	#
	# Sample GRUB configuration file
	#
	# Boot automatically after 0 secs.
	set timeout=5
	# By default, boot the Euler/Linux
	set default=ubuntu316
	# For booting GNU/Linux
	menuentry "ubuntu-3.16" --id ubuntu316 {
	set root=(hd1,gpt1)
	linux /Image_3.16 rdinit=/init root=/dev/sda2 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	devicetree /hip05-d02_3.16.dtb
	}
	menuentry "ubuntu-3.19" --id ubuntu319 {
	set root=(hd1,gpt1)
	linux /Image_3.19 rdinit=/init root=/dev/sda2 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	devicetree /hip05-d02_3.19.dtb
	}
	menuentry "opensuse-3.16" --id opensuse316 {
	set root=(hd1,gpt1)
	linux /Image_3.16 rdinit=/init root=/dev/sda3 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	devicetree /hip05-d02_3.16.dtb
	}
	menuentry "opensuse-3.19" --id opensuse319 {
	set root=(hd1,gpt1)
	linux /Image_3.19 rdinit=/init root=/dev/sda3 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	devicetree /hip05-d02_3.19.dtb
	}
	menuentry "mini-3.16" --id mini316 {
	set root=(hd1,gpt1)
	linux /Image_3.16 rdinit=/init root=/dev/sda4 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	devicetree /hip05-d02_3.16.dtb
	}
	menuentry "mini-3.19" --id mini319 {
	set root=(hd1,gpt1)
	linux /Image_3.19 rdinit=/init root=/dev/sda4 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
	devicetree /hip05-d02_3.19.dtb
	}

***Note:If you only want to boot D02 from one kernel and a distribution,one menuentry is left as above.***

<4>Reboot and enter UEFI menu
<5>Select boot from HD
<6>Type arrow key up and down to select grub boot menu to decise which kernel and 
distribution to boot

### Boot via ACPI
D02 also supports booting via ACPI,and the steps are as follows:
<1>Power on and enter UEFI SHELL
<2>IP address config

    ifconfig -s eth0 [IP.address] [mask] [gateway]
    eg. ifconfig -s eth0 192.168.1.102 255.255.255.0 192.168.1.1

<3>Burn the kernel from FTP

    provision [server.address] -u [user.name] -p [passwd] -f [kernel] -a [address1]
    mdfile \[kernel] [address1] [address2]
    eg. provision 192.168.1.112 -u d02 -p 123456 -f Image -a 0x80000
        mdfile \Image 0x80000 0x82A000

***Note:address2 is the real Image size which can be got from above command.***

<4>Burn the filesystem from FTP

    provision [server.address] -u [user.name] -p [passwd] -f [filesystem.cpio.gz] -a [address3]
    mdfile \[filesystem.cpio.gz [address3] [address4]
    eg. provision 192.168.1.112 -u d02 -p 123456 -f hulk-hip05.cpio.gz -a 0x7000000
        mdfile \hulk-hip05.cpio.gz 0x7000000 0x1845F12

***Note:address4 is the real Image size which can be got from above command.***

<5>Get into directory to check whether boot via ACPI is done

    cd fs0:
    dir

<6>Set the parameters of booting via ACPI

    start Image "acpi=fore console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000 initrd=hulk-hip05.cpio.gz"

