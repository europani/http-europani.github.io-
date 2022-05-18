---
layout: post
title: Git Commit Message 쓰는 법
categories: Git
tags: [Git]
---
커밋 메시지를 작성하는 도중 너무 그때그때 만들어 내는 느낌을 받았다. 형상관리에서 커밋 메시지는 영원히 기록에 남아 코드 추적을 남길텐데 좀 더 좋은 메시지를 작성할 필요가 있다고 생각했다.

### 커밋 메시지를 잘 써야하는 이유
1. 더 좋은 커밋 로그 가독성
2. 더 나은 협업과 리뷰 프로세스
3. 더 쉬운 코드 유지보수

```
제목(Subject)

본문(Body) - 선택

푸터(footer) - 선택
```

- 제목 : 50자이내, 명령문, 첫글자는 대문자, 마침표금지
- 내용 : 한줄에 72자이내
- 푸터 : Issue번호

### 커밋 메시지 작성 방법 7가지
본 규칙은 영어에 특화되어 있습니다. 
1. 제목과 본문 사이는 빈 칸으로 구분
2. 제목은 50자 이내
3. 제목의 첫글자는 대문자 
   - ex) Refectoring login process
4. 제목 끝에 마침표(.) 금지
5. 제목은 명령문으로 작성(동사로 시작) 
   - 문장이 아닌 단어로 연결 (ex XXXX 수정)
6. 내용은 영문 기준 한 줄에는 72자이내
7. How 보단 `What`, `Why` 기록

- 제목에는 50자 이내의 요약문장을 작성
- 내용에 추가적인 설명을 작성

### 자주 쓰는 단어
- 키워드
  - `in` : 어느 파일에서 수정했는지 명시
  - `to` or `for` : 수정 이유 명시
  - `when` : 어느 상황에 발생 했었는지 명시

#### 1. Fix
- 잘못된 로직을 수정할 때 사용

- Fix A in B : B에서 A를 수정했습니다
```
Fix type in abcController
```

- Fix A when B : B일때 발생하는 A를 수정했습니다
```
Fix crash when removing root nodes
```

#### 2. Add
- 무엇인가 추가했을 때 사용

- Add A for B : B를 위해 A를 추가했습니다
```
Add documentation for the defaultPort option
```

- Add A to B : A에 B를 추가했습니다
```
Add deprecation notice to SwipeableListView
```
  
#### 3. Remove
- 무엇을 제거했을 때 사용
- `from` : 어느 파일에서 제거했는지 명시

- Remove A from B : B에서 A를 삭제했습니다
```
Remove absolute path parameter from transformers
```

#### 4. Refactor
- 코드를 리펙토링 했을 때 사용 (전면 수정)

#### 5. Simplify
- 코드를 단순화 했을 때 사용 (약한 수정)

#### 6. Update
- Fix와 달리 정상적으로 동작하지만 보완을 했을 때 사용

- Update A to B : A를 B로 업데이트합니다
```
Update acorn to 6.1.0
```

#### 7. Prevent
- 특정 동작을 못하게 막았을 때 사용

- Prevent A from B : A가 B하지 못하게 막습니다
```
Prevent event handlers from receiving extra argument in development
```

#### 8. Move
- 코드나 파일을 이동시켰을 때 사용

- Move A to B : A를 B로 이동합니다
```
Move function from header to source file
```

#### 9. Rename
- 이름을 변경했을 때 사용

- Rename A to B : A를 B로 이름 변경합니다
```
Rename node-report to report
```

<hr>

\<출처>  
https://cbea.ms/git-commit/  
https://blog.ull.im/engineering/2019/03/10/logs-on-git.html