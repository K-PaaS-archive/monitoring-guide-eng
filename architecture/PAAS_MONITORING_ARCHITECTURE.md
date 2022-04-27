### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > PaaS


# PaaS Monitoring Architecture


### │ PaaS Monitoring Architecture
PaaS Monitoring 운영환경에서는 크게 Backend 환경에서 실행되는 Batch 프로세스 영역과 Frontend 환경에서 실행되는 Monitoring 시스템 영역으로 나누어진다.  
Batch 프로세스는 PaaS-TA Portal에서 등록한 임계치 정보와 AutoScale 대상 정보를 기준으로 주기적으로 시스템 Metrics 정보를 조회 및 분석하여, 임계치를 초과한 서비스 발견시 관리자에게 Alarm을 전송하며, 임계치를 초과한 컨테이너 리스트 중에서 AutoScale 대상의 컨테이너 존재시 AutoScale Server 서비스에 관련 정보를 전송하여 자동으로 AutoScaling 기능이 수행되도록 처리한다.  
PaaS-TA Monitoring 시스템은 TSDB(InfluxDB)로부터 시스템 환경 정보 데이터를 조회하고, Lucene(Elasticsearch)을 통해 로그 정보를 조회한다.  
조회된 정보로 PaaS-TA Monitoring 시스템의 현재 자원 사용 현황을 조회하고, PaaS-TA Monitoring Dashboard를 통해 로그 정보를 조회할 수 있도록 한다.  
PaaS-TA Monitoring Dashboard는 관리자 화면으로 알람이 발생된 이벤트 현황 정보를 조회하고, 컨테이너 배치 현황과 장애 발생 서비스에 대한 통계 정보를 조회할 수 있으며, 이벤트 관련 처리정보를 이력관리할 수 있는 화면을 제공한다.  

![PaaSTa_Monit_architecure_Image]


### │ PaaS Collecting Metric Data Architecture
PaaS는 내부적으로 메트릭스 정보를 수집 및 전달하는 Metric Agent와 로그 정보를 수집 및 전달하는 Syslog 모듈을 제공한다.  
Metric Agent는 시스템 관련 메트릭스를 수집하여 InfluxDB에 정보를 저장한다.  
Syslog는 PaaS-TA를 Deploy 하기 위한 manfiest 파일의 설정으로도 로그 정보를 ELK 서비스에 전달할 수 있으며, 로그 정보를 전달하기 위해서는 RELP 프로토콜(Reliable Event Logging Protocol)을 사용한다.

![PaaSTa_Monit_collect_architecure_Image]


### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Architecture](PAAS-TA_MONITORING_ARCHITECTURE.md) > PaaS


<!-- Images Links -->
[PaaSTa_Monit_architecure_Image]:./images/monit_architecture.png
[PaaSTa_Monit_collect_architecure_Image]:./images/collect_architecture.png