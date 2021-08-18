---
layout: post
title: Gitlab-runner를 이용한 CI/CD 구축 
categories: Git
tags: [Git]
---
Jenkins, travisCI, Github-Actions, Gitlab-runner 등 다양한 CI/CD 도구들이 있다.  
이번에는 gitlab을 사용하는 만큼 gitlab-runner로 파이프라인을 구축해보려고 한다.

### 1\. gitlab-runner 설치
- gitlab-runner는 Jenkins과 같이 설치형으로써 서버에 직접 설치하여 사용한다

```bash
$ curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.rpm.sh | sudo bash
$ yum install gitlab-runner
$ chmod +x /usr/local/bin/gitlab-runner
$ systemctl status gitlab-runner

# root 유저로 설치
$ gitlab-runner install --user=root --working-directory=/home/gitlab-runner

# 만약 install 명령시 이미 실행중이라고 뜬다면 service 파일에서 사용자를 직접 수정
$ cd /etc/systemd/system
$ vi gitlab-runner.service

ExecStart=/usr/bin/gitlab-runner .... "--user" "root"

# gitlab-runner 서비스 재시작
$ systemctl daemon-reload
$ systemctl restart gitlab-runner
```

### 2\. runner 등록
- gitlab의 push를 감지하고 event가 일어나면 입력해놓은 커맨드를 실행하는 것을 runner라고 한다
- 한 서버에 여러개의 runner를 등록 할 수도 있다
- 서버주소와 토큰은 `Gitlab > project > Settings > CI/CD > Runners`에서 확인 가능하다

```bash
$ gitlab-runner register

# 1. gitlab 서버주소 (gitlab project runner에서 확인)
https://gitlab.com/

# 2. token (gitlab project runner에서 확인)
yboVk-t_P.......So

# 3. descrption - name 설정
mypro production

# 4. tag (매우중요!!) - gitlab-ci.yml에서 해당 tag를 참조하여 실행함
master

# 5. executor
shell
```

### 3\. gitlab 사이트에서 runner등록 확인
- `Gitlab > project > Settings > CI/CD > Runners`


### 4\. gitlab-ci.yml 작성

```yml
deploy-to-develop:
  stage: deploy
  only:
    - develop
  variables:
    GIT_STRATEGY: clone
    PROJECT_PATH: /usr/local/mypro
  script:
    - whoami
    - cd $PROJECT_PATH
    - git pull
    - docker-compose up -d --build
    - echo "Deployed To Develop Server"
  tags:           # runner 등록 4번 과정의 tag와 맵핑된다
    - develop
    
deploy-to-production:
  stage: deploy
  only:
    - master
  variables:
    GIT_STRATEGY: clone
    PROJECT_PATH: /usr/local/mypro
  script:
    - whoami
    - cd $PROJECT_PATH
    - git pull
    - docker-compose up -d --build
    - echo "Deployed To Production Server"
  tags:           # runner 등록 4번 과정의 tag와 맵핑된다
    - master
```
- Feature-Branch 전략을 사용하고 있으며 배포가 `develop`과 `master` 브랜치에서 이루어진다
- 개발환경-`develop`, 운영환경-`master` 브랜치에 각각 반응하여 배포한다
- `develop`브랜치에 push되면 `deploy-to-develop`, `master`브랜치에 push되면 `deploy-to-production` job이 작동한다



### runner 삭제
- 등록되어 있는 runner를 삭제한다
```bash
$ gitlab-runner unregister --url <url> --token <token>    # runner 삭제
$ gitlab-runner verify --delete                           # 최신정보로 갱신
```

```bash
# 1. 등록되어 있는 runner 확인
$ gitlab-runner list 
Runtime platform                                    arch=amd64 os=linux pid=235182 revision=1b659122 version=12.8.0
Listing configured runners                          ConfigFile=/etc/gitlab-runner/config.toml
mypro production                                    Executor=shell Token=yboVk-t_P.......So URL=https://gitlab.com/

$ cat /etc/gitlab-runner/config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "mypro production"
  url = "https://gitlab.com/"
  token = "yboVk-t_P.......So"
  executor = "shell"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]

# 2. runner 삭제
$ gitlab-runner unregister --url https://gitlab.com/ --token yboVk-t_P.......So
Runtime platform                                    arch=amd64 os=linux pid=3357 revision=1b659122 version=12.8.0
Running in system-mode.

Unregistering runner from GitLab succeeded          runner=yboVk-t_

$ gitlab-runner verify --delete
Runtime platform                                    arch=amd64 os=linux pid=4445 revision=1b659122 version=12.8.0
Running in system-mode.

ERROR: Verifying runner... is removed               runner=yboVk-t_


# 3. runner 삭제 확인
$ gitlab-runner list
Runtime platform                                    arch=amd64 os=linux pid=4627 revision=1b659122 version=12.8.0
Listing configured runners                          ConfigFile=/etc/gitlab-runner/config.toml

$ cat /etc/gitlab-runner/config.toml
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800
```