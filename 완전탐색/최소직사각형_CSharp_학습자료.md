# 💳 최소직사각형 — C# 학습자료

## 📌 1. 문제 핵심

여러 장의 명함이 있습니다.

각 명함은 다음 형태로 주어집니다.

```text
[w, h]
```

여기서:

```text
w → 가로 길이
h → 세로 길이
```

명함은 지갑에 넣을 때 **회전할 수 있습니다**. 🔄

즉, `[30, 70]` 크기의 명함은 다음처럼 볼 수도 있습니다.

```text
가로 30, 세로 70
```

또는 회전해서:

```text
가로 70, 세로 30
```

으로 넣을 수 있습니다.

목표는 모든 명함을 넣을 수 있는 **가장 작은 지갑의 넓이**를 구하는 것입니다. 🎯

# 🧠 핵심 아이디어

각 명함을 회전할 수 있으므로, 모든 명함을 같은 방향으로 정리하면 됩니다.

가장 쉬운 기준은 다음입니다.

```text
각 명함에서 긴 쪽을 가로로 둔다.
각 명함에서 짧은 쪽을 세로로 둔다.
```

예를 들어:

```text
[30, 70]
```

은 긴 쪽이 70, 짧은 쪽이 30입니다.

따라서 다음처럼 정리합니다.

```text
[70, 30]
```

이렇게 모든 명함을 정리한 뒤:

```text
가로 후보 중 최댓값 × 세로 후보 중 최댓값
```

을 계산하면 됩니다. ✅

# 🔍 예제 1 이해하기

```text
sizes = [[60, 50], [30, 70], [60, 30], [80, 40]]
```

각 명함에서 긴 쪽을 앞에 두면:

```text
[60, 50] → [60, 50]
[30, 70] → [70, 30]
[60, 30] → [60, 30]
[80, 40] → [80, 40]
```

이제 긴 쪽들의 최댓값은:

```text
max(60, 70, 60, 80) = 80
```

짧은 쪽들의 최댓값은:

```text
max(50, 30, 30, 40) = 50
```

따라서 지갑 크기는:

```text
80 × 50 = 4000
```

입니다. 🎉

# 🔍 예제 2 이해하기

```text
sizes = [[10, 7], [12, 3], [8, 15], [14, 7], [5, 15]]
```

긴 쪽을 앞으로 정리하면:

```text
[10, 7]  → [10, 7]
[12, 3]  → [12, 3]
[8, 15]  → [15, 8]
[14, 7]  → [14, 7]
[5, 15]  → [15, 5]
```

긴 쪽 최댓값:

```text
15
```

짧은 쪽 최댓값:

```text
8
```

넓이:

```text
15 × 8 = 120
```

정답은:

```text
120
```

✅

# ⚠️ 왜 긴 쪽을 가로로 모으면 될까?

명함은 회전할 수 있습니다.

그러면 각 명함마다 두 선택지가 있습니다.

```text
[w, h]
[h, w]
```

모든 명함을 넣는 지갑을 작게 만들려면 한쪽 방향에는 각 명함의 큰 값을 몰아넣고, 다른 방향에는 작은 값을 몰아넣는 것이 유리합니다.

예를 들어 `[30, 70]`을 그대로 두면 세로가 70까지 필요합니다.

하지만 회전하면:

```text
[70, 30]
```

이 되어 세로 부담이 줄어듭니다.

이렇게 모든 명함에 대해:

```text
큰 값 → 가로
작은 값 → 세로
```

로 통일하면, 지갑의 한쪽 길이만 커지고 다른 한쪽 길이는 최대한 작아집니다. 🧠

# 🧠 풀이 1. 이해하기 쉬운 방법 — 반복문으로 최댓값 갱신하기

## 💡 아이디어

1. `maxWidth`, `maxHeight`를 0으로 시작합니다.
2. 각 명함마다 긴 쪽과 짧은 쪽을 구합니다.
3. 긴 쪽의 최댓값을 `maxWidth`에 저장합니다.
4. 짧은 쪽의 최댓값을 `maxHeight`에 저장합니다.
5. 마지막에 `maxWidth * maxHeight`를 반환합니다.

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int solution(int[,] sizes)
    {
        int maxWidth = 0, maxHeight = 0;

        for (int i = 0; i < sizes.GetLength(0); i++)
        {
            int w = sizes[i, 0];
            int h = sizes[i, 1];

            int longer = Math.Max(w, h);
            int shorter = Math.Min(w, h);

            maxWidth = Math.Max(maxWidth, longer);
            maxHeight = Math.Max(maxHeight, shorter);
        }

        return maxWidth * maxHeight;
    }
}
```

## 🔎 코드 해설

### 1. 최댓값 변수 준비

```csharp
int maxWidth = 0;
int maxHeight = 0;
```

지갑의 가로와 세로 후보를 저장합니다.

### 2. 명함 하나씩 확인

```csharp
for (int i = 0; i < sizes.GetLength(0); i++)
```

`int[,]` 형태의 2차원 배열에서 행 개수는 다음처럼 구합니다.

```csharp
sizes.GetLength(0)
```

### 3. 가로와 세로 꺼내기

```csharp
int w = sizes[i, 0];
int h = sizes[i, 1];
```

각 명함은 `[w, h]` 형태입니다.

### 4. 긴 쪽과 짧은 쪽 구하기

```csharp
int longer = Math.Max(w, h);
int shorter = Math.Min(w, h);
```

명함을 회전할 수 있으므로 긴 쪽을 가로, 짧은 쪽을 세로로 생각합니다. 🔄

### 5. 지갑 크기 갱신

```csharp
maxWidth = Math.Max(maxWidth, longer);
maxHeight = Math.Max(maxHeight, shorter);
```

모든 명함을 담아야 하므로 가장 큰 긴 쪽과 가장 큰 짧은 쪽을 기억합니다.

## ⏱️ 시간 복잡도

명함을 한 번만 순회합니다.

```text
O(n)
```

`n`은 명함 개수입니다.

## 📦 공간 복잡도

추가 변수 몇 개만 사용합니다.

```text
O(1)
```

입니다.

# 🚀 풀이 2. 짧은 코드 버전 — LINQ 사용하기

## 💡 아이디어

LINQ를 사용해서 각 행 번호를 만들고, 각 명함의 긴 쪽 최댓값과 짧은 쪽 최댓값을 구합니다.

프로그래머스 C#에서 `sizes`는 `int[,]` 형태이므로 `Enumerable.Range()`로 행 번호를 만들어 사용합니다.

## 💻 C# 코드

```csharp
using System;
using System.Linq;

public class Solution
{
    public int solution(int[,] sizes)
    {
        var rows = Enumerable.Range(0, sizes.GetLength(0));

        int maxWidth = rows.Max(i => Math.Max(sizes[i, 0], sizes[i, 1]));
        int maxHeight = rows.Max(i => Math.Min(sizes[i, 0], sizes[i, 1]));

        return maxWidth * maxHeight;
    }
}
```

## ✨ 코드 의미

### 1. 행 번호 만들기

```csharp
var rows = Enumerable.Range(0, sizes.GetLength(0));
```

명함이 4개라면 다음 숫자를 만듭니다.

```text
0, 1, 2, 3
```

각 숫자는 `sizes`의 행 번호입니다.

### 2. 긴 쪽의 최댓값 구하기

```csharp
int maxWidth = rows.Max(i => Math.Max(sizes[i, 0], sizes[i, 1]));
```

각 명함에서 긴 쪽을 구한 뒤, 그중 최댓값을 찾습니다.

### 3. 짧은 쪽의 최댓값 구하기

```csharp
int maxHeight = rows.Max(i => Math.Min(sizes[i, 0], sizes[i, 1]));
```

각 명함에서 짧은 쪽을 구한 뒤, 그중 최댓값을 찾습니다.

## ⚠️ 주의할 점

위 코드에서 `rows`는 지연 실행되는 LINQ 시퀀스입니다.

`Max()`를 두 번 호출하므로 내부적으로 행 번호를 두 번 순회합니다.

그래도 `sizes.Length`가 최대 10,000이므로 충분히 빠릅니다. ✅

LINQ를 사용하므로 반드시 다음이 필요합니다.

```csharp
using System.Linq;
```

## ⏱️ 시간 복잡도

`Max()`를 두 번 수행합니다.

```text
O(n) + O(n) = O(n)
```

입니다.

## 📦 공간 복잡도

큰 배열을 새로 만들지 않습니다.

```text
O(1)
```

로 볼 수 있습니다.

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 반복문으로 최댓값 갱신 | 원리가 잘 보인다 😊 | O(n) | O(1) |
| 풀이 2 | LINQ `Max` 사용 | 코드가 짧다 🚀 | O(n) | O(1) |

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 반복문 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 명함을 회전시키는 아이디어가 잘 보입니다.
2. 긴 쪽과 짧은 쪽을 나눠서 생각하기 쉽습니다.
3. 디버깅하기 쉽습니다.
```

LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

다만 `rows`를 두 번 순회한다는 점은 알고 있으면 좋습니다. 🌱

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
각 명함은 회전할 수 있다.
```

그래서 각 명함마다:

```text
긴 쪽 → 지갑의 가로 후보
짧은 쪽 → 지갑의 세로 후보
```

로 맞춥니다.

그다음:

```text
가장 긴 가로 후보 × 가장 긴 세로 후보
```

를 계산하면 됩니다.

💳 “최소직사각형”은 회전 가능한 직사각형을 한 방향으로 정리하는 그리디 문제입니다.
