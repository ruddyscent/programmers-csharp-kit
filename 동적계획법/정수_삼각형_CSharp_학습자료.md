# 🔺 정수 삼각형 — C# 학습자료

## 📌 1. 문제 핵심

정수로 이루어진 삼각형이 있습니다.

예를 들어:

```text
        7
      3   8
    8   1   0
  2   7   4   4
4   5   2   6   5
```

꼭대기에서 시작해서 아래로 내려갑니다.

이때 이동 규칙은 다음과 같습니다.

```text
아래층의 왼쪽 대각선 또는 오른쪽 대각선으로만 이동 가능
```

목표는 다음입니다.

```text
꼭대기에서 바닥까지 내려가며 거친 숫자 합의 최댓값 구하기
```

🎯

---

# 🔍 예제 이해하기

입력:

```text
triangle =
[
  [7],
  [3, 8],
  [8, 1, 0],
  [2, 7, 4, 4],
  [4, 5, 2, 6, 5]
]
```

가능한 경로 중 하나는 다음입니다.

```text
7 → 3 → 8 → 7 → 5
```

합은:

```text
7 + 3 + 8 + 7 + 5 = 30
```

이 경로가 만들 수 있는 가장 큰 합입니다.

따라서 정답은:

```text
30
```

✅

---

# 🧠 핵심 아이디어

각 칸에 도착했을 때의 최대 합을 저장하면 됩니다.

예를 들어 어떤 칸에 도착하는 방법은 최대 두 가지입니다.

```text
1. 바로 위 왼쪽 칸에서 내려오기
2. 바로 위 오른쪽 칸에서 내려오기
```

삼각형 배열에서 `triangle[row][col]` 위치를 생각하면, 이 칸에 올 수 있는 이전 위치는 다음입니다.

```text
triangle[row - 1][col - 1]
triangle[row - 1][col]
```

단, 양쪽 끝 칸은 이전 위치가 하나뿐입니다.

---

# 🧩 DP란?

DP는 Dynamic Programming, 즉 동적 계획법입니다.

쉽게 말하면:

```text
작은 문제의 정답을 저장해 두고,
그 값을 이용해 더 큰 문제를 푸는 방법
```

입니다.

이 문제에서는:

```text
각 칸까지 오는 최대 합
```

을 저장합니다.

---

# 🔍 DP 계산 과정

처음 삼각형:

```text
        7
      3   8
    8   1   0
  2   7   4   4
4   5   2   6   5
```

## 1층

```text
7
```

꼭대기는 그대로 7입니다.

---

## 2층

```text
3은 7에서만 올 수 있음 → 7 + 3 = 10
8은 7에서만 올 수 있음 → 7 + 8 = 15
```

```text
      7
   10   15
```

---

## 3층

```text
8은 10에서만 올 수 있음 → 10 + 8 = 18
1은 10 또는 15에서 올 수 있음 → max(10, 15) + 1 = 16
0은 15에서만 올 수 있음 → 15 + 0 = 15
```

```text
        7
     10   15
   18   16   15
```

이렇게 마지막 줄까지 계산한 뒤, 마지막 줄의 최댓값을 구하면 됩니다. 🎉

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — DP 배열 따로 만들기

## 💡 아이디어

원본 `triangle`은 그대로 두고, 같은 모양의 `dp` 배열을 만듭니다.

`dp[row, col]`은 다음 뜻입니다.

```text
row, col 위치까지 도착했을 때 가능한 최대 합
```

계산 규칙은 다음입니다.

```text
왼쪽 끝 칸: 위에서만 내려옴
오른쪽 끝 칸: 위 왼쪽에서만 내려옴
가운데 칸: 위의 두 칸 중 큰 값에서 내려옴
```

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int solution(int[,] triangle)
    {
        int height = triangle.GetLength(0);
        int[,] dp = new int[height, height];

        dp[0, 0] = triangle[0, 0];

        for (int row = 1; row < height; row++)
        {
            for (int col = 0; col <= row; col++)
            {
                if (col == 0)
                {
                    dp[row, col] = dp[row - 1, col] + triangle[row, col];
                }
                else if (col == row)
                {
                    dp[row, col] = dp[row - 1, col - 1] + triangle[row, col];
                }
                else
                {
                    dp[row, col] = Math.Max(dp[row - 1, col - 1], dp[row - 1, col]) + triangle[row, col];
                }
            }
        }

        int answer = 0;

        for (int col = 0; col < height; col++)
        {
            answer = Math.Max(answer, dp[height - 1, col]);
        }

        return answer;
    }
}
```

---

## 🔎 코드 해설

### 1. 삼각형 높이 구하기

```csharp
int height = triangle.GetLength(0);
```

프로그래머스 C#에서 2차원 배열은 `int[,]` 형태입니다.

`GetLength(0)`은 행 개수, 즉 삼각형의 높이입니다.

---

### 2. DP 배열 만들기

```csharp
int[,] dp = new int[height, height];
```

삼각형은 행마다 길이가 다르지만, `int[,]`에서는 정사각형처럼 공간을 만들어도 됩니다.

실제로 사용하는 위치는 다음 범위입니다.

```text
row번째 줄에서는 col = 0부터 row까지만 사용
```

---

### 3. 꼭대기 값 초기화

```csharp
dp[0, 0] = triangle[0, 0];
```

시작점은 꼭대기 하나뿐입니다.

---

### 4. 왼쪽 끝 칸 처리

```csharp
if (col == 0)
{
    dp[row, col] = dp[row - 1, col] + triangle[row, col];
}
```

왼쪽 끝 칸은 바로 위에서만 내려올 수 있습니다.

---

### 5. 오른쪽 끝 칸 처리

```csharp
else if (col == row)
{
    dp[row, col] = dp[row - 1, col - 1] + triangle[row, col];
}
```

오른쪽 끝 칸은 위 왼쪽에서만 내려올 수 있습니다.

---

### 6. 가운데 칸 처리

```csharp
dp[row, col] = Math.Max(dp[row - 1, col - 1], dp[row - 1, col]) + triangle[row, col];
```

가운데 칸은 위쪽 두 칸 중 더 큰 합을 선택합니다.

```text
더 큰 이전 합 + 현재 칸 값
```

입니다. 🔺

---

### 7. 마지막 줄에서 최댓값 찾기

```csharp
for (int col = 0; col < height; col++)
{
    answer = Math.Max(answer, dp[height - 1, col]);
}
```

바닥까지 내려가는 경로는 마지막 줄의 어느 칸에서든 끝날 수 있습니다.

따라서 마지막 줄의 최댓값이 정답입니다.

---

## ⏱️ 시간 복잡도

삼각형의 모든 칸을 한 번씩 확인합니다.

높이를 `n`이라고 하면 칸 개수는 대략:

```text
1 + 2 + 3 + ... + n = n(n + 1) / 2
```

입니다.

따라서 시간 복잡도는:

```text
O(n²)
```

입니다.

---

## 📦 공간 복잡도

`dp` 배열을 추가로 사용합니다.

```text
O(n²)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — 원본 배열에 바로 누적하기

## 💡 아이디어

`triangle` 배열 자체를 DP 배열처럼 사용할 수 있습니다.

즉, 각 칸에:

```text
그 칸까지 도착하는 최대 합
```

을 바로 저장합니다.

원본 값을 보존할 필요가 없다면 이 방식이 더 짧고 메모리를 덜 씁니다.

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int solution(int[,] triangle)
    {
        int height = triangle.GetLength(0);

        for (int row = 1; row < height; row++)
        {
            for (int col = 0; col <= row; col++)
            {
                if (col == 0)
                {
                    triangle[row, col] += triangle[row - 1, col];
                }
                else if (col == row)
                {
                    triangle[row, col] += triangle[row - 1, col - 1];
                }
                else
                {
                    triangle[row, col] += Math.Max(triangle[row - 1, col - 1], triangle[row - 1, col]);
                }
            }
        }

        int answer = 0;

        for (int col = 0; col < height; col++)
        {
            answer = Math.Max(answer, triangle[height - 1, col]);
        }

        return answer;
    }
}
```

---

## ✨ 코드 의미

### 1. 원본 triangle을 DP처럼 사용

```csharp
triangle[row, col] += ...
```

현재 칸 값에 이전 최대 합을 더해서 저장합니다.

예를 들어 현재 칸이 `1`이고 위에서 올 수 있는 최대 합이 `15`라면:

```text
1 + 15 = 16
```

을 현재 칸에 저장합니다.

---

### 2. 별도 dp 배열이 필요 없음

풀이 1에서는:

```csharp
int[,] dp = new int[height, height];
```

를 사용했습니다.

풀이 2에서는 원본 배열에 바로 누적하므로 추가 DP 배열이 필요 없습니다. ⚡

---

## ⚠️ 주의할 점

이 방식은 `triangle` 값을 직접 바꿉니다.

프로그래머스에서는 입력 배열을 다시 사용할 일이 없기 때문에 괜찮습니다.

하지만 원본 데이터가 필요하다면 풀이 1처럼 별도 `dp` 배열을 사용하는 것이 안전합니다. 🌱

---

## ⏱️ 시간 복잡도

모든 칸을 한 번씩 확인합니다.

```text
O(n²)
```

입니다.

---

## 📦 공간 복잡도

추가 배열을 만들지 않습니다.

```text
O(1)
```

입니다.

단, 입력 배열 자체를 수정합니다.

---

# ✨ LINQ를 살짝 쓰는 방법

마지막 줄의 최댓값을 구할 때 LINQ를 사용할 수도 있습니다.

```csharp
using System;
using System.Linq;

public class Solution
{
    public int solution(int[,] triangle)
    {
        int height = triangle.GetLength(0);

        for (int row = 1; row < height; row++)
        {
            for (int col = 0; col <= row; col++)
            {
                if (col == 0)
                {
                    triangle[row, col] += triangle[row - 1, col];
                }
                else if (col == row)
                {
                    triangle[row, col] += triangle[row - 1, col - 1];
                }
                else
                {
                    triangle[row, col] += Math.Max(triangle[row - 1, col - 1], triangle[row - 1, col]);
                }
            }
        }

        return Enumerable.Range(0, height)
            .Max(col => triangle[height - 1, col]);
    }
}
```

다만 이 문제는 DP 갱신이 핵심이라 LINQ를 많이 쓰기보다는 반복문을 쓰는 편이 이해하기 쉽습니다. 😊

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 별도 DP 배열 | 원본을 보존하고 이해하기 쉽다 😊 | O(n²) | O(n²) |
| 풀이 2 | 원본 배열에 누적 | 코드가 짧고 메모리를 아낀다 🚀 | O(n²) | O(1) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 DP 배열 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. dp[row, col]의 의미가 명확합니다.
2. 원본 삼각형과 계산 결과를 구분할 수 있습니다.
3. DP 개념을 이해하기 쉽습니다.
```

프로그래머스 제출용으로는 **풀이 2번 원본 배열 누적 방식**이 좋습니다.

코드가 짧고 공간을 덜 사용합니다. ⚡

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
각 칸까지 도착하는 최대 합을 저장한다.
```

점화식은 다음과 같습니다.

```text
왼쪽 끝: 위에서만 내려온다.
오른쪽 끝: 위 왼쪽에서만 내려온다.
가운데: 위쪽 두 칸 중 큰 값에서 내려온다.
```

마지막에는:

```text
마지막 줄의 최댓값
```

을 반환하면 됩니다.

🔺 “정수 삼각형”은 동적 계획법을 처음 익히기 좋은 대표 문제입니다.
