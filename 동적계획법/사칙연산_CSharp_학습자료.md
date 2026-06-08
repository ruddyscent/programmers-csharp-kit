# ➕➖ 사칙연산 — C# 학습자료

## 📌 1. 문제 핵심

숫자와 `+`, `-` 연산자가 번갈아 들어 있는 배열 `arr`가 주어집니다.

예를 들어:

```text
["1", "-", "3", "+", "5", "-", "8"]
```

이는 다음 식을 뜻합니다.

```text
1 - 3 + 5 - 8
```

이 문제에서는 괄호를 어디에 치느냐에 따라 계산 결과가 달라집니다.

목표는 다음입니다.

```text
가능한 모든 연산 순서 중 결과의 최댓값 구하기
```

🎯

---

# 🔍 예제 이해하기

```text
1 - 3 + 5 - 8
```

괄호를 치는 방법에 따라 결과가 달라집니다.

예를 들어:

```text
((1 - 3) + 5) - 8 = -5
1 - ((3 + 5) - 8) = 1
1 - (3 + (5 - 8)) = 1
```

이 중 최댓값은:

```text
1
```

입니다.

따라서 정답은:

```text
1
```

✅

---

# ⚠️ 왜 어려울까?

`+`는 괄호 위치가 달라도 결과가 같습니다.

```text
(1 + 2) + 3 = 1 + (2 + 3)
```

하지만 `-`는 괄호 위치에 따라 결과가 달라집니다.

```text
(1 - 5) - 3 = -7
1 - (5 - 3) = -1
```

즉, 뺄셈 때문에 단순히 앞에서부터 계산하면 안 됩니다. 🚨

---

# 🧠 핵심 아이디어: 구간 DP

식의 일부 구간을 계산한다고 생각합니다.

예를 들어 숫자만 따로 보면:

```text
numbers = [1, 3, 5, 8]
ops = ["-", "+", "-"]
```

구간 `[0..2]`는 다음 식입니다.

```text
1 - 3 + 5
```

이 구간에서 만들 수 있는 최댓값과 최솟값을 저장합니다.

```text
maxDp[start, end] = start부터 end까지 숫자로 만들 수 있는 최댓값
minDp[start, end] = start부터 end까지 숫자로 만들 수 있는 최솟값
```

---

# 🧩 왜 최솟값도 필요할까?

최댓값만 저장하면 부족합니다.

뺄셈에서는 오른쪽 값이 작을수록 결과가 커집니다.

```text
A - B
```

에서 결과를 크게 만들려면:

```text
A는 최대
B는 최소
```

가 되어야 합니다.

예를 들어:

```text
10 - (-5) = 15
```

처럼 오른쪽 값이 작으면 전체 결과가 커집니다.

따라서 각 구간에 대해:

```text
최댓값
최솟값
```

둘 다 저장해야 합니다. 💡

---

# 🎯 DP 점화식

구간 `[start..end]`를 어떤 연산자 위치 `mid`에서 나눕니다.

```text
[start..mid] 연산자 [mid+1..end]
```

왼쪽 구간과 오른쪽 구간을 조합합니다.

## 연산자가 `+`일 때

```text
최댓값 = 왼쪽 최댓값 + 오른쪽 최댓값
최솟값 = 왼쪽 최솟값 + 오른쪽 최솟값
```

## 연산자가 `-`일 때

```text
최댓값 = 왼쪽 최댓값 - 오른쪽 최솟값
최솟값 = 왼쪽 최솟값 - 오른쪽 최댓값
```

여기서 뺄셈이 핵심입니다. ✅

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 숫자와 연산자 분리 후 구간 DP

## 💡 아이디어

1. `arr`에서 숫자와 연산자를 분리합니다.
2. `maxDp`, `minDp` 배열을 만듭니다.
3. 길이 1인 구간은 숫자 자체입니다.
4. 구간 길이를 2부터 늘려 가며 계산합니다.
5. 모든 나누는 위치를 시도해 최댓값과 최솟값을 갱신합니다.
6. 전체 구간의 최댓값을 반환합니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int solution(string[] arr)
    {
        List<int> numbers = new List<int>();
        List<string> operators = new List<string>();

        for (int i = 0; i < arr.Length; i++)
        {
            if (i % 2 == 0)
            {
                numbers.Add(int.Parse(arr[i]));
            }
            else
            {
                operators.Add(arr[i]);
            }
        }

        int n = numbers.Count;

        int[,] maxDp = new int[n, n];
        int[,] minDp = new int[n, n];

        for (int i = 0; i < n; i++)
        {
            maxDp[i, i] = numbers[i];
            minDp[i, i] = numbers[i];
        }

        for (int length = 2; length <= n; length++)
        {
            for (int start = 0; start + length - 1 < n; start++)
            {
                int end = start + length - 1;

                maxDp[start, end] = int.MinValue;
                minDp[start, end] = int.MaxValue;

                for (int mid = start; mid < end; mid++)
                {
                    string op = operators[mid];

                    int maxValue;
                    int minValue;

                    if (op == "+")
                    {
                        maxValue = maxDp[start, mid] + maxDp[mid + 1, end];
                        minValue = minDp[start, mid] + minDp[mid + 1, end];
                    }
                    else
                    {
                        maxValue = maxDp[start, mid] - minDp[mid + 1, end];
                        minValue = minDp[start, mid] - maxDp[mid + 1, end];
                    }

                    maxDp[start, end] = Math.Max(maxDp[start, end], maxValue);
                    minDp[start, end] = Math.Min(minDp[start, end], minValue);
                }
            }
        }

        return maxDp[0, n - 1];
    }
}
```

---

## 🔎 코드 해설

### 1. 숫자와 연산자 분리

```csharp
if (i % 2 == 0)
{
    numbers.Add(int.Parse(arr[i]));
}
else
{
    operators.Add(arr[i]);
}
```

`arr`는 숫자와 연산자가 번갈아 나옵니다.

따라서 짝수 인덱스는 숫자, 홀수 인덱스는 연산자입니다.

예:

```text
arr = ["1", "-", "3", "+", "5", "-", "8"]
```

분리 결과:

```text
numbers = [1, 3, 5, 8]
operators = ["-", "+", "-"]
```

---

### 2. DP 배열 의미

```csharp
int[,] maxDp = new int[n, n];
int[,] minDp = new int[n, n];
```

의미는 다음과 같습니다.

```text
maxDp[start, end] = start부터 end까지 만들 수 있는 최댓값
minDp[start, end] = start부터 end까지 만들 수 있는 최솟값
```

---

### 3. 길이 1 구간 초기화

```csharp
maxDp[i, i] = numbers[i];
minDp[i, i] = numbers[i];
```

숫자 하나만 있는 구간은 최댓값과 최솟값이 모두 자기 자신입니다.

---

### 4. 구간 길이 늘리기

```csharp
for (int length = 2; length <= n; length++)
```

길이 2짜리 구간부터 전체 구간까지 계산합니다.

작은 구간을 먼저 계산해야 큰 구간을 계산할 수 있습니다. 🧱

---

### 5. 나누는 위치 선택

```csharp
for (int mid = start; mid < end; mid++)
```

구간 `[start..end]`를 `mid` 위치의 연산자를 기준으로 나눕니다.

```text
[start..mid] op [mid+1..end]
```

---

### 6. 덧셈 처리

```csharp
maxValue = maxDp[start, mid] + maxDp[mid + 1, end];
minValue = minDp[start, mid] + minDp[mid + 1, end];
```

덧셈은 큰 값끼리 더하면 최댓값, 작은 값끼리 더하면 최솟값입니다.

---

### 7. 뺄셈 처리

```csharp
maxValue = maxDp[start, mid] - minDp[mid + 1, end];
minValue = minDp[start, mid] - maxDp[mid + 1, end];
```

뺄셈에서는 오른쪽 값이 작을수록 결과가 커집니다.

그래서 최댓값을 만들 때는:

```text
왼쪽 최댓값 - 오른쪽 최솟값
```

을 사용합니다. 🎯

---

## ⏱️ 시간 복잡도

숫자의 개수를 `n`이라고 하겠습니다.

구간의 시작과 끝을 정하고, 중간 분할 위치를 모두 확인합니다.

```text
O(n³)
```

입니다.

`n <= 101`이므로 충분히 가능합니다. ✅

---

## 📦 공간 복잡도

`maxDp`, `minDp` 2차원 배열을 사용합니다.

```text
O(n²)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — 배열과 LINQ로 분리하기

## 💡 아이디어

풀이 1과 같은 구간 DP입니다.

다만 숫자와 연산자를 LINQ로 조금 더 간결하게 분리합니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Linq;

public class Solution
{
    public int solution(string[] arr)
    {
        int[] nums = arr.Where((_, i) => i % 2 == 0)
            .Select(int.Parse)
            .ToArray();

        string[] ops = arr.Where((_, i) => i % 2 == 1)
            .ToArray();

        int n = nums.Length;

        int[,] maxDp = new int[n, n];
        int[,] minDp = new int[n, n];

        for (int i = 0; i < n; i++)
        {
            maxDp[i, i] = nums[i];
            minDp[i, i] = nums[i];
        }

        for (int len = 2; len <= n; len++)
        {
            for (int s = 0; s + len <= n; s++)
            {
                int e = s + len - 1;

                maxDp[s, e] = int.MinValue;
                minDp[s, e] = int.MaxValue;

                for (int m = s; m < e; m++)
                {
                    int maxValue;
                    int minValue;

                    if (ops[m] == "+")
                    {
                        maxValue = maxDp[s, m] + maxDp[m + 1, e];
                        minValue = minDp[s, m] + minDp[m + 1, e];
                    }
                    else
                    {
                        maxValue = maxDp[s, m] - minDp[m + 1, e];
                        minValue = minDp[s, m] - maxDp[m + 1, e];
                    }

                    maxDp[s, e] = Math.Max(maxDp[s, e], maxValue);
                    minDp[s, e] = Math.Min(minDp[s, e], minValue);
                }
            }
        }

        return maxDp[0, n - 1];
    }
}
```

---

## ✨ 코드 의미

### 1. 숫자만 추출

```csharp
int[] nums = arr.Where((_, i) => i % 2 == 0)
    .Select(int.Parse)
    .ToArray();
```

짝수 인덱스에 있는 숫자 문자열만 가져와 정수 배열로 바꿉니다.

---

### 2. 연산자만 추출

```csharp
string[] ops = arr.Where((_, i) => i % 2 == 1)
    .ToArray();
```

홀수 인덱스에 있는 연산자만 가져옵니다.

---

### 3. DP 로직은 동일

이후 로직은 풀이 1과 같습니다.

구간마다 만들 수 있는 최댓값과 최솟값을 함께 계산합니다.

---

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ를 사용하므로 다음 네임스페이스가 필요합니다.

```csharp
using System.Linq;
```

다만 DP 계산 자체는 반복문으로 작성하는 것이 가장 명확합니다. 🌱

---

## ⏱️ 시간 복잡도

```text
O(n³)
```

입니다.

---

## 📦 공간 복잡도

```text
O(n²)
```

입니다.

---

# 🔍 예제 1 계산 느낌

```text
1 - 3 + 5 - 8
```

숫자와 연산자:

```text
numbers = [1, 3, 5, 8]
operators = ["-", "+", "-"]
```

DP는 다음 구간들을 계산합니다.

```text
길이 1: 1, 3, 5, 8
길이 2: 1-3, 3+5, 5-8
길이 3: 1-3+5, 3+5-8
길이 4: 1-3+5-8
```

마지막 전체 구간에서 만들 수 있는 최댓값이 정답입니다.

```text
1
```

✅

---

# ❌ 잘못된 접근: 앞에서부터 계산하기

식이 다음과 같을 때:

```text
1 - 5 - 3
```

앞에서부터 계산하면:

```text
(1 - 5) - 3 = -7
```

하지만 괄호를 다르게 치면:

```text
1 - (5 - 3) = -1
```

더 큰 값이 나옵니다.

따라서 단순히 왼쪽부터 계산하면 안 됩니다. 🚨

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | List 분리 + 구간 DP | 원리가 잘 보인다 😊 | O(n³) | O(n²) |
| 풀이 2 | LINQ 분리 + 구간 DP | 코드가 조금 짧다 🚀 | O(n³) | O(n²) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 숫자와 연산자를 분리하는 과정이 명확합니다.
2. maxDp와 minDp의 의미가 잘 보입니다.
3. 구간 DP 흐름을 이해하기 좋습니다.
```

LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

다만 이 문제의 핵심은 LINQ가 아니라:

```text
최댓값과 최솟값을 함께 저장하는 구간 DP
```

입니다. ✅

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
뺄셈 때문에 최댓값만 저장하면 안 된다.
```

각 구간마다 다음 두 값을 저장해야 합니다.

```text
최댓값
최솟값
```

점화식은 다음과 같습니다.

```text
+ 연산:
max = leftMax + rightMax
min = leftMin + rightMin

- 연산:
max = leftMax - rightMin
min = leftMin - rightMax
```

풀이 흐름은 다음과 같습니다.

```text
1. 숫자와 연산자를 분리한다.
2. 길이 1 구간을 초기화한다.
3. 구간 길이를 늘려 가며 모든 분할 위치를 확인한다.
4. 각 구간의 최댓값과 최솟값을 갱신한다.
5. 전체 구간의 최댓값을 반환한다.
```

➕➖ “사칙연산”은 구간 DP와 최댓값/최솟값 동시 관리가 핵심인 고급 DP 문제입니다.
