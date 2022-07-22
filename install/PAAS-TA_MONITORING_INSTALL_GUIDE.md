### [Index](https://github.com/PaaS-TA/Guide-eng) > Monitoring Install


# PaaS-TA Monitoring Install Guide


## Required Installation List
> ⚠️ When using monitoring function at the <b>PaaS-TA {Platform}</b>, **installation with necessary options applied for monitoring function** is required before installing <b>BOSH</b>and <b>PaaS-TA</b>.

- [BOSH (+Monitoring Dashboard Option) Installation](PAAS-TA_BOSH2_MONITORING_INSTALL_GUIDE.md)
- [PaaS-TA (+Monitoring Dashboard Option) Installation](PAAS-TA_CORE_MONITORING_INSTALL_GUIDE.md)
- [Monitoring Dashboard Installation](PAAS-TA_MONITORING_MONITORING_DASHBOARD_INSTALL.md)


## Selective installation items
> ⚠️ Note that the **Zabbix Package** installation must precede **Required Installation Items** to prevent dependency issues such as entering option information related to the IaaS monitoring environment during deployment only when using the **IaaS monitoring option**.

+ [**[IaaS] Infrastructure Monitoring** - Zabbix Package](#)
  + [Zabbix Server Installation](PAAS-TA_MONITORING_ZABBIX-SERVER_INSTALL.md)
  + [Zabbix Proxy Installation](PAAS-TA_MONITORING_ZABBIX-PROXY_INSTALL.md)
  + [Zabbix Agent Installation](PAAS-TA_MONITORING_ZABBIX-AGENT_INSTALL.md)
  
- [**[PaaS] Log Monitoring** - Logsearch Installation](PAAS-TA_MONITORING_LOGSEARCH_INSTALL.md)

+ [**[SaaS] Application Monitoring** - Pinpoint Monitoring Installation](PAAS-TA_MONITORING_PINPOINT_MONITORING_INSTALL.md)
  + [Creating and Registering Pinpoint Java Buildpack](PAAS-TA_MONITORING_PINPOINT_JAVA_BUILDPACK_CREATING_AND_UPLOADING.md)
  + [Push and Bind Application & Pinpoint Service](PAAS-TA_MONITORING_PINPOINT_APPLICATION_PUSHING_AND_BINDING.md)

- [**[CaaS] Container Monitoring** - Prometheus Installation](PAAS-TA_MONITORING_CONTAINER_SERVICE_INSTALL.md)


### [Index](https://github.com/PaaS-TA/Guide-eng) > Monitoring Install
