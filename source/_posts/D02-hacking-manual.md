title: D02-hacking-manual
date: 2015-04-17 09:05:25
tags: D02
---
##Hisilicon Opensource Board D02 hacking manual

##Overview
* [D02 Board Hardwawre Feature](#hardware)
    * [The Architecture of D02](#architure)
    * [The function of each component](#function)
       * [UEFI](#UEFI)
       * [GRUB](#GRUB)
       * [Kernel](#kernel)
       * [Distributions](#dist)
       * [Toolchain](#toolchain)
* [D02 Hacking](#hacking)
    * [UEFI hacking](#UEFI)
    * [Kernel hacking](#kernel)
    * [Boot via NORFLASH](#NORFLASH)
    * [Boot via PXE](#PXE)
    * [Boot via SATA](#SATA)
    * [Boot via ACPI](#ACPI)
    * [Boot via NFS](#NFS)

##<span id="hardware">D02 Board Hardware Features</span>
Major hardware features of D02 are listed as follow:

<img src="http://7vilkn.com1.z0.glb.clouddn.com/d02_structure.jpg" style="width:700px;height:500px;margin-left:30px"/> 

The structure picture of D02 is as follow:

<img src="http://7vilkn.com1.z0.glb.clouddn.com/hardware.png" style="width:700px;height:500px;margin-left:30px"/> 

### <span id="architure">The Architecture of D02</span>

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

You can have a quick trying with all binaries, which can be downloaded from following link:

	https://github.com/hisilicon/estuary/releases

Here, you can click the 'Latest release' on left of page firstly, then download any files in the version as you like.

	e.g.: wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/Image

All binaries and their descriptions are listed as follows:

    +----------------------------+------------------------------------+       
    |  FILE NAME                 |    DESCRIPTION                     |
    +----------------------------+------------------------------------+    
    |  UEFI_Release.bin          |    UEFI binary                     |
    +----------------------------+------------------------------------+
    |  CH02_TEVBC_V03.bin        |    CPLD binary                     |
    +----------------------------+------------------------------------+
    |  bl1.bin                   |    Trust Fireware binary           |
    |  fip.bin                   |                                    |
    +----------------------------+------------------------------------+                    
    |  arch64aa.efi              |    grub binary                     |
    +----------------------------+------------------------------------+       
    |  grub.cfg                  |    grub configure                  |        
    +----------------------------+------------------------------------+       
    |  Image                     |    kernel image of 3.19            |
    +----------------------------+------------------------------------+
    |  hip05-d02.dtb             |    dtb file for kernel 3.19        |
    +----------------------------+------------------------------------+              
    |  hulk-hip05.cpio.gz        |    initramfs stored in NORFLASH    |   
    +----------------------------+------------------------------------+
    |  readme                    |    readme                          |
    +----------------------------+------------------------------------+

###<span id="function"> The function of each component</span>
1.<span id="UEFI">UEFI</span>:responsible for loading and booting Image and dtb file,and you could download binary file from:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/UEFI_Release.bin 

2.<span id="GRUB">GRUB</span>:grub configure file and grub image file for D02,and you could download binary file from:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/grub.cfg
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/grubaa64.efi

3.<span id="kernel">Kernel</span>:the operation system that D02 runs on,and you could get source code from follow website:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/Image
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/hip05-d02.dtb

4.<span id="dist">Linux Distribution</span>: Because the distribution's Images are too large, all of them are storaged into another place, you can dowload them from following website, the default user name and passowrd are: ubuntu, ubuntu.

	Ubuntu:     wget -c http://7xjz0v.com1.z0.glb.clouddn.com/dist/ubuntu-vivid.img.tar.gz
	OpenSUSE:   wget -c http://download.opensuse.org/ports/aarch64/distribution/13.2/appliances/openSUSE-13.2-ARM-E17.aarch64-rootfs.aarch64-Current.tbz (no validation)
	Fedora:     TBD
	Redhat:     TBD
	Debian:     TBD
	OpenEmbedded: TBD

5.<span id="toolchain">Toolchain</span>:used to compile and debug some above files, and now you could download it from follow website:

    wget -c http://releases.linaro.org/14.09/components/toolchain/binaries/gcc-linaro-aarch64-linux-gnu-4.9-2014.09_linux.tar.bz2

***Note:you should export the path of toolchain after you download and decompression it.***

##<span id="hacking">D02 hacking</span>

###<span id="UEFI">UEFI hacking</span>

FTP protocol is used for downloading between D02 and local network.So before this step, please make sure you have a working FTP server in local network. D02 could get files from network using FTP.

1. Check the hardware of D02 board

	You should check the type of dimm and disk on D02 board. For disks, it should satisfy the specification of **SATA3**.For dimms, it should satisfy the specification of **UDIMM 2Rank 8Gbytes** and **RDIMM 2Rank 8Gbytes**.

2. Prepare files about UEFI on local computer

	There are three files which should be prepared before hacking UEFI:D02.fd for BIOS, bl1.bin and fip.bin for Trust Fireware.Then put them on the root directory of FTP.

3. Boot D02 to UEFI SHELL

	Follow these steps to UEFI SHELL:

		a.Serial port setting:115200/8/N/1;
		b.Press any key to enter default Boot selection Menu;
		c.Select EBL selection;

	Then D02 enters the UEFI SHELL.

4. Update UEFI files

 * IP address config

			ifconfig -s eth0 [IP.address] [mask] [gateway]
			eg. ifconfig -s eth0  192.168.1.4 255.255.255.0 192.168.1.1

 * Burn BIOS file

			provision [server.IP] -u [user.name] -p [passwd] -f [UEFI_Release.bin] -a [address]
			spiwfmem [source address] [offset] [data length]
			eg. provision 192.168.1.107 -u sch -p aaa -f UEFI_Release.bin -a 100000
			spiwfmem 100000 0000000 300000

 * Burn CPLD file

			provision [server.IP] -u [user.name] -p [passwd] -f [cpld.bin] -a [address]
			updatecpld [address]
			eg. provision 192.168.1.107 -u sch -p aaa -f CH02_TEVBC_V03.bin -a 100000
			updatecpld 100000

 * Burn files for Trust Firmware(this step is not necessary when the format of UEFI is end of **fd** which is consist of trusted firmware and uefi) 

			provision [server.IP] -u [user.name] -p [passwd] -f bl1.bin -a [address]
			spiwfmem [source address] [offset] [data length]
			provision [server.IP] -u [user.name] -p [passwd] -f fip.bin -a [address]
			spiwfmem [source address] [offset] [data length]
			eg. provision 192.168.1.107 -u sch -p aaa -f bl1.bin -a 100000
			spiwfmem 100000 200000 10000
			provision 192.168.1.107 -u sch -p aaa -f fip.bin -a 100000
			spiwfmem 100000 220000 10000

Then D02 must be reset or powered off after this step.

***Note:The first time you boot D02,you have to update UEFI files,and this step is not necessary next time.***

### how to restore the UEFI when UEFI doesn't work
Actually D02 can restore two UEFI in case of failure in the default UEFI.you can resore UEFI in the follow way:

1. Power off the board and disconnect power supply
2. Push the dial swift s3 to the side that has silk screen '3'
3. Power on and enter UEFI SHELL
4. IP address config

	ifconfig -s eth0 [IP.address] [mask] [gateway]
  	eg. ifconfig -s eth0  192.168.1.4 255.255.255.0 192.168.1.1

5. Push the dial swift s3 to the other side that has no silk screen 's'
6. Burn UEFI file for BIOS as above part
Then you have updated your failture BIOS.Then D02 enters the UEFI SHELL.
7. Update UEFI files
  * IP address config

			ifconfig -s eth0 [IP.address] [mask] [gateway]
			eg. ifconfig -s eth0  192.168.1.4 255.255.255.0 192.168.1.1

  * Burn BIOS file

			provision [server.IP] -u [user.name] -p [passwd] -f [UEFI.fd] -a [address]
			spiwfmem [source address] [offset] [data length]
			eg. provision 192.168.1.107 -u sch -p aaa -f PV660D02_B903_Release.bin -a 100000
			spiwfmem 100000 0000000 300000

  * Burn files for Trust Firmware (This step is not necessary when the version of BIOS is B903 or more than B903)

			provision [server.IP] -u [user.name] -p [passwd] -f bl1.bin -a [address]
			spiwfmem [source address] [offset] [data length]
			provision [server.IP] -u [user.name] -p [passwd] -f fip.bin -a [address]
			spiwfmem [source address] [offset] [data length]
			eg. provision 192.168.1.107 -u sch -p aaa -f bl1.bin -a 100000
			spiwfmem 100000 200000 10000
			provision 192.168.1.107 -u sch -p aaa -f fip.bin -a 100000
			spiwfmem 100000 220000 10000

Then D02 must be reset or powered off after this step.

***Note:The first time you boot D02,you have to update UEFI files,and this step is not necessary next time.***

###<span id="kernelh"> Kernel Hacking</span>

1.Clone the latest kernel source code with following command 

    git clone https://github.com/hisilicon/linaro-kernel.git -b estuary

2.Setting envirnment from compiling D02 as above
3.Build steps as follow:
```
   export ARCH=arm64
   export CROSS_COMPILE=aarch64-linux-gnu-
   make defconfig
   make -j16
   make ./hisilicon/hip05-d02.dtb
```
Or you can also directly do build.sh in kernel tree directory.
Then there are two files that have been created:Image in the directory **arch/arm64/boot/**, and hip05-d02.dtb in the directory **arch/arm64/boot/dts/hisilicon**

### <span id="NORFLASH">Boot via NORFLASH</span>

Boot D02 to UEFI SHELL, and type the follow commands in EBL:
1.IP address config

    ifconfig -s eth0 [IP.address] [mask] [gateway]
    eg. ifconfig -s eth0 192.168.1.4 255.255.255.0 192.168.1.1

2.Download Image binary file from FTP

    provision [server.IP] -u [user.name] -p [passwd] -f [image.file] -a [address]
    norwfmem [source address] [offset] [data length]
    eg. provision 192.168.1.107 -u sch -p aaa -f Image -a 100000
        norwfmem 100000 100000 1f00000

3.Download dtb file from FTP

    provision [server.IP] -u [user.name] -p [passwd] -f [dtb.file] -a [address]
    spiwfmem [source address] [offset] [data length]
    eg. provision 192.168.1.107 -u sch -p aaa -f hip05-d02.dtb -a 100000
        spiwfmem 100000 300000 100000

4.Download filesystem file from FTP

    provision [server.IP] -u [user.name] -p [passwd] -f [filesystem] -a [address]
    norwfmem [source address] [offset] [data length]
    eg. provision 192.168.1.107 -u sch -p aaa -f hulk-hip05.cpio.gz -a 100000
        norwfmem 100000 2000000 4000000

5.Reboot D02
    exit ESL and select:FLASH Start OS

show & change kernel command line in UEFI

###<span id="PXE"> Boot via PXE</span>

PXE boot depends on DHCP and TFTP services.So before verifing PXE, you need to setup a working DHCP server and TFTP server on your local network. In this case, my case is on ubuntu.
1.Setup DHCP server on ubuntu
Refer to https://help.ubuntu.com/community/isc-dhcp-server . For a simplified direction, try these steps:
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
         filename "archaa64.efi";
     }
     #

c.Edit /etc/default/isc-dhcp-server to specify the interfaces dhcpd
      should listen to. By default it listens to eth0.
      Assign a static ip to the interface that you will use for dhcp.
d.Use these commands to start or check dhcp service

      sudo service isc-dhcp-server status
      sudo service isc-dhcp-server start

2.Setup TFTP server on ubuntu
a.Install TFTP server and TFTP client(optional, tftp-hpa is the client package)

      sudo apt-get install tftpd-hpa tftp-hpa

b.Configure the TFTP server, update /etc/default/tftpd-hpa like following:

      TFTP_USERNAME="tftp"
      TFTP_ADDRESS="0.0.0.0:69"
      TFTP_DIRECTORY="/var/lib/tftpboot"
      TFTP_OPTIONS="-l -c -s"

c.Set up TFTP server directory

      sudo mkdir /var/lib/tftpboot
      sudo chmod -R 777 /var/lib/tftpboot/

d.Restart TFTP server

      service tftpd-hpa status
      service tftpd-hpa restart
      service tftpd-hpa force-reload

3.Prepare some files on the TFTP root path
    The files include:grub binary file, grub configure file,Image and dtb file. In my case, they are grubaa64_linux.efi, grub.cfg-01-xx-xx-xx-xx-xx-xx,hip05-d02.dtb and Image.

***Note:The grub configure file grub.cfg-01-xx-xx-xx-xx-xx-xx ends in D02's mac address.***

4.Reboot and enter UEFI boot menu
5.Select boot from PXE
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

###<span id="SATA"> Boot via SATA</span>
This part will tell you how to boot D02 via GRUB on SATA disk.In my case, SATA disk
will be partitioned into five parts:sda1(EFI part),sda2(ubuntu release),sda3(OpenSUSE release),sda4(miniDistribution),sda5(for user).
1.Partition and format SATA disk
    Format SATA disk: mkfs -t ext4 /dev/sda
    Partition SATA disk as follow:

    +---------+-----------+--------------+------------------+
    | Name    |   Size    |    Type      |   Tag            |
    +---------+-----------+--------------+------------------+
    | sda1    |   200M    |  EFI system  |   EFI            |
    +---------+-----------+--------------+------------------+
    | sda2    |   10G     |    ext4      | linux filesystem |
    +---------+-----------+--------------+------------------+
    | sda3    |   10G     |    ext4      | linux filesystem |
    +---------+-----------+--------------+------------------+
    | sda4    |   10G     |    ext4      | linux filesystem |
    +---------+-----------+--------------+------------------+
    | sda5    |rest space |    ext4      | linux swap       |
    +---------+-----------+--------------+------------------+ 
    ***Note:EFI system should have a fat filesystem, so we should format sda1 with "mkfs.vfat /dev/sda1".***

###How to process with the disk when the disk is not identified by D02 board
when the disk is not identified by D02, you can try the following step to process the disk.(for some specfic disk eg seagate disk made by samsung, it can be useful) 
a.Find a pc or another board which can identify this disk
You should find a pc or another board which can identify this disk, and the system of pc or board should be linux system. For us,we can use D01 board.

b.Use tool fdisk to process this disk

	format the disk firstly: 
                        mkfs.ext4 /dev/sda
	add a gpt to this disk : 
                        fdisk /dev/sda
                        g-------add a gpt partition
	add some EFI partition : 
                        n-------add a partition
                        1-------the number of partition
                        +200M---------size of partition
                        t-------change the type of partition
                        EFI system
	add some anther partition  ...
	save the change	       : w
	formate EFI partition  : mkfs.vfat /dev/sda1

Then this disk can be identified by D02 board.

2.Relative files are placed as follow:

        sda1: -------EFI
              |       |
              |       GRUB2-----archaa64.efi
              |            |
              |            ------grub.cfg
              |
              |-------------Image_3.1
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

***Note:The names of grub files archaa64.efi is the same name with filename item in DHCP configure file /etc/dhcp/dhcpd.conf.***

3.modify grub config file according to situation
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

4.Reboot and enter UEFI menu
5.Select boot from HD
6.Type arrow key up and down to select grub boot menu to decise which kernel and distribution to boot

###<span id="ACPI"> Boot via ACPI</span>
D02 also supports booting via ACPI,and the steps are as follows:
1.Power on and enter UEFI SHELL
2.IP address config

    ifconfig -s eth0 [IP.address] [mask] [gateway]
    eg. ifconfig -s eth0 192.168.1.4 255.255.255.0 192.168.1.1

3.Burn the kernel from FTP

    provision [server.address] -u [user.name] -p [passwd] -f [kernel] -a [address1]
    mdfile \[kernel] [address1] [address2]
    eg. provision 192.168.1.107 -u d02 -p 123456 -f Image -a 0x80000
        mdfile \Image 0x80000 0x82A000

***Note:address2 is the real Image size which can be got from above command.***

4.Burn the filesystem from FTP

    provision [server.address] -u [user.name] -p [passwd] -f [filesystem.cpio.gz] -a [address3]
    mdfile \[filesystem.cpio.gz [address3] [address4]
    eg. provision 192.168.1.107 -u d02 -p 123456 -f hulk-hip05.cpio.gz -a 0x7000000
        mdfile \hulk-hip05.cpio.gz 0x7000000 0x1845F12

***Note:address4 is the real Image size which can be got from above command.***

5.Get into directory to check whether boot via ACPI is done

    cd fs0:
    dir

6.Set the parameters of booting via ACPI

    start Image "acpi=fore console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000 initrd=hulk-hip05.cpio.gz"

###<span id="NFS">Boot via NFS</span>
D02 supports booting via NFS, and before this step you should make sure that NFS service has been installed.
1.Setup NFS service on ubuntu
a.Install NFS service package

	sudo apt-get install nfs-kernel-server nfs-common portmap

b.Modify configure file /etc/exports
Add contents at the end of this file as follows:

	/rootnfs *(rw,sync,no_root_squash)

***Note:/rootnfs is the shared directory of nfs.***
c.Restart nfs service 

	sudo service nfs-kernel-server restart

2.Modify grub config file grub.cfg according to situation

	set timeout=0
	set default=nfsboot
	menuentry "nfs-boot" --id nfsboot {
        set root=(tftp,192.168.1.107)
        linux /Image rdinit=/init console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000 root=/dev/nfs rw nfsroot=192.168.1.107:/home/chenxiang/nfs ip=:::::eth0:dhcp
        devicetree /hip05-d02.dtb
	}

Note:Image is the kernel , and hip05-d02 is the dtb file. And the filesystem locates on ***192.168.1.107:/home/chenxiang/nfs***
3.Reboot D02 and enter UEFI menu.
4.Select item 2: PXE on MAC Address.
