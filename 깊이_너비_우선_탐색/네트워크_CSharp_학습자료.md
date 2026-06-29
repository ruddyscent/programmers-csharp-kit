# 🌐 네트워크 — C# 학습자료

## 📌 1. 문제 핵심

컴퓨터들이 서로 연결되어 있습니다.

직접 연결되어 있지 않아도, 중간 컴퓨터를 통해 이어질 수 있다면 같은 네트워크입니다.

예를 들어:

```text
A - B - C
```

처럼 연결되어 있다면 `A`와 `C`는 직접 연결되어 있지 않아도 같은 네트워크에 속합니다.

목표는 전체 컴퓨터들을 몇 개의 네트워크 그룹으로 나눌 수 있는지 세는 것입니다. 🧩

---

# 🔍 예제 이해하기

```text
n = 3
computers = [
  [1, 1, 0],
  [1, 1, 0],
  [0, 0, 1]
]
```

0번 컴퓨터와 1번 컴퓨터는 연결되어 있습니다.

2번 컴퓨터는 자기 자신과만 연결되어 있습니다.

따라서 네트워크는 다음처럼 2개입니다.

```text
{0, 1}
{2}
```

정답은 `2`입니다. ✅

---

## ⚠️ 주의할 점

`computers[i, j] == 1`이면 `i`번 컴퓨터와 `j`번 컴퓨터가 연결되어 있다는 뜻입니다.

그리고 `computers[i, i]`는 항상 `1`입니다.

자기 자신과 연결된 정보는 네트워크 개수를 세는 데 특별히 중요하지 않습니다.

진짜 중요한 것은 다음 질문입니다.

```text
아직 방문하지 않은 컴퓨터에서 시작해서,
연결된 컴퓨터들을 한 번에 모두 방문할 수 있는가?
```

한 번의 DFS 또는 BFS로 방문되는 컴퓨터들은 모두 같은 네트워크입니다. 🚶

---

# 풀이 1. 이해하기 쉬운 방법 — DFS

## 💡 아이디어

방문하지 않은 컴퓨터를 하나 찾습니다.

그 컴퓨터에서 DFS를 시작해 연결된 컴퓨터들을 모두 방문 처리합니다.

DFS 한 번이 끝났다는 것은 네트워크 하나를 모두 확인했다는 뜻입니다.

따라서 DFS를 새로 시작할 때마다 정답을 1 증가시키면 됩니다. 🌱

흐름은 다음과 같습니다.

```text
모든 컴퓨터를 차례대로 확인한다.
아직 방문하지 않았다면 새로운 네트워크를 발견한 것이다.
정답을 1 증가시킨다.
DFS로 연결된 컴퓨터들을 모두 방문 처리한다.
```

---

## 💻 C# 코드

```csharp
public class Solution
{
    bool[] visited;
    int[,] computers;
    int n;

    public int solution(int n, int[,] computers)
    {
        this.n = n;
        this.computers = computers;
        visited = new bool[n];

        int answer = 0;

        for (int i = 0; i < n; i++)
        {
            if (visited[i])
            {
                continue;
            }

            answer++;
            Dfs(i);
        }

        return answer;
    }

    void Dfs(int current)
    {
        visited[current] = true;

        for (int next = 0; next < n; next++)
        {
            if (computers[current, next] == 1 && !visited[next])
            {
                Dfs(next);
            }
        }
    }
}
```

---

## 🔎 코드 해설

### 1. 방문 배열 만들기

```csharp
visited = new bool[n];
```

각 컴퓨터를 이미 확인했는지 저장합니다.

`visited[i] == true`라면 `i`번 컴퓨터는 어떤 네트워크에 이미 포함된 상태입니다.

---

### 2. 새 네트워크 찾기

```csharp
for (int i = 0; i < n; i++)
{
    if (visited[i])
    {
        continue;
    }

    answer++;
    Dfs(i);
}
```

아직 방문하지 않은 컴퓨터를 만나면 새 네트워크를 발견한 것입니다.

그래서 `answer`를 1 증가시킨 뒤, DFS로 연결된 컴퓨터들을 모두 방문 처리합니다.

---

### 3. 연결된 컴퓨터 탐색

```csharp
for (int next = 0; next < n; next++)
{
    if (computers[current, next] == 1 && !visited[next])
    {
        Dfs(next);
    }
}
```

현재 컴퓨터 `current`와 연결된 컴퓨터 `next`를 찾습니다.

연결되어 있고 아직 방문하지 않았다면 같은 네트워크이므로 DFS를 이어갑니다. 🔁

---

## ⏱️ 시간 복잡도

`N`은 컴퓨터의 개수입니다.

시간 복잡도: `O(N^2)`

각 컴퓨터에서 연결 여부를 확인하기 위해 길이 `N`짜리 행을 살펴봅니다.

인접 행렬 전체를 확인하는 것과 같으므로 전체 시간 복잡도는 `O(N^2)`입니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N)`

방문 배열 `visited`가 `O(N)` 공간을 사용합니다.

재귀 DFS의 호출 스택도 최악의 경우 `O(N)`까지 깊어질 수 있습니다.

---

# 풀이 2. 재귀 없는 방법 — BFS

## 💡 아이디어

DFS 대신 BFS를 사용해도 같은 방식으로 풀 수 있습니다.

차이는 연결된 컴퓨터를 재귀로 따라가지 않고, `Queue`에 넣어 차례대로 꺼낸다는 점입니다.

새 네트워크를 발견하면 큐를 만들고, 연결된 컴퓨터들을 모두 방문할 때까지 반복합니다. 📦

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int n, int[,] computers)
    {
        bool[] visited = new bool[n];
        int answer = 0;

        for (int i = 0; i < n; i++)
        {
            if (visited[i])
            {
                continue;
            }

            answer++;
            var queue = new Queue<int>();
            queue.Enqueue(i);
            visited[i] = true;

            while (queue.Count > 0)
            {
                int current = queue.Dequeue();

                for (int next = 0; next < n; next++)
                {
                    if (computers[current, next] == 0 || visited[next])
                    {
                        continue;
                    }

                    visited[next] = true;
                    queue.Enqueue(next);
                }
            }
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. BFS 시작점 넣기

```csharp
Queue<int> queue = new Queue<int>();
queue.Enqueue(i);
visited[i] = true;
```

새 네트워크의 시작 컴퓨터를 큐에 넣습니다.

큐에 넣는 순간 방문 처리해야 같은 컴퓨터가 중복으로 들어가지 않습니다.

---

### 2. 큐가 빌 때까지 탐색

```csharp
while (queue.Count > 0)
```

큐가 비었다는 것은 현재 네트워크에 속한 컴퓨터를 모두 확인했다는 뜻입니다.

---

### 3. 연결된 컴퓨터 추가

```csharp
if (computers[current, next] == 0 || visited[next])
{
    continue;
}

visited[next] = true;
queue.Enqueue(next);
```

연결되어 있지 않거나 이미 방문했다면 건너뜁니다.

연결되어 있고 아직 방문하지 않았다면 같은 네트워크이므로 큐에 넣습니다.

---

## ⏱️ 시간 복잡도

`N`은 컴퓨터의 개수입니다.

시간 복잡도: `O(N^2)`

BFS에서도 각 컴퓨터의 연결 정보를 한 줄씩 확인합니다.

인접 행렬을 사용하므로 전체적으로 `N x N`개의 연결 정보를 확인할 수 있습니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N)`

방문 배열이 `O(N)` 공간을 사용합니다.

큐에는 최악의 경우 같은 네트워크에 속한 컴퓨터들이 들어갈 수 있으므로 `O(N)` 공간을 사용합니다.

---

## ✅ 정리

이 문제는 그래프에서 연결 요소의 개수를 세는 문제입니다.

프로그래머스 문제에서는 컴퓨터 연결 정보가 인접 행렬로 주어집니다.

핵심은 다음입니다.

```text
방문하지 않은 컴퓨터를 발견할 때마다 네트워크 개수를 1 증가시킨다.
DFS 또는 BFS로 그 네트워크 전체를 방문 처리한다.
```

DFS 풀이는 코드 흐름이 직관적이고, BFS 풀이는 재귀 호출 없이 반복문으로 처리할 수 있습니다.

둘 다 시간 복잡도는 `O(N^2)`, 공간 복잡도는 `O(N)`입니다. 🚀
