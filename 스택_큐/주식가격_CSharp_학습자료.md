# 📈 주식가격 — C# 학습자료

## 📌 1. 문제 핵심

초 단위로 기록된 주식 가격 배열 `prices`가 주어집니다.

각 시점마다 다음을 구해야 합니다.

```text
현재 가격이 몇 초 동안 떨어지지 않았는가?
```

예를 들어:

```text
prices = [1, 2, 3, 2, 3]
```

정답은:

```text
[4, 3, 1, 1, 0]
```

입니다.

---

## ⚠️ “떨어지지 않은 기간”의 의미

가격이 떨어지는 순간까지의 시간을 셉니다.

중요한 점은 **떨어진 그 순간도 1초로 계산**한다는 것입니다.

예를 들어 3초 시점의 가격이 `3`이고, 다음 시점 가격이 `2`라면:

```text
3 → 2
```

1초 뒤에 가격이 떨어졌습니다.

따라서 결과는:

```text
1
```

입니다. ⏱️

---

# 🔍 예제 이해하기

```text
prices = [1, 2, 3, 2, 3]
```

각 시점별로 보겠습니다.

## 0번 시점: 가격 1

뒤 가격들:

```text
2, 3, 2, 3
```

모두 1보다 작지 않습니다.

끝까지 가격이 떨어지지 않았습니다.

```text
4초
```

---

## 1번 시점: 가격 2

뒤 가격들:

```text
3, 2, 3
```

모두 2보다 작지 않습니다.

끝까지 가격이 떨어지지 않았습니다.

```text
3초
```

---

## 2번 시점: 가격 3

다음 가격은 2입니다.

```text
3 → 2
```

1초 뒤에 가격이 떨어졌습니다.

```text
1초
```

---

## 3번 시점: 가격 2

다음 가격은 3입니다.

```text
2 → 3
```

가격이 떨어지지 않았고, 이후 시간이 없습니다.

```text
1초
```

---

## 4번 시점: 가격 3

마지막 시점입니다.

비교할 뒤 가격이 없습니다.

```text
0초
```

---

## 최종 결과

```text
[4, 3, 1, 1, 0]
```

✅

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 이중 반복문

## 💡 아이디어

각 시점 `i`마다 뒤쪽 시점 `j`를 하나씩 확인합니다.

```text
현재 가격 prices[i]
뒤쪽 가격 prices[j]
```

뒤쪽 가격이 현재 가격보다 작아지는 순간 멈춥니다.

그전까지 지난 시간을 셉니다.

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int[] solution(int[] prices)
    {
        int n = prices.Length;
        int[] answer = new int[n];

        for (int i = 0; i < n; i++)
        {
            for (int j = i + 1; j < n; j++)
            {
                answer[i]++;

                if (prices[j] < prices[i])
                {
                    break;
                }
            }
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. 정답 배열 만들기

```csharp
int[] answer = new int[n];
```

각 시점마다 가격이 떨어지지 않은 시간을 저장합니다.

---

### 2. 각 시점 기준으로 확인

```csharp
for (int i = 0; i < n; i++)
```

`i`는 현재 기준 시점입니다.

---

### 3. 뒤쪽 가격 확인

```csharp
for (int j = i + 1; j < n; j++)
```

현재 시점 이후의 가격들을 하나씩 확인합니다.

---

### 4. 1초 증가

```csharp
answer[i]++;
```

뒤쪽 가격을 하나 확인했다는 것은 시간이 1초 지났다는 뜻입니다.

---

### 5. 가격이 떨어지면 멈춤

```csharp
if (prices[j] < prices[i])
{
    break;
}
```

처음으로 가격이 떨어진 순간 더 이상 확인할 필요가 없습니다. 🛑

---

## ⏱️ 시간 복잡도

최악의 경우 모든 가격이 떨어지지 않는 형태라면 뒤쪽을 계속 확인해야 합니다.

예:

```text
[1, 2, 3, 4, 5]
```

이 경우 시간 복잡도는:

```text
O(n²)
```

입니다.

`n`이 최대 100,000이면 이론적으로는 부담될 수 있습니다.

---

## 📦 공간 복잡도

정답 배열을 제외하면 추가 공간은 거의 사용하지 않습니다.

```text
O(1)
```

정답 배열까지 포함하면:

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 효율적인 방법 — Stack 사용하기

## 💡 아이디어

아직 가격이 떨어진 시점을 찾지 못한 인덱스를 Stack에 저장합니다.

현재 가격을 보면서 Stack의 맨 위 가격보다 현재 가격이 더 낮으면,  
그 시점의 가격은 지금 떨어진 것입니다.

즉:

```text
prices[current] < prices[stack.Peek()]
```

이면 `stack.Peek()` 시점의 가격이 현재 시점에서 떨어진 것입니다. 📉

---

## 🧺 Stack에는 무엇을 넣을까?

가격 자체가 아니라 **인덱스**를 넣습니다.

왜냐하면 정답은 시간 차이이기 때문입니다.

```text
떨어진 시간 = 현재 인덱스 - 이전 인덱스
```

예를 들어:

```text
이전 인덱스 = 2
현재 인덱스 = 3
```

이면:

```text
3 - 2 = 1초
```

입니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int[] solution(int[] prices)
    {
        int n = prices.Length;
        int[] answer = new int[n];
        Stack<int> stack = new Stack<int>();

        for (int i = 0; i < n; i++)
        {
            while (stack.Count > 0 && prices[i] < prices[stack.Peek()])
            {
                int index = stack.Pop();
                answer[index] = i - index;
            }

            stack.Push(i);
        }

        while (stack.Count > 0)
        {
            int index = stack.Pop();
            answer[index] = n - 1 - index;
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. Stack 만들기

```csharp
Stack<int> stack = new Stack<int>();
```

아직 가격이 떨어진 시점을 찾지 못한 인덱스를 저장합니다.

---

### 2. 현재 가격이 더 낮은지 확인

```csharp
while (stack.Count > 0 && prices[i] < prices[stack.Peek()])
```

현재 가격 `prices[i]`가 Stack 맨 위 시점의 가격보다 낮다면,  
Stack 맨 위 시점은 지금 가격이 떨어진 것입니다.

---

### 3. 떨어진 시간 계산

```csharp
int index = stack.Pop();
answer[index] = i - index;
```

예를 들어:

```text
index = 2
i = 3
```

이면:

```text
answer[2] = 3 - 2 = 1
```

입니다. ✅

---

### 4. 현재 인덱스 Stack에 넣기

```csharp
stack.Push(i);
```

현재 시점도 나중에 가격이 떨어지는지 확인해야 하므로 Stack에 넣습니다.

---

### 5. 끝까지 가격이 떨어지지 않은 시점 처리

```csharp
while (stack.Count > 0)
{
    int index = stack.Pop();
    answer[index] = n - 1 - index;
}
```

Stack에 남은 인덱스들은 끝까지 가격이 떨어지지 않은 시점입니다.

따라서 마지막 시점까지의 시간을 계산합니다.

```text
마지막 인덱스 - 현재 인덱스
```

즉:

```csharp
n - 1 - index
```

입니다.

---

## ⏱️ 시간 복잡도

각 인덱스는 Stack에 한 번 들어가고, 한 번 나옵니다.

따라서 전체 시간 복잡도는:

```text
O(n)
```

입니다. ⚡

---

## 📦 공간 복잡도

Stack에 인덱스를 저장합니다.

최악의 경우 모든 인덱스가 Stack에 들어갈 수 있습니다.

```text
O(n)
```

입니다.

---

# ✨ 풀이 3. 코드가 짧은 LINQ 스타일 참고

이 문제는 누적 상태와 Stack이 필요한 문제라서 LINQ만으로 풀면 오히려 가독성이 떨어집니다.

다만 이해하기 쉬운 이중 반복 풀이를 짧게 쓰면 다음처럼 작성할 수 있습니다.

## 💻 C# 코드

```csharp
using System;
using System.Linq;

public class Solution
{
    public int[] solution(int[] prices)
    {
        return prices
            .Select((price, i) =>
            {
                int count = 0;

                for (int j = i + 1; j < prices.Length; j++)
                {
                    count++;

                    if (prices[j] < price)
                    {
                        break;
                    }
                }

                return count;
            })
            .ToArray();
    }
}
```

---

## ⚠️ 주의

이 코드는 짧고 읽기 쉽지만, 시간 복잡도는 이중 반복문과 같습니다.

```text
O(n²)
```

따라서 입력이 큰 경우에는 Stack 풀이를 추천합니다. 🚨

프로그래머스에서 일부 테스트는 통과할 수 있지만, 효율성까지 고려하면 Stack 풀이가 안전합니다.

---

# ⚖️ 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 이중 반복문 | 가장 이해하기 쉽다 😊 | O(n²) | O(1) |
| 풀이 2 | Stack | 효율적이고 안전하다 🚀 | O(n) | O(n) |
| LINQ 참고 | `Select` 사용 | 코드가 짧다 ✨ | O(n²) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 이중 반복문**으로 문제 의미를 이해하는 것이 좋습니다.

하지만 실제 프로그래머스 제출용으로는 **풀이 2번 Stack 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. prices 길이가 최대 100,000입니다.
2. O(n²)은 최악의 경우 느릴 수 있습니다.
3. Stack 풀이는 O(n)이라 효율성 테스트에 안전합니다.
```

---

# ✅ 최종 정리

이 문제는 각 시점마다 가격이 언제 처음 떨어지는지 찾는 문제입니다.

단순하게는 뒤쪽을 하나씩 확인하면 됩니다.

하지만 효율적으로 풀려면 Stack을 사용합니다.

```text
1. 아직 가격이 떨어지지 않은 인덱스를 Stack에 저장한다.
2. 현재 가격이 Stack 맨 위 가격보다 낮으면 정답을 계산한다.
3. 끝까지 남은 인덱스는 마지막까지 떨어지지 않은 것으로 처리한다.
```

📈 “주식가격”은 Stack을 이용해 다음으로 가격이 떨어지는 시점을 찾는 문제입니다.
