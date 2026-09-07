# Active-Directory-Home-Lab

## Overview
In this project, I aim to set up a hom lab using virtual machines.  Then I will perform attacks on the servers and also learn to anaylse/monitor attacks using seim tools.  I will document my learning of how the vulnerabilities are exploited, and how to spot the attacks in the seim tool.
### Technologies
OS: Windows 10, Kali Linux, Windows Server 2022, Ubuntu Server 22.04.5\
Hypervisor: Virtual Box\
Tools: Sysmon, Splunk
### Learning Objectives
- [ ] Learning how to create, configure, and manage VMs using a hypervisor
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
Steps for hosting a VM in Virtual Box:
1. Prepare an ISO file of the OS you want to run
1. Press "New"
1. Name the machine, and select the ISO file to boot from ( check 'Skip Unattended Installation' if you want to manually run through the OS setup wizard )\
<img src="assets/images/VM-naming.png" alt="the naming section for creating a VM on Virtual Box" width="50%"/>
1. Set the amount of memory (RAM), numbers of maximum CPU used, and amount of virtual hard disk space to allocate to the VM\
<img src="assets/images/VM-memory.png" alt="the memory and cpu section for creating a VM on Virtual Box" width="50%"/>
<img src="assets/images/VM-harddisk.png" alt="the virtual harddisk section for creating a VM on Virtual Box" width="50%"/>
1. Spin up the VM and install the desired OS 


### Setting up Splunk Server
