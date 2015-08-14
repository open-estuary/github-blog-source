title: Overview Introduction
date: 2015-08-10 10:24:54
tags: Overview Introduction
---
Estuary is a complete open source project for ARM64 ecosystem, it tries to provide a total solution for universal-purpose computer based on aarch64 architecture, so that anyone can quickly get and bring up an ARM64-based software\hardware computer system. Maybe you are a user to ARM64 computer, a community developer based on ARM64 computer, a student or scholar working on ARM64 computer in university... anyway, nomatter whoever you are, wherever you are in, so long as you want to work\develop\debug\estimate\validate on an ARM64 computer\solution, you can quickly get this solution and bring up the whole system from here. 

The main targets of Estuary are:
1. A stable\available quick start solution based on ARM64 computer, so that anyone can quickly and easily get and bring up it, then only focus on their own part.
2. A common basic platform, which should be sustainable and continuously improved, can attract\absorb and integrate all 3rd achievement ceaselessly.
3. Sufficient technological documentation, so that user\partner can easily get useful help from it.
4. A popular BBS for ARM64 ecosystem, so that anyone can comunicate about ARM64 ecosystem each other here.
5. A bug report\track system, so that anyone can raise\track and resolve the bugs about this solution.
6. An universal ARM64 basic platform, so that more different hardware board based on ARM64 will be supported here.
All above targets are to consolidate ARM64 ecosystem, quicken the maturity of ARM64 ecosytem.

The Estuary total overview architecture is shown as following picture:

<img src="http://7xjz0v.com1.z0.glb.clouddn.com/EstuaryArch.png" style="width:700px;height:500px;margin-left:30px"/>

There are total 7 levels and some peripheral tools, they are introduced one by one as follows.
1. Hardware boards:
	A variety of boards based on ARM64 SoC will be enabled and integrated by Estuary, no matter which company produce it, so long as the board is designed based on ARM64 SoC, all of them will be encouraged to be integrated into this solution.
	Now we can support D01, D02 boards, more detail board's information, please refer to <a href="/tags/Hardware-Boards/">Hardware Boards</a>

2. UEFI, Grub & bootloader:
	Multi-kinds of boot loader will be enabled for Estuary, include but not limited to UEFI\Grub, uboot.
	UEFI source code: https://github.com/hisilicon/UEFI.git
	Grub source code: http://git.savannah.gnu.org/cgit/grub.git
	
	More detail about UEFI, please refer to <a href="/tags/UEFI-and-Grub/">UEFI and Grub</a>

3. Operation System:
	Two main kinds of OS will be enabled, one is based on Windows, another is based on Linux.
	For Linux OS, not only the latest kernel will be enabled for Estuary, various distributions will be eanbled for it too, include but not limited to Ubuntu, Fedora, OpenEmbedded, Debian, Redhat and etc.
	
	Kernel source code can be gotten as follow:

    git clone https://github.com/hisilicon/linaro-kernel -b estuary

	More detail about kernel, please refer to <a href="https://github.com/hisilicon/estuary/blob/master/README">Estuary Readme</a>

    For the distributions, they can be gotten from <a href="/tags/Binary-Files/">Binary Files</a>.

4. Basic Components:
	Some basic components will be eanbled for Estuary, e.g.: <a href="https://www.mysql.com/">MySQL</a>, <a href="https://mariadb.org/">MariaDB</a>, <a href="http://www.postgresql.org/">PostgresSQL</a>, <a href="https://www.python.org/">Python</a>, <a href="http://www.opendataplane.org/">ODP</a>, <a href="http://openjdk.java.net/">OpenJDK</a> and etc.

5. Virtualization Technologies:
	Multi-kinds of virtualization technologies will also be enabled for Estuary, e.g.: <a href="https://www.docker.com/">Docker</a>\<a href="https://linuxcontainers.org/">Lxc</a>, <a href="http://www.linux-kvm.org/page/Main_Page">KVM</a>, <a href="http://www.xenproject.org/">Xen</a>, <a href="http://wiki.qemu.org/Main_Page">Qemu</a>, <a href="https://www.openstack.org/">OpenStack</a>, <a href="http://www.vmware.com">Vmware</a> or <a href="https://www.cloudfoundry.org/">CloudFoundry</a> and so on.

6. Cloud Platform Service:
	In order to work as a universal server based on ARM64 machine, some common cloud platform services are necessary. e.g.: <a href="http://ceph.com/">Ceph</a>, <a href="https://hadoop.apache.org/">Hadoop</a>, <a href="http://httpd.apache.org/">Apache</a>, Spark, Swift and so on.

7. Typical Applications:
	To use easily, some typical applications should be enabled and estimated too. e.g: Web server, Cloud machine, Big data retrieve and etc.

8. Peripheral Tools:
	Good peripheral support tools are very important factors too for a total solution, in this project, they are:
	A. Quick Start Solution: Anyone can quickly get and bring up the system step by step easily. More detail refer to <a href="/tags/Quick-Start/">Quick Start</a>.
	B. Tool Chain: Cross complier and different compilers. More detail refer to <a href="http://releases.linaro.org/14.09/components/toolchain/">Tool Chains</a>.
	C. Benchmarking: To test and estimate the system maturity and performance. More detail refer to <a href="/tags/Caliper-for-Benchmarking/">Caliper Benchmark</a>.
	D. Open Board Laboratory: To provide a way to remote access, debug hardware board. More detail refer to <a href="/tags/Shared-Boards-in-Open-Lab/">Shared Boards in Open Lab</a>.
	E. Website: Just this website, news publish, technological communication, customer support, documentation release, mail list service, wiki and etc.
	F. Armor Tools: Provide more useful tools used on debug, diagnoses, analyze of system. e.g.: perf, strace and etc. More detail refer to <a href="/tags/Armor-Tools/">Armor Tools</a>.
	G. Bug system: Bug report, assign, track system. More detail please refer to <a href="https://github.com/hisilicon/hisilicon.github.io/issues/">Issue Report</a>
    H. LAVA system: Automated test and job validation system. More detail please refer to <a href="http://htsat.vicp.cc:800/">LAVA</a>.

In above picture, green block with yellow border means they will be enabled defaultly by quick start, green block means all these functions will be implemented in Estuary last version, grey block means options which will be implemented finally if there are enough resource and time.

To get Quick Start for Estuary project, please refer to <a href="/tags/Quick-Start/">Quick Start</a>.
To get binary files for a quick validation, please refer to <a href="/tags/Binary-Files/">Binary Files</a>.
To get complete methods to go through whole project about how to get\build\bring up system, please refer to <a href="/tags/Go-Through/">Go Through</a>.
To get all source code of Estuary, please refer to <a href="https://github.com/hisilicon/estuary/">Source Code</a>.
