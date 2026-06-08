# 🏃 체육복 — C# 학습자료

## 📌 1. 문제 핵심

학생들은 체육수업을 들으려면 체육복이 필요합니다.

그런데 일부 학생은 체육복을 도난당했고, 일부 학생은 여벌 체육복을 가지고 있습니다.

여벌 체육복이 있는 학생은 바로 앞번호 또는 바로 뒷번호 학생에게만 빌려줄 수 있습니다.

예를 들어:

```text
4번 학생 → 3번 또는 5번에게만 빌려줄 수 있음
```

목표는 다음입니다.

```text
체육수업을 들을 수 있는 학생 수의 최댓값 구하기
```

🎯

## ⚠️ 가장 중요한 예외

여벌 체육복을 가져온 학생이 체육복을 도난당했을 수 있습니다.

예를 들어:

```text
lost = [3]
reserve = [3]
```

3번 학생은 여벌 체육복이 있었지만 하나를 도난당했습니다.

그래서 남은 체육복은 1벌입니다.

즉:

```text
3번 학생은 수업은 들을 수 있지만,
다른 학생에게 빌려줄 수는 없음
```

입니다. 🚨

따라서 먼저 해야 할 일은:

```text
lost와 reserve에 동시에 있는 학생 처리하기
```

입니다.

# 🔍 예제 1 이해하기

```text
n = 5
lost = [2, 4]
reserve = [1, 3, 5]
```

2번 학생은 1번 또는 3번에게 빌릴 수 있습니다.

4번 학생은 3번 또는 5번에게 빌릴 수 있습니다.

가능한 방법:

```text
1번 → 2번에게 빌려줌
3번 → 4번에게 빌려줌
```

또는:

```text
1번 → 2번에게 빌려줌
5번 → 4번에게 빌려줌
```

모든 학생이 체육수업을 들을 수 있습니다.

```text
return 5
```

✅

# 🔍 예제 2 이해하기

```text
n = 5
lost = [2, 4]
reserve = [3]
```

3번 학생은 2번 또는 4번 중 한 명에게만 빌려줄 수 있습니다.

따라서 한 명은 체육복을 빌릴 수 있지만, 다른 한 명은 빌릴 수 없습니다.

수업을 들을 수 있는 학생 수는:

```text
5 - 1 = 4
```

정답:

```text
4
```

# 🧠 핵심 아이디어

이 문제는 탐욕법, 즉 그리디 문제입니다.

앞 번호 학생부터 차례대로 확인하면서 빌릴 수 있으면 빌려줍니다.

가장 안전한 방식은 학생별 체육복 개수를 직접 관리하는 것입니다.

```text
0벌 → 체육복 없음
1벌 → 수업 가능
2벌 → 여벌 있음
```

이렇게 만들면 도난과 여벌이 겹치는 학생도 자연스럽게 처리됩니다. 😊

# 🧠 풀이 1. 이해하기 쉬운 방법 — 배열로 체육복 개수 관리하기

## 💡 아이디어

학생마다 체육복 개수를 저장합니다.

처음에는 모두 1벌씩 가지고 있다고 생각합니다.

```text
모든 학생: 1벌
도난 학생: -1
여벌 학생: +1
```

그다음 체육복이 없는 학생을 앞에서부터 확인하면서, 앞번호나 뒷번호가 2벌이면 빌립니다.

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int solution(int n, int[] lost, int[] reserve)
    {
        int[] clothes = new int[n + 1];

        for (int i = 1; i <= n; i++)
        {
            clothes[i] = 1;
        }

        foreach (int student in lost)
        {
            clothes[student]--;
        }

        foreach (int student in reserve)
        {
            clothes[student]++;
        }

        for (int student = 1; student <= n; student++)
        {
            if (clothes[student] != 0)
            {
                continue;
            }

            if (student > 1 && clothes[student - 1] == 2)
            {
                clothes[student - 1]--;
                clothes[student]++;
            }
            else if (student < n && clothes[student + 1] == 2)
            {
                clothes[student + 1]--;
                clothes[student]++;
            }
        }

        int answer = 0;

        for (int student = 1; student <= n; student++)
        {
            if (clothes[student] >= 1)
            {
                answer++;
            }
        }

        return answer;
    }
}
```

## 🔎 코드 해설

### 1. 학생별 체육복 개수 배열

```csharp
int[] clothes = new int[n + 1];
```

학생 번호가 1번부터 시작하므로 배열 크기를 `n + 1`로 만듭니다.

`clothes[1]`은 1번 학생의 체육복 개수입니다.

### 2. 모든 학생에게 기본 체육복 1벌 지급

```csharp
for (int i = 1; i <= n; i++)
{
    clothes[i] = 1;
}
```

처음에는 모든 학생이 체육복을 1벌 가지고 있다고 생각합니다.

### 3. 도난 학생 처리

```csharp
foreach (int student in lost)
{
    clothes[student]--;
}
```

체육복을 도난당한 학생은 1벌 줄어듭니다.

### 4. 여벌 학생 처리

```csharp
foreach (int student in reserve)
{
    clothes[student]++;
}
```

여벌이 있는 학생은 1벌 늘어납니다.

여기서 도난도 당하고 여벌도 있는 학생은:

```text
1 - 1 + 1 = 1
```

이 됩니다.

즉, 본인은 수업을 들을 수 있지만 남에게 빌려줄 수는 없습니다. ✅

### 5. 체육복 없는 학생 찾기

```csharp
if (clothes[student] != 0)
{
    continue;
}
```

체육복이 0벌인 학생만 빌려야 합니다.

### 6. 앞번호 학생에게 먼저 빌리기

```csharp
if (student > 1 && clothes[student - 1] == 2)
```

앞번호 학생이 있고, 그 학생이 2벌 가지고 있으면 빌릴 수 있습니다.

### 7. 뒷번호 학생에게 빌리기

```csharp
else if (student < n && clothes[student + 1] == 2)
```

앞번호에게 빌릴 수 없으면 뒷번호를 확인합니다.

### 8. 수업 가능한 학생 수 세기

```csharp
if (clothes[student] >= 1)
{
    answer++;
}
```

체육복이 1벌 이상 있으면 수업을 들을 수 있습니다.

## ⏱️ 시간 복잡도

학생 수를 `n`이라고 하면 학생 배열을 몇 번 순회합니다.

```text
O(n)
```

입니다.

## 📦 공간 복잡도

학생별 체육복 개수 배열을 사용합니다.

```text
O(n)
```

입니다.

# 🚀 풀이 2. 코드가 짧은 방법 — HashSet 사용하기

## 💡 아이디어

도난 학생과 여벌 학생을 각각 `HashSet<int>`로 관리합니다.

먼저 도난도 당하고 여벌도 있는 학생을 제거합니다.

그다음 남은 도난 학생을 정렬해서 앞에서부터 처리합니다.

## 💻 C# 코드

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(int n, int[] lost, int[] reserve)
    {
        var lostSet = new HashSet<int>(lost);
        var reserveSet = new HashSet<int>(reserve);

        foreach (int student in lost.Intersect(reserve).ToArray())
        {
            lostSet.Remove(student);
            reserveSet.Remove(student);
        }

        foreach (int student in lostSet.OrderBy(x => x).ToArray())
        {
            if (reserveSet.Remove(student - 1))
            {
                lostSet.Remove(student);
            }
            else if (reserveSet.Remove(student + 1))
            {
                lostSet.Remove(student);
            }
        }

        return n - lostSet.Count;
    }
}
```

## ✨ 코드 의미

### 1. HashSet으로 변환

```csharp
var lostSet = new HashSet<int>(lost);
var reserveSet = new HashSet<int>(reserve);
```

`HashSet`은 특정 학생 번호가 있는지 빠르게 확인하고 제거할 수 있습니다.

### 2. 도난과 여벌이 겹치는 학생 제거

```csharp
foreach (int student in lost.Intersect(reserve).ToArray())
{
    lostSet.Remove(student);
    reserveSet.Remove(student);
}
```

도난도 당하고 여벌도 있는 학생은 자기 체육복만 남습니다.

그래서 빌릴 필요도 없고, 빌려줄 수도 없습니다.

두 Set에서 모두 제거합니다. 🧹

### 3. 도난 학생을 앞번호부터 처리

```csharp
foreach (int student in lostSet.OrderBy(x => x).ToArray())
```

앞번호 학생부터 차례대로 처리합니다.

`ToArray()`를 붙이는 이유는 반복 중에 `lostSet.Remove()`로 Set을 수정하기 때문입니다.

Set을 순회하면서 동시에 수정하면 오류가 날 수 있습니다. ⚠️

### 4. 앞번호 학생에게 빌리기

```csharp
if (reserveSet.Remove(student - 1))
```

`Remove()`는 해당 값이 있으면 제거하고 `true`를 반환합니다.

즉, 앞번호 학생이 여벌을 가지고 있으면 바로 빌리는 효과가 납니다.

### 5. 뒷번호 학생에게 빌리기

```csharp
else if (reserveSet.Remove(student + 1))
```

앞번호에게 못 빌렸다면 뒷번호에게 빌려 봅니다.

### 6. 빌렸다면 lostSet에서 제거

```csharp
lostSet.Remove(student);
```

체육복을 빌린 학생은 더 이상 체육복이 없는 학생이 아닙니다.

### 7. 최종 수업 가능 인원

```csharp
return n - lostSet.Count;
```

마지막까지 체육복을 못 구한 학생 수가 `lostSet.Count`입니다.

전체 학생 수에서 이 수를 빼면 수업 가능한 학생 수입니다. ✅

## ⚠️ 프로그래머스에서 실행할 때 주의

이 풀이에서는 LINQ를 사용하므로 다음 네임스페이스가 필요합니다.

```csharp
using System.Linq;
```

`ToHashSet()` 대신 `new HashSet<int>(lost)`를 사용하면 프로그래머스 환경에서 더 안전합니다. 🌱

## ⏱️ 시간 복잡도

정렬 때문에:

```text
O(l log l)
```

입니다.

여기서 `l`은 도난 학생 수입니다.

학생 수가 최대 30이므로 매우 빠릅니다. ⚡

## 📦 공간 복잡도

HashSet을 사용합니다.

```text
O(l + r)
```

입니다.

`l`은 도난 학생 수, `r`은 여벌 학생 수입니다.

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 학생별 체육복 개수 배열 | 가장 이해하기 쉽다 😊 | O(n) | O(n) |
| 풀이 2 | HashSet + 정렬 | 코드가 짧고 깔끔하다 🚀 | O(l log l) | O(l + r) |

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 배열 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 학생별 체육복 개수가 눈에 잘 보입니다.
2. 도난과 여벌이 겹치는 경우가 자연스럽게 처리됩니다.
3. 그리디 흐름을 이해하기 쉽습니다.
```

HashSet과 LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

단, 프로그래머스 환경 호환성을 생각하면 `new HashSet<int>(lost)` 방식이 더 안전합니다. ✅

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
여벌이 있지만 도난도 당한 학생은 다른 학생에게 빌려줄 수 없다.
```

풀이 흐름은 다음과 같습니다.

```text
1. 도난과 여벌이 겹치는 학생을 먼저 처리한다.
2. 체육복이 없는 학생을 앞번호부터 확인한다.
3. 앞번호 학생에게 먼저 빌려 본다.
4. 안 되면 뒷번호 학생에게 빌려 본다.
5. 끝까지 못 빌린 학생 수를 전체에서 뺀다.
```

🏃 “체육복”은 그리디와 예외 처리를 함께 연습하기 좋은 대표 문제입니다.
