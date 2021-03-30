---
layout: post
title: virtualenv, pip
categories: Django
tags: [Django]
---

### virtualenv

프로젝트별로 설정을 다르게 하기 위해 사용하는 독립적인 가상환경

●명령어

\- 가상환경 생성

```python
$ python -m venv 가상환경명
```

\- 가상환경 접속 ( Alt + F12 in Pycharm)

1\. Windows (윈도우는 \\)

```python
>call 가상환경명\Scripts\activate
```

2\. Linux (리눅스는 /)

```python
$ source 가상환경명/bin/activate
```

\- 가상환경 접속해제

```python
(venv) C:\Users\europa\pythonProject> deactivate
```

### pip

Python Package Index (PyPI) 저장소로부터 파이썬 패키지를 받아 설치하는패키지 관리 도구

가상환경에서 패키지를 관리할때 사용

●명령어

\- package install

```python
$ pip install package명
```

\- package install (requirements.txt 이용)

```python
$ pip install -r requirements.txt
```

\- 설치된 package 확인

```python
$ pip list
```

\- package 설명

```python
$ pip show package명
```

\- package 찾기

```python
$ pip search pacakge명
```

\- pip 업그레이드

```python
$ python -m pip install --upgrade pip
```

\- package 업그레이드 

```python
$ pip install --upgrade package명
```