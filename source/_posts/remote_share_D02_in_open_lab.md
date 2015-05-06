title: remote share D02 in open lab
date: 2015-04-24 11:50:00
tags: D02
---
#Remote using D02 in HTSAT open lab

This is a document about how to remote use the D02 board which located in HTSAT open lab. We have deployed two baords in open lab to share with you, if you are interested in D02 development,please don't hesitate to contact with [alan](mailto:huangdaode@hisilicon.com "huangdaode@hisilicon.com") to get your own account.

*For local environment configuration and board hacking , please refer to [D02-hacking-manual](http://hisilicon.github.io/2015/02/10/D02-hacking-manual/ "d02-hacking").*

## Part 1 Use the ssh protocol to transfer image file
For this part, we assume that you are using the Ubuntu system.
### Configure the SSH client
Open the ssh config file (~/.ssh/config),if there is no config file, please create it, append the context below:

	host htsat.vicp.cc
 	hostname htsat.vicp.cc
	port 222

### Login the server 
#### Through ssh protocol
Use command below to login the server.

    ssh XXXX@htsat.vicp.cc
    
The default password for all account is *"123456"*. For security, you must change the password the first time you log in.Once logged in the system, you will see a grub config file named *"config"* and a ftp directory named *"ftp"* which is used for store kernel Image, dtb file and nfs_rootfs. Both are necessary, if there is no, please contact the administrator. Most of time you need not to change the config file, because the environment is stable. So what you will do is to put your kernel image or other file to the ftp directory  
***NOTE: XXX is your account that is assigned by the administrator.***

#### Through web browser
In some case, the ssh port may be not permited to access,such as inside huawei, then you can accessthe board through web browser (recommend chrome browser). Input the following address.

	http://htsat.vicp.cc:803

### Put file to the ftp 
Open another terminal and go to the directory where your build image is located. Use the command below to put the image file or other necessary files to the ftp directory.

    scp Image XXXX@htsat.vicp.cc:~/ftp/
    scp hip05-d02.dtb XXXX@htsat.vicp.cc:~/ftp/

### Edit the grub confige file 
As you log in the host through ssh, the system will create a default config file(grub.conf) for you.Once use the command board_connect successfully, it will replace the board's grub.cfg that the "config" file  linked to. Therefore, you can edit the grub.conf file to add you own information.
    
    vim grub.conf
    
***NOTE: XXX is your account that is assigned by the administrator.***
## Part 2 Access the board
Now it very convenient for you to connect the board. As you have logged in the host, so just type the command below to connect board.

    board_connect [board]

Board option can be d01 or d02. So command "board_connect d02" will connect to d02 board. Default will connect d02. For more information about the command, please try "board_connect -h" command to show details.
## Part 3 Reboot the board 
We have deployed pdu to power the board, so it's convinent to remote reboot the board in open lab. use the command below to reboot the board you are using.

	board_reboot [board] [cmd]
	    
Board option can be d01 or d02, cmd option can be on、off or reboot, therefore command "board_reboot d02 reboot" will reboot the d02 board that you are using. Default no option will reboot d02 board. 
## Part 4 For provison method to boot the board
If you want to try provision boot the board, the FTP account and password is the same with your login account! The root directory is user's home directory, so specify relative path by ftp/XXXXX.bin, an example is:

    provision 192.168.1.107 -u username -p password -f ftp/XXXX.bin -a 100000

    
## Part 5 Attentions and FAQ
1) If you want to use the board, you should first check that whether other members in the same group using the board,if not, ok ,go ahead to use it.

2) After finish using the board, we suggest you to quit the telnet connection as well as the ssh connection.

*press "ctrl+]" and input "quit" to quit the telnet connection*

3) If there is no activity on the telnet connection in 10 minitues, the connection will disconnect.

4)  I upload an Image into the ~/ftp/ directory, but it fails to boot up, how I verify whether the system is OK?
A: For this issue, first disconnect the board. then remove the Image and dtb file in you ftp directory. Connect the board again, the system will use the backup Image and dtb to boot the board.
5) For detail information about D02, please connect with [Alan](mailto:huangdaode@hisilicon.com "huangdaode@hisilicon.com").
