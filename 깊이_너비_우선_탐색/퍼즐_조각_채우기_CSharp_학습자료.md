# 🧩 퍼즐 조각 채우기 — C# 학습자료

## 📌 1. 문제 핵심

게임 보드에는 빈칸이 있고, 테이블에는 퍼즐 조각이 놓여 있습니다.

퍼즐 조각을 회전시켜서 게임 보드의 빈 공간에 딱 맞게 넣어야 합니다.

중요한 규칙은 다음입니다.

```text
조각은 회전할 수 있다.
조각은 뒤집을 수 없다.
빈 공간과 조각의 모양이 정확히 같아야 한다.
```

목표는 규칙에 맞게 채울 수 있는 칸 수의 최댓값을 구하는 것입니다. 🎯

---

# 🔍 예제 이해하기

게임 보드에서는 `0`이 빈칸입니다.

테이블에서는 `1`이 퍼즐 조각입니다.

따라서 할 일은 크게 두 가지입니다.

```text
game_board에서 0으로 연결된 빈 공간들을 찾는다.
table에서 1로 연결된 퍼즐 조각들을 찾는다.
```

그 다음 빈 공간 하나와 퍼즐 조각 하나를 비교합니다.

회전했을 때 모양이 같다면 그 조각을 넣을 수 있습니다. ✅

---

## ⚠️ 주의할 점

이 문제는 단순히 칸 개수만 같다고 맞는 것이 아닙니다.

모양까지 같아야 합니다.

예를 들어 둘 다 4칸짜리라도:

```text
일자 모양
ㄴ자 모양
```

은 서로 맞지 않습니다.

또한 조각은 회전할 수 있지만 뒤집을 수는 없습니다.

그래서 비교할 때는 다음 4가지 회전만 확인합니다.

```text
0도
90도
180도
270도
```

좌우 반전이나 상하 반전은 하면 안 됩니다. 🚧

---

# 풀이 1. 이해하기 쉬운 방법 — BFS로 모양 찾고 직접 회전 비교

## 💡 아이디어

1. `game_board`에서 빈 공간을 모두 찾습니다.
2. `table`에서 퍼즐 조각을 모두 찾습니다.
3. 각 빈 공간마다 아직 쓰지 않은 퍼즐 조각을 하나씩 비교합니다.
4. 퍼즐 조각을 4방향으로 회전해 보며 빈 공간과 같은지 확인합니다.
5. 맞는 조각을 찾으면 그 칸 수만큼 정답에 더합니다.

모양을 비교하기 쉽게 각 모양의 좌표를 정규화합니다.

정규화란 모양의 가장 위쪽, 가장 왼쪽 좌표를 `(0, 0)`으로 맞추는 것입니다. 🌱

예를 들어 좌표가 다음과 같다면:

```text
(2, 3), (2, 4), (3, 3)
```

가장 작은 행은 `2`, 가장 작은 열은 `3`입니다.

각 좌표에서 `(2, 3)`을 빼면:

```text
(0, 0), (0, 1), (1, 0)
```

이렇게 위치와 상관없이 모양만 비교할 수 있습니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    private int n;
    private int[] dr = { -1, 1, 0, 0 };
    private int[] dc = { 0, 0, -1, 1 };

    public int solution(int[,] game_board, int[,] table)
    {
        n = game_board.GetLength(0);

        List<List<int[]>> blanks = FindShapes(game_board, 0);
        List<List<int[]>> pieces = FindShapes(table, 1);
        bool[] used = new bool[pieces.Count];

        int answer = 0;

        for (int i = 0; i < blanks.Count; i++)
        {
            for (int j = 0; j < pieces.Count; j++)
            {
                if (used[j] || blanks[i].Count != pieces[j].Count)
                {
                    continue;
                }

                if (CanFit(blanks[i], pieces[j]))
                {
                    used[j] = true;
                    answer += blanks[i].Count;
                    break;
                }
            }
        }

        return answer;
    }

    private List<List<int[]>> FindShapes(int[,] board, int target)
    {
        bool[,] visited = new bool[n, n];
        List<List<int[]>> shapes = new List<List<int[]>>();

        for (int row = 0; row < n; row++)
        {
            for (int col = 0; col < n; col++)
            {
                if (visited[row, col] || board[row, col] != target)
                {
                    continue;
                }

                shapes.Add(Bfs(board, visited, row, col, target));
            }
        }

        return shapes;
    }

    private List<int[]> Bfs(int[,] board, bool[,] visited, int startRow, int startCol, int target)
    {
        Queue<int[]> queue = new Queue<int[]>();
        List<int[]> cells = new List<int[]>();

        queue.Enqueue(new int[] { startRow, startCol });
        visited[startRow, startCol] = true;

        while (queue.Count > 0)
        {
            int[] current = queue.Dequeue();
            int row = current[0];
            int col = current[1];

            cells.Add(new int[] { row, col });

            for (int i = 0; i < 4; i++)
            {
                int nextRow = row + dr[i];
                int nextCol = col + dc[i];

                if (nextRow < 0 || nextRow >= n || nextCol < 0 || nextCol >= n)
                {
                    continue;
                }

                if (visited[nextRow, nextCol] || board[nextRow, nextCol] != target)
                {
                    continue;
                }

                visited[nextRow, nextCol] = true;
                queue.Enqueue(new int[] { nextRow, nextCol });
            }
        }

        return Normalize(cells);
    }

    private bool CanFit(List<int[]> blank, List<int[]> piece)
    {
        List<int[]> rotated = piece;

        for (int i = 0; i < 4; i++)
        {
            rotated = Normalize(rotated);

            if (IsSameShape(blank, rotated))
            {
                return true;
            }

            rotated = Rotate(rotated);
        }

        return false;
    }

    private List<int[]> Rotate(List<int[]> shape)
    {
        List<int[]> rotated = new List<int[]>();

        foreach (int[] cell in shape)
        {
            int row = cell[0];
            int col = cell[1];
            rotated.Add(new int[] { col, -row });
        }

        return rotated;
    }

    private List<int[]> Normalize(List<int[]> shape)
    {
        int minRow = int.MaxValue;
        int minCol = int.MaxValue;

        foreach (int[] cell in shape)
        {
            if (cell[0] < minRow)
            {
                minRow = cell[0];
            }

            if (cell[1] < minCol)
            {
                minCol = cell[1];
            }
        }

        List<int[]> normalized = new List<int[]>();

        foreach (int[] cell in shape)
        {
            normalized.Add(new int[] { cell[0] - minRow, cell[1] - minCol });
        }

        normalized.Sort((a, b) =>
        {
            if (a[0] == b[0])
            {
                return a[1].CompareTo(b[1]);
            }

            return a[0].CompareTo(b[0]);
        });

        return normalized;
    }

    private bool IsSameShape(List<int[]> a, List<int[]> b)
    {
        if (a.Count != b.Count)
        {
            return false;
        }

        for (int i = 0; i < a.Count; i++)
        {
            if (a[i][0] != b[i][0] || a[i][1] != b[i][1])
            {
                return false;
            }
        }

        return true;
    }
}
```

---

## 🔎 코드 해설

### 1. 빈 공간과 퍼즐 조각 찾기

```csharp
List<List<int[]>> blanks = FindShapes(game_board, 0);
List<List<int[]>> pieces = FindShapes(table, 1);
```

`game_board`에서는 `0`이 빈 공간입니다.

`table`에서는 `1`이 퍼즐 조각입니다.

같은 값으로 상하좌우 연결된 칸들을 BFS로 하나의 모양으로 묶습니다.

---

### 2. 모양 정규화

```csharp
return Normalize(cells);
```

BFS로 찾은 좌표는 실제 보드 위치를 기준으로 합니다.

하지만 모양 비교에는 위치가 중요하지 않습니다.

그래서 가장 위쪽, 가장 왼쪽으로 당겨서 `(0, 0)` 기준 좌표로 바꿉니다.

---

### 3. 회전하기

```csharp
rotated.Add(new int[] { col, -row });
```

좌표 `(row, col)`을 90도 회전시키는 방식입니다.

회전하면 음수 좌표가 생길 수 있으므로, 비교하기 전에 다시 정규화합니다.

---

### 4. 퍼즐 조각 재사용 막기

```csharp
used[j] = true;
```

한 번 사용한 퍼즐 조각은 다른 빈 공간에 다시 사용할 수 없습니다.

그래서 `used` 배열로 사용 여부를 관리합니다.

---

## ⏱️ 시간 복잡도

`N`은 보드 한 변의 길이, `B`는 빈 공간 개수, `P`는 퍼즐 조각 개수, `K`는 한 모양의 최대 칸 수입니다.

시간 복잡도: `O(N^2 + B * P * K log K)`

BFS로 전체 보드와 테이블을 훑는 데 `O(N^2)`이 걸립니다.

이후 빈 공간과 퍼즐 조각을 비교할 때, 회전과 정렬이 들어가므로 `B * P * K log K` 정도가 걸립니다.

이 문제에서는 `K`가 최대 6이라 비교 비용은 작습니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N^2)`

방문 배열과 BFS 큐, 추출한 모양 목록이 보드 크기에 비례하는 공간을 사용할 수 있습니다.

---

# 풀이 2. 실전형 방법 — 회전 표준 키 + Dictionary

## 💡 아이디어

이번에는 모양을 문자열 키로 바꿔서 빠르게 매칭합니다.

각 모양에 대해 4가지 회전 모양을 모두 만들고, 그중 알파벳 순서로 가장 앞서는 문자열을 대표 키로 사용합니다.

이 키를 “표준 모양”이라고 생각하면 됩니다.

```text
같은 모양이라면 회전해도 같은 표준 키가 나온다.
뒤집은 모양까지 일부러 같은 모양으로 취급하지는 않는다.
```

먼저 테이블의 퍼즐 조각들을 `Dictionary`에 개수로 저장합니다.

그 다음 게임 보드의 빈 공간을 하나씩 보며 같은 키를 가진 조각이 남아 있는지 확인합니다. 🧠

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    private int n;
    private int[] dr = { -1, 1, 0, 0 };
    private int[] dc = { 0, 0, -1, 1 };

    public int solution(int[,] game_board, int[,] table)
    {
        n = game_board.GetLength(0);

        List<List<int[]>> blanks = FindShapes(game_board, 0);
        List<List<int[]>> pieces = FindShapes(table, 1);

        Dictionary<string, int> pieceCounts = new Dictionary<string, int>();

        foreach (List<int[]> piece in pieces)
        {
            string key = GetCanonicalKey(piece);

            if (!pieceCounts.ContainsKey(key))
            {
                pieceCounts[key] = 0;
            }

            pieceCounts[key]++;
        }

        int answer = 0;

        foreach (List<int[]> blank in blanks)
        {
            string key = GetCanonicalKey(blank);

            if (!pieceCounts.ContainsKey(key) || pieceCounts[key] == 0)
            {
                continue;
            }

            pieceCounts[key]--;
            answer += blank.Count;
        }

        return answer;
    }

    private List<List<int[]>> FindShapes(int[,] board, int target)
    {
        bool[,] visited = new bool[n, n];
        List<List<int[]>> shapes = new List<List<int[]>>();

        for (int row = 0; row < n; row++)
        {
            for (int col = 0; col < n; col++)
            {
                if (visited[row, col] || board[row, col] != target)
                {
                    continue;
                }

                shapes.Add(Bfs(board, visited, row, col, target));
            }
        }

        return shapes;
    }

    private List<int[]> Bfs(int[,] board, bool[,] visited, int startRow, int startCol, int target)
    {
        Queue<int[]> queue = new Queue<int[]>();
        List<int[]> cells = new List<int[]>();

        queue.Enqueue(new int[] { startRow, startCol });
        visited[startRow, startCol] = true;

        while (queue.Count > 0)
        {
            int[] current = queue.Dequeue();
            int row = current[0];
            int col = current[1];

            cells.Add(new int[] { row, col });

            for (int i = 0; i < 4; i++)
            {
                int nextRow = row + dr[i];
                int nextCol = col + dc[i];

                if (nextRow < 0 || nextRow >= n || nextCol < 0 || nextCol >= n)
                {
                    continue;
                }

                if (visited[nextRow, nextCol] || board[nextRow, nextCol] != target)
                {
                    continue;
                }

                visited[nextRow, nextCol] = true;
                queue.Enqueue(new int[] { nextRow, nextCol });
            }
        }

        return Normalize(cells);
    }

    private string GetCanonicalKey(List<int[]> shape)
    {
        List<string> keys = new List<string>();
        List<int[]> current = shape;

        for (int i = 0; i < 4; i++)
        {
            current = Normalize(current);
            keys.Add(ToKey(current));
            current = Rotate(current);
        }

        keys.Sort(string.CompareOrdinal);
        return keys[0];
    }

    private string ToKey(List<int[]> shape)
    {
        List<string> parts = new List<string>();

        foreach (int[] cell in shape)
        {
            parts.Add(cell[0] + "," + cell[1]);
        }

        return string.Join(";", parts.ToArray());
    }

    private List<int[]> Rotate(List<int[]> shape)
    {
        List<int[]> rotated = new List<int[]>();

        foreach (int[] cell in shape)
        {
            rotated.Add(new int[] { cell[1], -cell[0] });
        }

        return rotated;
    }

    private List<int[]> Normalize(List<int[]> shape)
    {
        int minRow = int.MaxValue;
        int minCol = int.MaxValue;

        foreach (int[] cell in shape)
        {
            if (cell[0] < minRow)
            {
                minRow = cell[0];
            }

            if (cell[1] < minCol)
            {
                minCol = cell[1];
            }
        }

        List<int[]> normalized = new List<int[]>();

        foreach (int[] cell in shape)
        {
            normalized.Add(new int[] { cell[0] - minRow, cell[1] - minCol });
        }

        normalized.Sort((a, b) =>
        {
            if (a[0] == b[0])
            {
                return a[1].CompareTo(b[1]);
            }

            return a[0].CompareTo(b[0]);
        });

        return normalized;
    }
}
```

---

## 🔎 코드 해설

### 1. 퍼즐 조각을 키로 저장

```csharp
Dictionary<string, int> pieceCounts = new Dictionary<string, int>();
```

같은 모양의 퍼즐 조각이 여러 개 있을 수 있습니다.

그래서 키마다 개수를 저장합니다.

---

### 2. 회전 표준 키 만들기

```csharp
for (int i = 0; i < 4; i++)
{
    current = Normalize(current);
    keys.Add(ToKey(current));
    current = Rotate(current);
}
```

현재 모양을 0도, 90도, 180도, 270도로 회전하며 문자열 키를 만듭니다.

그중 가장 앞서는 키를 대표 키로 사용합니다.

---

### 3. 빈 공간과 조각 매칭

```csharp
if (!pieceCounts.ContainsKey(key) || pieceCounts[key] == 0)
{
    continue;
}

pieceCounts[key]--;
answer += blank.Count;
```

빈 공간의 표준 키와 같은 퍼즐 조각이 남아 있다면 채울 수 있습니다.

사용한 조각의 개수를 1 줄이고, 빈 공간의 칸 수를 정답에 더합니다.

---

## ⏱️ 시간 복잡도

`N`은 보드 한 변의 길이, `S`는 추출된 모양의 개수, `K`는 한 모양의 최대 칸 수입니다.

시간 복잡도: `O(N^2 + S * K log K)`

BFS로 보드와 테이블을 훑는 데 `O(N^2)`이 걸립니다.

각 모양은 4번 회전하며 정규화하므로 `O(K log K)` 정도가 걸립니다.

`K`는 최대 6이므로 실제로는 매우 작습니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N^2)`

방문 배열, BFS 큐, 모양 목록, `Dictionary`가 필요합니다.

전체적으로 보드 크기에 비례하는 공간을 사용합니다.

---

## ✅ 정리

이 문제는 다음 세 가지를 정확히 처리하면 됩니다.

```text
BFS로 빈 공간과 퍼즐 조각을 찾는다.
모양 좌표를 정규화해서 위치 차이를 없앤다.
4가지 회전을 비교하되, 뒤집기는 하지 않는다.
```

처음에는 직접 회전 비교 풀이가 이해하기 좋습니다.

실전에서는 회전 표준 키를 만들어 `Dictionary`로 매칭하면 더 깔끔하게 풀 수 있습니다. 🚀
