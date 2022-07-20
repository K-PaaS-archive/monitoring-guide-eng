### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > IaaS


# IaaS Monitoring Architecture


### │ IaaS  Monitoring Architecture
PaaS-TA IaaS monitoring refers to the monitoring of virtual machines running the PaaS-TA platform and the infrastructure areas in which they operate, or traditionally physical computing resources and is developed based on the **OpenStack open-source project** that implements this clustering of physical computing resources.  
Monasca was used as a monitoring tool before PaaS-TA 4.6 but was discontinued and is now replaced by Zabbix.  

<p align="center">
  <img src="images/iaas-monitoring-architecture.png">
</p>

When creating images in qcow2 format to collect or transmit monitoring information for VMs created per project in an OpenStack environment, the Zabbix monitoring environment is configured by installing the Zabbix Agent by default. When a VM is created using the created image, the Zabbix Agent runs and transfers the collected data to the Zabbix Proxy to reduce the load on the Zabbix Server and adopt a proxy configuration method that can be expected to improve performance.  
The VM created in the OpenStack environment has a problem that corresponds to a virtual network area that can communicate with an external network but cannot be accessed from the outside, and Zabbix Proxy is also responsible for the communication relay of external and internal networks to solve this problem. At this time, Public IP is allocated to the proxy node, and communication with the external network is possible, allowing the Zabbix Server to check the status of the Zabbix Proxy. Data relayed to the Zabbix Proxy is managed by sending monitoring data to the Zabbix Server, which is installed and running on the physical equipment.  
By installing/operating the Zabbix Agent together on a physical node (real server equipment configured for clustering), it is easy to check infrastructure system resource usage for that node.


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > IaaS
