# ⚡ 전력망을 둘로 나누기 — C# 학습자료

## 📌 1. 문제 핵심

`n`개의 송전탑이 전선으로 연결되어 있습니다.

이 전력망은 **트리 형태**입니다.

트리란 다음 조건을 만족하는 구조입니다.

```text
1. 모든 노드가 연결되어 있다.
2. 사이클이 없다.
3. n개의 노드가 있으면 간선은 n - 1개다.
```

문제에서는 전선 하나를 끊어서 전력망을 정확히 두 개로 나누려고 합니다.

목표는 다음입니다.

```text
두 전력망의 송전탑 개수 차이를 최소로 만들기
```

즉, 가능한 한 비슷한 크기로 나누는 것이 목표입니다. 🎯

---

# 🧠 핵심 아이디어

트리에서 간선 하나를 끊으면 반드시 두 개의 덩어리로 나뉩니다.

예를 들어 전체 송전탑 수가 `n = 9`이고, 전선 하나를 끊었더니 한쪽 전력망에 송전탑이 3개 있다고 해봅시다.

그러면 다른 쪽은:

```text
9 - 3 = 6개
```

입니다.

두 전력망의 차이는:

```text
|6 - 3| = 3
```

입니다.

즉, 한쪽 송전탑 개수만 구하면 됩니다. ✅

---

## 🔑 차이 계산 공식

한쪽 전력망의 송전탑 개수를 `count`라고 하면:

```text
다른 쪽 송전탑 개수 = n - count
```

두 전력망의 차이는:

```text
|count - (n - count)|
```

정리하면:

```text
|n - 2 × count|
```

입니다.

C# 코드로는:

```csharp
Math.Abs(n - 2 * count)
```

입니다. ⚡

---

# 🔍 예제 1 이해하기

```text
n = 9
wires = [[1,3],[2,3],[3,4],[4,5],[4,6],[4,7],[7,8],[7,9]]
```

예를 들어 `[4, 7]` 전선을 끊으면 두 전력망은 다음처럼 나뉩니다.

```text
한쪽: 7, 8, 9 → 3개
다른 쪽: 1, 2, 3, 4, 5, 6 → 6개
```

차이는:

```text
|6 - 3| = 3
```

입니다.

이보다 더 작은 차이를 만들 수 없으므로 정답은:

```text
3
```

입니다. ✅

---

# 🔍 예제 2 이해하기

```text
n = 4
wires = [[1,2],[2,3],[3,4]]
```

`[2, 3]` 전선을 끊으면:

```text
한쪽: 1, 2 → 2개
다른 쪽: 3, 4 → 2개
```

차이는:

```text
|2 - 2| = 0
```

정답은:

```text
0
```

🎉

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 전선을 하나씩 끊고 BFS

## 💡 아이디어

1. 끊을 전선을 하나 선택합니다.
2. 그 전선을 제외하고 그래프를 만듭니다.
3. 1번 송전탑에서 BFS를 시작해 연결된 송전탑 개수를 셉니다.
4. 나머지 송전탑 수와의 차이를 계산합니다.
5. 모든 전선에 대해 반복하며 최솟값을 구합니다.

`n <= 100`이라 이 방식으로도 충분히 빠릅니다. 😊

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int solution(int n, int[,] wires)
    {
        int answer = n;

        for (int cut = 0; cut < wires.GetLength(0); cut++)
        {
            List<int>[] graph = new List<int>[n + 1];

            for (int i = 1; i <= n; i++)
            {
                graph[i] = new List<int>();
            }

            for (int i = 0; i < wires.GetLength(0); i++)
            {
                if (i == cut)
                {
                    continue;
                }

                int a = wires[i, 0];
                int b = wires[i, 1];

                graph[a].Add(b);
                graph[b].Add(a);
            }

            int count = CountConnectedNodes(1, graph, n);
            int difference = Math.Abs(n - 2 * count);

            answer = Math.Min(answer, difference);
        }

        return answer;
    }

    private int CountConnectedNodes(int start, List<int>[] graph, int n)
    {
        bool[] visited = new bool[n + 1];
        Queue<int> queue = new Queue<int>();

        visited[start] = true;
        queue.Enqueue(start);

        int count = 0;

        while (queue.Count > 0)
        {
            int current = queue.Dequeue();
            count++;

            foreach (int next in graph[current])
            {
                if (visited[next])
                {
                    continue;
                }

                visited[next] = true;
                queue.Enqueue(next);
            }
        }

        return count;
    }
}
```

---

## 🔎 코드 해설

### 1. 끊을 전선을 하나씩 선택

```csharp
for (int cut = 0; cut < wires.GetLength(0); cut++)
```

`cut`번째 전선을 끊는다고 가정하고 시뮬레이션합니다.

---

### 2. 그래프 초기화

```csharp
List<int>[] graph = new List<int>[n + 1];

for (int i = 1; i <= n; i++)
{
    graph[i] = new List<int>();
}
```

송전탑 번호가 1부터 시작하므로 배열 크기를 `n + 1`로 만듭니다.

---

### 3. 끊은 전선을 제외하고 연결

```csharp
if (i == cut)
{
    continue;
}
```

현재 끊기로 한 전선은 그래프에 넣지 않습니다.

나머지 전선은 양방향으로 연결합니다.

```csharp
graph[a].Add(b);
graph[b].Add(a);
```

---

### 4. BFS로 한쪽 송전탑 개수 세기

```csharp
int count = CountConnectedNodes(1, graph, n);
```

1번 송전탑에서 갈 수 있는 송전탑 개수를 셉니다.

전선 하나를 끊었기 때문에 전체 전력망은 두 덩어리로 나뉘어 있습니다.

1번 송전탑이 포함된 한쪽 덩어리의 크기가 `count`입니다.

---

### 5. 차이 계산

```csharp
int difference = Math.Abs(n - 2 * count);
```

한쪽이 `count`개라면 다른 쪽은 `n - count`개입니다.

차이는:

```text
|count - (n - count)| = |n - 2 × count|
```

입니다.

---

## ⏱️ 시간 복잡도

전선은 `n - 1`개입니다.

각 전선을 하나씩 끊고, 매번 그래프를 만들고 BFS를 수행합니다.

```text
O(n²)
```

입니다.

`n <= 100`이므로 충분히 빠릅니다. ✅

---

## 📦 공간 복잡도

그래프와 방문 배열, 큐를 사용합니다.

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — 그래프를 한 번 만들고 끊은 간선만 무시하기

## 💡 아이디어

풀이 1은 전선을 끊을 때마다 그래프를 새로 만듭니다.

더 짧게 쓰려면 그래프는 한 번만 만들고, DFS/BFS 중에 끊은 전선만 지나가지 않도록 처리할 수 있습니다.

즉, DFS에서 다음 간선이 끊은 간선이면 건너뜁니다.

```text
cutA - cutB 간선은 이동하지 않기
```

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int solution(int n, int[,] wires)
    {
        List<int>[] graph = new List<int>[n + 1];

        for (int i = 1; i <= n; i++)
        {
            graph[i] = new List<int>();
        }

        for (int i = 0; i < wires.GetLength(0); i++)
        {
            int a = wires[i, 0];
            int b = wires[i, 1];

            graph[a].Add(b);
            graph[b].Add(a);
        }

        int answer = n;

        for (int i = 0; i < wires.GetLength(0); i++)
        {
            int cutA = wires[i, 0];
            int cutB = wires[i, 1];

            bool[] visited = new bool[n + 1];

            int count = Dfs(1, cutA, cutB, graph, visited);
            int difference = Math.Abs(n - 2 * count);

            answer = Math.Min(answer, difference);
        }

        return answer;
    }

    private int Dfs(int current, int cutA, int cutB, List<int>[] graph, bool[] visited)
    {
        visited[current] = true;

        int count = 1;

        foreach (int next in graph[current])
        {
            bool isCutEdge =
                (current == cutA && next == cutB) ||
                (current == cutB && next == cutA);

            if (isCutEdge || visited[next])
            {
                continue;
            }

            count += Dfs(next, cutA, cutB, graph, visited);
        }

        return count;
    }
}
```

---

## ✨ 코드 의미

### 1. 그래프는 한 번만 만든다

```csharp
graph[a].Add(b);
graph[b].Add(a);
```

모든 전선을 그래프에 넣습니다.

---

### 2. 끊을 전선을 정한다

```csharp
int cutA = wires[i, 0];
int cutB = wires[i, 1];
```

이번에 끊는 전선입니다.

---

### 3. DFS 중 끊은 전선은 무시한다

```csharp
bool isCutEdge =
    (current == cutA && next == cutB) ||
    (current == cutB && next == cutA);
```

그래프는 양방향이므로 두 방향 모두 확인해야 합니다.

끊은 전선이면 이동하지 않습니다.

```csharp
if (isCutEdge || visited[next])
{
    continue;
}
```

---

### 4. 연결된 송전탑 개수 반환

```csharp
count += Dfs(next, cutA, cutB, graph, visited);
```

DFS로 갈 수 있는 송전탑 개수를 모두 더합니다.

---

## ⏱️ 시간 복잡도

각 전선을 하나씩 끊어 보고, 매번 DFS를 수행합니다.

```text
O(n²)
```

입니다.

---

## 📦 공간 복잡도

그래프와 방문 배열, 재귀 호출 스택을 사용합니다.

```text
O(n)
```

입니다.

---

# ✨ LINQ를 살짝 쓴 참고 풀이

그래프 초기화를 LINQ로 조금 줄일 수 있습니다.

```csharp
List<int>[] graph = Enumerable.Range(0, n + 1)
    .Select(_ => new List<int>())
    .ToArray();
```

다만 이 문제는 그래프 탐색이 핵심이므로, LINQ를 많이 쓰기보다는 DFS/BFS 흐름을 명확히 쓰는 편이 좋습니다. 🌱

LINQ를 사용하려면 다음이 필요합니다.

```csharp
using System.Linq;
```

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 전선마다 그래프 재생성 + BFS | 이해하기 쉽다 😊 | O(n²) | O(n) |
| 풀이 2 | 그래프 1번 생성 + DFS에서 간선 무시 | 코드가 더 효율적이고 깔끔하다 🚀 | O(n²) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 전선을 실제로 끊는 느낌이 코드에 잘 드러납니다.
2. BFS로 한쪽 전력망 크기를 세는 과정이 직관적입니다.
3. 트리 문제에 익숙하지 않아도 이해하기 쉽습니다.
```

프로그래머스 제출용으로는 **풀이 2번**도 좋습니다.

그래프를 매번 다시 만들지 않아 더 깔끔합니다. ⚡

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
트리에서 간선 하나를 끊으면 두 개의 연결 요소로 나뉜다.
```

풀이 흐름은 다음과 같습니다.

```text
1. 전선을 하나 선택해 끊는다.
2. 한쪽 전력망의 송전탑 개수를 DFS/BFS로 센다.
3. 다른 쪽은 n - count개다.
4. 차이 |n - 2 × count|를 계산한다.
5. 모든 전선에 대해 최솟값을 찾는다.
```

⚡ “전력망을 둘로 나누기”는 트리, 그래프 탐색, 간선 제거를 연습하기 좋은 문제입니다.
