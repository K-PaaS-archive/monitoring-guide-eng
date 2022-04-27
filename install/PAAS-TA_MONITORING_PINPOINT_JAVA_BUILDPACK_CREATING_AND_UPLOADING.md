### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Pinpoint Java Buildpack


# Pinpoint Java Buildpack Creating and Uploading
1. [개요](#1)
2. [Pinpoint Java Buildpack 생성과 등록](#2)


## <div id="1">1. 개요
본 문서는 Pinpoint Monitoring 서비스에서 사용할 Java Builpack을 생성하는 방법에 대한 설명을 다루고 있다.


## <div id="2">2. Pinpoint Java Buildpack 생성과 등록


### 2.1. Ruby 설치
푸시할 Sample Application에 적용할 자바 빌드팩을 만들기 위해서는 먼저 Ruby 설치가 선행되어야 한다. 아래와 같이 필요한 의존성 패키지들의 설치를 진행한다.

```
$ sudo apt update
$ sudo apt install git curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev libffi-dev nodejs openjdk-8-jdk
```

**rbenv**(Ruby 설치 및 버전 관리 도구)를 설치한다.

```
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ source ~/.bashrc
```

**ruby-build**(Ruby를 위한 CLI 도구)를 설치한다.

```
$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ source ~/.bashrc
```

**Ruby**를 설치한다.

```
$ rbenv install 2.7.3
$ rbenv global 2.7.3
$ rbenv rehash
$ ruby -v
```


### 2.2. Java Buildpack 생성과 등록
자바 빌드팩을 생성하기 위한 소스를 PaaS-TA 저장소로부터 내려 받는다.

```
$ cd ~/workspace
$ git clone https://github.com/PaaS-TA/monitoring-pinpoint-buildpack.git
```

Bundler를 설치한다.

```
$ gem install bundler
$ rbenv rehash
```

Gemfile에 등록된 gem들을 설치한다.

```
$ cd monitoring-pinpoint-buildpack/
$ bundle install
```

빌드팩을 패키징한다.

```
$ bundle exec rake package OFFLINE=true
```

`build` 하위 폴더에 생성된 빌드팩의 파일명을 변경하고 cf의 빌드팩 리스트에 등록한다. 빌드팩을 등록할 때 `cf create-buildpack` 명령 후미의 `12`는 빌드팩 리스트 넘버를 의미하고 파일명을 변경하는 이유는 단순히 다른 빌드팩과 구분을 위함이다.

```
$ mv build/java-buildpack-offline-745b745.zip build/java-buildpack-for-pinpoint.zip
$ cf create-buildpack java-buildpack-pinpoint build/java-buildpack-for-pinpoint.zip 12
```

빌드팩 등록이 정상적으로 완료 되었는지 확인한다.

```
$ cf buildpacks
Getting buildpacks as admin...

position   name                      stack        enabled   locked   filename
1          staticfile_buildpack      cflinuxfs3   true      false    staticfile_buildpack-cflinuxfs3-v1.5.10.zip
2          java_buildpack            cflinuxfs3   true      false    java-buildpack-cflinuxfs3-v4.32.1.zip
3          ruby_buildpack            cflinuxfs3   true      false    ruby_buildpack-cflinuxfs3-v1.8.23.zip
4          dotnet_core_buildpack     cflinuxfs3   true      false    dotnet-core_buildpack-cflinuxfs3-v2.3.14.zip
5          nodejs_buildpack          cflinuxfs3   true      false    nodejs_buildpack-cflinuxfs3-v1.7.26.zip
6          go_buildpack              cflinuxfs3   true      false    go_buildpack-cflinuxfs3-v1.9.17.zip
7          python_buildpack          cflinuxfs3   true      false    python_buildpack-cflinuxfs3-v1.7.20.zip
8          php_buildpack             cflinuxfs3   true      false    php_buildpack-cflinuxfs3-v4.4.20.zip
9          nginx_buildpack           cflinuxfs3   true      false    nginx_buildpack-cflinuxfs3-v1.1.14.zip
10         r_buildpack               cflinuxfs3   true      false    r_buildpack-cflinuxfs3-v1.1.7.zip
11         binary_buildpack          cflinuxfs3   true      false    binary_buildpack-cflinuxfs3-v1.0.36.zip
12         java-buildpack-pinpoint                true      false    java-buildpack-for-pinpoint.zip
```


### [Index](https://github.com/PaaS-TA/Guide) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Pinpoint Java Buildpack
