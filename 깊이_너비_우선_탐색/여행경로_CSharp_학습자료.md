# ✈️ 여행경로 — C# 학습자료

## 📌 1. 문제 핵심

항공권 목록이 주어집니다.

각 항공권은 다음 뜻입니다.

```text
[출발 공항, 도착 공항]
```

항상 `"ICN"` 공항에서 출발하고, 주어진 항공권을 모두 정확히 한 번씩 사용해야 합니다.

가능한 여행 경로가 여러 개라면, 알파벳 순서로 가장 앞서는 경로를 반환해야 합니다. 🎯

---

# 🔍 예제 이해하기

```text
tickets = [
  ["ICN", "SFO"],
  ["ICN", "ATL"],
  ["SFO", "ATL"],
  ["ATL", "ICN"],
  ["ATL", "SFO"]
]
```

가능한 경로 중 하나는 다음과 같습니다.

```text
ICN -> SFO -> ATL -> ICN -> ATL -> SFO
```

하지만 알파벳 순서로 더 앞서는 경로가 있습니다.

```text
ICN -> ATL -> ICN -> SFO -> ATL -> SFO
```

따라서 두 번째 경로를 반환해야 합니다. ✅

---

## ⚠️ 주의할 점

이 문제는 단순히 갈 수 있는 공항을 계속 고르는 문제가 아닙니다.

반드시 모든 항공권을 사용해야 합니다.

예를 들어 지금 알파벳 순서가 가장 빠른 공항으로 이동했더라도, 나중에 남은 항공권을 모두 사용할 수 없다면 그 선택은 실패입니다.

그래서 쉬운 방식으로는 DFS 백트래킹을 사용할 수 있습니다.

다만 항공권 수가 많을 수 있으므로, 더 효율적인 풀이에서는 “모든 간선을 한 번씩 사용하는 경로”를 만드는 방식으로 접근합니다. 🧭

---

# 풀이 1. 이해하기 쉬운 방법 — DFS 백트래킹

## 💡 아이디어

항공권을 도착 공항 기준으로 알파벳 순서대로 정렬합니다.

그 다음 `"ICN"`에서 시작해 사용할 수 있는 항공권을 하나씩 선택합니다.

선택한 항공권은 방문 처리하고, 다음 공항으로 이동합니다.

모든 항공권을 사용했다면 경로가 완성됩니다.

정렬된 순서대로 시도하므로, 처음 완성되는 경로가 알파벳 순서로 가장 앞서는 경로입니다. 🌱

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    string[,] tickets;
    int[] order;
    bool[] used;
    List<string> path = new List<string>();
    string[] answer;

    public string[] solution(string[,] tickets)
    {
        this.tickets = tickets;
        int n = tickets.GetLength(0);

        order = Enumerable.Range(0, n)
            .OrderBy(i => tickets[i, 1])
            .ThenBy(i => tickets[i, 0])
            .ToArray();
        used = new bool[n];
        path.Add("ICN");
        Dfs("ICN", 0);

        return answer;
    }

    bool Dfs(string current, int count)
    {
        if (count == used.Length)
        {
            answer = path.ToArray();
            return true;
        }

        foreach (int i in order)
        {
            if (used[i] || tickets[i, 0] != current)
            {
                continue;
            }

            used[i] = true;
            path.Add(tickets[i, 1]);

            if (Dfs(tickets[i, 1], count + 1))
            {
                return true;
            }

            path.RemoveAt(path.Count - 1);
            used[i] = false;
        }

        return false;
    }
}
```

---

## 🔎 코드 해설

### 1. 항공권 정렬

```csharp
order = Enumerable.Range(0, n)
    .OrderBy(i => tickets[i, 1])
    .ThenBy(i => tickets[i, 0])
    .ToArray();
```

도착 공항 이름이 알파벳 순서로 앞서는 항공권부터 시도합니다.

현재 공항에서 갈 수 있는 후보들 중 알파벳 순서가 빠른 도착지를 먼저 고르기 위해서입니다.

---

### 2. 경로 시작

```csharp
path.Add("ICN");
```

문제에서 항상 `"ICN"`에서 출발한다고 했으므로 경로의 첫 공항은 `"ICN"`입니다.

---

### 3. 모든 항공권을 사용한 경우

```csharp
if (count == used.Length)
{
    answer = path.ToArray();
    return true;
}
```

사용한 항공권 수가 전체 항공권 수와 같다면 여행 경로가 완성된 것입니다.

이때 현재까지 만든 `path`를 정답으로 저장합니다.

---

### 4. 항공권 선택과 되돌리기

```csharp
used[i] = true;
path.Add(tickets[i, 1]);

if (Dfs(tickets[i, 1], count + 1))
{
    return true;
}

path.RemoveAt(path.Count - 1);
used[i] = false;
```

항공권을 하나 사용해 다음 공항으로 이동합니다.

그 선택으로 끝까지 갈 수 없다면, 방금 선택을 취소하고 다른 항공권을 시도합니다.

이 되돌리기 과정이 백트래킹입니다. 🔁

---

## ⏱️ 시간 복잡도

`E`는 항공권의 개수입니다.

시간 복잡도: 최악의 경우 매우 큽니다.

정렬은 `O(E log E)`입니다.

하지만 DFS 백트래킹은 선택이 많이 꼬이면 여러 경로를 다시 시도할 수 있어 최악의 경우 지수적으로 커질 수 있습니다.

그래서 이 풀이는 아이디어를 이해하기에는 좋지만, 큰 입력에서는 다음 풀이를 더 추천합니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(E)`

방문 배열, 경로 배열, 재귀 호출 스택이 항공권 수에 비례합니다.

---

# 풀이 2. 효율적인 방법 — 정렬된 인접 리스트 + 스택

## 💡 아이디어

이 문제는 그래프에서 모든 항공권을 한 번씩 사용하는 경로를 찾는 문제입니다.

항공권은 그래프의 간선처럼 볼 수 있습니다.

```text
ICN -> ATL
ATL -> SFO
```

각 출발 공항마다 도착 공항 목록을 모아 둡니다.

그리고 도착 공항을 알파벳 역순으로 정렬합니다.

이렇게 하면 리스트의 마지막에서 꺼낼 때 알파벳 순서가 가장 빠른 공항을 쉽게 꺼낼 수 있습니다.

스택에는 현재 따라가고 있는 여행 경로를 넣습니다.

현재 공항에서 더 갈 수 있는 항공권이 있으면 그 항공권을 사용해 다음 공항으로 이동합니다.

더 이상 갈 수 있는 항공권이 없으면 그 공항을 경로에 추가합니다.

마지막에 경로를 뒤집으면 정답이 됩니다. ✨

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public string[] solution(string[,] tickets)
    {
        var graph = new Dictionary<string, List<string>>();

        for (int i = 0; i < tickets.GetLength(0); i++)
        {
            string from = tickets[i, 0];
            string to = tickets[i, 1];

            if (!graph.ContainsKey(from))
            {
                graph[from] = new List<string>();
            }

            graph[from].Add(to);
        }

        foreach (var list in graph.Values)
        {
            list.Sort((a, b) => string.CompareOrdinal(b, a));
        }

        var stack = new Stack<string>();
        var route = new List<string>();
        stack.Push("ICN");

        while (stack.Count > 0)
        {
            string current = stack.Peek();

            if (graph.ContainsKey(current) && graph[current].Count > 0)
            {
                var nexts = graph[current];
                string next = nexts[nexts.Count - 1];
                nexts.RemoveAt(nexts.Count - 1);

                stack.Push(next);
            }
            else
            {
                route.Add(stack.Pop());
            }
        }

        route.Reverse();
        return route.ToArray();
    }
}
```

---

## 🔎 코드 해설

### 1. 출발 공항별 도착지 모으기

```csharp
Dictionary<string, List<string>> graph = new Dictionary<string, List<string>>();
```

`graph["ICN"]`은 `"ICN"`에서 출발해 갈 수 있는 공항들의 목록입니다.

같은 출발 공항에서 여러 항공권이 있을 수 있으므로 `List<string>`으로 저장합니다.

---

### 2. 도착지를 역순 정렬하기

```csharp
list.Sort((a, b) => string.CompareOrdinal(b, a));
```

도착지를 알파벳 역순으로 정렬합니다.

예를 들어 다음처럼 저장합니다.

```text
["SFO", "ATL"]
```

그러면 리스트의 마지막 값을 꺼낼 때 `"ATL"`을 먼저 사용할 수 있습니다.

```csharp
string next = nexts[nexts.Count - 1];
nexts.RemoveAt(nexts.Count - 1);
```

`List`의 마지막 원소 제거는 빠르기 때문에 효율적입니다. ⚡

---

### 3. 스택으로 현재 경로 따라가기

```csharp
stack.Push("ICN");

while (stack.Count > 0)
{
    string current = stack.Peek();
    ...
}
```

스택의 맨 위가 현재 공항입니다.

현재 공항에서 출발하는 항공권이 남아 있다면 그 항공권을 사용해 다음 공항을 스택에 넣습니다.

항공권을 리스트에서 제거했으므로 같은 항공권을 두 번 쓰지 않습니다.

---

### 4. 더 갈 곳이 없을 때 경로에 추가하기

```csharp
route.Add(stack.Pop());
```

현재 공항에서 더 사용할 항공권이 없다면 그 공항은 경로의 뒤쪽부터 확정됩니다.

그래서 경로가 거꾸로 만들어집니다.

마지막에 뒤집으면 출발 공항부터 시작하는 여행 경로가 됩니다.

```csharp
route.Reverse();
```

이 방식은 모든 항공권을 정확히 한 번씩 사용해야 하는 문제에 잘 맞습니다.

---

## ⏱️ 시간 복잡도

`E`는 항공권의 개수입니다.

시간 복잡도: `O(E log E)`

출발 공항별 도착지를 정렬하는 데 전체적으로 `O(E log E)`가 걸립니다.

스택 기반 탐색에서는 각 항공권을 한 번씩 제거하고 사용하므로 `O(E)`입니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(E)`

그래프에 모든 항공권을 저장합니다.

경로 배열과 명시적인 `Stack`도 항공권 수에 비례할 수 있습니다.

---

## ✅ 정리

이 문제의 핵심은 다음입니다.

```text
ICN에서 시작한다.
모든 항공권을 정확히 한 번씩 사용한다.
가능한 경로가 여러 개라면 알파벳 순서가 가장 앞서는 경로를 고른다.
```

처음 배울 때는 DFS 백트래킹으로 “항공권을 선택하고, 안 되면 되돌린다”는 감각을 익히기 좋습니다.

실전에서는 항공권 수가 많을 수 있으므로, 정렬된 인접 리스트를 이용해 모든 항공권을 한 번씩 제거하며 경로를 만드는 풀이가 더 안정적입니다. 🚀
