---
layout: post
title: 애플리케이션 계층(L7)
categories: Network
tags: [Network, CS]
---

## 프로토콜
#### HTTP [TCP : 80]
- HyperText Transfer Protocol
- 웹에서 HTML 등을 주고 받기 위한 프로토콜 
- [(링크)](https://europani.github.io//web/2021/04/16/002-HTTP.html)

#### HTTPS [TCP : 443]
- HyperText Transfer Protocol over SSL
- HTTP보다 보안이 강화된 웹 프로토콜
- 정보를 암호화하는 SSL이나 TLS 프로토콜를 사용
- **SSL 인증서**를 통해 사용자가 사이트에 제공하는 정보를 암호화하여 전송할 수 있다. 이로 인해 중간에서 누군가 전송되는 데이터를 탈취 한다 해도 암호화된 데이터는 해독이 불가능하다

### 보안 프로토콜
#### SSL(Secure Socket Layer)
![image](https://user-images.githubusercontent.com/48157259/165896091-17d29de3-4664-4718-84a6-025259faab9a.png)

- 7계층의 어느 Layer에서도 보안기능을 제공하지 않는다
- 부족한 보안기능을 제공해주는 부분
    - 새로운 Layer가 아닌 `애플리케이션 Layer`에 속하는 부분이다
    - SSL의 업그레이드 버전이 **TLS(Transport Layer Secure)**이다

1. SSL 레코드
- 레코드 : SSL의 전송단위
- SSL은 애플리케이션 계층에서 만든 메시지를 레코드로 변환한다
![image](https://user-images.githubusercontent.com/48157259/165896168-952ac319-ef41-456f-91e2-f7c878a00190.png)
- 헤더 : Type, Version, Length
- MAC(Message Authentication Code)

2. MAC(Message Authentication Code)
- 메시지`m`을 해싱하여 해시값`H(m)`을 만들 수 있다
- 하지만 얼마든지 조작이 가능하기 때문에 둘만아는 인증키`s`를 사용해야 한다
- **인증키를 포함시킨 해시값 `H(m+s)`가 MAC이다**


### 주소 변환 및 설정 프로토콜
#### DNS [TCP&UDP : 53]
 - 도메인(문자)을 IP주소(숫자)로 변환하는 프로토콜
 - 계층형 구조

|www|korea|co|kr|
|4단계|3단계|2단계|최상위|

#### DHCP
 - 인터넷에 접속하기 위한 접속환경 설정
 - 설정 : IP주소, 서브넷마스크, 게이트웨이 주소, DNS주소


### 원격접속 프로토콜
#### Telnet [TCP : 23]
- 대칭적인 통신을 통해 클라이언트와 서버가 동일한 명령 및 응답을 주고 받음

### 파일전송 프로토콜
#### FTP [TCP : 21]
- 큰 크기의 파일 전송


#### TFTP [UDP : 69]
- 작은 크기의 파일 전송(Trivial)

### 전자메일 프로토콜
#### SMTP [TCP : 25]
- 텍스트 파일만 전송
- MIME 프로토콜: 멀티미디어 데이터 파일 전송가능

#### POP [TCP : 110]
- 수신된 모든 메일을 서버로부터 클라이언트로 전송하는 기능(필터 불가능)

#### IMAP [TCP : 143]
- 수신된 메일 중 제목만 확인하고 선택적으로 서버로부터 클라이언트로 전송하는 기능
- POP에 필터기능 탑재



### 네트워크 관리 프로토콜
#### SNMP [UDP : 161, 162]
 - 네트워크 관리자와 관리 대상 에이전트 간의 프로토콜
 - 구성요소 : 관리 시스템, 관리 에이전트, 관리 프로토콜, MIB