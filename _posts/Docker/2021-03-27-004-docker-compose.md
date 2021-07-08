---
layout: post
title: Docker-Compose
categories: Docker
tags: [Docker]
---

## docker-compose 
  * 도커 컨테이너 간의 연결
  * 각 컨테이너의 Dockerfile을 사용하여 컨테이너를 만들어 연결한다
  * docker-compose 에서는 컨테이너를 서비스라고 한다
  * 파일명 : docker-compose.yml

```yml
version: "3"            # docker-compose 버전
services:               # 서비스 묶음
  database:             # 서비스 이름
    image: mysql:5.7        # hub에 업로드된 image 파일
    container_name: mysql   # 컨테이너 이름 설정
    restart: always
    ports:
      - "3306:3306"         # 로컬 포트:컨테이너 포트
    environment:      # -e
      - MYSQL_ROOT_PASSWORD= {패스워드}
      - MYSQL_USER= {user명}
      - MYSQL_PASSWORD= {패스워드}
      - MYSQL_DATABASE= {db명}
    volumes:          # -v
      - ./database/mysql_data:/var/lib/mysql      # 외부 디렉토리:컨테이너 디렉토리
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
  backend:
    build: 
      context: ./backend        # Dockerfile의 위치
      dockerfile: Dockerfile    # Dockerfile 명
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
  frontend:
    build: ./frontend
    volumes:
      - ./frontend:/usr/src/app  
    ports:
      - "8080:8080"
```
→ DB컨테이너를 먼저 만들고 백엔드 컨테이너를 만들어야 오류가 생기지 않는다  
→ 위에서부터 순서대로 실행되기 때문에 DB컨테이너를 맨 위에 위치시켜야한다


\* 실행

```bash
$ docker-compose up
```
→ docker-compose.yml 디렉토리에서 실행해야 함.