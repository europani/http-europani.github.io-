---
layout: post
title: '[EC2] 자바 11, Nginx, Git 설치'
categories: AWS
tags: [AWS]
---
현재 아마존 EC2 AMI로 `Amazon Linux 2`를 사용하고 있다  
패키지 설치가 기존 Linux와는 다르기 때문에 정리해 보려고 한다

### JAVA 11

yum에서 설치가능한 JDK는 1.8버전까지이다.
11버전은 Amazon에서 제공하는 OpenJDK인 `Amazon Coretto`를 다운받아 설치해야 한다

```bash
$ yum list java*jdk-devel
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Available Packages
java-1.7.0-openjdk-devel.x86_64    1:1.7.0.261-2.6.22.2.amzn2.0.1     amzn2-core
java-1.8.0-openjdk-devel.x86_64    1:1.8.0.302.b08-0.amzn2.0.1        amzn2-core
```

- `amaazon-linus-extras`를 이용해서도 설치가 가능하다.
```bash
$ sudo amazon-linux-extras install -y java-openjdk11
```

#### 1. 패키지 관리자에 `Corretto RPM` 리포지토리 추가

```bash
$ sudo rpm --import https://yum.corretto.aws/corretto.key
$ sudo curl -L -o /etc/yum.repos.d/corretto.repo https://yum.corretto.aws/corretto.repo
```

#### 2. 자바 11 설치

```java
$ sudo yum install -y java-11-amazon-corretto-devel

// 설치확인
$ java -version
openjdk version "11.0.13" 2021-10-19 LTS
OpenJDK Runtime Environment Corretto-11.0.13.8.1 (build 11.0.13+8-LTS)
OpenJDK 64-Bit Server VM Corretto-11.0.13.8.1 (build 11.0.13+8-LTS, mixed mode)
```

### Nginx

`Amazon Linux 2`에서는 yum을 통해 nginx를 설치 할 수 없다  
`amazon-linux-extras` 명령어를 통해 `nginx1`으로 설치해야 한다

```bash
$ amazon-linux-extras list | grep nginx
 38  nginx1                   available    [ =stable ]
```

#### Nginx 설치

```bash
$ sudo amazon-linux-extras install -y nginx1

// 설치확인
$ nginx -v
nginx version: nginx/1.20.0
```


### Git
git이 설치되어 있지 않기 때문에 직접 설치해줘야 한다

```bash
$ sudo yum install -y git
```