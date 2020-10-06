---
title: "Skills for short coding"
categories: 
  - ALGORITHM
tags:
  - python
  - algorithm
toc: true
---

## 1. 입력 시간 단축 

```python
from sys import stdin

# 기존 코드
N, M, V = map(int, input().split())

# 새로운 코드
N, M, V = map(int, stdin.readline().split())
```

<Br><br>

## 2. 공백 생성

띄어쓰기가 되지 않은 값을 입력받는 경우 split()가 적용되지 않기 때문에, 이를 구분해서 저장하는 것에 애를 먹는 경우가 있다. 그 때는 다음의 코드를 사용한다. 

```python
from sys import stdin

# 기존 코드
# 새로운 코드 
stdin.readline().rstrip()
```

<br><br>

## 3. Deque 자료구조 사용

파이썬에는 사실상 que 자료구조가 없고 리스트를 que 처럼 사용하는 상태이다. 그 단점은 복수 스레드 사용으로 시간이 오래걸린다는 것! 그러나 collections에서 deque 자료구조는 제공해주기 때문에 이제는 list를 사용하지 말고 deque를 받아 que처럼 사용하자.

```python
from collections import deque

# 기존 코드
que = []

# 새로운 코드
deq = collections.deque()    
```

<br><br>

## 4. PyPy3

Python이랑 동일한 문법과 Python과 온전히(?) 동일하지만, 내부적으로 다르게 설계되어있어 시간이 빠름! 혹시 시간초과로 인한 문제를 겪고 있다면 한 번  PyPy3 언어로 제출해보자.

<br><br>

## 5. Dictionary 자료구조 사용 

key-value 쌍으로 구현된 dictionary 자료구조를 잘 활용해본다. 백준 17825번 윷놀이 문제의 경우 상당히 도움이 될 것이며, 배열을 사용한 부분은 다 대체 사용이 가능하다. 단, 메모리 초과에 대해 범위 설정이 필수적이다. 

```python
#예를 들어서 10개의 지점을 방문했는지 체크하기 위해서 visited 방문정보를 저장하는 경우

# 기존 코드
visited = [0 for _ in range(10)]if visited[x] == 0:    
    visited[x] = 1    
    
# 새로운 코드
visited = {}if not 0 <= nextX <= 100000:    
    continueif not nextX in visited:    
        visited[x] = 1
```