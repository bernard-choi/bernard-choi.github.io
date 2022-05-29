---  
layout: post  
title: "[Algorithm] union-find 알고리즘"  
subtitle: "[Algorithm] union-find 알고리즘"  
categories: Algorithm
tags: 알고리즘 Kruscal Union-find graph
comments: true  


---  
## Union-Find 알고리즘

Union-Find는 대표적인 그래프 알고리즘입니다. 바로 **합집합 찾기** 라는 의미를 가진 알고리즘입니다. 서로소 집합(Disjoint-Set)알고리즘이라고도 부릅니다. 구체적으로 여러개의 노드가 존재할 때 두개의 노드를 선택해서 현재 이 노드가 서로 같은 그래프에 속하는지 판별하는 알고리즘입니다.

---

## 동작방법

- 1) Union 연산 확인
  - 1.1) 서로 연결된 두 노드를 확인
  - 1.2) A의 루트 노드 A' 과 B의 루트 노드 B'를 찾기 (find)
  - 1.3) **두 노드의 루트 노드가 다르다면**, A'를 B'의 부모 노드로 설정(A' > B') -> 방향은 임의로 설정

- 2) 모든 union 연산을 처리할 때까지 1 반복

ex) `{1,2,3,4,5,6}`의 집합에서 (4,5), (5,6), (3,5), (1,3), (4,6), (2,4), (1,2), (3,4)를 싸이클이 생기는 경우를 제외하여 잇는다고하자. 이를 그래프로 표현하면

![union_find1](https://yunsikus.github.io/assets/img/post_img/union_find1.jpg)

다음과 같습니다. 이를 완성하기 위한 구체적인 알고리즘 동작 방법을 알아봅시다.

#### 1. 부모 테이블 초기화
노드의 개수 크기의 부모 테이블을 초기화합니다. 초기값은 자기 자신을 부모로 가지도록 설정합니다.

|노드번호|1|2|3|4|5|6|
|-|-|-|-|-|-|-|
|부모|1|2|3|4|5|6|

#### 2. 각각의 union 연산을 확인한다. -> Union (4,5)
4와 5의 루트노드를 각각 찾습니다. 현재 루트 노드는 각각 4와5로 다릅니다. 따라서 A'인 5의 부모를 B'인 4로 설정
|노드번호|1|2|3|4|5|6|
|-|-|-|-|-|-|-|
|부모|1|2|3|4|4|6|

![union_find2](https://yunsikus.github.io/assets/img/post_img/union_find2.jpg)

#### 3. Union (5,6)
5의 루트노드는 4, 6의 루트노드는 6입니다. A'인 6의 부모 노드를 B'인 4로 설정합니다.

|노드번호|1|2|3|4|5|6|
|-|-|-|-|-|-|-|
|부모|1|2|3|4|4|4|

![union_find3](https://yunsikus.github.io/assets/img/post_img/union_find3.jpg)

#### 4. Union (3,5)
3의 루트노드느 3, 5의 루트노드는 4입니다. A' 4의 부모노드를 B'인 3으로 설정합니다.

|노드번호|1|2|3|4|5|6|7|
|-|-|-|-|-|-|-|-|
|부모|1|2|3|3|4|4|7|

![union_find4](https://yunsikus.github.io/assets/img/post_img/union_find4.jpg)

#### 5. Union (1,3)

1의 루트노드는 1, 3의 루트노드는 3이니다. A'인 3의 부모노드를 B'인 1로 설정합니다.

|노드번호|1|2|3|4|5|6|7|
|-|-|-|-|-|-|-|-|
|부모|1|2|1|3|4|4|7|

![union_find5](https://yunsikus.github.io/assets/img/post_img/union_find5.jpg)

#### 6. Union (4,6)

4의 루트노드는 1, 6의 루트노드는 1입니다. 서로 같은 그래프에 포함되므로(싸이클이 생기는 경우) Union을 수행하지 않습니다.

![union_find6](https://yunsikus.github.io/assets/img/post_img/union_find6.jpg)


#### 7. Union (2,4)
2의 루트노드는 2, 4의 루트노드는 1입니가. A'인 1의 부모노드를 B'인 2로 설정합니다.

|노드번호|1|2|3|4|5|6|7|
|-|-|-|-|-|-|-|-|
|부모|2|2|1|3|4|4|7|

![union_find7](https://yunsikus.github.io/assets/img/post_img/union_find7.jpg)

#### 8. Union (1,2)
1의 루트노드는 2, 2의 루트노드는 2로 Union을 수행하지 않습니다.

#### 9. Union (3,4)
3의 루트노드는 1, 4의 루트노드는 1로 Union을 수행하지 않습니다.

---

## 파이썬 코드

```python
graph = [(4,5), (5,6), (3,5), (1,3), (4,6), (2,4), (1,2), (3,4)]

final_graph = []
n = 6 # 정점 개수
p = [0] # 상호배타적 집합
tree_edges = 0 # 간선개수

for i in range(1, n+1):
    p.append(i)

def find(u): ## u 정점의 루트 노드 탐색
    if u != p[u]: ## u가 루트 노드가 아니면
        p[u] = find(p[u]) # 3 -> 2, 2 -> 1 일 경우 3 -> 1 로 경로 압축
    return p[u] ## u가 루트 노트이면 u 반환

def union(u, v):
    root1 = find(u) # B'
    root2 = find(v)  # A'
    p[root2] = root1 # A'의 부모느드를 B'로 변경

while True:
    if tree_edges == n-1:
        break
    u,v= graph.pop(0)
    if find(u) != find(v): # 두 노드의 루트 노드가 다르다면 두 노드가 포함되어 있는 집합을 하나로 결합
        union(u,v)
        final_graph.append((u,v))
        tree_edges += 1

# [(4,5), (5,6), (3,5), (1,3), (2,4)]
print(final_graph)
```
---

## 알고리즘을 활용하는 문제

- [백준 1976: 여행가자](https://yunsikus.github.io/algorithm/2021/06/12/1976_%EC%97%AC%ED%96%89%EA%B0%80%EC%9E%90/)

## Reference

- [https://it-garden.tistory.com/411](https://it-garden.tistory.com/411)
- [https://deep-learning-study.tistory.com/589](https://deep-learning-study.tistory.com/589)
- [https://velog.io/@woo0_hooo/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-union-find-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98](https://velog.io/@woo0_hooo/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-union-find-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)
- [https://www.youtube.com/watch?v=AMByrd53PHM](https://www.youtube.com/watch?v=AMByrd53PHM)
