title: UEFI and Grub
date: 2015-08-10 10:13:18
tags: UEFI and Grub
---

Grub is a kind of boot loader to load kernel\OS into RAM and run it, meanwhile UEFI is a kind of BIOS to provide runtime service to OS which can do some basic IO operation with the runtime service, e.g.: reboot, power off and etc.

###Get them

For the source code of Grub, you can get it by follow:

	git clone git://git.savannah.gnu.org/grub.git

For the source code of UEFI, you can get it by follow:

	git clone https://github.com/hisilicon/UEFI.git

For binaries mentioned above, you can get them by follows, more introduction to these binaries, please refer to <a href="/tags/Binary-Files/">Binary Files</a>:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/CH02_TEVBC_V03.bin
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/bl1.bin 
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/fip.bin
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/UEFI_Release.bin 
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/grub.cfg
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/grubaa64.efi


###UEFI hacking

***Note: This step is not necessary unless you want to update UEFI***

FTP protocol is used for downloading between hardware boards and local network. So before this step, please make sure you have a working FTP server in local network, so that board could get files from network using FTP.

1. Prepare files about UEFI on local computer

	There are several files which should be prepared before hacking UEFI: UEFI_Release.bin for BIOS, bl1.bin and fip.bin for Trust Fireware, CH02_TVBC_V03.bin for power control. Then put them on the root directory of FTP.

2. Boot board into UEFI SHELL

	Follow these steps to UEFI SHELL:

		a.Serial port setting:115200/8/N/1;
		b.Press any key to enter default Boot selection Menu;
		c.Select EBL selection;

	Then the board will enter the UEFI SHELL.

3. Update UEFI files

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

Then board must be reset or powered off after this step.

###Recover the UEFI when it doesn't work
Actually the board can restore two UEFI in case of failure in the default UEFI.you can resore UEFI in the follow way:

1. Power off the board and disconnect power supply.
2. Push the dial switch s3 to the side that has silk screen '3', please check <a href="/tags/Hardware-Boards/">Hardware Boards</a> to find where it is.
3. Power on and enter UEFI SHELL again.
4. IP address config

	ifconfig -s eth0 [IP.address] [mask] [gateway]
  	eg. ifconfig -s eth0  192.168.1.4 255.255.255.0 192.168.1.1

5. Push the dial swift s3 to the other side that has no silk screen 's'
6. Burn UEFI file for BIOS as above step3.
7. Reset the system again.

Now you have already updated your failed BIOS, and the board will enter the UEFI SHELL.

###Config Grub file
Grub is used to load the kernel into RAM and start it, normally they are place into bootable partition as following structure. 

<img src="http://7xjz0v.com1.z0.glb.clouddn.com/bootarch.jpg" style="width:556px;height:164px;margin-left:30px"/>

You can edit the grub.cfg to support various boot mode, follow is an example.

    #
    # Sample GRUB configuration file
    #
    
    # Boot automatically after 0 secs.
    set timeout=5
    
    # By default, boot the Euler/Linux
    set default=ubuntu_nfs
    
    # Booting with ramdisk rootfs by PXE
    menuentry "minilinux" --id minilinux {
    	set root=(tftp,192.168.1.107)
    	linux /Image rdinit=/init crashkernel=256M@32M console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000 ip=:::::eth0:dhcp
    	initrd /hulk-hip05.cpio.gz
    	devicetree /hip05-d02.dtb
    }
    
    # Booting with NFS rootfs by PXE
    menuentry "Ubuntu NFS" --id ubuntu_nfs {
    	set root=(tftp,192.168.1.107)
    	linux /Image rdinit=/init console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000 root=/dev/nfs rw nfsroot=192.168.1.107:/home/hisilicon/ftp/user/rootfs_ubuntu64 ip=:::::eth0:dhcp
    	devicetree /hip05-d02.dtb
    }
    
    # Booting with SATA rootfs
    menuentry "Ubuntu SATA" --id ubuntu_sata {
    	set root=(hd1,gpt1)
    	linux /Image rdinit=/init root=/dev/sda2 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000 ip=:::::eth0:dhcp
    	devicetree /hip05-d02.dtb
    }

***Note: --id is the name of boot options, Image indicates the name of kernel image, hip05-d02.dtb indicates the name of data tree binary file, hulk-hip05.cpio.gz indicates the name of ramdisk rootfs. You can change their names and locations.***
