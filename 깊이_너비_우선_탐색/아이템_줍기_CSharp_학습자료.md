# 🎁 아이템 줍기 — C# 학습자료

## 📌 1. 문제 핵심

여러 직사각형이 겹쳐서 만들어진 지형이 있습니다.

캐릭터는 이 지형의 테두리만 따라 이동할 수 있습니다.

시작 위치에서 아이템 위치까지 테두리를 따라 이동할 때, 가장 짧은 거리를 구하는 문제입니다. 🎯

---

# 🔍 예제 이해하기

```text
rectangle = [[1,1,7,4], [3,2,5,5], [4,3,6,9], [2,6,8,8]]
characterX = 1
characterY = 3
itemX = 7
itemY = 8
```

여러 직사각형이 겹치면서 하나의 다각형 지형이 됩니다.

캐릭터는 직사각형 내부로 들어갈 수 없고, 테두리만 따라 이동할 수 있습니다.

이 예제에서 가장 짧은 테두리 경로의 길이는 `17`입니다. ✅

---

## ⚠️ 주의할 점

이 문제의 핵심 함정은 좌표를 그대로 쓰면 안 된다는 점입니다.

예를 들어 두 테두리가 실제로는 이어지지 않았는데, 격자에서는 붙어 있는 것처럼 보일 수 있습니다.

또는 꺾이는 모서리에서 테두리가 아닌 길을 가로질러 가는 문제가 생길 수 있습니다.

그래서 좌표를 모두 2배로 키워서 처리합니다. 🔍

```text
(1, 3) -> (2, 6)
(7, 8) -> (14, 16)
```

좌표를 2배로 늘리면 테두리 사이의 좁은 간격과 모서리 연결을 더 정확히 표현할 수 있습니다.

마지막에 BFS로 구한 거리는 2배 좌표계 기준이므로, 정답을 반환할 때 `2`로 나누면 됩니다.

---

# 풀이 1. 이해하기 쉬운 방법 — 좌표 2배 확대 + BFS

## 💡 아이디어

먼저 2차원 배열에 지형을 표시합니다.

각 칸의 의미는 다음처럼 둡니다.

```text
0: 아무것도 아님
1: 테두리
2: 직사각형 내부
```

직사각형을 모두 2배 좌표로 바꾼 뒤, 내부는 `2`, 테두리는 `1`로 표시합니다.

단, 이미 내부로 표시된 칸은 다시 테두리로 바꾸면 안 됩니다.

그 다음 캐릭터 위치에서 아이템 위치까지 `1`인 칸만 따라 BFS를 진행합니다. 🚶

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    int[,] board = new int[102, 102];
    int[] dx = { -1, 1, 0, 0 };
    int[] dy = { 0, 0, -1, 1 };

    public int solution(int[,] rectangle, int characterX, int characterY, int itemX, int itemY)
    {
        for (int i = 0; i < rectangle.GetLength(0); i++)
        {
            int x1 = rectangle[i, 0] * 2;
            int y1 = rectangle[i, 1] * 2;
            int x2 = rectangle[i, 2] * 2;
            int y2 = rectangle[i, 3] * 2;

            for (int x = x1; x <= x2; x++)
            {
                for (int y = y1; y <= y2; y++)
                {
                    if (x > x1 && x < x2 && y > y1 && y < y2)
                    {
                        board[x, y] = 2;
                    }
                    else if (board[x, y] != 2)
                    {
                        board[x, y] = 1;
                    }
                }
            }
        }

        var queue = new Queue<(int x, int y, int d)>();
        queue.Enqueue((characterX * 2, characterY * 2, 0));
        board[characterX * 2, characterY * 2] = 0;

        while (queue.Count > 0)
        {
            var now = queue.Dequeue();

            if (now.x == itemX * 2 && now.y == itemY * 2)
            {
                return now.d / 2;
            }

            for (int i = 0; i < 4; i++)
            {
                int nx = now.x + dx[i];
                int ny = now.y + dy[i];

                if (nx < 0 || nx >= 102 || ny < 0 || ny >= 102)
                {
                    continue;
                }

                if (board[nx, ny] != 1)
                {
                    continue;
                }

                board[nx, ny] = 0;
                queue.Enqueue((nx, ny, now.d + 1));
            }
        }

        return 0;
    }
}
```

---

## 🔎 코드 해설

### 1. 좌표를 2배로 키우기

```csharp
int x1 = rectangle[i, 0] * 2;
int y1 = rectangle[i, 1] * 2;
int x2 = rectangle[i, 2] * 2;
int y2 = rectangle[i, 3] * 2;
```

문제의 좌표는 최대 `50`입니다.

2배로 키워도 최대 `100`이므로 `102 x 102` 배열이면 충분합니다.

---

### 2. 내부와 테두리 구분하기

```csharp
bool isInside = x > x1 && x < x2 && y > y1 && y < y2;
```

직사각형의 경계선을 제외한 부분은 내부입니다.

내부는 캐릭터가 이동할 수 없으므로 `2`로 표시합니다.

---

### 3. 내부를 테두리로 덮어쓰지 않기

```csharp
else if (board[x, y] != 2)
{
    board[x, y] = 1;
}
```

직사각형이 겹치면 어떤 직사각형의 테두리가 다른 직사각형의 내부에 들어갈 수 있습니다.

그런 칸은 실제 이동 가능한 테두리가 아닙니다.

그래서 이미 내부 `2`로 표시된 칸은 다시 `1`로 바꾸지 않습니다. ✅

---

### 4. 테두리만 따라 BFS

```csharp
if (board[nx, ny] != 1)
{
    continue;
}
```

캐릭터는 테두리만 따라 이동할 수 있습니다.

따라서 `board` 값이 `1`인 칸만 큐에 넣습니다.

---

### 5. 거리 나누기

```csharp
return now.d / 2;
```

좌표를 2배로 키웠기 때문에 이동 거리도 2배가 됩니다.

원래 문제의 거리로 바꾸려면 마지막에 `2`로 나누면 됩니다.

---

## ⏱️ 시간 복잡도

`R`은 직사각형의 개수, `C`는 2배로 키운 좌표 범위입니다.

시간 복잡도: `O(R * C^2 + C^2)`

직사각형을 격자에 표시하는 데 최대 `O(R * C^2)`가 걸립니다.

BFS는 격자의 각 칸을 최대 한 번 방문하므로 `O(C^2)`입니다.

좌표 최댓값이 50으로 작기 때문에 실제 실행 시간은 충분히 빠릅니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(C^2)`

지형 배열 `board`와 BFS 큐가 격자 크기에 비례하는 공간을 사용합니다.

방문한 테두리는 `board` 값을 `0`으로 바꿔 처리하므로 별도의 방문 배열은 만들지 않습니다.

---

# 풀이 2. 짧은 코드 버전 — 테두리 표시 후 내부 지우기

## 💡 아이디어

이번에는 `bool[,] border` 하나로 테두리 여부를 관리합니다.

과정은 두 단계입니다.

1. 모든 직사각형의 테두리를 `true`로 표시합니다.
2. 모든 직사각형의 내부를 다시 `false`로 지웁니다.

이 순서가 중요합니다.

먼저 테두리를 전부 칠하고, 나중에 내부를 지워야 겹친 직사각형의 안쪽 선이 깔끔하게 제거됩니다. 🧹

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[,] rectangle, int characterX, int characterY, int itemX, int itemY)
    {
        bool[,] border = new bool[102, 102];

        for (int step = 0; step < 2; step++)
        {
            for (int i = 0; i < rectangle.GetLength(0); i++)
            {
                int x1 = rectangle[i, 0] * 2;
                int y1 = rectangle[i, 1] * 2;
                int x2 = rectangle[i, 2] * 2;
                int y2 = rectangle[i, 3] * 2;

                for (int x = x1; x <= x2; x++)
                {
                    for (int y = y1; y <= y2; y++)
                    {
                        bool inside = x > x1 && x < x2 && y > y1 && y < y2;

                        if (step == 0 && !inside)
                        {
                            border[x, y] = true;
                        }
                        else if (step == 1 && inside)
                        {
                            border[x, y] = false;
                        }
                    }
                }
            }
        }

        int[] dx = { -1, 1, 0, 0 };
        int[] dy = { 0, 0, -1, 1 };
        var queue = new Queue<(int x, int y, int d)>();

        queue.Enqueue((characterX * 2, characterY * 2, 0));
        border[characterX * 2, characterY * 2] = false;

        while (queue.Count > 0)
        {
            var now = queue.Dequeue();

            if (now.x == itemX * 2 && now.y == itemY * 2)
            {
                return now.d / 2;
            }

            for (int i = 0; i < 4; i++)
            {
                int nx = now.x + dx[i];
                int ny = now.y + dy[i];

                if (nx < 0 || nx >= 102 || ny < 0 || ny >= 102)
                {
                    continue;
                }

                if (!border[nx, ny])
                {
                    continue;
                }

                border[nx, ny] = false;
                queue.Enqueue((nx, ny, now.d + 1));
            }
        }

        return 0;
    }
}
```

---

## 🔎 코드 해설

### 1. 모든 테두리 표시

```csharp
if (step == 0 && !inside)
{
    border[x, y] = true;
}
```

직사각형의 네 변에 해당하는 칸을 이동 가능한 후보로 표시합니다.

---

### 2. 모든 내부 제거

```csharp
else if (step == 1 && inside)
{
    border[x, y] = false;
}
```

테두리를 모두 표시한 뒤 내부를 지웁니다.

이렇게 하면 겹친 직사각형의 안쪽 테두리도 이동 경로에서 제거됩니다.

---

### 3. border 배열을 방문 배열처럼 사용

```csharp
border[nx, ny] = false;
```

이미 방문한 테두리는 다시 방문하지 않아도 됩니다.

그래서 방문한 칸을 `false`로 바꿔 방문 배열 역할까지 함께 하게 만들었습니다.

---

## ⏱️ 시간 복잡도

`R`은 직사각형의 개수, `C`는 2배로 키운 좌표 범위입니다.

시간 복잡도: `O(R * C^2 + C^2)`

테두리 표시와 내부 제거가 각각 최대 `O(R * C^2)`입니다.

BFS는 격자 칸을 최대 한 번씩 확인합니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(C^2)`

`border` 배열과 BFS 큐가 격자 크기에 비례하는 공간을 사용합니다.

---

## ✅ 정리

이 문제는 그냥 그래프 탐색만 하면 되는 문제가 아닙니다.

먼저 직사각형들이 만든 실제 이동 가능한 테두리를 정확히 만들어야 합니다.

핵심은 다음입니다.

```text
좌표를 2배로 키운다.
직사각형의 내부와 테두리를 구분한다.
테두리만 따라 BFS로 최단거리를 구한다.
마지막에 거리를 2로 나눈다.
```

좌표 2배 확대를 하지 않으면 모서리에서 잘못 연결된 길을 타는 실수가 생기기 쉽습니다.

이 문제는 “BFS + 좌표 전처리”를 함께 연습하기 좋은 문제입니다. 🚀
