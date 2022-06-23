# **신장트리 (Spanning Tree)란 ??**

그래프 내의 모든 정점을 연결하고, 사이클이 없는 그래프를 의미한다.

n개의 정점으로 이루어진 무향그래프에서 n개의 정점과 n-1개의 간선으로 이루어진 트리이다.

<img width="450" alt="Untitled" src="https://user-images.githubusercontent.com/87989933/175049942-7e70384c-f1a5-4981-871c-8063040d662f.png">

# 최소 신장 트리 (MST , Minimum Spanning Tree)

신장트리를 구성하는 간선들의 가중치의 합이 최소가 되는 트리를 의미한다.

### **그래프 표현 방법**

1. 정점 중심 - 인접행렬 , 인접리스트
2. 간선 중심 - 간선리스트(from,to, weight를 하나의 객체로 저장)

# 📌 크루스칼(KRUSKAL) 알고리즘

간선을 하나씩 선택해서 MST를 찾는 알고리즘이다.

1. 최초, 모든 간선을 가중치에 따라 **오름차순으로 정렬**
2. 가중치가 가장 낮은 간선부터 선택하면서 트리를 증가시킨다
   이때, 사이클이 발생하면 포함하지 않고, 다음으로 가중치가 낮은 간선 선택
   **union**으로 신장트리에 넣으면서 head를 바꾸어 하나의 집합으로 합쳐준다.
   → head가 같으면 사이클이 발생하는것을 boolean으로 리턴하여 확인할 수 있다.
3. n-1개의 간선이 선택될때까지 반복
   → **union 횟수가 n-1** 이 되면 최소신장트리가 만들어진다.

![Untitled (1)](https://user-images.githubusercontent.com/87989933/175049907-e22bc169-bfe8-48bb-8fe1-971ef71373f2.png)

**Kruskal 알고리즘은 간선중심이다!!**

1. 간선리스트를 생성한다.
2. 가중치를 오름차순으로 정렬한다. (primitive type이 아니므로 comparator로 정렬)

### Kruskal 코드

```java
public class KruskalTest {

    static class Edge implements Comparable<Edge> {
        int from,to,weight;

        public Edge(int from,int to, int weight) {
            super();
            this.from = from;
            this.to = to;
            this.weight = weight;
        }

        @Override
        public int compareTo(Edge o) {
            return this.weight - o.weight;
        }
    }

    static int N;
    static int[] parents;
    static Edge[] edgeList;

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

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        int E = Integer.parseInt(st.nextToken());
        edgeList = new Edge[E];

        // 간선리스트 생성
        for(int i = 0; i < E; i++) {
            st = new StringTokenizer(br.readLine());
            int from = Integer.parseInt(st.nextToken());
            int to = Integer.parseInt(st.nextToken());
            int weight = Integer.parseInt(st.nextToken());
            edgeList[i] = new Edge(from , to, weight);
        }

        Arrays.sort(edgeList); // 간선비용 오름차순 정렬
        makeSet();

        int result = 0;
        int count = 0;

        for(Edge edge :edgeList) {
            if(union(edge.from, edge.to)) { // 사이클인지 확인
                count++;
                result += edge.weight;

                if(count == N-1) break;
            }
        }

        System.out.println(result);
    }
}
```

# 📌 프림 (PRIM) 알고리즘

하나의 정점에서 연결된 간선들 중에 하나씩 선택하면서 MST를 만들어가는 방식 (정점 중심으로 해결)

1. 임의 정점 하나를 선택해서 시작한다.
2. 선택한 정점과 인접한 정점들의 최소 비용의 간선이 존재하는 정점을 선택한다.
3. 모든 정점이 선택될때까지 반복한다.

시작점이 달라져도 결과는 동일하다!!

![Untitled (2)](https://user-images.githubusercontent.com/87989933/175049930-af8f42c2-16cd-4c21-a51a-ff51cbced973.png)

### Prim 코드

```java
public class PrimTest {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int N = Integer.parseInt(br.readLine());
        StringTokenizer st;

        int[][] adjMatrix = new int[N][N];
        int[] minEdge = new int[N]; // 다른 정점에서 자신(i)으로 연결하는 간선비용 중 최소값을 저장하는 배열 minEdge
        boolean[] visited = new boolean[N]; // 신장트리에 포함되었는지 확인하는 visited

        for (int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            for (int j = 0; j < N; j++) {
                adjMatrix[i][j] = Integer.parseInt(st.nextToken());
            }
            minEdge[i] = Integer.MAX_VALUE; // 큰값으로 최소값 초기화
        }
        int result = 0; // MST 비용
        minEdge[0] = 0; // 0번부터 시작한다는 의미로 본인을 선택하므로 0

        for (int c = 0; c < N; c++) { // N개의 정점을 모두 연결
            //신장트리에 연결되지 않은 정점중 가장 유리한 비용의 정점을 선택

            int min = Integer.MAX_VALUE;
            int minVertex = 0; // 최소값을 가지는 정점이 누구인지 기억

            for (int i = 0; i < N; i++) {
                if (!visited[i] && min > minEdge[i]) { // 가장 최소비용을 가진 정점을 찾아간다.
                    min = minEdge[i];
                    minVertex = i;
                }
            }

            // 선택된 정점을 신장트리에 포함시킴
            visited[minVertex] = true;
            result += min;

            // 선택된 정점 기준으로 신장트리에 포함되지 않은 다른 정점으로의 간선비용을 따져보고 최소값 갱신
            for (int i = 0; i < N; i++) {
                if (!visited[i] && adjMatrix[minVertex][i] != 0 && minEdge[i] > adjMatrix[minVertex][i]) {
                    // 아직 신장트리에 선택되지않고, 서로 인접해있고, i정점에서 다른정점들이 손을뻗을때의 간선비용보다 크다면
                    minEdge[i] = adjMatrix[minVertex][i];
                }
            }

            System.out.println(result);
        }
    }
}
```

- PrioritQueue를 활용하여 최소비용 정점을 찾을 수 있다.

# ❗️ Kruskal vs Prim

- 크루스칼은 간선위주의 알고리즘이고, 프림은 정점 위주의 알고리즘이다.
- 크루스칼은 간선수가 많아지면 간선리스트를 정렬하는데 오래걸린다.
- 간선의 개수가 적다면 크루스칼, 간선의 개수가 많으면 프림 알고리즘을 선택하자
