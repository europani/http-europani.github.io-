---
layout: post
title: django 셋팅 (pycharm 포함)
categories: Django
tags: [Django]
---

### 1\. Python 설치

 - python 3.x 버전으로 적절히 골라서 설치

![](https://blog.kakaocdn.net/dn/WN0pG/btqWn2PQFEK/qB2sfFkHOENpeIAM3se5T0/img.png)

 - Add python 3.x to PATH 꼭 설정!!

### 2\. venv(가상환경) 설정

(1) 가상환경 생성

```bash
$ mkdir mypro
$ cd mypro
```

```bash
$ python -m venv 가상환경명
```

(2) 가상환경 활성화

\- Windows (윈도우는 \\)

```bash
>call 가상환경명\Scripts\activate
```

\- Linux (리눅스는 /)

```bash
$ source 가상환경명/bin/activate
```

### 3\. 패키지 설치(pip 사용)

\- django 설치

```bash
$ pip install django
```

### 4\. 프로젝트, 앱 생성

```bash
$ django-admin startproject mypro .		# mypro라는 project 생성  /  .는 현재 디렉토리인 mypro를 의미

$ django-admin startapp board			# board 라는 app생성
```

---

#### **Pycharm 에서 프로젝트 import**

0\. git clone

1\. pycharm에서 import 후 **python interpreter**, **venv** 설정

![](https://blog.kakaocdn.net/dn/cOhNVN/btqWNdKezP0/cJaUftNDeULPrKJeLeh8jk/img.png)

→ Location : import 하는 프로젝트의 venv 선택

→ Base interpreter : 컴퓨터에 설치되어 있는 python의 exe

→ Dependencies : import 하는 프로젝트의 requirements.txt

2\. requirements.txt를 통해 필요한 패키지 다운로드

```bash
$ pip install -r requirements.txt
```

\- requirements.txt를 통해 설치시 오류가 나는 패키지는 직접 최신버전으로 설치 (pip install 명령어사용) 

3\. 설정파일(config.yml) 존재 시 적절한 디렉토리에 배치

4\. Pycharm run configuration 설정

(1) python 사용시 (community version)  
- 우측상단 Add Configuration -> + 버튼 -> Python

![](https://blog.kakaocdn.net/dn/VUwb6/btqWsJbADMW/Q0OffKb5AKKxRE5fs7fIH1/img.png)

→ name : 마음대로 설정  
→ Script path : 프로젝트의 manage.py  
→ Parameters : runserver  
→ Python interpreter : 해당 프로젝트 venv 파이썬 interpreter (Project Default)  


(2) django server 사용시 (professional version)
- 우측상단 Add Configuration -> + 버튼 -> Django Server

![](https://user-images.githubusercontent.com/48157259/116200182-fc8ad780-a772-11eb-9dee-a05df0e0ad7a.png)

→ 환경변수에 settings 파일 명시 


![](https://user-images.githubusercontent.com/48157259/116200311-23490e00-a773-11eb-9969-2a1b1061fb75.png)

→ 장고 프레임워크 사용 설정 (root, settings, manage.py)


#### MacOS mysqlclient 패키지 설치 오류시
  - OpenSSL을 통해 설치해야 함

```bash
$ brew install mysql@5.7      # MySQL 먼저 설치
[...MySQL 셋팅...]
$ brew install openssl

$ source ./venv/bin/activ     # 가상환경진입
# 가상환경에서 openSSL을 사용하여 mysqlclient 설치
(venv)$ LDFLAGS=-L/usr/local/opt/openssl/lib pip install mysqlclient==1.4.4
```