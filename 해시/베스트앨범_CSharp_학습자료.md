# 🎧 베스트앨범 — C# 학습자료

## 📌 1. 문제 핵심

스트리밍 사이트에서 장르별로 가장 인기 있는 노래를 모아 **베스트앨범**을 만들려고 합니다.

노래마다 다음 정보가 있습니다.

```text
genres[i] → i번 노래의 장르
plays[i]  → i번 노래의 재생 횟수
```

예를 들어:

```text
genres = ["classic", "pop", "classic", "classic", "pop"]
plays  = [500, 600, 150, 800, 2500]
```

이면:

| 고유 번호 | 장르 | 재생 횟수 |
|---:|---|---:|
| 0 | classic | 500 |
| 1 | pop | 600 |
| 2 | classic | 150 |
| 3 | classic | 800 |
| 4 | pop | 2500 |

---

## 🎯 수록 기준

베스트앨범에 노래를 넣는 규칙은 3가지입니다.

```text
1. 총 재생 횟수가 많은 장르부터 수록한다.
2. 같은 장르 안에서는 재생 횟수가 많은 노래부터 수록한다.
3. 재생 횟수가 같으면 고유 번호가 낮은 노래부터 수록한다.
```

그리고 장르마다 최대 2곡까지만 고릅니다. 🎵

---

# 🔍 예제 이해하기

```text
genres = ["classic", "pop", "classic", "classic", "pop"]
plays  = [500, 600, 150, 800, 2500]
```

## 1단계: 장르별 총 재생 수 구하기

classic 장르:

```text
500 + 150 + 800 = 1450
```

pop 장르:

```text
600 + 2500 = 3100
```

따라서 장르 순서는 다음과 같습니다.

```text
pop → classic
```

pop이 더 많이 재생되었기 때문입니다. 🚀

---

## 2단계: 각 장르에서 인기곡 고르기

pop 장르 노래:

```text
4번: 2500회
1번: 600회
```

따라서:

```text
[4, 1]
```

classic 장르 노래:

```text
3번: 800회
0번: 500회
2번: 150회
```

상위 2곡만 고르면:

```text
[3, 0]
```

---

## 3단계: 최종 결과

장르 순서는 `pop → classic`입니다.

따라서 최종 답은:

```text
[4, 1, 3, 0]
```

입니다. ✅

---

# 🧠 풀이 1. 이해하기 쉬운 방법 — Dictionary로 직접 관리하기

## 💡 아이디어

필요한 정보는 두 가지입니다.

```text
1. 장르별 총 재생 횟수
2. 장르별 노래 목록
```

그래서 Dictionary를 두 개 사용합니다.

```csharp
Dictionary<string, int> genreTotal
Dictionary<string, List<(int index, int play)>> songsByGenre
```

튜플에는 다음 정보를 저장합니다.

```text
고유 번호 index
재생 횟수 play
```

그다음:

```text
1. 장르를 총 재생 횟수 기준으로 정렬
2. 각 장르의 노래를 재생 횟수 내림차순, 고유 번호 오름차순으로 정렬
3. 앞에서 최대 2곡 선택
```

하면 됩니다. 🎯

---

## 💻 C# 코드

```csharp
using System.Collections.Generic;
using System.Linq;

public class Solution
{
    public int[] solution(string[] genres, int[] plays)
    {
        var genreTotal = new Dictionary<string, int>();
        var songsByGenre = new Dictionary<string, List<(int index, int play)>>();

        for (int i = 0; i < genres.Length; i++)
        {
            string genre = genres[i];
            int play = plays[i];

            genreTotal[genre] = genreTotal.GetValueOrDefault(genre) + play;

            if (!songsByGenre.ContainsKey(genre))
                songsByGenre[genre] = new List<(int index, int play)>();

            songsByGenre[genre].Add((i, play));
        }

        var answer = new List<int>();

        foreach (var genre in genreTotal.OrderByDescending(g => g.Value).Select(g => g.Key))
        {
            answer.AddRange(songsByGenre[genre]
                .OrderByDescending(song => song.play)
                .ThenBy(song => song.index)
                .Take(2)
                .Select(song => song.index));
        }

        return answer.ToArray();
    }
}
```

---

## 🔎 코드 해설

### 1. 장르별 총 재생 횟수 저장

```csharp
Dictionary<string, int> genreTotal = new Dictionary<string, int>();
```

예제에서는 다음처럼 저장됩니다.

```text
classic → 1450
pop     → 3100
```

---

### 2. 장르별 노래 목록 저장

```csharp
Dictionary<string, List<(int index, int play)>> songsByGenre
```

예제에서는 다음처럼 저장됩니다.

```text
classic → (0, 500), (2, 150), (3, 800)
pop     → (1, 600), (4, 2500)
```

여기서 `(0, 500)`은 다음 뜻입니다.

```text
고유 번호 0번 노래가 500회 재생됨
```

---

### 3. 장르를 총 재생 수 기준으로 정렬

```csharp
genreTotal.OrderByDescending(g => g.Value)
```

총 재생 횟수가 많은 장르부터 정렬합니다.

```text
pop → classic
```

---

### 4. 장르 안에서 노래 정렬

```csharp
.OrderByDescending(song => song.play)
.ThenBy(song => song.index)
```

정렬 기준은 다음과 같습니다.

```text
1. 재생 횟수 많은 순서
2. 재생 횟수가 같으면 고유 번호 낮은 순서
```

---

### 5. 최대 2곡만 선택해서 답에 추가

```csharp
.Take(2)
.Select(song => song.index)
```

장르에 속한 곡이 하나라면 1곡만 선택됩니다.

장르에 곡이 3개 이상이어도 2곡까지만 선택합니다. 🎵

`AddRange()`로 선택된 고유 번호들을 한 번에 답에 넣습니다.

---

## ⏱️ 시간 복잡도

노래 개수를 `n`, 장르 수를 `g`라고 하겠습니다.

장르별로 노래를 모으는 데:

```text
O(n)
```

장르를 정렬하는 데:

```text
O(g log g)
```

각 장르 안의 노래를 정렬하는 전체 비용은 최악의 경우:

```text
O(n log n)
```

따라서 전체 시간 복잡도는:

```text
O(n log n)
```

입니다.

---

## 📦 공간 복잡도

장르별 총합과 노래 목록을 저장합니다.

```text
O(n)
```

입니다.

---

# 🚀 풀이 2. 짧은 코드 버전 — LINQ GroupBy 활용하기

## 💡 아이디어

LINQ를 적극적으로 사용하면 다음 흐름을 한 번에 표현할 수 있습니다.

```text
1. 노래 번호를 장르별로 그룹화한다.
2. 장르 총 재생 수로 그룹을 정렬한다.
3. 각 그룹에서 상위 2곡을 고른다.
4. 고유 번호 배열로 반환한다.
```

---

## 💻 C# 코드

```csharp
using System.Linq;

public class Solution
{
    public int[] solution(string[] genres, int[] plays)
    {
        return Enumerable.Range(0, genres.Length)
            .GroupBy(i => genres[i])
            .OrderByDescending(group => group.Sum(i => plays[i]))
            .SelectMany(group => group
                .OrderByDescending(i => plays[i])
                .ThenBy(i => i)
                .Take(2))
            .ToArray();
    }
}
```

---

## ✨ 코드 의미

### 1. 노래 번호 만들기

```csharp
Enumerable.Range(0, genres.Length)
```

노래의 고유 번호를 `0`부터 `genres.Length - 1`까지 만듭니다.

```text
0, 1, 2, 3, 4
```

---

### 2. 장르별로 그룹화

```csharp
.GroupBy(i => genres[i])
```

노래 번호 `i`를 해당 노래의 장르 기준으로 묶습니다.

```text
classic 그룹
pop 그룹
```

---

### 3. 장르 총 재생 수 기준 정렬

```csharp
.OrderByDescending(group => group.Sum(i => plays[i]))
```

각 장르 그룹의 총 재생 수를 구한 뒤, 큰 순서대로 정렬합니다. 📊

---

### 4. 각 장르에서 상위 2곡 선택

```csharp
.SelectMany(group => group
    .OrderByDescending(i => plays[i])
    .ThenBy(i => i)
    .Take(2))
```

각 장르 안에서:

```text
재생 횟수 내림차순
고유 번호 오름차순
```

으로 정렬한 뒤 2곡만 고릅니다.

`SelectMany`는 장르별 결과를 하나의 목록으로 펼쳐 줍니다. 🧺

---

### 5. 고유 번호 배열로 반환

```csharp
.ToArray()
```

이미 노래 번호만 다루고 있으므로 그대로 배열로 만들면 됩니다.

---

## ⚠️ 프로그래머스에서 실행할 때 주의

LINQ를 사용하므로 반드시 아래 네임스페이스가 필요합니다.

```csharp
using System.Linq;
```

프로그래머스 C# 환경에서 실행 가능합니다. ✅

---

## ⏱️ 시간 복잡도

노래 정보 생성과 그룹화는:

```text
O(n)
```

장르 정렬은:

```text
O(g log g)
```

각 장르 안의 정렬을 모두 합치면 최악의 경우:

```text
O(n log n)
```

전체 시간 복잡도는:

```text
O(n log n)
```

입니다.

---

## 📦 공간 복잡도

LINQ 과정에서 노래 정보와 그룹 정보를 저장합니다.

```text
O(n)
```

입니다.

---

# ⚖️ 두 풀이 비교

| 풀이 | 핵심 방법 | 장점 | 시간 복잡도 | 공간 복잡도 |
|---|---|---|---:|---:|
| 풀이 1 | Dictionary + 정렬 | 흐름이 명확하다 😊 | O(n log n) | O(n) |
| 풀이 2 | LINQ `GroupBy` | 코드가 짧고 깔끔하다 🚀 | O(n log n) | O(n) |

---

# 🏆 추천 풀이

처음 공부할 때는 **풀이 1번 Dictionary 방식**을 추천합니다.

이유는 다음과 같습니다.

```text
1. 장르별 총 재생 수와 노래 목록을 따로 이해하기 쉽습니다.
2. 정렬 기준 3개가 명확하게 보입니다.
3. 디버깅하기 쉽습니다.
```

코딩 테스트 제출용으로 코드 길이를 우선한다면 **풀이 2번**이 훨씬 간결합니다.

프로그래머스 제출용으로도 사용할 수 있습니다.  
단, `using System.Linq;`를 반드시 포함해야 합니다. ⚠️

---

# ✅ 최종 정리

이 문제는 다음 순서로 풀면 됩니다.

```text
1. 노래를 장르별로 묶는다.
2. 장르별 총 재생 횟수를 구한다.
3. 총 재생 횟수가 큰 장르부터 처리한다.
4. 각 장르 안에서 재생 횟수가 큰 노래를 고른다.
5. 재생 횟수가 같으면 고유 번호가 낮은 노래를 먼저 고른다.
6. 장르마다 최대 2곡만 선택한다.
```

🎧 이 문제는 “그룹화 + 정렬 기준 여러 개 적용” 패턴으로 기억하면 좋습니다.
