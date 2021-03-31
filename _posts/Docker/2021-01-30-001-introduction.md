---
layout: post
title: 도커
categories: Docker
tags: [Docker]
---

Docker File : 도커 이미지를 만들기 위한 파일로써 CMD를 사용하여 파일과 설정값을 셋팅함

Docker Image : 컨테이너 실행에 필요한 파일과 설정값이 포함되어 있는 파일, Immutable

Docker Container : 파일이 실행되는 독립적인 환경, Mutable

Docker hub : 도커 이미지파일을 저장할 수 있는 remote repository

 

도커파일 생성 --(build)--> 파일을 통한 도커이미지 생성 ----> 도커허브에 업로드 ----> 허브에 업로드된 이미지를 다운받아 사용(=컨테이너 생성, run)