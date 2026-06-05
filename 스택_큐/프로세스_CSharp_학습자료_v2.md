# 🖨️ 프로세스 — C# 학습자료

## 📌 1. 문제 핵심

운영체제는 실행 대기 중인 프로세스를 Queue에서 하나씩 꺼냅니다.

규칙은 다음과 같습니다.

```text
1. Queue 맨 앞의 프로세스를 꺼낸다.
2. Queue 안에 더 높은 우선순위의 프로세스가 있으면 다시 뒤에 넣는다.
3. 더 높은 우선순위가 없으면 실행한다.
4. 한 번 실행된 프로세스는 Queue로 돌아가지 않는다.
```

우리가 구해야 하는 것은 다음입니다.

```text
location 위치에 있던 프로세스가 몇 번째로 실행되는가?
```

입니다. 🎯

## 🧠 중요한 점

`priorities` 배열에는 우선순위만 들어 있습니다.

하지만 우리는 특정 위치 `location`에 있던 프로세스를 찾아야 합니다.

따라서 각 프로세스에는 두 가지 정보가 필요합니다.

```text
1. 우선순위
2. 원래 위치
```

예를 들어:

```text
priorities = [2, 1, 3, 2]
```

라면 Queue에는 다음처럼 넣으면 좋습니다.

```text
(2, 0), (1, 1), (3, 2), (2, 3)
```

여기서 `(3, 2)`는 다음 뜻입니다.

```text
우선순위는 3이고, 원래 위치는 2번이다.
```

# 🔍 예제 1 이해하기

```text
priorities = [2, 1, 3, 2]
location = 2
```

처음 상태:

```text
A: 2
B: 1
C: 3  ← 우리가 찾는 프로세스
D: 2
```

실행 순서는 다음과 같습니다.

```text
C → D → A → B
```

C는 첫 번째로 실행됩니다.

```text
return 1
```

✅

# 🔍 예제 2 이해하기

```text
priorities = [1, 1, 9, 1, 1, 1]
location = 0
```

처음 상태:

```text
A: 1  ← 우리가 찾는 프로세스
B: 1
C: 9
D: 1
E: 1
F: 1
```

C의 우선순위가 가장 높으므로 C가 먼저 실행됩니다.

그 뒤에는 모두 우선순위가 1이므로 Queue 순서대로 실행됩니다.

```text
C → D → E → F → A → B
```

A는 5번째로 실행됩니다.

```text
return 5
```

# 🧠 풀이 1. 이해하기 쉬운 방법 — Queue로 직접 시뮬레이션하기

## 💡 아이디어

문제 설명 그대로 구현합니다.

1. Queue에 `(우선순위, 원래 위치)`를 넣습니다.
2. Queue에서 하나 꺼냅니다.
3. Queue 안에 더 높은 우선순위가 있는지 확인합니다.
4. 있으면 다시 Queue 뒤에 넣습니다.
5. 없으면 실행 횟수를 증가시킵니다.
6. 실행된 프로세스의 원래 위치가 `location`이면 정답입니다.

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(int[] priorities, int location)
    {
        Queue<(int priority, int index)> queue = new Queue<(int priority, int index)>();

        for (int i = 0; i < priorities.Length; i++)
        {
            queue.Enqueue((priorities[i], i));
        }

        int order = 0;

        while (queue.Count > 0)
        {
            var current = queue.Dequeue();

            bool hasHigherPriority = queue.Any(process => process.priority > current.priority);

            if (hasHigherPriority)
            {
                queue.Enqueue(current);
            }
            else
            {
                order++;

                if (current.index == location)
                {
                    return order;
                }
            }
        }

        return -1;
    }
}
```

## 🔎 코드 해설

### 1. Queue에 우선순위와 위치 저장

```csharp
queue.Enqueue((priorities[i], i));
```

프로세스의 우선순위와 원래 위치를 함께 저장합니다.

예를 들어:

```text
(2, 0)
```

은 다음 뜻입니다.

```text
우선순위 2, 원래 위치 0번
```

📍 원래 위치를 저장해야 `location`의 프로세스를 찾을 수 있습니다.

### 2. Queue에서 하나 꺼내기

```csharp
var current = queue.Dequeue();
```

현재 실행 후보 프로세스를 꺼냅니다.

### 3. 더 높은 우선순위가 있는지 확인

```csharp
bool hasHigherPriority = queue.Any(process => process.priority > current.priority);
```

Queue 안에 현재 프로세스보다 우선순위가 높은 프로세스가 있는지 확인합니다.

있다면 현재 프로세스는 아직 실행될 수 없습니다.

### 4. 더 높은 우선순위가 있으면 뒤로 보내기

```csharp
queue.Enqueue(current);
```

다시 Queue의 맨 뒤에 넣습니다. 🔁

### 5. 실행 가능한 경우

```csharp
order++;
```

실제로 실행된 프로세스 수를 증가시킵니다.

그리고 우리가 찾는 프로세스인지 확인합니다.

```csharp
if (current.index == location)
{
    return order;
}
```

맞다면 현재 실행 순서가 정답입니다. 🎯

## ⏱️ 시간 복잡도

프로세스 개수를 `n`이라고 하겠습니다.

각 단계에서 `Any()`가 Queue를 훑을 수 있습니다.

```text
O(n)
```

이 작업이 여러 번 반복되므로 전체 시간 복잡도는:

```text
O(n²)
```

입니다.

하지만 제한사항에서 `n <= 100`이므로 충분히 빠릅니다. ✅

## 📦 공간 복잡도

Queue에 프로세스 정보를 저장합니다.

```text
O(n)
```

입니다.

# 🚀 풀이 2. 코드가 짧은 방법 — LINQ Select와 Max 사용하기

## 💡 아이디어

풀이 1과 같은 방식입니다.

다만 Queue를 만들 때 LINQ `Select`를 사용하고, 더 높은 우선순위 확인에는 `Max`를 사용합니다.

현재 프로세스보다 Queue 안의 최대 우선순위가 크면 다시 뒤로 보냅니다.

```text
current.priority < queue.Max(x => x.priority)
```

이면 아직 실행할 수 없습니다.

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(int[] priorities, int location)
    {
        var queue = new Queue<(int priority, int index)>(
            priorities.Select((priority, index) => (priority, index))
        );

        int answer = 0;

        while (true)
        {
            var current = queue.Dequeue();

            if (queue.Count > 0 && current.priority < queue.Max(x => x.priority))
            {
                queue.Enqueue(current);
                continue;
            }

            answer++;

            if (current.index == location)
            {
                return answer;
            }
        }
    }
}
```

## ✨ 코드 의미

### 1. Queue를 짧게 만들기

```csharp
priorities.Select((priority, index) => (priority, index))
```

`priorities` 배열을 다음 형태로 바꿉니다.

```text
(priority, index)
```

예를 들어:

```text
[2, 1, 3, 2]
```

는 다음처럼 바뀝니다.

```text
(2, 0), (1, 1), (3, 2), (2, 3)
```

### 2. Queue 안의 최대 우선순위 확인

```csharp
queue.Max(x => x.priority)
```

Queue에 남아 있는 프로세스 중 가장 높은 우선순위를 구합니다.

현재 프로세스의 우선순위가 이것보다 작으면 뒤로 보냅니다.

```csharp
queue.Enqueue(current);
continue;
```

🔁

### 3. 실행 순서 증가

```csharp
answer++;
```

현재 프로세스가 실행되었으므로 실행 순서를 1 증가시킵니다.

### 4. 찾는 프로세스인지 확인

```csharp
if (current.index == location)
{
    return answer;
}
```

원래 위치가 `location`과 같으면 정답을 반환합니다.

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ를 사용하므로 아래 네임스페이스를 반드시 추가해야 합니다.

```csharp
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

## ⏱️ 시간 복잡도

`Max()`가 Queue를 한 번 훑습니다.

이 작업이 여러 번 반복될 수 있으므로 전체 시간 복잡도는:

```text
O(n²)
```

입니다.

`n <= 100`이므로 문제 조건에서는 충분히 빠릅니다.

## 📦 공간 복잡도

Queue에 프로세스 정보를 저장합니다.

```text
O(n)
```

입니다.

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | Queue + Any | 흐름이 직관적이다 😊 | O(n²) | O(n) |
| 풀이 2 | Queue + Select + Max | 코드가 짧다 🚀 | O(n²) | O(n) |

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 Queue 시뮬레이션 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 문제 설명과 코드 흐름이 거의 같습니다.
2. 프로세스가 뒤로 밀리는 과정이 잘 보입니다.
3. Queue 자료구조를 연습하기 좋습니다.
```

LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

다만 `Select`, `Max` 문법이 익숙하지 않다면 처음에는 조금 어렵게 느껴질 수 있습니다. 🌱

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
Queue에서 하나 꺼낸 뒤,
더 높은 우선순위가 남아 있으면 뒤로 보낸다.
없으면 실행한다.
```

그리고 우리가 찾는 프로세스를 찾기 위해 반드시 원래 위치도 함께 저장해야 합니다.

```text
(priority, index)
```

🖨️ “프로세스” 문제는 Queue 시뮬레이션의 대표 문제입니다.
