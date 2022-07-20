### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > SaaS


# SaaS Monitoring Architecture


### │ SaaS Monitoring Architecture
The Saas Monitoring operating environment is largely divided into the Batch process area running in the Backend environment and the Monitoring system area running in the Frontend environment.  
The Batch process periodically checks and analyzes system metrics information based on threshold information registered by the PaaS-TA Portal SaaS service and sends an alarm(notification) to the administrator when a service exceeding the threshold is found.  
The Monitoring system checks system metadata from the Pinpoint APM Server, and the checked information queries the current resource usage status of the SaaS monitoring system.  
The Monitoring Portal provides a screen that allows to check on event status information that has an alarm on the administrator screen, chekcs statistics about application status and services, and manage history of event-related processing information.

![Saas_Monit_architecure_Image]


### │ SaaS Collecting Metric Data Architecture
PaaS-TA SaaS provides a Pinpoint Metric Agent that internally collects metric information.  
The Metric Agent collects metrics related to the Application JVM and stores the information in the Hbase DB.  
The information can be checked through the API of the Pinpoint APM server.

![Saas_Monit_collect_architecure_Image]


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > SaaS


<!-- Images Links -->
[Saas_Monit_architecure_Image]:./images/saas_monitoring_architecture.png
[SaaS_Monit_collect_architecure_Image]:./images/saas_collect_architecture.png
