# 🚚 다리를 지나는 트럭 — C# 학습자료

## 📌 1. 문제 핵심

트럭들이 정해진 순서대로 일차선 다리를 건너야 합니다.

다리에는 두 가지 제한이 있습니다.

```text
1. 다리 위에는 최대 bridge_length대의 트럭만 올라갈 수 있다.
2. 다리 위 트럭들의 총 무게는 weight 이하여야 한다.
```

모든 트럭이 다리를 완전히 건너는 데 걸리는 최소 시간을 구해야 합니다. ⏱️

---

## 🧠 핵심 아이디어

이 문제는 **Queue 시뮬레이션** 문제입니다.

다리를 하나의 Queue처럼 생각합니다.

예를 들어 `bridge_length = 2`라면, 다리 위에는 동시에 2칸이 있습니다.

```text
[앞쪽 칸, 뒤쪽 칸]
```

매초마다 다음 일이 일어납니다.

```text
1. 시간이 1초 흐른다.
2. 다리 맨 앞 칸의 트럭이 빠져나간다.
3. 새 트럭을 올릴 수 있으면 올린다.
4. 못 올리면 빈 칸 0을 넣는다.
```

여기서 빈 칸을 `0`으로 표현하면 다리 길이를 항상 일정하게 유지할 수 있습니다. 🌉

---

## 🔍 예제 이해하기

```text
bridge_length = 2
weight = 10
truck_weights = [7, 4, 5, 6]
```

처음 상태:

```text
다리: [0, 0]
대기 트럭: [7, 4, 5, 6]
현재 다리 무게: 0
시간: 0
```

### 1초

7kg 트럭이 올라갑니다.

```text
다리: [0, 7]
현재 다리 무게: 7
시간: 1
```

### 2초

4kg 트럭을 올리려고 하면:

```text
7 + 4 = 11
```

다리가 견딜 수 있는 무게 10을 넘습니다.

그래서 빈 칸을 넣습니다.

```text
다리: [7, 0]
현재 다리 무게: 7
시간: 2
```

### 3초

7kg 트럭이 빠져나갑니다.  
4kg 트럭이 올라갑니다.

```text
다리: [0, 4]
현재 다리 무게: 4
시간: 3
```

이런 식으로 계속 진행하면 모든 트럭이 다리를 건너는 데 총 8초가 걸립니다.

```text
return 8
```

✅

---

# ⚠️ 시간 초과가 나는 흔한 이유

다리 위 트럭 무게를 매번 이렇게 계산하면 비효율적입니다.

```csharp
bridge.Sum()
```

이 방식은 매초마다 다리 전체를 훑습니다.

`bridge_length`가 최대 10,000이고, 트럭 수도 최대 10,000이므로 불필요하게 느려질 수 있습니다. 🚨

대신 현재 다리 위 총 무게를 변수로 관리해야 합니다.

```csharp
int currentWeight = 0;
```

트럭이 다리에서 빠져나가면 빼고:

```csharp
currentWeight -= bridge.Dequeue();
```

트럭이 다리에 올라가면 더합니다.

```csharp
currentWeight += nextTruck;
```

이렇게 하면 매번 합계를 새로 구하지 않아도 됩니다. ⚡

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 다리 Queue를 실제 길이만큼 유지하기

## 💡 아이디어

다리 위 상태를 Queue로 관리합니다.

다리 Queue의 길이는 항상 `bridge_length`로 유지합니다.

트럭이 없는 빈 칸은 `0`으로 넣습니다.

매초마다:

```text
1. 다리 앞에서 하나 제거
2. 새 트럭을 올릴 수 있으면 추가
3. 못 올리면 0 추가
4. 시간 증가
```

합니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int bridge_length, int weight, int[] truck_weights)
    {
        Queue<int> bridge = new Queue<int>();

        for (int i = 0; i < bridge_length; i++)
            bridge.Enqueue(0);

        int time = 0, currentWeight = 0, truckIndex = 0;

        while (truckIndex < truck_weights.Length)
        {
            time++;

            currentWeight -= bridge.Dequeue();

            int nextTruck = truck_weights[truckIndex];

            if (currentWeight + nextTruck <= weight)
            {
                bridge.Enqueue(nextTruck);
                currentWeight += nextTruck;
                truckIndex++;
            }
            else
            {
                bridge.Enqueue(0);
            }
        }

        return time + bridge_length;
    }
}
```

---

## 🔎 코드 해설

### 1. 다리 Queue 만들기

```csharp
Queue<int> bridge = new Queue<int>();

for (int i = 0; i < bridge_length; i++)
{
    bridge.Enqueue(0);
}
```

처음에는 다리 위에 트럭이 없으므로 `0`으로 채웁니다.

예를 들어 `bridge_length = 2`라면:

```text
[0, 0]
```

입니다.

---

### 2. 시간 증가

```csharp
time++;
```

반복문 한 번은 1초가 흐른 것을 의미합니다. ⏱️

---

### 3. 다리 앞 칸 제거

```csharp
currentWeight -= bridge.Dequeue();
```

다리 맨 앞에 있던 값이 빠져나갑니다.

그 값이 트럭 무게라면 다리 위 무게에서 빠지고,  
`0`이라면 변화가 없습니다.

---

### 4. 다음 트럭 확인

```csharp
int nextTruck = truck_weights[truckIndex];
```

아직 다리에 올라가지 않은 다음 트럭입니다.

---

### 5. 트럭을 올릴 수 있는 경우

```csharp
if (currentWeight + nextTruck <= weight)
```

현재 다리 무게에 다음 트럭을 더해도 제한 무게를 넘지 않으면 트럭을 올립니다.

```csharp
bridge.Enqueue(nextTruck);
currentWeight += nextTruck;
truckIndex++;
```

---

### 6. 트럭을 올릴 수 없는 경우

```csharp
else
{
    bridge.Enqueue(0);
}
```

트럭을 못 올리면 빈 칸을 넣습니다.

그래야 다리 Queue의 길이가 계속 `bridge_length`로 유지됩니다. 🌉

---

### 7. 마지막에 `bridge_length`를 더하는 이유

반복문은 모든 트럭이 **다리에 올라간 순간** 끝납니다.

하지만 마지막 트럭이 다리를 완전히 빠져나가려면 `bridge_length`초가 더 필요합니다.

그래서:

```csharp
return time + bridge_length;
```

를 반환합니다.

---

## ⏱️ 시간 복잡도

각 초마다 Queue 연산을 합니다.

트럭은 최대 한 번씩 다리에 올라갑니다.

전체 시간은 대략:

```text
O(truck_weights.Length + bridge_length)
```

입니다.

문제 조건에서는 충분히 빠릅니다. ✅

---

## 📦 공간 복잡도

다리 Queue가 `bridge_length`만큼 공간을 사용합니다.

```text
O(bridge_length)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — Queue에 트럭과 0을 함께 넣기

## 💡 아이디어

풀이 1과 같은 방식입니다.

조금 더 짧게 쓰면 다음처럼 작성할 수 있습니다.

```text
다리에서 하나 빼고
다음 트럭을 올릴 수 있으면 트럭 무게 넣기
아니면 0 넣기
```

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(int bridge_length, int weight, int[] truck_weights)
    {
        Queue<int> bridge = new Queue<int>(new int[bridge_length]);

        int time = 0, currentWeight = 0, index = 0;

        while (index < truck_weights.Length)
        {
            time++;
            currentWeight -= bridge.Dequeue();

            if (currentWeight + truck_weights[index] <= weight)
            {
                currentWeight += truck_weights[index];
                bridge.Enqueue(truck_weights[index++]);
            }
            else
            {
                bridge.Enqueue(0);
            }
        }

        return time + bridge_length;
    }
}
```

---

## ✨ 짧은 코드의 핵심

### 1. 다리를 한 줄로 초기화

```csharp
Queue<int> bridge = new Queue<int>(new int[bridge_length]);
```

`new int[bridge_length]`는 0으로 채워진 배열입니다.

---

### 2. 트럭을 올리고 index 증가

```csharp
bridge.Enqueue(truck_weights[index++]);
```

현재 트럭을 다리에 올린 뒤, 다음 트럭을 가리키도록 `index`를 증가시킵니다. 🚚

---

## ⏱️ 시간 복잡도

Queue 연산은 모두 평균적으로 `O(1)`입니다.

전체 시간 복잡도는:

```text
O(truck_weights.Length + bridge_length)
```

입니다.

---

## 📦 공간 복잡도

다리 상태를 Queue로 저장합니다.

```text
O(bridge_length)
```

입니다.

---

# 💡 참고: 왜 `return time + bridge_length`가 가능할까?

위 두 풀이의 반복문은 마지막 트럭이 다리에 **올라가는 순간** 멈춥니다.

마지막 트럭은 다리에 올라간 뒤, 다리 길이만큼 시간이 지나야 완전히 빠져나갑니다.

예를 들어:

```text
bridge_length = 2
```

라면 트럭이 다리에 올라간 뒤 2초 후에 빠져나갑니다.

그래서 마지막에:

```csharp
return time + bridge_length;
```

를 해도 됩니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | Queue를 직접 초기화 | 이해하기 쉽다 😊 | O(t + b) | O(b) |
| 풀이 2 | 0 배열로 Queue 초기화 | 코드가 짧다 🚀 | O(t + b) | O(b) |

`t`는 트럭 수, `b`는 다리 길이입니다.

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 다리 상태가 Queue로 표현되는 과정이 잘 보입니다.
2. currentWeight를 왜 쓰는지 이해하기 쉽습니다.
3. 시간 흐름을 디버깅하기 좋습니다.
```

코드를 조금 더 짧게 쓰고 싶다면 **풀이 2번**도 좋습니다.

`new int[bridge_length]`로 0이 채워진 다리를 바로 만들 수 있습니다. ⚡

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
다리를 Queue로 표현하고, 매초 한 칸씩 이동시킨다.
```

시간 초과를 피하려면 다리 위 무게 합을 매번 새로 계산하지 말고:

```csharp
int currentWeight
```

로 관리해야 합니다.

흐름은 다음과 같습니다.

```text
1. 다리 Queue를 0으로 채운다.
2. 매초 다리 앞 칸을 제거한다.
3. 다음 트럭을 올릴 수 있으면 올린다.
4. 못 올리면 0을 넣는다.
5. 모든 트럭이 올라간 뒤 bridge_length초를 더한다.
```

🚚 “다리를 지나는 트럭”은 Queue 시뮬레이션의 대표 문제입니다.
