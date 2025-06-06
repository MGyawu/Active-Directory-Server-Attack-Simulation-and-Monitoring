# Active-Directory-Brute-Force-Attack-Simulation-and-Monitoring
## Introduction

At the center of this project is a virtual environment meant to represent a real-world network
through a collection of virtual machines. Users and permissions are provided within an Active
Directory server, logs are sent to a separate Splunk server, a Windows 10 VM serves as the
target PC (the PC through which users log in), and a kali linux VM is a lone PC. This
environment is illustrated in the diagram below:

![Diagram](ADProject.png)

The purpose of this project is to create users are created within the Active Directory server,
the kali linux VM attacks and gains access to those user accounts, and to identify and observe
those attacks through logs sent to a Splunk server. 

Note: All of the virtual machines used in this project was made using Virtualbox as a hypervisor

## Splunk Server (ADsplunk) Configurations
OS: [Ubuntu 20.04 LTS](https://ubuntu.com/download/server)</p>
Splunk: [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html)</p>
Memory: 8 gbs (8192 mbs)</p>
Storage: 100 gbs</p>

![Splunk server video]()

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
