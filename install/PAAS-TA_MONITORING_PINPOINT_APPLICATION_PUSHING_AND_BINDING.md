### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Application & Pinpoint Service


# Application & Pinpoint Service Pushing and Binding
1. [Outline](#1)
2. [Pushing and Binding of Application & Pinpoint Service](#2)


## <div id="1">1. Outline
This document describes how to push applications and bind services to installed PaaS-TA platforms.


## <div id="2">2. Pushing and Binding of Application & Pinpoint Service


### 2.1. Sample Application Build and Push
Download the developed 'spring-music' source for sample app testing from the Cloud Foundry Sample Applications repository.

```
$ cd ~/workspace
$ git clone https://github.com/cloudfoundry-samples/spring-music.git
```

Build the downloaded source.

```
$ cd spring-music
$ ./gradlew clean assemble
```

Modify the `manifest.yml` file. The user may arbitrarily modify the `name` value.

```
$ vi manifest.yml
```
```yaml
---
applications:
- name: spring-music-paasta
  memory: 1G
  random-route: true
  path: build/libs/spring-music-1.0.jar
  env:
    JBP_CONFIG_SPRING_AUTO_RECONFIGURATION: '{enabled: false}'
```

Add the buildpack option to push the app. `-b` is an option to add a buildpack, and `--no-start` is an option to make it not start right after pushing the app. In other words, the default option starts the app after push. When the push is complete, check if the app is pushed normally.

```
$ cf push -b java-buildpack-pinpoint --no-start

...

$ cf apps
Getting apps in org system / space paasta as admin...

name                  requested state   processes   routes
spring-music-paasta   stopped           web:0/1     spring-music-paasta-impressive-shark-pd.10.5.0.240.nip.io
```

If the space of cf is not specified, specify the space as shown below.

```
$ cf create-space paasta
$ cf target -s paasta
```


### 2.2. Registering Security Group and User Provided Service
Create a new JSON file named `pinpoint-asg.json` and write it as shown below. The modify the `destination` value to suit the environment of the user.

```json
[
  {
    "protocol": "all",
    "destination": "10.5.0.0/24",
    "log": true,
    "description": "Allow tcp traffic to z1"
  }
]
```

Register and bind the security-group 'pinpoint' using the JSON file created earlier.

```
$ cf create-security-group pinpoint pinpoint-asg.json
$ cf bind-staging-security-group pinpoint
$ cf bind-running-security-group pinpoint
```

Register User Provided Service.  note that the values of `application_name` and `collector_host` should be appropriately modified according to the environment of the user.

```
$ cf cups pinpoint-monitoring-service -p '{"application_name":"spring-music-paasta","collector_host":"10.5.0.139","collector_span_port":"29996","collector_stat_port":"29995","collector_tcp_port":"29994"}' -t 'pinpoint'
```


### 2.3. Binding of Application and Service
Bind Application and Service.

```
$ cf bind-service spring-music-paasta pinpoint-monitoring-service
```

Execute Application.

```
$ cf start spring-music-paasta
```

If the application is running, you can access the URL (`routes`)inquired by the following command through an Internet browser to check whether the app is being serviced normally.

```
$ cf apps
Getting apps in org system / space paasta as admin...

name                  requested state   processes           routes
spring-music-paasta   started           web:1/1, task:0/0   spring-music-paasta-brash-alligator-tz.10.5.0.240.nip.io
```

![](images/cf_pushed_spring_music_app.png)

You can also check monitoring information about the application pushed by the user on the Monitoring Dashboard.

![](images/cf_pushed_spring_music_app_info_on_monitoring-dashboard_01.png)

![](images/cf_pushed_spring_music_app_info_on_monitoring-dashboard_02.png)

![](images/cf_pushed_spring_music_app_info_on_monitoring-dashboard_03.png)



### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Application & Pinpoint Service
