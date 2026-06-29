# 👕 의상 — C# 학습자료

## 📌 1. 문제 핵심

코니는 여러 종류의 옷을 가지고 있습니다.

예를 들어:

| 종류 | 의상 |
|---|---|
| headgear | yellow_hat, green_turban |
| eyewear | blue_sunglasses |

코니는 각 종류별로 **최대 1개**만 입을 수 있습니다.

즉:

```text
headgear에서 yellow_hat과 green_turban을 동시에 입을 수는 없음
```

하지만 서로 다른 종류는 함께 입을 수 있습니다.

```text
yellow_hat + blue_sunglasses
```

는 가능합니다. ✅

그리고 코니는 하루에 **최소 1개 이상의 의상**은 입어야 합니다.

---

## 🧠 핵심 아이디어

각 의상 종류마다 선택지는 다음과 같습니다.

```text
그 종류의 의상 중 하나를 고른다
또는
그 종류는 아예 안 입는다
```

예를 들어 `headgear`에 의상이 2개 있다면:

```text
yellow_hat
green_turban
안 입음
```

총 3가지 선택이 있습니다.

즉:

```text
의상 개수 + 1
```

입니다. 🎯

---

# 🔍 예제 1 이해하기

```text
clothes = [
  ["yellow_hat", "headgear"],
  ["blue_sunglasses", "eyewear"],
  ["green_turban", "headgear"]
]
```

종류별 개수는 다음과 같습니다.

```text
headgear → 2개
eyewear  → 1개
```

각 종류별 선택지는:

```text
headgear → 2개 중 하나 선택 또는 안 입음 → 3가지
eyewear  → 1개 중 하나 선택 또는 안 입음 → 2가지
```

전체 조합 수는:

```text
3 × 2 = 6
```

하지만 여기에는 아무것도 안 입는 경우가 포함되어 있습니다.

```text
headgear 안 입음 + eyewear 안 입음
```

이 경우는 문제 조건에 맞지 않습니다. 🚫

그래서 1을 빼야 합니다.

```text
6 - 1 = 5
```

정답은:

```text
5
```

입니다. ✅

---

# 🔍 예제 2 이해하기

```text
clothes = [
  ["crow_mask", "face"],
  ["blue_sunglasses", "face"],
  ["smoky_makeup", "face"]
]
```

종류는 `face` 하나뿐입니다.

```text
face → 3개
```

선택지는:

```text
crow_mask
blue_sunglasses
smoky_makeup
안 입음
```

총 4가지입니다.

하지만 아무것도 안 입는 경우는 제외합니다.

```text
4 - 1 = 3
```

정답은:

```text
3
```

입니다. 🎉

---

# 🧠 공식으로 정리하기

종류별 의상 개수가 다음과 같다고 해봅시다.

```text
headgear → 2개
eyewear  → 1개
outer    → 1개
```

그러면 전체 조합 수는:

```text
(2 + 1) × (1 + 1) × (1 + 1) - 1
```

입니다.

즉:

```text
정답 = 모든 종류에 대해 (의상 개수 + 1)을 곱한 뒤, 1을 뺀 값
```

여기서 `+1`은 “그 종류를 안 입는 경우”입니다. 🧢

마지막 `-1`은 “모든 종류를 다 안 입는 경우”입니다. 🚫

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — Dictionary로 종류별 개수 세기

## 💡 아이디어

1. `Dictionary<string, int>`로 의상 종류별 개수를 셉니다.
2. 각 종류마다 `(개수 + 1)`을 곱합니다.
3. 마지막에 아무것도 입지 않는 경우 1을 뺍니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;

public class Solution
{
    public int solution(string[,] clothes)
    {
        Dictionary<string, int> countByType = new Dictionary<string, int>();

        for (int i = 0; i < clothes.GetLength(0); i++)
        {
            string type = clothes[i, 1];
            countByType[type] = countByType.GetValueOrDefault(type) + 1;
        }

        int answer = 1;

        foreach (int count in countByType.Values)
            answer *= count + 1;

        return answer - 1;
    }
}
```

---

## ⚠️ 프로그래머스 C# 입력 형태 주의

프로그래머스의 이 문제는 C#에서 보통 다음 형태로 들어옵니다.

```csharp
string[,] clothes
```

즉, 2차원 배열입니다.

각 행은 다음 구조입니다.

```text
[의상 이름, 의상 종류]
```

따라서 의상 종류는 다음처럼 꺼냅니다.

```csharp
string type = clothes[i, 1];
```

의상 이름은 조합 수 계산에 직접 필요하지 않습니다.

```csharp
clothes[i, 0] → 의상 이름
clothes[i, 1] → 의상 종류
```

---

## 🔎 코드 해설

### 1. 종류별 개수 저장하기

```csharp
Dictionary<string, int> countByType = new Dictionary<string, int>();
```

의상 종류를 key로, 개수를 value로 저장합니다.

예를 들어:

```text
headgear → 2
eyewear  → 1
```

처럼 저장됩니다. 📒

---

### 2. 의상 종류 꺼내기

```csharp
string type = clothes[i, 1];
```

각 옷 정보는 다음과 같습니다.

```text
clothes[i, 0] = 의상 이름
clothes[i, 1] = 의상 종류
```

이 문제에서 중요한 것은 종류별 개수이므로 `clothes[i, 1]`만 사용합니다.

---

### 3. 종류별 개수 증가

```csharp
countByType[type] = countByType.GetValueOrDefault(type) + 1;
```

`GetValueOrDefault()`는 처음 보는 종류라면 `0`을 반환합니다.

같은 종류의 옷이 하나 더 있다는 뜻입니다. 👕

---

### 4. 조합 수 계산

```csharp
int answer = 1;

foreach (int count in countByType.Values)
{
    answer *= count + 1;
}
```

각 종류마다 다음 선택지가 있습니다.

```text
그 종류의 옷 중 하나를 입기
또는
그 종류를 안 입기
```

그래서 `count + 1`을 곱합니다.

---

### 5. 아무것도 안 입는 경우 제거

```csharp
return answer - 1;
```

모든 종류에서 “안 입음”을 선택하는 경우는 문제 조건에 맞지 않습니다.

그래서 마지막에 1을 뺍니다. ✅

---

## ⏱️ 시간 복잡도

의상 목록을 한 번 순회합니다.

```text
O(n)
```

`n`은 의상의 개수입니다.

---

## 📦 공간 복잡도

의상 종류별 개수를 Dictionary에 저장합니다.

```text
O(k)
```

`k`는 의상 종류의 수입니다.

최악의 경우 모든 의상이 다른 종류라면:

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — LINQ GroupBy 사용하기

## 💡 아이디어

LINQ의 `GroupBy()`를 사용하면 의상 종류별로 쉽게 묶을 수 있습니다.

종류별로 묶은 뒤, 각 그룹의 개수에 1을 더해서 모두 곱합니다.

---

## 💻 C# 코드

```csharp
using System.Linq;

public class Solution
{
    public int solution(string[,] clothes)
    {
        return Enumerable.Range(0, clothes.GetLength(0))
            .GroupBy(i => clothes[i, 1])
            .Aggregate(1, (answer, group) => answer * (group.Count() + 1))
            - 1;
    }
}
```

---

## ✨ 코드 의미

### 1. 행 번호 만들기

```csharp
Enumerable.Range(0, clothes.GetLength(0))
```

`clothes`의 행 번호를 만듭니다.

예를 들어 옷이 3개라면:

```text
0, 1, 2
```

를 만듭니다.

---

### 2. 의상 종류별로 묶기

```csharp
.GroupBy(i => clothes[i, 1])
```

각 행 번호 `i`에 대해 의상 종류 `clothes[i, 1]`를 기준으로 묶습니다.

예를 들어:

```text
headgear → 0번, 2번
eyewear  → 1번
```

처럼 묶입니다. 🧺

---

### 3. 조합 수 누적해서 곱하기

```csharp
.Aggregate(1, (answer, group) => answer * (group.Count() + 1))
```

각 종류마다 `(개수 + 1)`을 곱합니다.

`Aggregate(1, ...)`에서 `1`은 곱셈의 시작값입니다.

---

### 4. 아무것도 안 입는 경우 빼기

```csharp
- 1
```

모든 종류에서 “안 입음”을 고른 경우를 제외합니다. 🚫

---

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ를 사용하므로 반드시 아래 네임스페이스가 필요합니다.

```csharp
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

---

## ⏱️ 시간 복잡도

전체 의상을 한 번 그룹화하고, 그룹별 개수를 계산합니다.

```text
O(n)
```

입니다.

---

## 📦 공간 복잡도

그룹 정보를 내부적으로 저장합니다.

```text
O(k)
```

최악의 경우:

```text
O(n)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | Dictionary로 종류별 개수 세기 | 원리가 잘 보인다 😊 | O(n) | O(n) |
| 풀이 2 | LINQ `GroupBy` + `Aggregate` | 코드가 짧다 🚀 | O(n) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 Dictionary 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 종류별 개수를 세는 과정이 잘 보입니다.
2. 왜 `(개수 + 1)`을 곱하는지 이해하기 쉽습니다.
3. 해시/딕셔너리 문제의 기본기를 익히기 좋습니다.
```

코딩 테스트 제출용으로 코드 길이를 우선한다면 **풀이 2번 LINQ 방식**이 가장 짧습니다.

다만 `GroupBy`, `Aggregate`가 익숙하지 않다면 풀이 1번도 충분히 짧고 안정적입니다. 🌱

---

# ✅ 최종 정리

이 문제의 핵심 공식은 다음입니다.

```text
정답 = (종류1 의상 수 + 1) × (종류2 의상 수 + 1) × ... - 1
```

여기서:

```text
+1 → 그 종류를 안 입는 경우
-1 → 아무것도 안 입는 경우 제거
```

입니다.

👕 이 문제는 “종류별 개수를 세고, 선택지를 곱하는 조합 문제”입니다.
