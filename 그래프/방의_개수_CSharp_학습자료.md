# 🏠 방의 개수 — C# 학습자료

## 📌 1. 문제 핵심

원점 `(0, 0)`에서 시작해 `arrows`에 적힌 방향대로 선을 그립니다.

선을 그리다가 닫힌 영역이 생기면 방이 하나 만들어집니다.

우리가 구해야 하는 것은 다음입니다.

```text
이동 경로가 만들 수 있는 방의 개수
```

입니다. 🎯

방은 단순히 같은 점을 다시 방문한다고 항상 생기는 것은 아닙니다.

이미 지나간 선을 그대로 다시 따라가는 경우에는 새 방이 생기지 않습니다.

핵심은 다음입니다.

```text
이미 방문한 점에 새 선으로 다시 들어가면 방이 하나 생긴다.
```

---

# 🔢 방향 번호

방향은 0부터 7까지입니다.

문제 그림 기준으로 다음처럼 생각할 수 있습니다.

```text
0: 위
1: 오른쪽 위
2: 오른쪽
3: 오른쪽 아래
4: 아래
5: 왼쪽 아래
6: 왼쪽
7: 왼쪽 위
```

C# 코드에서는 다음 배열로 표현합니다.

```csharp
int[] dx = { 0, 1, 1, 1, 0, -1, -1, -1 };
int[] dy = { 1, 1, 0, -1, -1, -1, 0, 1 };
```

예를 들어 `arrow = 2`라면:

```text
dx[2] = 1
dy[2] = 0
```

이므로 오른쪽으로 이동합니다. ➡️

---

# ⚠️ 가장 중요한 주의점: 대각선 교차

이 문제에서 가장 많이 틀리는 부분은 대각선 교차입니다.

예를 들어 두 대각선 선분이 `X` 모양으로 교차할 수 있습니다.

```text
\ /
 X
/ \
```

이때 교차점은 원래 좌표의 정수 점이 아닐 수 있습니다.

그냥 한 번에 한 칸씩 이동하면 이 교차점을 방문한 점으로 기록하지 못합니다.

그러면 실제로는 방이 생겼는데 놓칠 수 있습니다. 🚨

그래서 해결 방법은 간단합니다.

```text
한 번 이동을 두 번의 작은 이동으로 쪼갠다.
```

즉, 모든 좌표를 2배로 확대한 것처럼 처리합니다.

이렇게 하면 대각선끼리 만나는 중간 교차점도 정수 좌표가 되어 방문 체크가 가능합니다. ✅

---

# 🧠 핵심 아이디어: 방문한 점과 방문한 선 기록하기

방이 생기는 순간은 다음입니다.

```text
다음 점은 이미 방문한 점이다.
하지만 현재 선은 처음 지나가는 선이다.
```

왜 그럴까요?

이미 방문한 점에 새로운 선으로 들어간다는 것은, 기존 경로와 새 경로가 만나 닫힌 영역을 만든다는 뜻입니다.

반대로 이미 지나간 선을 다시 따라가는 경우는 새 영역을 만들지 않습니다.

그래서 두 가지를 모두 기록해야 합니다.

```text
1. 방문한 점
2. 방문한 선
```

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 점과 선을 HashSet에 저장하기

## 💡 아이디어

1. 현재 위치를 `(x, y)`로 저장합니다.
2. 방문한 점을 `HashSet<(int x, int y)>`에 저장합니다.
3. 방문한 선을 `HashSet<(int x1, int y1, int x2, int y2)>`에 저장합니다.
4. 각 방향 이동을 두 번 반복해서 대각선 교차를 처리합니다.
5. 이미 방문한 점에 처음 지나가는 선으로 들어가면 방 개수를 증가시킵니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[] arrows)
    {
        int[] dx = { 0, 1, 1, 1, 0, -1, -1, -1 };
        int[] dy = { 1, 1, 0, -1, -1, -1, 0, 1 };

        var visitedNodes = new HashSet<(int x, int y)>();
        var visitedEdges = new HashSet<(int x1, int y1, int x2, int y2)>();

        int x = 0;
        int y = 0;
        int answer = 0;

        visitedNodes.Add((x, y));

        foreach (int arrow in arrows)
        {
            for (int step = 0; step < 2; step++)
            {
                int nx = x + dx[arrow];
                int ny = y + dy[arrow];

                var nextNode = (nx, ny);
                var edge = (x, y, nx, ny);
                var reverseEdge = (nx, ny, x, y);

                if (visitedNodes.Contains(nextNode) && !visitedEdges.Contains(edge))
                    answer++;

                visitedNodes.Add(nextNode);
                visitedEdges.Add(edge);
                visitedEdges.Add(reverseEdge);

                x = nx;
                y = ny;
            }
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. 방향 배열

```csharp
int[] dx = { 0, 1, 1, 1, 0, -1, -1, -1 };
int[] dy = { 1, 1, 0, -1, -1, -1, 0, 1 };
```

`arrow` 값에 따라 이동할 좌표 변화량을 가져옵니다.

예를 들어 `arrow = 6`이면 왼쪽으로 이동합니다.

```text
dx[6] = -1
dy[6] = 0
```

---

### 2. 방문한 점과 선 저장

```csharp
var visitedNodes = new HashSet<(int x, int y)>();
var visitedEdges = new HashSet<(int x1, int y1, int x2, int y2)>();
```

`visitedNodes`는 이미 방문한 좌표를 저장합니다.

`visitedEdges`는 이미 지나간 선을 저장합니다.

선은 시작점과 끝점이 모두 필요합니다.

```text
(x1, y1, x2, y2)
```

---

### 3. 한 방향을 두 번 이동

```csharp
for (int step = 0; step < 2; step++)
```

대각선 교차를 놓치지 않기 위해 한 번의 이동을 두 번으로 나눕니다.

좌표를 2배로 확대한 효과가 있습니다. 🔍

---

### 4. 새 방이 생기는 조건

```csharp
if (visitedNodes.Contains(nextNode) && !visitedEdges.Contains(edge))
    answer++;
```

다음 점이 이미 방문한 점이고, 현재 선은 처음 지나가는 선이라면 새 방이 생깁니다.

이미 지나간 선을 다시 걷는 경우는 방을 새로 만들지 않으므로 `visitedEdges`를 함께 확인합니다.

---

### 5. 선을 양방향으로 저장

```csharp
visitedEdges.Add(edge);
visitedEdges.Add(reverseEdge);
```

선은 방향이 중요하지 않습니다.

`A → B`로 지나간 선은 `B → A`로 다시 지나가도 같은 선입니다.

그래서 양방향을 모두 저장합니다. 🔁

---

## ⏱️ 시간 복잡도

`arrows`의 길이를 `n`이라고 하겠습니다.

각 방향마다 2번 이동합니다.

각 이동에서 `HashSet` 조회와 추가를 합니다.

평균적으로 `O(1)`이므로 전체 시간 복잡도는:

```text
O(n)
```

입니다.

---

## 📦 공간 복잡도

방문한 점과 선을 저장합니다.

이동 횟수에 비례해서 저장하므로:

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — 튜플을 바로 사용하기

## 💡 아이디어

풀이 1과 같은 방식입니다.

다만 변수 선언을 줄이고, 현재 좌표와 다음 좌표를 튜플로 바로 다룹니다.

코딩 테스트 제출용으로 조금 더 짧게 쓸 수 있습니다. ⚡

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[] arrows)
    {
        int[] dx = { 0, 1, 1, 1, 0, -1, -1, -1 };
        int[] dy = { 1, 1, 0, -1, -1, -1, 0, 1 };
        var nodes = new HashSet<(int x, int y)>();
        var edges = new HashSet<(int x1, int y1, int x2, int y2)>();

        int x = 0, y = 0, answer = 0;
        nodes.Add((0, 0));

        foreach (int arrow in arrows)
        {
            for (int i = 0; i < 2; i++)
            {
                int nx = x + dx[arrow], ny = y + dy[arrow];

                if (nodes.Contains((nx, ny)) && !edges.Contains((x, y, nx, ny)))
                    answer++;

                nodes.Add((nx, ny));
                edges.Add((x, y, nx, ny));
                edges.Add((nx, ny, x, y));

                x = nx;
                y = ny;
            }
        }

        return answer;
    }
}
```

---

## ✨ 코드 의미

### 1. 점 방문 확인

```csharp
nodes.Contains((nx, ny))
```

다음 좌표가 이미 방문한 점인지 확인합니다.

---

### 2. 선 방문 확인

```csharp
!edges.Contains((x, y, nx, ny))
```

현재 위치에서 다음 위치로 가는 선을 처음 지나가는지 확인합니다.

---

### 3. 방 개수 증가

```csharp
if (nodes.Contains((nx, ny)) && !edges.Contains((x, y, nx, ny)))
    answer++;
```

이미 방문한 점에 새로운 선으로 들어가면 닫힌 영역이 생깁니다.

그때 방 개수를 1 증가시킵니다. ✅

---

## ⚠️ 주의할 점

이 문제에서 한 번 이동을 두 번으로 쪼개는 부분은 생략하면 안 됩니다.

```csharp
for (int i = 0; i < 2; i++)
```

이 부분이 있어야 대각선끼리 중간에서 교차하는 경우도 올바르게 처리됩니다.

---

## ⏱️ 시간 복잡도

각 이동을 두 번씩 처리하므로 상수배만 늘어납니다.

```text
O(n)
```

입니다.

---

## 📦 공간 복잡도

방문한 점과 선을 저장합니다.

```text
O(n)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 점/선 방문 기록 | 원리가 잘 보인다 😊 | O(n) | O(n) |
| 풀이 2 | 튜플 직접 사용 | 코드가 조금 더 짧다 🚀 | O(n) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 방문한 점과 방문한 선의 의미가 잘 보입니다.
2. 새 방이 생기는 조건을 이해하기 쉽습니다.
3. 대각선 교차를 왜 두 번 이동으로 처리하는지 따라가기 좋습니다.
```

코딩 테스트 제출용으로 코드 길이를 우선한다면 **풀이 2번**도 좋습니다.

다만 한 방향을 두 번 처리하는 부분은 절대 줄이면 안 됩니다. 🚨

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
이미 방문한 점에 처음 지나는 선으로 들어가면 방이 생긴다.
```

그리고 대각선 교차를 처리하기 위해:

```text
한 번 이동을 두 번으로 쪼갠다.
```

방문한 점과 방문한 선을 모두 기록하면 됩니다.

🏠 “방의 개수”는 그래프에서 경로가 만드는 사이클을 세는 까다로운 문제입니다.
