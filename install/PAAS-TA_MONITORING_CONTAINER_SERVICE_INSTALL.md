### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > [CaaS] Prometheus


# Prometheus Install Guide

1. [Document Outline](#1)  
2. [PaaS-TA Container Monitoring Installation](#2)  
3. [PaaS-TA Container Monitoring Installation Check](#3)  
4. [Prometheus Information Check](#4)  
5. [Grafana Information Check](#5)  
 
## <div id='1'/>1. Document Outline
This document (PaaS-TA-CaaS-Monitoring) describes the installation method for collecting Container Usage from installed CaaS and presenting it to Paasta-monitoring.

### <div id='1.1'/>1.1 Purpose
The purpose of this document is to collect Container Usage from CaaS and represent it as Paasta-monitoring.
  
### <div id='1.2'/>1.2 Range
This document is based on the installation of Kubespray and CaaS for CaaS deployments based on the master of paas-ta-container-platform.      

## <div id='2'/>2.	CaaS Monitoring Installation

### <div id='2.1'/>2.1 Prerequisite
This installation guide is based on installation in the Ubuntu environment. BOSH 2.0, PaaS-TA 5.5, PaaS-TA Portal API, PaaS-TA Portal UI, Kubespray Installation, and CaaS Deployment must be installed for service installation.
- [BOSH 2.0 Installation Guide](https://github.com/PaaS-TA/Guide/blob/master/install-guide/bosh/PAAS-TA_BOSH2_INSTALL_GUIDE_V5.0.md)
- [PaaS-TA 5.5 Installation Guide](https://github.com/PaaS-TA/Guide/blob/master/install-guide/paasta/PAAS-TA_CORE_INSTALL_GUIDE_V5.0.md)
- [PaaS-TA Portal API Installation Guide](https://github.com/PaaS-TA/Guide/blob/master/install-guide/portal/PAAS-TA_PORTAL_API_SERVICE_INSTALL_GUIDE_V1.0.md)
- [PaaS-TA Portal UI Installation Guide](https://github.com/PaaS-TA/Guide/blob/master/install-guide/portal/PAAS-TA_PORTAL_UI_SERVICE_INSTALL_GUIDE_V1.0.md)
- [Kubespray Installation Guide](https://github.com/PaaS-TA/paas-ta-container-platform/blob/master/install-guide/standalone/paas-ta-container-platform-standalone-deployment-guide-v1.0.md)
- [CaaS Deployment Installation Guide](https://github.com/PaaS-TA/paas-ta-container-platform/blob/master/install-guide/bosh/paas-ta-container-platform-bosh-deployment-caas-guide-v1.0.md)

### <div id='2.2'/>2.2 Helm Installation  (v3)
> Download and Execution Helm
```
#  Helm Download
$  curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

#  Helm File Authority Modification
$  chmod 700 get_helm.sh

#  Execute Helm
$  ./get_helm.sh
```

> Install Helm and Check Version
```
$  helm version
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /home/ubuntu/.kube/config
version.BuildInfo{Version:"v3.7.1", GitCommit:"1d11fcb5d3f3bf00dbe6fe31b8412839a96b3dc4", GitTreeState:"clean", GoVersion:"go1.16.9"}
```

> Add and Apply Helm Repository 
```
$  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$  helm repo update
```

> Check details of Repo registered with Helm
```
$  helm search repo prometheus

NAME                                                CHART VERSION   APP VERSION     DESCRIPTION
prometheus-community/kube-prometheus-stack          14.3.0          0.46.0          kube-prometheus-stack collects Kubernetes manif...
prometheus-community/prometheus                     13.6.0          2.26.0          Prometheus is a monitoring system and time seri...
prometheus-community/prometheus-adapter             2.12.1          v0.8.3          A Helm chart for k8s prometheus adapter
prometheus-community/prometheus-blackbox-exporter   4.10.2          0.18.0          Prometheus Blackbox Exporter
prometheus-community/prometheus-cloudwatch-expo...  0.14.1          0.10.0          A Helm chart for prometheus cloudwatch-exporter
prometheus-community/prometheus-consul-exporter     0.4.0           0.4.0           A Helm chart for the Prometheus Consul Exporter
prometheus-community/prometheus-couchdb-exporter    0.2.0           1.0 A           Helm chart to export the metrics from couchdb...
prometheus-community/prometheus-druid-exporter      0.9.0           v0.8.0          Druid exporter to monitor druid metrics with Pr...
prometheus-community/prometheus-elasticsearch-e...  4.3.0           1.1.0           Elasticsearch stats exporter for Prometheus
prometheus-community/prometheus-kafka-exporter      1.0.0           v1.2.0          A Helm chart to export the metrics from Kafka i...
prometheus-community/prometheus-mongodb-exporter    2.8.1           v0.10.0         A Prometheus exporter for MongoDB metrics
prometheus-community/prometheus-mysql-exporter      1.1.0           v0.12.1         A Helm chart for prometheus mysql exporter with...
prometheus-community/prometheus-nats-exporter       2.6.0           0.7.0           A Helm chart for prometheus-nats-exporter
prometheus-community/prometheus-node-exporter       1.16.2          1.1.2           A Helm chart for prometheus node-exporter
prometheus-community/prometheus-operator            9.3.2           0.38.1          DEPRECATED - This chart will be renamed. See ht...
prometheus-community/prometheus-pingdom-exporter    2.3.2           20190610-1      A Helm chart for Prometheus Pingdom Exporter
prometheus-community/prometheus-postgres-exporter   2.2.0           0.9.0 A         Helm chart for prometheus postgres-exporter
prometheus-community/prometheus-pushgateway         1.7.1           1.3.0 A         Helm chart for prometheus pushgateway
prometheus-community/prometheus-rabbitmq-exporter   0.7.0           v0.29.0         Rabbitmq metrics exporter for prometheus
prometheus-community/prometheus-redis-exporter      4.0.0           1.11.1          Prometheus exporter for Redis metrics
prometheus-community/prometheus-snmp-exporter       0.1.2           0.19.0          Prometheus SNMP Exporter
prometheus-community/prometheus-stackdriver-exp...  1.8.2           0.11.0          Stackdriver exporter for Prometheus
prometheus-community/prometheus-statsd-exporter     0.3.1           0.20.0          A Helm chart for prometheus stats-exporter
prometheus-community/prometheus-to-sd               0.4.0           0.5.2           Scrape metrics stored in prometheus format and ...
prometheus-community/alertmanager                   0.8.0           v0.21.0         The Alertmanager handles alerts sent by client ...
```

> Create and check Kubernetes namespace
```
#  Create Kubernetes namespace 
$  kubectl create namespace paas-ta-container-monitoring

#  Check Kubernetes namespace 
$  kubectl get namespace
```

> Change Kubernetes default namespace (Set Namespace for paas-ta-container-monitoring.)  
```
#  Change Kubernetes namespace  
$  kubectl config set-context --current --namespace=paas-ta-container-monitoring
```

> Installation of Prometheus using Helm    
```
$  helm install prometheus-community/kube-prometheus-stack --generate-name

#  Message shown below will appear if the installation is completed.
NAME: kube-prometheus-stack-1616563003
LAST DEPLOYED: Wed Mar 24 05:16:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
kubectl --namespace default get pods -l "release=kube-prometheus-stack-1616563003"
Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.
```

> Check Deployment Status    
```
$  kubectl get deploy,po,svc

NAME                                                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/kube-prometheus-stack-1617-operator                   1/1     1            1           20m
deployment.apps/kube-prometheus-stack-1617006674-grafana              1/1     1            1           20m
deployment.apps/kube-prometheus-stack-1617006674-kube-state-metrics   1/1     1            1           20m

NAME                                                                  READY   STATUS    RESTARTS   AGE
pod/alertmanager-kube-prometheus-stack-1617-alertmanager-0            2/2     Running   0          20m
pod/kube-prometheus-stack-1617-operator-64b644c454-kc4jv              1/1     Running   0          20m
pod/kube-prometheus-stack-1617006674-grafana-74fc6bc6c9-s8z8r         2/2     Running   0          20m
pod/kube-prometheus-stack-1617006674-kube-state-metrics-c86f6dqs52k   1/1     Running   0          20m
pod/kube-prometheus-stack-1617006674-prometheus-node-exporter-8jwvd   1/1     Running   0          20m
pod/kube-prometheus-stack-1617006674-prometheus-node-exporter-f4zv4   1/1     Running   0          20m
pod/kube-prometheus-stack-1617006674-prometheus-node-exporter-sr5gf   1/1     Running   0          20m
pod/prometheus-kube-prometheus-stack-1617-prometheus-0                2/2     Running   0          20m

NAME                                                                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/alertmanager-operated                                       ClusterIP   None            <none>        9093/TCP,9094/TCP,9094/UDP   20m
service/kube-prometheus-stack-1617-alertmanager                     ClusterIP   10.233.9.127    <none>        9093/TCP                     20m
service/kube-prometheus-stack-1617-operator                         ClusterIP   10.233.16.79    <none>        443/TCP                      20m
service/kube-prometheus-stack-1617-prometheus                       ClusterIP   10.233.50.86    <none>        9090/TCP                     20m
service/kube-prometheus-stack-1617006674-grafana                    ClusterIP   10.233.39.116   <none>        80/TCP                       20m
service/kube-prometheus-stack-1617006674-kube-state-metrics         ClusterIP   10.233.63.208   <none>        8080/TCP                     20m
service/kube-prometheus-stack-1617006674-prometheus-node-exporter   ClusterIP   10.233.22.17    <none>        9100/TCP                     20m
service/prometheus-operated                                         ClusterIP   None            <none>        9090/TCP                     20m
```

> Change the Prometheus ClusterIP Type to NodePort Type (Port 30060)    
```
$  kubectl edit svc kube-prometheus-stack-1617-prometheus
-----------------------------before-----------------------------------
apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: kube-prometheus-stack-1617006674
    meta.helm.sh/release-namespace: paas-ta-container-monitoring
  creationTimestamp: "2021-03-29T08:31:29Z"
  labels:
    app: kube-prometheus-stack-prometheus
    app.kubernetes.io/managed-by: Helm
    chart: kube-prometheus-stack-14.4.0
    heritage: Helm
    release: kube-prometheus-stack-1617006674
    self-monitor: "true"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:meta.helm.sh/release-name: {}
          f:meta.helm.sh/release-namespace: {}
        f:labels:
          .: {}
          f:app: {}
          f:app.kubernetes.io/managed-by: {}
          f:chart: {}
          f:heritage: {}
          f:release: {}
          f:self-monitor: {}
      f:spec:
        f:ports:
          .: {}
          k:{"port":9090,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
        f:selector:
          .: {}
          f:app: {}
          f:prometheus: {}
        f:sessionAffinity: {}
        f:type: {}
    manager: Go-http-client
    operation: Update
    time: "2021-03-29T08:31:29Z"
  name: kube-prometheus-stack-1617-prometheus
  namespace: paas-ta-container-monitoring
  resourceVersion: "1284586"
  selfLink: /api/v1/namespaces/paas-ta-container-monitoring/services/kube-prometheus-stack-1617-prometheus
  uid: a9bbe2f4-abf8-42a5-8329-7621119fcbad
spec:
  clusterIP: 10.233.50.86
  ports:
  - name: web
    port: 9090
    protocol: TCP
    targetPort: 9090
  selector:
    app: prometheus
    prometheus: kube-prometheus-stack-1617-prometheus
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}

-----------------------------after-----------------------------------

apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: kube-prometheus-stack-1617006674
    meta.helm.sh/release-namespace: paas-ta-container-monitoring
  creationTimestamp: "2021-03-29T08:31:29Z"
  labels:
    app: kube-prometheus-stack-prometheus
    app.kubernetes.io/managed-by: Helm
    chart: kube-prometheus-stack-14.4.0
    heritage: Helm
    release: kube-prometheus-stack-1617006674
    self-monitor: "true"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:meta.helm.sh/release-name: {}
          f:meta.helm.sh/release-namespace: {}
        f:labels:
          .: {}
          f:app: {}
          f:app.kubernetes.io/managed-by: {}
          f:chart: {}
          f:heritage: {}
          f:release: {}
          f:self-monitor: {}
      f:spec:
        f:ports:
          .: {}
          k:{"port":9090,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
        f:selector:
          .: {}
          f:app: {}
          f:prometheus: {}
        f:sessionAffinity: {}
        f:type: {}
    manager: Go-http-client
    operation: Update
    time: "2021-03-29T08:31:29Z"
  name: kube-prometheus-stack-1617-prometheus
  namespace: paas-ta-container-monitoring
  resourceVersion: "1284586"
  selfLink: /api/v1/namespaces/paas-ta-container-monitoring/services/kube-prometheus-stack-1617-prometheus
  uid: a9bbe2f4-abf8-42a5-8329-7621119fcbad
spec:
  clusterIP: 10.233.50.86
  ports:
  - name: web
    nodePort: 30060
    port: 9090
    protocol: TCP
    targetPort: 9090
  selector:
    app: prometheus
    prometheus: kube-prometheus-stack-1617-prometheus
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}

```

> Change ClusterIP Type of Grafana to NodePort Type (Port 30061)    
```
$  kubectl edit svc kube-prometheus-stack-1617006674-grafana
-----------------------------before-----------------------------------
apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: kube-prometheus-stack-1617006674
    meta.helm.sh/release-namespace: paas-ta-container-monitoring
  creationTimestamp: "2021-03-29T08:31:29Z"
  labels:
    app.kubernetes.io/instance: kube-prometheus-stack-1617006674
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: grafana
    app.kubernetes.io/version: 7.4.5
    helm.sh/chart: grafana-6.6.4
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:meta.helm.sh/release-name: {}
          f:meta.helm.sh/release-namespace: {}
        f:labels:
          .: {}
          f:app.kubernetes.io/instance: {}
          f:app.kubernetes.io/managed-by: {}
          f:app.kubernetes.io/name: {}
          f:app.kubernetes.io/version: {}
          f:helm.sh/chart: {}
      f:spec:
        f:ports:
          .: {}
          k:{"port":80,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
        f:selector:
          .: {}
          f:app.kubernetes.io/instance: {}
          f:app.kubernetes.io/name: {}
        f:sessionAffinity: {}
    manager: Go-http-client
    operation: Update
    time: "2021-03-29T08:31:29Z"
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:spec:
        f:externalTrafficPolicy: {}
        f:ports:
          k:{"port":80,"protocol":"TCP"}:
            f:nodePort: {}
        f:type: {}
    manager: kubectl
    operation: Update
    time: "2021-03-29T09:01:37Z"
  name: kube-prometheus-stack-1617006674-grafana
  namespace: paas-ta-container-monitoring
  resourceVersion: "1290056"
  selfLink: /api/v1/namespaces/paas-ta-container-monitoring/services/kube-prometheus-stack-1617006674-grafana
  uid: 08c55f35-7572-4c29-8ed4-07e956bf41c3
spec:
  clusterIP: 10.233.39.116
  externalTrafficPolicy: Cluster
  ports:
  - name: service
    port: 80
    protocol: TCP
    targetPort: 3000
  selector:
    app.kubernetes.io/instance: kube-prometheus-stack-1617006674
    app.kubernetes.io/name: grafana
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}

-----------------------------after-----------------------------------
apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: kube-prometheus-stack-1617006674
    meta.helm.sh/release-namespace: paas-ta-container-monitoring
  creationTimestamp: "2021-03-29T08:31:29Z"
  labels:
    app.kubernetes.io/instance: kube-prometheus-stack-1617006674
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: grafana
    app.kubernetes.io/version: 7.4.5
    helm.sh/chart: grafana-6.6.4
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .: {}
          f:meta.helm.sh/release-name: {}
          f:meta.helm.sh/release-namespace: {}
        f:labels:
          .: {}
          f:app.kubernetes.io/instance: {}
          f:app.kubernetes.io/managed-by: {}
          f:app.kubernetes.io/name: {}
          f:app.kubernetes.io/version: {}
          f:helm.sh/chart: {}
      f:spec:
        f:ports:
          .: {}
          k:{"port":80,"protocol":"TCP"}:
            .: {}
            f:name: {}
            f:port: {}
            f:protocol: {}
            f:targetPort: {}
        f:selector:
          .: {}
          f:app.kubernetes.io/instance: {}
          f:app.kubernetes.io/name: {}
        f:sessionAffinity: {}
    manager: Go-http-client
    operation: Update
    time: "2021-03-29T08:31:29Z"
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:spec:
        f:externalTrafficPolicy: {}
        f:ports:
          k:{"port":80,"protocol":"TCP"}:
            f:nodePort: {}
        f:type: {}
    manager: kubectl
    operation: Update
    time: "2021-03-29T09:01:37Z"
  name: kube-prometheus-stack-1617006674-grafana
  namespace: paas-ta-container-monitoring
  resourceVersion: "1290056"
  selfLink: /api/v1/namespaces/paas-ta-container-monitoring/services/kube-prometheus-stack-1617006674-grafana
  uid: 08c55f35-7572-4c29-8ed4-07e956bf41c3
spec:
  clusterIP: 10.233.39.116
  externalTrafficPolicy: Cluster
  ports:
  - name: service
    nodePort: 30061
    port: 80
    protocol: TCP
    targetPort: 3000
  selector:
    app.kubernetes.io/instance: kube-prometheus-stack-1617006674
    app.kubernetes.io/name: grafana
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```

>  Change Kubernetes default namespace  
```
$  kubectl config set-context --current --namespace=""
```


## <div id='3'/>3.	PaaS-TA Container Monitoring Installation Check

>  Check by accessing to the Monitoring-Web and click CaaS Menu.
 
![paasta_container_monitoring]


## <div id='4'/>4.	Prometheus Information Check 

>  Connect and check through the Kubernetes Master IP and the external port of Prometheus.

![paasta_container_monitoring_prometheus]

## <div id='5'/>5.	Grafana Information Check

>  Check the Kubernetes Secret list.
 ```
 NAME                                                              TYPE                                  DATA   AGE
 alertmanager-kube-prometheus-stack-1617-alertmanager              Opaque                                1      15h
 alertmanager-kube-prometheus-stack-1617-alertmanager-generated    Opaque                                1      15h
 alertmanager-kube-prometheus-stack-1617-alertmanager-tls-assets   Opaque                                0      15h
 default-token-lw5ks                                               kubernetes.io/service-account-token   3      17h
 kube-prometheus-stack-1617-admission                              Opaque                                3      15h
 kube-prometheus-stack-1617-alertmanager-token-vc8x6               kubernetes.io/service-account-token   3      15h
 kube-prometheus-stack-1617-operator-token-xkd9x                   kubernetes.io/service-account-token   3      15h
 kube-prometheus-stack-1617-prometheus-token-7lfmn                 kubernetes.io/service-account-token   3      15h
 kube-prometheus-stack-1617006674-grafana                          Opaque                                3      15h
 kube-prometheus-stack-1617006674-grafana-test-token-wq4xh         kubernetes.io/service-account-token   3      15h
 kube-prometheus-stack-1617006674-grafana-token-zd2cg              kubernetes.io/service-account-token   3      15h
 kube-prometheus-stack-1617006674-kube-state-metrics-token-d54rf   kubernetes.io/service-account-token   3      15h
 kube-prometheus-stack-1617006674-prometheus-node-exporter-94khs   kubernetes.io/service-account-token   3      15h
 prometheus-kube-prometheus-stack-1617-prometheus                  Opaque                                1      15h
 prometheus-kube-prometheus-stack-1617-prometheus-tls-assets       Opaque                                1      15h
 sh.helm.release.v1.kube-prometheus-stack-1617006674.v1            helm.sh/release.v1                    1      15h
 ```

>  Check the Admin Password of Grafana.
 ```
$  kubectl get secret kube-prometheus-stack-1617006674-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
 *************
 ```

>  Connect and check through the external port of Kubernetes Master IP and Grafana.

![paasta_container_monitoring_grafana]


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Prometheus


<!-- Image Links -->
[paasta_container_monitoring]:./images/paasta-container-monitoring.png
[paasta_container_monitoring_prometheus]:./images/paasta-container-monitoring-prometheus.png
[paasta_container_monitoring_grafana]:./images/paasta-container-monitoring-grafana.png
