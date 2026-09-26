# Active-Directory-Home-Lab

## Overview
In this project, I aim to document setting up a hom lab using virtual machines.  Then I will perform attacks on the servers and also learn to anaylse/monitor attacks using seim tools.  I will also document my learning of how the vulnerabilities are exploited, and how to spot the attacks in the seim tool.

Credits to Youtuber MyDFIR, this project mostly followed his tutorial Active Directory Project(Home Lab) to set up this project.\
You can find his series following this link:
[Active Directory Project(Home Lab)](https://www.youtube.com/playlist?list=PLGrcVHQv6mp-_5jY1XU1SJ3tjoUMrTI7E)

### Technologies
OS: Windows 10, Kali Linux, Windows Server 2022, Ubuntu Server 22.04.5\
Hypervisor: Virtual Box\
Tools: Sysmon, Splunk
### Learning Objectives
- [x] Learning how to create, configure, and manage VMs using a hypervisor
- [ ] Learning how to configure and manage an Active Directory server
- [ ] Learning how to configure and manage a Splunk server (Ubuntu Server)
### Network Design
<br>
<img src="assets/images/AD-Network-Diagram.png" alt="network diagram for this project" width="50%"/>
<br>

## Set-up
### Creating and Configuring VMs
In this project, I used Virtual Box as my hypervisor to host my VMs.\
<br>

#### Steps for hosting a VM in Virtual Box:
1. Prepare an ISO file of the OS you want to run
1. Press "New"
1. Name the machine, and select the ISO file to boot from ( check 'Skip Unattended Installation' if you want to manually run through the OS setup wizard )\
    <img src="assets/images/VM-naming.png" alt="the naming section for creating a VM on Virtual Box" width="50%"/>
1. Set the amount of memory (RAM), numbers of maximum CPU used, and amount of virtual hard disk space to allocate to the VM\
    <img src="assets/images/VM-memory.png" alt="the memory and cpu section for creating a VM on Virtual Box" width="50%"/>
    <img src="assets/images/VM-harddisk.png" alt="the virtual harddisk section for creating a VM on Virtual Box" width="50%"/>
1. Spin up the VM and install the desired OS 
<br>
<br>


#### Setting up NAT Network Subnet:
*It is important to use a NAT Network subnet to allow the VMs to discover and communicate with each other, while keeping them isolated from the home network
1. On the left sidebar, select Network
1. Select NAT Networks and click create
1. Name the network, enter the IPv4 prefix (or IPv6, but in this project, IPv4 is used), and choose to enable DHCP or not.  After finished, click apply\
    <img src="assets/images/NAT-naming.png" alt="the naming section for creating a VM on Virtual Box" width="50%"/>
1. Go back to the machines page, and for each machine, do the following steps
1. For the "attached to" select NAT Network, and for the "name", select the name you have created.\
    <img src="assets/images/NAT-selection.png" alt="the naming section for creating a VM on Virtual Box" width="50%"/>



### Setting up Splunk Server
#### Setting up a staic IP:

*While I was following the tutorial, I found that in my setup, there is no "00-installer-config.yaml" file. Instead there is a "50-cloud-init.yaml" file in where that file should be.\
After researching, I found out that it is due to the cloud-init package that modern ubuntu includes in their ISO files by default.  In my lab enviroment, it would overwrite my netplan settings on every reboot and convert it back to DHCP(dynamic IP).  To work around this, I researched that I have to disable cloud-init's netplan management so it does not actively overwrite the "50-cloud-init.yaml" on reboot, and then write the correct network information into the file.\
You can read more about cloud-init in the [official documentation](https://docs.cloud-init.io/en/latest/)

*During further research, I found out it might be better practice to delete the "50-cloud-init.yaml" file and write a new netplan file to ensure clarity

1. In bash, use the following command to create a configuration file that disables the network management of the cloud-init package:\
    *sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg*
1. In nano, add the following line to the file, then save and exit (ctrl+o, ctrl+x):\
    *network: {config: disabled}*
1. In bash, use the following command to delete the "50-cloud-init.yaml" file:\
    *sudo rm /etc/netplan/50-cloud-init.yaml*
1. In bash, use the following command to create a new netplan configuration file:\
    *sudo nano /etc/netplan/x-file-name.yaml*
1. In nano, edit the file to fit the following structure(replace the "addresses" and "default" with the desired IP addresses), then save and exit:\
    <img src="assets/images/netplan-layout.png" alt="showing the format of the netplan file" width="50%"/>
1. In bash, use the following commands to apply the netplan configuration:\
    *sudo netplan try*\
    *sudo netplan apply*
1. In bash, use *ip a* to confirm the static ip is applied and use *ping* to confirm connection is working 

#### Setting up a shared directory on the VM

*You need to setup a shared directory to load the splunk .deb file into the VM

1. Create a directory for the VM in the host machine
1. In bash, use the following commands to install the Guest Additions:\
    *this enables native shared folders(vboxsf) for the VM, and also other functionalities, learn more in the [official documentation](https://www.virtualbox.org/manual/ch04.html)\
    *sudo apt-get install virtualbox-guest-additions-iso*\
    *sudo apt-get install virtualbox-guest-utils*
1. In bash, use the following command to reboot the VM to apply the changes:\
    *sudo reboot*
1. In bash, use the following command to add youself to the vboxsf group:\
    *sudo adduser [your username] vboxsf*
1. In bash, use the following command to make a new directory called share:\
    *mkdir share*
1. In bash, use the following command to mount the created directory(host) to the VM share folder:\
    *sudo mount -t vboxsf -o uid=1000,gid=1000 [your directory name] share/*

#### Installing Splunk
1. Register an account on the Splunk website, and download the Enterprise Linux .deb file to the shared folder
1. In bash, cd into the folder and cd into /opt/splunk
1. In bash, use the following command to Change into the "splunk" user:\
    *sudo -u splunk bash*
1. In bash, cd into the bin directory and us the following binary to start the install:\
    *./splunk start*
1. After installation is complete, use *exit* to exit the "splunk" user bash.
1. In bash, cd into bin and use the following command to make splunk automatically run on boot as user "splunk":\
    *sudo ./splunk enable boot-start -user splunk*
