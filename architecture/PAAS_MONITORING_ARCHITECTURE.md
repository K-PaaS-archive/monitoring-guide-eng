### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > PaaS


# PaaS Monitoring Architecture


### │ PaaS Monitoring Architecture
The PaaS Monitoring operating environment is largely divided into the Batch process area running in the Backend environment and the Monitoring system area running in the Frontend environment.  
The Batch process periodically checks and analyzes system metrics information based on registered threshold information and AutoScale target information in PaaS-TA Portal, sends an alarm(notification) to the administrator when service exceeds the threshold, and sends relevant information to the AutoScale Server service when the container exits.  
The PaaS-TA Monitoring system queries system environmental information data from TSDB (InfluxDB) and queries log information through Lucene (Elasticsearch).  
The current resource usage of the PaaS-TA Monitoring system is checked with the inquired information, and log information can be checked through the PaaS-TA Monitoring Dashboard.  
PaaS-TA Monitoring Dashboard provides a screen that allows you to check event status information that has an alarm(notification) on the administrator screen, check statistics on container placement status and failure service and history management of event-related processing information.  

![PaaSTa_Monit_architecure_Image]


### │ PaaS Collecting Metric Data Architecture
PaaS provides a Metric Agent that internally collects and delivers metric information and a Syslog module that collects and delivers log information.  
Metric Agent collects matric related to the system and saves the collected information at the InfluxDB.  
Syslog can also forward log information to ELK services by setting the manifest file to deploy PaaS-TA and using the Reliable Event Logging Protocol (RELP) to forward log information.

![PaaSTa_Monit_collect_architecure_Image]


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > PaaS


<!-- Images Links -->
[PaaSTa_Monit_architecure_Image]:./images/monit_architecture.png
[PaaSTa_Monit_collect_architecure_Image]:./images/collect_architecture.png
