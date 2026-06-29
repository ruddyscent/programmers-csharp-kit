# 🗺️ 게임 맵 최단거리 — C# 학습자료

## 📌 1. 문제 핵심

게임 맵은 `0`과 `1`로 이루어진 2차원 배열입니다.

```text
0: 벽이라서 갈 수 없는 칸
1: 이동할 수 있는 칸
```

캐릭터는 왼쪽 위 `(0, 0)`에서 시작하고, 목표는 오른쪽 아래 `(n - 1, m - 1)`까지 가는 것입니다.

한 번에 이동할 수 있는 방향은 네 방향입니다.

```text
위, 아래, 왼쪽, 오른쪽
```

목표는 상대 팀 진영까지 가기 위해 지나야 하는 칸 수의 최솟값을 구하는 것입니다. 도착할 수 없다면 `-1`을 반환합니다. 🎯

---

# 🔍 예제 이해하기

```text
maps = [
  [1, 0, 1, 1, 1],
  [1, 0, 1, 0, 1],
  [1, 0, 1, 1, 1],
  [1, 1, 1, 0, 1],
  [0, 0, 0, 0, 1]
]
```

시작 위치는 왼쪽 위입니다.

도착 위치는 오른쪽 아래입니다.

이 맵에서는 가장 짧은 경로로 이동하면 총 `11`개의 칸을 지나 도착할 수 있습니다.

따라서 정답은 `11`입니다. ✅

---

## ⚠️ 주의할 점

이 문제는 “갈 수 있는 길이 있는가?”만 묻는 문제가 아닙니다.

가장 짧은 길이를 찾아야 합니다.

그래서 DFS보다 BFS가 더 잘 맞습니다.

BFS는 시작점에서 가까운 칸부터 차례대로 탐색합니다.

즉, 어떤 칸에 처음 도착했을 때의 거리가 그 칸까지 가는 최단거리입니다. 🚶

---

# 풀이 1. 이해하기 쉬운 방법 — BFS + 거리 함께 저장

## 💡 아이디어

큐에 현재 위치와 현재까지 지나온 칸 수를 함께 넣습니다.

처음 위치 `(0, 0)`도 지나간 칸에 포함하므로 시작 거리는 `1`입니다.

큐에서 하나씩 꺼내면서 네 방향으로 이동합니다.

이동할 수 있는 칸이고 아직 방문하지 않았다면 큐에 넣습니다.

도착 지점을 만나면 그때의 거리를 바로 반환하면 됩니다. 🌱

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[,] maps)
    {
        int rowCount = maps.GetLength(0);
        int colCount = maps.GetLength(1);

        int[] dr = { -1, 1, 0, 0 };
        int[] dc = { 0, 0, -1, 1 };

        bool[,] visited = new bool[rowCount, colCount];
        Queue<int[]> queue = new Queue<int[]>();

        queue.Enqueue(new int[] { 0, 0, 1 });
        visited[0, 0] = true;

        while (queue.Count > 0)
        {
            int[] current = queue.Dequeue();
            int row = current[0];
            int col = current[1];
            int distance = current[2];

            if (row == rowCount - 1 && col == colCount - 1)
            {
                return distance;
            }

            for (int i = 0; i < 4; i++)
            {
                int nextRow = row + dr[i];
                int nextCol = col + dc[i];

                if (nextRow < 0 || nextRow >= rowCount || nextCol < 0 || nextCol >= colCount)
                {
                    continue;
                }

                if (maps[nextRow, nextCol] == 0 || visited[nextRow, nextCol])
                {
                    continue;
                }

                visited[nextRow, nextCol] = true;
                queue.Enqueue(new int[] { nextRow, nextCol, distance + 1 });
            }
        }

        return -1;
    }
}
```

---

## 🔎 코드 해설

### 1. 맵 크기 구하기

```csharp
int rowCount = maps.GetLength(0);
int colCount = maps.GetLength(1);
```

`maps`는 2차원 배열입니다.

`GetLength(0)`은 행의 개수, `GetLength(1)`은 열의 개수입니다.

---

### 2. 네 방향 이동 배열

```csharp
int[] dr = { -1, 1, 0, 0 };
int[] dc = { 0, 0, -1, 1 };
```

`dr`은 행 변화량, `dc`는 열 변화량입니다.

두 배열을 같은 인덱스로 사용하면 네 방향을 간단히 확인할 수 있습니다.

```text
(-1,  0): 위
( 1,  0): 아래
( 0, -1): 왼쪽
( 0,  1): 오른쪽
```

---

### 3. 시작점 넣기

```csharp
queue.Enqueue(new int[] { 0, 0, 1 });
visited[0, 0] = true;
```

큐에는 `행`, `열`, `거리`를 저장합니다.

시작 칸도 지나간 칸으로 세므로 거리는 `1`입니다.

---

### 4. 도착하면 바로 반환

```csharp
if (row == rowCount - 1 && col == colCount - 1)
{
    return distance;
}
```

BFS는 가까운 칸부터 탐색합니다.

따라서 도착 지점을 처음 꺼냈을 때의 거리가 최단거리입니다. ✨

---

### 5. 이동할 수 있는 칸만 큐에 넣기

```csharp
if (maps[nextRow, nextCol] == 0 || visited[nextRow, nextCol])
{
    continue;
}
```

벽이거나 이미 방문한 칸은 건너뜁니다.

처음 방문하는 길 칸만 큐에 넣습니다.

---

## ⏱️ 시간 복잡도

`N`은 행의 개수, `M`은 열의 개수입니다.

시간 복잡도: `O(N * M)`

각 칸은 최대 한 번 방문됩니다.

각 방문마다 네 방향을 확인하므로 전체 시간 복잡도는 `O(N * M)`입니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N * M)`

방문 배열 `visited`가 `O(N * M)` 공간을 사용합니다.

큐에도 최악의 경우 많은 칸이 들어갈 수 있으므로 `O(N * M)` 공간을 사용할 수 있습니다.

---

# 풀이 2. 짧은 코드 버전 — maps를 거리 배열로 사용

## 💡 아이디어

별도의 거리 배열을 만들지 않고 `maps` 자체에 거리를 기록합니다.

처음에는 이동 가능한 칸이 모두 `1`입니다.

BFS로 이동하면서 다음 칸에 현재 칸의 거리보다 1 큰 값을 저장합니다.

예를 들어 현재 칸까지의 거리가 `5`라면, 다음 칸에는 `6`을 저장합니다.

이 방식은 코드가 짧고 메모리를 조금 덜 씁니다. 다만 입력 배열 `maps`의 값이 바뀐다는 점은 기억해야 합니다. 📝

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[,] maps)
    {
        int n = maps.GetLength(0);
        int m = maps.GetLength(1);

        int[] dr = { -1, 1, 0, 0 };
        int[] dc = { 0, 0, -1, 1 };

        Queue<int[]> queue = new Queue<int[]>();
        queue.Enqueue(new int[] { 0, 0 });

        while (queue.Count > 0)
        {
            int[] current = queue.Dequeue();
            int row = current[0];
            int col = current[1];

            for (int i = 0; i < 4; i++)
            {
                int nextRow = row + dr[i];
                int nextCol = col + dc[i];

                if (nextRow < 0 || nextRow >= n || nextCol < 0 || nextCol >= m)
                {
                    continue;
                }

                if (nextRow == 0 && nextCol == 0)
                {
                    continue;
                }

                if (maps[nextRow, nextCol] != 1)
                {
                    continue;
                }

                maps[nextRow, nextCol] = maps[row, col] + 1;
                queue.Enqueue(new int[] { nextRow, nextCol });
            }
        }

        return maps[n - 1, m - 1] <= 1 ? -1 : maps[n - 1, m - 1];
    }
}
```

---

## 🔎 코드 해설

### 1. 방문 여부를 maps 값으로 판단

```csharp
if (maps[nextRow, nextCol] != 1)
{
    continue;
}
```

`1`인 칸만 아직 방문하지 않은 길입니다.

`0`은 벽입니다.

`2` 이상인 값은 이미 거리로 갱신된 칸입니다.

---

### 2. 시작점 재방문 막기

```csharp
if (nextRow == 0 && nextCol == 0)
{
    continue;
}
```

시작점도 값이 `1`입니다.

그냥 두면 주변 칸에서 다시 시작점으로 돌아올 수 있습니다.

그래서 시작점은 따로 건너뜁니다.

---

### 3. 다음 칸에 거리 저장

```csharp
maps[nextRow, nextCol] = maps[row, col] + 1;
```

현재 칸까지의 최단거리에 1을 더해 다음 칸의 거리로 저장합니다.

BFS이므로 처음 저장되는 값이 최단거리입니다.

---

### 4. 도착할 수 없는 경우

```csharp
return maps[n - 1, m - 1] <= 1 ? -1 : maps[n - 1, m - 1];
```

도착 칸이 끝까지 `1`이라면 방문하지 못했다는 뜻입니다.

도착 칸이 `0`인 경우도 갈 수 없는 칸이므로 `-1`을 반환합니다.

이 문제에서는 맵 크기가 `1 x 1`인 경우가 주어지지 않으므로, 도착 칸이 그대로 `1`이면 `-1`을 반환해도 괜찮습니다.

---

## ⏱️ 시간 복잡도

`N`은 행의 개수, `M`은 열의 개수입니다.

시간 복잡도: `O(N * M)`

각 칸은 한 번만 거리로 갱신되고, 각 칸에서 네 방향을 확인합니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N * M)`

별도의 방문 배열은 만들지 않습니다.

하지만 BFS 큐에는 최악의 경우 `O(N * M)`개의 칸이 들어갈 수 있습니다.

---

## ✅ 정리

이 문제는 2차원 격자에서 최단거리를 찾는 문제입니다.

가중치가 모두 같은 칸 이동 문제이므로 BFS를 사용하면 가장 빠른 길을 정확하게 구할 수 있습니다.

핵심은 다음입니다.

```text
시작점에서 가까운 칸부터 탐색한다.
처음 도착한 거리가 그 칸까지의 최단거리다.
```

처음 풀 때는 `visited`와 `distance`를 큐에 함께 넣는 풀이가 이해하기 좋습니다.

익숙해진 뒤에는 `maps`에 거리를 직접 기록하는 짧은 풀이도 자주 사용됩니다. 🚀
