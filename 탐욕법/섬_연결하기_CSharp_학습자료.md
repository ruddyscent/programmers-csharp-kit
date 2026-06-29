# 🌉 섬 연결하기 — C# 학습자료

## 📌 1. 문제 핵심

여러 개의 섬이 있습니다.

섬과 섬 사이를 연결하는 다리의 건설 비용이 주어집니다.

예를 들어:

```text
0 --(1)-- 1
|          |
(2)      (1)
|          |
2 --(8)-- 3
```

목표는:

```text
모든 섬을 연결하되,
총 비용을 최소로 만들기
```

입니다. 🎯

---

# 🧠 이 문제의 정체

이 문제는 대표적인 **최소 신장 트리(MST, Minimum Spanning Tree)** 문제입니다.

최소 신장 트리란:

```text
모든 정점을 연결하면서
사이클은 만들지 않고
총 비용이 가장 작은 트리
```

를 말합니다.

---

# 🔍 예제 이해하기

```text
n = 4

costs =
[
 [0,1,1],
 [0,2,2],
 [1,2,5],
 [1,3,1],
 [2,3,8]
]
```

비용 기준으로 정렬하면:

```text
(0,1) : 1
(1,3) : 1
(0,2) : 2
(1,2) : 5
(2,3) : 8
```

입니다.

---

## 1단계

가장 싼 다리 선택

```text
0 - 1 (비용 1)
```

현재 비용:

```text
1
```

---

## 2단계

다음으로 싼 다리

```text
1 - 3 (비용 1)
```

현재 비용:

```text
2
```

---

## 3단계

다음으로 싼 다리

```text
0 - 2 (비용 2)
```

현재 비용:

```text
4
```

이제 모든 섬이 연결되었습니다.

정답:

```text
4
```

🎉

---

# ⚠️ 왜 1-2 비용 5는 선택하지 않을까?

현재 연결 상태:

```text
0 - 1
|   |
2   3
```

이미:

```text
1 → 0 → 2
```

로 이동할 수 있습니다.

따라서:

```text
1 - 2
```

를 추가하면 사이클이 생깁니다.

최소 신장 트리에서는 사이클이 생기면 안 됩니다. 🚫

---

# 🧩 필요한 개념: Union-Find

Kruskal 알고리즘에서는 다음을 반복합니다.

```text
가장 싼 다리부터 확인
사이클이 생기지 않으면 선택
사이클이 생기면 버림
```

사이클 여부를 빠르게 확인하기 위해 사용하는 자료구조가:

```text
Union-Find
Disjoint Set
```

입니다.

---

## Find

어떤 노드가 속한 집합의 대표를 찾습니다.

```text
Find(3)
```

예:

```text
0-1-3
```

이면:

```text
대표 = 0
```

---

## Union

두 집합을 합칩니다.

```text
Union(0, 2)
```

예:

```text
{0,1}
{2}
```

를:

```text
{0,1,2}
```

로 합칩니다.

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — Kruskal 알고리즘

## 💡 아이디어

1. 비용 기준으로 정렬한다.
2. 가장 싼 다리부터 본다.
3. 두 섬이 이미 연결되어 있으면 건너뛴다.
4. 연결되어 있지 않으면 다리를 선택한다.
5. 모든 섬이 연결될 때까지 반복한다.

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    private int[] parent;

    public int solution(int n, int[,] costs)
    {
        parent = new int[n];

        for (int i = 0; i < n; i++)
            parent[i] = i;

        int edgeCount = costs.GetLength(0);
        var edges = new (int a, int b, int cost)[edgeCount];

        for (int i = 0; i < edgeCount; i++)
            edges[i] = (costs[i, 0], costs[i, 1], costs[i, 2]);

        Array.Sort(edges, (a, b) => a.cost.CompareTo(b.cost));

        int answer = 0;

        foreach (var edge in edges)
        {
            if (Find(edge.a) == Find(edge.b))
                continue;

            Union(edge.a, edge.b);
            answer += edge.cost;
        }

        return answer;
    }

    private int Find(int x) =>
        parent[x] == x ? x : parent[x] = Find(parent[x]);

    private void Union(int a, int b) =>
        parent[Find(b)] = Find(a);
}
```

---

# 🔎 코드 해설

## 1. 부모 배열 만들기

```csharp
parent[i] = i;
```

처음에는 모든 섬이 자기 자신을 대표합니다.

예:

```text
0 → 0
1 → 1
2 → 2
3 → 3
```

---

## 2. 비용 기준 정렬

```csharp
Array.Sort(edges,
    (a, b) => a.cost.CompareTo(b.cost));
```

다리 비용이 작은 순서대로 정렬합니다.

---

## 3. 이미 연결되어 있는지 확인

```csharp
if (Find(edge.a) == Find(edge.b))
```

대표가 같으면 이미 같은 집합입니다.

다리를 추가하면 사이클이 생깁니다.

---

## 4. 연결

```csharp
Union(edge.a, edge.b);
```

두 집합을 합칩니다.

---

## 5. 비용 추가

```csharp
answer += edge.cost;
```

선택된 다리의 비용을 더합니다.

---

# ⏱️ 시간 복잡도

정렬 비용:

```text
O(E log E)
```

Union-Find:

```text
거의 O(1)
```

전체:

```text
O(E log E)
```

입니다.

여기서:

```text
E = 다리 개수
```

입니다.

---

# 📦 공간 복잡도

부모 배열:

```text
O(n)
```

간선 배열:

```text
O(E)
```

전체:

```text
O(n + E)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — LINQ + Kruskal

## 💡 아이디어

같은 Kruskal 알고리즘입니다.

다만 간선 정렬을 LINQ로 처리합니다.

---

## 💻 C# 코드

```csharp
using System.Linq;

public class Solution
{
    public int solution(int n, int[,] costs)
    {
        int[] parent = Enumerable.Range(0, n).ToArray();

        int Find(int x) => parent[x] == x ? x : parent[x] = Find(parent[x]);

        var edges = Enumerable.Range(0, costs.GetLength(0))
            .Select(i => (a: costs[i, 0], b: costs[i, 1], cost: costs[i, 2]))
            .OrderBy(edge => edge.cost);

        int answer = 0;

        foreach (var edge in edges)
        {
            if (Find(edge.a) == Find(edge.b))
                continue;

            parent[Find(edge.b)] = Find(edge.a);
            answer += edge.cost;
        }

        return answer;
    }
}
```

---

# ✨ 코드 의미

## LINQ 정렬

```csharp
.OrderBy(edge => edge.cost)
```

비용이 작은 다리부터 처리합니다.

---

## 부모 배열 초기화

```csharp
Enumerable.Range(0, n)
    .ToArray();
```

0 ~ n-1까지 생성합니다.

---

## 로컬 함수와 한 줄 연결

```csharp
int Find(int x) => ...
parent[Find(edge.b)] = Find(edge.a);
```

`Find`는 `solution` 안의 로컬 함수로 두고, 두 집합을 합치는 코드는 한 줄로 작성했습니다. 🚀

---

# ⚠️ Prim 알고리즘으로도 가능할까?

가능합니다.

최소 신장 트리는 보통 두 가지 방식으로 풉니다.

```text
Kruskal
Prim
```

하지만 이 문제는:

```text
간선 정보(costs)가 그대로 주어짐
```

이라서 Kruskal이 훨씬 자연스럽습니다.

프로그래머스에서도 대부분 Kruskal 풀이를 사용합니다. 👍

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 |
|---|---|---|---:|
| 풀이 1 | Kruskal + Union-Find | 이해하기 쉽다 😊 | O(E log E) |
| 풀이 2 | LINQ + Kruskal | 코드가 짧다 🚀 | O(E log E) |

---

# 🏆 추천 풀이

처음 공부할 때는:

```text
풀이 1
```

을 추천합니다.

이유:

```text
1. Union-Find를 배우기 좋음
2. Kruskal 흐름이 잘 보임
3. MST 대표 문제
```

코딩 테스트 제출용으로 코드 길이를 우선한다면:

```text
풀이 2
```

도 좋습니다.

---

# ✅ 최종 정리

이 문제는:

```text
최소 신장 트리(MST)
```

문제입니다.

가장 많이 사용하는 방법은:

```text
Kruskal 알고리즘
```

입니다.

흐름은:

```text
1. 비용이 작은 다리부터 정렬
2. 사이클이 생기지 않으면 선택
3. 비용 누적
4. 모든 섬이 연결되면 종료
```

입니다.

🌉 “섬 연결하기”는 Union-Find와 Kruskal 알고리즘을 배우기 위한 대표 문제입니다.
