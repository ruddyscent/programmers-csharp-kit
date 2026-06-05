# 🏃 완주하지 못한 선수 — C# 학습자료

## 📌 1. 문제 핵심

마라톤 참가자 명단 `participant`와 완주자 명단 `completion`이 주어집니다.

단 한 명만 완주하지 못했습니다.

우리가 해야 할 일은 다음입니다.

```text
participant에는 있지만 completion에는 부족한 이름 찾기
```

입니다. 🎯

## ⚠️ 주의할 점: 동명이인

이 문제에서 가장 중요한 조건은 이것입니다.

```text
참가자 중에는 동명이인이 있을 수 있습니다.
```

예를 들어:

```text
participant = ["mislav", "stanko", "mislav", "ana"]
completion  = ["stanko", "ana", "mislav"]
```

`mislav`라는 이름은 참가자 명단에 2번 나옵니다.

하지만 완주자 명단에는 1번만 나옵니다.

따라서 완주하지 못한 선수는:

```text
"mislav"
```

입니다. ✅

단순히 “이 이름이 completion에 있나?”만 확인하면 안 됩니다.  
**이름의 개수**를 비교해야 합니다. 🧠

# 🔍 예제 이해하기

## 예제 1

```text
participant = ["leo", "kiki", "eden"]
completion  = ["eden", "kiki"]
```

`leo`만 완주자 명단에 없습니다.

```text
return "leo"
```

## 예제 2

```text
participant = ["marina", "josipa", "nikola", "vinko", "filipa"]
completion  = ["josipa", "filipa", "marina", "nikola"]
```

`vinko`만 완주하지 못했습니다.

```text
return "vinko"
```

## 예제 3

```text
participant = ["mislav", "stanko", "mislav", "ana"]
completion  = ["stanko", "ana", "mislav"]
```

`mislav`가 한 명 부족합니다.

```text
return "mislav"
```

# 🧠 풀이 1. 이해하기 쉬운 방법 — 정렬 후 비교하기

## 💡 아이디어

참가자 명단과 완주자 명단을 둘 다 정렬합니다.

그러면 같은 이름끼리 같은 위치에 오게 됩니다.

예를 들어:

```text
participant = ["mislav", "stanko", "mislav", "ana"]
completion  = ["stanko", "ana", "mislav"]
```

정렬하면:

```text
participant = ["ana", "mislav", "mislav", "stanko"]
completion  = ["ana", "mislav", "stanko"]
```

앞에서부터 비교합니다.

```text
ana     == ana
mislav  == mislav
mislav  != stanko
```

여기서 다른 이름인 `mislav`가 완주하지 못한 선수입니다. 🎯

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public string solution(string[] participant, string[] completion)
    {
        Array.Sort(participant);
        Array.Sort(completion);

        for (int i = 0; i < completion.Length; i++)
        {
            if (participant[i] != completion[i])
            {
                return participant[i];
            }
        }

        return participant[participant.Length - 1];
    }
}
```

## 🔎 코드 해설

### 1. 두 배열 정렬하기

```csharp
Array.Sort(participant);
Array.Sort(completion);
```

정렬하면 같은 이름끼리 나란히 오게 됩니다.

동명이인이 있어도 개수 차이가 나는 순간 위치가 어긋납니다. 🔍

### 2. 앞에서부터 비교하기

```csharp
for (int i = 0; i < completion.Length; i++)
{
    if (participant[i] != completion[i])
    {
        return participant[i];
    }
}
```

참가자 배열과 완주자 배열을 같은 위치끼리 비교합니다.

다른 이름이 처음 나타나는 곳이 바로 완주하지 못한 선수입니다.

### 3. 마지막 참가자가 답인 경우

```csharp
return participant[participant.Length - 1];
```

중간에서 다른 이름이 나오지 않았다면, 마지막 참가자가 완주하지 못한 선수입니다. ✅

## ⏱️ 시간 복잡도

정렬이 가장 큰 비용입니다.

```text
O(n log n)
```

`n`은 참가자 수입니다.

## 📦 공간 복잡도

별도의 큰 자료구조를 만들지 않습니다.

```text
O(1)
```

단, 정렬 내부 구현에 따라 약간의 추가 공간이 사용될 수 있습니다.

# 🚀 풀이 2. 빠른 방법 — Dictionary로 이름 개수 세기

## 💡 아이디어

참가자 이름이 몇 번 나왔는지 Dictionary에 저장합니다.

```text
이름 → 등장 횟수
```

먼저 참가자 명단을 보면서 이름 개수를 증가시킵니다.

그다음 완주자 명단을 보면서 이름 개수를 감소시킵니다.

마지막에 개수가 1 남은 이름이 완주하지 못한 선수입니다. 🏃

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public string solution(string[] participant, string[] completion)
    {
        Dictionary<string, int> count = new Dictionary<string, int>();

        foreach (string name in participant)
        {
            if (!count.ContainsKey(name))
            {
                count[name] = 0;
            }

            count[name]++;
        }

        foreach (string name in completion)
        {
            count[name]--;
        }

        foreach (var pair in count)
        {
            if (pair.Value > 0)
            {
                return pair.Key;
            }
        }

        return "";
    }
}
```

## 🔎 코드 해설

### 1. 참가자 이름 개수 세기

```csharp
foreach (string name in participant)
{
    if (!count.ContainsKey(name))
    {
        count[name] = 0;
    }

    count[name]++;
}
```

예를 들어:

```text
["mislav", "stanko", "mislav", "ana"]
```

라면 Dictionary는 다음처럼 됩니다.

```text
mislav → 2
stanko → 1
ana    → 1
```

### 2. 완주자 이름 개수 빼기

```csharp
foreach (string name in completion)
{
    count[name]--;
}
```

완주한 사람은 참가자 명단에서 제거한다고 생각하면 됩니다. 🧹

```text
stanko → 1에서 0
ana    → 1에서 0
mislav → 2에서 1
```

결국 `mislav`만 1이 남습니다.

### 3. 남은 이름 찾기

```csharp
foreach (var pair in count)
{
    if (pair.Value > 0)
    {
        return pair.Key;
    }
}
```

값이 0보다 큰 이름이 완주하지 못한 선수입니다. ✅

## ⏱️ 시간 복잡도

참가자 배열 한 번, 완주자 배열 한 번, Dictionary 한 번을 확인합니다.

```text
O(n)
```

입니다.

## 📦 공간 복잡도

이름 개수를 저장하는 Dictionary를 사용합니다.

```text
O(n)
```

입니다.

# ✨ 코드가 짧은 Dictionary 풀이

C#의 `GetValueOrDefault()`를 사용하면 코드를 더 짧게 쓸 수 있습니다.

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public string solution(string[] participant, string[] completion)
    {
        var count = new Dictionary<string, int>();

        foreach (var name in participant)
        {
            count[name] = count.GetValueOrDefault(name) + 1;
        }

        foreach (var name in completion)
        {
            count[name]--;
        }

        foreach (var name in participant)
        {
            if (count[name] > 0)
            {
                return name;
            }
        }

        return "";
    }
}
```

## ⚠️ 참고

`GetValueOrDefault()`는 해당 키가 없으면 기본값인 `0`을 반환합니다.

그래서 다음 코드를:

```csharp
if (!count.ContainsKey(name))
{
    count[name] = 0;
}

count[name]++;
```

이렇게 줄일 수 있습니다.

```csharp
count[name] = count.GetValueOrDefault(name) + 1;
```

코드는 짧아지지만, 처음 공부할 때는 기본 Dictionary 버전이 더 이해하기 쉽습니다. 🌱

## ⏱️ 시간 복잡도

```text
O(n)
```

## 📦 공간 복잡도

```text
O(n)
```

# ⚖️ 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 정렬 후 비교 | 이해하기 쉽다 😊 | O(n log n) | O(1) |
| 풀이 2 | Dictionary로 개수 세기 | 빠르고 동명이인 처리에 좋다 🚀 | O(n) | O(n) |
| 짧은 풀이 | `GetValueOrDefault()` 사용 | 코드가 짧다 ✨ | O(n) | O(n) |

# 🏆 추천 풀이

처음 공부할 때는 **정렬 풀이**가 이해하기 쉽습니다.

```text
두 배열을 정렬한 뒤 앞에서부터 비교한다.
```

하지만 실전에서는 **Dictionary 풀이**를 더 추천합니다.

이유는 다음과 같습니다.

```text
1. 시간 복잡도가 O(n)으로 더 빠릅니다.
2. 동명이인을 자연스럽게 처리할 수 있습니다.
3. 해시 문제의 기본 패턴을 익히기 좋습니다.
```

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
이름이 있는지만 보면 안 된다.
이름이 몇 번 나왔는지를 봐야 한다.
```

그래서 두 가지 방법이 가능합니다.

```text
1. 정렬해서 다른 위치 찾기
2. Dictionary로 이름 개수 세기
```

🏃 동명이인이 있다는 조건 때문에 `Dictionary<string, int>` 방식이 특히 잘 어울립니다.
