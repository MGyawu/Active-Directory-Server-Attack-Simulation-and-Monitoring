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

## Specifications

Note: Adjust depending on what your computer can handle

### Splunk Server (AD-splunk)
OS: [Ubuntu 20.04 LTS](https://ubuntu.com/download/server)</p>
Splunk: [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html)</p>
Memory: 8 gbs (8192 mbs)</p>
Storage: 100 gbs</p>

### Active Directory Server (AD-server)
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

### Attacking PC (Pre-built Kali-linux VM)
OS: [Kali](https://www.kali.org/get-kali/#kali-virtual-machines)</p>
Memory: 2 gbs (2048 mbs)</p>
Storage: 80 gbs</p>



____________________________________________________________________________________________________________________________________________________________________________________________________________________
## 1. Network Configuration
It was important for this project to ensure that each virtual machine is on the name network and have internet access. In the virtualbox menu navigate from Tools > Network > NAT Network > select create. From here give the new network a name, I chose AD-network, give the IPV4 Prefix a value of 192.168.10.0/24, and finally select apply.

![AD Nat Network Config](AD-NatNetworkimage.png)

Now for each virtual machine, select settings > Network. Set "Attached to: " to "NAT Network" and then set "Name" to the network that you have chosen. Finally click "OK."

![Nat Network for each VM](AD-NatNewtorkSelectionForEachMachine.png)

## 2. Splunk Server (AD-splunk) Configurations and Splunk Installation

In order to collect logs of and monitor our simulated attack, we need to configure an instance of a an SIEM (secure information event manager) within a system that ingests logs from other devices within this network. Here, we will be configure an Ubuntu based Splunk server. After the initial setup my initial setup of my ubuntu server, I needed to give the server the static IP address that I specified in the diagram above. In order to do that, I entered this command into the Ubuntu terminal:

```
sudo nano /etc/netplan/50-cloud-init.yaml
```
Now that the file "50-cloud-init.yaml" is open, fill its contents with the following:

![Splunk server static IP](AD-splunkserverStaticIP1.png)

In order for the changes to this file to persist after a reboot, enter this command:

```
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

Now that "99-disable-network-config.cfg" is open, fill its contents with the following:

![Splunk server static IP pt2](AD-splunkserverStaticIP2.png)

In order to apply these changes enter this command:

```
sudo netplan apply
```

Note: if you want to check and see if the static IP has been correctly applied, enter this command:
```
ip a
```

The Next step is to install the guest addons for virtual box. In order to do so, I performed this command:
```
sudo apt-get install virtualbox-guest-additions-iso
```
I then typed "y" when prompted and hit "enter" and "enter" again once the the purple screen and menu appears. Now at the top of the server window (virtualbox menu at the top), use the mouse to navigate from Devices > Shared Folders > Shared Folder Settings. Now select the add folder button on the right side of the window. Under "Folder Path" search for and chose the path underwhich you stored the Splunk Enterprise download. Next provide a name for "Folder Name", I chose "Splunk", and check all of the boxes underneath.

![Add Splunk Folder](AD-splunkaddfolder.png)

Click "OK" twice and return to the server. Now reboot the virtual machine. This can be done by typing:

```
sudo reboot
```

Now is the time to add some virtual box installations that may have yet to be installed. Enter this command:

```
sudo apt-get install virtualbox-guest-utils
```

Now reboot one more time. After I logged in I added the user to the "vboxsf" group. This can be done by entering this command:

```
sudo adduser [enter your username here] vboxsf
```

Now to mount the shared folder to a directory to a folder in the server, I created a folder named "share". I then entered this command:
```
sudo mount -t vboxsf -o uid=1000,gid=1000 Splunk share/
```
I then changed directions into the share folder. You can see it in the share folder here:

![Splunk Mounted](AD-splunkmounted.png)

To perform my splunk install I entered this command:

```
sudo dpkg -i [splunk installer file]
```

Once I saw "complete" following this command, I changed to the /opt/splunk directory, which is where splunk is now located. To enter the splunk user and install splunk, I entered these commands:
```
sudo -u splunk bash
cd bin
./splunk start
```

I went to the end of the user aggreement and then entered "y" when I was prompted. I then entered and administrator username and password for my splunk instance. Afterward, I entered this command to ensure that every time I boot this server/virtual machine my splunk instance starts up:

```
exit
cd bin
sudo ./splunk enable boot-start -user splunk
```

## 3. Target PC (AD-demo) and Active Directory Server (AD-server) Configurations: Static IP addresses, Splunk Universal Forwarder, and Sysmon

## 4. Active Directory Server (AD-server) Configurations: Active Directory and Creating Users and Groups

## 5. Attack Simulation and Monitoring

____________________________________________________________________________________________________________________________________________________________________________________________________________________
## 1. Splunk Server (AD-splunk) Configurations and Splunk Installation

In order to collect logs of and monitor our simulated attack, we need to configure an instance of a an SIEM (secure information event manager) within a system that ingests logs from other devices within this network. Here, we will be configure an Ubuntu based Splunk server. Here are the links from where I acquired the Ubuntu OS and Splunk enterprise for this server:

OS: [Ubuntu 20.04 LTS](https://ubuntu.com/download/server)</p>
Splunk: [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html)</p>
Memory: 8 gbs (8192 mbs)</p>
Storage: 100 gbs</p>

After the initial setup my initial setup of my ubuntu server, I needed to give the server the static IP address that I specified in the diagram above. In order to do that, I entered this command into the Ubuntu terminal:

```
sudo nano /etc/netplan/50-cloud-init.yaml
```
Now that the file "50-cloud-init.yaml" is open, fill its contents with the following:

![Splunk server static IP](AD-splunkserverStaticIP1.png)

In order for the changes to this file to persist after a reboot, enter this command:

```
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

Now that "99-disable-network-config.cfg" is open, fill its contents with the following:

![Splunk server static IP pt2](AD-splunkserverStaticIP2.png)

In order to apply these changes enter this command:

```
sudo netplan apply
```

Note: if you want to check and see if the static IP has been correctly applied, enter this command:
```
ip a
```

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
