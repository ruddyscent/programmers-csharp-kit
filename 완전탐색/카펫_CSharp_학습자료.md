# 🟫 카펫 — C# 학습자료

## 📌 1. 문제 핵심

카펫은 다음 구조를 가집니다.

```text
갈색(Brown) : 테두리 1줄
노란색(Yellow) : 내부 영역
```

예를 들어:

```text
🟫🟫🟫🟫
🟫🟨🟨🟫
🟫🟫🟫🟫
```

이라면:

```text
brown = 10
yellow = 2
```

입니다.

우리는:

```text
brown 개수
yellow 개수
```

만 알고 있습니다.

카펫의:

```text
[가로, 세로]
```

를 구해야 합니다. 🎯

---

# 🧠 핵심 아이디어

전체 카펫 크기를:

```text
width × height
```

라고 합시다.

그러면 노란색 영역은 테두리를 제외한 내부입니다.

```text
(width - 2) × (height - 2)
```

입니다.

따라서:

```text
yellow = (width - 2) × (height - 2)
```

가 반드시 성립합니다.

또한 전체 칸 수는:

```text
brown + yellow
```

입니다.

즉:

```text
width × height = brown + yellow
```

입니다.

🎯 결국 다음 두 조건을 만족하는 `(width, height)`를 찾으면 됩니다.

```text
width × height = brown + yellow
(width - 2) × (height - 2) = yellow
```

---

# 🔍 예제 1 이해하기

```text
brown = 10
yellow = 2
```

전체 칸 수:

```text
10 + 2 = 12
```

즉:

```text
width × height = 12
```

가능한 조합:

```text
12 × 1
6 × 2
4 × 3
```

입니다.

하나씩 확인해 봅니다.

```text
4 × 3
```

이면 내부는:

```text
(4 - 2) × (3 - 2)
= 2 × 1
= 2
```

입니다.

노란색 개수와 같습니다. ✅

따라서 정답:

```text
[4, 3]
```

---

# 🔍 예제 2 이해하기

```text
brown = 8
yellow = 1
```

전체 칸 수:

```text
9
```

가능한 조합:

```text
9 × 1
3 × 3
```

확인:

```text
(3 - 2) × (3 - 2)
= 1
```

성립합니다.

정답:

```text
[3, 3]
```

🎉

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — 약수 탐색

## 💡 아이디어

전체 칸 수를 구합니다.

```text
total = brown + yellow
```

그리고 `total`의 약수를 찾아서:

```text
width × height = total
```

을 만족하는 후보를 확인합니다.

그중:

```text
(width - 2) × (height - 2) == yellow
```

를 만족하면 정답입니다.

---

## 💻 C# 코드

```csharp
public class Solution
{
    public int[] solution(int brown, int yellow)
    {
        int total = brown + yellow;

        for (int height = 3; height * height <= total; height++)
        {
            if (total % height != 0)
                continue;

            int width = total / height;

            if ((width - 2) * (height - 2) == yellow)
                return new int[] { width, height };
        }

        return new int[0];
    }
}
```

---

## 🔎 코드 해설

### 1. 전체 칸 수 계산

```csharp
int total = brown + yellow;
```

전체 카펫 크기입니다.

예:

```text
10 + 2 = 12
```

---

### 2. 높이 후보 탐색

```csharp
for (int height = 3; height * height <= total; height++)
```

세로 길이는 최소 3입니다.

왜냐하면:

```text
갈색 테두리
노란색 내부
갈색 테두리
```

가 있어야 하기 때문입니다.

---

### 3. 약수인지 확인

```csharp
if (total % height != 0)
{
    continue;
}
```

높이가 전체 칸 수의 약수여야 합니다.

---

### 4. 가로 계산

```csharp
int width = total / height;
```

입니다.

---

### 5. 내부 노란색 확인

```csharp
if ((width - 2) * (height - 2) == yellow)
```

문제의 핵심 공식입니다.

조건이 맞으면 정답입니다. 🎯

---

## ⏱️ 시간 복잡도

약수만 탐색합니다.

반복 횟수는:

```text
O(√(brown + yellow))
```

입니다.

최대:

```text
√2,005,000 ≈ 1416
```

정도라 매우 빠릅니다. ⚡

---

## 📦 공간 복잡도

추가 배열을 거의 사용하지 않습니다.

```text
O(1)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — 약수 조건 바로 확인하기

## 💡 아이디어

풀이 1과 같은 약수 탐색입니다.

다만 `Math.Sqrt()`와 LINQ를 쓰지 않고 `height * height <= total` 조건으로 바로 확인합니다.

---

## 💻 C# 코드

```csharp
public class Solution
{
    public int[] solution(int brown, int yellow)
    {
        int total = brown + yellow;

        for (int h = 3; h * h <= total; h++)
            if (total % h == 0 && (total / h - 2) * (h - 2) == yellow)
                return new int[] { total / h, h };

        return new int[0];
    }
}
```

---

## ✨ 코드 의미

### 1. 가능한 높이 확인

```csharp
for (int h = 3; h * h <= total; h++)
```

3부터 √total까지 확인합니다.

---

### 2. 조건 만족하는 높이 찾기

조건:

```csharp
total % h == 0 && (total / h - 2) * (h - 2) == yellow
```

약수이면서 내부 노란색 개수가 맞아야 합니다.

---

### 3. 가로 계산

```csharp
total / h
```

입니다.

---

## ⏱️ 시간 복잡도

약수 탐색이므로:

```text
O(√(brown + yellow))
```

입니다.

---

## 📦 공간 복잡도

추가 자료구조가 거의 없습니다.

```text
O(1)
```

입니다.

---

# ✨ 더 쉬운 공식 풀이

사실 카펫에서는 다음 공식을 사용할 수도 있습니다.

노란색 내부 크기를:

```text
a × b = yellow
```

라고 하면:

```text
width = a + 2
height = b + 2
```

입니다.

그때 갈색 개수가:

```text
2 × width + 2 × height - 4
```

와 같으면 정답입니다.

하지만 일반적으로는:

```text
전체 칸 수
+
약수 탐색
```

방식이 더 직관적입니다. 😊

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 약수 탐색 | 이해하기 쉽다 😊 | O(√N) | O(1) |
| 풀이 2 | LINQ + First | 코드가 짧다 🚀 | O(√N) | O(1) |

여기서:

```text
N = brown + yellow
```

입니다.

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 전체 칸 수와 내부 노란색 관계를 이해하기 쉽습니다.
2. 약수 탐색을 연습할 수 있습니다.
3. 디버깅하기 쉽습니다.
```

LINQ에 익숙하다면 **풀이 2번**도 좋습니다.

---

# ✅ 최종 정리

이 문제의 핵심 공식은 다음 두 개입니다.

```text
width × height = brown + yellow
```

그리고:

```text
(width - 2) × (height - 2) = yellow
```

따라서:

```text
1. 전체 칸 수를 구한다.
2. 약수를 탐색한다.
3. 내부 노란색 개수가 맞는지 확인한다.
```

💳 “카펫”은 약수 탐색과 직사각형 크기 계산을 연습하기 좋은 대표 문제입니다.
