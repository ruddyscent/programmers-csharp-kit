# 🔎 소수 찾기 — C# 학습자료

## 📌 1. 문제 핵심

숫자가 적힌 종이 조각들이 있습니다.

예를 들어:

```text
numbers = "17"
```

이라면 종이 조각은 다음과 같습니다.

```text
1, 7
```

이 숫자들을 사용해서 만들 수 있는 숫자는 다음과 같습니다.

```text
1
7
17
71
```

이 중 소수는:

```text
7, 17, 71
```

따라서 정답은:

```text
3
```

입니다. ✅

---

## 🧠 핵심 아이디어

이 문제는 크게 두 단계로 나눌 수 있습니다.

```text
1. 주어진 숫자 조각으로 만들 수 있는 모든 숫자를 만든다.
2. 그 숫자들 중 소수의 개수를 센다.
```

여기서 중요한 점은 **중복 제거**입니다.

예를 들어:

```text
numbers = "011"
```

종이 조각으로 `011`을 만들 수 있지만, 숫자로 보면:

```text
011 = 11
```

입니다.

즉, `11`과 `011`은 같은 숫자로 취급해야 합니다.

그래서 만든 숫자는 `HashSet<int>`에 넣어 중복을 제거합니다. 🧺

---

# 🔍 예제 1 이해하기

```text
numbers = "17"
```

만들 수 있는 숫자:

```text
1
7
17
71
```

소수는:

```text
7
17
71
```

정답:

```text
3
```

🎉

---

# 🔍 예제 2 이해하기

```text
numbers = "011"
```

만들 수 있는 숫자 중 일부는 다음과 같습니다.

```text
0
1
10
11
101
110
```

주의할 점:

```text
011 = 11
```

입니다.

소수는:

```text
11
101
```

정답:

```text
2
```

✅

---

# 🧩 필요한 개념 1: 순열

순열은 순서를 고려해서 숫자를 나열하는 것입니다.

예를 들어 숫자 `1`, `7`이 있으면:

```text
17
71
```

둘은 서로 다른 숫자입니다.

이 문제에서는 1자리 숫자부터 전체 길이 숫자까지 모두 만들어야 합니다.

예를 들어 `"123"`이면:

```text
1자리: 1, 2, 3
2자리: 12, 13, 21, 23, 31, 32
3자리: 123, 132, 213, 231, 312, 321
```

처럼 만들 수 있습니다.

---

# 🧩 필요한 개념 2: 소수 판별

소수는 다음 조건을 만족하는 수입니다.

```text
1보다 크고,
1과 자기 자신으로만 나누어떨어지는 수
```

예:

```text
2, 3, 5, 7, 11, 13
```

은 소수입니다.

반대로:

```text
0, 1, 4, 6, 8, 9
```

는 소수가 아닙니다.

---

## 💡 소수 판별 최적화

어떤 수 `n`이 소수인지 확인할 때 `2`부터 `n - 1`까지 모두 나눠볼 필요는 없습니다.

`2`부터 `sqrt(n)`까지만 확인하면 됩니다.

예를 들어 `36`의 약수는:

```text
1 × 36
2 × 18
3 × 12
4 × 9
6 × 6
```

처럼 짝을 이룹니다.

따라서 제곱근까지만 확인해도 약수가 있는지 알 수 있습니다. ⚡

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — DFS로 모든 숫자 만들기

## 💡 아이디어

1. 숫자 조각을 하나씩 선택합니다.
2. 선택한 숫자를 현재 숫자 뒤에 붙입니다.
3. 만들어진 숫자를 `HashSet<int>`에 넣습니다.
4. 아직 사용하지 않은 숫자를 계속 붙여 봅니다.
5. 모든 숫자를 만든 뒤 소수 개수를 셉니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    private HashSet<int> numbersSet = new HashSet<int>();

    public int solution(string numbers)
    {
        bool[] used = new bool[numbers.Length];

        MakeNumbers(numbers, 0, used);

        return numbersSet.Count(IsPrime);
    }

    private void MakeNumbers(string numbers, int current, bool[] used)
    {
        numbersSet.Add(current);

        for (int i = 0; i < numbers.Length; i++)
        {
            if (used[i])
                continue;

            used[i] = true;
            MakeNumbers(numbers, current * 10 + numbers[i] - '0', used);
            used[i] = false;
        }
    }

    private bool IsPrime(int number)
    {
        if (number < 2)
            return false;

        for (int i = 2; i * i <= number; i++)
            if (number % i == 0)
                return false;

        return true;
    }
}
```

---

## 🔎 코드 해설

### 1. 중복 제거용 HashSet

```csharp
private HashSet<int> numbersSet = new HashSet<int>();
```

만들어진 숫자를 저장합니다.

`HashSet`은 같은 숫자를 여러 번 넣어도 하나만 저장합니다.

예를 들어:

```text
011
11
```

은 둘 다 숫자 `11`이므로 하나만 저장됩니다. ✅

---

### 2. 사용 여부 배열

```csharp
bool[] used = new bool[numbers.Length];
```

각 종이 조각을 이미 사용했는지 표시합니다.

예를 들어 `"011"`에서 두 번째 `1`과 세 번째 `1`은 값은 같지만 서로 다른 종이 조각입니다.

그래서 위치 기준으로 사용 여부를 관리해야 합니다. 📍

---

### 3. 현재까지 만든 숫자 저장

```csharp
numbersSet.Add(current);
```

현재까지 만든 숫자를 저장합니다.

예를 들어:

```text
current = 1
```

입니다.

처음 호출의 `0`도 저장되지만, `0`은 소수가 아니므로 정답에는 포함되지 않습니다.

---

### 4. DFS로 다음 숫자 선택

```csharp
for (int i = 0; i < numbers.Length; i++)
```

아직 사용하지 않은 종이 조각을 하나 골라 현재 숫자 뒤에 붙입니다.

```csharp
MakeNumbers(numbers, current * 10 + numbers[i] - '0', used);
```

---

### 5. 백트래킹

```csharp
used[i] = true;
MakeNumbers(numbers, current * 10 + numbers[i] - '0', used);
used[i] = false;
```

숫자 조각을 사용한 뒤, 재귀 호출이 끝나면 다시 사용하지 않은 상태로 되돌립니다.

이렇게 해야 다른 순서의 숫자도 만들 수 있습니다. 🔁

---

### 6. 소수 판별

```csharp
for (int i = 2; i * i <= number; i++)
```

`2`부터 제곱근까지만 확인합니다.

중간에 나누어떨어지면 소수가 아닙니다.

---

## ⏱️ 시간 복잡도

`numbers`의 길이를 `n`이라고 하면, 만들 수 있는 순열의 수는 대략 다음과 같습니다.

```text
O(n!)
```

정확히는 1자리부터 n자리까지의 순열을 모두 만듭니다.

하지만 제한사항에서:

```text
n <= 7
```

이므로 충분히 가능합니다. ✅

소수 판별은 각 숫자에 대해 `O(sqrt(M))`입니다.

`M`은 만들 수 있는 가장 큰 숫자입니다.

---

## 📦 공간 복잡도

만들어진 숫자를 `HashSet`에 저장합니다.

```text
O(n!)
```

재귀 호출 깊이는 최대 `n`입니다.

```text
O(n)
```

---

# 🚀 풀이 2. 짧은 코드 버전 — 숫자를 바로 만들기

## 💡 아이디어

풀이 1과 같은 DFS 방식입니다.

다만 문자열을 이어 붙이지 않고, 현재 숫자에 다음 숫자를 바로 붙입니다.

```text
next = current * 10 + digit
```

마지막 소수 개수는 LINQ `Count()`로 계산합니다.

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int solution(string numbers)
    {
        var set = new HashSet<int>();
        var used = new bool[numbers.Length];

        void Dfs(int current)
        {
            set.Add(current);

            for (int i = 0; i < numbers.Length; i++)
            {
                if (used[i])
                    continue;

                used[i] = true;
                Dfs(current * 10 + numbers[i] - '0');
                used[i] = false;
            }
        }

        bool IsPrime(int number)
        {
            if (number < 2)
                return false;

            for (int i = 2; i * i <= number; i++)
                if (number % i == 0)
                    return false;

            return true;
        }

        Dfs(0);

        return set.Count(IsPrime);
    }
}
```

---

## ✨ 코드 의미

### 1. 로컬 함수 `Dfs`

```csharp
void Dfs(int current)
```

`solution` 함수 안에 DFS 함수를 정의했습니다.

이렇게 하면 `set`, `used`, `numbers`를 별도 필드로 만들지 않고 바로 사용할 수 있습니다. 😊

문자열 대신 숫자를 직접 들고 다녀서 `int.Parse()`도 필요 없습니다.

---

### 2. 로컬 함수 `IsPrime`

```csharp
bool IsPrime(int number)
```

소수 판별 함수도 `solution` 안에 넣었습니다.

프로그래머스 C# 환경에서 실행할 수 있습니다.

---

### 3. LINQ로 소수 개수 세기

```csharp
return set.Count(IsPrime);
```

`set`에 들어 있는 숫자 중 `IsPrime`이 `true`인 숫자만 셉니다.

즉:

```text
소수인 숫자의 개수
```

를 바로 반환합니다. 🚀

---

## ⚠️ 프로그래머스에서 실행할 때 주의

이 풀이에서는 LINQ의 `Count()`를 사용하므로 다음이 필요합니다.

```csharp
using System.Linq;
```

또한 로컬 함수는 최신 C# 문법입니다.

프로그래머스 C# 환경에서는 일반적으로 사용할 수 있습니다. ✅

---

## ⏱️ 시간 복잡도

DFS로 만들 수 있는 숫자를 모두 생성합니다.

```text
O(n!)
```

소수 판별까지 포함하면:

```text
O(n! × sqrt(M))
```

입니다.

`n <= 7`이므로 충분히 통과 가능합니다.

---

## 📦 공간 복잡도

HashSet에 만든 숫자를 저장합니다.

```text
O(n!)
```

재귀 깊이는 최대 7입니다.

```text
O(n)
```

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | DFS + HashSet + 소수 판별 | 흐름이 명확하다 😊 | O(n! × sqrt(M)) | O(n!) |
| 풀이 2 | 로컬 함수 + LINQ Count | 코드가 짧다 🚀 | O(n! × sqrt(M)) | O(n!) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 순열 생성 과정이 잘 보입니다.
2. used 배열과 백트래킹을 이해하기 좋습니다.
3. 중복 제거가 왜 필요한지 명확합니다.
```

코드를 짧게 쓰고 싶다면 **풀이 2번**도 좋습니다.

다만 로컬 함수와 LINQ에 익숙하지 않다면 처음에는 조금 낯설 수 있습니다. 🌱

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
종이 조각으로 만들 수 있는 모든 숫자를 만든다.
중복을 제거한다.
소수인지 확인한다.
```

풀이 흐름은 다음과 같습니다.

```text
1. DFS로 가능한 모든 숫자를 만든다.
2. HashSet에 넣어 중복을 제거한다.
3. 각 숫자가 소수인지 판별한다.
4. 소수의 개수를 반환한다.
```

🔎 “소수 찾기”는 순열, 백트래킹, 소수 판별을 함께 연습하기 좋은 문제입니다.
