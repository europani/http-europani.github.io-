---
layout: post
title: GitHub 사용자 인증(PAT, SSH keys)
categories: Git
tags: [Git]
---

깃허브 보안 인증에는 3가지 방법이 있다.  
이름과 패스워드로 인증하는 방법은 2021-08-13 부터 폐지되었다.

1. ~~Username and Password Only~~
2. Personal Access Token(PAT)
3. SSH keys



### Personal Access Token(PAT)
깃허브 페이지에서 토큰을 생성하여 비밀번호 대신 사용하는 방식이다.  
토큰의 만료기한이나 권한도 설정할 수 있다.  
`Profile > Settings > Developer settings > Personal access tokens`

#### (1) Generate new token  
- 토큰명, 만료기한, 권한을 설정한다. (권한은 일반적으로 repo) 
- `Generate token`을 누르면 토큰이 생성되어 토큰값을 확인할 수 있다
- 토큰값은 페이지를 벗어나면 절대 재확인 할 수 없으니 복사해둔다 

![image](https://user-images.githubusercontent.com/48157259/141709587-6c117f51-9c4e-44ea-b1bf-b4b528ea1e40.png)


#### (2) 생성된 토큰 사용
- Push 나 Clone 작업을 수행하면 `username` 과 `password` 를 입력하라고 할 것이다
- `password`에 생성한 토큰값을 입력하면 된다




### SSH keys
터미널에서 SSH 키를 생성하여 깃허브에 등록하는 방식이다.

#### (1) SSH 키 생성

```bash
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "github"
```

#### (2) 생성한 키 등록
- 커맨드를 통해 생성된 SSH키 중 **public키**를 깃허브에 등록한다  
`Profile > Settings > SSH and GPG keys`

```bash
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDREnWhlKfRXpWX1PCXqzVPAl3qb3asscB6xHfn3zts57ybtXapjHw== github
```

![image](https://user-images.githubusercontent.com/48157259/141710201-a2a7ccdf-afb0-4d4c-8d06-425c2df7aee0.png)

#### (3) SSH 접속 설정 (선택)
- `~/.ssh/config` 파일에 호스트와 **private키**를 설정한다
- 이 과정이 없어도 키를 최초로 사용할 때 `~/.ssh/known_host`에 추가되어 그냥 사용이 가능하다

```bash
$ vi ~/.ssh/config
Host github.com
  IdentityFile ~/.ssh/id_rsa
  User git
```

- 테스트 진행

```bash
$ ssh -T git@github.com
```



