# 🌐 가장 먼 노드 — C# 학습자료

## 📌 1. 문제 핵심

1번부터 `n`번까지 번호가 붙은 노드가 있습니다.

간선은 양방향입니다.

우리가 구해야 하는 것은 다음입니다.

```text
1번 노드에서 최단거리로 갔을 때 가장 멀리 있는 노드의 개수
```

입니다. 🎯

여기서 거리는 지나가는 간선의 개수입니다.

예를 들어 1번 노드에서 어떤 노드까지 간선 2개를 지나야 한다면 그 노드의 거리는 `2`입니다.

---

# 🔍 예제 이해하기

```text
n = 6
edge = [
  [3, 6],
  [4, 3],
  [3, 2],
  [1, 3],
  [1, 2],
  [2, 4],
  [5, 2]
]
```

1번 노드에서 가까운 순서로 보면 다음과 같습니다.

```text
거리 0: 1
거리 1: 2, 3
거리 2: 4, 5, 6
```

가장 먼 거리는 `2`입니다.

그 거리에 있는 노드는 `4`, `5`, `6`입니다.

따라서 정답은:

```text
3
```

입니다. ✅

---

# 🧠 핵심 아이디어: BFS로 최단거리 구하기

간선의 가중치가 모두 같습니다.

즉, 한 번 이동할 때마다 거리는 항상 1씩 증가합니다.

이런 그래프에서 한 시작점으로부터의 최단거리는 **BFS**로 구할 수 있습니다. 🚀

BFS는 가까운 노드부터 차례대로 방문합니다.

```text
1번 노드
→ 1번에서 1칸 떨어진 노드들
→ 1번에서 2칸 떨어진 노드들
→ 1번에서 3칸 떨어진 노드들
...
```

그래서 처음 방문한 순간의 거리가 그 노드까지의 최단거리입니다.

---

## ⚠️ 주의할 점

문제 설명에서는 간선 배열을 `vertex`라고 부르기도 하지만, 프로그래머스 C# 제출에서는 보통 다음 형태로 작성합니다.

```csharp
public int solution(int n, int[,] edge)
```

여기서 `edge[i, 0]`과 `edge[i, 1]`은 서로 연결된 두 노드입니다.

그래프가 양방향이므로 양쪽에 모두 추가해야 합니다.

```text
a → b
b → a
```

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 거리 배열로 BFS

## 💡 아이디어

1. 인접 리스트로 그래프를 만듭니다.
2. `distance[node]`에 1번 노드로부터의 최단거리를 저장합니다.
3. 아직 방문하지 않은 노드는 `-1`로 표시합니다.
4. 1번 노드부터 BFS를 시작합니다.
5. BFS가 끝나면 가장 큰 거리와 그 거리의 노드 개수를 셉니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int n, int[,] edge)
    {
        var graph = new List<int>[n + 1];

        for (int i = 1; i <= n; i++)
            graph[i] = new List<int>();

        for (int i = 0; i < edge.GetLength(0); i++)
        {
            int a = edge[i, 0];
            int b = edge[i, 1];

            graph[a].Add(b);
            graph[b].Add(a);
        }

        int[] distance = new int[n + 1];

        for (int i = 1; i <= n; i++)
            distance[i] = -1;

        var queue = new Queue<int>();
        queue.Enqueue(1);
        distance[1] = 0;

        while (queue.Count > 0)
        {
            int now = queue.Dequeue();

            foreach (int next in graph[now])
            {
                if (distance[next] != -1)
                    continue;

                distance[next] = distance[now] + 1;
                queue.Enqueue(next);
            }
        }

        int maxDistance = 0;
        int answer = 0;

        for (int node = 1; node <= n; node++)
        {
            if (distance[node] > maxDistance)
            {
                maxDistance = distance[node];
                answer = 1;
            }
            else if (distance[node] == maxDistance)
            {
                answer++;
            }
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. 인접 리스트 만들기

```csharp
var graph = new List<int>[n + 1];

for (int i = 1; i <= n; i++)
    graph[i] = new List<int>();
```

노드 번호가 1부터 시작하므로 배열 크기를 `n + 1`로 만듭니다.

`graph[1]`에는 1번 노드와 연결된 노드들이 들어갑니다.

---

### 2. 양방향 간선 추가

```csharp
graph[a].Add(b);
graph[b].Add(a);
```

간선은 양방향입니다.

따라서 `a`에서 `b`로 갈 수 있고, `b`에서 `a`로도 갈 수 있습니다. 🔁

---

### 3. 거리 배열 초기화

```csharp
for (int i = 1; i <= n; i++)
    distance[i] = -1;
```

`-1`은 아직 방문하지 않았다는 뜻입니다.

1번 노드는 시작점이므로 거리를 0으로 둡니다.

```csharp
distance[1] = 0;
```

---

### 4. BFS 탐색

```csharp
while (queue.Count > 0)
{
    int now = queue.Dequeue();

    foreach (int next in graph[now])
    {
        if (distance[next] != -1)
            continue;

        distance[next] = distance[now] + 1;
        queue.Enqueue(next);
    }
}
```

현재 노드와 연결된 노드들을 확인합니다.

아직 방문하지 않은 노드라면 현재 거리보다 1 큰 값을 저장합니다.

BFS에서는 처음 방문한 거리가 최단거리입니다. ✅

---

### 5. 가장 먼 거리의 노드 개수 세기

```csharp
if (distance[node] > maxDistance)
{
    maxDistance = distance[node];
    answer = 1;
}
else if (distance[node] == maxDistance)
{
    answer++;
}
```

더 먼 거리가 나오면 최댓값을 갱신하고 개수를 1로 다시 시작합니다.

같은 최댓거리의 노드가 나오면 개수를 증가시킵니다.

---

## ⏱️ 시간 복잡도

노드 수를 `V`, 간선 수를 `E`라고 하겠습니다.

그래프를 만드는 데 모든 간선을 한 번 봅니다.

```text
O(E)
```

BFS는 각 노드와 간선을 한 번씩 확인합니다.

```text
O(V + E)
```

따라서 전체 시간 복잡도는:

```text
O(V + E)
```

입니다.

---

## 📦 공간 복잡도

인접 리스트, 거리 배열, 큐를 사용합니다.

```text
O(V + E)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — BFS 중 답 갱신하기

## 💡 아이디어

풀이 1과 같은 BFS입니다.

다만 거리 배열을 `0`이면 미방문, `1` 이상이면 방문으로 사용합니다.

1번 노드의 거리를 `1`로 시작하면 `-1` 초기화 반복문을 줄일 수 있습니다.

그리고 새 노드를 방문할 때마다 가장 먼 거리와 개수를 바로 갱신합니다. ⚡

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int n, int[,] edge)
    {
        var graph = new List<int>[n + 1];

        for (int i = 1; i <= n; i++)
            graph[i] = new List<int>();

        for (int i = 0; i < edge.GetLength(0); i++)
        {
            int a = edge[i, 0], b = edge[i, 1];
            graph[a].Add(b);
            graph[b].Add(a);
        }

        int[] dist = new int[n + 1];
        var q = new Queue<int>();

        q.Enqueue(1);
        dist[1] = 1;

        int max = 1, answer = 1;

        while (q.Count > 0)
        {
            int now = q.Dequeue();

            foreach (int next in graph[now])
            {
                if (dist[next] > 0)
                    continue;

                dist[next] = dist[now] + 1;
                q.Enqueue(next);

                if (dist[next] > max)
                {
                    max = dist[next];
                    answer = 1;
                }
                else if (dist[next] == max)
                {
                    answer++;
                }
            }
        }

        return answer;
    }
}
```

---

## ✨ 코드 의미

### 1. 거리 배열을 방문 배열처럼 사용

```csharp
int[] dist = new int[n + 1];
```

`int` 배열은 기본값이 `0`입니다.

그래서 `dist[node] == 0`이면 아직 방문하지 않은 노드로 볼 수 있습니다.

---

### 2. 시작 거리 1로 두기

```csharp
dist[1] = 1;
```

실제 거리는 0이지만, 코드에서는 방문 여부를 구분하기 위해 1부터 시작합니다.

이 문제는 거리의 실제 값이 아니라 가장 먼 거리의 노드 개수만 필요합니다.

그래서 모든 거리가 1씩 커져도 정답에는 영향이 없습니다. ✅

---

### 3. 방문할 때마다 답 갱신

```csharp
if (dist[next] > max)
{
    max = dist[next];
    answer = 1;
}
else if (dist[next] == max)
{
    answer++;
}
```

새로운 최댓거리가 나오면 개수를 다시 1로 만듭니다.

현재 최댓거리와 같은 노드가 나오면 개수를 늘립니다.

---

## ⚠️ 주의할 점

이 짧은 버전은 `dist[1] = 1`로 시작합니다.

따라서 `dist` 값은 실제 간선 개수보다 1 큽니다.

하지만 우리는 가장 먼 거리의 **개수**만 구하므로 괜찮습니다.

실제 거리 값 자체를 출력해야 하는 문제라면 풀이 1처럼 `distance[1] = 0`으로 두는 편이 더 자연스럽습니다. 🌱

---

## ⏱️ 시간 복잡도

인접 리스트 생성과 BFS를 합쳐:

```text
O(V + E)
```

입니다.

---

## 📦 공간 복잡도

그래프, 거리 배열, 큐를 사용합니다.

```text
O(V + E)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 거리 배열 + BFS | 실제 최단거리가 명확하다 😊 | O(V + E) | O(V + E) |
| 풀이 2 | BFS 중 답 갱신 | 코드가 조금 더 짧다 🚀 | O(V + E) | O(V + E) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 거리 배열의 의미가 정확히 보입니다.
2. BFS가 최단거리를 만드는 과정이 이해하기 쉽습니다.
3. 마지막에 최댓거리를 세는 흐름이 명확합니다.
```

코딩 테스트 제출용으로 코드 길이를 우선한다면 **풀이 2번**도 좋습니다.

다만 거리 값이 1씩 밀려 있다는 점을 기억해야 합니다.

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
가중치가 없는 그래프에서 1번 노드로부터의 최단거리를 구한다.
```

가중치가 없는 그래프의 최단거리는 BFS가 잘 맞습니다.

풀이 흐름은 다음과 같습니다.

```text
1. 양방향 그래프를 인접 리스트로 만든다.
2. 1번 노드에서 BFS를 시작한다.
3. 각 노드까지의 최단거리를 저장한다.
4. 가장 큰 거리의 노드 개수를 센다.
```

🌐 “가장 먼 노드”는 BFS로 최단거리 레벨을 구하는 대표적인 그래프 문제입니다.
