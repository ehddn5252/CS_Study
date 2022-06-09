# Dijkstra Algorithm

## 2022.06.03

학습 목표: 다익스트라 알고리즘을 구현할 수 있고 설명할 수 있다.


음의 가중치가 없는 그래프의 한 정점에서 다른 모든 정점까지 가는 최소 거리를 구할 때 사용한다. 
맨 처음 고안한 알고리즘은 O(V^2)의 시간복잡도를 가지지만 Heapq를 사용하면 O((V+E)logV) 의 시간 복잡도를 가진다.

O((V+E)logV)의 시간복잡도를 가지는 이유는 각 노드마다 미방문 노드 중 출발점으로부터 현재까지 계산된 최단 거리를 가지는 노드를 찾는데 O(VlogV)O(VlogV)의 시간이 필요하고, 각 노드마다 이웃한 노드의 최단 거리를 갱신할 때 O(ElogV)O(ElogV)의 시간이 필요하기 때문이다.

### 알고리즘 동작 과정
시작 노드와 2차원 인접 행렬(adj)이 주어진다고 하자.
1. visit 배열, distance 배열을 만든다.
2. 시작 노드로부터 인접한 노드들 중 방문하지 않았고 거리가 가장 짧은 노드(current)를 선택하고 방문 표시를 한다.
3. 방문하지 않았고 인접배열에 연결되어 있는 모든 노드를 돌려보면서 dist배열을 최신화해준다. 
4. 모든 정점 2번과 3번과정을 모든 정점에 대해서 하면 dist 배열의 최신화가 끝난다.

### 알고리즘 동작 과정과 코드 
시작 노드와 인접행렬(adj[][])이 주어진다고 하자.
1. visit 배열, distance 배열을 만든다.
```python
visit = [ False for i in range(V)]
distance = [ INF for i in range(V)]
distance[start_node - 1] = 0
```
2. 시작 노드로부터 인접한 노드들 중 방문하지 않았고 거리가 가장 짧은 노드(current)를 선택하고 방문 표시를 한다.
```python
min = INF
    for j in range(V):
        # 주변 노드 중 최소 비용 노드를 찾는다.
        # 이부분을 heapq로 바꿀 수 있다
        if visit[j] == False and distance[j]<min:
            min = distance[j]
            current = j
```
3. 방문하지 않았고 인접배열에 연결되어 있는 모든 노드를 돌려보면서 dist배열을 최신화해준다. 
```python
for j in range(V):
    if not visit[j] and adj[current][j]!=0 and adj[current][j]+distance[current]<distance[j]:
        dist[j] = dist[current] + adj[current][j]
```

4. 모든 정점 2번과 3번과정을 모든 정점에 대해서 하면 dist 배열의 최신화가 끝난다.

전체 코드

```python
import sys

INF = 10 * 20000
pq=[]
V, E = map(int,sys.stdin.readline().split(" "))
start_node = int(sys.stdin.readline())
adj = [[0 for i in range(V)] for i in range(V)]
for _ in range(E):
    s, e, w = map(int, sys.stdin.readline().split(" "))
    adj[s - 1][e - 1] = w

visit = [ False for i in range(V)]
distance = [ INF for i in range(V)]
distance[start_node - 1] = 0
visit[start_node - 1] = True
current = 0
for i in range(V):

    min = INF
    for j in range(V):
        # 주변 노드 중 최소 비용 노드를 찾는다.
        # 이부분을 heapq로 바꿀 수 있다
        if visit[j] == False and distance[j]<min:
            min = distance[j]
            current = j

    visit[current] = True
    # 최소 비용 노드에서 다른 노드로 가는 비용을 넣는다.
    for j in range(V):
        if not visit[j] and adj[current][j]!=0 and adj[current][j]+distance[current]<distance[j]:
            distance[j] = adj[current][j] + distance[current]

for i in range(distance):
    if distance[i]==INF:
        print("INF")
    else:
        print(distance[i])

```



힙큐로 구현 예시 (백준 1753 최소경로 문제)
```python
import heapq
import sys

input = sys.stdin.readline
INF = sys.maxsize
# 정정과 간선
V, E = map(int, input().split())
# 시작 노드
K = int(input())

# 인접 행렬
edge = [[] for _ in range(V+1)]
# 거리가 저장된 배열
dist = [INF] * (V+1)

for i in range(E):
    u,v,w = map(int, input().split(" "))
    edge[u].append([w,v])

# 시작점 초기화
dist[K] = 0
# 시작점 시작점의 가중치는 0이다.
heap = [[0,K]]

# 힙큐가 비지 않을 때까지 반복한다.
while heap:
    # heapq 를 pop 해서 최소 가중치인 가중치와 vertex를 얻는다.
    ew, ev = heapq.heappop(heap)
    # 만약 현재 pop한 값의 가중치가 dist의 가중치와 다르면 바로 다음을 pop 해준다. (최신화 할 필요가 없다, 시간 줄이기 위함, 없어도 됨)
    # 이 부분이 헤깔림
    if dist[ev] != ew: continue
    # 인접 리스트를 확인한다.
    for nw,nv in edge[ev]:
        # 인접 리스트의 값+ 현재 노드의 가중치 값이 dist 배열에 저장된 값보다 작으면 dist 배열을 최신화 해주고 힙에 넣어 준다.
        if dist[nv] > ew + nw:
            dist[nv] = ew + nw
            heapq.heappush(heap, [dist[nv], nv])

for i in range(1,V+1):
    if dist[i] == INF : print("INF")
    else: print(dist[i])
```

참고 자료
- 다익스트라 알고리즘 나무위키(https://namu.wiki/w/%EB%8B%A4%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%9D%BC%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)