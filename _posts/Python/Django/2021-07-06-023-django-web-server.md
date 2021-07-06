---
layout: post
title: '장고 웹서버, WAS 셋팅(Nginx, uWSGI)'
categories: Django
tags: [Django]
---

### Python 및 Django 설치
1. Python 3.x 설치
   - Linux의 경우 2.x 버전이 설치되어 있으며 3.x버전을 따로 설치해야 한다.

```bash
# CentOS
$ yum install gcc openssl-devel bzip2-devel libffi-devel wget

$ wget https://www.python.org/ftp/python/3.8.11/Python-3.8.11.tgz
$ tar xzf Python-3.8.11.tgz             # 압축해제
$ cd Python-3.8.11
$ ./configure --enable-optimizations    # 컴파일
$ make altinstall    # 설치
```

- python 명령어를 기존의 2.x 버전에서 3.x 버전으로 변경

```bash
$ python -V
Python 2.7.5
$ python3 -V
Python 3.8.11

$ vi ~/.bashrc

alias python=python3 추가
alias pip=pip3 추가

$ source ~/.bashrc          # 설정 적용
```

2\. 가상환경 설정
  - python의 내장된 venv 이용 (virtualenv를 사용 해도 되긴 함)

```bash
$ python -m venv venv
$ source venv/bin/activate      # 가상환경 접속
```

3\. Django 및 패키지 설치  
  - 가상환경에서 장고를 포함한 패키지 설치

```bash
$ pip install django
$ pip install -r requirements.txt        # requirements.txt 를 이용한 패키지 설치
```


### uWSGI 설치
1. uWSGI 설치 및 구동 테스트
   - 가상환경 내에서 설치 및 테스트

```bash
$ pip install uwsgi
$ uwsgi --http :8000 --module project.wsgi          # project명.wsgi
```

2\. uWSGI 설정파일 생성
   - 경로 : /etc/uwsgi/sites/uwsgi.ini

```ini
[uwsgi]
# Django-related settings
# the base directory (full path)
chdir           = /usr/local/project/
# the virtualenv (full path)
home            = /usr/local/project/venv
# Django's wsgi file
module          = project.wsgi


# process-related settings
# master(마스터 프로세스)
master          = true
# maximum number of worker processes
processes       = 10
# the socket (use the full path to be safe)
socket          = /tmp/uwsgi.sock
# ... with appropriate permissions - may be needed
chmod-socket    = 666
# clear environment on exit(tmp파일 삭제)
vacuum          = true

# respawn processes after serving 1000 requests
max-requests = 1000
buffer-size = 65535

# respawn processes taking more than 60 seconds
harakiri = 60

# 배포시 0으로
py-autoreload = 0

# 로그
logger = file:/var/log/uwsgi.log
```

3\. uWSGI 서비스 등록
   - 경로 : /etc/systemd/system/uwsgi.service

```
[Unit]
Description=uWSGI
After=syslog.target

[Service]
ExecStart=/usr/local/project/venv/bin/uwsgi --emperor /etc/uwsgi/sites --uid root --gid nginx
Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```
→ --emperor 옵션으로 uwsgi.ini(설정파일) 디렉토리

4\. uWSGI 서비스 구동

```bash
$ systemctl start uwsgi         # 서비스 시작
$ systemctl enable uwsgi        # 서버 부팅시 자동시작 설정
$ systemctl status uwsgi        # 구동 확인

$ systemctl daemon-reload       # 수정후 데몬 reload
```
→ 에러 로그는 `/var/log/syslog`에 기록


### Nginx 설치
1. Nginx 설치

```bash
# CentOS
$ yum install nginx
```

2\. Nginx 설정파일 생성
   - 경로 : /etc/nginx/conf.d/project.conf
  
```conf
upstream django {       # 서버 그룹
    server unix:/tmp/uwsgi.sock;            # 유닉스 소켓사용
}

server {                # 서버 상세정의
        listen 80;
        server_name 127.0.0.1;

        charset utf-8;

        location / {
            include         /etc/nginx/uwsgi_params;
            uwsgi_pass      django;         # uWSGI 요청이 오면 바로 장고로
        }
}
```

3\. Nginx 기본 설정파일 변경
   - 경로 : /etc/nginx/nginx.conf

```conf
# ...생략...

http {

    include /etc/nginx/conf.d/*.conf;

    server {
        #... 생략 ...
    }
}

}

#...생략...
```
→ http > server > location 의 계층구조  
→ include를 사용하여 `conf.d/` 아래의 다른 conf 파일을 포함시킬 수 있다.

4\. Nginx 서비스 구동

```bash
$ systemctl start nginx
$ systemctl enable nginx 

# 이런 방법도 있다
$ nginx             # nginx 실행
$ nginx -s stop     # nginx 실행종료
```