---
title: "Divide and Conquer"
use_math: true
---

Divide and Conquer (분할정복) 알고리즘은 세 개의 단계로 쪼갤 수 있다.
1) **Divide**: Base case일 경우 바로 풀고, 아니라면 문제를 여러 부분으로 나눈다.
2) **Recur**: 각 부분 문제를 재귀적으로 푼다.
3) **Conquer**: 각 부분 문제의 솔루션을 합쳐서 최종 솔루션을 만든다.

### 정렬된 배열 탐색
Given: n개의 숫자 $a_{0}, a_{1}, ..., a_{n-1}$를 정렬한 시퀀스 $S$가 $A[0, 1, ..., n - 1]$ 배열에 저장되어 있다.
Problem: 어떠한 $x$가 주어졌을 때, 이 숫자가 $S$에 존재하는가?

- Naive Approach
Idea: 각 배열을 순서대로 방문하여 $x$인지 확인한다.
이러한 Naive한 접근법은 O(n) 러닝 타임을 가진다.

- Binary Search
정렬되어 있다는 속성을 활용하여 이진 탐색을 하면 훨씬 더 효율적이다.
아이디어는 다음과 같다.
1. 배열이 empty하면 "No"를 리턴한다.
2. $x$와 중간 원소를 비교한다. 여기서 중간 원소는 A[floor(n/2)]가 된다.
3. 중간 원소가 x라면, "Yes"를 리턴한다.
4. 중간 원소가 x가 아니라면:
    -만약 A[floor(n/2)] > x 이면, A[0 ... floor(n/2) - 1]를 재귀적으로 탐색한다.
    -만약 A[floor(n/2)] < x 이면, A[floor(n/2) + 1 ... n - 1]를 재귀적으로 탐색한다.

Correctness:
Invariant는, $x$가 divide 단계 전에 $A$에 있다면, divide 단계 후에도 $A$에 있다는 것이다.
즉, 만약 A[floor(n/2)] > x 이면, x는 A[0 ... floor(n/2) - 1]에 있고,
만약 A[floor(n/2)] < x 이면, x는 A[floor(n/2) + 1 ... n - 1]에 있다.

매 divide 단계에서 배열은 점점 작아진다.
따라서 만약 $x$가 $A$에 있다면, invariant에 의해서 언젠가는 $x$의 위치를 방문할 것이고, "Yes"를 리턴한다.
만약 $x$가 $A$에 없다면, empty 배열에 도달하여 "No"를 리턴한다.

- Recurrence Formula
Divide and Conquer 알고리즘의 시간 복잡도를 분석하기 위해 재귀 함수를 정의하여 풀 수 있다.

$T(n)$을 알고리즘의 러닝 타임이라고 하자. 우리는 다음의 것들을 알아내야 한다.
- Divide step cost in terms of $n$
- Recur step(s) cost in terms of $T$
- Conquer step cost in terms of $n$

베이스 케이스에 대한 경우를 추가하여 우리는 다음과 같이 $T(n)$을 정의할 수 있다.

$$
  T(n) =
\begin{cases}
\text{Recur + Divide and Conquer},  & \text{if $n > 1$} \\
\text{Base case}, & \text{if $n = 1$}
\end{cases}
$$

배열로 구현하는 이진 탐색을 예로 들어보자.
Divide step (중간을 찾고 $x$와 비교하는 것)은 O(1)이 걸린다.
Recur step (왼쪽 or 오른쪽 부분 문제를 푸는 것)은 T(n/2)이 걸린다.
Conquer step (재귀에서 답을 리턴하는 것)은 O(1)이 걸린다.
Base case는 O(1)이 걸린다. (대체로 Base case는 O(1))

따라서 $T(n)$을 다시 적으면,
$$
  T(n) =
\begin{cases}
\Tfrac{n}{2},  & \text{if $n > 1$} \\
O(1), & \text{if $n = 1$}
\end{cases}
$$

Base case에 도달하기 전까지 Input을 O(log n)번까지 밖에 반으로 쪼갤 수 없으므로, $T(n) = O(logn)$이 된다.

--증명 추가-- 링크드 리스트일 경우는?
--T(n)에 대한 이론 추가--

- Merge Sort
1. **Divide**: 배열을 반으로 나눈다.
2. **Recur**: 각각 재귀적으로 정렬한다.
3. **Conquer**: 정렬된 두 개를 붙여서 하나로 만든다.

{% include pseudocode.html id="1" code="
\begin{algorithm}
\caption{Merge Sort}
\begin{algorithmic}
\PROCEDURE{MERGESORT}{$A$}
    \IF{$\abs(S) < 2$}
        \STATE return $S$
    \ENDIF
    \STATE mid $\leftarrow \lfloor\abs(S)/2\rfloor$
    \STATE left $\leftarrow S[\text{:mid}]$
    \STATE right $\leftarrow S[\text{mid:}]$

    \STATE sortedLeft $\leftarrow$ MERGESORT(left)
    \STATE sortedRight $\leftarrow MERGESORT(right)

    \STATE return MERGE(sortedLeft, sortedRight)
\ENDPROCEDURE
\end{algorithmic}
\end{algorithm}
" %}

근데 Merge 단계는 어떻게 진행될까?

--이미지 추가--

1. 각 부분 배열에서 최소값을 가지는 원소를 계속 추적한다.
2. 둘을 비교해서 더 작은 원소를 result 배열에 넣는다.
3. 끝날때까지 반복한다.

총 n번의 비교가 수행된다.

TBC...