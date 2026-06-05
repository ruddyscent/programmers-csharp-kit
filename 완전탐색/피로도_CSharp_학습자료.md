# ⚔️ 피로도 — C# 학습자료

## 📌 1. 문제 핵심

현재 피로도 `k`가 있고, 여러 던전이 있습니다.

각 던전은 다음 두 값을 가집니다.

```text
[최소 필요 피로도, 소모 피로도]
```

예를 들어:

```text
[80, 20]
```

이라는 던전은 다음 뜻입니다.

```text
현재 피로도가 80 이상이어야 입장 가능
탐험 후 피로도 20 소모
```

목표는 다음입니다.

```text
하루에 탐험할 수 있는 던전 수의 최댓값 구하기
```

🎯

---

# 🔍 예제 이해하기

```text
k = 80
dungeons = [[80,20], [50,40], [30,10]]
```

## 순서 1: 1번 → 2번 → 3번

```text
현재 피로도 80
```

1번 던전 `[80, 20]` 탐험 가능

```text
80 - 20 = 60
```

2번 던전 `[50, 40]` 탐험 가능

```text
60 - 40 = 20
```

3번 던전 `[30, 10]`은 최소 필요 피로도가 30이므로 탐험 불가능

```text
총 2개 탐험
```

---

## 순서 2: 1번 → 3번 → 2번

```text
현재 피로도 80
```

1번 던전 `[80, 20]` 탐험

```text
80 - 20 = 60
```

3번 던전 `[30, 10]` 탐험

```text
60 - 10 = 50
```

2번 던전 `[50, 40]` 탐험

```text
50 - 40 = 10
```

총 3개 탐험할 수 있습니다. 🎉

따라서 정답은:

```text
3
```

입니다.

---

# 🧠 핵심 아이디어

이 문제는 던전의 순서가 중요합니다.

같은 던전들이 있어도 어떤 순서로 가느냐에 따라 결과가 달라집니다.

따라서 가능한 모든 순서를 확인해야 합니다.

다행히 제한사항이 작습니다.

```text
던전 개수 ≤ 8
```

가능한 순서의 수는 최대:

```text
8! = 40320
```

입니다.

충분히 탐색할 수 있습니다. ✅

---

# 🧩 필요한 개념: DFS와 백트래킹

DFS는 가능한 선택을 하나씩 깊게 들어가며 탐색하는 방법입니다.

이 문제에서는 다음처럼 생각할 수 있습니다.

```text
현재 피로도에서 갈 수 있는 던전을 하나 고른다.
그 던전을 탐험한다.
남은 피로도로 다시 갈 수 있는 던전을 고른다.
더 갈 수 없으면 최대값을 갱신한다.
```

백트래킹은 선택을 한 뒤 다시 되돌리는 방식입니다.

```text
던전 A 방문 표시
DFS 진행
던전 A 방문 표시 취소
```

이렇게 해야 다른 순서도 시도할 수 있습니다. 🔁

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — DFS + visited 배열

## 💡 아이디어

1. 방문 여부를 저장하는 `visited` 배열을 만듭니다.
2. 현재 피로도로 갈 수 있는 던전을 찾습니다.
3. 갈 수 있다면 방문 처리하고 DFS를 진행합니다.
4. DFS가 끝나면 방문 처리를 되돌립니다.
5. 탐험한 던전 수의 최댓값을 계속 갱신합니다.

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    private int answer = 0;
    private bool[] visited;

    public int solution(int k, int[,] dungeons)
    {
        visited = new bool[dungeons.GetLength(0)];

        Dfs(k, 0, dungeons);

        return answer;
    }

    private void Dfs(int currentFatigue, int count, int[,] dungeons)
    {
        answer = Math.Max(answer, count);

        for (int i = 0; i < dungeons.GetLength(0); i++)
        {
            int required = dungeons[i, 0];
            int cost = dungeons[i, 1];

            if (visited[i])
            {
                continue;
            }

            if (currentFatigue < required)
            {
                continue;
            }

            visited[i] = true;
            Dfs(currentFatigue - cost, count + 1, dungeons);
            visited[i] = false;
        }
    }
}
```

---

## 🔎 코드 해설

### 1. 정답과 방문 배열

```csharp
private int answer = 0;
private bool[] visited;
```

`answer`는 지금까지 탐험할 수 있었던 최대 던전 수입니다.

`visited`는 각 던전을 이미 탐험했는지 표시합니다.

---

### 2. 방문 배열 초기화

```csharp
visited = new bool[dungeons.GetLength(0)];
```

프로그래머스 C#에서 `dungeons`는 보통 `int[,]` 형태입니다.

`dungeons.GetLength(0)`은 던전 개수입니다.

---

### 3. DFS 시작

```csharp
Dfs(k, 0, dungeons);
```

처음 피로도는 `k`이고, 아직 탐험한 던전 수는 0입니다.

---

### 4. 최대값 갱신

```csharp
answer = Math.Max(answer, count);
```

현재까지 탐험한 던전 수가 최대라면 갱신합니다. 🏆

---

### 5. 모든 던전 확인

```csharp
for (int i = 0; i < dungeons.GetLength(0); i++)
```

현재 상태에서 갈 수 있는 모든 던전을 확인합니다.

---

### 6. 이미 방문한 던전은 건너뛰기

```csharp
if (visited[i])
{
    continue;
}
```

던전은 하루에 한 번씩만 탐험할 수 있습니다.

---

### 7. 피로도가 부족하면 건너뛰기

```csharp
if (currentFatigue < required)
{
    continue;
}
```

현재 피로도가 최소 필요 피로도보다 작으면 탐험할 수 없습니다.

---

### 8. 방문 처리 후 DFS

```csharp
visited[i] = true;
Dfs(currentFatigue - cost, count + 1, dungeons);
visited[i] = false;
```

던전을 탐험한 뒤 다음 상태로 이동합니다.

DFS가 끝나면 다시 방문 표시를 지웁니다.

이 되돌리기 과정이 백트래킹입니다. 🔁

---

## ⏱️ 시간 복잡도

던전 개수를 `n`이라고 하면 가능한 순서를 탐색합니다.

최악의 경우:

```text
O(n!)
```

입니다.

하지만 `n <= 8`이므로 충분히 빠릅니다.

---

## 📦 공간 복잡도

방문 배열과 재귀 호출 스택을 사용합니다.

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 코드가 짧은 방법 — 로컬 함수로 간결하게 작성하기

## 💡 아이디어

풀이 1과 같은 DFS입니다.

다만 `answer`, `visited`, `Dfs`를 모두 `solution` 함수 안에 넣어 더 짧게 작성합니다.

프로그래머스 C#에서 실행 가능합니다. ✅

---

## 💻 C# 코드

```csharp
using System;

public class Solution
{
    public int solution(int k, int[,] dungeons)
    {
        int n = dungeons.GetLength(0);
        bool[] visited = new bool[n];
        int answer = 0;

        void Dfs(int fatigue, int count)
        {
            answer = Math.Max(answer, count);

            for (int i = 0; i < n; i++)
            {
                if (visited[i] || fatigue < dungeons[i, 0])
                {
                    continue;
                }

                visited[i] = true;
                Dfs(fatigue - dungeons[i, 1], count + 1);
                visited[i] = false;
            }
        }

        Dfs(k, 0);

        return answer;
    }
}
```

---

## ✨ 코드 의미

### 1. 던전 개수 저장

```csharp
int n = dungeons.GetLength(0);
```

던전 개수를 자주 쓰므로 변수에 저장합니다.

---

### 2. 로컬 함수 사용

```csharp
void Dfs(int fatigue, int count)
```

`solution` 안에 DFS 함수를 정의했습니다.

이렇게 하면 `visited`, `dungeons`, `answer`를 자연스럽게 사용할 수 있습니다.

---

### 3. 조건을 한 줄로 합치기

```csharp
if (visited[i] || fatigue < dungeons[i, 0])
{
    continue;
}
```

이미 방문했거나 피로도가 부족하면 건너뜁니다.

---

## ⚠️ 참고

이 풀이에는 LINQ를 쓰지 않았습니다.

이 문제는 상태를 바꿨다가 되돌리는 백트래킹이 핵심이라, LINQ를 억지로 쓰면 오히려 코드가 복잡해집니다.

프로그래머스 제출용으로는 위 코드가 가장 깔끔합니다. 😊

---

## ⏱️ 시간 복잡도

가능한 순서를 탐색하므로:

```text
O(n!)
```

입니다.

`n <= 8`이라 충분히 가능합니다. ✅

---

## 📦 공간 복잡도

방문 배열과 재귀 호출 스택을 사용합니다.

```text
O(n)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | 필드 + DFS + visited | 흐름이 명확하다 😊 | O(n!) | O(n) |
| 풀이 2 | 로컬 함수 DFS | 코드가 짧다 🚀 | O(n!) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번**을 추천합니다.

이유는 다음과 같습니다.

```text
1. DFS 함수의 역할이 명확합니다.
2. visited 배열의 의미를 이해하기 쉽습니다.
3. 백트래킹 과정을 따라가기 좋습니다.
```

코드를 짧게 쓰고 싶다면 **풀이 2번**도 좋습니다.

단, 로컬 함수 문법이 익숙하지 않다면 풀이 1번이 더 편합니다. 🌱

---

# ✅ 최종 정리

이 문제의 핵심은 다음입니다.

```text
던전 순서에 따라 결과가 달라진다.
```

던전 수가 최대 8개라서 가능한 순서를 모두 탐색할 수 있습니다.

풀이 흐름은 다음과 같습니다.

```text
1. 현재 피로도에서 갈 수 있는 던전을 찾는다.
2. 갈 수 있으면 방문 처리한다.
3. 피로도를 줄이고 다음 DFS로 간다.
4. 돌아오면 방문 처리를 취소한다.
5. 최대 탐험 수를 갱신한다.
```

⚔️ “피로도”는 DFS와 백트래킹을 연습하기 좋은 대표 문제입니다.
