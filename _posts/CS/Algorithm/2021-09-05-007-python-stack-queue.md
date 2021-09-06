---
layout: post
title: 파이썬 스택, 큐
categories: Algorithm
tags: [Algorithm, CS, Python]
---
### 스택
- 배열 사용
- `append(원소)` : push 실행
- `pop()` : pop 실행

```python
stack = []

stack.append(5)
stack.append(2)
stack.append(3)
stack.append(7)
stack.pop()
stack.append(1)

print(stack)                # [5, 2, 3, 1]
print(stack[::-1])          # [1, 3, 2, 5]
```

### 큐
- `collection` 라이브러리의 **데크** 사용
- `append(원소)` : enqueue 실행
- `popleft()` : dequeue 실행

```python
from collections import deque

queue = deque()

queue.append(5)
queue.append(2)
queue.append(3)
queue.append(7)
queue.popleft()
queue.append(1)

print(queue)            # deque([2, 3, 7, 1])
queue.reverse()
print(queue)            # deque([1, 7, 3, 2])
```