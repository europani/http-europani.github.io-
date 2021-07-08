---
layout: post
title: '도커 설정(Django-Nginx-uWSGI-MySQL)'
categories: Docker
tags: [Docker]
---

### 0. 도커 설치



### 1. Mysql 컨테이너
 - Mysql은 Dockerfile을 작성하지 않고 도커허브에 올라와 있는 공식 image를 사용할 것이다.
 - `docker-compose.yml`의 mysql 컨테이너 부분은 다음과 같다.

```yml
services:
  database:
    image: mysql:5.7        # mysql 5.7 공식 이미지
    container_name: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD={root비밀번호}
      - MYSQL_DATABASE={DB명}
    volumes:
      - ./database/mysql_data:/var/lib/mysql    # 컨테이너의 데이터를 호스트OS에 저장되도록 설정 
    command:
      - --character-set-server=utf8mb4          # 인코딩
      - --collation-server=utf8mb4_unicode_ci   # 인코딩
```


### 2. Backend 컨테이너

- Backend 도커파일

```dockerfile
FROM python:3.8

ENV PYTHONUNBUFFERED 1            #

RUN apt-get -y update
RUN apt-get -y install build-essential python3-dev    # 파이썬 패키지 설치에 필요한 패키지 설치

RUN mkdir /usr/local/edix-net

WORKDIR /usr/local/edix-net

COPY requirements.txt .                               # 의존성 파일 먼저 복사하여 패키지 설치

RUN pip install --upgrade pip
RUN pip install -r requirements.txt

COPY . .
```
→ `COPY requirements.txt .`를 먼저 실행 해야 의존성 파일을 매번 다운받지 않아도 된다.  
→ 그 후, `COPY . .`를 실행하여 backend폴더 전체를 컨테이너로 복사시킨다.

- uwsgi.ini (uWSGI 설정파일)

```ini
[uwsgi]
# Django-related settings
chdir           = /usr/local/mypro/
module          = mypro.wsgi:application

# process-related settings
master          = true
processes       = 10
socket          = /usr/local/mypro/uwsgi.sock     # 유닉스소켓
chmod-socket    = 666
vacuum          = true

# respawn processes after serving 1000 requests
max-requests = 1000
buffer-size = 65535

# respawn processes taking more than 60 seconds
harakiri = 60

# 배포시 0으로
py-autoreload = 0

logger = file:/var/log/uwsgi/uwsgi.log
```

### 3. Nginx 컨테이너

- Nginx 도커파일

```dockerfile
FROM nginx:latest

COPY nginx.conf /etc/nginx/nginx.conf               # nginx 설정파일 복사
COPY edix.conf /etc/nginx/conf.d/edix.conf          # nginx 기본 설정파일 복사

CMD ["nginx", "-g", "daemon off;"]
```
→ Nginx는 CMD를 `docker-compose.yml`에 작성하지 않고 도커파일에 직접 작성한다

- mypro.conf (Nginx 설정파일)

```conf
upstream django {
     server unix:///usr/local/mypro/uwsgi.sock; 
}

server {
        listen 80;
        server_name 127.0.0.1;

	charset utf-8;

	location = /favicon.ico { access_log off; log_not_found off; }

	location / {
            include         /etc/nginx/uwsgi_params;
            uwsgi_pass      django;
        }
}
```
→ uWSGI 컨테이너가 생성한 `uwsgi.sock`을 사용한다.


### 4. docker-compose.yml 작성
- 이제 mysql + backend + nginx 컨테이너를 결합하기 위해 `docker-compose.yml`을 작성한다.

```yml
version: "3"
services:
  database:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD={root비밀번호}
      - MYSQL_DATABASE={DB명}
    volumes:
      - ./database/mysql_data:/var/lib/mysql
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile-dev
    container_name: backend
    restart: always
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/usr/local/edix-net
      - ./log/backend:/var/log/uwsgi
    command: uwsgi --ini uwsgi.ini
  nginx:
    build:
      context: ./nginx
      dockerfile: Dockerfile-dev
    container_name: nginx
    restart: always
    ports:
      - "80:80"
    volumes:
      - ./backend:/usr/local/edix-net
      - ./log/nginx:/var/log/nginx
```

- 디렉토리 구조  
프로젝트명 : mypro

```
/usr/local/mypro
 │
 ├── backend         
 │   ├── Dockerfile
 │   ├── Dockerfile-dev
 │   ├── .gitignore
 │   ├── manage.py
 │   ├── README.md
 │   ├── requirements.txt
 │   ├── mypro
 │   │   ├── __init__.py 
 │   │   ├── asgi.py 
 │   │   ├── settings.py 
 │   │   ├── urls.py 
 │   │   └── wsgi.py 
 │   ├── uwsgi.sock      # 유닉스소켓
 │   └── uwsgi.ini       # uWSGI 설정파일
 │
 ├── frontend           
 │   ├── Dockerfile
 │   ├── Dockerfile-dev
 │   ├── .gitignore
 │   ├── 
 │   
 ├── database           
 │   ├── .gitignore
 │   └── mysql_data       # DB 데이터
 │   
 ├── nginx           
 │   ├── Dockerfile-dev
 │   ├── .gitignore
 │   ├── nginx.conf
 │   └── mypro.conf        # nginx 설정파일 
 │ 
 ├── docker-compose.yml
 ├── log/
 └── config/
```