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
Sysmon Configuration: [olafhartong Sysmon Configuration](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)</p>
[Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder.html)</p>

### Target PC (ADdemo)
OS:  [Windows 10](https://www.microsoft.com/en-us/software-download/windows10)</p>
Memory: 6 gbs (6114 mbs)</p>
Storage: 50 gbs</p>
Sysmon: [Sysmon monitoring system](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)</p>
Sysmon Configuration: [olafhartong Sysmon Configuration](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml)</p>
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

Before installing these components I made sure to the change the name of AD-demo to "target-PC" and AD-server to "ADserver" from the windows menu, this way these are the names of the hosts that appear in the Splunk logs. This can be done by Navigating Windows menu > Type PC and select properties (in the windows server you may have to right click PC to get this option) > select "Rename PC". I then entered the new name and restarted these machines once prompted.

Now, in order to give these devices their static IP addresses I navigated to the the network icon and right clicked it > Open Network & Internet Settings > Change Adapter Options > Right Click the Adapter and select properties > doublie click Internet Protocol Version 4 (TCP/IPv4) > Select "Use the following IP address" and "Use the following DNS server address". I then entred these values:</p>

|Target-PC|AD-server|
|---------|---------|
|![AD-demoStaticIP](AD-demoStaticIP.png)|![AD-serverStaticIP](AD-serverStaticIP.png)|

Next I installed Splunk Universal Forwarder. The link I showed above is the link to the Splunk Universal Forwarder installer, and I downloaded the installer from a web browser within my Target-PC and AD-server. Once the download was completed, I ran the installer, checked off the box that stated "Check this box to accept the License Agreement", selected "An on-premises Splunk Enterprise instance", and then selected "Next".

![AD-DemoAndServerSplunkForwarder1](AD-DemoAndServerSplunkForwarder1.png)

On the next page, I entered "admin" as the user name then selected "Next" twice, skipping over the Deployment Server as I did not have one. Under the Receiving Indexer, I entered the Splunk Server IP address under "Hostname of IP", which is 192.168.10.10, and for the default port I entered 9997.

![AD-DemoAndServerSplunkForwarder2](AD-DemoAndServerSplunkForwarder2.png)

Following this, I navigated Next > Install. With this install continuing in the background, I then went on to download Sysmon and retrieve olafhartong's Sysmon configuration, links for the webpages for both above. From olafhartong's page I selected "Raw", and, once the new page loaded, I right clicked > clicked "Save as" > saved into Downloads.

![AD-DemoAndServerSysmonConfig](AD-DemoAndServerSysmonConfig.png)

From this point I extracted the orginal downloaded Sysmon zip file > copied the file path > ran Powershell as Administrator and entered this command
```
cd [File path of Sysmon]
.\Sysmon64.exe -i ..\sysmonconfig.xml
```

This is how mine looked:

![AD-DemoAndServerDownloadSysmon1](AD-DemoAndServerDownloadSysmon1.png)

After running this command a pop-up titled "System Monitor License Agreement" with info titled "SYSINTERNALS SOFTWARE LICENSE TERMS" appeared. Once I clicked agree, Sysmon will now download onto your system and begin.

![AD-DemoAndServerDownloadSysmon2](AD-DemoAndServerDownloadSysmon2.png)

Once I saw the message "Sysmon64 started", I know the download was complete. By this point the Splunk Universal Forwarder also finished its installation, and with that, I opened the file explorer and navigated down this path: This PC > Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > local. From here I ran Notepad as administrator and created the file "inputs.conf" that contains this:

![AD-DemoAndServerInputs](AD-DemoAndServerInputs.png)

This is vital for instructing the Splunk forwarder on what to send to the Splunk server. This specifially instructs the forwarder to forward information related to application, security, system, and sysmon to the Splunk server. Also, the index named "endpoint" specified in this file will need a corresponding index named endpoint created in order to receive these events. I create that later when I access Splunk enterprise. </p>

Due to the fact that I created this "inputs.conf" file, I then followed that up by restarting the Splunk Forwarder Service. This action is necessary whenever inputs.conf is updated or changed. In order to do so, from the windows search bar at the bottom left I searched for the Services application > right clicked and chose Run as Administrator > Found "SplunkForwarder" among the list of applications and double clicked it > navigated to Log On > selected "Local System account" > click Apply and OK > Right click SplunkForwarder in services > select Restart.

![AD-DemoAndServerSplunkRestart1](AD-DemoAndServerSplunkRestart1.png)
![AD-DemoAndServerSplunkRestart2](AD-DemoAndServerSplunkRestart2.png)
![AD-DemoAndServerSplunkRestart3](AD-DemoAndServerSplunkRestart3.png)


I then received a pop-up message stating "Windows could not stop the SplunkForwarder service on Local Computer". However, I clicked OK > selected SplunkForwarder > clicked Start at the top left of the "Services (Local)" tab, and this forwarder was still functional.

![AD-DemoAndServerSplunkRestart4](AD-DemoAndServerSplunkRestart4.png)



## 4. Active Directory Server (AD-server) Configurations: Active Directory and Creating Users and Groups

In order to install and configure Active Directory into my AD server, I opened the Service Manager and navigated Manage > Add Roles and Features > Next > Ensured that "Role-Based or feature-based installation" is selected and click Next > Selected AD-server from a list of servers and clicked Next > Active Directory Domain Services > Add Features > Kept on clicking Next until I saw "Install" > Install. Once I saw "Configuration required. Installation Succeeded" under the progress bar, I knew that Active Directory has completed installation.

|![AD-ServerInstallAD1](AD-ServerInstallAD1.png)|![AD-ServerInstallAD2](AD-ServerInstallAD2.png)|![AD-ServerInstallAD3](AD-ServerInstallAD3.png)|
|------------------------|-------------------------|-------------------------|


After closing out from the installation page, I returned to Server Manager and navigated from the Flag icon at the top of the window > Promote this server to a domain controller > Selected "Add a new forest", entered meadbag.local (needed a top level domain) as my Root domain name, and clicked Next > Entered a password and clicked Next > continued to click Next until I saw "Verifying prerequisites for domain > controller operation ..." > Install when it was available. Once the install was finished the server restarted on its own.

|![AD-ServerPromote1](AD-ServerPromote1.png)|![AD-ServerPromote2](AD-ServerPromote2.png)|![AD-ServerPromote3](AD-ServerPromote3.png)|
|----------------------|----------------------|----------------------|

Once the reboot was finished, I saw that my login screen had MEADBAG\Administrator as the account to log into for the server. Now that I have installed Active Directory on this server, it is time for me to create a new organizational unit and a user that is assigned to that unit. In order to do this, I went back to main page of the Server Manager and navigated from Tools > Active Directory Users and Computers. From here I saw the top level domain(meadbag.local) of the active directory server and a list of all the organizational units within that domain on the left side, and on the right a list of premade groups.

![AD-ServerDomainAndUnits](AD-ServerDomainAndUnits.png)

I then right clicked meadbag.local and navigated from New > Organizational Unit. I then created two new units: IT and HR. Within the HR unit, I right clicked and navigated from New > User and gave this new user the name Terry Smith and the User logon name tsmith, and clicked Next. I then gave it the password Blamo@abc.69, unchecked "User must change password at next logon", and hit Next and Finish on the following page.

![AD-ServerCreateNewUnit1](AD-ServerCreateUnit1.png)
![AD-ServerCreateNewUnit2](AD-ServerCreateUnit2.png)

I then repeated the same steps to create a user named Jenny Smith with a user logon name of jsmith, the same password, and in the IT organizational unit.

## 5. Target PC (AD-demo) Configuration: Enabling Remote Desktop and Installing AtomicRedTeam

## 6. Splunk Enterprise Configuration: Creating Endpoint Index

This can be done from either the AD server or the Target PC as this was done in a browser. I completed this from the Target PC, but that has no bearing on whether this can be done. I opened a browser and entered 192.168.10.10:8000 into the search bar. This brought me to the Splunk Web Portal, where I enter the same credentials that I entered when installing Splunk onto the Splunk server. 

![AD-EnterpriseLogin](AD-EnterpriseLogIn.png)


After logging in, I navigated Settings at the top of the screen > Indexes > New Index > set the Index Name to "endpoint" as show in inputs.conf > save. The endpoint index is now be visible among the list of indexes. From this page I scrolled down and saw it among the other index.

![AD-EnterpriseEndpointIndexList](AD-EnterpriseEndpointIndexList.png)

Now, to enable my Splunk server to receive the data that is sent to endpoint from my machine, I navigated from Settings > Forwarding and receiving > Configure receiving > New Receiving Port > and I entered 9997 into "Listen on this port" (this is the same port that I set as the default port in the Splunk Forwarder) > Save. This brought me to a page titled "Receiving Data" with the port I entered listed.

|![AD-EnterpriseDefaultPort](AD-EnterpriseDefaultPort.png)|![AD-EnterpriseDefaultPortEnabled](AD-EnterpriseDefaultPortEnabled.png)|
|-----------------------------|------------------------------------|

In order to see logs from devices, I navigated Apps > Search & Reporting > In the search bar I entered "index=endpoint". This search produces logs from all the devices (the Target PC and the AD Server) forwarding them to the Splunk server I created earlier, and these logs contain information on Application, System, Security, and Sysmon (as defined in inputs.conf).

|![AD-EnterpriseFirstSearch1](AD-EnterpriseFirstSearch1.png)|![AD-EnterpriseFirstSearch2](AD-EnterpriseFirstSearch2.png)|![AD-EnterpriseFirstSearch3](AD-EnterpriseFirstSearch3.png)|
|------------------------------|------------------------------|------------------------------|



## 7. Attacking PC Configuration: Static IP

## Attack Simulation and Monitoring

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
