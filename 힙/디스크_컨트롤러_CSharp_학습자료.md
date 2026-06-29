# 💽 디스크 컨트롤러 — C# 학습자료

## 📌 1. 문제 핵심

하드디스크는 한 번에 하나의 작업만 처리할 수 있습니다.

각 작업은 다음 정보를 가집니다.

```text
[요청 시각, 소요 시간]
```

예를 들어:

```text
jobs = [[0, 3], [1, 9], [3, 5]]
```

은 다음 뜻입니다.

```text
0번 작업: 0ms에 요청, 3ms 소요
1번 작업: 1ms에 요청, 9ms 소요
2번 작업: 3ms에 요청, 5ms 소요
```

우리가 구해야 하는 값은 모든 작업의 **평균 반환 시간**입니다.

---

## 🧠 반환 시간이란?

반환 시간은 다음과 같습니다.

```text
작업 종료 시각 - 작업 요청 시각
```

예를 들어 1ms에 요청된 작업이 17ms에 끝났다면:

```text
17 - 1 = 16ms
```

입니다. ⏱️

---

## 🎯 우선순위 규칙

현재 처리할 수 있는 작업이 여러 개라면 다음 순서로 우선순위가 높습니다.

```text
1. 소요 시간이 짧은 작업
2. 요청 시각이 빠른 작업
3. 작업 번호가 작은 작업
```

즉, 기본적으로는 **짧은 작업 먼저 처리하기**입니다. 🚀

---

# 🔍 예제 이해하기

```text
jobs = [[0, 3], [1, 9], [3, 5]]
```

## 0ms

0번 작업이 요청됩니다.

```text
0번 작업: 요청 0ms, 소요 3ms
```

바로 실행합니다.

```text
0ms ~ 3ms
```

0번 작업 종료 시각은 3ms입니다.

반환 시간:

```text
3 - 0 = 3
```

---

## 3ms

3ms 시점에는 다음 작업들이 대기 중입니다.

```text
1번 작업: 요청 1ms, 소요 9ms
2번 작업: 요청 3ms, 소요 5ms
```

소요 시간이 더 짧은 2번 작업을 먼저 실행합니다.

```text
3ms ~ 8ms
```

2번 작업 종료 시각은 8ms입니다.

반환 시간:

```text
8 - 3 = 5
```

---

## 8ms

남은 작업은 1번 작업입니다.

```text
8ms ~ 17ms
```

1번 작업 종료 시각은 17ms입니다.

반환 시간:

```text
17 - 1 = 16
```

---

## 평균 반환 시간

```text
0번 작업: 3
1번 작업: 16
2번 작업: 5
```

평균:

```text
(3 + 16 + 5) / 3 = 8
```

정답:

```text
8
```

✅

---

# ⚠️ 헷갈리기 쉬운 점

## 1. 요청되지 않은 작업은 고를 수 없습니다

예를 들어 현재 시간이 0ms라면, 3ms에 요청되는 작업은 아직 대기 큐에 넣으면 안 됩니다.

```text
현재 시각 <= 요청 시각
```

을 잘 확인해야 합니다.

---

## 2. 작업을 시작하면 중간에 멈출 수 없습니다

소요 시간이 더 짧은 작업이 나중에 들어와도, 현재 작업을 멈추고 바꿀 수 없습니다.

```text
한 번 시작한 작업은 끝까지 처리
```

입니다. 🛑

---

## 3. 대기 큐가 비어 있으면 시간을 점프해야 합니다

현재 시각에 처리할 수 있는 작업이 없다면, 다음 작업의 요청 시각으로 바로 이동하면 됩니다.

예를 들어 현재 시간이 0ms인데 첫 작업 요청이 10ms라면:

```text
time = 10
```

으로 이동합니다. ⏩

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 정렬 + PriorityQueue

## 💡 아이디어

1. 작업에 번호를 붙입니다.
2. 요청 시각 기준으로 작업을 정렬합니다.
3. 현재 시각까지 요청된 작업을 PriorityQueue에 넣습니다.
4. PriorityQueue에서 우선순위가 가장 높은 작업을 꺼냅니다.
5. 작업을 처리하고 반환 시간을 누적합니다.
6. 모든 작업을 처리할 때까지 반복합니다.

---

## 🧺 PriorityQueue 우선순위

C#의 `PriorityQueue<TElement, TPriority>`는 우선순위가 작은 것이 먼저 나옵니다.

이 문제의 우선순위는 다음 순서입니다.

```text
소요 시간 짧은 순
요청 시각 빠른 순
작업 번호 작은 순
```

C# 튜플은 앞에서부터 비교되므로 다음처럼 우선순위를 줄 수 있습니다.

```csharp
(job.Length, job.Start, job.Index)
```

처럼 사용합니다. 🎯

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(int[,] jobs)
    {
        int n = jobs.GetLength(0);

        var jobList = new List<(int Index, int Start, int Length)>();

        for (int i = 0; i < n; i++)
            jobList.Add((i, jobs[i, 0], jobs[i, 1]));

        jobList = jobList
            .OrderBy(job => job.Start)
            .ToList();

        var pq = new PriorityQueue<(int Index, int Start, int Length), (int Length, int Start, int Index)>();

        int time = 0;
        int jobIndex = 0;
        int completed = 0;
        int totalTurnaround = 0;

        while (completed < n)
        {
            while (jobIndex < n && jobList[jobIndex].Start <= time)
            {
                var job = jobList[jobIndex];
                pq.Enqueue(job, (job.Length, job.Start, job.Index));
                jobIndex++;
            }

            if (pq.Count == 0)
            {
                time = jobList[jobIndex].Start;
                continue;
            }

            var current = pq.Dequeue();

            time += current.Length;
            totalTurnaround += time - current.Start;
            completed++;
        }

        return totalTurnaround / n;
    }
}
```

---

## 🔎 코드 해설

### 1. 작업 정보 만들기

```csharp
jobList.Add((i, jobs[i, 0], jobs[i, 1]));
```

각 작업에 다음 정보를 저장합니다.

```text
작업 번호
요청 시각
소요 시간
```

작업 번호가 필요한 이유는 우선순위가 완전히 같을 때 번호가 작은 작업을 먼저 처리해야 하기 때문입니다. 📌

---

### 2. 요청 시각 기준 정렬

```csharp
jobList = jobList
    .OrderBy(job => job.Start)
    .ToList();
```

작업을 요청 시각 순서대로 정렬합니다.

이렇게 하면 현재 시각까지 요청된 작업을 앞에서부터 차례대로 PriorityQueue에 넣을 수 있습니다.

---

### 3. 현재 시각까지 요청된 작업 넣기

```csharp
while (jobIndex < n && jobList[jobIndex].Start <= time)
{
    var job = jobList[jobIndex];
    pq.Enqueue(job, (job.Length, job.Start, job.Index));
    jobIndex++;
}
```

현재 시각 `time`까지 요청된 작업들을 대기 큐에 넣습니다.

---

### 4. 대기 큐가 비어 있다면 시간 점프

```csharp
if (pq.Count == 0)
{
    time = jobList[jobIndex].Start;
    continue;
}
```

현재 처리할 수 있는 작업이 없다면 다음 작업의 요청 시각으로 이동합니다.

---

### 5. 우선순위가 가장 높은 작업 처리

```csharp
var current = pq.Dequeue();
```

소요 시간이 가장 짧고, 요청 시각이 빠르고, 번호가 작은 작업이 나옵니다.

---

### 6. 작업 종료 시각 갱신

```csharp
time += current.Length;
```

작업을 시작하면 끝날 때까지 처리하므로 현재 시각에 소요 시간을 더합니다.

---

### 7. 반환 시간 누적

```csharp
totalTurnaround += time - current.Start;
```

반환 시간은 다음입니다.

```text
작업 종료 시각 - 작업 요청 시각
```

---

## ⏱️ 시간 복잡도

작업 개수를 `n`이라고 하면:

정렬:

```text
O(n log n)
```

PriorityQueue 삽입/삭제:

```text
O(log n)
```

각 작업은 한 번 들어가고 한 번 나옵니다.

전체 시간 복잡도:

```text
O(n log n)
```

입니다.

---

## 📦 공간 복잡도

작업 목록과 PriorityQueue를 사용합니다.

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — LINQ + PriorityQueue

## 💡 아이디어

풀이 1과 같은 알고리즘입니다.

다만 별도 클래스를 만들지 않고 튜플을 사용해서 코드를 줄입니다.

작업 튜플은 다음 형태입니다.

```text
(index, start, length)
```

PriorityQueue 우선순위는 다음과 같습니다.

```text
(length, start, index)
```

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(int[,] jobs)
    {
        int n = jobs.GetLength(0);

        var sortedJobs = Enumerable.Range(0, n)
            .Select(i => (Index: i, Start: jobs[i, 0], Length: jobs[i, 1]))
            .OrderBy(job => job.Start)
            .ToArray();

        var pq = new PriorityQueue<(int Index, int Start, int Length), (int Length, int Start, int Index)>();

        int time = 0;
        int next = 0;
        int done = 0;
        int total = 0;

        while (done < n)
        {
            while (next < n && sortedJobs[next].Start <= time)
            {
                var job = sortedJobs[next];
                pq.Enqueue(job, (job.Length, job.Start, job.Index));
                next++;
            }

            if (pq.Count == 0)
            {
                time = sortedJobs[next].Start;
                continue;
            }

            var current = pq.Dequeue();

            time += current.Length;
            total += time - current.Start;
            done++;
        }

        return total / n;
    }
}
```

---

## ✨ 코드 의미

### 1. 작업 튜플 만들기

```csharp
Enumerable.Range(0, n)
    .Select(i => (Index: i, Start: jobs[i, 0], Length: jobs[i, 1]))
```

0부터 `n - 1`까지의 작업 번호를 만들고, 각 작업 정보를 튜플로 만듭니다.

```text
(Index, Start, Length)
```

---

### 2. 요청 시각 순으로 정렬

```csharp
.OrderBy(job => job.Start)
.ToArray();
```

요청 시각이 빠른 작업부터 정렬합니다.

---

### 3. PriorityQueue 선언

```csharp
var pq = new PriorityQueue<(int Index, int Start, int Length), (int Length, int Start, int Index)>();
```

작업 자체는 `(Index, Start, Length)` 형태로 저장합니다.

우선순위는 `(Length, Start, Index)`로 줍니다.

그래서 다음 순서대로 꺼내집니다.

```text
소요 시간 짧은 순
요청 시각 빠른 순
작업 번호 작은 순
```

---

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ와 PriorityQueue를 사용하므로 다음 네임스페이스가 필요합니다.

```csharp
using System.Collections.Generic;
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

---

## ⏱️ 시간 복잡도

정렬과 PriorityQueue 작업 때문에:

```text
O(n log n)
```

입니다.

---

## 📦 공간 복잡도

정렬된 작업 배열과 PriorityQueue를 사용합니다.

```text
O(n)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 튜플 + PriorityQueue | 흐름이 명확하다 😊 | O(n log n) | O(n) |
| 풀이 2 | 튜플 + LINQ + PriorityQueue | 코드가 짧다 🚀 | O(n log n) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 작업 번호, 요청 시각, 소요 시간이 명확하게 보입니다.
2. 대기 큐에 작업이 들어가는 흐름을 이해하기 쉽습니다.
3. 디버깅하기 좋습니다.
```

코딩 테스트 제출용으로 코드 길이를 우선한다면 **풀이 2번**이 더 좋습니다.

튜플과 LINQ에 익숙하다면 훨씬 간결합니다. ⚡

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
현재 시각까지 요청된 작업 중에서
소요 시간이 가장 짧은 작업을 먼저 처리한다.
```

풀이 흐름은 다음과 같습니다.

```text
1. 작업을 요청 시각 순으로 정렬한다.
2. 현재 시각까지 요청된 작업을 PriorityQueue에 넣는다.
3. PriorityQueue에서 우선순위가 가장 높은 작업을 꺼낸다.
4. 작업을 끝까지 처리한다.
5. 반환 시간을 누적한다.
6. 평균 반환 시간의 정수 부분을 반환한다.
```

💽 “디스크 컨트롤러”는 우선순위 큐와 시뮬레이션을 함께 사용하는 대표 문제입니다.
