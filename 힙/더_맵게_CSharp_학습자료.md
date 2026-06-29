# 🌶️ 더 맵게 — C# 학습자료

## 📌 1. 문제 핵심

여러 음식의 스코빌 지수가 배열 `scoville`에 들어 있습니다.

목표는 모든 음식의 스코빌 지수를 `K` 이상으로 만드는 것입니다.

음식을 섞는 규칙은 다음과 같습니다.

```text
새 음식의 스코빌 지수
= 가장 맵지 않은 음식
+ 두 번째로 맵지 않은 음식 × 2
```

즉, 가장 작은 두 값을 꺼내서 새 값을 만들고, 다시 음식 목록에 넣습니다.

이 과정을 모든 음식이 `K` 이상이 될 때까지 반복합니다. 🌶️

## 🔍 예제 이해하기

```text
scoville = [1, 2, 3, 9, 10, 12]
K = 7
```

처음 가장 작은 두 음식은 `1`, `2`입니다.

```text
1 + 2 × 2 = 5
```

새 음식 `5`를 다시 넣으면:

```text
[3, 5, 9, 10, 12]
```

아직 가장 작은 값 `3`이 `7`보다 작습니다.

다시 가장 작은 두 음식 `3`, `5`를 섞습니다.

```text
3 + 5 × 2 = 13
```

새 음식 `13`을 넣으면:

```text
[9, 10, 12, 13]
```

이제 모든 음식이 `7` 이상입니다.

섞은 횟수는:

```text
2
```

입니다. ✅

## ⚠️ 시간 초과가 나는 흔한 풀이

매번 배열을 정렬해서 가장 작은 두 값을 찾으면 느립니다.

예를 들어:

```text
정렬
가장 작은 두 개 꺼내기
새 값 넣기
다시 정렬
...
```

이렇게 하면 입력이 커질 때 매우 비효율적입니다.

`scoville`의 길이는 최대:

```text
1,000,000
```

입니다. 🚨

따라서 매번 전체 정렬을 하면 시간 초과가 날 가능성이 큽니다.

# 🧠 핵심 자료구조: 최소 힙

이 문제에서 필요한 작업은 반복적으로 다음 두 가지입니다.

```text
1. 가장 작은 값 꺼내기
2. 새 값 넣기
```

이 작업에 가장 적합한 자료구조가 **최소 힙**입니다.

C#에서는 `PriorityQueue<TElement, TPriority>`를 사용할 수 있습니다.

```csharp
PriorityQueue<int, int> pq = new PriorityQueue<int, int>();
```

값과 우선순위를 같은 값으로 넣으면 가장 작은 값이 먼저 나옵니다.

```csharp
pq.Enqueue(value, value);
```

가장 작은 값 꺼내기:

```csharp
int min = pq.Dequeue();
```

🎯 즉, `PriorityQueue`를 쓰면 가장 작은 두 음식을 빠르게 꺼낼 수 있습니다.

# 🧠 풀이 1. 이해하기 쉬운 방법 — PriorityQueue 사용하기

## 💡 아이디어

1. 모든 스코빌 지수를 최소 힙에 넣습니다.
2. 가장 작은 값이 `K` 이상이면 종료합니다.
3. 가장 작은 두 값을 꺼냅니다.
4. 새 스코빌 지수를 계산해서 다시 넣습니다.
5. 섞은 횟수를 증가시킵니다.
6. 더 이상 섞을 수 없는데 `K` 미만이면 `-1`을 반환합니다.

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[] scoville, int K)
    {
        PriorityQueue<int, int> pq = new PriorityQueue<int, int>();

        foreach (int s in scoville)
            pq.Enqueue(s, s);

        int answer = 0;

        while (pq.Count > 0 && pq.Peek() < K)
        {
            if (pq.Count < 2)
                return -1;

            int first = pq.Dequeue();
            int second = pq.Dequeue();

            int mixed = first + second * 2;

            pq.Enqueue(mixed, mixed);
            answer++;
        }

        return answer;
    }
}
```

## 🔎 코드 해설

### 1. PriorityQueue 만들기

```csharp
PriorityQueue<int, int> pq = new PriorityQueue<int, int>();
```

`PriorityQueue<TElement, TPriority>`는 우선순위가 낮은 값부터 꺼내는 자료구조입니다.

이 문제에서는 스코빌 지수가 낮은 음식부터 꺼내야 하므로 적합합니다. 🧺

### 2. 모든 음식 넣기

```csharp
foreach (int s in scoville)
{
    pq.Enqueue(s, s);
}
```

음식의 스코빌 지수와 우선순위를 같은 값으로 넣습니다.

```text
값: 3
우선순위: 3
```

이렇게 하면 가장 작은 스코빌 지수가 먼저 나옵니다.

### 3. 가장 작은 값 확인

```csharp
pq.Peek() < K
```

`Peek()`은 가장 작은 값을 꺼내지 않고 확인합니다.

가장 작은 값이 이미 `K` 이상이면 모든 음식이 `K` 이상입니다.

왜냐하면 최소 힙에서 가장 작은 값이 `K` 이상이면 나머지는 모두 그보다 크거나 같기 때문입니다. ✅

### 4. 섞을 음식이 부족한 경우

```csharp
if (pq.Count < 2)
{
    return -1;
}
```

가장 작은 값이 아직 `K`보다 작은데 음식이 하나뿐이면 더 이상 섞을 수 없습니다.

이 경우 모든 음식을 `K` 이상으로 만들 수 없으므로 `-1`입니다. 🚫

### 5. 가장 작은 두 음식 꺼내기

```csharp
int first = pq.Dequeue();
int second = pq.Dequeue();
```

`Dequeue()`를 두 번 호출하면 가장 작은 음식과 두 번째로 작은 음식을 꺼낼 수 있습니다.

### 6. 새 음식 만들기

```csharp
int mixed = first + second * 2;
```

문제에서 주어진 공식 그대로 계산합니다.

```text
가장 맵지 않은 음식 + 두 번째로 맵지 않은 음식 × 2
```

### 7. 새 음식 다시 넣기

```csharp
pq.Enqueue(mixed, mixed);
```

섞어서 만든 새 음식도 다시 음식 목록에 넣어야 합니다.

## ⏱️ 시간 복잡도

음식 개수를 `n`이라고 하겠습니다.

PriorityQueue에 넣고 빼는 연산은 각각:

```text
O(log n)
```

입니다.

전체적으로 최대 `n - 1`번 섞을 수 있습니다.

전체 시간 복잡도는:

```text
O(n log n)
```

입니다.

## 📦 공간 복잡도

PriorityQueue에 음식들을 저장합니다.

```text
O(n)
```

입니다.

# 🚀 풀이 2. 짧은 코드 버전 — PriorityQueue를 간결하게 쓰기

## 💡 아이디어

풀이 1과 같은 최소 힙 풀이입니다.

변수 이름과 흐름을 조금 줄여 더 짧게 작성할 수 있습니다.

프로그래머스 C# 제출 환경에서 실행 가능합니다. ✅

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int[] scoville, int K)
    {
        var pq = new PriorityQueue<int, int>();

        foreach (var s in scoville)
            pq.Enqueue(s, s);

        int count = 0;

        while (pq.Count >= 2 && pq.Peek() < K)
        {
            int mixed = pq.Dequeue() + pq.Dequeue() * 2;
            pq.Enqueue(mixed, mixed);
            count++;
        }

        return pq.Peek() >= K ? count : -1;
    }
}
```

## ✨ 짧은 코드의 핵심

### 1. 조건을 while에 압축

```csharp
while (pq.Count >= 2 && pq.Peek() < K)
```

음식이 2개 이상 있고, 가장 작은 값이 아직 `K`보다 작을 때만 섞습니다.

### 2. 가장 작은 두 값을 바로 섞기

```csharp
int mixed = pq.Dequeue() + pq.Dequeue() * 2;
```

첫 번째 `Dequeue()`가 가장 작은 값입니다.

두 번째 `Dequeue()`가 두 번째로 작은 값입니다.

공식 그대로 계산합니다. 🌶️

### 3. 마지막 상태 확인

```csharp
return pq.Peek() >= K ? count : -1;
```

반복이 끝난 뒤 가장 작은 값이 `K` 이상이면 성공입니다.

그렇지 않으면 더 이상 섞을 수 없어서 실패입니다.

## ⚠️ 주의할 점

이 코드는 마지막에 `pq.Peek()`를 호출합니다.

문제 조건에서 `scoville` 길이는 2 이상이므로 Queue가 완전히 비는 상황은 발생하지 않습니다.

따라서 안전하게 사용할 수 있습니다. ✅

## ⏱️ 시간 복잡도

```text
O(n log n)
```

입니다.

## 📦 공간 복잡도

```text
O(n)
```

입니다.

# ❌ 참고: LINQ 정렬 풀이가 위험한 이유

LINQ로 매번 정렬하면 코드는 짧아 보일 수 있습니다.

예를 들어 이런 흐름입니다.

```text
정렬
가장 작은 두 값 선택
새 값 추가
다시 정렬
```

하지만 매번 정렬하면 시간 복잡도가 크게 증가합니다.

```text
O(n² log n)
```

에 가까워질 수 있습니다.

`scoville` 길이가 최대 1,000,000이므로 이 방식은 피해야 합니다. 🚨

이 문제는 LINQ보다 `PriorityQueue`가 정답에 가까운 도구입니다.

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | PriorityQueue 기본 풀이 | 흐름이 이해하기 쉽다 😊 | O(n log n) | O(n) |
| 풀이 2 | PriorityQueue 간결 풀이 | 코드가 짧다 🚀 | O(n log n) | O(n) |

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 실패 조건을 명확히 볼 수 있습니다.
2. 가장 작은 두 값을 꺼내는 흐름이 잘 보입니다.
3. 최소 힙의 역할을 이해하기 좋습니다.
```

코딩 테스트 제출용으로 코드 길이를 우선한다면 **풀이 2번**이 더 좋습니다.

다만 `PriorityQueue` 사용법에 익숙하지 않다면 풀이 1번이 더 안전합니다. 🌱

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
가장 작은 두 음식을 계속 빠르게 꺼내야 한다.
```

따라서 필요한 자료구조는:

```text
최소 힙 / PriorityQueue
```

입니다.

흐름은 다음과 같습니다.

```text
1. 모든 음식을 PriorityQueue에 넣는다.
2. 가장 작은 값이 K 이상이면 종료한다.
3. 가장 작은 두 값을 꺼내 섞는다.
4. 새 음식을 다시 넣는다.
5. 더 이상 섞을 수 없으면 -1을 반환한다.
```

🌶️ “더 맵게”는 최소 힙을 연습하기 좋은 대표 문제입니다.
