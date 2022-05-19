---
layout: post
title: 'AWS CodeDeploy Agent 저장 파일 갯수변경'
categories: AWS
tags: [AWS]
---
- EC2 인스턴스에 CodeDeploy Agent를 설치해 자동배포를 하고 있다.
- 하지만, 인스턴스의 용량이 얼마 남지 않은 것을 확인했고 원인을 파악했다.
- 문제는 설치된 CodeDeploy Agent가 배포에 쓰이는 jar파일을 서버에 저장하여 이력을 관리하고 있었다.
- 설정을 변경해 Agent가 보관하는 파일의 갯수를 변경하면 된다
- 기본 설정 값은 **5개**이다


- 배포에 쓰이는 파일이 저장되는 위치
  - `/opt/codedeploy-agent/deployment-root/{배포그룹ID}/{배포ID}`


### CodeDeploy Agent 설정변경
- EC2 인스턴스에서 해당 설정파일을 찾는다
  - `/etc/codedeploy-agent/conf`
- `max_revisions` 속성에서 갯수를 변경한다
