### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > PaaS-TA (+Monitoring Dashboard)


# PaaS-TA Install Guide (+Using Monitoring Dashboard) 
1. [Outline](#1)  
 1.1. [Purpose](#1.1)  
 1.2. [Range](#1.2)  
 1.3. [References](#1.3)
   
2. [PaaS-TA AP Installation](#2)  
 2.1. [Prerequisite](#2.1)  
 2.2. [Installation File Download](#2.2)  
 2.3. [Stemcell Upload](#2.3)  
 2.4. [Runtime Config Settings](#2.4)  
 2.5. [Cloud Config Settings](#2.5)  
 2.6. [PaaS-TA AP Installation File](#2.6)  
　2.6.1. [PaaS-TA AP Installation Variable File](#2.6.1)    
　2.6.2. [PaaS-TA AP Operation 파일](#2.6.2)  
　2.6.3. [PaaS-TA AP Installation Shell Scripts](#2.6.3)  
 2.7. [PaaS-TA AP Installation](#2.7)  
 2.8. [PaaS-TA AP Login](#2.8)   

# <div id='1'/>1.  Document Outline

## <div id='1.1'/>1.1. Purpose
The purpose of this document is to provide a guide for manually installing the PaaS-TA Application Platform (hereinafter referred to as PaaS-TA AP) using a monitoring dashboard.


## <div id='1.2'/>1.2. Range
PaaS-TA AP is installed in a BOSH environment based on Bosh-deployment, and a guide is prepared based on the installation of Paasta-deployment v5.6.2.  
PaaS-TA AP provides IaaS such as VMware vSphere, Google Cloud Platform, Amazon Web Services EC2, OpenStack, and Microsoft Azure. The IaaS environment The IaaS environment validated in paasta-deployment v5.6.2 is an AWS and OpenStack environment.


## <div id='1.3'/>1.3. References

This document was written by referring to the BOSH Document and Cloud Foundry Document of Cloud Foundry.

BOSH Document: [http://bosh.io](http://bosh.io)

Cloud Foundry Document: [https://docs.cloudfoundry.org](https://docs.cloudfoundry.org)

BOSH Deployment: [https://github.com/cloudfoundry/bosh-deployment](https://github.com/cloudfoundry/bosh-deployment)

CF Deployment: [https://github.com/cloudfoundry/cf-deployment](https://github.com/cloudfoundry/cf-deployment)

<br><br>

# <div id='2'/>2. PaaS-TA AP Installation
## <div id='2.1'/>2.1. Prerequisite

- Install BOSH2-based BOSH.
- PaaS-TA AP installation works in the Inception (installation environment) where BOSH is installed.
- Conduct BOSH LOGIN for PaaS-TA AP installation.

<br>

## <div id='2.2'/>2.2. Installation File Download
- Download if the deployment for PaaS-TA AP installation does not exist

```
$ mkdir -p ~/workspace
$ cd ~/workspace
$ git clone https://github.com/PaaS-TA/common.git
$ cd ~/workspace
$ git clone https://github.com/PaaS-TA/paasta-deployment.git -b v5.6.2
```

After downloading the Monitoring Dashboard deployment file through the storage link, move (copy) some of the deployment files (below the `*-addon` directory) to the appropriate directory in the PaaS-TA deployment file as follows: If you need a specific version when downloading the monitoring dashboard deployment file, you can also download and use the `-b` option by referring to the branch or tag information in the repository.
```
$ git clone https://github.com/PaaS-TA/monitoring-deployment.git

$ cp -r monitoring-deployment/bosh-addon/* paasta-deployment/bosh/
$ cp -r monitoring-deployment/paasta-addon/* paasta-deployment/paasta/
```
Skip if this procedure was done in the '[BOSH Deployment(Using Monitoring Dashboard)](PAAS-TA_BOSH2_MONITORING_INSTALL_GUIDE.md)'. 


## <div id='2.3'/>2.3. Stemcell Upload
Stemcell is a PaaS-TA AP VM Base OS image created during deployment.  
paasta-deployment v5.6.2 is done based on Ubuntu bionic stemcell 1.34.  
The basic Stemcell upload command is as follows.  
```                     
$ bosh -e ${BOSH_ENVIRONMENT} upload-stemcell {URL}
```

Paasta-deployment supports Stemcell upload scripts from v5.5.0 and uploads Stemcell by executing the command below after logging in to BOSH.  
BOSH_ENVIRONMENT is the director's name used to install BOSH. CURRENT_IAAS is entered according to the deployment environment IaaS (aws, azure, gcp, openstack, vsphere, and other input bos-lite).
<br>(When BOSH LOGIN using the create-bosh-login.sh being provided by the PaaS-TA AP, the BOSH_ENVIRONMENT and CURRENT_IAAS is automatically entered.)


- Modify the Settings of Stemcell Upload Script (Modify BOSH_ENVIRONMENT)

> $ vi ~/workspace/paasta-deployment/bosh/upload-stemcell.sh
```                     
#!/bin/bash
STEMCELL_VERSION=1.34
CURRENT_IAAS="${CURRENT_IAAS}"				# IaaS Information (Enter aws/azure/gcp/openstack/vsphere/bosh-lite if you are not using the create-bosh-login.sh being provided by the PaaS-TA)
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"			# bosh director alias name (Check and enter the name from bosh envs if you are not using the create-bosh-login.sh being provided by the PaaS-TA)
if [[ ${CURRENT_IAAS} = "aws" ]]; then
        bosh -e ${BOSH_ENVIRONMENT} upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/${STEMCELL_VERSION}/bosh-stemcell-${STEMCELL_VERSION}-aws-xen-hvm-ubuntu-bionic-go_agent.tgz -n
elif [[ ${CURRENT_IAAS} = "azure" ]]; then
        bosh -e ${BOSH_ENVIRONMENT} upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/${STEMCELL_VERSION}/bosh-stemcell-${STEMCELL_VERSION}-azure-hyperv-ubuntu-bionic-go_agent.tgz -n
elif [[ ${CURRENT_IAAS} = "gcp" ]]; then
        bosh -e ${BOSH_ENVIRONMENT} upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/${STEMCELL_VERSION}/bosh-stemcell-${STEMCELL_VERSION}-google-kvm-ubuntu-bionic-go_agent.tgz -n
elif [[ ${CURRENT_IAAS} = "openstack" ]]; then
        bosh -e ${BOSH_ENVIRONMENT} upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/${STEMCELL_VERSION}/bosh-stemcell-${STEMCELL_VERSION}-openstack-kvm-ubuntu-bionic-go_agent.tgz -n
elif [[ ${CURRENT_IAAS} = "vsphere" ]]; then
        bosh -e ${BOSH_ENVIRONMENT} upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/${STEMCELL_VERSION}/bosh-stemcell-${STEMCELL_VERSION}-vsphere-esxi-ubuntu-bionic-go_agent.tgz -n
elif [[ ${CURRENT_IAAS} = "bosh-lite" ]]; then
        bosh -e ${BOSH_ENVIRONMENT} upload-stemcell https://storage.googleapis.com/bosh-core-stemcells/${STEMCELL_VERSION}/bosh-stemcell-${STEMCELL_VERSION}-warden-boshlite-ubuntu-bionic-go_agent.tgz -n
else
        echo "plz check CURRENT_IAAS"
fi
```
- Execute Stemcell Upload Script 

```
$ cd ~/workspace/paasta-deployment/bosh
$ source upload-stemcell.sh
```

<br>

## <div id='2.4'/>2.4. Runtime Config Settings
Runtime config is a configuration that is applied collectively to VMs deployed in BOSH. 
The basic Runtime Config configuration command is as follows.  
```                     
$ bosh -e ${BOSH_ENVIRONMENT} update-runtime-config {PATH} --name={NAME}
```

The Runtime Config applied by PaaS-TA AP is as follows.  

- DNS Runtime Config  
  A component that has been applied from PaaS-TA 4.0, the component that the Consul has been replaced in PaaS-TA Component.  
  BOSH DNS deployment must be done first for the communication between PaaS-TA Component.  

- OS Configuration Runtime Config  
  Configure sysctl by using the BOSH Linux OS configuration release.  

paasta-deployment supports from v5.5.0 to Runtime Config setting scripts. Use the command below to set Runtime Config after BOSH Login.  

  - Modify Runtime Config Update Script (Modify BOSH_ENVIRONMENT)
> $ vi ~/workspace/paasta-deployment/bosh/update-runtime-config.sh
```                     
#!/bin/bash
BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"			 # bosh director alias name (Check and enter the name from bosh envs if you are not using the create-bosh-login.sh being provided by the PaaS-TA)
bosh -e ${BOSH_ENVIRONMENT} update-runtime-config -n runtime-configs/dns.yml
bosh -e ${BOSH_ENVIRONMENT} update-runtime-config -n --name=os-conf runtime-configs/os-conf.yml
```
- Execute Runtime Config Update Script
```                     
$ cd ~/workspace/paasta-deployment/bosh
$ source update-runtime-config.sh
```
  - Runtime Config Check  
  ```  
  $ bosh -e ${BOSH_ENVIRONMENT} runtime-config
  $ bosh -e ${BOSH_ENVIRONMENT} runtime-config --name=os-conf
  ```

<br>

## <div id='2.5'/>2.5. Cloud Config Settings

When deploying VMs through BOSH, IaaS-related network, storage, and define VM-related settings as Cloud Config.  
When the Paasta-deployment installation file is downloaded, you can view IaaS-specific CloudConfig examples below the ~/workspace/paasta-deployment/cloud-config directory, and modify cloud-config.yml to suit IaaS by referring to the examples.  
Apply the Cloud Config to BOSH before deploying PaaS-TA AP.

- AWS based [cloud-config.yml](https://github.com/PaaS-TA/paasta-deployment/blob/master/cloud-config/aws-cloud-config.yml) example

```
## azs :: Defines Availability Zone.
azs:
- cloud_properties:
    availability_zone: ap-northeast-2a
  name: z1
- cloud_properties:
    availability_zone: ap-northeast-2a
  name: z2
... ((Skip)) ...
## compilation :: Defines the available zone and the type of virtual machine in which the compile virtual machine is to be created.
compilation:
  az: z4
  network: default
  reuse_compilation_vms: true
  vm_type: xlarge
  workers: 5
## disk_types :: Defines Disk Type and Persistent Disk.
disk_types:
- disk_size: 1024
  name: default
- disk_size: 1024
  name: 1GB
  
... ((Skip)) ...
## networks :: Defines Network. (For AWS, Set Subnet, Security Group, DNS, Gateway, etc.)
networks:
- name: default
  subnets:
  - az: z1
    cloud_properties:
      security_groups: paasta-v50-security
      subnet: subnet-XXXXXXXXXXXXXXXXX
    dns:
    - 8.8.8.8
    gateway: 10.0.1.1
    range: 10.0.1.0/24
    reserved:
    - 10.0.1.2 - 10.0.1.9
    static:
    - 10.0.1.10 - 10.0.1.120
... ((Skip)) ...
## vm_extentions :: Defines a virtual machine configuration that specifies any specific IaaS configuration (such as Security Groups and Load Balancers)
vm_extensions:
- name: cf-router-network-properties
- name: cf-tcp-router-network-properties
- name: diego-ssh-proxy-network-properties
- name: cf-haproxy-network-properties
- cloud_properties:
    ephemeral_disk:
      size: 51200
      type: gp2
  name: 50GB_ephemeral_disk
... ((Skip)) ...
## vm_type :: Defines the VM Type. (For AWS, set Instance type)
vm_types:
- cloud_properties:
    ephemeral_disk:
      size: 3000
      type: gp2
    instance_type: t2.small
  name: minimal
- cloud_properties:
    ephemeral_disk:
      size: 10000
      type: gp2
    instance_type: t2.small
  name: small
  
... ((Skip)) ...
```

- AZs

Cloud Config examples provided in PaaS-TA are set from z1 to z6.  
For z1 ~ z3, is a Zone where PaaS-TA AP VM is being installed. For z4 ~ z6, is defined as the Zone where the services are being installed.   
The reason for setting it in three units is to triple the service, and it may be set differently depending on the installation environment.  

- VM Types

The VM Type is a VM Type defined by IaaS.  

※ Below is an Instance Type defined by the AWS.
![](images/paasta-monitoring/aws-vmtype.png)

- Compilation

When installing PaaS-TA AP and service, the BOSH creates a VM for Compile operations and compiles the source, then creates a VM and installs the compiled file on the target VM, and deletes the VM for Compile operations is deleted. (The number of Workers is equal to the number of Compile VM which makes the compile speed when there are many.)    

- Disk Size

A Persistent Disk Size of the VM where PaaS-TA AP and service are being installed.

- Networks

Networks define Subnet Network, DNS, Security Groups, and Network ID per AZ.  
Usually, a Range Cider is defined so that 256 IPs can be defined per AZ.

<br>

- Cloud Config Update

```
$ bosh -e ${BOSH_ENVIRONMENT} update-cloud-config ~/workspace/paasta-deployment/cloud-config/{iaas}-cloud-config.yml
```

- Cloud Config Check

```
$ bosh -e ${BOSH_ENVIRONMENT} cloud-config  
```

<br>

## <div id='2.6'/>2.6.  PaaS-TA (+Monitoring Dashboard) Installation File

Set the variables to be applied when installing PaaS-TA AP by modifying the common_vars.yml file and vars.yml.

<table>
<tr>
<td>common_vars.yml</td>
<td>Common variable settings file when installing PaaS-TA AP and various services</td>
</tr>
<tr>
<td>vars.yml</td>
<td>Variable Setting file to apply when installing PaaS-TA AP</td>
</tr>
<tr>
<td>deploy-aws.sh</td>
<td>Shell Script File for PaaS-TA AP Installation in AWS Environment</td>
</tr>
<tr>
<td>deploy-openstack.sh</td>
<td>Shell Script File for PaaS-TA AP Installation in an OpenStack Environment</td>
</tr>
<tr>
<td>paasta-deployment.yml</td>
<td>Manifest file to deploy PaaS-TA AP</td>
</tr>
</table>

<br>

### <div id='2.6.1'/>2.6.1. PaaS-TA (+Monitoring Dashboard) Installation Variable File


- common_vars.yml  

[common_vars.yml](https://github.com/PaaS-TA/common/blob/master/common_vars.yml) at the ~/workspace/common folder contains common variable settings files that apply when installing PaaS-TA AP and various services.  
When installing PaaS-TA AP, you can change the value of system_domain, paasta_admin_username, paasta_admin_password, paasta_database_port, paasta_cc_db_password, paasta_uaa_db_password, uaa_client_admin_secret, and uaa_client_portal_secret.

The following file contents are setting information tested in a hands-on environment for guide writing, and according to the reader's needs, the Host Address area IP may be configured by referring to the following example. However, depending on the user's installation environment (such as the Network Address area of the IP address according to the network environment used), the installer must carefully consider each element and set it.  

> $ vi ~/workspace/common/common_vars.yml
```yaml
# BOSH INFO
bosh_ip: "10.5.0.6"                              # BOSH IP
bosh_url: "https://10.5.0.6"                     # BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"                    # BOSH Client Admin ID
bosh_client_admin_secret: "mf4nxvkzytihm61fo0sl" # BOSH Client Admin Secret (can be checked with the 'echo $(bosh int ~/workspace/paasta-5.0/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' command)
bosh_director_port: 25555                        # BOSH director port
bosh_oauth_port: 8443                            # BOSH oauth port
bosh_version: 271.2                              # BOSH version (can be checked with the 'bosh env' command, for on-demand service, e.g. "271.2")

# PAAS-TA INFO
system_domain: "10.5.0.240.nip.io"               # Domain (When using nip.io, it is the same with the HAProxy Public IP)
paasta_admin_username: "admin"                   # PaaS-TA Admin Username
paasta_admin_password: "admin"                   # PaaS-TA Admin Password
paasta_nats_ip: "10.5.0.121"
paasta_nats_port: 4222
paasta_nats_user: "nats"
paasta_nats_password: "VB29evJTNK63PM0lLR91UBhqyiKtcR" # PaaS-TA Nats Password (can be checked with the 'credhub get -n /micro-bosh/paasta/nats_password' command after logging in to the CredHub)
paasta_nats_private_networks_name: "default"     # Network name of the PaaS-TA Nats 
paasta_database_ips: "10.5.0.123"                # PaaS-TA Database IP (e.g. "10.0.1.123")
paasta_database_port: 5524                       # PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysql
paasta_database_type: "postgresql"               # PaaS-TA Database Type (e.g. "postgresql" or "mysql")
paasta_database_driver_class: "org.postgresql.Driver" # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")
paasta_cc_db_id: "cloud_controller"              # CCDB ID (e.g. "cloud_controller")
paasta_cc_db_password: "cc_admin"                # CCDB Password (e.g. "c418e687c4Kx!" 8+ digits mixed with English/numeric/special characters)
paasta_uaa_db_id: "uaa"                          # UAADB ID (e.g. "uaa")
paasta_uaa_db_password: "uaa_admin"              # UAADB Password (e.g. "ifb2497iEA5!" 8+ digits mixed with English/numeric/special characters)
paasta_api_version: "v3"

# UAAC INFO
uaa_client_admin_id: "admin"                     # UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"          # Secret Variable to access to the UAAC Admin Client
uaa_client_portal_secret: "clientsecret"         # Secret Variable to access to the UAAC Portal Client

# Monitoring INFO
metric_url: "10.5.1.101"                         # Monitoring InfluxDB IP
elasticsearch_master_ip: "10.5.2.101"            # Logsearch의 elasticsearch master IP
elasticsearch_master_port: 9200                  # Logsearch의 elasticsearch master Port
syslog_address: "10.5.2.100"                     # Logsearch의 ls-router IP
syslog_port: "2514"                              # Logsearch의 ls-router Port
syslog_transport: "relp"                         # Logsearch Protocol
saas_monitoring_url: "10.5.1.122"                # Pinpoint HAProxy WEBUI의 Public IP
monitoring_api_url: "10.5.1.121"                 # Monitoring-WEB의 Public IP

...

### Zabbix Agent for monitoring ###
server_ip: "10.5.0.68"                           # Zabbix server (or Proxy) IP address
listen_port: "10050"                             # Zabbix server (or Proxy) Port number
server_active: "10.5.0.68:10051"                 # Zabbix server (or Proxy) IP:Port string
host_metadata: "paasta"                          # Metadata for Zabbix Agent autoregistration

...
```
The address of the Zabbix Proxy for the Zabbix Agent allows to create an instance of the Proxy, and then reference the value.

- vars.yml  

When installing PaaS-TA AP, you can change the values of various variables applied or the settings of the VMs to be deployed.

> $ vi ~/workspace/paasta-deployment/paasta/vars.yml
```
# SERVICE VARIABLE
deployment_name: "paasta"			# Deployment Name
network_name: "default"				# Default Network Name not specified separately for VM
haproxy_public_ip: "52.78.32.153"		# HAProxy IP (Needed when deploying Public IP, HAproxy VM)
haproxy_public_network_name: "vip"		# PaaS-TA Public Network Name
haproxy_private_network_name: "private" 	# PaaS-TA Private Network Name (Need to be set when deploying including the vSphere use-haproxy-public-network-vsphere.yml)
cc_db_encryption_key: "db-encryption-key"	# Database Encryption Key (Same KEY required for Version Upgrade)
cert_days: 3650					# PaaS-TA authentication certificate expiration date
private_ip: "10.244.0.34"   			# Proxy IP (Need to be set when using Private IP and BOSH-LITE )
uaa_login_logout_redirect_parameter_disable: "false"
uaa_login_logout_redirect_parameter_whitelist: ["http://portal-web-user.15.165.2.88.nip.io","http://portal-web-user.15.165.2.88.nip.io/callback","http://portal-web-user.15.165.2.88.nip.io/login"]	# UAA Redirect Whitelist registration variable for portal page transportation
uaa_login_branding_company_name: "PaaS-TA R&D"	# UAA Page Title Name
uaa_login_branding_footer_legal_text: "Copyright © PaaS-TA R&D Foundation, Inc. 2017. All Rights Reserved."	# UAA Page Bottom Area Text
uaa_login_branding_product_logo: "iVBORw0KGgoAAAANSUhEUgAAAM0AAAAdCAYAAAAJguhGAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyJpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMy1jMDExIDY2LjE0NTY2MSwgMjAxMi8wMi8wNi0xNDo1NjoyNyAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNiAoV2luZG93cykiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6QUNDMTA1MTZCRDNBMTFFNjkzMTVEQjMxRkE5QjkxNUMiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6QUNDMTA1MTdCRDNBMTFFNjkzMTVEQjMxRkE5QjkxNUMiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDpBQ0MxMDUxNEJEM0ExMUU2OTMxNURCMzFGQTlCOTE1QyIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDpBQ0MxMDUxNUJEM0ExMUU2OTMxNURCMzFGQTlCOTE1QyIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/Piy2YkgAAA9pSURBVHja7FwJeBRF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"	 # UAA Page Logo Image (Base64)
uaa_login_branding_square_logo: "iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyhpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTMyIDc5LjE1OTI4NCwgMjAxNi8wNC8xOS0xMzoxMzo0MCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6QkIwMjA5M0U5NEQ0MTFFNjk1M0FFQ0UxNkIxNEZFNjciIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6QkIwMjA5M0Q5NEQ0MTFFNjk1M0FFQ0UxNkIxNEZFNjciIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTUuNSAoV2luZG93cykiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDpEMzRGNDdCNTgxNEIxMUU2QjJFODk1MEQzM0EzNkMxOSIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDpEMzRGNDdCNjgxNEIxMUU2QjJFODk1MEQzM0EzNkMxOSIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/Psx4+gAAAASbSURBVHja7FZ9aFVlGP8973vOud9Tt7nl1lzNOWailKkV/iEohYn2Vx/QHxKCQkUUmEFhREXQvxJGhSFRlERFhEk1K9PUjJk0dZJzzuGc+3B3997tfp3zfvScu0X4T1AQ/nPP5eGce17e5/l9PO9zL1lrcTMvgZt8VQFUAVQBVAE4KHbDZIYWq1P7dpiJwQ7HdYxZsOyiXLzmYxGvOwJZD6ICDFxQKQeKNgEyi6A8BYcUSE0CJKCpDkExikgyBmTLQJzXhIaVSejxfsjaZtDkeaD3M9iVu6ByKbiNc+GYsUtt5R/fPIzxoSaSUZhIADM9sN5c/WG707Z2r+jc8gJFvAz0/zOyhTrf9RImBptsOQ4bSA4P0Alma8hcOLAtOPnaT7pwbQXJ2H+vQsRaRzkis8+ci5wZAHb4+EYpErBFbgdWzpYJ1p8NHQeNnVuuj7/epca6N89spH9ZOAb+vZsf9B18Juj97osgkznk9+zfY0ojbeEaFd9tT0OV55lhycwJFGOpoxbkcoSAIxZClmHiXkCdD7/qLtr6BkSa/Z74xx6gFBOwvtSXf9mp+r5+mrJDt5IRMNIB+T5MtHHUXfvyFvnKltUbbP7KbRRzoDMSxHBp9hNetnLnTaE/6d/XmdLwUjuvuRtOKiNCALpQYWopDqMcOB43q/Zggusd6rf3PjF9X26ncr5GOGyxGwUZJiq4TmkyqXOjHXLXi8/l7eCRR0WUi3gWekowKxGaMyvjXzd+z0wp27tUjZ18TFiboljdALleNlTAEjdw4MKJp2CvdW/2T+/5VEz03FmRee4S8NECpljSUqTiv5U+53N9spM/o3TsrYN06dCDSDCLPCcb4apBaAdXjhkg3OOZmYgISLfMCdiWRPN1arz7Qzm//aCNd5wIVEPeyXZvs6d2v0N+UZhIDUztWqgreYjRiywk53QjHFzD9oI6H/mIrD4Pk0svKH+zo8tJX1hqonwCmLyeZFlzrET4pVJYQ3isgxf2iOZ3mokXWBkNlWyHbX3oLBVL31P//mcpKMFG50PVb4Y+fQZycoDJJBkAd75gFeU4bFPT2ei6nRvJls9W5qEZOrYoOLHvADIXOxHzWB62RGnoosfyJhlAimW07CNHRCK0TM6tBSX4BAlen7gASp8GBQo62XLF1G4S9ujRZuQGYNkWwX1C0Qh0jeu7i5Z/7tz7xPNCmOEZAIbP3/QgjK9a/F/3vY+x7vtlCMrjQoI3Oi4XTUE5c2CcGsDlo8XKRHhC2uJViOkxBltkLdirhnu+dToff0qd6LnDz11/wHHVLcKUha5bOCXdfA9aFh7y6u46g4aFQG4QfwPI9LGsNbD5aaHT57bq/sNPYuryCjLTDEJUpLbcmCTNjC0cllvFWrYHcWbd2idvX/+2bNq0m8HY4PhRFNrXYI6bBQpp2LZVMP2HYVIJuBEe541tQP4qnBsGh1FhIeO0rN6L1pUfmNFz99HIHxtsYXCZ1qoO0taTKgs+a4xAjguZmaLaVSNINHS5NUu+Eqn6HFTAeYIQGdtRRGW6KR/w+VkrXtM3zqrq3/IqgCqAKoCbDeBPAQYAvdcfKsxKtoUAAAAASUVORK5CYII="	# UAA Page Title Logo Image (Base64)
uaa_login_links_passwd: "http://portal-web-user.15.165.2.88.nip.io/resetpasswd"	# The address of the link that moves when you press Reset Password on the UAA page
uaa_login_links_signup: "http://portal-web-user.15.165.2.88.nip.io/createuser"	# The address of the link that moves when you press Create Account on the UAA page 
uaa_client_portal_redirect_uri: "http://portal-web-user.15.165.2.88.nip.io,http://portal-web-user.15.165.2.88.nip.io/callback"	# Redirect URI specified variable in UAA Portal Client, URI path to be moved when successfully logged in from the UAA page after clicking the Login button on the portal
syslog_custom_rule: 'if ($msg contains "DEBUG") then stop'	# [MONITORING] Custom Rule to be sent by PaaS-TA Logging Agent
syslog_fallback_servers: []					# [MONITORING] PaaS-TA Syslog Fallback Servers
# STEMCELL
stemcell_os: "ubuntu-bionic"		# Stemcell OS
stemcell_version: "1.61" # Stemcell Version
# SMOKE-TEST
smoke_tests_azs: ["z1"]			# Smoke-Test Available Zone
smoke_tests_instances: 1		# Smoke-Test Instance Number
smoke_tests_vm_type: "minimal"		# Smoke-Test VM Type
smoke_tests_network: "default"		# Smoke-Test Network
# NATS
nats_azs: ["z1", "z2"]			# Nats Available Zone
nats_instances: 2			# Nats Instance Number
nats_vm_type: "minimal"			# Nats VM Type
nats_network: "default"			# Nats Network
# DATABASE
database_azs: ["z1"]			# Database Available Zone
database_instances: 1			# Database Instance Number
database_vm_type: "small"		# Database VM Type
database_network: "default"		# Database Network
database_persistent_disk_type: "10GB"	# Database Permanent Disk Type
# DIEGO-API
diego_api_azs: ["z1", "z2"]		# Diego-API Available Zone
diego_api_instances: 2			# Diego-API Instance Number
diego_api_vm_type: "small"		# Diego-API VM Type
diego_api_network: "default"		# Diego-API Network
# UAA
uaa_azs: ["z1", "z2"]			# UAA Available Zone
uaa_instances: 2			# UAA Instance Number
uaa_vm_type: "minimal"			# UAA VM Type
uaa_network: "default"			# UAA Network
# SINGLETON-BLOBSTORE
singleton_blobstore_azs: ["z1"]		# Singleton-Blobstore Available Zone
singleton_blobstore_instances: 1	# Singleton-Blobstore Instance Number
singleton_blobstore_vm_type: "small"	# Singleton-Blobstore VM Type
singleton_blobstore_network: "default"	# Singleton-Blobstore Network
singleton_blobstore_persistent_disk_type: "100GB"	# Singleton-Blobstore Permanent Disk Type
# API
api_azs: ["z1", "z2"]			# API Available Zone
api_instances: 2			# API Instance Number
api_vm_type: "small"			# API VM Type
api_network: "default"			# API Network
api_vm_extensions: ["50GB_ephemeral_disk"]	# API VM Expansion
# CC-WORKER
cc_worker_azs: ["z1", "z2"]		# CC-Worker Available Zone
cc_worker_instances: 2			# CC-Worker Instance Number
cc_worker_vm_type: "minimal"		# CC-Worker VM Type
cc_worker_network: "default"		# CC-Worker Network
# SCHEDULER
scheduler_azs: ["z1", "z2"]		# Scheduler Available Zone
scheduler_instances: 2			# Scheduler Instance Number
scheduler_vm_type: "minimal"		# Scheduler VM Type
scheduler_network: "default"		# Scheduler Network
scheduler_vm_extensions: ["diego-ssh-proxy-network-properties"] # Scheduler VM Expansion
# ROUTER
router_azs: ["z1", "z2"]		# Router Available Zone
router_instances: 2			# Router Instance Number
router_vm_type: "minimal"		# Router VM Type
router_network: "default"		# Router Network
router_vm_extensions: ["cf-router-network-properties"]	# Router VM Expansion
# TCP-ROUTER
tcp_router_azs: ["z1", "z2"]		# TCP-Router Available Zone
tcp_router_instances: 2			# TCP-Router Instance Number
tcp_router_vm_type: "minimal"		# TCP-Router VM Type
tcp_router_network: "default"		# TCP-Router Network
tcp_router_vm_extensions: ["cf-tcp-router-network-properties"]	# TCP-Router VM Expansion
# DOPPLER
doppler_azs: ["z1", "z2"]		# Doppler Available Zone
doppler_instances: 4			# Doppler Instance Number
doppler_vm_type: "minimal"		# Doppler VM Type
doppler_network: "default"		# Doppler Network
# DIEGO-CELL
diego_cell_azs: ["z1", "z2"]		# Diego-Cell Available Zone
diego_cell_instances: 3			# Diego-Cell Instance Number
diego_cell_vm_type: "small-highmem-16GB"		# Diego-Cell VM Type
diego_cell_network: "default"		# Diego-Cell Network
diego_cell_vm_extensions: ["100GB_ephemeral_disk"]	# Diego-Cell VM Expansion
# LOG-API
log_api_azs: ["z1", "z2"]		# Log-API Available Zone
log_api_instances: 2			# Log-API Instance Number
log_api_vm_type: "minimal"		# Log-API VM Type
log_api_network: "default"		# Log-API Network
# CREDHUB
credhub_azs: ["z1", "z2"]		# CredHub Available Zone
credhub_instances: 2			# CredHub Instance Number
credhub_vm_type: "minimal"		# CredHub VM Type
credhub_network: "default"		# CredHub Network
# ROTATE-CC-DATABASE-KEY
rotate_cc_database_key_azs: ["z1"]	# Rotate-CC-Database-Key Available Zone
rotate_cc_database_key_instances: 1	# Rotate-CC-Database-KeyInstance Number
rotate_cc_database_key_vm_type: "minimal"	# Rotate-CC-Database-Key VM Type
rotate_cc_database_key_network: "default"	# Rotate-CC-Database-Key Network
# HAPROXY
haproxy_azs: ["z7"]			# HAProxy Available Zone
haproxy_instances: 1			# HAProxy Instance Number
haproxy_vm_type: "minimal"		# HAProxy VM Type
haproxy_network: "default"		# HAProxy Network
```
The following is a description of the variables related to UAA.

1. uaa_login_logout_redirect_parameter_whitelist : UAA Redirect Whitelist registering variable for portal page transportation

```
ex) uaa_login_logout_redirect_parameter_whitelist=["{PaaS-TA PORTAL URI}","{PaaS-TA PORTAL URI}/callback","{PaaS-TA PORTAL URI}/login"]
```

2. uaa_login_links_signup : The address of the link that moves when you press Create Account on the UAA page

```
ex) uaa_login_links_signup="{PaaS-TA PORTAL URI}/createuser"
```

<img src="https://github.com/PaaS-TA/Guide-5.0-Ravioli/blob/master/install-guide/paasta/images/uaa-login-2.png">

3. uaa_login_links_passwd : The address of the link that moves when you press Reset Password on the UAA page  

```
ex) uaa_login_links_passwd="{PaaS-TA PORTAL URI}/resetpasswd"
```

<img src="https://github.com/PaaS-TA/Guide-5.0-Ravioli/blob/master/install-guide/paasta/images/uaa-login.png" width="663px">


4. uaa_client_portal_redirect_uri : Redirect URI specified variable in UAAC Portal Client, URIs that are moved upon successful login on the UAA page after clicking the Login button on the portal

```
ex) uaa_client_portal_redirect_uri="{PaaS-TA PORTAL URI}, {PaaS-TA PORTAL URI}/callback"
```

5. uaa_client_portal_secret : Secret Variable to access the UAAC Portal Client

```
ex) uaa_client_portal_secret="portalclient"
```

6. uaa_client_admin_secret : Secret Variable to access the UAAC Admin Client

```
ex) uaa_client_admin_secret="admin-secret"
```

For instructions on how to use UAAC after installing PaaS-TA AP, refer to the Other CLI in the User's Guide.

<br>

### <div id='2.6.2'/>2.6.2. PaaS-TA AP Operation File

<table>
<tr>
<td>File Name</td>
<td>Description</td>
<td>Requirements</td>
</tr>
<tr>
<td>operations/use-postgres.yml</td>
<td>Install Database with Postgres <br>
    - When use-postgres.yml is not applied, install MySQL  <br>
    - Required for Migration from versions earlier than 3.5  
</td>
<td></td>
</tr>
<tr>
<td>operations/use-haproxy.yml</td>
<td>Apply HAProxy <br>
    - When installing PaaS-TA AP using the LB provided by IaaS, remove and install the Operation file.
</td>
<td>Requires operation file: use-haproxy-public-network.yml <br>
    Requires value :  -v haproxy_private_ip
</td>
</tr>
<tr>
<td>operations/use-haproxy-public-network.yml</td>
<td>HAProxy Public Network Setting <br>
    - When installing PaaS-TA AP using the LB provided by IaaS, remove and install the Operation file.
</td>
<td>Requires: use-haproxy.yml <br>
    Requires Value :  <br>
    -v haproxy_public_ip <br>
    -v haproxy_public_network_name
</td>
</tr>
<tr>
<td>operations/use-haproxy-public-network-vsphere.yml</td>
<td>HAProxy Public Network Setting <br>
    - used in the vsphere, When installing PaaS-TA AP using the LB provided by IaaS, remove and install the Operation file.
</td>
<td>Requires: use-haproxy.yml <br>
    Requires Value :  <br>
    -v haproxy_public_ip <br>
    -v haproxy_public_network_name <br>
    -v haproxy_private_network_name
</td>
</tr>
<tr>
<td>operations/cce.yml</td>
<td>Apply CCE when installing.</td>
<td></td>
</tr>
</table>

<br>

### <div id='2.6.3'/>2.6.3.   PaaS-TA AP Installation Shell Scripts
The paasta-deployment.yml file is a Manifest file that deploys PaaS-TA AP which defines the installation of PaaS-TA AP VM.  
When redeploying an already installed PaaS-TA AP, changing the AZs(zone) of singleton-blobstore, database, the information in ORG, SPACE, and APP gets deleted.

**※ Use Bosh deploy command when installing the PaaS-TA AP. (Options vary from the IaaS environment)**

Example of PaaS-TA AP Deployment BOSH Command

```
$ bosh -e ${BOSH_ENVIRONMENT} -d paasta deploy paasta-deployment.yml
```

Installation Option should be added when deploying PaaS-TA AP. Instruction on the Installation Option is as shown below.

<table>
<tr>
<td>-e</td>
<td>BOSH Director Name</td>
</tr>
<tr>
<td>-d</td>
<td>Deployment Name (Default Value paasta, Modifications affect other PaaS-TA services.)</td>
</tr>   
<tr>
<td>-o</td>
<td>An option file to be applied when installing PaaS-TA and provides attributes for each IaaS, whether or not to use Haproxy, and database setting functions.
</td>
</tr>
<tr>
<td>-v</td>
<td>Used to set variables in the variable or option file applied when installing PaaS-TA. <br> It is divided into required or optional items depending on the Option file properties.</td>
</tr>
<tr>
<td>-l, --var-file</td>
<td>Used to read the variables created in the YAML file.</td>
</tr>
</table>

**│ When installing AWS Environment**

```
$ vi ~/workspace/paasta-deployment/paasta/deploy-aws-monitoring.sh
```
```shell script
#!/bin/bash

BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"

bosh -e ${BOSH_ENVIRONMENT} -d paasta -n deploy paasta-deployment.yml \
    -o operations/aws.yml \
    -o operations/use-haproxy.yml \
    -o operations/use-haproxy-public-network.yml \
    -o operations/use-postgres.yml \
    -o operations/cce.yml \
    -o operations/rename-network-and-deployment.yml \
    -o operations/addons/paasta-monitoring-agent.yml \
    -o operations/addons/enable-component-syslog.yml \
    -o operations/addons/enable-zabbix-agent.yml \
    -o operations/addons/custom-diego-release-for-monitoring.yml \
    -l vars.yml \
    -l ../../common/common_vars.yml
```

**│ When Installing OpenStack Environment**

```
$ vi ~/workspace/paasta-deployment/paasta/deploy-openstack-monitoring.sh
```
```shell script
#!/bin/bash

BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"

bosh -e ${BOSH_ENVIRONMENT} -d paasta -n deploy paasta-deployment.yml \
    -o operations/openstack.yml \
    -o operations/use-haproxy.yml \
    -o operations/use-haproxy-public-network.yml \
    -o operations/use-postgres.yml \
    -o operations/cce.yml \
    -o operations/rename-network-and-deployment.yml \
    -o operations/addons/paasta-monitoring-agent.yml \
    -o operations/addons/enable-component-syslog.yml \
    -o operations/addons/enable-zabbix-agent.yml \
    -l vars.yml \
    -l ../../common/common_vars.yml
```

- Assign execution authority to Shell script file

```
$ chmod +x ~/workspace/paasta-deployment/paasta/*.sh
```

<br>

## <div id='2.7'/>2.7.  PaaS-TA (+Monitoring Dashboard) Installation
- Modify common_vars.yml and vars.yml according to the server environment and modify the setting of the Deploy script file.

```
$ vi ~/workspace/paasta-deployment/paasta/deploy-aws-monitoring.sh
```
```shell script
#!/bin/bash

BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"

bosh -e ${BOSH_ENVIRONMENT} -d paasta -n deploy paasta-deployment.yml \
    -o operations/aws.yml \
    -o operations/use-haproxy.yml \
    -o operations/use-haproxy-public-network.yml \
    -o operations/use-postgres.yml \
    -o operations/cce.yml \
    -o operations/rename-network-and-deployment.yml \
    -o operations/addons/paasta-monitoring-agent.yml \
    -o operations/addons/enable-component-syslog.yml \
    -o operations/addons/enable-zabbix-agent.yml \
    -o operations/addons/custom-diego-release-for-monitoring.yml \
    -l vars.yml \
    -l ../../common/common_vars.yml
```

- Execute Shell Script File when installing PaaS-TA AP (BOSH Login Required)

```
$ cd ~/workspace/paasta-deployment/paasta
$ ./deploy-{IaaS}-monitoring.sh
```

- PaaS-TA AP Installation Check

> $ bosh -e ${BOSH_ENVIRONMENT} vms -d paasta
```
ubuntu@inception:~$ bosh -e micro-bosh vms -d paasta
Using environment '10.0.1.6' as client 'admin'
Task 134. Done
Deployment 'paasta'
Instance                                                  Process State  AZ  IPs           VM CID               VM Type             Active  Stemcell  
api/918da8e3-36c9-4144-b457-f48792041ece                  running        z1  10.0.31.206   i-093920c2caf43fe63  small               true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
api/c01d1a66-56c0-4dfb-87cd-b4e7323012ec                  running        z2  10.0.32.204   i-0bd6841ee37df618b  small               true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34
cc-worker/30aa88de-8b5c-4e3a-a0ae-b2933f3af492            running        z1  10.0.31.207   i-02a7032164038f09b  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
cc-worker/31a465bd-64af-49c6-a867-3439d98b2014            running        z2  10.0.32.205   i-0d8345c5348a42fdd  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
credhub/0d2da1ef-dbdc-47d8-9514-69c1e0e83f82              running        z2  10.0.32.213   i-0f21b57a610868775  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
credhub/a43132d5-ab04-4fe3-8b75-b8194f28678b              running        z1  10.0.31.216   i-0ea2f77eb95a32f21  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
database/07b7ba09-7ace-4428-b4d4-a80163aaf82c             running        z1  10.0.31.202   i-0c532e0a7a53015c2  small               true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
diego-api/a05bbf7b-f513-48f0-8444-c90cd4b63ae2            running        z2  10.0.32.202   i-0b982d70a8debde41  small               true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
diego-api/ba388ba5-e6df-4d5e-9c6e-3af6b1fdc319            running        z1  10.0.31.203   i-0a5dfee4dc8ba1b68  small               true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
diego-cell/15378660-b457-4b6e-a9cb-5729b091c675           running        z1  10.0.31.213   i-095a00b9cb171c444  small-highmem-16GB  true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
diego-cell/7d7ed58e-c82e-429e-a6ce-18e4d70cca29           running        z2  10.0.32.211   i-02d836e28133368a1  small-highmem-16GB  true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
diego-cell/eb3b22f3-2905-4ef5-81d0-1ba6974b7316           running        z1  10.0.31.214   i-0a26ae4105e8ef6f4  small-highmem-16GB  true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
doppler/75577265-7f33-45c0-b4de-b24a881462bf              running        z1  10.0.31.211   i-01b19951e2ed96a55  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
doppler/82956ad8-d103-4223-b426-cebc793c45ee              running        z2  10.0.32.209   i-01e7d7cf7d117bf96  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
doppler/8d1fa381-c9d4-4b51-b195-c25d5d7a1a55              running        z1  10.0.31.212   i-048de3c6ad38a0184  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
doppler/ece4a895-03b9-47a1-9b48-9eaabaf258ef              running        z2  10.0.32.210   i-09a3cf0e5ac171012  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
haproxy/abb270ef-01e8-4d4c-941c-2187ca2cc8ad              running        z7  10.0.30.201   i-08af20c6712d54dd6  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
                                                                             54.180.53.80                                                    
log-api/7b45f808-22c4-45ff-a81c-74a20bac852a              running        z1  10.0.31.215   i-0b11b17bdbc23553e  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
log-api/dac3304c-f0a2-4c20-999d-db08ee39c7a7              running        z2  10.0.32.212   i-0b8426cba9bc7db7a  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
nats/35b3ab92-453f-4e9f-adf8-04477f41ee80                 running        z2  10.0.32.201   i-05a787d09b5a2df0a  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
nats/d08e1c80-bdf4-40c8-9134-16fb4a34ee11                 running        z1  10.0.31.201   i-04eddc4dfa9f9793e  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
router/0c77c858-f0c7-400c-868d-e96cd2dff4a9               running        z1  10.0.31.209   i-075290e50e0ef541d  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
router/5458b789-8ed0-4ba8-8093-6155ba1fa9b1               running        z2  10.0.32.207   i-02bc3f58d3c0306c9  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
scheduler/348e2a4e-2da7-47a3-92f8-8bf3b00e9bf0            running        z1  10.0.31.208   i-0a0b2bd3e712f0b26  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
scheduler/f56a196b-1f76-4ecc-b721-9b7fd04b8a94            running        z2  10.0.32.206   i-0c0917f591ce872f5  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
singleton-blobstore/af6b0c3a-27d0-46ef-b432-0b5c8e81519d  running        z1  10.0.31.205   i-0c519ef6d50d74d1e  small               true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
tcp-router/891c0b3e-4de6-44a5-a98b-96dd0490cac3           running        z2  10.0.32.208   i-084e044926e602669  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
tcp-router/ff3e0a98-092c-4e4c-a20c-0c0abf094a44           running        z1  10.0.31.210   i-076ef16b4d4114f83  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
uaa/3e0f17c1-cd11-4ce6-b3b8-bf1b0f45aa9f                  running        z1  10.0.31.204   i-0454401aa5fcf61fb  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
uaa/f8f6b0e8-2bbf-4be5-8f69-ac8dc7a3d943                  running        z2  10.0.32.203   i-0abd8df56336a799e  minimal             true    bosh-openstack-kvm-ubuntu-bionic-go_agent/1.34  
30 vms
Succeeded
```

<br>

## <div id='2.8'/>2.8.  PaaS-TA AP Login

Install CF CLI and log in to PaaS-TA AP.  
For CF CLI installation, choose between v6 and v7.  
Use the System Domain Name assigned when deploying PaaS-TA AP for CF API.

- CF CLI v6 Installation

```
$ wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add -
$ echo "deb https://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list
$ sudo apt update
$ sudo apt install cf-cli -y
$ cf --version
```

- CF CLI v7 Installation (PaaS-TA AP 5.1.0 and above)

```
$ wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add -
$ echo "deb https://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list
$ sudo apt update
$ sudo apt install cf7-cli -y
$ cf --version
```

- CF API URL Settings

> $ cf api api.{system_domain} --skip-ssl-validation
```
ubuntu@inception:~$ cf api api.54.180.53.80.nip.io --skip-ssl-validation
Setting api endpoint to api.54.180.53.80.nip.io...
OK
api endpoint:   https://api.54.180.53.80.nip.io
api version:    3.87.0
```

- PaaS-TA AP Login

> $ cf login
```
ubuntu@inception:~$ cf login
API endpoint: https://api.54.180.53.80.nip.io
Email> admin
Password>
Authenticating...
OK
Select an org (or press enter to skip):
```


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > PaaS-TA (+Monitoring Dashboard)
