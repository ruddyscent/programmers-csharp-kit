# 📖 모음 사전 — C# 학습자료

## 📌 1. 문제 핵심

사전에는 알파벳 모음 5개만 사용한 단어가 들어 있습니다.

```text
A, E, I, O, U
```

단어 길이는 1 이상 5 이하입니다.

사전 순서는 다음처럼 시작합니다.

```text
A
AA
AAA
AAAA
AAAAA
AAAAE
AAAAI
AAAAO
AAAAU
AAAE
...
```

목표는 주어진 `word`가 이 사전에서 몇 번째 단어인지 구하는 것입니다. 🎯

---

# 🔍 예제 이해하기

## 예제 1

```text
word = "AAAAE"
```

사전 앞부분은 다음과 같습니다.

```text
1. A
2. AA
3. AAA
4. AAAA
5. AAAAA
6. AAAAE
```

따라서 정답은:

```text
6
```

입니다. ✅

---

## 예제 2

```text
word = "AAAE"
```

사전 앞부분을 보면:

```text
1. A
2. AA
3. AAA
4. AAAA
5. AAAAA
6. AAAAE
7. AAAAI
8. AAAAO
9. AAAAU
10. AAAE
```

따라서 정답은:

```text
10
```

입니다. 🎉

---

# 🧠 풀이 방향

이 문제는 두 가지 방식으로 풀 수 있습니다.

```text
1. 가능한 모든 단어를 만들어서 찾기
2. 자리별 가중치를 이용해 바로 계산하기
```

단어 길이가 최대 5이고 알파벳도 5개뿐입니다.

가능한 모든 단어 개수는:

```text
5 + 25 + 125 + 625 + 3125 = 3905
```

뿐입니다.

그래서 모든 단어를 만들어도 충분히 빠릅니다. 😊

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 모든 단어 만들기

## 💡 아이디어

1. DFS로 만들 수 있는 모든 단어를 만듭니다.
2. 만든 단어들을 리스트에 저장합니다.
3. 사전순으로 정렬합니다.
4. `word`의 위치를 찾습니다.
5. 인덱스는 0부터 시작하므로 `+1`을 해서 반환합니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    private readonly char[] vowels = { 'A', 'E', 'I', 'O', 'U' };
    private List<string> words = new List<string>();

    public int solution(string word)
    {
        MakeWords("");

        words.Sort();

        return words.IndexOf(word) + 1;
    }

    private void MakeWords(string current)
    {
        if (current.Length > 0)
        {
            words.Add(current);
        }

        if (current.Length == 5)
        {
            return;
        }

        foreach (char vowel in vowels)
        {
            MakeWords(current + vowel);
        }
    }
}
```

---

## 🔎 코드 해설

### 1. 사용할 모음 배열

```csharp
private readonly char[] vowels = { 'A', 'E', 'I', 'O', 'U' };
```

사전에 사용할 수 있는 문자는 5개입니다.

---

### 2. 모든 단어를 저장할 리스트

```csharp
private List<string> words = new List<string>();
```

DFS로 만든 단어들을 저장합니다.

---

### 3. DFS 시작

```csharp
MakeWords("");
```

빈 문자열에서 시작해서 모음을 하나씩 붙입니다.

---

### 4. 현재 단어 저장

```csharp
if (current.Length > 0)
{
    words.Add(current);
}
```

빈 문자열은 사전에 들어가지 않습니다.

그래서 길이가 1 이상일 때만 저장합니다.

---

### 5. 길이가 5이면 멈춤

```csharp
if (current.Length == 5)
{
    return;
}
```

문제에서 단어 길이는 최대 5입니다.

---

### 6. 다음 글자 붙이기

```csharp
foreach (char vowel in vowels)
{
    MakeWords(current + vowel);
}
```

현재 단어 뒤에 `A`, `E`, `I`, `O`, `U`를 각각 붙여 봅니다. 🔁

---

### 7. 정렬 후 위치 찾기

```csharp
words.Sort();

return words.IndexOf(word) + 1;
```

`IndexOf()`는 0부터 시작하는 위치를 반환합니다.

문제에서는 첫 번째 단어가 1번이므로 `+1`을 합니다.

---

## ⏱️ 시간 복잡도

만들 수 있는 단어 수는 최대 3905개입니다.

정렬 비용은:

```text
O(3905 log 3905)
```

입니다.

일반화하면 단어 개수를 `N`이라 할 때:

```text
O(N log N)
```

입니다.

---

## 📦 공간 복잡도

모든 단어를 리스트에 저장합니다.

```text
O(N)
```

입니다.

여기서 `N = 3905`입니다.

---

# 🚀 풀이 2. 코드가 짧고 빠른 방법 — 자리별 가중치 공식

## 💡 아이디어

사전 순서를 직접 계산할 수 있습니다.

각 자리에서 문자가 바뀔 때마다 건너뛰는 단어 수가 정해져 있습니다.

자리별 가중치는 다음과 같습니다.

```text
1번째 자리: 781
2번째 자리: 156
3번째 자리: 31
4번째 자리: 6
5번째 자리: 1
```

즉:

```text
weights = [781, 156, 31, 6, 1]
```

입니다.

그리고 모음의 순서는 다음과 같습니다.

```text
A = 0
E = 1
I = 2
O = 3
U = 4
```

각 자리마다 다음 값을 더합니다.

```text
모음 순서 × 자리별 가중치 + 1
```

---

## 🧮 왜 가중치가 이렇게 될까?

첫 번째 자리에서 `A` 다음에 `E`로 넘어가려면, `A`로 시작하는 모든 단어를 건너뛰어야 합니다.

`A` 뒤에 붙을 수 있는 길이는 최대 4자리입니다.

```text
A
AA
AAA
AAAA
AAAAA
AAAAE
...
AUUUU
```

`A`로 시작하는 단어 수는:

```text
1 + 5 + 25 + 125 + 625 = 781
```

입니다.

그래서 첫 번째 자리 가중치는 781입니다.

두 번째 자리에서는 남은 길이가 최대 3자리이므로:

```text
1 + 5 + 25 + 125 = 156
```

입니다.

같은 방식으로:

```text
31, 6, 1
```

이 나옵니다. 💡

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int solution(string word)
    {
        string vowels = "AEIOU";
        int[] weights = { 781, 156, 31, 6, 1 };

        int answer = 0;

        for (int i = 0; i < word.Length; i++)
        {
            int order = vowels.IndexOf(word[i]);

            answer += order * weights[i] + 1;
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. 모음 순서

```csharp
string vowels = "AEIOU";
```

문자가 몇 번째 모음인지 찾기 위해 사용합니다.

```text
A → 0
E → 1
I → 2
O → 3
U → 4
```

---

### 2. 자리별 가중치

```csharp
int[] weights = { 781, 156, 31, 6, 1 };
```

각 자리에서 문자가 하나 뒤로 밀릴 때 건너뛰는 단어 수입니다.

---

### 3. 현재 문자의 순서 찾기

```csharp
int order = vowels.IndexOf(word[i]);
```

예를 들어:

```text
word[i] = 'I'
```

이면:

```text
order = 2
```

입니다.

---

### 4. 순서 계산

```csharp
answer += order * weights[i] + 1;
```

`order * weights[i]`는 앞에 있는 단어 묶음을 건너뛰는 개수입니다.

`+1`은 현재 자리의 문자를 선택하면서 생기는 단어 하나를 세는 것입니다. ✅

---

# 🔍 공식 풀이 예제: "AAAAE"

```text
word = "AAAAE"
```

가중치:

```text
[781, 156, 31, 6, 1]
```

계산:

```text
A: 0 × 781 + 1 = 1
A: 0 × 156 + 1 = 1
A: 0 × 31  + 1 = 1
A: 0 × 6   + 1 = 1
E: 1 × 1   + 1 = 2
```

합:

```text
1 + 1 + 1 + 1 + 2 = 6
```

정답:

```text
6
```

🎉

---

# 🔍 공식 풀이 예제: "I"

```text
word = "I"
```

`I`는 모음 순서에서 2번입니다.

```text
A = 0
E = 1
I = 2
```

계산:

```text
2 × 781 + 1 = 1563
```

정답:

```text
1563
```

✅

---

# ✨ LINQ 스타일 짧은 풀이

LINQ를 적극적으로 쓰면 다음처럼 작성할 수도 있습니다.

```csharp
using System;
using System.Linq;

public class Solution
{
    public int solution(string word)
    {
        string vowels = "AEIOU";
        int[] weights = { 781, 156, 31, 6, 1 };

        return word
            .Select((ch, i) => vowels.IndexOf(ch) * weights[i] + 1)
            .Sum();
    }
}
```

깔끔하지만, 처음 공부할 때는 반복문 풀이가 더 이해하기 쉽습니다. 🌱

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 모든 단어 생성 + 정렬 | 사전 구조가 잘 보인다 😊 | O(N log N) | O(N) |
| 풀이 2 | 자리별 가중치 공식 | 빠르고 코드가 짧다 🚀 | O(L) | O(1) |
| LINQ 참고 | 공식 + Select + Sum | 가장 간결하다 ✨ | O(L) | O(1) |

여기서:

```text
N = 만들 수 있는 전체 단어 수, 최대 3905
L = word 길이, 최대 5
```

입니다.

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 모든 단어 생성 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 사전이 어떤 순서로 만들어지는지 눈에 잘 보입니다.
2. DFS 연습에 좋습니다.
3. 문제 의미를 직관적으로 이해할 수 있습니다.
```

프로그래머스 제출용으로는 **풀이 2번 공식 방식**을 추천합니다.

빠르고 코드도 짧습니다. ⚡

---

# ✅ 최종 정리

이 문제는 두 가지 방식으로 풀 수 있습니다.

```text
1. 모든 단어를 만들어 정렬한 뒤 위치 찾기
2. 자리별 가중치를 이용해 바로 계산하기
```

공식 풀이의 핵심은 다음 가중치입니다.

```text
[781, 156, 31, 6, 1]
```

그리고 각 문자의 순서는:

```text
A=0, E=1, I=2, O=3, U=4
```

입니다.

📖 “모음 사전”은 DFS 완전탐색과 규칙 기반 계산을 함께 연습하기 좋은 문제입니다.
