### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > CaaS


# CaaS Monitoring Architecture


### │ CaaS Monitoring Architecture
The CaaS Monitoring operating environment is largely divided into the Batch process area running in the Backend environment and the Monitoring system area running in the Frontend environment.  
The Batch process periodically checks and analyzes system metrics information based on threshold information registered by CaaS and sends an alarm(notification) to the administrator when a service that exceeds the threshold is found.  
The PaaS-TA Monitoring system checks system metric data from the Prometheus Agent (K8s) and checks the current resource usage of the CaaS Monitoring system with the checked information.  
PaaS-TA Monitoring Dashboard provides a screen that allows checking on event status information that has a notification. Checks on the statistics about Kubernetes Pod status and services, and manage event-related processing information.  

![Caas_Monit_architecure_Image]


### │ CaaS Collecting Metric Data Architecture
CaaS provides Prometheus Metric Agent(Node Exporter, cAdvisor) that internally collects metric information.  
Prometheus stores Local Digis Time-Series Database information being provided as default. This information can be checked through the API provided by Prometheus.

![Caas_Monit_collect_architecure_Image]


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > CaaS


<!-- Images Links -->
[Caas_Monit_architecure_Image]:./images/caas_monitoring_architecture.png
[CaaS_Monit_collect_architecure_Image]:./images/caas_collect_architecture.png
