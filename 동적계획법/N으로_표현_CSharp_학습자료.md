# 🔢 N으로 표현 — C# 학습자료

## 📌 1. 문제 핵심

숫자 `N`과 목표 숫자 `number`가 주어집니다.

`N`을 여러 번 사용하고, 사칙연산을 이용해서 `number`를 만들고자 합니다.

이때 구해야 하는 것은 다음입니다.

```text
number를 만들기 위해 필요한 N 사용 횟수의 최솟값
```

단, `N`은 최대 8번까지만 사용할 수 있습니다.

```text
8번 안에 만들 수 없으면 -1 반환
```

예를 들어:

```text
N = 5
number = 12
```

라면 다음처럼 만들 수 있습니다.

```text
12 = 5 + 5 + (5 / 5) + (5 / 5)  → 5를 6번 사용
12 = 55 / 5 + 5 / 5              → 5를 5번 사용
12 = (55 + 5) / 5                → 5를 4번 사용
```

가장 적게 사용하는 경우는 4번입니다.

```text
return 4
```

✅

---

## 🧠 핵심 아이디어

이 문제는 **동적 계획법(DP)** 으로 풀 수 있습니다.

`N`을 정확히 `i`번 사용해서 만들 수 있는 모든 수를 저장합니다.

```text
dp[1] = N을 1번 사용해서 만들 수 있는 수들
dp[2] = N을 2번 사용해서 만들 수 있는 수들
dp[3] = N을 3번 사용해서 만들 수 있는 수들
...
dp[8] = N을 8번 사용해서 만들 수 있는 수들
```

각 `dp[i]`는 중복을 없애기 위해 `HashSet<int>`로 관리합니다. 🧺

---

## 🔍 예시: N = 5

### N을 1번 사용

```text
5
```

```text
dp[1] = { 5 }
```

---

### N을 2번 사용

먼저 이어 붙인 수가 있습니다.

```text
55
```

그리고 `dp[1]`과 `dp[1]`을 사칙연산으로 조합합니다.

```text
5 + 5 = 10
5 - 5 = 0
5 * 5 = 25
5 / 5 = 1
```

따라서:

```text
dp[2] = { 55, 10, 0, 25, 1 }
```

---

### N을 3번 사용

먼저 이어 붙인 수가 있습니다.

```text
555
```

그리고 다음 조합을 생각합니다.

```text
dp[1]과 dp[2] 조합
dp[2]와 dp[1] 조합
```

여기서 조합은 실제로 다음 연산을 모두 시도한다는 뜻입니다.

```text
+, -, *, /
```

---

## 🎯 DP 점화식 느낌

`N`을 `i`번 써서 만들 수 있는 수는 다음처럼 만들 수 있습니다.

```text
dp[i] =
N을 i번 이어 붙인 수
+ dp[1]과 dp[i-1]의 사칙연산 조합
+ dp[2]과 dp[i-2]의 사칙연산 조합
+ ...
+ dp[i-1]과 dp[1]의 사칙연산 조합
```

예를 들어 `i = 4`라면:

```text
dp[4] =
5555
dp[1]과 dp[3] 조합
dp[2]과 dp[2] 조합
dp[3]과 dp[1] 조합
```

입니다. 🧠

---

## ⚠️ 왜 HashSet을 사용할까?

같은 값을 여러 방식으로 만들 수 있습니다.

예를 들어:

```text
5 + 5 = 10
55 / 5 - 1 = 10
```

같은 값 `10`이 여러 번 만들어져도 한 번만 저장하면 됩니다.

그래서 중복을 자동으로 제거하는 `HashSet<int>`을 사용합니다. ✅

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — DP 배열 + HashSet

## 💡 아이디어

1. `dp[1]`부터 `dp[8]`까지 `HashSet<int>`를 준비합니다.
2. 각 사용 횟수 `count`마다 `N`, `NN`, `NNN` 같은 반복 숫자를 넣습니다.
3. `leftCount + rightCount = count`가 되도록 나눕니다.
4. `dp[leftCount]`와 `dp[rightCount]`의 모든 값을 사칙연산으로 조합합니다.
5. `number`가 만들어지면 현재 `count`를 반환합니다.
6. 8번까지 못 만들면 `-1`을 반환합니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int N, int number)
    {
        List<HashSet<int>> dp = new List<HashSet<int>>();

        for (int i = 0; i <= 8; i++)
            dp.Add(new HashSet<int>());

        int repeatedNumber = 0;

        for (int count = 1; count <= 8; count++)
        {
            repeatedNumber = repeatedNumber * 10 + N;
            dp[count].Add(repeatedNumber);

            for (int leftCount = 1; leftCount < count; leftCount++)
            {
                int rightCount = count - leftCount;

                foreach (int left in dp[leftCount])
                {
                    foreach (int right in dp[rightCount])
                    {
                        dp[count].Add(left + right);
                        dp[count].Add(left - right);
                        dp[count].Add(left * right);

                        if (right != 0)
                            dp[count].Add(left / right);
                    }
                }
            }

            if (dp[count].Contains(number))
                return count;
        }

        return -1;
    }
}
```

---

## 🔎 코드 해설

### 1. DP 저장소 만들기

```csharp
List<HashSet<int>> dp = new List<HashSet<int>>();
```

`dp[1]`부터 `dp[8]`까지 사용합니다.

각 칸에는 `N`을 해당 횟수만큼 사용해서 만들 수 있는 수들이 들어갑니다.

---

### 2. HashSet 초기화

```csharp
for (int i = 0; i <= 8; i++)
{
    dp.Add(new HashSet<int>());
}
```

`dp[0]`은 사용하지 않지만, 인덱스를 편하게 맞추기 위해 만들어 둡니다.

---

### 3. N을 이어 붙인 수 넣기

```csharp
repeatedNumber = repeatedNumber * 10 + N;
dp[count].Add(repeatedNumber);
```

예를 들어:

```text
N = 5
count = 3
```

이면:

```text
555
```

를 만듭니다.

이 값도 `N`을 3번 사용해서 만들 수 있는 수입니다. 🔢

---

### 4. 사용 횟수 나누기

```csharp
for (int leftCount = 1; leftCount < count; leftCount++)
{
    int rightCount = count - leftCount;
}
```

`count`번 사용한 식을 두 부분으로 나눕니다.

예를 들어 `count = 4`라면:

```text
1 + 3
2 + 2
3 + 1
```

처럼 나눌 수 있습니다.

---

### 5. 사칙연산 조합

```csharp
dp[count].Add(left + right);
dp[count].Add(left - right);
dp[count].Add(left * right);
```

더하기, 빼기, 곱하기 결과를 넣습니다.

나누기는 0으로 나누면 안 되므로 따로 확인합니다.

```csharp
if (right != 0)
{
    dp[count].Add(left / right);
}
```

문제에서 나누기 연산은 정수 나눗셈입니다.

즉, 나머지는 버립니다.

---

### 6. 목표 숫자를 찾으면 바로 반환

```csharp
if (dp[count].Contains(number))
{
    return count;
}
```

`count`는 1부터 증가하므로 처음 찾은 순간이 최소 사용 횟수입니다. 🎯

---

## ⏱️ 시간 복잡도

최대 8번까지만 계산합니다.

각 `dp[i]`의 원소 수에 따라 조합 수가 달라집니다.

일반적으로는 다음처럼 볼 수 있습니다.

```text
O(8 × 집합 조합 수)
```

제한이 작아 프로그래머스에서 충분히 통과 가능합니다. ✅

---

## 📦 공간 복잡도

`dp[1]`부터 `dp[8]`까지 가능한 중간 결과를 저장합니다.

```text
O(가능한 중간 결과 수)
```

입니다.

`HashSet`을 사용하므로 중복 값은 저장되지 않습니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — 배열로 간결하게 쓰기

## 💡 아이디어

풀이 1과 같은 DP 방식입니다.

다만 `HashSet` 배열을 바로 만들고, 반복문 중괄호를 줄여 제출 코드에 가깝게 작성합니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int N, int number)
    {
        var dp = new HashSet<int>[9];
        for (int i = 0; i < 9; i++) dp[i] = new HashSet<int>();

        for (int i = 1, repeated = 0; i <= 8; i++)
        {
            repeated = repeated * 10 + N;
            dp[i].Add(repeated);

            for (int j = 1; j < i; j++)
            foreach (int a in dp[j])
            foreach (int b in dp[i - j])
            {
                dp[i].Add(a + b);
                dp[i].Add(a - b);
                dp[i].Add(a * b);

                if (b != 0) dp[i].Add(a / b);
            }

            if (dp[i].Contains(number)) return i;
        }

        return -1;
    }
}
```

---

## ✨ 코드 의미

### 1. HashSet 배열 만들기

```csharp
var dp = new HashSet<int>[9];
for (int i = 0; i < 9; i++) dp[i] = new HashSet<int>();
```

`dp[0]`부터 `dp[8]`까지 `HashSet<int>`를 만듭니다.

---

### 2. N을 반복한 숫자 만들기

```csharp
repeated = repeated * 10 + N;
```

예를 들어:

```text
N = 5
i = 3
```

처럼 한 번씩 갱신하면:

```text
5 → 55 → 555
```

가 됩니다.

---

### 3. 이전 결과 조합

```csharp
foreach (int a in dp[j])
{
    foreach (int b in dp[i - j])
```

`N`을 `j`번 사용한 값과 `i - j`번 사용한 값을 조합합니다.

---

## ⚠️ 프로그래머스에서 실행할 때 주의

`HashSet`을 사용하므로 다음 네임스페이스가 필요합니다.

```csharp
using System.Collections.Generic;
```

다만 핵심 로직은 여전히 DP와 HashSet입니다.

---

## ⏱️ 시간 복잡도

풀이 1과 같습니다.

```text
O(8 × 집합 조합 수)
```

입니다.

---

## 📦 공간 복잡도

풀이 1과 같습니다.

```text
O(가능한 중간 결과 수)
```

입니다.

---

# 🔍 예제: N = 2, number = 11

```text
N = 2
number = 11
```

`N`을 1번 사용:

```text
2
```

`N`을 2번 사용:

```text
22
2 + 2 = 4
2 - 2 = 0
2 * 2 = 4
2 / 2 = 1
```

`N`을 3번 사용하면:

```text
22 / 2 = 11
```

따라서 정답은:

```text
3
```

✅

---

# ❌ 잘못된 접근: 단순 그리디

목표 숫자에 가까워지도록 더하거나 빼는 식으로 풀려고 하면 실패할 수 있습니다.

이 문제는 괄호 위치와 연산 순서에 따라 다양한 값이 만들어집니다.

따라서:

```text
N을 i번 사용해서 만들 수 있는 모든 값
```

을 저장하는 DP가 필요합니다. 🧠

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | DP + HashSet + 직접 반복 숫자 생성 | 원리가 잘 보인다 😊 | O(8 × 조합 수) | O(중간 결과 수) |
| 풀이 2 | DP + HashSet 배열 | 제출 코드가 짧다 🚀 | O(8 × 조합 수) | O(중간 결과 수) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. dp[count]의 의미가 명확합니다.
2. N을 여러 번 이어 붙이는 과정이 잘 보입니다.
3. 사용 횟수를 나누어 조합하는 흐름을 이해하기 쉽습니다.
```

프로그래머스 제출용으로는 **풀이 2번**도 좋습니다.

다만 중첩 반복문의 중괄호를 많이 생략하므로, 처음에는 풀이 1번으로 흐름을 잡는 편이 안전합니다. ✅

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
N을 i번 사용해서 만들 수 있는 모든 값을 저장한다.
```

그리고:

```text
dp[i] = dp[j]와 dp[i-j]의 사칙연산 조합
```

으로 값을 만들어 갑니다.

풀이 흐름은 다음과 같습니다.

```text
1. dp[1]부터 dp[8]까지 HashSet을 준비한다.
2. N, NN, NNN 같은 반복 숫자를 넣는다.
3. 이전 dp들을 조합해 사칙연산 결과를 넣는다.
4. number가 나오면 해당 사용 횟수를 반환한다.
5. 8번까지 못 만들면 -1을 반환한다.
```

🔢 “N으로 표현”은 DP와 HashSet 조합을 연습하기 좋은 대표 문제입니다.
