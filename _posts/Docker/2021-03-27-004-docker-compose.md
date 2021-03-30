---
layout: post
title: Docker-Compose
categories: Docker
tags: [Docker]
---

## docker-compose 
  * 도커 컨테이너 간의 연결
  * 파일명 : docker-compose.yml

```yml
version: "3"        # docker-compose 버전
services:           # 컨테이너 묶음
  database:             # 컨테이너 이름
    build: ./database       # Dockerfile의 위치
    ports:
      - "3306:3306"
  backend:
    build: ./backend
    volumes:
      - ./backend:/usr/src/app
    ports:
      - "8080:8080"             # 로컬 포트:컨테이너 포트
    environment:
      - DBHOST=database     # 컨테이너명-database
  frontend:
    build: ./frontend
    volumes:
      - ./frontend:/usr/src/app  # 외부 디렉토리:컨테이너 디렉토리
    ports:
      - "8080:8080"
```

- 실행
```bash
$ docker-compose up
```
→ docker-compose.yml 디렉토리에서 실행해야 함.