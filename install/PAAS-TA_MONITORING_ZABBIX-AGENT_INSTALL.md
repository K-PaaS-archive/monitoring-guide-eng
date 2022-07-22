### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Zabbix Agent


# Zabbix Agent Install Guide
1. [Outline](#1)
2. [Installation of Zabbix Agent](#2)


## <div id="1">1. Outline


### 1.1. Introduction
This document provides a guide on how to install and environment setting of Zabbix Proxy with connection with the monitoring dashboard on the PaaS-TA Platform by collecting system resource information about the user's IaaS environment by measuring the usage and the after usage.
  

### 1.2. Range and Limitation
This guide is based on the following installation environment, so there may be some restrictions or limitations in installation or application for other areas not mentioned in the guide.

<table>
  <tr>
    <td><b>Virtual Machine OS</b></td>
    <td>[AP] Ubuntu 18.04.6 LTS (Bionic)<br>
        [CP] Ubuntu 18.04.6 LTS (Bionic)</td>
  </tr>
  <tr>
    <td><b>IaaS Vendor SW</b></td>
    <td> OpenStack 5.4.0 (Stein)
    </td>
  </tr>
</table>


## <div id="2">2. Installation of Zabbix Agent


### 2.1. Zabbix Agent installation according to the service platform
**│ Application Platform (AP)**  

In an AP environment, the Zabbix Agent is designed to be installed with each instance that configures the PaaS-TA environment at the same time as the PaaS-TA platform deployment. It is not necessary to install the Zabbix Agent separately in the AP environment, but only Zabbix Server or Zabbix Proxy IP information needs to be properly set in the common variable reference file(`common-vars.yml`)used when installing PaaS-TA..

**│ Container Platform (CP)**  

Unlike the way the platform was deployed using deployment automation scripts in the AP environment, the CP environment uses Kubernetes to determine the number of nodes to implement clustering or to create virtual machines on the IaaS platform. Therefore, it is necessary to install the Zabbix Agent separately on each node constituting the CP environment.

Visit the Zabbix official website to download the Zabbix Version, Operating System, and Type Version and receive an installation script suitable for your operating environment through the  [Download](https://www.zabbix.com/download) page.

![](images/zabbix_agent_install_guide_01.png)

The installation script available on the **Zabbix Packages** tab allows you to download storage information that allows you to install all Zabbix-related packages, such as Zabbix Server, Proxy, and Agent. Because only the Zabbix Agent needs to be installed at this stage, the script associated with installing other Zabbix packages is omitted, and only the Zabbix Agent installation script is followed.

This guide selected and installed the Zabbix 5.0 LTS version of Proxy on Ubuntu 18.04 operating system and MySQL configuration on database SW. Therefore, it is recommended that you refer to the installation script available on the Zabbix official website, as the content of the guide may vary in part depending on the operating system, database, or web server SW type.


### 2.2. Zabbix Agent Installation
Install Zabbix Repository.
```
$ wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+bionic_all.deb
$ sudo dpkg -i zabbix-release_5.0-1+bionic_all.deb
$ sudo apt update
```

Install Zabbix Agent.
```
$ sudo apt -y install zabbix-agent
```
Modify the `/etc/zabbix/zabbix_agentd.conf` file to suit each user's installation environment for Zabbix Agent Setup.
```
...

Server=11.11.11.11

...

ServerActive=11.11.11.11:10051

...

Hostname=dev-kube-master

...

HostMetadata=paasta

...
```
> **[ Necessary Setup Parameter ]**  
. `Server`: Enter the IP address of the Zabbix Server. Enter the IP address of the proxy in the Zabbix Proxy configuration environment.  
. `ServerActive`: Enter the IP address of the Zabbix Server (or Proxy) and the service port number in a colon.  
. `Hostname`: Enter the host name. The value set here applies to the monitoring hostname of the Zabbix Server.  
. `HostMetadata`: A sort of tag-like role for distinguishing between instance ranges for auto-monitoring host registration and grouping.

Restart Zabbix Agent and complete the Agent Installation and Settings.
```
$ sudo systemctl restart zabbix-agent
```

Repeat the installation guide **'2.2. Zabbix Agent Installation'** on all Kubernetes cluster nodes (Master nodes and all Slave nodes) for configuring the CP environment.


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Zabbix Agent
