---
layout: post
title: '스프링부트 Nginx 웹서버 셋팅'
categories: Spring
tags: [Spring]
---

### 자바 설치
#### 1. Openjdk 11 설치
- 서버에 깔려있는 8버전을 대신하여 사용할 11버전을 따로 설치한다.

```bash
# jdk
$ yum install java-11-openjdk-devel 
# jre (jdk를 설치할 경우에는 jre가 포함되어 있어서 따로 설치할 필요가 없다)
$ yum install java-11-openjdk
```

#### 2. Java 버전 설정
```bash
$ java -version
openjdk version "1.8.0_131"

$ alternatives --config java
2 개의 프로그램이 'java'를 제공합니다.

  선택    명령
-----------------------------------------------
*+ 1           java-1.8.0-openjdk.x86_64 (/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.131-3.b12.el7_3.x86_64/jre/bin/java)
  2           java-11-openjdk.x86_64 (/usr/lib/jvm/java-11-openjdk-11.0.12.0.7-0.el7_9.x86_64/bin/java)

현재 선택[+]을 유지하려면 엔터키를 누르고, 아니면 선택 번호를 입력하십시오:2

$ java -version
openjdk version "11.0.12"
```

#### 3. Java 환경변수(JAVA_HOME) 설정
```bash
$ vi ~/.zshrc

JAVA_HOME="/usr/lib/jvm/java-11-openjdk-11.0.12.0.7-0.el7_9.x86_64/bin/java" 추가

$ source ~/.zshrc         # 설정 적용
```

### Nginx 설치
#### 1. Nginx 설치 및 서비스 구동

```bash
# CentOS
$ yum install nginx
$ systemctl start nginx
$ systemctl enable nginx 
```

#### 2. Nginx 설정파일 생성
   - 경로 : /etc/nginx/conf.d/project.conf
  
```conf
server {
    listen 80;
    listen [::]:80;

    server_name localhost;

    location / {
         proxy_pass http://localhost:8080;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header Host $http_host;
    }
}
```
→ `127.0.0.1:80/` 로 요청이 들어오면 8080 포트로 연결한다. 

#### 3. Nginx 재구동

```bash
$ systemctl restart nginx
```

<br>
- 오류 해결

```
*1 connect() to 127.0.0.1:8080 failed (13: Permission denied) while connecting to upstream,
```
→ 502 페이지가 뜨고 log 확인시 `Permission denied`가 발생할 경우 SELinux의 httpd 관련 허용 설정이 필요하다

```bash
$ setsebool -P httpd_can_network_connect on
```

### 빌드 된 Jar 파일 실행
- FTP를 통해 아래 디렉토리로 빌드된 jar파일 전송

```bash
$ cd /usr/local/mypro
$ java -jar mypro-0.0.1-SNAPSHOT.jar

# 백그라운드 실행
$ nohup java -jar mypro-0.0.1-SNAPSHOT.jar &

# 백그라운드 종료시
$ ps -ef | grep java
root     10197  9188 46 12:28 pts/0    00:00:21 java -jar mypro-0.0.1-SNAPSHOT.jar
$ kill -9 10197
```