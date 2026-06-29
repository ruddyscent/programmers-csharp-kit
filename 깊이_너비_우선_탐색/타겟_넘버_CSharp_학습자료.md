# 🎯 타겟 넘버 — C# 학습자료

## 📌 1. 문제 핵심

여러 개의 양의 정수가 들어 있는 배열 `numbers`가 있습니다.

각 숫자 앞에 `+` 또는 `-`를 붙여서 모두 사용했을 때, 계산 결과가 `target`이 되는 경우의 수를 구하는 문제입니다.

예를 들어 `1, 1, 1, 1, 1`에서 목표값이 `3`이라면, 숫자 하나만 빼고 나머지를 더하는 방식으로 여러 조합을 만들 수 있습니다.

---

# 🔍 예제 이해하기

```text
numbers = [4, 1, 2, 1]
target = 4
```

각 숫자마다 선택지는 2개입니다.

```text
더하기
빼기
```

가능한 조합 중에서 결과가 `4`가 되는 경우는 다음처럼 2가지입니다.

```text
+4 +1 -2 +1 = 4
+4 -1 +2 -1 = 4
```

따라서 정답은 `2`입니다. ✅

---

## ⚠️ 주의할 점

숫자의 순서는 바꾸지 않습니다.

하지만 각 숫자마다 `+`를 붙일지, `-`를 붙일지는 선택할 수 있습니다.

즉, 핵심은 다음과 같습니다.

```text
0번째 숫자에 + 또는 -
1번째 숫자에 + 또는 -
2번째 숫자에 + 또는 -
...
마지막 숫자까지 선택했을 때 target인지 확인
```

숫자의 개수는 최대 20개입니다.

각 숫자마다 선택지가 2개이므로 경우의 수는 최대:

```text
2^20 = 1,048,576
```

충분히 탐색할 수 있습니다. 👍

---

# 풀이 1. 이해하기 쉬운 방법 — DFS 재귀

## 💡 아이디어

DFS로 숫자를 하나씩 처리합니다.

현재 위치 `index`와 지금까지 만든 합 `sum`을 함께 들고 갑니다.

각 단계에서는 두 가지 선택을 합니다.

1. 현재 숫자를 더한다.
2. 현재 숫자를 뺀다.

마지막 숫자까지 모두 사용했다면 `sum == target`인지 확인합니다.

맞으면 경우의 수를 1 증가시킵니다. 🌱

---

## 💻 C# 코드

```csharp
public class Solution
{
    private int answer = 0;

    public int solution(int[] numbers, int target)
    {
        Dfs(numbers, target, 0, 0);
        return answer;
    }

    private void Dfs(int[] numbers, int target, int index, int sum)
    {
        if (index == numbers.Length)
        {
            if (sum == target)
            {
                answer++;
            }

            return;
        }

        Dfs(numbers, target, index + 1, sum + numbers[index]);
        Dfs(numbers, target, index + 1, sum - numbers[index]);
    }
}
```

---

## 🔎 코드 해설

### 1. 정답을 저장할 변수

```csharp
private int answer = 0;
```

`target`을 만들 수 있는 방법의 수를 저장합니다.

---

### 2. DFS 시작

```csharp
Dfs(numbers, target, 0, 0);
```

처음에는 아직 아무 숫자도 사용하지 않았습니다.

그래서:

```text
index = 0
sum = 0
```

에서 시작합니다.

---

### 3. 모든 숫자를 사용한 경우

```csharp
if (index == numbers.Length)
```

`index`가 배열 길이와 같다는 것은 모든 숫자에 대해 `+` 또는 `-` 선택을 끝냈다는 뜻입니다.

이때 현재 합이 `target`과 같으면 정답을 증가시킵니다.

```csharp
if (sum == target)
{
    answer++;
}
```

---

### 4. 현재 숫자에 대해 두 방향으로 탐색

```csharp
Dfs(numbers, target, index + 1, sum + numbers[index]);
Dfs(numbers, target, index + 1, sum - numbers[index]);
```

현재 숫자를 더하는 경우와 빼는 경우를 모두 확인합니다.

이렇게 하면 가능한 모든 부호 조합을 빠짐없이 탐색할 수 있습니다. 🔁

---

## ⏱️ 시간 복잡도

`N`은 `numbers`의 길이입니다.

시간 복잡도: `O(2^N)`

각 숫자마다 `+` 또는 `-` 두 가지 선택이 있으므로 전체 경우의 수는 `2^N`개입니다.

각 경우를 DFS로 한 번씩 방문합니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(N)`

재귀 호출 깊이가 최대 `N`까지 쌓입니다.

별도의 배열이나 컬렉션을 만들지 않으므로, 추가 공간은 재귀 호출 스택이 대부분입니다.

---

# 풀이 2. 짧은 코드 버전 — LINQ Aggregate

## 💡 아이디어

지금까지 만들 수 있는 합의 목록을 계속 갱신합니다.

처음에는 합이 `0` 하나뿐입니다.

숫자 하나를 볼 때마다 기존 합마다 두 값을 새로 만듭니다.

```text
기존 합 + 현재 숫자
기존 합 - 현재 숫자
```

마지막에는 만들어진 모든 합 중에서 `target`과 같은 값의 개수를 세면 됩니다. ✨

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(int[] numbers, int target)
    {
        return numbers
            .Aggregate(
                new List<int> { 0 },
                (sums, number) => sums
                    .SelectMany(sum => new[] { sum + number, sum - number })
                    .ToList())
            .Count(sum => sum == target);
    }
}
```

---

## 🔎 코드 해설

### 1. 시작 합

```csharp
new List<int> { 0 }
```

아무 숫자도 사용하지 않았을 때 만들 수 있는 합은 `0`입니다.

---

### 2. 가능한 합 갱신

```csharp
sums.SelectMany(sum => new[] { sum + number, sum - number })
```

기존에 만들 수 있던 각 합에서 현재 숫자를 더한 값과 뺀 값을 모두 만듭니다.

예를 들어 현재 가능한 합이 `[0]`이고 숫자가 `4`라면:

```text
0 + 4 = 4
0 - 4 = -4
```

다음 가능한 합은 `[4, -4]`가 됩니다.

---

### 3. target 개수 세기

```csharp
.Count(sum => sum == target);
```

모든 숫자를 처리한 뒤, 최종 합 목록에서 `target`과 같은 값의 개수를 세면 정답입니다.

---

## ⏱️ 시간 복잡도

`N`은 `numbers`의 길이입니다.

시간 복잡도: `O(2^N)`

매 숫자를 처리할 때 가능한 합의 개수가 2배씩 늘어납니다.

마지막에는 최대 `2^N`개의 합을 확인합니다.

---

## 🧺 공간 복잡도

공간 복잡도: `O(2^N)`

가능한 합을 리스트에 저장합니다.

DFS 풀이보다 코드가 짧지만, 중간 합들을 모두 저장하므로 메모리는 더 많이 사용합니다.

---

## ✅ 정리

이 문제는 각 숫자에 `+` 또는 `-`를 붙이는 모든 경우를 탐색하는 문제입니다.

핵심은 다음 한 줄입니다.

```text
각 숫자마다 선택지는 2개이고, 모든 선택 조합을 확인한다.
```

DFS 재귀 풀이는 메모리를 적게 쓰고 흐름을 이해하기 좋습니다.

LINQ 버전은 가능한 합 목록을 직접 만들어 가기 때문에 코드가 짧고 표현이 깔끔합니다.

코딩테스트에서는 DFS 재귀 풀이를 먼저 정확히 익히는 것을 추천합니다. 🚀
