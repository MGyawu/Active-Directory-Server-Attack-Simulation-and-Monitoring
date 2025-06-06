# Active-Directory-Brute-Force-Attack-Simulation-and-Monitoring
## Introduction

At the center of this project is a virtual environment meant to represent a real-world network
through a collection of virtual machines. Users and permissions are provided within an Active
Directory server, logs are sent to a separate Splunk server, a Windows 10 VM serves as the
target PC (the PC through which users log in), and a kali linux VM is a lone PC. This
environment is illustrated in the diagram below:

![Diagram](ADProject.png)

The purpose of this project is to create users are created within the Active Directory server, the kali linux VM attacks and gains access to those user accounts, and to identify and observe those attacks through logs sent to a Splunk server. 

Note: All of the virtual machines used in this project was made using Virtualbox as a hypervisor


## 1. Splunk Server (ADsplunk) Configurations

In order to collect logs of and monitor our simulated attack, we need to configure an instance of a an SIEM (secure information event manager) within a system that ingests logs from other devices within this network. Here, we will be configure an Ubuntu based Splunk server. Here are the links from where I acquired the Ubuntu OS and Splunk enterprise for this server:

OS: [Ubuntu 20.04 LTS](https://ubuntu.com/download/server)</p>
Splunk: [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html)</p>
Memory: 8 gbs (8192 mbs)</p>
Storage: 100 gbs</p>

After the initial setup my initial setup of my ubuntu server, I needed to give the server the static IP address that I specified in the diagram above. In order to do that, I entered this command into the Ubuntu terminal:

```
sudo nano /etc/netplan/50-cloud-init.yaml
```

![Splunk server static IP]()

## 2. Network Configuration in VirtualBox
It was important for this project to ensure that each virtual machine is on the name network and have internet access. In the virtualbox menu navigate from Tools > Network > NAT Network > select create. From here give the new network a name, I chose AD-network, give the IPV4 Prefix a value of 192.168.10.0/24, and finally select apply.

![AD Nat Network Config](AD-NatNetworkimage.png)

Now for each virtual machine, select settings > Network. Set "Attached to: " to "NAT Network" and then set "Name" to the network that you have chosen. Finally click "OK."

![Nat Network for each VM](AD-NatNewtorkSelectionForEachMachine.png)

###

### Active Directory Server (ADserver)
OS: [Windows Server 2022](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022)</p>
Memory: 4 gbs(4096 mbs)</p>
Storage: 50 gbs</p>
Sysmon: [Sysmon monitoring system](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) </p>
[Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder.html)</p>


### Target PC (ADdemo)
OS:  [Windows 10](https://www.microsoft.com/en-us/software-download/windows10)</p>
Memory: 6 gbs (6114 mbs)</p>
Storage: 50 gbs</p>
Sysmon: [Sysmon monitoring system](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)</p>
[Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder.html)</p>



### Attacking PC (Kali-linux VM)
OS: [Kali](https://www.kali.org/get-kali/#kali-virtual-machines)</p>
Memory: 2 gbs (2048 mbs)</p>
Storage: 80 gbs</p>



## Collecting Telemetry
## Performing Attacks
