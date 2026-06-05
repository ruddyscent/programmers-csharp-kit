# 🔁 이중우선순위큐 — C# 학습자료

## 📌 1. 문제 핵심

이중 우선순위 큐는 일반 큐와 달리 다음 연산을 처리해야 합니다.

| 명령어 | 의미 |
|---|---|
| `I 숫자` | 숫자를 큐에 삽입 |
| `D 1` | 최댓값 삭제 |
| `D -1` | 최솟값 삭제 |

모든 연산을 처리한 뒤:

```text
큐가 비어 있으면 [0, 0]
큐가 비어 있지 않으면 [최댓값, 최솟값]
```

을 반환해야 합니다. 🎯

---

## ⚠️ 주의할 점

### 1. 같은 숫자가 여러 번 들어올 수 있음

예를 들어:

```text
I 5
I 5
D 1
```

이면 `5`가 하나만 삭제되고, `5` 하나는 남아 있어야 합니다.

즉, 단순히 `HashSet<int>`처럼 값만 저장하면 안 됩니다. 🚫

숫자마다 **개수**를 관리해야 합니다.

---

### 2. 빈 큐에서 삭제 명령이 들어오면 무시

예를 들어:

```text
D 1
D -1
```

큐가 비어 있다면 아무 일도 하지 않아야 합니다.

---

### 3. 최댓값과 최솟값을 모두 빠르게 찾아야 함

최솟값만 빠르게 찾는 `PriorityQueue` 하나만으로는 부족합니다.

이 문제에서는 다음 두 작업이 모두 필요합니다.

```text
최솟값 삭제
최댓값 삭제
```

그래서 C#에서는 `SortedDictionary<int, int>`를 쓰면 좋습니다. ✅

---

# 🧠 핵심 자료구조: SortedDictionary

`SortedDictionary<TKey, TValue>`는 key를 정렬된 상태로 관리합니다.

이 문제에서는 다음처럼 사용합니다.

```text
숫자 → 개수
```

예를 들어 큐에 다음 값들이 있다고 합시다.

```text
[-45, 45, 333]
```

SortedDictionary에는 다음처럼 저장됩니다.

```text
-45 → 1
45  → 1
333 → 1
```

정렬된 상태이므로:

```text
최솟값 = 가장 앞 key
최댓값 = 가장 뒤 key
```

가 됩니다. 🧺

---

## 🔑 최솟값과 최댓값 구하기

LINQ를 사용하면 다음처럼 쓸 수 있습니다.

```csharp
int min = map.First().Key;
int max = map.Last().Key;
```

`SortedDictionary`는 key가 정렬되어 있으므로 `First()`가 최솟값, `Last()`가 최댓값입니다.

단, `First()`와 `Last()`를 쓰려면:

```csharp
using System.Linq;
```

가 필요합니다. ⚠️

---

# 🔍 예제 1 이해하기

```text
operations = ["I 16", "I -5643", "D -1", "D 1", "D 1", "I 123", "D -1"]
```

차례대로 처리해 봅시다.

```text
I 16     → [16]
I -5643  → [-5643, 16]
D -1     → 최솟값 -5643 삭제 → [16]
D 1      → 최댓값 16 삭제 → []
D 1      → 빈 큐이므로 무시 → []
I 123    → [123]
D -1     → 최솟값 123 삭제 → []
```

마지막에 큐가 비어 있습니다.

```text
return [0, 0]
```

✅

---

# 🔍 예제 2 이해하기

```text
operations = ["I -45", "I 653", "D 1", "I -642", "I 45", "I 97", "D 1", "D -1", "I 333"]
```

처리 결과:

```text
I -45  → [-45]
I 653  → [-45, 653]
D 1    → 653 삭제 → [-45]
I -642 → [-642, -45]
I 45   → [-642, -45, 45]
I 97   → [-642, -45, 45, 97]
D 1    → 97 삭제 → [-642, -45, 45]
D -1   → -642 삭제 → [-45, 45]
I 333  → [-45, 45, 333]
```

최댓값은 `333`, 최솟값은 `-45`입니다.

```text
return [333, -45]
```

🎉

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — SortedDictionary로 개수 관리하기

## 💡 아이디어

1. `SortedDictionary<int, int>`를 만든다.
2. 삽입 명령이면 숫자의 개수를 1 증가시킨다.
3. 삭제 명령이면 최댓값 또는 최솟값의 개수를 1 감소시킨다.
4. 개수가 0이 되면 Dictionary에서 제거한다.
5. 마지막에 비어 있으면 `[0, 0]`, 아니면 `[최댓값, 최솟값]`을 반환한다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int[] solution(string[] operations)
    {
        SortedDictionary<int, int> map = new SortedDictionary<int, int>();

        foreach (string operation in operations)
        {
            string[] parts = operation.Split(' ');
            string command = parts[0];
            int number = int.Parse(parts[1]);

            if (command == "I")
            {
                if (!map.ContainsKey(number))
                {
                    map[number] = 0;
                }

                map[number]++;
            }
            else
            {
                if (map.Count == 0)
                {
                    continue;
                }

                int target = number == 1 ? map.Last().Key : map.First().Key;

                map[target]--;

                if (map[target] == 0)
                {
                    map.Remove(target);
                }
            }
        }

        if (map.Count == 0)
        {
            return new int[] { 0, 0 };
        }

        return new int[] { map.Last().Key, map.First().Key };
    }
}
```

---

## 🔎 코드 해설

### 1. 숫자와 개수를 저장

```csharp
SortedDictionary<int, int> map = new SortedDictionary<int, int>();
```

숫자를 key로, 해당 숫자의 개수를 value로 저장합니다.

예를 들어:

```text
I 5
I 5
I 3
```

이면:

```text
3 → 1
5 → 2
```

처럼 저장됩니다.

---

### 2. 명령어 분리

```csharp
string[] parts = operation.Split(' ');
string command = parts[0];
int number = int.Parse(parts[1]);
```

예를 들어:

```text
"I 16"
```

은 다음처럼 나뉩니다.

```text
command = "I"
number = 16
```

---

### 3. 삽입 처리

```csharp
if (command == "I")
{
    if (!map.ContainsKey(number))
    {
        map[number] = 0;
    }

    map[number]++;
}
```

처음 들어온 숫자라면 개수를 0으로 만들고, 그다음 1 증가시킵니다.

이미 있는 숫자라면 개수만 증가시킵니다. ➕

---

### 4. 삭제 처리

```csharp
if (map.Count == 0)
{
    continue;
}
```

큐가 비어 있으면 삭제 명령을 무시합니다.

---

### 5. 최댓값 또는 최솟값 선택

```csharp
int target = number == 1 ? map.Last().Key : map.First().Key;
```

`D 1`이면 최댓값 삭제입니다.

```csharp
map.Last().Key
```

`D -1`이면 최솟값 삭제입니다.

```csharp
map.First().Key
```

---

### 6. 하나만 삭제하기

```csharp
map[target]--;
```

해당 숫자의 개수를 1 줄입니다.

개수가 0이 되면 완전히 제거합니다.

```csharp
if (map[target] == 0)
{
    map.Remove(target);
}
```

---

## ⏱️ 시간 복잡도

연산 개수를 `n`이라고 하겠습니다.

`SortedDictionary`의 삽입, 삭제, 탐색은 보통 다음 시간에 처리됩니다.

```text
O(log k)
```

`k`는 서로 다른 숫자의 개수입니다.

전체 시간 복잡도는:

```text
O(n log k)
```

최악의 경우 `k = n`이므로:

```text
O(n log n)
```

입니다.

---

## 📦 공간 복잡도

서로 다른 숫자와 개수를 저장합니다.

```text
O(k)
```

최악의 경우:

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — GetValueOrDefault 사용하기

## 💡 아이디어

풀이 1과 같은 `SortedDictionary` 방식입니다.

다만 삽입 부분을 더 짧게 작성합니다.

```csharp
map[number] = map.GetValueOrDefault(number) + 1;
```

`GetValueOrDefault()`는 key가 없으면 기본값 `0`을 반환합니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int[] solution(string[] operations)
    {
        var map = new SortedDictionary<int, int>();

        foreach (var op in operations)
        {
            var parts = op.Split(' ');
            int value = int.Parse(parts[1]);

            if (parts[0] == "I")
            {
                map[value] = map.GetValueOrDefault(value) + 1;
                continue;
            }

            if (map.Count == 0)
            {
                continue;
            }

            int key = value == 1 ? map.Last().Key : map.First().Key;

            if (--map[key] == 0)
            {
                map.Remove(key);
            }
        }

        return map.Count == 0
            ? new int[] { 0, 0 }
            : new int[] { map.Last().Key, map.First().Key };
    }
}
```

---

## ✨ 코드 의미

### 1. 삽입을 짧게 처리

```csharp
map[value] = map.GetValueOrDefault(value) + 1;
```

기존 풀이의 다음 부분을 줄인 것입니다.

```csharp
if (!map.ContainsKey(value))
{
    map[value] = 0;
}

map[value]++;
```

---

### 2. 삭제 후 바로 0인지 확인

```csharp
if (--map[key] == 0)
{
    map.Remove(key);
}
```

먼저 개수를 1 줄이고, 그 결과가 0이면 제거합니다.

---

### 3. 마지막 결과 반환

```csharp
return map.Count == 0
    ? new int[] { 0, 0 }
    : new int[] { map.Last().Key, map.First().Key };
```

삼항 연산자를 사용해 결과를 간단하게 반환합니다. ⚡

---

## ⚠️ 프로그래머스에서 실행할 때 주의

이 풀이에서는 LINQ의 `First()`, `Last()`를 사용합니다.

따라서 반드시 아래 네임스페이스를 포함해야 합니다.

```csharp
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

---

## ⏱️ 시간 복잡도

각 연산마다 `SortedDictionary` 작업을 수행합니다.

```text
O(log n)
```

전체 연산 개수가 `n`이면:

```text
O(n log n)
```

입니다.

---

## 📦 공간 복잡도

서로 다른 숫자 개수만큼 저장합니다.

```text
O(n)
```

입니다.

---

# ❌ 참고: List 정렬 방식이 위험한 이유

간단하게 생각하면 `List<int>`에 숫자를 넣고, 삭제할 때마다 정렬해서 최댓값/최솟값을 지울 수도 있습니다.

하지만 operations 길이는 최대:

```text
1,000,000
```

입니다.

매번 정렬하면 시간 복잡도가 너무 커집니다.

```text
O(n² log n)
```

에 가까워질 수 있습니다. 🚨

따라서 이 문제에서는 정렬된 상태를 유지하는 자료구조가 필요합니다.

C#에서는 `SortedDictionary`가 가장 안정적인 선택입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | SortedDictionary 기본 풀이 | 흐름이 이해하기 쉽다 😊 | O(n log n) | O(n) |
| 풀이 2 | GetValueOrDefault 활용 | 코드가 짧다 🚀 | O(n log n) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 숫자의 개수를 관리하는 흐름이 잘 보입니다.
2. 중복 숫자를 하나씩 삭제하는 과정이 명확합니다.
3. 최댓값/최솟값 삭제 원리를 이해하기 좋습니다.
```

프로그래머스 제출용으로는 **풀이 2번**도 좋습니다.

짧고 깔끔하지만 `GetValueOrDefault()`와 `First()`, `Last()`에 익숙해야 합니다. 🌱

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
최댓값과 최솟값을 모두 빠르게 삭제해야 한다.
```

또한 같은 숫자가 여러 번 들어올 수 있으므로:

```text
숫자 → 개수
```

를 관리해야 합니다.

C#에서는 다음 자료구조를 사용하면 좋습니다.

```csharp
SortedDictionary<int, int>
```

흐름은 다음과 같습니다.

```text
1. I 숫자: 해당 숫자의 개수를 증가시킨다.
2. D 1: 가장 큰 key의 개수를 하나 줄인다.
3. D -1: 가장 작은 key의 개수를 하나 줄인다.
4. 개수가 0이 되면 key를 제거한다.
5. 마지막에 비어 있으면 [0, 0], 아니면 [최댓값, 최솟값] 반환.
```

🔁 “이중우선순위큐”는 정렬된 Dictionary와 중복 개수 관리가 핵심인 문제입니다.
