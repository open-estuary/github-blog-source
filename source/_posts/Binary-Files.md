title: Binary Files
date: 2015-08-10 10:15:39
tags: Binary Files
---

You can have a quick trying with all binaries, which can be downloaded from following link:

	https://github.com/hisilicon/estuary/releases

Here, you can click the 'Latest release' on left of page firstly, then download any files in the version as you like.

	e.g.: wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/Image

All binaries and their descriptions are listed as follows, how to use these binaries, please refer to <a href="/tags/System-Hacking/">System Hacking</a>:

    +----------------------------+------------------------------------+       
    |  FILE NAME                 |    DESCRIPTION                     |
    +----------------------------+------------------------------------+    
    |  UEFI_Release.bin          |    UEFI binary                     |
    +----------------------------+------------------------------------+
    |  CH02_TEVBC_V03.bin        |    CPLD binary, power controll     |
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

###<span id="function"> The function of each component</span>
1.<span id="UEFI">UEFI</span>:responsible for loading and booting Image and dtb file,and you could download binary file from:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/UEFI_Release.bin 

2.<span id="GRUB">GRUB</span>:grub configure file and grub image file for D02,and you could download binary file from:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/grub.cfg
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/grubaa64.efi

3.<span id="kernel">Kernel</span>:the operation system that D02 runs on,and you could binary files from following website:

	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/Image
	wget -c https://github.com/hisilicon/estuary/releases/download/bin-v1.2/hip05-d02.dtb

4.<span id="dist">Linux Distribution</span>: Because the distribution's Images are too large, all of them are storaged into another place. The validated distributions for this project's boards can be downloaded from following website, the default user name and passowrd of distributions are: ubuntu, ubuntu.

	Ubuntu:     wget -c http://7xjz0v.com1.z0.glb.clouddn.com/dist/ubuntu-vivid.img.tar.gz
	OpenSUSE:	TBD
	Fedora:		TBD
	Redhat:		TBD
	Debian:		TBD
	OpenEmbedded: TBD
All these validated distributions can be delpoyed to target partition directly with following example command.

	dd if=ubuntu-vivid.img of=/dev/sda2

All original distributions without validation for this project's boards can be downloaded from follows:

	Ubuntu:		wget -c https://cloud-images.ubuntu.com/vivid/current/vivid-server-cloudimg-arm64.tar.gz
	OpenSUSE:   wget -c http://download.opensuse.org/ports/aarch64/distribution/13.2/appliances/openSUSE-13.2-ARM-JeOS.aarch64-rootfs.aarch64-Current.tbz
	Fedora:     wget -c http://dmarlin.fedorapeople.org/fedora-arm/aarch64/F21-20140407-foundation-v8.tar.xz
	Redhat:     TBD
	Debian:     wget -c http://people.debian.org/~wookey/bootstrap/rootfs/debian-unstable-arm64.tar.gz
	OpenEmbedded: wget -c http://releases.linaro.org/14.06/openembedded/aarch64/vexpress64-openembedded_minimal-armv8-gcc-4.8_20140623-668.img.gz

5.<span id="toolchain">Toolchain</span>:used to compile and debug some above files, and now you could download it from following website:

    wget -c http://releases.linaro.org/14.09/components/toolchain/binaries/gcc-linaro-aarch64-linux-gnu-4.9-2014.09_linux.tar.bz2
	
***Note: By default, all distributions and tool chains are compressed, you should decompress them firstly before you use them.***
