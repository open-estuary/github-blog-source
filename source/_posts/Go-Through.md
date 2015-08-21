title: Go Through
date: 2015-08-10 10:16:19
tags: Go Through
---
##Go through Estuary Manual

This documentation will lead you to go through the whole project about how to get, build, deploy and bring up target system based on ZERO, of course some contents in it are linked to dedicated page, you can just click the link to get more detail information about the special part.

##Overview
* [Preparation Before Booting](#preparation)
    * [Hardware & UEFI](#UEFI)
    * [Get\Build Estuary yourself](#build)
    * [Only build kernel](#kernel)
    * [Use binaries direclty](#binary)
* [Bring up System](#bringup)
    * [Boot via NORFLASH](#NORFLASH)
    * [Boot via PXE](#PXE)
    * [Boot via SATA](#SATA)
    * [Boot via ACPI](#ACPI)
    * [Boot via NFS](#NFS)

All following desriptions will take the D02 boards as example, other boards have the similar steps to hack, for more detail difference, please refer to <a href="/tags/Hardware-Boards/">Hardware Boards</a>

##<span id="preparation">Preparation Before Boot</span>

You need do some preparation to get all target binaries before you booting system, and you are supposedly using Ubunt newer than 12.04 as your host OS. (But only Ubuntu 12.04 is validated)

###<span id="UEFI">Hardware & UEFI</span>

This step is not necessary unless you want ot update UEFI and trust firmware.

1. Check the hardware board

	Firstly, hardware board should be ready and checked carefully to make sure it is available, more detail information about different hardware board, please refer to <a href="/tags/Hardware-Boards/">Hardware Boards</a>

2. Update UEFI and trust firmware

	You can update UEFI and trust firmare yourself based on FTP service, More detail information about it, please refer to <a href="/tags/UEFI-and-Grub/">UEFI and Grub</a>

###<span id="build"> Get\Build Estuary yourself </span>

You can get and build the whole project to get all binaries yourself as follows.

**Firstly, to get the whole project as follows:**

    $mkdir -p ~/bin
    $curl "https://android-git.linaro.org/gitweb?p=tools/repo.git;a=blob_plain;f=repo;hb=refs/heads/stable" > ~/bin/repo
	
    $chmod a+x ~/bin/repo
    $echo 'export PATH=~/bin:$PATH' >> ~/.bashrc
    $export PATH=~/bin:$PATH

    $mkdir workdir
    $cd workdir 

    $repo init -u "https://github.com/hisilicon/estuary.git" -b refs/tags/estuary-v1.2 --no-repo-verify --repo-url=git://android.git.linaro.org/tools/repo
    $repo sync
If the repo sync fails during syncing, you can try it again.

**Secondly, to build the target system as follows:**
***Before building, you must change MAC address at 244 line in "arch/arm64/boot/dts/hisilicon/hip05_xge.dtsi" to adapt MAC address set in UEFI***

    $./estuary/build.sh -p D02 -d Ubuntu

Then you will get all target binaries in 'build' directory.

Following command will provide more useful help information about build script.

	$./estuary/build.sh -h

For more detail information about system obtaining and building, please refer to the **README** file in <a href="https://github.com/hisilicon/estuary">Estuary Source Code</a>

###<span id="kernel"> Only build kernel </span>

You can do as follows, if you only want to rebuild kernel.

1.Clone the latest kernel source code with following command.

    git clone https://github.com/hisilicon/linaro-kernel.git -b estuary

2.Install tool chain from <a href="http://releases.linaro.org/14.09/components/toolchain/">Linaro Tool Chain</a>

3.Build steps as follow:
```
   export ARCH=arm64
   export CROSS_COMPILE=aarch64-linux-gnu-
   make defconfig
   make -j16
   make ./hisilicon/hip05-d02.dtb
```
Then there are two files that have been created: Image in the directory **arch/arm64/boot/**, and hip05-d02.dtb in the directory **arch/arm64/boot/dts/hisilicon**

###<span id="binary"> Use binaries directly </span>

Anyway, before booting up system, all binaries should be avaiable firstly.

You can get them as above description, of course, you can also do a quick trying with all these existing binaries from <a href="/tags/Binary-Files/">Binary Files</a> directly.

##<span id="bringup">Bring up System</span>

There are several methods to bring up system, you can select following anyone fitting you to boot up.

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

    sudo apt-get install -y isc-dhcp-server syslinux

b.Edit /etc/dhcp/dhcpd.conf to suit your needs and particular configuration.Make sure filename is “grub2.efi”. Here is an example:

    $ cat /etc/dhcp/dhcpd.conf
    # Sample /etc/dhcpd.conf
    # (add your comments here)
    default-lease-time 600;
    max-lease-time 7200;
    subnet 192.168.1.0 netmask 255.255.255.0 {
        range 192.168.1.210 192.168.1.250;
        option subnet-mask 255.255.255.0;
        option domain-name-servers 192.168.1.1;
        option routers 192.168.1.1;
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.1.255;
        filename "grubaa64.efi";
        #next-server 192.168.1.107
    }
    #

c.Edit /etc/default/isc-dhcp-server to specify the interfaces dhcpd should listen to. By default it listens to eth0.

    INTERFACES="eth0"

d.Use these commands to start or check dhcp service

    sudo service isc-dhcp-server restart

Check status

    netstat -lu

Output

    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    udp        0      0 *:bootpc                *:*                                

2.Setup TFTP server on ubuntu
a.Install TFTP server and TFTP client(optional, tftp-hpa is the client package)

    sudo apt-get install -y topenbsd-inetd ftpd-hpa tftp-hpa lftp

b.Edit /etc/inetd.conf, remove #<off># from the beginning of tftp line or add if it not there under #:BOOT: comment.

    tftp    dgram   udp wait    root    /usr/sbin/in.tftpd  /usr/sbin/in.tftpd -s /var/lib/tftpboot

c.Enable boot service for inetd

    sudo update-inetd --enable BOOT

d.Configure the TFTP server, update /etc/default/tftpd-hpa like following:

    TFTP_USERNAME="tftp"
    TFTP_ADDRESS="0.0.0.0:69"
    TFTP_DIRECTORY="/var/lib/tftpboot"
    TFTP_OPTIONS="-l -c -s"

e.Set up TFTP server directory

    sudo mkdir /var/lib/tftpboot
    sudo chmod -R 777 /var/lib/tftpboot/

f.Restart inet & TFTP server

    sudo service openbsd-inetd restart
    sudo service tftpd-hpa restart

Check status

    netstat -lu

Output

    Proto Recv-Q Send-Q Local Address           Foreign Address         State 
    udp        0      0 *:tftp                  *:*                          

3.Prepare some files on the TFTP root path
    The files include:grub binary file, grub configure file,Image and dtb file. In my case, they are grubaa64_linux.efi, grub.cfg-01-xx-xx-xx-xx-xx-xx,hip05-d02.dtb and Image.

***Note:The grub configure file grub.cfg-01-xx-xx-xx-xx-xx-xx ends in D02's mac address.***

4.Reboot and enter UEFI boot menu
5.Select boot from PXE
After seral seconds,D02 will boot automaticlly.
The content of grub config file as follow:

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
    	linux /Image rdinit=/init console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
    	initrd /hulk-hip05.cpio.gz
    	devicetree /hip05-d02.dtb
	}

***Note: minilinux is the name of default PXE that will boot automatically.Image is the name of kernel that locates on root directory of TFTP.hip05-d02.dtb is the name of dtb file that locates on root directory of TFTP.hulk-hip05.cpio.gz is the name of filesystem that locates on root diretory of TFTP.And you can change the names and locations.***

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
              |-------------Image
              |
              |-------------hip05-d02.dtb
        sda2: ubuntu distribution
        sda3: OpenSUSE distribution
        sda4: miniDistribution
        sda5: reserved for user

***Note:The names of grub files archaa64.efi is the same name with filename item in DHCP configure file /etc/dhcp/dhcpd.conf.***

3.modify grub config file according to situation
This part we will build 6 different grub config.They are as follow:

	+-----------+-----------+----------------+------------------+
	| boot name |  kernel   |  distribution  |  dtb file        |
	+-----------+-----------+----------------+------------------+
	| ubuntu    |Image      |    ubuntu      |  hip05-d02.dtb   |
	+-----------+-----------+----------------+------------------+
	| opensuse  |Image      |    opensuse    |  hip05-d02.dtb   |
	+-----------+-----------+----------------+------------------+
	| mini      |Image      |    mini        |  hip05-d02.dtb   |
	+-----------+-----------+----------------+------------------+

And the context of grub.cfg file is as follow:

	#
	# Sample GRUB configuration file
	#
	# Boot automatically after 0 secs.
	set timeout=5
	# By default, boot the Euler/Linux
	set default=ubuntu
	# For booting GNU/Linux

	menuentry "ubuntu" --id ubuntu {
    	set root=(hd1,gpt1)
    	linux /Image rdinit=/init root=/dev/sda2 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
    	devicetree /hip05-d02.dtb
	}
	menuentry "opensuse" --id opensuse {
    	set root=(hd1,gpt1)
    	linux /Image rdinit=/init root=/dev/sda3 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
    	devicetree /hip05-d02.dtb
	}
	menuentry "mini" --id mini {
    	set root=(hd1,gpt1)
    	linux /Image rdinit=/init root=/dev/sda4 rootfstype=ext4 rw console=ttyS0,115200 earlycon=uart8250,mmio32,0x80300000
    	devicetree /hip05-d02.dtb
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
