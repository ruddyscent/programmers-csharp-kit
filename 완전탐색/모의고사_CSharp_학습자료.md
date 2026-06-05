# 📝 모의고사 — C# 학습자료

## 📌 1. 문제 핵심

수포자 3명이 각자 정해진 패턴대로 답을 찍습니다.

정답 배열 `answers`가 주어졌을 때, 각 수포자가 몇 문제를 맞혔는지 계산하고, 가장 많이 맞힌 사람의 번호를 반환해야 합니다.

수포자 번호는 다음과 같습니다.

```text
1번 수포자
2번 수포자
3번 수포자
```

가장 높은 점수를 받은 사람이 여러 명이면 번호를 오름차순으로 모두 반환합니다. ✅

---

# 🧠 찍는 패턴

각 수포자는 다음 패턴을 반복합니다.

## 1번 수포자

```text
1, 2, 3, 4, 5
```

반복:

```text
1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
```

---

## 2번 수포자

```text
2, 1, 2, 3, 2, 4, 2, 5
```

반복:

```text
2, 1, 2, 3, 2, 4, 2, 5, 2, 1, ...
```

---

## 3번 수포자

```text
3, 3, 1, 1, 2, 2, 4, 4, 5, 5
```

반복:

```text
3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, ...
```

---

# 🔑 핵심 아이디어: 나머지 연산

패턴은 계속 반복됩니다.

예를 들어 1번 수포자의 패턴은 길이가 5입니다.

```text
pattern1 = [1, 2, 3, 4, 5]
```

문제 번호가 `i`일 때, 1번 수포자가 찍은 답은:

```csharp
pattern1[i % pattern1.Length]
```

입니다.

예를 들어:

| 문제 인덱스 i | i % 5 | 찍은 답 |
|---:|---:|---:|
| 0 | 0 | 1 |
| 1 | 1 | 2 |
| 2 | 2 | 3 |
| 3 | 3 | 4 |
| 4 | 4 | 5 |
| 5 | 0 | 1 |
| 6 | 1 | 2 |

이렇게 나머지 연산을 사용하면 반복 패턴을 쉽게 처리할 수 있습니다. 🎯

---

# 🔍 예제 1 이해하기

```text
answers = [1, 2, 3, 4, 5]
```

1번 수포자의 답:

```text
[1, 2, 3, 4, 5]
```

모두 맞습니다.

2번 수포자의 답:

```text
[2, 1, 2, 3, 2]
```

모두 틀립니다.

3번 수포자의 답:

```text
[3, 3, 1, 1, 2]
```

모두 틀립니다.

따라서 정답은:

```text
[1]
```

입니다. 🎉

---

# 🔍 예제 2 이해하기

```text
answers = [1, 3, 2, 4, 2]
```

각 수포자는 모두 2문제씩 맞힙니다.

따라서 가장 많이 맞힌 사람이 3명 모두입니다.

```text
[1, 2, 3]
```

✅

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 반복문으로 점수 세기

## 💡 아이디어

1. 세 수포자의 패턴 배열을 만듭니다.
2. 정답 배열을 처음부터 끝까지 확인합니다.
3. 각 수포자의 패턴 답과 실제 정답을 비교합니다.
4. 맞으면 해당 수포자의 점수를 1 증가시킵니다.
5. 최고 점수를 찾습니다.
6. 최고 점수와 같은 수포자 번호를 결과에 넣습니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int[] solution(int[] answers)
    {
        int[] pattern1 = { 1, 2, 3, 4, 5 };
        int[] pattern2 = { 2, 1, 2, 3, 2, 4, 2, 5 };
        int[] pattern3 = { 3, 3, 1, 1, 2, 2, 4, 4, 5, 5 };

        int[] scores = new int[3];

        for (int i = 0; i < answers.Length; i++)
        {
            if (answers[i] == pattern1[i % pattern1.Length])
            {
                scores[0]++;
            }

            if (answers[i] == pattern2[i % pattern2.Length])
            {
                scores[1]++;
            }

            if (answers[i] == pattern3[i % pattern3.Length])
            {
                scores[2]++;
            }
        }

        int maxScore = Math.Max(scores[0], Math.Max(scores[1], scores[2]));

        List<int> result = new List<int>();

        for (int i = 0; i < scores.Length; i++)
        {
            if (scores[i] == maxScore)
            {
                result.Add(i + 1);
            }
        }

        return result.ToArray();
    }
}
```

---

## 🔎 코드 해설

### 1. 패턴 배열 만들기

```csharp
int[] pattern1 = { 1, 2, 3, 4, 5 };
int[] pattern2 = { 2, 1, 2, 3, 2, 4, 2, 5 };
int[] pattern3 = { 3, 3, 1, 1, 2, 2, 4, 4, 5, 5 };
```

각 수포자가 반복해서 찍는 답안 패턴입니다.

---

### 2. 점수 배열 만들기

```csharp
int[] scores = new int[3];
```

각 수포자의 점수를 저장합니다.

```text
scores[0] → 1번 수포자 점수
scores[1] → 2번 수포자 점수
scores[2] → 3번 수포자 점수
```

---

### 3. 정답과 패턴 비교

```csharp
answers[i] == pattern1[i % pattern1.Length]
```

`i % pattern1.Length`를 사용하면 패턴이 반복됩니다.

예를 들어 `i = 6`이면:

```text
6 % 5 = 1
```

따라서 1번 수포자의 6번 인덱스 답은 `pattern1[1]`입니다.

---

### 4. 최고 점수 구하기

```csharp
int maxScore = Math.Max(scores[0], Math.Max(scores[1], scores[2]));
```

세 점수 중 가장 높은 값을 구합니다.

---

### 5. 최고 점수인 수포자 번호 추가

```csharp
if (scores[i] == maxScore)
{
    result.Add(i + 1);
}
```

배열 인덱스는 0부터 시작하지만, 수포자 번호는 1부터 시작합니다.

그래서 `i + 1`을 넣습니다. ✅

---

## ⏱️ 시간 복잡도

정답 배열을 한 번 순회합니다.

```text
O(n)
```

`n`은 문제 수입니다.

---

## 📦 공간 복잡도

패턴 배열과 점수 배열, 결과 리스트를 사용합니다.

패턴 크기는 고정입니다.

```text
O(1)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — LINQ로 점수 계산하기

## 💡 아이디어

패턴들을 2차원 배열처럼 관리하고, LINQ로 각 수포자의 점수를 계산할 수 있습니다.

흐름은 다음과 같습니다.

```text
1. 패턴 배열들을 하나의 배열에 담는다.
2. 각 패턴마다 맞힌 개수를 Count로 계산한다.
3. 최고 점수를 구한다.
4. 최고 점수인 사람 번호를 반환한다.
```

---

## 💻 C# 코드

```csharp
using System;
using System.Linq;

public class Solution
{
    public int[] solution(int[] answers)
    {
        int[][] patterns =
        {
            new int[] { 1, 2, 3, 4, 5 },
            new int[] { 2, 1, 2, 3, 2, 4, 2, 5 },
            new int[] { 3, 3, 1, 1, 2, 2, 4, 4, 5, 5 }
        };

        int[] scores = patterns
            .Select(pattern =>
                answers
                    .Where((answer, index) => answer == pattern[index % pattern.Length])
                    .Count()
            )
            .ToArray();

        int maxScore = scores.Max();

        return scores
            .Select((score, index) => new { Score = score, Person = index + 1 })
            .Where(x => x.Score == maxScore)
            .Select(x => x.Person)
            .ToArray();
    }
}
```

---

## ✨ 코드 의미

### 1. 패턴들을 배열로 묶기

```csharp
int[][] patterns =
{
    new int[] { 1, 2, 3, 4, 5 },
    new int[] { 2, 1, 2, 3, 2, 4, 2, 5 },
    new int[] { 3, 3, 1, 1, 2, 2, 4, 4, 5, 5 }
};
```

각 수포자의 패턴을 하나의 배열 안에 담습니다.

---

### 2. 각 패턴별 점수 계산

```csharp
answers
    .Where((answer, index) => answer == pattern[index % pattern.Length])
    .Count()
```

정답 배열을 보면서 해당 수포자의 패턴과 일치하는 답만 세어 점수를 구합니다. 🧮

---

### 3. 최고 점수 구하기

```csharp
int maxScore = scores.Max();
```

LINQ의 `Max()`로 가장 높은 점수를 구합니다.

---

### 4. 최고 점수인 사람 번호만 선택

```csharp
scores
    .Select((score, index) => new { Score = score, Person = index + 1 })
    .Where(x => x.Score == maxScore)
    .Select(x => x.Person)
    .ToArray();
```

점수와 사람 번호를 묶은 뒤, 최고 점수인 사람만 골라냅니다.

---

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ를 사용하므로 반드시 아래 네임스페이스가 필요합니다.

```csharp
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

---

## ⏱️ 시간 복잡도

수포자는 3명으로 고정입니다.

각 수포자마다 정답 배열을 한 번 확인합니다.

```text
O(3n) = O(n)
```

입니다.

---

## 📦 공간 복잡도

패턴 배열과 점수 배열을 사용합니다.

패턴 크기는 고정입니다.

```text
O(1)
```

결과 배열도 최대 3개입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 반복문으로 점수 계산 | 흐름이 명확하다 😊 | O(n) | O(1) |
| 풀이 2 | LINQ `Select`, `Where`, `Count` | 코드가 깔끔하다 🚀 | O(n) | O(1) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 반복문 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 패턴 반복 원리가 잘 보입니다.
2. 나머지 연산 `%`의 역할을 이해하기 좋습니다.
3. 디버깅하기 쉽습니다.
```

LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

다만 처음에는 LINQ 체인이 길어 보일 수 있으니, 반복문 풀이를 먼저 이해하는 것이 좋습니다. 🌱

---

# ✅ 최종 정리

이 문제의 핵심은 반복 패턴입니다.

```text
패턴[index % 패턴길이]
```

를 사용하면 아무리 문제가 많아도 반복되는 답안을 쉽게 구할 수 있습니다.

풀이 흐름은 다음과 같습니다.

```text
1. 각 수포자의 찍기 패턴을 배열로 저장한다.
2. 정답과 비교해 점수를 센다.
3. 최고 점수를 구한다.
4. 최고 점수를 받은 사람 번호를 모두 반환한다.
```

📝 “모의고사”는 반복 패턴과 나머지 연산을 연습하기 좋은 문제입니다.
