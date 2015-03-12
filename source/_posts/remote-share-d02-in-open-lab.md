title: remote_share_d02_in_open_lab
date: 2015-03-12 10:56:59
tags: D02 remote_sharing
---
#Guideline of remote using D02 located in HTSAT open lab
This is the document about how to remote use the D02 board located in HTSAT open lab. For local environment configure and board hacking , please refer to [D02-hacking-manual](http://hisilicon.github.io/2015/02/10/D02-hacking-manual/ "d02-hacking").
## Work requirement
* FTP client 
* SSH client
* FTP account (get from the administartor)

## Part 1 Configure and push the binary image file to the server

### use the ssh to connect the host and scp to transfer file

#### Configure the SSH client
open the ssh config file (~/.ssh/config), add the content below:

	host htsat.vicp.cc
 	hostname htsat.vicp.cc
	port 221

#### a) use scp to transfer the image file to the tftp server

    scp Image hisilicon@htsat.vicp.cc:~/ftp/XXX
    
you will be asked to give the user hisilicon's passord, the administrator will provide it.

**Note: XXX is the ftp user directory, the administator will give you the directory name**

#### b) use FTP to transfer the image file to the tftp server
	ftp htsat.vicp.cc
the system will ask you to input the name and password, you can get them from the administrator.
## Part 2 Access the board
### Login in the control host through ssh
    ssh hisilicon@htsat.vicp.cc

you will be asked to give the user hisilicon's passord same as above.  
### connect the d02 board
	connect XXX

**Note: XXX is your name, and the system is automatic connect the board according the name.**
## Part 3 Reboot the board 
we have deployed pdu to power the board, so it's convinent to remote reboot the board in open lab. use the command below to reboot the board you are using.

	board_reboot XXX
	    
**Note: XXX is your name, and the system is automatic reboot the board according the name.**
## Part 4 Attentions
1) If you want to use the board, you should first check that whether other members in the same group using the board,if not, ok ,go ahead to use it.

2) After finish using the board, we suggest you to quit the telnet connection as well as the ssh connection.

*press ctrl+] and type quit to quit the telnet connection*

*type exit to exit the ssh connection*  

3) If there is on activity on the telnet connection in 10 minitues, the connection will disconnect.

4) For detail information about D02, please connect with [Alan](mailto:huangdaode@hisilicon.com "huangdaode@hisilicon.com").
