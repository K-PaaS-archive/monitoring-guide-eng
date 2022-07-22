### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Pinpoint Java Buildpack


# Pinpoint Java Buildpack Creating and Uploading
1. [Outline](#1)
2. [Creating and Registering Pinpoint Java Buildpack](#2)


## <div id="1">1. Outline
본 문서는 Pinpoint Monitoring 서비스에서 사용할 Java Builpack을 생성하는 방법에 대한 설명을 다루고 있다.


## <div id="2">2. Creating and Registering Pinpoint Java Buildpack 


### 2.1. Ruby Installation
The Ruby installation must precede when creating a Java buildpack to be applied to the Sample Application to be pushed. Installation of necessary dependency packages is carried out as follows.

```
$ sudo apt update
$ sudo apt install git curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev libffi-dev nodejs openjdk-8-jdk
```

Install **rbenv**(A tool for Ruby installation and version management).

```
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
$ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
$ source ~/.bashrc
```

Install **ruby-build**(CLI Tool for Ruby).

```
$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
$ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
$ source ~/.bashrc
```

Install **Ruby**.

```
$ rbenv install 2.7.3
$ rbenv global 2.7.3
$ rbenv rehash
$ ruby -v
```


### 2.2. Creating and Registering Java Buildpack 
Download the source to create the Java buildpack from the PaaS-TA repository.

```
$ cd ~/workspace
$ git clone https://github.com/PaaS-TA/monitoring-pinpoint-buildpack.git
```

Install Bundler.

```
$ gem install bundler
$ rbenv rehash
```

Install the gems registered at the Gemfile.

```
$ cd monitoring-pinpoint-buildpack/
$ bundle install
```

Package the buildpacks.

```
$ bundle exec rake package OFFLINE=true
```

Change the file name of the build pack created in the `build` subfolder and register it in the build pack list in the cf. When registering a build pack, `12` behind the `cf create-buildpack` command means the build pack list number, and the reason for changing the file name is simply to distinguish it from other build packs.
```
$ mv build/java-buildpack-offline-745b745.zip build/java-buildpack-for-pinpoint.zip
$ cf create-buildpack java-buildpack-pinpoint build/java-buildpack-for-pinpoint.zip 12
```

Verify if the buildpack registration was completed normally.

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


### [Index](https://github.com/PaaS-TA/Guide-eng) > [Monitoring Install](PAAS-TA_MONITORING_INSTALL_GUIDE.md) > Pinpoint Java Buildpack
