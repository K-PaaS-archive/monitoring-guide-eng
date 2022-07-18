## Table of Contents

1. [PaaS-TA Monitoring IaaS Installation](#1)  
  1.1 [Pre-requsite](#1-1)  
  1.2 [Monasca Installation](#1-2)  
    * [Monasca Server Installation](#1-2-1)
    * [Monasca Client(agent) Installation](#1-2-2)



## <div id='1'/>1.	PaaS-TA Monitoring IaaS Installation

### <div id='1-1'/>1.1. Pre-requsite
 1. Openstack Queens version and above
 2. PaaS-TA must me installed at the  Openstack.
 3. PaaS-TA must be installed on the installed Openstack (PaaS-TA Agent must be installed)
 4. IaaS-PaaS-Monitoring systems must have Monasca Server installed as Prerequisites. The Monasca Client (agent) must be installed on the openstack controller, computer node. You must first install the Monasca Server/Client below and then install IaaS-PaaS-Monitoring.
 
### <div id='1-2'/>1.2.	Monasca Installation
Monasca consists of a server and a client. The Monasca-Client (Agent) is installed on the Openstack controller/compute node to transmit the Monasca status information to the Monasca-Server. IaaS monitoring is performed based on the collected data.
Monasca-Server manually creates VMs on Openstack and proceeds with the installation.

#### <div id='1-2-1'/>1.2.1.	Monasca Server Installation

> **[Monasca - Server](./monasca-server.md)**

#### <div id='1-2-2'/>1.2.2.	Monasca Client(agent) Installation

> **[Monasca - Client](./monasca-client.md)** 
