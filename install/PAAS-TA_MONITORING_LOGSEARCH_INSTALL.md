### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > [PaaS] Logsearch


# Logsearch Install Guide

1\. [Outline](#1)  
2\. [Logsearch Installation](#2)  
　2.1. [Prerequisite](#3)  
　2.2. [Download Installation File](#4)  
　2.3. [Logsearch Installation Environment Setting](#5)   
　　● [common_vars.yml](#6)  
　　● [logsearch-vars.yml](#7)  
　　● [deploy-logsearch.sh](#8)  
　2.4. [Logsearch Installation](#9)  
　2.5. [Check Service Installation](#10)


## <div id='1'/>1. Outline

This document (Logsearch Installation Guide) describes how to install Logsearch using BOSH 2.0 to collect VM Log from BOSH and PaaS-TA before installing PaaS-TA Monitoring.

## <div id='2'/>2. Logsearch Installation
### <div id='3'/>2.1 Prerequisite

1. BOSH should be installed and BOSH Login should be done.
2. Check if cloud-config and runtime-config are updated.
3. Check Stemcell List and verify if the Stemcell(ubuntu bionic 1.34) required for service installation is uploaded.


> Check cloud-config   
> $ bosh -e {director-name} cloud-config  

> Check runtime-config   
> $ bosh -e {director-name} runtime-config  

> Check stemcell   
> $ bosh -e {director-name} stemcells  


### <div id='4'/>2.2. Download Installation File

- Download if the deployment for installing Logsearch does not exist
```
$ cd ${HOME}/workspace
$ git clone https://github.com/paas-ta/common.git
$ git clone https://github.com/paas-ta/monitoring-deployment.git
```

### <div id='5'/>2.3. Logsearch Installation Environment Setting

Logsearch must be installed to collect PaaS-TA VM Log. 

```
$ cd ${HOME}/workspace/monitoring-deployment/logsearch
```

### <div id='6'/>● common_vars.yml
The common_vars.yml in the common folder contains a common variable setting file that is applied when installing PaaS-TA and various services.  
When installing Logsearch, you can change the value of syslog_address and install.  
The syslog_address is installed with the same value as the variable value when installing BOSH and PaaS-TA, including the Monitoring option.

```
# BOSH INFO
bosh_ip: "10.0.1.6"				# BOSH IP
bosh_url: "https://10.0.1.6"				# BOSH URL (e.g. "https://00.000.0.0")
bosh_client_admin_id: "admin"			# BOSH Client Admin ID
bosh_client_admin_secret: "giej8ett7mqsho9tx7s3"	# BOSH Client Admin Secret(Can be checked with the 'echo $(bosh int ~/workspace/paasta-5.0/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' command)
bosh_director_port: 25555			# BOSH director port
bosh_oauth_port: 8443				# BOSH oauth port
bosh_version: 271.2				# BOSH version(Can be checked with the 'bosh env' command, for on-demand service, e.g. "271.2")

# PAAS-TA INFO
system_domain: "10.0.1.80.nip.io"		# Domain (When using nip.io, it is the same with the HAProxy Public IP)
paasta_admin_username: "admin"			# PaaS-TA Admin Username
paasta_admin_password: "admin"			# PaaS-TA Admin Password
paasta_nats_ip: "10.0.1.121"
paasta_nats_port: 4222
paasta_nats_user: "nats"
paasta_nats_password: "7EZB5ZkMLMqT73h2JtxPv1fvh3UsqO"	# PaaS-TA Nats Password (Can be checked with the 'credhub get -n /micro-bosh/paasta/nats_password' command after CredHub login)
paasta_nats_private_networks_name: "default"	# Network Name of the PaaS-TA Nats
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



### <div id='7'/>● logsearch-vars.yml

```
# SERVICE VARIABLE
inception_os_user_name: "ubuntu"                     # Deployment Name

# STEMCELL
stemcell_os: "ubuntu-bionic"                         # Stemcell OS
stemcell_version: "1.61"                             # Stemcell Version

# ELASTICSEARCH-MASTER
elasticsearch_master_azs: ["z1"]                     # Elasticsearch-Master Available Zone
elasticsearch_master_instances: 1                    # Elasticsearch-Master Instance Number
elasticsearch_master_vm_type: "medium"               # Elasticsearch-Master VM Type
elasticsearch_master_network: "default"              # Elasticsearch-Master Network
elasticsearch_master_persistent_disk_type: "50GB"    # Elasticsearch-Master 영구 Disk Type

# ELASTICSEARCH-DATA
elasticsearch_data_azs: ["z1"]                       # Elasticsearch-Data Available Zone
elasticsearch_data_instances: 1                      # Elasticsearch-Data Instance Number
elasticsearch_data_vm_type: "medium"                 # Elasticsearch-Data VM Type
elasticsearch_data_network: "default"                # Elasticsearch-Data Network
elasticsearch_data_persistent_disk_type: "50GB"      # Elasticsearch-Data Permanent Disk Type

# CLUSTER-MONITOR
cluster_monitor_azs: ["z1"]                          # Cluster-Monitor Available Zone
cluster_monitor_instances: 1                         # Cluster-Monitor Instance Number
cluster_monitor_vm_type: "medium"                    # Cluster-Monitor VM Type
cluster_monitor_network: "default"                   # Cluster-Monitor Network
cluster_monitor_persistent_disk_type: "10GB"         # Cluster-Monitor Permanent Disk Type

# MAINTENANCE
maintenance_azs: ["z1"]                              # Maintenance Available Zone
maintenance_instances: 1                             # Maintenance Instance Number
maintenance_vm_type: "medium"                        # Maintenance VM Type
maintenance_network: "default"                       # Maintenance Network
maintenance_persistent_disk_type: "5GB"

# KIBANA
kibana_azs: ["z1"]                                   # Kibana Available Zone
kibana_instances: 1                                  # Kibana Instance Number
kibana_vm_type: "medium"                             # Kibana VM Type
kibana_network: "default"                            # Kibana Network
kibana_persistent_disk_type: "5GB"                   # Kibana Permanent Disk Type

# INGESTOR
ingestor_azs: ["z1"]                                 # Ingestor Available Zone
ingestor_instances: 1                                # Ingestor Instance Number
ingestor_vm_type: "medium"                           # Ingestor VM Type
ingestor_network: "default"                          # Ingestor Network
ingestor_persistent_disk_type: "10GB"                # Ingestor Permanent Disk Type

# LS-ROUTER
ls_router_azs: ["z1"]                                # LS-Router Available Zone
ls_router_instances: 1                               # LS-Router Instance Number
ls_router_vm_type: "small"                           # LS-Router VM Type
ls_router_network: "default"                         # LS-Router Network
```

### <div id='8'/>● deploy-logsearch.sh
```
bosh -e micro-bosh -n -d logsearch deploy logsearch-deployment.yml \
	-o operations/enable-router.yml \
	-o addons/enable-zabbix-agent.yml \
	-l logsearch-vars.yml \
	-l ../../common/common_vars.yml
```

### <div id='9'/>2.4. Logsearch Installation

- Modify the settings in the Deploy script file to suit the server environment.  
  Is an online installation in default.

> $ vi ${HOME}/workspace/monitoring-deployment/logsearch/deploy-logsearch.sh

```
# Check if there are any issues to change the settings of the .yml files.  
bosh -e micro-bosh -n -d logsearch deploy logsearch-deployment.yml \
	-o operations/enable-router.yml \
	-o addons/enable-zabbix-agent.yml \
	-l logsearch-vars.yml \
	-l ../../common/common_vars.yml
```

- Execute Logsearch Installing Shell Script File (BOSH Login Required)

```
$ cd ~/workspace/monitoring-deployment/logsearch
$ sh deploy-logsearch.sh
```

### <div id='10'/>2.5. Check Service Installation
Verify if the installation of the logsearch is completed.
```
$ bosh –e {director_name} vms
```
![PaaSTa_logsearch_vms_5.0]


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Logsearch


<!-- Images Links -->
[PaaSTa_logsearch_vms_5.0]:./images/logsearch_5.0.png
