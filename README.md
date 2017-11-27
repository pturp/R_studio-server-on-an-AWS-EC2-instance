# R_studio-server-on-an-AWS-EC2-instance
## Context: Installation of both R software and RStudio-server on an Linux EC2 instance
### Objective: using Rstudio from a remote Web browser on AWS.
#### Needs:
An ec2 instance . 
Port 8787 used by Rstudio-server needs to be accessible through internet by a Web browser      


## Setup of the AWS EC2 instance:
### Choice of a region and Availability Zone:
EU West (Ireland)  
eu-west-1a  

### Choice of an Amazon Machine Image, configuration
___
Amazon Linux AMI 2017.09.1 (HVM), SSD Volume Type - ami-1a962263

Instance type: t2.micro (free)

Enable termination protection checked for avoiding termination 

### User data (will be executed when launching):
( Rstudio-server for RedHat/CentOS 6 and 7 --> it works fine in an EC2 )
___
```
#!/bin/bash
#install R
yum install -y R

#install RStudio-Server 1.0.183 (2017-07-20)
wget ://download2.rstudio.org/rstudio-server-rhel-1.1.183-x86_64.rpm
yum install -y --nogpgcheck rstudio-server-rhel-1.0.183-x86_64.rpm

#add user(s) to be able to connect from a Browser 
#(rstudio-server will prompt you for a username/password
#on your browser 
useradd rstudio
echo rstudio:rstudio | chpasswd 
```
### Add storage:
___
Uncheck 'delete on termination'

### Add tags:
___
Key-pair value: 
Name  R_RStudioServ

### Configure Security Group
___
This will allow 8787 port used by Rstudio-server to be open  
Will allow ONLY connections from YOUR Ip address to connect to RStudio-server  
add tcp 8787 source: My Ip  
Change ssh 22 source: My Ip  

When launching you have the choice between reusing a previous public-private security file 
or creating a brand new one...  
However keep it safely . 
Wait a few minutes and test your connection via Putty or Macos terminal ssh (here Macos terminal where you put your pem public/private security file:  
Here "LinuxAccess.pem" is the name of the pem file you saved on your computer.       
ssh -i "LinuxAccess.pem" ec2-user@XXXXXXXXXXX.eu-west-1.compute.amazonaws.com

### Checking installation and troubleshooting:
___
more /var/log/cloud-init-output.log .  
We can see that due to a typo the Rstudio-server has not been installed   
>Discrepancy ://download2.rstudio.org/rstudio-server-rhel-1.1.183-x86_64.rpm and rstudio-server-rhel-1.0.183-x86_64.rpm .   
>Loaded plugins: priorities, update-motd, upgrade-helper
>Existing lock /var/run/yum.pid: another copy is running as pid 4779.
>Another app is currently holding the yum lock; waiting for it to exit...
>  The other application is: yum
>    Memory :  39 M RSS (286 MB VSZ)
>    Started: Sat Nov 25 11:44:55 2017 - 00:06 ago
>    State  : Running, pid: 4779
>No package rstudio-server-rhel-1.0.183-x86_64.rpm available

So need to install it by hand:      
```sudo wget https://download2.rstudio.org/rstudio-server-rhel-1.1.383-x86_64.rpm```
Launching Rstudio-server:
```sudo yum install --nogpgcheck rstudio-server-rhel-1.1.383-x86_64.rpm```

### Checking Rstudio-server is started:
___
```ps -edf|grep -i 'rstudio'```
Should get something like that:    
498       6829     1  0 12:12 ?        00:00:00 /usr/lib/rstudio-server/bin/rserver

Checking we can access Rstudio-server from a browser on your computer:    
**http://xxxxxxxxxx.eu-west-1.compute.amazonaws.com:8787/**

And you are prompt for entering username/password: input the one you setup in the User Data.  
Enjoy!





