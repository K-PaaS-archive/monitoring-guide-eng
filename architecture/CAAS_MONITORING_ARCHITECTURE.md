### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > CaaS


# CaaS Monitoring Architecture


### │ CaaS Monitoring Architecture
CaaS Monitoring 운영환경에는 크게 Backend 환경에서 실행되는 Batch 프로세스 영역과 Frontend 환경에서 실행되는 Monitoring 시스템 영역으로 나누어진다.  
Batch 프로세스는 CaaS에서 등록한 임계치 정보를 기준으로 주기적으로 시스템 metrics 정보를 조회 및 분석하여, 임계치를 초과한 서비스 발견시 관리자에게 Alarm을 전송한다.  
PaaS-TA Monitoring 시스템은 K8s(Prometheus Agent)로부터 시스템 메트릭 데이터를 조회하고, 조회된 정보로 CaaS Monitoring 시스템의 현재 자원 사용 현황을 조회한다.  
PaaS-TA Monitoring Dashboard는 관리자 화면으로 알람이 발생된 이벤트 현황 정보를 조회하고, kubernetes Pod 현황 및 서비스에 대한 통계 정보를 조회할 수 있으며, 이벤트 관련 처리정보를 이력관리할 수 있는 화면을 제공한다.  

![Caas_Monit_architecure_Image]


### │ CaaS Collecting Metric Data Architecture
CaaS는 내부적으로 메트릭스 정보를 수집 하는 Prometheus Metric Agent(Node Exporter, cAdvisor) 제공한다.  
Prometheus 기본 제공되는 로컬 디지스 Time-Series Database 정보를 저장한다. 해당 정보를 조회하기 위해서는 Prometheus 제공하는 API를 통하여 조회할 수 있다.

![Caas_Monit_collect_architecure_Image]


### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > CaaS


<!-- Images Links -->
[Caas_Monit_architecure_Image]:./images/caas_monitoring_architecture.png
[CaaS_Monit_collect_architecure_Image]:./images/caas_collect_architecture.png