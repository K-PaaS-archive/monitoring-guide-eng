### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > [SaaS] Pinpoint Monitoring


# Pinpoint Monitoring Install Guide
1\. [Document Outline](#1)  
　● [Purpose](#11)  
　● [Range](#12)  
　● [System Configuration Diagram](#13)  
　● [References](#14)  
2\. [Pinpoint Servicepack Installation](#2)  
　2.1 [Prerequisite](#21)  
　2.2 [Download Installation File](#22)  
　2.3 [Pinpoint Monitoring Installation Configuration Settings](#23)  
　　● [common_vars.yml](#231)  
　　● [pinpoint-vars.yml](#232)  
　　● [deploy-pinpoint.sh](#233)  
　　● [deploy-pinpoint-vsphere.sh](#234)  
　2.4. [Pinpoint Monitoring Installation](#24)  
　2.5. [Check Service Installation](#25)  
　2.6. [Security-Group Registration](#26)  
　2.7. [Pinpoint User-Provided Service Registration](#27)  
3\. [Sample Web App Interwork Pinpoint Interwork](#3)  
　● [Sample Web App Configuration](#31)  
　● [Apply for Service Bind and App Check at the Sample Web App](#32)  

# <div id='1'> 1. Document Outline
## <div id='11'> ● Purpose

This document (SaaS Monitoring Pinpoint Service Pack Installation Guide) describes how to install the Pinpoint service pack, a service pack provided by PaaS-TA based on the e-government standard framework, using BOSH 2.0, and how to use the Pinpoint service in an application that provides PaaS-TA as a SaaS.  
Starting with PaaS-TA 3.5 version, deploys are based on BOSH 2.0, and if you want to install based on existing BOSH 1.0, refer to the documentation of PaaS-TA 3.1 or lower.

## <div id='12'> ● Range
The installation range was created based on the basic installation to verify the Pinpoint service pack.

## <div id='13'> ● System Configuration Diagram

It is the installed system configuration diagram of this document.  
Minimum was configured using the Pinpoint Server, HBase의 HBase Master, Collector , and WebUI2. 

![](images/pinpoint-image1.png)

<table>
  <tr>
    <th>Classification</th>
    <th>Resource Pool</th>
    <th>Spec</th>
  </tr>
  <tr>
  <td>collector      </td><td>pinpoint_medium</td><td>2vCPU / 2GB RAM / 8GB Disk</td>
  </tr>
  <td>h_master      </td><td>pinpoint_medium</td><td>2vCPU / 2GB RAM / 8GB Disk</td>
  </tr>
  <tr>
  <td>haproxy_webui </td><td>services-small</td><td>1vCPU / 1GB RAM / 4GB Disk</td>
  </tr>
  <tr>
  <td>pinpoint_web          </0><td>services-small	</td><td>1vCPU / 1GB RAM / 4GB Disk</td>
  </tr>
</table>

## <div id='14'> ● References
[**http://bosh.io/docs**](http://bosh.io/docs)  
[**http://docs.cloudfoundry.org/**](http://docs.cloudfoundry.org/)

## <div id='2'> 2. Pinpoint Servicepack Installation

### <div id='21'> 2.1. Prerequisite

1. BOSH should be installed and BOSH Login should be done.
2. Check if the cloud-config and runtime-config are updated.
3. Check the Stemcell list to make sure that the Stemcell (ubuntubionic 1.34) required for service installation is uploaded.


> Check cloud-config   
> $ bosh -e {director-name} cloud-config  

> Check runtime-config   
> $ bosh -e {director-name} runtime-config  

> Check stemcell   
> $ bosh -e {director-name} stemcells  



## <div id='22'/>2.2.  Download Installation File

- Download if the deployment for PaaS-TA installation does not exist
```
$ cd ${HOME}/workspace
$ git clone https://github.com/paas-ta/common.git 
$ git clone https://github.com/paas-ta/monitoring-deployment.git
```



## <div id='23'> 2.3. Pinpoint Monitoring Installation Configuration Setting

$ Uner the {HOME}/workspace/monitoring-deployment/pinpoint-monitoring directory, there is a Shell Script file for Pinpoint Monitoring Installation.
	
### <div id='231'/>● common_vars.yml
The common_vars.yml in the common folder contains a common variable setting file that is applied when installing PaaS-TA and various services.  
When installing Pinpoint-Monitoring, you can install it by changing the saas_monitoring_url value.  

```
# BOSH INFO
bosh_ip: "10.0.1.6"				# BOSH IP
bosh_url: "https://10.0.1.6"				# BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"			# BOSH Client Admin ID
bosh_client_admin_secret: "giej8ett7mqsho9tx7s3"	# BOSH Client Admin Secret(can be checked with 'echo $(bosh int ~/workspace/paasta-5.0/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' command)
bosh_director_port: 25555			# BOSH director port
bosh_oauth_port: 8443				# BOSH oauth port
bosh_version: 271.2				# BOSH version(can be checked with 'bosh env' command, for on-demand service, e.g. "271.2")

# PAAS-TA INFO
system_domain: "10.0.1.80.nip.io"		# Domain (When using nip.io, it is the same with the HAProxy Public IP)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password
paasta_nats_ip: "10.0.1.121"
paasta_nats_port: 4222
paasta_nats_user: "nats"
paasta_nats_password: "7EZB5ZkMLMqT73h2JtxPv1fvh3UsqO"	# PaaS-TA Nats Password (can be checked with 'credhub get -n /micro-bosh/paasta/nats_password' command after CredHub Login)
paasta_nats_private_networks_name: "default"	# Network Name of PaaS-TA Nats
paasta_database_ips: "10.0.1.123"		# PaaS-TA Database IP (e.g. "10.0.1.123")
paasta_database_port: 5524			# PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysql
paasta_database_type: "postgresql"                      # PaaS-TA Database Type (e.g. "postgresql" or "mysql")
paasta_database_driver_class: "org.postgresql.Driver"   # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")
paasta_cc_db_id: "cloud_controller"		# CCDB ID (e.g. "cloud_controller")
paasta_cc_db_password: "cc_admin"		# CCDB Password (e.g. "c418e687c4Kx!" 8+ digits mixed with English/numeric/special characters)
paasta_uaa_db_id: "uaa"				# UAADB ID (e.g. "uaa")
paasta_uaa_db_password: "uaa_admin"		# UAADB Password (e.g. "ifb2497iEA5!" 8+ digits mixed with English/numeric/special characters)
paasta_api_version: "v3"

# UAAC INFO
uaa_client_admin_id: "admin"			# UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"		# Secret Variable to access to UAAC Admin Client
uaa_client_portal_secret: "clientsecret"	# Secret Variable to access to UAAC Portal Client

# Monitoring INFO
metric_url: "10.0.1.101"			# Monitoring InfluxDB IP
elasticsearch_master_ip: "10.0.1.105"           # elasticsearch master IP of Logsearch
elasticsearch_master_port: 9200                 # elasticsearch master Port of Logsearch
index_retention_period: "10"                    # logstash index retention period(Days) of Logsearch
syslog_address: "10.0.1.100"            	# ls-router IP of Logsearch
syslog_port: "2514"                          	# ls-router Port of Logsearch
syslog_transport: "relp"                        # Logsearch Protocol
saas_monitoring_url: "61.252.53.248"	   	# Public IP of Pinpoint HAProxy WEBUI
monitoring_api_url: "61.252.53.241"        	# Public IP of Monitoring-WEB

### Portal INFO
portal_web_user_ip: "52.78.88.252"
portal_web_user_url: "http://portal-web-user.52.78.88.252.nip.io" 

### ETC INFO
abacus_url: "http://abacus.61.252.53.248.nip.io"	# abacus url (e.g. "http://abacus.xxx.xxx.xxx.xxx.nip.io")

### Zabbix Agent for monitoring ###
server_ip: "10.37.2.114"               # Zabbix server (or Proxy) IP address
listen_port: "10050"                   # Zabbix server (or Proxy) Port number
server_active: "10.37.2.114:10051"     # Zabbix server (or Proxy) IP:Port string
host_metadata: "paasta"                # Metadata for Zabbix Agent autoregistration
```


### <div id='232'>● pinpoint-vars.yml
	
If the value of PemSSH is set to true to access the VM you want to monitor, you must copy the key that permitted you to create the VM in IaaS in the same folder to pem.yml in the same format.

```
### On-Demand Bosh Deployment Name Setting ###
inception_os_user_name: "ubuntu"
deployment_name: "pinpoint-monitoring"                  # On-Demand Deployment Name

### Main Stemcells Setting ###
stemcell_os: "ubuntu-bionic"                            # Deployment Main Stemcell OS
stemcell_version: "1.61"                                # Main Stemcell Version
stemcell_alias: "default"                               # Main Stemcell Alias

### On-Demand Release Deployment Setting ###
releases_name:  "paasta-pinpoint-monitoring-release"    # On-Demand Release Name
public_networks_name: "vip"                             # Pinpoint Public Network Name
PemSSH: "true"                                          # Whether the h_master specifies the Key File to use for SSH access to the VM it is monitoring(default:false)

# H-Master
h_master_azs: ["z1"]                                    # H-Master Available Zone
h_master_instances: 1                                   # H-Master Instance Number
h_master_vm_type: "small-highmem-16GB"                  # H-Master VM Type
h_master_network: "default"                             # H-Master Network
h_master_persistent_disk_type: "30GB"                   # H-Master Permanent Disk Type

# COLLECTOR
collector_azs: ["z1"]                                   # Collector Available Zone
collector_instances: 1                                  # Collector Instance Number
collector_vm_type: "small-highmem-16GB"                 # Collector VM Type
collector_network: "default"                            # Collector Network
collector_persistent_disk_type: "30GB"                  # Collector Permanent Disk Type

# PINPOINT
pinpoint_web_azs: ["z1"]                                # Pinpoint Available Zone
pinpoint_web_instances: 1                               # Pinpoint Instance Number
pinpoint_web_vm_type: "small-highmem-16GB"              # Pinpoint VM Type
pinpoint_web_network: "default"                         # Pinpoint Network
pinpoint_web_persistent_disk_type: "30GB"               # Pinpoint Permanent Disk Type

# HAPROXY
haproxy_webui_azs: ["z1"]                               # HAProxy Available Zone
haproxy_webui_instances: 1                              # HAProxy Instance Number
haproxy_webui_vm_type: "small-highmem-16GB"             # HAProxy VM Type
haproxy_webui_network: "default"                        # HAProxy Network
haproxy_webui_persistent_disk_type: "30GB"              # HAProxy Permanent Disk Type
```

### <div id='233'>● deploy-pinpoint.sh
```
echo 'y' | bosh -e micro-bosh -d pinpoint-monitoring deploy paasta-pinpoint.yml \
	-o use-public-network.yml \
	-o addons/enable-zabbix-agent.yml \
	-l pinpoint-vars.yml \
	-l ../../common/common_vars.yml \
	-l pem.yml
```

### <div id='234'>● deploy-pinpoint-vsphere.sh
```
echo 'y' | bosh -e micro-bosh -d pinpoint-monitoring deploy paasta-pinpoint.yml \
	-o use-public-network-vsphere.yml \
	-l pinpoint-vars.yml \
	-l ../../common/common_vars.yml \
	-l pem.yml
```

## <div id='24'> 2.4. Pinpoint Monitoring Installation
	
- Modify the settings in the Deploy script file to suit the server environment. 

> $ vi ${HOME}/workspace/monitoring-deployment/pinpoint-monitoring/deploy-pinpoint.sh

```
echo 'y' | bosh -e micro-bosh -d pinpoint-monitoring deploy paasta-pinpoint.yml \
	-o use-public-network.yml \
	-o addons/enable-zabbix-agent.yml \
	-l pinpoint-vars.yml \
	-l ../../common/common_vars.yml \
	-l pem.yml
```

- Execute Pinpoint Monitoring Installation Shell Script File (BOSH Login Required)

```
$ cd ~/workspace/monitoring-deployment/paasta-monitoring
$ sh deploy-pinpoint.sh
```

## <div id='25'/>2.5. Service Installation Check
Verify if the Pinpoint Monitoring installation is completed.
```
$ bosh –e {director_name} vms


$ bosh -e micro-bosh -d paasta-pinpoint-monitoring vms
Deployment 'paasta-pinpoint-monitoring'

Instance                                            Process State  AZ  IPs           VM CID               VM Type             Active  
collector/a7932462-5a55-4ad6-9a50-6d9775d8391a      running        z3  10.0.81.122   i-0104012f0c4cf1051  caas_small_highmem  true  
h_master/7024f1d8-7911-4cc6-ac5c-8d9295221efa       running        z3  10.0.81.121   i-02b1cd70c35117d8d  caas_small_highmem  true  
haproxy_webui/b30b856c-ad74-4ff5-a9ee-32e2ef641ffa  running        z7  10.0.0.122    i-046052aa5360f6b6f  caas_small_highmem  true  
								       15.165.3.150                                             
pinpoint_web/c23b79cf-ef55-42f5-9c2a-b8102b6e5ca8   running        z3  10.0.81.123   i-02a82ab6f02784317  caas_small_highmem  true 
```


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Pinpoint Monitoring
