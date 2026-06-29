# 🔤 단어 변환 — C# 학습자료

## 📌 1. 문제 핵심

시작 단어 `begin`에서 목표 단어 `target`으로 변환하려고 합니다.

단어를 바꿀 때는 두 가지 규칙을 지켜야 합니다.

```text
한 번에 한 글자만 바꿀 수 있다.
words 배열 안에 있는 단어로만 바꿀 수 있다.
```

목표는 `begin`에서 `target`까지 가는 가장 짧은 변환 횟수를 구하는 것입니다.

변환할 수 없다면 `0`을 반환합니다. 🎯

---

# 🔍 예제 이해하기

```text
begin = "hit"
target = "cog"
words = ["hot", "dot", "dog", "lot", "log", "cog"]
```

가능한 변환 과정 중 하나는 다음과 같습니다.

```text
hit -> hot -> dot -> dog -> cog
```

변환은 총 4번 일어납니다.

```text
hit에서 hot으로 1번
hot에서 dot으로 2번
dot에서 dog로 3번
dog에서 cog로 4번
```

따라서 정답은 `4`입니다. ✅

---

## ⚠️ 주의할 점

단어를 아무렇게나 바꿀 수 없습니다.

두 단어가 서로 변환 가능하려면 정확히 한 글자만 달라야 합니다.

예를 들어:

```text
hit -> hot
```

은 `i`와 `o` 한 글자만 다르므로 가능합니다.

하지만:

```text
hit -> cog
```

은 세 글자가 모두 다르므로 한 번에 변환할 수 없습니다.

또한 `target`이 `words` 안에 없다면 도달할 수 없습니다. 이 경우 정답은 `0`입니다. 🚧

---

# 풀이 1. 이해하기 쉬운 방법 — BFS

## 💡 아이디어

이 문제는 단어들을 그래프처럼 생각하면 쉽습니다.

각 단어는 하나의 노드입니다.

한 글자만 다른 단어끼리는 간선으로 연결되어 있다고 볼 수 있습니다.

```text
hit -- hot -- dot -- dog -- cog
```

가장 적은 변환 횟수를 찾아야 하므로 BFS를 사용합니다.

BFS는 시작점에서 가까운 단어부터 확인하기 때문에, `target`을 처음 만났을 때의 단계 수가 최단 변환 횟수입니다. 🌱

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(string begin, string target, string[] words)
    {
        bool[] visited = new bool[words.Length];
        Queue<string> wordQueue = new Queue<string>();
        Queue<int> countQueue = new Queue<int>();

        wordQueue.Enqueue(begin);
        countQueue.Enqueue(0);

        while (wordQueue.Count > 0)
        {
            string current = wordQueue.Dequeue();
            int count = countQueue.Dequeue();

            if (current == target)
            {
                return count;
            }

            for (int i = 0; i < words.Length; i++)
            {
                if (visited[i])
                {
                    continue;
                }

                if (!CanConvert(current, words[i]))
                {
                    continue;
                }

                visited[i] = true;
                wordQueue.Enqueue(words[i]);
                countQueue.Enqueue(count + 1);
            }
        }

        return 0;
    }

    private bool CanConvert(string from, string to)
    {
        int differentCount = 0;

        for (int i = 0; i < from.Length; i++)
        {
            if (from[i] != to[i])
            {
                differentCount++;
            }

            if (differentCount > 1)
            {
                return false;
            }
        }

        return differentCount == 1;
    }
}
```

---

## 🔎 코드 해설

### 1. 큐에 단어와 변환 횟수 넣기

```csharp
Queue<string> wordQueue = new Queue<string>();
Queue<int> countQueue = new Queue<int>();
```

현재 단어와 현재까지의 변환 횟수를 함께 관리합니다.

처음에는 아직 변환하지 않았으므로 `begin`의 변환 횟수는 `0`입니다.

```csharp
wordQueue.Enqueue(begin);
countQueue.Enqueue(0);
```

---

### 2. target을 만나면 바로 반환

```csharp
if (current == target)
{
    return count;
}
```

BFS는 변환 횟수가 적은 단어부터 확인합니다.

따라서 `target`을 처음 만났을 때의 `count`가 정답입니다. ✨

---

### 3. 변환 가능한 단어 찾기

```csharp
if (!CanConvert(current, words[i]))
{
    continue;
}
```

현재 단어에서 `words[i]`로 한 번에 바꿀 수 있는지 확인합니다.

한 글자만 다르면 변환할 수 있습니다.

---

### 4. 한 글자 차이 판정

```csharp
private bool CanConvert(string from, string to)
{
    int differentCount = 0;

    for (int i = 0; i < from.Length; i++)
    {
        if (from[i] != to[i])
        {
            differentCount++;
        }

        if (differentCount > 1)
        {
            return false;
        }
    }

    return differentCount == 1;
}
```

두 단어를 앞에서부터 비교하면서 다른 글자의 개수를 셉니다.

다른 글자가 2개 이상이면 한 번에 변환할 수 없으므로 바로 `false`를 반환합니다.

마지막에 다른 글자가 정확히 1개라면 `true`입니다. 🔍

---

## ⏱️ 시간 복잡도

`N`은 `words`의 길이, `L`은 단어 하나의 길이입니다.

시간 복잡도: `O(N^2 * L)`

BFS에서 각 단어를 최대 한 번 방문합니다.

각 방문마다 전체 `words`를 훑으며 변환 가능한지 확인하고, 단어 비교에 `O(L)`이 걸립니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N)`

방문 배열 `visited`가 `O(N)` 공간을 사용합니다.

큐에도 최악의 경우 `words`에 있는 단어들이 들어갈 수 있으므로 `O(N)` 공간을 사용합니다.

---

# 풀이 2. 짧은 코드 버전 — LINQ로 한 글자 차이 확인

## 💡 아이디어

전체 흐름은 BFS와 같습니다.

다만 방문 여부와 거리를 `Dictionary` 하나로 관리합니다.

그리고 두 단어가 한 글자만 다른지 확인하는 부분을 LINQ로 짧게 표현합니다.

```csharp
a.Where((ch, i) => ch != b[i]).Count() == 1
```

이 코드는 `a`의 각 글자를 보면서 같은 위치의 `b` 글자와 다른 경우만 세는 뜻입니다. 🧠

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(string begin, string target, string[] words)
    {
        Queue<string> queue = new Queue<string>();
        Dictionary<string, int> distance = new Dictionary<string, int>();

        queue.Enqueue(begin);
        distance[begin] = 0;

        while (queue.Count > 0)
        {
            string current = queue.Dequeue();

            foreach (string next in words.Where(word => !distance.ContainsKey(word) && IsOneDifferent(current, word)))
            {
                distance[next] = distance[current] + 1;

                if (next == target)
                {
                    return distance[next];
                }

                queue.Enqueue(next);
            }
        }

        return 0;
    }

    private bool IsOneDifferent(string a, string b)
    {
        return a.Where((ch, i) => ch != b[i]).Count() == 1;
    }
}
```

---

## 🔎 코드 해설

### 1. 거리 Dictionary

```csharp
Dictionary<string, int> distance = new Dictionary<string, int>();
```

`distance[word]`는 `begin`에서 `word`까지 변환하는 데 필요한 최소 횟수입니다.

또한 `distance`에 들어 있는 단어는 이미 방문한 단어로 볼 수 있습니다.

---

### 2. 아직 방문하지 않았고 한 글자만 다른 단어 찾기

```csharp
words.Where(word => !distance.ContainsKey(word) && IsOneDifferent(current, word))
```

아직 방문하지 않은 단어 중에서 현재 단어와 한 글자만 다른 단어를 고릅니다.

이 단어들은 현재 단어에서 한 번 더 변환해서 갈 수 있는 후보입니다.

---

### 3. 거리 갱신

```csharp
distance[next] = distance[current] + 1;
```

현재 단어까지 온 횟수보다 1번 더 변환하면 `next`에 도착합니다.

BFS이므로 처음 기록되는 거리가 최단거리입니다.

---

## ⏱️ 시간 복잡도

`N`은 `words`의 길이, `L`은 단어 하나의 길이입니다.

시간 복잡도: `O(N^2 * L)`

각 단어를 꺼낼 때마다 `words`를 훑고, 한 글자 차이 판정에 `O(L)`이 걸립니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N)`

거리 정보를 저장하는 `Dictionary`와 BFS 큐가 `O(N)` 공간을 사용합니다.

---

## ✅ 정리

이 문제는 단어를 노드로 보고, 한 글자만 다른 단어끼리 연결된 그래프로 생각하면 됩니다.

최소 변환 횟수를 찾아야 하므로 BFS가 가장 잘 맞습니다.

핵심은 다음입니다.

```text
현재 단어에서 한 글자만 다른 단어로 이동한다.
BFS로 가까운 변환부터 확인한다.
target을 처음 만나면 그때의 단계 수가 정답이다.
```

DFS로도 모든 경로를 탐색할 수는 있지만, 최단거리는 BFS가 훨씬 자연스럽습니다. 🚀
