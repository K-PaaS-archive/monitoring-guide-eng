### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > SaaS


# SaaS Monitoring Architecture


### │ SaaS Monitoring Architecture
Saas Monitoring 운영환경에는 크게 Backend 환경에서 실행되는 Batch 프로세스 영역과 Frontend 환경에서 실행되는 Monitoring 시스템 영역으로 나누어진다.  
Batch 프로세스는 PaaS-TA Portal SaaS 서비스에서 등록한 임계치 정보를 기준으로 주기적으로 시스템 metrics 정보를 조회 및 분석하여, 임계치를 초과한 서비스 발견시 관리자에게 Alarm을 전송한다.  
Monitoring 시스템 은 Pinpoint APM Server 로부터 시스템 메트 데이터를 조회하고, 조회된 정보는 SaaS Monitoring 시스템의 현재 자원 사용 현황을 조회한다.  
Monitoring Portal은 관리자 화면으로 알람이 발생된 이벤트 현황 정보를 조회하고, Application 현황 및 서비스에 대한 통계 정보를 조회할 수 있으며, 이벤트 관련 처리정보를 이력관리할 수 있는 화면을 제공한다.

![Saas_Monit_architecure_Image]


### │ SaaS Collecting Metric Data Architecture
PaaS-TA SaaS는 내부적으로 메트릭스 정보를 수집 하는 Pinpoint Metric Agent 제공한다.  
Metric Agent는 Application JVM 관련 메트릭스를 수집하여 Hbase DB에 정보를 저장한다.  
해당 정보는 Pinpoint APM 서버의 API를 통하여 조회할 수 있다.

![Saas_Monit_collect_architecure_Image]


### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > SaaS


<!-- Images Links -->
[Saas_Monit_architecure_Image]:./images/saas_monitoring_architecture.png
[SaaS_Monit_collect_architecure_Image]:./images/saas_collect_architecture.png