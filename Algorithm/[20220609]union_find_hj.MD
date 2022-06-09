## Disjoint Set

서로 중복되지 않는 부분 집합들 로 나눠진 원소들에 대한 정보를 저장하고 조작하는 자료구조

즉, 공통 원소가 없는, 즉 **“상호 배타적”** 인 부분 집합들로 나눠진 원소들에 대한 자료구조이다.

**Disjoint Set = 서로소 집합 자료구조**

<br/>

## Union-Find

Disjoint Set을 표현할 때 사용하는 대표적인 그래프 알고리즘이다. (서로소 집합 알고리즘)

여러개의 노드중에 두 노드가 같은 그래프에 속하는지 판별한다.

집합을 구현하는 데는 비트 벡터, 배열, 연결 리스트를 이용할 수 있으나 그 중 가장 효율적인 트리 구조를 이용하여 구현한다.

<br/>

## Union-Find 연산

- **make-set(x)**

x를 유일한 원소로 하는 새로운 집합을 만든다.

- **union(x, y)**

x가 속한 집합과 y가 속한 집합을 합친다. 즉, x와 y가 속한 두 집합을 합치는 연산

- **find(x)**

x가 속한 집합의 대표값(루트 노드 값)을 반환한다. 즉, x가 어떤 집합에 속해 있는지 찾는 연산

![union_find](https://user-images.githubusercontent.com/87989933/172767280-e8533f4f-63ff-4a8a-a65b-601e206a1bb4.png)

평균적으로 트리의 높이만큼 탐색하게되므로 시간복잡도는 **O(log N)** 이다.

<br/>

## 최악의 상황

- **트리 구조가 완전 비대칭인 경우**
- 연결리스트 형태

트리의 높이가 최대가 되므로, 시간복잡도는 **_O_(N)**이 된다.

배열로 구현하는것보다 비효율적이다.

<br/>

## 소스코드

```java
// 단위집합 생성
    public static void makeSet() {
        parents = new int[N];
        // 자신의 부모노드를 자신의 값으로 세팅
        for(int i = 0; i < N; i++) {
            parents[i] = i;
        }
    }

    // a의 집합 찾기 : a의 대표자 찾기
    public static int findSet(int a) {
        if(a == parents[a]) return a; // 자신이 부모와 같다 = 자신이 루트이다.

        return parents[a] = findSet(parents[a]); // path compression 루트노드의 번호를 재귀로 찾아와서 나의 부모로 대체한다.

    }

    // a,b 두 집합 합치기
    public static boolean union(int a, int b) {
        int aRoot = findSet(a); // 두집합의 대표자 찾기
        int bRoot = findSet(b);

        if(aRoot == bRoot) return false; // 둘이 같은 집합이면 합칠 수 없다.

        parents[bRoot] = aRoot; // a밑에 b붙이기
        return true;
    }
```
