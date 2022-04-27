### [Index](https://github.com/PaaS-TA/Guide) > Monitoring Install


# PaaS-TA Monitoring Install Guide


## 필수 설치 항목
> ⚠️ <b>PaaS-TA 플랫폼</b>에서 모니터링 기능을 사용할 경우 <b>BOSH</b>와 <b>PaaS-TA</b> 설치 시에 **모니터링 기능에 필요한 옵션을 적용한 설치 작업**이 필수 선행되어야 한다.

- [BOSH (+Monitoring Dashboard Option) 설치](PAAS-TA_BOSH2_MONITORING_INSTALL_GUIDE.md)
- [PaaS-TA (+Monitoring Dashboard Option) 설치](PAAS-TA_CORE_MONITORING_INSTALL_GUIDE.md)
- [Monitoring Dashboard 설치](PAAS-TA_MONITORING_MONITORING_DASHBOARD_INSTALL.md)


## 선택 설치 항목
> ⚠️ **IaaS 모니터링 옵션**을 사용할 경우에 한해서 배포 시 IaaS 모니터링 환경과 관련된 옵션 정보를 입력하는 등의 의존성 문제 예방을 위해 반드시 **Zabbix Package** 설치가 **'필수 설치 항목'** 보다도 선행되어야 하는 것에 주의한다.

+ [**[IaaS] Infrastructure Monitoring** - Zabbix Package](#)
  + [Zabbix Server 설치](PAAS-TA_MONITORING_ZABBIX-SERVER_INSTALL.md)
  + [Zabbix Proxy 설치](PAAS-TA_MONITORING_ZABBIX-PROXY_INSTALL.md)
  + [Zabbix Agent 설치](PAAS-TA_MONITORING_ZABBIX-AGENT_INSTALL.md)
  
- [**[PaaS] Log Monitoring** - Logsearch 설치](PAAS-TA_MONITORING_LOGSEARCH_INSTALL.md)

+ [**[SaaS] Application Monitoring** - Pinpoint Monitoring 설치](PAAS-TA_MONITORING_PINPOINT_MONITORING_INSTALL.md)
  + [Pinpoint Java Buildpack 생성과 등록](PAAS-TA_MONITORING_PINPOINT_JAVA_BUILDPACK_CREATING_AND_UPLOADING.md)
  + [Application & Pinpoint Service 푸싱과 바인딩](PAAS-TA_MONITORING_PINPOINT_APPLICATION_PUSHING_AND_BINDING.md)

- [**[CaaS] Container Monitoring** - Prometheus 설치](PAAS-TA_MONITORING_CONTAINER_SERVICE_INSTALL.md)


### [Index](https://github.com/PaaS-TA/Guide) > Monitoring Install
