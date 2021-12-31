---
layout: post
title: 알고리즘을 위한 파이썬
categories: Algorithm
tags: [Algorithm, CS, Python]
---
### 0. 시간복잡도
- 파이썬은 초당 2000만번 연산이 가능하다

★시간 제한이 1초인 경우

|N의 범위|시간복잡도|
|:--:|:--:|
|500|O(N<sup>3</sup>)|
|2000|O(N<sup>2</sup>)|
|100,000|O(NlogN)|
|10,000,000|O(N)|

### 1. 리스트(List)
- 크기가 n이고, 모든 값이 0인 1차원 리스트 초기화

```python
n = 10
array = [0] * n

print(array)
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

- 크기가 n*m이고, 모든 값이 0인 2차원 리스트 초기화

```python
n = 3
m = 4
array = [[0] * m for _ in range(n)]

print(array)
[[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
```

- 리스트 Comprehension

```python
# 0~19 중 홀수만 포함하는 리스트
array = [i for i in range(20) if i % 2 == 1]

print(array)
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19]
```

- 리스트 Method  
(1) append(v) : 리스트에 v를 삽입 [O(1)]  
(2) insert(i, v) : i 위치에 v를 삽입 [O(N)]  
(3) remove(v) : 리스트에서 v를 삭제, 여러개일 경우 하나만 삭제 [O(N)]  
(4) pop(i) : 리스트에서 i위치의 원소를 꺼냄 (default : 마지막원소) [O(N)]  
  - pop()는 [O(1)]

```python
array = [1, 4, 3]

array.append(2)
print(array)
[1, 4, 3, 2]

array.insert(2, 5)
print(array)
[1, 4, 5, 3, 2]

array.remove(4)
print(array)
[1, 5, 3, 2]

array.pop()
print(array)
[1, 5, 3]
v = array.pop(0)
print(v)    # 1
print(array)
[5, 3]
```

- 리스트 값 찾기
  
```python
array = [1, 4, 3]

print(1 in array)   # True
print(5 in array)   # False
```
- 리스트 + 리스트
  
```python
array = [1, 4, 3]
array = array + [5]
print(array)  # [1, 4, 3, 5]
```

- 리스트를 공백으로 붙여서 출력

```python
array = ['a', 'b', 'c']
print("".join(array))
abc

array2 = [1, 2, 3, 4]
print("".join(map(str, array2)))
1234
```

- 리스트 값 출력 : `*`사용

```python
for x in array:
  print(x)

# 위와 같은 출력을 보임
print(*array)
```

- 리스트 복사 : `슬라이싱[:]` 사용

```python
a=[1, 2, 3]
b=a
c=a[:]

id(a), id(b), id(c)
8274944 8274944 8274304
```

### 2. 딕셔너리(Dictionary, Map)
- 딕셔너리 Method  
(1) `keys()` : 모든 key값을 리스트로 출력  
(2) `values()` : 모든 value값을 리스트로 출력  
(3) `items()` : 모든 key-value쌍을 리스트로 출력  
<br>
(4) `get(key값, [false시 출력할 값])` : 해당 key의 value값 출력, 해당 key가 없을시 설정한 값 출력  
(5) `key값 in dic` : 해당 dic에 key값이 존재하면 True  
(6) `del dic[key값]` : 딕셔너리에서 삭제

```python
dic = dict()
dic['name']='europani'
dic['age']=20

# get values
for v in dic.values() {
    print(v);
}

# get keys
for k in dic.keys() {
    v = v_dic.get(k);
    print(k, v);
}

# get keys, values
for k, v in dic.items() {
    print(k, v);
}

dic['name']         # europani
dic.get('name')     # europani

dic['address']      # ERROR!!
dic.get('address', 0)   # 0 (default 설정안하면 None)

'age' in dic        # True
'address' in dic    # False

del dic['age']
print(dic)          # dic = {'name' : 'europani'}
```

- `defaultdict(디폴트 자료형)` : 디폴트값을 지정하여 없는 키가 호출됐을 때 디폴트 값을 넣는 딕셔너리

```python
from collections import defaultdict

dic = defaultdict(list)
print(dic)     # defaultdict(<class 'list'>, {})

dic['name']='europani'
dic['hobby']
print(dic)    # defaultdict(<class 'list'>, {'name': 'europani', 'hobby': []})
```

- 딕셔너리 sort

```python
dic = {
  'a' : 4,
  'c' : 3,
  'e' : 1,
  'b' : 5
}
print(dic)          # {'a': 4, 'c': 3, 'e': 1, 'b': 5}
print(dic.items())  # dict_items([('a', 4), ('c', 3), ('e', 1), ('b', 5)])

# key 오름차순 정렬
dic_sort1 = sorted(dic.items(), key=lambda x:x[0])
print(dic_sort1)    # [('a', 4), ('b', 5), ('c', 3), ('e', 1)]

# value 오름차순 정렬
dic_sort2 = sorted(dic.items(), key=lambda x:x[1])
print(dic_sort2)    # [('e', 1), ('c', 3), ('a', 4), ('b', 5)]

# value 내림차순 정렬
dic_sort3 = sorted(dic.items(), key=lambda x:x[1], reverse=True)
print(dic_sort3)    # [('b', 5), ('a', 4), ('c', 3), ('e', 1)]
```
→ `dic.items()`결과 : (key, value) 튜플쌍이 리스트에 담겨서 반환  
→ `sorted()`결과 : 리스트에 담겨서 반환

### 3. 문자열
(1) count(word) : word 갯수 세기  
(2) find(word) : word를 찾아 인덱스 반환 **(없을시 -1 출력)**  
(3) index(word) : word를 찾아 인덱스 반환 **(없을시 에러발생)**  
(4) upper(), lower(), capitalize() : 대소문자 변환  
(5) lstrip(), rstrip(), strip() : 공백제거  
(6) replace(a, b) : a를 b로 치환  
(7) split(deli) : 문자열을 deli로 기준으로 나누어 반환  

```python
a='hobby'
a.count('b')    # 2
a.find('b')     # 2
a.find('p')     # -1
a.index('p')    # 에러발생

a.replace('o', 'a')
print(a)        # habby
```

- 슬라이싱

|안|녕|하|세|요|
|:--:|:--:|:--:|:--:|:--:|
|0|1|2|3|4|
|-5|-4|-3|-2|-1|

```python
S = '안녕하세요'

S[1:3] == 녕하
S[1:-2] == 녕하
S[1:] == 녕하세요
S[:1] == 안
S[:-3] == 안녕
S[-3:] == 하세요

S[::2] == 안하요
S[::-1] == 요세하녕안
S[:] == 안녕하세요
```

### 4. 입출력
- 각 데이터를 공백으로 구분하여 입력
  - `map(함수, 데이터)` : 모든 데이터에 첫번째 파라미터로 들어온 함수 적용  

```python
data = list(map(int, input().split()))
data.sort()

65 90 75 34 99
print(data)
[34, 65, 75, 90, 99]
```

- 한 줄씩 입력 : 줄바꿈 기호를 제거하기 위해 `rstrip()` 사용

```python
import sys

data = sys.stdin.readline().rstrip()
```

- 정수와 배열이 한 줄에 입력시 : `*`를 사용하여 가변인자로 받기

```python
4 10 20 30 40
3 7 5 12

n, *arr = map(int, input().split())
```

- 포맷팅 (f-string)

```python
answer = 7
print(f"정답은 {answer}입니다.")
```

- 실행 종료

```python
import sys

sys.exit(0)
```

- 최대값, 최소값 설정

```python
import sys
max = sys.maxsize
min = -sys.maxsize
```

### 5. 라이브러리
#### (1) itertools : 반복, 순열/조합
```python
from itertools import permutations, combinations, product, combination_with_replacement

data = ['A', 'B', 'C']

# 1. 순열
per = list(permutations(data, 3))
print(per) # 6개
[('A', 'B', 'C'), ('A', 'C', 'B'), ('B', 'A', 'C'), ('B', 'C', 'A'), ('C', 'A', 'B'), ('C', 'B', 'A')]

# 2. 조합
com = list(combinations(data, 2))
print(com) # 3개
[('A', 'B'), ('A', 'C'), ('B', 'C')]

# 3. 중복순열
pro = list(product(data, repeat=2))
print(pro) # 9개
[('A', 'A'), ('A', 'B'), ('A', 'C'), ('B', 'A'), ('B', 'B'), ('B', 'C'), ('C', 'A'), ('C', 'B'), ('C', 'C')]

# 4. 중복조합
comr = list(combination_with_replacement(data, 2))
print(comr) # 6개
[('A', 'A'), ('A', 'B'), ('A', 'C'), ('B', 'B'), ('B', 'C'), ('C', 'C')]
```

#### (2) heapq : 힙(우선순위큐)
- 파이썬의 힙은 **최소힙**만 제공
  - 최소힙 : 낮은 숫자의 우선순위가 높음
  - 우선순위에 마이너스(-)를 붙여 최대힙으로 사용 가능
- heappush(리스트, 값)
- heappop(리스트)

```python
import heapq
heap = []

heapq.heappush(heap, 4)
heapq.heappush(heap, 9)
heapq.heappush(heap, 1)
heapq.heappush(heap, 7)

print(heap)     # [1, 4, 7, 9]

num = heapq.heappop(heap)
print(num)      # 1
print(heap)     # [4, 7, 9]
```

#### (3) ~~bisect : 이진탐색~~(직접구현추천)
- bisect_left(a, x) : 정렬된 순서를 유지하면서 리스트 a에 데이터 x를 삽입할 가장 왼쪽 인덱스를 찾음
- bisect_right(a, x) : 정렬된 순서를 유지하면서 리스트 a에 데이터 x를 삽입할 가장 오른쪽 인덱스를 찾음

```python
from bisect import bisect_left, bisect_right

a = [1, 2, 4, 4, 8]
x = 4

print(bisect_left(a, x))  # 2
print(bisect_right(a, x))  # 4
```

#### (4) collections : 데크, 카운터
1\. 데크
- popleft() : 첫번째 원소 제거
- pop() : 마지막 원소 제거
- appendleft(x) : 첫번째 인덱스에 삽입
- append(x) : 마지막 인덱스에 삽입

```python
from Colletions import deque

data = deque([2, 3, 4])
data.appendleft(1)
data.append(5)

print(data)
[1, 2, 3, 4, 5]
```

2\. 카운터

```python
form collections import Counter

counter = Counter(['red', 'blue', 'red', 'green', 'blue', 'blue'])

print(counter(['blue'])
3
print(dict(counter))
{'red': 2, 'blue': 3, 'green': 1}
```


#### (5) math
- factorial(a) : 펙토리얼
- sqrt(a) : 제곱근
- gcd(a, b) : 최대공약수

```python
import math

math.factorial(5)   # 120
math.sqrt(7)        # 2.6457513110645907
math.gcd(21, 14)    # 7
```

#### (6) zip (내장함수)
여러 iterable 객체를 인덱스에 따라 튜플로 생성해준다  
iterable 객체의 길이가 다를경우 짧은 인자 길이를 따르며 **더 긴부분은 버려진다**

```python
numbers = [1, 2, 3]
letters = ['A', 'B', 'C']

for pair in zip(numbers, letters):
    print(pair)
(1, 'A')
(2, 'B')
(3, 'C')
```

- 컬렉션으로 묶기

```python
pair_list = list(zip(numbers, letters))
print(pair_list)
[(1, 'A'), (2, 'B'), (3, 'C')]

pair_dict = dict(zip(numbers, letters))
print(pair_dict)
{1: 'A', 2: 'B', 3: 'C'}
```

- 컬렉션에서 분리하기
  
```python
numbers, letters = zip(*pair_list)
print(numbers)
(1, 2, 3)
print(letters)
('A', 'B', 'C')
```

### 6. 람다식

```python
def add(a, b):
    return a+b

print(add(3, 7))

# 람다식 적용
print((lambda a,b: a+b)(3,7))
```

- 람다식을 이용한 정렬 : 리스트의 두번째 인자로 정렬하기
  
```python
n = int(input())
time = []

for _ in range(n):
  time.append(list(map(int, input().split())))
time.sort(key=lambda x: (x[1], x[0]))     # 두번째 인자, 첫번째 인자 오름차순

print(time)

>>> 5
1 4
2 3
3 5
4 6
5 7

[[2, 3], [1, 4], [3, 5], [4, 6], [5, 7]]  # 두번째 인자 오름차순 우선
```