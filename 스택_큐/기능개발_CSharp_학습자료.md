# 🚀 기능개발 — C# 학습자료

## 📌 1. 문제 핵심

여러 기능을 개발하고 있습니다.

각 기능에는 두 가지 정보가 있습니다.

```text
progresses[i] → 현재 개발 진도
speeds[i]     → 하루에 개발되는 속도
```

기능은 진도가 `100%`가 되면 배포할 수 있습니다.

하지만 중요한 규칙이 있습니다.

```text
뒤 기능이 먼저 완성되어도,
앞 기능이 배포될 때까지 기다려야 한다.
```

즉, 배포 순서는 반드시 앞에서부터 지켜야 합니다. 🚦

---

## 🔍 예제 1 이해하기

```text
progresses = [93, 30, 55]
speeds     = [1, 30, 5]
```

각 기능이 완성되는 데 걸리는 날짜를 구해 봅시다.

### 1번 기능

```text
현재 93%
하루 1%
남은 진도 7%
필요 날짜 7일
```

### 2번 기능

```text
현재 30%
하루 30%
남은 진도 70%
필요 날짜 3일
```

2번 기능은 3일 만에 끝나지만, 1번 기능이 7일째에 끝납니다.

그래서 2번 기능은 1번 기능과 함께 7일째 배포됩니다. 📦

### 3번 기능

```text
현재 55%
하루 5%
남은 진도 45%
필요 날짜 9일
```

3번 기능은 9일째에 배포됩니다.

따라서 결과는:

```text
[2, 1]
```

입니다. ✅

---

# 🧠 핵심 아이디어

먼저 각 기능이 완성되는 데 필요한 날짜를 구합니다.

```text
필요 날짜 = ceiling((100 - 현재 진도) / 개발 속도)
```

예를 들어:

```text
현재 진도 95
개발 속도 4
남은 진도 5
```

5를 4로 나누면 1.25입니다.

하지만 하루 단위로 배포하므로 2일이 필요합니다.

```text
ceil(5 / 4) = 2
```

C#에서는 정수 나눗셈으로 올림을 이렇게 계산할 수 있습니다.

```csharp
int days = (100 - progress + speed - 1) / speed;
```

이 공식은 자주 쓰입니다. 🧮

---

# 🧩 배포 묶음 만들기

필요 날짜가 다음과 같다고 해봅시다.

```text
[7, 3, 9]
```

첫 번째 기능은 7일째 배포됩니다.

두 번째 기능은 3일째 이미 끝났지만, 앞 기능이 7일째 배포되므로 함께 배포됩니다.

```text
7일째: 1번, 2번 기능 → 2개
```

세 번째 기능은 9일째 끝나므로 새 배포 묶음이 됩니다.

```text
9일째: 3번 기능 → 1개
```

결과:

```text
[2, 1]
```

🎯 핵심은 현재 배포 기준일보다 빨리 끝난 기능은 같은 묶음에 넣는 것입니다.

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 날짜 배열을 만든 뒤 묶기

## 💡 아이디어

1. 각 기능의 완료 날짜를 계산합니다.
2. 앞에서부터 보면서 배포 묶음을 만듭니다.
3. 현재 배포 기준일보다 작거나 같은 기능은 같은 날 배포합니다.
4. 더 늦게 끝나는 기능이 나오면 새 배포 묶음을 시작합니다.

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int[] solution(int[] progresses, int[] speeds)
    {
        int n = progresses.Length;
        int[] days = new int[n];

        for (int i = 0; i < n; i++)
        {
            int remaining = 100 - progresses[i];
            days[i] = (remaining + speeds[i] - 1) / speeds[i];
        }

        List<int> answer = new List<int>();

        int deployDay = days[0];
        int count = 1;

        for (int i = 1; i < n; i++)
        {
            if (days[i] <= deployDay)
            {
                count++;
            }
            else
            {
                answer.Add(count);
                deployDay = days[i];
                count = 1;
            }
        }

        answer.Add(count);

        return answer.ToArray();
    }
}
```

---

## 🔎 코드 해설

### 1. 완료까지 필요한 날짜 계산

```csharp
int remaining = 100 - progresses[i];
days[i] = (remaining + speeds[i] - 1) / speeds[i];
```

예를 들어:

```text
progress = 93
speed = 1
remaining = 7
days = 7
```

입니다.

또 다른 예:

```text
progress = 95
speed = 4
remaining = 5
days = (5 + 4 - 1) / 4 = 8 / 4 = 2
```

정수 나눗셈으로 올림을 처리했습니다. ✅

---

### 2. 첫 번째 기능을 기준으로 시작

```csharp
int deployDay = days[0];
int count = 1;
```

첫 번째 기능은 반드시 첫 배포 묶음에 들어갑니다.

`deployDay`는 현재 배포 묶음의 기준 날짜입니다.

---

### 3. 같은 배포에 포함되는 경우

```csharp
if (days[i] <= deployDay)
{
    count++;
}
```

뒤 기능이 현재 배포 기준일보다 빨리 끝났거나 같은 날 끝났다면, 함께 배포됩니다.

예:

```text
deployDay = 7
days[i] = 3
```

이면 3일째 이미 완성되었지만, 앞 기능이 7일째 배포되므로 같이 배포됩니다. 📦

---

### 4. 새 배포 묶음 시작

```csharp
else
{
    answer.Add(count);
    deployDay = days[i];
    count = 1;
}
```

현재 기능이 더 늦게 끝난다면 새로운 배포 묶음이 시작됩니다.

---

### 5. 마지막 묶음 추가

```csharp
answer.Add(count);
```

반복문이 끝난 뒤 마지막 배포 묶음을 추가해야 합니다.

이 줄을 빼먹으면 마지막 배포가 결과에 들어가지 않습니다. ⚠️

---

## ⏱️ 시간 복잡도

완료 날짜 계산에 한 번, 배포 묶음 계산에 한 번 순회합니다.

```text
O(n)
```

`n`은 기능의 개수입니다.

---

## 📦 공간 복잡도

완료 날짜 배열과 결과 리스트를 사용합니다.

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — 날짜 배열 없이 바로 묶기

## 💡 아이디어

굳이 `days` 배열을 따로 만들지 않고, 각 기능의 완료 날짜를 계산하면서 바로 배포 묶음을 만들 수 있습니다.

흐름은 같습니다.

```text
현재 기능의 완료 날짜를 계산한다.
현재 배포 기준일보다 빠르면 같은 묶음
더 늦으면 새 묶음
```

---

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;

public class Solution
{
    public int[] solution(int[] progresses, int[] speeds)
    {
        List<int> answer = new List<int>();

        int deployDay = 0;
        int count = 0;

        for (int i = 0; i < progresses.Length; i++)
        {
            int day = (100 - progresses[i] + speeds[i] - 1) / speeds[i];

            if (day > deployDay)
            {
                if (count > 0)
                {
                    answer.Add(count);
                }

                deployDay = day;
                count = 1;
            }
            else
            {
                count++;
            }
        }

        answer.Add(count);

        return answer.ToArray();
    }
}
```

---

## ✨ 이 코드의 핵심

```csharp
int day = (100 - progresses[i] + speeds[i] - 1) / speeds[i];
```

현재 기능의 완료 날짜를 바로 계산합니다.

```csharp
if (day > deployDay)
```

현재 배포 기준일보다 늦게 끝나면 새 배포가 필요합니다.

```csharp
else
{
    count++;
}
```

현재 배포 기준일 안에 끝나는 기능이라면 같은 배포에 포함합니다. 🎁

---

## ⏱️ 시간 복잡도

배열을 한 번만 순회합니다.

```text
O(n)
```

---

## 📦 공간 복잡도

완료 날짜 배열을 만들지 않습니다.

결과 리스트만 사용합니다.

```text
O(k)
```

`k`는 배포 횟수입니다.

최악의 경우 모든 기능이 따로 배포되면:

```text
O(n)
```

입니다.

---

# ✨ 참고: LINQ로 완료 날짜 만들기

LINQ를 사용하면 완료 날짜 배열을 짧게 만들 수 있습니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int[] solution(int[] progresses, int[] speeds)
    {
        var days = progresses
            .Select((progress, i) => (100 - progress + speeds[i] - 1) / speeds[i])
            .ToArray();

        List<int> answer = new List<int>();

        int deployDay = days[0];
        int count = 1;

        foreach (int day in days.Skip(1))
        {
            if (day <= deployDay)
            {
                count++;
            }
            else
            {
                answer.Add(count);
                deployDay = day;
                count = 1;
            }
        }

        answer.Add(count);

        return answer.ToArray();
    }
}
```

`using System.Linq;`를 추가하면 프로그래머스에서도 실행할 수 있습니다. ✅

---

# ⚖️ 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 완료 날짜 배열 생성 후 묶기 | 이해하기 쉽다 😊 | O(n) | O(n) |
| 풀이 2 | 날짜 계산과 묶기를 동시에 처리 | 코드가 짧고 메모리를 덜 쓴다 🚀 | O(n) | O(n) |
| LINQ 참고 풀이 | `Select`로 날짜 계산 | 표현이 깔끔하다 ✨ | O(n) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 각 기능의 완료 날짜가 눈에 보입니다.
2. 배포 묶음이 만들어지는 과정을 이해하기 쉽습니다.
3. 디버깅하기 쉽습니다.
```

프로그래머스 제출용으로는 **풀이 2번**도 좋습니다.

완료 날짜 배열을 따로 만들지 않아 코드가 조금 더 간결합니다. ⚡

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
뒤 기능이 먼저 끝나도 앞 기능보다 먼저 배포될 수 없다.
```

따라서:

```text
1. 각 기능의 완료 날짜를 구한다.
2. 앞 기능의 배포 날짜를 기준으로 뒤 기능을 묶는다.
3. 더 늦게 끝나는 기능이 나오면 새 배포 묶음을 시작한다.
```

📦 “기능개발”은 완료 날짜를 계산한 뒤, 순서대로 배포 묶음을 세는 문제입니다.
