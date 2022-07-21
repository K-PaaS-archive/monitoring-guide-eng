### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Monitoring Dashboard


# Monitoring Dashboard Install Guide
1. [Outline](#1)  
2. [PaaS-TA Monitoring Dashboard Installation](#2)  
  2.1. [Prerequisite](#2.1)  
  2.2. [Download Installation File](#2.2)  
  2.3. [Settings for PaaS-TA Monitoring Dashboard Installation](#2.3)  
  2.4. [PaaS-TA Monitoring Dashboard Installation](#2.4)  
3. [PaaS-TA Monitoring Dashboard Access](#3)  


## <div id="1">1. Outline
This document describes the installation of the PaaS-TA Monitoring Dashboard for monitoring the BOSH-based PaaS-TA (AP) environment.


## <div id="2">2. PaaS-TA Monitoring Dashboard Installation


### <div id="2.1">2.1. Prerequisite
1. BOSH Installation and BOSH Login must be done.
2. Check if the 'cloud-config' and 'runtime-config' is updated.
3. Check the Stemcell List and verify if the Stemcell needed for installation is uploaded.
4. PaaS-TA (AP) must be installed, including monitoring options during BOSH and PaaS-TA (AP) installation.
5. If monitoring of the IaaS environment is required, the installation of the Zabbix Package must be preceded.

**│ cloud-config Check**

```shell script
$ bosh -e {director-name} cloud-config
```

**│ runtime-config Check**

```shell script
$ bosh -e {director-name} runtime-config
```

**│ Stemcell Check**

```shell script
$ bosh -e {director-name} stemcells
```


### <div id="2.2">2.2. Download Installation File
Download Deployment for installing PaaS-TA Monitoring Dashboard. Skip if all the installation deployment was downloaded.

```shell script
$ cd ~/workspace

$ git clone https://github.com/paas-ta/common.git
$ git clone https://github.com/PaaS-TA/paasta-deployment.git
$ git clone https://github.com/paas-ta/monitoring-deployment.git
```


### <div id="2.3">2.3. Settings for PaaS-TA Monitoring Dashboard Installation
**│ common_vars.yml**

The `common/common_vars.yml` file contains the PaaS-TA environment and the common variable settings required for various deployment installations. The following file examples can be written by referring to the actual environment configuration.

```yaml
# BOSH INFO
bosh_ip: "10.200.1.6"                                  # BOSH IP
bosh_url: "https://10.200.1.6"                         # BOSH URL (e.g. "https://xxx.xxx.xxx.xxx")
bosh_client_admin_id: "admin"                          # BOSH Client Admin ID
bosh_client_admin_secret: "tux6o6pbh5onzm7up7ve"       # BOSH Client Admin Secret(can be checked through the 'echo $(bosh int ~/workspace/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' command)
bosh_director_port: 25555                              # BOSH director port
bosh_oauth_port: 8443                                  # BOSH oauth port
bosh_version: 271.2                                    # BOSH version(can be checked through the 'bosh env' command, for on-demand service, e.g. "271.2")

# PAAS-TA INFO
system_domain: "10.200.1.80.nip.io"                    # Domain (When using nip.io, it is the same with the HAProxy Public IP)
paasta_admin_username: "admin"                         # PaaS-TA Admin Username
paasta_admin_password: "admin"                         # PaaS-TA Admin Password
paasta_nats_ip: "10.200.1.121"
paasta_nats_port: 4222
paasta_nats_user: "nats"
paasta_nats_password: "x2OgPGck4ttWOf8GYu7SMRrqCe69H1" # PaaS-TA Nats Password (can be checked with the 'credhub get -n /micro-bosh/paasta/nats_password' command after logging in to the CredHub)
paasta_nats_private_networks_name: "default"           # Network Name of PaaS-TA Nats
paasta_database_ips: "10.200.1.123"                    # PaaS-TA Database IP (e.g. "10.0.1.123")
paasta_database_port: 5524                             # PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysql
paasta_database_type: "postgresql"                     # PaaS-TA Database Type (e.g. "postgresql" or "mysql")
paasta_database_driver_class: "org.postgresql.Driver"  # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")
paasta_cc_db_id: "cloud_controller"                    # CCDB ID (e.g. "cloud_controller")
paasta_cc_db_password: "cc_admin"                      # CCDB Password (e.g. "c418e687c4Kx!"  8+ digits mixed with English/numeric/special characters)
paasta_uaa_db_id: "uaa"                                # UAADB ID (e.g. "uaa")
paasta_uaa_db_password: "uaa_admin"                    # UAADB Password (e.g. "ifb2497iEA5!"  8+ digits mixed with English/numeric/special characters)
paasta_api_version: "v3"

# UAAC INFO
uaa_client_admin_id: "admin"                           # UAAC Admin Client Admin ID
uaa_client_admin_secret: "admin-secret"                # Secret Variable to access to the UAAC Admin Client
uaa_client_portal_secret: "clientsecret"               # Secret Variable to access to the UAAC Portal Client

# Monitoring INFO
metric_url: "10.200.1.105"                             # Monitoring InfluxDB IP
elasticsearch_master_ip: "10.200.1.101"                # elasticsearch master IP of Logsearch
elasticsearch_master_port: 9200                        # elasticsearch master Port of Logsearch
index_retention_period: "10"                           # Logsearch's logstash index retention period (Days)
syslog_address: "10.200.1.100"                         # ls-router IP of Logsearch
syslog_port: "2514"                                    # ls-router Port of Logsearch
syslog_transport: "relp"                               # Logsearch Protocol
saas_monitoring_url: "61.252.53.248"                   # Public IP of Pinpoint HAProxy WEBUI
monitoring_api_url: "61.252.53.241"                    # Public IP of Monitoring-WEB

### Portal INFO
portal_web_user_ip: "52.78.88.252"
portal_web_user_url: "http://portal-web-user.52.78.88.252.nip.io" 

### ETC INFO
abacus_url: "http://abacus.61.252.53.248.nip.io"       # abacus url (e.g. "http://abacus.xxx.xxx.xxx.xxx.nip.io")

### Zabbix Agent for monitoring ###
server_ip: "10.200.1.200"                              # Zabbix server (or Proxy) IP address
listen_port: "10050"                                   # Zabbix server (or Proxy) Port number
server_active: "10.200.1.200:10051"                    # Zabbix server (or Proxy) IP:Port string
host_metadata: "paasta"                                # Metadata for Zabbix Agent autoregistration
```

**│ paasta-monitoring-vars.yml**  

The `monitoring-deployment/paasta-monitoring/paasta-monitoring-vars.yml` file contains various variable settings required for installing the PaaS-TA Monitoring Dashboard. Within the contents, the `mariadb` and `influxdb` accounts have security settings and account information connected between modules, so if they are changed and deployed, the related modules may not operate normally. Therefore, modification of the relevant files in the `job` directory is required in the [monitoring-dashboard-release](https://github.com/PaaS-TA/monitoring-dashboard-release) or [monitoring-influxdb-release](https://github.com/PaaS-TA/monitoring-influxdb-release) repository. The following file examples can be written by referring to the actual environment configuration.

```yaml
# SERVICE VARIABLE
inception_os_user_name: "ubuntu"
mariadb_ip: "10.200.1.102"            # MariaDB VM Private IP
mariadb_port: "7706"                  # PaaS-TA Database Port (e.g. 7706(mariadb)) -- Do Not Use "3306" in mysql
mariadb_username: "root"              # MariaDB Root Account Username
mariadb_password: "PaaS-TA2020"       # MariaDB Root Account Password. 8+ digits mixed with English/numeric/special characters
director_name: "micro-bosh"           # BOSH Director Name
resource_url: "resource_url"          # TBD
paasta_deploy_name: "paasta"          # PaaS-TA Deployment Name
paasta_cell_prefix: "cell"            # PaaS-TA Cell Name
smtp_url: "smtp.naver.com"            # SMTP Server URL
smtp_port: "587"                      # SMTP Server Port
mail_sender: "aaa@naver.com"          # SMTP Server Admin ID
mail_password: "aaaa"                 # SMTP Server Admin Password
mail_enable: "true"                   # Whether to send mail when an alarm occurs
mail_tls_enable: "true"               # When authenticating SMTP server is True for TLS mode 
redis_ip: "10.200.1.103"              # Redis Private IP
redis_password: "PaaS-TA2020"         # Redis Authentication Password. 8+ digits mixed with English/numeric/special characters
redis_protected_mode: "yes"           # Redis Loopback Interface Query Response Settings
influxdb_username: "admin"            # Influxdb Admin Account Username
influxdb_password: "PaaS-TA2020"      # Influxdb Admin Account Password
influxdb_interval: "30d"              # Influxdb Retention Policy (bootstrapper)
influxdb_https_enabled: "true"        # Influxdb HTTPS Setting
elasticsearch_username: "admin"       # Elasticsearch admin Account Username
elasticsearch_password: "logsearch"   # Elasticsearch Admin Account Password
elasticsearch_https_enabled: "true"   # Elasticsearch HTTPS Setting
utc_time_gap: "9"                     # Time difference between UTC Time Zone and Client Time Zone
public_network_name: "vip"            # Monitoring-WEB Public Network Name
system_type: "PaaS,CaaS,SaaS,IaaS"    # Select Environment to Monitor
prometheus_ip: "52.78.16.121"         # Node IP of Kubernates prometheus-prometheus-prometheus-oper-prometheus-0 Pod
kubernetes_ip: "52.78.16.121"         # Service API IP of Kubernetes
pinpoint_was_ip: "10.0.1.152"         # Pinpoint HAProxy WEBUI Private IP
cassbroker_ip: "3.36.38.199"          # API IP (Kubernetes Master IP/service-common-api-deployment) for processing CaaS service login authentication
kubernetes_token: "eyJhbGciOiJSUzI1NiIsImtpZCI6IlMtbHpvLUYybGpiejl4c1ZqelRGRnFlX3pxaVFkS2NQOWhpWEJLS1JXckUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrOHNhZG1pbi10b2tlbi12cWhudiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrOHNhZG1pbiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImExMGVkODgyLTU1ZGEtNGNmYS04Y2ZmLThiOTdiZTQzODE4MSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTprOHNhZG1pbiJ9.QZ3mTvdyaPwzDt7mtDoDYlaCxEqN7QIpWTIpUUCJm6qurOonhJd_kdmvhQsylkft6z-Pp4odMr6eQcTD3WwUQ95UMqvQirLZOjLF7MU07CBIxWvhIDK5qx3xrYEa5KswuhfTNre4py9I-tPV9_vHxRNcwZUjnBGo_BimuwJgqG_1LsZjXvdIeJfMQlEY2LokUDt07bMoUtH9sWLKHv8-rZfiMU1fGfifz9NwQ54Uh-CVspbg-YaJbVC6tnbyriuTNciViOth6Onwx_xuzBfFGRlh6ck0HlYKYz5-JxEpZy3zJwoNfukVmjl-4gNXb67Esm7rQyjHFMF2Z2c2EgDQEw"

# Zabbix API INFO
zabbix_host: "http://10.200.1.200/zabbix/api_jsonrpc.php"
zabbix_admin_id: "Admin"
zabbix_admin_pw: "zabbix"

# Openstack INFO
openstack_region: "RegionOne"
openstack_domain: "default"
openstack_username: "paas-ta-monitoring"
openstack_password: "paas-ta-monitoring!@#"
openstack_tenant_name: "paas-ta-monitoring"
openstack_tenant_id: "3f7134b979074ea493c265ca39cc1ead"
openstack_endpoint: "http://xxx.xxx.xxx.xxx:5000/v3"
openstack_keystone_url: "http://xxx.xxx.xxx.xxx:5000/v3"

# STEMCELL
stemcell_os: "ubuntu-bionic"               # Stemcell OS
stemcell_version: "1.61"                   # Stemcell Version

# REDIS
redis_azs: ["z1"]                          # Redis Available Zones
redis_instances: 1                         # Redis Instance Number
redis_vm_type: "small"                     # Redis VM Type
redis_network: "default"                   # Redis Network

# SANITY-TEST
sanity_tests_azs: ["z1"]                   # Sanity-Test Available Zones
sanity_tests_instances: 1                  # Sanity-Test Instance Number
sanity_tests_vm_type: "small"              # Sanity-Test VM Type
sanity_tests_network: "default"	           # Sanity-Test Network

# INFLUXDB
influxdb_azs: ["z1"]                       # InfluxDB Available Zones
influxdb_instances: 1                      # InfluxDB Instance Number
influxdb_vm_type: "large"                  # InfluxDB VM Type
influxdb_network: "default"                # InfluxDB Network
influxdb_persistent_disk_type: "10GB"      # InfluxDB Permanent Disk Type

# MARIADB
mariadb_azs: ["z1"]                        # MariaDB Available Zones
mariadb_instances: 1                       # MariaDB Instance Number
mariadb_vm_type: "medium"                  # MariaDB VM Type
mariadb_network: "default"                 # MariaDB Network
mariadb_persistent_disk_type: "5GB"        # MariaDB Permanent Disk Type

# MONITORING-BATCH
monitoring_batch_azs: ["z1"]               # Monitoring-Batch Available Zones
monitoring_batch_instances: 1              # Monitoring-Batch Instance Number
monitoring_batch_vm_type: "small"          # Monitoring-Batch VM Type
monitoring_batch_network: "default"        # Monitoring-Batch Network

# IAAS-MONITORING-BATCH
iaas_monitoring_batch_azs: ["z1"]          # IAAS-Monitoring-Batch Available Zones
iaas_monitoring_batch_instances: 1         # IAAS-Monitoring-Batch Instance Number
iaas_monitoring_batch_vm_type: "small"     # IAAS-Monitoring-Batch VM Type
iaas_monitoring_batch_network: "default"   # IAAS-Monitoring-Batch Network

# CAAS-MONITORING-BATCH
caas_monitoring_batch_azs: ["z1"]          # CAAS-Monitoring-Batch Available Zones
caas_monitoring_batch_instances: 1         # CAAS-Monitoring-Batch Instance Number
caas_monitoring_batch_vm_type: "small"     # CAAS-Monitoring-Batch VM Type
caas_monitoring_batch_network: "default"   # CAAS-Monitoring-Batch Network

# SAAS-MONITORING-BATCH
saas_monitoring_batch_azs: ["z1"]          # SAAS-Monitoring-Batch Available Zones
saas_monitoring_batch_instances: 1         # SAAS-Monitoring-Batch Instance Number
saas_monitoring_batch_vm_type: "small"     # SAAS-Monitoring-Batch VM Type
saas_monitoring_batch_network: "default"   # SAAS-Monitoring-Batch Network

# MONITORING-WEB
monitoring_web_azs: ["z1"]                 # Monitoring-WEB Available Zones
monitoring_web_instances: 1                # Monitoring-WEB Instance Number
monitoring_web_vm_type: "small"            # Monitoring-WEB VM Type
monitoring_web_network: "default"          # Monitoring-WEB Network
```

**│ deploy-paasta-monitoring.sh**  

```shell script
#!/bin/bash

# Verify that the IaaS monitoring option is used in the paasta-monitoring-vars.yml and branch whether or not to deploy the iaas-monitoring-batch VM depending on the conditions.
ScanIaaSOption=`grep IaaS paasta-monitoring-vars.yml`

if [ -n "$ScanIaaSOption" ]; then
    bosh -e micro-bosh -n -d paasta-monitoring deploy paasta-monitoring.yml \
        -o use-public-network-aws.yml \
        -o addons/enable-zabbix-agent.yml \
        -l paasta-monitoring-vars.yml \
        -l ../../common/common_vars.yml
else
    bosh -e micro-bosh -n -d paasta-monitoring deploy paasta-monitoring.yml \
        -o use-public-network-aws.yml \
        -o addons/enable-zabbix-agent.yml \
        -o addons/disable-iaas-monitoring-batch.yml \
        -l paasta-monitoring-vars.yml \
        -l ../../common/common_vars.yml
fi
```

### <div id="2.4">2.4. PaaS-TA Monitoring Dashboard Installation
If the previous steps have completed the configuration of manifest files and variable settings files for installing the PaaS-TA Monitoring Dashboard, the installation can be started using the `deploy-paasta-monitoring.sh` file.

```shell script
$ cd ~/workspace/monitoring-deployment/paasta-monitoring
$ source deploy-paasta-monitoring.sh
```

If the deployment is complete, check the deployment status using the following command:

```shell script
$ bosh vms
```

![](images/paasta-monitoring_5.0.png)


## <div id="3">3. PaaS-TA Monitoring Dashboard Access
You can use the PaaS-TA Monitoring Dashboard after creating an account by accessing ***http://{monitoring-web_ip}:8080/public/login.html***using a web browser. Click the **Register now!** button on the Login screen.

![](images/monit_login_5.0.png)

In the PaaS Certification part, enter the account information set in the common variable file (`common_vars.yml`)when deploying PaaS-TA (AP). In the IaaS Certification item, enter the account information set in the variable file (`paasta-monitoring-vars.yml`)required for monitoring and deployment. In the Member Info entry, enter the ID and password that the user will use. For each item, you must click the **Authenticate** button to verify authentication, and click the **Join** button to complete creating the account. 

![](images/member_join_5.0.png)

If you succeed in logging in using the generated account information, you can check the main screen of the dashboard as shown below. If the same, you have succeeded in installing the PaaS-TA Monitoring Dashboard.

![](images/monit_main_5.0.png)


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Monitoring Dashboard
