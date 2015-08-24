title: Estuary QuickStart Guide
date: 2015-08-10 10:16:36
tags: Quick Start
---
Firstly,Download Estuary source code from GitHub.
About how to get Estuary source code, please refer to : 
https://github.com/hisilicon/estuary/blob/master/README

Create USB installation disk deploying Linux system into Hard Disk of D02 board
1. Plug USB Disk to ubuntu machine
Execute these command:
cd xxx/estuary
./create-udisk.sh
Afterwards, input selection according to prompt information.
At last, USB intallation disk may be created successfully.
3. Plug USB Disk to D02 board
Switch on the D02 board, select USB startup selection in UEFI shell, and sys_setup.sh will be execute after ubuntu is started up.
Afterwards, input selection according to prompt information.
At last, the Hard Disk of D02 board may be deployed successfully.
4. Switch off the D02 board
Unplug USB Disk, switch on the D02 board again, select Hard Disk startup selection in UEFI shell, and linux system can be started up from Hard Disk.
