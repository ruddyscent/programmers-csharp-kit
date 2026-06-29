# ✂️ K번째수 — C# 학습자료

## 📌 1. 문제 핵심

배열 `array`와 여러 개의 명령 `commands`가 주어집니다.

각 명령은 다음 형태입니다.

```text
[i, j, k]
```

의미는 다음과 같습니다.

```text
1. array의 i번째 숫자부터 j번째 숫자까지 자른다.
2. 자른 배열을 정렬한다.
3. 정렬된 배열에서 k번째 숫자를 구한다.
```

중요한 점은 문제의 번호는 **1부터 시작**하지만, C# 배열 인덱스는 **0부터 시작**한다는 것입니다. ⚠️

---

# 🔍 예제 이해하기

```text
array = [1, 5, 2, 6, 3, 7, 4]
command = [2, 5, 3]
```

2번째부터 5번째까지 자르면:

```text
[5, 2, 6, 3]
```

정렬하면:

```text
[2, 3, 5, 6]
```

3번째 숫자는:

```text
5
```

입니다. ✅

---

# 🧠 핵심 아이디어

각 명령마다 같은 일을 반복합니다.

```text
잘라내기 → 정렬하기 → k번째 값 가져오기
```

배열 길이는 최대 100, 명령 개수는 최대 50입니다.

입력이 작기 때문에 복잡한 최적화는 필요 없습니다. 😊

---

# ⚠️ 인덱스 변환

문제는 1번째부터 세지만 C#은 0번 인덱스부터 시작합니다.

```text
문제의 i번째 → C# index i - 1
문제의 k번째 → C# index k - 1
```

예를 들어:

```text
command = [2, 5, 3]
```

이면:

```text
시작 인덱스 = 2 - 1 = 1
잘라낼 길이 = 5 - 2 + 1 = 4
정답 인덱스 = 3 - 1 = 2
```

🎯 이 변환만 정확히 하면 됩니다.

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — Array.Copy 사용하기

## 💡 아이디어

1. 각 command에서 `i`, `j`, `k`를 꺼냅니다.
2. 잘라낼 시작 인덱스와 길이를 계산합니다.
3. 새 배열을 만들고 `Array.Copy()`로 복사합니다.
4. 새 배열을 정렬합니다.
5. `k - 1`번째 값을 정답에 넣습니다.

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int[] solution(int[] array, int[,] commands)
    {
        int commandCount = commands.GetLength(0);
        int[] answer = new int[commandCount];

        for (int row = 0; row < commandCount; row++)
        {
            int i = commands[row, 0], j = commands[row, 1], k = commands[row, 2];
            int startIndex = i - 1, length = j - i + 1;

            int[] sliced = new int[length];

            Array.Copy(array, startIndex, sliced, 0, length);

            Array.Sort(sliced);

            answer[row] = sliced[k - 1];
        }

        return answer;
    }
}
```

## 🔎 코드 해설

### 1. commands의 행 개수 구하기

```csharp
int commandCount = commands.GetLength(0);
```

프로그래머스 C#에서 `commands`는 보통 `int[,]` 형태의 2차원 배열입니다.

`GetLength(0)`은 행 개수를 의미합니다.

### 2. 명령 하나 꺼내기

```csharp
int i = commands[row, 0];
int j = commands[row, 1];
int k = commands[row, 2];
```

각 행은 `[i, j, k]`입니다.

### 3. C# 인덱스로 변환

```csharp
int startIndex = i - 1;
```

문제의 `i번째`는 C#에서 `i - 1`번 인덱스입니다.

### 4. 잘라낼 길이 계산

```csharp
int length = j - i + 1;
```

예를 들어 2번째부터 5번째까지는 총 4개입니다.

```text
5 - 2 + 1 = 4
```

### 5. 배열 복사

```csharp
Array.Copy(array, startIndex, sliced, 0, length);
```

의미는 다음과 같습니다.

```text
array의 startIndex부터
sliced의 0번 위치로
length개 복사
```

### 6. 정렬 후 k번째 값 저장

```csharp
Array.Sort(sliced);
answer[row] = sliced[k - 1];
```

정렬한 뒤 `k번째` 값을 가져옵니다.

C# 인덱스는 0부터 시작하므로 `k - 1`을 사용합니다. ✅

## ⏱️ 시간 복잡도

명령 하나에서 잘라낸 길이를 `m`이라고 하면:

```text
복사: O(m)
정렬: O(m log m)
```

전체 명령 수를 `q`, 배열 길이를 `n`이라고 하면 최악의 경우:

```text
O(q × n log n)
```

입니다.

`n <= 100`, `q <= 50`이므로 충분히 빠릅니다. ⚡

## 📦 공간 복잡도

명령마다 잘라낸 배열을 만듭니다.

```text
O(n)
```

정답 배열까지 포함하면:

```text
O(q + n)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — LINQ 사용하기

## 💡 아이디어

LINQ를 사용하면 자르기, 정렬하기, k번째 고르기를 짧게 표현할 수 있습니다.

```text
Skip(i - 1) → 앞부분 건너뛰기
Take(j - i + 1) → 필요한 개수만 가져오기
OrderBy(x => x) → 정렬하기
ElementAt(k - 1) → k번째 값 가져오기
```

## 💻 C# 코드

```csharp
using System.Linq;

public class Solution
{
    public int[] solution(int[] array, int[,] commands)
    {
        return Enumerable.Range(0, commands.GetLength(0))
            .Select(row =>
                array
                    .Skip(commands[row, 0] - 1)
                    .Take(commands[row, 1] - commands[row, 0] + 1)
                    .OrderBy(x => x)
                    .ElementAt(commands[row, 2] - 1)
            )
            .ToArray();
    }
}
```

## ✨ 코드 의미

### 1. 명령 행 번호 만들기

```csharp
Enumerable.Range(0, commands.GetLength(0))
```

명령이 3개라면 다음 숫자를 만듭니다.

```text
0, 1, 2
```

각 숫자는 `commands`의 행 번호입니다.

### 2. 앞부분 건너뛰기

```csharp
.Skip(commands[row, 0] - 1)
```

문제의 `i번째`부터 시작해야 하므로 `i - 1`개를 건너뜁니다.

### 3. 필요한 개수만 가져오기

```csharp
.Take(commands[row, 1] - commands[row, 0] + 1)
```

`i번째`부터 `j번째`까지의 개수는:

```text
j - i + 1
```

입니다.

### 4. 정렬하기

```csharp
.OrderBy(x => x)
```

오름차순 정렬합니다.

### 5. k번째 값 가져오기

```csharp
.ElementAt(commands[row, 2] - 1)
```

문제의 `k번째`는 C# 인덱스로 `k - 1`입니다.

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ를 사용하므로 반드시 아래 네임스페이스가 필요합니다.

```csharp
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

## ⏱️ 시간 복잡도

명령 하나마다 부분 배열을 만들고 정렬합니다.

```text
O(n log n)
```

명령 수가 `q`개라면:

```text
O(q × n log n)
```

입니다.

## 📦 공간 복잡도

LINQ 내부에서 부분 시퀀스와 정렬 결과를 만듭니다.

```text
O(n)
```

정답 배열까지 포함하면:

```text
O(q + n)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | `Array.Copy` + `Array.Sort` | 흐름이 명확하다 😊 | O(q × n log n) | O(q + n) |
| 풀이 2 | LINQ `Skip`, `Take`, `OrderBy` | 코드가 짧다 🚀 | O(q × n log n) | O(q + n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 Array.Copy 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 자르기, 정렬하기, k번째 고르기 흐름이 잘 보입니다.
2. 1-based index를 0-based index로 바꾸는 과정이 명확합니다.
3. 디버깅하기 쉽습니다.
```

LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

프로그래머스 C# 환경에서 실행 가능하며, 코드가 매우 간결합니다. ⚡

---

# ✅ 최종 정리

이 문제는 복잡한 알고리즘보다 인덱스 처리가 핵심입니다.

```text
문제의 i번째 → C# index i - 1
문제의 k번째 → C# index k - 1
```

풀이 흐름은 다음과 같습니다.

```text
1. i번째부터 j번째까지 자른다.
2. 자른 배열을 정렬한다.
3. k번째 값을 정답 배열에 넣는다.
```

✂️ “K번째수”는 배열 자르기와 정렬, 그리고 인덱스 변환을 연습하기 좋은 문제입니다.
