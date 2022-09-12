---
layout: post
title: 브랜치 merge 방법
categories: Git
tags: [Git]
---

### 1. Merge
- 작업한 브랜치를 메인 브랜치로 병합시키는 방법

```git
$ git checkout master
$ git merge develop
```

#### (1) Fast-Forward
![image](https://user-images.githubusercontent.com/48157259/189587735-c1843b8d-59ab-47e8-a5dd-bcacc1572775.png)

- base에 변경이 생기지 않았을 때 실행
- `develop`로 분기 이후 `master`에 새로운 커밋이 생기지 않고 최신 상태를 유지하고 있다면 병합시 `develop`의 커밋을 그대로 다 가져올 수 있다

#### (2) Recursive
![image](https://user-images.githubusercontent.com/48157259/189587768-c02313ec-88f9-43b5-a297-a15b7987bc82.png)

- base에 변경이 일어 났을 때 실행
- `develop`로 분기 이후 `master`에 새로운 커밋이 생긴다면 `develop` 병합시 `master`의 변경된 내용과 합쳐서 병합이 이루어 져야 한다
- merge 커밋 메시지를 작성


### 2. Squash & Merge 
![image](https://user-images.githubusercontent.com/48157259/189587820-47250af1-0d18-4129-8d9b-50269290f602.png)

- 여러 개의 commit을 하나로 합쳐서 병합시키는 방법
- `develop`의 여러 commit을 합친 새로운 commit을 만들어 `master`에 추가된다

```git
$ git checkout master
$ git merge --sqaush develop
$ git commit -m "squah & merge"
```


### 3. Rebase & Merge
![image](https://user-images.githubusercontent.com/48157259/189587843-bacea8ee-a592-453e-9d15-c3062e9bdd56.png)

- 메인 브랜치로 base를 변경해 병합시키는 방법
- `develop`의 base를 `master`로 변경해 `develop`의 커밋을 `master`의 최신 커밋 뒤로 추가한다
- rebase 후에 merge하는 과정이 필요하다

```git
$ git checkout develop
$ git rebese master        # REBASE
$ git checkout master
$ git merge develop
```


<hr>
\<출처>  
https://hudi.blog/git-merge-squash-rebase/