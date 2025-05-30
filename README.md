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

## Configurations

Note: All of the virtual machines used in this project was made using Virtualbox as a hypervisor

### Active Directory Server (ADserver)
### Splunk Server (ADsplunk)
### Target PC (ADdemo)
OS: [Windows 10](https://www.microsoft.com/en-us/software-download/windows10)

### Attacking PC (Kali-linux VM)


## Collecting Telemetry
## Performing Attacks
