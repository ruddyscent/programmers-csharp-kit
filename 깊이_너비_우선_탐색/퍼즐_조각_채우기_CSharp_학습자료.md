# 🧩 퍼즐 조각 채우기 — C# 학습자료

## 📌 1. 문제 핵심

게임 보드의 빈칸(`0`)에 테이블의 퍼즐 조각(`1`)을 끼워 넣는 문제입니다.

퍼즐 조각은 회전할 수 있지만, 뒤집을 수는 없습니다.

따라서 해야 할 일은 다음입니다.

```text
game_board에서 빈 공간 모양을 찾는다.
table에서 퍼즐 조각 모양을 찾는다.
회전했을 때 같은 모양끼리 매칭한다.
```

전체 문제 조건과 원문은 프로그래머스 공식 문제 페이지에서 확인하세요.

- [퍼즐 조각 채우기](https://school.programmers.co.kr/learn/courses/30/lessons/84021)

---

# 🔍 핵심 아이디어

이 문제는 모양 비교가 핵심입니다.

같은 모양이라도 보드에서의 위치는 다를 수 있습니다.

그래서 모양 좌표를 항상 `(0, 0)` 근처로 옮겨서 비교합니다.

이 과정을 정규화라고 부릅니다. 🌱

예를 들어:

```text
(2, 3), (2, 4), (3, 3)
```

를 정규화하면:

```text
(0, 0), (0, 1), (1, 0)
```

이 됩니다.

또 조각은 회전할 수 있으므로, 0도, 90도, 180도, 270도 모양을 모두 만들어 봅니다.

그 4개 중 문자열로 봤을 때 가장 앞서는 값을 “대표 키”로 정하면, 같은 모양은 같은 키를 갖게 됩니다. 🔑

---

## ⚠️ 짧게 쓰기 위한 선택

제출용 코드 길이를 줄이기 위해 다음 방식을 씁니다.

1. BFS를 할 때 `visited` 배열을 따로 만들지 않고, 입력 배열 값을 바꿔 방문 처리합니다.
2. 좌표는 `(int r, int c)` 튜플로 저장합니다.
3. 모양 비교는 직접 리스트끼리 비교하지 않고, 대표 문자열 키로 처리합니다.
4. 퍼즐 조각 개수는 `Dictionary<string, int>`에 저장합니다.

이렇게 하면 직접 회전 비교 풀이보다 코드가 훨씬 짧아집니다. ✨

---

# 풀이 1. 이해하기 쉬운 방법 — BFS + 회전 대표 키

## 💡 아이디어

빈 공간과 퍼즐 조각을 각각 BFS로 찾습니다.

찾은 모양은 좌표 리스트로 저장하고, 정규화한 뒤 대표 키로 바꿉니다.

테이블의 퍼즐 조각 키를 `Dictionary`에 먼저 저장해 둔 다음, 게임 보드의 빈 공간 키와 맞는 조각이 있는지 확인합니다.

직접 모양끼리 여러 번 비교하지 않고 문자열 키로 비교해서 코드가 너무 길어지는 것을 막습니다. 👍

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    int n;
    int[] dr = { -1, 1, 0, 0 };
    int[] dc = { 0, 0, -1, 1 };

    public int solution(int[,] game_board, int[,] table)
    {
        n = game_board.GetLength(0);

        var pieces = new Dictionary<string, int>();

        foreach (var p in Find(table, 1))
        {
            string k = Key(p);
            pieces[k] = pieces.ContainsKey(k) ? pieces[k] + 1 : 1;
        }

        int answer = 0;

        foreach (var b in Find(game_board, 0))
        {
            string k = Key(b);

            if (!pieces.ContainsKey(k) || pieces[k] == 0)
            {
                continue;
            }

            pieces[k]--;
            answer += b.Count;
        }

        return answer;
    }

    List<List<(int r, int c)>> Find(int[,] board, int target)
    {
        var result = new List<List<(int r, int c)>>();

        for (int r = 0; r < n; r++)
        {
            for (int c = 0; c < n; c++)
            {
                if (board[r, c] != target)
                {
                    continue;
                }

                var q = new Queue<(int r, int c)>();
                var shape = new List<(int r, int c)>();

                q.Enqueue((r, c));
                board[r, c] = 1 - target;

                while (q.Count > 0)
                {
                    var now = q.Dequeue();
                    shape.Add(now);

                    for (int d = 0; d < 4; d++)
                    {
                        int nr = now.r + dr[d];
                        int nc = now.c + dc[d];

                        if (nr < 0 || nr >= n || nc < 0 || nc >= n || board[nr, nc] != target)
                        {
                            continue;
                        }

                        board[nr, nc] = 1 - target;
                        q.Enqueue((nr, nc));
                    }
                }

                result.Add(Norm(shape));
            }
        }

        return result;
    }

    string Key(List<(int r, int c)> shape)
    {
        var keys = new List<string>();

        for (int i = 0; i < 4; i++)
        {
            shape = Norm(shape);
            keys.Add(string.Join(";", shape.Select(p => p.r + "," + p.c)));
            shape = shape.Select(p => (r: p.c, c: -p.r)).ToList();
        }

        keys.Sort();
        return keys[0];
    }

    List<(int r, int c)> Norm(List<(int r, int c)> shape)
    {
        int minR = shape.Min(p => p.r);
        int minC = shape.Min(p => p.c);

        return shape
            .Select(p => (r: p.r - minR, c: p.c - minC))
            .OrderBy(p => p.r)
            .ThenBy(p => p.c)
            .ToList();
    }
}
```

---

## 🔎 코드 해설

### 1. 빈 공간과 퍼즐 조각 찾기

```csharp
Find(game_board, 0)
Find(table, 1)
```

`game_board`에서는 `0`으로 연결된 칸을 빈 공간으로 찾습니다.

`table`에서는 `1`로 연결된 칸을 퍼즐 조각으로 찾습니다.

BFS로 연결된 칸들을 하나의 `shape` 리스트에 담습니다.

---

### 2. 방문 처리 줄이기

```csharp
board[r, c] = 1 - target;
```

방문한 칸은 반대 값으로 바꿉니다.

`target`이 `1`이면 `0`으로, `target`이 `0`이면 `1`로 바뀝니다.

덕분에 별도의 `visited` 배열이 필요 없습니다. ✅

---

### 3. 대표 키 만들기

```csharp
shape = Norm(shape);
keys.Add(string.Join(";", shape.Select(p => p.r + "," + p.c)));
shape = shape.Select(p => (r: p.c, c: -p.r)).ToList();
```

현재 모양을 정규화한 뒤 문자열 키로 바꿉니다.

그 다음 `(r, c)`를 `(c, -r)`로 바꿔 90도 회전합니다.

이 과정을 4번 반복하면 가능한 모든 회전 모양을 확인할 수 있습니다.

---

### 4. Dictionary로 매칭하기

```csharp
pieces[k] = pieces.ContainsKey(k) ? pieces[k] + 1 : 1;
```

테이블의 퍼즐 조각을 대표 키별로 세어 둡니다.

빈 공간의 대표 키와 같은 조각이 남아 있으면 사용할 수 있습니다.

```csharp
pieces[k]--;
answer += b.Count;
```

조각을 하나 사용하고, 채운 칸 수를 정답에 더합니다. 🎉

---

## ⏱️ 시간 복잡도

`N`은 보드 한 변의 길이, `S`는 추출된 모양의 개수, `K`는 한 모양의 최대 칸 수입니다.

시간 복잡도: `O(N^2 + S * K log K)`

BFS로 `game_board`와 `table`을 훑는 데 `O(N^2)`이 걸립니다.

각 모양은 4번 회전하며 정규화하고, 정렬에 `O(K log K)`가 걸립니다.

이 문제에서 `K`는 최대 6이라 실제 부담은 작습니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N^2)`

BFS 큐, 추출한 모양 목록, `Dictionary`가 필요합니다.

입력 배열을 방문 처리에 재사용하므로 별도의 방문 배열 공간은 줄였습니다.

---

# 풀이 2. 짧은 코드 버전

## 💡 아이디어

풀이 1과 같은 방식입니다.

다만 `Sum`, `Enumerable.Range`, `Min` 같은 LINQ를 조금 더 사용해서 매칭과 대표 키 생성을 짧게 씁니다.

코딩테스트에서 코드 길이를 줄이고 싶을 때 참고하기 좋은 버전입니다. ✨

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    int n;
    int[] dr = { -1, 1, 0, 0 }, dc = { 0, 0, -1, 1 };

    public int solution(int[,] game_board, int[,] table)
    {
        n = game_board.GetLength(0);
        var pieces = new Dictionary<string, int>();

        foreach (var s in Shapes(table, 1))
        {
            string k = Key(s);
            pieces[k] = pieces.ContainsKey(k) ? pieces[k] + 1 : 1;
        }

        return Shapes(game_board, 0).Sum(s =>
        {
            string k = Key(s);

            if (!pieces.ContainsKey(k) || pieces[k] == 0)
            {
                return 0;
            }

            pieces[k]--;
            return s.Count;
        });
    }

    List<List<(int r, int c)>> Shapes(int[,] a, int v)
    {
        var res = new List<List<(int r, int c)>>();

        for (int r = 0; r < n; r++)
        for (int c = 0; c < n; c++)
        {
            if (a[r, c] != v)
            {
                continue;
            }

            var q = new Queue<(int r, int c)>();
            var s = new List<(int r, int c)>();
            q.Enqueue((r, c));
            a[r, c] = 1 - v;

            while (q.Count > 0)
            {
                var p = q.Dequeue();
                s.Add(p);

                for (int d = 0; d < 4; d++)
                {
                    int nr = p.r + dr[d], nc = p.c + dc[d];

                    if (nr < 0 || nr >= n || nc < 0 || nc >= n || a[nr, nc] != v)
                    {
                        continue;
                    }

                    a[nr, nc] = 1 - v;
                    q.Enqueue((nr, nc));
                }
            }

            res.Add(Norm(s));
        }

        return res;
    }

    string Key(List<(int r, int c)> s)
    {
        return Enumerable.Range(0, 4).Select(_ =>
        {
            s = Norm(s);
            string k = string.Join(";", s.Select(p => p.r + "," + p.c));
            s = s.Select(p => (r: p.c, c: -p.r)).ToList();
            return k;
        }).Min();
    }

    List<(int r, int c)> Norm(List<(int r, int c)> s)
    {
        int mr = s.Min(p => p.r), mc = s.Min(p => p.c);

        return s.Select(p => (r: p.r - mr, c: p.c - mc))
            .OrderBy(p => p.r)
            .ThenBy(p => p.c)
            .ToList();
    }
}
```

---

## ⏱️ 시간 복잡도

`N`은 보드 한 변의 길이, `S`는 추출된 모양의 개수, `K`는 한 모양의 최대 칸 수입니다.

시간 복잡도: `O(N^2 + S * K log K)`

풀이 1과 같은 작업을 더 짧게 쓴 코드이므로 복잡도는 같습니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N^2)`

BFS 큐, 모양 목록, `Dictionary`가 필요합니다.

---

## ✅ 정리

이 문제를 짧게 풀려면 “모양을 직접 비교”하기보다 “대표 키로 바꿔 비교”하는 편이 좋습니다.

핵심은 다음입니다.

```text
BFS로 연결된 모양을 찾는다.
모양을 정규화한다.
4번 회전한 키 중 가장 앞선 것을 대표 키로 쓴다.
Dictionary로 조각 개수를 세고 빈칸과 매칭한다.
```

코드는 짧아졌지만, 좌표 정규화와 회전 원리만 이해하면 충분히 따라갈 수 있습니다. 🚀
